# Integration and Use of Veo Reshoot API

This document introduces the Veo Reshoot API. This API re-generates an already-generated Veo video using a **new camera motion**, keeping the scene content the same while reinterpreting the camera angle and movement according to the specified `motion_type`.

## Application Process

To use the API, you first need to apply for the corresponding service on the Veo service page. If you are not logged in or registered, you will be automatically redirected to the login page.

## Basic Usage

The following parameters are required when calling this API:

- `video_id` (required): The task ID of the source video. **Cannot** be a video produced by `/veo/extend`.
- `motion_type` (required): The new camera motion style, specified as an uppercase underscore-separated alias.

### Supported `motion_type` Values

| Alias | Description |
|---|---|
| `STATIONARY` | Camera stays fixed |
| `STATIONARY_UP` | Camera fixed, tilt up |
| `STATIONARY_DOWN` | Camera fixed, tilt down |
| `STATIONARY_LEFT` | Camera fixed, pan left |
| `STATIONARY_RIGHT` | Camera fixed, pan right |
| `STATIONARY_DOLLY_IN_ZOOM_OUT` | Camera fixed, dolly in + zoom out |
| `STATIONARY_DOLLY_OUT_ZOOM_IN` | Camera fixed, dolly out + zoom in |
| `UP` | Camera moves upward |
| `DOWN` | Camera moves downward |
| `LEFT_TO_RIGHT` | Camera pans from left to right |
| `RIGHT_TO_LEFT` | Camera pans from right to left |
| `FORWARD` | Camera moves forward |
| `BACKWARD` | Camera moves backward |
| `DOLLY_IN_ZOOM_OUT` | Moving dolly in + zoom out |
| `DOLLY_OUT_ZOOM_IN` | Moving dolly out + zoom in |

### Request Example

```bash
curl -X POST 'https://api.acedata.cloud/veo/reshoot' \
  -H 'accept: application/json' \
  -H 'authorization: Bearer {token}' \
  -H 'content-type: application/json' \
  -d '{
    "video_id": "dd01fc69-e1f7-4b68-aa8c-463f6b748d11",
    "motion_type": "LEFT_TO_RIGHT"
  }'
```

The response format is the same as `/veo/videos`.

## Pricing

- Per reshoot: 1.20 Credit

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

Through this document, you have learned how to use the Veo Reshoot API to re-generate an already-generated Veo video with a new camera motion. We hope this document can help you better integrate and use this API. If you have any questions, please feel free to contact our technical support team.
