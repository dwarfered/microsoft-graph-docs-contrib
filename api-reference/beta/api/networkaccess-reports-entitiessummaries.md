---
title: "reports: entitiesSummaries"
description: "Get the number of users, devices, and workloads per traffic type in a specified time period."
author: Moti-ba
ms.localizationpriority: medium
ms.prod: global-secure-access
doc_type: apiPageType
---

# reports: entitiesSummaries
Namespace: microsoft.graph.networkaccess

[!INCLUDE [beta-disclaimer](../../includes/beta-disclaimer.md)]

Get the number of users, devices, and workloads per traffic type in a specified time period.

## Permissions
Choose the permission or permissions marked as least privileged for this API. Use a higher privileged permission or permissions [only if your app requires it](/graph/permissions-overview#best-practices-for-using-microsoft-graph-permissions). For details about delegated and application permissions, see [Permission types](/graph/permissions-overview#permission-types). To learn more about these permissions, see the [permissions reference](/graph/permissions-reference).

<!-- { "blockType": "permissions", "name": "networkaccess_reports_entitiessummaries" } -->
[!INCLUDE [permissions-table](../includes/permissions/networkaccess-reports-entitiessummaries-permissions.md)]

[!INCLUDE [rbac-global-secure-access-apis-read](../includes/rbac-for-apis/rbac-global-secure-access-apis-read.md)]

## HTTP request

<!-- {
  "blockType": "ignored"
}
-->
``` http
GET /networkAccess/reports/entitiesSummaries(startDateTime={startDateTime},endDateTime={endDateTime})
```

## Function parameters
In the request URL, provide the following query parameters with values.
The following table shows the parameters that can be used with this function.

|Parameter|Type|Description|
|:---|:---|:---|
|startDateTime|DateTimeOffset|The date and time when the reporting period starts.|
|endDateTime|DateTimeOffset|The date and time when the reporting period ends.|


## Request headers
|Name|Description|
|:---|:---|
|Authorization|Bearer {token}. Required.|

## Request body
Don't supply a request body for this method.

## Response

If successful, this function returns a `200 OK` response code and a [microsoft.graph.networkaccess.entitiesSummary](../resources/networkaccess-entitiessummary.md) collection in the response body.

## Examples

### Request
The following example shows a request.
<!-- {
  "blockType": "request",
  "name": "reportsthis.entitiessummaries"
}
-->
``` http
GET https://graph.microsoft.com/beta/networkAccess/reports/entitiesSummaries(startDateTime=2023-01-01T00:00:00Z,endDateTime=2023-01-31T00:00:00Z) 
```


### Response
The following example shows the response.
>**Note:** The response object shown here might be shortened for readability.
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "Collection(microsoft.graph.networkaccess.entitiesSummary)"
}
-->
``` http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "@odata.context": "https://graph.microsoft.com/beta/$metadata#Collection(microsoft.graph.networkaccess.entitiesSummary)",
  "value": [
    {
      "trafficType": "internet",
      "userCount": 1000,
      "deviceCount": 8000,
      "workloadCount": 76000
    },
    {
      "trafficType": "microsoft365",
      "userCount": 100000,
      "deviceCount": 2000,
      "workloadCount": 76000
    }
  ]
}
```

