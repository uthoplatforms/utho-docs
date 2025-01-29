---
weight: 11
title: Dettach Security Group
title_meta: "Dettach Security Group to the Kubernetes Cluster"
description: "Learn how Utho makes Kubernetes management simple and easy so you easily anticipate your kubernetes infrastructure costs"
keywords: ["Kubernetes", "Instances",  "scaling", "server"]
tags: ["utho platform","Kubernetes"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/products/kubernetes/how-tos/dettach-security-groups']
icon: 'api'
tab: true
---
Here's the documentation for detaching a firewall (security group) from an **existing Utho Kubernetes cluster** using the Utho API in multiple programming languages.

---

# **Detach Firewall from Utho Kubernetes Cluster**

This guide explains how to detach a **firewall (security group)** from an existing **Utho Kubernetes Cluster** using the Utho API.

## **API Details**

* **URL:** `https://api.utho.com/v2/kubernetes/750031/securitygroup/23432730`
* **Method:** `DELETE`
* **Response:**
  ```json
  {
    "status": "success",
    "message": "Security Group detached from the Cluster."
  }
  ```

**Headers:**

* **Authorization:** `Bearer YOUR_BEARER_TOKEN`

---

## **1. Using cURL (Command Line)**

```sh
curl -X DELETE "https://api.utho.com/v2/kubernetes/750031/securitygroup/23432730" \
     -H "Authorization: Bearer YOUR_API_KEY" \
     -H "Content-Type: application/json"
```

---

## **2. Using Python (requests)**

```python
import requests

url = "https://api.utho.com/v2/kubernetes/750031/securitygroup/23432730"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}

response = requests.delete(url, headers=headers)

print(response.json())  # {"status": "success", "message": "Security Group detached from the Cluster."}
```

---

## **3. Using JavaScript (Axios)**

```javascript
const axios = require('axios');

const url = "https://api.utho.com/v2/kubernetes/750031/securitygroup/23432730";
const headers = {
    "Authorization": "Bearer YOUR_ACCESS_TOKEN",
    "Content-Type": "application/json"
};

axios.delete(url, { headers })
    .then(response => console.log(response.data))
    .catch(error => console.error(error.response ? error.response.data : error.message));
```

---

## **4. Using PHP (cURL)**

```php
<?php
$url = "https://api.utho.com/v2/kubernetes/750031/securitygroup/23432730";

$headers = [
    "Authorization: Bearer YOUR_ACCESS_TOKEN",
    "Content-Type: application/json"
];

$ch = curl_init($url);
curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "DELETE");
curl_setopt($ch, CURLOPT_HTTPHEADER, $headers);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);

$response = curl_exec($ch);
curl_close($ch);

echo $response;
?>
```

---

## **5. Using Node.js (Native HTTP)**

```javascript
const https = require('https');

const options = {
    hostname: 'api.utho.com',
    path: '/v2/kubernetes/750031/securitygroup/23432730',
    method: 'DELETE',
    headers: {
        'Authorization': 'Bearer YOUR_ACCESS_TOKEN',
        'Content-Type': 'application/json'
    }
};

const req = https.request(options, res => {
    let data = '';
    res.on('data', chunk => { data += chunk; });
    res.on('end', () => console.log(JSON.parse(data)));
});

req.on('error', error => console.error(error));
req.end();
```

---

## **6. Using PowerShell**

```powershell
$headers = @{
    "Authorization" = "Bearer YOUR_ACCESS_TOKEN"
    "Content-Type" = "application/json"
}

Invoke-RestMethod -Uri "https://api.utho.com/v2/kubernetes/750031/securitygroup/23432730" -Method Delete -Headers $headers
```

---

## **7. Using Go**

```go
package main

import (
	"bytes"
	"fmt"
	"net/http"
	"io/ioutil"
)

func main() {
	url := "https://api.utho.com/v2/kubernetes/750031/securitygroup/23432730"

	req, _ := http.NewRequest("DELETE", url, bytes.NewBuffer([]byte("")))
	req.Header.Set("Authorization", "Bearer YOUR_ACCESS_TOKEN")
	req.Header.Set("Content-Type", "application/json")

	client := &http.Client{}
	resp, err := client.Do(req)
	if err != nil {
		fmt.Println("Error:", err)
		return
	}
	defer resp.Body.Close()

	body, _ := ioutil.ReadAll(resp.Body)
	fmt.Println(string(body))
}
```

---

## **Response Handling**

A successful request will return:

```json
{
  "status": "success",
  "message": "Security Group detached from the Cluster."
}
```

If the security group is not found or already detached, an error response will be returned.
