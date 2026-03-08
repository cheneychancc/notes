## 开发环境搭建

### Node.js & Java

数据库，缓存，对象存储等如果有服务器放在服务器上，没有服务器放在docker里

``` mermaid
flowchart TB
    subgraph Local_Dev["本地开发环境 (Mac/L/W)"]
        direction TB
        Java["Java 应用 (Spring Boot)"]
        NodeJS["Node.js 应用 (Express/Nest)"]
        IDE["IDE: VSCode / IntelliJ / WebStorm"]
        Browser["浏览器调试"]
    end

    subgraph Docker_Containers["Docker 容器服务"]
        direction TB
        MySQL["MySQL 数据库"]
        Redis["Redis 缓存"]
        Nginx["Nginx 反向代理 / 静态资源"]
        MinIO["MinIO 对象存储"]
    end

    IDE --> Java
    IDE --> NodeJS
    Browser --> Nginx
    Java -->|数据库请求| MySQL
    NodeJS -->|缓存/消息| Redis
    Java -->|文件存储| MinIO
    NodeJS -->|文件存储| MinIO
    Java -->|HTTP/API| Nginx
    NodeJS -->|HTTP/API| Nginx
```
