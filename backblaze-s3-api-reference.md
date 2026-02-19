# Backblaze S3-Compatible API — Complete Endpoint Reference

**Base URL:** `https://s3.<region>.backblazeb2.com`

**Authorization:** All endpoints use **AWS Signature V4** authentication from the Backblaze B2 Cloud Storage S3 Compatible API collection.

**Common Response Headers** (returned by most/all endpoints):

| Header | Example Value |
|---|---|
| `Cache-Control` | `max-age=0, no-cache, no-store` |
| `Content-Type` | `application/xml` |
| `Content-Length` | varies |
| `x-amz-request-id` | `699c79d678ac85eb` |
| `x-amz-id-2` | `aMb41pGbJOWYzrTUIY0JmFjSgZMljS2L4` |
| `Date` | RFC 1123 date |
| `Keep-Alive` | `timeout=5` |
| `Connection` | `keep-alive` |

---

## MULTIPART UPLOADS

---

### 1. S3 Abort Multipart Upload

| Field | Value |
|---|---|
| **HTTP Method** | `DELETE` |
| **Path** | `/{bucket}/{key}?uploadId={uploadId}` |
| **Description** | Aborts a multipart upload. |

**Query Parameters:**

| Parameter | Required | Description |
|---|---|---|
| `uploadId` | Yes | The upload ID obtained from S3 Create Multipart Upload. |

**Path Variables:**

| Variable | Required | Description |
|---|---|---|
| `bucket` | Yes | The name of the bucket. |
| `key` | Yes | Key of the object to be written. |

**Request Headers:** Standard AWS Signature headers only.

**Request Body:** None.

**Response:**

| Status | Body |
|---|---|
| `204 No Content` | No response body. |

**Response Headers:**

| Header | Example |
|---|---|
| `Cache-Control` | `max-age=0, no-cache, no-store` |
| `x-amz-request-id` | `56aa38410aaebdd` |
| `x-amz-id-2` | `aMa41BGZMOZMztjUfY9JmpzRnZLRjZmIQ` |
| `Date` | `Fri, 28 Jan 2022 23:14:48 GMT` |
| `Keep-Alive` | `timeout=5` |
| `Connection` | `keep-alive` |

**Error Codes:** Standard S3 errors (e.g., `NoSuchUpload`).

---

### 2. S3 Complete Multipart Upload

| Field | Value |
|---|---|
| **HTTP Method** | `POST` |
| **Path** | `/{bucket}/{key}?uploadId={uploadId}` |
| **Description** | Completes a multipart upload by assembling previously uploaded parts. |

**Query Parameters:**

| Parameter | Required | Description |
|---|---|---|
| `uploadId` | Yes | An Upload ID obtained from S3 Create Multipart Upload. |

**Path Variables:**

| Variable | Required | Description |
|---|---|---|
| `bucket` | Yes | The name of the bucket. |
| `key` | Yes | Key of the object to be written. |

**Request Headers:** Standard AWS Signature headers only.

**Request Body (XML):**

```xml
<CompleteMultipartUpload xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
   <Part>
      <ETag>3f83ebfa030f57196f30cd07c03fb632</ETag>
      <PartNumber>1</PartNumber>
   </Part>
   <Part>
      <ETag>196f30cd07c03fb6323f83ebfa030f57</ETag>
      <PartNumber>2</PartNumber>
   </Part>
   ...
</CompleteMultipartUpload>
```

**Response:**

| Status | Body |
|---|---|
| `200 OK` | XML (see below) |

**Response Body (XML):**

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<CompleteMultipartUploadResult xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
    <Bucket>my-bucket-name</Bucket>
    <ETag>"3729bf569924c845dfe90bfe6281a9ff-1"</ETag>
    <Key>object-1</Key>
    <Location>/my-bucket-name/object-1</Location>
</CompleteMultipartUploadResult>
```

**Response Headers:**

| Header | Example |
|---|---|
| `Cache-Control` | `max-age=0, no-cache, no-store` |
| `x-amz-request-id` | `56aa38410aaebdd` |
| `x-amz-version-id` | `4_z6145af89f355ac2f74ed0c1b_f416a807037a7ee2a_d20220128_m231233_c004_v0402000_t0056` |
| `x-amz-id-2` | `aMa41BGZMOZMztjUfY9JmpzRnZLRjZmIQ` |
| `Content-Length` | `353` |
| `Content-Type` | `application/xml` |
| `Date` | `Fri, 28 Jan 2022 23:14:48 GMT` |
| `Keep-Alive` | `timeout=5` |
| `Connection` | `keep-alive` |

**Error Codes:** Standard S3 errors.

---

### 3. S3 Create Multipart Upload

| Field | Value |
|---|---|
| **HTTP Method** | `POST` |
| **Path** | `/{bucket}/{key}?uploads` |
| **Description** | Initiates a multipart upload and returns an `uploadId` used to associate all parts of the upload. |

**Query Parameters:**

| Parameter | Required | Description |
|---|---|---|
| `uploads` | Yes | Must be present for a multipart upload. |

**Path Variables:**

| Variable | Required | Description |
|---|---|---|
| `bucket` | Yes | The name of the bucket. |
| `key` | Yes | Key of the object to be written. |

**Request Headers:**

| Header | Required | Description |
|---|---|---|
| `x-backblaze-live-read-enabled` | No | Set to `TRUE` (case-insensitive) to enable the Live Read feature. Any value other than `TRUE` is treated as false. |
| `x-backblaze-live-read-part-size` | No | Part size (in bytes) for Live Read. Min: 5,000,000 (5 MB). Max: 5,368,709,120 (5 GiB). Used to determine which part numbers correspond to the requested byte range. |

**Request Body:** None.

**Response:**

| Status | Body |
|---|---|
| `200 OK` | XML (see below) |

**Response Body (XML):**

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<InitiateMultipartUploadResult xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
    <Bucket>my-bucket-name</Bucket>
    <Key>object-1</Key>
    <UploadId>4_z6145af89f355ac2f74ed0c1b_f2025e1f1817e0030_d20220128_m233615_c004_v0402000_t0048</UploadId>
</InitiateMultipartUploadResult>
```

**Response Headers:**

| Header | Example |
|---|---|
| `Cache-Control` | `max-age=0, no-cache, no-store` |
| `Content-Type` | `application/xml` |
| `Content-Length` | `348` |
| `x-amz-request-id` | `90e17e227a223955` |
| `x-amz-version-id` | `4_z6145af89f355ac2f74ed0c1b_f2025e1f1817e0030_d20220128_m233615_c004_v0402000_t0048` |
| `x-amz-id-2` | `aMa41BGZMOZMztjUfY9JmpzRnZLRjZmIQ` |
| `Date` | `Fri, 28 Jan 2022 23:14:48 GMT` |
| `Keep-Alive` | `timeout=5` |
| `Connection` | `keep-alive` |

**Live Read Error Codes:**

| Error Code | Message | HTTP Status |
|---|---|---|
| `InvalidArgument` | Invalid Live Read part size provided in header `x-backblaze-live-read-part-size`. | `400 Bad Request` |
| `InvalidArgument` | Live Read part size in header `x-backblaze-live-read-part-size` is smaller than the minimum allowed size of 5000000 bytes. | `400 Bad Request` |
| `InvalidArgument` | Live Read part size in header `x-backblaze-live-read-part-size` is larger than the maximum allowed size of 5368709120 bytes. | `400 Bad Request` |

---

### 4. S3 Upload Part

| Field | Value |
|---|---|
| **HTTP Method** | `PUT` |
| **Path** | `/{bucket}/{key}?partNumber={partNumber}&uploadId={uploadId}` |
| **Description** | Uploads a part in a multipart upload. Must initiate a multipart upload first via S3 Create Multipart Upload. |

**Query Parameters:**

| Parameter | Required | Description |
|---|---|---|
| `partNumber` | Yes | The number of the part being uploaded, between 1 and 10,000. |
| `uploadId` | Yes | An Upload ID obtained from S3 Create Multipart Upload. |

**Path Variables:**

| Variable | Required | Description |
|---|---|---|
| `bucket` | Yes | The name of the bucket. |
| `key` | Yes | Key of the object to be written. |

**Request Headers:** Standard AWS Signature headers only.

**Request Body:** Raw binary object data for this part.

**Response:**

| Status | Body |
|---|---|
| `200 OK` | No response body. |

**Response Headers:**

| Header | Example |
|---|---|
| `Cache-Control` | `max-age=0, no-cache, no-store` |
| `Content-Type` | `text/plain` |
| `Content-Length` | `0` |
| `ETag` | `"85f30635602dc09bd85957a6e82a2c21"` |
| `x-amz-request-id` | `d6f7509a150075f7` |
| `x-amz-id-2` | `aMa41BGZMOZMztjUfY9JmpzRnZLRjZmIQ` |
| `Date` | `Fri, 28 Jan 2022 23:14:48 GMT` |
| `Keep-Alive` | `timeout=5` |
| `Connection` | `keep-alive` |

**Live Read Error Codes:**

| Error Code | Message | HTTP Status |
|---|---|---|
| `InvalidArgument` | This Live Read upload requires parts of size `<size>` for all but the last part. | `400 Bad Request` |

**Note:** If Live Read is enabled, the same part size must be used in an Upload Part request unless the part number in the request is larger than or equal to the highest part number previously uploaded.

---

### 5. S3 Upload Part Copy

| Field | Value |
|---|---|
| **HTTP Method** | `PUT` |
| **Path** | `/{bucket}/{key}?partNumber={partNumber}&uploadId={uploadId}` |
| **Description** | Uploads a part by copying data from an existing object as a data source. Checksum data for replicated or copied objects is maintained. |

**Query Parameters:**

| Parameter | Required | Description |
|---|---|---|
| `partNumber` | Yes | The number of the part being uploaded, between 1 and 10,000. |
| `uploadId` | Yes | An Upload ID obtained from S3 Create Multipart Upload. |

**Path Variables:**

| Variable | Required | Description |
|---|---|---|
| `bucket` | Yes | The name of the bucket. |
| `key` | Yes | Key of the object to be written. |

**Request Headers:**

| Header | Required | Description |
|---|---|---|
| `x-amz-copy-source` | Yes | The name of the source bucket and the key of the source object in the form `/{sourceBucket}/{sourceKey}`. |

**Request Body:** None.

**Response:**

| Status | Body |
|---|---|
| `200 OK` | XML (see below) |

**Response Body (XML):**

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<CopyPartResult xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
    <ETag>"85f30635602dc09bd85957a6e82a2c21"</ETag>
    <LastModified>2022-01-28T23:37:39.426Z</LastModified>
</CopyPartResult>
```

**Response Headers:**

| Header | Example |
|---|---|
| `Cache-Control` | `max-age=0, no-cache, no-store` |
| `Content-Type` | `application/xml` |
| `Content-Length` | `249` |
| `x-amz-request-id` | `90e17e227a223955` |
| `x-amz-version-id` | `4_z6145af89f355ac2f74ed0c1b_f2025e1f1817e0030_d20220128_m233615_c004_v0402000_t0048` |
| `x-amz-id-2` | `aMa41BGZMOZMztjUfY9JmpzRnZLRjZmIQ` |
| `Date` | `Fri, 28 Jan 2022 23:14:48 GMT` |
| `Keep-Alive` | `timeout=5` |
| `Connection` | `keep-alive` |

**Error Codes:** Standard S3 errors.

---

### 6. S3 List Multipart Uploads

| Field | Value |
|---|---|
| **HTTP Method** | `GET` |
| **Path** | `/{bucket}?uploads` |
| **Description** | Lists in-progress multipart uploads in a bucket. An in-progress multipart upload is one that has been initiated but has not yet been completed or aborted. |

> **Note:** The Backblaze docs show `PUT` as the method, but standard S3 and the behavior described (listing) indicate this should be `GET`. Verify against actual API behavior.

**Query Parameters:**

| Parameter | Required | Description |
|---|---|---|
| `uploads` | Yes | Must be present to list multipart uploads. |
| `delimiter` | No | Character used to group keys. |
| `encoding-type` | No | Specifies an encoding method for characters not supported in XML 1.0. |
| `key-marker` | No | With `upload-id-marker`, specifies the multipart upload after which listing should begin. |
| `max-uploads` | No | Maximum number of multipart uploads to return (up to 1000). |
| `prefix` | No | Limit response to keys with this prefix. |
| `upload-id-marker` | No | With `key-marker`, specifies the multipart upload after which listing should begin. |

**Path Variables:**

| Variable | Required | Description |
|---|---|---|
| `bucket` | Yes | The name of the bucket. |

**Request Headers:** Standard AWS Signature headers only.

**Request Body:** None.

**Response:**

| Status | Body |
|---|---|
| `200 OK` | XML (see below) |

**Response Body (XML):**

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ListMultipartUploadsResult xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
    <Bucket>my-bucket-name</Bucket>
    <MaxUploads>1000</MaxUploads>
    <IsTruncated>false</IsTruncated>
    <Upload>
        <Initiated>2022-01-28T23:27:20Z</Initiated>
        <Initiator>
            <ID>15f935cf4dcb</ID>
        </Initiator>
        <Key>object-1</Key>
        <Owner>
            <ID>15f935cf4dcb</ID>
        </Owner>
        <StorageClass>STANDARD</StorageClass>
        <UploadId>4_z6145af89f355ac2f74ed0c1b_f2006887251b080dd_d20220128_m232720_c004_v0402000_t0046</UploadId>
    </Upload>
</ListMultipartUploadsResult>
```

**Response Headers:**

| Header | Example |
|---|---|
| `Cache-Control` | `max-age=0, no-cache, no-store` |
| `Content-Type` | `application/xml` |
| `Content-Length` | `688` |
| `x-amz-request-id` | `6259899fa1d2e6b9` |
| `x-amz-id-2` | `aMa41BGZMOZMztjUfY9JmpzRnZLRjZmIQ` |
| `Date` | `Fri, 28 Jan 2022 23:14:48 GMT` |
| `Keep-Alive` | `timeout=5` |
| `Connection` | `keep-alive` |

**Error Codes:** Standard S3 errors.

---

### 7. S3 List Parts

| Field | Value |
|---|---|
| **HTTP Method** | `GET` |
| **Path** | `/{bucket}/{key}?uploadId={uploadId}` |
| **Description** | Lists parts that have been uploaded for a given multipart upload. Returns up to 1,000 results by default. Supports pagination via `part-number-marker`. |

**Query Parameters:**

| Parameter | Required | Description |
|---|---|---|
| `uploadId` | Yes | An Upload ID obtained from S3 Create Multipart Upload. |
| `max-parts` | No | The maximum number of parts that should be returned. |
| `part-number-marker` | No | B2 will start listing after this part number. |

**Path Variables:**

| Variable | Required | Description |
|---|---|---|
| `bucket` | Yes | The name of the bucket. |
| `key` | Yes | The object key. |

**Request Headers:** Standard AWS Signature headers only.

**Request Body:** None.

**Response:**

| Status | Body |
|---|---|
| `200 OK` | XML (see below) |

**Response Body (XML):**

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ListPartsResult>
    <Bucket>metadaddy-test</Bucket>
    <Key>multi123</Key>
    <UploadId>4_z0145cfc9e3f5ec0f74ed0c1b_f22652c66b7873274_d20230831_m185108_c004_v0402018_t0044_u01693507868189</UploadId>
    <PartNumberMarker>0</PartNumberMarker>
    <NextPartNumberMarker>1</NextPartNumberMarker>
    <MaxParts>1000</MaxParts>
    <IsTruncated>false</IsTruncated>
    <Part>
        <ETag>"85f30635602dc09bd85957a6e82a2c21"</ETag>
        <LastModified>2023-08-31T18:54:55.693Z</LastModified>
        <PartNumber>1</PartNumber>
        <Size>11</Size>
    </Part>
    <Initiator>
        <ID>15f935cf4dcb</ID>
        <DisplayName></DisplayName>
    </Initiator>
    <Owner>
        <ID>15f935cf4dcb</ID>
        <DisplayName></DisplayName>
    </Owner>
    <StorageClass>STANDARD</StorageClass>
</ListPartsResult>
```

**Response Headers:**

| Header | Example |
|---|---|
| `x-amz-id-2` | `aMb41pGbJOWYzrTUIY0JmFjSgZMljS2L4` |
| `Cache-Control` | `max-age=0, no-cache, no-store` |
| `Content-Type` | `application/xml` |
| `Content-Length` | `874` |
| `Date` | `Fri, 28 Jan 2022 22:46:26 GMT` |
| `Keep-Alive` | `timeout=5` |
| `Connection` | `keep-alive` |
| `x-amz-request-id` | `699c79d678ac85eb` |

**Error Codes:** Standard S3 errors.

---

## BUCKET OPERATIONS

---

### 8. S3 Create Bucket

| Field | Value |
|---|---|
| **HTTP Method** | `PUT` |
| **Path** | `/{bucket}` |
| **Description** | Creates a new bucket. The bucket name must be globally unique. You can optionally specify a canned ACL of `private` (default) or `public-read` via the `x-amz-acl` header. |

**Path Variables:**

| Variable | Required | Description |
|---|---|---|
| `bucket` | Yes | The name of the bucket to create. |

**Request Headers:**

| Header | Required | Description |
|---|---|---|
| `x-amz-acl` | No | Canned ACL. Allowed values: `private`, `public-read`. Default is `private`. |

**Request Body (XML):**

```xml
<CreateBucketConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
   <LocationConstraint><your-region></LocationConstraint>
</CreateBucketConfiguration>
```

**Response:**

| Status | Body |
|---|---|
| `200 OK` | No response body. |

**Response Headers:**

| Header | Example |
|---|---|
| `x-amz-id-2` | `aMb41pGbJOWYzrTUIY0JmFjSgZMljS2L4` |
| `Location` | `/my-bucket-name` |
| `Cache-Control` | `max-age=0, no-cache, no-store` |
| `Content-Length` | `0` |
| `x-amz-request-id` | `699c79d678ac85eb` |
| `Date` | `Fri, 28 Jan 2022 22:46:26 GMT` |
| `Keep-Alive` | `timeout=5` |
| `Connection` | `keep-alive` |

**Error Codes:** Standard S3 errors (e.g., `BucketAlreadyExists`, `BucketAlreadyOwnedByYou`).

---

### 9. S3 Delete Bucket

| Field | Value |
|---|---|
| **HTTP Method** | `DELETE` |
| **Path** | `/{bucket}` |
| **Description** | Deletes the specified bucket. Only buckets that contain no versions of any files can be deleted. |

**Path Variables:**

| Variable | Required | Description |
|---|---|---|
| `bucket` | Yes | Specifies the bucket to delete. |

**Request Headers:** Standard AWS Signature headers only.

**Request Body:** None.

**Response:**

| Status | Body |
|---|---|
| `204 No Content` | No response body. |

**Response Headers:**

| Header | Example |
|---|---|
| `x-amz-id-2` | `aMb41pGbJOWYzrTUIY0JmFjSgZMljS2L4` |
| `Cache-Control` | `max-age=0, no-cache, no-store` |
| `x-amz-request-id` | `699c79d678ac85eb` |
| `Date` | `Fri, 28 Jan 2022 22:46:26 GMT` |
| `Keep-Alive` | `timeout=5` |
| `Connection` | `keep-alive` |

**Error Codes:** Standard S3 errors (e.g., `BucketNotEmpty`, `NoSuchBucket`).

---

### 10. S3 Head Bucket

| Field | Value |
|---|---|
| **HTTP Method** | `HEAD` |
| **Path** | `/{bucket}/` |
| **Description** | Determines whether the bucket exists in your B2 account. Returns `200 OK` if it exists, `404 Not Found` if it does not. |

**Path Variables:**

| Variable | Required | Description |
|---|---|---|
| `bucket` | Yes | The name of the bucket. |

**Request Headers:** Standard AWS Signature headers only.

**Request Body:** None.

**Response:**

| Status | Body |
|---|---|
| `200 OK` | No response body. |
| `404 Not Found` | No response body. |

**Response Headers:**

| Header | Example |
|---|---|
| `x-amz-id-2` | `aMb41pGbJOWYzrTUIY0JmFjSgZMljS2L4` |
| `Cache-Control` | `max-age=0, no-cache, no-store` |
| `Content-Type` | `application/xml` |
| `Content-Length` | `0` |
| `x-amz-request-id` | `699c79d678ac85eb` |
| `Date` | `Fri, 28 Jan 2022 22:46:26 GMT` |
| `Keep-Alive` | `timeout=5` |
| `Connection` | `keep-alive` |

**Error Codes:** `404 Not Found` if bucket does not exist in account.

---

### 11. S3 List Buckets

| Field | Value |
|---|---|
| **HTTP Method** | `GET` |
| **Path** | `/` |
| **Description** | Lists buckets in your account in alphabetical order by bucket name. |

**Query Parameters:** None.

**Path Variables:** None.

**Request Headers:** Standard AWS Signature headers only.

**Request Body:** None.

**Response:**

| Status | Body |
|---|---|
| `200 OK` | XML (see below) |

**Response Body (XML):**

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ListAllMyBucketsResult xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
    <Owner>
        <ID>15f935cf4dcb</ID>
        <DisplayName></DisplayName>
    </Owner>
    <Buckets>
        <Bucket>
            <Name>metadaddy-test</Name>
            <CreationDate>2022-01-04T22:52:35.669Z</CreationDate>
        </Bucket>
    </Buckets>
</ListAllMyBucketsResult>
```

**Response Headers:**

| Header | Example |
|---|---|
| `x-amz-id-2` | `aMb41pGbJOWYzrTUIY0JmFjSgZMljS2L4` |
| `Cache-Control` | `max-age=0, no-cache, no-store` |
| `Content-Type` | `application/xml` |
| `Content-Length` | `1414` |
| `x-amz-request-id` | `699c79d678ac85eb` |
| `Date` | `Fri, 28 Jan 2022 22:46:26 GMT` |
| `Keep-Alive` | `timeout=5` |
| `Connection` | `keep-alive` |

**Error Codes:** Standard S3 errors.

---

### 12. S3 List Objects (V1)

| Field | Value |
|---|---|
| **HTTP Method** | `GET` |
| **Path** | `/{bucket}/` |
| **Description** | Returns a list of up to 1,000 objects in the bucket, sorted alphabetically by key. **Note:** S3 List Objects V2 should be used in preference to this operation, since V1 has no mechanism to page through more than 1,000 results. |

**Query Parameters:**

| Parameter | Required | Description |
|---|---|---|
| `delimiter` | No | The delimiter is used to group keys. |
| `encoding-type` | No | Specifies an encoding method for characters not supported in XML 1.0. |
| `key-marker` | No | The key that you want to start listing from. |
| `max-keys` | No | The maximum number of keys that should be returned. |
| `prefix` | No | Limit response to keys with this prefix. |

**Path Variables:**

| Variable | Required | Description |
|---|---|---|
| `bucket` | Yes | The name of the bucket. |

**Request Headers:** Standard AWS Signature headers only.

**Request Body:** None.

**Response:**

| Status | Body |
|---|---|
| `200 OK` | XML (see below) |

**Response Body (XML):**

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ListBucketResult xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
    <Contents>
        <ETag>"85f30635602dc09bd85957a6e82a2c21"</ETag>
        <Key>object-1</Key>
        <LastModified>2022-01-28T23:24:07.000Z</LastModified>
        <Owner>
            <ID>15f935cf4dcb</ID>
            <DisplayName></DisplayName>
        </Owner>
        <Size>11</Size>
        <StorageClass>STANDARD</StorageClass>
    </Contents>
    <IsTruncated>false</IsTruncated>
    <MaxKeys>1000</MaxKeys>
    <Name>my-bucket-name</Name>
    <Prefix></Prefix>
    <Marker></Marker>
</ListBucketResult>
```

**Response Headers:**

| Header | Example |
|---|---|
| `x-amz-id-2` | `aMb41pGbJOWYzrTUIY0JmFjSgZMljS2L4` |
| `Cache-Control` | `max-age=0, no-cache, no-store` |
| `Content-Type` | `application/xml` |
| `Content-Length` | `1358` |
| `x-amz-request-id` | `699c79d678ac85eb` |
| `Date` | `Fri, 28 Jan 2022 22:46:26 GMT` |
| `Keep-Alive` | `timeout=5` |
| `Connection` | `keep-alive` |

**Error Codes:** Standard S3 errors.

---

### 13. S3 List Objects V2

| Field | Value |
|---|---|
| **HTTP Method** | `GET` |
| **Path** | `/{bucket}/?list-type=2` |
| **Description** | Returns a list of up to 1,000 objects in the bucket, sorted alphabetically by key. Supports pagination via `continuation-token`. |

**Query Parameters:**

| Parameter | Required | Description |
|---|---|---|
| `list-type` | Yes | Must be set to `2` for V2 object list. |
| `continuation-token` | No | Continuation token returned by a previous request. |
| `delimiter` | No | The delimiter is used to group keys. |
| `encoding-type` | No | Specifies an encoding method for characters not supported in XML 1.0. |
| `fetch-owner` | No | Set to `true` to return the owner with each key. |
| `max-keys` | No | The maximum number of keys that should be returned. |
| `prefix` | No | Limit response to keys with this prefix. |
| `start-after` | No | B2 will start listing after this key. |

**Path Variables:**

| Variable | Required | Description |
|---|---|---|
| `bucket` | Yes | The name of the bucket. |

**Request Headers:** Standard AWS Signature headers only.

**Request Body:** None.

**Response:**

| Status | Body |
|---|---|
| `200 OK` | XML (see below) |

**Response Body (XML):**

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ListBucketResult xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
    <Contents>
        <ETag>"85f30635602dc09bd85957a6e82a2c21"</ETag>
        <Key>object-1</Key>
        <LastModified>2022-01-28T23:24:07.000Z</LastModified>
        <Size>11</Size>
        <StorageClass>STANDARD</StorageClass>
    </Contents>
    <IsTruncated>false</IsTruncated>
    <MaxKeys>1000</MaxKeys>
    <Name>my-bucket-name</Name>
    <Prefix></Prefix>
    <KeyCount>3</KeyCount>
</ListBucketResult>
```

**Response Headers:**

| Header | Example |
|---|---|
| `x-amz-id-2` | `aMb41pGbJOWYzrTUIY0JmFjSgZMljS2L4` |
| `Cache-Control` | `max-age=0, no-cache, no-store` |
| `Content-Type` | `application/xml` |
| `Content-Length` | `1042` |
| `x-amz-request-id` | `699c79d678ac85eb` |
| `Date` | `Fri, 28 Jan 2022 22:46:26 GMT` |
| `Keep-Alive` | `timeout=5` |
| `Connection` | `keep-alive` |

**Error Codes:** Standard S3 errors.

---

### 14. S3 List Object Versions

| Field | Value |
|---|---|
| **HTTP Method** | `GET` |
| **Path** | `/{bucket}/?versions` |
| **Description** | Returns metadata of the versions of objects in the bucket. |

**Query Parameters:**

| Parameter | Required | Description |
|---|---|---|
| `versions` | Yes | Must be present to retrieve object versions. |
| `delimiter` | No | The delimiter is used to group keys. |
| `encoding-type` | No | Specifies an encoding method for characters not supported in XML 1.0. |
| `key-marker` | No | The key that you want to start listing from. |
| `max-keys` | No | The maximum number of keys that should be returned. |
| `prefix` | No | Limit response to keys with this prefix. |
| `version-id-marker` | No | The object version id that you want to start listing from. |

**Path Variables:**

| Variable | Required | Description |
|---|---|---|
| `bucket` | Yes | The name of the bucket. |

**Request Headers:** Standard AWS Signature headers only.

**Request Body:** None.

**Response:**

| Status | Body |
|---|---|
| `200 OK` | XML (see below) |

**Response Body (XML):**

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ListVersionsResult xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
    <DeleteMarker>
        <IsLatest>true</IsLatest>
        <Key>object-2</Key>
        <LastModified>2022-01-28T23:40:31.000Z</LastModified>
        <Owner>
            <ID>15f935cf4dcb</ID>
            <DisplayName></DisplayName>
        </Owner>
        <VersionId>4_z6145af89f355ac2f74ed0c1b_f40057de773908ada_d20220128_m234031_c004_v0402002_t0050</VersionId>
    </DeleteMarker>
    <IsTruncated>false</IsTruncated>
    <MaxKeys>1000</MaxKeys>
    <Name>my-bucket-name</Name>
    <Version>
        <ETag>"85f30635602dc09bd85957a6e82a2c21"</ETag>
        <IsLatest>true</IsLatest>
        <Key>object-1</Key>
        <LastModified>2022-01-28T23:24:07.000Z</LastModified>
        <Owner>
            <ID>15f935cf4dcb</ID>
            <DisplayName></DisplayName>
        </Owner>
        <Size>11</Size>
        <StorageClass>STANDARD</StorageClass>
        <VersionId>4_z6145af89f355ac2f74ed0c1b_f4030346ce9e208f0_d20220128_m232407_c004_v0402005_t0021</VersionId>
    </Version>
</ListVersionsResult>
```

**Response Headers:**

| Header | Example |
|---|---|
| `x-amz-id-2` | `aMb41pGbJOWYzrTUIY0JmFjSgZMljS2L4` |
| `Cache-Control` | `max-age=0, no-cache, no-store` |
| `Content-Type` | `application/xml` |
| `Content-Length` | `2646` |
| `x-amz-request-id` | `699c79d678ac85eb` |
| `Date` | `Fri, 28 Jan 2022 22:46:26 GMT` |
| `Keep-Alive` | `timeout=5` |
| `Connection` | `keep-alive` |

**Error Codes:** Standard S3 errors.

---

### 15. S3 Get Bucket ACL

| Field | Value |
|---|---|
| **HTTP Method** | `GET` |
| **Path** | `/{bucket}/?acl` |
| **Description** | Retrieves the bucket's ACL in XML format. |

**Query Parameters:**

| Parameter | Required | Description |
|---|---|---|
| `acl` | Yes | Must be present to retrieve bucket ACL. |

**Path Variables:**

| Variable | Required | Description |
|---|---|---|
| `bucket` | Yes | The name of the bucket. |

**Request Headers:** Standard AWS Signature headers only.

**Request Body:** None.

**Response:**

| Status | Body |
|---|---|
| `200 OK` | XML (see below) |

**Response Body (XML):**

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<AccessControlPolicy xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
    <Owner>
        <ID>15f935cf4dcb</ID>
        <DisplayName></DisplayName>
    </Owner>
    <AccessControlList>
        <Grant>
            <Grantee xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="CanonicalUser">
                <ID>15f935cf4dcb</ID>
                <DisplayName></DisplayName>
            </Grantee>
            <Permission>FULL_CONTROL</Permission>
        </Grant>
        <Grant>
            <Grantee xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="Group">
                <URI>http://acs.amazonaws.com/groups/global/AllUsers</URI>
            </Grantee>
            <Permission>READ</Permission>
        </Grant>
    </AccessControlList>
</AccessControlPolicy>
```

**Response Headers:**

| Header | Example |
|---|---|
| `x-amz-id-2` | `aMb41pGbJOWYzrTUIY0JmFjSgZMljS2L4` |
| `Cache-Control` | `max-age=0, no-cache, no-store` |
| `Content-Type` | `application/xml` |
| `Content-Length` | `844` |
| `x-amz-request-id` | `699c79d678ac85eb` |
| `Date` | `Fri, 28 Jan 2022 22:46:26 GMT` |
| `Keep-Alive` | `timeout=5` |
| `Connection` | `keep-alive` |

**Error Codes:** Standard S3 errors.

---

### 16. S3 Put Bucket ACL

| Field | Value |
|---|---|
| **HTTP Method** | `PUT` |
| **Path** | `/{bucket}/?acl` |
| **Description** | Sets the ACL on an existing bucket via the `x-amz-acl` header. Setting the ACL via XML in the request body is **not** supported. |

**Query Parameters:**

| Parameter | Required | Description |
|---|---|---|
| `acl` | Yes | Must be present to put bucket ACL. |

**Path Variables:**

| Variable | Required | Description |
|---|---|---|
| `bucket` | Yes | The name of the bucket. |

**Request Headers:**

| Header | Required | Description |
|---|---|---|
| `x-amz-acl` | Yes | Allowed values: `private` or `public-read`. |

**Request Body:** None.

**Response:**

| Status | Body |
|---|---|
| `200 OK` | No response body. |

**Response Headers:**

| Header | Example |
|---|---|
| `x-amz-id-2` | `aMb41pGbJOWYzrTUIY0JmFjSgZMljS2L4` |
| `Cache-Control` | `max-age=0, no-cache, no-store` |
| `Content-Length` | `0` |
| `x-amz-request-id` | `699c79d678ac85eb` |
| `Date` | `Fri, 28 Jan 2022 22:46:26 GMT` |
| `Keep-Alive` | `timeout=5` |
| `Connection` | `keep-alive` |

**Error Codes:** Standard S3 errors.

---

### 17. S3 Get Bucket CORS

| Field | Value |
|---|---|
| **HTTP Method** | `GET` |
| **Path** | `/{bucket}/?cors` |
| **Description** | Gets the bucket's CORS configuration. |

**Query Parameters:**

| Parameter | Required | Description |
|---|---|---|
| `cors` | Yes | Must be present to retrieve bucket CORS configuration. |

**Path Variables:**

| Variable | Required | Description |
|---|---|---|
| `bucket` | Yes | The name of the bucket. |

**Request Headers:** Standard AWS Signature headers only.

**Request Body:** None.

**Response:**

| Status | Body |
|---|---|
| `200 OK` | XML (see below) |

**Response Body (XML):**

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<CORSConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
    <CORSRule>
        <AllowedOrigin>https://www.example.com</AllowedOrigin>
        <AllowedMethod>POST</AllowedMethod>
        <AllowedMethod>GET</AllowedMethod>
        <AllowedHeader>*</AllowedHeader>
        <ExposeHeader>x-amz-server-side-encryption</ExposeHeader>
        <MaxAgeSeconds>3600</MaxAgeSeconds>
    </CORSRule>
</CORSConfiguration>
```

**Response Headers:**

| Header | Example |
|---|---|
| `x-amz-id-2` | `aMb41pGbJOWYzrTUIY0JmFjSgZMljS2L4` |
| `Cache-Control` | `max-age=0, no-cache, no-store` |
| `Content-Type` | `application/xml` |
| `Content-Length` | `477` |
| `x-amz-request-id` | `699c79d678ac85eb` |
| `Date` | `Fri, 28 Jan 2022 22:46:26 GMT` |
| `Keep-Alive` | `timeout=5` |
| `Connection` | `keep-alive` |

**Error Codes:** Standard S3 errors.

---

### 18. S3 Put Bucket CORS

| Field | Value |
|---|---|
| **HTTP Method** | `PUT` |
| **Path** | `/{bucket}/?cors` |
| **Description** | Sets the bucket's CORS configuration. If the configuration exists, it is replaced. |

**Query Parameters:**

| Parameter | Required | Description |
|---|---|---|
| `cors` | Yes | Must be present to put bucket CORS configuration. |

**Path Variables:**

| Variable | Required | Description |
|---|---|---|
| `bucket` | Yes | The name of the bucket. |

**Request Headers:** Standard AWS Signature headers only.

**Request Body (XML):**

```xml
<CORSConfiguration>
    <CORSRule>
        <ID>Allow GET/POST from Example site</ID>
        <AllowedOrigin>https://www.example.com</AllowedOrigin>
        <AllowedMethod>GET</AllowedMethod>
        <AllowedMethod>POST</AllowedMethod>
        <AllowedHeader>*</AllowedHeader>
        <MaxAgeSeconds>3600</MaxAgeSeconds>
        <ExposeHeader>x-amz-server-side-encryption</ExposeHeader>
    </CORSRule>
</CORSConfiguration>
```

**Response:**

| Status | Body |
|---|---|
| `200 OK` | No response body. |

**Response Headers:**

| Header | Example |
|---|---|
| `x-amz-id-2` | `aMb41pGbJOWYzrTUIY0JmFjSgZMljS2L4` |
| `Cache-Control` | `max-age=0, no-cache, no-store` |
| `Content-Length` | `0` |
| `x-amz-request-id` | `699c79d678ac85eb` |
| `Date` | `Fri, 28 Jan 2022 22:46:26 GMT` |
| `Keep-Alive` | `timeout=5` |
| `Connection` | `keep-alive` |

**Error Codes:** Standard S3 errors.

---

### 19. S3 Delete Bucket CORS

| Field | Value |
|---|---|
| **HTTP Method** | `DELETE` |
| **Path** | `/{bucket}/?cors` |
| **Description** | Deletes CORS configuration from the bucket. |

**Query Parameters:**

| Parameter | Required | Description |
|---|---|---|
| `cors` | Yes | Must be present to delete bucket CORS configuration. |

**Path Variables:**

| Variable | Required | Description |
|---|---|---|
| `bucket` | Yes | The name of the bucket. |

**Request Headers:** Standard AWS Signature headers only.

**Request Body:** None.

**Response:**

| Status | Body |
|---|---|
| `204 No Content` | No response body. |

**Response Headers:**

| Header | Example |
|---|---|
| `x-amz-id-2` | `aMb41pGbJOWYzrTUIY0JmFjSgZMljS2L4` |
| `Cache-Control` | `max-age=0, no-cache, no-store` |
| `x-amz-request-id` | `699c79d678ac85eb` |
| `Date` | `Fri, 28 Jan 2022 22:46:26 GMT` |
| `Keep-Alive` | `timeout=5` |
| `Connection` | `keep-alive` |

**Error Codes:** Standard S3 errors.

---

### 20. S3 Get Bucket Encryption

| Field | Value |
|---|---|
| **HTTP Method** | `GET` |
| **Path** | `/{bucket}/?encryption` |
| **Description** | Retrieves the bucket's default encryption configuration. |

**Query Parameters:**

| Parameter | Required | Description |
|---|---|---|
| `encryption` | Yes | Must be present to retrieve bucket encryption configuration. |

**Path Variables:**

| Variable | Required | Description |
|---|---|---|
| `bucket` | Yes | The name of the bucket. |

**Request Headers:** Standard AWS Signature headers only.

**Request Body:** None.

**Response:**

| Status | Body |
|---|---|
| `200 OK` | XML (see below) |

**Response Body (XML):**

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ServerSideEncryptionConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
    <Rule>
        <ApplyServerSideEncryptionByDefault>
            <SSEAlgorithm>AES256</SSEAlgorithm>
        </ApplyServerSideEncryptionByDefault>
    </Rule>
</ServerSideEncryptionConfiguration>
```

**Response Headers:**

| Header | Example |
|---|---|
| `x-amz-id-2` | `aMb41pGbJOWYzrTUIY0JmFjSgZMljS2L4` |
| `Cache-Control` | `max-age=0, no-cache, no-store` |
| `Content-Type` | `application/xml` |
| `Content-Length` | `339` |
| `x-amz-request-id` | `699c79d678ac85eb` |
| `Date` | `Fri, 28 Jan 2022 22:46:26 GMT` |
| `Keep-Alive` | `timeout=5` |
| `Connection` | `keep-alive` |

**Error Codes:** Standard S3 errors.

---

### 21. S3 Put Bucket Encryption

| Field | Value |
|---|---|
| **HTTP Method** | `PUT` |
| **Path** | `/{bucket}/?encryption` |
| **Description** | Sets the bucket's default encryption configuration. See Default Bucket Encryption for more info. |

**Query Parameters:**

| Parameter | Required | Description |
|---|---|---|
| `encryption` | Yes | Must be present to put bucket encryption configuration. |

**Path Variables:**

| Variable | Required | Description |
|---|---|---|
| `bucket` | Yes | The name of the bucket. |

**Request Headers:** Standard AWS Signature headers only.

**Request Body (XML):**

```xml
<ServerSideEncryptionConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
    <Rule>
        <ApplyServerSideEncryptionByDefault>
            <SSEAlgorithm>AES256</SSEAlgorithm>
        </ApplyServerSideEncryptionByDefault>
    </Rule>
</ServerSideEncryptionConfiguration>
```

**Response:**

| Status | Body |
|---|---|
| `200 OK` | No response body. |

**Response Headers:**

| Header | Example |
|---|---|
| `x-amz-id-2` | `aMb41pGbJOWYzrTUIY0JmFjSgZMljS2L4` |
| `Cache-Control` | `max-age=0, no-cache, no-store` |
| `Content-Length` | `0` |
| `x-amz-request-id` | `699c79d678ac85eb` |
| `Date` | `Fri, 28 Jan 2022 22:46:26 GMT` |
| `Keep-Alive` | `timeout=5` |
| `Connection` | `keep-alive` |

**Error Codes:** Standard S3 errors.

---

### 22. S3 Delete Bucket Encryption

| Field | Value |
|---|---|
| **HTTP Method** | `DELETE` |
| **Path** | `/{bucket}/?encryption` |
| **Description** | Deletes default encryption configuration from the bucket. |

**Query Parameters:**

| Parameter | Required | Description |
|---|---|---|
| `encryption` | Yes | Must be present to delete bucket encryption configuration. |

**Path Variables:**

| Variable | Required | Description |
|---|---|---|
| `bucket` | Yes | The name of the bucket. |

**Request Headers:** Standard AWS Signature headers only.

**Request Body:** None.

**Response:**

| Status | Body |
|---|---|
| `204 No Content` | No response body. |

**Response Headers:**

| Header | Example |
|---|---|
| `x-amz-id-2` | `aMb41pGbJOWYzrTUIY0JmFjSgZMljS2L4` |
| `Cache-Control` | `max-age=0, no-cache, no-store` |
| `x-amz-request-id` | `699c79d678ac85eb` |
| `Date` | `Fri, 28 Jan 2022 22:46:26 GMT` |
| `Keep-Alive` | `timeout=5` |
| `Connection` | `keep-alive` |

**Error Codes:** Standard S3 errors.

---

### 23. S3 Get Bucket Location

| Field | Value |
|---|---|
| **HTTP Method** | `GET` |
| **Path** | `/{bucket}/?location` |
| **Description** | Returns the bucket's region. |

**Query Parameters:**

| Parameter | Required | Description |
|---|---|---|
| `location` | Yes | Must be present to retrieve bucket location. |

**Path Variables:**

| Variable | Required | Description |
|---|---|---|
| `bucket` | Yes | The name of the bucket. |

**Request Headers:** Standard AWS Signature headers only.

**Request Body:** None.

**Response:**

| Status | Body |
|---|---|
| `200 OK` | XML (see below) |

**Response Body (XML):**

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<LocationConstraint xmlns="http://s3.amazonaws.com/doc/2006-03-01/">us-west-004</LocationConstraint>
```

**Response Headers:**

| Header | Example |
|---|---|
| `x-amz-id-2` | `aMb41pGbJOWYzrTUIY0JmFjSgZMljS2L4` |
| `Cache-Control` | `max-age=0, no-cache, no-store` |
| `Content-Type` | `application/xml` |
| `Content-Length` | `157` |
| `x-amz-request-id` | `699c79d678ac85eb` |
| `Date` | `Fri, 28 Jan 2022 22:46:26 GMT` |
| `Keep-Alive` | `timeout=5` |
| `Connection` | `keep-alive` |

**Error Codes:** Standard S3 errors.

---

### 24. S3 Get Bucket Versioning

| Field | Value |
|---|---|
| **HTTP Method** | `GET` |
| **Path** | `/{bucket}/?versioning` |
| **Description** | Gets the versioning state of the bucket. |

**Query Parameters:**

| Parameter | Required | Description |
|---|---|---|
| `versioning` | Yes | Must be present to retrieve bucket versioning. |

**Path Variables:**

| Variable | Required | Description |
|---|---|---|
| `bucket` | Yes | The name of the bucket. |

**Request Headers:** Standard AWS Signature headers only.

**Request Body:** None.

**Response:**

| Status | Body |
|---|---|
| `200 OK` | XML (see below) |

**Response Body (XML):**

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<VersioningConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
    <Status>Enabled</Status>
</VersioningConfiguration>
```

**Response Headers:**

| Header | Example |
|---|---|
| `x-amz-id-2` | `aMb41pGbJOWYzrTUIY0JmFjSgZMljS2L4` |
| `Cache-Control` | `max-age=0, no-cache, no-store` |
| `Content-Type` | `application/xml` |
| `Content-Length` | `186` |
| `x-amz-request-id` | `699c79d678ac85eb` |
| `Date` | `Fri, 28 Jan 2022 22:46:26 GMT` |
| `Keep-Alive` | `timeout=5` |
| `Connection` | `keep-alive` |

**Error Codes:** Standard S3 errors.

---

### 25. S3 Get Object Lock Configuration

| Field | Value |
|---|---|
| **HTTP Method** | `GET` |
| **Path** | `/{bucket}/?object-lock` |
| **Description** | Gets the Object Lock configuration for a bucket. |

**Query Parameters:**

| Parameter | Required | Description |
|---|---|---|
| `object-lock` | Yes | Must be present to retrieve object lock configuration. |

**Path Variables:**

| Variable | Required | Description |
|---|---|---|
| `bucket` | Yes | The name of the bucket. |

**Request Headers:** Standard AWS Signature headers only.

**Request Body:** None.

**Response:**

| Status | Body |
|---|---|
| `200 OK` | XML (see below) |

**Response Body (XML):**

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ObjectLockConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
    <ObjectLockEnabled>Enabled</ObjectLockEnabled>
    <Rule>
        <DefaultRetention>
            <Mode>GOVERNANCE</Mode>
            <Days>14</Days>
        </DefaultRetention>
    </Rule>
</ObjectLockConfiguration>
```

**Response Headers:**

| Header | Example |
|---|---|
| `x-amz-id-2` | `aMb41pGbJOWYzrTUIY0JmFjSgZMljS2L4` |
| `Cache-Control` | `max-age=0, no-cache, no-store` |
| `Content-Type` | `application/xml` |
| `Content-Length` | `350` |
| `x-amz-request-id` | `699c79d678ac85eb` |
| `Date` | `Fri, 28 Jan 2022 22:46:26 GMT` |
| `Keep-Alive` | `timeout=5` |
| `Connection` | `keep-alive` |

**Error Codes:** Standard S3 errors.

---

### 26. S3 Put Object Lock Configuration

| Field | Value |
|---|---|
| **HTTP Method** | `PUT` |
| **Path** | `/{bucket}?object-lock` |
| **Description** | Sets the Object Lock configuration for a bucket. The specified rule will apply by default to new objects created in the bucket. |

**Query Parameters:**

| Parameter | Required | Description |
|---|---|---|
| `object-lock` | Yes | Must be present to put object lock configuration. |

**Path Variables:**

| Variable | Required | Description |
|---|---|---|
| `bucket` | Yes | The name of the bucket. |

**Request Headers:** Standard AWS Signature headers only.

**Request Body (XML):**

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ObjectLockConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
    <ObjectLockEnabled>Enabled</ObjectLockEnabled>
    <Rule>
        <DefaultRetention>
            <Mode>GOVERNANCE</Mode>
            <Days>14</Days>
        </DefaultRetention>
    </Rule>
</ObjectLockConfiguration>
```

**`ObjectLockConfiguration` Elements:**

| Element | Description |
|---|---|
| `ObjectLockEnabled` | `Enabled` to enable Object Lock on the bucket. |
| `Rule` | Contains the default retention rule. |
| `DefaultRetention` | Default retention settings for new objects. |
| `Mode` | Retention mode: `GOVERNANCE` or `COMPLIANCE`. |
| `Days` | Number of days for the retention period. |
| `Years` | (Alternative to Days) Number of years for the retention period. |

**Response:**

| Status | Body |
|---|---|
| `200 OK` | No response body. |

**Response Headers:**

| Header | Example |
|---|---|
| `x-amz-id-2` | `aMb41pGbJOWYzrTUIY0JmFjSgZMljS2L4` |
| `Cache-Control` | `max-age=0, no-cache, no-store` |
| `Content-Length` | `0` |
| `x-amz-request-id` | `699c79d678ac85eb` |
| `Date` | `Fri, 28 Jan 2022 22:46:26 GMT` |
| `Keep-Alive` | `timeout=5` |
| `Connection` | `keep-alive` |

**Error Codes:** Standard S3 errors.

---

### 27. S3 Get Lifecycle Configuration

| Field | Value |
|---|---|
| **HTTP Method** | `GET` |
| **Path** | `/{bucket}?lifecycle` |
| **Description** | Returns the lifecycle configuration information set on a bucket. |

**URI Request Parameters:**

| Parameter | Required | Description |
|---|---|---|
| `Bucket` (path) | Yes | The name of the bucket. The bucket must have versioning enabled. |
| `x-amz-expected-bucket-owner` (header) | No | The account ID of the expected bucket owner. If it does not match, the request fails with `403 Forbidden`. |

**Request Body:** None.

**B2 Compatibility Notes:**

| B2 Lifecycle Action | S3 Lifecycle Action |
|---|---|
| `hide` | `Expiration` |
| `delete` | `NoncurrentVersionExpiration` |
| `daysFromStartingToCancelingUnfinishedLargeFiles` | `AbortIncompleteMultipartUpload` |

B2 lifecycle rules always remove orphan delete markers. Each B2 lifecycle rule with a "hide" action is returned as two S3 rules with the same prefix: one `Expiration` rule with `Days`, and one `Expiration` rule with `ExpiredObjectDeleteMarker` set to `true`.

**Response:**

| Status | Body |
|---|---|
| `200 OK` | XML (see below) |
| `404 Not Found` | XML error (see below) |

**Response Body (200 OK XML):**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<LifecycleConfiguration>
   <Rule>
      <ID>string</ID>
      <Filter>
         <Prefix>string</Prefix>
      </Filter>
      <Status>Enabled</Status>
      <Expiration>
         <Days>integer</Days>
      </Expiration>
   </Rule>
   <Rule>
      <ID>string</ID>
      <Filter>
         <Prefix>string</Prefix>
      </Filter>
      <Status>Enabled</Status>
      <Expiration>
         <ExpiredObjectDeleteMarker>true</ExpiredObjectDeleteMarker>
      </Expiration>
   </Rule>
   <Rule>
      <ID>string</ID>
      <Filter>
         <Prefix>string</Prefix>
      </Filter>
      <Status>Enabled</Status>
      <NoncurrentVersionExpiration>
         <NoncurrentDays>integer</NoncurrentDays>
      </NoncurrentVersionExpiration>
   </Rule>
   <Rule>
      <ID>string</ID>
      <Filter>
         <Prefix>string</Prefix>
      </Filter>
      <Status>Enabled</Status>
      <AbortIncompleteMultipartUpload>
         <DaysAfterInitiation>integer</DaysAfterInitiation>
      </AbortIncompleteMultipartUpload>
   </Rule>
</LifecycleConfiguration>
```

**Error Response (404 Not Found XML):**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Error>
   <Code>NoSuchLifecycleConfiguration</Code>
   <Message>The lifecycle configuration does not exist.</Message>
</Error>
```

**Error Codes:**

| Error Code | Message | HTTP Status |
|---|---|---|
| `NoSuchLifecycleConfiguration` | The lifecycle configuration does not exist. | `404 Not Found` |

---

### 28. S3 Put Lifecycle Configuration

| Field | Value |
|---|---|
| **HTTP Method** | `PUT` |
| **Path** | `/{bucket}?lifecycle` |
| **Description** | Creates a new lifecycle configuration for a bucket or replaces an existing one. This operation overwrites the existing configuration — all desired rules must be included. |

**URI Request Parameters / Headers:**

| Parameter | Required | Description |
|---|---|---|
| `Bucket` (path) | Yes | The name of the bucket. The bucket must have versioning enabled. |
| `x-amz-expected-bucket-owner` (header) | No | The account ID of the expected bucket owner. Returns `403 Forbidden` if it doesn't match. |
| `x-amz-sdk-checksum-algorithm` (header) | No | Algorithm for checksum. Valid values: `CRC32`, `CRC32C`, `SHA1`, `SHA256`, `CRC64NVME`. Must have a corresponding `x-amz-checksum` or `x-amz-trailer` header. |

**Request Body (XML):**

| Element | Required | Description |
|---|---|---|
| `LifecycleConfiguration` | Yes | Root element. |
| `Rule` | Yes | A lifecycle rule. Up to 1,000 rules per configuration. |

```xml
<?xml version="1.0" encoding="UTF-8"?>
<LifecycleConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
   <Rule>
      <ID>expire-logs-after-30-days</ID>
      <Filter>
         <Prefix>logs/</Prefix>
      </Filter>
      <Status>Enabled</Status>
      <Expiration>
         <Days>30</Days>
      </Expiration>
   </Rule>
   <Rule>
      <ID>expire-logs-delete-markers</ID>
      <Filter>
         <Prefix>logs/</Prefix>
      </Filter>
      <Status>Enabled</Status>
      <Expiration>
         <ExpiredObjectDeleteMarker>true</ExpiredObjectDeleteMarker>
      </Expiration>
   </Rule>
   <Rule>
      <ID>delete-old-versions-after-90-days</ID>
      <Filter>
         <Prefix>documents/</Prefix>
      </Filter>
      <Status>Enabled</Status>
      <NoncurrentVersionExpiration>
         <NoncurrentDays>90</NoncurrentDays>
      </NoncurrentVersionExpiration>
   </Rule>
   <Rule>
      <ID>abort-incomplete-uploads</ID>
      <Filter>
         <Prefix></Prefix>
      </Filter>
      <Status>Enabled</Status>
      <AbortIncompleteMultipartUpload>
         <DaysAfterInitiation>7</DaysAfterInitiation>
      </AbortIncompleteMultipartUpload>
   </Rule>
</LifecycleConfiguration>
```

**B2 Compatibility Notes:**

| S3 Lifecycle Action | B2 Lifecycle Action |
|---|---|
| `Expiration` | `hide` |
| `NoncurrentVersionExpiration` | `delete` |
| `AbortIncompleteMultipartUpload` | `daysFromStartingToCancelingUnfinishedLargeFiles` |

**Supported Features:**

- `Expiration` with `Days` — Hide the current version after N days.
- `Expiration` with `ExpiredObjectDeleteMarker` set to `true` — Remove orphan delete markers.
- `NoncurrentVersionExpiration` with `NoncurrentDays` — Delete noncurrent versions after N days.
- `AbortIncompleteMultipartUpload` with `DaysAfterInitiation` — Abort incomplete multipart uploads after N days.
- `Filter` with `Prefix` — Apply rules to objects matching a key prefix.
- `ID` — Optional identifier.
- `Status` set to `Enabled` — Rules must be enabled.

**Unsupported Features (return `400 Bad Request`):**

- `Transition` and `NoncurrentVersionTransition` — Storage class transitions.
- `Expiration` with `Date` — Date-based expiration (use `Days` instead).
- `ExpiredObjectDeleteMarker` set to `false` — B2 always removes orphan delete markers.
- `NewerNoncurrentVersions` — Keeping a specific number of noncurrent versions.
- `Status` set to `Disabled` — Disabled rules.
- Filter sub-elements other than `Prefix` (`Tag`, `ObjectSizeGreaterThan`, `ObjectSizeLessThan`, `And`).
- Overlapping prefix filters.
- Top-level `Prefix` (deprecated) — Use `Filter` with `Prefix` instead.
- Unversioned or versioning-suspended buckets.

**Expiration Rule Pairing Requirement:**

B2 always removes orphan delete markers, so S3 `Expiration` rules must be defined in pairs per prefix:
1. One rule with `Expiration` specifying `Days`.
2. One rule with the same prefix with `Expiration` and `ExpiredObjectDeleteMarker` set to `true`.

These two S3 rules map to a single B2 lifecycle rule.

**Response:**

| Status | Body |
|---|---|
| `200 OK` | No response body. |

**Response Headers (200 OK):**

```
x-amz-id-2: <value>
x-amz-request-id: <value>
Date: <date>
Content-Length: 0
```

**Error Codes:**

| Error Code | Message | HTTP Status |
|---|---|---|
| `NotSupported` | The specified lifecycle configuration feature is not supported. | `400 Bad Request` |

---

### 29. S3 Delete Lifecycle Configuration

| Field | Value |
|---|---|
| **HTTP Method** | `DELETE` |
| **Path** | `/{bucket}?lifecycle` |
| **Description** | Deletes the lifecycle configuration from a bucket. Objects no longer expire and B2 no longer automatically hides or deletes objects based on the deleted lifecycle rules. |

**URI Request Parameters / Headers:**

| Parameter | Required | Description |
|---|---|---|
| `Bucket` (path) | Yes | The bucket name of the lifecycle to delete. |
| `x-amz-expected-bucket-owner` (header) | No | The account ID of the expected bucket owner. Returns `403 Forbidden` if it doesn't match. |

**Request Body:** None.

**Response:**

| Status | Body |
|---|---|
| `204 No Content` | No response body. |

**Response Headers:**

```
x-amz-id-2: <value>
x-amz-request-id: <value>
Date: <date>
Connection: keep-alive
```

**Error Codes:** Standard S3 errors.

---

### 30. S3 Put Bucket Logging

| Field | Value |
|---|---|
| **HTTP Method** | `PUT` |
| **Path** | `/{bucket}/?logging` |
| **Description** | Configures Bucket Access Logs for a bucket. You must be the bucket owner. Destination bucket must be a B2 Cloud Storage bucket in the same account as the source bucket. |

**URI Request Parameters / Headers:**

| Parameter | Required | Description |
|---|---|---|
| `bucket` (path) | Yes | The name of the bucket for which to set the logging parameters. |
| `Content-MD5` (header) | No | MD5 hash of the request body. |
| `x-amz-expected-bucket-owner` (header) | No | The account ID of the expected bucket owner. |
| `x-amz-sdk-checksum-algorithm` (header) | No | The algorithm used to create the checksum. Must have a corresponding `x-amz-checksum` or `x-amz-trailer` header. |

**Request Body (XML):**

| Element | Required | Description |
|---|---|---|
| `BucketLoggingStatus` | Yes | Root level tag. |
| `LoggingEnabled` | No | Describes where logs are stored and the key prefix. |
| `TargetBucket` | Yes (within LoggingEnabled) | The destination bucket name for logs. |
| `TargetPrefix` | No | The prefix for all log object keys. |

```xml
<BucketLoggingStatus xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
   <LoggingEnabled>
      <TargetBucket>target-bucket</TargetBucket>
      <TargetPrefix>prefix/</TargetPrefix>
   </LoggingEnabled>
</BucketLoggingStatus>
```

**Response:**

| Status | Body |
|---|---|
| `200 OK` | No response body. |

**Response Headers:**

```
x-amz-id-2: <value>
x-amz-request-id: <value>
Date: <date>
```

**Error Codes:** Standard S3 errors.

---

### 31. S3 Get Bucket Logging

| Field | Value |
|---|---|
| **HTTP Method** | `GET` |
| **Path** | `/{bucket}/?logging` |
| **Description** | Returns the logging status of a bucket with Bucket Access Logs enabled and the permissions users have to view and modify that status. |

**URI Request Parameters / Headers:**

| Parameter | Required | Description |
|---|---|---|
| `Bucket` (path) | Yes | The bucket name for which to get the logging information. |
| `x-amz-expected-bucket-owner` (header) | No | The account ID of the expected bucket owner. |

**Request Body:** None.

**Response:**

| Status | Body |
|---|---|
| `200 OK` | XML (see below) |

**Response Body (XML):**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<BucketLoggingStatus>
   <LoggingEnabled>
      <TargetBucket>string</TargetBucket>
      <TargetObjectKeyFormat>
         <PartitionedPrefix>
            <PartitionDateSource>string</PartitionDateSource>
         </PartitionedPrefix>
         <SimplePrefix>
         </SimplePrefix>
      </TargetObjectKeyFormat>
      <TargetPrefix>string</TargetPrefix>
   </LoggingEnabled>
</BucketLoggingStatus>
```

**`BucketLoggingStatus` Elements:**

| Element | Description |
|---|---|
| `LoggingEnabled` | Container for logging information. |
| `TargetBucket` | The bucket where logs are stored. |
| `TargetPrefix` | Prefix for log object keys. |
| `TargetObjectKeyFormat` | Key format for log objects. |
| `PartitionedPrefix` | Partitioned prefix configuration. |
| `PartitionDateSource` | Source for partition date. |
| `SimplePrefix` | Simple prefix configuration (empty element). |

**Error Codes:** Standard S3 errors.

---

## QUICK REFERENCE TABLE

| # | Endpoint | Method | Path | Status |
|---|---|---|---|---|
| 1 | Abort Multipart Upload | `DELETE` | `/{bucket}/{key}?uploadId={id}` | `204` |
| 2 | Complete Multipart Upload | `POST` | `/{bucket}/{key}?uploadId={id}` | `200` |
| 3 | Create Multipart Upload | `POST` | `/{bucket}/{key}?uploads` | `200` |
| 4 | Upload Part | `PUT` | `/{bucket}/{key}?partNumber={n}&uploadId={id}` | `200` |
| 5 | Upload Part Copy | `PUT` | `/{bucket}/{key}?partNumber={n}&uploadId={id}` | `200` |
| 6 | List Multipart Uploads | `GET` | `/{bucket}?uploads` | `200` |
| 7 | List Parts | `GET` | `/{bucket}/{key}?uploadId={id}` | `200` |
| 8 | Create Bucket | `PUT` | `/{bucket}` | `200` |
| 9 | Delete Bucket | `DELETE` | `/{bucket}` | `204` |
| 10 | Head Bucket | `HEAD` | `/{bucket}/` | `200`/`404` |
| 11 | List Buckets | `GET` | `/` | `200` |
| 12 | List Objects (V1) | `GET` | `/{bucket}/` | `200` |
| 13 | List Objects V2 | `GET` | `/{bucket}/?list-type=2` | `200` |
| 14 | List Object Versions | `GET` | `/{bucket}/?versions` | `200` |
| 15 | Get Bucket ACL | `GET` | `/{bucket}/?acl` | `200` |
| 16 | Put Bucket ACL | `PUT` | `/{bucket}/?acl` | `200` |
| 17 | Get Bucket CORS | `GET` | `/{bucket}/?cors` | `200` |
| 18 | Put Bucket CORS | `PUT` | `/{bucket}/?cors` | `200` |
| 19 | Delete Bucket CORS | `DELETE` | `/{bucket}/?cors` | `204` |
| 20 | Get Bucket Encryption | `GET` | `/{bucket}/?encryption` | `200` |
| 21 | Put Bucket Encryption | `PUT` | `/{bucket}/?encryption` | `200` |
| 22 | Delete Bucket Encryption | `DELETE` | `/{bucket}/?encryption` | `204` |
| 23 | Get Bucket Location | `GET` | `/{bucket}/?location` | `200` |
| 24 | Get Bucket Versioning | `GET` | `/{bucket}/?versioning` | `200` |
| 25 | Get Object Lock Config | `GET` | `/{bucket}/?object-lock` | `200` |
| 26 | Put Object Lock Config | `PUT` | `/{bucket}?object-lock` | `200` |
| 27 | Get Lifecycle Config | `GET` | `/{bucket}?lifecycle` | `200`/`404` |
| 28 | Put Lifecycle Config | `PUT` | `/{bucket}?lifecycle` | `200` |
| 29 | Delete Lifecycle Config | `DELETE` | `/{bucket}?lifecycle` | `204` |
| 30 | Put Bucket Logging | `PUT` | `/{bucket}/?logging` | `200` |
| 31 | Get Bucket Logging | `GET` | `/{bucket}/?logging` | `200` |
