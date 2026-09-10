---
updatedAt: 2026-01-15T19:55:52.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Bulk upload tax docs

```shell Shell
# Configuration
API_KEY="your_api_key_here"
BASE_URL="https://api.mercury.com/api/v1"

# Array of recipient IDs and their corresponding tax document file paths
declare -A RECIPIENTS=(
  ["recipient_abc123"]="/path/to/w9_contractor1.pdf"
  ["recipient_def456"]="/path/to/w9_contractor2.pdf"
  ["recipient_ghi789"]="/path/to/1099_vendor1.pdf"
)

# Initialize counters
SUCCESS_COUNT=0
FAILURE_COUNT=0

echo "Starting bulk upload of recipient tax documents..."
echo "Total recipients to process: ${#RECIPIENTS[@]}"
echo ""

# Loop through recipients and upload tax documents
for RECIPIENT_ID in "${!RECIPIENTS[@]}"; do
  FILE_PATH="${RECIPIENTS[$RECIPIENT_ID]}"
  
  echo "Uploading $FILE_PATH to recipient $RECIPIENT_ID..."
  
  RESPONSE=$(curl -s -w "\n%{http_code}" \
    --request POST \
    --url "$BASE_URL/recipient/$RECIPIENT_ID/attachments" \
    --header "Accept: application/json" \
    --header "Authorization: Bearer $API_KEY" \
    -F "file=@$FILE_PATH")
  
  HTTP_CODE=$(echo "$RESPONSE" | tail -n1)
  BODY=$(echo "$RESPONSE" | sed '$d')
  
  if [ "$HTTP_CODE" -eq 200 ] || [ "$HTTP_CODE" -eq 201 ]; then
    echo "✓ Success: $RECIPIENT_ID"
    ((SUCCESS_COUNT++))
  else
    echo "✗ Failed: $RECIPIENT_ID (HTTP $HTTP_CODE)"
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
echo "Total processed: ${#RECIPIENTS[@]}"
```

```python Python
import os
import requests
from typing import Dict, List, Tuple

# Configuration
API_KEY = "your_api_key_here"
BASE_URL = "https://backend.mercury.com/api/v1"

# Recipient ID to tax document file path mapping
recipients = {
    "recipient_abc123": "/path/to/w9_contractor1.pdf",
    "recipient_def456": "/path/to/w9_contractor2.pdf",
    "recipient_ghi789": "/path/to/1099_vendor1.pdf",
}

def upload_recipient_attachment(recipient_id: str, file_path: str) -> Tuple[bool, str]:
    """
    Upload a single tax document to a recipient.
    
    Returns:
        Tuple of (success: bool, message: str)
    """
    url = f"{BASE_URL}/recipient/{recipient_id}/attachments"
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

def bulk_upload_recipient_attachments(recipients: Dict[str, str]) -> None:
    """
    Upload tax documents for multiple recipients.
    """
    print("Starting bulk upload of recipient tax documents...")
    print(f"Total recipients to process: {len(recipients)}\n")
    
    results = {"success": [], "failed": []}
    
    for recipient_id, file_path in recipients.items():
        print(f"Uploading {file_path} to recipient {recipient_id}...")
        
        success, message = upload_recipient_attachment(recipient_id, file_path)
        
        if success:
            print(f"✓ Success: {recipient_id}")
            results["success"].append(recipient_id)
        else:
            print(f"✗ Failed: {recipient_id}")
            print(f"  Error: {message}")
            results["failed"].append({"id": recipient_id, "error": message})
        
        print()
    
    # Summary
    print("=" * 50)
    print("Bulk Upload Complete")
    print("=" * 50)
    print(f"Successful uploads: {len(results['success'])}")
    print(f"Failed uploads: {len(results['failed'])}")
    print(f"Total processed: {len(recipients)}")
    
    if results["failed"]:
        print("\nFailed uploads:")
        for item in results["failed"]:
            print(f"  - {item['id']}: {item['error']}")

# Execute bulk upload
if __name__ == "__main__":
    bulk_upload_recipient_attachments(recipients)
```

```ruby Ruby
require 'net/http'
require 'uri'
require 'json'

# Configuration
API_KEY = 'your_api_key_here'
BASE_URL = 'https://backend.mercury.com/api/v1'

# Recipient ID to tax document file path mapping
RECIPIENTS = {
  'recipient_abc123' => '/path/to/w9_contractor1.pdf',
  'recipient_def456' => '/path/to/w9_contractor2.pdf',
  'recipient_ghi789' => '/path/to/1099_vendor1.pdf'
}

# Upload a single tax document to a recipient
def upload_recipient_attachment(recipient_id, file_path)
  uri = URI("#{BASE_URL}/recipient/#{recipient_id}/attachments")
  
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

# Upload tax documents for multiple recipients
def bulk_upload_recipient_attachments(recipients)
  puts 'Starting bulk upload of recipient tax documents...'
  puts "Total recipients to process: #{recipients.size}\n\n"
  
  results = { success: [], failed: [] }
  
  recipients.each do |recipient_id, file_path|
    puts "Uploading #{file_path} to recipient #{recipient_id}..."
    
    result = upload_recipient_attachment(recipient_id, file_path)
    
    if result[:success]
      puts "✓ Success: #{recipient_id}"
      results[:success] << recipient_id
    else
      puts "✗ Failed: #{recipient_id}"
      puts "  Error: #{result[:message]}"
      results[:failed] << { id: recipient_id, error: result[:message] }
    end
    
    puts ''
  end
  
  # Summary
  puts '=' * 50
  puts 'Bulk Upload Complete'
  puts '=' * 50
  puts "Successful uploads: #{results[:success].size}"
  puts "Failed uploads: #{results[:failed].size}"
  puts "Total processed: #{recipients.size}"
  
  if results[:failed].any?
    puts "\nFailed uploads:"
    results[:failed].each do |item|
      puts "  - #{item[:id]}: #{item[:error]}"
    end
  end
end

# Execute bulk upload
bulk_upload_recipient_attachments(RECIPIENTS)
```

```node Node
const fs = require('fs');
const FormData = require('form-data');
const axios = require('axios');

// Configuration
const API_KEY = 'your_api_key_here';
const BASE_URL = 'https://backend.mercury.com/api/v1';

// Recipient ID to tax document file path mapping
const recipients = {
  'recipient_abc123': '/path/to/w9_contractor1.pdf',
  'recipient_def456': '/path/to/w9_contractor2.pdf',
  'recipient_ghi789': '/path/to/1099_vendor1.pdf',
};

/**
 * Upload a single tax document to a recipient
 */
async function uploadRecipientAttachment(recipientId, filePath) {
  const url = `${BASE_URL}/recipient/${recipientId}/attachments`;
  
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
 * Upload tax documents for multiple recipients
 */
async function bulkUploadRecipientAttachments(recipients) {
  console.log('Starting bulk upload of recipient tax documents...');
  console.log(`Total recipients to process: ${Object.keys(recipients).length}\n`);
  
  const results = { success: [], failed: [] };
  
  for (const [recipientId, filePath] of Object.entries(recipients)) {
    console.log(`Uploading ${filePath} to recipient ${recipientId}...`);
    
    const result = await uploadRecipientAttachment(recipientId, filePath);
    
    if (result.success) {
      console.log(`✓ Success: ${recipientId}`);
      results.success.push(recipientId);
    } else {
      console.log(`✗ Failed: ${recipientId}`);
      console.log(`  Error: ${result.message}`);
      results.failed.push({ id: recipientId, error: result.message });
    }
    
    console.log('');
  }
  
  // Summary
  console.log('='.repeat(50));
  console.log('Bulk Upload Complete');
  console.log('='.repeat(50));
  console.log(`Successful uploads: ${results.success.length}`);
  console.log(`Failed uploads: ${results.failed.length}`);
  console.log(`Total processed: ${Object.keys(recipients).length}`);
  
  if (results.failed.length > 0) {
    console.log('\nFailed uploads:');
    results.failed.forEach(item => {
      console.log(`  - ${item.id}: ${item.error}`);
    });
  }
}

// Execute bulk upload
bulkUploadRecipientAttachments(recipients)
  .then(() => console.log('\nProcess completed'))
  .catch(error => console.error('Fatal error:', error));
```

# Initizialize and configure

<!-- shell@1-19 -->
<!-- python@ -->
<!-- ruby@1-15 -->
<!-- node@1-15 -->



# Loop through recipeints and upload docs

<!-- shell@20-46 -->
<!-- python@16-83 -->
<!-- ruby@16-97 -->
<!-- node@19-88 -->
