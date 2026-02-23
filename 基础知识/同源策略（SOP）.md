# 同源策略（SOP）

同源策略限制了不同源之间如何进行资源交互，是用于隔离潜在恶意文件的重要安全机制。 是否同源由URL决定，URL由协议、域名、端口和路径组成，如果两个URL的协议、域名和端口相同，则表示他们同源。



同源策略控制了不同源之间的交互，这些交互通常分为三类：

- **通常允许跨域写操作(Cross-origin writes)**

- 链接(links)
- 重定向
- 表单提交

- **通常允许跨域资源嵌入(Cross-origin embedding)**
- 通常不允许跨域读操作(Cross-origin reads)

参考：

- https://websec.readthedocs.io/zh/latest/vuln/xss/sop.html