---
title: Shiro验证配置
categories:
  - 配置
abbrlink: 4045269161
---

## pom.xml添加依赖
<!--more-->
```xml
<!--添加对shior的支持-->
<dependency>
    <groupId>org.apache.shiro</groupId>
    <artifactId>shiro-core</artifactId>
    <version>1.4.0</version>
</dependency>

<dependency>
    <groupId>org.apache.shiro</groupId>
    <artifactId>shiro-spring</artifactId>
    <version>1.4.0</version>
</dependency>
<!-- 添加thymeleaf整合shiro标签 依赖 -->
<dependency>
    <groupId>com.github.theborakompanioni</groupId>
    <artifactId>thymeleaf-extras-shiro</artifactId>
    <version>2.0.0</version>
</dependency>
```

## controller层

```java
//获取 subject
Subject subject= SecurityUtils.getSubject();
//封装用户数据
UsernamePasswordToken token=new UsernamePasswordToken(user.getUsername(), CryptographyUtil.md5(user.getPassword(), "java"));
try {
    subject.login(token);
    User user2 = userMapper.findByName(user.getUsername());
    //把当前用户信息存到session中
    SecurityUtils.getSubject().getSession().setAttribute("currentUser", user2);
    return LayuiJSON.jsonStr(true,"登陆成功！");
} catch (UnknownAccountException e) {
    return LayuiJSON.jsonStr(false,"用户名不存在！");
}catch (IncorrectCredentialsException e) {
    return LayuiJSON.jsonStr(false,"密码错误！");
}
```

LayuiJSON.jsonStr(true,"登陆成功！");是我封装的工具类

```java
public static String jsonStr(boolean success,String msg)  {
        JSONObject obj = new JSONObject();
        obj.put("msg", msg);
        obj.put("success",success);
        return obj.toString();
    }
```

## 编写配置类

```java
@Configuration
 class ShiroConfig {

    /**
     * ShiroFilterFactoryBean 处理拦截资源文件问题。
     * 注意：单独一个ShiroFilterFactoryBean配置是或报错的，以为在
     * 初始化ShiroFilterFactoryBean的时候需要注入：SecurityManager
     *
     * Filter Chain定义说明 1、一个URL可以配置多个Filter，使用逗号分隔 2、当设置多个过滤器时，全部验证通过，才视为通过
     * 3、部分过滤器可指定参数，如perms，roles
     *
     */
    @Bean
    public ShiroFilterFactoryBean shirFilter(SecurityManager securityManager) {
        ShiroFilterFactoryBean shiroFilterFactoryBean = new ShiroFilterFactoryBean();

        // 必须设置 SecurityManager
        shiroFilterFactoryBean.setSecurityManager(securityManager);

        // 如果不设置默认会自动寻找Web工程根目录下的"/login.jsp"页面
        //设置 没有权限 登陆页面
        shiroFilterFactoryBean.setLoginUrl("/admin");
        //设置 没有权限 登陆页面


        // 拦截器.
        Map<String, String> filterChainDefinitionMap = new LinkedHashMap<String, String>();
        // 配置不会被拦截的链接 顺序判断
        filterChainDefinitionMap.put("/static/**", "anon");
        filterChainDefinitionMap.put("/blog/**", "anon");
        filterChainDefinitionMap.put("/authIamge", "anon");

        // 配置退出过滤器,其中的具体的退出代码Shiro已经替我们实现了
        filterChainDefinitionMap.put("/logout", "logout");

        // <!-- 过滤链定义，从上向下顺序执行，一般将 /**放在最为下边 -->:这是一个坑呢，一不小心代码就不好使了;
        // <!-- authc:所有url都必须认证通过才可以访问; anon:所有url都都可以匿名访问-->
        //filterChainDefinitionMap.put("/**", "authc");

        //authc需要 登陆才能访问
        filterChainDefinitionMap.put("/admin/**", "authc");
        //authc需要 登陆才能访问


        shiroFilterFactoryBean.setFilterChainDefinitionMap(filterChainDefinitionMap);
        return shiroFilterFactoryBean;
    }


    /**
     * 创建 安全管理器 SecurityManager
     * @return
     */
    @Bean
    public SecurityManager securityManager() {
        DefaultWebSecurityManager securityManager = new DefaultWebSecurityManager();
        // 设置realm.
        securityManager.setRealm(myRealm());
        return securityManager;
    }

    /**
     *
     * 身份认证realm; (这个需要自己写，账号密码校验；权限等)
     *
     * @return
     */
    @Bean
    public MyRealm myRealm() {
        MyRealm myRealm = new MyRealm();
        return myRealm;
    }

    /**
     * Shiro生命周期处理器
     * @return
     */
    @Bean
    public LifecycleBeanPostProcessor lifecycleBeanPostProcessor(){
        return new LifecycleBeanPostProcessor();
    }
    /**
     * 开启Shiro的注解(如@RequiresRoles,@RequiresPermissions),需借助SpringAOP扫描使用Shiro注解的类,并在必要时进行安全逻辑验证
     * 配置以下两个bean(DefaultAdvisorAutoProxyCreator(可选)和AuthorizationAttributeSourceAdvisor)即可实现此功能
     * @return
     */
    @Bean
    @DependsOn({"lifecycleBeanPostProcessor"})
    public DefaultAdvisorAutoProxyCreator advisorAutoProxyCreator(){
        DefaultAdvisorAutoProxyCreator advisorAutoProxyCreator = new DefaultAdvisorAutoProxyCreator();
        advisorAutoProxyCreator.setProxyTargetClass(true);
        return advisorAutoProxyCreator;
    }

    @Bean
    public AuthorizationAttributeSourceAdvisor authorizationAttributeSourceAdvisor(){
        AuthorizationAttributeSourceAdvisor authorizationAttributeSourceAdvisor = new AuthorizationAttributeSourceAdvisor();
        authorizationAttributeSourceAdvisor.setSecurityManager(securityManager());
        return authorizationAttributeSourceAdvisor;
    }

    /**
     * 配置 ShiroDialect 用于thymeleaf 和shiro标签配合使用
     * @return
     */
    @Bean
    public ShiroDialect getShiroDialect(){
        return new ShiroDialect();
    }
```

```java
/**
 * 自定义Realm
 *
 */
public class MyRealm extends AuthorizingRealm{


    @Resource
    private UserMapper userMapper;

    /**
     * 授权
     */
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principals) {
        String name=(String) SecurityUtils.getSubject().getPrincipal();
        User user=userMapper.findByName(name);
        SimpleAuthorizationInfo info=new SimpleAuthorizationInfo();
        info.addStringPermission("user:add"); //添加权限
        return info;
    }

    /**
     * 权限认证
     */
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
        String name=(String)token.getPrincipal();
        User user=userMapper.findByName(name);
        if(user!=null){
            AuthenticationInfo authcInfo=new SimpleAuthenticationInfo(user.getUsername(),user.getPassword(),"java");
            return authcInfo;
        }else{
            return null;
        }
    }
```





关键是拦截**资源路径**(路径指的是RequestMapping配置的路径)的配置 因为跳转

到登录界面RequestMapping配置的是/admin

先配置放行的资源,再配置**登录后才能访问的资源**,就是拦截资源这个很关键

比如你的登录逻辑判断访问路径是/admin/login下边,也会把你拦截,因为这个时候还没登录成功

