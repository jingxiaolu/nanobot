# Witty 技能使用指南

## 技能概览

Witty 集成了以下专业技能：

| 技能 | 用途 | 类型 | 调用方式 |
|------|------|------|----------|
| **osmind** | 系统性能监控 | 外部 | "分析系统性能" |
| **anuma** | 推理优化 | 外部 | "优化推理工作负载" |

## 外部技能

OSMind 和 ANUMA 是**外部项目**，witty 通过 shell 调用进行集成。它们不包含在 witty 的代码库中，必须单独安装。

### 安装

1. **克隆外部项目**：
   ```bash
   # OSMind
   git clone https://github.com/your-org/OSMind.git ~/workspace/OSMind
   
   # ANUMA
   git clone https://github.com/your-org/ANUMA.git ~/workspace/ANUMA
   ```

2. **配置 witty** 以知道它们的位置：
   编辑 `~/.witty/config.json`：
   ```json
   {
     "skills": {
       "osmind": {
         "project_path": "/Users/lewis/workspace/OSMind"
       },
       "anuma": {
         "project_path": "/Users/lewis/workspace/ANUMA"
       }
     }
   }
   ```

## 自然语言调用

只需用自然语言与 Witty 对话：

### OSMind 技能
- "帮我检查系统资源使用情况"
- "分析 CPU 和内存瓶颈"
- "收集 2 分钟的性能指标"
- "监控磁盘和网络活动"

### ANUMA 技能
- "优化我的 vLLM 推理性能"
- "帮我绑定推理进程"
- "分析 NUMA 拓扑并给出优化建议"
- "检查 AI 推理的 CPU 亲和性"

## 工作原理

### OSMind 集成

当您调用 OSMind 技能时，witty：

1. 从配置中读取 `skills.osmind.project_path`
2. 构建 shell 命令：
   ```bash
   cd <project_path>/metrics-analysis && \
   python3 collector.py --duration 30 --interval 1.0 --format json
   ```
3. 通过 shell 执行
4. 解析输出并呈现结果

### ANUMA 集成

ANUMA 使用 5 阶段工作流：

1. **第 1 阶段：进程发现** - 识别关键 PIDs/TIDs
2. **第 2 阶段：系统收集** - 执行：
   ```bash
   cd <project_path> && \
   python3 scripts/collect_system_info.py <tid1> <tid2> ... --md
   ```
3. **第 3 阶段：瓶颈分析** - 分析结果
4. **第 4 阶段：策略生成** - 创建 `scripts/bindcore.sh`
5. **第 5 阶段：实施** - 执行：
   ```bash
   cd <project_path> && bash scripts/bindcore.sh
   ```

## 工作流示例

### 性能诊断流程

1. "系统有点慢，帮我看看" → Witty 调用 OSMind 收集指标
2. "分析一下结果" → Witty 分析瓶颈
3. "给出优化建议" → Witty 提供建议

### 推理优化流程

1. "我想优化推理工作负载" → Witty 启动 ANUMA 工作流
2. 提供关键 PIDs/TIDs → Witty 通过 ANUMA 收集系统信息
3. "分析瓶颈" → Witty 生成瓶颈报告
4. "生成绑核策略" → Witty 通过 ANUMA 创建 bindcore.sh
5. "执行优化" → Witty 应用策略

## 技能详情

### OSMind

**用途**：收集和分析系统性能指标（CPU、内存、磁盘、网络）

**外部项目**：OSMind/metrics-analysis

**主要功能**：
- 实时指标收集
- 带严重性级别的瓶颈识别
- 自动阈值检测（CPU > 95%，内存 > 90%）
- JSON 和文本输出格式

**配置**：
```json
{
  "skills": {
    "osmind": {
      "project_path": "/path/to/OSMind",
      "default_duration": 30,
      "default_interval": 1.0
    }
  }
}
```

**示例对话**：
```
用户：分析我的系统性能
Witty：我将使用 OSMind 收集 30 秒的系统指标...
[执行：cd /path/to/OSMind/metrics-analysis && python3 collector.py...]
分析完成！以下是发现：
- CPU：平均使用率 45%（正常）
- 内存：使用率 82%（警告 - 接近阈值）
- 磁盘：/dev/sda 检测到高 I/O 等待
建议：考虑升级内存或减少内存密集型进程。
```

### ANUMA

**用途**：通过 CPU 核心绑定和 NUMA 亲和性调优优化 AI 推理工作负载性能

**外部项目**：ANUMA/

**主要功能**：
- 5 阶段优化工作流
- NPU/CPU 拓扑分析
- 自动亲和性策略生成
- 支持 vLLM、vllm-ascend 等推理框架

**配置**：
```json
{
  "skills": {
    "anuma": {
      "project_path": "/path/to/ANUMA",
      "load_command": "your-benchmark-command"
    }
  }
}
```

**示例对话**：
```
用户：优化我的推理工作负载
Witty：我将使用 ANUMA 帮助您优化推理工作负载。
       项目路径是什么？（或使用配置的默认路径）

用户：使用默认
Witty: [第 1 阶段] 您有具体的 PIDs/TIDs 吗？

用户：1234, 1235, 1236
Witty: [第 2 阶段] 通过 ANUMA 收集系统拓扑...
       [执行：cd /path/to/ANUMA && python3 scripts/collect_system_info.py 1234 1235 1236 --md]

Witty: [第 3 阶段] 分析瓶颈...
       发现：算子下发线程不在最优 NUMA 节点上

Witty: [第 4 阶段] 通过 ANUMA 生成亲和性策略...
       已创建 scripts/bindcore.sh

Witty: [第 5 阶段] 是否执行优化？（是/否）
用户：是
Witty: [执行：cd /path/to/ANUMA && bash scripts/bindcore.sh]
       优化完成！验证通过。
```

## 故障排除

### OSMind
- **"未找到 OSMind"**：检查配置中的 `project_path`
- **"未找到 collector.py"**：确保 OSMind 克隆时包含 metrics-analysis 目录
- **权限被拒绝**：确保 OSMind 脚本可执行
- **缺少 Python 依赖**：安装 OSMind 的 requirements.txt

### ANUMA
- **"未找到 ANUMA"**：验证配置中的 `project_path`
- **npu-smi 错误**：确保已安装 NPU 驱动
- **权限错误**：某些操作可能需要 sudo
- **无改进**：确保分析期间工作负载有压力（设置 load_command）

## 与其他工具集成

两个技能可以协同工作：
1. 使用 OSMind 识别系统级瓶颈
2. 根据发现使用 ANUMA 优化推理工作负载
3. 重新运行 OSMind 验证改进效果

## 快速参考

### 常用命令

**系统监控**：
```
"检查系统性能"
"分析 CPU 和内存"
"收集系统指标 60 秒"
```

**推理优化**：
```
"优化推理性能"
"绑定 PID 1234 到核心 0-3"
"分析 NUMA 拓扑"
"生成绑核策略"
```

### 文件位置

- OSMind Skill：`nanobot/skills/osmind/SKILL.md`
- ANUMA Skill：`nanobot/skills/anuma/SKILL.md`
- 配置目录：`~/.witty/config.json`
- 外部项目：可配置（默认：`~/workspace/OSMind`、`~/workspace/ANUMA`）
