# Backblaze B2 Native API — Endpoint Details

---

## 1. b2_download_file_by_id

**HTTP Method:** `GET`
**Path:** `/b2api/v4/b2_download_file_by_id`
**Description:** Downloads one file from B2 by its file ID. The response body is the raw file content (not JSON). Also supports `POST` with a JSON body containing the parameters (`Content-Type: application/json`).

### Request Parameters

#### Header Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `Authorization` | string | Conditional | Account authorization token from `b2_authorize_account`. Must have `readFiles` capability. Required for private buckets; optional for public buckets. Must be in request headers. |
| `X-Bz-Server-Side-Encryption-Customer-Algorithm` | string | No | Algorithm for SSE-C encryption. Only supported value: `AES256`. |
| `X-Bz-Server-Side-Encryption-Customer-Key` | string | No | Base64-encoded AES256 encryption key for SSE-C. |
| `X-Bz-Server-Side-Encryption-Customer-Key-Md5` | string | No | Base64-encoded MD5 digest of the SSE-C encryption key. |
| `Range` | string | No | Byte-range request (e.g. `bytes=0-99`). Only single-range requests are supported. Returns `206 Partial Content` when partial, `200 OK` when whole file returned. Must be in request headers. |

#### Query Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `fileId` | string | **Yes** | The file ID returned from `b2_upload_file` or found via `b2_list_file_versions`. |
| `b2ContentDisposition` | string | No | Overrides the `Content-Disposition` header value. Must match RFC 6266. Requires authorization token. |
| `b2ContentLanguage` | string | No | Overrides the `Content-Language` header value. Must match RFC 2616. Requires authorization token. |
| `b2Expires` | string | No | Overrides the `Expires` header value. Must match RFC 2616. Requires authorization token. |
| `b2CacheControl` | string | No | Overrides the `Cache-Control` header value. Must match RFC 2616. Requires authorization token. |
| `b2ContentEncoding` | string | No | Overrides the `Content-Encoding` header value. Must match RFC 2616. Requires authorization token. |
| `b2ContentType` | string | No | Overrides the `Content-Type` header value. Must match RFC 2616. Requires authorization token. |

### Response — `200 OK`

The response body is the raw file content. Metadata is returned via response headers.

#### Response Headers

| Name | Type | Description |
|------|------|-------------|
| `Content-Length` | integer | Size of the message body in bytes. |
| `Content-Type` | string | Original media type of the resource. |
| `X-Bz-File-Id` | string | File ID. |
| `X-Bz-File-Name` | string | File name (percent-encoded). |
| `X-Bz-Content-Sha1` | string | SHA1 checksum of the file content. |
| `X-Bz-Info-*` | string | Custom file info headers provided at upload. |
| `X-Bz-Upload-Timestamp` | integer | Timestamp (ms since epoch) of when the upload began. |
| `Content-Disposition` | string | From b2-content-disposition at upload or download request override. |
| `Content-Language` | string | From b2-content-language at upload or download request override. |
| `Expires` | integer | From b2-expires at upload or download request override. |
| `Cache-Control` | string | From download request, upload value, or bucket default. |
| `Content-Encoding` | string | From b2-content-encoding at upload or download request override. |
| `X-Bz-Server-Side-Encryption` | string | If SSE-B2 encrypted: the encryption algorithm. Otherwise omitted. |
| `X-Bz-Server-Side-Encryption-Customer-Algorithm` | string | If SSE-C encrypted: the algorithm. Otherwise omitted. |
| `X-Bz-Server-Side-Encryption-Customer-Key-Md5` | string | If SSE-C encrypted: MD5 of the customer key. Otherwise omitted. |
| `X-Bz-File-Retention-Mode` | string | Object Lock retention mode (`governance` or `compliance`). Requires `readFileRetentions` capability. |
| `X-Bz-File-Retention-Retain-Until-Timestamp` | integer | Object Lock retention timestamp (ms since epoch). Requires `readFileRetentions` capability. |
| `X-Bz-File-Legal-Hold` | string | Object Lock legal hold status (`on` or `off`). Requires `readFileLegalHolds` capability. |
| `X-Bz-Client-Unauthorized-To-Read` | string | Lists header names the client lacks capability to read (e.g. `X-Bz-File-Retention-Mode,X-Bz-File-Retention-Retain-Until-Timestamp`). |

### Response — `400 Bad Request`

**Content-Type:** `application/json`

| Code | Description |
|------|-------------|
| `bad_bucket_id` | The requested bucket ID does not match an existing bucket. |
| `bad_request` | Wrong fields or illegal values. |

```json
{
  "status": 400,
  "code": "invalid_bucket_name",
  "message": "bucket name is too long"
}
```

#### Error Response Schema

| Field | Type | Description | Example |
|-------|------|-------------|---------|
| `status` | integer | Numeric HTTP status code. | `400` |
| `code` | string | Single-identifier error code. | `"invalid_bucket_name"` |
| `message` | string | Human-readable error message. | `"bucket name is too long"` |

### Response — `401 Unauthorized`

| Code | Description |
|------|-------------|
| `bad_auth_token` | Auth token is not valid. Re-call `b2_authorize_account`. |
| `expired_auth_token` | Auth token has expired. Re-call `b2_authorize_account`. |
| `unauthorized` | Auth token is valid but does not authorize this call with these parameters. |

### Response — `403 Forbidden`

| Code | Description |
|------|-------------|
| `access_denied` | The provided customer-managed encryption key is wrong. |
| `download_cap_exceeded` | Usage cap exceeded. |
| `transaction_cap_exceeded` | Transaction cap exceeded. |

### Response — `404 Not Found`

| Code | Description |
|------|-------------|
| `not_found` | File is not in B2 Cloud Storage. |

### Response — `416 Range Not Satisfiable`

| Code | Description |
|------|-------------|
| `range_not_satisfiable` | The Range header is valid but cannot be satisfied for the file. |

---

## 2. b2_download_file_by_name

**HTTP Method:** `GET`
**Path:** `/file/{BUCKET_NAME}/{FILE_NAME}`
**Description:** Downloads one file using a bucket name and file name. The base URL comes from `b2_authorize_account` (e.g. `https://f345.backblazeb2.com`). The response body is the raw file content (not JSON). Also supports `POST` with a JSON body.

### Request Parameters

#### Header Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `Authorization` | string | Conditional | Account authorization token from `b2_authorize_account` (must have `readFiles` capability), or a download authorization token from `b2_get_download_authorization`. Required for private buckets; optional for public. |
| `X-Bz-Server-Side-Encryption-Customer-Algorithm` | string | No | Algorithm for SSE-C encryption. Only supported value: `AES256`. |
| `X-Bz-Server-Side-Encryption-Customer-Key` | string | No | Base64-encoded AES256 encryption key for SSE-C. |
| `X-Bz-Server-Side-Encryption-Customer-Key-Md5` | string | No | Base64-encoded MD5 digest of the SSE-C encryption key. |
| `Range` | string | No | Byte-range request (e.g. `bytes=0-99`). Only single-range supported. Returns `206 Partial Content` for partial, `200 OK` for whole file. Must be in request headers. |

#### Path Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `BUCKET_NAME` | string | **Yes** | The name of the bucket. Bucket names can consist of letters, digits, and `-`. Cannot start with `b2-`. |
| `FILE_NAME` | string | **Yes** | The name of the file (may include `/` characters). |

#### Query Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `Authorization` | string | No | Alternative to the header. Account auth token or download auth token. Example: `https://f345.backblazeb2.com/file/photos/cute/kitten.jpg?Authorization=<token>` |
| `b2ContentDisposition` | string | No | Overrides `Content-Disposition`. Must match RFC 6266. Requires auth token. If token from `b2_get_download_authorization`, must match the value given to that call. |
| `b2ContentLanguage` | string | No | Overrides `Content-Language`. Must match RFC 2616. Requires auth token. |
| `b2Expires` | string | No | Overrides `Expires`. Must match RFC 2616. Requires auth token. |
| `b2CacheControl` | string | No | Overrides `Cache-Control`. Must match RFC 2616. Requires auth token. |
| `b2ContentEncoding` | string | No | Overrides `Content-Encoding`. Must match RFC 2616. Requires auth token. |
| `b2ContentType` | string | No | Overrides `Content-Type`. Must match RFC 2616. Requires auth token. |

### Response — `200 OK`

The response body is the raw file content. Metadata is returned via response headers.

#### Response Headers

| Name | Type | Description |
|------|------|-------------|
| `Content-Length` | integer | Size of the message body in bytes. |
| `Content-Type` | string | Original media type of the resource. |
| `X-Bz-File-Id` | string | File ID. |
| `X-Bz-File-Name` | string | File name (percent-encoded). |
| `X-Bz-Content-Sha1` | string | SHA1 checksum of the file content. |
| `X-Bz-Info-*` | string | Custom file info headers provided at upload. |
| `X-Bz-Upload-Timestamp` | integer | Timestamp (ms since epoch) of when the upload began. |
| `Content-Disposition` | string | From b2-content-disposition at upload or download request override. |
| `Content-Language` | string | From b2-content-language at upload or download request override. |
| `Expires` | integer | From b2-expires at upload or download request override. |
| `Cache-Control` | string | From download request, upload value, or bucket default. |
| `Content-Encoding` | string | From b2-content-encoding at upload or download request override. |
| `X-Bz-Server-Side-Encryption` | string | If SSE-B2 encrypted: algorithm. Otherwise omitted. |
| `X-Bz-Server-Side-Encryption-Customer-Algorithm` | string | If SSE-C encrypted: algorithm. Otherwise omitted. |
| `X-Bz-Server-Side-Encryption-Customer-Key-Md5` | string | If SSE-C encrypted: MD5 of customer key. Otherwise omitted. |
| `X-Bz-File-Retention-Mode` | string | Object Lock retention mode (`governance` or `compliance`). Requires `readFileRetentions`. |
| `X-Bz-File-Retention-Retain-Until-Timestamp` | integer | Object Lock retention timestamp (ms since epoch). Requires `readFileRetentions`. |
| `X-Bz-File-Legal-Hold` | string | Object Lock legal hold status (`on` or `off`). Requires `readFileLegalHolds`. |
| `X-Bz-Client-Unauthorized-To-Read` | string | Lists headers the client lacks capability to read. |

### Response — `400 Bad Request`

**Content-Type:** `application/json`

| Code | Description |
|------|-------------|
| `bad_bucket_id` | The requested bucket ID does not match an existing bucket. |
| `bad_request` | Wrong fields or illegal values. |

#### Error Response Schema

| Field | Type | Description | Example |
|-------|------|-------------|---------|
| `status` | integer | Numeric HTTP status code. | `400` |
| `code` | string | Single-identifier error code. | `"invalid_bucket_name"` |
| `message` | string | Human-readable error message. | `"bucket name is too long"` |

### Response — `401 Unauthorized`

| Code | Description |
|------|-------------|
| `bad_auth_token` | Auth token is not valid. |
| `expired_auth_token` | Auth token has expired. |
| `unauthorized` | Auth token valid but does not authorize this call. |

### Response — `403 Forbidden`

| Code | Description |
|------|-------------|
| `access_denied` | Customer-managed encryption key is wrong. |
| `download_cap_exceeded` | Usage cap exceeded. |
| `transaction_cap_exceeded` | Transaction cap exceeded. |

### Response — `404 Not Found`

| Code | Description |
|------|-------------|
| `not_found` | File is not in B2 Cloud Storage. |

### Response — `416 Range Not Satisfiable`

| Code | Description |
|------|-------------|
| `range_not_satisfiable` | The Range header is valid but cannot be satisfied for the file. |

---

## 3. b2_finish_large_file

**HTTP Method:** `POST`
**Path:** `/b2api/v4/b2_finish_large_file`
**Description:** Converts the parts that have been uploaded into a single B2 file. After uploading all parts, use this call to combine them into one large file. Also supports `GET` by converting body parameters to query parameters.

### Request Parameters

#### Header Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `Authorization` | string | **Yes** | Account authorization token from `b2_authorize_account`. Must have `writeFiles` capability. |

#### Body Parameters (`application/json`)

| Field | Type | Required | Description | Example |
|-------|------|----------|-------------|---------|
| `fileId` | string | **Yes** | The ID returned by `b2_start_large_file`. | `"4_zb2f6f21365e1d29f6c580f18_f20150c1fda5f4c8d_d20180919_m215912_c002_v0001110_t0050"` |
| `partSha1Array` | array of string | **Yes** | JSON array of hex SHA1 checksums of the parts, in order. Part 1's SHA1 is at index 0. | `["40d2b810f4b3978444b98d44a01cbc2680432726", "956a86f77f2fd6181fcec963d3244689b720d845"]` |

#### Request Body Example

```json
{
  "fileId": "4_zb2f6f21365e1d29f6c580f18_f20150c1fda5f4c8d_d20180919_m215912_c002_v0001110_t0050",
  "partSha1Array": [
    "40d2b810f4b3978444b98d44a01cbc2680432726",
    "956a86f77f2fd6181fcec963d3244689b720d845"
  ]
}
```

### Response — `200 OK`

**Content-Type:** `application/json`

| Field | Type | Description | Example |
|-------|------|-------------|---------|
| `accountId` | string | The account that owns the file. | `"ACCOUNT_ID"` |
| `action` | string | One of `"start"`, `"upload"`, `"hide"`, `"folder"`, or future values. `"upload"` = uploaded file. `"start"` = large file started but not finished. `"hide"` = hidden file version. `"folder"` = virtual folder. | `"upload"` |
| `bucketId` | string | The bucket the file is in. | `"e73ede9c9c8412db49f60715"` |
| `contentLength` | integer | Number of bytes stored. 0 for `"start"`, `"hide"`, `"folder"`. | `208158542` |
| `contentSha1` | string | SHA1 of stored bytes (40-digit hex). `"none"` for large files. `null` for `"hide"` / `"folder"`. | `"none"` |
| `contentMd5` | string | MD5 of stored bytes (32-digit hex). `null` for large files, `"hide"`, `"folder"`, or files without MD5. | `"142be8357f26eda2ec6775cce257753b"` |
| `contentType` | string | MIME type for `"upload"` / `"start"`. `"application/x-bz-hide-marker"` for `"hide"`. `null` for `"folder"`. | `"b2/x-auto"` |
| `fileId` | string | Unique identifier for this file version. `null` for `"folder"`. | `"4_ze73ede9c9c8412db49f60715_f200b4e93fbae6252_d20150824_m224353_c900_v8881000_t0001"` |
| `fileInfo` | object | Custom key/value pairs uploaded with the file. | `{"author": "unknown"}` |
| `fileName` | string | The file name. | `"bigfile.dat"` |
| `fileRetention` | object | Object Lock retention settings (requires `readFileRetentions`). Omitted for `"hide"` / `"folder"`. | `{"isClientAuthorizedToRead": true, "value": {"mode": null, "retainUntilTimestamp": null}}` |
| `fileRetention.isClientAuthorizedToRead` | boolean | Whether the client can read file retention info. | `true` |
| `fileRetention.value` | object | Retention settings. | |
| `fileRetention.value.mode` | string | Retention mode (`"governance"`, `"compliance"`, or `null`). | `null` |
| `fileRetention.value.retainUntilTimestamp` | integer | Retain-until timestamp (ms since epoch) or `null`. | `null` |
| `legalHold` | object | Object Lock legal hold status (requires `readFileLegalHolds`). Omitted for `"hide"` / `"folder"`. | `{"isClientAuthorizedToRead": true, "value": null}` |
| `legalHold.isClientAuthorizedToRead` | boolean | Whether the client can read legal hold info. | `true` |
| `legalHold.value` | string | Legal hold status or `null`. | `null` |
| `replicationStatus` | string | Replication status: `"PENDING"`, `"COMPLETED"`, `"FAILED"`, or `"REPLICA"`. Omitted if not part of a replication rule. | `"PENDING"` |
| `serverSideEncryption` | object | SSE info. Mode is `"SSE-B2"` or `"SSE-C"`. Both `null` if not encrypted. Omitted for `"hide"` / `"folder"`. | `{"algorithm": null, "mode": null}` |
| `serverSideEncryption.mode` | string | Encryption mode or `null`. | `null` |
| `serverSideEncryption.algorithm` | string | Encryption algorithm or `null`. | `null` |
| `uploadTimestamp` | integer | UTC upload time (ms since epoch). 0 for `"folder"`. | `1460162909000` |

#### 200 Response Example

```json
{
  "accountId": "ACCOUNT_ID",
  "action": "upload",
  "bucketId": "e73ede9c9c8412db49f60715",
  "contentLength": 208158542,
  "contentSha1": "none",
  "contentMd5": null,
  "contentType": "b2/x-auto",
  "fileId": "4_ze73ede9c9c8412db49f60715_f200b4e93fbae6252_d20150824_m224353_c900_v8881000_t0001",
  "fileInfo": {"author": "unknown"},
  "fileName": "bigfile.dat",
  "fileRetention": {
    "isClientAuthorizedToRead": true,
    "value": {"mode": null, "retainUntilTimestamp": null}
  },
  "legalHold": {
    "isClientAuthorizedToRead": true,
    "value": null
  },
  "replicationStatus": "PENDING",
  "serverSideEncryption": {"algorithm": null, "mode": null},
  "uploadTimestamp": 1460162909000
}
```

### Response — `400 Bad Request`

**Content-Type:** `application/json`

| Code | Description |
|------|-------------|
| `bad_bucket_id` | The requested bucket ID does not match an existing bucket. |
| `bad_request` | Wrong fields or illegal values. |

#### Error Response Schema

| Field | Type | Description | Example |
|-------|------|-------------|---------|
| `status` | integer | Numeric HTTP status code. | `400` |
| `code` | string | Single-identifier error code. | `"invalid_bucket_name"` |
| `message` | string | Human-readable error message. | `"bucket name is too long"` |

### Response — `401 Unauthorized`

| Code | Description |
|------|-------------|
| `bad_auth_token` | Auth token is not valid. |
| `expired_auth_token` | Auth token has expired. |
| `unauthorized` | Auth token valid but does not authorize this call. |

---

## 4. b2_get_bucket_notification_rules

**HTTP Method:** `GET`
**Path:** `/b2api/v4/b2_get_bucket_notification_rules`
**Description:** Lists bucket event notification rules alphabetically by rule name. Returns an error if called on a non-existent `bucketId`.

### Request Parameters

#### Header Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `Authorization` | string | **Yes** | Account authorization token from `b2_authorize_account`. Must have `readBucketNotifications` capability. |

#### Query Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `bucketId` | string | **Yes** | The unique identifier for the bucket containing the event notification rules. |

### Response — `200 OK`

**Content-Type:** `application/json`

The response is an object with the following fields:

| Field | Type | Description | Example |
|-------|------|-------------|---------|
| `bucketId` | string | The unique identifier for the bucket. | `"aea8c5bc362ae55070130333"` |
| `eventNotificationRules` | array of object | Array of event notification rules. | *(see below)* |

#### `eventNotificationRules[*]` Object

| Field | Type | Description | Example |
|-------|------|-------------|---------|
| `eventTypes` | array of string | List of event types for the rule. | `["b2:ObjectCreated:*"]` |
| `isEnabled` | boolean | Whether the rule is enabled. | `true` |
| `isSuspended` | boolean | Whether the rule is suspended. | `false` |
| `maxEventsPerBatch` | integer | Max events per webhook invocation (1–50, default 1). | `1` |
| `name` | string | Unique name for the rule within the bucket. | `"mySampleRule1"` |
| `objectNamePrefix` | string | Object name prefix the rule applies to. | `"images/"` |
| `suspensionReason` | string | Why the rule was suspended. | `"A reason for suspension"` |
| `targetConfiguration` | object | Target configuration for the rule. | *(see below)* |

#### `targetConfiguration` Object

| Field | Type | Description | Example |
|-------|------|-------------|---------|
| `targetType` | string | Type of target. Currently only `"webhook"`. | `"webhook"` |
| `url` | string | The webhook URL. | `"https://www.example.com/sampleurl"` |
| `hmacSha256SigningSecret` | string | Signing secret for verifying `X-Bz-Event-Notification-Signature`. | `"MTIzNDU2Nzg5MDEyMzQ1Njc4OTAxMjM0"` |
| `customHeaders` | array of object | Additional header name/value pairs sent on webhook invocation. | *(see below)* |

#### `customHeaders[*]` Object

| Field | Type | Description | Example |
|-------|------|-------------|---------|
| `name` | string | Header name. | `"my-custom-header-name"` |
| `value` | string | Header value. | `"my-custom-header-value"` |

#### 200 Response Example

```json
{
  "bucketId": "aea8c5bc362ae55070130333",
  "eventNotificationRules": [
    {
      "eventTypes": [
        "b2:ObjectCreated:Upload",
        "b2:ObjectCreated:MultipartUpload"
      ],
      "isEnabled": true,
      "isSuspended": false,
      "maxEventsPerBatch": 1,
      "name": "mySampleRule1",
      "objectNamePrefix": "",
      "suspensionReason": "",
      "targetConfiguration": {
        "customHeaders": [
          {
            "name": "X-My-Custom-Header-1",
            "value": "myCustomHeaderVal1"
          },
          {
            "name": "X-My-Custom-Header-2",
            "value": "myCustomHeaderVal2"
          }
        ],
        "hmacSha256SigningSecret": "MTIzNDU2Nzg5MDEyMzQ1Njc4OTAxMjM0",
        "targetType": "webhook",
        "url": "https://www.example.com/sampleurl"
      }
    }
  ]
}
```

### Response — `400 Bad Request`

**Content-Type:** `application/json`

| Code | Description |
|------|-------------|
| `bad_bucket_id` | `bucketId` is not valid. |

#### Error Response Schema

| Field | Type | Description | Example |
|-------|------|-------------|---------|
| `status` | integer | Numeric HTTP status code. | `400` |
| `code` | string | Single-identifier error code. | `"invalid_bucket_name"` |
| `message` | string | Human-readable error message. | `"bucket name is too long"` |

### Response — `401 Unauthorized`

| Code | Description |
|------|-------------|
| `bad_auth_token` | Auth token is not valid. |
| `expired_auth_token` | Auth token has expired. |
| `unauthorized` | Auth token valid but does not authorize this call. |

### Response — `403 Forbidden`

| Code | Description |
|------|-------------|
| `transaction_cap_exceeded` | Transaction cap exceeded. |

---

## 5. b2_get_download_authorization

**HTTP Method:** `POST`
**Path:** `/b2api/v4/b2_get_download_authorization`
**Description:** Generates an authorization token that can be used to download files with the specified prefix (and other optional headers) from a private B2 bucket. The token can be passed to `b2_download_file_by_name` in the `Authorization` header or as a query parameter. Also supports `GET` by converting body parameters to query parameters.

### Request Parameters

#### Header Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `Authorization` | string | **Yes** | Account authorization token from `b2_authorize_account`. Must have `shareFiles` capability. |

#### Body Parameters (`application/json`)

| Field | Type | Required | Description | Example |
|-------|------|----------|-------------|---------|
| `bucketId` | string | **Yes** | The identifier for the bucket. | `"a71f544e781e6891531b001a"` |
| `fileNamePrefix` | string | **Yes** | File name prefix the download auth token will allow access to. E.g. `"pets/"` allows access to files starting with `pets/` in `b2_download_file_by_name`. | `"pets/"` |
| `validDurationInSeconds` | integer | **Yes** | Seconds before the token expires. Min: 1. Max: 604800 (one week). | `604800` |
| `b2ContentDisposition` | string | No | If present, download requests using the returned auth must include the same value. Must match RFC 6266. | `""` |
| `b2ContentLanguage` | string | No | If present, download requests using the returned auth must include the same value. Must match RFC 2616. | `""` |
| `b2Expires` | string | No | If present, download requests using the returned auth must include the same value. Must match RFC 2616. | `""` |
| `b2CacheControl` | string | No | If present, download requests using the returned auth must include the same value. Must match RFC 2616. | `""` |
| `b2ContentEncoding` | string | No | If present, download requests using the returned auth must include the same value. Must match RFC 2616. | `""` |
| `b2ContentType` | string | No | If present, download requests using the returned auth must include the same value. Must match RFC 2616. | `""` |

#### Request Body Example

```json
{
  "bucketId": "a71f544e781e6891531b001a",
  "fileNamePrefix": "pets/",
  "validDurationInSeconds": 604800
}
```

### Response — `200 OK`

**Content-Type:** `application/json`

| Field | Type | Description | Example |
|-------|------|-------------|---------|
| `bucketId` | string | The identifier for the bucket. | `"a71f544e781e6891531b001a"` |
| `fileNamePrefix` | string | The prefix for files the token allows access to. | `"pets"` |
| `authorizationToken` | string | The download authorization token. | `"3_20160803004041_53982a92f631a8c7303e3266_d940c7f5ee17cd1de3758aaacf1024188bc0cd0b_000_20160804004041_0006_dnld"` |

#### 200 Response Example

```json
{
  "bucketId": "a71f544e781e6891531b001a",
  "fileNamePrefix": "pets",
  "authorizationToken": "3_20160803004041_53982a92f631a8c7303e3266_d940c7f5ee17cd1de3758aaacf1024188bc0cd0b_000_20160804004041_0006_dnld"
}
```

### Response — `400 Bad Request`

**Content-Type:** `application/json`

| Code | Description |
|------|-------------|
| `bad_bucket_id` | The requested bucket ID does not match an existing bucket. |
| `bad_request` | Wrong fields or illegal values. |

#### Error Response Schema

| Field | Type | Description | Example |
|-------|------|-------------|---------|
| `status` | integer | Numeric HTTP status code. | `400` |
| `code` | string | Single-identifier error code. | `"invalid_bucket_name"` |
| `message` | string | Human-readable error message. | `"bucket name is too long"` |

### Response — `401 Unauthorized`

| Code | Description |
|------|-------------|
| `bad_auth_token` | Auth token is not valid. |
| `expired_auth_token` | Auth token has expired. |
| `unauthorized` | Auth token valid but does not authorize this call. |

### Response — `403 Forbidden`

| Code | Description |
|------|-------------|
| `transaction_cap_exceeded` | Transaction cap exceeded. |

### Response — `503 Service Unavailable`

| Code | Description |
|------|-------------|
| `service_unavailable` | Service temporarily unavailable. |

---

## 6. b2_get_file_info

**HTTP Method:** `GET`
**Path:** `/b2api/v4/b2_get_file_info`
**Description:** Gets information about a file. Returns an error if called on a non-existent file ID or on an unfinished large file. Also supports `POST` with a JSON body.

### Request Parameters

#### Header Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `Authorization` | string | **Yes** | Account authorization token from `b2_authorize_account`. Must have `readFiles` capability. |

#### Query Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `fileId` | string | **Yes** | The ID of the file, as returned by `b2_upload_file`, `b2_hide_file`, `b2_list_file_names`, or `b2_list_file_versions`. |

### Response — `200 OK`

**Content-Type:** `application/json`

| Field | Type | Description | Example |
|-------|------|-------------|---------|
| `accountId` | string | The account that owns the file. | `"ACCOUNT_ID"` |
| `action` | string | One of `"start"`, `"upload"`, `"hide"`, `"folder"`, or future values. | `"start"` |
| `bucketId` | string | The identifier for the bucket. | `"e73ede9c9c8412db49f60715"` |
| `contentLength` | integer | Number of bytes stored. 0 for `"start"`, `"hide"`, `"folder"`. | `122573` |
| `contentSha1` | string | SHA1 of stored bytes (40-digit hex). `"none"` for large files. `null` for `"hide"` / `"folder"`. | `"a01a21253a07fb08a354acd30f3a6f32abb76821"` |
| `contentMd5` | string | MD5 of stored bytes (32-digit hex). `null` for large files, `"hide"`, `"folder"`, or if unavailable. | `"142be8357f26eda2ec6775cce257753b"` |
| `contentType` | string | MIME type for `"upload"` / `"start"`. `"application/x-bz-hide-marker"` for `"hide"`. `null` for `"folder"`. | `"image/jpeg"` |
| `fileId` | string | Unique identifier for this file version. `null` for `"folder"`. | `"4_ze73ede9c9c8412db49f60715_f100b4e93fbae6252_d20150824_m224353_c900_v8881000_t0001"` |
| `fileInfo` | object | Custom key/value pairs uploaded with the file. | `{"src_last_modified_millis": "1535629029000"}` |
| `fileName` | string | The file name. | `"akitty.jpg"` |
| `fileRetention` | object | Object Lock retention settings (requires `readFileRetentions`). Omitted for `"hide"` / `"folder"`. | `{"isClientAuthorizedToRead": true, "value": {"mode": null, "retainUntilTimestamp": null}}` |
| `fileRetention.isClientAuthorizedToRead` | boolean | Whether client can read retention info. | `true` |
| `fileRetention.value` | object | Retention settings. | |
| `fileRetention.value.mode` | string | `"governance"`, `"compliance"`, or `null`. | `null` |
| `fileRetention.value.retainUntilTimestamp` | integer | Retain-until timestamp (ms since epoch) or `null`. | `null` |
| `legalHold` | object | Object Lock legal hold status (requires `readFileLegalHolds`). Omitted for `"hide"` / `"folder"`. | `{"isClientAuthorizedToRead": true, "value": null}` |
| `legalHold.isClientAuthorizedToRead` | boolean | Whether client can read legal hold info. | `true` |
| `legalHold.value` | string | Legal hold status or `null`. | `null` |
| `replicationStatus` | string | `"PENDING"`, `"COMPLETED"`, `"FAILED"`, or `"REPLICA"`. Omitted if not part of a replication rule. | `"PENDING"` |
| `serverSideEncryption` | object | SSE info. Omitted for `"hide"` / `"folder"`. | `{"algorithm": "AES256", "mode": "SSE-B2"}` |
| `serverSideEncryption.algorithm` | string | Encryption algorithm or `null`. | `"AES256"` |
| `serverSideEncryption.mode` | string | `"SSE-B2"`, `"SSE-C"`, or `null`. | `"SSE-B2"` |
| `uploadTimestamp` | integer | UTC upload time (ms since epoch). 0 for `"folder"`. | `1536964279000` |

#### 200 Response Example

```json
{
  "accountId": "ACCOUNT_ID",
  "action": "start",
  "bucketId": "e73ede9c9c8412db49f60715",
  "contentLength": 122573,
  "contentSha1": "a01a21253a07fb08a354acd30f3a6f32abb76821",
  "contentMd5": "142be8357f26eda2ec6775cce257753b",
  "contentType": "image/jpeg",
  "fileId": "4_ze73ede9c9c8412db49f60715_f100b4e93fbae6252_d20150824_m224353_c900_v8881000_t0001",
  "fileInfo": {"src_last_modified_millis": "1535629029000"},
  "fileName": "akitty.jpg",
  "fileRetention": {
    "isClientAuthorizedToRead": true,
    "value": {"mode": null, "retainUntilTimestamp": null}
  },
  "legalHold": {
    "isClientAuthorizedToRead": true,
    "value": null
  },
  "replicationStatus": "PENDING",
  "serverSideEncryption": {"algorithm": "AES256", "mode": "SSE-B2"},
  "uploadTimestamp": 1536964279000
}
```

### Response — `400 Bad Request`

**Content-Type:** `application/json`

| Code | Description |
|------|-------------|
| `bad_bucket_id` | The requested bucket ID does not match an existing bucket. |
| `bad_request` | Wrong fields or illegal values. |

#### Error Response Schema

| Field | Type | Description | Example |
|-------|------|-------------|---------|
| `status` | integer | Numeric HTTP status code. | `400` |
| `code` | string | Single-identifier error code. | `"invalid_bucket_name"` |
| `message` | string | Human-readable error message. | `"bucket name is too long"` |

### Response — `401 Unauthorized`

| Code | Description |
|------|-------------|
| `bad_auth_token` | Auth token is not valid. |
| `expired_auth_token` | Auth token has expired. |
| `unauthorized` | Auth token valid but does not authorize this call. |

### Response — `403 Forbidden`

| Code | Description |
|------|-------------|
| `access_denied` | Customer-managed encryption key is wrong. |
| `download_cap_exceeded` | Usage cap exceeded. |
| `transaction_cap_exceeded` | Transaction cap exceeded. |

### Response — `404 Not Found`

| Code | Description |
|------|-------------|
| `not_found` | File is not in B2 Cloud Storage. |

---

## 7. b2_get_upload_part_url

**HTTP Method:** `GET`
**Path:** `/b2api/v4/b2_get_upload_part_url`
**Description:** Returns a URL to use for uploading parts of a large file. Call this first to get the URL, then use `b2_upload_part` on the returned URL. The `uploadUrl` and `authorizationToken` are valid for 24 hours or until the endpoint rejects an upload. Also supports `POST` with a JSON body.

### Request Parameters

#### Header Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `Authorization` | string | **Yes** | Account authorization token from `b2_authorize_account`. Must have `writeFiles` capability. |

#### Query Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `fileId` | string | **Yes** | The ID of the large file whose parts you want to upload. |

### Response — `200 OK`

**Content-Type:** `application/json`

| Field | Type | Description | Example |
|-------|------|-------------|---------|
| `fileId` | string | The unique ID of the file being uploaded. | `"4_ze73ede9c9c8412db49f60715_f200b4e93fbae6252_d20150824_m224353_c900_v8881000_t0001"` |
| `uploadUrl` | string | The URL to use with `b2_upload_part`. | `"https://pod-000-1016-09.backblaze.com/b2api/v4/b2_upload_part/4_ze73ede9c9c8412db49f60715_f200b4e93fbae6252_d20150824_m224353_c900_v8881000_t0001/0037"` |
| `authorizationToken` | string | Auth token for uploading parts. Valid 24 hours or until endpoint rejects upload. | `"3_20160409004829_42b8f80ba60fb4323dcaad98_ec81302316fccc2260201cbf17813247f312cf3b_000_uplg"` |

#### 200 Response Example

```json
{
  "fileId": "4_ze73ede9c9c8412db49f60715_f200b4e93fbae6252_d20150824_m224353_c900_v8881000_t0001",
  "uploadUrl": "https://pod-000-1016-09.backblaze.com/b2api/v4/b2_upload_part/4_ze73ede9c9c8412db49f60715_f200b4e93fbae6252_d20150824_m224353_c900_v8881000_t0001/0037",
  "authorizationToken": "3_20160409004829_42b8f80ba60fb4323dcaad98_ec81302316fccc2260201cbf17813247f312cf3b_000_uplg"
}
```

### Response — `400 Bad Request`

**Content-Type:** `application/json`

| Code | Description |
|------|-------------|
| `bad_bucket_id` | The requested bucket ID does not match an existing bucket. |
| `bad_request` | Wrong fields or illegal values. |

#### Error Response Schema

| Field | Type | Description | Example |
|-------|------|-------------|---------|
| `status` | integer | Numeric HTTP status code. | `400` |
| `code` | string | Single-identifier error code. | `"invalid_bucket_name"` |
| `message` | string | Human-readable error message. | `"bucket name is too long"` |

### Response — `401 Unauthorized`

| Code | Description |
|------|-------------|
| `bad_auth_token` | Auth token is not valid. |
| `expired_auth_token` | Auth token has expired. |
| `unauthorized` | Auth token valid but does not authorize this call. |

### Response — `403 Forbidden`

| Code | Description |
|------|-------------|
| `storage_cap_exceeded` | Usage cap exceeded. |

### Response — `503 Service Unavailable`

| Code | Description |
|------|-------------|
| `service_unavailable` | No uploads available in vault. |

---

## 8. b2_get_upload_url

**HTTP Method:** `GET`
**Path:** `/b2api/v4/b2_get_upload_url`
**Description:** Returns a URL for uploading files. Call this first to get the URL, then use `b2_upload_file` on the returned URL. The `uploadUrl` and `authorizationToken` are valid for 24 hours or until the endpoint rejects an upload. Request multiple URLs for parallel uploads. Also supports `POST` with a JSON body.

### Request Parameters

#### Header Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `Authorization` | string | **Yes** | Account authorization token from `b2_authorize_account`. Must have `writeFiles` capability. |

#### Query Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `bucketId` | string | **Yes** | The ID of the bucket that you want to upload to. |

### Response — `200 OK`

**Content-Type:** `application/json`

| Field | Type | Description | Example |
|-------|------|-------------|---------|
| `bucketId` | string | The identifier for the bucket. | `"4a48fe8875c6214145260818"` |
| `uploadUrl` | string | The URL to use with `b2_upload_file`. | `"https://pod-000-1005-03.backblaze.com/b2api/v4/b2_upload_file?cvt=c001_v0001005_t0027&bucket=4a48fe8875c6214145260818"` |
| `authorizationToken` | string | Auth token for uploading files. Valid 24 hours or until endpoint rejects upload. | `"2_20151009170037_f504a0f39a0f4e657337e624_9754dde94359bd7b8f1445c8f4cc1a231a33f714_upld"` |

#### 200 Response Example

```json
{
  "bucketId": "4a48fe8875c6214145260818",
  "uploadUrl": "https://pod-000-1005-03.backblaze.com/b2api/v4/b2_upload_file?cvt=c001_v0001005_t0027&bucket=4a48fe8875c6214145260818",
  "authorizationToken": "2_20151009170037_f504a0f39a0f4e657337e624_9754dde94359bd7b8f1445c8f4cc1a231a33f714_upld"
}
```

### Response — `400 Bad Request`

**Content-Type:** `application/json`

| Code | Description |
|------|-------------|
| `bad_bucket_id` | The requested bucket ID does not match an existing bucket. |
| `bad_request` | Wrong fields or illegal values. |

#### Error Response Schema

| Field | Type | Description | Example |
|-------|------|-------------|---------|
| `status` | integer | Numeric HTTP status code. | `400` |
| `code` | string | Single-identifier error code. | `"invalid_bucket_name"` |
| `message` | string | Human-readable error message. | `"bucket name is too long"` |

### Response — `401 Unauthorized`

| Code | Description |
|------|-------------|
| `bad_auth_token` | Auth token is not valid. |
| `expired_auth_token` | Auth token has expired. |
| `unauthorized` | Auth token valid but does not authorize this call. |

### Response — `403 Forbidden`

| Code | Description |
|------|-------------|
| `storage_cap_exceeded` | Usage cap exceeded. |

### Response — `503 Service Unavailable`

| Code | Description |
|------|-------------|
| `service_unavailable` | Service temporarily unavailable. |
