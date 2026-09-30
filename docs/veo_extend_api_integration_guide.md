# Veo Extend API Integration Instructions

This document will introduce the integration instructions for the Veo Extend API. This interface is used to **extend the duration** of an already generated Veo video, with AI automatically continuing the subsequent scenes.

## Application Process

To use the API, you need to first apply on the corresponding Veo service page. If you are not logged in or registered, you will be automatically redirected to the login page.

## Basic Usage

To call this interface, the following parameters need to be passed:

- `video_id` (required): The task ID of the source video (from `/veo/videos` or this interface itself).
- `model` (required): The model used for extension, **only supports `veo31-fast` and `veo31` models**, other models are not supported.
- `prompt` (optional): A text prompt to guide the extension of the scene.
- `aspect_ratio` (optional): `16:9` or `9:16`, default is `16:9`.
- `resolution` (optional): `720p` or `1080p`.

Request example:

```shell
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

The return result is the same as `/veo/videos`:

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

## Important Restrictions

The `video_id` must come from the current `/veo/videos` line generated or the `data[].id` after extension completion. Video IDs generated from earlier historical lines cannot be used for this interface, and calling it will return `400 bad_request`; please regenerate through `/veo/videos` before extending.

Videos from the extension results of `/veo/extend` can **continue to be extended again by `/veo/extend`**, but **cannot** be processed by the following interfaces:

- `/veo/reshoot` — cannot change camera movements
- `/veo/objects` — cannot add or remove objects

If the passed `video_id` is an extension result, the related interfaces will return a 400 error; please use the original video as the source.

## Billing Instructions

- `model=veo31-fast`: 1.68 Credit / time
- `model=veo31`: 12.00 Credit / time

## Asynchronous Callback

The interface supports asynchronous mode; by passing `callback_url`, you can receive the result via a POST request after the task is completed. If there is no public callback address, you can also set `async` to `true`, and the interface will immediately return `task_id`, after which you can poll the corresponding task query interface to get the result.