# SpringSecurity

[SpringSecurity详解 (ngui.cc)](https://www.ngui.cc/51cto/show-528708.html?action=onClick)

## 一、SpringSecurity概述

### 1.1 什么是SpringSecurity

​		Spring Security 是基于 Spring 的安全框架。它提供全面的安全性解决方案，同时在 Web 请求级和方法调用级处理身份认证和授权。在 Spring Framework 基础 上，Spring Security 充分利用了依赖注入（DI）和面向切面编程（AOP）功能，为 应用系统提供声明式的安全访问控制功能，减少了为企业系统安全控制编写大量重复 代码的工作。是一个轻量级的安全框架。它与 Spring MVC 有很好地集成。



### 1.2 SpringSecurity核心功能

- `认证(Authentication)`（你是谁？认证 用户/设备/系统 的身份）

- `验证(Authorization)`（你能干什么（授权）？也叫权限控制/授权，允许执行的操作）



### 1.3 SpringSecurity原理

​		基于 Filter、Servlet、AOP 实现`身份认证`和`权限验证`。

|                        各组件架构示意                        |
| :----------------------------------------------------------: |
| ![image-20230214093942143](imgs\image-20230214093942143.png) |



## 二、SpringSecurity入门使用案例

### 2.1 使用SpringSecurity默认生成的用户

- 依赖引入

![SpringSecurity依赖](imgs\SpringSecurity依赖.png)



- 创建一个简单的Controller控制器类接收用户请求

  ```java
  @RestController
  public class SimpleFacadeController {
      @GetMapping("/hello")
      public String responseHelloWorld(){
          return "HelloSpringSecurity！";
      }
  }
  ```

  

- 启动项目，框架会默认生成一个用户

```java
// 秘钥在服务启动时自动生成，并在项目日志中打印
Using generated security password: 356ef67f-3c60-494d-96ef-f51078191389
    
用户名默认为：user 
```



### 2.2 使用在配置文件中自定义的用户

> 使用配置文件创建用户。其它操作同[2.1](#2.1-使用SpringSecurity默认生成的用户)

- 自定义用户

  ```yml
  spring:
    security: # 自定义SpringSecurity项目中的用户
      user:
        name: rainlu
        password: 666666
  ```

  

- 访问服务，会出现登录界面：登录成功则进入系统操作……

  ![认证登录](imgs\认证登录.png)



- 根据实际需求关闭SpringSecurity的安全认证

```java
// 即排除 Security 的配置，让它不启用
@SpringBootApplication(exclude = {SecurityAutoConfiguration.class})
public class SpringSecutityApplication {
    public static void main(String[] args) {
        SpringApplication.run(SpringSecutityApplication.class, args);
    }
}
```



### 2.3 Configure方法说明

> 继承WebSecurityConfigurerAdapter，重写三个configure方法。
>
> 这三个configure方法功能各有千秋。

```java
//开启安全认证
@EnableWebSecurity
//开启方法级别的安全验证
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class AppSpringSecurity extends WebSecurityConfigurerAdapter {
 
    //configure(AuthenticationManagerBuilder)用于通过允许AuthenticationProvider容易地添加来建立认证机制, 也就是说用来记录账号，密码，角色信息。
    /*
    	AuthenticationManager：认证的核心接口
    	AuthenticationManagerBuilder：用于构建AuthenticationManager对象的工具
    	ProviderManager：AuthenticationManager接口的默认实现类
    */
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        super.configure(auth);
    }
 
    //configure(HttpSecurity)允许基于选择匹配在资源级配置基于网络的安全性。
    //也就是对角色的权限——所能访问的路径做出限制
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        super.configure(http);
    }
   
 
    //configure(WebSecurity)用于影响全局安全性(配置资源，设置调试模式，通过实现自定义防火墙定义拒绝请求)的配置设置。
    //一般用于配置全局的某些通用事物，例如静态资源等
    @Override
    public void configure(WebSecurity web) throws Exception {
                //排除resources/assets/下的静态资源  
                web.ignoring().antMatchers("/assets/**");
               //super.configure(web);
    }
}
```

> - 重写configure(WebSecurity security)方法作用：
>
> ​		不会进入SpringSecurity框架进行解析和处理（即使你在原有的过滤器链上添加了crsf、xss等漏洞处理器）。所以，如果将非静态资源路径配置到了此方法中，那么他人极有可能通过此链接来入侵你的项目。
>
> ```java
> @Override
> public void configure(WebSecurity security) {
>     security.ignoring()
>         .antMatchers("/**/*.html")
>         .antMatchers("/**/*.htm")
>         .antMatchers("/**/*.js")
>         .antMatchers("/**/*.png")
>         .antMatchers("/**/*.jpg")
>         .antMatchers("/favicon.ico")
>         .antMatchers("/**/*.css")
>         .antMatchers("/images/**");
> }
> ```
> 
>
> 
>- 重写configure(HttpSecurity http)方法作用：
> 
>  ​		如果没有重写此configure方法，那么将默认使用父类中的设置。此方法中主要配置你的登录和权限控制、以及配置过滤器链。在此方法中亦可配置资源请求的权限忽略（即__不需要登录也可访问，但会经过SpringSecurity的过滤器链__）。
> 
>  
> 
>  
> 
>- 总结：
> 
>  ​	__public修饰的configure和protected修饰的configure方法：__
>   共同点：都可以配置后台权限可以忽略的请求
>   区别：
>   ​	1、public修饰的configure：
>   ​			经常用来处理__<font color="blue">静态资源</font>__的忽略，因为此方法中配置的请求不会经过SpringSecurity的框架及自定义添加的过滤器链。容易被攻击。
>   ​	2、protected修饰的configure：
>   ​			用于处理__<font color="blue">动态资源</font>__的忽略，因为此方法中配置的请求将经过spring security的框架及自定义添加的过滤器链，只是不经过登录验证。可以保证动态资源的请求不容易被他人攻击，更加安全。



## 三、SpringSecurity中认证接口和类的使用

### 3.1 在内存（配置类）中自定义用户

​		 使用内存中的用户信息——使用__配置类__创建用户。

 ​		使用configure(AuthenticationManagerBuilder auth)的方法参数AuthenticationManagerBuilder 来创建对象。



- 定义配置类

> 关键注解说明：
>
> __<font color="red">@EnableWebSecurity：</font>__启用 SpringSecurity 安全框架的功能。
>
> ​		__<font color="#3d6fe3">必须！但如果只使用这个注解标注配置类，则表示只要通过进入系统前的认证，就可以操作系统中的任何接口（方法）。</font>__
>
> 
>
> __<font color="red">@EnableGlobalMethodSecurity(prePostEnabled = true)：</font>__启用方法级别的安全认证。
>
> ​		__<font color="#3d6fe3">非必须！如果想将权限的粒度进一步进行缩小至每个方法，就加上此注解。</font>__
>
> - __<font color="red">prePostEnabled</font>__默认是false；当设置为true时：**表示可以使用@PreAuthorize("hasAnyRole('<font color="#fd7439">自定义角色名</font>')")注解 和 @PostAuthorize("hasAnyRole('<font color="#fd7439">自定义角色名</font>')")注解 **标注方法。表示__给方法进行角色权限控制。__
>
>   
>
> __<font color="green">不用加@Configuration注解的原因是@EnableWebSecurity是一个复合注解，并包含了@Configuration</font>__

```java
@EnableWebSecurity
@EnableGlobalMethodSecurity (prePostEnabled = true)
public class MyWebSecurityConfig extends WebSecurityConfigurerAdapter {

    @Resource
    private PasswordEncoder passwordEncoder;

    // 重写configure方法：在configure方法中定义用户信息
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception { 
        /*
    		在role中的字符串所定义的角色，完全是自定义的！根据业务需求指定即可！
    		误区提醒：admin也只是个普通角色而已。
    	*/
         auth.inMemoryAuthentication()
	             // 设置用户名
                .withUser("rainlu")
	             // 设置密码，并给密码加密
                .password(passwordEncoder.encode("123456"))
             	// 基于角色 role 的身份认证， 同一个用户可以有多种不同的角色。
                .roles("normal", "admin", "user");

        auth.inMemoryAuthentication()
                .withUser("lujun")
                .password(passwordEncoder.encode("123456"))
                .roles("normal");

        auth.inMemoryAuthentication()
                .withUser("admin")
                .password(passwordEncoder.encode("123456"))
                .roles("admin");
    }

    /*
        SpringSecurity5 版本要求密码必须进行加密，不可以是明文形式，否则报错
        java.lang.IllegalArgumentException: 
                              There is no PasswordEncoder mapped for the id "null
    */                             
    @Bean
    public PasswordEncoder passwordEncoder() {
        // 创建 PasswordEncoder 的实现类，实现类是一种加密算法：
        // 根据项目具体需求，选择不同的加密算法。
        return new BCryptPasswordEncoder();
    }

}
```

---



|                     SpringSecurity加密类                     |
| :----------------------------------------------------------: |
| <img src="imgs\多种加密算法类.png" alt="多种加密算法类" style="zoom: 67%;" /> |



### 3.2 使用数据库中的用户信息进行认证

#### 1）为什么要使用这种方式进行认证

> ​		在实际应用中，用户数量会有很多，如果再使用以上的两种方法来进行用户信息配置，就不方便进行管理了。所以，SpringSecurity还可以使用数据表中的信息进行认证。



​		在 SpringSecurity 框架中，所规定的表示_对象用户信息的类_是 __<font color="blue">UserDetails的实现类</font>__。UserDetails 是一个接口，是一个高度抽象的用户信息类。

- `User 类`：是SpringSecurity官方提供的 UserDetails 接口的实现类。需要向 SpringSecurity 提供 User 对象， 这个对象的数据来自数据库 的查询。



#### 3）案例演示：自定义的UserDetailsService接口实现类

- 使用MyBatis操作数据库

- 创建用户信息表及其实体类（此处只做基本演示，具体需要依据实际项目中的需求来实现）

```java
public class UserInfo {
    // 用户id
    private String id;
    // 用户名
    private String username;
    // 用户密码
    private String password;  // 字段长度：255
    // 用户角色
    private String role;
}
```

- 给数据表中添加用户信息

> 1. 此类只是初始化数据表中的用户信息以进行下一步的测试。
>
> 2. 第一次用完后，请注释掉@Component注解！！！

```java
@Component
public class DBUserInfoInit {

    @Resource
    private UserService userService;

    @Resource
    private PasswordEncoder passwordEncoder;

    @PostConstruct
    public void init() {
        UserInfo user = new UserInfo();
        user.setId("1");
        user.setUsername("rainlu");
        user.setPassword(passwordEncoder.encode("123456"));
        user.setRole("normal");
        userService.addUserInfo(user);

        user.setId("2");
        user.setUsername("lujun");
        user.setPassword(passwordEncoder.encode("123456"));
        user.setRole("admin");
        userService.addUserInfo(user);
    }

}
```

- 创建UserDetailsService实现类

> 这个自定义的UserDetailsService实现类是为了加载数据表中的用户信息。
>
> __SpringSecurity将根据用户进行认证时输入的username进行数据库查询。__
>
> __<font color="red">供SpringSecurity调用，程序员只负责定义、注册、指定加密规则。</font>__

```java
@Service("userDetailsService")
public class UserDetailsServiceImpl implements UserDetailsService {

    @Resource
    UserMapper userMapper;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        UserInfo userInfo = userMapper.selectUserInfoByName(username);

        if (userInfo != null) {
            List<GrantedAuthority> roleList = new LinkedList<>();
            // 角色命名规则：必须以“ROLE_”开头
            GrantedAuthority role1 = new SimpleGrantedAuthority("ROLE_" + userInfo.getRole());
            roleList.add(role1);

            // 创建并返回User对象（UserDetails实现类）
            return new User(userInfo.getUsername(), userInfo.getPassword(), roleList);
        }

        return null;
    }
}
```

- 定义配置类

> - 注册自定义的UserDetailsService实现类
>
> - 指定加密规则

``` java
@EnableWebSecurity
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class MyWebSecurityConfig extends WebSecurityConfigurerAdapter {
    @Resource
    private PasswordEncoder passwordEncoder;

    @Resource
    private UserDetailsService userDetailsService;

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {

/**
 * 1、注册自定义的UserDetailsService实现类，用于被SpringSecurity自动调用 加载数据表中的用户信息
 * 2、指明用户信息密码的加密规则
 */
        auth.userDetailsService(userDetailsService).passwordEncoder(passwordEncoder);
    }
}
```

- 给需要进行权限认证的处理器方法加上注解`@PreAuthorize(value = "hasAnyRole('角色名称'……)")`

```java
例子：
@GetMapping("/normal")
@PreAuthorize("hasAnyRole('normal')")    //  指定可以访问此方法的role；可以配置多个角色
public String normalResponse(){
    return "Hello normalResponse！";
}
```





## 四、认证和授权的基本操作

### 4.1 什么是 认证和授权

> authentication：认证。认证访问者是谁。 一个用户或者一个其他系统是不是当前要访问的系统中的有效用户。
> authorization：授权。认证通过进入系统后，访问者能做些什么，拥有怎样的权限。

比如说张三用户要访问一个公司 oa 系统。 首先系统要判断张三是不是公司中的有效用户。

- 认证：张三是不是有效的用户，是不是公司的职员。

- 授权： 判断张三能否做某些操作。 
  - 如果张三是个领导可以批准下级的请假， 其他的操作。
  - 如果张三只是一个普通用户，只能看自己的相关数据， 只能提交请假申请等等。



### 4.2 RBAC 是什么

> 简而言之
>
> ​	RBAC： __<font color="blue">用户是属于角色的， 角色拥有权限的集合。 用户属于某个角色， 他就具有角色对应的权限。</font>__
>
> - __权限：__能对资源进行的操作。比如增加，修改，删除，查看等等。 
>
> - __角色：__自定义的， 表示多种权限的集合。一个角色可以有多个权限。

​		__RBAC ：基于角色的访问控制（Role-Based Access Control ）。__

​		在 RBAC 中，权限与角色相关联，用户通过成为某个角色的旗下的成员而得到这些角色的权限。这就极大地简化了权限的管理（不需要为每个用户单独设置权限）。这样的管理都是层级之间相互依赖的，权限赋予给角色，又将角色赋予给用户，这样的权限设计很清楚，管理起来很方便。

​		其基本思想是，对系统操作的各种权限不是直接授予具体的用户，而是在用户集合与权限集合之间建立一个角色集合。每一种角色对应一组相应的权限。一旦用户被分配了适当的角色后，该用户就拥有此角色的所有操作权限。这样做的__<font color="red">好处是，不必在每次创建用户时都进行分配权限的操作，只要分配用户相应的角色即可，而且角色的权限变更比用户的权限变更要少得多，这样将简化用户的权限管理，减少系统的开销。</font>__



> RBAC 设计中的表：
> 1. 用户表： 用户认证（登录用到的表）
>     字段信息：用户名，密码，是否启用，是否锁定等信息。
>
> 2. 角色表：定义角色信息
>
>    字段信息：角色名称， 角色的描述。
>
> 3. 用户和角色的关系表： 用户和角色是多对多的关系。 
>
> ​	   映射关系：一个用户可以有多个角色， 一个角色可以有多个用户。 
>
> 4. 权限表
>
> 5. 角色和权限的关系表
>
>    表示角色可以拥有哪些权限。
>
>    
>
>    __RBAC所涉及到的数据表依赖关系示意图__
>
>    ![RBAC所涉及到的数据表依赖关系](imgs\RBAC所涉及到的角色数据表依赖关系.png)
>
>    





### 4.3 SpringSecurity中认证的接口和类

- UserDetails接口

> SpringSecurity官方所提供的核心用户信息接口规范

```java
public interface UserDetails extends Serializable {
    
    // 存储用户的角色（权限）信息：因为一个用户可以对应多个角色，所以使用集合表示
    Collection<? extends GrantedAuthority> getAuthorities();

    // 用户密码
    String getPassword();

    // 用户名
    String getUsername();

    // 当前用户的账户是否已经过期
    boolean isAccountNonExpired();

     // 当前用户的账户是否已经被锁定
    boolean isAccountNonLocked();

    // 当前用户的凭据（证书）是否已过期。过期的凭据会阻止身份验证。
    boolean isCredentialsNonExpired();

    // 是否启用当前用户账号
    boolean isEnabled();
}
```



- UserDetailsService 接口的实现类
  - 在[自定义UserDetailsService接口实现类](#@-案例演示：自定义UserDetailsService接口实现类)中，已经基本了解到了UserDetailsService 接口的作用。而SpringSecurity官方提供了两种实现类以供使用。

> 1. __<font color="blue">InMemoryUserDetailsManager：</font>__在内存中维护用户信息。
>
> - 优点：使用方便。
>
> - 缺点：数据不是持久的。系统重启后数据恢复原样。
>
>   
>
> 2. __<font color="blue">JdbcUserDetailsManager：</font>__用户信息存放在数据库中，底层使用JdbcTemplate 操作数据库。 可以使用 JdbcUserDetailsManager 中的方法完成定义用户的管理。
>
>    - createUser ： 创建用户
>    - updateUser：更新用户
>    - deleteUser：删除用户
>    - userExists：判断用户是否存在
>
> ```sql
> // sql脚本locate
> org.springframework.security.core.userdetails.jdbc
> 
> // 根据不同版本数据库进行调整
> create table users(username varchar_ignorecase(50) not null primary key,
>                    password varchar_ignorecase(500) not null,
>                    enabled boolean not null
>                   );
> 
> create table authorities (username varchar_ignorecase(50) not null,
>                           authority varchar_ignorecase(50) not null,
>                           constraint fk_authorities_users 
>                           foreign key(username) references users(username)
>                          );
> 
> create unique index ix_auth_username on authorities (username,authority);
> ```
>
> 



- User类（部分）

> SpringSecurity官方所提供的核心用户信息接口规范的实现类。
>
> - 当然，也可以自定义 UserDetails 接口实现类，作为系统中的用户信息类。并将类交给 SpringSecurity 管理使用。

```java
public class User implements UserDetails, CredentialsContainer {

	private static final long serialVersionUID = SpringSecurityCoreVersion.SERIAL_VERSION_UID;

	private static final Log logger = LogFactory.getLog(User.class);

	private String password;
	private final String username;
	private final Set<GrantedAuthority> authorities;
	private final boolean accountNonExpired;
	private final boolean accountNonLocked;
	private final boolean credentialsNonExpired;
	private final boolean enabled;

    // 构造一个只有“用户名。用户密码，用户权限”的用户对象
	public User(String username, String password,
			Collection<? extends GrantedAuthority> authorities) {
		this(username, password, true, true, true, true, authorities);
	}
    
    // 构造一个拥有详细权限认证的用户对象
	public User(String username, String password, boolean enabled,
			boolean accountNonExpired, boolean credentialsNonExpired,
			boolean accountNonLocked, Collection<? extends GrantedAuthority> authorities) {

		if (((username == null) || "".equals(username)) || (password == null)) {
			throw new IllegalArgumentException(
					"Cannot pass null or empty values to constructor");
		}

		this.username = username;
		this.password = password;
		this.enabled = enabled;
		this.accountNonExpired = accountNonExpired;
		this.credentialsNonExpired = credentialsNonExpired;
		this.accountNonLocked = accountNonLocked;
		this.authorities = Collections.unmodifiableSet(sortAuthorities(authorities));
	}
```



- UserDetailsService接口

> 主要作用：在该接口实现类的loadUserByUsername方法中获取数据库中的用户信息，得到是 UserDetails 对象。
>
> 对应的数据库操作即为：__<font color="red">根据用户名查询用户信息</font>__。

```java
public interface UserDetailsService {
	UserDetails loadUserByUsername(String username) throws UsernameNotFoundException;
}
```





### 4.4 基于内存中的用户角色权限控制（InMemoryUserDetailsManager）

- 创建所需___加密类对象___以及再用___UserDetails的实现类___创建用户信息

```java
@Configuration
public class ApplicationBeanFactory {

    @Resource
    private PasswordEncoder passwordEncoder;

    @Bean
    public PasswordEncoder passwordEncoder() {
        //创建 PasswordEncoder 的实现类， 实现类是加密算法：根据项目具体需求，选择不同的加密算法
        return new BCryptPasswordEncoder();
    }

	// 使用UserDetails的实现类来创建用户信息对象
    @Bean
    public UserDetailsService userDetailsService() {
        // 在内存中维护用户信息。
        InMemoryUserDetailsManager manager = new InMemoryUserDetailsManager();

        // 创建一些用户
        manager.createUser(User.withUsername("rainlu")
                .password(passwordEncoder.encode("1234567"))
                .roles("admin", "normal")
                .build());

        manager.createUser(User.withUsername("lujun")
                .password(passwordEncoder.encode("123456"))
                .roles("normal")
                .build());

        return manager;
    }
}
```



- 定义配置类

> 使用UserDetails的实现类创建对象，再注册进configure方法中。

```java
@EnableWebSecurity
public class MyWebSecurityConfig extends WebSecurityConfigurerAdapter {
    @Resource
    private UserDetailsService userDetailsService;

    // 在configure方法中定义创建的用户信息
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        // 此处不可以省略！！！！需要使用父类的formLogin()表单登录进行安全登录验证！
        super.configure(http);   
        http.userDetailsService(userDetailsService);
    }
}
```



### 4.5 基于数据库中的用户角色权限控制（JdbcUserDetailsManager）

- 根据官方所给ddl文件中的SQL语句，定义好所需表

```sql
// sql脚本locate
org.springframework.security.core.userdetails.jdbc

// 根据不同版本数据库进行调整
create table users(username varchar(50) not null primary key,
                   password varchar(500) not null,
                   enabled boolean not null
                  );

create table authorities (username varchar(50) not null,
                          authority varchar(50) not null,
      constraint fk_authorities_users foreign key(username) references users(username)
                         );

create unique index ix_auth_username on authorities (username,authority);
```



- 创建所需___加密类对象___以及再用___UserDetails的实现类___创建用户信息

```java
@Configuration
public class ApplicationConfig {

    //通过spring容器注入 DataSource
    @Resource
    private DataSource dataSource;
    
    @Resource
    private PasswordEncoder passwordEncoder;

    //创建PasswordEncoder对象
    @Bean
    public PasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder();
    }

    //创建JdbcUserDetatilsService对象
    @Bean(name = "jdbcUserDetatilsService")
    public UserDetailsService jdbcUserDetatilsService(){

        // 初始数据源DataSource --- JdbcTemplate对象
        // 底层使用的是JdbcTemplate对象创建的用户信息
        JdbcUserDetailsManager manager = new JdbcUserDetailsManager(dataSource);

        //如果数据库中已经存在账号不添加
        if( !manager.userExists("admin")){
            manager.createUser(User.withUsername("admin")
                    .password(passwordEncoder.encode("admin"))
                    .roles("ADMIN","USER","MANAGER").build());
        }

        if( !manager.userExists("zs")){
            manager.createUser(User.withUsername("zs").
                    password(passwordEncoder.encode("123"))
                    .roles("USER").build());
        }


        if( !manager.userExists("lisi")){
            manager.createUser(User.withUsername("lisi")
                    .password(passwordEncoder.encode("456"))
                    .roles("USER","NORMAL").build());
        }
        return manager;
    }
}
```



- 定义配置类

```java
@EnableWebSecurity
public class MySecurityConfig extends WebSecurityConfigurerAdapter {

    @Autowired
    @Qualifier("jdbcUserDetatilsService")
    private UserDetailsService userDetailsService;

    @Override
    protected void configure(HttpSecurity http) throws Exception {
       super.configure(http);
       http.userDetailsService(userDetailsService);
    }
}
```





## 五、用户角色访问权限控制案例

- 建表

```sql
SET NAMES utf8mb4;
SET FOREIGN_KEY_CHECKS = 0;

-- ----------------------------
-- Table structure for relation_user_role
-- ----------------------------
DROP TABLE IF EXISTS `relation_user_role`;
CREATE TABLE `relation_user_role`  (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `userid` int(11) NULL DEFAULT NULL,
  `roleid` int(11) NULL DEFAULT NULL,
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 5 CHARACTER SET = utf8mb4 COLLATE = utf8mb4_general_ci ROW_FORMAT = Dynamic;


-- ----------------------------
-- Table structure for sys_role
-- ----------------------------
DROP TABLE IF EXISTS `sys_role`;
CREATE TABLE `sys_role`  (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `rolename` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NULL DEFAULT NULL,
  `rolememo` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NULL DEFAULT NULL,
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 4 CHARACTER SET = utf8mb4 COLLATE = utf8mb4_general_ci ROW_FORMAT = Dynamic;


-- ----------------------------
-- Table structure for sys_user
-- ----------------------------
DROP TABLE IF EXISTS `sys_user`;
CREATE TABLE `sys_user`  (
  `id` int(11) NOT NULL,
  `username` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NULL DEFAULT NULL,
  `password` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NULL DEFAULT NULL,
  `realname` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NULL DEFAULT NULL,
  `isenable` int(11) NULL DEFAULT NULL,
  `isexpire` int(11) NULL DEFAULT NULL,
  `islock` int(11) NULL DEFAULT NULL,
  `iscredentials` int(11) NULL DEFAULT NULL,
  `createtime` date NULL DEFAULT NULL,
  `logintime` date NULL DEFAULT NULL,
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8mb4 COLLATE = utf8mb4_general_ci ROW_FORMAT = Dynamic;
```

- 创建实体类
  - User类需要继承UserDetails；Role实体类正常创建

```java
@Data
public class SysUser implements UserDetails {

    private Integer id;
    private String username;
    private String password;
    private String realname;

    private Integer isExpired;           // 1：true  0：false
    private Integer isLocked;            // 1：true  0：false
    private Integer isCredentials;       // 1：true  0：false
    private Integer isEnabled;           // 1：true  0：false


    private Date createTime;
    private Date loginTime;

    private List<GrantedAuthority> authorities;


    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        return authorities;
    }

    @Override
    public String getPassword() {
        return password;
    }

    @Override
    public String getUsername() {
        return username;
    }

    @Override
    public boolean isAccountNonExpired() {
        return isExpired == 1;
    }

    @Override
    public boolean isAccountNonLocked() {
        return isLocked == 1;
    }

    @Override
    public boolean isCredentialsNonExpired() {
        return isCredentials == 1;
    }

    @Override
    public boolean isEnabled() {
        return isEnabled == 1;
    }
}
```

- 自定义UserDetailsService实现类，完成用户的权限信息查询、封装

```java
@Service("userDetailsService")
public class JdbcUserDetatilsService implements UserDetailsService {

    @Resource
    private SysUserMapper userMapper;

    @Resource
    private SysRoleMapper roleMapper;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        //1. 根据username 获取SysUser
        SysUser user = userMapper.selectSysUser(username);
        System.out.println("loadUserByUsername user:" + user);

        // 2、给自定义的 UserDetails实现类：SysUser 封装 授权 信息
        if (user != null) {
            // 3. 根据userid 获取用户的 role 信息
            List<SysRole> roleList = roleMapper.selectRoleByUser(user.getId());
            System.out.println("roleList:" + roleList);

            List<GrantedAuthority> authorities = new ArrayList<>();
            String roleName = "";
            for (SysRole role : roleList) {
                roleName = role.getName();
                // SimpleGrantedAuthority最常用
                //参数角色名称，需要以"ROLE_"开头， 后面加上自定义的角色名称
                GrantedAuthority authority = new SimpleGrantedAuthority("ROLE_" + roleName);
                authorities.add(authority);
            }
            user.setAuthorities(authorities);
        }
        return user;
    }
}
```



- 定义配置类

```java
@EnableWebSecurity
public class CustomSecurityConfig extends WebSecurityConfigurerAdapter {

    @Resource
    private UserDetailsService userDetailsService;

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        // 这里如果使用super.configure中的方法，将不会使用当前配置的加密规则
        //super.configure(auth);
        auth.userDetailsService(userDetailsService).passwordEncoder(new BCryptPasswordEncoder());
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        System.out.println("=======configure HttpSecurity========== ");
        http.authorizeRequests()
                // 指定哪些地址可以访问，和自定义登录有关的页面也需要指定
                .antMatchers("/index", "/login", "/", "/mylogin.html", "/error.html").permitAll()
	            // 指定资源访问的角色权限信息
                .antMatchers("/access/user/**").hasRole("USER")  
                .antMatchers("/access/read/**").hasRole("READONLY")
                .antMatchers("/access/admin/**").hasRole("ADMIN")
                .anyRequest().authenticated()
                .and()
            	// 表单登录
                .formLogin()
                .loginPage("/mylogin.html")          // 自定义的登录页面
                .loginProcessingUrl("/login")        // 登录页面提交访问的uri地址
                .failureUrl("/error.html")           // 登录验证错误提示页面
	            .usernameParameter("myname") // 指定表单登录的表示用户的name
				.passwordParameter("mypwd")  // 指定表单登录表示密码的name
                .and()
                .csrf()
                .disable();
    }
    
    
    // 加密规则实现类
    @Bean
    public PasswordEncoder passwordEncoder() {
        //创建 PasswordEncoder 的实现类， 实现类是加密算法：根据项目具体需求，选择不同的加密算法
        return new BCryptPasswordEncoder();
    }

}
```



## 六、过滤器简单使用

![过滤器的使用](imgs\过滤器的使用.png)

https://github.com/RainLu996/SpringSecurity.git
