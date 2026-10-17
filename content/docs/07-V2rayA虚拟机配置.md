---
title: "虚拟机连接 V2rayA"
slug: "v2raya"
weight: 7
draft: false
ShowToc: true
hideMeta: true
description: "在 Ubuntu 22.04 虚拟机中安装、启动并使用 V2rayA 的操作说明，包含命令、检查流程与常见问题。"
---

[返回技术文档目录]({{< relref "/docs" >}}) · [返回工具合集]({{< relref "/tools.md" >}})

## 目的与适用场景

在 Ubuntu 虚拟机内部运行 V2rayA，使虚拟机自身具备代理能力，而不依赖 Windows 宿主机上的代理软件。

- 适用：Ubuntu 22.04 虚拟机，已有可用的线路配置信息。
- 不适用：本文不是通用安装教程，仅记录一次实际成功的配置过程。
- 采用的分流模式：国内直连分流模式（国内直连，其余走代理）。

## 准备环境

| 项目       | 内容                                        |
| ---------- | ------------------------------------------- |
| 宿主机     | Windows                                     |
| 虚拟机系统 | Ubuntu 22.04                                |
| V2rayA     | `v2raya_linux_x64_2.5.10`（Linux x64）      |
| V2Ray Core | `v2raya_core_linux_x64_2.5.10`（Linux x64） |
| 数据文件   | `geoip.dat`、`geosite.dat`                  |
| 其他       | 线路配置信息                                |

前置条件：

- 虚拟机拥有独立的网络连接，能获得局域网地址（示例：`192.168.0.104`）。
- 虚拟机系统时间正确。
- 具体的虚拟机网络模式（桥接 / NAT 等）未在记录中确认，此处不作限定。

## 操作步骤

### 1. 确认虚拟机联网与系统时间

原则：先保证 Ubuntu 自身网络正常，再处理代理。

```bash
date
ping -c 4 8.8.8.8
```

- 时间不正确会导致 HTTPS 证书验证失败（见“遇到的问题”）。
- 无法 ping 通 IP，说明虚拟机网络本身有问题，应先解决，不要继续后续步骤。

### 2. 安装下载工具

```bash
sudo apt update
sudo apt install curl
```

### 3. 下载并授权 V2rayA

下载 `v2raya_linux_x64_2.5.10`，放在 `~/Downloads/`，然后添加执行权限：

```bash
chmod +x ~/Downloads/v2raya_linux_x64_2.5.10
```

### 4. 准备 GeoIP / GeoSite 数据文件

V2rayA 运行需要 `geoip.dat` 和 `geosite.dat`。命令行下载不顺利时，可用浏览器下载后手动放入资源目录：

```text
~/.config/v2raya/assets/
├── geoip.dat
└── geosite.dat
```

### 5. 准备 V2Ray Core

V2rayA 只是管理程序，不是 V2Ray Core，需要另外准备 Core。使用 Linux x64 版本的核心文件，并确认系统能找到它：

```bash
which v2ray
```

预期输出：

```text
/usr/bin/v2ray
```

### 6. 启动 V2rayA

```bash
~/Downloads/v2raya_linux_x64_2.5.10 --v2ray-bin ~/Downloads/v2raya_core_linux_x64_2.5.10
```

参数说明：

| 部分                                       | 含义                   |
| ------------------------------------------ | ---------------------- |
| `~/Downloads/v2raya_linux_x64_2.5.10`      | V2rayA 程序本身        |
| `--v2ray-bin`                              | 指定 V2Ray Core 的路径 |
| `~/Downloads/v2raya_core_linux_x64_2.5.10` | 实际使用的 V2Ray Core  |

> 注意：此方式为前台运行。运行 V2rayA 的终端即 V2rayA 进程，**关闭该终端会导致 V2rayA 退出**。本文未配置后台常驻或开机自启。

### 7. 打开管理界面

在 Ubuntu 中用浏览器访问：

```text
http://127.0.0.1:2017
```

- `127.0.0.1` 指 Ubuntu 本机，`2017` 为 V2rayA 管理端口。
- 首次进入需创建管理员账号。
- 管理网页可以关闭，运行 V2rayA 的终端不能关闭。

### 8. 导入线路并建立线路组

1. 在管理界面导入线路配置信息。
2. 将线路加入线路组，而非将所有线路各自单独使用。
3. 按需选择线路组的使用方式：
   - **自动选择**：根据线路状态自动选择合适的线路。
   - **URL 测试**：通过访问指定网址，判断线路是否可用、延迟是否正常。
   - **故障切换**：当前线路异常时切换到其他可用线路。
   - 也可在线路组中手动选择某个线路。

### 9. 选择分流模式

选择 **国内直连分流模式**：

```text
国内网站  → 直接访问
需要代理  → 通过海外线路访问
```

## 常用命令与配置

| 用途            | 命令 / 地址                                                                                | 执行位置      |
| --------------- | ------------------------------------------------------------------------------------------ | ------------- |
| 查看系统时间    | `date`                                                                                     | Ubuntu 终端   |
| 检查 V2Ray Core | `which v2ray`                                                                              | Ubuntu 终端   |
| 测试 IP 网络    | `ping -c 4 8.8.8.8`                                                                        | Ubuntu 终端   |
| 测试域名解析    | `ping -c 4 google.com`                                                                     | Ubuntu 终端   |
| 添加执行权限    | `chmod +x ~/Downloads/v2raya_linux_x64_2.5.10`                                             | Ubuntu 终端   |
| 启动 V2rayA     | `~/Downloads/v2raya_linux_x64_2.5.10 --v2ray-bin ~/Downloads/v2raya_core_linux_x64_2.5.10` | Ubuntu 终端   |
| 管理地址        | `http://127.0.0.1:2017`                                                                    | Ubuntu 浏览器 |

## 检查结果

配置完成后，按以下顺序检查（出问题时也按此顺序排查）：

1. **虚拟机能否联网**

   ```bash
   ping -c 4 8.8.8.8
   ```

   不通则先解决虚拟机网络。
2. **域名解析是否正常**

   ```bash
   ping -c 4 google.com
   ```

   IP 通而域名不通，检查 DNS。
3. **V2Ray Core 是否可被找到**

   ```bash
   which v2ray
   ```

   预期输出 `/usr/bin/v2ray`。
4. **V2rayA 是否在运行**：查看运行 V2rayA 的终端，程序已退出则 V2rayA 不再运行。
5. **管理页面能否打开**：访问 `http://127.0.0.1:2017`，能进入说明管理服务正常。
6. **线路是否可用**：在管理界面检查“线路 → 线路组 → 当前使用线路”，确认线路没有全部失效。
7. **访问测试**：在国内直连分流模式下，目标海外网站能正常访问即为成功。

## 遇到的问题与解决办法

### HTTPS 访问报证书错误

- **现象**：出现 SSL、HTTPS、certificate、`certificate verify failed` 等报错。
- **原因**：虚拟机系统时间不正确，导致证书验证失败，不一定是网站或代理的问题。
- **解决**：执行 `date` 确认系统时间，校正后重试。

### 关闭终端后 V2rayA 停止工作

- **现象**：管理页面无法打开，代理失效。
- **原因**：V2rayA 以前台方式运行，关闭终端即结束进程。
- **解决**：重新执行启动命令，并保持该终端开启。

### 启动时找不到程序或无执行权限

- **解决**：确认文件路径正确，并执行 `chmod +x ~/Downloads/v2raya_linux_x64_2.5.10`。

### 找不到 V2Ray Core

- **解决**：执行 `which v2ray` 检查；同时确认启动命令中 `--v2ray-bin` 指向的路径正确。

### 虚拟机可访问 IP 但无法访问域名

- **解决**：检查 DNS 配置，参见“检查结果”第 2 步。

## 参考资料与更新记录

- 配套的使用记录：[我的 V2rayA 折腾记录]({{< relref "/posts/v2raya-zhe-teng-ji-lu" >}})
- 软件版本：V2rayA 2.5.10，Ubuntu 22.04
- 最后验证日期：2026-10-15
