# 装修笔记 - 软膜天花照明规划

## 区域整体照明规划
计划在以下区域使用软膜天花做整体照明：
- 衣帽间
- 洗手池顶部
- 手工区
- 单独梳妆台

## 晴空灯要求
- 原则上不做晴空灯
- 如果坚持安装晴空灯，必须保证：灯珠朝向不要是亮反光面或过道

---

# 定期任务：Reol 演唱会检查

## 任务说明
每周一北京时间 9:00 自动检查 Reol 官网(https://reol.jp/feature/bijigaku)是否新增中国香港、中国澳门、中国内地演唱会安排，有新增则通知用户。台湾场不需要检查。

## 错误反思（2026-03-18）
- **错误**：访问了错误 URL（末尾多加斜杠 → 404），并错误将 404 页面判断为无新增场次
- **修正**：正确 URL 为 `https://reol.jp/feature/bijigaku`（末尾无斜杠），访问前确认 URL 格式，检查页面是否正常加载后再解析结果

## 当前状态（2026-03-18）
- 最新检查结果：无新增中国香港/澳门/内地演唱会安排

---

# 系统配置 - 浏览器开发错误记录

## 问题背景
用户需要配置浏览器自动化来执行网页检查任务（如 Reol 演唱会监控）

## 错误记录
- **初始环境问题**：系统仅安装了 Firefox，而 OpenClaw 的 browser control 服务仅支持 Chrome/Brave/Edge/Chromium，Firefox 不兼容
- **解决方案**：需要安装 Chrome 或 Brave 浏览器来启用浏览器自动化功能

## 当前状态
- 浏览器自动化功能尚未配置完成
- 待解决：安装 Chromium 内核浏览器，或配置 Brave Search API 来替代网页检查自动化

---

# SpeedInspect 开发 - CORS 错误记录

## 问题背景
SpeedInspect 是前后端分离项目（Next.js 前端 + FastAPI 后端），开发环境下前端运行在 `localhost:3000`，后端运行在 `localhost:8000`，会产生跨域请求

## 错误记录
- **常见 CORS 错误**：
  1. 开发环境前端无法调用后端 API → 浏览器跨域拦截
  2. CORS 配置解析问题：最初配置中 `CORS_ORIGINS` 从环境变量读取时需要正确解析为列表

## 已修复方案
- 在 FastAPI 主文件 `main.py` 中正确添加了 `CORSMiddleware`
- 配置解析：`config.py` 中使用 `field_validator` 将环境变量中的逗号分隔字符串正确解析为列表
- 当前配置：
  - 允许 origins: `settings.CORS_ORIGINS`（默认 `http://localhost:3000`）
  - 允许 credentials: `True`
  - 允许所有 methods/headers
  - 暴露 `X-Request-ID` 和 `X-Response-Time` 头

## 排查经验
如果再次遇到 CORS 错误，按顺序检查：
1. 检查 `CORS_ORIGINS` 是否包含前端实际域名/地址
2. 确认 `CORSMiddleware` 是否正确添加到 FastAPI 应用（中间件顺序有影响）
3. 如果凭证模式开启，`allow_origins` 不能用 `["*"]` 通配符，必须明确指定 origins

