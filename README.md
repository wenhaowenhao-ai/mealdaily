# 每日餐食 (MealDaily)

> 每日推送菜谱，自己做菜的 iOS 本地化应用 — 2菜1主食1饮品，全苹果原生扁平风，无联网，小组件同步

## 功能概览

| 功能 | 说明 |
|------|------|
| 每日推送 | 11:00 午餐 / 17:00 晚餐本地通知，周六额外饮品推送 |
| 标准搭配 | 2热菜 + 1主食 + 1饮品（周六：2菜 + 1主食 + 2饮品） |
| 就餐人数 | 自主选择 1-10 人，成本自动按比例调整 |
| 菜品数据库 | 内置 2154 道菜（含主食、饮品），覆盖 15 个菜系 |
| 三向互通 | 随机生成 / 搜索自定义 / 菜库选择替换 |
| 自定义菜品 | 手动新增菜品入库，与内置菜品同等使用 |
| 打卡系统 | 5级评价（夯→拉）+ 备注 + 拍照 + 食材步骤 + 评价，可删 |
| 家人同步 | 通过 iCloud (CloudKit) 多设备同步推送 |
| 小组件 | 桌面 Widget 显示今日餐食（小/中/大三种尺寸） |
| 4Tab 架构 | 今日餐食 / 菜库 / 打卡 / 其他 |

## 菜品数据统计

```
总菜品数：2154 道
├── 热菜：1318 道
├── 凉菜：389 道
├── 汤羹：209 道
├── 主食：195 道
└── 饮品：43 道

覆盖菜系：15 个
├── 川菜（200）  鲁菜（176）  粤菜（158）  苏菜（152）  浙菜（151）
├── 西北菜（139） 新疆菜（138） 闽菜（137）  湘菜（137）  云贵菜（134）
└── 徽菜 / 京菜 / 东北菜 / 客家菜 / 湖北菜
```

每道菜包含：菜名、图片(emoji)、菜系、省份、口味、原材料、大概成本、烹饪时间、难度、做菜步骤、抖音/小红书搜索链接。

## 项目结构

```
MealDaily/
├── MealDaily/                        # 主 App
│   ├── MealDailyApp.swift            # App 入口
│   ├── Info.plist                    # 配置文件
│   ├── MealDaily.entitlements        # 权限配置
│   ├── Models/
│   │   ├── Dish.swift                # 菜品模型 + Color扩展
│   │   ├── MealPlan.swift            # 每日餐食计划模型
│   │   ├── CheckIn.swift             # 打卡记录模型
│   │   └── AppState.swift            # 全局状态管理
│   ├── Views/
│   │   ├── ContentView.swift         # 4Tab 主框架
│   │   ├── TodayMeals/
│   │   │   └── TodayMealsView.swift  # 今日餐食页（搜索+三向互通）
│   │   ├── RecipeLibrary/
│   │   │   └── RecipeLibraryView.swift # 菜库（筛选+新增自定义）
│   │   ├── CheckIn/
│   │   │   └── CheckInView.swift     # 打卡（5级评价+拍照+记录）
│   │   ├── Other/
│   │   │   └── OtherView.swift       # 设置（推送/同步/偏好）
│   │   └── Components/
│   │       ├── DishCardView.swift    # 菜品卡片组件
│   │       ├── DishDetailView.swift  # 菜品详情弹窗
│   │       └── RatingStars.swift     # 评分组件+人数选择器
│   ├── Services/
│   │   ├── DishDatabase.swift        # (数据加载在 AppState 中)
│   │   ├── MealGenerator.swift       # 餐食生成器（随机/替换）
│   │   ├── NotificationService.swift # 本地通知服务
│   │   ├── CloudKitSync.swift        # 家人 iCloud 同步
│   │   └── WidgetDataManager.swift   # Widget 数据共享 (★ 两个Target共享)
│   └── Resources/
│       └── dishes_database.json      # 2154道菜品数据
├── MealDailyWidget/                  # Widget Extension
│   ├── MealDailyWidget.swift         # 小组件（小/中/大）
│   ├── Info.plist
│   └── MealDailyWidget.entitlements
├── generate_dishes.py                # 菜品数据库生成脚本
└── README.md
```

## 本地部署步骤

### 第一步：创建 Xcode 项目

1. 打开 Xcode → New → Project
2. 选择 **iOS** → **App**
3. 填写：
   - Product Name: `MealDaily`
   - Interface: **SwiftUI**
   - Language: **Swift**
   - Storage: **None**（使用 UserDefaults）
   - Include Tests: 可选
4. 保存到任意目录

### 第二步：导入源码

1. 将 `MealDaily/` 目录下所有 `.swift` 文件拖入 Xcode 项目
   - 勾选 **Copy items if needed**
   - 勾选 **Create groups**
   - 确保 **Add to target: MealDaily** 已勾选
2. 将 `Resources/dishes_database.json` 拖入项目
   - 勾选 **Copy items if needed**
   - 确保 **Add to target: MealDaily** 已勾选
3. 替换 `Info.plist` 为项目中的版本
4. 删除 Xcode 自动生成的 `ContentView.swift` 和 `MealDailyApp.swift`（已被替换）

### 第三步：添加 Widget Extension

1. Xcode 菜单 → File → New → Target
2. 选择 **Widget Extension**
3. 填写：
   - Product Name: `MealDailyWidget`
   - Language: Swift
   - Include Configuration App Intent: **不勾选**
4. 删除自动生成的 `MealDailyWidget.swift`
5. 将项目中的 `MealDailyWidget/MealDailyWidget.swift` 拖入
6. **关键**：将以下共享文件同时添加到 Widget Extension Target：
   - `Models/Dish.swift`
   - `Models/MealPlan.swift`
   - `Services/WidgetDataManager.swift`
   - 在 File Inspector → Target Membership → 勾选 `MealDailyWidget`

### 第四步：配置 App Group

1. 打开 https://developer.apple.com → Certificates, Identifiers & Profiles
2. **App Groups** → 新建 → Identifier: `group.com.mealdaily.app`
3. **Identifiers** → 你的 App ID → 编辑 → 勾选 App Groups → 选择上面的 Group
4. Xcode 中：
   - 主 App Target → Signing & Capabilities → + Capability → App Groups → 添加 `group.com.mealdaily.app`
   - Widget Target → 同样添加 App Group

### 第五步：配置 CloudKit（家人同步，可选）

1. **Identifiers** → App ID → 勾选 **iCloud** → 编辑 → 选择 CloudKit
2. Xcode 中：
   - 主 App Target → Signing & Capabilities → + Capability → iCloud → 勾选 CloudKit
   - Container: `iCloud.com.mealdaily.app`
3. 添加 `MealDaily.entitlements` 中的 iCloud 配置

### 第六步：配置通知权限

通知权限已在 `Info.plist` 中配置，首次启动 App 时会请求授权。

### 第七步：构建运行

1. 连接 iPhone（需 iOS 17.0+）
2. 选择你的开发团队
3. Build & Run (⌘R)
4. 首次运行会请求通知权限 → 点击 **允许**

## 关键技术实现

### 1. 本地通知（无需联网）
- `UNCalendarNotificationTrigger` 实现每日定时推送
- 午餐 11:00 / 晚餐 17:00 重复触发
- 周六 15:00 额外饮品推送

### 2. 家人多设备同步
- `CloudKit` + `CKQuerySubscription` 实现静默推送
- 一台设备生成餐食计划 → 写入 iCloud → 其他设备收到推送
- 纯 Apple 原生方案，无第三方服务器

### 3. Widget 小组件
- `WidgetKit` + `StaticConfiguration`
- 通过 App Group 共享数据
- 三种尺寸：小（菜品预览）/ 中（完整列表）/ 大（含食材详情）
- 主 App 更新计划时自动刷新 Widget

### 4. 菜品数据库
- Python 脚本生成 2154 道菜品 JSON
- 八大菜系 + 地方特色菜系
- 每道菜含完整信息：菜名、emoji、菜系、省份、口味、食材、成本、步骤、视频链接

### 5. 三向互通替换
- **随机替换**：同类别菜品随机更换
- **搜索自定义**：通过搜索找到菜品替换
- **菜库选择**：从菜库浏览选择替换

### 6. 打卡系统
- 5级评价：夯(1★) → 顶级(2★) → 人上人(3★) → NPC(4★) → 拉(5★)
- 支持拍照（PhotosPicker）
- 完整记录菜品快照（含食材步骤）
- 可删除记录

## 系统要求

- iOS 17.0+
- Xcode 15.0+
- Swift 5.9+
- iCloud 账号（家人同步功能）
- Apple Developer 账号（真机部署）

## 重新生成菜品数据库

如需修改或扩充菜品数据库：

```bash
cd MealDaily
python3 generate_dishes.py
```

生成的 JSON 文件会覆盖 `MealDaily/Resources/dishes_database.json`。

## 隐私说明

- 所有数据存储在设备本地（UserDefaults）
- 菜品数据库内置，无需联网下载
- 家人同步通过 Apple iCloud（端到端加密）
- 无任何第三方数据采集
- 无网络风控风险
