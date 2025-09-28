# AI儿童绘本有声读物微信小程序-后端设计文档

## 1. API设计

### 1.1 API设计原则

- RESTful风格设计
- 统一响应格式
- 版本化管理
- 幂等性保证
- 错误码规范化

### 1.2 API端点设计

#### 1.2.1 故事管理模块

**生成故事**

```
POST /api/v1/story/generate
```

请求参数:
```json
{
  "prompt_type": "preset|custom",
  "prompt_id": "string",
  "custom_prompt": "string",
  "language": "zh|en",
  "page_count": 6,
  "style": "cartoon|watercolor|sketch"
}
```

响应数据:
```json
{
  "code": 0,
  "message": "success",
  "data": {
    "story_id": "string",
    "title": "string",
    "pages": [
      {
        "page_number": 1,
        "content": "string",
        "image_url": "string",
        "audio_url": "string"
      }
    ],
    "created_at": "2025-01-01T00:00:00Z"
  }
}
```

**保存故事**

```
POST /api/v1/story/save
```

请求参数:
```json
{
  "story_id": "string",
  "title": "string",
  "pages": [],
  "metadata": {
    "prompt_id": "string",
    "custom_prompt": "string",
    "language": "zh|en",
    "page_count": 6,
    "style": "cartoon|watercolor|sketch"
  }
}
```

响应数据:
```json
{
  "code": 0,
  "message": "success",
  "data": {
    "story_id": "string",
    "title": "string",
    "pages": [
      {
        "page_number": 1,
        "content": "string",
        "image_url": "string",
        "audio_url": "string"
      }
    ],
    "created_at": "2025-01-01T00:00:00Z"
  }
}
```

**获取故事列表**

```
GET /api/v1/story/list
```

请求参数:
- page: 1
- page_size: 20
- user_id: string

响应数据:
```json
{
  "code": 0,
  "message": "success",
  "data": {
    "total": 100,
    "page": 1,
    "page_size": 20,
    "list": [
      {
        "story_id": "string",
        "title": "string",
        "cover_url": "string",
        "page_count": 6,
        "created_at": "2025-01-01T00:00:00Z",
        "updated_at": "2025-01-01T00:00:00Z"
      }
    ]
  }
}
```

**获取故事详情**

```
GET /api/v1/story/detail/{story_id}
```

**删除故事**

```
DELETE /api/v1/story/{story_id}
```

#### 1.2.2 音频服务模块

**文字转语音**

```
POST /api/v1/audio/tts
```

请求参数:
```json
{
  "text": "string",
  "language": "zh|en",
  "voice_type": "child|adult",
  "speed": 1.0
}
```

响应数据:
```json
{
  "code": 0,
  "message": "success",
  "data": {
    "audio_url": "string",
    "duration": 120,
    "format": "mp3"
  }
}
```

**获取音频URL**

```
GET /api/v1/audio/url/{audio_id}
```

**调整语速**

```
POST /api/v1/audio/speed
```

请求参数:
```json
{
  "audio_id": "string",
  "speed": 1.5
}
```

#### 1.2.3 翻译服务模块

**文本翻译**

```
POST /api/v1/translate
```

请求参数:
```json
{
  "text": "string",
  "source_lang": "en",
  "target_lang": "zh"
}
```

响应数据:
```json
{
  "code": 0,
  "message": "success",
  "data": {
    "translated_text": "string",
    "source_lang": "en",
    "target_lang": "zh"
  }
}
```

#### 1.2.4 用户管理模块

**用户登录**

```
POST /api/v1/user/login
```

请求参数:
```json
{
  "code": "wx_login_code"
}
```

响应数据:
```json
{
  "code": 0,
  "message": "success",
  "data": {
    "token": "string",
    "user_id": "string",
    "openid": "string",
    "session_key": "string"
  }
}
```

**更新用户信息**

```
PUT /api/v1/user/profile
```

请求参数:
```json
{
  "nickname": "string",
  "avatar_url": "string",
  "preferences": {
    "language": "zh",
    "voice_speed": 1.0,
    "show_translation": true
  }
}
```

**获取用户信息**

```
GET /api/v1/user/profile
```

#### 1.2.5 预设提示词模块

**获取预设提示词列表**

```
GET /api/v1/prompts/preset
```

响应数据:
```json
{
  "code": 0,
  "message": "success",
  "data": {
    "categories": [
      {
        "category_id": "string",
        "category_name": "经典童话",
        "prompts": [
          {
            "prompt_id": "string",
            "title": "小红帽的冒险",
            "description": "string",
            "tags": ["冒险", "勇敢"]
          }
        ]
      }
    ]
  }
}
```

### 1.3 错误码规范

| 错误码 | 说明 | HTTP状态码 |
|--------|------|------------|
| 0 | 成功 | 200 |
| 10001 | 参数错误 | 400 |
| 10002 | 认证失败 | 401 |
| 10003 | 权限不足 | 403 |
| 10004 | 资源不存在 | 404 |
| 10005 | 请求频率限制 | 429 |
| 20001 | AI生成失败 | 500 |
| 20002 | TTS转换失败 | 500 |
| 20003 | 翻译失败 | 500 |
| 30001 | 数据库错误 | 500 |
| 30002 | 文件上传失败 | 500 |

### 1.4 API版本管理策略

- URL版本控制: /api/v1/, /api/v2/
- 向后兼容原则
- 废弃API提前通知
- 版本切换过渡期

## 2. 数据库设计

### 2.1 数据模型设计

#### 2.1.1 用户表 (users)

```javascript
{
  _id: ObjectId,
  openid: String, // 微信openid
  unionid: String, // 微信unionid
  nickname: String, // 昵称
  avatar_url: String, // 头像
  gender: Number, // 性别
  city: String, // 城市
  province: String, // 省份
  country: String, // 国家
  language: String, // 语言偏好
  preferences: {
    voice_speed: Number, // 语速设置
    show_translation: Boolean, // 显示翻译
    auto_play_audio: Boolean, // 自动播放
    background_music: Boolean // 背景音乐
  },
  statistics: {
    total_stories: Number, // 总故事数
    total_reading_time: Number, // 总阅读时长
    last_active_time: Date // 最后活跃时间
  },
  created_at: Date,
  updated_at: Date
}
```

#### 2.1.2 故事表 (stories)

```javascript
{
  _id: ObjectId,
  story_id: String, // 故事唯一ID
  user_id: String, // 用户ID
  title: String, // 故事标题
  cover_url: String, // 封面图片
  language: String, // 语言
  style: String, // 绘画风格
  prompt: {
    type: String, // preset/custom
    prompt_id: String, // 预设提示词ID
    content: String // 提示词内容
  },
  pages: [{
    page_number: Number, // 页码
    content: String, // 文本内容
    translation: String, // 翻译内容
    image_url: String, // 图片URL
    audio_url: String, // 音频URL
    duration: Number // 音频时长
  }],
  metadata: {
    page_count: Number, // 总页数
    total_words: Number, // 总字数
    reading_time: Number, // 预计阅读时间
    generation_time: Number // 生成耗时
  },
  stats: {
    view_count: Number, // 查看次数
    play_count: Number, // 播放次数
    share_count: Number, // 分享次数
    like_count: Number // 点赞次数
  },
  status: String, // draft/published/deleted
  created_at: Date,
  updated_at: Date
}
```

#### 2.1.3 预设提示词表 (preset_prompts)

```javascript
{
  _id: ObjectId,
  prompt_id: String, // 提示词ID
  category_id: String, // 分类ID
  title: String, // 标题
  description: String, // 描述
  content: String, // 提示词内容
  tags: [String], // 标签
  difficulty: String, // 难度等级
  age_range: {
    min: Number,
    max: Number
  },
  usage_count: Number, // 使用次数
  rating: Number, // 评分
  is_active: Boolean, // 是否启用
  created_at: Date,
  updated_at: Date
}
```

#### 2.1.4 提示词分类表 (prompt_categories)

```javascript
{
  _id: ObjectId,
  category_id: String, // 分类ID
  name: String, // 分类名称
  description: String, // 分类描述
  icon: String, // 图标
  sort_order: Number, // 排序
  is_active: Boolean, // 是否启用
  created_at: Date,
  updated_at: Date
}
```

#### 2.1.5 音频资源表 (audio_resources)

```javascript
{
  _id: ObjectId,
  audio_id: String, // 音频ID
  story_id: String, // 关联故事ID
  page_number: Number, // 页码
  text_content: String, // 原文本
  audio_url: String, // 音频URL
  format: String, // 音频格式
  duration: Number, // 时长(秒)
  size: Number, // 文件大小(bytes)
  voice_type: String, // 语音类型
  language: String, // 语言
  speed: Number, // 语速
  created_at: Date,
  expires_at: Date // 过期时间
}
```

#### 2.1.6 用户行为日志表 (user_behaviors)

```javascript
{
  _id: ObjectId,
  user_id: String, // 用户ID
  action_type: String, // 行为类型
  target_type: String, // 目标类型
  target_id: String, // 目标ID
  details: Object, // 详细信息
  device_info: {
    platform: String, // 平台
    version: String, // 版本
    model: String // 设备型号
  },
  created_at: Date
}
```

### 2.2 索引设计

**用户表索引**
```javascript
db.users.createIndex({ "openid": 1 }, { unique: true })
db.users.createIndex({ "created_at": -1 })
```

**故事表索引**
```javascript
db.stories.createIndex({ "user_id": 1, "created_at": -1 })
db.stories.createIndex({ "story_id": 1 }, { unique: true })
db.stories.createIndex({ "status": 1 })
db.stories.createIndex({ "title": "text" })
```

**预设提示词表索引**
```javascript
db.preset_prompts.createIndex({ "category_id": 1 })
db.preset_prompts.createIndex({ "tags": 1 })
db.preset_prompts.createIndex({ "usage_count": -1 })
```

**音频资源表索引**
```javascript
db.audio_resources.createIndex({ "story_id": 1, "page_number": 1 })
db.audio_resources.createIndex({ "expires_at": 1 })
```

**用户行为日志表索引**
```javascript
db.user_behaviors.createIndex({ "user_id": 1, "created_at": -1 })
db.user_behaviors.createIndex({ "action_type": 1 })
```

### 2.3 数据迁移策略

#### 2.3.1 版本升级迁移

- 使用版本号标记数据结构
- 编写迁移脚本
- 支持回滚操作
- 迁移前备份

#### 2.3.2 数据备份方案

- 每日自动备份
- 增量备份机制
- 异地容灾备份
- 定期恢复测试

#### 2.3.3 数据归档策略

- 超过6个月的日志数据归档
- 已删除故事30天后清理
- 过期音频资源自动清理
- 冷数据迁移到低成本存储

## 3. 缓存设计

### 3.1 缓存策略

- 热点数据缓存（预设提示词）
- 用户会话缓存
- 故事内容缓存
- 音频URL缓存

### 3.2 缓存键设计

```
user:session:{user_id}        # 用户会话
story:detail:{story_id}       # 故事详情
prompt:preset:list            # 预设提示词列表
audio:url:{audio_id}          # 音频URL
translate:cache:{text_hash}   # 翻译缓存
```

### 3.3 缓存过期时间

- 用户会话: 7天
- 故事详情: 1小时
- 预设提示词: 24小时
- 音频URL: 30分钟
- 翻译缓存: 7天

## 4. 安全设计

### 4.1 接口鉴权

- Token验证机制
- 签名验证
- 时间戳校验
- 重放攻击防护

### 4.2 数据加密

- 敏感数据AES加密
- 传输层HTTPS
- 数据库字段加密
- 文件存储加密

### 4.3 访问控制

- 基于角色的权限控制
- API访问频率限制
- IP白名单（管理接口）
- 异常访问检测

## 5. 性能优化

### 5.1 查询优化

- 使用投影减少数据传输
- 分页查询优化
- 聚合管道优化
- 避免全表扫描

### 5.2 写入优化

- 批量写入操作
- 异步写入非关键数据
- 写入队列缓冲
- 分片写入策略

### 5.3 存储优化

- 图片CDN加速
- 音频文件压缩
- 冷热数据分离
- 定期数据清理