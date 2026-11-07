# 癫影每日大包解锁

每日通过本人的癫影账号和站点资格解锁最多五个大包，显示当日进度，并通过Muvyo通知发送结果；不转存资源。

## 先部署dian-bridge执行端

**dian-bridge是部署在你自己NAS或服务器上的配套浏览器执行端。** 它负责按需启动浏览器、网站登录、查询历史、执行解锁，以及保存会话和防重复提交记录。Muvyo插件负责定时触发、配置与结果显示。

- [执行端部署说明](https://github.com/kzerghost/muvyo-addons/blob/main/docs/DEPLOYMENT.md)
- [下载 Compose](https://raw.githubusercontent.com/kzerghost/muvyo-addons/main/deployment/dian-bridge-0.4.5/compose.yaml)
- [完整插件使用说明](https://github.com/kzerghost/muvyo-addons/blob/main/docs/DIAN.md)

下载Compose，修改开头的`DIAN_ACCESS_PASSWORD`后，在自己的NAS容器管理器或Docker Compose中创建dian-bridge项目。它包含worker与browser两个容器，无需另行下载源码目录。

插件“执行端地址”填写`http://NAS地址:8788/api/v1`，访问密码填写Compose中设置的同一个值；地址和映射端口按实际部署填写。

**插件 0.4.5 需要协议 5 和 dian-bridge 0.4.4 或以上。推荐执行端 0.4.5，可在 worker 容器日志查询任务结果。**

## 设置与使用

1. 填写执行端地址和执行端访问密码。
2. 选择邮箱密码登录，或使用Muvyo已登录的Telegram获取机器人登录链接；有效浏览器会话直接复用。
3. 保存300秒单次执行时限，开启每日任务和需要的通知。
4. 整个执行时段与签到、转盘错开。网站已有的当日解锁计入五包额度。

“积分消耗后继续解锁”默认关闭：检测到网站扣分或余额减少后，停止当天剩余解锁并说明原因；开启后按设置继续剩余额度。它决定发现消耗后的处理，不承诺首次解锁绝不扣分。余额增加不视为消耗。

失败由Muvyo每隔1小时最多重试3次，每次先核对原任务与网站历史，已成功的不重复提交。额外验证、身份不一致、结果不明时停止；遵守网站等待要求。

## 查看任务故障

任务结果显示错误码、失败阶段和可用的 HTTP 状态。刷新「解锁状态」可查看执行端版本及失败诊断，并把最近的失败补记到插件日志。执行端 0.4.5 的 worker 容器日志使用相同的任务编号。

最低 Muvyo 版本为 3.14.0。升级时保留原连接、登录和定时设置。更新执行端时沿用原项目名、访问密码以及 `profiles` 和 `state` 的目录或卷。
