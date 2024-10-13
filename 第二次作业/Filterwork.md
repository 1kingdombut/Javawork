学院：省级示范性软件学院

题目：《作业二：Filter练习》

姓名：石松林

学号：2200770159

班级：软工2201

日期：2024-10-01
# 文档说明
## 实验要求
题目: 实现一个登录验证过滤器
目标: 创建一个 Servlet的 过滤器,用于验证用户是否已登录。对于未登录的用户,将其重定向到登录页面。
要求: 
1. 创建一个名为 LoginFilter 的类, 实现 javax.servlet.Filter 接口。
2. 使用 @WebFilter 注解配置过滤器,使其应用于所有 URL 路径 ("/*")。
3. 在 doFilter 方法中实现以下逻辑: 
  a. 检查当前请求是否是对登录页面、注册页面或公共资源的请求。如果是,则允许请求通过。 
  b. 如果不是上述情况,检查用户的 session 中是否存在表示已登录的属性(如 "user" 属性)。
  c. 如果用户已登录,允许请求继续。 
  d. 如果用户未登录,将请求重定向到登录页面。
4. 创建一个排除列表,包含不需要登录就能访问的路径(如 "/login", "/register", "/public")。
5. 实现一个方法来检查当前请求路径是否在排除列表中。
6. 添加适当的注释,解释代码的主要部分。

## 主要组件
### LoginFilter类实现逻辑
使用 @WebFilter 注解配置过滤器，适用于所有 URL 路径。
在 doFilter 方法中实现以下逻辑：
检查请求是否为登录页面、注册页面或公共资源，如果是，则允许请求通过。
检查用户 session 中是否存在已登录的属性（如 "user"）。
如果用户已登录，允许请求继续。
如果用户未登录，重定向到登录页面。
#### LoginFilter类

```java
package ssl.javawork;

import jakarta.servlet.*;
import jakarta.servlet.annotation.WebFilter;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import jakarta.servlet.http.HttpSession;

import java.io.IOException;
import java.util.Arrays;
import java.util.List;

@WebFilter(urlPatterns = "/*")
public class LoginFilter implements Filter {

    private static final List<String> excludedPaths = Arrays.asList( "/login", "/register", "/public","/login.html");

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        HttpServletRequest httpRequest = (HttpServletRequest) servletRequest;
        HttpServletResponse httpResponse = (HttpServletResponse) servletResponse;
        String path = httpRequest.getServletPath();


        // 如果请求路径是排除的路径，继续请求
        if (isExcludedPath(path)) {
            filterChain.doFilter(servletRequest, servletResponse);
            return;
        }

        // 获取用户的 session
        HttpSession session = httpRequest.getSession(false);
        // 检查用户是否已登录
        if (session != null && session.getAttribute("user") != null) {
            filterChain.doFilter(servletRequest, servletResponse);
        } else {
            httpResponse.sendRedirect(httpRequest.getContextPath() + "/login.html");
        }
        System.out.println("Requested Path: " + path);


    }

    private boolean isExcludedPath(String path) {
        return excludedPaths.stream().anyMatch(path::startsWith);
    }

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        Filter.super.init(filterConfig);
    }

    @Override
    public void destroy() {
        Filter.super.destroy();
        // 可选的清理代码
    }
}

```

#### LoginServlet类

```java
package ssl.javawork;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import jakarta.servlet.http.HttpSession;

import java.io.IOException;
@WebServlet("/login")
public class LoginServlet extends HttpServlet    {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {

        // 从请求中获取用户名和密码
        String username = request.getParameter("username");
        String password = request.getParameter("password");

        // 简单的验证逻辑，可以替换成数据库验证
        if ("ssl".equals(username) && "123456".equals(password)) {
            HttpSession session = request.getSession();
            session.setAttribute("user", username);

            // 登录成功，重定向到欢迎页面
            response.sendRedirect("welcome.html");
        } else {
            // 登录失败，重定向到登录页面并显示错误信息
            request.setAttribute("errorMessage", "用户名或密码错误");
            request.getRequestDispatcher("login.html").forward(request, response);
        }
    }
}

```

