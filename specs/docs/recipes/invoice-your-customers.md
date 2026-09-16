---
updatedAt: 2026-09-11T19:32:16.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Invoice your customers

```curl cURL
# 1. Look for the customer. Read every page before you conclude nothing matches.
curl --request GET \
  --url 'https://api.mercury.com/api/v1/ar/customers?limit=1000' \
  --header 'Accept: application/json' \
  --header 'Authorization: Bearer <<apiKey>>'

# When page.nextPage holds an id, pass it as start_after and read the next page.
curl --request GET \
  --url 'https://api.mercury.com/api/v1/ar/customers?limit=1000&start_after=8f1a4c2e-0d55-4a1e-9f7c-6b2f0f0f1a21' \
  --header 'Accept: application/json' \
  --header 'Authorization: Bearer <<apiKey>>'

# No match on any page? Then create them. Name and email are the required fields.
curl --request POST \
  --url https://api.mercury.com/api/v1/ar/customers \
  --header 'Accept: application/json' \
  --header 'Content-Type: application/json' \
  --header 'Authorization: Bearer <<apiKey>>' \
  --data '{
    "name": "Acme Robotics",
    "email": "ap@acme-robotics.example"
}'

# 2. Raise a single invoice. All nine required fields are in this body.
curl --request POST \
  --url https://api.mercury.com/api/v1/ar/invoices \
  --header 'Accept: application/json' \
  --header 'Content-Type: application/json' \
  --header 'Authorization: Bearer <<apiKey>>' \
  --data '{
    "customerId": "8f1a4c2e-0d55-4a1e-9f7c-6b2f0f0f1a21",
    "destinationAccountId": "4560b56a-3a08-11e9-a549-5b373eacd5d3",
    "invoiceDate": "2026-08-12",
    "dueDate": "2026-09-11",
    "lineItems": [
        {
            "name": "Monthly Retainer, August",
            "quantity": 1,
            "unitPrice": 2400.00
        }
    ],
    "ccEmails": [],
    "creditCardEnabled": false,
    "achDebitEnabled": true,
    "useRealAccountNumber": false,
    "invoiceNumber": "ACME-2026-08",
    "sendEmailOption": "SendNow"
}'

# 3. Before a run, read the invoice numbers you have already used.
curl --request GET \
  --url 'https://api.mercury.com/api/v1/ar/invoices?limit=1000&order=desc' \
  --header 'Accept: application/json' \
  --header 'Authorization: Bearer <<apiKey>>'

# ACME-2026-08 came back, so do not send it again. Create only what is
# missing, one request per invoice.
curl --request POST \
  --url https://api.mercury.com/api/v1/ar/invoices \
  --header 'Accept: application/json' \
  --header 'Content-Type: application/json' \
  --header 'Authorization: Bearer <<apiKey>>' \
  --data '{
    "customerId": "3d90f11c-77a4-4f0e-9c62-1b8d5a2e6c07",
    "destinationAccountId": "4560b56a-3a08-11e9-a549-5b373eacd5d3",
    "invoiceDate": "2026-08-12",
    "dueDate": "2026-09-11",
    "lineItems": [
        {
            "name": "Monthly Retainer, August",
            "quantity": 1,
            "unitPrice": 1800.00
        }
    ],
    "ccEmails": [],
    "creditCardEnabled": false,
    "achDebitEnabled": true,
    "useRealAccountNumber": false,
    "invoiceNumber": "BOREAL-2026-08",
    "sendEmailOption": "SendNow"
}'

# 4. On a cadence you own, the same create runs once per customer per period,
# with the period inside your own invoiceNumber.
#   September:  "invoiceNumber": "ACME-2026-09"
#   October:    "invoiceNumber": "ACME-2026-10"

# 5. Poll the list and read status on each invoice.
curl --request GET \
  --url 'https://api.mercury.com/api/v1/ar/invoices?limit=1000&order=desc' \
  --header 'Accept: application/json' \
  --header 'Authorization: Bearer <<apiKey>>'
```

```python Python
import datetime
import requests

BASE = "https://api.mercury.com/api/v1"
HEADERS = {
    "Accept": "application/json",
    "Content-Type": "application/json",
    "Authorization": "Bearer <<apiKey>>",
}

DESTINATION_ACCOUNT_ID = "4560b56a-3a08-11e9-a549-5b373eacd5d3"


# 1. Look up the customer, and create one only when no page holds them.
def find_customer_by_email(email):
    """Read every page. The list endpoint has no search, so matching is client side."""
    params = {"limit": 1000}
    while True:
        page = requests.get(BASE + "/ar/customers", headers=HEADERS,
                            params=params).json()
        for customer in page["customers"]:
            if customer["email"].lower() == email.lower():
                return customer["id"]
        cursor = page["page"].get("nextPage")
        if not cursor:
            return None
        params["start_after"] = cursor


def find_or_create_customer(name, email):
    """Return a customerId, creating the customer only when the lookup finds none."""
    found = find_customer_by_email(email)
    if found:
        return found
    created = requests.post(BASE + "/ar/customers", headers=HEADERS,
                            json={"name": name, "email": email}, timeout=30)
    created.raise_for_status()
    return created.json()["id"]


# 2. Build the invoice body. Every one of the nine required fields is here.
def build_invoice(customer_id, number, unit_price, today):
    return {
        "customerId": customer_id,
        "destinationAccountId": DESTINATION_ACCOUNT_ID,
        "invoiceDate": today.isoformat(),
        "dueDate": (today + datetime.timedelta(days=30)).isoformat(),
        "lineItems": [
            {"name": "Monthly Retainer, August", "quantity": 1,
             "unitPrice": unit_price}
        ],
        "ccEmails": [],
        "creditCardEnabled": False,
        "achDebitEnabled": True,
        "useRealAccountNumber": False,
        # Optional, and both worth setting on purpose.
        "invoiceNumber": number,
        "sendEmailOption": "SendNow",
    }


today = datetime.date.today()
customer_id = find_or_create_customer("Acme Robotics", "ap@acme-robotics.example")
response = requests.post(
    BASE + "/ar/invoices", headers=HEADERS,
    json=build_invoice(customer_id, "ACME-2026-08", 2400.00, today), timeout=30)
print(response.status_code, response.text)


# 3. Many invoices. One request each, and never repeat an invoiceNumber.
def used_invoice_numbers():
    """Every invoiceNumber already on the account. This is the duplicate check."""
    numbers = set()
    params = {"limit": 1000, "order": "desc"}
    while True:
        page = requests.get(BASE + "/ar/invoices", headers=HEADERS, params=params).json()
        numbers.update(item["invoiceNumber"] for item in page["invoices"])
        cursor = page["page"].get("nextPage")
        if not cursor:
            return numbers
        params["start_after"] = cursor


def create_once(invoice, used):
    """Create a single invoice, and never repeat an invoiceNumber.

    Returns ("created", id), ("existing", None), or ("failed", None), so the
    caller can tell a create it already has from one it still owes.
    """
    number = invoice["invoiceNumber"]
    if number in used:
        print("skipped, already exists:", number)
        return "existing", None
    try:
        response = requests.post(BASE + "/ar/invoices", headers=HEADERS,
                                 json=invoice, timeout=30)
    except requests.exceptions.RequestException as error:
        # The request may have reached Mercury before the connection broke.
        # Re-read before retrying so a lost response is not read as a failure.
        if number in used_invoice_numbers():
            print("created despite the error, not retrying:", number)
            used.add(number)
            return "existing", None
        print("failed, safe to retry:", number, error)
        return "failed", None
    if response.status_code != 200:
        print("rejected:", number, response.status_code, response.text)
        return "failed", None
    used.add(number)
    return "created", response.json()["id"]


# The batch for this run: one dict per customer, turned into a request body by
# the same build_invoice from step 2. Acme was invoiced above, so watch the
# guard skip it rather than raise a second invoice for the same amount.
BILLING_RUN = [
    {"name": "Acme Robotics", "email": "ap@acme-robotics.example",
     "number": "ACME-2026-08", "unit_price": 2400.00},
    {"name": "Boreal Freight", "email": "ap@boreal-freight.example",
     "number": "BOREAL-2026-08", "unit_price": 1800.00},
]

invoices_to_send = [
    build_invoice(find_or_create_customer(row["name"], row["email"]),
                  row["number"], row["unit_price"], today)
    for row in BILLING_RUN
]

used = used_invoice_numbers()
outcomes = {"created": [], "existing": [], "failed": []}
for invoice in invoices_to_send:
    outcome, invoice_id = create_once(invoice, used)
    outcomes[outcome].append(invoice["invoiceNumber"])

print("created", len(outcomes["created"]))
print("already existed, not sent again", len(outcomes["existing"]))
print("failed, still owed", len(outcomes["failed"]), outcomes["failed"])


# 4. Schedule the create call yourself. Run this from cron on the first.
CUSTOMERS = [
    {"customer_id": "8f1a4c2e-0d55-4a1e-9f7c-6b2f0f0f1a21",
     "prefix": "ACME", "unit_price": 2400.00},
    {"customer_id": "3d90f11c-77a4-4f0e-9c62-1b8d5a2e6c07",
     "prefix": "BOREAL", "unit_price": 1800.00},
]

period = today.strftime("%Y-%m")
used = used_invoice_numbers()
for customer in CUSTOMERS:
    # One number per customer per period. Re-running the job is now safe.
    monthly = build_invoice(customer["customer_id"],
                            customer["prefix"] + "-" + period,
                            customer["unit_price"], today)
    outcome, invoice_id = create_once(monthly, used)
    print(customer["prefix"], outcome, invoice_id)


# 5. Poll the list and read status. There is no invoice-specific event.
def open_invoices():
    still_open = []
    params = {"limit": 1000, "order": "desc"}
    while True:
        page = requests.get(BASE + "/ar/invoices", headers=HEADERS, params=params).json()
        still_open += [i for i in page["invoices"] if i["status"] == "Unpaid"]
        cursor = page["page"].get("nextPage")
        if not cursor:
            return still_open
        params["start_after"] = cursor


for invoice in open_invoices():
    print(invoice["invoiceNumber"], invoice["status"], invoice["dueDate"])
```

```ruby Ruby
require 'date'
require 'json'
require 'net/http'
require 'uri'

BASE = "https://api.mercury.com/api/v1"
HEADERS = {
  "Accept" => "application/json",
  "Content-Type" => "application/json",
  "Authorization" => "Bearer <<apiKey>>"
}

DESTINATION_ACCOUNT_ID = "4560b56a-3a08-11e9-a549-5b373eacd5d3"


def http_for(uri)
  http = Net::HTTP.new(uri.host, uri.port)
  http.use_ssl = true
  http
end


def get_json(path, params = {})
  uri = URI(BASE + path)
  uri.query = URI.encode_www_form(params) unless params.empty?
  request = Net::HTTP::Get.new(uri)
  HEADERS.each { |k, v| request[k] = v }
  JSON.parse(http_for(uri).request(request).body)
end


def post_json(path, payload)
  uri = URI(BASE + path)
  request = Net::HTTP::Post.new(uri)
  HEADERS.each { |k, v| request[k] = v }
  request.body = payload.to_json
  http_for(uri).request(request)
end


# 1. Look up the customer, and create one only when no page holds them.
def find_customer_by_email(email)
  params = { "limit" => 1000 }
  loop do
    page = get_json("/ar/customers", params)
    page["customers"].each do |customer|
      return customer["id"] if customer["email"].downcase == email.downcase
    end
    cursor = page["page"]["nextPage"]
    return nil unless cursor
    params["start_after"] = cursor
  end
end


def find_or_create_customer(name, email)
  found = find_customer_by_email(email)
  return found if found
  response = post_json("/ar/customers", { "name" => name, "email" => email })
  JSON.parse(response.body)["id"]
end


# 2. Build the invoice body. Every one of the nine required fields is here.
def build_invoice(customer_id, number, unit_price, today)
  {
    "customerId" => customer_id,
    "destinationAccountId" => DESTINATION_ACCOUNT_ID,
    "invoiceDate" => today.strftime("%Y-%m-%d"),
    "dueDate" => (today + 30).strftime("%Y-%m-%d"),
    "lineItems" => [
      { "name" => "Monthly Retainer, August", "quantity" => 1,
        "unitPrice" => unit_price }
    ],
    "ccEmails" => [],
    "creditCardEnabled" => false,
    "achDebitEnabled" => true,
    "useRealAccountNumber" => false,
    # Optional, and both worth setting on purpose.
    "invoiceNumber" => number,
    "sendEmailOption" => "SendNow"
  }
end


today = Date.today
customer_id = find_or_create_customer("Acme Robotics", "ap@acme-robotics.example")
response = post_json("/ar/invoices",
                     build_invoice(customer_id, "ACME-2026-08", 2400.00, today))
puts "#{response.code} #{response.body}"


# 3. Many invoices. One request each, and never repeat an invoiceNumber.
def used_invoice_numbers
  numbers = []
  params = { "limit" => 1000, "order" => "desc" }
  loop do
    page = get_json("/ar/invoices", params)
    numbers.concat(page["invoices"].map { |item| item["invoiceNumber"] })
    cursor = page["page"]["nextPage"]
    return numbers unless cursor
    params["start_after"] = cursor
  end
end


def create_once(invoice, used)
  number = invoice["invoiceNumber"]
  if used.include?(number)
    puts "skipped, already exists: #{number}"
    return ["existing", nil]
  end
  begin
    response = post_json("/ar/invoices", invoice)
  rescue StandardError => error
    # The request may have reached Mercury before the connection broke.
    # Re-read before retrying so a lost response is not read as a failure.
    if used_invoice_numbers.include?(number)
      puts "created despite the error, not retrying: #{number}"
      used << number
      return ["existing", nil]
    end
    puts "failed, safe to retry: #{number} #{error}"
    return ["failed", nil]
  end
  if response.code.to_i != 200
    puts "rejected: #{number} #{response.code} #{response.body}"
    return ["failed", nil]
  end
  used << number
  ["created", JSON.parse(response.body)["id"]]
end


# The batch for this run: one hash per customer, turned into a request body by
# the same build_invoice from step 2.
BILLING_RUN = [
  { "name" => "Acme Robotics", "email" => "ap@acme-robotics.example",
    "number" => "ACME-2026-08", "unit_price" => 2400.00 },
  { "name" => "Boreal Freight", "email" => "ap@boreal-freight.example",
    "number" => "BOREAL-2026-08", "unit_price" => 1800.00 }
]

invoices_to_send = BILLING_RUN.map do |row|
  build_invoice(find_or_create_customer(row["name"], row["email"]),
                row["number"], row["unit_price"], today)
end

used = used_invoice_numbers
outcomes = { "created" => [], "existing" => [], "failed" => [] }
invoices_to_send.each do |invoice|
  outcome, _invoice_id = create_once(invoice, used)
  outcomes[outcome] << invoice["invoiceNumber"]
end

puts "created #{outcomes['created'].length}"
puts "already existed, not sent again #{outcomes['existing'].length}"
puts "failed, still owed #{outcomes['failed'].length} #{outcomes['failed']}"


# 4. Schedule the create call yourself. Run this from cron on the first.
CUSTOMERS = [
  { "customer_id" => "8f1a4c2e-0d55-4a1e-9f7c-6b2f0f0f1a21",
    "prefix" => "ACME", "unit_price" => 2400.00 },
  { "customer_id" => "3d90f11c-77a4-4f0e-9c62-1b8d5a2e6c07",
    "prefix" => "BOREAL", "unit_price" => 1800.00 }
]

period = today.strftime("%Y-%m")
used = used_invoice_numbers
CUSTOMERS.each do |customer|
  # One number per customer per period. Re-running the job is now safe.
  monthly = build_invoice(customer["customer_id"],
                          "#{customer['prefix']}-#{period}",
                          customer["unit_price"], today)
  outcome, invoice_id = create_once(monthly, used)
  puts "#{customer['prefix']} #{outcome} #{invoice_id}"
end


# 5. Poll the list and read status. There is no invoice-specific event.
def open_invoices
  still_open = []
  params = { "limit" => 1000, "order" => "desc" }
  loop do
    page = get_json("/ar/invoices", params)
    still_open.concat(page["invoices"].select { |i| i["status"] == "Unpaid" })
    cursor = page["page"]["nextPage"]
    return still_open unless cursor
    params["start_after"] = cursor
  end
end


open_invoices.each do |invoice|
  puts "#{invoice['invoiceNumber']} #{invoice['status']} #{invoice['dueDate']}"
end
```

```node Node
const fetch = require('node-fetch');

const BASE = "https://api.mercury.com/api/v1";
const HEADERS = {
  Accept: "application/json",
  "Content-Type": "application/json",
  Authorization: "Bearer <<apiKey>>"
};

const DESTINATION_ACCOUNT_ID = "4560b56a-3a08-11e9-a549-5b373eacd5d3";


async function getJson(path, params = {}) {
  const url = new URL(BASE + path);
  Object.entries(params).forEach(([k, v]) => url.searchParams.set(k, v));
  const res = await fetch(url, { method: "GET", headers: HEADERS });
  return res.json();
}


async function postJson(path, payload) {
  return fetch(BASE + path, {
    method: "POST",
    headers: HEADERS,
    body: JSON.stringify(payload)
  });
}


// 1. Look up the customer, and create one only when no page holds them.
async function findCustomerByEmail(email) {
  const params = { limit: 1000 };
  while (true) {
    const page = await getJson("/ar/customers", params);
    for (const customer of page.customers) {
      if (customer.email.toLowerCase() === email.toLowerCase()) {
        return customer.id;
      }
    }
    const cursor = page.page.nextPage;
    if (!cursor) return null;
    params.start_after = cursor;
  }
}


async function findOrCreateCustomer(name, email) {
  const found = await findCustomerByEmail(email);
  if (found) return found;
  const res = await postJson("/ar/customers", { name, email });
  return (await res.json()).id;
}


// 2. Build the invoice body. Every one of the nine required fields is here.
function buildInvoice(customerId, number, unitPrice, today) {
  const due = new Date(today);
  due.setDate(due.getDate() + 30);
  const isoDate = (d) => d.toISOString().slice(0, 10);
  return {
    customerId: customerId,
    destinationAccountId: DESTINATION_ACCOUNT_ID,
    invoiceDate: isoDate(today),
    dueDate: isoDate(due),
    lineItems: [
      { name: "Monthly Retainer, August", quantity: 1, unitPrice: unitPrice }
    ],
    ccEmails: [],
    creditCardEnabled: false,
    achDebitEnabled: true,
    useRealAccountNumber: false,
    // Optional, and both worth setting on purpose.
    invoiceNumber: number,
    sendEmailOption: "SendNow"
  };
}


// 3. Many invoices. One request each, and never repeat an invoiceNumber.
async function usedInvoiceNumbers() {
  const numbers = new Set();
  const params = { limit: 1000, order: "desc" };
  while (true) {
    const page = await getJson("/ar/invoices", params);
    page.invoices.forEach((item) => numbers.add(item.invoiceNumber));
    const cursor = page.page.nextPage;
    if (!cursor) return numbers;
    params.start_after = cursor;
  }
}


async function createOnce(invoice, used) {
  const number = invoice.invoiceNumber;
  if (used.has(number)) {
    console.log("skipped, already exists:", number);
    return ["existing", null];
  }
  let res;
  try {
    res = await postJson("/ar/invoices", invoice);
  } catch (error) {
    // The request may have reached Mercury before the connection broke.
    // Re-read before retrying so a lost response is not read as a failure.
    if ((await usedInvoiceNumbers()).has(number)) {
      console.log("created despite the error, not retrying:", number);
      used.add(number);
      return ["existing", null];
    }
    console.log("failed, safe to retry:", number, error);
    return ["failed", null];
  }
  if (res.status !== 200) {
    console.log("rejected:", number, res.status, await res.text());
    return ["failed", null];
  }
  used.add(number);
  return ["created", (await res.json()).id];
}


async function main() {
  const today = new Date();
  const customerId = await findOrCreateCustomer("Acme Robotics", "ap@acme-robotics.example");
  const response = await postJson("/ar/invoices",
    buildInvoice(customerId, "ACME-2026-08", 2400.00, today));
  console.log(response.status, await response.text());

  // The batch for this run: one object per customer, turned into a request
  // body by the same buildInvoice from step 2.
  const BILLING_RUN = [
    { name: "Acme Robotics", email: "ap@acme-robotics.example",
      number: "ACME-2026-08", unitPrice: 2400.00 },
    { name: "Boreal Freight", email: "ap@boreal-freight.example",
      number: "BOREAL-2026-08", unitPrice: 1800.00 }
  ];

  const invoicesToSend = [];
  for (const row of BILLING_RUN) {
    invoicesToSend.push(buildInvoice(
      await findOrCreateCustomer(row.name, row.email),
      row.number, row.unitPrice, today));
  }

  const used = await usedInvoiceNumbers();
  const outcomes = { created: [], existing: [], failed: [] };
  for (const invoice of invoicesToSend) {
    const [outcome] = await createOnce(invoice, used);
    outcomes[outcome].push(invoice.invoiceNumber);
  }

  console.log("created", outcomes.created.length);
  console.log("already existed, not sent again", outcomes.existing.length);
  console.log("failed, still owed", outcomes.failed.length, outcomes.failed);

  // 4. Schedule the create call yourself. Run this from cron on the first.
  const CUSTOMERS = [
    { customerId: "8f1a4c2e-0d55-4a1e-9f7c-6b2f0f0f1a21",
      prefix: "ACME", unitPrice: 2400.00 },
    { customerId: "3d90f11c-77a4-4f0e-9c62-1b8d5a2e6c07",
      prefix: "BOREAL", unitPrice: 1800.00 }
  ];

  const period = today.toISOString().slice(0, 7);
  const usedMonthly = await usedInvoiceNumbers();
  for (const customer of CUSTOMERS) {
    // One number per customer per period. Re-running the job is now safe.
    const monthly = buildInvoice(customer.customerId,
      `${customer.prefix}-${period}`, customer.unitPrice, today);
    const [outcome, invoiceId] = await createOnce(monthly, usedMonthly);
    console.log(customer.prefix, outcome, invoiceId);
  }

  // 5. Poll the list and read status. There is no invoice-specific event.
  const params = { limit: 1000, order: "desc" };
  const openInvoices = [];
  while (true) {
    const page = await getJson("/ar/invoices", params);
    openInvoices.push(...page.invoices.filter((i) => i.status === "Unpaid"));
    const cursor = page.page.nextPage;
    if (!cursor) break;
    params.start_after = cursor;
  }
  for (const invoice of openInvoices) {
    console.log(invoice.invoiceNumber, invoice.status, invoice.dueDate);
  }
}

main().catch((err) => console.error(err));
```

```json Response Example
{
    "GET /ar/customers": {
        "customers": [
            {
                "id": "8f1a4c2e-0d55-4a1e-9f7c-6b2f0f0f1a21",
                "name": "Acme Robotics",
                "email": "ap@acme-robotics.example"
            }
        ],
        "page": {
            "nextPage": null,
            "previousPage": null
        }
    },
    "POST /ar/invoices": {
        "id": "c4b7a1d0-9e33-4d2b-8a10-2f5c7e9b4411",
        "invoiceNumber": "ACME-2026-08",
        "status": "Unpaid",
        "amount": 2400.00,
        "dueDate": "2026-09-11"
    },
    "GET /ar/invoices": {
        "invoices": [
            {
                "id": "c4b7a1d0-9e33-4d2b-8a10-2f5c7e9b4411",
                "invoiceNumber": "ACME-2026-08",
                "status": "Paid",
                "amount": 2400.00,
                "dueDate": "2026-09-11"
            },
            {
                "id": "a0e2f7b3-1c48-4f6a-b7d9-83c1e5407d62",
                "invoiceNumber": "BOREAL-2026-08",
                "status": "Unpaid",
                "amount": 1800.00,
                "dueDate": "2026-09-11"
            }
        ],
        "page": {
            "nextPage": null,
            "previousPage": null
        }
    }
}
```

# Before You Start

<!-- curl@5,11,18,29,54,62,92 -->
<!-- python@1-9,11 -->
<!-- ruby@1-13,16-38 -->
<!-- node@1-10,13-27 -->

This recipe raises invoices from your own system and confirms who has paid. The product is called Accounts Receivable in our API reference.

For an overview of what the invoicing endpoints support, start with the [Invoicing guide](/docs/invoicing).

Every sample uses the documented base URL `https://api.mercury.com/api/v1`. In each `Authorization` header, `<<apiKey>>` stands for your Mercury API token.

See [Getting Started](/docs/getting-started) to generate one.

Set these up before your first request:

- **API Token Scopes**: Read actions require a read-only token. Create actions require a read-write token bound to an allowlisted IP address (see [Getting Started](/docs/getting-started) and [API token security policies](/docs/api-token-security-policies)).
- **Plan, Cards, and ACH Setup**: Invoicing requires a paid plan, and card payments need a connected Stripe account. The [Invoicing guide](/docs/invoicing) covers the plan, Stripe, and ACH prerequisites in full.
- **Destination Account**: Call `GET /accounts` and note the `id` of the checking or savings account where invoice payments should land.
- **Test in Sandbox**: The Accounts Receivable API works in Mercury's sandbox. Point requests at `https://api-sandbox.mercury.com/api/v1` with a sandbox-created token to test the full flow before going live. See [Using the Mercury Sandbox](/docs/using-mercury-sandbox).

# Find or Create the Customer

<!-- curl@1-22 -->
<!-- python@14-38,63 -->
<!-- ruby@41-61,87 -->
<!-- node@30-52,124 -->

Every invoice points at a customer, so start by getting that customer's `id`.

- **Look Them Up**: Call `GET /ar/customers`, which returns your customers one page at a time. Page through the results and match on email in your own code.
- **Create One if None Matches**: Call `POST /ar/customers`, then use the `id` from the response as the `customerId` on the invoice.

**Review Results Before Adding a New Customer**: The list returns up to 1000 customers per page and includes a `nextPage` cursor. Read every page before concluding that no match exists. Stopping at the first page risks a duplicate record, which then splits that customer's invoices across two records.

# Create One Invoice

<!-- curl@24-48 -->
<!-- python@41-59,64-67 -->
<!-- ruby@64-83,86-90 -->
<!-- node@55-76,123-127 -->

Every `POST /ar/invoices` create requires all nine of the fields below.

| Required field         | What it is                                                                                                                                  |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| `customerId`           | The customer from step 1                                                                                                                    |
| `destinationAccountId` | The Mercury account where payments land, from `GET /accounts`. Checking and savings only                                                    |
| `invoiceDate`          | The date of the invoice, `YYYY-MM-DD`. It does not have to be today                                                                         |
| `dueDate`              | The date the invoice should be paid by, `YYYY-MM-DD`                                                                                        |
| `lineItems`            | An array. Each item needs `name`, `quantity`, and `unitPrice`, with `salesTaxRate` optional                                                 |
| `ccEmails`             | Emails copied on invoice notifications and reminders. Send an empty array when there are none                                               |
| `creditCardEnabled`    | Whether the invoice can be paid by card. Setting `true` requires a connected Stripe account, and create returns `400` when that check fails |
| `achDebitEnabled`      | Whether the customer can pay by ACH debit, an Automated Clearing House pull from their bank account                                         |
| `useRealAccountNumber` | Set `false` to show virtual payment instructions instead of your real account and routing number                                            |

Two optional fields are worth setting deliberately.

- **`invoiceNumber`**: Pass a unique value on creation. Reused numbers return an error, so retries are safe from duplicate invoicing. When omitted, Mercury assigns a number, which is not guaranteed to be sequential and can skip values. Set it yourself for predictable numbering, as covered in step 3.
- **`sendEmailOption`**: Accepts `DontSend` or `SendNow`, and we send immediately when it is omitted. Sending a `DontSend` invoice at a later time is not available through the API today.

Setting `useRealAccountNumber: false` adds virtual payment instructions to the invoice. The virtual account number behind them belongs to the customer and is shared across all of that customer's invoices, so it does not identify a single invoice. See the [Invoicing guide](/docs/invoicing) for more details on reconciliation.

# Invoice Many Customers

<!-- curl@50-81 -->
<!-- python@70-137 -->
<!-- ruby@94-158 -->
<!-- node@80-119,129-154 -->

To invoice many customers, loop through your customer list and send one create request per invoice. Track and persist the status of each request as you go, so you always know which invoices have been created.

**Set a Unique `invoiceNumber` So Retries Are Safe**: Pass a unique `invoiceNumber` on creation. Reused numbers return an error, so retries are safe from duplicate invoicing. Without your own `invoiceNumber`, Mercury assigns a new one on each create, so a blind retry can duplicate. For bank transfers to the virtual account, Mercury matches payments by customer and amount, so duplicate open invoices for the same customer and amount cause reconciliation errors.

**Handle the Timeout Case**: Send invoices one at a time. Reading your existing invoices first lets you skip numbers already used and avoid an error round-trip. The tricky case is a timeout, because the invoice may have been created even though the connection dropped, so re-read your invoices before retrying to tell a real failure from a lost response.

**Check Your Plan Before a Large Run**: Confirm your monthly invoice limits on [Mercury pricing](https://mercury.com/pricing) before you design for high volume.

# Repeat on a Schedule

<!-- curl@83-86 -->
<!-- python@140-156 -->
<!-- ruby@161-178 -->
<!-- node@156-172 -->

To bill customers on a recurring basis, schedule the create call from your own system. Run a cron job or background worker that sends one `POST /ar/invoices` per customer each billing period. It is the step 3 loop on a timer.

Mercury does not store a recurring series through the API, so two details matter:

- **Assign Period-Based IDs**: A number like `ACME-2026-09` shows at a glance whether that period's invoice was sent, and it doubles as the duplicate check from step 3. Omit it and Mercury assigns a number, which is not guaranteed to be sequential.
- **Track the Schedule in Your Own System**: These invoices carry no series ID, so they do not appear under **Invoicing > Recurring** in the Mercury dashboard. Track and display the recurring billing schedule in your own application.

# Confirm You Were Paid

<!-- curl@88-92 -->
<!-- python@159-173 -->
<!-- ruby@181-197 -->
<!-- node@174-186 -->

Track payment status by polling `GET /ar/invoices` and reading the `status` field (`Unpaid`, `Processing`, `Paid`, or `Cancelled`). There is no invoice-specific webhook event today, so listen for the `transaction.created` event to detect an incoming payment, then poll to confirm the invoice's status has updated. See [Webhooks](/reference/webhooks).

Store the `id` of every invoice you create, and check the ones that matter on an interval that suits your close process.

**Payer Reference (`externalMemo`)**: Read `externalMemo` via `GET /transaction/{transactionId}`. It lives on the transaction, not the invoice, and is often absent, so treat it as optional. The [Invoicing guide](/docs/invoicing) explains where it applies.
