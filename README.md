# kzerghost 的 Muvyo 插件

Muvyo 第三方插件仓库，由 kzerghost 独立维护。当前提供“癫影每日大包解锁”公测版。

## 安装插件

在 Muvyo「第三方插件 → 插件仓库」添加：

```text
https://github.com/kzerghost/muvyo-addons
```

刷新仓库后安装插件，并在安装窗口确认所需域名、执行端和 Telegram 机器人权限。

| 插件 | 作用 | 需要准备 |
| --- | --- | --- |
| 癫影每日大包解锁 | 每天自动解锁最多五个大包，显示当日进度，并使用 Muvyo 通知发送结果 | 自己的癫影账号及站点认可的解锁资格，配套 dian-bridge 执行端；选择 Telegram 登录时，还需在 Muvyo 登录 Telegram |

- [插件设置与使用说明](docs/DIAN.md)
- [dian-bridge 的作用、Compose 下载与部署步骤](docs/DEPLOYMENT.md)
- [版本记录](CHANGELOG.md)

## dian-bridge 是什么

**dian-bridge 是本插件配套、部署在你自己 NAS 或服务器上的浏览器执行端。** 它负责启动浏览器，使用你的账号登录网站，查询解锁历史并完成每日任务；同时保存浏览器会话和防重复提交记录。浏览器按需启动，任务结束后清理并回收空闲内存。

它由同一个 Compose 项目中的两个容器组成：

| 容器 | 作用 |
| --- | --- |
| worker | 接收插件的加密任务请求，管理任务进度、每日额度与防重复记录 |
| browser | 为任务提供按需启动的浏览器，保留网站登录会话，任务结束后清理浏览器进程 |

插件连接 worker 提供的地址，例如 `http://NAS地址:8788/api/v1`。这套服务运行在你自己的设备上，网站登录信息和会话不经过作者的服务器。

### 下载部署文件

**[下载配套 compose.yaml](https://raw.githubusercontent.com/kzerghost/muvyo-addons/main/deployment/dian-bridge-0.4.5/compose.yaml)** · [查看文件](deployment/dian-bridge-0.4.5/compose.yaml) · [升级已有部署](docs/DEPLOYMENT.md#升级与恢复)

下载后先创建 `profiles`、`state` 两个专用数据目录，在文件开头填写它们的绝对路径和 `DIAN_ACCESS_PASSWORD`，再用 NAS 的容器管理器或 Docker Compose 创建项目。默认路径是 `/volume1/docker/dian-bridge/data/profiles` 和 `/volume1/docker/dian-bridge/data/state`，可以改到自己的持久目录。文件内已包含运行所需的执行端程序，无需另行下载源码目录。

登录会话和任务记录保存在这两个可见目录中；删除项目或容器会保留目录，持久数据不会自动清空。备份、卸载和旧命名卷部署的处理见[数据管理说明](docs/DEPLOYMENT.md#登录资料和记录存在哪里)。

当前插件版本为 **0.4.5**，执行端最低 **dian-bridge 0.4.4（协议 5）**。推荐执行端 **0.4.5**，可在 worker 容器日志中按任务编号查看结果。插件的任务结果和「解锁状态」会显示失败原因和执行端升级要求。

## 使用范围与验证情况

任务使用本人账号和站点授予的资格，通过正常页面完成解锁；每天最多五包，不转存。遇到额外验证、身份不一致或无法确认的提交结果时停止。失败重试由 Muvyo 调度，每次先核对旧任务和网站历史。

本版面向 **Muvyo 3.14.0 及以上版本**。此前已验证完整五包定时、结果通知和任务后空闲内存回落。新版诊断和兼容性已通过本地回归，Muvyo 日志与 Docker 日志的联动待实机验收。

## 仓库内容

- `index.json`、`index.json.sig`：插件仓库索引与索引签名。
- `plugins/`：加密并签名后的插件、权限清单和说明。
- `deployment/`：可直接下载的配套执行端 Compose 文件。
- `docs/`：安装、使用、升级与恢复说明。

发布目录不包含网站账号、访问密码、登录会话、作者私钥或明文插件入口代码。配套执行端程序随 Compose 分发。

仓库组织参考 [fnyys 的 Muvyo 插件仓库](https://github.com/fnyys6694-arch/muvyo-addon)，发布格式依据 [Muvyo 官方开发指南](https://github.com/thsrite/muvyo-addon-guide)。
