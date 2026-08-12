### 1. `request` 事件

- **触发时机**：当客户端发送一个**标准的 HTTP 请求**（如 GET、POST、PUT 等）时触发。
    
- **参数**：`(req, res)`，分别代表 `http.IncomingMessage`（请求对象）和 `http.ServerResponse`（响应对象）。
    
- **典型用途**：处理常规的 Web 请求，返回 HTML、JSON 或文件等内容。`http.createServer()` 的回调函数就是监听这个事件的快捷方式。
    
- **示例**：
    
    js
    
    const server = http.createServer((req, res) => {
      res.end('Hello World');
    });
    // 等同于 server.on('request', (req, res) => {...})
    

### 2. `connect` 事件

- **触发时机**：当客户端发送一个 **HTTP `CONNECT` 方法**请求时触发。`CONNECT` 方法通常用于建立隧道连接，例如通过 HTTP 代理访问 HTTPS 网站。
    
- **参数**：`(req, socket, head)`，其中 `req` 是请求对象，`socket` 是客户端与服务器之间的网络套接字（`net.Socket`），`head` 是一个 Buffer，可能包含隧道建立后收到的第一个数据包。
    
- **典型用途**：实现代理服务器。服务器收到 `CONNECT` 请求后，需要与目标主机建立 TCP 连接，然后让客户端和目标主机直接通过该套接字通信，完成 TLS 握手等操作。
    
- **示例**（简单代理片段）：
    
    js
    
    server.on('connect', (req, clientSocket, head) => {
      const { port, hostname } = new URL(`http://${req.url}`);
      const serverSocket = net.connect(port, hostname, () => {
        clientSocket.write('HTTP/1.1 200 Connection Established\r\n\r\n');
        serverSocket.write(head);
        // 双向管道：客户端 <-> 目标服务器
        clientSocket.pipe(serverSocket).pipe(clientSocket);
      });
    });
    

### 总结

- **`request`**：处理普通 HTTP 请求，用于构建 Web 应用或 API。
    
- **`connect`**：处理 `CONNECT` 方法，用于代理服务器中建立隧道，转发 TCP 流量（常见于 HTTPS 代理）。
    

除了这两个事件，`http.Server` 还有 `upgrade` 事件（处理 WebSocket 升级）和 `clientError` 等，需要根据场景分别监听。