# 我的 react 筆記

## 開發文件目錄

```plaintext
my-react-app/
│
├── public/                  # 公共靜態文件
│   ├── index.html           # 頁面入口 HTML
│
├── src/                     # 源代碼
│   ├── assets/              # 靜態資源（如圖片、字體等）
│   ├── components/          # 可重用組件
│   │   ├── Button.js        # 按鈕組件
│   │   └── Header.js        # 頁面頭部組件
│   ├── context/             # React Context 存放處
│   │   └── UserContext.js   # 用戶 Context
│   ├── services/            # 封裝 API 請求等服務
│   │   └── api.js           # API 請求封裝
│   ├── pages/               # 頁面級別的組件
│   │   ├── Home.js          # 首頁
│   │   └── Login.js         # 登錄頁
│   ├── App.js               # 主組件
│   ├── index.js             # 頁面入口
│   └── styles/              # 全局樣式文件
│       └── index.css        # 全局樣式
├── .gitignore               # git 忽略文件
├── .eslintrc.js             # ESLint 配置
├── .prettierrc              # Prettier 配置
├── package.json             # npm 配置
└── README.md                # 項目說明文件
```

### 1. `services` 文件夾

`services` 文件夾主要用來封裝與外部服務交互的邏輯，例如 API 請求、與後端溝通等。它的目的是把這些複雜的邏輯和組件分離，讓組件更加專注於 UI 渲染。

#### `services/api.js`

這個文件會封裝 `axios` 或其他 HTTP 客戶端來進行 API 請求。

```javascript
import axios from 'axios';

// 創建 axios 實例
const api = axios.create({
  baseURL: 'https://api.example.com', // 設置基礎 URL
  timeout: 10000, // 設定請求超時
  headers: {
    'Content-Type': 'application/json',
  },
});

// 範例：封裝一個獲取用戶資料的 API 請求
export const fetchUserData = (userId) => {
  return api.get(`/users/${userId}`);
};

// 範例：封裝一個登錄 API 請求
export const loginUser = (credentials) => {
  return api.post('/login', credentials);
};
```

#### 使用範例

在需要的組件中引入並使用 `services/api.js` 中的方法：

```javascript
import React, { useEffect, useState } from 'react';
import { fetchUserData } from '../services/api';

const UserProfile = ({ userId }) => {
  const [user, setUser] = useState(null);

  useEffect(() => {
    fetchUserData(userId)
      .then(response => {
        setUser(response.data);  // 假設回應數據是 { name, age, etc. }
      })
      .catch(error => {
        console.error('Error fetching user data:', error);
      });
  }, [userId]);

  if (!user) return <div>Loading...</div>;

  return (
    <div>
      <h1>{user.name}</h1>
      <p>{user.age} years old</p>
    </div>
  );
};

export default UserProfile;
```

### 2. `context` 文件夾

`context` 文件夾主要用來處理 React Context，提供全局狀態管理的功能。這可以用於存儲和管理在應用中任何地方需要共享的數據，並允許組件在不需要層層傳遞 props 的情況下共享這些數據。

#### context/UserContext.js

這個文件創建一個上下文，用來存儲用戶的登錄狀態，並提供更新用戶信息的功能。

```javascript
import React, { createContext, useContext, useState } from 'react';

// 創建 Context
const UserContext = createContext();

// 提供者組件
export const UserProvider = ({ children }) => {
  const [user, setUser] = useState(null);

  const login = (userData) => {
    setUser(userData); // 假設這裡的 userData 是登錄成功後返回的用戶資料
  };

  const logout = () => {
    setUser(null); // 用戶登出，清空用戶資料
  };

  return (
    <UserContext.Provider value={{ user, login, logout }}>
      {children}
    </UserContext.Provider>
  );
};

// 自定義 hook 用來獲取用戶資料
export const useUser = () => {
  return useContext(UserContext);
};
```

#### 使用範例

在組件中使用 `UserContext` 來存取或修改用戶資料：

```javascript
import React, { useState } from 'react';
import { useUser } from '../context/UserContext';

const Login = () => {
  const { login } = useUser();
  const [username, setUsername] = useState('');

  const handleLogin = () => {
    // 假設這裡進行登錄操作，然後返回用戶資料
    const userData = { username }; 
    login(userData);
  };

  return (
    <div>
      <input 
        type="text" 
        value={username} 
        onChange={(e) => setUsername(e.target.value)} 
        placeholder="Enter username" 
      />
      <button onClick={handleLogin}>Login</button>
    </div>
  );
};

export default Login;
```

在另一個組件中使用 `useUser` 來訪問當前的用戶資料：

```javascript
import React from 'react';
import { useUser } from '../context/UserContext';

const UserProfile = () => {
  const { user, logout } = useUser();

  if (!user) {
    return <div>Please login to view your profile.</div>;
  }

  return (
    <div>
      <h1>Welcome, {user.username}</h1>
      <button onClick={logout}>Logout</button>
    </div>
  );
};

export default UserProfile;
```

### 總結

- **`services` 文件夾**：封裝了與後端或其他服務的交互邏輯（例如 API 請求），將這些邏輯集中在一起，方便維護和測試。
- **`context` 文件夾**：提供全局狀態管理功能，使用 React Context 來共享應用中的狀態，避免層層傳遞 props，實現跨組件共享數據。

  這兩者在 React 應用中通常都是必不可少的，`services` 用來處理 API 交互，`context` 用來管理全局狀態

---

## 新建專案的目錄特殊檔案說明

### App.test.js

`App.test.js` 是 React 應用程式中的測試檔案，通常是用來寫測試用例，檢查 `App.js` 中的組件是否正確運行。它是由 `create-react-app` 預設生成的一部分，用於測試 React 元件的功能和行為。`App.test.js` 中通常會使用到 **Jest** 和 **React Testing Library** 來撰寫測試。

#### 1. `App.test.js` 的基本作用

- **測試 React 組件**：它檢查 `App.js` 組件（或其他組件）的渲染結果和行為。
- **確保功能正常**：可以用來檢查組件是否正確渲染，是否觸發預期的事件，是否顯示正確的內容等。

#### 2. 使用 Jest 和 React Testing Library 測試 React 組件

`create-react-app` 預設集成了 **Jest** 和 **React Testing Library**，用來測試 React 組件。

如果你是從頭開始建立專案，`create-react-app` 已經為你安裝好了這些依賴。如果沒有，請確保安裝：

```bash
npm install --save-dev jest @testing-library/react @testing-library/jest-dom
```

#### 3. 測試範例：檢查 `App.js`

假設 `App.js` 內有一些簡單的內容，我們可以使用 `App.test.js` 來測試這些內容是否正確渲染。

##### **`App.js` 範例**

```jsx
import React from 'react';

function App() {
    return (
        <div>
            <h1>Welcome to React</h1>
        </div>
    );
}

export default App;
```

##### `App.test.js` 範例

`App.test.js` 會測試 `App` 組件中的內容是否正確渲染。

```jsx
import { render, screen } from '@testing-library/react';
import App from './App';

test('renders welcome message', () => {
    render(<App />);
    const linkElement = screen.getByText(/Welcome to React/i); // 檢查是否渲染了 "Welcome to React"
    expect(linkElement).toBeInTheDocument(); // 驗證該元素存在
});
```

#### 4. 如何執行測試

當你撰寫完測試用例後，可以使用以下指令來執行測試：

```bash
npm test
```

這會啟動 Jest 測試執行器，並運行 `App.test.js` 中的測試。

#### 5. 測試的常見用法

- **渲染測試**：確保組件被正確渲染，並顯示預期的內容。

    ```jsx
    test('renders the correct heading', () => {
        render(<App />);
        const heading = screen.getByText(/Welcome to React/i);
        expect(heading).toBeInTheDocument();
    });
    ```

- 事件處理測試：檢查事件是否正常觸發（例如，點擊按鈕）。

    ```jsx
    test('button click triggers event', () => {
        const handleClick = jest.fn();
        render(<button onClick={handleClick}>Click me</button>);

        const button = screen.getByText(/Click me/i);
        button.click();

        expect(handleClick).toHaveBeenCalledTimes(1); // 驗證是否觸發了點擊事件
    });
    ```

- 快照測試：將組件的渲染結果保存為快照，以後可以對比檢查是否發生了變化。

    ```jsx
    import renderer from 'react-test-renderer';

    test('matches snapshot', () => {
        const tree = renderer.create(<App />).toJSON();
        expect(tree).toMatchSnapshot();
    });
    ```

---

### setupTests.js

`setupTests.js` 是 React 應用中自動生成的一個配置文件，通常在使用 **Create React App** 工具創建的專案中會包含這個文件。這個文件的主要目的是配置測試環境，尤其是對於使用 **Jest** 和 **React Testing Library** 進行單元測試的專案。

#### `setupTests.js` 的用途

1. **初始化測試環境**：`setupTests.js` 用來在測試運行之前配置一些全局設定。這些設定會影響所有測試文件，通常用來設置測試框架、插件或全局變量等。
2. **全局配置**：它會自動加載於每個測試運行之前，讓你能夠在這裡設置一些一次性需要執行的操作，例如全局的 mock 函數或測試工具的初始化。

#### `setupTests.js` 中常見的設置

- 設置全局的 mock 函數或變量。
- 引入測試工具或庫，例如 `jest-dom` 來提供額外的 DOM 斷言。
- 配置測試框架，像是設置測試執行器、時間控制等。

#### 範例：`setupTests.js` 的內容

```javascript
// 在 React 專案中使用 jest-dom 提供的額外斷言語法
import '@testing-library/jest-dom';

// 可選：設定全局變量或函數（例如全局的 mock 函數）
global.fetch = jest.fn(() =>
    Promise.resolve({
        json: () => Promise.resolve({ data: 'some data' }),
    })
);
```

#### 如何使用 `setupTests.js`

1. **默認配置**
   如果你是使用 `create-react-app` 創建的 React 專案，`setupTests.js` 文件通常會在 `src` 目錄下自動生成。你只需要在這個文件中進行配置，它會自動被 Jest 載入，並在測試執行前運行。

2. **自定義配置**
   你可以根據專案需求來修改 `setupTests.js` 文件。例如，如果你需要設置一些全局變量或第三方測試庫，這些設置可以在 `setupTests.js` 中完成。

    假設你想在測試中使用 `jest-dom`（這是一個為 Jest 提供更多的 DOM 斷言的庫），你只需要在 `setupTests.js` 文件中引入它：

    ```javascript
    // src/setupTests.js
    import '@testing-library/jest-dom';
    ```

3. **執行 Jest 測試**
   一旦設置好 `setupTests.js`，Jest 測試框架會自動在每個測試文件運行前執行 `setupTests.js`，這樣所有的配置就會被應用到測試中。

#### 典型使用場景

- **引入擴展庫**：你可以在 `setupTests.js` 中引入擴展庫來增強測試能力。例如，`jest-dom` 提供了更多有用的 DOM 斷言方法，像是 `toBeInTheDocument`，`toHaveClass` 等。
- **設置全局 mock 函數**：如果你的應用需要使用如 `fetch` 或其他外部 API，可以在 `setupTests.js` 中進行 mock，使測試更加獨立和穩定。

#### 範例：使用 `setupTests.js` 來設置 `fetch` 的 mock

假設你的應用需要使用 `fetch` 來獲取數據，在測試過程中你可能不想讓實際的 API 被調用，而是使用 mock 來模擬響應。

```javascript
// src/setupTests.js
global.fetch = jest.fn(() =>
    Promise.resolve({
        json: () => Promise.resolve({ message: 'Hello, world!' }),
    })
);
```

然後在測試中，你可以使用 `fetch` 函數，並且它將返回你在 `setupTests.js` 中設置的 mock 響應。

---

### reportWebVitals.js

`reportWebVitals.js` 是 React 應用中的一個可選文件，用來測量和報告應用的性能指標。這個文件通常會在 `create-react-app` 預設的 React 專案中自動生成。它的主要目的是幫助開發者了解應用的 Web Vitals（即網站的性能指標），這些指標對於網站的用戶體驗至關重要。

#### Web Vitals 指標

Web Vitals 是 Google 提出的標準，旨在衡量網站的性能對用戶體驗的影響。這些指標主要包括：

1. **LCP (Largest Contentful Paint)**：測量頁面主要內容渲染完成的時間，越快越好。
2. **FID (First Input Delay)**：衡量用戶首次與頁面互動到頁面響應的延遲時間，越短越好。
3. **CLS (Cumulative Layout Shift)**：測量頁面內容布局穩定性，過大的值可能會影響用戶體驗。

#### `reportWebVitals.js` 的作用

`reportWebVitals.js` 是用來測量這些 Web Vitals 指標並將結果上報的文件。具體來說，它會將這些指標數據發送到指定的端點（通常是第三方的性能分析工具或自家的服務端）以便進行分析。

#### `reportWebVitals.js` 的內容

該文件通常包括以下幾個主要部分：

1. **導入 `web-vitals` 庫**：這個庫提供了用來測量 Web Vitals 指標的函數。
2. **函數 `reportWebVitals`**：用來將測量結果發送到指定的回調函數。

#### 範例

```javascript
import { reportWebVitals } from './reportWebVitals';

reportWebVitals(console.log); // 將測量結果打印到控制台
```

#### 如何使用 `reportWebVitals.js`

在 React 應用中，你可以選擇性地將 `reportWebVitals` 用於性能監控。默認情況下，這個文件不會自動啟用，你可以根據需求將它與你的應用進行集成。

1. **修改 `index.js` 來啟用性能報告**
   在 `src/index.js` 中，`reportWebVitals` 被導入並調用。你可以修改這個呼叫，以便將性能數據發送到你自己的分析服務，或者像預設一樣將數據輸出到控制台：

    ```javascript
    import React from 'react';
    import ReactDOM from 'react-dom';
    import './index.css';
    import App from './App';
    import reportWebVitals from './reportWebVitals';

    ReactDOM.render(
        <React.StrictMode>
            <App />
        </React.StrictMode>,
        document.getElementById('root')
    );

    // 將性能指標輸出到控制台
    reportWebVitals(console.log); // 或者你可以將它發送到服務端分析
    ```

2. **自定義回調函數**：
   你也可以提供自定義的回調函數來處理 Web Vitals 的數據，例如將它們發送到第三方服務如 Google Analytics 或你的自有服務：

    ```javascript
    import { reportWebVitals } from './reportWebVitals';

    function sendToAnalytics(metric) {
        // 將性能指標發送到你的分析服務
        console.log(metric);
        // 可以透過 fetch 或其他方式將資料發送到你的服務端
    }

    reportWebVitals(sendToAnalytics);
    ```

---

## 嚴格模式

有時候 console.log 之類的會執行 2 次是因為嚴格模式的關係；在 `index.js` 中預設開發會使用嚴格模式，這是因為 React 在渲染時，會在開發模式下「預執行」你的函數組件（例如 Son），並執行一次 render()，以確保函數組件沒有副作用或不良行為，打包時就會移除了。

```jsx
root.render(
    <React.StrictMode>
        <App />
    </React.StrictMode>
);
```

---

## createBrowserRouter、createHashRouter 比較

`createBrowserRouter` 和 `createHashRouter` 都是 React Router v6 中用來設置路由的函數，它們之間的主要區別在於 URL 的處理方式。根據你的應用需求，你可以選擇其中一個來管理路由。

### 1. createBrowserRouter

`createBrowserRouter` 使用瀏覽器的 **HTML5 History API** 來處理路由。這意味著它會使用 **正常的路徑（路由）** 來管理 URL，通常是你在現代 Web 應用中所見的 URL 結構，例如：`https://example.com/about`。

#### 使用場景

- 適用於現代瀏覽器支持 HTML5 History API 的情況，通常是 **單頁應用（SPA）**。
- 需要乾淨的 URL（不包含 `#` 符號），這對於 SEO、分享鏈接和用戶體驗都比較友好。
- 用於需要支持 **正確的瀏覽器刷新行為** 和 **前端路由導航** 的場景。

#### 範例

```javascript
import { createBrowserRouter, RouterProvider } from 'react-router-dom';
import Home from './Home';
import About from './About';

const router = createBrowserRouter([
    {
        path: '/',
        element: <Home />,
    },
    {
        path: '/about',
        element: <About />,
    },
]);

function App() {
    return <RouterProvider router={router} />;
}
```

- URL: `https://yourdomain.com/` 或 `https://yourdomain.com/about`

#### 優點

- 支持乾淨的 URL（沒有 `#` 符號）。
- 瀏覽器的前進/後退按鈕、刷新等行為都會正確觸發。
- 良好的 SEO 支援（因為 URL 是標準的）。

#### 缺點

- 需要伺服器配置支援，以處理所有的路徑，並將它們指向 `index.html`。否則，瀏覽器刷新時可能會出現 404 錯誤。

---

### 2. createHashRouter

`createHashRouter` 使用瀏覽器的 **URL hash**（即 `#` 符號）來處理路由。這意味著它會將路由放置在 URL 的哈希部分，例如：`https://example.com/#/about`。哈希部分的 URL 不會被伺服器處理，所有的路由邏輯完全由客戶端處理。

#### 使用場景

- 當你無法控制伺服器配置，並且需要在 **靜態頁面** 或 **GitHub Pages** 上部署你的應用，因為伺服器可能無法處理 SPA 的路由時。
- 使用哈希路由可以避免伺服器配置的問題，因為 URL 的 `#` 部分不會發送到伺服器，只有瀏覽器負責處理它。
- 適用於不關心 SEO 的應用，或者是在內部應用中，不需要讓 URL 更加語義化。

#### 範例

```javascript
import { createHashRouter, RouterProvider } from 'react-router-dom';
import Home from './Home';
import About from './About';

const router = createHashRouter([
    {
        path: '/',
        element: <Home />,
    },
    {
        path: '/about',
        element: <About />,
    },
]);

function App() {
    return <RouterProvider router={router} />;
}
```

- URL: `https://yourdomain.com/#/` 或 `https://yourdomain.com/#/about`

#### 優點

- 不需要伺服器配置，因為哈希部分不會被發送到伺服器。
- 簡單的部署選項，特別適合靜態網站或服務。
- 支持老舊瀏覽器和一些不支持 HTML5 History API 的環境。

#### 缺點

- URL 中包含 `#`，不夠乾淨，對 SEO 和用戶分享體驗不利。
- 無法利用瀏覽器的內建功能（如書籤）來標識某些頁面，因為 URL 中的 `#` 部分並不會影響頁面加載。

---

### **比較：**

| 特性             | `createBrowserRouter`                         | `createHashRouter`                                   |
| ---------------- | --------------------------------------------- | ---------------------------------------------------- |
| **URL 顯示方式** | 正常 URL（無 `#` 符號）                       | 包含 `#` 符號的 URL                                  |
| **伺服器需求**   | 需要伺服器支持，處理所有路徑指向 `index.html` | 不需要伺服器配置                                     |
| **瀏覽器行為**   | 完全支持瀏覽器的前進/後退按鈕和刷新行為       | 支援前進/後退，但不支援刷新/回滾歷史                 |
| **SEO 支援**     | 良好                                          | 較差，因為 URL 包含 `#`                              |
| **使用場景**     | 適用於現代單頁應用，且伺服器支持配置          | 適用於靜態站點或無法配置伺服器的情況，並且不依賴 SEO |

---

### **選擇建議：**

- `createBrowserRouter`：

  - 如果你有控制伺服器，並且希望使用乾淨的 URL 和支持瀏覽器的刷新行為，選擇 `createBrowserRouter`。
  - 適合大多數現代的前端應用，尤其是當 SEO 和可共享的 URL 重要時。

- `createHashRouter`：
  - 如果你需要在靜態文件托管（如 GitHub Pages）上部署，並且無法修改伺服器配置，選擇 `createHashRouter`。
  - 適用於對 SEO 需求較低的應用，或者開發內部工具/頁面時。

---
