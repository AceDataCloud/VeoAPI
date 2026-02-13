# Veo Video Generation API

Veo AI video generation service with text and image input.

API home page: [Ace Data Cloud - Veo Video Generation](https://platform.acedata.cloud/services/73cd74ba-a1bd-4a12-8df6-11d64c38df14)

## Get Started


This article will introduce the integration instructions for the Veo Videos Generation API, which can generate official Veo videos by inputting custom parameters.

### Application Process

To use the API, you need to first apply for the corresponding service on the [Veo Videos Generation API](https://platform.acedata.cloud/documents/63e01dc3-eb21-499e-8049-3025c460058f) page. After entering the page, click the "Acquire" button, as shown in the image below:

![](https://cdn.acedata.cloud/q6ytrc.png)

If you are not logged in or registered, you will be automatically redirected to the login page inviting you to register and log in. After logging in or registering, you will be automatically returned to the current page.

There will be a free quota granted upon your first application, allowing you to use the API for free.

### Basic Usage

First, understand the basic usage method, which involves inputting the prompt `prompt`, the action `action`, the array of reference images for the first and last frames `image_urls`, and the model `model` to obtain the processed result. You first need to simply pass a field `action`, with the value set to `text2video`. It mainly includes three actions: text to video (`text2video`), image to video (`image2video`), and get 1080p video (`get1080p`). Then, we also need to input the model `model`, which currently includes `veo2`, `veo2-fast`, `veo3`, `veo31`, `veo31-fast`, `veo31-fast-ingredients`, and `veo3-fast`. The specific content is as follows:

<p><img src="https://cdn.acedata.cloud/vv5pe8.png" width="500" class="m-auto"></p>

Here we can see that we have set the Request Headers, including:

- `accept`: the format of the response result you want to receive, filled in as `application/json`, which means JSON format.
- `authorization`: the key to call the API, which can be directly selected after application.

Additionally, the Request Body is set, including:

- `model`: the model for generating the video, mainly including `veo2`, `veo2-fast`, `veo3`, `veo31`, `veo31-fast`, `veo31-fast-ingredients`, and `veo3-fast`.
- `action`: the action for this video generation task, mainly including three actions: text to video (`text2video`), image to video (`image2video`), and get 1080p video (`get1080p`).
- `image_urls`: when selecting the image to video action `image2video`, you must upload the reference image links for the first and last frames, with a maximum of three reference images.
- `resolution`: choose the resolution of the generated video, where the veo31 model supports 4k resolution, while other models do not. All models support 1080p and gif resolutions. If this value is not provided, the default is 720p resolution, mainly divided into: `1080p`, `gif`, `4k`.
- `prompt`: the prompt.
- `callback_url`: the URL to receive the callback result.

#### 📌 Model Summary

| **Model Name**                   | **Supported Modes**                          | **Image Input Rules**                        |
| -------------------------- | --------------------------------- | --------------------------------- |
| **veo2-fast**              | Text to video (no image)<br>Image to video mode (with image)           | Only supports **1 image** → First frame mode                |
| **veo3-fast**              | Text to video (no image)<br>Image to video mode (with image)           | **1 image** → First frame mode<br>**3 images** → First and last frame mode |
| **veo31-fast**             | Text to video (no image)<br>Image to video mode (with image)           | **1 image** → First frame mode<br>**3 images** → First and last frame mode |
| **veo31-fast-ingredients** | ❌ Text to video (not supported)<br>✅ **Forced multi-image fusion** (must provide images) | **1-3 images** → Multi-image fusion mode (up to 3 images)        |
| **veo2**                   | Text to video (no image)<br>Image to video mode (with image)           | **1 image** → First frame mode<br>**3 images** → First and last frame mode |
| **veo3**                   | Text to video (no image)<br>Image to video mode (with image)           | **1 image** → First frame mode<br>**3 images** → First and last frame mode |
| **veo31**                  | Text to video (no image)<br>Image to video mode (with image)           | **1 image** → First frame mode<br>**3 images** → First and last frame mode |

---

#### 🔑 Key Rules Explanation

1. **General Logic**:
   - **No image input** → Automatically triggers text to video mode.
   - **Image input present** → Triggers image to video mode (specific behavior determined by the number of images).
2. **Image to Video Mode Types**:
   - **First frame mode** (1 image): The first frame is fixed as the input image.
   - **First and last frame mode** (2 images): The first and last frames are fixed as the input images.
   - **Multi-image fusion mode** (1-3 images): Only supported by `veo31-fast-ingredients`, fuses multiple images to generate a video.
3. **Mode Classification**:
   - **Fast Mode**: `veo2-fast`, `veo3-fast`, `veo31-fast`, `veo31-fast-ingredients`.
   - **Quality Mode**: `veo2`, `veo3`, `veo31` (higher generation quality).

---

#### ⚠️ Notes

- **The only model that requires image input**: `veo31-fast-ingredients` must provide images (1-3 images), otherwise it cannot run.
- **Image quantity limit**:
  - Except for `veo31-fast-ingredients`, other models support a maximum of **3 images** input.

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
- `success`, the status of the video generation task at this time.
- `task_id`, the ID of the video generation task at this time.
- `data`, the result of the video generation task at this time.
  - `id`, the video ID of the video generation task at this time.
  - `video_url`, the video link of the video generation task at this time.
  - `created_at`, the creation time of the video generation task at this time.
  - `complete_at`, the completion time of the video generation task at this time.
  - `state`, the status of the video generation task at this time.

We can see that we have obtained satisfactory video information, and we only need to obtain the generated Veo video based on the video link address in the `data` result.

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


## More

For more info, please check below APIs and integration documents.

| API | Path | Integration Guidance |
| ---- | ---- | ------------ |
| [Veo Videos Generation API](http://platform.acedata.cloud/documents/63e01dc3-eb21-499e-8049-3025c460058f) | `/veo/videos` | [Veo Videos Generation API Integration Guide](http://platform.acedata.cloud/documents/0df6db8d-8b46-4af8-bca5-a15166b938e2) |
| [$t(document_title_veo_tasks_api)](http://platform.acedata.cloud/documents/52778f8b-93ce-4db3-a62c-bcf0a92e5f3c) | `/veo/tasks` | [Veo Tasks API Integration Guide](http://platform.acedata.cloud/documents/add58f98-765a-4c2a-b036-a60ee6044879) |

Base URL: [https://api.acedata.cloud](https://api.acedata.cloud)

## Support

If you meet any issue, check our from [support info](https://platform.acedata.cloud/support).