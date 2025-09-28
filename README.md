## 个性化旅游管理系统

一个包含前端、后端与数据库脚本的完整示例项目，用于实现基于用户偏好与行为数据的个性化旅游推荐、游记展示、评论与评分等功能。本仓库同时提供多数据源支持：MySQL（业务主库）与 PostgreSQL/PostGIS（地理位置/导航相关）。

### 目录结构
- `travel-system/`：前端（Vue 3）源码与已构建产物（`dist/`）
- `travel-system(后端)/travel/demo(1)/demo/`：业务后端（Spring Boot 3）MySQL 版
- `travel-system(后端-导航)/place/`：导航/地理后端（Spring Boot 2.7 + PostGIS）PostgreSQL 版
- `database-MySQL/`：MySQL 建表与示例数据脚本
- `database-PostgreSQL/`：PostgreSQL 备份/脚本
- `travel-media/` 与 `diary示例/`：示例图片/视频/文本资源

### 技术栈
- 前端：Vue 3、Vue Router、Vuex、Axios、Leaflet 与 @vue-leaflet/vue-leaflet
- 后端（业务）：Spring Boot 3.4.5、Spring Web、Spring Data JPA、MySQL、Hibernate Validator、WebSocket（已引入依赖）
- 后端（导航/地理）：Spring Boot 2.7.6、Spring Web、Spring Data JPA、PostgreSQL、Hibernate Spatial（PostGIS）
- 其他：Kotlin（少量配置/类，Kotlin Maven 插件）、缩略图/图片处理（Thumbnailator）

### 功能概览
- 个性化推荐：基于用户行为与兴趣标签的景点/美食推荐（见业务后端的 `RecommendationController`/`Service`）
- 游记与多媒体：上传/展示游记、图片与视频（静态资源目录与压缩工具已内置）
- 评论与评分：对游记与景点评论、评分
- 学校/景点数据：查询展示（`SpotSchoolController` 等）
- 导航/地理：基于 PostGIS 的地点与空间数据支持（独立导航后端）

---

## 本地开发与运行

### 先决条件
- Node.js 16+ 与 npm
- JDK 17 与 Maven 3.8+
- MySQL 8+（用于业务后端）
- PostgreSQL 13+ 与 PostGIS 扩展（用于导航后端）

### 数据库初始化
1) MySQL：
   - 启动 MySQL，创建数据库（如 `demo`）。
   - 进入 `database-MySQL/database-mysql/`，按需导入以下脚本：
     - `demo_tb_user.sql`、`demo_tb_food.sql`、`demo_tb_travel_diary.sql`、`demo_tb_comment.sql`、`demo_tb_rating.sql`、`demo_tb_user_behavior.sql`、`demo_tb_user_interest_tag.sql`、`demo_tb_diary_media.sql`、`demo_tb_diary_tag.sql`、`demo_tb_spot_school.sql` 等。
   - 根据需要可调整数据或新增索引。

2) PostgreSQL（含 PostGIS）：
   - 启动 PostgreSQL，确保安装并启用 PostGIS 扩展。
   - 创建数据库（如 `qinghua`），并在该库中 `CREATE EXTENSION postgis;`。
   - 可参考 `database-PostgreSQL/backup(1).sql` 进行恢复或导入。

### 配置后端
1) 业务后端（MySQL）：`travel-system(后端)/travel/demo(1)/demo/`
   - 配置文件：`src/main/resources/application.properties`
   - 关键默认配置（请按本地环境修改）：
     - `spring.datasource.url=jdbc:mysql://localhost:3306/demo?useSSL=false&serverTimezone=UTC`
     - `spring.datasource.username` / `spring.datasource.password`
     - `spring.jpa.hibernate.ddl-auto=none`
     - `server.port=9090`
     - `app.upload-dir` 静态资源/媒体上传目录（默认示例：`D:\\travel-media`，Windows 注意转义）

2) 导航后端（PostgreSQL + PostGIS）：`travel-system(后端-导航)/place/`
   - 配置文件：`src/main/resources/application.properties`
   - 关键默认配置（请按本地环境修改）：
     - `server.port=8085`
     - `spring.datasource.url=jdbc:postgresql://localhost:5432/qinghua`
     - `spring.datasource.username=postgres`
     - `spring.datasource.password=11223344`
     - `spring.jpa.database-platform=org.hibernate.spatial.dialect.postgis.PostgisDialect`
     - `spring.jpa.hibernate.ddl-auto=none`

### 启动后端服务
在各自模块目录下执行：
```bash
# 业务后端（MySQL）
cd "travel-system(后端)/travel/demo(1)/demo"
mvn spring-boot:run

# 导航后端（PostgreSQL + PostGIS）
cd "travel-system(后端-导航)/place"
mvn spring-boot:run
```

默认访问：
- 业务后端 API：`http://localhost:9090`
- 导航后端 API：`http://localhost:8085`

若需打包：
```bash
mvn clean package -DskipTests
```

### 启动前端
方式一：源码开发模式
```bash
cd travel-system
npm install
npm run serve
```
默认开发地址：`http://localhost:8080`

方式二：使用已构建产物
- 直接通过 `travel-system/dist/` 下的 `index.html` 提供静态资源（需结合任意静态服务器）。

### 静态资源与上传
- 示例媒体位于：`travel-media/travel-media/uploads/` 与根级 `travel-media/`。
- 业务后端通过 `app.upload-dir` 指定上传目录；请确认该目录存在且应用对其有读写权限。

---

## 常见问题与提示
- 端口冲突：调整 `server.port`（前端 8080、业务后端 9090、导航后端 8085 可按需变更）。
- CORS 跨域：如前端直连后端 API，请确保后端已放开对应跨域策略，或通过本地开发代理解决。
- 数据库驱动：确保 MySQL 与 PostgreSQL 驱动版本与数据库实际版本兼容。
- PostGIS：导航能力依赖 PostGIS，请在 PostgreSQL 目标库中启用扩展。
- Windows 路径：`app.upload-dir` 等路径注意使用双反斜杠或前缀 `\\`。

---

## 主要模块与代码位置（快速索引）
- 前端页面与组件：`travel-system/src/`
- 业务后端控制器：`travel-system(后端)/travel/demo(1)/demo/src/main/java/com/example/demo/Controller/`
- 业务实体/仓储/服务：`Entity/`、`Repository/`、`Service/`
- 导航后端入口：`travel-system(后端-导航)/place/src/main/java/`

---

## 许可与版权
本项目用于学习与教学示例，数据与媒体文件仅作演示用途。请在遵守相关法律与许可证的前提下使用、修改与分发。


