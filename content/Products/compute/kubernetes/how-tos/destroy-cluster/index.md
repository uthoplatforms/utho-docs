---
weight: 9
title: Destroy Kubernetes Cluster
title_meta: "Destroy Kubernetes Cluster on the Utho Platform"
description: "Learn how Utho makes Kubernetes management simple and easy so you easily anticipate your kubernetes infrastructure costs"
keywords: ["Kubernetes", "Instances",  "scaling", "server"]
tags: ["utho platform","Kubernetes"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/products/kubernetes/how-tos/destroy-cluster']
icon: 'api'
tab: true
---
## Endpoint:

**URL:** `https://api.utho.com/v2/kubernetes/750031/destroy`

**Method:** `DELETE`

**Headers:**

* **Authorization:** `Bearer YOUR_BEARER_TOKEN`

**Payload:**

```json
{
  "confirm": "I am aware this action will delete data and cluster permanently"
}
```

### Response:

```json
{
  "k8s": [],
  "status": "success",
  "message": "Cluster has been deleted."
}
```

### Instructions to Destroy Kubernetes Cluster

#### 1. **Using `curl` (Command Line)**

Run the following `curl` command to destroy the Kubernetes cluster:

```bash
curl -X DELETE "https://api.utho.com/v2/kubernetes/750031/destroy" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"confirm": "I am aware this action will delete data and cluster permanently"}'
```

#### 2. **Using PHP**

Here's how you can send the DELETE request using PHP and `cURL`:

```php
<?php
$url = 'https://api.utho.com/v2/kubernetes/750031/destroy';
$data = array('confirm' => 'I am aware this action will delete data and cluster permanently');
$options = array(
  'http' => array(
    'header'  => "Content-type: application/json\r\n",
    'method'  => 'DELETE',
    'authorization' => 'Bearer YOUR_API_KEY',
    'content' => json_encode($data),
  ),
);
$context  = stream_context_create($options);
$result = file_get_contents($url, false, $context);
if ($result === FALSE) {
  die('Error occurred');
}
echo $result;
?>
```

#### 3. **Using Python (with `requests` library)**

Here’s a Python example using the `requests` library:

```python
import requests
import json

url = 'https://api.utho.com/v2/kubernetes/750031/destroy'
headers = {
    "Authorization": "Bearer YOUR_API_KEY",
    "Content-Type": "application/json"
}
payload = {'confirm': 'I am aware this action will delete data and cluster permanently'}

response = requests.delete(url, json=payload, headers=headers)

if response.status_code == 200:
    print("Cluster has been deleted.")
else:
    print(f"Error: {response.status_code}")
```

#### 4. **Using Node.js (with `axios` library)**

Here's an example in Node.js using `axios`:

```javascript
const axios = require('axios');

const url = 'https://api.utho.com/v2/kubernetes/750031/destroy';
const payload = {
  confirm: "I am aware this action will delete data and cluster permanently"
};
const headers = {
  'Content-Type': 'application/json',
  'Authorization': 'Bearer <your_api_token>',
};

axios.delete(url, { data: payload, headers: headers })
  .then(response => {
    console.log("Cluster has been deleted:", response.data);
  })
  .catch(error => {
    console.error("Error:", error);
  });
```
