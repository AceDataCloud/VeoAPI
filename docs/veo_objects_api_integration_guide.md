# Veo Objects API Integration Instructions

This document will introduce the integration instructions for the Veo Objects API. This interface is used for **adding or removing objects** in a generated Veo video (based on mask-based local repainting).

## Application Process

To use the API, you need to apply on the corresponding Veo service page. If you are not logged in or registered, you will be automatically redirected to the login page.

## Basic Usage

To call this interface, the following parameters are required:

- `video_id` (required): The task ID of the source video. It **cannot** be the output video of `/veo/extend`.
- `action` (required): The type of operation, `insert` or `remove`.
- `prompt`:
  - `action=insert`: **required**, describes the object to be added.
  - `action=remove`: optional, describes the content to be removed (mainly for logging purposes, the actual erased area is determined by `image_mask`).
- `image_mask`:
  - `action=insert`: optional. If not provided, the AI will automatically decide the insertion position; if provided, it will insert in the white pixel area.
  - `action=remove`: **required**, the white pixel area is the object to be erased.

`image_mask` accepts two formats:

1. A **publicly accessible HTTP(S) image URL** (recommended).
2. A **base64 encoded JPEG string**, which can have the `data:image/jpeg;base64,` prefix or be a bare base64.

Request example (insert object without providing a mask, allowing AI to automatically locate):

```shell
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

Request example (remove object by mask):

```shell
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

The return result structure is the same as `/veo/videos`.

## Billing Instructions

- `action=insert` or `remove`: 1.50 Credit / time.

## Asynchronous Callback

The interface supports asynchronous mode; by providing `callback_url`, you can receive the result via a POST request after the task is completed. If there is no public callback address, you can also set `async` to `true`, and the interface will immediately return `task_id`, after which you can poll the corresponding task query interface to obtain the result.