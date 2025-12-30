---
layout: center
class: text-center
---

# N8nController 重構

提取服務層與統一回應處理

<div class="text-sm text-gray-500 mt-4">

<a href="https://github.com/weberyanglalala/Dotnet10Practices/commit/e0cc829707b8d055a58ccabb208c8ab0be9fa671" target="_blank">
=> Git Commit Link
</a>
</div>

---
layout: default
---

# 重構概述

這次重構主要改善了程式碼的組織架構，並引入了統一的回應處理機制。

<v-clicks>

- 🔧 **關注點分離**：將 N8n API 呼叫邏輯從 Controller 提取至 Service 層
- 📦 **統一回應格式**：引入 `OperationResult<T>` 泛型類別
- ✅ **提升可測試性**：透過依賴注入讓單元測試更容易撰寫
- 🎯 **提升可維護性**：集中化的 N8n 邏輯讓未來修改更簡單

</v-clicks>

---
layout: two-cols
layoutClass: gap-16
---

# 重構前的問題

Controller 承擔過多職責

::right::

## 程式碼異味

<v-clicks>

- 🔴 Controller 直接建立 HttpClient
- 🔴 商業邏輯與 HTTP 關注點混雜
- 🔴 難以進行單元測試
- 🔴 缺乏完整的錯誤處理機制

</v-clicks>

---

# 新增：OperationResult.cs (1/2)

不可變的結果包裝類別

````md magic-move {lines: true}
```csharp
// 基本結構：不可變屬性
public class OperationResult<T>
{
    public bool IsSuccess { get; }
    public T Data { get; }
    public string ErrorMessage { get; }
    public int Code { get; }
}
```

```csharp {*|10-16}
// 加入私有建構子確保只能透過工廠方法建立
public class OperationResult<T>
{
    public bool IsSuccess { get; }
    public T Data { get; }
    public string ErrorMessage { get; }
    public int Code { get; }

    private OperationResult(bool isSuccess, T data,
        string errorMessage, int code)
    {
        IsSuccess = isSuccess;
        Data = data;
        ErrorMessage = errorMessage;
        Code = code;
    }
}
```
````

---

# 新增：OperationResult.cs (2/2)

工廠方法確保物件正確建立

````md magic-move {lines: true}
```csharp
private OperationResult(bool isSuccess, T data,
    string errorMessage, int code)
{
    IsSuccess = isSuccess;
    Data = data;
    ErrorMessage = errorMessage;
    Code = code;
}
```

```csharp {*|10-13}
private OperationResult(bool isSuccess, T data,
    string errorMessage, int code)
{
    IsSuccess = isSuccess;
    Data = data;
    ErrorMessage = errorMessage;
    Code = code;
}

public static OperationResult<T> Success(T data, int statusCode = 200)
{
    return new OperationResult<T>(true, data, null, statusCode);
}
```

```csharp {*|10-14}
private OperationResult(bool isSuccess, T data,
    string errorMessage, int code)
{
    IsSuccess = isSuccess;
    Data = data;
    ErrorMessage = errorMessage;
    Code = code;
}

public static OperationResult<T> Failure(
    string errorMessage = "Operation Failed.", int statusCode = 400)
{
    return new OperationResult<T>(false, default, errorMessage, statusCode);
}
```
````

---

# 新增：N8nService.cs (1/7)

定義服務介面

```csharp
using WebApplication1.Common;
using WebApplication1.Controllers.N8n;

namespace WebApplication1.Services;

public interface IN8nService
{
    Task<OperationResult<CreateProductResponse>> CreateProductAsync(
        CreateProductRequest request);
}
```

<div v-click class="mt-4 p-4 bg-blue-50 rounded text-sm">
💡 使用介面設計支援依賴注入與單元測試
</div>

---

# 新增：N8nService.cs (2/7)

建構子與依賴注入

```csharp {*|3-5|7-12}
public class N8nService : IN8nService
{
    private readonly ILogger<N8nService> _logger;
    private readonly IConfiguration _configuration;
    private readonly IHttpClientFactory _httpClientFactory;

    public N8nService(ILogger<N8nService> logger,
        IConfiguration configuration,
        IHttpClientFactory httpClientFactory)
    {
        _logger = logger;
        _configuration = configuration;
        _httpClientFactory = httpClientFactory;
    }
}
```

---

# 新增：N8nService.cs (3/7)

驗證 Webhook Endpoint 設定

```csharp {*|5-6|8-12}
public async Task<OperationResult<CreateProductResponse>>
    CreateProductAsync(CreateProductRequest request)
{
    try {
        var client = _httpClientFactory.CreateClient();
        var endpoint = _configuration["N8nWebhookEndpoint"];

        if (string.IsNullOrEmpty(endpoint))
        {
            _logger.LogError("N8nWebhookEndpoint configuration is missing");
            return OperationResult<CreateProductResponse>.Failure(
                "N8n webhook endpoint not configured", 500);
        }
    }
}
```

---

# 新增：N8nService.cs (4/7)

驗證 API Key 並設定授權標頭

```csharp {*|3-8|10-11}
var endpoint = _configuration["N8nWebhookEndpoint"];
// ... endpoint validation ...

var apiKey = _configuration["N8nApiKey"];
if (string.IsNullOrEmpty(apiKey))
{
    _logger.LogError("N8nApiKey configuration is missing");
    return OperationResult<CreateProductResponse>.Failure(
        "N8n API key not configured", 500);
}

client.DefaultRequestHeaders.Authorization =
    new AuthenticationHeaderValue("Bearer", apiKey);
_logger.LogInformation("Sending request to N8n webhook: {Endpoint}", endpoint);
```

---

# 新增：N8nService.cs (5/7)

發送請求並處理失敗回應

```csharp {*|3|5-11}
_logger.LogInformation("Sending request to N8n webhook: {Endpoint}", endpoint);

var response = await client.PostAsJsonAsync(endpoint, request);

if (!response.IsSuccessStatusCode)
{
    var errorContent = await response.Content.ReadAsStringAsync();
    _logger.LogError("N8n webhook returned error status {StatusCode}: {Error}",
        response.StatusCode, errorContent);
    return OperationResult<CreateProductResponse>.Failure(
        $"N8n webhook error: {response.StatusCode}", (int)response.StatusCode);
}
```

---

# 新增：N8nService.cs (6/7)

處理空回應與反序列化

```csharp {*|3-8|10-16}
var response = await client.PostAsJsonAsync(endpoint, request);

var responseString = await response.Content.ReadAsStringAsync();

if (string.IsNullOrEmpty(responseString))
{
    _logger.LogError("N8n webhook returned empty response");
    return OperationResult<CreateProductResponse>.Failure(
        "Empty response from N8n webhook", 502);
}

var result = JsonSerializer.Deserialize<CreateProductResponse>(responseString);

if (result == null)
{
    _logger.LogError("Failed to deserialize N8n response: {Response}", responseString);
    return OperationResult<CreateProductResponse>.Failure(
        "Invalid response format from N8n webhook", 502);
}
```

---

# 新增：N8nService.cs (7/7)

成功處理與例外捕捉

```csharp {*|3-4|6-10|11-15}
var result = JsonSerializer.Deserialize<CreateProductResponse>(responseString);

_logger.LogInformation("Successfully created product via N8n webhook");
return OperationResult<CreateProductResponse>.Success(result, 200);
}
catch (HttpRequestException ex)
{
    _logger.LogError(ex, "HttpRequestException occurred while calling N8n webhook");
    return OperationResult<CreateProductResponse>.Failure(
        $"Network error: {ex.Message}", 503);
}
catch (Exception ex)
{
    _logger.LogError(ex, "Unexpected error occurred in CreateProductAsync");
    return OperationResult<CreateProductResponse>.Failure(
        "An unexpected error occurred", 500);
}
```

---

# Controller 重構 (1/3)

重構前：直接建立 HttpClient 與處理請求

```csharp {*|7-8|10-12|14-15|17-18|20-25}
[HttpPost]
public async Task<IActionResult> CreateProduct(
    [FromBody] CreateProductRequest request)
{
    // create a httpclient
    var client = new HttpClient();
    // set up request endpoint
    var endpoint = _configuration["N8nWebhookEndpoint"];
    // setup headers
    client.DefaultRequestHeaders.Authorization =
        new AuthenticationHeaderValue("Bearer", _configuration["N8nApiKey"]);
    // send json to the endpoint using http method post
    var response = await client.PostAsJsonAsync(endpoint, request);
    // get response content
    var responseString = await response.Content.ReadAsStringAsync();
    // deserialize CreateProductResponse
    var result = JsonSerializer.Deserialize<CreateProductResponse>(responseString);
    // return Ok(result);
    return Ok(new ApiResponse<CreateProductResponse>
    {
        Data = result,
        Code = 200,
        Message = "取得商品資料成功"
    });
}
```

---

# Controller 重構 (2/3)

重構後：委派給 Service 處理

```csharp {*|1-2|4-9|11-17}
// 注入 IN8nService 而非 IConfiguration
public N8NController(ILogger<N8NController> logger, IN8nService n8nService)
{
    _logger = logger;
    _n8nService = n8nService;
}

[HttpPost]
public async Task<IActionResult> CreateProduct(
    [FromBody] CreateProductRequest request)
{
    var result = await _n8nService.CreateProductAsync(request);

    if (result.IsSuccess)
    {
        // 成功處理...
    }
    else
    {
        // 錯誤處理...
    }
}
```

---

# Controller 重構 (3/3)

完整的成功與錯誤處理

```csharp {*|5-11|12-18}
[HttpPost]
public async Task<IActionResult> CreateProduct(
    [FromBody] CreateProductRequest request)
{
    var result = await _n8nService.CreateProductAsync(request);

    if (result.IsSuccess)
    {
        return Ok(new ApiResponse<CreateProductResponse>
        {
            Data = result.Data,
            Code = result.Code,
            Message = "取得商品資料成功"
        });
    }
    else
    {
        return Problem(
            detail: result.ErrorMessage,
            statusCode: result.Code,
            title: "Create Product Failed"
        );
    }
}
```

---

# 依賴注入設定

Program.cs 的變更

````md magic-move {lines: true}
```csharp
// 重構前
var builder = WebApplication.CreateBuilder(args);

builder.Services.AddControllersWithViews();

var app = builder.Build();
```

```csharp {*|1|6-8}
// 重構後
using WebApplication1.Services;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddControllersWithViews();
builder.Services.AddHttpClient();
builder.Services.AddScoped<IN8nService, N8nService>();

var app = builder.Build();
```
````

---
layout: two-cols
layoutClass: gap-12
---

# 重構前架構

```mermaid
graph TB
    A[HTTP Request] --> B[N8nController]
    B --> C[new HttpClient]
    C --> D[N8n Webhook]
    B --> E[直接處理回應]
    E --> F[ApiResponse]

    style B fill:#ff6b6b
    style C fill:#ff6b6b
    style E fill:#ff6b6b
```

<div class="mt-4 text-sm text-gray-600">
❌ Controller 承擔過多職責
</div>

::right::

# 重構後架構

```mermaid
graph TB
    A[HTTP Request] --> B[N8nController]
    B --> C[IN8nService]
    C --> D[HttpClientFactory]
    D --> E[N8n Webhook]
    C --> F[OperationResult]
    F --> B
    B --> G[ApiResponse]

    style B fill:#51cf66
    style C fill:#51cf66
    style F fill:#51cf66
```

<div class="mt-4 text-sm text-gray-600">
✅ 清楚的職責分離
</div>

---

# 重構帶來的好處 (1/2)

<div class="grid grid-cols-2 gap-6 mt-8">

<div v-click>
<div class="p-6 bg-blue-50 rounded-lg">
<h3 class="text-lg font-bold mb-3">🧪 可測試性提升</h3>
<ul class="text-sm space-y-2">
<li>✓ 可模擬 IN8nService 介面</li>
<li>✓ 可獨立測試服務邏輯</li>
<li>✓ 使用 IHttpClientFactory</li>
</ul>
</div>
</div>

<div v-click>
<div class="p-6 bg-green-50 rounded-lg">
<h3 class="text-lg font-bold mb-3">🔧 可維護性提升</h3>
<ul class="text-sm space-y-2">
<li>✓ 單一職責原則</li>
<li>✓ 程式碼更易於理解</li>
<li>✓ 修改影響範圍小</li>
</ul>
</div>
</div>

</div>

---

# 重構帶來的好處 (2/2)

<div class="grid grid-cols-2 gap-6 mt-8">

<div v-click>
<div class="p-6 bg-purple-50 rounded-lg">
<h3 class="text-lg font-bold mb-3">♻️ 可重用性提升</h3>
<ul class="text-sm space-y-2">
<li>✓ Service 可在多處使用</li>
<li>✓ OperationResult 統一格式</li>
<li>✓ 避免程式碼重複</li>
</ul>
</div>
</div>

<div v-click>
<div class="p-6 bg-orange-50 rounded-lg">
<h3 class="text-lg font-bold mb-3">📊 錯誤處理改善</h3>
<ul class="text-sm space-y-2">
<li>✓ 完整的驗證與日誌記錄</li>
<li>✓ 區分網路錯誤與一般錯誤</li>
<li>✓ 明確的錯誤訊息</li>
</ul>
</div>
</div>

</div>

---

# 關鍵設計決策 (1/2)

<div class="mt-8">

<v-clicks>

### 1️⃣ 不可變的 OperationResult

使用 `{ get; }` 與私有建構子確保物件建立後不可修改

### 2️⃣ 使用 Bearer Token 認證

`AuthenticationHeaderValue("Bearer", apiKey)` 符合標準的 OAuth 2.0 規範

</v-clicks>

</div>

---

# 關鍵設計決策 (2/2)

<div class="mt-8">

<v-clicks>

### 3️⃣ 完整的錯誤處理

- 驗證設定存在
- 檢查空回應
- 驗證反序列化結果
- 區分 HttpRequestException 與一般例外

### 4️⃣ 使用 PostAsJsonAsync

比手動序列化更簡潔，自動處理 Content-Type

</v-clicks>

</div>

---
layout: fact
---

# 從直接建立到依賴注入

Controller 更精簡，Service 層承擔複雜邏輯

---
layout: end
class: text-center
---

# 學習更多

探索更多重構模式與最佳實踐

<div class="mt-8 text-sm text-gray-500">
Commit: [e0cc829707b8d055a58ccabb208c8ab0be9fa671](https://github.com/weberyanglalala/Dotnet10Practices/commit/e0cc829707b8d055a58ccabb208c8ab0be9fa671)
</div>
