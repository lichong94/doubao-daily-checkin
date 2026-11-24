# doubao-daily-checkin

An AI Agent skill for Doubao Work (豆包工作): daily check-in, completing campaign tasks, claiming credit rewards, and verifying reward progress.

## What it solves

The Doubao Work campaign page ("从容开工 用豆包工作 额度天天送") contains two kinds of cards:

- **Direct-claim cards** — a "立即领" (claim now) button on the right;
- **Task cards** — a "去完成" (go complete) button, e.g. generate a spreadsheet, generate a PPT, finish one work task per day, organize scattered materials into a document.

If a task card is completed in an ordinary chat instead of the campaign task context, the page will not mark it done and the credit cannot be claimed. This skill encodes the whole loop: **click 去完成 → enter the task context → actually produce the deliverable → return to the campaign page and claim the reward → verify the accumulated credits.**

## Contents

```
doubao-daily-checkin/
└── SKILL.md    # Trigger conditions, core rules, full execution flow, constraints
```

## Installation

Drop the `doubao-daily-checkin` folder into the agent's skill directory, e.g.:

- Personal skills: `<workspace>/.user_skills/doubao-daily-checkin/`
- Built-in skills: `<workspace>/.skills/doubao-daily-checkin/`

Keep `SKILL.md` at the root of the folder so the skill can be discovered.

## Usage

Select the skill, then ask in natural language, for example:

> Finish today's Doubao check-in and verify my accumulated credits and reward progress.

> Claim every claimable task reward, complete whatever tasks you can along the way, then collect the credits.

> Just check in today, no tasks.

The skill then runs: open the campaign page (`https://www.doubao.com/pc/agent-explore?enter_method=fixed_entry`) → check in once → read task-card states and accumulated credits → for each claimable task, enter its task context and produce the deliverable → claim rewards on the campaign page → report credits earned, total, distance to the next tier, and what was claimed or not.

## Core rules

- Task cards must be entered with **Ctrl + click on 去完成**; the composer then pre-fills the task prompt and attaches the matching skill tag, and only a message sent there counts as a campaign task.
- A plain click on 去完成 only opens a "试试这些灵感" overlay whose suggestion chips do nothing — do not retry that path.
- For PPT tasks, if Ctrl+click still only opens the overlay, Ctrl+click the matching chip inside the overlay instead.
- A task is complete when a "任务已完成，立即领取活动激励额度 / 去领取" notification appears.
- Claim by clicking 去领取 in the notification, or by returning to the campaign page and clicking 领奖励 until "领取成功" appears.
- After each claim, re-read 累计获得额度 to confirm the number actually increased.
- Tasks that require inviting members or scanning a QR code by the account owner cannot be automated and are reported as such.

## Scope

- Operates only the user's own logged-in Doubao Work client; never handles accounts, passwords or verification codes.
- Default behavior is check-in plus credit verification; completing tasks and claiming other rewards require an explicit request in that turn.
- All credit and progress numbers come from live page values; nothing is inferred when the page does not show it.

## Changelog

- 2026-11-24: added the full "complete tasks and claim credits" flow (Ctrl+click entry, prompt rewriting, completion signals, claiming and credit re-verification); repositioned from "check-in only" to "check-in + tasks + rewards + verification".
- 2026-11-23: initial version with check-in and credit verification.

## Links

- GitHub: https://github.com/lichong94/doubao-daily-checkin
- Gitee: https://gitee.com/lichong2/doubao-daily-checkin
