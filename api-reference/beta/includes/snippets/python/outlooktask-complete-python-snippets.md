---
description: "Automatically generated file. DO NOT MODIFY"
---

```python

# THE PYTHON SDK IS IN PREVIEW. FOR NON-PRODUCTION USE ONLY

graph_client = GraphServiceClient(credentials, scopes)


request_configuration = CompleteRequestBuilder.CompleteRequestBuilderPostRequestConfiguration(
headers = {
		'Prefer' : "outlook.timezone=\"Pacific Standard Time\"",
}

)

result = await graph_client.me.outlook.tasks.by_outlook_task_id('outlookTask-id').complete.post(request_configuration = request_configuration)


```