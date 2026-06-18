# 认证

Kite 支持通过 API 密钥进行程序化访问。API 密钥会以一个特殊用户身份完成认证，并沿用与页面登录用户相同的 RBAC 模型。

## 登录尝试封禁

基于账号密码的登录会在连续失败时按客户端 IP 临时封禁。Kite 允许 1 分钟内最多 10 次错误密码或 MFA 验证失败；第 11 次失败会封禁该客户端 IP 的账号密码登录 5 分钟。

当 Kite 运行在 Ingress 或负载均衡器后面时，封禁使用的客户端 IP 取决于 `TRUSTED_PROXIES` 配置。详见[环境变量](/zh/config/env)。

## API 密钥格式

完整 API 密钥格式如下：

```text
kite<ID>-<SECRET>
```

使用时直接把完整值放进 `Authorization` 请求头，不要加 `Bearer`。

```http
Authorization: kite12-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

## 在哪里配置

拥有 `admin` 角色的用户可以在 **设置 -> API 密钥** 中创建 API 密钥。

创建后，复制完整密钥值，并把它作为 API 请求里的 `Authorization` 请求头使用。

## 权限说明

API 密钥与普通用户共用同一套 RBAC 权限模型。

- 创建 API 密钥本身不会自动获得任何资源权限。
- `/api/v1/...` 下的资源访问仍然会经过 RBAC 校验。
- `/api/v1/admin/...` 下的管理接口要求调用方拥有 `admin` 角色。
- 集群资源接口通常还需要传 `x-cluster-name`。

## 认证请求

示例：

```bash
curl \
  -H "Authorization: kite12-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx" \
  -H "x-cluster-name: demo-cluster" \
  https://kite.example.com/api/v1/pods/default
```

说明：

- `/api/v1/...` 下的资源接口通常还需要传 `x-cluster-name`。
