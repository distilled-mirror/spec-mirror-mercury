Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# SAFE API now available

We've introduced three new API endpoints for reading SAFE data.

* GET `/api/v1/safes` to [list all SAFE requests](https://docs.mercury.com/reference/getsaferequests) for your org
* GET `api/v1/safes/{safeRequestId}` to [fetch a SAFE by id](https://docs.mercury.com/reference/getsaferequest)
* GET `/api/v1/safes/{safeRequestId}/document` for [downloading a SAFE document](https://docs.mercury.com/reference/getsaferequestdocument)

<br />
