# UmgMcp Plugin Sync Instructions

## Sync Plugin to UE Project

When asked to copy/sync the UmgMcp plugin to a UE project, always use `robocopy` with proper exclusions. **Never use `Copy-Item -Recurse`** as it can create nested directories and copy unnecessary files.

### Command Template

```powershell
robocopy "D:\githubcode\UmgMcp" "<TARGET_PROJECT>\Plugins\UmgMcp" /MIR /XD .git __pycache__ .venv Binaries Intermediate
```

### Parameters

- `/MIR` — Mirror mode: copies new/updated files, deletes files in destination that don't exist in source. Keeps the target clean.
- `/XD` — Exclude directories:
  - `.git` — Git history, not needed in target project
  - `__pycache__` — Python cache
  - `.venv` — Python virtual environment (target should create its own)
  - `Binaries` — Compiled binaries (target will recompile)
  - `Intermediate` — Build intermediates (target will regenerate)

### After Sync

1. If the UE Editor is **not running**, build with:
   ```powershell
   & "C:\Program Files\Epic Games\UE_5.7\Engine\Build\BatchFiles\Build.bat" <ProjectName>Editor Win64 Development "<PROJECT_PATH>\<ProjectName>.uproject" -WaitMutex
   ```

2. If the UE Editor **is running**, it must be closed first (Live Coding blocks external builds):
   ```powershell
   Get-Process -Name "UnrealEditor*" | Stop-Process -Force
   ```

### Important Notes

- The target project's `Plugins\UmgMcp\Resources\Python\.venv` should be set up independently via `uv venv && uv pip install -e .`
- The `Binaries` and `Intermediate` folders are project-specific and should never be copied between projects
- Always verify only one `UmgMcp.Build.cs` exists after sync to avoid "already contains a definition" errors

## Current Project Paths

- **Dev repo**: `D:\githubcode\UmgMcp`
- **UE project**: `D:\UnrealProjects\Beginer_project_list\ThirdPersonTest1`
- **Target plugin dir**: `D:\UnrealProjects\Beginer_project_list\ThirdPersonTest1\Plugins\UmgMcp`

## Git Workflow

- **origin** → `winyunq/UnrealMotionGraphicsMCP` (upstream, read-only)
- **myfork** → `BowenYang666/UMG-mcp` (fork, read-write)
- Push to `myfork`, merge to `main`, then push `main`
