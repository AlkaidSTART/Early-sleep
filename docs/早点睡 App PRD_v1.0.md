# **早点睡 (Sleep Early) \- 详细产品需求文档 (PRD) v1.0**

---

# **1\. 文档概述与版本历史**

| 版本 | 更新日期 | 更新内容概述 | 状态 |
| :---- | :---- | :---- | :---- |
| v0.1 | 2026-10-12 | 初始需求梳理：系统 API 督促、暖色氛围、白噪音 | 草案 |
| v0.2 | 2026-10-12 | 确立分级督促（温和/严格）、沉浸视觉白噪音、虚拟宠物与社交监督 | 讨论中 |
| v0.3 | 2026-10-12 | 增加底层技术规避与 Android 国内各厂商状态栏私有 SDK 适配矩阵 | 评审中 |
| v0.4 | 2026-10-12 | 引入系统级线性马达触觉反馈（Haptics）规范与全局交互细节 | 评审中 |
| v0.5 | 2026-10-12 | 确立夜间全局状态机、MVP 核心数据模型 (Prisma) 与原型结构 | 评审中 |
| **v1.0** | **2026-10-12** | **全量执行级充实：用户旅程、渐进式权限漏斗、详细数值模型、防作弊与边界容灾、REST/WS 接口规范、埋点监控体系** | **正式执行版** |

---

# **2\. 用户画像与端到端用户旅程 (User Persona & Journey)**

## **2.1 典型用户画像**

1. **林同学（21岁，大学生 / 考研备考）**：  
   - **痛点**：睡前习惯性在床上刷短视频、玩手机到凌晨 2 点，明知第二天要早起但控制不住，充满自责与焦虑。  
   - **核心诉求**：需要硬性物理手段强力切断娱乐 App，需要情感寄托与陪伴感，喜欢可爱的虚拟宠物打卡养成。  
2. **张工（25岁，互联网高压打工人）**：  
   - **痛点**：长期加班，睡前大脑神经高度兴奋难以入眠，对刺眼的手机冷光极其敏感，容易失眠。  
   - **核心诉求**：暖色温暗屏、无杂质的沉浸式低频白噪音、4-7-8 呼吸减压，快速平稳入睡。  
3. **小周与小陈（异地恋情侣）**：  
   - **痛点**：经常因谁熬夜晚睡而互相担心，缺少一个共同遵守的健康契约和睡前仪式感。  
   - **核心诉求**：能实时看到对方是否安睡，对方熬夜时能“隔空强力催促”，次日早晨能互道早安并共同养成宠物。

## **2.2 24小时端到端用户旅程 (User Journey Map)**

\[白天 IDLE\] 

  └── 打开主页：抚摸小精灵（触觉微震） \-\> 查看昨夜睡眠达标报告与精力值 \-\> 查看伴侣状态

\[21:30 睡前准备 WIND\_DOWN 激活\]

  └── 距离目标入睡时间 30 分钟：系统发送柔和推送，灵动岛/厂商状态栏浮现倒计时胶囊

  └── 界面主色温平滑渐变为暗琥珀暖光（剥离蓝光刺激）

  └── 用户点击【进入睡前准备】：开启 4-7-8 呼吸法（手机随呼吸引导节律性脉冲震动）

\[22:00 目标入睡时间到达 IN\_SLEEP\]

  └── 严格模式生效：Screen Time API 锁闭黑名单 App

  └── 用户点击【进入梦乡】-\> 手机进入暗屏守护模式（KeepAwake 防熄屏，OLED 极暗时钟，小精灵戴睡帽打呼噜）

  └── 耳机/外放流淌出定制暴雨/篝火双轨混音白噪音，设定 30 分钟平滑线性淡出

\[夜间微醒\]

  └── 凌晨 3:00 用户醒来碰触屏幕：10% 微弱亮度的呼吸时钟显示时间，90 秒内不计违规，防误触长滑块防止误关

\[07:00 唤醒与结算 WAKE\_UP\]

  └── App 内部高音量渐进闹铃唤醒（避开系统静音限制）

  └── 解除应用限制，生成昨夜睡眠成绩单：

      \- 睡眠达标！小精灵获得 100 睡眠能量点，解锁新睡帽外观

      \- 向伴侣端同步推送：“对方已按时醒来，快给 Ta 留句早安便签吧！”

---

# **3\. 冷启动与渐进式权限获取漏斗 (Onboarding & Permission Funnel)**

为了避免用户在初次打开 App 时因“权限弹窗连环轰炸”而导致 60%+ 的流失率，本产品设计**场景触发式（Just-in-Time）渐进授权策略**。

| 步骤 | 触发时机 | 索取权限 | 授权理由说明页 (Pre-permission Modal) | 拒权降级与挽回方案 |
| :---- | :---- | :---- | :---- | :---- |
| **Step 1** | 新手引导第一步 | **系统通知 (Notification)** | “让小精灵能在入睡前 30 分钟轻轻提醒你该准备休息了” | 若拒绝：仅能在 App 前台运行时接收倒计时，主页顶部红点提示补开。 |
| **Step 2** | 设置目标睡眠时间时 | **后台活动与厂商状态栏 (OEM SDK / Live Activity)** | “在屏幕顶部常驻入睡胶囊，让你不用频繁切入 App 也能掌控时间” | 若拒绝：仅使用普通单次锁屏横幅通知，不常驻胶囊。 |
| **Step 3** | 用户勾选【严格模式】时 | **Screen Time / Family Controls (iOS) 或使用情况访问 (Android)** | “为了帮你斩断短视频与游戏诱惑，我们将准时为你锁闭选定应用” | 若拒绝：自动回退至【温和模式】，并在模式选择旁标注文案：“需授权以解锁硬核防沉迷”。 |
| **Step 4** | 开启白噪音与夜间守护时 | **系统震动 (Vibrate) & 防熄屏 (KeepAwake)** | “同步呼吸脉冲节奏，并在夜间为你保持微光守护” | 默认集成无感授权，支持在设置页手动开关触觉反馈。 |

---

# **4\. 核心功能深度规格说明 (Detailed Feature Specs)**

## **4.1 分级睡眠督促系统**

### **4.1.1 严格模式应用拦截 (Screen Time Shield UI)**

- **屏蔽规则**：  
  - 用户可在设置页勾选多达 20 个高频耗时 App（如短视频、社交、游戏类 App 等）。  
  - 到达 `IN_SLEEP` 阶段后，iOS 触发 `ManagedSettingsStore().shield.applications`，Android 通过原生服务阻断启动并弹起覆盖层。  
- **拦截页设计规范 (Shield View)**：  
  - **背景**：暗黑色沉浸夜空，中央浮现抱枕沉睡的虚拟小精灵。  
  - **文案**：动态文案随机呈现（例如：“说好今晚早睡的，小精灵已经睡着啦”、“放下手机吧，明天太阳升起前没有什么事情是非看不可的”）。  
  - **紧急解除机制 (Emergency Breakout)**：  
    - 每日仅限 **1 次** 紧急解除机会。  
    - **防冲动门槛**：点击解除后，必须完成一段强制性 **60 秒专注 4-7-8 呼吸测试** 或 **12 点连线记忆防解压拼图**。如果在倒计时未结束前退出，解除失败。  
    - **社交同步**：若触发紧急解除，系统自动向绑定伴侣发送一条通知：“对方刚刚消耗了紧急通行证，熬夜使用了手机！”。  
- **系统级绝对白名单（不可拦截）**：  
  - 系统拨号（Phone）、紧急呼叫（SOS）、短信（Messages）、系统设置（Settings）。

---

## **4.2 沉浸式视听白噪音与混音引擎**

### **4.2.1 双轨立体声混音器 (Multi-track Audio Mixer)**

白噪音不是单调的单一循环音频，提供环境声与细节声的动态双轨调节：

1. **场景一：深夜暴雨 (Midnight Downpour)**  
   - **基础轨 (Base Track)**：中高频密集的低沉窗外雨声（无缝平滑 Loop，无爆音节点）。  
   - **细节轨 (Accent Track)**：低频远雷声（随机 45\~90 秒间隔淡入）+ 窗户微风震动拟真音。  
2. **场景二：山野篝火 (Campfire Solitude)**  
   - **基础轨 (Base Track)**：深林夜风与微弱虫鸣。  
   - **细节轨 (Accent Track)**：干燥木柴燃烧时的噼啪爆裂声（随机间隔触发）。

### **4.2.2 渐弱淡出曲线 (Audio Fade-out Curve)**

- 用户可设置播放时长：默认 30 分钟（可选 15 / 30 / 60 分钟 / 整夜播放）。  
- 在倒计时最后 10 分钟，音量遵循 **对数递减衰减曲线 (Logarithmic Decay)** 从 100% 平滑过渡至 0%，防止突然停音惊醒浅睡眠阶段的用户。

### **4.2.3 视觉粒子引擎与 OLED 防烧屏微位移机制 (Pixel Shift)**

- 雨滴与火光粒子采用微轻量 GPU 渲染，粒子帧率动态降频至 30fps 以节省电量。  
- **防烧屏算法 (Pixel Shift)**：在暗屏守护模式下，中心时钟与小精灵图标每隔 180 秒在 X 轴与 Y 轴方向各偏移 2\~4 个物理像素，避免 OLED 屏幕长时间静态显示产生局部残影。

---

## **4.3 睡眠氛围与全局触觉反馈矩阵 (Haptics Spec)**

为规范全 App 的触觉质感，制定统一的 Haptic 震动调用字典：

| 触发场景 | iOS 原生 API 映射 | Android API 映射 (VibrationEffect) | 触觉感知特征 |
| :---- | :---- | :---- | :---- |
| **下拉刷新触发点** | `UIImpactFeedbackGenerator(style: .light)` | `createOneShot(10, 50)` | 类似机械齿轮咬合的轻微卡顿阻尼感 |
| **核心按钮点击 (CTA)** | `UIImpactFeedbackGenerator(style: .medium)` | `createOneShot(20, 100)` | 干净利落的实体按键按压反馈 |
| **4-7-8 吸气阶段** | 连续 4 秒线性增强脉冲（从每秒 1 次增至 3 次） | 自定义波形（振幅逐渐增强） | 引导胸腔扩张与专注力提升 |
| **4-7-8 屏气阶段** | 静默无震动 | 静默无震动 | 引导心率放缓，身心静止 |
| **4-7-8 呼气阶段** | 连续 8 秒平缓递减微脉冲 | 自定义波形（振幅逐渐衰减） | 伴随气流呼出的肌肉彻底放松感 |
| **抚摸虚拟宠物** | `UIImpactFeedbackGenerator(style: .soft)` 伴随心跳频率 | 双轻触序列 `[0, 15, 60, 15]` | 模拟猫咪呼噜声或平稳的生命心跳 |
| **社交催睡强提醒 (Nudge)** | `UINotificationFeedbackGenerator(type: .error)` 重复 3 组 | 强力长震动序列 `[0, 300, 100, 300, 100, 500]` | 无法被轻易忽视的高辨识度物理打断 |

---

# **5\. 虚拟宠物成长数值体系 (Pet System Mechanics & Economy)**

## **5.1 宠物生命周期形态**

\[Level 1: 睡眠精灵蛋\] ──(累积 300 能量)──\> \[Level 2: 幼年呼呼兽\] 

                     ──(累积 1000 能量)──\> \[Level 3: 成年夜幕猫头鹰\] 

                     ──(累积 3000 能量)──\> \[Level 4: 究极星空造梦灵\]

## **5.2 数值模型与打卡计算公式**

### **1\. 基础睡眠能量获取规则**

\$\$\\text{每日获得能量} \= \\text{基础点数 (Base)} \\times \\text{准时系数} \\times \\text{连续打卡倍率 (Streak Multiplier)}\$\$

- **基础点数**：每次达成目标并完成睡眠即得 **50 能量点**。  
- **准时系数 (Punctuality Factor)**：  
  - 提前 15 分钟至准时进入 `IN_SLEEP`：系数 \= **1.2**（奖励提前准备）  
  - 准时进入至延迟 15 分钟内：系数 \= **1.0**  
  - 延迟 15 \~ 30 分钟：系数 \= **0.6**  
  - 延迟超过 30 分钟：系数 \= **0.0**（不发基础能量）  
- **连续打卡倍率 (Streak Multiplier)**：  
  - 连续 1\~3 天：\$\\times 1.0\$  
  - 连续 4\~7 天：\$\\times 1.2\$  
  - 连续 8\~14 天：\$\\times 1.5\$  
  - 连续 15 天以上：\$\\times 2.0\$（最高上限）

### **2\. 熬夜惩罚与疲劳机制 (Fatigue Mechanism)**

- 若当晚严重违规（延迟超 1 小时未睡、或中途强退 App 刷手机超 3 分钟）：  
  - **连续打卡计数归零**。  
  - 宠物进入 **“疲惫状态 (isFatigued \= true)”**：头顶冒黑线，闭眼打哈欠，主页抚摸互动无活力反馈。  
  - 惩罚机制**不扣除宠物已有等级与历史能量积累**（避免挫败感过强导致用户直接卸载），只需次日准时入睡即可消除疲劳态。

---

# **6\. 双人社交监督契约规范 (Social Protocol)**

1. **绑定机制**：  
   - 用户 A 生成 6 位随机邀请码（有效时间 24 小时）。  
   - 用户 B 输入邀请码，双方建立 1对1 强监督关联。  
2. **实时状态广播 (Realtime Broadcast)**：  
   - 客户端通过 WebSocket 长连接（断线自动轮询 Redis）向对方推送状态：  
     - `OFFLINE`（离线）  
     - `ACTIVE_DAY`（白天活跃）  
     - `WINDING_DOWN`（正在睡前准备）  
     - `ASLEEP`（已进入梦乡）  
     - `AWAKE_VIOLATION`（超写入睡时间仍在玩手机）  
3. **“一键催睡”防骚扰频控机制**：  
   - 仅在对方处于 `AWAKE_VIOLATION` 或 `WINDING_DOWN` 超时阶段可点击。  
   - **单夜催促上限**：每晚最多发送 **3 次**。  
   - **冷却时间 (Cooldown)**：每次发送后强制冷却 **15 分钟**，避免恶意连击骚扰。  
4. **早安便签 (Morning Memo)**：  
   - 晨间醒来结算后，支持向伴侣发送一张预设卡片或 20 字以内的温情问候，展示在对方今日结算页第一屏。

---

# **7\. 夜间状态机与异常边界情况处理 (Edge Cases & Fallbacks)**

## **7.1 异常场景矩阵与容灾方案**

| 异常场景 | 表现情况 | 处理与降级策略 |
| :---- | :---- | :---- |
| **半夜微醒看时间** | 用户拿起手机晃动或轻点屏幕 | 仅点亮极暗时钟界面。若持续操作时间 **\< 90 秒**，不计入中断；超过 90 秒判定为夜间清醒，记录微中断次数。 |
| **突发紧急电话** | 半夜有电话接入或系统闹钟响起 | 原生音频中断监听 (`AVAudioSessionInterruption`) 自动暂停白噪音；系统白名单允许通话正常接听，通话结束后白噪音平滑淡入恢复。 |
| **飞行模式 / 断网关机** | 用户睡前开启飞行模式逃避监督 | 客户端采用本地时间戳 \+ 随机盐（Salt）生成防篡改 Hash 会话存入 SQLite。次日连网后服务端比对 Hash，防止篡改本地时间作弊。 |
| **手机低电量** | 电量低于 10% 且未插充电器 | App 自动弹出提示：“电量较低，建议连接充电器以确保闹钟正常工作”；若电量低于 5%，自动关闭白噪音并降级为纯待机模式。 |
| **跨时区旅行 / 夏令时** | 用户在飞行或跨时区出差 | 目标睡眠时间绑定为设备本地时区（Local Timezone），根据定位变更自动提醒用户是否调整入睡基准时间。 |

---

# **8\. 完整核心数据模型 (Prisma Schema 生产级全量)**

datasource db {

  provider \= "postgresql"

  url      \= env("DATABASE\_URL")

}

generator client {

  provider \= "prisma-client-js"

}

// 1\. 用户核心表

model User {

  id              String         @id @default(uuid())

  phone           String?        @unique

  nickname        String         @db.VarChar(50)

  avatarUrl       String?

  timezone        String         @default("Asia/Shanghai")

  createdAt       DateTime       @default(now())

  updatedAt       DateTime       @updatedAt

  sleepGoal       SleepGoal?

  petState        PetState?

  sessions        SleepSession\[\]

  sentRelations   Relationship\[\] @relation("UserSender")

  receivedRelations Relationship\[\] @relation("UserReceiver")

  eventLogs       AnalyticsEvent\[\]

}

// 2\. 睡眠目标与管控策略表

model SleepGoal {

  id              String   @id @default(uuid())

  userId          String   @unique

  targetBedtime   String   // "23:00"

  targetWakeTime  String   // "07:30"

  mode            String   @default("GENTLE") // "GENTLE" | "STRICT"

  windDownMinutes Int      @default(30)

  whiteNoiseScene String   @default("RAINSTORM") // "RAINSTORM" | "CAMPFIRE"

  hapticsEnabled  Boolean  @default(true)

  blockedApps     Json?    // 屏蔽的应用 bundleId / 包名数组

  user            User     @relation(fields: \[userId\], references: \[id\], onDelete: Cascade)

}

// 3\. 睡眠单次会话流水表

model SleepSession {

  id                String    @id @default(uuid())

  userId            String

  targetDate        DateTime  @db.Date // 归属的打卡日期

  startTime         DateTime  // 进入 IN\_SLEEP 的时间

  endTime           DateTime? // 醒来结算时间

  actualSleepMinutes Int      @default(0)

  status            String    // "SUCCESS" | "FAILED" | "INTERRUPTED"

  interruptionCount Int       @default(0)

  emergencyBreakout Boolean   @default(false) // 是否触发紧急解除

  energyEarned      Int       @default(0)

  hashSignature     String?   // 本地防作弊防篡改签名

  createdAt         DateTime  @default(now())

  user              User      @relation(fields: \[userId\], references: \[id\], onDelete: Cascade)

  @@index(\[userId, targetDate\])

}

// 4\. 虚拟宠物状态表

model PetState {

  id              String   @id @default(uuid())

  userId          String   @unique

  petType         String   @default("SPIRIT\_EGG") // 宠物种类

  level           Int      @default(1)

  currentEnergy   Int      @default(0)

  streakDays      Int      @default(0) // 连续打卡天数

  isFatigued      Boolean  @default(false)

  unlockedOutfits Json     @default("\[\]") // 已解锁装扮数组

  updatedAt       DateTime @updatedAt

  user            User     @relation(fields: \[userId\], references: \[id\], onDelete: Cascade)

}

// 5\. 契约监督关系表

model Relationship {

  id              String    @id @default(uuid())

  senderId        String

  receiverId      String

  status          String    @default("PENDING") // "PENDING" | "ACTIVE" | "REVOKED"

  nudgeCountToday Int       @default(0)

  lastNudgeAt     DateTime?

  morningNote     String?   @db.VarChar(100) // 晨间互赠便签

  createdAt       DateTime  @default(now())

  updatedAt       DateTime  @updatedAt

  sender          User      @relation("UserSender", fields: \[senderId\], references: \[id\], onDelete: Cascade)

  receiver        User      @relation("UserReceiver", fields: \[receiverId\], references: \[id\], onDelete: Cascade)

  @@unique(\[senderId, receiverId\])

}

// 6\. 行为埋点日志表

model AnalyticsEvent {

  id        String   @id @default(uuid())

  userId    String

  eventName String   @db.VarChar(50)

  payload   Json?

  timestamp DateTime @default(now())

  user      User     @relation(fields: \[userId\], references: \[id\], onDelete: Cascade)

  @@index(\[eventName, timestamp\])

}

---

# **9\. 服务端 API 与 WebSocket 通信设计**

## **9.1 RESTful 核心接口**

### **1\. 睡眠会话上报与结算**

- **Endpoint**: `POST /api/v1/sleep/session/settle`  
- **Request Body**:  
    
  {  
    
    "sessionId": "b8a5c3d2-4e6f-4a1b-9c2d-8e7f6a5b4c3d",  
    
    "startTime": "2026-10-12T23:02:10Z",  
    
    "endTime": "2026-10-13T07:15:30Z",  
    
    "interruptionCount": 1,  
    
    "emergencyBreakout": false,  
    
    "hashSignature": "a4f89b...e71c"  
    
  }  
    
- **Response Body**:  
    
  {  
    
    "code": 200,  
    
    "data": {  
    
      "status": "SUCCESS",  
    
      "energyEarned": 120,  
    
      "currentStreak": 5,  
    
      "streakMultiplier": 1.2,  
    
      "petLeveledUp": false,  
    
      "petCurrentEnergy": 420,  
    
      "summaryMessage": "太棒了！已连续早睡 5 天，小精灵充满活力！"  
    
    }  
    
  }

### **2\. 社交催睡强提醒**

- **Endpoint**: `POST /api/v1/social/nudge`  
- **Request Body**:  
    
  {  
    
    "relationshipId": "rel\_123456"  
    
  }  
    
- **Response Body (正常)**:  
    
  {  
    
    "code": 200,  
    
    "data": {  
    
      "remainingNudges": 2,  
    
      "nextAvailableAt": "2026-10-12T23:45:00Z"  
    
    }  
    
  }  
    
- **Response Body (频控拦截)**:  
    
  {  
    
    "code": 429,  
    
    "message": "催得太频繁啦，给对方一点准备入睡的时间吧（冷却中 12 分钟）"  
    
  }

## **9.2 WebSocket 实时事件规范 (信道: `/ws/v1/stream`)**

| 事件名 (Event) | 触发方向 | Payload 结构 | 说明 |
| :---- | :---- | :---- | :---- |
| `status:sync` | Client \-\> Server | `{"status": "WINDING_DOWN"}` | 客户端状态变更广播 |
| `partner:update` | Server \-\> Client | `{"partnerId": "...", "status": "ASLEEP"}` | 伴侣状态变更即时推达 |
| `nudge:incoming` | Server \-\> Client | `{"fromName": "...", "timestamp": "..."}` | 触发高强度震动与强提醒横幅 |

---

# **10\. 埋点与核心指标监控体系 (Analytics & Metrics)**

## **10.1 北极星业务指标 (North Star Metrics)**

- **NSM**：**7日入睡达标留存率 (7-Day Sleep Compliance Retention)**  
- **健康度指标**：  
  - 严格模式开启渗透率（Strict Mode Adoption Rate）\> 35%  
  - 紧急解除触发率（Emergency Breakout Rate）\< 8%（越低说明阻断越成功）  
  - 双人监督契约绑定率（Social Pair Rate）\> 50%  
  - 平均睡前延时（Average Wind-down Delay）从基线 45 分钟缩短至 15 分钟以内

## **10.2 核心事件埋点清单 (Event Tracking Schema)**

| 事件标识符 (Event ID) | 触发时机 | 关键采集属性 (Properties) |
| :---- | :---- | :---- |
| `app_onboarding_perm_grant` | 用户在权限引导页点击授权 | `permission_type`, `is_granted`, `step_index` |
| `wind_down_start` | 进入睡前准备状态 | `trigger_type` (auto/manual), `mode` |
| `sleep_session_start` | 点击【进入梦乡】激活暗屏 | `mode`, `target_bedtime`, `delay_minutes` |
| `emergency_unlock_attempt` | 严格模式下尝试解锁 | `method` (breath\_test/pattern), `is_success` |
| `white_noise_play` | 开启白噪音 | `scene` (rain/campfire), `timer_duration` |
| `pet_petting_interact` | 主页点击抚摸小精灵 | `pet_level`, `is_fatigued`, `haptic_triggered` |
| `social_nudge_send` | 点击一键催睡 | `remaining_quota`, `recipient_status` |
| `morning_settle_view` | 晨间查看打卡结算单 | `status`, `energy_earned`, `streak_days` |

