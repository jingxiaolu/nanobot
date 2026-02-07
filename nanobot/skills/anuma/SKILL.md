---
name: anuma
description: Optimize inference workloads by calling external ANUMA project for CPU/NUMA affinity tuning
metadata:
  category: optimization
  runtime: python
  external:
    project: ANUMA
    path_config_key: skills.anuma.project_path
    required_files:
      - scripts/collect_system_info.py
      - scripts/bindcore.sh
      - config/load_command.yaml
---

# ANUMA Skill - External Project Integration

This skill integrates with the external **ANUMA** project to optimize AI inference workloads through CPU core binding and NUMA affinity tuning.

## Prerequisites

**ANUMA must be installed separately**. This skill calls the external project via shell.

### Installation

1. Clone ANUMA to your preferred location:
   ```bash
   git clone https://github.com/your-org/ANUMA.git ~/workspace/ANUMA
   ```

2. Configure witty to know where ANUMA is located:
   Edit `~/.witty/config.json`:
   ```json
   {
     "skills": {
       "anuma": {
         "project_path": "/Users/lewis/workspace/ANUMA"
       }
     }
   }
   ```

## What This Skill Does

Optimizes inference performance through 5-phase workflow:
1. **Process Discovery** - Identify key inference processes/threads
2. **System Collection** - Gather NPU/CPU topology and affinity info
3. **Bottleneck Analysis** - Detect topology misalignment, cache contention
4. **Strategy Generation** - Create CPU/NUMA affinity strategy
5. **Implementation** - Execute taskset/numactl commands

## Natural Language Commands

Talk to witty naturally:

- "Optimize my inference workload"
- "Help me bind inference processes to specific cores"
- "Analyze NUMA topology for my AI inference"
- "Optimize vLLM performance"
- "Check and fix CPU affinity for NPU workloads"
- "Generate bindcore strategy for PID 1234"

## How It Works

witty invokes ANUMA via shell commands:

### Phase 2: System Information Collection
```bash
cd <configured_project_path> && \
python3 scripts/collect_system_info.py <tid1> <tid2> ... --md
```

### Phase 4: Generate Affinity Strategy
```bash
cd <configured_project_path> && \
# Generates scripts/bindcore.sh based on analysis
```

### Phase 5: Execute Strategy
```bash
cd <configured_project_path> && \
bash scripts/bindcore.sh
```

## Configuration

In `~/.witty/config.json`:

```json
{
  "skills": {
    "anuma": {
      "project_path": "/path/to/ANUMA",
      "load_command": "your-workload-command"
    }
  }
}
```

You can also set `load_command` in `ANUMA/config/load_command.yaml`.

## Requirements

- **Environment**: Linux
- **Tools**: `taskset`, `numactl`, `npu-smi`, `lscpu`, `ps`
- **Hardware**: NPU-equipped systems (for NPU topology analysis)

## Workflow Example

```
User: Optimize my inference workload
Witty: I'll help optimize your inference workload using ANUMA.
       What's the project path? (or uses configured path)

User: Use default
Witty: [Phase 1] Do you have specific PIDs/TIDs?

User: 1234, 1235, 1236
Witty: [Phase 2] Collecting system topology...
       Executing: cd /path/to/ANUMA && python3 scripts/collect_system_info.py 1234 1235 1236 --md

Witty: [Phase 3] Analyzing bottlenecks...
       Found: Operator dispatch threads not on optimal NUMA nodes

Witty: [Phase 4] Generating affinity strategy...
       Created scripts/bindcore.sh

Witty: [Phase 5] Execute optimization? (yes/no)
User: yes
Witty: Executing bindcore.sh...
       Optimization complete! Verification passed.
```

## Troubleshooting

- **"ANUMA not found"**: Verify `project_path` in config
- **npu-smi errors**: Ensure NPU drivers are installed
- **Permission errors**: May need sudo for some operations
- **No improvement**: Ensure workload is under pressure during analysis
