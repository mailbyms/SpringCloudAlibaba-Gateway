
- 测试
  - 启动 nacos 服务器（不需要在网页里配置）
  - 启动 sentinel 服务器
  - 启动一个接口服务器，例如 [SpringCloudAlibaba-Provider](https://github.com/mailbyms/SpringCloudAlibaba-Provider)
  - 测试接口服务器可用，例如 http://localhost:8081/echo/2
  - 最后测试网关：http://localhost:8083/hello/echo/2

- 路由规则：
    ```yaml
    routes:
      - id: demo-server
        uri: lb://nacos-provider
        predicates:
          - Path=/hello/**
        filters:
          - StripPrefix=1
    ```
    - id：保持系统全局唯一，可以在 sentinel 上针对它限流
    - uri: lb 表示使用负载均衡（nacos 会搞定）；也可以用 http://xxx
    - predicates 用来测试路径是否匹配而已
    - StripPrefix 截取路径的个数
    - 当 请求 http://SERVER:port/hello/echo/2 的时候，由 predictes 规则验证，匹配符合。StripPrefix 指示去掉一个路径（剩下 /echo/2），最后转发到 nacos-provider 的 /echo/2 下

- 注意：
  - 默认下 sentinel 懒加载，配置文件加上了 eager: true，项目启动后在 sentinel 控制台显示本项目，可在“请求链路“ 里添加限流规则 
  - sentinel 未持久化，重启本项目，则 sentinel 限流规则清空
  - gateway 的 pom.xml 不能引入 starter-web 

