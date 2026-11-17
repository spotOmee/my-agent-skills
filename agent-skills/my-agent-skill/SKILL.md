---
name: my-agent-skill
description: 5-mode context-aware ephemeral GitHub skill router. Routes via (1) ongoing conversation context, (2) broad task intent, (3) specific feature description, (4) direct skill name lookup, or (5) loose keywords, visual metaphors, and action phrases—then fetches skills into /tmp at verified commits, applies them, and cleans up immediately.
---

# My Agent Skill — 5-Mode Ephemeral GitHub Skill Router

Whenever this skill is invoked, execute these 6 steps in strict order:

### Step 0: Pre-Flight Hygiene Sweep
- Run `rm -rf /tmp/ag-ephemeral-skill-*` immediately to wipe any leftover folders from previously interrupted sessions.

### Step 1: Detect Routing Mode (1 to 5) & Build Semantic Search Target
Analyze my prompt and the active chat thread to classify the request into one (or a combination) of the **5 Routing Modes**:

1. **Mode 1 — Full Conversation Context Routing (Implicit Mid-Chat Trigger):**
   - *When triggered:* I invoke `my-agent-skill` during an ongoing conversation without repeating details (e.g., *"Use my-agent-skill for this"* or *"Bring the right skills for what we are building"*).
   - *Action:* Scan the **entire active conversation thread** and local workspace files to extract the project goal, tech stack (e.g., Android/Kotlin, SwiftUI, React/Three.js, FFmpeg), and current bottleneck (e.g., app UI/UX design, state logic, animations).

2. **Mode 2 — Broad Task Intent Routing (Goal-Based Trigger):**
   - *When triggered:* I invoke `my-agent-skill` with a high-level goal (e.g., *"Build an app"*, *"Edit this video"*, *"Create a website"*).
   - *Action:* Break the high-level goal into its core required sub-domains (e.g., *"Build an app"* -> UI/UX design + app architecture; *"Edit this video"* -> FFmpeg/media processing) and route across up to 3 matching categories simultaneously.

3. **Mode 3 — Specific Feature / Style Description Routing (Attribute-Based Trigger):**
   - *When triggered:* I invoke `/my-agent-skill` and clearly describe a specific style, design system, or capability (e.g., *"I need Apple UI design"*, *"Smooth 3D scroll animations"*).
   - *Action:* Search `skills_index.json` triggers and scan every relevant category `README.md` to locate **all repositories** matching that exact description or design standard across the repo.

4. **Mode 4 — Direct Skill Name Lookup (Exact Match Trigger):**
   - *When triggered:* I call out a specific repository or skill name directly (e.g., *"Use my-agent-skill reverse-engineer-anything and reverse engineer this"*).
   - *Action:* Match the exact or closest repository name directly across `skills_index.json` and the category `README.md` tables without needing extra context, and fetch that specific skill immediately.

5. **Mode 5 — Loose Keyword, Metaphor & Action-Phrase Semantic Routing (Fuzzy Trigger):**
   - *When triggered:* I invoke `my-agent-skill` alongside raw keywords, casual comparisons, or rough engineering actions—such as *"Apple UI"*, *"make it look like Apple"* (*Apple jaisa dikhne wala*), *"engineer a proper fix for this"*, *"edit code architecture"*, or *"clean up this logic"*.
   - *Action:* Translate the loose keywords, metaphors, or action phrases into technical domains (e.g., *"make it look like Apple"* -> `apple-ui` / Cupertino HIG; *"edit code architecture"* / *"engineer a fix"* -> `architecture` / `refactoring` / `debugging` categories) and map them directly to matching `triggers` and `purpose` fields in `skills_index.json`.

### Step 2: One-Read JSON Lookup (`skills_index.json`)
- Fetch **only** `https://raw.githubusercontent.com/spotOmee/my-agent-skills/main/skills_index.json`.
- Map the output of Step 1 to the exact target `readme_path` file(s) (up to 3 category `README.md` files for multi-domain tasks).

### Step 3: Targeted Category `README.md` Selection
- Fetch **only** the matched category `README.md` file(s) via raw GitHub URLs.
- Select the best 1–2 repositories per matched category (or the exact named repo in Mode 4) along with their verified commit SHAs (`sha: <7-char-sha>`).

### Step 4: Isolated `/tmp` Fetch (Zero Permanent Install)
- **STRICTLY FORBIDDEN:** Never run `npm install -g`, `pip install` globally, or clone any skill permanently into the user's project or home directory.
- Fetch the selected repository files at their verified commit SHA into `/tmp/ag-ephemeral-skill-$(date +%s)/`.

### Step 5: Execute & Apply to Active Workspace
- Read the fetched `SKILL.md`, `README.md`, templates, or scripts inside `/tmp/ag-ephemeral-skill-*/` and apply them directly to complete my task in the current project.

### Step 6: Mandatory Post-Task Cleanup
- Run `rm -rf /tmp/ag-ephemeral-skill-*` immediately after completing the task (even if an error occurs) and verify zero temporary files remain on the device.
