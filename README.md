# 8gkg QuantusMiner

主页：[8gkg.com](https://8gkg.com)。用于 NVIDIA 显卡的 Quantus（qpow-poseidon2）挖矿软件，支持 Windows、Linux 与 HiveOS。默认开发者费用 **1%**，可自行调整或关闭。

## 文件与启动

- Windows：`8gkg_quantusminer.exe`，或解压 Windows 压缩包后运行。
- Linux：`8gkg_quantusminer`，首次运行执行 `chmod +x 8gkg_quantusminer`。
- HiveOS：使用本目录的 `.tar.gz` 安装包，自定义矿工名填写 `8gkg_quantusminer`。
- 每个可执行文件和压缩包均提供同名 `.sha256` 文件。

请在启动前设置自己的收款地址。无参数启动及随附脚本保留项目默认钱包，默认钱包与下方开发者钱包相同；如不修改，用户挖矿部分也会发往该默认钱包。使用 `--auth-token` 时，收款身份以 token 中的地址为准。

## **quanpool** 连接说明

[quanpool](https://quanpool.com/) 使用 QUIC 接口，需要放行节点的 UDP 端口，并提供证书 SHA-256 指纹。以下示例使用亚洲节点，欧洲节点作为故障切换；节点与指纹若有变化，以矿池官网 Start mining 页面为准。

Windows PowerShell（将 `YOUR_PAYOUT_ADDRESS` 换成自己的地址，`rig1-gpu` 换成矿工名）：

```powershell
.\8gkg_quantusminer.exe serve --node-addr "15.235.146.115:9834;37.187.143.115:9834" --auth-token YOUR_PAYOUT_ADDRESS.rig1-gpu --tls-cert-sha256 87dc37af6096a3ddc860b94368ca087775f3ad3e0c4e9bcff3b07ea08d8abef6
```

Linux：

```bash
./8gkg_quantusminer serve --node-addr '15.235.146.115:9834;37.187.143.115:9834' --auth-token YOUR_PAYOUT_ADDRESS.rig1-gpu --tls-cert-sha256 87dc37af6096a3ddc860b94368ca087775f3ad3e0c4e9bcff3b07ea08d8abef6
```

HiveOS 飞行表中选择 Custom，设置矿工名 `8gkg_quantusminer`，安装地址填写对应安装包的下载地址。在“附加配置”中填写：

```text
--node-addr "15.235.146.115:9834;37.187.143.115:9834" --auth-token YOUR_PAYOUT_ADDRESS.rig1-gpu --tls-cert-sha256 87dc37af6096a3ddc860b94368ca087775f3ad3e0c4e9bcff3b07ea08d8abef6
```

本软件的 HiveOS 脚本从“附加配置”读取参数，请把钱包身份写在上述 `--auth-token` 中。省略 `-d` 使用所有支持的显卡，追加 `-d 0,1` 选择指定设备；追加 `--devfee 0` 关闭开发者费用。QUIC 接口不要追加 `--ssl`。

只检查连接、证书与任务：在相同连接参数后追加 `--check-pool --seconds 15`。此模式不运行 GPU，也不抽水。原有 TCP/SSL 矿池仍可通过 `--pool HOST:PORT --wallet ADDRESS --worker RIG` 使用。

QUIC 协议没有逐份接受确认，软件显示已发送结果，矿池算力与接受数显示 `--`；实际入账请查矿池页面。本地有效证明校验不等于公网入账确认。

## 显卡适配与自动调优

以下型号已有实机运行与正确性验证：

| 显卡 | 架构 | 路径 |
| --- | --- | --- |
| GTX 1660 SUPER | SM75 / Turing | loop-v2；自动比较展开版 |
| RTX 3080 | SM86 / Ampere | 展开版、循环版自动选择 |
| RTX 4070 SUPER | SM89 / Ada | raw-x7 |
| RTX 5060 Ti | SM120 / Blackwell | 原生路径 |

RTX 20 系（2060、2070、2080、2080 Ti 及对应 SUPER 型号）按实际 SM75 架构参与同一套自动调优，包含循环版、展开版和精确兼容实现；**尚无 RTX 20 系实机测速，不能承诺提升幅度**。CMP 30HX 也按实际架构选核，尚未实机验证。

正常启动最多使用 20 秒，按每张显卡的 UUID 单独选择内核、线程块和批次。相同架构可共用实现，最终参数因显卡资源、频率和功耗状态而异，无需逐型号硬编码。1660 SUPER 实测仍以循环版更快，因此保留其默认路径。`--no-autotune` 跳过调优；手动参数会限制搜索范围。

## 开发者费用

默认 **1%** 的 GPU 计算时间用于开发者钱包：

```text
qzpV7LAcu9wgxqpcf9Sv3c7oGVhNC9zqebZYAagJVFNZ77dtm
```

使用同一矿池配置建立独立开发者会话，在 GPU 批次之间切换计算。抽水期间用户连接继续保持、接收任务和保活；切换过程不额外刷屏，不改变界面中的用户钱包、矿工名或矿池。启动信息始终显示已配置的费用比例。

费用按每张 GPU 的实际搜索批次耗时累计，长期接近设定比例；受批次粒度影响，短时比例会有偏差。默认通常累计约 198 秒用户计算后执行约 2 秒开发者计算。等待网络、空闲与自动调优不计费。开发者连接不可用时继续用户任务，不积累长时间补扣。

`Total Rate`、统计 API 与 HiveOS 算力显示用户和开发者计算的总速度；用户 A/R/P 与矿池算力仅统计用户份额。因此总速度不等于用户收款地址在矿池端的有效算力。

关闭开发者费用：在启动命令或 HiveOS 附加配置末尾添加：

```text
--devfee 0
```

关闭后不建立开发者连接，也不执行开发者任务。可使用 `--devfee 0.5`、`--devfee 2` 等自定义比例，范围为 0–100；`--devfee 100` 表示全部计算给开发者，开发者无可用任务时等待。JSON 配置示例：`{"devfee": 0}`，命令行参数覆盖配置文件。此费用与矿池自身的收费分开。

## 运行与已知限制

程序不需要另装 .NET、Go 或 CUDA Toolkit，需要可支持显卡的 NVIDIA 驱动。Linux 程序最高 GLIBC 导入版本为 2.17，实机验证环境为 Ubuntu 22.04。QUIC 传输程序由单文件释放到临时目录，退出时清理；临时目录须允许执行。

快速路径保留原有算术边界行为：CPU 完整 512 位校验可过滤错误提交，但不能恢复漏掉的候选。需要精确算术时使用 `--portable`、`--portable-unrolled` 或 `--carry-valid`。短时扫描速度不能作为矿池长期有效算力的保证。

使用 `--self-test -d 0` 检查显卡，`--benchmark -d 0 --seconds 10` 限时测速，`--help` 查看全部选项，`--licenses` 查看许可证。源码、重建步骤与测试记录保存在项目目录中。
