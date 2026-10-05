# Veo Upsample API Integration Instructions

This document will introduce the integration instructions for the Veo Upsample API. This interface is used to upsample an already generated Veo video or output a GIF preview.

## Application Process

To use the API, you need to apply on the corresponding Veo service page. If you are not logged in or registered, you will be automatically redirected to the login page.

## Basic Usage

Calling this interface requires two parameters:

- `video_id`: The task ID of the source video. It can be the video ID generated from any of the following interfaces: `/veo/videos`, `/veo/extend`, `/veo/reshoot`, `/veo/objects`.
- `action`: The upsampling action, supporting the following three options:
  - `1080p`: Upsample the video to 1080p resolution.
  - `4k`: Upsample the video to 4K resolution.
  - `gif`: Convert the video to a GIF preview.

Request example:

```shell
curl -X POST 'https://api.acedata.cloud/veo/upsample' \
  -H 'accept: application/json' \
  -H 'authorization: Bearer {token}' \
  -H 'content-type: application/json' \
  -d '{
    "video_id": "dd01fc69-e1f7-4b68-aa8c-463f6b748d11",
    "action": "4k"
  }'
```

The return result is the same as `/veo/videos`, still in an asynchronous task structure, and you can query the task status through `task_id` in the `/veo/tasks` interface:

```json
{
  "success": true,
  "task_id": "8f5a90ae-3f86-4c4f-86f7-7126f5e92c76",
  "trace_id": "0f3e09c2-4c2e-46ea-9cdc-a31ec48b7c12",
  "data": [
    {
      "id": "253eedc47f1c4eb2a370ed2312168f4b",
      "video_url": "https://platform.cdn.acedata.cloud/veo/8f5a90ae.mp4",
      "created_at": "2025-07-25 16:07:43",
      "complete_at": "2025-07-25 16:10:28",
      "state": "succeeded"
    }
  ]
}
```

Return field descriptions:

- `success`: Whether the upsampling task was submitted successfully.
- `task_id`: The ID of this upsampling task.
- `data`: The result of the upsampled video.
  - `video_url`: The address of the upsampled video.

## Billing Instructions

- `action=1080p`: 0.16 Credit / time
- `action=4k`: 0.50 Credit / time
- `action=gif`: 0.13 Credit / time

If the `action` passed in is not within the above enumeration, it will default to a billing of 0.50 Credit. It is recommended to explicitly pass in supported values.

## Asynchronous Callback

The interface also supports asynchronous mode; by passing in `callback_url`, you can receive the result via a POST request after the task is completed. If there is no public callback address, you can also set `async` to `true`, and the interface will immediately return `task_id`, after which you can poll the corresponding task query interface to obtain the result.