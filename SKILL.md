---
name: cloud-ru-ai-agents-skills
description: Instructions and API reference for AI agents working with Cloud.ru Object Storage (S3-compatible, endpoint s3.cloud.ru) — creating/removing buckets, uploading/reading/copying/deleting/listing objects. Use when a task involves cloud.ru buckets, objects, s3.cloud.ru endpoints, or cloud.ru Object Storage API.
---

# Cloud.ru Object Storage — agent skill

This skill makes an agent able to operate **Cloud.ru Object Storage** (S3-compatible) correctly: manage buckets and objects via its API. Each operation is fully documented in `methods/*.md` — read the linked file before writing code for an operation not yet familiar.

## How to connect

```text
Endpoint:   https://s3.cloud.ru     (HTTP/2)
SigV4 region: ru-central-1        (the API itself reports "unexpected region, expecting 'ru-central-1'")
Auth:       Authorization header — AWS SigV4
```

**Verified credential format** (empirically checked against the live API; also reported by the [console of Cloud.ru](https://cloud.ru)):

- `AccessKeyId` = `{TENANT_ID}:{KEY_ID}` — plain `KEY_ID` alone is **rejected** (`InvalidAccessKeyId`).
- `SecretAccessKey` = `KEY_SECRET` as given.
- Signing: standard AWS SigV4, service `s3`.
- Paths currently documented as `/{bucket}` are used directly; bucket **name** is the console name (e.g. `bucket-f77da8`), not the internal resource GUID.
- Note: a key may be scoped to specific buckets — service-level `GET /` (ListBuckets) returned `AccessDenied` while per-bucket operations fully worked.

### Verified round-trip (2026-10-26, bucket `bucket-f77da8`)

ListObjectsV2 → 200 · PutObject → 200 · GetObject (byte-equal round-trip) → 200 · HeadObject → 200 · DeleteObject → 204 · re-Get → 404.

Common headers also listed at: https://cloud.ru/docs/s3e/ug/topics/api__headers-request · Errors: https://cloud.ru/docs/s3e/ug/topics/api__response-codes · Roles: https://cloud.ru/docs/s3e/ug/topics/security__role

## Decision tree

1. **Which buckets exist?** → [ListBuckets](methods/list-buckets.md) (`GET /`, XML). Pick the bucket; if the needed one is missing, [CreateBucket](methods/create-bucket.md) (`PUT /{bucket}`, empty body) — same path as PutObject, different intent.
2. **Inspect bucket contents** → [ListObjectsV2](methods/list-objects-v2.md) (`GET /{bucket}?list-type=2`, XML, ≤1000 keys per page). Legacy alternative: [ListObjects](methods/list-objects.md).
3. **Upload / replace data** → [PutObject](methods/put-object.md) (`PUT /{bucket}/{key}`, body = data, support `x-amz-meta-*`).
4. **Read data** → [GetObject](methods/get-object.md) (`GET /{bucket}/{key}`, body = data). Metadata only: [HeadObject](methods/head-object.md) (`HEAD /{bucket}/{key}`) — same headers, no download.
5. **Copy/move** → [CopyObject](methods/copy-object.md) (`PUT` on destination key + `x-amz-copy-source: /src-bucket/src-key`); then [DeleteObject](methods/delete-object.md) on the source for a **move**.
6. **Clean objects** → [DeleteObject](methods/delete-object.md) (per key, idempotent `204`) or [DeleteObjects](methods/delete-objects.md) (batch, body of delete-object XML list).
7. **Destroy bucket** → [DeleteBucket](methods/delete-bucket.md) (`DELETE /{bucket}`); **it fails/succeeds only on an empty bucket** — enumerate every page and empty it first.
8. **Versioned objects** → `?versionId=` parameter exists on GetObject/HeadObject/DeleteObject; copy responses report `x-amz-version-id`.

## Method catalogue

| Method | HTTP | Path | Doc |
|--------|------|------|-----|
| ListBuckets | `GET` | `/` | methods/list-buckets.md |
| CreateBucket | `PUT` | `/{bucket}` | methods/create-bucket.md |
| DeleteBucket | `DELETE` | `/{bucket}` | methods/delete-bucket.md |
| PutObject | `PUT` | `/{bucket}/{key}` | methods/put-object.md |
| GetObject | `GET` | `/{bucket}/{key}` | methods/get-object.md |
| HeadObject | `HEAD` | `/{bucket}/{key}` | methods/head-object.md |
| CopyObject | `PUT` | `/{bucket}/{key}` + `x-amz-copy-source` | methods/copy-object.md |
| DeleteObject | `DELETE` | `/{bucket}/{key}` | methods/delete-object.md |
| DeleteObjects | `DELETE` | `/{bucket}` | methods/delete-objects.md |
| ListObjects (legacy) | `GET` | `/{bucket}` | methods/list-objects.md |
| ListObjectsV2 | `GET` | `/{bucket}?list-type=2` | methods/list-objects-v2.md |

## Pitfalls (do-not-skip)

- **`list-type=2` is mandatory** for ListObjectsV2 — omitting it silently invokes legacy ListObjects.
- **Pagination:** list methods return ≤1000 entries with `IsTruncated`; continue using `key-marker`. Check `KeyCount` (V2) per page.
- **Idempotent deletes:** Delete/DeleteObjects answer `204` even when nothing was deleted — existence checks need [HeadObject](methods/head-object.md) first.
- **Empty-body vs data-body:** `PUT /{bucket}` (empty) creates a bucket; `PUT /{bucket}/{key}` (with data) uploads an object. Do not mix.
- **ETag integrity:** compare `ETag` from Put/Copy against a later Head to detect changes or confirm an upload.
- **Large uploads:** send `Expect: 100-continue` and `Content-Length`, as in the official PutObject example.
- **DeleteObjects batch body:** the exact batch-format body is not exemplified by cloud.ru — verify against S3 Multi-Object Delete convention before scripting.
- **Destructive ops:** DeleteBucket is irreversible; verify bucket state with a fresh [ListObjectsV2](methods/list-objects-v2.md) after emptying and [ListBuckets](methods/list-buckets.md) after destruction.

## Scope and growth

Documented today: 11 methods (8 object + 3 bucket). Coming: remaining Bucket methods (HeadBucket, GetBucketLocation, CORS, policy), Multipart upload, Object Lock, ACL/roles. Prefer linking agents to the newest version of every doc; update this file when new methods land in `methods/`.

## Sources

- Method index (objects): https://cloud.ru/docs/s3e/ug/topics/api__methods-object
- Bucket methods index: https://cloud.ru/docs/s3e/ug/topics/api__methods-bucket
- Common request headers: https://cloud.ru/docs/s3e/ug/topics/api__headers-request
- Response codes: https://cloud.ru/docs/s3e/ug/topics/api__response-codes
