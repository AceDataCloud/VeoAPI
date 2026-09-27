# Veo Reshoot API Integration Instructions

This document will introduce the integration instructions for the Veo Reshoot API. This interface is used to **regenerate** a previously generated Veo video according to a **new camera motion style**, keeping the visual content consistent, but the camera position and movement will be reinterpreted according to `motion_type`.

## Application Process

To use the API, you need to first apply on the corresponding Veo service page. If you are not logged in or registered, you will be automatically redirected to the login page.

## Basic Usage

To call this interface, the following parameters need to be passed:

- `video_id` (required): The task ID of the source video. **Cannot** be the output video of `/veo/extend`.
- `motion_type` (required): The new camera motion style, using concise aliases in uppercase underscore format.

### Supported Values for `motion_type`

| Alias                             | Meaning           |
| ------------------------------ | ------------ |
| `STATIONARY`                   | Camera fixed in place       |
| `STATIONARY_UP`                | Camera fixed, tilting up    |
| `STATIONARY_DOWN`              | Camera fixed, tilting down    |
| `STATIONARY_LEFT`              | Camera fixed, panning left      |
| `STATIONARY_RIGHT`             | Camera fixed, panning right      |
| `STATIONARY_DOLLY_IN_ZOOM_OUT` | Camera fixed, dolly in + zoom out |
| `STATIONARY_DOLLY_OUT_ZOOM_IN` | Camera fixed, dolly out + zoom in |
| `UP`                           | Camera rising         |
| `DOWN`                         | Camera descending         |
| `LEFT_TO_RIGHT`                | Camera panning from left to right     |
| `RIGHT_TO_LEFT`                | Camera panning from right to left     |
| `FORWARD`                      | Camera moving forward       |
| `BACKWARD`                     | Camera moving backward        |
| `DOLLY_IN_ZOOM_OUT`            | Moving dolly in + zoom out   |
| `DOLLY_OUT_ZOOM_IN`            | Moving dolly out + zoom in   |

Request example:

```shell
curl -X POST 'https://api.acedata.cloud/veo/reshoot' \
  -H 'accept: application/json' \
  -H 'authorization: Bearer {token}' \
  -H 'content-type: application/json' \
  -d '{
    "video_id": "dd01fc69-e1f7-4b68-aa8c-463f6b748d11",
    "motion_type": "LEFT_TO_RIGHT"
  }'
```

The structure of the return result is the same as `/veo/videos`.

## Billing Instructions

- Single reshoot: 1.20 Credit.

## Asynchronous Callback

The interface supports asynchronous mode; by passing in `callback_url`, you can receive the result via a POST request after the task is completed. If there is no public callback address, you can also set `async` to `true`, and the interface will immediately return `task_id`, after which you can poll the corresponding task query interface to obtain the result.