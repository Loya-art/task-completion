---
name: task-completion
description: "Task completion rituals and user workflow preferences for Loya-art — file change notification, full automation, proxy reminders, cleanup, D: drive backup, entity extraction."
version: 1.1.0
author: Agent
tags: [workflow, cleanup, automation, windows, productivity, chinese-user]
---

# Task Completion — User Workflow Preferences

## ⚠️ Golden Rules (from repeated user corrections)

These override any general-purpose approach rules. Violating any of these will cause immediate user frustration.

1. **STUDY FIRST, CODE SECOND** — Before ANY modification to existing code, read and understand how the working version does it. The user said: "先学习，再实践再检查有没有问题，搞清楚了再来敲代码，先学习，再实践再检查有没有问题。" Show the plan, get approval, THEN code.

2. **SELF-VERIFY BEFORE DELIVERING** — The user explicitly requires: "你先完成，然后自行检查好，或者是还不够好，自我优化。你最好给我优化几轮，拿到一个没有问题的成果，再给我。" Do NOT ask "how does this look?" after every small change. Batch, verify, optimize, then present a complete result.

3. **THREE STRIKES → PIVOT** — If an approach fails 3 times, do NOT retry with slightly different parameters. The user gets visibly frustrated when the same broken path is retried. Fundamentally rethink: search for existing forks/extended versions, use a different API, or report the blocker honestly.

4. **CODE CONCISENESS** — "不要堆砌代码，代码不是写得越多越好，而是要学得写得精。" Prefer 4-5 targeted patches over bulk replacements. Each change should be deliberate.

5. **SEARCH FIRST** — Before building anything from scratch, search GitHub for `ProjectName-Extended/fork` and Douyin/Bilibili for `ProjectName 二创`. Someone may have already done the work.

6. **NO REGRESSION WHEN FIXING** — When the user reports a problem with your output (e.g. "文字太小了"), fix the reported issue without removing features that were working (e.g. background decorations, images). The user explicitly corrected: "不要搞着搞着还回退了". After each fix pass, self-check: does the new version still have everything the old version had that the user liked? If not, add it back before presenting.

This skill governs HOW tasks are completed for Loya-art (Chinese-speaking user, Windows git-bash environment). Follow these rules on every task, regardless of domain.

## 1. File Change Notification

**After every file modification**, explicitly list changed files with paths and brief descriptions in the reply. Format:

```
📝 修改文件:
- `path/to/file.ts` — 添加了 xxx 功能
- `path/to/file2.css` — 修复了 xxx 样式
```

## 2. Full Automation — Never Ask User to Type Commands

**The user will never manually type commands in a terminal.** All operations must be automated by the agent:

- Run commands via `terminal()` directly
- Download files via `execute_code()` (Python urllib) — preferred over `terminal()` curl
- Install packages via `terminal()` automatically
- Handle confirmations with `echo y | command` or `--yes` flags
- If a command needs user interaction (e.g. `kling login` opening browser), handle it in the background

**Windows git-bash curl workaround:** On MSYS/git-bash, `curl -o <file> <url>` often fails with **exit code 23** (write error caused by MSYS path translation issues). When this happens:
```python
# Use Python urllib instead:
python -c "import urllib.request; data = urllib.request.urlopen('URL').read(); open('PATH', 'wb').write(data); print('OK', len(data), 'bytes')"
```
This applies to ANY file download on this Windows environment — not just one tool.

**Exception — Failed Login/OAuth Flow (hard limit):** If truly impossible to automate (e.g. user must physically scan a QR code on their phone), explain clearly and provide ONE straightforward path with clear instructions (file location, short URL, or single terminal command). After **one attempt**, if the user reports the QR/image/link didn't work ("都找不到", "无法识别", "没用"), do NOT generate a second QR code or retry the same flow with slightly different parameters — immediately **pivot to a parallel alternative** (Canva manual, Python generation, different tool). Rationale: the user gets visibly frustrated when the same broken path is retried with cosmetic variations. One shot, then pivot.

## 3. Proxy Reminder

**Before accessing GitHub, Google, HuggingFace, or other blocked sites**, remind the user to enable proxy/VPN. Check if the site is reachable first:

```bash
curl -sI https://github.com --max-time 5 | head -3
```

If unreachable, say: "GitHub 需要代理，你先打开 VPN 我继续操作" and wait for confirmation.

## 4. Version Saving

When the user says "保存版本", use git:

```bash
git add -A
git commit -m "提交说明：改了哪些文件，做了什么"
```

- Auto `git init` if no `.git` exists
- **Local only** — never `git push`

## 5. Temporary File Cleanup

**After every task is completed**, automatically clean up:

**Delete:**
- Downloaded install scripts (`*.sh`, `*.bat` from curl)
- Generated QR code images
- pip/npm cache files created during this task
- Temp files in `/tmp/` or `~/AppData/Local/Temp/`
- Any file the agent created that was only needed during the task
- Helper scripts left on the Desktop (`.py`, `.ps1`, etc.)

**Keep:**
- Permanently installed CLI tools (dreamina.exe, npm global packages)
- Skills files (in skills/)
- Credentials (.kling/.credentials, config files)
- Configuration files

## 6. Document Organization — Ask Before Merging

When the user sends multiple files (diaries, notes, documents):

- **Do NOT automatically merge them into one file** unless the user explicitly asks.
- Ask: "These are N files. Keep them separate, or combine into one?"
- Default to separate files with their original titles.
- **This was a user correction**: combining personal diary entries into one combined file was not what they wanted.

## 7. Default Storage Location

**Two domains:**

### Tool/Skill Backups → `D:\\hermes-environment\\`

**All tools/skills/credentials are synced to D:\\hermes-environment\\** after installation:

Originals stay in their default locations — D: drive is an organized copy. Sync after every new install.

### Personal/User Files → `E:\\<user>\\`

The user's personal content (diaries, documents, images, projects) goes to E:\\<user>\\. **Never** put personal files on the desktop — the user finds it embarrassing and explicitly requested this. Subfolders as appropriate:
```
E:\\<user>\\
├── 日记/            ← Diary entries
├── 文档/            ← Documents
├── 项目/            ← Personal projects
└── ...
```

### Cleanup Rule

When deleting temp/script files created during a task, if they landed on the desktop, double-check they are truly temporary (not the user's files).

## 8. Bulk Operations — Use delegate_task for Parallel Work

When installing multiple tools/skills/cloning repos that are independent of each other, use `delegate_task(tasks=[])` for parallel execution. Reserve for cloning multiple git repos, installing multiple npm/pip packages, or any set of independent operations where each takes 5-60 seconds. For simple sequential operations (2-3 `terminal()` calls), just chain them.

## 9. Response Language

- Always respond in Chinese (this user is Chinese-speaking)
- Technical field names (generationId, works[].url, etc.) keep English
- Be concise — the user gets frustrated with verbose explanations and repeated failed approaches

## 10. Feedback & Coaching Style (Diary / 捞石头 Method)

When the user asks for personal feedback, diary analysis, or self-improvement advice:

- **Be direct, objective, and critical** — the user explicitly said "别特别认可我，现实一些去评判我". Do NOT praise/validate/pad.
- **Point out contradictions** between what they say and what they do.
- **Call out excuses** by name — the user already knows they make them.
- **Focus on behavior change, not self-awareness** — they have plenty of self-awareness; what they need is actionable mechanisms/protocols.
- Tone: blunt but constructive, like a coach who cares enough to tell the truth. No sugar-coating.

**Diary-specific: 捞石头框架**

The user's diary method comes from 姜蓝(自己造船的路飞)'s 《人选天选论》 theory — the "捞石头" (stone fishing) method. When analyzing or writing a diary for the user, use this 6-layer structure:

```
① 事实 (Facts — objective, no interpretation)
② 第一反应 (First reaction — raw instinct)
③ 我想要什么/贪婪 (What I want / Greed — what drives the "选福" choice)
④ 我害怕什么/恐惧 (What I fear — the other "选福" driver)
⑤ 我找了什么理由 (Excuses — rationalizations for choosing comfort)
⑥ 今天捞出来的石头 (The core cognitive pattern — what to change next time)
```

**Core theory reference:**
- **人选福，天选祸；人选祸，天选福**
- **选福** = follow instinct (grab when greedy, flee when scared) → short-term comfort, long-term cost
- **选祸** = go against instinct (let go when greedy, face when scared) → short-term pain, long-term gain
- When guessing the user's reactions (第一反应, 贪婪, 恐惧, 借口), **clearly mark them** with 🤔 and ask the user to correct.

## 11. Cron Job Setup Patterns

When the user asks for automated recurring tasks, use the following patterns:

### Pattern A: Pure Script Execution (no_agent=true)
For deterministic scripts that produce output independently (stock strategy, data collection):
```
cronjob(
    action='create',
    schedule='0 15 * * 1-5',
    script='script_name.py',
    no_agent=True,
    deliver='origin'
)
```
- Script MUST be placed in `~/.hermes/scripts/`
- Script path must NOT contain Chinese characters (MSYS path issue)
- Output is delivered verbatim — the script IS the job
- Use for: stock strategy runs, data collection scripts, watchdog alerts

### Pattern B: Agent-Driven Tasks
For tasks that need reasoning, analysis, or synthesis from multiple sources:
```
cronjob(
    action='create',
    schedule='0 9 * * 1',
    prompt='...',
    skills=['skill-name'],
    deliver='origin'
)
```
- The agent loads the specified skills, then executes the prompt
- Use for: weekly strategy reviews, daily briefings, any task needing LLM reasoning

### Pattern C: One-Shot Scheduled Tasks
For future one-time actions:
```
cronjob(
    action='create',
    schedule='2026-08-01T10:00:00',
    prompt='...'
)
```
- Omit `repeat` (defaults to 1 for one-shot)

### Common Pitfalls
- Script path error: `cronjob` rejects absolute paths — must be relative filename under `~/.hermes/scripts/`
- Cron delivery: always set `deliver='origin'` to auto-deliver to current chat
- Python scripts in no_agent mode: output everything via `print()` — the stdout IS the delivery
- Chinese PS1 files: PowerShell scripts with Chinese text fail under git-bash due to encoding issues. Use ASCII-only or write as Python instead.

## 12. Model Switching Protocol

**Never silently switch models.** The user's explicit rule: "像这种事情你可以先看哪个模型最合适，然后问我要不要切换。"

When the current model lacks a capability for the task:

1. **Assess** — Identify what capability is missing (vision, reasoning, code, etc.)
2. **Recommend** — Suggest the most suitable model (e.g. qwen-vl-max for Chinese image recognition, deepseek-reasoner for complex logic)
3. **Ask** — "这个任务需要X能力，建议切到Y模型，要切吗？"
4. **Proceed** — Only switch after the user approves

**For new API integrations:**
- "先看能不能用切换模型 或者接入新模型再去想别的办法" — try model switching first, only suggest new API integration if switching doesn't work
- When suggesting a new API, mention cost/availability (free tier vs paid) so the user can decide
- Remember the user already has DASHSCOPE_API_KEY (Qwen) configured — check available providers via `hermes auth list` before suggesting new ones

**Known model capabilities for reference:**
| Task Type | Recommended Model | Notes |
|-----------|------------------|-------|
| Chinese image/table OCR | qwen-vl-max (DashScope) | Much better than DeepSeek for Chinese text in images |
| Math/logic/debug | deepseek-reasoner | Internal reasoning chain |
| Max capability | deepseek-v4-pro | Strongest DeepSeek model |
| Daily chat/coding | deepseek-v4-flash | Fast, cheap, sufficient |

## 13. Dependency Audit (User Requested: "检查你自身缺失的依赖")

When the user asks to check for missing dependencies (tools, packages, binaries) across all skills:

1. **Check cross-cutting tools first** — tools used by many skills:
   ```bash
   # Essential: ffmpeg, jq, curl
   which ffmpeg && ffmpeg -version 2>&1 | head -1
   which jq && jq --version
   # Node.js and npm
   node -v; npm -v
   ```

2. **Check Python packages** used by multiple skills:
   ```bash
   pip list | grep -iE "pymupdf|markitdown|yt-dlp|manim|python-pptx|pillow|websocket-client|python-docx|comfy"
   ```

3. **Check CLI tools** for commonly referenced skills:
   ```bash
   # Media/creative: ffmpeg, soffice, pdftoppm
   # Coding agents: claude, codex, opencode
   # Email: himalaya
   # Password: op (1password)
   ```

4. **Cross-reference skill SKILL.md files** — check `prerequisites.commands` and `required_commands` fields from `skill_view()` output for each skill in active use.

5. **Report format:** Group into three columns:
   | 工具 | 涉及 Skill | 状态 |
   | 已安装跨工具 | 已安装按需装 | ❌ 缺失 |

6. **Only install what the user actually uses** — don't install huge dependencies (manim ~50MB, comfy-cli + ComfyUI ~5GB) unless user confirms.

7. **Fix PATH persistence** for winget-installed tools (see task-standards skill's winget section).

When asked "检查所有 skill" (comprehensive), batch-load skills via `skills_list()` + `skill_view()` on the most actively-used ones. Skip 117 one-by-one — focus on categories with real external dependencies: creative, productivity, mlops, media, autonomous-ai-agents.

## 12. Windows Power Management

When the user asks about power settings (lid close, sleep, hibernate) on this Windows laptop:

- See `references/windows-power-management.md` for the exact `powercfg` commands.
- The LIDACTION alias may not be recognized — use the GUID `5ca83367-6e45-459f-a27b-476b1d01c936` directly.
- After setting, always call `powercfg /setactive SCHEME_CURRENT` to apply.
- Set npm mirror first if npm-based tools will be needed: `npm config set registry https://registry.npmmirror.com`
