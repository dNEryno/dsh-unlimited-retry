# Changelog

All notable changes to this project are documented here. The format follows
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project
adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Fixed

- **A new step no longer reuses the finished step's `retryId`.** The ladder
  reset `attempt` at a step boundary but kept `retryId`, and the session
  loader pairs `llm/retry` with `llm/retry-started` by retryId. A second
  step that reused an id an earlier chain already held made the whole session
  log unreadable: `SessionFormatError: llm/retry must keep one retryId per
  policy chain`. The identity now dies with the step; the turn budget still
  survives it. Both the projection and the bounded local ladder are fixed.
- **The Settings panel now saves "read failure text"
  (`classifyTransientFailures`).** The save handler built its POST body from
  eleven config fields and skipped this one, so toggling the switch changed the
  draft, posted a body without the key, and the next poll snapped the switch
  back. The server accepted the key all along (`CONFIG_KEYS`,
  `validateConfigPatch`); it was simply never sent.

### Tests

- `test/host.test.mjs`: "a new step mints a new retryId instead of reusing the
  finished one" - red on the previous behaviour, green on the fix.

## 中文

### 修复

- **新 step 不再复用上一个 step 的 `retryId`。** 阶梯在 step 边界只清了
  `attempt`、没清 `retryId`，而会话加载器按 retryId 配对 `llm/retry` 与
  `llm/retry-started`。新 step 复用旧 id 会让整份会话日志读不出来：
  `SessionFormatError: llm/retry must keep one retryId per policy chain`。
  现在身份随 step 一起作废，轮预算照旧跨 step 保留；投影与退化本地阶梯都改了。
- **设置面板能保存「读取失败信息」（`classifyTransientFailures`）了。** 保存
  请求体只拼了 11 个字段、独漏这一个，所以勾选后草稿变了、发出去的 body 里没有
  这个键，下一次轮询就弹回。服务端一直支持这个键（`CONFIG_KEYS`、
  `validateConfigPatch`），只是客户端从来没发过。

### 测试

- `test/host.test.mjs` 新增 "a new step mints a new retryId instead of reusing
  the finished one"：旧行为红、修复后绿。