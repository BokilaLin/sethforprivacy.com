---
author: Seth For Privacy
comments: false
date: "2021-08-17T10:00:00-04:00"
summary: 期待很久了，但它终于来了。*今天*，你可以直接点对点交换比特币 <> 门罗币，通过Tor，无需托管人或受信任的第3方，无需KYC（了解你的客户）资讯，什么都不需要。
draft: false
hidemeta: false
showToc: true
tags:
- 原子交换
- 比特币
- 门罗币
title: 执行比特币 <> 门罗原子交换
---

# 介绍
期待很久了，但它终于来了。*今天*，你可以直接点对点交换比特币 <> 门罗币，通过Tor，无需托管人或受信任的第3方，无需KYC（了解你的客户）资讯，什么都不需要。本指南将引导您成为XMR买家，或将比特币出售给门罗币提供者。

这是跨链交换的未来，而就在今天成为可能。

原子交换打开了广泛的跨链用例，但关键是它们是无需信任的、不可审查的，并且是完全匿名/假匿名的。

有关原子交换的更多信息，请查看以下链接：

- <https://localmonero.co/knowledge/monero-atomic-swaps>
- <https://www.monerooutreach.org/stories/monero-atomic-swaps.html>
- <https://github.com/comit-network/xmr-btc-swap>
- <https://comit.network/blog/2020/10/06/monero-bitcoin/>

# 工具
对于这个特定的原子交换实作，我们将使用由[COMIT network](https://comit.network/)提供和构建的工具, 可从他们Github存储库的发行版部分获取：

<https://github.com/comit-network/xmr-btc-swap/releases>

我（且希望其他人很快地）正在运行提供门罗币端交换的`asb`工具，因此为了深入了解并购买门罗币，您只需要下载所提供的最新版本的`swap`工具。

我提供的说明将假定您使用的是Linux，但Windows/macOS应该类似。

# 获取工具

1. 下载最新版本的`swap`工具，即`swap_0.11.0_linux x86_64.tar`

    <https://github.com/comit-network/xmr-btc-swap/releases/latest>

2. 提取swap二进制文件
    打开终端机并执行以下命令：

    ```bash
    cd ~/Downloads
    tar xvf swap_0.11.0_Linux_x86_64.tar
    ```

3. 验证二进制文件是否适当地运作

    ```bash
    ./swap --version
    ```

    {{< figure src="/bitcoin-monero-atomic-swaps/version.png" align="center" style="border-radius: 8px;" >}}

# 进行交换(命令列介面)

1. 列出卖家并选择一个（如果您知道要与之交换的端点，则可以跳过此步骤）

    原测试会合点：

    ```bash
    ./swap list-sellers --rendezvous-point /dnsaddr/rendezvous.coblox.tech/p2p/12D3KooWQUt9DkNZxEn2R5ymJzWj15MpG6mTW84kyd8vDaRZi46o
    ```

    我的生产环境会合点：

    ```bash
    ./swap list-sellers --rendezvous-point /dnsaddr/swap.sethforprivacy.com/p2p/12D3KooWCULyZKuV9YEkb6BX8FuwajdvktSzmMg4U5ZX2uYZjHeu
    ```

    其他推荐的会合点：

    ```bash
    /dns4/rendezvous.xmr.radio/tcp/8888/p2p/12D3KooWN3n2MioS515ek6LoUBNwFKxtG2ribRpFkVwJufSr7ro7
    ```

    此命令将列出可用的卖家、他们的最小/最大交易量以及他们提供的当前价格

    {{< figure src="/bitcoin-monero-atomic-swaps/list-sellers.png" align="center" style="border-radius: 8px;" >}}

2. 开始交换（在这里使用我的端点，根据需要替换为偏爱的端点）

    ```bash
    ./swap buy-xmr --receive-address <YOUR MONERO ADDRESS> --change-address <YOUR BITCOIN CHANGE ADDRESS> --seller <SELLER ADDRESS>
    ```

    ***不要忘记用您自己的地址替换门罗币和比特币地址占位符！***

    {{< figure src="/bitcoin-monero-atomic-swaps/buy-xmr.png" align="center" style="border-radius: 8px;" >}}

3. 将比特币存入提供的地址，确保您发送的金额超过最低金额以支付交换交易的费用
4. 观察交换过程

    日志将显示正在发生的步骤，请耐心等待，因为比特币和门罗币方面都需要确认，这可能需要一些时间

5. 获益

# 进行交换（网络用户界面）
1. 前往<https://unstoppableswap.net/>
2. 选择偏爱的交换提供者
3. 在交换提供者设置的最小和最大范围内输入您想要交换的偏爱的比特币或门罗币数量

    {{< figure src="/bitcoin-monero-atomic-swaps/swap_web.png" align="center" style="border-radius: 8px;" >}}

4. 输入您控制的适当门罗币和比特币地址

    {{< figure src="/bitcoin-monero-atomic-swaps/addresses_web.png" align="center" style="border-radius: 8px;" >}}

5. 按照说明打开终端机

    {{< figure src="/bitcoin-monero-atomic-swaps/instructions_web.png" align="center" style="border-radius: 8px;" >}}

6. 将提供的命令复制并粘贴到您的终端机并执行命令

    {{< figure src="/bitcoin-monero-atomic-swaps/cli_web.png" align="center" style="border-radius: 8px;" >}}

7. 将比特币存入提供的地址，确保您发送的金额超过最低金额以支付费用
8. 观察交换过程
    日志将显示正在发生的步骤，请耐心等待，因为比特币和门罗币方面都需要确认，这可能需要一些时间

    {{< figure src="/bitcoin-monero-atomic-swaps/cli_swap_web.png" align="center" style="border-radius: 8px;" >}}

9. 获益

    {{< figure src="/bitcoin-monero-atomic-swaps/success_web.png" align="center" style="border-radius: 8px;" >}}

# 如果交换失败怎么办

重要的是要意识到，如果*在您发送比特币之后*交换失败，您需要确保及时执行以下两个步骤：

1. 尝试恢复交换
    ```bash
    ./swap resume --swap-id <SWAP ID>
    ```

2. 如果恢复失败，等待比特币存款交易72次确认
3. 在比特币存款交易72次确认后取消交换

    ```bash
    ./swap cancel --swap-id <SWAP ID>
    ```

4. 在发布取消交易后且在比特币取消交易72次确认*之前*,立即退款

    ```bash
    ./swap refund --swap-id <SWAP ID>
    ```

如果上述步骤未能正确取消和退款交易，并且您确定您已等待规定的期限，请提交[issue in Github](https://github.com/comit-network/xmr-btc-swap/issues)或联系Matrix(`#comit-monero:matrix.org`)寻求协助*越快越好*.

有关交换协议步骤的更多详细信息，请参见<https://comit.network/blog/2020/10/06/monero-bitcoin/#long-story-short>.

***注意：如果执行取消后72次确认通过，ASB可以选择对没有正确执行的交换进行惩罚，允许他们将比特币作为你没有正确回应的惩罚。请务必在发起取消后的72此确认期内执行退款步骤。***

# 须知

- 价格自动从Kraken收集并定期更新，并在ASB runner设定的市场价格之上增加价差。
- 出于隐私原因，您提供的比特币找零地址应该是一个*未使用*的地址。
- 比特币找零地址将在交换失败的情况下用于将资金返回到您自己的钱包。
- 理想情况下，门罗币接收地址应该是每个交换端点（或每次交换）的子地址。
- 比特币端需要2次确认，门罗币端需要10次确认，因此在交换过程中请耐心等待，让`swap`工具完成它的工作。如果你必须在交换期间停止它，你可以使用 `./swap resume` 功能，但理想的做法是让工具保持打开状态，直到交换完成。
- 有关交换协议和相关步骤的更多信息，请参阅 <https://comit.network/blog/2020/10/06/monero-bitcoin/>。
- 如果您对交换有疑问，请提交 [issue in Github](https://github.com/comit-network/xmr-btc-swap/issues) 或联系Matrix(`#comit-monero:matrix.org`)寻求协助。

# 免责声明

虽然这个软件对我来说运行良好并且适用于主网，但它当然不是万无一失的并且仍在积极开发中。交换应该始终以完全交换或双方收回资金结束，但请注意可能存在错误，并且您*非常*早于一般的原子交换。

对于您在交换中遇到有关比特币/门罗币处理时的任何资金损失或问题，我概不负责，但如果您确实遇到问题，我会尽力提供帮助。

# 结论

希望这是一个很好的简单指南，可以帮助您开始通过原子交换以无需信任的方式将比特币换成门罗币！原子交换是移除交易所信任和消除未来潜在监管力量的重要工具，因此我非常兴奋它们终于成为可能并且运作良好。

如果您有具体问题或需要帮助，请通过[Signal, SimpleX, Threema, or Nostr]({{< ref "/content/about.md#how-to-contact-me" >}})联系.

