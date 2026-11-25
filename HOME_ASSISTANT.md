# TP-Link Smart Home (TPAP)

这个自定义集成的域名是 `tplink_tpap`。它基于树莓派当前的
[Home Assistant Core 2026.11.2 TP-Link Smart Home](https://github.com/home-assistant/core/tree/2026.11.2/homeassistant/components/tplink)，
保留原集成的配置流程、设备平台、服务和默认轮询逻辑。TPAP 支持来自
[python-kasa PR #1592](https://github.com/python-kasa/python-kasa/pull/1592)，
使用本地 `tpap-custom` 的提交 `df229a7aae8d56f71e4dd85382e9b3b3377c9786`。
依赖位于自定义集成的命名空间内，可与官方 `tplink` 并存。

## 安装到树莓派

1. 从 `ha-tplink-tpap` 分支打包集成，再把 ZIP 传到 Pi 的 SSH 用户主目录。已有安装 ZIP 时，可以直接运行第二条命令。

   ```bash
   git archive --format=zip --output=ha-tplink-tpap.zip HEAD custom_components/tplink_tpap
   scp ha-tplink-tpap.zip dukchen@pi:~/
   ```

2. 在 Pi 的 SSH 会话中执行。确认 `docker exec homeassistant hass --version`
   显示 `2026.11.2`。以下命令只添加新的目录，发现同名目录时停止。

   ```bash
   kasa_unpack=$(mktemp -d) &&
   python3 -m zipfile -e "$HOME/ha-tplink-tpap.zip" "$kasa_unpack" &&
   sudo test ! -e /home/pi/ha-config/custom_components/tplink_tpap &&
   sudo install -d /home/pi/ha-config/custom_components &&
   sudo cp -R "$kasa_unpack/custom_components/tplink_tpap" /home/pi/ha-config/custom_components/ &&
   docker restart homeassistant
   ```

3. 在 Pi 的 Home Assistant 中打开 **设置 → 设备与服务 → 添加集成**，
   搜索 **TP-Link Smart Home (TPAP)**。建议先手动输入一台 TPAP 设备的 IP 测试。
   这个域名会创建自己的实体和设备记录。若同一设备已经在其他 TP-Link
   集成中配置，避免同时添加，以免出现重复实体和轮询。

## 验证和许可证

包内提供英文配置界面翻译。已检查 Python 语法、依赖命名空间隔离、
TPAP 连接参数的序列化及 transport 选择。尚未在 Pi 的 HA 进程或真实
设备上运行。上游 TPAP 实现可能以可逆编码保存认证数据，请把 HA
配置备份视为包含凭据。

Home Assistant 源文件采用 Apache-2.0，python-kasa 源文件采用
GPL-3.0-or-later。上游许可证随包提供。对上游源文件的修改主要是
隔离库导入、使用新集成域名、调整服务目标和生成英文翻译文件。
