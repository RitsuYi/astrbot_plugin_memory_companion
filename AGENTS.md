# Codex 维护说明

## 范围与分支

本仓库为 `RitsuYi/astrbot_plugin_memory_companion` 的微信适配版 `2.6.5+weixin.2.2`。唯一发布分支 `weixin`。
`origin=https://github.com/RitsuYi/astrbot_plugin_memory_companion.git`；`upstream=https://github.com/menglimi/astrbot_plugin_memory_companion.git`。
当前锁定作者基线 `f16d64365f23514cd8faa459899ec92cf94e2925`。来源及包哈希见 `UPSTREAM_BASELINE.json`，完整流程见 `MAINTENANCE.md`。
先检查工作区、Git 状态和实际远端，保留用户文件与提交。不得创建作者原版 main 发布分支、强推、重建仓库，
或将作者最新代码直接覆盖当前微信版。只有用户提出新版本更新时才获取并合并对应目标，不引入未交付的 R3。

## 结构

`main.py` 为 AstrBot 入口，`core/` 处理记忆、身份、持久化与陪伴桥接；
`page_api.py`、`companion_page_bridge.py`、`companion_page_legacy.py` 处理页面接口，`pages/` 为资源。
本 R2 纯插件交付不包含开发测试目录；需要上游测试时，在隔离目录从锁定作者提交读取并审查，不能将旧版源码覆盖当前插件。

## 验证

Python/JSON 语法可在仓库根目录使用只读检查（不产生 `__pycache__`）：

```powershell
python -B -X utf8 -c "import ast,pathlib; paths=list(pathlib.Path('.').rglob('*.py')); [ast.parse(p.read_bytes(),filename=str(p)) for p in paths if '.git' not in p.parts]"
python -B -X utf8 -c "import json,pathlib; [json.loads(p.read_bytes()) for p in pathlib.Path('.').rglob('*.json') if '.git' not in p.parts]"
git merge-base --is-ancestor f16d64365f23514cd8faa459899ec92cf94e2925 HEAD
git merge-base HEAD f16d64365f23514cd8faa459899ec92cf94e2925
git diff --check
```

核对 `metadata.yaml`、`main.py`、`pyproject.toml`、基线文件及发布清单版本；实际更新地址必须指向本维护仓库。
README 及许可中的作者引用应保留。发布清单校验全部列出的文件，新增公开文档也应更新清单；清单自身不做自哈希。
本纯插件快照不带 `tests/`，不能声称执行了不存在的测试。验证时至少对当前 `core/identity.py`、
`core/companion_linkage.py` 执行隔离身份/桥接回归，并在独立 SDK 环境导入当前入口；
记录外部测试脚本和实际结果，不能复用旧聊天中的通过数。

## 微信边界与交付

检查 `weixin_oc`、平台实例/真实账号/用户身份、管理员绑定、私聊路由、主动消息、记忆所有者/人格/会话隔离及双插件桥接。
未来正常合并作者新提交，禁止 `ours` 丢弃新改动。所有开发依赖和 SDK 只装入独立虚拟环境，插件运行依赖不得安装 `astrbot`。
不要执行私人运行目录中的修复脚本；不要改 AstrBot 桌面端内置 Python。测试使用合成身份和隔离数据目录。
只报告本次实际测试；未登录真实微信验收时写“未实测”。推送后必须比对远端 SHA 和关键文件。
插件 ZIP 分别构建，排除维护/恢复工具、CMD、Git/bundle、虚拟环境、真实数据、凭证、日志和缓存。
本公开 AGENTS.md 不含私人配置，不得将私人运行手册写入其中。迁移和配套回退见 MAINTENANCE.md。
不擅自开启定时任务、自动合并或无人值守发布。

## 文件保护

禁止批量删除文件或目录，禁止 `del /s`、`rd /s`、`rmdir /s`、`Remove-Item -Recurse`、`rm -rf`。
需要删除时只能一次删除一个明确路径的文件；需要批量删除时停止并请用户手动处理。
已有非空目录不得清空或覆盖；需替换维护副本时先逐个完整保留旧目录，并验证所有移动路径均在工作区内。
