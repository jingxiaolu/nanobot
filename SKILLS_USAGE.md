# Witty Skills Usage Guide

## Skills Overview

Witty integrates the following professional skills:

| Skill | Purpose | Type | How to Invoke |
|-------|---------|------|---------------|
| **osmind** | System performance monitoring | external | "Analyze system performance" |
| **anuma** | Inference optimization | external | "Optimize inference workload" |

## External Skills

OSMind and ANUMA are **external projects** that witty integrates via shell calls. They are not included in witty's codebase and must be installed separately.

### Installation

1. **Clone the external projects**:
   ```bash
   # OSMind
   git clone https://github.com/your-org/OSMind.git ~/workspace/OSMind
   
   # ANUMA
   git clone https://github.com/your-org/ANUMA.git ~/workspace/ANUMA
   ```

2. **Configure witty** to know where they are located:
   Edit `~/.witty/config.json`:
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

## Natural Language Invocation

Simply talk to Witty in natural language:

### OSMind Skill
- "Help me check system resource usage"
- "Analyze CPU and memory bottlenecks"
- "Collect 2 minutes of performance metrics"
- "Monitor disk and network activity"

### ANUMA Skill
- "Optimize my vLLM inference performance"
- "Help me bind inference processes"
- "Analyze NUMA topology and give optimization recommendations"
- "Check CPU affinity for my AI inference"

## How It Works

### OSMind Integration

When you invoke OSMind skill, witty:

1. Reads `skills.osmind.project_path` from config
2. Constructs shell command:
   ```bash
   cd <project_path>/metrics-analysis && \
   python3 collector.py --duration 30 --interval 1.0 --format json
   ```
3. Executes via shell
4. Parses output and presents results

### ANUMA Integration

ANUMA uses a 5-phase workflow:

1. **Phase 1: Process Discovery** - Identify key PIDs/TIDs
2. **Phase 2: System Collection** - Execute:
   ```bash
   cd <project_path> && \
   python3 scripts/collect_system_info.py <tid1> <tid2> ... --md
   ```
3. **Phase 3: Bottleneck Analysis** - Analyze results
4. **Phase 4: Strategy Generation** - Create `scripts/bindcore.sh`
5. **Phase 5: Implementation** - Execute:
   ```bash
   cd <project_path> && bash scripts/bindcore.sh
   ```

## Workflow Examples

### Performance Diagnosis Workflow

1. "System is running slow, help me check" → Witty calls OSMind to collect metrics
2. "Analyze the results" → Witty analyzes bottlenecks
3. "Give optimization recommendations" → Witty provides suggestions

### Inference Optimization Workflow

1. "I want to optimize my inference workload" → Witty initiates ANUMA workflow
2. Provide key PIDs/TIDs → Witty collects system info via ANUMA
3. "Analyze bottlenecks" → Witty generates bottleneck report
4. "Generate affinity strategy" → Witty creates bindcore.sh via ANUMA
5. "Execute optimization" → Witty applies the strategy

## Skill Details

### OSMind

**Purpose**: Collect and analyze system performance metrics (CPU, memory, disk, network)

**External Project**: OSMind/metrics-analysis

**Key Features**:
- Real-time metrics collection
- Bottleneck identification with severity levels
- Automatic threshold detection (CPU > 95%, Memory > 90%)
- JSON and text output formats

**Configuration**:
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

**Example Session**:
```
User: Analyze my system performance
Witty: I'll collect system metrics using OSMind for 30 seconds...
[Executing: cd /path/to/OSMind/metrics-analysis && python3 collector.py...]
Analysis complete! Here are the findings:
- CPU: 45% average usage (Normal)
- Memory: 82% usage (Warning - approaching threshold)
- Disk: High I/O wait detected on /dev/sda
Recommendation: Consider upgrading RAM or reducing memory-intensive processes.
```

### ANUMA

**Purpose**: Optimize AI inference workload performance through CPU core binding and NUMA affinity tuning

**External Project**: ANUMA/

**Key Features**:
- 5-phase optimization workflow
- NPU/CPU topology analysis
- Automatic affinity strategy generation
- Support for vLLM, vllm-ascend, and other inference frameworks

**Configuration**:
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

**Example Session**:
```
User: Optimize my inference workload
Witty: I'll help you optimize your inference workload using ANUMA.
       What's the project path? (or uses configured path)

User: Use default
Witty: [Phase 1] Do you have specific PIDs/TIDs?

User: 1234, 1235, 1236
Witty: [Phase 2] Collecting system topology via ANUMA...
       [Executing: cd /path/to/ANUMA && python3 scripts/collect_system_info.py 1234 1235 1236 --md]

Witty: [Phase 3] Analyzing bottlenecks...
       Found: Operator dispatch threads not on optimal NUMA nodes

Witty: [Phase 4] Generating affinity strategy via ANUMA...
       Created scripts/bindcore.sh

Witty: [Phase 5] Execute optimization? (yes/no)
User: yes
Witty: [Executing: cd /path/to/ANUMA && bash scripts/bindcore.sh]
       Optimization complete! Verification passed.
```

## Troubleshooting

### OSMind
- **"OSMind not found"**: Check `project_path` in config
- **"collector.py not found"**: Ensure OSMind is cloned with metrics-analysis directory
- **Permission denied**: Ensure OSMind scripts are executable
- **Missing Python deps**: Install OSMind's requirements.txt

### ANUMA
- **"ANUMA not found"**: Verify `project_path` in config
- **npu-smi errors**: Ensure NPU drivers are installed
- **Permission errors**: May need sudo for some operations
- **No improvement**: Ensure workload is under pressure during analysis (set load_command)

## Integration with Other Tools

Both skills can work together:
1. Use OSMind to identify system-level bottlenecks
2. Use ANUMA to optimize inference workloads based on findings
3. Re-run OSMind to verify improvements

## File Locations

- OSMind Skill: `nanobot/skills/osmind/SKILL.md`
- ANUMA Skill: `nanobot/skills/anuma/SKILL.md`
- Configuration: `~/.witty/config.json`
- External Projects: Configurable (default: `~/workspace/OSMind`, `~/workspace/ANUMA`)
