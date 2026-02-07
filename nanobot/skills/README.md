# witty Skills

This directory contains built-in skills that extend witty's capabilities.

## Skill Format

Each skill is a directory containing a `SKILL.md` file with:
- YAML frontmatter (name, description, metadata)
- Markdown instructions for the agent

## Attribution

These skills are adapted from [OpenClaw](https://github.com/openclaw/openclaw)'s skill system.
The skill format and metadata structure follow OpenClaw's conventions to maintain compatibility.

## Available Skills

| Skill | Description | Type |
|-------|-------------|------|
| `github` | Interact with GitHub using the `gh` CLI | built-in |
| `weather` | Get weather info using wttr.in and Open-Meteo | built-in |
| `summarize` | Summarize URLs, files, and YouTube videos | built-in |
| `tmux` | Remote-control tmux sessions | built-in |
| `skill-creator` | Create new skills | built-in |
| `osmind` | System performance monitoring via external OSMind project | external |
| `anuma` | Inference optimization via external ANUMA project | external |

## External Skills

### OSMind

System performance monitoring skill that calls the external **OSMind** project.

**Requires**: OSMind installed separately and configured in `~/.witty/config.json`

**Natural language commands:**
- "Analyze my system performance"
- "Check CPU and memory usage"
- "Collect system metrics for 2 minutes"

**Location:** `nanobot/skills/osmind/`

### ANUMA

Inference workload optimization skill that calls the external **ANUMA** project.

**Requires**: ANUMA installed separately and configured in `~/.witty/config.json`

**Natural language commands:**
- "Optimize my inference workload"
- "Bind inference processes to specific cores"
- "Analyze NUMA topology"

**Location:** `nanobot/skills/anuma/`

## External Skill Configuration

External skills require the external project to be installed separately. Configure the path in `~/.witty/config.json`:

```json
{
  "skills": {
    "osmind": {
      "project_path": "/path/to/OSMind"
    },
    "anuma": {
      "project_path": "/path/to/ANUMA"
    }
  }
}
```
