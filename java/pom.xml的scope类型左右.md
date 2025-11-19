为什么scope 设为 provided就可以了，这个是什么意思

在 Maven 依赖管理中，`<scope>provided</scope>` 表示 **该依赖在编译和测试时需要，但在运行时由运行环境（如 Tomcat）提供，不需要打包进最终的部署文件（如 WAR 包）**。

------

## **为什么 `provided` 能解决这个问题？**

### **1. Tomcat 本身已经包含 Servlet API**

- Tomcat 是一个 **Servlet 容器**，它本身已经内置了 `javax.servlet` 相关的类（如 `Servlet`、`Filter`、`ServletContainerInitializer` 等）。
- 如果你在项目中直接引入 `javax.servlet-api` 并打包进 WAR，可能会导致：
  - **类加载冲突**：Tomcat 加载自带的 Servlet API，而你的 WAR 包里也有同样的类，可能版本不一致。
  - **`ClassCastException`**：由于类加载器不同，`SpringServletContainerInitializer` 可能无法转换成 Tomcat 提供的 `ServletContainerInitializer`。

### **2. `provided` 的作用**

| Scope            | 编译时可用 | 测试时可用 | 运行时可用      | 是否打包进 WAR/JAR |
| :--------------- | :--------- | :--------- | :-------------- | :----------------- |
| `compile` (默认) | ✔️          | ✔️          | ✔️               | ✔️                  |
| `provided`       | ✔️          | ✔️          | ❌（由容器提供） | ❌                  |
| `runtime`        | ❌          | ✔️          | ✔️               | ✔️                  |
| `test`           | ❌          | ✔️          | ❌               | ❌                  |

- **`provided` 的作用**：
  - **编译和测试阶段**：Maven 会提供该依赖，确保代码能编译通过（如 `@WebServlet`、`HttpServletRequest` 等类可用）。
  - **运行阶段**：**不打包进 WAR**，而是由 Tomcat 提供 Servlet API 的实现，避免冲突。