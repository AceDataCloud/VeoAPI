# Veo API

Generate videos through Ace Data Cloud and retrieve the completed video by task ID.

[API documentation](https://platform.acedata.cloud/documents/veo-videos-integration?lang=en&utm_source=github&utm_medium=referral&utm_campaign=evergreen) · [Models and pricing](https://platform.acedata.cloud/services/veo?utm_source=github&utm_medium=referral&utm_campaign=evergreen) · [中文文档](https://platform.acedata.cloud/documents/veo-videos-integration?lang=zh-cn&utm_source=github&utm_medium=referral&utm_campaign=evergreen)

## Get an API key

1. Open [Applications](https://platform.acedata.cloud/console/applications?lang=en&utm_source=github&utm_medium=referral&utm_campaign=evergreen) and sign in or register.
2. Find **General Application** and click the copy button marked **1**. If needed, select **Initialize General Application** first. Confirm access to this service and enough balance for your request.

![Copy the API key from General Application using the button marked 1](https://raw.githubusercontent.com/AceDataCloud/SunoDify/8d3d03570e73324220f111f7fe343da0792a8071/_assets/tutorial/get-api-key-en.png)

3. Save the copied key in your terminal. The examples add the `Bearer` prefix in the HTTP header.

```bash
export ACEDATACLOUD_API_KEY='YOUR_API_KEY'
```

## Quickstart

### Submit a request

```bash
curl --fail-with-body --silent --show-error https://api.acedata.cloud/veo/videos \
  --header "Authorization: Bearer $ACEDATACLOUD_API_KEY" \
  --header 'Content-Type: application/json' \
  --data '{
  "action": "text2video",
  "model": "veo31-fast",
  "prompt": "A sailboat crossing a calm lake at sunrise, slow camera pan",
  "aspect_ratio": "16:9",
  "async": true
}'
```

### Submission response JSON

The following JSON examples illustrate the documented fields. IDs, timestamps and output URLs are placeholders. Use the values returned by your own request.

```json
{
  "task_id": "TASK_ID"
}
```

Save `task_id` and query that same task.

### Query the task

```bash
curl --fail-with-body --silent --show-error https://api.acedata.cloud/veo/tasks \
  --header "Authorization: Bearer $ACEDATACLOUD_API_KEY" \
  --header 'Content-Type: application/json' \
  --data '{"action":"retrieve","id":"TASK_ID"}'
```

### Processing response JSON

```json
{
  "id": "TASK_ID",
  "type": "videos",
  "created_at": 1790000000,
  "trace_id": "TRACE_ID",
  "request": {
    "action": "text2video",
    "model": "veo31-fast",
    "prompt": "A sailboat crossing a calm lake at sunrise, slow camera pan",
    "aspect_ratio": "16:9",
    "async": true
  },
  "finished_at": null
}
```

Query again after a few seconds while processing continues.

### Completed response JSON

```json
{
  "id": "TASK_ID",
  "type": "videos",
  "created_at": 1790000000,
  "trace_id": "TRACE_ID",
  "request": {
    "action": "text2video",
    "model": "veo31-fast",
    "prompt": "A sailboat crossing a calm lake at sunrise, slow camera pan",
    "aspect_ratio": "16:9",
    "async": true
  },
  "finished_at": 1790000045,
  "elapsed": 45,
  "response": {
    "success": true,
    "task_id": "TASK_ID",
    "trace_id": "TRACE_ID",
    "data": [
      {
        "id": "VIDEO_ID",
        "video_url": "https://media.example.com/result.mp4",
        "state": "succeeded"
      }
    ]
  }
}
```

Continue while `finished_at` is null or absent. When the task finishes, inspect `response.success` and the result state. On success, open `response.data[0].video_url`. On failure, inspect `response.error` and retain the original task ID for support.

Keep querying the original task after a timeout or page reload. Submit again when you intend to start another generation.

## APIs and Guides

Explore the supported endpoints and integration guides for Veo Video Generation.

| API | Path | Integration Guidance |
| ---- | ---- | ------------ |
| [Veo Videos Generation API](https://platform.acedata.cloud/documents/veo-videos?utm_source=github&utm_medium=referral&utm_campaign=evergreen) | `/veo/videos` | [Veo Videos Generation API Integration Guide](https://platform.acedata.cloud/documents/veo-videos-integration?utm_source=github&utm_medium=referral&utm_campaign=evergreen) |
| [Veo Tasks API](https://platform.acedata.cloud/documents/veo-tasks?utm_source=github&utm_medium=referral&utm_campaign=evergreen) | `/veo/tasks` | [Veo Tasks API Integration Guide](https://platform.acedata.cloud/documents/veo-tasks-integration?utm_source=github&utm_medium=referral&utm_campaign=evergreen) |
| Veo Upsample API | `/veo/upsample` | [Veo Upsample API Integration Guide](docs/veo_upsample_api_integration_guide.md) |
| Veo Extend API | `/veo/extend` | [Veo Extend API Integration Guide](docs/veo_extend_api_integration_guide.md) |
| Veo Reshoot API | `/veo/reshoot` | [Veo Reshoot API Integration Guide](docs/veo_reshoot_api_integration_guide.md) |
| Veo Objects API | `/veo/objects` | [Veo Objects API Integration Guide](docs/veo_objects_api_integration_guide.md) |

## Troubleshooting

- For authentication errors, open [Manage Keys](https://platform.acedata.cloud/console/credentials?lang=en&utm_source=github&utm_medium=referral&utm_campaign=evergreen) to check the key’s allowed APIs, expiration and usage limit.
- For insufficient balance, open the application and review its balance and the current service price.
- For request validation errors, compare the model and required fields with the linked API reference.
- For a long-running or failed task, retain `task_id` and `trace_id` when contacting [support](https://platform.acedata.cloud/support?utm_source=github&utm_medium=referral&utm_campaign=evergreen).

## Trial account concurrency

Accounts with neither a successful paid top-up nor an administrator's manual credit addition may run **one billable request at a time across the entire account**, shared by all API keys and services. Rewards, redemption cards and transferred credits do not count as paid top-ups. A successful paid top-up or manual credit addition removes this trial restriction; balance and key usage-limit checks still apply.

A new billable request returns `429 too_many_requests` while another is running. Wait for the active request's final result before submitting the next one. Free task polling remains available.

For async APIs, acceptance or a task ID is not completion: keep querying the same task until its final result. A disconnect or proxy timeout does not mean execution stopped. If a task has no terminal result for a long time, contact support with `trace_id`.

This restriction changes neither trial API availability nor pricing; fees are settled after execution based on actual usage.
