# peach-dependencies

Peach 基础架构统一依赖版本管理 BOM（`packaging=pom`），供业务工程通过 `dependencyManagement` 引入后统一管理第三方与 Spring 生态版本。

> 文档创作日期：2026-04-21，作者：leiyangjun

---

## 环境要求

- **JDK**：建议 **21**（与 `pom.xml` 中 `java.version` 及 Spring Boot 4 线一致）。
- **Maven**：建议 **3.9+**（与 `central-publishing-maven-plugin` 等插件兼容）。

---

## 使用说明

### 1. 在业务父工程中引入本 BOM

在父 POM 的 `<dependencyManagement>` 中 **import** 本工程（版本号请替换为当前发布或本地安装的版本）：

```xml
<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>org.peach.pom</groupId>
      <artifactId>peach-dependencies</artifactId>
      <version>0.0.1-SNAPSHOT</version>
      <type>pom</type>
      <scope>import</scope>
    </dependency>
  </dependencies>
</dependencyManagement>
```

引入后，子模块依赖 **Spring Boot Starter、Spring Cloud、已在 BOM 中声明坐标的组件** 时，通常 **无需再写 `<version>`**（版本由本 BOM 及所 import 的官方 BOM 共同管理）。

### 2. 与 Spring Boot 4 的关系

本 BOM 固定对齐 **Spring Boot 4.0.x** 与 **Spring Cloud 2025.1.x**。业务应用需按 **Spring Boot 4 迁移指南** 调整代码与 Starter（例如模块化 Web/Kafka 等），不能继续按 Boot 3 方式混用。

---

## Git 远程仓库

本仓库已配置两个 `remote`（在工程根目录执行 `git remote -v` 可查看）：

| 名称 | 地址 |
|------|------|
| **gitee** | `git@gitee.com:leiyangjun/peach-dependencies.git` |
| **github** | `git@github.com:leiyangjun/peach-dependencies.git` |

首次推送示例（分支名按实际修改，如 `main` 或 `master`）：

```bash
git push -u github main
git push -u gitee main
```

---

## 本地安装命令

在本工程根目录执行，将当前版本安装到本机 Maven 仓库（其他项目即可通过坐标引用）：

```bash
mvn clean install
```

仅校验 POM 与依赖解析（不安装）：

```bash
mvn clean validate
```

打包并生成 sources / javadoc 等附属构件（与 `deploy` 前本地验证类似）：

```bash
mvn clean package
```

---

## 推送到 Maven Central 的命令与前提

### 前置条件（摘要）

1. 在 **central.sonatype.com** 注册命名空间，并确保 **`groupId`（如 `org.peach.pom`）** 已获授权。
2. **`pom.xml` 中 `url`、`scm`** 已指向 **GitHub**：`https://github.com/leiyangjun/peach-dependencies`；**Gitee 镜像** 推送地址：`git@gitee.com:leiyangjun/peach-dependencies.git`（网页：<https://gitee.com/leiyangjun/peach-dependencies>；与 Central 元数据无冲突）。
3. **正式发布**建议使用 **非 SNAPSHOT** 版本号（例如 `1.0.0`）；`SNAPSHOT` 行为与私服策略以 Sonatype 文档为准。
4. 本机已安装 **GPG**，且公钥已上传到公钥服务器，供 Central 校验签名。
5. 在本机 **Maven `settings.xml`**（如 `%USERPROFILE%\.m2\settings.xml` 或 `%MAVEN_HOME%\conf\settings.xml`）中配置 `<server><id>central</id>...</server>` 的 Central 凭据，且与 `pom.xml` 中 **`distributionManagement` / `central-publishing-maven-plugin` 的 `publishingServerId`** 一致（当前均为 **`central`**）。

### 推送命令

在项目根目录执行：

```bash
mvn clean deploy -Prelease-sign
```

说明：

- **`-Prelease-sign`**：激活 `release-sign` Profile，执行 **GPG 签名**（`maven-gpg-plugin`）。
- **`deploy`** 阶段由 **`central-publishing-maven-plugin`** 将构件发布至 **Sonatype Central Portal**（与 `distributionManagement` 中 URL 一致）。

若仅本地验证发布流水线而不实际上传（视插件与权限而定），可先执行 **`mvn clean package -Prelease-sign`** 做部分检查。

---

## 开源协议说明

本工程在 `pom.xml` 中声明采用：

**Apache License, Version 2.0**

全文见：<https://www.apache.org/licenses/LICENSE-2.0.txt>

在遵守 Apache 2.0 条款的前提下，你可以使用、修改和再分发本 BOM；**本 BOM 所管理的第三方组件**各自遵循其原项目许可证（如 Spring、Alibaba、GPL/LGPL 等），请在业务系统中自行审阅合规性。

---

## 版本管理说明（与 `pom.xml` 中 `properties` 对齐）

| 属性 / 含义 | 当前版本 |
|-------------|----------|
| Java | **21** |
| Spring Boot（`spring-boot-dependencies`） | **4.0.2** |
| Spring Cloud（`spring-cloud-dependencies`） | **2025.1.1** |
| Spring Cloud Alibaba（`spring-cloud-alibaba-dependencies`） | **2025.1.0.0** |
| Druid Spring Boot Starter | **1.2.27** |
| SpringDoc OpenAPI（Boot 4 线） | **3.0.1** |
| Knife4j BOM | **4.5.0** |
| MyBatis Spring Boot Starter | **3.0.5** |
| MyBatis Generator | **1.4.2** |
| Jasypt Spring Boot Starter | **4.0.4** |
| Fastjson2 | **2.0.61** |
| EasyExcel | **4.0.3** |
| Redisson Spring Boot Starter | **3.52.0** |
| ZXing | **3.5.4** |
| Hutool BOM | **5.8.35** |
| MapStruct | **1.6.3** |
| Lombok | **1.18.42** |
| Commons Lang3 | **3.18.0** |

**说明**：`spring-boot-dependencies`、`spring-cloud-dependencies`、`spring-cloud-alibaba-dependencies`、`knife4j-dependencies`、`hutool-bom` 以 **BOM import** 方式纳入；其中 **Redis / RabbitMQ / Kafka** 等 Starter 的版本由 **Spring Boot BOM** 托管，本 README 不一一列举，请在 IDE 或 `mvn dependency:tree` 中查看解析结果。

---

## 组件说明（本 BOM 管理的范围）

| 类别 | 说明 |
|------|------|
| **Spring Boot** | 官方 BOM，统一 Boot 4 及官方 Starter、测试、Kafka、Redis、RabbitMQ 等版本。 |
| **Spring Cloud** | 官方 BOM，统一 2025.1 线下各子项目版本。 |
| **Spring Cloud Alibaba** | 官方 BOM，统一 Nacos、Sentinel、Seata、RocketMQ 等阿里微套件与部分关联库版本。 |
| **Knife4j** | OpenAPI 文档 UI 相关 BOM。 |
| **SpringDoc** | OpenAPI 3 / Spring Boot 4 适配的 Starter 族（WebMVC / WebFlux 等）版本锁定。 |
| **Druid / MyBatis / Jasypt** | 数据源、ORM、配置加解密（**`jasypt-spring-boot-starter`**）等常用扩展的版本锁定。 |
| **Fastjson2 / EasyExcel** | 阿里系 JSON 与 Excel 处理库版本锁定。 |
| **Redisson** | Redis 客户端增强（与 Spring Data 大版本搭配使用前请查阅 Redisson 官方说明）。 |
| **Hutool / MapStruct / Lombok / Commons Lang3 / ZXing** | 工具类、映射、Lombok、字符串与二维码等常用库版本锁定。 |

---

## 常见问题

**Q：为什么仓库里有一个 `package-info.java`？**  
A：Maven Central 发布通常需要 **`-sources.jar` / `-javadoc.jar`**；在仅含 BOM 的工程里增加最小 Java 包便于生成 javadoc 构件，属发布合规用途。

**Q：`0.0.1-SNAPSHOT` 能否直接推到 Central？**  
A：一般 **正式发布** 使用 **RELEASE** 版本更稳妥；`SNAPSHOT` 是否允许、是否需单独快照仓，以 Sonatype 当前政策为准。

---

## 维护

- **维护者**：leiyangjun  
- **与 `pom.xml` 同步**：升级依赖时请同时更新本 README 中的版本表与组件说明。
