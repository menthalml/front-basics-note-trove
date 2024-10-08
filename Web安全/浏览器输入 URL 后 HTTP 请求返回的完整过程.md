##### 浏览器输入 URL 后 HTTP 请求返回的完整过程

[![pAVmHPS.png](https://s21.ax1x.com/2024/09/02/pAVmHPS.png)](https://imgse.com/i/pAVmHPS)

- **重定向**

  `当浏览器中输入URL并按下回车时，浏览器会先检查是否需要重定向。这可能是之前访问该URL时，浏览器曾经返回过永久重定向的响应，浏览器已将此信息缓存下来`

  这一步检查的目的是确定是否需要将请求指定指向另一个 URL，这与安全性和永久迁移有关。这一步发生在发送请求之前，它决定了请求最终会发送到哪个 URL，如果需要重定向，会自动跳转到新的 URL，具体流程如下：

  - 检查 HSTS 策略

    `HSTS是一种安全机制，告诉浏览器应该强制通过HTTPS访问某个域名，当访问一个使用HSTS的站点时，服务器会在响应头中包含了一个Strict- Transport-Security的指令，这个指令指定了浏览器在一定时间内应该直接使用HTTPS访问该站点，而不再尝试通过HTTP访问。浏览器会缓存这个指令，因此下次输入这个站点的URL时，会自动重定向`

    > 1. 浏览器缓存：浏览器维护一个 HSTS 缓存列表，这个列表包含了所有启用 HSTS 的域名，该缓存在浏览器启动时加载，并且在运行时可以更新。
    > 2. 请求之前检查：如果输入的 URL 域名在 HSTS 缓存中存在，浏览器会自动将 HTTP 请求
    > 3. 不在缓存中的域名：如果域名不在 HSTS 缓存中，浏览器会发起初始的 HTTP 请求，并且服务器会返回 Strict- Transport-Security 头部来启用 HSTS，随后，浏览器会将这个域名添加到 HSTS 缓存中，并在将来的请求中自动使用 HTTPS

  - 检查内部重定向缓存

    `在请求之前，浏览器还会检查本地的重定向缓存`

    | 状态码                 | 解释                                                     |
    | ---------------------- | -------------------------------------------------------- |
    | 301 Moved Permanently  | 永久重定向                                               |
    | 302 Found              | 临时重定向                                               |
    | 307 Temporary Redirect | 临时重定向，类似 302，但要求重定向保持 HTTP 方法         |
    | 308 Permanent Redirect | 永久重定向，类似于 301，但要求重定向保持相同的 HTTP 方法 |

- **应用缓存**

  `在浏览器发送请求之前，会检查是否有缓存的资源,包括HTTP缓存，Server Worker缓存等。如果缓存中有效的资源且符合条件，则会直接使用缓存的内容，而不再请求服务器`

  [![pAVnwRg.webp](https://s21.ax1x.com/2024/09/02/pAVnwRg.webp)](https://imgse.com/i/pAVnwRg)

  区别于第一步，这一步主要是为了减少网络请求，加快页面加载速度，通过使用已缓存的资源。

  > 重定向检查和应用缓存的区别
  >
  > 1.  重定向检查的目的是确实是否需要将请求指向另一个 URL，这与安全性和永久迁移有关。应用缓存检查的对是减少网络请求，加快页面加载速度，通过使用已缓存的资源
  > 2.  重定向检查发生在发送请求之前，它决定了请求最终会发送到哪个 URL，如果需要重定向，浏览器会直接跳转到新的 URL。应用缓存检查发送在请求目标 URL 确定之后，在这个阶段，浏览器检查是否已经有缓存的资源与请求的目标匹配
  > 3.  涉及的缓存机制不同，重定向检查涉及 H STS 缓存，重定向缓存等。应用缓存检查涉及 HTTP 缓存，Server Worker 缓存等

- **DNS 解析**

  `由于输入的URL是域名，浏览器需要将域名解析成IP地址，以便与服务器建立连接。浏览器首先会查询本地DNS缓存，如果缓存中没有该域名的记录，则会向DNS服务器发送请求，获取域名对应的IP地址。DNS解析的结果将被缓存一段时间，以加快后续的解析速度`

  [![pAVn0zQ.webp](https://s21.ax1x.com/2024/09/02/pAVn0zQ.webp)](https://imgse.com/i/pAVn0zQ)

- **创建 TCP 连接**

  `建立IP地址后，浏览器会与服务器建立TCP连接，TCP连接的建立过程需要经过三次握手，如果使用HTTPS,需要额外的TLS/SSL握手，以确保数据传输的安全性`

  [![TCP四层传输模型](https://s21.ax1x.com/2024/09/02/pAVucXd.webp)](https://imgse.com/i/pAVucXd)

  [![pAVuW7t.webp](https://s21.ax1x.com/2024/09/02/pAVuW7t.webp)](https://imgse.com/i/pAVuW7t)

- **发送 HTTP 请求**

  `TCP连接建立后，浏览器会向服务器发送HTTP请求，这个请求包含了各种信息，如请求方法，请求头，请求体，服务器接收后，会根据请求的内容进行处理，如读取数据看，执行计算等`

  [![pAVu2nA.webp](https://s21.ax1x.com/2024/09/02/pAVu2nA.webp)](https://imgse.com/i/pAVu2nA)

- **服务器处理并响应**

  `服务器处理完请求后，会返回HTTP响应，其中包含了响应状态码，响应头，以及响应体，浏览器接收到响应后，开始解析并渲染内容`

- **渲染页面**

https://juejin.cn/post/6990187326747967519
