---
updatedAt: 2026-01-15T19:55:49.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Bulk upload receipts 

```shell Shell
# Configuration
API_KEY="your_api_key_here"
BASE_URL="https://api.mercury.com/api/v1"

# Array of transaction IDs and their corresponding file paths
declare -A TRANSACTIONS=(
  ["trans_abc123"]="/path/to/receipt1.pdf"
  ["trans_def456"]="/path/to/receipt2.jpg"
  ["trans_ghi789"]="/path/to/receipt3.png"
)

# Initialize counters
SUCCESS_COUNT=0
FAILURE_COUNT=0

echo "Starting bulk upload of transaction attachments..."
echo "Total transactions to process: ${#TRANSACTIONS[@]}"
echo ""

# Loop through transactions and upload attachments
for TRANSACTION_ID in "${!TRANSACTIONS[@]}"; do
  FILE_PATH="${TRANSACTIONS[$TRANSACTION_ID]}"
  
  echo "Uploading $FILE_PATH to transaction $TRANSACTION_ID..."
  
  RESPONSE=$(curl -s -w "\n%{http_code}" \
    --request POST \
    --url "$BASE_URL/transaction/$TRANSACTION_ID/attachments" \
    --header "Accept: application/json" \
    --header "Authorization: Bearer $API_KEY" \
    -F "file=@$FILE_PATH")
  
  HTTP_CODE=$(echo "$RESPONSE" | tail -n1)
  BODY=$(echo "$RESPONSE" | sed '$d')
  
  if [ "$HTTP_CODE" -eq 200 ] || [ "$HTTP_CODE" -eq 201 ]; then
    echo "✓ Success: $TRANSACTION_ID"
    ((SUCCESS_COUNT++))
  else
    echo "✗ Failed: $TRANSACTION_ID (HTTP $HTTP_CODE)"
    echo "  Error: $BODY"
    ((FAILURE_COUNT++))
  fi
  
  echo ""
done

# Summary
echo "========================================="
echo "Bulk Upload Complete"
echo "========================================="
echo "Successful uploads: $SUCCESS_COUNT"
echo "Failed uploads: $FAILURE_COUNT"
echo "Total processed: ${#TRANSACTIONS[@]}"

```

```python Python
import os
import requests
from typing import Dict, List, Tuple

# Configuration
API_KEY = "your_api_key_here"
BASE_URL = "https://api.mercury.com/api/v1"

# Transaction ID to file path mapping
transactions = {
    "trans_abc123": "/path/to/receipt1.pdf",
    "trans_def456": "/path/to/receipt2.jpg",
    "trans_ghi789": "/path/to/receipt3.png",
}

def upload_transaction_attachment(transaction_id: str, file_path: str) -> Tuple[bool, str]:
    """
    Upload a single attachment to a transaction.
    
    Returns:
        Tuple of (success: bool, message: str)
    """
    url = f"{BASE_URL}/transaction/{transaction_id}/attachments"
    headers = {
        "Accept": "application/json",
        "Authorization": f"Bearer {API_KEY}"
    }
    
    try:
        with open(file_path, 'rb') as file:
            files = {'file': (os.path.basename(file_path), file)}
            response = requests.post(url, headers=headers, files=files)
            
        if response.status_code in [200, 201]:
            return True, "Upload successful"
        else:
            return False, f"HTTP {response.status_code}: {response.text}"
    
    except FileNotFoundError:
        return False, f"File not found: {file_path}"
    except Exception as e:
        return False, f"Error: {str(e)}"

def bulk_upload_transaction_attachments(transactions: Dict[str, str]) -> None:
    """
    Upload attachments for multiple transactions.
    """
    print("Starting bulk upload of transaction attachments...")
    print(f"Total transactions to process: {len(transactions)}\n")
    
    results = {"success": [], "failed": []}
    
    for transaction_id, file_path in transactions.items():
        print(f"Uploading {file_path} to transaction {transaction_id}...")
        
        success, message = upload_transaction_attachment(transaction_id, file_path)
        
        if success:
            print(f"✓ Success: {transaction_id}")
            results["success"].append(transaction_id)
        else:
            print(f"✗ Failed: {transaction_id}")
            print(f"  Error: {message}")
            results["failed"].append({"id": transaction_id, "error": message})
        
        print()
    
    # Summary
    print("=" * 50)
    print("Bulk Upload Complete")
    print("=" * 50)
    print(f"Successful uploads: {len(results['success'])}")
    print(f"Failed uploads: {len(results['failed'])}")
    print(f"Total processed: {len(transactions)}")
    
    if results["failed"]:
        print("\nFailed transactions:")
        for item in results["failed"]:
            print(f"  - {item['id']}: {item['error']}")

# Execute bulk upload
if __name__ == "__main__":
    bulk_upload_transaction_attachments(transactions)

```

```ruby Ruby

require 'net/http'
require 'uri'
require 'json'

# Configuration
API_KEY = 'your_api_key_here'
BASE_URL = 'https://api.mercury.com/api/v1'

# Transaction ID to file path mapping
TRANSACTIONS = {
  'trans_abc123' => '/path/to/receipt1.pdf',
  'trans_def456' => '/path/to/receipt2.jpg',
  'trans_ghi789' => '/path/to/receipt3.png'
}

# Upload a single attachment to a transaction
def upload_transaction_attachment(transaction_id, file_path)
  uri = URI("#{BASE_URL}/transaction/#{transaction_id}/attachments")
  
  begin
    File.open(file_path, 'rb') do |file|
      request = Net::HTTP::Post.new(uri)
      request['Accept'] = 'application/json'
      request['Authorization'] = "Bearer #{API_KEY}"
      
      # Create multipart form data
      boundary = "----WebKitFormBoundary#{rand(10**16)}"
      request['Content-Type'] = "multipart/form-data; boundary=#{boundary}"
      
      body = []
      body << "--#{boundary}\r\n"
      body << "Content-Disposition: form-data; name=\"file\"; filename=\"#{File.basename(file_path)}\"\r\n"
      body << "Content-Type: application/octet-stream\r\n\r\n"
      body << file.read
      body << "\r\n--#{boundary}--\r\n"
      
      request.body = body.join
      
      response = Net::HTTP.start(uri.hostname, uri.port, use_ssl: true) do |http|
        http.request(request)
      end
      
      if response.code.to_i.between?(200, 201)
        { success: true, message: 'Upload successful' }
      else
        { success: false, message: "HTTP #{response.code}: #{response.body}" }
      end
    end
  rescue Errno::ENOENT
    { success: false, message: "File not found: #{file_path}" }
  rescue => e
    { success: false, message: "Error: #{e.message}" }
  end
end

# Upload attachments for multiple transactions
def bulk_upload_transaction_attachments(transactions)
  puts 'Starting bulk upload of transaction attachments...'
  puts "Total transactions to process: #{transactions.size}\n\n"
  
  results = { success: [], failed: [] }
  
  transactions.each do |transaction_id, file_path|
    puts "Uploading #{file_path} to transaction #{transaction_id}..."
    
    result = upload_transaction_attachment(transaction_id, file_path)
    
    if result[:success]
      puts "✓ Success: #{transaction_id}"
      results[:success] << transaction_id
    else
      puts "✗ Failed: #{transaction_id}"
      puts "  Error: #{result[:message]}"
      results[:failed] << { id: transaction_id, error: result[:message] }
    end
    
    puts ''
  end
  
  # Summary
  puts '=' * 50
  puts 'Bulk Upload Complete'
  puts '=' * 50
  puts "Successful uploads: #{results[:success].size}"
  puts "Failed uploads: #{results[:failed].size}"
  puts "Total processed: #{transactions.size}"
  
  if results[:failed].any?
    puts "\nFailed transactions:"
    results[:failed].each do |item|
      puts "  - #{item[:id]}: #{item[:error]}"
    end
  end
end

# Execute bulk upload
bulk_upload_transaction_attachments(TRANSACTIONS)

```

```node Node
const fs = require('fs');
const FormData = require('form-data');
const axios = require('axios');

// Configuration
const API_KEY = 'your_api_key_here';
const BASE_URL = 'https://api.mercury.com/api/v1';

// Transaction ID to file path mapping
const transactions = {
  'trans_abc123': '/path/to/receipt1.pdf',
  'trans_def456': '/path/to/receipt2.jpg',
  'trans_ghi789': '/path/to/receipt3.png',
};

/**
 * Upload a single attachment to a transaction
 */
async function uploadTransactionAttachment(transactionId, filePath) {
  const url = `${BASE_URL}/transaction/${transactionId}/attachments`;
  
  try {
    const form = new FormData();
    form.append('file', fs.createReadStream(filePath));
    
    const response = await axios.post(url, form, {
      headers: {
        'Accept': 'application/json',
        'Authorization': `Bearer ${API_KEY}`,
        ...form.getHeaders(),
      },
    });
    
    return { success: true, message: 'Upload successful' };
  } catch (error) {
    const message = error.response 
      ? `HTTP ${error.response.status}: ${JSON.stringify(error.response.data)}`
      : error.message;
    return { success: false, message };
  }
}

/**
 * Upload attachments for multiple transactions
 */
async function bulkUploadTransactionAttachments(transactions) {
  console.log('Starting bulk upload of transaction attachments...');
  console.log(`Total transactions to process: ${Object.keys(transactions).length}\n`);
  
  const results = { success: [], failed: [] };
  
  for (const [transactionId, filePath] of Object.entries(transactions)) {
    console.log(`Uploading ${filePath} to transaction ${transactionId}...`);
    
    const result = await uploadTransactionAttachment(transactionId, filePath);
    
    if (result.success) {
      console.log(`✓ Success: ${transactionId}`);
      results.success.push(transactionId);
    } else {
      console.log(`✗ Failed: ${transactionId}`);
      console.log(`  Error: ${result.message}`);
      results.failed.push({ id: transactionId, error: result.message });
    }
    
    console.log('');
  }
  
  // Summary
  console.log('='.repeat(50));
  console.log('Bulk Upload Complete');
  console.log('='.repeat(50));
  console.log(`Successful uploads: ${results.success.length}`);
  console.log(`Failed uploads: ${results.failed.length}`);
  console.log(`Total processed: ${Object.keys(transactions).length}`);
  
  if (results.failed.length > 0) {
    console.log('\nFailed transactions:');
    results.failed.forEach(item => {
      console.log(`  - ${item.id}: ${item.error}`);
    });
  }
}

// Execute bulk upload
bulkUploadTransactionAttachments(transactions)
  .then(() => console.log('\nProcess completed'))
  .catch(error => console.error('Fatal error:', error));
```

# Configure and Inititalize

<!-- shell@1-10 -->
<!-- python@1-14 -->
<!-- ruby@1-15 -->
<!-- node@1-14 -->

Set up your transaction data and your API config.

# Loop through transactions and upload

<!-- shell@12-43 -->
<!-- python@16-83 -->
<!-- ruby@17-98 -->
<!-- node@16-88 -->
