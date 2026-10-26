# ListBuckets

Returns the list of buckets created by the user who performs the request.

> **Source:** [ListBuckets — official cloud.ru docs](https://cloud.ru/docs/s3e/ug/topics/api__listbuckets)

Access to this method depends on the user's role. See [role management in Object Storage](https://cloud.ru/docs/s3e/ug/topics/security__role).

## Request

```http
GET / HTTP/2
Host: s3.cloud.ru
```

## Parameters

- None. The path is the service root `/` — do **not** append a bucket name (that would be [ListObjects](list-objects.md)).

## Headers

- See the [common request headers](https://cloud.ru/docs/s3e/ug/topics/api__headers-request) for the full supported list.

## Example request

The request lists the available buckets:

```http
GET / HTTP/2
Host: s3.cloud.ru
Date: Wed, 25 Oct 2023 22:32:00 GMT
Authorization: authorization string
```

## Example response

```http
HTTP/2 200 OK

<ListAllMyBucketsResult>
  <Owner></Owner>
  <Buckets>
    <Bucket>
      <CreationDate>2023-10-26T09:54:56.144Z</CreationDate>
      <Name>my-bucket</Name>
    </Bucket>
    <Bucket>
      <CreationDate>2023-10-25T06:57:58.736Z</CreationDate>
      <Name>bucket-cold</Name>
    </Bucket>
    <Bucket>
      <CreationDate>2023-10-25T06:58:12.670Z</CreationDate>
      <Name>bucket-ice</Name>
    </Bucket>
    <Bucket>
      <CreationDate>2023-10-25T06:58:24.931Z</CreationDate>
      <Name>bucket-standard</Name>
    </Bucket>
  </Buckets>
</ListAllMyBucketsResult>
```

## Agent notes

- Success code: `200 OK`; the body is XML (`ListAllMyBucketsResult`), one `<Bucket>` entry per bucket with `Name` and `CreationDate`.
- This is always the first discovery call for a new agent session: it shows which buckets you can already address — [CreateBucket](create-bucket.md) only if the needed one is missing.
- Bucket names returned here are used verbatim as the `{bucket}` path component in every bucket/object operation ([ListObjects](list-objects.md), [PutObject](put-object.md), [DeleteBucket](delete-bucket.md)).
- The `<Owner>` block identifies the credentials owner; the cloud.ru example shows it empty.
- Possible error responses are collected in the [response codes reference](https://cloud.ru/docs/s3e/ug/topics/api__response-codes).
