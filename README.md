# Veo video generation API

Veo AI video generation service creates stunning videos through prompts and starting images.

API home page: [Ace Data Cloud - Veo video generation](https://platform.acedata.cloud/services/73cd74ba-a1bd-4a12-8df6-11d64c38df14)

## Get Started


This article will introduce the integration instructions for the Veo Videos Generation API, which can generate official Veo videos by inputting custom parameters.

Next, we will introduce the integration instructions for the Veo Videos Generation API.

### Application Process

To use the API, you need to first apply for the corresponding service on the [Veo Videos Generation API](https://platform.acedata.cloud/documents/63e01dc3-eb21-499e-8049-3025c460058f) page. After entering the page, click the "Acquire" button, as shown in the image:

![](https://cdn.acedata.cloud/q6ytrc.png)

If you are not logged in or registered, you will be automatically redirected to the login page inviting you to register and log in. After logging in or registering, you will be automatically returned to the current page.

When applying for the first time, there will be a free quota available for you to use the API for free.

### Basic Usage

First, understand the basic usage method, which involves inputting the prompt `prompt`, the action `action`, the array of reference images for the first and last frames `image_urls`, and the model `model` to obtain the processed result. You first need to simply pass a field `action` with the value `text2video`, which mainly includes three actions: text to video (`text2video`), image to video (`image2video`), and get 1080p video (`get_1080p`). Then, we also need to input the model `model`, which currently mainly includes `veo2`, `veo2-fast`, `veo3`, and `veo3-fast`, as detailed below:

<p><img src="https://cdn.acedata.cloud/vv5pe8.png" width="500" class="m-auto"></p>

Here we can see that we have set the Request Headers, including:

- `accept`: the format of the response result you want to receive, filled in as `application/json`, which means JSON format.
- `authorization`: the key to call the API, which can be selected directly after applying.

Additionally, the Request Body is set, including:

- `model`: the model for generating the video, mainly including `veo2`, `veo2-fast`, `veo3`, and `veo3-fast`.
- `action`: the action for this video generation task, mainly including three actions: text to video (`text2video`), image to video (`image2video`), and get 1080p video (`get_1080p`).
- `image_urls`: when selecting the image to video action `image2video`, the links to the reference images for the first and last frames must be uploaded.
- `prompt`: the prompt.
- `callback_url`: the URL for the callback result.

After selection, you can see that the corresponding code is also generated on the right side, as shown in the image:

<p><img src="https://cdn.acedata.cloud/pmwh4y.png" width="500" class="m-auto"></p>

Click the "Try" button to test, as shown in the above image, and we get the following result:

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

We can see that we have obtained satisfactory video information, and we only need to access the generated Veo video using the video link address in `data`.

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

### Image to Video Function

If you want to generate a video based on the first and last frame images, you can set the parameter `action` to `image2video` and input the array of first and last frame image links `image_urls`.

Next, you must fill in the prompt for the next step to customize the generated video, specifying the following content:

- `model`: the model for generating the video, mainly including `veo2`, `veo2-fast`, `veo3`, and `veo3-fast`.
- `image_urls`: when selecting the image to video action `image2video`, the links to the reference images for the first and last frames must be uploaded.
- `prompt`: the prompt.

An example of the filled input is as follows:

<p><img src="https://cdn.acedata.cloud/8wvlqd.png" width="500" class="m-auto"></p>

After filling in, the code is automatically generated as follows:

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

Clicking run, you can find that you will get a result, as follows:

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

It can be seen that the result content is consistent with the previous text, thus achieving the image to video function.


## More

For more info, please check below APIs and integration documents.

| API | Path | Integration Guidance |
| ---- | ---- | ------------ |
| [Veo Videos Generation API](http://platform.acedata.cloud/documents/63e01dc3-eb21-499e-8049-3025c460058f) | `/veo/videos` | [Veo Videos Generation API Integration Guide](http://platform.acedata.cloud/documents/0df6db8d-8b46-4af8-bca5-a15166b938e2) |
| [$t(document_title_veo_tasks_api)](http://platform.acedata.cloud/documents/52778f8b-93ce-4db3-a62c-bcf0a92e5f3c) | `/veo/tasks` | [Veo Tasks API Integration Guide](http://platform.acedata.cloud/documents/add58f98-765a-4c2a-b036-a60ee6044879) |

Base URL: [https://api.acedata.cloud](https://api.acedata.cloud)

## Support

If you meet any issue, check our from [support info](https://platform.acedata.cloud/support).