---
title: 面试知识
date: 2025-04-07 11:23:51
categories: interview #文章分类
tags: [interview] #文章标签，可以一次添加多个标签
---

...

<!-- more -->

## 问题点

1. 为什么 webpack 启动慢首屏加载快，而 vite 启动快首屏加载慢？

2. 做过了项目的最有挑战性的东西

3. hash 模式和 history 模式的区别

4. Promise 为什么支持链式调用,我用 try catch 能捕获到错误吗

MutationObserver

5. pnpm + turborepo + monorepo 怎么实现？

6. canvas 怎么截图视频某个时间点的一帧画面

监听 timeupdate 和 draWImage

7. npm/yarn/pnpm 的区别

8. es6 数组的使用

9. 用 import.meta.glob 导入的文件怎么使用？

10. jwt 和 session-cookie 的区别

11. type 和 interface 的区别

信创数据库 tdsql

12. 打包文件分包

```javascript
config.optimization = {
  splitChunks: {
    cacheGroups: {
      vendor: {
        test: /[\\/]node_modules[\\/]/,
        name(module) {
          // get the name. e.g. node_modules/packageName/not/this/part.js
          // or node_modules/packageName
          const packageName = module.context.match(
            /[\\/]node_modules[\\/](.*?)([\\/]|$)/
          )[1];
          // npm package names are URL-safe,but some servers dont like @ symbols
          return `npm.${packageName.replace("@", "")}`;
        },
        chunks: "all",
        enforce: true,
        priority: 10,
        minSize: 10000, // 10kb
        maxSize: 30000, // 30kb
        reuseExistingChunk: true,
      },
    },
  },
};
```

// 删除 node_sass 依赖改成依赖 sass

```javascript
loaderOptions: {
  sass: {
    api: "modern-compiler";
  }
}
```

13. ide----cursor

14. spring security 认证流程 (ruoyi)

AuthenticationContextHolder
ThreadLocal 存一下 Authentication

UsernamePasswordAuthenticationToken authenticationToken = new UsernamePasswordAuthenticationToken(username, password);
AuthenticationContextHolder.setContext(authenticationToken);
// 该方法会去调用 UserDetailsServiceImpl.loadUserByUsername
authentication = authenticationManager.authenticate(authenticationToken);

LoginUser loginUser = (LoginUser) authentication.getPrincipal();

### 认证框架 shiro

### 认证

1. 添加依赖 spring-boot-starter-security
2. 配置类 SecurityConfig, 实现 protected SecurityFilterChain filterChain(HttpSecurity httpSecurity) throws Exception
3. 自定义认证逻辑 public class UserDetailsServiceImpl implements UserDetailsService

```java
class LoginUser implements UserDetails {}
@Service
public class UserDetailsServiceImpl implements UserDetailsService{
  public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException{
  return new LoginUser()
}
}
```

4. LoginService

```java
@Resource
private AuthenticationManager authenticationManager;
// 用户验证
Authentication authentication = null;
try
{
    UsernamePasswordAuthenticationToken authenticationToken = new UsernamePasswordAuthenticationToken(username, password);
    AuthenticationContextHolder.setContext(authenticationToken);
    // 该方法会去调用UserDetailsServiceImpl.loadUserByUsername
    authentication = authenticationManager.authenticate(authenticationToken);
}

LoginUser loginUser = (LoginUser) authentication.getPrincipal();

return tokenService.createToken(loginUser);
```

5. 请求的用户校验

```java
/**
 * token过滤器 验证token有效性
 */
@Component
public class JwtAuthenticationTokenFilter extends OncePerRequestFilter{
  //验证token有效性，设置Authentication的ThreadLocal
  UsernamePasswordAuthenticationToken authenticationToken = new UsernamePasswordAuthenticationToken(loginUser, null, loginUser.getAuthorities());
  authenticationToken.setDetails(new WebAuthenticationDetailsSource().buildDetails(request));
  SecurityContextHolder.getContext().setAuthentication(authenticationToken);
}
```

6. 退出登录

```java
@Configuration
public class LogoutSuccessHandlerImpl implements LogoutSuccessHandler{}
```

token 逻辑，生成一个 uuid,loginUser.setToken(uuid);
uuid-->loginUser 的映射存 redis
Map<String, Object> claims = new HashMap<>();
claims.put(Constants.LOGIN_USER_KEY, uuid);
生成 jwt 的 token

7. 授权模块独立另一个应用使用认证
   引入 spring-boot-starter-security
   在 SecurityContextHolder 中获取
   Authentication auth = SecurityContextHolder.getContext().getAuthentication();

JPA 依赖@PrePersist,@PreUpdate
@PrePersist 注解用于标记一个方法，该方法会在实体首次被持久化到数据库之前自动调用。当调用 EntityManager.persist() 方法时，JPA 会先执行被 @PrePersist 注解标记的方法。
@PreUpdate 注解用于标记一个方法，该方法会在实体被更新到数据库之前自动调用。当调用 EntityManager.merge() 方法或者 JPA 检测到实体状态发生变化时，会先执行被 @PreUpdate 注解标记的方法。

### 授权

3. SecurityFilterChain 配置

```java
http
    .authorizeRequests()
        .antMatchers("/api/users/**").hasRole("USER")  // 需要USER角色
        .antMatchers("/api/admin/**").hasAuthority("ADMIN")  // 需要ADMIN权限
        .anyRequest().authenticated();
```

4. 启用 @PreAuthorize 和 @PostAuthorize：可以在 service 层的方法上添加注解，来控制用户是否有权限访问该方法。
   使用@PreAuthorize 注解结合 SpEL 表达式，对 API 接口进行权限校验。

5. 借助 BCryptPasswordEncoder 对用户密码进行加密存储
6. 怎么和 jwt 结合 JwtUtil + JwtAuthenticationTokenFilter

ruoyi-react 认证和授权涉及类

- SecurityConfig
- UserDetailsServiceImpl
- JwtAuthenticationTokenFilter
- TokenService

- LoginService
- LoginUser

### 监控接入

7. 监控 spring-boot-starter-actuator 怎么使用

- 添加依赖

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring - boot - starter - actuator</artifactId>
</dependency>
```

- 配置端点,暴露全部端点

```
management:
  endpoints:
    web:
      exposure:
        include: '*'
```

- 默认指标
  spring-boot-starter-actuator 默认集成了 Micrometer 库（Java 应用的度量收集工具），
  并自动注册了一系列与 JVM、系统资源、HTTP 请求等相关的指标。
  这些指标无需手动配置，引入依赖后即可自动生效。

- prometheus 主动拉取采集指标

启动 Spring Boot 应用后，
Prometheus 可通过 http://<应用地址>:<端口>/actuator/prometheus 端点采集指标。
接着在 Prometheus 配置文件 prometheus.yml 中添加抓取配置：

```yml
scrape_configs:
  - job_name: "spring-boot-app"
    metrics_path: "/actuator/prometheus"
    static_configs:
      - targets: ["localhost:8080"]
```

```xml
<dependencies>
    <!-- Spring Boot Actuator -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    <!-- Micrometer Prometheus 集成 -->
    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-registry-prometheus</artifactId>
    </dependency>
</dependencies>
```

- 整合第三方监控系统
  若需对接 Prometheus、Grafana 等系统，
  可添加额外依赖（如 spring-boot-starter-actuator-prometheus），
  指标会自动转换为对应格式。

### iot 智慧办公项目

搭建 IoT 智慧办公平台，对办公环境设备进行智能管理，，实现办公环境的自动化控制、资源高效管理与数据可视化分析。
智能门锁模块：
功能实现：通过蓝牙 / Wi-Fi 与门锁设备通信，实现手机扫码开锁、权限管理（如访客临时授权）。
技术细节：使用 React Native 的 react-native-bluetooth 库处理蓝牙连接，
结合 Redux 管理门锁状态与用户权限数据。
智能工位模块：
功能实现：支持工位在线预订、实时占用状态查看、设备（如插座、灯光）远程控制。
技术细节：采用 WebSocket 实现工位状态的实时推送，
使用 react - virtualized 优化大数据量工位列表的渲染性能。

我运用 React 的虚拟 DOM Diff 算法原理，对组件进行了细致的优化，避免不必要的重新渲染。
例如，在智能工位模块，只对数据发生变化的组件进行更新，而不是整个页面重新渲染，大大提升了页面加载速度和响应性能。

智能门锁状态更新存在延迟。经排查，是因为频繁请求导致网络拥塞。
我通过设置合理的请求防抖和节流机制，减少不必要的请求次数，优化了网络请求频率，有效解决了延迟问题

登录
云打印
智能工位模块
智能会议室模块 会议室的预定包括智能门锁，智能开关灯，智能窗帘等其他智能 iot 设备的关停

## 构建

使用 npx create-react-app demo-app
···
npx create-react-app demo-app
npx create-react-app demo-app-ts --template typescript
···

npm run eject 的作用

放开所有的 webpack 配置，让你可以自定义 webpack 配置。

## react 的组件常见的声明方式

- 函数式组件
- 类组件

## 官方 hook

- useReducer

- useState

- useEffect

如果没有第二个参数，则每次 render 时，useEffect 中的函数都会执行；
如果有第二个参数，则只有当第二个参数变化时，useEffect 中的函数才会执行。

- useLayoutEffect

- useContext

```
export const haha = createContext(null);
return (<haha.Provider  value={{ loading, enabledFlags }}></haha.Provider>)
子组件
 const { loading, enabledFlags } = useContext(haha);
```

- useMemo
- useCallback
- useRef
- useImperativeHandle
  允许父组件通过 ref 访问子组件中定义的方法和属性，实现父子组件之间的交互 ‌

```js
useImperativeHandle(ref, () => ({
  function1: () => {},
  function2: () => {},
}));
```

父传子
父亲传递 prop
父亲传递 Prop+createRef
子传父
父亲传递函数回调
使用 useImperativeHandle Hook 将指定的方法暴露给父组件

## 非官方的 hook

- ahooks
- react-use

## 第三方库

- react-router-dom
  useLocation
  useNavigate

```jsx
function App() {
  return (
    <Router>
      {/* 导航菜单 */}
      <nav>
        <ul>
          <li>
            <Link to="/">Home</Link>
          </li>
          <li>
            <Link to="/about">About</Link>
          </li>
          <li>
            <Link to="/users">Users</Link>
          </li>
        </ul>
      </nav>

      {/* 路由匹配区域 */}
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/users" element={<Users />} />
        <Route path="*" element={<NotFound />} /> {/* 404页面 */}
      </Routes>
    </Router>
  );
}
```

suspense + React.lazy()

嵌套路由：必须在父组件中使用 <Outlet /> 来渲染子路由。

## react 常见的生命周期

### 构建阶段

- constructor
- getDerivedStateFromProps
  静态函数，当作一个纯函数
  传入 props 和 state，返回值会合并之前的 state，作为·一个新的 state
- componentWillMount

什么是 fiber

- stack reconciler
- fiber reconciler

## 回答：什么是 Fiber

作为 React+Java 岗位的候选人，我会这样回答关于 Fiber 的问题：

Fiber 是 React 16 中引入的全新协调引擎（reconciliation engine），它是 React 核心算法的重写。Fiber 架构主要解决了两个关键问题：

1. **增量渲染能力**：Fiber 将渲染工作分割成多个小任务单元（称为"fibers"），可以分片执行，而不是一次性完成整个渲染。这使得 React 能够实现：

   - 更流畅的用户体验，避免长时间任务阻塞主线程
   - 支持优先级调度，高优先级更新（如用户输入）可以中断低优先级更新

2. **更灵活的渲染控制**：Fiber 架构使 React 能够：
   - 暂停、中止或复用渲染工作
   - 为不同类型的更新分配优先级
   - 在新的并发模式(Concurrent Mode)中实现高级功能

从技术实现角度看：

- 每个 React 元素对应一个 Fiber 节点，它们组成一个链表结构的 Fiber 树
- Fiber 节点包含了组件类型、props、state 等信息
- 协调过程分为两个主要阶段：render/reconciliation（可中断）和 commit（不可中断）

在 Java 全栈开发中理解 Fiber 很有价值，因为它：

1. 帮助我们优化 React 应用性能
2. 理解现代前端框架的设计思想
3. 在处理复杂 UI 时能更好地规划组件结构和状态管理

Fiber 架构是 React 能够实现并发特性的基础，也是 React 保持高性能的关键设计。

为什么 Vue 没有 Fiber？

Vue 采用基于模板的细粒度更新（组件级更新），而 React 的更新策略是从根节点递归遍历，因此需要 Fiber 来拆分任务以避免阻塞

## React.memo 与 useMemo 的区别？

都是 react 中用于优化性能的工具
React.memo 是一个高阶组件，用于优化函数组件的性能。它会比较组件的 props，如果 props 没有变化，则不会重新渲染组件。

useMemo 是一个钩子函数，用于在函数组件中缓存计算结果。它接受一个函数和一个依赖数组作为参数，只有当依赖数组中的值发生变化时，才会重新计算函数的结果。

## react 做过的性能优化

1. React.memo
2. useMemo&useCallback

缓存函数引用，配合 React.memo 防止子组件不必要的渲染。

3. 虚拟化长列表，只渲染可视区域的内容
4. 代码分割，通过 React.lazy 和 Suspense 来实现路由懒加载，减少初始加载时间
5. 减少不必要的渲染，使用 shouldComponentUpdate 或 React.PureComponent 来避免不必要的组件渲染
6. 避免不必要的状态更新，使用 useState 的函数式更新来避免不必要的状态更新
7. 使用事件委托，减少事件处理函数的数量

## hoc

本质是一个函数，接收一个组件并返回一个增强的组件

## 渲染和更新的全过程

初始化阶段
渲染阶段
协调阶段
提交阶段
清理阶段

## React.StrictMode

## React.Fragment

## vue 和 react 的区别

React：React 采用不可变数据（Immutable Data）的方式来触发更新。当状态发生变化时，需要创建一个新的对象或数组，然后通过 setState 或 useState 来更新状态，这样 React 才能检测到变化并重新渲染组件。
Vue：Vue 则是基于 Object.defineProperty ()（Vue 2）或 Proxy（Vue 3）实现的响应式系统。当数据发生变化时，Vue 会自动追踪依赖并更新相关的 DOM，开发者无需手动创建新对象，使用起来更加便捷。

设计理念

- 声明式、组件化、单向数据流
- 渐进式框架，提供一站式解决方案

api 风格

- jsx
- 模板语法

响应式系统

- 基于不可变数据（需创建新对象触发更新），依赖 setState/useState。
- 基于 Proxy（Vue 3）或 Object.defineProperty（Vue 2），自动追踪数据变化。

虚拟 dom

- 纯 JS 实现，需要手动优化（如 useMemo、React.memo）。
- 编译时优化（静态节点标记），运行时性能更好。

## vue2 和 vue3 的区别

特性
​​Vue2​​
Vue3​
​ 优势 ​​ 响应式系统
​Object.defineProperty
Proxy 支持动态属性/数组索引监听，性能更优
​ 代码组织
​Options API
Composition API 逻辑复用更灵活，类型推导更友好
​ 源码组织
​Flow 类型系统
TypeScript 重写更好的类型支持和源码可维护性
​ 包体积
​ 全量引入（22.5kb）
按需引入（<10kb）Tree Shaking 减少 41% 体积

## pinia 和 vuex

摒弃了 mutations 这个概念，直接通过 actions 来修改状态，让 API 更加扁平化。它的设计与 Vue 3 的组合式 API 十分契合，还支持 TypeScript 类型推导。

代码体积更小,更简洁的代码和更好的类型支持
