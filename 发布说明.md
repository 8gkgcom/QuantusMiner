# QuantusMiner 1.4.6 · QTS 挖矿使用指南

面向 NVIDIA 显卡，支持 Windows 与 HiveOS，开发者费用 0%。启动时自动调优，通常在 20 秒内完成；支持 SSL 连接和异常退出、卡死后的自动重启。

## HiveOS 配置

**钱包、矿池、矿工名全部填写在「附加参数 / Extra config」中。** 本版本不读取飞行表单独的钱包模板、矿池、密码和算法字段，无需添加算法参数。

1. 在飞行表中选择自定义矿工 **Custom**，打开自定义配置。
2. 矿工名称填写 `quantusminer`。
3. 安装地址填写本次发布提供的 **quantusminer-1.4.6.tar.gz 下载链接**，不要填写 Windows 压缩包链接。
4. 在「附加参数 / Extra config」粘贴下方参数，替换为自己的 QTS 钱包地址。
5. 保存并应用飞行表。

```text
--server sg.lproute.com:5660 --user 你的QTS钱包地址.WORKER_NAME --pass x --ssl 0
```

**务必将 `你的QTS钱包地址` 替换为自己的完整 QTS 收款地址。** `WORKER_NAME` 保留原样，程序会自动替换成本机的系统主机名。例如主机名为 `rig01`，矿工名就会使用 `rig01`。

其他钱包、矿池、算法字段如可留空，直接留空；如 HiveOS 界面要求填写，这些字段也不会覆盖附加参数。更换钱包或矿池时，请修改附加参数并重新应用飞行表。

## 常用附加参数

| 参数 | 用途 | 示例 |
| --- | --- | --- |
| `--server` / `-server` | 矿池地址和端口 | `--server sg.lproute.com:5660` |
| `--user` / `-user` | QTS 钱包，可在末尾添加矿工名 | `--user 你的QTS钱包地址.WORKER_NAME` |
| `--work` / `-work` | 单独指定矿工名 | `--work rig01` |
| `--pass` / `-pass` | 矿池密码，通常为 `x` | `--pass x` |
| `--ssl` / `-ssl` | `1` 开启 SSL，`0` 使用普通 TCP | `--ssl 0` |
| `-d` | 选择显卡，编号从 `0` 开始；不填则使用全部显卡 | `-d 0,1,3` |
| `--print-interval` | 算力统计刷新间隔，单位秒，默认 `30` | `--print-interval 30` |

### 指定显卡

例如只使用编号为 0、1、3 的显卡，请确认这些编号在机器上实际存在：

```text
--server sg.lproute.com:5660 --user 你的QTS钱包地址.WORKER_NAME --pass x --ssl 0 -d 0,1,3
```

### 固定矿工名

```text
--server sg.lproute.com:5660 --user 你的QTS钱包地址 --work rig01 --pass x --ssl 0
```

### 使用 SSL 矿池

将下方地址和端口替换为矿池提供的 SSL 接入地址：

```text
--server 矿池地址:SSL端口 --user 你的QTS钱包地址.WORKER_NAME --pass x --ssl 1
```

SSL 连接不验证服务器证书，支持自签证书，无需加载证书文件。端口必须使用矿池提供的 SSL 端口；上面的 `sg.lproute.com:5660` 示例使用普通 TCP。

## Windows 启动

下载并解压 `quantusminer-1.4.6-win64.zip`，右键编辑 `start.bat`：

- 将 `WALLET` 改为自己的 QTS 钱包地址。
- 按需修改 `POOL`、`WORKER` 和 `SSL`。
- 保存后双击 `start.bat` 启动，程序与 BAT 保持在同一目录。

也可以在程序所在目录打开 PowerShell，执行：

```powershell
.\quantusminer.exe --server sg.lproute.com:5660 --user 你的QTS钱包地址.WORKER_NAME --pass x --ssl 0
```

## 启动后检查

- 确认启动信息中的钱包、矿池、矿工名和显卡正确。
- 等待自动调优完成。开始挖矿后约 10 秒首次显示统计，此后默认每 30 秒刷新。
- 出现 `Share accepted` 表示矿池已接受份额；矿池后台数据可能稍后更新。
- HiveOS 可通过矿机页面查看运行状态与算力。

如连接失败，检查附加参数中的矿池地址、端口和 SSL 设置。钱包修改未生效时，确认修改的是「附加参数」，并重新应用飞行表。旧版本若出现算法不支持的报错，请更新到 **1.4.6**，并从附加参数中删除其他软件遗留的算法参数。
