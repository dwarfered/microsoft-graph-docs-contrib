---
author: "vanshisingh"
description: "Get newly created, updated, or deleted sites without having to perform a full read of the entire sites collection."
title: "sites: delta"
ms.localizationpriority: "medium"
ms.prod: "sharepoint"
doc_type: apiPageType
---

# sites: delta

[!INCLUDE [beta-disclaimer](../../includes/beta-disclaimer.md)]

Get newly created, updated, or deleted [sites](../resources/site.md) without having to perform a full read of the entire sites collection.

Your app begins by calling `delta` without any parameters.
The service starts enumerating sites, returning pages of changes to sites and either an **@odata.nextLink** or an **@odata.deltaLink**.
Your app should continue calling with the **@odata.nextLink** until you see an **@odata.deltaLink** returned.

After you have received all the changes, you can apply them to your local state.
To check for changes in the future, call `delta` again with the **@odata.deltaLink** from the previous response.

Deleted sites are returned with the [deleted](../resources/deleted.md) facet.
Resources with this property set should be removed from your local state.

## Permissions

One of the following permissions is required to call this API. To learn more, including how to choose permissions, see [Permissions](/graph/permissions-reference).

|Permission type      | Permissions (from least to most privileged)              |
|:--------------------|:---------------------------------------------------------|
|Delegated (work or school account) | Not supported.  |
|Delegated (personal Microsoft account) | Not supported.   |
|Application | Sites.Read.All, Sites.ReadWrite.All |

## HTTP request

<!-- { "blockType": "ignored" } -->

```http
GET /sites/delta
```

## Query parameters

In the request URL, you can include the following optional query parameter.

| Parameter    | Type   | Description                                                                                                                          |
|:-------------|:-------|:-------------------------------------------------------------------------------------------------------------------------------------|
| token        | String | If unspecified, enumerates the current state of the hierarchy. If `latest`, returns an empty response with the latest delta token. If a previous delta token, returns a new state since that token.|

This method also supports the `$select`, `$expand`, and `$top` [OData query parameters](/graph/query-parameters) to customize the response.

## Request headers

|Header       |Value                    |
|-------------|-------------------------|
|Authorization|Bearer {token}. Required.|

## Request body

Don't supply a request body for this method.

## Response

If successful, this method returns a `200 OK` response code and a collection of [site](../resources/site.md) objects in the response body.

In addition to the collection of **site** objects, the response also includes one of the following properties.

| Name             | Value  | Description                                                                                                                                     |
|:-----------------|:-------|:------------------------------------------------------------------------------------------------------------------------------------------------|
| @odata.nextLink  | URL    | A URL to retrieve the next available page of changes if there are additional changes in the current set.                                        |
| @odata.deltaLink | URL    | A URL returned instead of **@odata.nextLink** after all current changes have been returned. Use this property to read the next set of changes in the future. |

In some cases, the service returns a `410 Gone` response code with an error response that contains one of the following error codes, and a `Location` header that contains a new `nextLink` that starts a fresh delta enumeration. This occurs when the service can't provide a list of changes for a given token; for example, if a client tries to reuse an old token after being disconnected for a long time, or if the server state has changed and a new token is required.

After the full enumeration is completed, compare the returned sites with your local state and follow the instructions based on the error type.

| Error type                     | Instructions                                                                                                                               |
|:-------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------|
| resyncChangesApplyDifferences  | Replace any local sites with the versions from the server (including deletes) if you're sure that the service was up-to-date with your local changes when you last synchronized. Upload any local changes that the server doesn't know about. |
| resyncChangesUploadDifferences | Upload any local sites that the service didn't return and upload any sites that differ from the versions from the server. Keep both copies if you're not sure which one is more up-to-date.                                       |

In addition to the resync errors and for more details about how errors are returned, see [Microsoft Graph error responses and resource types](/graph/errors).

## Examples

### Example 1: Initial request

The following example shows the initial request and how to call this API to establish your local state.

#### Request

The following example shows the initial request.

# [HTTP](#tab/http)
<!-- { "blockType": "request", "name": "get_sites_delta_first", "tags": "service.graph" } -->

```msgraph-interactive
GET https://graph.microsoft.com/beta/sites/delta
```

# [C#](#tab/csharp)
[!INCLUDE [sample-code](../includes/snippets/csharp/get-sites-delta-first-csharp-snippets.md)]
[!INCLUDE [sdk-documentation](../includes/snippets/snippets-sdk-documentation-link.md)]

# [CLI](#tab/cli)
[!INCLUDE [sample-code](../includes/snippets/cli/get-sites-delta-first-cli-snippets.md)]
[!INCLUDE [sdk-documentation](../includes/snippets/snippets-sdk-documentation-link.md)]

# [Go](#tab/go)
[!INCLUDE [sample-code](../includes/snippets/go/get-sites-delta-first-go-snippets.md)]
[!INCLUDE [sdk-documentation](../includes/snippets/snippets-sdk-documentation-link.md)]

# [Java](#tab/java)
[!INCLUDE [sample-code](../includes/snippets/java/get-sites-delta-first-java-snippets.md)]
[!INCLUDE [sdk-documentation](../includes/snippets/snippets-sdk-documentation-link.md)]

# [JavaScript](#tab/javascript)
[!INCLUDE [sample-code](../includes/snippets/javascript/get-sites-delta-first-javascript-snippets.md)]
[!INCLUDE [sdk-documentation](../includes/snippets/snippets-sdk-documentation-link.md)]

# [PHP](#tab/php)
[!INCLUDE [sample-code](../includes/snippets/php/get-sites-delta-first-php-snippets.md)]
[!INCLUDE [sdk-documentation](../includes/snippets/snippets-sdk-documentation-link.md)]

# [PowerShell](#tab/powershell)
[!INCLUDE [sample-code](../includes/snippets/powershell/get-sites-delta-first-powershell-snippets.md)]
[!INCLUDE [sdk-documentation](../includes/snippets/snippets-sdk-documentation-link.md)]

# [Python](#tab/python)
[!INCLUDE [sample-code](../includes/snippets/python/get-sites-delta-first-python-snippets.md)]
[!INCLUDE [sdk-documentation](../includes/snippets/snippets-sdk-documentation-link.md)]

---

#### Response

The following example shows the response that includes the first page of changes and the **@odata.nextLink** property that indicates that no more sites are available in the current set of sites. Your app should continue to request the URL value of **@odata.nextLink** until all pages of sites have been retrieved.

<!-- { "blockType": "response", "@odata.type": "Collection(microsoft.graph.site)", "truncated": true, "scope": "site.read" } -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
  "value": [
    {
      "id": "contoso.sharepoint.com,da60e844-ba1d-49bc-b4d4-d5e36bae9019,712a596e-90a1-49e3-9b48-bfa80bee8740",
      "name": "teamSiteA"
    },
    {
      "id": "contoso.sharepoint.com,da60e844-ba1d-49bc-b4d4-d5e36bae9019,0271110f-634f-4300-a841-3a8a2e851851",
      "name": "teamSiteB"
    },
    {
      "id": "contoso.sharepoint.com,da60e844-ba1d-49bc-b4d4-d5e36bae9019,0271110f-634f-4300-a841-3a8a2e851851",
      "name": "teamSiteC"
    }
  ],
  "@odata.nextLink": "https://graph.microsoft.com/beta/sites/delta?token=1230919asd190410jlka"
}
```

### Example 2: Last page request

The following example shows a request that accesses the last page in a set and how to call this API to update your local state.

#### Request

The following example shows a request after the initial request.

# [HTTP](#tab/http)
<!-- { "blockType": "request", "name": "get-sites-delta-last", "tags": "service.graph" }-->

```msgraph-interactive
GET https://graph.microsoft.com/beta/sites/delta?token=1230919asd190410jlka
```

# [C#](#tab/csharp)
[!INCLUDE [sample-code](../includes/snippets/csharp/get-sites-delta-last-csharp-snippets.md)]
[!INCLUDE [sdk-documentation](../includes/snippets/snippets-sdk-documentation-link.md)]

# [CLI](#tab/cli)
[!INCLUDE [sample-code](../includes/snippets/cli/get-sites-delta-last-cli-snippets.md)]
[!INCLUDE [sdk-documentation](../includes/snippets/snippets-sdk-documentation-link.md)]

# [Go](#tab/go)
[!INCLUDE [sample-code](../includes/snippets/go/get-sites-delta-last-go-snippets.md)]
[!INCLUDE [sdk-documentation](../includes/snippets/snippets-sdk-documentation-link.md)]

# [Java](#tab/java)
[!INCLUDE [sample-code](../includes/snippets/java/get-sites-delta-last-java-snippets.md)]
[!INCLUDE [sdk-documentation](../includes/snippets/snippets-sdk-documentation-link.md)]

# [JavaScript](#tab/javascript)
[!INCLUDE [sample-code](../includes/snippets/javascript/get-sites-delta-last-javascript-snippets.md)]
[!INCLUDE [sdk-documentation](../includes/snippets/snippets-sdk-documentation-link.md)]

# [PHP](#tab/php)
[!INCLUDE [sample-code](../includes/snippets/php/get-sites-delta-last-php-snippets.md)]
[!INCLUDE [sdk-documentation](../includes/snippets/snippets-sdk-documentation-link.md)]

# [PowerShell](#tab/powershell)
[!INCLUDE [sample-code](../includes/snippets/powershell/get-sites-delta-last-powershell-snippets.md)]
[!INCLUDE [sdk-documentation](../includes/snippets/snippets-sdk-documentation-link.md)]

# [Python](#tab/python)
[!INCLUDE [sample-code](../includes/snippets/python/get-sites-delta-last-python-snippets.md)]
[!INCLUDE [sdk-documentation](../includes/snippets/snippets-sdk-documentation-link.md)]

---

#### Response

The following example shows the response that indicates that the site named `teamSiteA` was deleted and the site `teamSiteB` was either added or modified between the initial request and this request to update the local state.

The final page of sites includes the **@odata.deltaLink** property that provides the URL that can be used later to retrieve changes since the current set of sites.

<!-- { "blockType": "response", "truncated": true, "@odata.type": "Collection(microsoft.graph.site)", "scope": "site.read" } -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
  "value": [
    {
      "id": "contoso.sharepoint.com,da60e844-ba1d-49bc-b4d4-d5e36bae9019,712a596e-90a1-49e3-9b48-bfa80bee8740",
      "name": "teamSiteA",
      "deleted": {
        "state": "deleted"
      }
    },
    {
      "id": "contoso.sharepoint.com,da60e844-ba1d-49bc-b4d4-d5e36bae9019,0271110f-634f-4300-a841-3a8a2e851851",
      "name": "teamSiteB"
    }
  ],
  "@odata.deltaLink": "https://graph.microsoft.com/beta/sites/delta?token=1230919asd190410jlka"
}
```

### Example 3: Delta link request

In some scenarios, you might want to request the current `deltaLink` value without first enumerating all of the sites, lists, and webs. This can be useful if your app only wants to know about changes and doesn't need to know about existing sites.
To retrieve the latest `deltaLink`, call `delta` with the query string parameter `?token=latest`.

> **Note:** If you want to maintain a full local representation of the resources, you must use `delta` for the initial enumeration. Using `delta` is the only way to guarantee that you've read all of the data you need to.

#### Request

The following example shows a request.

# [HTTP](#tab/http)
<!-- { "blockType": "request", "name": "get-sites-delta-latest", "scopes": "sites.read", "tags": "service.graph", "target": "action" } -->

```msgraph-interactive
GET https://graph.microsoft.com/beta/sites/delta?token=latest
```

# [C#](#tab/csharp)
[!INCLUDE [sample-code](../includes/snippets/csharp/get-sites-delta-latest-csharp-snippets.md)]
[!INCLUDE [sdk-documentation](../includes/snippets/snippets-sdk-documentation-link.md)]

# [CLI](#tab/cli)
[!INCLUDE [sample-code](../includes/snippets/cli/get-sites-delta-latest-cli-snippets.md)]
[!INCLUDE [sdk-documentation](../includes/snippets/snippets-sdk-documentation-link.md)]

# [Go](#tab/go)
[!INCLUDE [sample-code](../includes/snippets/go/get-sites-delta-latest-go-snippets.md)]
[!INCLUDE [sdk-documentation](../includes/snippets/snippets-sdk-documentation-link.md)]

# [Java](#tab/java)
[!INCLUDE [sample-code](../includes/snippets/java/get-sites-delta-latest-java-snippets.md)]
[!INCLUDE [sdk-documentation](../includes/snippets/snippets-sdk-documentation-link.md)]

# [JavaScript](#tab/javascript)
[!INCLUDE [sample-code](../includes/snippets/javascript/get-sites-delta-latest-javascript-snippets.md)]
[!INCLUDE [sdk-documentation](../includes/snippets/snippets-sdk-documentation-link.md)]

# [PHP](#tab/php)
[!INCLUDE [sample-code](../includes/snippets/php/get-sites-delta-latest-php-snippets.md)]
[!INCLUDE [sdk-documentation](../includes/snippets/snippets-sdk-documentation-link.md)]

# [PowerShell](#tab/powershell)
[!INCLUDE [sample-code](../includes/snippets/powershell/get-sites-delta-latest-powershell-snippets.md)]
[!INCLUDE [sdk-documentation](../includes/snippets/snippets-sdk-documentation-link.md)]

# [Python](#tab/python)
[!INCLUDE [sample-code](../includes/snippets/python/get-sites-delta-latest-python-snippets.md)]
[!INCLUDE [sdk-documentation](../includes/snippets/snippets-sdk-documentation-link.md)]

---

#### Response

The following example shows the response.

<!-- { "blockType": "response", "isEmpty": true, "@odata.type": "Collection(microsoft.graph.site)" } -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
  "value": [ ],
  "@odata.deltaLink": "https://graph.microsoft.com/beta/sites/delta?token=1230919asd190410jlka"
}
```

## See also
- [Use delta query to track changes in Microsoft Graph data](/graph/delta-query-overview)
- [Best practices for discovering files and detecting changes at scale](/onedrive/developer/rest-api/concepts/scan-guidance)

<!-- {
  "type": "#page.annotation",
  "description": "Sync changes from the service to your client state.",
  "keywords": "sync,delta,changes,$delta",
  "section": "documentation",
  "suppressions": [],
  "tocPath": "Sites/Get delta"
} -->
