# 概述

## 适用场景

本指南适用于将基于 **Next.js Pages Router** 的项目迁移到 **Vite + React** 单页应用（SPA）。

## 什么时候适合迁移？

- 你的 Next.js 项目实际上是一个纯前端 SPA，极少或没有使用 SSR
- 你不需要 `getServerSideProps` / `getStaticProps` 带来的 SEO 优势
- 你希望更快的开发服务器启动和构建速度
- 你希望简化部署架构（静态文件托管 vs Node.js 服务器）

## 如果是 App Router？

Next.js App Router（`app/` 目录）的迁移更复杂，涉及 Server Components、layout 嵌套、`async` 组件等概念。本指南主要覆盖 Pages Router。

## 关键技术变更一览

| 类别 | Next.js (Pages Router) | Vite + React |
|------|------------------------|--------------|
| 构建工具 | `next build` (webpack/turbopack) | `vite build` (esbuild + rollup) |
| 开发服务器 | `next dev` | `vite` |
| 路由 | 文件系统路由 + `next/router` | `react-router-dom` v6 |
| 数据获取 | `getServerSideProps` / `getStaticProps` | `useEffect` + `fetch` / axios |
| Head 管理 | `next/head` | `react-helmet-async` |
| 链接组件 | `next/link` | `react-router-dom` `Link` |
| 图片组件 | `next/image` | 普通 `<img>` 或手动优化方案 |
| 环境变量 | `NEXT_PUBLIC_*` | `VITE_*` |
| API 路由 | `pages/api/` | 移除（由独立后端服务处理） |
| 入口文件 | `_app` + `_document` | `index.html` + `main.jsx` |
| 部署方式 | Node.js 运行时 (`next start`) | 纯静态文件 + 任意 Web 服务器 |

---

# 依赖替换

## 1. 卸载 Next.js

```bash
npm uninstall next
```

## 2. 卸载仅在 API Routes（服务端）使用的依赖

如果你的项目中有 `pages/api/` 目录，你需要识别那些仅在服务端使用的包并将它们卸载。常见的有：

- 数据库驱动（`mysql2`、`pg`、`mongodb`、`prisma` 等）
- 服务端 SDK（`openai`、`aws-sdk`、`stripe` 等）
- Node.js 专属库（`fs-extra`、`sharp`、`bcrypt` 等）
- 服务端会话管理（`iron-session`、`next-auth` 等）

> **识别方法**：全局搜索这些包的 import 语句，如果只出现在 `pages/api/` 或服务端代码中，就可以安全卸载。这些功能需要迁移到你的独立后端服务。

## 3. 安装 Vite 及替代依赖

```bash
# Vite 核心
npm install -D vite @vitejs/plugin-react

# 路由（替代文件系统路由 + next/router）
npm install react-router-dom

# Head 管理（替代 next/head）
npm install react-helmet-async
```

## 4. 更新 package.json scripts

```diff
  "scripts": {
-   "dev": "next dev",
-   "build": "next build",
-   "start": "next start",
-   "lint": "next lint"
+   "dev": "vite",
+   "build": "vite build",
+   "preview": "vite preview"
  }
```

`vite preview` 用于本地预览生产构建，相当于 `next start` 的作用。

---

# 构建配置

## 删除 `next.config.js`，创建 `vite.config.js`

`vite.config.js` 是 ESM 模块，如果你原来用的是 `.mjs` 或 TypeScript，Vite 也支持 `vite.config.ts`。

```js
// vite.config.js
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import path from 'path'

export default defineConfig({
    plugins: [react()],

    // 路径别名 — 对应 next.config.js 中的 resolve.alias
    resolve: {
        alias: {
            '@': path.resolve(__dirname, 'src'),
            // 你项目中的其他别名
        }
    },

    // 开发服务器
    server: {
        port: 3000,

        // API 代理 — 对应 next.config.js 中的 rewrites
        proxy: {
            '/api': {
                target: 'http://localhost:8000',
                changeOrigin: true,
            },
            // 其他需要代理的路径
        }
    }
})
```

## 功能对应关系

| 你需要做的事 | Next.js (`next.config.js`) | Vite (`vite.config.js`) |
|-------------|---------------------------|------------------------|
| 启用 React | 内置 | `plugins: [react()]` |
| 路径别名 `@/` → `src/` | `resolve.alias` | `resolve.alias` |
| API 代理 | `async rewrites()` | `server.proxy` |
| 端口 | `dev -p 3000` 或配置 | `server.port: 3000` |
| 环境变量（在配置中使用） | `process.env.*` 直接可用 | 需用 `loadEnv()` 显式加载 |
| 静态文件目录 | `public/`（自动） | `public/`（自动，保持一致） |

## 如果需要环境变量在 vite.config.js 中使用

```js
import { defineConfig, loadEnv } from 'vite'

export default defineConfig(({ mode }) => {
    const env = loadEnv(mode, process.cwd(), '')
    // 现在可以用 env.VITE_XXX 了
    return {
        server: {
            proxy: {
                '/api': {
                    target: env.VITE_API_BASE_URL,
                    changeOrigin: true,
                }
            }
        }
    }
})
```

> `loadEnv` 第三个参数传 `''` 表示加载所有前缀的变量，默认只加载 `VITE_` 前缀。

---

# 入口文件改造

Next.js 的 `_app` 和 `_document` 承担了应用初始化的职责。迁移到 Vite 后，这些由 `index.html` 和 `main.jsx` 替代。

## 创建 `index.html`

在项目根目录创建 `index.html`，Vite 会自动将其作为 HTML 入口（无需像 webpack 那样在配置中指定）：

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8" />
    <link rel="icon" href="/favicon.ico" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>你的应用名称</title>
</head>
<body>
<div id="root"></div>
<script type="module" src="/src/main.jsx"></script>
</body>
</html>
```

> **关键点**：
> - `index.html` 必须放在项目根目录
> - `<script type="module">` 指向你的 JS 入口文件
> - `<div id="root">` 是 React 的挂载点，与 `_document` 中的 `<div id="__next">` 类似

## 创建 `src/main.jsx`

替代 `_app.js` 和 `_document.js` 的初始化逻辑：

```jsx
import React from 'react'
import ReactDOM from 'react-dom/client'
import { BrowserRouter } from 'react-router-dom'
import { HelmetProvider } from 'react-helmet-async'
import App from './App'
import './styles/globals.css'  // 你的全局样式

ReactDOM.createRoot(document.getElementById('root')).render(
    <React.StrictMode>
        <HelmetProvider>
            <BrowserRouter>
                <App />
            </BrowserRouter>
        </HelmetProvider>
    </React.StrictMode>
)
```

**与 Next.js 的关键区别：**

- Next.js 服务端用 `ReactDOM.hydrate`（内部处理），Vite SPA 用 `ReactDOM.createRoot`
- `BrowserRouter` 是整个路由系统的根，替代 Next.js 文件系统路由
- `HelmetProvider` 提供动态 `<head>` 管理能力（如果你用 `react-helmet-async`）
- 全局 CSS 在这里导入，对应原来 `_app.js` 中的全局样式导入

## 创建 `src/App.jsx`

将原 `_app.js` 中的应用级逻辑（主题、布局、全局状态等）迁移到 `App.jsx`。

**迁移前（`_app.js`）核心结构：**

```jsx
import '../styles/globals.css'

export default function MyApp({ Component, pageProps }) {
    return (
        <>
            <Head>...</Head>
            <SomeProvider>
                <Layout>
                    <Component {...pageProps} />
                </Layout>
            </SomeProvider>
        </>
    )
}
```

**迁移后（`App.jsx`）：**

```jsx
import { useLocation } from 'react-router-dom'
import { Helmet } from 'react-helmet-async'
import AppRoutes from './routes'

export default function App() {
    const location = useLocation()

    return (
        <>
            <Helmet>
                <title>你的应用</title>
                <meta name="viewport" content="width=device-width, initial-scale=1" />
                <link rel="icon" href="/favicon.ico" />
            </Helmet>

            <SomeProvider>
                <Layout>
                    <AppRoutes />   {/* 取代 <Component {...pageProps} /> */}
                </Layout>
            </SomeProvider>
        </>
    )
}
```

**对照表：**

| 功能 | Next.js `_app.js` | Vite `App.jsx` |
|------|-------------------|----------------|
| 渲染页面 | `<Component {...pageProps} />` | `<AppRoutes />` |
| 服务端数据注入 | `getInitialProps` | 不存在（CSR） |
| 路由变化监听 | `router.events.on('routeChangeComplete')` | `useEffect` 监听 `location.pathname` |
| 全局 `<head>` | `<Head>` | `<Helmet>` |
| 全局布局 | 在 `_app` 中包裹 | 在 `App` 中包裹 |

---

# 路由系统迁移

## 从文件系统路由到集中式路由定义

Next.js 的 `pages/` 目录结构就是路由。迁移到 Vite 后，你需要用 `react-router-dom` 集中定义路由。

**创建 `src/routes.jsx`：**

```jsx
import { Routes, Route } from 'react-router-dom'
import HomePage from './pages/index'
import AboutPage from './pages/about'
import ProductDetail from './pages/products/[id]'
import Dashboard from './pages/dashboard'
import Settings from './pages/settings'

export default function AppRoutes() {
    return (
        <Routes>
            <Route path="/" element={<HomePage />} />
            <Route path="/about" element={<AboutPage />} />
            <Route path="/products/:id" element={<ProductDetail />} />
            <Route path="/dashboard" element={<Dashboard />} />
            <Route path="/settings" element={<Settings />} />
        </Routes>
    )
}
```

## 路由模式映射

| Next.js 文件系统路由 | react-router-dom |
|----------------------|-------------------|
| `pages/index.js` | `<Route path="/" element={<HomePage />} />` |
| `pages/about.js` | `<Route path="/about" element={<AboutPage />} />` |
| `pages/products/[id].js` | `<Route path="/products/:id" element={<ProductDetail />} />` |
| `pages/blog/[...slug].js` (catch-all) | `<Route path="/blog/*" element={<BlogPost />} />` |
| `pages/admin/users/[id].js` | `<Route path="/admin/users/:id" element={<UserAdmin />} />` |

## 组件内 Hook 替换

在页面和组件内部，所有 `next/router` 的 API 需要替换为 `react-router-dom` 的等价物：

| 用途 | Next.js | react-router-dom |
|------|---------|-----------------|
| 编程式导航 | `router.push('/path')` | `navigate('/path')` |
| 替换当前路由 | `router.replace('/path')` | `navigate('/path', { replace: true })` |
| 获取路由参数 | `router.query.id` | `const { id } = useParams()` |
| 获取当前路径 | `router.pathname` | `location.pathname` (from `useLocation()`) |
| 获取查询字符串 | `router.query.page` | `new URLSearchParams(location.search).get('page')` |
| 监听路由变化 | `router.events.on('routeChangeComplete', fn)` | `useEffect(() => { ... }, [location.pathname])` |
| 路由预取 | `router.prefetch('/path')` | 无直接等价物（SPA 客户端按需加载） |

## 代码示例：从 `useRouter` 迁移

**迁移前：**
```jsx
import { useRouter } from 'next/router'

function Page() {
    const router = useRouter()
    const { id } = router.query

    const goDetail = () => {
        router.push('/products/' + id)
    }

    return <button onClick={goDetail}>查看详情</button>
}
```

**迁移后：**
```jsx
import { useNavigate, useParams } from 'react-router-dom'

function Page() {
    const navigate = useNavigate()
    const { id } = useParams()

    const goDetail = () => {
        navigate('/products/' + id)
    }

    return <button onClick={goDetail}>查看详情</button>
}
```

## 可选：保持目录结构

迁移后 `pages/products/[id].jsx` 这样的带动态参数的文件名可以保留（方便识别），路由的实际匹配逻辑集中在 `routes.jsx` 中，文件名不影响路由行为。

---

# Head 管理

Next.js 的 `next/head` 需要替换为客户端方案。推荐 `react-helmet-async`。

## 替换模式

**迁移前：**
```jsx
import Head from 'next/head'

export default function Page() {
    return (
        <>
            <Head>
                <title>页面标题</title>
                <meta name="description" content="页面描述" />
            </Head>
            <div>页面内容</div>
        </>
    )
}
```

**迁移后：**
```jsx
import { Helmet } from 'react-helmet-async'

export default function Page() {
    return (
        <>
            <Helmet>
                <title>页面标题</title>
                <meta name="description" content="页面描述" />
            </Helmet>
            <div>页面内容</div>
        </>
    )
}
```

> **前置条件**：`main.jsx` 中需要用 `<HelmetProvider>` 包裹应用。多个页面的 `<Helmet>` 可以共存，后渲染的会覆盖前者。

---

# 数据获取：SSR → CSR

这是迁移的核心变化。Next.js 的服务端数据获取方法需要全部改为客户端数据获取。

## 改造模式

| Next.js 方法 | Vite 等价方案 |
|-------------|-------------|
| `getServerSideProps` | `useState` + `useEffect` + `fetch`/axios |
| `getStaticProps` | `useState` + `useEffect` + `fetch`/axios |
| `getStaticPaths` | 不需要，路由由 `react-router-dom` 定义 |
| `getInitialProps`（在 `_app` 中） | 移除，在 `App.jsx` 中用 `useEffect` |
| `getInitialProps`（在页面中） | `useState` + `useEffect` |

## 代码示例：getServerSideProps 改造

**迁移前（SSR）：**
```jsx
export async function getServerSideProps(context) {
    const { id } = context.params
    const data = await fetch(`https://api.example.com/items/${id}`).then(r => r.json())
    return { props: { item: data } }
}

export default function ItemPage({ item }) {
    return (
        <div>
            <h1>{item.title}</h1>
            <p>{item.content}</p>
        </div>
    )
}
```

**迁移后（CSR）：**
```jsx
import { useState, useEffect } from 'react'
import { useParams } from 'react-router-dom'

export default function ItemPage() {
    const { id } = useParams()
    const [item, setItem] = useState(null)
    const [loading, setLoading] = useState(true)
    const [error, setError] = useState(null)

    useEffect(() => {
        setLoading(true)
        fetch(`https://api.example.com/items/${id}`)
            .then(r => r.json())
            .then(data => {
                setItem(data)
                setLoading(false)
            })
            .catch(err => {
                setError(err.message)
                setLoading(false)
            })
    }, [id])

    if (loading) return <div>加载中...</div>
    if (error) return <div>出错了：{error}</div>
    if (!item) return <div>未找到</div>

    return (
        <div>
            <h1>{item.title}</h1>
            <p>{item.content}</p>
        </div>
    )
}
```

## 需要额外处理的状态

SSR 模式下数据在服务端就绪后才返回 HTML，用户看不到加载态。迁移到 CSR 后，你必须处理三种状态：

```
┌──────────┐     ┌──────────┐     ┌──────────┐
│  loading  │ ──▶ │  loaded  │     │  loaded  │
│  (加载中)  │     │  (成功)   │     │  (成功)   │
└──────────┘     └──────────┘     └──────────┘
       │
       ▼
  ┌──────────┐
  │  error   │
  │  (失败)   │
  └──────────┘
```

建议封装一个通用的数据获取 Hook 来减少重复代码：

```jsx
function useFetch(url) {
    const [data, setData] = useState(null)
    const [loading, setLoading] = useState(true)
    const [error, setError] = useState(null)

    useEffect(() => {
        let cancelled = false
        setLoading(true)
        fetch(url)
            .then(r => r.json())
            .then(d => { if (!cancelled) { setData(d); setLoading(false) } })
            .catch(e => { if (!cancelled) { setError(e.message); setLoading(false) } })
        return () => { cancelled = true }
    }, [url])

    return { data, loading, error }
}
```

---

# Next.js 专属组件替换

## 1. `next/link` → `react-router-dom` `Link`

```diff
- import Link from 'next/link'
+ import { Link } from 'react-router-dom'

- <Link href="/about">关于</Link>
+ <Link to="/about">关于</Link>

- <Link href={{ pathname: '/products/[id]', query: { id: product.id } }}>
+ <Link to={`/products/${product.id}`}>
```

## 2. `next/image` → 普通 `<img>` 或手动优化

`next/image` 提供了懒加载、尺寸优化、格式转换（WebP/AVIF）等功能。迁移后这些需要手动处理：

```diff
- import Image from 'next/image'
- <Image src="/hero.jpg" alt="Hero" width={800} height={400} priority />

+ <img src="/hero.jpg" alt="Hero" style={{ width: 800, height: 400, objectFit: 'cover' }} loading="lazy" />
```

**失去的功能及补偿方案：**

| next/image 功能 | Vite 替代方案 |
|----------------|-------------|
| 自动懒加载 | `<img loading="lazy">`（浏览器原生支持） |
| 响应式尺寸 | CSS `srcset` + `sizes` 或手写 |
| 格式优化（WebP/AVIF） | 构建时用 `vite-plugin-image-optimizer` 或 CDN 处理 |
| 占位符 blur | 手动实现或使用第三方库 |
| 优先级控制 | `<link rel="preload">` 或 `<img fetchpriority="high">` |

如果你大量使用 `next/image`，可以考虑在 Vite 中使用 `vite-plugin-image-optimizer` 来获得类似体验。

## 3. `next/dynamic` → `React.lazy` + `Suspense`

```diff
- import dynamic from 'next/dynamic'
- const HeavyComponent = dynamic(() => import('./HeavyComponent'), {
-   ssr: false,
-   loading: () => <p>加载中...</p>
- })

+ import { lazy, Suspense } from 'react'
+ const HeavyComponent = lazy(() => import('./HeavyComponent'))
+
+ // 使用时
+ <Suspense fallback={<p>加载中...</p>}>
+   <HeavyComponent />
+ </Suspense>
```

> Vite 也原生支持动态 `import()`，在 `useEffect` 中动态加载模块同样可用。

## 4. `next/router` 事件 → `useEffect` 监听

```diff
- useEffect(() => {
-   const handleRouteChange = (url) => { /* ... */ }
-   router.events.on('routeChangeComplete', handleRouteChange)
-   return () => router.events.off('routeChangeComplete', handleRouteChange)
- }, [])

+ const location = useLocation()
+ useEffect(() => {
+   // location.pathname 变化时执行
+ }, [location.pathname])
```

## 5. `next/script` → 普通 `<script>` 或 `useEffect`

```diff
- import Script from 'next/script'
- <Script src="https://example.com/analytics.js" strategy="lazyOnload" />

+ useEffect(() => {
+   const script = document.createElement('script')
+   script.src = 'https://example.com/analytics.js'
+   script.async = true
+   document.body.appendChild(script)
+   return () => { document.body.removeChild(script) }
+ }, [])
```

---

# 环境变量

## 前缀替换

Next.js 通过 `NEXT_PUBLIC_*` 前缀将变量暴露到客户端；Vite 使用 `VITE_*` 前缀。

```diff
# .env.development
- NEXT_PUBLIC_API_BASE_URL=http://localhost:8000
+ VITE_API_BASE_URL=http://localhost:8000
```

## 代码中引用

```diff
- const baseUrl = process.env.NEXT_PUBLIC_API_BASE_URL
+ const baseUrl = import.meta.env.VITE_API_BASE_URL
```

## 全局搜索替换

建议用编辑器全局搜索以下模式并替换：

```
process.env.NEXT_PUBLIC_  →  import.meta.env.VITE_
```

> **注意**：不带 `NEXT_PUBLIC_` 前缀的变量在 Next.js 中仅服务端可用，迁移到 SPA 后如果需要暴露到客户端，需要加上 `VITE_` 前缀。

---

# API Routes 处理

## 识别并移除

`pages/api/` 目录下的文件在 Next.js 中是服务端 API 端点。迁移到 Vite SPA 后，这些代码需要移除。

**步骤：**

1. **审计 `pages/api/` 目录**，列出所有 API 端点
2. **将 API 逻辑迁移到独立后端**（如果你已有后端，需要确认这些端点是否已经被覆盖）
3. **更新客户端 API 调用**，确保请求指向正确的后端地址
4. **卸载仅被 API Routes 使用的 npm 包**（见上文"卸载仅在 API Routes 使用的依赖"）
5. **删除 `pages/api/` 目录**

## 更新 API 请求层

确保你的 HTTP 请求工具（axios / fetch）的 baseURL 配置正确。例如：

```js
// src/utils/request.js
const http = axios.create({
    baseURL: import.meta.env.VITE_API_BASE_URL,
})
```

原来在 Next.js 中通过 `rewrites` 代理的路径，现在通过 `vite.config.js` 的 `server.proxy` 实现（仅开发环境），生产环境则通过 Nginx 等反向代理处理。

---

# 文件重命名

## .js → .jsx

Next.js 允许在 `.js` 文件中写 JSX，但 Vite 默认只在 `.jsx` 文件中处理 JSX。虽然可以通过配置 esbuild 来支持 `.js` 中的 JSX，但最简单的方式是将所有含 JSX 的文件重命名为 `.jsx`。

```bash
# Linux/macOS/WSL
find src -name "*.js" -exec bash -c 'git mv "$0" "${0%.js}.jsx"' {} \;
```

也可以用脚本逐个处理，或借助 IDE 批量重命名。

> **注意**：纯逻辑文件（无 JSX）可以保持 `.js` 扩展名，不影响构建。

---

# 部署方式

## 从 Node.js 运行时到静态文件托管

这是迁移带来的最大架构变化：

| | Next.js | Vite SPA |
|--|---------|----------|
| 运行 `build` 后产物 | `.next/`（需要 Node.js 执行） | `dist/`（纯静态文件） |
| 启动命令 | `next start`（Node.js 进程） | 任何 Web 服务器（nginx/caddy/apache） |
| 服务器依赖 | 必须安装 Node.js | 不需要任何运行时 |

## Dockerfile 示例

**迁移前（Next.js）：**
```dockerfile
FROM node:20-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build
EXPOSE 3000
CMD ["npm", "run", "start"]
```

**迁移后（Vite SPA 多阶段构建）：**
```dockerfile
# 阶段 1：构建
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# 阶段 2：运行（nginx）
FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

## Nginx 配置（SPA 路由关键设置）

```nginx
server {
    listen       80;
    server_name  localhost;

    root   /usr/share/nginx/html;
    index  index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    # API 反向代理（如果有后端）
    location /api/ {
        proxy_pass http://backend:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

> **`try_files $uri $uri/ /index.html`** 是 SPA 部署的核心配置。它确保所有前端路由（如 `/products/456`）都回退到 `index.html`，由 react-router-dom 在客户端接管路由。

## .gitignore 调整

```diff
- /.next/
+ /dist/
```

---

# 完整迁移 Checklist

## 阶段 1：准备
- [ ] 创建迁移分支
- [ ] 确保当前代码已全部提交，工作区干净
- [ ] 列出所有页面路由，做好记录（作为后续对照）
- [ ] 列出 `pages/api/` 中的所有端点，确认后端已覆盖

## 阶段 2：依赖
- [ ] 卸载 `next`
- [ ] 识别并卸载仅用于 API Routes 的服务端依赖（数据库驱动、SDK 等）
- [ ] 安装 `vite`、`@vitejs/plugin-react`（devDependencies）
- [ ] 安装 `react-router-dom`、`react-helmet-async`（dependencies）
- [ ] 更新 `package.json` 的 `scripts`

## 阶段 3：配置文件
- [ ] 创建 `vite.config.js`（插件、路径别名、开发代理、端口）
- [ ] 在项目根目录创建 `index.html`
- [ ] 删除 `next.config.js`
- [ ] 将所有 `.env` 文件中的 `NEXT_PUBLIC_` 替换为 `VITE_`
- [ ] 全局搜索 `process.env.NEXT_PUBLIC_` 替换为 `import.meta.env.VITE_`
- [ ] 更新 `.gitignore`（`/.next/` → `/dist/`）

## 阶段 4：入口文件
- [ ] 创建 `src/main.jsx`（`createRoot` + `BrowserRouter` + `HelmetProvider`）
- [ ] 创建 `src/App.jsx`（从 `_app.js` 迁移全局逻辑）
- [ ] 创建 `src/routes.jsx`（所有路由的集中定义）
- [ ] 删除 `_app.js` 和 `_document.js`（如有）

## 阶段 5：路由迁移（逐文件）
- [ ] `useRouter` → `useNavigate` / `useParams` / `useLocation`
- [ ] `router.push()` → `navigate()`
- [ ] `router.query.xxx` → `useParams()`
- [ ] `router.events` → `useEffect` 监听 `location.pathname`
- [ ] `next/link` → `react-router-dom` `Link`（`href` → `to`）

## 阶段 6：数据获取改造（逐页面）
- [ ] 移除 `getServerSideProps` / `getStaticProps` / `getInitialProps`
- [ ] 添加 `useState` + `useEffect` 客户端数据获取
- [ ] 添加 loading 状态处理
- [ ] 添加错误状态处理
- [ ] 添加空数据状态处理

## 阶段 7：组件替换（逐文件）
- [ ] `next/head` → `react-helmet-async` `Helmet`
- [ ] `next/image` → `<img>` 或替代方案
- [ ] `next/dynamic` → `React.lazy` + `Suspense`
- [ ] `next/script` → 原生 script 或 `useEffect` 动态加载

## 阶段 8：文件重命名
- [ ] 所有含 JSX 的 `.js` → `.jsx`

## 阶段 9：API 层
- [ ] 删除 `pages/api/` 目录
- [ ] 清理 API 文件中的服务端专属函数引用
- [ ] 确认所有客户端 API 调用指向正确的后端地址

## 阶段 10：部署
- [ ] 重写 Dockerfile（多阶段构建：node 构建 + nginx 运行）
- [ ] 创建或更新 `nginx.conf`（`try_files` SPA 规则 + API 代理）
- [ ] 更新 CI/CD 配置

## 阶段 11：验证
- [ ] `npm run dev` 启动成功
- [ ] `npm run build` 构建成功，无警告
- [ ] `npm run preview` 预览正常
- [ ] 每个页面逐一手动检查
- [ ] 浏览器控制台无报错
- [ ] 路由切换正常（前进/后退）
- [ ] API 请求正常（开发 + 生产环境）
- [ ] Docker 构建并运行验证

---

# 常见问题

## Q1: 原来 `public/` 下的文件怎么引用？

和 Next.js 完全一样。`public/` 下的文件通过绝对路径引用，如 `public/favicon.ico` → `/favicon.ico`。Vite 对 `public/` 目录的处理与 Next.js 一致。

## Q2: CSS Modules 还能用吗？

可以。Vite 原生支持 CSS Modules（文件名需为 `*.module.css`），无需额外配置。使用方式与 Next.js 相同。

## Q3: `next/font` 怎么迁移？

移除 `next/font` 的 import。使用以下替代方案：
- **Google Fonts**：在 `index.html` 中通过 `<link>` 引入
- **本地字体**：在 CSS 中用 `@font-face` 声明，字体文件放在 `public/fonts/` 下
- **字体优化**：使用 `vite-plugin-fonts` 或 CDN 服务

## Q4: `next/headers`、`next/cookies` 等服务端 API 怎么处理？

这些是 Next.js 服务端专属 API，在纯 SPA 中没有对应。获取 cookie 改用 `document.cookie`；获取请求头相关的信息需要设计替代方案（如后端 API 返回）。

## Q5: 构建产物放到哪里？

Vite 默认输出到 `dist/` 目录（Next.js 是 `.next/`）。可以通过 `vite.config.js` 中的 `build.outDir` 修改。

## Q6: 环境变量在 `index.html` 中能用吗？

可以。Vite 支持在 HTML 中使用 `<% VITE_XXX %>` 语法：

```html
<title><% VITE_APP_TITLE %></title>
```

## Q7: SSR / SEO 怎么办？

纯 SPA 对 SEO 不友好。如果 SEO 对你的项目很重要，有以下选择：
- 使用 **`vite-plugin-ssr`**（现已更名为 Vike）实现 Vite 的 SSR
- 使用预渲染工具如 `prerender-spa-plugin` 或 `react-snap`
- 在 Nginx/CDN 层为爬虫返回预渲染页面
- 如果 SEO 是核心需求，建议重新评估是否应该迁移

## Q8: 开发时 HMR 比 Next.js 好多少？

Vite 的 HMR 基于 ESM，只更新修改的模块，而不是整个页面。对于中大型项目，改动后的热更新通常在几十毫秒内完成，而 Next.js 可能需要数秒。

## Q9: 生产构建速度对比？

取决于项目规模，但通常 Vite（Rollup + esbuild）比 Next.js（webpack / turbopack）快 3-8 倍。
