# AGM Docker 构建问题修复

date: 2026-03-29
tags: #agent-git-mail #docker #debug

## 问题

`docker compose build` 卡在拉取 `node:22-bookworm-slim` 基础镜像，最终失败。

## 排查过程

### Step 1: 基础镜像拉取

```bash
docker pull node:22-bookworm-slim
# 错误：TLS handshake timeout / unexpected EOF
```

国内访问 Docker Hub 不稳定，TLS 握手频繁超时。

### Step 2: Registry Mirror

添加 DaoCloud 镜像加速：

```json
// ~/.docker/daemon.json
{
  "registry-mirrors": ["https://m.daocloud.io"]
}
```

添加后 `docker pull` 成功。

### Step 3: Docker Compose Build 失败

Build 时 TypeScript 编译报错：

```
Cannot find module 'openclaw'
```

**根因**：`@t0u9h/openclaw-agent-git-mail` 包的 `tsconfig.json` 里的 `paths` 硬编码了宿主机路径 `/usr/local/lib/node_modules/openclaw/...`，容器内构建时找不到。

### Step 4: openclaw 包无 TypeScript 类型

`openclaw` npm 包是纯 JS，没有 `.d.ts` 类型声明文件。

## 修复

### 1. Docker Registry Mirror
修改 `~/.docker/daemon.json`，添加 `"registry-mirrors": ["https://m.daocloud.io"]`

### 2. 类型声明文件
创建 `packages/openclaw-plugin/src/openclaw.d.ts`，基于源码使用情况手动声明接口（`OpenClawPluginApi`、`OpenClawPluginService`、`ServiceContext` 等）。

### 3. tsconfig.json
移除硬编码的 `paths`，让 TypeScript 找到本地 `node_modules` 里的声明文件。

### 4. devDependency
在 `package.json` 添加 `"openclaw": "^2026.3.28"`，让构建时能解析类型引用。

## 关键教训

- **openclaw 包没有类型声明**：这是设计问题，插件开发需要自己补全类型
- **宿主机路径不要进 tsconfig**：paths 只适合本地开发，生产构建需要本地声明文件
- **Docker Hub 国内访问问题**：需要配置 registry mirror（DaoCloud、阿里云等）
