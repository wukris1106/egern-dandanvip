# egern-dandanvip · 蛋蛋不语 VIP 解锁

本地改写 App 后端返回的会员字段，让 App 显示 VIP。**纯声明式 `response_jq`，不加载外部 JS。**

## 导入

```
https://cdn.jsdelivr.net/gh/wukris1106/egern-dandanvip@main/dandanvip.yaml
```

## 必须做的一步：HTTP Engine Hosts

App 后端是明文 HTTP + 非标端口：`http://38.76.202.248:8000/`。Egern **默认只解析 80 端口**，不加白名单请求根本不进重写引擎，而且不报错。

设置 → **HTTP Engine Hosts / HTTP 引擎主机** → 「包含」里加：

```
38.76.202.248:8000
```

然后：点保存 → VPN 开关关一次再开 → 杀掉 App 重开（Flutter 有本地缓存）。

## 改写内容

`/rest/v1/profiles`（Supabase 表）响应注入：

- `vip_status: true`
- `vip_level: 9`
- `vip_expire_at: 2099-09-19`
- `username` / `avatar_url` 走模板参数，默认用本仓库 `avatar.jpg`

jq 同时处理数组和单对象两种顶层形态（Supabase 带 `Accept: application/vnd.pgrst.object+json` 时返回对象）。

## 自检

- Safari 访问 `http://38.76.202.248:8000/` 返回 401 + Basic 弹窗 = 通道正常（弹窗是预期的）
- 不弹 → 端口作用域问题，回去查 HTTP Engine Hosts
- 弹了但 App 没变化 → App 没发这个请求，或字段名和你这版 App 不一致

## 重建说明

2026-11-21 重建。原版本含四个诊断探针模块（debug / report），确认生效后已全部删掉，只保留主模块 —— 探针用完就该拆。
