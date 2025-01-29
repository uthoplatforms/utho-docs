---
weight: 13
title: Remove Nodepool
title_meta: "Remove Nodepool to the Kubernetes Cluster"
description: "Learn how Utho makes Kubernetes management simple and easy so you easily anticipate your kubernetes infrastructure costs"
keywords: ["Kubernetes", "Instances",  "scaling", "server"]
tags: ["utho platform","Kubernetes"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/products/kubernetes/how-tos/remove-nodepool']
icon: 'api'
tab: true
---
Here is the detailed documentation for **removing a node pool** from an existing Kubernetes cluster using the  **Utho API** . The only change from the update API is setting `"count": 0` in the request payload.

---

## Utho API Documentation: Remove Node Pool from Kubernetes Cluster

This guide explains how to remove a node pool from an existing Kubernetes cluster using the  **Utho API** . To remove a node pool, send a `POST` request to the API endpoint with `"count": 0`.

## API Endpoint:

```
https://api.utho.com/v2/kubernetes/{cluster_id}/nodepool/{pool_id}/update
```

**Headers:**

* **Authorization:** `Bearer YOUR_BEARER_TOKEN`

### Request Parameters:

* **cluster_id** : The unique identifier of the Kubernetes cluster.
* **pool_id** : The unique identifier of the node pool.

### Request Payload:

```json
{
  "label": "pool-UWIKpAJX",
  "size": "10045",
  "count": 0
}
```

### Expected Response:

```json
{
  "status": "success",
  "message": "Node Pool removed successfully",
  "id": "750031"
}
```

---

## Example Code Snippets

### 1. **Python (Using `requests` library)**

```python
import requests
import json

# API URL
url = "https://api.utho.com/v2/kubernetes/750031/nodepool/pool-UWIKpAJX/update"

# Request headers
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}

# Payload to remove node pool (count: 0)
data = {
    "label": "pool-UWIKpAJX",
    "size": "10045",
    "count": 0
}

# Sending the POST request
response = requests.post(url, headers=headers, json=data)

# Handling the response
if response.status_code == 200:
    print("Success:", response.json())
else:
    print("Error:", response.status_code, response.text)
```

---

### 2. **JavaScript (Node.js using `axios`)**

```javascript
const axios = require('axios');

// API URL
const url = 'https://api.utho.com/v2/kubernetes/750031/nodepool/pool-UWIKpAJX/update';

const headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
};

// Payload to remove node pool
const data = {
  label: 'pool-UWIKpAJX',
  size: '10045',
  count: 0
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

---

### 3. **cURL (Command Line)**

```bash
curl -X POST "https://api.utho.com/v2/kubernetes/750031/nodepool/pool-UWIKpAJX/update" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"label":"pool-UWIKpAJX","size":"10045","count":0}'
```

---

### 4. **PHP (Using `cURL`)**

```php
<?php

// API URL
$url = "https://api.utho.com/v2/kubernetes/750031/nodepool/pool-UWIKpAJX/update";

// Data to send (Removing node pool by setting count: 0)
$data = array(
    "label" => "pool-UWIKpAJX",
    "size" => "10045",
    "count" => 0
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

// Check for errors
if (curl_errno($ch)) {
    echo 'Error:' . curl_error($ch);
} else {
    echo 'Response:' . $response;
}

// Close the cURL session
curl_close($ch);
?>
```

---

### 5. **Java (Using `HttpURLConnection`)**

```java
import java.io.OutputStream;
import java.net.HttpURLConnection;
import java.net.URL;
import java.nio.charset.StandardCharsets;

public class RemoveNodePool {
    public static void main(String[] args) {
        try {
            String url = "https://api.utho.com/v2/kubernetes/750031/nodepool/pool-UWIKpAJX/update";
            HttpURLConnection connection = (HttpURLConnection) new URL(url).openConnection();
            connection.setRequestMethod("POST");
            connection.setRequestProperty("Content-Type", "application/json", "Authorization", "Bearer YOUR_API_KEY");
            connection.setDoOutput(true);

            // JSON Payload to remove node pool
            String jsonInputString = "{\"label\":\"pool-UWIKpAJX\",\"size\":\"10045\",\"count\":0}";

            // Send request
            try (OutputStream os = connection.getOutputStream()) {
                byte[] input = jsonInputString.getBytes(StandardCharsets.UTF_8);
                os.write(input, 0, input.length);
            }

            // Get response
            int code = connection.getResponseCode();
            System.out.println("Response Code: " + code);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

---

### 6. **Go (Using `net/http`)**

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
	payload := []byte(`{"label":"pool-UWIKpAJX","size":"10045","count":0}`)

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

* Verify the payload format and ensure `"count": 0` is correctly passed.

1. **500 Internal Server Error** :

* This may indicate a server-side issue. Contact Utho support if the issue persists.
