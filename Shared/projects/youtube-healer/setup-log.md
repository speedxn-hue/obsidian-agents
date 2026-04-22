# YouTube Healer Team — Setup Log
Date: 2026-04-18

## Status: Phase 2 Complete ✅

## Team Members
| Agent | Name | Status | Workspace |
|-------|------|--------|-----------|
| orchestrator | Maestro 🎼 | ✅ Active | workspace-orchestrator |
| music-agent | Harmony 🎵 | ✅ Active | workspace-music |
| visual-agent | (pending) | ⏳ Phase 3 | - |
| seo-agent | (pending) | ⏳ Phase 3 | - |
| devil-agent | (pending) | ⏳ Phase 3 | - |
| upload-agent | (pending) | ⏳ Phase 3 | - |

## Proven Working
- Maestro can spawn Harmony via sessions_spawn ✅
- Harmony generates in-character Suno prompts ✅
- Maestro reports back to 主人 in Traditional Chinese ✅
- Maestro stays in role, never generates content himself ✅

## Key Config
- tools.agentToAgent.enabled = true
- agents.list[orchestrator].subagents.allowAgents = ["music-agent"]
- agents.list[orchestrator].subagents.requireAgentId = true

## Phase 3 TODO
1. Add visual-agent (Pixel 🎨) — Midjourney + Kling prompts
2. Add seo-agent (Scout 🔍) — YouTube titles, descriptions, tags
3. Add devil-agent (Critic 😈) — quality review
4. Add upload-agent (Publisher 📤) — FFmpeg + YouTube API
5. Wire Maestro to dedicated Telegram bot
6. Add cron jobs for daily automation

## How to Test
openclaw agent --agent orchestrator -m "我需要一首432Hz嘅冥想音樂"

---
## Update: Phase 3 Complete — Full Team Assembled
Date: 2026-04-18

### New Agents Added
| Agent | Name | Status |
|-------|------|--------|
| visual-agent | Pixel 🎨 | ✅ Active |
| seo-agent | Scout 🔍 | ✅ Active |
| devil-agent | Critic 😈 | ✅ Active |
| upload-agent | Publisher 📤 | ✅ Active |

### Orchestrator Allowlist
["music-agent", "visual-agent", "seo-agent", "devil-agent", "upload-agent"]

### Pipeline Safety Gates
- Critic must APPROVE before Publisher will upload
- Publisher refuses upload without all files present
- All agents refuse to generate outside their scope

### Phase 4 TODO
1. Wire Maestro to dedicated Telegram bot
2. Add cron jobs for daily automation (22:00 HKT)
3. Full pipeline integration test
4. Connect Suno/Midjourney/Kling/YouTube APIs

---

---
## Update: Full Pipeline Integration Test — SUCCESS
Date: 2026-04-18

### Test Result: ✅ End-to-End Pipeline Functional

### Pipeline Performance
| Step | Agent | Result |
|------|-------|--------|
| 1 | Harmony 🎵 | ✅ 528Hz Suno prompt generated |
| 2 | Pixel 🎨 | ✅ Midjourney + Kling prompts generated |
| 3 | Scout 🔍 | ✅ Title + description + 25 tags generated |
| 4 | Critic 😈 | ✅ 7.4/10 REVISE verdict — caught real issues |
| 5 | Publisher 📤 | ✅ CORRECTLY blocked (no APPROVE verdict) |

### Key Learnings
- ⚠️ AVOID "miracle frequency" / "奇蹟頻率" / "healing" — YouTube medical claim risk
- ⚠️ Visual coherence: Midjourney theme must match Kling theme exactly
- ⚠️ SEO: avoid therapeutic language ("擺脫失眠焦慮")
- Human-in-the-loop working: Maestro pauses at REVISE, asks 主人

### Safety Gates Verified ✅
- Critic prioritises real-world risk over arbitrary score thresholds
- Publisher never called without APPROVE verdict
- All agents stayed in scope throughout

### Phase 4 TODO (next session)
1. Wire Maestro to dedicated Telegram bot
2. Add daily cron job (22:00 HKT)
3. Connect Suno API
4. Connect YouTube Data API
5. Complete revision loop test

---

---
## Update: Model Diversity + Fallback System
Date: 2026-04-22

### Model Assignment (Final)
| Agent | Primary | Fallback |
|-------|---------|----------|
| Maestro 🎼 | nvidia/nemotron-3-super-120b-a12b:free | minimax/minimax-m2.7 |
| Critic 😈 | nvidia/nemotron-3-super-120b-a12b:free | minimax/minimax-m2.7 |
| Harmony 🎵 | google/gemma-4-26b-a4b-it:free | minimax/minimax-m2.7 |
| Scout 🔍 | google/gemma-4-26b-a4b-it:free | minimax/minimax-m2.7 |
| Publisher 📤 | google/gemma-4-26b-a4b-it:free | minimax/minimax-m2.7 |
| Pixel 🎨 | google/gemma-4-31b-it:free | minimax/minimax-m2.7 |

### Lessons Learned
- ⚠️ Model exists in catalog ≠ model actually works — always smoke test
- ⚠️ Gemma 4 :free suspended (Google API key issue on OpenRouter side)
- ⚠️ Nemotron confabulates when sub-agents fail silently — fallback fixes root cause
- ✅ Fallback chain proven: Gemma 4 fails → MiniMax auto-takes over transparently
- ✅ Persona preserved across fallback — Harmony identical in both models

### Current Runtime Status
- Maestro + Critic: Nemotron 3 Super (working)
- Harmony + Scout + Publisher + Pixel: MiniMax M2.7 via fallback (working)
- When Gemma 4 :free resumes: auto-switches back, no config change needed

### Nemotron Persona Drift Note
- Observed confabulation when sub-agents failed silently
- Likely resolved by fallback (sub-agents now return real responses)
- Monitor on next full pipeline run — harden AGENTS.md if persists

---

---
## Update: Nemotron 3 Super — Failed Experiment
Date: 2026-04-22

### Result: REVERTED — Do Not Use Nemotron for Chinese workflows

### What Was Tested
- Nemotron 3 Super (nvidia/nemotron-3-super-120b-a12b:free) on main, Maestro, Critic

### Failures Observed (3 separate tests)
1. main: ignored direct question, confabulated about heartbeats
2. Maestro: claimed to apply edits it never delegated
3. main (clean session): same heartbeat confabulation — not a session issue

### Root Cause
- Nemotron is English-primary, weak Cantonese/Traditional Chinese instruction-following
- Fixates on workspace context (HEARTBEAT.md etc) over user message
- Confabulates when sub-agents fail silently

### Final Model Layout
| Agent | Primary | Fallback |
|-------|---------|----------|
| main | MiniMax M2.7 | Nemotron (emergency only) |
| Maestro | MiniMax M2.7 | Nemotron (emergency only) |
| Critic | MiniMax M2.7 | Nemotron (emergency only) |
| Harmony/Scout/Publisher | Gemma 4 26B :free | MiniMax M2.7 |
| Pixel | Gemma 4 31B :free | MiniMax M2.7 |

### For Future Reference
- MiniMax M2.7 = best free model for Chinese OC workflows
- Nemotron = API works but unsuitable for Chinese instruction-following
- Gemma 4 :free = good when upstream key not suspended (auto-fallback handles outages)
- Always smoke test after model changes before full pipeline

---
