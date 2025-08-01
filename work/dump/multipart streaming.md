### Pattern: S3 Multipart Upload with Streaming

This example uses the AWS SDK for Go v2, but the pattern is similar for other S3-compatible libraries.
#### 1. Start the multipart upload

```go
input := &s3.CreateMultipartUploadInput{
    Bucket: aws.String(bucketName),
    Key:    aws.String(objectKey),
}
result, err := s3Client.CreateMultipartUpload(ctx, input)
if err != nil {
    // handle error
}
uploadID := result.UploadId
```
#### 2. Stream and upload parts

```go
const partSize = 5 * 1024 * 1024 // 5MB minimum for S3

var (
    completedParts []types.CompletedPart
    partNumber     int32 = 1
    buffer         = make([]byte, partSize)
)

for {
    n, err := io.ReadFull(sourceReader, buffer)
    if err != nil && err != io.EOF && err != io.ErrUnexpectedEOF {
        // abort upload on error
        s3Client.AbortMultipartUpload(ctx, &s3.AbortMultipartUploadInput{
            Bucket:   aws.String(bucketName),
            Key:      aws.String(objectKey),
            UploadId: uploadID,
        })
        return err
    }
    if n == 0 {
        break
    }

    uploadPartInput := &s3.UploadPartInput{
        Bucket:     aws.String(bucketName),
        Key:        aws.String(objectKey),
        PartNumber: partNumber,
        UploadId:   uploadID,
        Body:       bytes.NewReader(buffer[:n]),
    }
    uploadPartResult, err := s3Client.UploadPart(ctx, uploadPartInput)
    if err != nil {
        // abort upload on error
        s3Client.AbortMultipartUpload(ctx, &s3.AbortMultipartUploadInput{
            Bucket:   aws.String(bucketName),
            Key:      aws.String(objectKey),
            UploadId: uploadID,
        })
        return err
    }

    completedParts = append(completedParts, types.CompletedPart{
        ETag:       uploadPartResult.ETag,
        PartNumber: partNumber,
    })
    partNumber++
    if err == io.EOF {
        break
    }
}
```

#### 3. Complete the multipart upload

```go
_, err = s3Client.CompleteMultipartUpload(ctx, &s3.CompleteMultipartUploadInput{
    Bucket:   aws.String(bucketName),
    Key:      aws.String(objectKey),
    UploadId: uploadID,
    MultipartUpload: &types.CompletedMultipartUpload{
        Parts: completedParts,
    },
})
if err != nil {
    // handle error
}
```

---

### How to Use with gRPC Stream

- Replace `sourceReader` with an `io.Reader` that streams data from your gRPC client (e.g., using `io.Pipe` as described earlier).
- Read from the gRPC stream, fill the buffer, and upload each part as you go.

---

### Notes

- **Minimum part size**: Except for the last part, each part must be at least 5MB.
- **Error handling**: Always abort the multipart upload if any part fails.
- **Concurrency**: For even better performance, you can upload parts concurrently (advanced).

---

If you want, I can show a concrete example using your gRPC bundle stream and S3 multipart upload in Go. Let me know if you want to see that!
