## JSTL和EL表达式

### 主要内容

- EL表达式介绍及作用
- EL表达式案例
- JSTL介绍及开发准备
- JSTL+EL表达式案例
- JSTL常用标签

#### EL表达式介绍

- EL表达式介绍及作用

Expression Language（表达式语言）,目的是替代JSP页面中的复杂代码

- EL表达式语法：${变量名}

从1.jsp提交数据到showEL.jsp:ELServlet实现数据传递转发页面

**1.jsp**

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>表单提交</title>
</head>
<body>
<form action="/EL/ELServlet" method="post">
    用户名：<input type="text" name="username"><br>
    年龄：<input type="text" name="age"><br>
    <input type="submit" value="提交">
</form>
</body>
</html>
```

**ELServlet:**

```java
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet("/ELServlet")
public class ELServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //1。获取username和age属性值
        String username=request.getParameter("username");
        String age=request.getParameter("age");

        //2。将获取的值保存到request域中
        request.setAttribute("username",username);
        request.setAttribute("age",age);

        //3。跳转到showEL.jsp页面，我们通过EL表达式取出request 中的数据
        request.getRequestDispatcher("/showEL.jsp").forward(request,response);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doGet(req,resp);
    }
}
```

**showEL.jsp**

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>通过EL表达式取数据</title>
</head>
<body>
姓名:${username}<br>
年龄:${age}
</body>
</html>
```

#### JSTL

- JSTL介绍
  - JSP标准标签库（JavaServerPages Standard Tag Library）
- 与EL表达式关系
  - JSTL通常会与EL表达式合作实现JSP页面的编码
- JSTL开发准备
  - 在JSP页面添加taglib指令:<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>

#### 为什么要使用JSTL标签

• 在**jsp中不建议直接书写Java代码(规范性问题)**

• EL表达式虽然可以解决“不用书写java代码”问题，但是对于 复杂的数据(如数组、集合等)取值会很麻烦

• 使用JSTL标签配合EL表达式能够很好的解决取复杂类型数据的问题，**简化代码书写**



**使用JSTL要导入jar包**



#### JSTL常用标签介绍

• 通用标签：set、out、remove

• 条件标签：if、choose

• 迭代标签：forEach

##### 通用标签

- set标签：将值保存到指定范围里
  - 将value值存储到范围为scope的变量variable中
  - <c:set var="username" value=" 张三 " scope=" scope "/>
- out标签:将结果输出显式
  - <c:out value="value" />
- remove标签：删除指定域内数据
  - <c:remove var=“username" scope="session"/>

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html >
<head>
    <title>jstl</title>
</head>
<body>
<%--set,out,remove--%>
<%--将user为ann的数据存在request域中--%>
<%--set标签主要是往指定的域中存放数据--%>

<c:set var="user" value="ann" scope="request"></c:set>
<hr>
<%--将数据ann打印显示--%>
<c:out value="${user}"></c:out>

<%--remove标签，删除request标签域的内容--%>
<c:remove var="user" scope="request"></c:remove>

<%--删除后结果显示--%>
<c:out value="${user}"></c:out>
</body>
</html>
```

**条件标签：**

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
<head>
    <title>if-choose</title>
</head>
<body>
<%--set标签存放数据--%>
<c:set var="age" value="12" scope="request"></c:set>
<%--if标签

test:接判断的条件，如果条件为true，则执行标签体中的内容

--%>
<c:if test="${age==12}"><!--如果满足条件age=12,则打印显示年龄-->
    age=12
</c:if>
hello！
<hr>
<%--choose标签类似if-else--%>
<c:choose>
    <c:when test="${age==12}">
        age=12
    </c:when> <!--if-->
    <c:otherwise>
        age!=12
    </c:otherwise> <!--else-->
</c:choose>
</body>
    
</html>
```

**迭代标签：**

案例：显示表格及其内容

JSTLELServlet:

```java
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

@WebServlet("/JSTLELServlet")
public class JSTLELServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //1。创建list，向list集合中存放多条数据
        Map<String,Object> dataMap1=new HashMap<>();
        dataMap1.put("shopName","联想笔记本");
        dataMap1.put("address","北京");
        dataMap1.put("price",4999.99);

        Map<String,Object> datamap2=new HashMap<String,Object>();
        datamap2.put("shopName", "神州笔记本");
        datamap2.put("address", "南京");
        datamap2.put("price", 3999.99);

        List<Map<String,Object>> list=new ArrayList<>();
        list.add(dataMap1);
        list.add(datamap2);
        //2。将存放多条数据的list集合保存到 request 域中
        req.setAttribute("list",list);
        //3。在JSTLEL.jsp中取出request域中存放的list集合
        req.getRequestDispatcher("JSTLEL.jsp").forward(req,resp);
  }


    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doGet(req,resp);
    }
}
```

```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>JSTL,EL迭代</title>
</head>
<body>
<%--
<c:forEach items="${list}" var="Map">
        ${Map.shopName}:${Map.address}:${Map.price}<br>
    </c:forEach>
    <%--for{数据类型 变量名：集合或者数组}--%>
--%>
<table border="1px" cellspacing="1px" align="center">
    <tr>
        <td>商品名称</td>
        <td>产地</td>
        <td>价格</td>
    </tr>
    <c:forEach items="${list}" var="Map">
    <tr>
           <td>${Map.shopName}</td> 
           <td>${Map.address}</td>
           <td>${Map.price}</td>
    </tr>
    </c:forEach>
    <%--for{数据类型 变量名：集合或者数组}--%>
</body>
</table>

</html>
```

#### 总结

- EL表达式的语法有两个要素：$ 和 {}

- EL表达式可以使用“.”或者“[]”操作符在相应的作用域中取得某个 属性的值

- JSTL核心标签库中常用的标签有如下三类。

  ```java
  通用标签；<c:set>、<c:out>、<c:remove>
  
  条件标签；<c:if>、<c:choose>、<c:when>、 <c:otherwise>
  
  迭代标签：<c:forEach>
  ```

- EL表达式与JSTL标签结合使用，可以减少JSP中嵌入的Java代码，有利于程序的维护和扩展
