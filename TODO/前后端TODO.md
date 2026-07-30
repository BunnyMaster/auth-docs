# 前后端待办追踪 Community

- [ ] 后端：P0 集成测试 + README Known limitations（`v1 发布信心清单` §3.3、§3.5、§六）
- [x] 后端：统一 auth-server LICENSE 文件（Apache-2.0）
- [x] 前端：统一 auth-web LICENSE 文件（Apache-2.0）

# 前后端待办追踪 Enterprise

## service-auth

- [x] 后端：应用层写路径 Port 收敛（读路径 CQRS 已落地）—§二

## service-system

### 数据范围（§3.8）

- [ ] 后端：角色数据范围 `role_scope` 管理 API
- [ ] 前端：角色页数据范围配置
- [ ] 后端：角色页授权对象维护（grant 视角，§3.7）
- [ ] 前端：角色页授权对象维护
- [ ] 后端：角色影响分析 API
- [ ] 前端：角色影响分析 UI
- [ ] 后端：`user_scope` 管理 API
- [ ] 前端：数据权限配置页 `system/data-scope/index.vue`（含 role_scope / user_scope）

### 消息（§3.10）

- [x] 后端：IN_APP 消息渠道 Sender
- [x] 后端：短信 Sender（channel=SMS，当前阿里云 Dypns）
- [x] 前端：SMS 模板管理 `message/sms-template/index.vue`
- [x] 后端：站内信模板扩展（channel=IN_APP）
- [x] 前端：站内信模板页 `message/in-app-template/index.vue`
- [x] 后端：渠道投递记录 API（`message_channel_delivery`）
- [x] 前端：消息发送记录页 `message/send-record/index.vue`
- [x] 后端：站内信发送任务 API（分页/详情/发送/重试/撤回/删除）
- [x] 前端：站内信任务页 `message/in-app-message/index.vue` + 编辑发送 `message/compose/index.vue`
- [x] 后端 / 前端：站内信任务收件人分页（已读 / 软删状态）
- [x] 后端 / 前端：站内信业务分类字典
- [x] 后端：我的消息分页 + 未读角标 + 详情 + 标已读 + 大类全部已读 + 用户侧删除
- [x] 前端：我的消息 `home/inbox`（列表/详情/标已读/全部已读/删除 + 顶栏角标）
- [ ] 后端 / 前端：文件异步导出



