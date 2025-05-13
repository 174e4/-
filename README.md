## API基礎概念

API（應用程式介面）是允許不同軟體元件之間進行溝通的機制。它定義了各種軟體元件如何互相請求和交換資訊。

### API的類型

1. **REST API** - 使用HTTP請求管理資料
2. **SOAP API** - 使用XML進行訊息交換
3. **GraphQL API** - 查詢語言型API，客戶端可以精確指定需要的資料
4. **WebSocket API** - 提供全雙工通訊通道
5. **RPC API** - 遠端程序調用

## REST API詳細教學

REST是目前最流行的API架構，所以我們將重點介紹。

### REST基礎

REST API遵循以下原則：
- 無狀態通訊
- 客戶端-伺服器架構
- 可緩存性
- 統一介面
- 分層系統

### HTTP方法

REST API使用標準HTTP方法：

- **GET** - 檢索資源
- **POST** - 創建新資源
- **PUT** - 更新現有資源
- **PATCH** - 部分更新資源
- **DELETE** - 刪除資源

### 實作REST API的步驟

我將為您創建一個使用Node.js和Express的REST API實例：​​​​​​​​​​​​​​​​

## API文檔和測試

優秀的API需要完善的文檔，以下是文檔範例：
<antArtifact None>
# 書籍API文檔

## 基本信息

- **基礎URL**: `http://localhost:3000/api`
- **內容類型**: `application/json`

## 端點

### 獲取所有書籍

- **URL**: `/books`
- **方法**: `GET`
- **成功響應**: 
  - **狀態碼**: 200
  - **響應體**: 書籍列表
  ```json
  [
    {
      "id": 1,
      "title": "哈利波特",
      "author": "J.K. 羅琳"
    },
    {
      "id": 2,
      "title": "魔戒",
      "author": "J.R.R. 托爾金"
    }
  ]
  ```

### 獲取特定書籍

- **URL**: `/books/:id`
- **方法**: `GET`
- **URL參數**: `id` - 書籍ID
- **成功響應**: 
  - **狀態碼**: 200
  - **響應體**: 書籍對象
  ```json
  {
    "id": 1,
    "title": "哈利波特",
    "author": "J.K. 羅琳"
  }
  ```
- **失敗響應**:
  - **狀態碼**: 404
  - **響應體**: 
  ```json
  {
    "message": "找不到書籍"
  }
  ```

### 創建新書籍

- **URL**: `/books`
- **方法**: `POST`
- **請求體**:
  ```json
  {
    "title": "新書標題",
    "author": "作者名"
  }
  ```
- **成功響應**: 
  - **狀態碼**: 201
  - **響應體**: 新創建的書籍
  ```json
  {
    "id": 3,
    "title": "新書標題",
    "author": "作者名"
  }
  ```
- **失敗響應**:
  - **狀態碼**: 400
  - **響應體**: 
  ```json
  {
    "message": "需要提供標題和作者"
  }
  ```

### 更新書籍

- **URL**: `/books/:id`
- **方法**: `PUT`
- **URL參數**: `id` - 書籍ID
- **請求體**:
  ```json
  {
    "title": "更新後的標題",
    "author": "更新後的作者"
  }
  ```
- **成功響應**: 
  - **狀態碼**: 200
  - **響應體**: 更新後的書籍
  ```json
  {
    "id": 1,
    "title": "更新後的標題",
    "author": "更新後的作者"
  }
  ```
- **失敗響應**:
  - **狀態碼**: 404
  - **響應體**: 
  ```json
  {
    "message": "找不到書籍"
  }
  ```

### 刪除書籍

- **URL**: `/books/:id`
- **方法**: `DELETE`
- **URL參數**: `id` - 書籍ID
- **成功響應**: 
  - **狀態碼**: 200
  - **響應體**: 被刪除的書籍
  ```json
  {
    "id": 1,
    "title": "哈利波特",
    "author": "J.K. 羅琳"
  }
  ```
- **失敗響應**:
  - **狀態碼**: 404
  - **響應體**: 
  ```json
  {
    "message": "找不到書籍"
  }
  ```

## 使用測試工具

您可以使用以下工具測試API:
- Postman
- cURL
- 瀏覽器開發者工具
</antArtifact>


## API身份驗證

保護API是至關重要的。以下是常用的身份驗證方法：
<antArtifact None>
// JWT驗證示例
const express = require('express');
const bodyParser = require('body-parser');
const jwt = require('jsonwebtoken');
const app = express();

app.use(bodyParser.json());

// 密鑰（在實際應用中應存儲在環境變量中）
const SECRET_KEY = 'your_secret_key';

// 用戶數據（在實際應用中應使用數據庫）
const users = [
  {
    id: 1,
    username: 'user1',
    password: 'password1', // 實際應用中密碼應該被哈希
    role: 'user'
  },
  {
    id: 2,
    username: 'admin',
    password: 'admin123',
    role: 'admin'
  }
];

// 登錄路由
app.post('/api/login', (req, res) => {
  const { username, password } = req.body;
  
  // 查找用戶
  const user = users.find(u => u.username === username && u.password === password);
  
  if (!user) {
    return res.status(401).json({ message: '無效的用戶名或密碼' });
  }
  
  // 創建令牌
  const token = jwt.sign(
    { id: user.id, username: user.username, role: user.role },
    SECRET_KEY,
    { expiresIn: '1h' }
  );
  
  res.json({ token });
});

// 驗證令牌的中間件
const authenticateToken = (req, res, next) => {
  const authHeader = req.headers['authorization'];
  const token = authHeader && authHeader.split(' ')[1]; // Bearer TOKEN
  
  if (!token) {
    return res.status(401).json({ message: '未提供訪問令牌' });
  }
  
  jwt.verify(token, SECRET_KEY, (err, user) => {
    if (err) {
      return res.status(403).json({ message: '無效或過期的令牌' });
    }
    
    req.user = user;
    next();
  });
};

// 角色授權中間件
const authorizeRole = (roles = []) => {
  return (req, res, next) => {
    if (!roles.includes(req.user.role)) {
      return res.status(403).json({ message: '不允許訪問此資源' });
    }
    next();
  };
};

// 受保護的路由
app.get('/api/user-info', authenticateToken, (req, res) => {
  res.json({ user: req.user });
});

// 需要管理員權限的路由
app.get('/api/admin-only', authenticateToken, authorizeRole(['admin']), (req, res) => {
  res.json({ message: '只有管理員可以訪問此資源' });
});

// 啟動服務器
const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`服務器運行在端口 ${PORT}`);
});

</antArtifact>


## API請求示例

以下是如何使用不同編程語言調用API的示例：
<antArtifact None>
# 使用不同編程語言調用API的示例

## JavaScript (使用Fetch API)

```javascript
// 獲取所有書籍
fetch('http://localhost:3000/api/books')
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error('錯誤:', error));

// 獲取特定書籍
fetch('http://localhost:3000/api/books/1')
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error('錯誤:', error));

// 創建新書籍
fetch('http://localhost:3000/api/books', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    title: '新書籍',
    author: '新作者'
  }),
})
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error('錯誤:', error));

// 更新書籍
fetch('http://localhost:3000/api/books/1', {
  method: 'PUT',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    title: '更新的標題',
    author: '更新的作者'
  }),
})
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error('錯誤:', error));

// 刪除書籍
fetch('http://localhost:3000/api/books/1', {
  method: 'DELETE',
})
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error('錯誤:', error));
```

## Python (使用requests庫)

```python
import requests

base_url = 'http://localhost:3000/api/books'

# 獲取所有書籍
response = requests.get(base_url)
print(response.json())

# 獲取特定書籍
response = requests.get(f'{base_url}/1')
print(response.json())

# 創建新書籍
new_book = {
    'title': '新書籍',
    'author': '新作者'
}
response = requests.post(base_url, json=new_book)
print(response.json())

# 更新書籍
updated_book = {
    'title': '更新的標題',
    'author': '更新的作者'
}
response = requests.put(f'{base_url}/1', json=updated_book)
print(response.json())

# 刪除書籍
response = requests.delete(f'{base_url}/1')
print(response.json())
```

## PHP (使用cURL)

```php
<?php
// 獲取所有書籍
function getAllBooks() {
    $ch = curl_init();
    curl_setopt($ch, CURLOPT_URL, 'http://localhost:3000/api/books');
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    $response = curl_exec($ch);
    curl_close($ch);
    return json_decode($response, true);
}

// 獲取特定書籍
function getBook($id) {
    $ch = curl_init();
    curl_setopt($ch, CURLOPT_URL, "http://localhost:3000/api/books/{$id}");
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    $response = curl_exec($ch);
    curl_close($ch);
    return json_decode($response, true);
}

// 創建新書籍
function createBook($title, $author) {
    $data = json_encode([
        'title' => $title,
        'author' => $author
    ]);
    
    $ch = curl_init();
    curl_setopt($ch, CURLOPT_URL, 'http://localhost:3000/api/books');
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($ch, CURLOPT_POST, true);
    curl_setopt($ch, CURLOPT_POSTFIELDS, $data);
    curl_setopt($ch, CURLOPT_HTTPHEADER, [
        'Content-Type: application/json',
        'Content-Length: ' . strlen($data)
    ]);
    
    $response = curl_exec($ch);
    curl_close($ch);
    return json_decode($response, true);
}

// 更新書籍
function updateBook($id, $title, $author) {
    $data = json_encode([
        'title' => $title,
        'author' => $author
    ]);
    
    $ch = curl_init();
    curl_setopt($ch, CURLOPT_URL, "http://localhost:3000/api/books/{$id}");
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "PUT");
    curl_setopt($ch, CURLOPT_POSTFIELDS, $data);
    curl_setopt($ch, CURLOPT_HTTPHEADER, [
        'Content-Type: application/json',
        'Content-Length: ' . strlen($data)
    ]);
    
    $response = curl_exec($ch);
    curl_close($ch);
    return json_decode($response, true);
}

// 刪除書籍
function deleteBook($id) {
    $ch = curl_init();
    curl_setopt($ch, CURLOPT_URL, "http://localhost:3000/api/books/{$id}");
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "DELETE");
    
    $response = curl_exec($ch);
    curl_close($ch);
    return json_decode($response, true);
}
?>
```

## Java (使用HttpClient)

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.net.http.HttpResponse.BodyHandlers;

public class ApiClient {
    private static final String BASE_URL = "http://localhost:3000/api/books";
    private static final HttpClient client = HttpClient.newHttpClient();
    
    // 獲取所有書籍
    public static String getAllBooks() throws Exception {
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create(BASE_URL))
                .GET()
                .build();
                
        HttpResponse<String> response = client.send(request, BodyHandlers.ofString());
        return response.body();
    }
    
    // 獲取特定書籍
    public static String getBook(int id) throws Exception {
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create(BASE_URL + "/" + id))
                .GET()
                .build();
                
        HttpResponse<String> response = client.send(request, BodyHandlers.ofString());
        return response.body();
    }
    
    // 創建新書籍
    public static String createBook(String title, String author) throws Exception {
        String jsonBody = String.format("{\"title\":\"%s\",\"author\":\"%s\"}", title, author);
        
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create(BASE_URL))
                .header("Content-Type", "application/json")
                .POST(HttpRequest.BodyPublishers.ofString(jsonBody))
                .build();
                
        HttpResponse<String> response = client.send(request, BodyHandlers.ofString());
        return response.body();
    }
    
    // 更新書籍
    public static String updateBook(int id, String title, String author) throws Exception {
        String jsonBody = String.format("{\"title\":\"%s\",\"author\":\"%s\"}", title, author);
        
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create(BASE_URL + "/" + id))
                .header("Content-Type", "application/json")
                .PUT(HttpRequest.BodyPublishers.ofString(jsonBody))
                .build();
                
        HttpResponse<String> response = client.send(request, BodyHandlers.ofString());
        return response.body();
    }
    
    // 刪除書籍
    public static String deleteBook(int id) throws Exception {
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create(BASE_URL + "/" + id))
                .DELETE()
                .build();
                
        HttpResponse<String> response = client.send(request, BodyHandlers.ofString());
        return response.body();
    }
}
```

</antArtifact>


## API 設計最佳實踐

設計一個優秀的API需要遵循一些最佳實踐：

1. **一致性** - 使用統一的命名和結構
2. **版本控制** - 在URL中包含版本號（如 `/v1/books`）
3. **適當的HTTP狀態碼** - 使用正確的狀態碼表達結果
4. **良好的錯誤處理** - 提供清晰的錯誤信息
5. **分頁** - 處理大量數據時實現分頁
6. **HATEOAS** - 在回應中包含相關資源的連結
7. **遵循HTTP協議** - 正確使用HTTP方法和頭信息
8. **安全性** - 實施適當的身份驗證和授權機制
9. **限流** - 防止API濫用
10. **文檔完善** - 提供詳細且最新的API文檔

## API開發工具與框架

不同語言的API開發框架：

- **Node.js**: Express, Koa, Fastify, NestJS
- **Python**: Flask, Django REST Framework, FastAPI
- **Java**: Spring Boot, Jersey, Micronaut
- **PHP**: Laravel, Symfony, Slim
- **Ruby**: Rails, Sinatra
- **Go**: Gin, Echo, Revel
- **.NET**: ASP.NET Core Web API

## API測試工具

幫助測試和調試API的工具：

1. **Postman** - API測試和文檔工具
2. **Insomnia** - REST和GraphQL API測試工具
3. **cURL** - 命令行工具
4. **Swagger UI** - API文檔和測試界面
5. **JMeter** - API負載測試工具

## API監控

API監控和分析工具：

1. **New Relic** - 性能監控
2. **Datadog** - 綜合監控平台
3. **Sentry** - 錯誤追蹤
4. **Prometheus** - 指標收集和警報
5. **Grafana** - 指標可視化

## API安全性考慮

保護您的API的最佳實踐：

1. **使用HTTPS** - 加密所有通信
2. **實施身份驗證和授權** - JWT、OAuth 2.0等
3. **輸入驗證** - 驗證所有輸入數據
4. **限流和節流** - 防止DoS攻擊
5. **CORS設置** - 適當配置跨域資源共享
6. **敏感數據處理** - 加密敏感信息
7. **審計日誌** - 記錄API訪問和操作

## 結論


