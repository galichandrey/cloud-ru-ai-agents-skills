# ListObjects (legacy)

Returns part or the full list of objects in a bucket (up to 1000 objects per response). This is the older version of the listing method — Object Storage keeps it for backward compatibility; the recommended version is [ListObjectsV2](list-objects-v2.md).

> **Source:** [ListObjects — official cloud.ru docs](https://cloud.ru/docs/s3e/ug/topics/api__listobjects)

Access to this method depends on the user's role. See [role management in Object Storage](https://cloud.ru/docs/s3e/ug/topics/security__role).

## Request

```http
GET /{bucket} HTTP/2
Host: s3.cloud.ru
```

## Parameters

### Path parameters

| Parameter | Description |
|-----------|-------------|
| `bucket`  | Bucket name |

### Query parameters

| Parameter       | Description                                                                                         |
|-----------------|-----------------------------------------------------------------------------------------------------|
| `delimiter`     | Delimiter character (groups keys into "folders" at that delimiter)                                   |
| `encoding-type` | Encoding of the server response; Object Storage can encode the response on client request             |
| `key-marker`    | Key to start listing from; keys *after* `key-marker` are returned (pagination cursor)                  |
| `max-keys`      | Maximum number of entries in the response                                                            |
| `prefix`        | Only keys beginning with this string are returned                                                      |

## Headers

- See the [common request headers](https://cloud.ru/docs/s3e/ug/topics/api__headers-request) for the full supported list.

## Example request

The request returns the list of objects in the bucket `my-bucket`:

```http
GET /my-bucket HTTP/2
Host: s3.cloud.ru
Date: Wed, 25 Oct 2023 22:32:00 GMT
Authorization: authorization string
Content-Type: text/plain
```

## Example response

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ListBucketResult xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
    <Name>my-bucket</Name>
    <Prefix/>
    <Marker/>
    <MaxKeys>1000</MaxKeys>
    <IsTruncated>false</IsTruncated>
    <Contents>
        <Key>my-file.txt</Key>
        <LastModified>2023-10-12T17:50:30.000Z</LastModified>
        <ETag>"fba9dede5f27731c9771645a39863328"</ETag>
        <Size>434234</Size>
        <StorageClass>STANDARD</StorageClass>
        <Owner>
            <ID>75aa57f09aa0c8caeab4f8c24e99d10f8e7faeebf76c078efc7c6caea54ba06a</ID>
            <DisplayName>mtd@cloud.ru</DisplayName>
        </Owner>
    </Contents>
    <Contents>
        <Key>my-file-new.txt</Key>
        <LastModified>2023-10-12T17:50:30.000Z</LastModified>
        <ETag>"1b2cf535f27731c974343645a3985328"</ETag>
        <Size>64994</Size>
        <StorageClass>STANDARD_IA</StorageClass>
        <Owner>
            <ID>75aa57f09aa0c8caeab4f8c24e99d10f8e7faeebf76c078efc7c6caea54ba06a</ID>
            <DisplayName>mtd@cloud.ru</DisplayName>
        </Owner>
   </Contents>
</ListBucketResult>
```

## Agent notes

- Response if truncated (`IsTruncated: true`) must be continued in a follow-up request using `key-marker` = key of the last returned entry; responses hold at most 1000 keys.
- Only `GET /{bucket}` without `list-type=2` invokes this legacy version — nothing extra to send, but prefer [ListObjectsV2](list-objects-v2.md).
- Use `prefix` to scope to a "directory" and `delimiter=/` to get folder-level groupings instead of the whole subtree.
- `StorageClass` values such as `STANDARD` and `STANDARD_IA` show where each object physically lives.
- Possible error responses are collected in the [response codes reference](https://cloud.ru/docs/s3e/ug/topics/api__response-codes).
