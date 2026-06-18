# PlayAndroid 精简入门与代码导读

> 这份文档适合第一次接触这个项目的同学阅读。
>
> 内容分成两部分：
> 1. 先用最少的概念帮你快速看懂项目整体结构
> 2. 再按核心 Kotlin 文件顺序做“代码导读”，帮助你知道每个文件是干什么的

---

# 一、适合入门学习的精简版笔记

## 1. 这个项目是什么

这是一个用 Jetpack Compose 写的 Android 应用示例项目，不是传统 XML 布局，而是用 Kotlin 直接声明界面。

它主要展示了这些 Android 现代技术：

- Jetpack Compose
- Hilt
- Navigation Compose
- Paging 3
- DataStore
- Retrofit
- Glance 小组件

## 2. 项目怎么分层

这个项目大致分成四层：

- `app`  
  负责界面、入口、导航、页面逻辑

- `model`  
  负责数据模型，比如文章、登录状态、查询条件

- `network`  
  负责网络请求

- `utils`  
  负责工具方法，比如日志、Toast、DataStore、图片加载等

你可以先把它理解成：

- `app` 管“页面”
- `model` 管“数据”
- `network` 管“从服务器拿数据”
- `utils` 管“各种辅助功能”

## 3. 页面是怎么启动的

启动入口是 `MainActivity`。

它做的事很简单：

1. 进入 Compose 界面
2. 套上主题
3. 把导航交给 `NavGraph`
4. 如果收到了文章数据，就跳转到文章详情页

一句话理解：
`MainActivity` 就是整个 App 的总入口。

## 4. 页面是怎么切换的

页面跳转主要由 `NavGraph` 管理。

它像一张“路线图”：

- 首页
- 搜索页
- 登录页
- 主题页
- 体系页
- 文章详情页

所有页面跳转逻辑都统一放在这里，避免散落在各处。

## 5. 数据是怎么流动的

一般是这样的顺序：

`页面 -> ViewModel -> Repository -> Network`

也就是说：

- 页面不直接请求网络
- 页面先把需求交给 ViewModel
- ViewModel 再去调仓库层
- 仓库层再去调用网络接口或分页数据源

这样做的好处是结构清晰，代码也更容易维护。

## 6. 为什么 Compose 会自动刷新

因为 Compose 是“状态驱动 UI”。

你只要改了状态：

- 当前 tab
- 文章列表
- 登录状态
- 搜索内容
- 主题颜色

界面就会自动重新绘制，不需要手动去找控件改值。

## 7. 这个项目里最重要的几个概念

你先把这些词认识一下：

- `@Composable`  
  表示这是一个 Compose 函数，可以用来画界面

- `Modifier`  
  控制大小、间距、点击、布局等

- `State / Flow`  
  状态数据，变了以后界面会跟着变

- `ViewModel`  
  管数据和业务逻辑

- `NavController`  
  管页面跳转

- `Hilt`  
  管依赖注入

- `PagingData`  
  分页加载数据

- `DataStore`  
  保存本地设置，比如主题

## 8. 初学者最该先建立的认识

你看这个项目时，不要先陷入每个函数细节，先建立这条主线：

1. App 从 `MainActivity` 启动  
2. `NavGraph` 管页面  
3. `MainPage` 管首页框架  
4. 各页面通过 ViewModel 拿数据  
5. 数据从 Repository / Network 来  
6. 状态变化后 Compose 自动刷新

这就是整个项目的核心流程。

---

# 二、代码导读

下面按文件顺序讲核心 Kotlin 文件。每个文件我都尽量用“它是什么、它做什么、怎么理解”来解释。

## 1. `app/src/main/java/com/zj/play/MainActivity.kt`

### 它是什么

应用入口 Activity。

### 它做什么

- 调用 `setContent {}` 进入 Compose 世界
- 套用应用主题
- 处理传进来的 `ArticleModel`
- 把页面导航交给 `NavGraph`
- 在退出时清理 Toast

### 怎么理解

它就是 App 的“总开关”。

你可以把它看作：
- 负责开机
- 负责把主页面装进去
- 负责接收外部传参

## 2. `app/src/main/java/com/zj/play/ui/main/NavGraph.kt`

### 它是什么

整个 App 的导航中心。

### 它做什么

- 定义首页、搜索页、登录页、主题页、文章页等路由
- 处理路由参数
- 做页面切换动画
- 如果收到文章数据，就自动进入文章详情

### 怎么理解

它像“地图导航系统”。

你要从 A 去 B，不需要到处找跳转代码，统一在这里管理。

## 3. `app/src/main/java/com/zj/play/ui/main/MainPage.kt`

### 它是什么

App 的主框架页。

### 它做什么

- 显示底部导航
- 根据当前选中的 tab 切换页面
- 横屏时改成左右分栏布局
- 首页、体系页、项目页、公众号页、我的页都在这里切换

### 怎么理解

它像“首页容器”。

真正内容不是写在一个大页面里，而是由它根据 tab 选择不同内容。

## 4. `app/src/main/java/com/zj/play/logic/viewmodel/BaseArticleViewModel.kt`

### 它是什么

文章分页相关 ViewModel 的基类。

### 它做什么

- 接收查询条件 `Query`
- 发起分页请求
- 对重复搜索做去重
- 暴露分页结果给页面使用

### 怎么理解

它是“数据控制中枢”。

页面只负责展示，具体怎么搜索、怎么分页、怎么避免重复请求，交给它。

## 5. `model/src/main/java/com/zj/model/PlayState.kt`

### 它是什么

统一的状态封装类。

### 它做什么

它定义了几种常见状态：
- `PlayLoading`
- `PlaySuccess`
- `PlayError`
- `PlayDefault`

### 怎么理解

它是异步任务的“状态语言”。

比如请求接口时：
- 先是加载中
- 成功后显示数据
- 失败后显示错误
- 默认状态表示还没开始

## 6. `model/src/main/java/com/zj/model/ArticleModel.kt`

### 它是什么

文章数据模型。

### 它做什么

保存一篇文章的各种信息：
- 标题
- 作者
- 链接
- 是否收藏
- 发布时间
- 章节信息

### 怎么理解

它就是“文章对象长什么样”的定义。

页面列表、详情页、收藏页都会用到它。

## 7. `model/src/main/java/com/zj/model/Query.kt`

### 它是什么

查询条件模型。

### 它做什么

表示搜索或分类查询参数：
- `cid`
- `k`

### 怎么理解

它是 ViewModel 发请求时的入参。

比如：
- 搜索文章
- 按分类查文章

都可以用它来描述条件。

## 8. `model/src/main/java/com/zj/model/BaseModel.kt`

### 它是什么

网络返回的通用包装模型。

### 它做什么

统一封装接口响应：
- `data`
- `errorCode`
- `errorMsg`

### 怎么理解

它像一个“外壳”。

接口真正的数据都包在里面，很多接口都能共用这个结构。

## 9. `model/src/main/java/com/zj/model/AndroidSystemModel.kt`

### 它是什么

体系分类数据模型。

### 它做什么

表示一个体系分类节点，包括：
- 名称
- id
- 子节点
- 父节点
- 是否可见

### 怎么理解

它是“体系页”展示树状分类的核心数据结构。

## 10. `model/src/main/java/com/zj/model/SystemChildren.kt`

### 它是什么

体系子分类模型。

### 它做什么

描述系统分类下面的子节点。

### 怎么理解

它和 `AndroidSystemModel` 搭配使用，构成树形结构。

## 11. `network/src/main/java/com/zj/network/PlayAndroidNetwork.kt`

### 它是什么

网络请求统一入口对象。

### 它做什么

- 持有各种 service
- 对外提供统一的 suspend 接口
- 调用首页、项目、公众号、登录、搜索、体系等 API

### 怎么理解

它像“网络总调度员”。

上层不用关心具体 Service，直接调它就行。

## 12. `utils/src/main/java/com/zj/utils/DataStoreUtils.kt`

### 它是什么

DataStore 工具类。

### 它做什么

- 读取本地数据
- 写入本地数据
- 支持 Boolean、Int、String、Float、Long
- 同时提供同步和异步读写

### 怎么理解

它是本地配置存储工具。

比如主题、偏好设置、一些开关状态，都可能存这里。

## 13. `app/src/main/java/com/zj/play/ui/theme/*`

### 它是什么

主题相关代码。

### 它做什么

- 定义颜色体系
- 提供主题切换
- 根据当前设置决定 App 外观

### 怎么理解

它决定 App “长什么样”。

比如是普通主题、深色主题、还是灰度主题。

## 14. `app/src/main/java/com/zj/play/widget/*`

### 它是什么

桌面小组件相关代码。

### 它做什么

- 在桌面展示文章内容
- 支持点击后打开 App
- 和主应用共享文章数据

### 怎么理解

它让 App 不只存在于打开后的界面里，还能出现在桌面上。

## 15. `app/src/main/java/com/zj/play/logic/repository/*`

### 它是什么

仓库层。

### 它做什么

- 组织数据获取流程
- 把网络请求和分页请求包装起来
- 提供给 ViewModel 使用

### 怎么理解

它像“数据加工厂”。

ViewModel 不直接碰网络细节，而是通过 Repository 获取整理好的结果。

## 16. `app/src/main/java/com/zj/play/ui/page/home/*`

### 它是什么

首页相关页面。

### 它做什么

- 展示 banner
- 展示首页文章列表
- 展示首页布局和交互

### 怎么理解

这是用户最常看到的页面之一，也是项目里最适合先看的地方。

## 17. `app/src/main/java/com/zj/play/ui/page/system/*`

### 它是什么

体系页面相关代码。

### 它做什么

- 展示分类树
- 展示分类下文章
- 处理体系页的层级结构

### 怎么理解

这个页面适合帮助你理解“树状数据 + 列表展示”的 Compose 写法。

## 18. `app/src/main/java/com/zj/play/ui/page/project/*`

### 它是什么

项目和公众号文章列表页面。

### 它做什么

- 根据不同数据源展示文章列表
- 复用同一套列表页面
- 只是传入的数据不同

### 怎么理解

这是一个很好的“页面复用”示例。

同样一套 UI，换数据就能服务多个页面。

## 19. `app/src/main/java/com/zj/play/ui/page/search/*`

### 它是什么

搜索页。

### 它做什么

- 展示搜索框
- 展示热词
- 展示搜索历史或搜索结果
- 根据输入触发搜索

### 怎么理解

它是典型的“输入驱动状态变化”页面。

Compose 在这种场景下很自然。

## 20. `app/src/main/java/com/zj/play/ui/page/login/*`

### 它是什么

登录页和登录相关逻辑。

### 它做什么

- 处理用户名密码输入
- 发起登录请求
- 展示登录状态
- 管理登录过程中的状态流

### 怎么理解

它能帮助你理解表单类页面在 Compose 中怎么写。

## 21. `app/src/main/java/com/zj/play/ui/page/mine/*`

### 它是什么

我的页面 / 个人中心相关代码。

### 它做什么

- 显示个人设置
- 显示主题入口
- 显示一些个人功能

### 怎么理解

它通常是项目里比较轻量但很重要的入口页。

## 22. `app/src/main/java/com/zj/play/ui/view/*`

### 它是什么

通用 UI 组件目录。

### 它做什么

- 封装通用按钮
- 封装搜索栏
- 封装条目样式
- 封装一些可复用视图

### 怎么理解

这些是“可重复使用的积木”。

以后页面多了，靠它们减少重复写法。

---

# 三、建议的阅读顺序

如果你是新手，我建议按这个顺序读：

1. `MainActivity.kt`
2. `NavGraph.kt`
3. `MainPage.kt`
4. `PlayState.kt`
5. `ArticleModel.kt`
6. `Query.kt`
7. `BaseArticleViewModel.kt`
8. `HomePage` 相关文件
9. `Repository` 和 `PagingSource`
10. `theme`、`widget`、`utils`

---

# 四、最适合新手的理解方式

你可以把这个项目想成一条流水线：

- `MainActivity` 启动 App
- `NavGraph` 管页面跳转
- `MainPage` 管首页壳子
- 页面通过 `ViewModel` 拿数据
- `Repository` / `Network` 负责请求
- `model` 负责定义数据
- `utils` 负责辅助功能
- Compose 根据状态自动刷新界面

---

# 五、总结

这个项目是一个很完整的 Jetpack Compose 示例，适合用来学习：

- Compose 页面怎么写
- 页面怎么导航
- 状态怎么驱动 UI
- 数据怎么分层
- 网络和分页怎么组织
- 本地存储怎么接入
- 多模块项目怎么拆分

如果你先把“页面入口 -> 导航 -> 数据流 -> 状态刷新”这条主线看懂，再去看具体文件，会轻松很多。
