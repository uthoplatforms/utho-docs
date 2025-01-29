---
weight: 11
title: Attach Security Group
title_meta: "Attach Security Group to the Kubernetes Cluster"
description: "Learn how Utho makes Kubernetes management simple and easy so you easily anticipate your kubernetes infrastructure costs"
keywords: ["Kubernetes", "Instances",  "scaling", "server"]
tags: ["utho platform","Kubernetes"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/products/kubernetes/how-tos/attach-security-groups']
icon: 'api'
tab: true
---
# Utho API Documentation: Attach Security Group to Kubernetes Cluster

## Overview

This API allows users to attach a security group to an existing Kubernetes cluster using the Utho API.

## Endpoint

**URL:**

```
https://api.utho.com/v2/kubernetes/{cluster_id}/securitygroup/{security_group_id}
```

**Headers:**

* **Authorization:** `Bearer YOUR_BEARER_TOKEN`

**Method:**

```
POST
```

## Request Parameters

| Parameter             | Type    | Description                                         |
| --------------------- | ------- | --------------------------------------------------- |
| `cluster_id`        | integer | The unique ID of the Kubernetes cluster.            |
| `security_group_id` | integer | The unique ID of the security group to be attached. |

## Response

### Success Response

```json
{
  "status": "success",
  "message": "Security Group attached to Cluster."
}
```

### Error Responses

#### Invalid Cluster or Security Group ID

```json
{
  "status": "error",
  "message": "Invalid Cluster ID or Security Group ID."
}
```

#### Unauthorized Access

```json
{
  "status": "error",
  "message": "Unauthorized access. Please provide a valid API key."
}
```

## Example Implementations

### 1. cURL

```sh
curl -X POST "https://api.utho.com/v2/kubernetes/750031/securitygroup/23432730" \
     -H "Authorization: Bearer YOUR_API_KEY" \
     -H "Content-Type: application/json"
```

### 2. Python (requests)

```python
import requests

url = "https://api.utho.com/v2/kubernetes/750031/securitygroup/23432730"
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}

response = requests.post(url, headers=headers)
print(response.json())
```

### 3. JavaScript (Axios)

```javascript
const axios = require('axios');

const url = "https://api.utho.com/v2/kubernetes/750031/securitygroup/23432730";
const headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
};

axios.post(url, {}, { headers })
    .then(response => console.log(response.data))
    .catch(error => console.error(error.response.data));
```

### 4. PHP (cURL)

```php
<?php
$url = "https://api.utho.com/v2/kubernetes/750031/securitygroup/23432730";

$headers = [
    "Authorization: Bearer YOUR_API_KEY",
    "Content-Type: application/json"
];

$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, $url);
curl_setopt($ch, CURLOPT_POST, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, $headers);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);

$response = curl_exec($ch);
curl_close($ch);

echo $response;
?>
```

## Notes

* Ensure that the `cluster_id` and `security_group_id` are valid before making the request.
* A valid API key must be included in the `Authorization` header.
* The response will indicate whether the operation was successful or if an error occurred.

## Support

For further assistance, contact support at [support@utho.com](mailto:support@utho.com).
