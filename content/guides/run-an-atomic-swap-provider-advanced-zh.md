---
author: Seth For Privacy
comments: false
date: "2021-08-24T22:00:00-04:00"
summary: 本指南将带您了解如何扮演提供者（也称为卖方或“自动交换后端”(ASB)）。
draft: false
hidemeta: false
showToc: true
tags:
- 原子交换
- 比特币
- 门罗币
title: 运行原子交换提供者（进阶）
---

# Introduction

***声明——如果您通读了本指南但对安装的过程和ASB工具的工作原理没有扎实的了解，请先在测试网上测试该软件，*不是*主网。本指南适用于那些认真对待运行ASB且理解其含义并能够处理尖端软件本身的人。***

期待很久了，但它终于来了。*今天*，你可以直接点对点交换比特币 <> 门罗币，通过Tor，无需托管人或受信任的第3方，无需KYC（了解你的客户）资讯，什么都不需要。本指南将带您了解如何扮演提供者（也称为卖方或“自动交换后端”(ASB)）。

这是跨链交换的未来，而就在今天成为可能。

原子交换打开了广泛的跨链用例，但关键是它们是无需信任的、不可审查的，并且是完全匿名/假匿名的。

有关原子交换的更多信息，请查看以下链接：

- <https://localmonero.co/knowledge/monero-atomic-swaps>
- <https://www.monerooutreach.org/stories/monero-atomic-swaps.html>
- <https://github.com/comit-network/xmr-btc-swap>
- <https://comit.network/blog/2020/10/06/monero-bitcoin/>

# 背景

本指南将尝试简化和提炼我从测试运行ASB中学到的东西，并提供一种更简单的复制粘贴格式来启动和运行。这个初始构建有点进阶，但我正在研究一个应该更容易上手并保持最新状态的Docker Compose设置。

可以在此处找到官方指南，如果您更愿意参考这些文档，其就足以入门了：

- <https://github.com/comit-network/xmr-btc-swap/blob/master/docs/asb/README.md>

为了更好地了解`asb`工具的作用，为什么需要运行它来销售XMR，以及它如何与`swap`工具互动，请阅读以下官方指南部分：

- <https://github.com/comit-network/xmr-btc-swap/blob/master/docs/asb/README.md#setup-details>

如果你想更深入地了解协议的工作原理以及交换过程中每个步骤的细节（我十分推荐），你可以阅读下面的博客文章：

- <https://comit.network/blog/2020/10/06/monero-bitcoin>

# 了解交换过程中的步骤

我不会在我的指南中重写它，因为它在COMIT网络博客文章中有很好的布局。我*强烈*建议熟悉交换过程并尽你理解所能地阅读以上文档，但最相关的部分在此处：

- <https://comit.network/blog/2020/10/06/monero-bitcoin/#long-story-short>

# 维护隐私/匿名

重要的是您了解运行此工具将允许CLI端的用户连结交易IDs与IP地址，除非您使用TOR于所有网络。以下是一些在运行该工具时保护您自己的隐私和/或匿名的快速建议：

- *绝不*在家运行这个工具，除非你只使用Tor，并且不暴露任何clearnet地址
  - 如果你在家运行, 如果可能的话，*绝不*将运行这个软件的机器通过SSH暴露在互联网上

- 只使用`asb`工具于Tor之后，除非你有特定的原因或者想确保那些没有Tor访问权限/不了解Tor的人可以和你交换
- 如果您出于任何原因需要共享日志，请务必编辑：
  - 交换IDs
  - 交易IDs
  - IP地址
- [运行你自己的门罗币节点]({{< ref "/content/guides/run-a-monero-node.md" >}})
- 如果可能，运行你自己的比特币节点和[ElectrumX server](https://electrumx-spesmilo.readthedocs.io/en/latest/)
- 在收到从交换而来的资金后，使用比特币隐私工具如[Samourai Wallet](https://www.samouraiwallet.com/)来保护您的隐私并保护您免于受污染的比特币
  - 有关比特币隐私的更多信息，请在此处查看BitcoinQnA关于该主题的帖子：<https://bitcoiner.guide/privacy/>
  - 具体如何使用Samourai钱包，请参阅他的指南： <https://bitcoiner.guide/privacy/separate/>

# 先决条件

本指南将假设以下内容已经到位：

- 您已经有一台电脑/伺服器来托管此工具（最好是为您托管的VPS或专用服务器）
- 您可以在要用于该工具的主机上进入命令行
- 如果你想使用DNS，你已经有一个域名并且可以轻松配置DNS
- 你要么运行自己的门罗币节点，要么手边有一个你信任的节点
  - 任何人都非常欢迎使用我的[公共门罗币节点]({{< ref "/content/about.md#high-performance-monero-nodes" >}})
- 你已经有一些你愿意通过ASB出售的门罗币
- 您可以轻松发送和接收门罗币
- 您可以通过工具轻松处理可能受污染的比特币,如[Samourai Wallet](https://www.samouraiwallet.com/)

# 获取工具

我提供的说明将假定您使用的是Linux，但Windows/macOS应该类似。入门的第一步是获得您需要的所有工具。

## 自动交换代理(ASB)

1. 创建一个文件夹来保存我们所有的相关文件

    ```bash
    mkdir ~/asb
    cd ~/asb
    ```

2. 通过浏览器下载最新版本的`asb`工具，即`asb_0.11.0_linux_x86_64.tar`
    1. <https://github.com/comit-network/xmr-btc-swap/releases/latest>
    2. 或者，您可以通过命令列下载该工具

        ```bash
        wget https://github.com/comit-network/xmr-btc-swap/releases/download/0.10.0/asb_0.10.0_Linux_x86_64.tar
        ```

3. 提取`asb`二进制文件
    1. 打开终端机
    2. 运行以下命令：

        ```bash
        tar xvf asb_0.11.0_Linux_x86_64.tar
        rm asb_0.11.0_Linux_x86_64.tar
        sudo chmod +x asb
        sudo cp asb /usr/local/bin/
        ```

4. 验证二进制文件是否正常工作

    ```bash
    asb --version
    ```

{{< figure src="/run-an-atomic-swap-provider-advanced/asb-version.png" align="center" style="border-radius: 8px;" >}}

## monero-wallet-rpc

1. 下载最新版本的门罗币二进制文件，即`monero-linux-x64-v0.18.1.0.tar.bz2`
    1. <https://github.com/monero-project/monero/releases/latest>
    2. 或者，您可以通过命令列下载该工具

        ```bash
        cd ~/asb
        wget https://downloads.getmonero.org/cli/monero-linux-x64-v0.18.1.0.tar.bz2
        ```

2. 提取`monero-wallet-rpc`二进制文件
    1. 打开终端机
    2. 运行以下命令：

        ```bash
        tar xvf monero-linux-x64-v0.18.1.0.tar.bz2
        sudo chmod +x monero-x86_64-linux-gnu-v0.18.1.0/monero-wallet-rpc
        sudo cp monero-x86_64-linux-gnu-v0.18.1.0/monero-wallet-rpc /usr/local/bin
        rm monero-linux-x64-v0.18.1.0.tar.bz2
        rm -rf monero-x86_64-linux-gnu-v0.18.1.0
        ```

3. 验证二进制文件是否正常工作

    ```bash
    monero-wallet-rpc --version
    ```

{{< figure src="/run-an-atomic-swap-provider-advanced/monero-wallet-rpc-version.png" align="center" style="border-radius: 8px;" >}}

## Tor 守护进程

- 如果您使用的是Debian，只需运行以下命令即可安装并启动Tor

    ```bash
    sudo apt-get install tor
    sudo systemctl enable tor
    sudo systemctl start tor
    ```

- 如果您使用的是Ubuntu，请按照官方文档使用Tor提供的存储库
  - <https://support.torproject.org/apt/tor-deb-repo/>
  - 配置完Tor存储库后，运行以下命令来安装和启动Tor

    ```bash
    sudo apt install tor deb.torproject.org-keyring
    sudo systemctl enable tor
    sudo systemctl start tor
    ```

- 如果您使用的是CentOS/RHEL，请按照他们的官方文档使用Tor提供的存储库
  - <https://support.torproject.org/rpm/>
  - 配置完Tor存储库后，运行以下命令来安装和启动Tor

    ```bash
    sudo yum install tor
    sudo systemctl enable tor
    sudo systemctl start tor
    ```

# 通过UFW进行初始强化
我们希望通过使用UFW锁定防火墙以仅允许访问SSH和`asb`所需的端口来确保以简单的方式强化系统。

UFW入门的精彩介绍[on Landchad.net](https://landchad.net/ufw).

运行以下命令以添加一些基本的UFW规则并启用防火墙：

```bash
# 拒绝所有非明确允许的端口
sudo ufw default deny incoming
sudo ufw default allow outgoing

# 允许SSH访问
sudo ufw allow ssh

# 允许预设的ASB端口（如果只在Tor上运行，请删除以下两行，因为它们不需要）
sudo ufw allow 9939/tcp
sudo ufw allow 9940/tcp

# 启用UFW
sudo ufw enable
```

# 配置工具

## 设置ASB用户和目录

我们将为这两个工具和下面需要的目录设置一个唯一的用户。

```bash
# 创建系统用户和群组以运行asb和monero-wallet-rpc
sudo addgroup --system asb
sudo adduser --system asb --home /var/lib/asb

# 为asb工具创建必要的目录
sudo mkdir /var/run/asb
sudo mkdir /var/log/asb
sudo mkdir /etc/asb

# 为新目录设置权限
sudo chown asb:asb /var/run/asb
sudo chown asb:asb /var/log/asb
sudo chown -R asb:asb /etc/asb
```

## monero-wallet-rpc systemd配置

`monero-wallet-rpc`是`asb`工具用于连接到门罗币区块链、管理门罗币资金以及根据需要为每次交换签署/发送交易的工具。

使用正确的选项自动运行它的最简单方法是简单地复制下面的systemd脚本的内容并使用vim或nano将其保存到`/etc/systemd/system/monero-wallet-rpc.service`：

```bash
sudo nano /etc/systemd/system/monero-wallet-rpc.service
```

*要退出nano shell并保存文件，请按`ctrl+x`。*

***注意：如果您没有在同一台主机上运行门罗币节点，请务必将`127.0.0.1:18089`daemon-host参数替换为适当的门罗币守护程序URL，如`node.sethforprivacy.com:18089`.***

```systemd
[Unit]
Description=Monero Wallet RPC (Mainnet)
After=network.target

[Service]
# 进程管理
####################

Type=forking
PIDFile=/var/run/asb/monero-wallet-rpc.pid
ExecStart=/usr/local/bin/monero-wallet-rpc --pidfile /var/run/asb/monero-wallet-rpc.pid --daemon-host 127.0.0.1:18089 --rpc-bind-port 18083 --disable-rpc-login --wallet-dir /etc/asb --detach --log-file /var/log/asb/monero-wallet-rpc.log
Restart=on-failure
RestartSec=30

# 目录创建和权限
####################################

# 作为asb:asb运行
User=asb
Group=asb

# /run/asb
RuntimeDirectory=asb
RuntimeDirectoryMode=0710

# /var/lib/asb
StateDirectory=asb
StateDirectoryMode=0710

# /var/log/asb
LogsDirectory=asb
LogsDirectoryMode=0710

# /etc/asb
ConfigurationDirectory=asb
ConfigurationDirectoryMode=0710

# 强化措施
####################

# 提供私有的/tmp和/var/tmp。
PrivateTmp=true

# 为进程以唯读方式挂载/usr、/boot/和/etc。
ProtectSystem=full

# 拒绝访问/home、/root和/run/user
ProtectHome=true

# 禁止进程及其所有子进程通过execve()获得新特权。
NoNewPrivileges=true

[Install]
WantedBy=multi-user.target
```

## 自动交换代理(ASB)systemd配置
使用正确的选项自动运行它的最简单方法是简单地复制下面的systemd脚本的内容并使用vim或nano将其保存到`/etc/systemd/system/asb.service`：

```bash
sudo nano /etc/systemd/system/asb.service
```

*要退出nano shell并保存文件，请按`ctrl+x`。*

```systemd
[Unit]
Description=Automated swap broker (ASB)
After=network.target monero-wallet-rpc.service

[Service]
# Process management
# 进程管理
####################

Type=simple
ExecStart=/usr/local/bin/asb --config /etc/asb/config.toml start
StandardOutput=append:/var/log/asb/asb.log

# 目录创建和权限
####################################

# 作为asb:asb运行
User=asb
Group=asb

# /var/log/asb
LogsDirectory=asb
LogsDirectoryMode=0710

# /etc/asb
ConfigurationDirectory=asb
ConfigurationDirectoryMode=0710

# 强化措施
####################

# 提供私有的/tmp和/var/tmp。
PrivateTmp=true

# 为进程以唯读方式挂载/usr、/boot/和/etc。
ProtectSystem=full

# 拒绝访问/home、/root和/run/user
ProtectHome=true

# 禁止进程及其所有子进程通过execve()获得新权限。
NoNewPrivileges=true

[Install]
WantedBy=multi-user.target
```

## ASB配置文件
此配置文件将指示`asb`工具的运行方式，因此请务必根据需要更改参数。

以下是您*必须*更改的关键参数：

- `external_addresses`应该反映可达的外部地址
  - 如果使用Tor-only ASB，你需要启动ASB一次，复制那里列出的`/onion3/`地址，然后像这样添加它们：

    ```conf
    external_addresses = ["/onion3/b4wfknratwn6rcpvpczs5pgtyyafedpcfjqnupr32qdfu63x6odql4id:9939", "/onion3/b4wfknratwn6rcpvpczs5pgtyyafedpcfjqnupr32qdfu63x6odql4id:9940"]
    ```

  - 如果使用不带DNS的IPv4地址，请使用如下条目：

    ```conf
    external_addresses = ["/ip4/5.9.120.18/tcp/9939", "/ip4/5.9.120.18/tcp/9940/ws"]
    ```

  - 如果使用DNS，请使用如下条目：

    ```conf
    external_addresses = ["/dns4/swap.sethforprivacy.com/tcp/9939", "/dns4/swap.sethforprivacy.com/tcp/9940/ws"]
    ```

  - 如果您对进阶DNS配置感到舒适，请使用下面的文档探索使用`/dnsaddr`格式
    - <https://github.com/multiformats/multiaddr/blob/master/protocols/DNSADDR.md>

以下是您应该更改的一些关键参数：

- `min_buy_btc`应该反映您希望交换参与者能够提供的比特币的最小金额
- `max_buy_btc`应该反映您希望互换参与者能够提供的比特币的最大金额
- `ask_spread`应设置为您偏爱的点差（您将收取的市场价格之上的百分比）
  - `0.05`等于5%，`0.10`等于10%，等等。
- `electrum_rpc_url` 如果您运行自己的Electrum服务器，或者拥有比预设服务器更信任的服务器

1. 为asb进程创建配置

    ```bash
        sudo nano /etc/asb/config.toml
    ```

    *要退出nano shell并保存文件，请按`ctrl+x`。*

    ```ini
    [data]
    dir = "/etc/asb"

    [network]
    listen = ["/ip4/0.0.0.0/tcp/9939", "/ip4/0.0.0.0/tcp/9940/ws"]
    rendezvous_point = "/dnsaddr/swap.sethforprivacy.com/p2p/12D3KooWCULyZKuV9YEkb6BX8FuwajdvktSzmMg4U5ZX2uYZjHeu"
    # 例子 external_addresses: 
    external_addresses = ["/onion3/example.onion/tcp/9939", "/onion3/example.onion/tcp/9940/ws"]

    [bitcoin]
    electrum_rpc_url = "ssl://electrum.blockstream.info:50002"
    target_block = 3
    network = "Mainnet"

    [monero]
    wallet_rpc_url = "http://127.0.0.1:18083/json_rpc"
    network = "Mainnet"

    [tor]
    control_port = 9051
    socks5_port = 9050

    [maker]
    min_buy_btc = 0.0005
    max_buy_btc = 0.001
    ask_spread = 0.05
    price_ticker_ws_url = "wss://ws.kraken.com/"
    ```

    在上面的配置中，可以使用其他推荐的会合节点来代替我的节点：

    ```bash
    /dns4/rendezvous.xmr.radio/tcp/8888/p2p/12D3KooWN3n2MioS515ek6LoUBNwFKxtG2ribRpFkVwJufSr7ro7
    ```

2. 重新加载`systemd`以启用新的systemd脚本：

    ```bash
    sudo systemctl daemon-reload
    ```

## Tor 配置

为了让`asb`工具能够为自己正确配置隐藏服务，您需要在`/etc/tor/torrc`的Tor配置文件中添加 3行，将新创建的用户添加到` debian-tor`群组，然后重新启动`tor`。

1. 使用vim或nano编辑`/etc/tor/torrc`中的Tor配置文件以配置Tor以允许`asb`设置和配置隐藏服务：

    ```bash
    sudo nano /etc/tor/torrc
    ```

    *要退出nano shell并保存文件，请按`ctrl+x`。*

    ```conf
    # 允许asb工具配置隐藏服务
    ControlPort 9051
    CookieAuthentication 1
    CookieAuthFileGroupReadable 1
    ```

2. 运行以下命令将`asb`用户添加到`debian-tor` 群组并重新启动`tor`：

    ```bash
    sudo adduser asb debian-tor
    sudo systemctl restart tor
    ```

# 使用工具

## 启动monero-wallet-rpc和asb

为了启动这些工具，只需运行以下适当的命令：

1. `monero-wallet-rpc`应该总是优先启动：

    ```bash
    sudo systemctl start monero-wallet-rpc
    ```

2. 然后启动`asb`：

    ```bash
    sudo systemctl start asb
    ```

## 重启monero-wallet-rpc和asb
为了重新启动工具，只需运行以下适当的命令：

1. `monero-wallet-rpc`应该总是优先重启：

    ```bash
    sudo systemctl restart monero-wallet-rpc
    ```

2. 然后重启`asb`：

    ```bash
    sudo systemctl restart asb
    ```

# 为你的门罗币钱包注资

在启动时，ASB工具将为您提供一个门罗币地址，用于将资金存入门罗币钱包。

1. 要获取地址，请运行以下命令：

    ```bash
    sudo grep monero_address /var/log/asb/asb.log
    ```

2. 确保保存地址，因为一旦您存入资金，它将不再显示。
    要在您的机器上获取地址的二维码，您可以运行以下命令（当然，将地址替换为您在上面收集的地址）：

    ```bash
    qrencode "4A4tLy1b2PFFdHHvZubb85enYMroBZ3b3i8AV45gBATb2Kas1jNmVP3BwGq4HhSMwsfuedh2hK6MBMmG8M6KAvGGDVBqLDw" -t ascii -o -
    ```

    如果未安装`qrencode`，您可以使用`sudo apt install qrencode`或`sudo dnf install qrencode`安装它

    如果您在存入资金之前未能保存地址，您可以通过以下命令直接从`monero-wallet-rpc`中获取：

    ```bash
    curl http://127.0.0.1:18083/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"get_address","params":{"account_index":0,"address_index":[0]}}' -H 'Content-Type: application/json'
    ```

3. 将门罗币发送到提供的地址，但请记住，这是个热钱包并且没有密码保护——您应该尽可能减少门罗币钱包中的门罗币数量。

# 将你的新ASB添加到unstoppableswap.net

***注意：你只能添加基于IPv4和DNS地址的ASBs ATM，所以如果你正在使用onion-only ASB，现在跳过这一步。***

1. 导航到<https://unstoppableswap.net/>
2. 单击"交换提供者"框

    {{< figure src="/run-an-atomic-swap-provider-advanced/unstoppable-home.png" align="center" style="border-radius: 8px;" >}}

3. 单击"提交交换提供者"

    {{< figure src="/run-an-atomic-swap-provider-advanced/unstoppable-providers.png" align="center" style="border-radius: 8px;" >}}

4. 输入您的外部地址和对端ID

    要获取您的对端ID，只需运行以下命令：

    ```bash
    sudo grep peer_id /var/log/asb/asb.log
    ```

    {{< figure src="/run-an-atomic-swap-provider-advanced/unstoppable-submit.png" align="center" style="border-radius: 8px;" >}}

5. 点击"提交"

# 交换期间问题处理

密切关注日志非常重要，尤其是在前几次交换期间，以确保您的配置正常运行。

要查看日志，只需运行以下命令：

```bash
sudo tail -f /var/log/asb/asb.log
```

如果您在交换失败期间看到以`ERROR`开头的行，请通过搜索以下网址来检查Github上报告的现有问题：

- <https://github.com/comit-network/xmr-btc-swap/issues>

如果您遇到的问题没有open issue，请务必提交一个尽可能详细的新问题，包括：

- `asb`的版本
  - 通过运行`asb --version`取得
- 失败的交换/问题周围的所有日志行
   - 一定要编辑IP地址、交换ID等。就像在[本文开头]({{< ref "#maintaining-privacy-anonymity" >}})提及的那样!
- 你在什么作业系统和版本上运行`asb`
- 您可以提供围绕该问题的任何其他详细信息

大部分问题都可以透过简单的[重新启动 `asb` 工具]({{< ref "#restarting-monero-wallet-rpc-and-asb" >}})解决, 但在重新启动之前收集日志，以确保稍后您可以在必要时追踪问题。

# 从ASB钱包中提取比特币

交换完成后，`asb`工具将比特币存储在内部钱包中。每当您想从该钱包中提取比特币时，您都需要停止`asb`工具，提取比特币，然后再次启动`asb`工具。

为此，请运行以下命令，替换您的比特币地址和偏爱金额：

```bash
sudo systemctl stop asb
asb withdraw-btc --address BITCOINADDRESS --amount "0.XX BTC"
sudo systemctl start asb
```

如果您想提取全部余额，只需运行：

*注意：目前有一个错误阻止此命令运行，所以现在只能通过上述命令提取金额：<https://github.com/comit-network/xmr-btc-swap/issues/662>*

```bash
sudo systemctl stop asb
asb withdraw-btc --address <BITCOINADDRESS>
sudo systemctl start asb
```

# 检查比特币和门罗币余额

检查两个钱包当前余额的最简单方法是停止ASB进程并运行`asb balance`：

```bash
sudo systemctl stop asb
asb balance
sudo systemctl start asb
```

如果你想在不停止ASB的情况下检查门罗币余额，你可以运行：

```bash
curl http://127.0.0.1:18083/json_rpc -d '{"jsonrpc":"2.0","id":"0","method":"get_balance","params":{"account_index":0,"address_indices":[0]}}' -H 'Content-Type: application/json'
```

# 升级工具

这两种工具都需要保持最新，因此为了简化过程，这里可以使用一组快速命令来更新这两种工具。

只需将下载网址替换为最新版本的网址即可。

## monero-wallet-rpc

```bash
cd ~/asb
wget https://downloads.getmonero.org/cli/monero-linux-x64-0.18.1.0.tar.bz2
tar xvf monero-linux-x64-0.18.1.0.tar.bz2
sudo chmod +x monero-x86_64-linux-gnu-0.18.1.0/monero-wallet-rpc
sudo mv -f monero-x86_64-linux-gnu-0.18.1.0/monero-wallet-rpc /usr/local/bin/
rm monero-linux-x64-0.18.1.0.tar.bz2
rm -rf monero-x86_64-linux-gnu-0.18.1.0
```

## asb

```bash
cd ~/asb
wget https://github.com/comit-network/xmr-btc-swap/releases/download/0.11.0/asb_0.11.0_Linux_x86_64.tar
tar xvf asb_0.11.0_Linux_x86_64.tar
rm asb_0.11.0_Linux_x86_64.tar
sudo chmod +x asb
sudo mv -f asb /usr/local/bin/
```

# 进阶配置选项
此部分在启动ASB时完全没有必要遵循，但将包含一些可供给你这样的ASB所有者使用的进阶选项。

## 使用/dnsaddr格式作为外部地址

COMIT原子交换的网络基础`libp2p`中包含的一项很酷的功能是能够使用一个统一的地址来描述您的ASB的所有可能的可达方法。这使您可以通过IP、DNS和Onionv3访问ASB，同时为swap的用户提供一个统一的地址，允许他们的客户端为其网络配置和/或Tor使用选择最佳选项。

有关可用/需要的规范和配置的更多详细信息，请参阅此处的官方文档： <https://github.com/multiformats/multiaddr/blob/master/protocols/DNSADDR.md>

要配置它，您需要为您的每个域名添加一笔TXT DNS记录,用于您希望通过`/dnsaddr`条目公布的地址。

1. 通过`listen`和Tor配置来配置期望的可达性

    通常这只是预设侦听选项并启用本指南前面提到的Tor配置。

2. 通过您的域名提供商使用`_dnsaddr`的主机条目和如下记录添加TXT记录，根据您的洋葱地址和其他偏爱可访问端点对其进行自定义：

    *注意：此示例适用于Namecheap，但所有域名提供商都应允许类似的自定义TXT记录。*

    {{< figure src="/run-an-atomic-swap-provider-advanced/dnsaddr-entries.png" align="center" style="border-radius: 8px;" >}}

    ```bash
    dnsaddr=/ip4/5.9.120.18/tcp/9939/p2p/12D3KooWCPcfhr6e7V7NHoKWRxZ5zPRr6v5hGrVPhHdsftQk2DXW
    dnsaddr=/ip4/5.9.120.18/tcp/9940/ws/p2p/12D3KooWCPcfhr6e7V7NHoKWRxZ5zPRr6v5hGrVPhHdsftQk2DXW
    dnsaddr=/onion3/b4wfknratwn6rcpvpczs5pgtyyafedpcfjqnupr32qdfu63x6odql4id:9939/p2p/12D3KooWCPcfhr6e7V7NHoKWRxZ5zPRr6v5hGrVPhHdsftQk2DXW
    dnsaddr=/onion3/b4wfknratwn6rcpvpczs5pgtyyafedpcfjqnupr32qdfu63x6odql4id:9940/p2p/12D3KooWCPcfhr6e7V7NHoKWRxZ5zPRr6v5hGrVPhHdsftQk2DXW
    ```

    每个条目必须以`dnsaddr=`开头并包括`/p2p/peer id`后缀，如上所示。

3. 通过`dig`和`swap`测试验证DNS条目是否正常运作

    `dig +short txt _dnsaddr.DOMAIN.NAME` should return similar output to the below:

    ```bash
    dig +short txt _dnsaddr.swap.sethforprivacy.com
    "dnsaddr=/ip4/5.9.120.18/tcp/9939/p2p/12D3KooWCPcfhr6e7V7NHoKWRxZ5zPRr6v5hGrVPhHdsftQk2DXW"
    "dnsaddr=/ip4/5.9.120.18/tcp/9940/ws/p2p/12D3KooWCPcfhr6e7V7NHoKWRxZ5zPRr6v5hGrVPhHdsftQk2DXW"
    "dnsaddr=/onion3/b4wfknratwn6rcpvpczs5pgtyyafedpcfjqnupr32qdfu63x6odql4id:9939/p2p/12D3KooWCPcfhr6e7V7NHoKWRxZ5zPRr6v5hGrVPhHdsftQk2DXW"
    "dnsaddr=/onion3/b4wfknratwn6rcpvpczs5pgtyyafedpcfjqnupr32qdfu63x6odql4id:9940/p2p/12D3KooWCPcfhr6e7V7NHoKWRxZ5zPRr6v5hGrVPhHdsftQk2DXW"
    ```

    针对您注册的会合点进行测试并验证ASB显示为在线：

    ```bash
    ./swap list-sellers --rendezvous-point /dnsaddr/swap.sethforprivacy.com/p2p/12D3KooWCULyZKuV9YEkb6BX8FuwajdvktSzmMg4U5ZX2uYZjHeu
    ```

# 免责声明

虽然这个软件对我来说运行良好并且适用于主网，但它当然不是万无一失的并且仍在积极开发中。交换应该始终以完全交换或双方收回资金结束，但请注意可能存在错误，并且您*非常*早于一般的原子交换。

对于您在交换中遇到有关比特币/门罗币处理时的任何资金损失或问题，我概不负责，但如果您确实遇到问题，我会尽力提供帮助。

# 结论

希望这是一个很好的（相对）简单的指南，可以让你开始为那些想要通过原子交换以无需信任的方式将比特币换成门罗币的人提供门罗币流动性！原子交换是移除交易所信任和消除未来潜在监管力量的重要工具，因此我非常兴奋它们终于成为可能并且运作良好。

如果您有具体问题或需要帮助，请通过[Signal, SimpleX, Threema, or Nostr]({{< ref "/content/about.md#how-to-contact-me" >}})联系.
