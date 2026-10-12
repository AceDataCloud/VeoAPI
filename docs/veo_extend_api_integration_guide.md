# Integration and Use of Veo Extend API

This document introduces the Veo Extend API. This API is used to **extend the duration** of an already-generated Veo video — the AI automatically generates and appends the subsequent scene.

## Application Process

To use the API, you first need to apply for the corresponding service on the Veo service page. If you are not logged in or registered, you will be automatically redirected to the login page.

## Basic Usage

The following parameters are required when calling this API:

- `video_id` (required): The task ID of the source video (from `/veo/videos` or a previous call to `/veo/extend`).
- `model` (required): The model used for extension. **Only `veo31-fast` and `veo31` are supported** — other models are not available upstream.
- `prompt` (optional): A text prompt to guide the content of the extended scene.

### Request Example

```bash
curl -X POST 'https://api.acedata.cloud/veo/extend' \
  -H 'accept: application/json' \
  -H 'authorization: Bearer {token}' \
  -H 'content-type: application/json' \
  -d '{
    "video_id": "dd01fc69-e1f7-4b68-aa8c-463f6b748d11",
    "model": "veo31-fast",
    "prompt": "the camera slowly zooms out to reveal more of the landscape"
  }'
```

### Response Example

The response format is the same as `/veo/videos`:

```json
{
  "success": true,
  "task_id": "5fa2d9a6-7e54-481b-a4b0-3dc6f25dd2ab",
  "data": [
    {
      "id": "9b6f3b6b9c45419fbf2e3fe2f10b2b3a",
      "video_url": "https://platform.cdn.acedata.cloud/veo/5fa2d9a6.mp4",
      "created_at": "2025-07-25 16:07:43",
      "complete_at": "2025-07-25 16:10:28",
      "state": "succeeded"
    }
  ]
}
```

## Important Limitations

A video produced by `/veo/extend` **can** itself be extended again by calling `/veo/extend`, but **cannot** be passed to the following APIs:

- `/veo/reshoot` — camera motion cannot be changed on an extended video.
- `/veo/objects` — objects cannot be inserted or removed from an extended video.

If you pass an extended video's `video_id` to those endpoints, the API will return a 400 error. Use the original source video instead.

## Pricing

- `model=veo31-fast`: 1.20 Credit / request
- `model=veo31`: 7.64 Credit / request

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

Through this document, you have learned how to use the Veo Extend API to extend the duration of an already-generated Veo video. We hope this document can help you better integrate and use this API. If you have any questions, please feel free to contact our technical support team.
