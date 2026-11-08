# R2 分离安装包验证

日期：2026-11-08；版本：`2.6.5+weixin.2.2`。

插件安装包为 `astrbot_plugin_memory_companion-2.6.5-weixin-r2-plugin-only.zip`。
恢复工具单独放在 `astrbot-desktop-recovery-tool.zip`，不随插件上传、加载或执行。

- 完整套件 **1727 passed、1 skipped、458 subtests passed**（40.71 秒），2 项原有弃用警告。
- 微信、安装修复与分包专项合计 **30 passed**。
- 插件发布物不含 CMD/BAT/PowerShell/EXE、恢复脚本、tests、scripts、tools 或开发依赖文件。
- 所有 core 代码和 docs/contracts 文件与已验证的安装修复版逐字节一致；运行页面完整保留。
- 最终 ZIP 使用真实 AstrBot 4.29.4 验证、解包、读取元数据、执行安装依赖预检与插件入口导入。
- 在模拟缺失 SDK 发行元数据的环境中，依赖预检不调用 pip，原宿主模块身份保持不变。
- 解包代码在隔离临时上下文中完成数据库初始化、81 条页面路由注册、必要契约文件读取和关闭。
  临时上下文不等于完整桌面端，查询工具绑定保留降级提示；已有查询工具契约测试另外通过。
- 两份 ZIP 的结构、CRC 和插件文件清单哈希均检查通过。

未在用户真实 Windows 桌面端或真实微信账号验收。插件包不会自动移走此前环境中误装的 SDK。

---

# R2 安装修复版验证

日期：2026-11-08；版本：`2.6.5+weixin.2.1`。

已交付 R2 ZIP CRC、语法和文件哈希完整，问题在运行依赖声明，不在 ZIP 损坏。
用户 Windows 日志确认错误的 `astrbot>=4.27` 导致宿主副本安装及 SQLModel 重复注册。
此前测试没有覆盖缺少宿主 pip 元数据的桌面版安装场景，这项遗漏在本版修正。

- 使用真实 AstrBot 4.29.4 requirements 预检复现原依赖把可导入的宿主判为缺失。
- 修复后，即使宿主 pip 元数据缺失或完全不可用，运行期安装也不调用 pip。
- 恢复工具验证只读预览、选择性备份移走、配置和数据库内容保留、原宿主保留、
  已修正版不移走、重复执行、后台进程阻止、链接拒绝及中途失败回滚。
- 本版完整套件：**1725 passed、1 skipped、458 subtests passed**（37.92 秒）；2 项原有弃用警告。
- 微信与安装修复专项合计 **28 passed**；微信联动逻辑保持 R2 行为。
- 发布阶段再使用真实 AstrBot ZIP 检查与解包器验收，并从解包目录重跑专项。

未在用户真实 Windows 系统执行恢复，未登录真实微信；启动恢复还需本机重启确认。
工具不会自动清理此次 pip 添加的其它依赖，也不把通用 heartbeat 提示当成唯一错误原因。

---

# 微信适配 R2 测试记录

## R2复查

日期：2026-11-08。版本：`2.6.5+weixin.2`。
复查对象为实际交付的R1 ZIP，SHA256：
`69446275b605b57d16260eecb4caca5834f8170e36b0ebdbefa8c138b5ad0078`。

将新增用例放入独立解压的R1代码执行：4 failed、11 passed。
失败分别为平台已移除、平台解析器不可用、已核验真实微信账号别名、其它人格近期记录挤占候选。
同样15项微信用例在R2全部通过。另验证关闭桥接后两种账号键均不能读取。

联动使用交付的陪伴R2 `7.1.6+weixin.2`，其ZIP SHA256：
`c63b14f6031ab2818b7712e7103274b040602764a6999840aa7c83f446e0ce53`。
实际执行注册表、上下文序列化和provider调用，不只模拟协议字段。
测试使用真实AstrBot4.29.4事件类型、合成身份和临时数据库；没有登录真实微信或调用真实模型。

R2完整 `tests/` 套件：**1712 passed、1 skipped、458 subtests passed**，35.19秒。
有2项宿主／旧注册装饰器弃用警告，无失败。
发布脚本检查Python和JSON语法、版本一致性、发布目录卫生、ZIP CRC和逐文件SHA256；
交付ZIP另行解压后执行相同微信专项，避免只验证工作目录。

推荐使用 `COMPANION_WEIXIN_ROOT` 指定配套陪伴代码；旧环境变量
`COMPANION_WEIXIN_R1_ROOT` 仍作为兼容别名接受。

---

## R1历史记录

日期：2026-11-08。版本：`2.6.5+weixin.1`。本记录描述本地验证范围，不代表真实微信服务端验收。

## 测试环境

- 记忆插件上游：`f16d64365f23514cd8faa459899ec92cf94e2925`，版本2.6.5。
- AstrBot：4.29.4，源码提交 `2b3aee5343b10e8148fb5935206c165ae89b4b9a`。
- 联动陪伴插件：7.1.6+weixin.1，即之前交付的个人微信R1适配版。
- 使用真实的 `WeixinOCAdapter` 和 `WeixinOCMessageEvent` 类、合成消息、临时SQLite库。
- 不使用真实用户资料、微信token、微信账号、云端模型或实际微信收发。

## 执行结果

| 检查 | 结果 |
| --- | --- |
| 修改前选取的私聊、群聊ACL、人格、Bot隔离基线 | 63 passed，32 subtests passed |
| 新增微信专项与两插件注册表联动 | 10 passed |
| 修改后完整 `tests/` 测试套件（包含上述10项） | 1707 passed，1 skipped，458 subtests passed |
| 完整套件耗时 | 37.16秒 |
| 非失败警告 | 2项：宿主audioop及旧register_star弃用警告 |

完整运行设置了 `COMPANION_WEIXIN_R1_ROOT`，因此两插件联动用例实际执行，没有因缺少另一插件而跳过。
测试并不统计为“真实微信聊天通过”。

## 新增用例覆盖

- 微信私聊事件解析为正确平台、发送者、私聊窗口、Bot实例及爱弥斯人格。
- 包装事件缺少self_id时使用微信平台实例，不借用另一个在线Bot。
- 通过原记忆工具写入偏好，在新的消息事件中召回并注入请求。
- 不召回其它用户、微信实例、人格、Bot所有者以及QQ平台的记忆内容。
- 原始消息采集、提交与重复消息去重。
- 陪伴7.x外部记忆接口读取限定当前完整微信私聊窗口，内容继续执行权限和生命周期检查。
- 联动summary按同一私聊窗口统计；这是窗口总数，包含窗口内各人格记录，不等于本轮可注入条数。
- 错误的account_id、session_id或会话种类返回空结果／不可用状态。
- 使用之前陪伴R1包的真实注册表和序列化上下文调用本插件provider。

## 复现

在已安装AstrBot4.29.4、pytest、pytest-asyncio及插件依赖的Python环境中：

```bash
ASTRBOT_ROOT=/absolute/path/to/isolated-test-root \
COMPANION_WEIXIN_R1_ROOT=/absolute/path/to/private-companion-weixin-r1 \
python -m pytest tests/ -q -p no:cacheprovider --confcutdir=tests
```

只检查新增微信适配可将 `tests/` 换为 `tests/test_weixin_oc_compat.py`。
未设置陪伴目录时，两插件联动专项会明确skip，其它微信专项仍执行。

在git源码目录中生成交付包：

```bash
python scripts/build_weixin_release.py --output /absolute/path/to/output
```

构建会解析全部Python与JSON、检查版本一致性，执行上游发布卫生检查，并验证ZIP的CRC和逐文件SHA256。
发布包不包含测试运行产生的数据库、缓存、日志或真实账号配置。

## 未覆盖

真实微信登录、服务端收发、微信凭证到期、附件下载、语音、远端模型及Embedding质量未验证。
实际可用性还需要用户按照 `WEIXIN_OC_COMPAT.md` 在自己的微信会话完成首次验收。
