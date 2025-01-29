---
weight: 13
title: Start Kubernetes Cluster
title_meta: "Start the Kubernetes Cluster"
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
Here is the detailed documentation for **starting a Kubernetes cluster** using the **Utho API** in multiple programming languages.

---

## Utho API Documentation: Start Kubernetes Cluster

This guide explains how to start a Kubernetes cluster using the  **Utho API** . To power on a cluster, send a `POST` request to the API endpoint.

## API Endpoint:

```
https://api.utho.com/v2/kubernetes/{cluster_id}/start
```

**Headers:**

* **Authorization:** `Bearer YOUR_BEARER_TOKEN`

### Request Parameters:

* **cluster_id** : The unique identifier of the Kubernetes cluster.

### Request Method:

`POST`

### Expected Response:

```json
{
  "status": "success",
  "message": "Power on Request has been completed!"
}
```

---

## Example Code Snippets

### 1. **Python (Using `requests` library)**

```python
import requests

# API URL
url = "https://api.utho.com/v2/kubernetes/750031/start"

# Request headers
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}

# Sending the POST request
response = requests.post(url, headers=headers)

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
const url = 'https://api.utho.com/v2/kubernetes/750031/start';

const headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
};

// Send the POST request
axios.post(url, {}, {
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
curl -X POST "https://api.utho.com/v2/kubernetes/750031/start" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json"
```

---

### 4. **PHP (Using `cURL`)**

```php
<?php

// API URL
$url = "https://api.utho.com/v2/kubernetes/750031/start";

// Initialize cURL
$ch = curl_init($url);

$headers = [
    "Authorization: Bearer YOUR_API_KEY",
    "Content-Type: application/json"
];

// Set cURL options
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, $headers);
curl_setopt($ch, CURLOPT_POST, true);

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

public class StartCluster {
    public static void main(String[] args) {
        try {
            String url = "https://api.utho.com/v2/kubernetes/750031/start";
            HttpURLConnection connection = (HttpURLConnection) new URL(url).openConnection();
            connection.setRequestMethod("POST");
            connection.setRequestProperty("Content-Type", "application/json", "Authorization", "Bearer YOUR_API_KEY");
            connection.setDoOutput(true);

            // Sending the request
            OutputStream os = connection.getOutputStream();
            os.flush();
            os.close();

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
	"fmt"
	"net/http"
)

func main() {
	url := "https://api.utho.com/v2/kubernetes/750031/start"

	// Create a new HTTP request
	req, err := http.NewRequest("POST", url, nil)
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

	// Print response status
	fmt.Println("Response Code:", resp.StatusCode)
}
```

---

## Troubleshooting

### Common Issues:

1. **401 Unauthorized** :

* Ensure that your API token or authentication credentials are correct. You may need to include an `Authorization` header with a valid token.

1. **400 Bad Request** :

* Verify that the API URL and request format are correct.

1. **500 Internal Server Error** :

* This may indicate a server-side issue. Contact Utho support if the issue persists.

---

This documentation provides **step-by-step** examples for multiple programming languages. Let me know if you need additional details or more languages! 🚀
