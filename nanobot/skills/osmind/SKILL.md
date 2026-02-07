---
name: osmind
description: Collect and analyze system performance metrics by calling external OSMind project
metadata:
  category: monitoring
  runtime: python
  external:
    project: OSMind
    path_config_key: skills.osmind.project_path
    required_files:
      - metrics-analysis/collector.py
---

# OSMind Skill - External Project Integration

This skill integrates with the external **OSMind** project to provide system performance monitoring capabilities.

## Prerequisites

**OSMind must be installed separately**. This skill does not include OSMind code - it calls the external project.

### Installation

1. Clone OSMind to your preferred location:
   ```bash
   git clone https://github.com/your-org/OSMind.git ~/workspace/OSMind
   ```

2. Configure witty to know where OSMind is located:
   Edit `~/.witty/config.json`:
   ```json
   {
     "skills": {
       "osmind": {
         "project_path": "/Users/lewis/workspace/OSMind"
       }
     }
   }
   ```

## What This Skill Does

Provides system performance analysis by invoking OSMind's collector:
- **CPU Metrics**: Usage, load average, context switches
- **Memory Metrics**: Usage, swap, availability
- **Disk Metrics**: I/O, partition usage
- **Network Metrics**: Traffic, errors, connections

## Natural Language Commands

Simply talk to witty:

- "Analyze my system performance"
- "Check CPU and memory usage"
- "Collect system metrics for 60 seconds"
- "Identify performance bottlenecks"
- "Monitor my system resources"

## How It Works

When you invoke this skill via natural language, witty will:

1. **Read configuration** from `~/.witty/config.json` to find OSMind path
2. **Construct shell command** to run OSMind's collector
3. **Execute** via shell with appropriate parameters
4. **Parse results** and present analysis in natural language

### Shell Command Pattern

```bash
cd <configured_project_path>/metrics-analysis && \
python3 collector.py --duration <seconds> --interval <seconds> --format json
```

## Configuration Options

In `~/.witty/config.json`:

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

## Output

OSMind provides:
- JSON or text formatted metrics
- Bottleneck analysis with severity (critical/warning)
- Recommendations for improvement

## Troubleshooting

- **"OSMind not found"**: Check `project_path` in config
- **Permission denied**: Ensure OSMind scripts are executable
- **Missing Python deps**: Install OSMind's requirements.txt
