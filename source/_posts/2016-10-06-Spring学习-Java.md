---
layout: post
date: 2016-10-06
title: Spring学习
categories: Java
---

代码在这里 https://github.com/Rayooo/SpringLearning/tree/master/demo2

路由
---

@Controller和@RequestMapping

@Controller标注的类表示是一个处理HTTP请求的控制器(即MVC中的C)，该类中所有被@RequestMapping标注的方法都会用来处理对应URL的请求。
在Spring MVC框架中，使用@RequestMapping标注可以将URL与处理方法绑定起来，例如：

```java
	@Controller
	public class IndexController {
	    
	    @RequestMapping("/")
	    @ResponseBody
	    public String index() {
	        return "index";
	    }

	    @RequestMapping("/hello")
	    @ResponseBody
	    public String hello() {
	        return "hello";
	    }
	}
```
IndexController类被@Controller标注，其中的两个方法都被@RequestMapping标注，当应用程序运行后，在浏览器中访问http://localhost:8080/ ,请求会被Spring MVC框架分发到index()方法进行处理。同理，http://localhost:8080/hello 会交给hello()方法进行处理。

@ResponseBody标注表示处理函数直接将函数的返回值传回到浏览器端显示。

@RequestMapping标注同样可以加在类上：create()绑定的URL路径是/blogs/create。

```java
	@Controller
	@RequestMapping("/blogs")
	public class AppController {

	    @RequestMapping("/create")
	    @ResponseBody
	    public String create() {
	        return "mapping url is /blogs/create";
	    }
	}
```
还有4种标注@PutMapping，@GetMapping，@PostMapping，@DeleteMapping

模版
---

将页面的HTML代码写在模板文件中，然后读取该文件并返回。Spring天然支持这种非常常见的场景，需要先在pom.xml引入Thymeleaf依赖

```xml
	<dependency>
	  <groupId>org.springframework.boot</groupId>
	  <artifactId>spring-boot-starter-thymeleaf</artifactId>
	</dependency>
```

将HTML文本保存在src/main/resources/templates/blog.html下

```html
	<html>
	  <head>
	    <title>Title</title>
	  </head>
	  <body>
	    <h1>This is title</h1>
	    <p> This is Content</h2>
	  </body>
	</html>
```

@Controller中去掉@ResponseBody标注，并将URL处理函数的返回值设为刚刚保存在templates/文件夹中的文件名（不需要扩展名）

```java
	@GetMapping("/blogs")
	public String blog() {
	    return "blog";
	}
```

Spring框架在发现返回值是"blog"后，就会去src/main/resources/templates/目录下读取blog.html文件并将它的内容返回给浏览器,为了保证Thymeleaf能够正确的识别HTML5，还需要添加Maven依赖

```xml
	<dependency>
	  <groupId>net.sourceforge.nekohtml</groupId>
	  <artifactId>nekohtml</artifactId>
	  <version>1.9.22</version>
	</dependency>
```

PathVariable
---

我们可以在@RequestMapping注解里用{}来表明它的变量部分，当@Controller处理HTTP请求时，userProfile的参数username会被自动设置为URL中的对应变量username（同名赋值）的值,例如:

```java
	//username同名
	@GetMapping("/users/{username}")
	public String userProfile(@PathVariable String username) {
	    return String.format("user %s", username);
	}

	//username不同名
	@GetMapping("/users/{username}")
	public String userProfile(@PathVariable("username") String user) {
	    return String.format("user %s", user);
	}

	//可以定义URL路由，其中包含多个URL变量：
	@GetMapping("/users/{username}/blogs/{blogId}")
	public String getUserBlog(@PathVariable String username, @PathVariable int blogId) {

	}

	//除了简单地定义{username}变量，还可以定义正则表达式进行更精确的控制，定义语法是：{变量名:正则表达式}
	@GetMapping("/users/{username:[a-z0-9_]+}")
```

RequestParam
---

在Spring MVC框架中，现在我们已经可以通过定义@RequestMapping来处理URL请求了，和@PathVariable一样，我们也需要在处理URL的函数中获取URL中的参数——也就是?key1=value1&key2=value2这样的参数列表。通过注解@RequestParam可以轻松的将URL中的参数绑定到处理函数方法的变量中：

```java
	@RestController
	public class EditPetForm {

	    @GetMapping("/blogs")
	    public String setupForm(@RequestParam("id") int blogId) {
	        return String.format("blog id = %d", blogId);
	    }

	}
	//可以设置默认值，是否必须
	@RequestParam(name="id", required=false, defaultValue="0")
```

模版
---

基于Thymeleaf提供的标签，我们可以将之前编写的静态HTML内容改写为动态的,保存在templates/blog.html下：

```html
	<!-- 使用Thymeleaf的标签，为了避免在IDE中出现错误提示，可以在HTML文件中增加命名空间的定义： -->
	<!-- <html xmlns="http://www.w3.org/1999/xhtml"
      			xmlns:th="http://www.thymeleaf.org">
    			...
		</html> -->
	<html xmlns="http://www.w3.org/1999/xhtml"
	      xmlns:th="http://www.thymeleaf.org">
	<div class="col-sm-8">
	    <div class="page-header">
	        <h2 th:text="${title}">Cum sociis（博客标题）</h2>
	        <p class="blog-post-meta"><span th:text="${createdTime}">2015年2月3日</span> 标签：<a href="#">Web开发</a></p>
	    </div>

	    <div class="blog-post-content" th:text="${content}">
	        ...
	        （这里是博客内容）
	    </div>
	</div>
	</html>
```

为了让模板引擎知道这些变量的值，我们需要在@Controller做一些工作：

```java
	import org.springframework.ui.Model;

    @RequestMapping("/blogs/{id}")
    public String blogs(@PathVariable("id")long id, Model model){
        model.addAttribute("title","This is a blog with id "+id);
        Date date = new Date();
        model.addAttribute("createdTime", date.getTime());
        model.addAttribute("content","AAAA");
        return "blog";
    }
```

在上面的代码中，index()方法增加了一个Model类型的参数。通过Spring MVC框架提供的Model，可以调用其addAttribute方法，这样Thymeleaf可以访问Model中的变量从而进行模板渲染。上述例子中可以看到，title变量的值是根据URL中的@PathVariable来确定的，虽然简单，但是这已经是一个动态页面了。

在Servlet编程中，如果希望在页面中动态渲染信息，一般需要往HttpRequest中添加属性，然后在JSP中获取。其实Model的属性实际上也是放在HttpRequest的属性中，但是Spring MVC提供了更高层的抽象，帮你屏蔽了HttpRequest，你看到的只有直接以MVC中M（即Model）。

如果你依然希望希望HttpRequest、HttpResponse和HttpSession等原生的Servlet API对象，往Controller方法中增加对应类型的参数即可

也可以使用blog类

```java
	public class Blog {
	    private Long id;
	    private String title;
	    private String content;
	    private Date createdTime;
	    //Getter/Setter方法略
	}

	@RequestMapping("/blogs/{id}")
	public String blog(@PathVariable("id") int id, Model model) {
		Blog blog = blogService.findBlog(id);
	    model.addAttribute("blog", blog);
	    return "item";
	}
```

```html
	<div class="col-sm-8">
	  <div class="page-header">
	    <h2 th:text="${blog.title}">Cum sociis（博客标题）</h2>
	    <p class="blog-post-meta"><span th:text="${blog.createdTime}">2015年2月3日</span> 标签：<a href="#">Web开发</a></p>
	  </div>

	  <div class="blog-post-content" th:text="${blog.content}">
	    ...
	    （这里是博客内容）
	  </div>
	</div>
```

重定向
---
在Spring MVC中可以这样处理Redirect：
```java
	@PostMapping("/create")
    public String create(@RequestParam("title") String title,
                         @RequestParam("content") String content) {
        System.out.print(title);
        System.out.print(content);
        //重定向redirect:
        return "redirect:/blogs/"+title;
    }
```

拦截器Interceptor
---
Spring MVC中拦截器是实现了HandlerInterceptor接口的Bean：

创建Interceptor类

preHandle()：预处理回调方法，若方法返回值为true，请求继续（调用下一个拦截器或处理器方法）；若方法返回值为false，请求处理流程中断，不会继续调用其他的拦截器或处理器方法，此时需要通过response产生响应；

postHandle()：后处理回调方法，实现处理器的后处理（但在渲染视图之前），此时可以通过ModelAndView对模型数据进行处理或对视图进行处理

afterCompletion()：整个请求处理完毕回调方法，即在视图渲染完毕时调用

HandlerInterceptor有三个方法需要实现，但大部分时候可能只需要实现其中的一个方法，HandlerInterceptorAdapter是一个实现了HandlerInterceptor的抽象类，它的三个实现方法都为空实现（或者返回true），继承该抽象类后可以仅仅实现其中的一个方法：

```java
	import org.springframework.web.servlet.HandlerInterceptor;
	import org.springframework.web.servlet.ModelAndView;
	import javax.servlet.http.HttpServletRequest;
	import javax.servlet.http.HttpServletResponse;

	public class Interceptor implements HandlerInterceptor {
	    public boolean preHandle(HttpServletRequest request,
	                             HttpServletResponse response,
	                             Object handler) throws Exception {
	        // 在controller方法调用前打印信息
	        System.out.println("This is interceptor.");
	        // 返回true，将强求继续传递（传递到下一个拦截器，没有其它拦截器了，则传递给Controller）
	        return true;
	    }

	    @Override
	    public void afterCompletion(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e) throws Exception {

	    }

	    @Override
	    public void postHandle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, ModelAndView modelAndView) throws Exception {

	    }
	}

```
定义Interceptor后，需要创建WebMvcConfigurerAdapter在MVC配置中将它们应用于特定的URL中。一般一个拦截器都是拦截特定的某一部分请求，这些请求通过URL模型来指定。

创建WebConfig类
```java
	import org.springframework.context.annotation.Configuration;
	import org.springframework.web.servlet.config.annotation.InterceptorRegistration;
	import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
	import org.springframework.web.servlet.config.annotation.WebMvcConfigurerAdapter;
	import org.springframework.web.servlet.i18n.LocaleChangeInterceptor;

	@Configuration
	public class WebConfig extends WebMvcConfigurerAdapter {

	    @Override
	    public void addInterceptors(InterceptorRegistry registry){
	        registry.addInterceptor(new Interceptor()).addPathPatterns("/**");
	    }
	}
```

异常处理
---

抛出异常
```java
	@RequestMapping(value = "/test", method = RequestMethod.GET)
	public String testException() {
	    throw new RuntimeException("this is a test exception");
	}
```

访问http://localhost:8080/test  会触发错误

在src/main/resources/static目录下放置一个error目录，里头存放各种错误码对应的页面：

	src/
	 +- main/
	     +- java/
	     |   + <source code>
	     +- resources/
	         +- templates/
	             +- error/
	             |   +- 404.html
	             +- <other public assets>

 框架会帮助我们自动建立{StatusCode}=>{StatusCode}.html的映射，那么在目录结构完成后，网站的所有404错误，都会将这个错误页面返回到浏览器。

