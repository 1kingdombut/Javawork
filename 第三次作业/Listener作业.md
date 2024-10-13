
# 文档说明
实现一个 ServletRequestListener，记录 HTTP 请求的详细信息，包括：
请求时间
客户端 IP 地址
请求方法（GET, POST 等）
请求 URI
查询字符串（如果有）
User-Agent
请求处理时间
在请求开始时记录开始时间，并在请求结束时计算处理时间。
使用适当的日志格式，确保日志易于阅读和分析。
实现一个简单的测试 Servlet，验证日志记录功能。
日志信息应在网页的控制台上输出，以便开发者快速调试和查看请求的详细信息。

# 实验步骤
1.创建 RequestLoggingListener 类：
2.实现 ServletRequestListener 接口。
3.在 requestInitialized 方法中记录请求的详细信息，并将其存储为请求的属性。
创建测试 Servlet TestLoggingServlet：
4.在 Servlet 中获取存储的日志信息。
将日志信息通过 JavaScript 输出到浏览器的控制台。

## RequestLoggingListener类

```java
import jakarta.servlet.ServletRequestEvent;
import jakarta.servlet.ServletRequestListener;
import jakarta.servlet.annotation.WebListener;
import jakarta.servlet.http.HttpServletRequest;

import java.time.LocalDateTime;

@WebListener
public class RequestLoggingListener implements ServletRequestListener {

    @Override
    public void requestInitialized(ServletRequestEvent sre) {
        HttpServletRequest request = (HttpServletRequest) sre.getServletRequest();

        // 记录请求的详细信息
        String logInfo = String.format("Request Initialized at %s - Method: %s, URI: %s, Client IP: %s, User-Agent: %s",
                LocalDateTime.now(),
                request.getMethod(),
                request.getRequestURI(),
                request.getRemoteAddr(),
                request.getHeader("User-Agent"));

        // 将日志信息存储为请求的属性，以便后续使用
        request.setAttribute("logInfo", logInfo);
    }

    @Override
    public void requestDestroyed(ServletRequestEvent sre) {
        // 在请求结束时可以添加额外的处理逻辑
    }
}
```
## TestLoggingServlet类

```java
package org.listenerwork.listenerwork;


import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.IOException;

@WebServlet("/testLogging")
public class TestLoggingServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // 获取日志信息
        String logInfo = (String) request.getAttribute("logInfo");

        // 设置响应内容类型为 HTML
        response.setContentType("text/html");
        response.setCharacterEncoding("UTF-8");

        // 输出 HTML 内容和 JavaScript，将日志信息发送到控制台
        response.getWriter().write("<html><body>");
        response.getWriter().write("<h1>Check the browser console for log details</h1>");
        response.getWriter().write("<script>");
        response.getWriter().write("console.log('Server Log: " + logInfo + "');");
        response.getWriter().write("</script>");
        response.getWriter().write("</body></html>");
    }
}

```
## 运行结果
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/c77839e84213447cba5f06b8d8a7761a.png#pic_center)

