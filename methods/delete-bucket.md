# DeleteBucket

Deletes a bucket from the storage. Before deleting a bucket, delete all objects stored in it using [DeleteObject](delete-object.md).

> **Source:** [DeleteBucket — official cloud.ru docs](https://cloud.ru/docs/s3e/ug/topics/api__deletebucket)

Access to this method depends on the user's role. See [role management in Object Storage](https://cloud.ru/docs/s3e/ug/topics/security__role).

## Request

```http
DELETE /{bucket} HTTP/2
Host: s3.cloud.ru
```

## Parameters

### Path parameters

| Parameter | Description     |
|-----------|-----------------|
| `bucket`  | Name of the bucket to delete |

## Headers

- See the [common request headers](https://cloud.ru/docs/s3e/ug/topics/api__headers-request) for the full supported list.
- Note: the same path — `DELETE /{bucket}` — serves the batch [DeleteObjects](delete-objects.md) operation; the official docs distinguish them by intent (bucket removal requires the bucket to be empty). Verify the target state with [ListBuckets](list-buckets.md)/[ListObjects](list-objects.md) before and after.

## Example request

The request deletes the bucket `my-bucket`:

```http
DELETE /my-bucket HTTP/2
Host: s3.cloud.ru
Date: Wed, 25 Oct 2023 22:32:00 GMT
Authorization: authorization string
```

## Example response

```http
HTTP/2 204 NoContent
x-amz-request-id: 32FE2CEB32F5EE25
Date: Wed, 25 Oct 2023 22:32:00 GMT
Connection: close
Server: s3.cloud.ru
```

## Agent notes

- Success code: `204 No Content` with an empty body.
- **Precondition:** the official docs require deleting ALL objects in the bucket first — see [DeleteObject](delete-object.md); for many objects, enumerate with [ListObjectsV2](list-objects-v2.md) (paginated) and remove them batch-wise with [DeleteObjects](delete-objects.md) before tearing the bucket down.
- This operation is destructive and idempotency-unfriendly: verify the bucket list after via [ListBuckets](list-buckets.md).
- A safe teardown flow: ListObjectsV2 (page through) → DeleteObjects per page → confirm `IsTruncated=false` and empty listing → DeleteBucket.
- Possible error responses are collected in the [response codes reference](https://cloud.ru/docs/s3e/ug/topics/api__response-codes).
