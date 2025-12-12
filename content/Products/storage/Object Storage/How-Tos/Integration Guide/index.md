---
weight: 40
title: "Integration Guide"
title_meta: "Integration Guide"
description: "Integration Guide"
keywords: ["utho cloud", "object storage", "cloud storage"]
tags: ["utho platform","cloud"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ["/products/storage/Object Storage/How-Tos/Integration Guide"]
icon: "globe"
tab: true
---

# Utho Object Storage - Complete Integration Guide

## Table of Contents
1. [Introduction](#introduction)
2. [Getting Started](#getting-started)
3. [Configuration Basics](#configuration-basics)
4. [Command Line Tools](#command-line-tools)
5. [Programming Language SDKs](#programming-language-sdks)
6. [GUI Tools](#gui-tools)
7. [Backup and Sync Tools](#backup-and-sync-tools)
8. [Accessing Objects via Direct URLs](#accessing-objects-via-direct-urls)
9. [Best Practices](#best-practices)
10. [Troubleshooting](#troubleshooting)

---

## Introduction

Utho Object Storage is a fully S3-compatible object storage service that allows you to store and retrieve any amount of data at any time. Being S3-compatible means you can use the vast ecosystem of AWS S3 tools, SDKs, and applications with Utho Object Storage.

### Key Features
- **S3 Compatible API** - Works with all AWS S3 tools and SDKs
- **Scalable** - Store unlimited amounts of data
- **Secure** - SSL/TLS encryption and access control
- **Cost-Effective** - Competitive pricing for Indian market
- **High Performance** - Low latency access across India

---

## Getting Started

### Prerequisites

Before using Utho Object Storage, you'll need:

1. **Access Key ID** - Your unique access identifier
2. **Secret Access Key** - Your secret credential
3. **Endpoint URL** - Utho Object Storage endpoint: `https://innoida.utho.io`
4. **Region** - Your storage zone: `innoida`

> **Important**: Keep your Secret Access Key secure. Never commit it to version control or share it publicly.

### Creating Your First Bucket

A bucket is a container for storing objects. Bucket names must be:
- Globally unique across Utho Object Storage
- 3-63 characters long
- Lowercase letters, numbers, and hyphens only
- Must start with a letter or number

### Bucket URL Formats

Utho Object Storage supports two URL formats for accessing your buckets and objects:

#### 1. Virtual-Hosted-Style URLs (Subdomain)
```
https://bucketname.innoida.utho.io/object-key
```
**Example:**
```
https://my-bucket.innoida.utho.io/images/photo.jpg
```

#### 2. Path-Style URLs
```
https://innoida.utho.io/bucketname/object-key
```
**Example:**
```
https://innoida.utho.io/my-bucket/images/photo.jpg
```

Both URL formats work identically. Choose the format that best suits your application:
- **Virtual-hosted-style** is recommended for most use cases and allows for better CDN integration
- **Path-style** is useful when bucket names contain dots or when DNS configuration is challenging

> **Note**: Most S3 tools and SDKs can be configured to use either format. By default, we recommend using path-style URLs with the `force_path_style` or `use_path_style_endpoint` option enabled.

---

## Configuration Basics

### Setting Up Credentials

Most tools read credentials from environment variables or configuration files.

#### Environment Variables (All Platforms)

**Linux/macOS:**
```bash
export AWS_ACCESS_KEY_ID="your-access-key-id"
export AWS_SECRET_ACCESS_KEY="your-secret-access-key"
export AWS_ENDPOINT_URL="https://innoida.utho.io"
export AWS_REGION="innoida"
```

**Windows (Command Prompt):**
```cmd
set AWS_ACCESS_KEY_ID=your-access-key-id
set AWS_SECRET_ACCESS_KEY=your-secret-access-key
set AWS_ENDPOINT_URL=https://innoida.utho.io
set AWS_REGION=innoida
```

**Windows (PowerShell):**
```powershell
$env:AWS_ACCESS_KEY_ID="your-access-key-id"
$env:AWS_SECRET_ACCESS_KEY="your-secret-access-key"
$env:AWS_ENDPOINT_URL="https://innoida.utho.io"
$env:AWS_REGION="innoida"
```

#### AWS Credentials File

Create `~/.aws/credentials` (Linux/macOS) or `%USERPROFILE%\.aws\credentials` (Windows):

```ini
[default]
aws_access_key_id = your-access-key-id
aws_secret_access_key = your-secret-access-key
```

Create `~/.aws/config`:

```ini
[default]
region = innoida
output = json
```

---

## Command Line Tools

### AWS CLI

The official AWS Command Line Interface works perfectly with Utho Object Storage.

#### Installation

**Linux:**
```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

**macOS:**
```bash
curl "https://awscli.amazonaws.com/AWSCLIV2.pkg" -o "AWSCLIV2.pkg"
sudo installer -pkg AWSCLIV2.pkg -target /
```

**Windows:**
Download and run the MSI installer from: https://awscli.amazonaws.com/AWSCLIV2.msi

#### Configuration
```bash
aws configure
# Enter your access key, secret key, region (innoida), and output format (json)
```

#### Common Operations

**List buckets:**
```bash
aws s3 ls --endpoint-url https://innoida.utho.io
```

**Create a bucket:**
```bash
aws s3 mb s3://my-utho-bucket --endpoint-url https://innoida.utho.io
```

**Upload a file:**
```bash
aws s3 cp file.txt s3://my-utho-bucket/ --endpoint-url https://innoida.utho.io
```

**Upload a directory recursively:**
```bash
aws s3 sync ./my-folder s3://my-utho-bucket/my-folder/ --endpoint-url https://innoida.utho.io
```

**Download a file:**
```bash
aws s3 cp s3://my-utho-bucket/file.txt ./downloaded-file.txt --endpoint-url https://innoida.utho.io
```

**List objects in a bucket:**
```bash
aws s3 ls s3://my-utho-bucket/ --endpoint-url https://innoida.utho.io
```

**Delete a file:**
```bash
aws s3 rm s3://my-utho-bucket/file.txt --endpoint-url https://innoida.utho.io
```

**Delete a bucket:**
```bash
aws s3 rb s3://my-utho-bucket --endpoint-url https://innoida.utho.io --force
```

**Set bucket policy (public read):**
```bash
aws s3api put-bucket-policy --bucket my-utho-bucket --policy file://policy.json --endpoint-url https://innoida.utho.io
```

Example `policy.json`:
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::my-utho-bucket/*"
    }
  ]
}
```

**After making a bucket public, access objects via direct URLs:**

Virtual-hosted-style:
```
https://my-utho-bucket.innoida.utho.io/file.txt
https://my-utho-bucket.innoida.utho.io/images/photo.jpg
```

Path-style:
```
https://innoida.utho.io/my-utho-bucket/file.txt
https://innoida.utho.io/my-utho-bucket/images/photo.jpg
```

---

### s3cmd

A popular command-line tool for S3 storage.

#### Installation

**Linux (Debian/Ubuntu):**
```bash
sudo apt-get install s3cmd
```

**Linux (RHEL/CentOS):**
```bash
sudo yum install s3cmd
```

**macOS:**
```bash
brew install s3cmd
```

**Windows/All Platforms (pip):**
```bash
pip install s3cmd
```

#### Configuration

```bash
s3cmd --configure
```

Or create `~/.s3cfg`:

```ini
[default]
access_key = your-access-key-id
secret_key = your-secret-access-key
host_base = innoida.utho.io
host_bucket = %(bucket)s.innoida.utho.io
use_https = True
```

#### Usage Examples

```bash
# List buckets
s3cmd ls

# Create bucket
s3cmd mb s3://my-utho-bucket

# Upload file
s3cmd put file.txt s3://my-utho-bucket/

# Upload directory
s3cmd sync ./my-folder/ s3://my-utho-bucket/my-folder/

# Download file
s3cmd get s3://my-utho-bucket/file.txt

# List bucket contents
s3cmd ls s3://my-utho-bucket/

# Make file public
s3cmd setacl s3://my-utho-bucket/file.txt --acl-public

# Delete file
s3cmd del s3://my-utho-bucket/file.txt
```

---

### MinIO Client (mc)

Fast and feature-rich S3-compatible client.

#### Installation

**Linux:**
```bash
wget https://dl.min.io/client/mc/release/linux-amd64/mc
chmod +x mc
sudo mv mc /usr/local/bin/
```

**macOS:**
```bash
brew install minio/stable/mc
```

**Windows:**
```powershell
Invoke-WebRequest -Uri "https://dl.min.io/client/mc/release/windows-amd64/mc.exe" -OutFile "mc.exe"
```

#### Configuration

```bash
mc alias set utho https://innoida.utho.io your-access-key-id your-secret-access-key
```

#### Usage Examples

```bash
# List buckets
mc ls utho

# Create bucket
mc mb utho/my-utho-bucket

# Upload file
mc cp file.txt utho/my-utho-bucket/

# Upload directory
mc cp --recursive ./my-folder utho/my-utho-bucket/my-folder/

# Download file
mc cp utho/my-utho-bucket/file.txt ./

# Mirror directory (sync)
mc mirror ./my-folder utho/my-utho-bucket/my-folder/

# List objects
mc ls utho/my-utho-bucket/

# Delete file
mc rm utho/my-utho-bucket/file.txt

# Set anonymous download policy
mc anonymous set download utho/my-utho-bucket
```

---

## Programming Language SDKs

### Python (boto3)

The most popular Python SDK for S3-compatible storage.

#### Installation
```bash
pip install boto3
```

#### Basic Usage

```python
import boto3
from botocore.client import Config

# Initialize S3 client
s3_client = boto3.client(
    's3',
    endpoint_url='https://innoida.utho.io',
    aws_access_key_id='your-access-key-id',
    aws_secret_access_key='your-secret-access-key',
    config=Config(signature_version='s3v4'),
    region_name='innoida'
)

# List all buckets
response = s3_client.list_buckets()
for bucket in response['Buckets']:
    print(f"Bucket: {bucket['Name']}")

# Create a bucket
s3_client.create_bucket(Bucket='my-python-bucket')

# Upload a file
with open('file.txt', 'rb') as file_data:
    s3_client.put_object(
        Bucket='my-python-bucket',
        Key='file.txt',
        Body=file_data
    )

# Upload with metadata
s3_client.put_object(
    Bucket='my-python-bucket',
    Key='image.jpg',
    Body=open('image.jpg', 'rb'),
    ContentType='image/jpeg',
    Metadata={'uploaded-by': 'python-script'}
)

# Download a file
with open('downloaded-file.txt', 'wb') as file_data:
    s3_client.download_fileobj('my-python-bucket', 'file.txt', file_data)

# List objects in bucket
response = s3_client.list_objects_v2(Bucket='my-python-bucket')
if 'Contents' in response:
    for obj in response['Contents']:
        print(f"File: {obj['Key']}, Size: {obj['Size']} bytes")

# Generate presigned URL (temporary public link)
url = s3_client.generate_presigned_url(
    'get_object',
    Params={'Bucket': 'my-python-bucket', 'Key': 'file.txt'},
    ExpiresIn=3600  # URL valid for 1 hour
)
print(f"Presigned URL: {url}")

# Make a file publicly accessible
s3_client.put_object_acl(
    Bucket='my-python-bucket',
    Key='file.txt',
    ACL='public-read'
)

# After making file public, access via direct URLs:
# Virtual-hosted-style: https://my-python-bucket.innoida.utho.io/file.txt
# Path-style: https://innoida.utho.io/my-python-bucket/file.txt

# Delete a file
s3_client.delete_object(Bucket='my-python-bucket', Key='file.txt')

# Delete multiple files
objects_to_delete = [{'Key': 'file1.txt'}, {'Key': 'file2.txt'}]
s3_client.delete_objects(
    Bucket='my-python-bucket',
    Delete={'Objects': objects_to_delete}
)
```

#### Upload Large Files with Progress

```python
import boto3
from boto3.s3.transfer import TransferConfig
import threading

class ProgressPercentage:
    def __init__(self, filename):
        self._filename = filename
        self._size = float(os.path.getsize(filename))
        self._seen_so_far = 0
        self._lock = threading.Lock()

    def __call__(self, bytes_amount):
        with self._lock:
            self._seen_so_far += bytes_amount
            percentage = (self._seen_so_far / self._size) * 100
            print(f"\r{self._filename}: {percentage:.2f}%", end='')

s3_client = boto3.client(
    's3',
    endpoint_url='https://innoida.utho.io',
    aws_access_key_id='your-access-key-id',
    aws_secret_access_key='your-secret-access-key'
)

# Configure multipart upload for large files
config = TransferConfig(
    multipart_threshold=1024 * 25,  # 25MB
    max_concurrency=10,
    multipart_chunksize=1024 * 25,
    use_threads=True
)

# Upload with progress tracking
s3_client.upload_file(
    'large-file.zip',
    'my-python-bucket',
    'large-file.zip',
    Config=config,
    Callback=ProgressPercentage('large-file.zip')
)
```

---

### Node.js (AWS SDK v3)

Modern JavaScript/TypeScript SDK for S3 operations.

#### Installation
```bash
npm install @aws-sdk/client-s3
```

#### Basic Usage

```javascript
const { S3Client, ListBucketsCommand, CreateBucketCommand,
        PutObjectCommand, GetObjectCommand, DeleteObjectCommand } = require('@aws-sdk/client-s3');
const { getSignedUrl } = require('@aws-sdk/s3-request-presigner');
const fs = require('fs');

// Initialize S3 client
const s3Client = new S3Client({
    endpoint: 'https://innoida.utho.io',
    region: 'innoida',
    credentials: {
        accessKeyId: 'your-access-key-id',
        secretAccessKey: 'your-secret-access-key'
    },
    forcePathStyle: true
});

// List all buckets
async function listBuckets() {
    try {
        const data = await s3Client.send(new ListBucketsCommand({}));
        console.log('Buckets:', data.Buckets);
    } catch (err) {
        console.error('Error:', err);
    }
}

// Create a bucket
async function createBucket(bucketName) {
    try {
        await s3Client.send(new CreateBucketCommand({ Bucket: bucketName }));
        console.log(`Bucket ${bucketName} created successfully`);
    } catch (err) {
        console.error('Error:', err);
    }
}

// Upload a file
async function uploadFile(bucketName, fileName, filePath) {
    try {
        const fileContent = fs.readFileSync(filePath);
        await s3Client.send(new PutObjectCommand({
            Bucket: bucketName,
            Key: fileName,
            Body: fileContent,
            ContentType: 'application/octet-stream'
        }));
        console.log(`File ${fileName} uploaded successfully`);
    } catch (err) {
        console.error('Error:', err);
    }
}

// Download a file
async function downloadFile(bucketName, fileName, downloadPath) {
    try {
        const data = await s3Client.send(new GetObjectCommand({
            Bucket: bucketName,
            Key: fileName
        }));

        const stream = data.Body;
        const writeStream = fs.createWriteStream(downloadPath);
        stream.pipe(writeStream);

        writeStream.on('finish', () => {
            console.log(`File ${fileName} downloaded successfully`);
        });
    } catch (err) {
        console.error('Error:', err);
    }
}

// Generate presigned URL
async function generatePresignedUrl(bucketName, fileName, expiresIn = 3600) {
    try {
        const command = new GetObjectCommand({
            Bucket: bucketName,
            Key: fileName
        });
        const url = await getSignedUrl(s3Client, command, { expiresIn });
        console.log('Presigned URL:', url);
        return url;
    } catch (err) {
        console.error('Error:', err);
    }
}

// Make file publicly accessible
async function makeFilePublic(bucketName, fileName) {
    try {
        const { PutObjectAclCommand } = require('@aws-sdk/client-s3');
        await s3Client.send(new PutObjectAclCommand({
            Bucket: bucketName,
            Key: fileName,
            ACL: 'public-read'
        }));
        console.log(`File ${fileName} is now public`);
        console.log(`Virtual-hosted URL: https://${bucketName}.innoida.utho.io/${fileName}`);
        console.log(`Path-style URL: https://innoida.utho.io/${bucketName}/${fileName}`);
    } catch (err) {
        console.error('Error:', err);
    }
}

// Delete a file
async function deleteFile(bucketName, fileName) {
    try {
        await s3Client.send(new DeleteObjectCommand({
            Bucket: bucketName,
            Key: fileName
        }));
        console.log(`File ${fileName} deleted successfully`);
    } catch (err) {
        console.error('Error:', err);
    }
}

// Example usage
(async () => {
    await listBuckets();
    await createBucket('my-nodejs-bucket');
    await uploadFile('my-nodejs-bucket', 'test.txt', './test.txt');
    await downloadFile('my-nodejs-bucket', 'test.txt', './downloaded.txt');
    const url = await generatePresignedUrl('my-nodejs-bucket', 'test.txt');
    await deleteFile('my-nodejs-bucket', 'test.txt');
})();
```

---

### PHP (AWS SDK)

Comprehensive PHP library for S3 operations.

#### Installation

```bash
composer require aws/aws-sdk-php
```

#### Basic Usage

```php
<?php
require 'vendor/autoload.php';

use Aws\S3\S3Client;
use Aws\Exception\AwsException;

// Initialize S3 client
$s3Client = new S3Client([
    'version' => 'latest',
    'region' => 'innoida',
    'endpoint' => 'https://innoida.utho.io',
    'use_path_style_endpoint' => true,
    'credentials' => [
        'key' => 'your-access-key-id',
        'secret' => 'your-secret-access-key',
    ],
]);

// List all buckets
try {
    $buckets = $s3Client->listBuckets();
    echo "Buckets:\n";
    foreach ($buckets['Buckets'] as $bucket) {
        echo "- " . $bucket['Name'] . "\n";
    }
} catch (AwsException $e) {
    echo "Error: " . $e->getMessage() . "\n";
}

// Create a bucket
try {
    $s3Client->createBucket([
        'Bucket' => 'my-php-bucket',
    ]);
    echo "Bucket created successfully\n";
} catch (AwsException $e) {
    echo "Error: " . $e->getMessage() . "\n";
}

// Upload a file
try {
    $result = $s3Client->putObject([
        'Bucket' => 'my-php-bucket',
        'Key' => 'test.txt',
        'Body' => fopen('test.txt', 'r'),
        'ContentType' => 'text/plain',
    ]);
    echo "File uploaded successfully. ETag: " . $result['ETag'] . "\n";
} catch (AwsException $e) {
    echo "Error: " . $e->getMessage() . "\n";
}

// Upload with metadata
try {
    $result = $s3Client->putObject([
        'Bucket' => 'my-php-bucket',
        'Key' => 'image.jpg',
        'SourceFile' => './image.jpg',
        'ContentType' => 'image/jpeg',
        'Metadata' => [
            'uploaded-by' => 'php-script',
            'created-date' => date('Y-m-d'),
        ],
    ]);
    echo "Image uploaded successfully\n";
} catch (AwsException $e) {
    echo "Error: " . $e->getMessage() . "\n";
}

// Download a file
try {
    $result = $s3Client->getObject([
        'Bucket' => 'my-php-bucket',
        'Key' => 'test.txt',
    ]);
    file_put_contents('downloaded.txt', $result['Body']);
    echo "File downloaded successfully\n";
} catch (AwsException $e) {
    echo "Error: " . $e->getMessage() . "\n";
}

// List objects in bucket
try {
    $objects = $s3Client->listObjectsV2([
        'Bucket' => 'my-php-bucket',
    ]);
    echo "Objects in bucket:\n";
    if (isset($objects['Contents'])) {
        foreach ($objects['Contents'] as $object) {
            echo "- " . $object['Key'] . " (" . $object['Size'] . " bytes)\n";
        }
    }
} catch (AwsException $e) {
    echo "Error: " . $e->getMessage() . "\n";
}

// Generate presigned URL (valid for 1 hour)
try {
    $cmd = $s3Client->getCommand('GetObject', [
        'Bucket' => 'my-php-bucket',
        'Key' => 'test.txt',
    ]);
    $request = $s3Client->createPresignedRequest($cmd, '+1 hour');
    $presignedUrl = (string) $request->getUri();
    echo "Presigned URL: " . $presignedUrl . "\n";
} catch (AwsException $e) {
    echo "Error: " . $e->getMessage() . "\n";
}

// Make file publicly accessible
try {
    $s3Client->putObjectAcl([
        'Bucket' => 'my-php-bucket',
        'Key' => 'test.txt',
        'ACL' => 'public-read',
    ]);
    echo "File is now public\n";
    echo "Virtual-hosted URL: https://my-php-bucket.innoida.utho.io/test.txt\n";
    echo "Path-style URL: https://innoida.utho.io/my-php-bucket/test.txt\n";
} catch (AwsException $e) {
    echo "Error: " . $e->getMessage() . "\n";
}

// Delete a file
try {
    $s3Client->deleteObject([
        'Bucket' => 'my-php-bucket',
        'Key' => 'test.txt',
    ]);
    echo "File deleted successfully\n";
} catch (AwsException $e) {
    echo "Error: " . $e->getMessage() . "\n";
}

// Upload large file with multipart
try {
    $uploader = new \Aws\S3\MultipartUploader($s3Client, './large-file.zip', [
        'bucket' => 'my-php-bucket',
        'key' => 'large-file.zip',
    ]);

    $result = $uploader->upload();
    echo "Large file uploaded successfully\n";
} catch (AwsException $e) {
    echo "Error: " . $e->getMessage() . "\n";
}
?>
```

---

### Java (AWS SDK)

Enterprise-grade Java SDK for S3 operations.

#### Maven Dependency

```xml
<dependency>
    <groupId>software.amazon.awssdk</groupId>
    <artifactId>s3</artifactId>
    <version>2.20.0</version>
</dependency>
```

#### Basic Usage

```java
import software.amazon.awssdk.auth.credentials.AwsBasicCredentials;
import software.amazon.awssdk.auth.credentials.StaticCredentialsProvider;
import software.amazon.awssdk.core.sync.RequestBody;
import software.amazon.awssdk.regions.Region;
import software.amazon.awssdk.services.s3.S3Client;
import software.amazon.awssdk.services.s3.model.*;
import java.io.File;
import java.net.URI;
import java.nio.file.Paths;

public class UthoS3Example {

    private static final String ACCESS_KEY = "your-access-key-id";
    private static final String SECRET_KEY = "your-secret-access-key";
    private static final String ENDPOINT = "https://innoida.utho.io";

    public static void main(String[] args) {
        // Initialize S3 client
        S3Client s3Client = S3Client.builder()
            .region(Region.of("innoida"))
            .endpointOverride(URI.create(ENDPOINT))
            .credentialsProvider(StaticCredentialsProvider.create(
                AwsBasicCredentials.create(ACCESS_KEY, SECRET_KEY)))
            .build();

        // List buckets
        listBuckets(s3Client);

        // Create bucket
        createBucket(s3Client, "my-java-bucket");

        // Upload file
        uploadFile(s3Client, "my-java-bucket", "test.txt", "./test.txt");

        // Download file
        downloadFile(s3Client, "my-java-bucket", "test.txt", "./downloaded.txt");

        // List objects
        listObjects(s3Client, "my-java-bucket");

        // Delete file
        deleteFile(s3Client, "my-java-bucket", "test.txt");

        s3Client.close();
    }

    private static void listBuckets(S3Client s3Client) {
        try {
            ListBucketsResponse response = s3Client.listBuckets();
            System.out.println("Buckets:");
            for (Bucket bucket : response.buckets()) {
                System.out.println("- " + bucket.name());
            }
        } catch (S3Exception e) {
            System.err.println("Error: " + e.getMessage());
        }
    }

    private static void createBucket(S3Client s3Client, String bucketName) {
        try {
            CreateBucketRequest request = CreateBucketRequest.builder()
                .bucket(bucketName)
                .build();
            s3Client.createBucket(request);
            System.out.println("Bucket created: " + bucketName);
        } catch (S3Exception e) {
            System.err.println("Error: " + e.getMessage());
        }
    }

    private static void uploadFile(S3Client s3Client, String bucketName,
                                   String keyName, String filePath) {
        try {
            PutObjectRequest request = PutObjectRequest.builder()
                .bucket(bucketName)
                .key(keyName)
                .build();
            s3Client.putObject(request, RequestBody.fromFile(new File(filePath)));
            System.out.println("File uploaded: " + keyName);
        } catch (S3Exception e) {
            System.err.println("Error: " + e.getMessage());
        }
    }

    private static void downloadFile(S3Client s3Client, String bucketName,
                                    String keyName, String downloadPath) {
        try {
            GetObjectRequest request = GetObjectRequest.builder()
                .bucket(bucketName)
                .key(keyName)
                .build();
            s3Client.getObject(request, Paths.get(downloadPath));
            System.out.println("File downloaded: " + downloadPath);
        } catch (S3Exception e) {
            System.err.println("Error: " + e.getMessage());
        }
    }

    private static void listObjects(S3Client s3Client, String bucketName) {
        try {
            ListObjectsV2Request request = ListObjectsV2Request.builder()
                .bucket(bucketName)
                .build();
            ListObjectsV2Response response = s3Client.listObjectsV2(request);
            System.out.println("Objects in bucket:");
            for (S3Object object : response.contents()) {
                System.out.println("- " + object.key() + " (" + object.size() + " bytes)");
            }
        } catch (S3Exception e) {
            System.err.println("Error: " + e.getMessage());
        }
    }

    private static void deleteFile(S3Client s3Client, String bucketName, String keyName) {
        try {
            DeleteObjectRequest request = DeleteObjectRequest.builder()
                .bucket(bucketName)
                .key(keyName)
                .build();
            s3Client.deleteObject(request);
            System.out.println("File deleted: " + keyName);
        } catch (S3Exception e) {
            System.err.println("Error: " + e.getMessage());
        }
    }
}
```

---

### Go (AWS SDK)

High-performance Go SDK for S3 operations.

#### Installation

```bash
go get github.com/aws/aws-sdk-go/aws
go get github.com/aws/aws-sdk-go/aws/credentials
go get github.com/aws/aws-sdk-go/aws/session
go get github.com/aws/aws-sdk-go/service/s3
```

#### Basic Usage

```go
package main

import (
    "bytes"
    "fmt"
    "io/ioutil"
    "os"

    "github.com/aws/aws-sdk-go/aws"
    "github.com/aws/aws-sdk-go/aws/credentials"
    "github.com/aws/aws-sdk-go/aws/session"
    "github.com/aws/aws-sdk-go/service/s3"
)

const (
    AccessKey = "your-access-key-id"
    SecretKey = "your-secret-access-key"
    Endpoint  = "https://innoida.utho.io"
    Region    = "innoida"
)

func main() {
    // Initialize S3 session
    sess := session.Must(session.NewSession(&aws.Config{
        Credentials:      credentials.NewStaticCredentials(AccessKey, SecretKey, ""),
        Endpoint:         aws.String(Endpoint),
        Region:           aws.String(Region),
        S3ForcePathStyle: aws.Bool(true),
    }))

    svc := s3.New(sess)

    // List buckets
    listBuckets(svc)

    // Create bucket
    createBucket(svc, "my-go-bucket")

    // Upload file
    uploadFile(svc, "my-go-bucket", "test.txt", "./test.txt")

    // Download file
    downloadFile(svc, "my-go-bucket", "test.txt", "./downloaded.txt")

    // List objects
    listObjects(svc, "my-go-bucket")

    // Delete file
    deleteFile(svc, "my-go-bucket", "test.txt")
}

func listBuckets(svc *s3.S3) {
    result, err := svc.ListBuckets(&s3.ListBucketsInput{})
    if err != nil {
        fmt.Println("Error:", err)
        return
    }

    fmt.Println("Buckets:")
    for _, bucket := range result.Buckets {
        fmt.Printf("- %s\n", *bucket.Name)
    }
}

func createBucket(svc *s3.S3, bucketName string) {
    _, err := svc.CreateBucket(&s3.CreateBucketInput{
        Bucket: aws.String(bucketName),
    })
    if err != nil {
        fmt.Println("Error:", err)
        return
    }
    fmt.Printf("Bucket created: %s\n", bucketName)
}

func uploadFile(svc *s3.S3, bucketName, keyName, filePath string) {
    file, err := os.Open(filePath)
    if err != nil {
        fmt.Println("Error:", err)
        return
    }
    defer file.Close()

    fileInfo, _ := file.Stat()
    buffer := make([]byte, fileInfo.Size())
    file.Read(buffer)

    _, err = svc.PutObject(&s3.PutObjectInput{
        Bucket: aws.String(bucketName),
        Key:    aws.String(keyName),
        Body:   bytes.NewReader(buffer),
    })
    if err != nil {
        fmt.Println("Error:", err)
        return
    }
    fmt.Printf("File uploaded: %s\n", keyName)
}

func downloadFile(svc *s3.S3, bucketName, keyName, downloadPath string) {
    result, err := svc.GetObject(&s3.GetObjectInput{
        Bucket: aws.String(bucketName),
        Key:    aws.String(keyName),
    })
    if err != nil {
        fmt.Println("Error:", err)
        return
    }
    defer result.Body.Close()

    body, err := ioutil.ReadAll(result.Body)
    if err != nil {
        fmt.Println("Error:", err)
        return
    }

    err = ioutil.WriteFile(downloadPath, body, 0644)
    if err != nil {
        fmt.Println("Error:", err)
        return
    }
    fmt.Printf("File downloaded: %s\n", downloadPath)
}

func listObjects(svc *s3.S3, bucketName string) {
    result, err := svc.ListObjectsV2(&s3.ListObjectsV2Input{
        Bucket: aws.String(bucketName),
    })
    if err != nil {
        fmt.Println("Error:", err)
        return
    }

    fmt.Println("Objects in bucket:")
    for _, object := range result.Contents {
        fmt.Printf("- %s (%d bytes)\n", *object.Key, *object.Size)
    }
}

func deleteFile(svc *s3.S3, bucketName, keyName string) {
    _, err := svc.DeleteObject(&s3.DeleteObjectInput{
        Bucket: aws.String(bucketName),
        Key:    aws.String(keyName),
    })
    if err != nil {
        fmt.Println("Error:", err)
        return
    }
    fmt.Printf("File deleted: %s\n", keyName)
}
```

---

### Ruby (AWS SDK)

Elegant Ruby SDK for S3 operations.

#### Installation

```bash
gem install aws-sdk-s3
```

#### Basic Usage

```ruby
require 'aws-sdk-s3'

# Initialize S3 client
s3_client = Aws::S3::Client.new(
  access_key_id: 'your-access-key-id',
  secret_access_key: 'your-secret-access-key',
  endpoint: 'https://innoida.utho.io',
  region: 'innoida',
  force_path_style: true
)

# List buckets
resp = s3_client.list_buckets
puts "Buckets:"
resp.buckets.each do |bucket|
  puts "- #{bucket.name}"
end

# Create bucket
s3_client.create_bucket(bucket: 'my-ruby-bucket')
puts "Bucket created: my-ruby-bucket"

# Upload file
File.open('test.txt', 'rb') do |file|
  s3_client.put_object(
    bucket: 'my-ruby-bucket',
    key: 'test.txt',
    body: file
  )
end
puts "File uploaded: test.txt"

# Download file
resp = s3_client.get_object(
  bucket: 'my-ruby-bucket',
  key: 'test.txt'
)
File.open('downloaded.txt', 'wb') do |file|
  file.write(resp.body.read)
end
puts "File downloaded: downloaded.txt"

# List objects
resp = s3_client.list_objects_v2(bucket: 'my-ruby-bucket')
puts "Objects in bucket:"
resp.contents.each do |object|
  puts "- #{object.key} (#{object.size} bytes)"
end

# Generate presigned URL
signer = Aws::S3::Presigner.new(client: s3_client)
url = signer.presigned_url(
  :get_object,
  bucket: 'my-ruby-bucket',
  key: 'test.txt',
  expires_in: 3600
)
puts "Presigned URL: #{url}"

# Delete file
s3_client.delete_object(
  bucket: 'my-ruby-bucket',
  key: 'test.txt'
)
puts "File deleted: test.txt"
```

---

### .NET/C# (AWS SDK)

Comprehensive .NET SDK for S3 operations.

#### Installation

```bash
dotnet add package AWSSDK.S3
```

#### Basic Usage

```csharp
using Amazon;
using Amazon.S3;
using Amazon.S3.Model;
using Amazon.Runtime;
using System;
using System.IO;
using System.Threading.Tasks;

class Program
{
    private const string AccessKey = "your-access-key-id";
    private const string SecretKey = "your-secret-access-key";
    private const string ServiceUrl = "https://innoida.utho.io";

    static async Task Main(string[] args)
    {
        // Initialize S3 client
        var config = new AmazonS3Config
        {
            ServiceURL = ServiceUrl,
            ForcePathStyle = true,
            SignatureVersion = "4"
        };

        var credentials = new BasicAWSCredentials(AccessKey, SecretKey);
        var s3Client = new AmazonS3Client(credentials, config);

        // List buckets
        await ListBuckets(s3Client);

        // Create bucket
        await CreateBucket(s3Client, "my-dotnet-bucket");

        // Upload file
        await UploadFile(s3Client, "my-dotnet-bucket", "test.txt", "./test.txt");

        // Download file
        await DownloadFile(s3Client, "my-dotnet-bucket", "test.txt", "./downloaded.txt");

        // List objects
        await ListObjects(s3Client, "my-dotnet-bucket");

        // Delete file
        await DeleteFile(s3Client, "my-dotnet-bucket", "test.txt");
    }

    static async Task ListBuckets(IAmazonS3 s3Client)
    {
        try
        {
            var response = await s3Client.ListBucketsAsync();
            Console.WriteLine("Buckets:");
            foreach (var bucket in response.Buckets)
            {
                Console.WriteLine($"- {bucket.BucketName}");
            }
        }
        catch (AmazonS3Exception e)
        {
            Console.WriteLine($"Error: {e.Message}");
        }
    }

    static async Task CreateBucket(IAmazonS3 s3Client, string bucketName)
    {
        try
        {
            var request = new PutBucketRequest
            {
                BucketName = bucketName
            };
            await s3Client.PutBucketAsync(request);
            Console.WriteLine($"Bucket created: {bucketName}");
        }
        catch (AmazonS3Exception e)
        {
            Console.WriteLine($"Error: {e.Message}");
        }
    }

    static async Task UploadFile(IAmazonS3 s3Client, string bucketName,
                                 string keyName, string filePath)
    {
        try
        {
            var request = new PutObjectRequest
            {
                BucketName = bucketName,
                Key = keyName,
                FilePath = filePath
            };
            await s3Client.PutObjectAsync(request);
            Console.WriteLine($"File uploaded: {keyName}");
        }
        catch (AmazonS3Exception e)
        {
            Console.WriteLine($"Error: {e.Message}");
        }
    }

    static async Task DownloadFile(IAmazonS3 s3Client, string bucketName,
                                   string keyName, string downloadPath)
    {
        try
        {
            var request = new GetObjectRequest
            {
                BucketName = bucketName,
                Key = keyName
            };

            using (var response = await s3Client.GetObjectAsync(request))
            using (var responseStream = response.ResponseStream)
            using (var fileStream = File.Create(downloadPath))
            {
                await responseStream.CopyToAsync(fileStream);
            }
            Console.WriteLine($"File downloaded: {downloadPath}");
        }
        catch (AmazonS3Exception e)
        {
            Console.WriteLine($"Error: {e.Message}");
        }
    }

    static async Task ListObjects(IAmazonS3 s3Client, string bucketName)
    {
        try
        {
            var request = new ListObjectsV2Request
            {
                BucketName = bucketName
            };
            var response = await s3Client.ListObjectsV2Async(request);
            Console.WriteLine("Objects in bucket:");
            foreach (var obj in response.S3Objects)
            {
                Console.WriteLine($"- {obj.Key} ({obj.Size} bytes)");
            }
        }
        catch (AmazonS3Exception e)
        {
            Console.WriteLine($"Error: {e.Message}");
        }
    }

    static async Task DeleteFile(IAmazonS3 s3Client, string bucketName, string keyName)
    {
        try
        {
            var request = new DeleteObjectRequest
            {
                BucketName = bucketName,
                Key = keyName
            };
            await s3Client.DeleteObjectAsync(request);
            Console.WriteLine($"File deleted: {keyName}");
        }
        catch (AmazonS3Exception e)
        {
            Console.WriteLine($"Error: {e.Message}");
        }
    }
}
```

---

## GUI Tools

### Cyberduck

Free, cross-platform FTP/SFTP/S3 client with GUI.

#### Installation
- **Windows/macOS**: Download from https://cyberduck.io/
- **Linux**: Available via package managers

#### Configuration

1. Open Cyberduck
2. Click "Open Connection"
3. Select "Amazon S3" from dropdown
4. Enter:
   - **Server**: `innoida.utho.io`
   - **Access Key ID**: Your access key
   - **Secret Access Key**: Your secret key
5. Click "Connect"

---

### CloudBerry Explorer

Professional S3 client for Windows.

#### Configuration

1. Open CloudBerry Explorer
2. File → New → S3 Compatible
3. Enter:
   - **Display Name**: Utho Object Storage
   - **Service Point**: `https://innoida.utho.io`
   - **Access Key**: Your access key
   - **Secret Key**: Your secret key
   - **Signature Version**: 4
4. Click "OK"

---

### S3 Browser (Windows)

Simple GUI client for Windows users.

#### Configuration

1. Download from http://s3browser.com/
2. Accounts → Add New Account
3. Select "S3 Compatible Storage"
4. Enter:
   - **Account Name**: Utho
   - **REST Endpoint**: `innoida.utho.io`
   - **Access Key ID**: Your access key
   - **Secret Access Key**: Your secret key
5. Click "Add"

---

## Backup and Sync Tools

### Rclone

Powerful command-line sync tool for cloud storage.

#### Installation

**Linux:**
```bash
curl https://rclone.org/install.sh | sudo bash
```

**macOS:**
```bash
brew install rclone
```

**Windows:**
Download from https://rclone.org/downloads/

#### Configuration

```bash
rclone config
# Select: n (New remote)
# Name: utho
# Storage: s3
# Provider: Other
# Access Key ID: your-access-key-id
# Secret Access Key: your-secret-access-key
# Region: innoida
# Endpoint: https://innoida.utho.io
# Complete setup
```

#### Usage Examples

```bash
# List buckets
rclone lsd utho:

# List files in bucket
rclone ls utho:my-bucket

# Sync local folder to bucket
rclone sync ./my-folder utho:my-bucket/my-folder

# Copy file to bucket
rclone copy file.txt utho:my-bucket/

# Download entire bucket
rclone sync utho:my-bucket ./local-backup

# Mount bucket as drive (Linux/macOS)
rclone mount utho:my-bucket /mnt/utho-storage --daemon

# Continuous sync (watch for changes)
rclone sync ./my-folder utho:my-bucket/my-folder --progress
```

---

### Duplicati

Encrypted backup solution with S3 support.

#### Setup

1. Install Duplicati from https://www.duplicati.com/
2. Create new backup
3. Configure destination:
   - **Storage Type**: S3 compatible
   - **Server**: `innoida.utho.io`
   - **Bucket name**: Your bucket
   - **AWS Access ID**: Your access key
   - **AWS Secret Key**: Your secret key
   - **Use SSL**: Yes
4. Schedule and encrypt your backups

---

### Restic

Fast, secure backup program.

#### Installation

**Linux:**
```bash
sudo apt-get install restic
```

**macOS:**
```bash
brew install restic
```

#### Setup and Usage

```bash
# Set credentials
export AWS_ACCESS_KEY_ID="your-access-key-id"
export AWS_SECRET_ACCESS_KEY="your-secret-access-key"

# Initialize repository
restic -r s3:https://innoida.utho.io/my-backup-bucket init

# Create backup
restic -r s3:https://innoida.utho.io/my-backup-bucket backup ~/important-files

# List snapshots
restic -r s3:https://innoida.utho.io/my-backup-bucket snapshots

# Restore backup
restic -r s3:https://innoida.utho.io/my-backup-bucket restore latest --target /restore/path
```

---

## Accessing Objects via Direct URLs

Once your objects are uploaded and made public, you can access them directly via web browsers or applications using either URL format.

### Making Objects Public

#### Using AWS CLI
```bash
# Make a single file public
aws s3api put-object-acl --bucket my-bucket --key file.txt --acl public-read --endpoint-url https://innoida.utho.io

# Make entire bucket public (use with caution)
aws s3api put-bucket-acl --bucket my-bucket --acl public-read --endpoint-url https://innoida.utho.io
```

#### Using s3cmd
```bash
# Make a single file public
s3cmd setacl s3://my-bucket/file.txt --acl-public

# Make entire bucket public
s3cmd setacl s3://my-bucket --acl-public --recursive
```

#### Using MinIO Client
```bash
# Set bucket policy to allow public downloads
mc anonymous set download utho/my-bucket

# Set bucket policy to allow public uploads and downloads
mc anonymous set public utho/my-bucket
```

### URL Format Examples

Once objects are public, access them using either format:

#### For Static Website Hosting
```html
<!-- Virtual-hosted-style URLs -->
<img src="https://my-images.innoida.utho.io/logo.png" alt="Logo">
<link rel="stylesheet" href="https://my-assets.innoida.utho.io/css/style.css">
<script src="https://my-assets.innoida.utho.io/js/app.js"></script>

<!-- Path-style URLs -->
<img src="https://innoida.utho.io/my-images/logo.png" alt="Logo">
<link rel="stylesheet" href="https://innoida.utho.io/my-assets/css/style.css">
<script src="https://innoida.utho.io/my-assets/js/app.js"></script>
```

#### For Direct Downloads
```bash
# Download using curl (virtual-hosted-style)
curl -O https://my-bucket.innoida.utho.io/document.pdf

# Download using curl (path-style)
curl -O https://innoida.utho.io/my-bucket/document.pdf

# Download using wget (virtual-hosted-style)
wget https://my-bucket.innoida.utho.io/archive.zip

# Download using wget (path-style)
wget https://innoida.utho.io/my-bucket/archive.zip
```

#### For Media Streaming
```html
<!-- Video streaming (virtual-hosted-style) -->
<video controls>
  <source src="https://my-videos.innoida.utho.io/demo.mp4" type="video/mp4">
</video>

<!-- Audio streaming (path-style) -->
<audio controls>
  <source src="https://innoida.utho.io/my-audio/song.mp3" type="audio/mpeg">
</audio>
```

#### For API Integration
```javascript
// Fetch image in JavaScript
fetch('https://my-bucket.innoida.utho.io/image.jpg')
  .then(response => response.blob())
  .then(blob => {
    const img = document.createElement('img');
    img.src = URL.createObjectURL(blob);
    document.body.appendChild(img);
  });

// Fetch JSON data
fetch('https://innoida.utho.io/my-api-data/config.json')
  .then(response => response.json())
  .then(data => console.log(data));
```

### CORS Configuration for Web Access

If you're accessing objects from web browsers, you may need to configure CORS (Cross-Origin Resource Sharing):

```bash
# Create CORS configuration file (cors.json)
cat > cors.json << 'EOF'
{
  "CORSRules": [
    {
      "AllowedOrigins": ["*"],
      "AllowedHeaders": ["*"],
      "AllowedMethods": ["GET", "HEAD"],
      "MaxAgeSeconds": 3000
    }
  ]
}
EOF

# Apply CORS configuration
aws s3api put-bucket-cors --bucket my-bucket --cors-configuration file://cors.json --endpoint-url https://innoida.utho.io
```

For production environments, replace `"*"` with specific domains:
```json
{
  "CORSRules": [
    {
      "AllowedOrigins": ["https://yourdomain.com", "https://www.yourdomain.com"],
      "AllowedHeaders": ["*"],
      "AllowedMethods": ["GET", "HEAD"],
      "MaxAgeSeconds": 3000
    }
  ]
}
```

---

## Best Practices

### Security

1. **Never commit credentials to version control**
   - Use environment variables
   - Use configuration files with restricted permissions
   - Add `.aws/credentials` to `.gitignore`

2. **Use IAM-style access policies**
   - Create separate credentials for different applications
   - Use least-privilege principle
   - Rotate credentials regularly

3. **Enable HTTPS/SSL**
   - Always use `https://` endpoints
   - Verify SSL certificates

4. **Implement bucket policies**
   - Set appropriate access controls
   - Use bucket policies for fine-grained access
   - Avoid making entire buckets public unless necessary

### Performance Optimization

1. **Use multipart uploads for large files**
   - Files > 100MB should use multipart upload
   - Improves reliability and speed
   - Automatically handled by most SDKs

2. **Enable parallel transfers**
   - Use concurrent uploads/downloads
   - Configure appropriate thread counts
   - Most tools support this out of the box

3. **Implement caching**
   - Cache frequently accessed objects
   - Use CDN for public content
   - Set appropriate Cache-Control headers

4. **Choose appropriate storage class**
   - Use standard storage for frequently accessed data
   - Consider lifecycle policies for archival

### Cost Optimization

1. **Delete unnecessary files**
   - Regular cleanup of old backups
   - Remove incomplete multipart uploads
   - Use lifecycle policies

2. **Compress data before upload**
   - Reduces storage costs
   - Reduces transfer time
   - Most beneficial for text and logs

3. **Monitor usage**
   - Track storage consumption
   - Review access patterns
   - Optimize based on usage

### Naming Conventions

1. **Bucket names**
   - Use lowercase and hyphens
   - Include project/environment prefix
   - Example: `myapp-prod-assets`
   - Consider URL format: `myapp-prod-assets.innoida.utho.io` vs `innoida.utho.io/myapp-prod-assets`
   - Avoid dots in bucket names if using virtual-hosted-style URLs with SSL

2. **Object keys**
   - Use forward slashes for hierarchy
   - Include timestamps for versions
   - Example: `logs/2024/12/application.log`
   - Results in URLs like: `https://my-logs.innoida.utho.io/logs/2024/12/application.log`

3. **Consistency**
   - Maintain consistent naming across projects
   - Document naming conventions
   - Use automation to enforce standards

---

## Troubleshooting

### Common Issues

#### Issue: "Access Denied" Errors

**Solution:**
- Verify your access key and secret key are correct
- Check bucket permissions and policies
- Ensure credentials have required permissions
- Verify the bucket name is correct

#### Issue: "SignatureDoesNotMatch" Errors

**Solution:**
- Check that your secret key is correct
- Ensure endpoint URL is properly configured
- Verify signature version (use v4)
- Check system time is synchronized

#### Issue: Connection Timeouts

**Solution:**
- Verify endpoint URL is reachable
- Check firewall settings
- Ensure network connectivity
- Try increasing timeout values

#### Issue: Slow Upload/Download Speeds

**Solution:**
- Use multipart upload for large files
- Enable parallel transfers
- Check network bandwidth
- Consider geographic proximity to storage

#### Issue: "BucketAlreadyExists" Error

**Solution:**
- Bucket names are globally unique
- Choose a different bucket name
- Check if you own the bucket already

#### Issue: Cannot Access Objects via Direct URL

**Solution:**
- Verify the object is marked as public (ACL: public-read)
- Check bucket policy allows public access
- Test both URL formats:
  - Virtual-hosted: `https://bucketname.innoida.utho.io/object`
  - Path-style: `https://innoida.utho.io/bucketname/object`
- Ensure object key/path is correct (case-sensitive)
- Check for CORS issues if accessing from web browser
- Verify DNS resolution for virtual-hosted URLs: `nslookup bucketname.innoida.utho.io`

#### Issue: CORS Errors in Web Browser

**Solution:**
- Configure CORS policy on the bucket
- Check browser console for specific CORS errors
- Ensure AllowedOrigins includes your domain
- Verify AllowedMethods includes the HTTP method you're using
- Try accessing from the same origin first to rule out CORS

### Debugging Tips

1. **Enable verbose logging**
   ```bash
   # AWS CLI
   aws s3 ls --debug --endpoint-url https://innoida.utho.io

   # s3cmd
   s3cmd ls --debug
   ```

2. **Test connectivity**
   ```bash
   curl -I https://innoida.utho.io
   ```

3. **Test bucket access (both URL formats)**
   ```bash
   # Path-style
   curl -I https://innoida.utho.io/my-bucket/

   # Virtual-hosted-style
   curl -I https://my-bucket.innoida.utho.io/
   ```

4. **Verify credentials**
   ```bash
   aws s3 ls --endpoint-url https://innoida.utho.io
   ```

5. **Check SSL certificates**
   ```bash
   openssl s_client -connect innoida.utho.io:443
   ```

6. **Test public object access**
   ```bash
   # Should return 200 OK for public objects
   curl -I https://my-bucket.innoida.utho.io/public-file.txt
   curl -I https://innoida.utho.io/my-bucket/public-file.txt
   ```

### Getting Help

If you continue to experience issues:

1. **Check documentation**: Review Utho's support documentation
2. **Contact support**: Reach out to Utho support team
3. **Community forums**: Search or ask in community forums
4. **Check status**: Verify service status on Utho's status page

---

## Additional Resources

### Official Documentation
- AWS S3 API Reference: https://docs.aws.amazon.com/s3/
- Utho Support Portal: [Your support URL]

### SDK Documentation
- Python (boto3): https://boto3.amazonaws.com/v1/documentation/api/latest/index.html
- Node.js: https://docs.aws.amazon.com/AWSJavaScriptSDK/v3/latest/
- PHP: https://docs.aws.amazon.com/sdk-for-php/
- Java: https://docs.aws.amazon.com/sdk-for-java/
- Go: https://docs.aws.amazon.com/sdk-for-go/
- Ruby: https://docs.aws.amazon.com/sdk-for-ruby/
- .NET: https://docs.aws.amazon.com/sdk-for-net/

### Tools Documentation
- AWS CLI: https://docs.aws.amazon.com/cli/
- s3cmd: https://s3tools.org/s3cmd
- MinIO Client: https://min.io/docs/minio/linux/reference/minio-mc.html
- Rclone: https://rclone.org/docs/
- Cyberduck: https://docs.cyberduck.io/

---

## Conclusion

Utho Object Storage provides a powerful, S3-compatible storage solution that works seamlessly with the entire AWS ecosystem. Whether you're using command-line tools, programming SDKs, GUI applications, or backup utilities, you can leverage your existing knowledge and tools to work with Utho Object Storage.

For the latest updates, additional examples, and support, please visit the Utho support portal or contact our support team.

**Happy Storing!** 🚀

---

## Quick Reference: URL Formats

### Virtual-Hosted-Style URLs
```
Format: https://BUCKET-NAME.innoida.utho.io/OBJECT-KEY

Examples:
- https://my-website.innoida.utho.io/index.html
- https://cdn-assets.innoida.utho.io/images/logo.png
- https://user-uploads.innoida.utho.io/documents/report.pdf
- https://api-data.innoida.utho.io/v1/config.json

Use when:
- You want cleaner URLs for CDN or static hosting
- Bucket names don't contain dots
- You need better separation between buckets
```

### Path-Style URLs
```
Format: https://innoida.utho.io/BUCKET-NAME/OBJECT-KEY

Examples:
- https://innoida.utho.io/my-website/index.html
- https://innoida.utho.io/cdn-assets/images/logo.png
- https://innoida.utho.io/user-uploads/documents/report.pdf
- https://innoida.utho.io/api-data/v1/config.json

Use when:
- Bucket names contain dots
- Simpler DNS configuration needed
- Tools require path-style format
```

### Common Use Cases

#### Static Website Hosting
```html
<!-- CSS -->
<link href="https://my-site.innoida.utho.io/css/main.css" rel="stylesheet">

<!-- JavaScript -->
<script src="https://my-site.innoida.utho.io/js/app.js"></script>

<!-- Images -->
<img src="https://my-site.innoida.utho.io/images/hero.jpg" alt="Hero">
```

#### File Downloads
```bash
# Direct download link
wget https://downloads.innoida.utho.io/software/app-v1.2.3.zip

# Or path-style
wget https://innoida.utho.io/downloads/software/app-v1.2.3.zip
```

#### API Responses
```json
{
  "profileImage": "https://user-data.innoida.utho.io/profiles/user123.jpg",
  "resume": "https://innoida.utho.io/documents/resumes/user123.pdf",
  "portfolio": "https://portfolios.innoida.utho.io/user123/index.html"
}
```

#### WordPress/CMS Media
```
wp-content/uploads/ → https://wp-media.innoida.utho.io/2024/12/image.jpg
assets/ → https://cms-assets.innoida.utho.io/themes/default/style.css
```

#### Mobile App Assets
```swift
// iOS Swift
let imageURL = URL(string: "https://app-assets.innoida.utho.io/icons/icon-1024.png")

// Android Kotlin
val imageUrl = "https://innoida.utho.io/app-assets/icons/icon-1024.png"
```

### Access Control Summary

| Access Type | Configuration | URL Access |
|-------------|--------------|------------|
| **Private** | Default | Requires authentication or presigned URL |
| **Public Read** | ACL: public-read | Direct URL access for anyone |
| **Public Read/Write** | ACL: public-read-write | Anyone can read and upload (not recommended) |
| **Presigned URL** | Generated via SDK | Temporary access with expiration |

### Making Content Public - Quick Commands

```bash
# Single file public (AWS CLI)
aws s3api put-object-acl --bucket BUCKET --key FILE --acl public-read --endpoint-url https://innoida.utho.io

# Single file public (s3cmd)
s3cmd setacl s3://BUCKET/FILE --acl-public

# Entire bucket public (MinIO Client)
mc anonymous set download utho/BUCKET

# Then access via:
# https://BUCKET.innoida.utho.io/FILE
# or
# https://innoida.utho.io/BUCKET/FILE
```
