---
name: my-agent-skill
description: 5-mode context-aware ephemeral GitHub skill router with interactive discovery, recommendation, and approval prompts. Analyzes conversational context, categories, and keywords prior to executing any task; lists matched & recommended skills for user confirmation (or auto-executes with mandatory notification in auto-mode); fetches skills into /tmp at verified commits, applies them, and cleans up immediately.
---

# My Agent Skill — 5-Mode Ephemeral GitHub Skill Router

Prior to executing any task, thoroughly analyze the user's input, ongoing conversational context, provided categories, and keywords to identify relevant skills from the repository catalog.

Whenever this skill is invoked, execute these steps in strict order:

### Step 0: Pre-Flight Hygiene Sweep
- Run `rm -rf /tmp/ag-ephemeral-skill-*` immediately to wipe any leftover folders from previously interrupted sessions.

---

### Step 1: Multi-Modal Semantic Analysis & Routing Target
Analyze user prompt and the active conversation context to classify the request into one (or a combination) of the **5 Routing Modes**:

1. **Mode 1 — Full Conversation Context Routing (Implicit Mid-Chat Trigger):**
   - *When triggered:* Invoked during an ongoing conversation without repeating details (e.g., *"Use my-agent-skill for this"* or *"Bring the right skills for what we are building"*).
   - *Action:* Scan the **entire active conversation thread** and local workspace files to extract the project goal, tech stack (e.g., Android/Kotlin, SwiftUI, React/Three.js, FFmpeg), and current bottleneck (e.g., app UI/UX design, state logic, animations).

2. **Mode 2 — Broad Task Intent Routing (Goal-Based Trigger):**
   - *When triggered:* Invoked with a high-level goal (e.g., *"Build an app"*, *"Edit this video"*, *"Create a website"*).
   - *Action:* Break the high-level goal into its core required sub-domains (e.g., *"Build an app"* -> UI/UX design + app architecture; *"Edit this video"* -> FFmpeg/media processing) and route across up to 3 matching categories simultaneously.

3. **Mode 3 — Specific Feature / Style Description Routing (Attribute-Based Trigger):**
   - *When triggered:* Clearly describes a specific style, design system, or capability (e.g., *"I need Apple UI design"*, *"Smooth 3D scroll animations"*).
   - *Action:* Search `skills_index.json` triggers and scan every relevant category `README.md` to locate **all repositories** matching that exact description or design standard across the repo.

4. **Mode 4 — Direct Skill Name Lookup (Exact Match Trigger):**
   - *When triggered:* A specific repository or skill name is directly called out (e.g., *"Use my-agent-skill reverse-engineer-anything and reverse engineer this"*).
   - *Action:* Match the exact or closest repository name directly across `skills_index.json` and the category `README.md` tables without needing extra context, and fetch that specific skill immediately.

5. **Mode 5 — Loose Keyword, Metaphor & Action-Phrase Semantic Routing (Fuzzy Trigger):**
   - *When triggered:* Invoked alongside raw keywords, casual comparisons, or rough engineering actions—such as *"Apple UI"*, *"make it look like Apple"* (*Apple jaisa dikhne wala*), *"engineer a proper fix for this"*, *"edit code architecture"*, or *"clean up this logic"*.
   - *Action:* Translate loose keywords, metaphors, or action phrases into technical domains (e.g., *"make it look like Apple"* -> `apple-ui` / Cupertino HIG; *"edit code architecture"* / *"engineer a fix"* -> `architecture` / `refactoring` / `debugging` categories) and map them directly to matching `triggers` and `purpose` fields in `skills_index.json`.

---

### Step 2: One-Read JSON Lookup (`skills_index.json`)
- Fetch or read `skills_index.json` (via `https://raw.githubusercontent.com/spotOmee/my-agent-skills/main/skills_index.json` or local `/home/my-agent-skills/skills_index.json`).
- Map the output of Step 1 to the exact target `readme_path` file(s) (up to 3 category `README.md` manifests for multi-domain tasks).
- Read the target category `README.md` manifest(s) to gather candidate skills with their verified 7-character commit SHAs (`sha: <7-char-sha>`).

---

### Step 3: Skill Discovery, Recommendation & Approval Gate

#### 1. Skill Discovery & Recommendation
Thoroughly evaluate all skills identified from Step 2 against prompt keywords, categories, and conversational context:
- **Matched Skills:** List all skills found matching the prompt's keywords, category, and conversational context, including repository name, category, verified commit SHA, and brief capability summary.
- **Recommended Skills:** Explicitly highlight and recommend the best-suited skill(s) for the current task based on deep analysis, detailing why each recommended skill directly solves the immediate objective or bottleneck.

#### 2. Interactive Approval Prompt (Default Mode)
*Default behavior whenever full permissions or auto-mode is not explicitly enabled:*
- **Present Findings First:** Present the matched and recommended skills clearly to the user before taking any action (fetching, installing, or executing).
- **Interactive Choice Prompt:** Prompt the user to select which skill(s) to install and execute, provide an "Other" option for custom input, or grant permission to proceed automatically:
  - Option 1 (Recommended): Install and run recommended skill(s) (e.g., `(Recommended) [Skill Name(s)] - [Brief reason]`).
  - Option 2..N: Alternative matched skill(s) or combinations.
  - Option "All Matched": Install and run all matched skills.
  - Option "Other": Custom user input (e.g., specify a different repository, custom parameters, or alternative skill).
  - Option "Auto-Proceed": Grant permission to proceed automatically without further prompts.
- **Strict Execution Gate:** **Wait for explicit user confirmation** before installing or running any skill. Never fetch into `/tmp` or execute tools before the user provides explicit approval.

#### 3. Full Permissions / Auto-Mode Override
*When full permissions, auto-mode (`--auto`, `--yes`, `-y`), or explicit permission to proceed automatically is enabled:*
- Automatically select all necessary skills required to complete the task.
- **Mandatory Notification:** Before executing the task, you **MUST** output a list of all selected and scheduled skills:
  ```markdown
  > [!NOTE]
  > **Auto-Mode / Full Permissions Active — Scheduled Skills:**
  > - **<Skill Name>** (`owner/repo` @ `<sha>`): <Role in task execution>
  > - **<Skill Name>** (`owner/repo` @ `<sha>`): <Role in task execution>
  ```
- **Immediate Execution:** Proceed immediately to Step 4 (Isolated `/tmp` Fetch), Step 5 (Execution), and Step 6 (Ephemeral Cleanup) following the notification without waiting for user confirmation.

---

### Step 4: Isolated `/tmp` Fetch (Zero Permanent Install)
- **STRICTLY FORBIDDEN:** Never run `npm install -g`, `pip install` globally, or clone any skill permanently into the user's project or home directory.
- Fetch the confirmed repository files at their verified commit SHA into `/tmp/ag-ephemeral-skill-$(date +%s)/`.

---

### Step 5: Execute & Apply to Active Workspace
- Read the fetched `SKILL.md`, `README.md`, templates, or scripts inside `/tmp/ag-ephemeral-skill-*/`.
- Apply them directly to complete the user's task in the current project workspace.

---

### Step 6: Mandatory Post-Task Ephemeral Cleanup
- Run `rm -rf /tmp/ag-ephemeral-skill-*` immediately after completing the task (even if an error occurs).
- Verify zero temporary files remain on device to guarantee zero storage bloat.
