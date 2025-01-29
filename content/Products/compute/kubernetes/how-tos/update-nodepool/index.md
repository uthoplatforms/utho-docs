---
weight: 12
title: Update Nodepool
title_meta: "Update Nodepool to the Kubernetes Cluster"
description: "Learn how Utho makes Kubernetes management simple and easy so you easily anticipate your kubernetes infrastructure costs"
keywords: ["Kubernetes", "Instances",  "scaling", "server"]
tags: ["utho platform","Kubernetes"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/products/kubernetes/how-tos/update-nodepool']
icon: 'api'
tab: true
---
 Here is the complete documentation for updating the node pool of an existing Kubernetes cluster using the Utho API, with more language examples. This version includes detailed instructions for  **Python** ,  **JavaScript (Node.js)** ,  **cURL** ,  **PHP** ,  **Java** , and  **Go** .

---

This documentation explains how to update the node pool for an existing Kubernetes cluster using the Utho API. This is done using the `POST` request to modify parameters such as the label, size, and count of the node pool.

## API URL:

```
https://api.utho.com/v2/kubernetes/{cluster_id}/nodepool/{pool_id}/update
```

**Headers:**

* **Authorization:** `Bearer YOUR_BEARER_TOKEN`

### Request Parameters:

* **cluster_id** : The unique identifier of the Kubernetes cluster.
* **pool_id** : The unique identifier of the node pool.

### Payload Format:

```json
{
  "label": "pool-UWIKpAJX",
  "size": "10045",
  "count": "1"
}
```

### Response Format:

```json
{
  "status": "success",
  "message": "Node Pool updated and node count adjusted to 1",
  "id": "750031"
}
```

---

## Example Code Snippets

### 1. Python (Using `requests` library)

Python is commonly used for making API requests due to its simplicity and readability. Here’s how to use Python to send the `POST` request.

```python
import requests
import json

# URL for the API endpoint
url = "https://api.utho.com/v2/kubernetes/750031/nodepool/pool-UWIKpAJX/update"

# Request headers
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}

# Data to be sent in the body of the request
data = {
    "label": "pool-UWIKpAJX",
    "size": "10045",
    "count": "1"
}

# Send the POST request
response = requests.post(url, headers=headers, json=data)

# Handle the response
if response.status_code == 200:
    print("Success:", response.json())
else:
    print("Error:", response.status_code, response.text)
```

### 2. JavaScript (Node.js using `axios`)

JavaScript with Node.js is widely used for server-side requests. Below is an example using `axios` to update the Kubernetes node pool.

```javascript
const axios = require('axios');

// API URL
const url = 'https://api.utho.com/v2/kubernetes/750031/nodepool/pool-UWIKpAJX/update';

const headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
};

// Request body
const data = {
  label: 'pool-UWIKpAJX',
  size: '10045',
  count: '1'
};

// Send the POST request
axios.post(url, data, {
  headers: headers
})
.then(response => {
  console.log('Success:', response.data);
})
.catch(error => {
  console.error('Error:', error.response ? error.response.data : error.message);
});
```

### 3. cURL

cURL is a powerful command-line tool for sending requests. Here's how to send a POST request using cURL.

```bash
curl -X POST "https://api.utho.com/v2/kubernetes/750031/nodepool/pool-UWIKpAJX/update" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"label":"pool-UWIKpAJX","size":"10045","count":"1"}'
```

### 4. PHP (Using `cURL` in PHP)

PHP is often used for server-side scripting. Below is an example that shows how to send a request using `cURL` in PHP.

```php
<?php

// API URL
$url = "https://api.utho.com/v2/kubernetes/750031/nodepool/pool-UWIKpAJX/update";

// Data to send
$data = array(
    "label" => "pool-UWIKpAJX",
    "size" => "10045",
    "count" => "1"
);

$headers = [
    "Authorization: Bearer YOUR_API_KEY",
    "Content-Type: application/json"
];

// Convert the data to JSON
$data_json = json_encode($data);

// Initialize cURL
$ch = curl_init($url);

// Set cURL options
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, $headers);
curl_setopt($ch, CURLOPT_POST, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, $data_json);

// Execute the request
$response = curl_exec($ch);

// Check if any error occurred
if (curl_errno($ch)) {
    echo 'Error:' . curl_error($ch);
} else {
    echo 'Response:' . $response;
}

// Close the cURL session
curl_close($ch);
?>
```

### 5. Java (Using `HttpURLConnection`)

Java provides `HttpURLConnection` for making HTTP requests. Below is an example using this class to update the node pool.

```java
import java.io.OutputStream;
import java.net.HttpURLConnection;
import java.net.URL;
import java.nio.charset.StandardCharsets;

public class UpdateNodePool {
    public static void main(String[] args) {
        try {
            String url = "https://api.utho.com/v2/kubernetes/750031/nodepool/pool-UWIKpAJX/update";
            HttpURLConnection connection = (HttpURLConnection) new URL(url).openConnection();
            connection.setRequestMethod("POST");
            connection.setRequestProperty("Content-Type", "application/json", "Authorization", "Bearer YOUR_API_KEY");
            connection.setDoOutput(true);

            // JSON Payload
            String jsonInputString = "{\"label\":\"pool-UWIKpAJX\",\"size\":\"10045\",\"count\":\"1\"}";

            // Send the request
            try (OutputStream os = connection.getOutputStream()) {
                byte[] input = jsonInputString.getBytes(StandardCharsets.UTF_8);
                os.write(input, 0, input.length);
            }

            // Get the response
            int code = connection.getResponseCode();
            System.out.println("Response Code: " + code);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

### 6. Go (Using `net/http` package)

Go is known for its performance and simplicity. Here’s how to use it to send a request to the Utho API.

```go
package main

import (
	"bytes"
	"fmt"
	"io/ioutil"
	"net/http"
)

func main() {
	url := "https://api.utho.com/v2/kubernetes/750031/nodepool/pool-UWIKpAJX/update"
	payload := []byte(`{"label":"pool-UWIKpAJX","size":"10045","count":"1"}`)

	// Create a new HTTP request
	req, err := http.NewRequest("POST", url, bytes.NewBuffer(payload))
	if err != nil {
		fmt.Println("Error creating request:", err)
		return
	}

	// Set Content-Type header
        req.Header.Set("Authorization", "Bearer YOUR_ACCESS_TOKEN")
	req.Header.Set("Content-Type", "application/json")

	// Send the request
	client := &http.Client{}
	resp, err := client.Do(req)
	if err != nil {
		fmt.Println("Error sending request:", err)
		return
	}
	defer resp.Body.Close()

	// Read and print the response
	body, _ := ioutil.ReadAll(resp.Body)
	fmt.Println("Response:", string(body))
}
```

---

## Troubleshooting

### Common Issues:

1. **401 Unauthorized** :

* Ensure that your API token or authentication credentials are correct. You may need to include an `Authorization` header with a valid token.

1. **400 Bad Request** :

* Verify the payload is properly formatted and that all required fields (`label`, `size`, `count`) are correctly provided.

1. **500 Internal Server Error** :

* This typically indicates a server-side issue. If the problem persists, contact Utho support for assistance.

---

This documentation provides clear examples for multiple languages. If you need more languages or additional details, let me know!
