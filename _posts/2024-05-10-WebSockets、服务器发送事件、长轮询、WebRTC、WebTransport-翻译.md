---
title: WebSockets、服务器发送事件、长轮询、WebRTC、WebTransport[译]
categories:
  - 翻译
tags:
  - 技术文档翻译
  - 消息获取
  - WebSockets
  - SSE
  - 长轮询
  - WebRTC
  - WebTransport
date: 2024-05-10 10:53:09
---

---

对于现代实时 Web 应用程序来说，将事件从服务器发送到客户端的能力是必不可少的。多年来，这种必要性导致了多种方法的发展，每种方法都有自己的优点和缺点。最初，长轮询是唯一可用的选择。随后 WebSockets 继承了它，为双向通信提供了更强大的解决方案。继 WebSocket 之后，服务器发送事件 (SSE) 提供了一种更简单的方法，用于从服务器到客户端的单向通信。展望未来，WebTransport 协议有望通过提供更高效、灵活和可扩展的方法来进一步彻底改变这一格局。对于利基用例[^1]，WebRTC 也可能被考虑用于服务器-客户端事件。

本文旨在深入研究这些技术，比较它们的性能，强调它们的优点和局限性，并为各种用例提供建议，以帮助开发人员在构建实时 Web 应用程序时做出明智的决策。这是我在实现 RxDB 复制协议以兼容各种后端技术时收集的经验的浓缩总结。

# 什么是长轮询？​

长轮询是第一个启用服务器-客户端消息传递方法的“妙招”，该方法可以通过 HTTP 在浏览器中使用。该技术通过正常的 XHR[^2] 请求模拟服务器推送通信。与传统轮询（客户端定期从服务器重复请求数据）不同，长轮询会建立与服务器的连接，该连接在新数据可用之前保持打开状态。一旦服务器有新的信息，它就会向客户端发送响应，并关闭连接。客户端收到服务器的响应后立即发起新的请求，如此循环往复。此方法允许更即时的数据更新并减少不必要的网络流量和服务器负载。但是，它仍然会导致通信延迟，并且效率低于 WebSocket 等其他实时技术。

```javascript
// JavaScript客户端中的长轮询
function longPoll() {
  fetch("http://example.com/poll")
    .then((response) => response.json())
    .then((data) => {
      console.log("Received data:", data);
      longPoll(); // 立即建立一个新的长轮询请求
    })
    .catch((error) => {
      /**
       * 在正常情况下，当连接超时或客户端离线时可能会出现错误。出现错误时，我们只需在一段延迟之后重新启动轮询。
       */
      setTimeout(longPoll, 10000);
    });
}
longPoll(); // 发起长轮询
```

在客户端实现长轮询非常简单，如上面的代码所示。然而，在后端，要确保客户端接收所有事件并且在客户端当前重新连接时不会错过更新，可能存在多种困难。

<!-- more -->

# 什么是 WebSocket？​

WebSockets 通过客户端和服务器之间的单个长连接提供全双工通信通道。该技术使浏览器和服务器能够交换数据，而无需 HTTP 请求响应周期的开销，从而促进实时聊天、游戏或金融交易平台等应用程序的实时数据传输。 WebSocket 相对于传统 HTTP 来说是一个重大进步，它允许双方在建立连接后独立发送数据，非常适合需要低延迟和高频更新的场景。

```js
//  JavaScript 客户端中的 WebSocket
const socket = new WebSocket("ws://example.com");

socket.onopen = function (event) {
  console.log("Connection established");
  // 向服务器发送消息
  socket.send("Hello Server!");
};

socket.onmessage = function (event) {
  console.log("Message from server:", event.data);
};
```

虽然 WebSocket API 的基础知识很容易使用，但在生产中却显得相当复杂。套接字可能会断开连接，并且必须相应地重新创建。特别是检测连接是否仍然可用，可能非常棘手。大多数情况下，您会添加乒乓心跳[^3]以确保打开的连接不会关闭。这种复杂性就是为什么大多数人使用 WebSocket 之上的库（例如 [Socket.IO](https://socket.io/)）来处理所有这些情况，甚至在需要时提供长轮询的回退。

# 什么是服务器发送事件？​

服务器发送事件 (SSE) 提供了一种通过 HTTP 将服务器更新推送到客户端的标准方法。与 WebSocket 不同，SSE 专为从服务器到客户端的单向通信而设计，这使得它们非常适合实时新闻提要、体育比分或客户端需要实时更新而不向服务器发送数据的任何情况。

您可以将 Server-Sent-Events 视为单个 HTTP 请求，后端不会立即发送整个请求体，而是保持连接打开，并通过每次需要向客户端发送事件时发送单个行来逐步传输数据。

使用 SSE 创建接收事件连接非常简单。在浏览器中的客户端侧，使用服务器端生成事件的脚本的 URL 初始化一个 [EventSource](https://developer.mozilla.org/en-US/docs/Web/API/EventSource) 实例。

侦听消息涉及将事件处理程序直接附加到 EventSource 实例。 该 API 区分通用消息事件和具名事件，从而实现更加结构化的通信。以下是在 JavaScript 中进行设置的方法：

```js
// 连接到服务器端的事件流
const evtSource = new EventSource("https://example.com/events");

// 处理通用消息事件
evtSource.onmessage = (event) => {
  console.log("got message: " + event.data);
};
```

与 WebSocket 不同，EventSource 将在连接丢失时自动重新连接。

在服务器端，您的脚本必须将 `Content-Type` 标头设置为 `text/event-stream` 并根据 [SSE 规范](https://www.w3.org/TR/2012/WD-eventsource-20120426/)格式化每条消息。这包括指定事件类型、数据有效负载以及事件 ID 和重试计时等可选字段。

以下是如何在 Node.js Express 应用程序中设置简单的 SSE 端点：

```js
import express from "express";
const app = express();
const PORT = process.env.PORT || 3000;

app.get("/events", (req, res) => {
  res.writeHead(200, {
    "Content-Type": "text/event-stream",
    "Cache-Control": "no-cache",
    Connection: "keep-alive",
  });

  const sendEvent = (data) => {
    // 所有消息行都必须以 "data:" 为前缀
    const formattedData = `data: ${JSON.stringify(data)}\n\n`;
    res.write(formattedData);
  };

  // 每 2 秒发送一个事件
  const intervalId = setInterval(() => {
    const message = {
      time: new Date().toTimeString(),
      message: "Hello from the server!",
    };
    sendEvent(message);
  }, 2000);

  // 关闭连接时清理定时器
  req.on("close", () => {
    clearInterval(intervalId);
    res.end();
  });
});
app.listen(PORT, () =>
  console.log(`Server running on http://localhost:${PORT}`)
);
```

# 什么是 WebTransport API？​

WebTransport 是一种前沿 API，旨在实现 Web 客户端和服务器之间高效、低延迟的通信。它利用 [HTTP/3 QUIC 协议](https://en.wikipedia.org/wiki/HTTP/3)来实现各种数据传输功能，例如以可靠和不可靠的方式通过多个流发送数据，甚至允许乱序发送数据。这使得 WebTransport 成为需要高性能网络的应用程序的强大工具，例如实时游戏、直播和协作平台。然而，值得注意的是，WebTransport 目前只是一个工作草案，尚未得到广泛采用。截至目前（2024 年 3 月），WebTransport 仍处于[工作草案](https://w3c.github.io/webtransport/)中，尚未得到广泛支持。您还无法在 [Safari 浏览器](https://caniuse.com/webtransport)中使用 WebTransport，并且 [Node.js](https://github.com/w3c/webtransport/issues/511) 中也没有本机支持。这限制了它在不同平台和环境中的可用性。

即使 WebTransport 将得到广泛支持，其 API 使用起来也非常复杂，并且人们很可能会在 WebTransport 之上构建库，而不是直接在应用程序的源代码中使用它。

# 什么是 WebRTC？​

[WebRTC](https://webrtc.org/)（Web 实时通信）是一个开源项目和 API 标准，可直接在 Web 浏览器和移动应用程序中实现实时通信 (RTC) 功能，无需复杂的服务器基础设施或安装其他插件。它支持点对点连接，以便在浏览器之间传输音频、视频和数据交换。 WebRTC 旨在通过 NAT 和防火墙工作，利用 ICE、STUN 和 TURN 等协议在对等点之间建立连接。

虽然 WebRTC 旨在用于客户端与客户端的交互，但它也可以用于服务器与客户端的通信，其中服务器可以模拟为客户端。这种方法仅对利基用例有意义，这就是为什么在下面的 WebRTC 将作为一个选项被忽略的原因。

问题是，要使 WebRTC 工作，您无论如何都需要一个信令服务器，然后该服务器将再次在 websockets、SSE 或 WebTransport 上运行。这违背了使用 WebRTC 替代这些技术的目的。

# 技术的局限性 ​

## 双向发送数据 ​

只有 WebSocket 和 WebTransport 允许双向发送数据，以便您可以通过同一连接接收服务器数据和发送客户端数据。

虽然理论上长轮询也是可能的，但不建议这样做，因为将 “新” 数据发送到现有的长轮询连接无论如何都需要执行额外的 http 请求。因此，您可以使用额外的 http 请求将数据直接从客户端发送到服务器，而无需中断长轮询连接。

Server-Sent-Events 不支持向服务器发送任何附加数据。您只能执行初始请求，即使在初始请求中，默认情况下也无法使用本机 [EventSource API](https://developer.mozilla.org/en-US/docs/Web/API/EventSource) 在 http-body 中发送类似 POST 的数据。相反，您必须将所有数据放入 url 参数中，这被认为是一种不好的安全做法，因为用户的身份验证信息可能会泄漏到服务器日志、代理和缓存中。为了解决这个问题，[RxDB](https://rxdb.info/) 使用 [eventsource polyfill](https://github.com/EventSource/eventsource) 而不是原生的 `EventSource API` 。该库添加了其他功能，例如发送`自定义 http 标头`。微软还有[一个库](https://github.com/Azure/fetch-event-source)，它允许发送正文数据并使用 `POST` 请求而不是 `GET` 。

## 每个域限制 6 个请求 ​

大多数现代浏览器允许每个域有六个连接，这限制了所有稳定的服务器到客户端消息传递方法的可用性。六个连接的限制甚至在浏览器选项卡之间共享，因此当您在多个选项卡中打开同一页面时，它们必须彼此共享六个连接池。此限制是 HTTP/1.1-RFC 的一部分（其甚至定义了仅两个连接的较低数量）。

{% note default %}
引自 [RFC 2616 – 第 8.1.4 节](https://www.w3.org/Protocols/rfc2616/rfc2616-sec8.html#sec8.1.4)：“使用持久连接的客户端应该限制它们与给定服务器保持的同时连接的数量。单用户客户端不应与任何服务器或代理保持超过 2 个连接。代理应该使用最多 2\*N 个连接到另一个服务器或代理，其中 N 是同时活动用户的数量。这些准则旨在缩短 HTTP 响应时间并避免拥塞。”
{% endnote %}

虽然该策略对于防止网站所有者使用 D-DOS 访问其他网站来说是有意义的，但当需要多个连接来处理合法用例的服务器客户端通信时，这可能是一个大问题。要解决此限制，您必须使用 HTTP/2 或 HTTP/3，浏览器将仅为每个域打开一个连接，然后使用多路复用通过单个连接传输所有数据。虽然这为您提供了几乎无限数量的并行连接，但有一个 [SETTINGS_MAX_CONCURRENT_STREAMS](https://www.rfc-editor.org/rfc/rfc7540#section-6.5.2) 设置限制了实际连接数量。对于大多数配置，默认值为 100 个并发流。

理论上，浏览器也可以增加连接限制，至少对于像 EventSource 这样的特定 API 来说是这样，但这些问题已被 [chromium](https://issues.chromium.org/issues/40329530) 和 [firefox](https://bugzilla.mozilla.org/show_bug.cgi?id=906896) 标记为 “无法修复”。

{% note default %}
减少浏览器应用程序中的连接量

当您构建浏览器应用程序时，您必须假设您的用户不仅会使用该应用程序一次，而且还会在多个浏览器选项卡中并行使用该应用程序。默认情况下，您可能会为每个选项卡打开一个服务器流连接，但这通常根本没有必要。相反，无论打开多少个选项卡，您都只打开一个连接并在选项卡之间共享它。 [RxDB](https://rxdb.info/) 通过[broadcast-channel npm 包](https://github.com/pubkey/broadcast-channel) 中的 [LeaderElection](https://rxdb.info/leader-election.html) 来实现这一点，以便在服务器和客户端之间只有一个复制流。您可以将该包独立使用（无需 RxDB）于任何类型的应用程序。
{% endnote %}

## 移动应用程序上的连接不会保持打开状态 ​

在 Android 和 iOS 等操作系统上运行的移动应用程序中，维护开放连接（例如用于 WebSocket 等的连接）带来了重大挑战。移动操作系统旨在在一段时间不活动后自动将应用程序移至后台，从而有效地关闭任何打开的连接。此行为是操作系统资源管理策略的一部分，旨在节省电池并优化性能。因此，开发人员通常依赖**移动推送通知**作为将数据从服务器发送到客户端的有效且可靠的方法。推送通知允许服务器向应用程序发出新数据报警，提示操作或更新，而无需持续打开连接。

## 代理和防火墙

通过咨询许多 [RxDB](https://rxdb.info/) 用户，结果表明，在企业环境（也称为 “工作”）中，通常很难在基础设施中实现 WebSocket 服务器，因为许多代理和防火墙会阻止非 HTTP 连接。因此，使用服务器发送事件提供了更简单的企业集成方式。此外，长轮询仅使用普通 HTTP 请求，或许也是一个选择。

# 性能比较

比较 WebSocket、服务器发送事件 (SSE)、长轮询和 WebTransport 的性能涉及评估各种条件下的关键方面，例如延迟、吞吐量、服务器负载和可扩展性。

首先让我们看一下原始数据。在[此存储库](https://github.com/Sh3b0/realtime-web?tab=readme-ov-file#demos)中可以找到良好的性能比较，该存储库测试了 [Go Lang](https://go.dev/) 服务器实现的消息时间。这里我们可以看到 WebSockets、WebRTC 和 WebTransport 的性能是相当的：

{% asset_img 1.png %}

{% note default %}
注意

请记住，WebTransport 是一项基于新的 HTTP/3 协议的全新技术。未来（2024 年 3 月之后）可能会有更多性能优化。此外，WebTransport 还经过优化，可以使用更少的功率，但该指标尚未测试。
{% endnote %}

我们还可以比较延迟、吞吐量和可扩展性：

## 延迟：

- **WebSockets**：由于其通过单个持久连接进行全双工通信，因此可提供最低的延迟。非常适合即时数据交换至关重要的实时应用程序。
- **服务器发送事件(SSE)**：也为服务器到客户端的通信提供低延迟，但如果没有额外的 HTTP 请求，则无法将客户端消息发送回服务器。
- **长轮询**：由于每次数据传输都依赖于建立新的 HTTP 连接，因此会产生较高的延迟，从而降低实时更新的效率。当服务器想要发送事件，而客户端仍在打开新连接的过程中时。在这种情况下，延迟会明显变大。
- **WebTransport**：承诺提供类似于 WebSocket 的低延迟，并具有利用 HTTP/3 协议实现更高效的多路复用和拥塞控制的额外优势。

## 吞吐量

- **WebSockets**：由于其持久连接而能够实现高吞吐量，但吞吐量可能会受到[背压](https://chromestatus.com/feature/5189728691290112)[^4]的影响，因为客户端处理数据的速度无法与服务器发送数据的速度一样快。
- **服务器发送事件(SSE)**：能够有效地将消息广播到许多客户端，并且开销比 WebSocket 更少，从而可能提高单向服务器到客户端通信的吞吐量。
- **长轮询**：由于频繁打开和关闭连接的开销，通常会提供较低的吞吐量，这会消耗更多的服务器资源。
- **WebTransport**：预计在单个连接内支持单向和双向流的高吞吐量，在需要多个流的场景中优于 WebSocket。

## 可扩展性和服务器负载

- **WebSockets**：维护大量 WebSocket 连接会显著增加服务器负载，可能会影响具有大量用户的应用程序的可扩展性。
- **服务器发送事件(SSE)**：在主要需要从服务器到客户端进行更新的场景中，更具可扩展性。与 WebSockets 相比，SSE 使用的连接开销较少，因为它使用的是 “普通” HTTP 请求，而不需要像 WebSockets 那样进行协议升级等操作。
- **长轮询**：由于频繁建立连接会产生高服务器负载，因此可扩展性最差，因此仅适合作为后备机制。
- **WebTransport**：旨在具有高度可扩展性，受益于 HTTP/3 在处理连接和流方面的高效性，与 WebSocket 和 SSE 相比，可能会减少服务器负载。

# 建议和用例适用性

在服务器 - 客户端通信技术领域，每种技术都有其独特的优势和用例适用性。**服务器发送事件 (SSE)** 成为最直接的实施选项，利用与传统 Web 请求相同的 HTTP/S 协议，从而规避企业防火墙限制和其他协议可能出现的其他技术问题。它可以轻松集成到 Node.js 和其他服务器框架中，使其成为需要频繁服务器到客户端更新的应用程序的理想选择，例如新闻订阅、股票行情和实时事件流。

另一方面，**WebSocket** 在需要持续双向通信的场景中表现出色。它支持持续交互的能力使其成为浏览器游戏、聊天应用程序和体育直播更新的首选。

然而，尽管 **WebTransport** 具有潜力，但采用它仍面临挑战。它没有得到[包括 Node.js](https://github.com/w3c/webtransport/issues/511) 在内的服务器框架的广泛支持，并且缺乏与 [safari](https://caniuse.com/webtransport) 的兼容性。此外，它对 HTTP/3 的依赖进一步限制了它的直接适用性，因为许多 Web 服务器（如 nginx）仅具有[实验性](https://nginx.org/en/docs/quic.html)的 HTTP/3 支持。虽然 WebTransport 支持可靠和不可靠的数据传输，有望为未来的应用带来希望，但对于大多数用例来说，WebTransport 还不是一个可行的选择。

**长轮询**曾经是一种常见技术，但由于其效率低下以及重复建立新 HTTP 连接的高开销，现在基本上已经过时了。尽管它可以作为缺乏 WebSockets 或 SSE 支持的环境中的后备方案，但由于显着的性能限制，通常不鼓励使用它。

# 已知问题

对于所有实时流技术，都存在已知的问题。当你在它们之上构建任何东西时，请记住以下这些问题。

## 客户端重新连接时可能会错过事件

当客户端正在连接、重新连接或离线时，它可能会错过服务器上发生但无法流式传输到客户端的事件。无论如何，当服务器每次都流式传输完整内容时（例如实时更新股票行情），这种错过的事件并不相关。但是，当后端流式传输部分结果时，您必须考虑错过的事件。在后端修复这个问题非常糟糕，因为后端必须记住每个客户端哪些事件已经成功发送。相反，这应该使用客户端逻辑来实现。

例如，[RxDB 复制协议](https://rxdb.info/replication.html)为此使用了两种操作模式。一种是[检查点迭代模式](https://rxdb.info/replication.html#checkpoint-iteration)，其中使用普通的 http 请求来迭代后端数据，直到客户端再次同步。然后它可以切换到[事件观察模式](https://rxdb.info/replication.html#event-observation)，其中来自实时流的更新用于保持客户端同步。每当客户端断开连接或出现任何错误时，复制都会立即切换到[检查点迭代模式](https://rxdb.info/replication.html#checkpoint-iteration)，直到客户端再次同步。此方法可以解决错过的事件，并确保客户端始终可以同步到服务器的完全相同的状态。

## 公司防火墙可能会导致问题 ​

使用任何流媒体技术时，公司基础设施都存在许多已知问题。代理和防火墙可能会阻止流量或无意中中断请求和响应。每当您在此类基础设施中实现实时应用程序时，请确保首先测试该技术本身是否适合您。

# 查看更多

- 查看[本文的 hackernews 讨论](https://news.ycombinator.com/item?id=39745993)
- 分享 / 点赞我的 [X] 推文(https://twitter.com/rxdbjs/status/1769507055298064818)
- 了解如何使用 Server-Sent-Events [通过后端复制客户端 RxDB 数据库](https://rxdb.info/replication-http.html#pullstream-for-ongoing-changes)。
- 通过 [RxDB 快速入门](https://rxdb.info/quickstart.html)了解如何使用 RxDB
- 查看 [RxDB github 存储库](https://github.com/pubkey/rxdb)并留下一颗星 ⭐

# 参考文档

[WebSockets vs Server-Sent-Events vs Long-Polling vs WebRTC vs WebTransport](https://rxdb.info/articles/websockets-sse-polling-webrtc-webtransport.html)
本文是以上链接的中文翻译，感谢原文作者。更多信息请点击以上链接获取。

[^1]: “利基”是指用例或应用程序的专门或特定子集。它表明，WebRTC 可能不常用于一般 Web 开发场景中的服务器-客户端事件，但在其功能与应用程序的要求非常吻合的特定、专门情况下可能会发现特别有用或相关性。因此，这里的“利基”意味着更狭窄、更集中的使用领域，而不是广泛或主流的使用领域。（来自 ChatGPT）
[^2]: XML Http Request
[^3]: [Pings and Pongs: The Heartbeat of WebSockets](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API/Writing_WebSocket_servers#pings_and_pongs_the_heartbeat_of_websockets)握手后的任何时刻，客户端或服务器都可以选择向对方发送 ping。收到 ping 后，接收者必须尽快发回 pong。例如，您可以使用它来确保客户端仍然处于连接状态。
[^4]: 在计算机科学和网络通信中，backpressure 是一种流量控制机制，指的是当数据接收方（客户端）处理数据的速度低于发送方（服务器）发送数据的速度时，系统会采取措施减慢数据的发送速度，以防止接收方被数据流压垮。具体来说，backpressure 旨在避免数据溢出和资源耗尽，确保系统稳定性和数据传输效率。
