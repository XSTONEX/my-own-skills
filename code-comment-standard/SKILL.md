---
name: code-comment-standard
description: Use when writing, rewriting, or reviewing code comments, docstrings, or comment style guides, especially when the user wants Chinese comments, function-level and block-level comment rules, punctuation constraints, explicit subject, plain-language wording, or TODO/FIXME conventions
---

# Code Comment Standard

## Overview

This skill helps write comments that explain intent instead of translating code line by line.

默认语言是中文简明体，优先写给维护代码的人看，尽量短、直接、具体
如果项目已经有明确的英文注释规范，先跟随项目现有约定

## When to Use

Use this skill when the user asks to:
- define a comment style guide
- rewrite or review code comments
- standardize docstrings, block comments, or TODO/FIXME notes
- make comments shorter, clearer, or more consistent
- enforce Chinese comment style with concrete examples

## Core Rules

### 1. Function-Level Comments

核心业务函数、长函数、对外 API 接口，函数定义下方要紧跟块注释
Python 优先用 docstring，其他语言用等价的多行注释形式

注释长度要按函数复杂度决定，不要所有函数都套 1/2/3 编号格式：
- 简单函数、薄封装、单一判断：一句话说明业务目的或关键原因即可
- 中等复杂函数：用 1-2 句话说明目的和核心做法
- 长函数、多阶段流程、对外 API：先写业务目的，再用列表说明核心步骤

只有当函数内部确实存在多个有维护价值的步骤时，才使用 1. 2. 3. 编号
不要为了凑格式拆出“获取参数、调用函数、返回结果”这类代码字面步骤

#### Good example — 简单函数一句话即可

```python
def is_valid_chunk_index(chunk_index: int) -> bool:
    """
    判断 chunk_index 是否可用于分片处理，系统只允许非负索引进入后续缓存和合并流程
    """
```

#### Good example — 多阶段流程才使用编号

```python
def sync_meeting_chunks(payload):
    """
    校验会议音频分片并写入缓存
    1. 检查 chunk_index 是否重复、越界、断号
    2. 将合法分片写入临时存储
    3. 当分片齐全时触发合并流程
    """
```

#### Good example — 类级别 docstring，带分节说明

```python
class TaskScheduler:
    """
    异步任务调度器，按优先级和依赖关系编排执行顺序

    核心保证：
    1. 同一任务不会被重复调度
    2. 依赖未完成时自动挂起，依赖就绪后立即唤醒
    3. 调度失败时回退到上一个稳定状态

    性能特点：
    - 任务入队和出队均为 O(log n)
    - 内部用最小堆而非排序列表，避免大量任务时退化
    """
```

#### Good example — 先说约束再说做法

```python
def upload_attachment(file_bytes, filename):
    """
    上传附件到对象存储，返回可访问的 CDN 地址

    限制：单文件不超过 50MB，超过需走分片上传接口
    格式：仅允许 pdf/docx/png/jpg，其余格式直接拒绝

    内部流程：
    - 校验文件大小和 MIME 类型
    - 生成唯一 object_key 写入 S3
    - 写入上传记录，便于后续审计和清理过期文件
    """
```

#### Good example — 简洁的"目的 + 原因"风格

```python
def refresh_token_if_needed(session):
    """
    检查当前 session 的 access_token 是否即将过期，提前续签

    提前 5 分钟续签而非等到过期，是为了避免并发请求在 token 失效瞬间
    集中触发续签，导致下游 Auth 服务被打满
    """
```

#### Bad example

```python
def sync_meeting_chunks(payload):
    """
    处理分片数据
    """
```

Why this is bad:
- 只说了“处理”，没有说明业务目的
- 没有写出核心步骤
- 对维护者没有决策价值

#### Good example — 多阶段业务函数

```python
def send_verification_email(user_email):
    """
    发送登录验证邮件，并记录投递结果便于排查失败
    1. 生成一次性验证码
    2. 调用邮件服务发送验证码
    3. 记录发送结果，便于排查投递失败
    """
```

#### Bad example — 简单函数不应强行编号

```python
def build_meeting_key(meeting_id: str) -> str:
    """
    生成会议缓存 key
    1. 接收 meeting_id
    2. 拼接 meeting 前缀
    3. 返回缓存 key
    """
```

Why this is bad:
- 函数只有一个直接动作，编号是在逐行翻译代码
- 注释比代码更啰嗦，增加维护负担
- 没有提供额外的业务判断价值

### 2. Block-Level Comments

禁止逐行翻译代码
注释必须按“代码块”来写，放在整块逻辑的最上方，先说这段逻辑的整体意图

#### Good example

```python
# 检查 chunk_index 是否合法，包括是否重复、是否连续、是否小于 0
if payload.chunk_index < 0:
    raise ValueError("invalid chunk_index")
if payload.chunk_index in self._received_chunk_indices:
    raise ValueError("duplicate chunk_index")
```

#### Bad example

```python
# 如果 chunk_index 小于 0 就报错
if payload.chunk_index < 0:
    raise ValueError("invalid chunk_index")

# 如果 chunk_index 已经存在就报错
if payload.chunk_index in self._received_chunk_indices:
    raise ValueError("duplicate chunk_index")
```

Why this is worse:
- 变成逐行说明，信息密度低
- 没有把这两段代码合成一个逻辑块来解释

#### Another good example

```python
# 先清掉旧缓存，避免重试请求把上一轮的中间状态带进来
cache.clear()
```

### 3. Punctuation Rules

注释里不要在末尾或中间使用中文句号「。」
如果一句话没说完，可以用逗号、分号、空格分隔
结尾可以直接留空，保持短促和干净

#### Good examples

```python
# 检查 chunk_index 是否连续，避免乱序分片污染合并结果
# 由于下游 API 响应很慢，这里把超时临时放宽到 5 秒
# 先写入临时目录，等全部成功后再做原子替换
```

#### Bad example

```python
# 检查 chunk_index 是否连续，避免乱序分片污染合并结果。
```

### 4. Explicit Subject

注释必须有明确的主语
不要写模糊的被动句，也不要让读者猜是谁在做事

#### Good examples

```python
# 系统校验用户传入的登录凭证
# 网关拦截未认证的请求
# 后端合并所有分片后再写入正式文件
```

#### Bad examples

```python
# 用户信息在这里进行校验
# 被处理了
# 这里做认证检查
```

Why this matters:
- 主语不清会让注释像口头记录
- 读者看完还是不知道谁在做什么

### 5. No Over-Engineering Jargon

不要写抽象黑话，不要把一个简单动作包装成学术表达
用程序员一眼能懂的话写

同时避免使用团队内部缩写或自造词，这些词对新人、其他团队成员和未来的自己都不友好
把背后的实际动作写出来，比如“切窗”应写成“按时间窗口切分音频”、“落库”应写成“写入数据库”

#### Good examples

```python
# 用 is_playing 标记当前是否正在播放音频
# 用 retry_count 记录失败后重试了几次
# 用 has_synced 标记这一轮是否已经同步完成
# 供本地按时间窗口切分音频使用
# 将处理结果写入数据库，供后续查询
```

#### Bad examples

```python
# 此处的 bool 变量作为当前上下文中音频流控制的状态机驱动源
# 该字段承担异步编排中的事件驱动角色
# 供本地切窗流程使用
# 落库后通知下游
```

### 6. Focus on Why

代码字面意思已经很明显时，不要重复“是什么”
优先解释“为什么要这么做”，尤其是业务死角、临时规避、兼容旧逻辑、绕开第三方问题时

#### Good examples

```python
# 由于下游第三方 API 响应极慢，这里把超时时间临时放宽到 5 秒
# 先保留旧字段，避免老客户端升级前解析失败
# 这里先落盘再回写状态，防止进程中断后数据丢失
```

#### Bad examples

```python
# 将超时时间设置为 5 秒
# 保存用户信息
# 更新状态
```

Why this matters:
- 代码已经说明“做了什么”
- 注释应该补上“为什么不能直接按常规写法做”

### 7. Language and Terminology Consistency

注释统一使用中文简明体
代码里的变量名、字段名、协议名、专业术语，直接沿用原词，不要硬翻译

#### Good examples

```python
# 检查 chunk_index 是否合法
# 根据 sha1_hash 判断文件是否重复
# 使用 meeting_id 关联这次同步结果
```

#### Bad examples

```python
# 检查块索引是否合法
# 根据散列值判断文件是否重复
# 使用会议编号关联这次同步结果
```

Why this matters:
- 原词更容易和代码、日志、接口字段对上
- 统一术语能减少维护时的认知成本

### 8. TODO / FIXME

如果代码里有临时改动、技术债、未完成逻辑，必须用统一格式标出来
同时写清触发条件，不要只留一句空 TODO

#### Good examples

```python
# TODO(travis): 待前端接入大文件分片后，这里改成流式写入
# FIXME(zhangsan): 目前先跳过空白行，等上游修复后再恢复严格校验
# TODO(li): 等会议回放接口稳定后，把这里的轮询改成事件推送
```

#### Bad examples

```python
# TODO
# FIXME
# 以后再优化
```

Why this matters:
- 只有作者名没有触发条件，后面没人知道什么时候该改
- 只有触发条件没有责任人，后续也不好追踪

## Practical Writing Template

### For function-level comments

Use one of these shapes:

简单函数：

```python
"""
<一句话说明业务目的或关键原因>
"""
```

中等复杂函数：

```python
"""
<业务目的>

<核心做法或关键原因>
"""
```

多阶段流程函数：

```python
"""
<业务目的>
1. <有维护价值的核心步骤一>
2. <有维护价值的核心步骤二>
3. <有维护价值的核心步骤三>
"""
```

先选最短能讲清楚的形状，只有多阶段流程才用编号列表

### For block-level comments

Use this shape:

```python
# <这段代码块的整体意图，最好带上为什么这么做>
```

## Quick Checklist

写完注释后，快速检查这 8 件事：
- [ ] 关键函数都有紧跟的块注释或 docstring
- [ ] 注释写了业务目的，不只是复述代码
- [ ] 函数注释按复杂度选择长度，简单函数不要强行写 1/2/3
- [ ] 没有中文句号「。」
- [ ] 每条注释都有明确主语
- [ ] 没有抽象黑话
- [ ] 讲清了为什么这样做
- [ ] TODO / FIXME 带作者和触发条件

## Suggested Default Position on Rules 6-8

规则 6、7、8 默认适合大多数团队场景
如果仓库里已经有更强的本地规范，优先跟随本地规范，再用这套规则做补充
这样能保住统一性，也不会和既有代码风格打架
