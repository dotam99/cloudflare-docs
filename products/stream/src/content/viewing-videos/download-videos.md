---
order: 5
hidden: true
---

# Download videos (Beta)

## Generating downloadable MP4 files for a video

Once a video has been uploaded to Cloudflare Stream and is ready to view, you can create downloads by making an HTTP request to the downloads API.

To get notified when a video is ready to view, please see the [webhook documentation](../../uploading-videos/using-webhooks#notifications).

The downloads API response will include all available download types for the video, the download URL for each type once it's finished processing, and the processing status of the download file.

### cURL example

```bash
curl -X POST \
-H "Authorization: Bearer $TOKEN" \
https://api.cloudflare.com/client/v4/accounts/$ACCOUNT/stream/$VIDEOID/downloads
```

#### Example response

```bash
{
  "result": {
    "default": {
      "status": "inprogress",
      "url": "https://videodelivery.net/$VIDEOID/downloads/default.mp4"
    }
  },
  "success": true,
  "errors": [],
  "messages": []
}
```

## Getting links to download files

You can view all available downloads for a video by making an `GET` HTTP request to the downloads API. The response for creating and fetching downloads are the same.

### cURL example

```bash
curl -X GET \
-H "Authorization: Bearer $TOKEN" \
https://api.cloudflare.com/client/v4/accounts/$ACCOUNT/stream/$VIDEOID/downloads
```

#### Example response

```bash
{
  "result": {
    "default": {
      "status": "ready",
      "url": "https://videodelivery.net/$VIDEOID/downloads/default.mp4"
    }
  },
  "success": true,
  "errors": [],
  "messages": []
}
```

## Retrieving the downloads

The generated MP4 download files can be retrieved via the link in the download API response.

### cURL example

```bash
curl -L \
-H "Authorization: Bearer $TOKEN" \
https://videodelivery.net/$VIDEOID/downloads/default.mp4 > download.mp4
```

## Securing video downloads

The download links can be secured with signed URLs. To enable this, you'll need to require signed URLs on the video, and ensure the `downloadable` flag is set to `true` in the signed token. The download should then be retrieved by replacing the `$VIDEOID` in the download URL with the `$SIGNEDTOKEN`.

### Example token payload

```json
---
highlight: [6]
---
{
    "sub": $VIDEOID,
    "kid": $KEYID,
    "exp": 1537460365,
    "nbf": 1537453165,
    "downloadable": true,
    "accessRules": [
      {
        "type": "ip.geoip.country",
        "action": "allow",
        "country": [
          "GB"
        ]
      },
      {
        "type": "any",
        "action": "block"
      }
    ]
  }
```

Download links will not work for videos already requiring signed URLs if the `downloadable` flag is not present in the token.

For more details about using signed URLs with videos, please see [the documentation](../securing-your-stream).