# Integration and Use of Veo Objects API

This document introduces the Veo Objects API. This API is used to **insert or remove objects** in an already-generated Veo video (mask-based local inpainting).

## Application Process

To use the API, you first need to apply for the corresponding service on the Veo service page. If you are not logged in or registered, you will be automatically redirected to the login page.

## Basic Usage

The following parameters are required when calling this API:

- `video_id` (required): The task ID of the source video. **Cannot** be a video produced by `/veo/extend`.
- `action` (required): The operation type — either `insert` or `remove`.
- `prompt`:
  - `action=insert`: **Required** — describes the object to be inserted.
  - `action=remove`: Optional — describes what to remove (mainly used for logging; the actual area to erase is determined by `image_mask`).
- `image_mask`:
  - `action=insert`: Optional. If not provided, the AI automatically decides where to insert the object; if provided, the object is inserted in the white-pixel region of the mask.
  - `action=remove`: **Required** — the white-pixel region marks the area to erase.

`image_mask` accepts two formats:
1. A **publicly accessible HTTP(S) image URL** (recommended).
2. A **base64-encoded JPEG string**, either with the `data:image/jpeg;base64,` prefix or as a bare base64 string.

### Request Example — Insert Object (no mask, AI auto-positions)

```bash
curl -X POST 'https://api.acedata.cloud/veo/objects' \
  -H 'accept: application/json' \
  -H 'authorization: Bearer {token}' \
  -H 'content-type: application/json' \
  -d '{
    "video_id": "dd01fc69-e1f7-4b68-aa8c-463f6b748d11",
    "action": "insert",
    "prompt": "add a flying pig with black wings"
  }'
```

### Request Example — Remove Object by Mask

```bash
curl -X POST 'https://api.acedata.cloud/veo/objects' \
  -H 'accept: application/json' \
  -H 'authorization: Bearer {token}' \
  -H 'content-type: application/json' \
  -d '{
    "video_id": "dd01fc69-e1f7-4b68-aa8c-463f6b748d11",
    "action": "remove",
    "image_mask": "https://example.com/mask.jpg",
    "prompt": "remove the white cloud"
  }'
```

The response format is the same as `/veo/videos`.

## Pricing

- `action=insert` or `remove`: 1.50 Credit / request

## Asynchronous Callback

This API supports asynchronous mode. Pass a `callback_url` field in the request body, and the result will be delivered via a POST request to that URL once the task completes.

## Error Handling

When calling the API, if an error occurs, the API will return the corresponding error code and message. For example:

- `400 token_mismatched`: Bad request, possibly due to missing or invalid parameters.
- `400 api_not_implemented`: Bad request, possibly due to missing or invalid parameters.
- `401 invalid_token`: Unauthorized, invalid or missing authorization token.
- `429 too_many_requests`: Too many requests, you have exceeded the rate limit.
- `500 api_error`: Internal server error, something went wrong on the server.

### Error Response Example

```json
{
  "success": false,
  "error": {
    "code": "api_error",
    "message": "fetch failed"
  },
  "trace_id": "2cf86e86-22a4-46e1-ac2f-032c0f2a4e89"
}
```

## Conclusion

Through this document, you have learned how to use the Veo Objects API to insert or remove objects in an already-generated Veo video. We hope this document can help you better integrate and use this API. If you have any questions, please feel free to contact our technical support team.
