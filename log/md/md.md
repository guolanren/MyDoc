# Md —— 开发日志

## 概述

开发日志。

## 2019

------

### 08

------

#### 404 找不到视图 (2019-08-02)

**BUG :**

​	**Could not resolve view with name 'forward:/oauth/confirm_access' in servlet with name 'dispatcherServlet'**

**Caused :**

​	关闭 **OAuth2** 客户端的 **autoApprove** ，用户登录后会跳转到授权批准页。由于程序中有继承 **WebMvcConfigurationSupport** 的配置类，导致 **Spring Boot** 的自动配置失效(使用 **@EnableWebMvc** 同样失效)

**Resolve :**

​	手动注册视图解析器(**ViewResolver**)，资源处理器(**ResourceHandler**)，资源位置(**ResourceLocations**)

```java
@Configuration
public class WebMvcConfiguration extends WebMvcConfigurationSupport {

    @Override
    protected void configureViewResolvers(ViewResolverRegistry registry) {
        registry.viewResolver(new InternalResourceViewResolver());
    }
    
    @Override
    protected void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/css/**")
            	.addResourceLocation("classpth:/static/css/");
        
        registry.addResourceHandler("/js/**")
            	.addResourceLocation("classpth:/static/js/");
    }    
}
```

------

#### Token Json Serializer (2019-08-01 ~ 2019-08-02)

**BUG :**

1. 循环重定向 **/oauth/authorize**
2. **access token provider returned a null access token**

**Caused :**

​	由于客户端一直获取不到 **token** 导致循环重定向至端点 **/oauth/authorize** 尝试获取 **token**。客户端使用 **RestTemplate** 通过端点 **/oauth/token** 获取 **OAuth2AccessToken**。

```java
return getRestTemplate().execute(getAccessTokenUri(resource, form), getHttpMethod(),
					getRequestCallback(resource, form, headers), extractor , form.toSingleValueMap());
```

​	**OAuth2AccessToken** 含有大量的 **jackson** 注解。使用 **fastjson** 替换框架默认的 **jackson** 进行序列化/反序列化时，导致 **OAuth2AccessToken** 序列化的结果不同。

```java
package org.springframework.security.oauth2.common;

...
    
@org.codehaus.jackson.map.annotate.JsonSerialize(using = OAuth2AccessTokenJackson1Serializer.class)
@org.codehaus.jackson.map.annotate.JsonDeserialize(using = OAuth2AccessTokenJackson1Deserializer.class)
@com.fasterxml.jackson.databind.annotation.JsonSerialize(using = OAuth2AccessTokenJackson2Serializer.class)
@com.fasterxml.jackson.databind.annotation.JsonDeserialize(using = OAuth2AccessTokenJackson2Deserializer.class)
public interface OAuth2AccessToken {
	public static String BEARER_TYPE = "Bearer";
	public static String OAUTH2_TYPE = "OAuth2";
	public static String ACCESS_TOKEN = "access_token";
	public static String TOKEN_TYPE = "token_type";
	public static String EXPIRES_IN = "expires_in";
	public static String REFRESH_TOKEN = "refresh_token";
	public static String SCOPE = "scope";
...
}
```

**Resolve :**

​	添加一个序列化过滤器[[1]](<https://github.com/alibaba/fastjson/issues/1640>)，针对 **OAuth2AccessToken** 进行特殊处理

```java
public class OAuthTokenSerializeFilter extends BeforeFilter implements PropertyPreFilter {
    @Override
    public void writeBefore(Object object) {
        if (object instanceof DefaultOAuth2AccessToken) {
            DefaultOAuth2AccessToken accessToken = (DefaultOAuth2AccessToken) object;
            if (accessToken.getValue() != null) {
                writeKeyValue("access_token", accessToken.getValue());
            }
            if (accessToken.getTokenType() != null) {
                writeKeyValue("token_type", accessToken.getTokenType());
            }
            if (accessToken.getRefreshToken() != null && accessToken.getRefreshToken().getValue != null) {
                writeKeyValue("refresh_token", accessToken.getRefreshToken().getValue());
            }
            writeKeyValue("expires_in", accessToken.getExpiresIn());
            if (accessToken.getScope() != null) {
                writeKeyValue("scope", String.join(" ", accessToken.getScope()));
            }
        }
    }
}

@Configuration
public class JsonConfiguration {
    @Bean
    public HttpMessageConverter httpMessageConverter() {
        FastJsonHttpMessageConverter fastJsonHttpMessageConverter = new FastJsonHttpMessageConverter();
        
        // 自定义配置...
        FastJsonConfig fastJsonConfig = new FastJsonConfig();
        fastJsonConfig.setSerializeFilters(new OAuthTokenSerializeFilter());
        
        fastJsonHttpMessageConverter.setFastJsonConfig(fastJsonConfig);
        
        // 高版本 fastjson 默认 Content-Type '*/*'
        // java.lang.IllegalArgumentException: 'Content-Type' cannot contain wildcard tyoe '*'
        List<MediaType> supportedMediaTypes = new ArrayList<>();
        supportedMediaTypes.add(MediaType.APPLICATION_JSON_UTF8);
        fastJsonHttpMessageConverter.setSupportedMediaType(supportedMediaTypes);
        
        return fastJsonHttpMessageConverter;
    }
}
```

------

#### 跳出过滤器链 (2019-08-28)

​	**OAuth2** 客户端对请求白名单做放行，使用一个优先级高于所有 **OAuth2** 相关过滤器的过滤器 **WhiteFilter**。

```java
@Component
@Order(Ordered.HIGHEST_PRECEDENCE + 1)
@WebFilter(filterName = "whiteFilter", urlPattern = "/*")
public class WhiteFilter implements Filter {
    
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        HttpServletRequest request = (HttpServletRequest) servletRequest;
        if (condition) {
            // 满足条件，跳出过滤器链，直接转发请求
            request.getRequestDispatcher(request.getServletPath()).forward(request, servletResponse);
        } else {
            // 不满足条件，执行下一个过滤器
            filterChain.doFilter(request, servletResponse);
        }
    }
    
}
```

------

#### Bean Scope is not active (2019-08-29)

**Bug :**

​	**error create bean with name 'scopedTarget.oauth2ClientContext': Scope 'session' is not active**

**Cause :**

​	使用 **@EnableOAuth2Sso** 时，引入 **OAuth2** 的配置。**Bean** **oauth2ClientContext** 在 **OAuth2ClientConfiguration** 中定义如下:

```java
package org.springframework.security.oauth2.config.annotation.web.configuration;
...
@Configuration
public class OAuth2ClientConfiguration {
    ...
	@Configuration
	protected static class OAuth2ClientContextConfiguration {
		...
         // 指定了 Bean 的 Scope 为 session
         // Scope 可取值: singleton、prototype、request、session、globalSession
         // 其中 request、session、globalSession 只适用于 web 应用
		@Bean
		@Scope(value = "session", proxyMode = ScopedProxyMode.INTERFACES)
		public OAuth2ClientContext oauth2ClientContext() {
			return new DefaultOAuth2ClientContext(accessTokenRequest);
		}
	}
}
```

​	在引入 **spring-boot-starter-web** 依赖后，**WebMvcAutoConfiguration **会做大量的自动配置。包括支持 **web** 应用的额外3种 **scope**。

```java
@Configuration
@ConditionalOnWebApplication(
    type = Type.SERVLET
)
@ConditionalOnClass({Servlet.class, DispatcherServlet.class, WebMvcConfigurer.class})
// 当存在 WebMvcConfigurationSupport Bean 的时候，不使用该自动配置
@ConditionalOnMissingBean({WebMvcConfigurationSupport.class})
@AutoConfigureOrder(-2147483638)
@AutoConfigureAfter({DispatcherServletAutoConfiguration.class, TaskExecutionAutoConfiguration.class, ValidationAutoConfiguration.class})
public class WebMvcAutoConfiguration {

    @Configuration
    @Import({WebMvcAutoConfiguration.EnableWebMvcConfiguration.class})
    @EnableConfigurationProperties({WebMvcProperties.class, ResourceProperties.class})
    @Order(0)
    public static class WebMvcAutoConfigurationAdapter implements WebMvcConfigurer, ResourceLoaderAware {
        ...
        @Bean
        // 当存在 RequestContextListener、RequestContextFilter Bean 的时候，不定义该 Bean
        @ConditionalOnMissingBean({RequestContextListener.class, RequestContextFilter.class})
        @ConditionalOnMissingFilterBean({RequestContextFilter.class})
        public static RequestContextFilter requestContextFilter() {
            return new OrderedRequestContextFilter();
        }
        ...
    }
}
```

​	如源码注释所述，应用中为了使用 **fastjson** 替换 **jackson**，确实定义了一个继承 **WebMvcConfigurationSupport** **Configuration** 以添加 **fastjson** 的 **HttpMessageConverter**。也就是说，现在并不支持 **session** 等 **http** 相关 **scope** **Bean** 的创建。

> `DispatcherServlet`, `RequestContextListener` and `RequestContextFilter` all do exactly the same thing, namely bind the HTTP request object to the Thread that is servicing that request. This makes beans that are request- and session-scoped available further down the call chain. [[2]](<https://stackoverflow.com/questions/21286675/scope-session-is-not-active-for-the-current-thread-illegalstateexception-no>)

**Resolve :**

​	定义 **RequestContextListener** 或者 **RequestContextFilter** **Bean**

```java
// 2选1
@Bean
public RequestContextListener requestContextListener() {
    return new RequestContextListener();
}

@Bean
public RequestContextFilter requestContextFilter() {
    return new RequestContextFilter();
}
```

------

#### Spring Security POST 403 (2019-08-29)

**Bug :**

```json
{
    "timestamp": "2019-08-29T07:31:02.500+0000",
    "status": 403,
    "error": "Forbidden",
    "message": "Forbidden",
    "path": "/post"
}
```

**Cause :**

​	**Spring Security** 默认开启 **CSRF** 保护，所以除了 **GET** 方法之外，其它的请求都需要携带 **CSRF Token** [[3]](<https://spring.io/blog/2013/08/21/spring-security-3-2-0-rc1-highlights-csrf-protection/>)。

**Resolve :**

​	1. 关闭 **CSRF** 保护

```java
@EnableWebSecurity
@Configuration
public class WebSecurityConfig extends
   WebSecurityConfigurerAdapter {

  @Override
  protected void configure(HttpSecurity http) throws Exception {
    http
      .csrf().disable()
      ...;
  }
}
```

​	2. 使用除 **GET** 之外的请求，携带 **CSRF Token**

**Thymeleaf** 表单 :

```html
<c:url var="logoutUrl" value="/logout"/>
<form action="${logoutUrl}"
    method="post">
  <input type="submit"
    value="Log out" />
  <input type="hidden"
    name="${_csrf.parameterName}"
    value="${_csrf.token}"/>
</form>
```

**Ajax** 请求 :

```html
<html>
  <head>
    <meta name="_csrf" content="${_csrf.token}"/>
    <!-- default header name is X-CSRF-TOKEN -->
    <meta name="_csrf_header" content="${_csrf.headerName}"/>
    ...
  </head>
  ...
</html>
```

```js
$(function () {
    var token = $("meta[name='_csrf']").attr("content");
    var header = $("meta[name='_csrf_header']").attr("content");
    $(document).ajaxSend(function(e, xhr, options) {
        xhr.setRequestHeader(header, token);
    });
});
```

------

### 09

------

#### 未认证 Ajax 302 (2019-09-03)

**Bug :**

​	前端未认证调用接口，页面不跳转到登录页。

**Cause :**

​	**Ajxa** 请求 **application/json** 资源，返回 **302** 。浏览器处理最终返回 **200** **text/html** 登录页。**Ajax** 无法处理。	

**Resolve :**

​	在未认证的前提下请求受保护资源，返回 **200** ，并提供 表示跳转 的 自定义状态码，以及即将要跳转的 **url**。

```java
// OAuth2 客户端代码
@Configuration
@EnableOAuth2Sso
@EnableGlobalMethodSecurity(prePostEnabled = true, proxyTargetClass = true)
public class WebSecurityConfiguration extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            	.requestMatchers()
            		.antMatchers("/**")
            		.and()
            	.authorizeRequests()
            		.anyRequest().authenticated()
            		.and()
            	.exceptionHandling()
            		.authenticationEntryPoint(new UnauthorizedEntryPoint());
    }
    
    private class UnauthorizedEntryPoint implements AuthenticationEntryPoint {
        @Override
        public void commence(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, AuthenticationException e) throws IOException, ServletException {
            ObjectNode ret = new ObjectMapper().createObjectNode();
            ret.put("code", 302);
            ret.put("msg", "redirect");
            ret.put("url", "http://localhost:8080/login");
            
            httpServletResponse.setStatus(HttpStatus.OK.value());
            httpServletResponse.getWriter().print(ret);
        }
    }
}
```

------

## 参考

[1\]  [使用 fastjson 时， spring security oauth2 获取 token 格式变化 @FelixFly](<https://github.com/alibaba/fastjson/issues/1640>)

[2\]  [Scope 'session' is not active for the current thread; IllegalStateException: No thread-bound request found @Jon、@Martin Strejc](<https://stackoverflow.com/questions/21286675/scope-session-is-not-active-for-the-current-thread-illegalstateexception-no>)

[3\]  [Spring Security 3.2.0.RC1 Highlights: CSRF Protection](<https://spring.io/blog/2013/08/21/spring-security-3-2-0-rc1-highlights-csrf-protection/>)