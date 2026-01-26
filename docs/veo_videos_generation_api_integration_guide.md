# Veo Videos Generation API Integration Instructions

This article will introduce the integration instructions for the Veo Videos Generation API, which can generate official Veo videos by inputting custom parameters.

## Application Process

To use the API, you need to first apply for the corresponding service on the [Veo Videos Generation API](https://platform.acedata.cloud/documents/63e01dc3-eb21-499e-8049-3025c460058f) page. After entering the page, click the "Acquire" button, as shown in the image below:

![](https://cdn.acedata.cloud/q6ytrc.png)

If you are not logged in or registered, you will be automatically redirected to the login page inviting you to register and log in. After logging in or registering, you will be automatically returned to the current page.

There will be a free quota granted upon your first application, allowing you to use the API for free.

## Basic Usage

First, understand the basic usage method, which involves inputting the prompt `prompt`, the action `action`, the array of reference images for the first and last frames `image_urls`, and the model `model` to obtain the processed result. You first need to simply pass a field `action`, with the value set to `text2video`. This mainly includes three actions: text-to-video (`text2video`), image-to-video (`image2video`), and get 1080p video (`get1080p`). Then, we also need to input the model `model`, which currently mainly includes `veo2`, `veo2-fast`, `veo3`, `veo31`, `veo31-fast`, `veo31-fast-ingredients`, and `veo3-fast`, with specific content as follows:

<p><img src="https://cdn.acedata.cloud/vv5pe8.png" width="500" class="m-auto"></p>

Here we can see that we have set the Request Headers, including:

- `accept`: the format of the response result you want to receive, filled in as `application/json`, which means JSON format.
- `authorization`: the key to call the API, which can be directly selected after application.

Additionally, the Request Body is set, including:

- `model`: the model for generating the video, mainly including `veo2`, `veo2-fast`, `veo3`, `veo31`, `veo31-fast`, `veo31-fast-ingredients`, and `veo3-fast`.
- `action`: the action for this video generation task, mainly including three actions: text-to-video (`text2video`), image-to-video (`image2video`), and get 1080p video (`get1080p`).
- `image_urls`: when selecting the image-to-video action `image2video`, it is necessary to upload the reference image links for the first and last frames, with a maximum of three reference images.
- `prompt`: the prompt.
- `callback_url`: the URL to receive the callback result.

### 📌 Model Summary

| **Model Name**                   | **Supported Modes**                          | **Image Input Rules**                        |
| -------------------------- | --------------------------------- | --------------------------------- |
| **veo2-fast**              | Text-to-video (no image)<br>Image-to-video mode (with image)           | Only supports **1 image** → First frame mode                |
| **veo3-fast**              | Text-to-video (no image)<br>Image-to-video mode (with image)           | **1 image** → First frame mode<br>**3 images** → First and last frame mode |
| **veo31-fast**             | Text-to-video (no image)<br>Image-to-video mode (with image)           | **1 image** → First frame mode<br>**3 images** → First and last frame mode |
| **veo31-fast-ingredients** | ❌ Text-to-video (not supported)<br>✅ **Forced multi-image fusion** (must provide images) | **1-3 images** → Multi-image fusion mode (up to 3 images)        |
| **veo2**                   | Text-to-video (no image)<br>Image-to-video mode (with image)           | **1 image** → First frame mode<br>**3 images** → First and last frame mode |
| **veo3**                   | Text-to-video (no image)<br>Image-to-video mode (with image)           | **1 image** → First frame mode<br>**3 images** → First and last frame mode |
| **veo31**                  | Text-to-video (no image)<br>Image-to-video mode (with image)           | **1 image** → First frame mode<br>**3 images** → First and last frame mode |

---

### 🔑 Key Rules Explanation

1. **General Logic**:
   - **No image input** → Automatically triggers text-to-video mode.
   - **Image input present** → Triggers image-to-video mode (specific behavior determined by the number of images).
2. **Image-to-video Mode Types**:
   - **First frame mode** (1 image): The first frame is fixed as the input image.
   - **First and last frame mode** (2 images): The first and last frames are fixed as the input images.
   - **Multi-image fusion mode** (1-3 images): Only `veo31-fast-ingredients` supports this, fusing multiple images to generate a video.
3. **Mode Classification**:
   - **Fast Mode**: `veo2-fast`, `veo3-fast`, `veo31-fast`, `veo31-fast-ingredients`.
   - **Quality Mode**: `veo2`, `veo3`, `veo31` (higher generation quality).

---

### ⚠️ Notes

- **The only model that requires image input**: `veo31-fast-ingredients` must have images provided (1-3 images), otherwise it cannot run.
- **Image quantity limit**:
  - Except for `veo31-fast-ingredients`, other models support a maximum of **3 images** as input.

After selection, you can find that the corresponding code is also generated on the right side, as shown in the image below:

<p><img src="https://cdn.acedata.cloud/pmwh4y.png" width="500" class="m-auto"></p>

Click the "Try" button to conduct a test, as shown in the image above, and we obtained the following result:

```json
{
  "success": true,
  "task_id": "dd01fc69-e1f7-4b68-aa8c-463f6b748d11",
  "trace_id": "9906dac0-1516-41dc-9fe3-067ca1ba8269",
  "data": [
    {
      "id": "253eedc47f1c4eb2a370ed2312168f4b",
      "video_url": "https://platform.cdn.acedata.cloud/veo/dd01fc69-e1f7-4b68-aa8c-463f6b748d11.mp4",
      "created_at": "2025-07-25 16:07:43",
      "complete_at": "2025-07-25 16:10:28",
      "state": "succeeded"
    }
  ]
}
```

The returned result contains multiple fields, described as follows:

- `success`: the status of the video generation task at this time.
- `task_id`: the ID of the video generation task at this time.
- `data`: the result of the video generation task at this time.
  - `id`: the video ID of the video generation task at this time.
  - `video_url`: the video link of the video generation task at this time.
  - `created_at`: the creation time of the video generation task at this time.
  - `complete_at`: the completion time of the video generation task at this time.
  - `state`: the status of the video generation task at this time.
We can see that we have obtained satisfactory video information, and we only need to obtain the generated Veo video based on the video link address in the `data` of the result.

Additionally, if you want to generate the corresponding integration code, you can directly copy the generated code, for example, the CURL code is as follows:

```shell
curl -X POST 'https://api.acedata.cloud/veo/videos' \
-H 'accept: application/json' \
-H 'authorization: Bearer {token}' \
-H 'content-type: application/json' \
-d '{
  "action": "text2video",
  "model": "veo2",
  "prompt": "White ceramic coffee mug on glossy marble countertop with morning window light. Camera slowly rotates 360 degrees around the mug, pausing briefly at the handle."
}'
```

## Image to Video Function

If you want to generate a video based on the first and last frame images, you can set the parameter `action` to `image2video` and input the array of first and last frame image links `image_urls`.

Next, we must fill in the prompt words needed for the next step to customize the generated video, specifying the following content:

- `model`: The model for generating the video, mainly `veo2`, `veo2-fast`, `veo3`, and `veo3-fast`.
- `image_urls`: When selecting the image-to-video action `image2video`, you must upload the reference image links for the first and last frames.
- `prompt`: Prompt words.

The sample input is as follows:

<p><img src="https://cdn.acedata.cloud/8wvlqd.png" width="500" class="m-auto"></p>

After filling it out, the code is automatically generated as follows:

<p><img src="https://cdn.acedata.cloud/tgzfxi.png" width="500" class="m-auto"></p>

The corresponding Python code:

```python
import requests

url = "https://api.acedata.cloud/veo/videos"

headers = {
    "accept": "application/json",
    "authorization": "Bearer {token}",
    "content-type": "application/json"
}

payload = {
    "action": "image2video",
    "model": "veo2",
    "prompt": "Let it dance",
    "image_urls": ["https://cdn.acedata.cloud/7p1jhy.png"]
}

response = requests.post(url, json=payload, headers=headers)
print(response.text)
```

Clicking run, you can find that a result is obtained, as follows:

```json
{
  "success": true,
  "task_id": "98e309f3-35bc-438d-8cb3-4015fc864b87",
  "trace_id": "8bc68066-36de-41ef-ae5e-b7d61ff6aee8",
  "data": [
    {
      "id": "59f12222b1fa4fbe9331ff2400ad1583",
      "video_url": "https://platform.cdn.acedata.cloud/veo/98e309f3-35bc-438d-8cb3-4015fc864b87.mp4",
      "created_at": "2025-07-25 16:13:07",
      "complete_at": "2025-07-25 16:16:12",
      "state": "succeeded"
    }
  ]
}
```

It can be seen that the result content is consistent with the above text, thus achieving the image-to-video function.

## Get 1080p Video Function

If you want to get 1080p for an already generated Veo video, you can set the parameter `action` to `get1080p` and input the ID of the video you need to get 1080p for. The video ID can be obtained based on basic usage, as shown in the figure below:

<p><img src="https://cdn.acedata.cloud/hacabc.png" width="500" class="m-auto"></p>

At this point, you can see that the video ID is:

```json
"id": "59f12222b1fa4fbe9331ff2400ad1583"
```

> Note that the `video_id` here is the ID of the generated video. If you do not know how to generate a video, you can refer to the basic usage above to generate a video.

Next, we must fill in the prompt words needed for the next step to customize the generated video, specifying the following content:

- `model`: The model for generating the video, mainly `veo2`, `veo2-fast`, `veo3`, and `veo3-fast`.
- `video_id`: The reference video ID used to get the 1080p video.

The sample input is as follows:

<p><img src="https://cdn.acedata.cloud/k56fhn.png" width="500" class="m-auto"></p>

After filling it out, the code is automatically generated as follows:

<p><img src="https://cdn.acedata.cloud/8gn4cr.png" width="500" class="m-auto"></p>

Clicking run, you can find that a result is obtained, as follows:

```json
{
  "success": true,
  "task_id": "47a51cfe-2e24-4aba-93b3-546c2dc52984",
  "trace_id": "a8922eec-6f50-4f77-8104-00ded071d59d",
  "data": [
    {
      "id": "59f12222b1fa4fbe9331ff2400ad1583",
      "video_url": "https://platform.cdn.acedata.cloud/veo/47a51cfe-2e24-4aba-93b3-546c2dc52984.mp4",
      "created_at": "2025-07-25 16:13:07",
      "complete_at": "2025-07-25 16:16:12",
      "state": "succeeded"
    }
  ]
}
```

It can be seen that the result content is consistent with the above text, thus achieving the function of obtaining a 1080p video.

## Specify Video Size Generation

If you want to specify the generation of a custom-sized Veo video, you can set the parameter `aspect_ratio` to the desired size. Next, we must fill in the prompt words needed for the next step to customize the generated video, specifying the following content:

- `model`: The model for generating the video, mainly `veo2`, `veo2-fast`, `veo3`, and `veo3-fast`.
- `aspect_ratio`: The size of the video, currently supporting: `16:9`, `16:9`, `3:4`, `4:3`, `1:1`, with the default being `16:9`.
- `translation`: Whether to enable automatic translation of prompt words, default is `false`.
  The sample input is as follows:

<p><img src="https://cdn.acedata.cloud/xau4cm.png" width="500" class="m-auto"></p>

After filling it out, the code is automatically generated as follows:

<p><img src="https://cdn.acedata.cloud/55r589.png" width="500" class="m-auto"></p>

Clicking run, you can find that a result is obtained, as follows:

```json
{
  "success": true,
  "task_id": "d2b93290-ab0e-4d20-ae45-60c062a32687",
  "trace_id": "9834e64d-c8fe-43ae-8114-ee2b5f93d886",
  "data": [
    {
      "id": "fc667e7d3b8f44beaa61a3c339af0e50",
      "video_url": "https://platform.cdn.acedata.cloud/veo/d2b93290-ab0e-4d20-ae45-60c062a32687.mp4",
      "created_at": "2025-08-24 20:09:06",
      "complete_at": "2025-08-24 20:10:45",
      "state": "succeeded"
    }
  ]
}
```

It can be seen that the result content is consistent with the above text, thus achieving the function of generating a specified size video.

## Asynchronous Callback
Due to the relatively long generation time of the Veo Videos Generation API, which takes about 1-2 minutes, if the API does not respond for a long time, the HTTP request will keep the connection open, leading to additional system resource consumption. Therefore, this API also provides support for asynchronous callbacks.

The overall process is as follows: when the client initiates a request, an additional `callback_url` field is specified. After the client makes the API request, the API will immediately return a result containing a `task_id` field, representing the current task ID. When the task is completed, the result of the generated video will be sent to the client-specified `callback_url` in the form of a POST JSON, which also includes the `task_id` field, allowing the task result to be associated by ID.

Let’s understand how to operate specifically through an example.

First, the Webhook callback is a service that can receive HTTP requests, and developers should replace it with the URL of their own HTTP server. For demonstration purposes, we use a public Webhook sample site https://webhook.site/, where you can open the site to get a Webhook URL, as shown in the image:

![](https://cdn.acedata.cloud/tbcnai.png)

Copy this URL, and it can be used as a Webhook. The sample here is `https://webhook.site/aed5cd28-f8aa-4dca-9480-8ec9b42137dc`.

Next, we can set the `callback_url` field to the above Webhook URL and fill in the corresponding parameters, as shown in the image:

<p><img src="https://cdn.acedata.cloud/rgivs2.png" width="500" class="m-auto"></p>

Clicking run, you will immediately receive a result, as follows:

```json
{
  "task_id": "1ebe4f2b-59ba-4385-a4ea-0ce8a3fe12ed"
}
```

After a moment, we can observe the result of the generated video at `https://webhook.site/aed5cd28-f8aa-4dca-9480-8ec9b42137dc`, as shown in the image:

![](https://cdn.acedata.cloud/238i32.png)

The content is as follows:

```json
{
  "success": true,
  "task_id": "1ebe4f2b-59ba-4385-a4ea-0ce8a3fe12ed",
  "trace_id": "d1d53c04-58c5-4c40-bb63-f00188540e56",
  "data": [
    {
      "id": "2f43ceed37944b4d836e1a1899dad0a1",
      "video_url": "https://platform.cdn.acedata.cloud/veo/1ebe4f2b-59ba-4385-a4ea-0ce8a3fe12ed.mp4",
      "created_at": "2025-07-25 17:19:20",
      "complete_at": "2025-07-25 17:21:45",
      "state": "succeeded"
    }
  ]
}
```

It can be seen that the result contains a `task_id` field, and the other fields are similar to the above, allowing the task to be associated through this field.

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

Through this document, you have learned how to use the Veo Videos Generation API to generate videos by inputting prompt words and reference images for the first frame. We hope this document helps you better integrate and use this API. If you have any questions, please feel free to contact our technical support team.