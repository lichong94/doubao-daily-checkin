# doubao-daily-checkin

An AI Agent skill for Doubao Work (豆包工作): daily check-in, campaign tasks, and credit rewards. By default it runs the **full sweep**: check in → claim every "立即领" card → complete every "去完成" task → claim each reward → verify accumulated credits and reward progress, with no need for the user to pick items one by one.

## What it solves

The Doubao Work campaign page ("从容开工 用豆包工作 额度天天送") has three kinds of cards:

- **Check-in** — once a day, one click on "立即签到";
- **Direct-claim cards** — a "立即领" (claim now) button on the right;
- **Task cards** — a "去完成" (go complete) button, e.g. generate a spreadsheet, generate a PPT, finish one work task per day, organize scattered materials into a document.

If a task card is completed in an ordinary chat instead of the campaign task context, the page will not mark it done and the credit cannot be claimed. Direct-claim credits also stay unclaimed unless someone clicks them. This skill encodes the whole loop — check in → claim all "立即领" → Ctrl+click each "去完成" into its task context → actually produce the deliverable → return to the campaign page and claim the reward → verify accumulated credits — and runs it **in full by default**, so the Agent clears the page in one pass.

## Default strategy

1. **Claim what is ready first** — click every "立即领" card on the page;
2. **Then do the tasks** — every "去完成" card is entered in its task context and completed, then its credit is claimed;
3. **Full loop** — work through the cards in order, claim after each one, until no "去完成" or "领奖励" buttons remain;
4. **Only skip what needs the account owner** — tasks requiring invited members, a personal QR-code verification or phone binding are skipped with a stated reason; everything else gets done;
5. **Stop after two failures** — if a task cannot enter its context or is never marked complete twice, record why and move on instead of retrying forever.

Only when the user explicitly says "just check in today" / "don't do tasks" does the skill downgrade to check-in plus credit verification.

## Contents

```
doubao-daily-checkin/
└── SKILL.md    # Trigger conditions, default strategy, core rules, full execution flow, constraints
```

## Installation

Drop the `doubao-daily-checkin` folder into the agent's skill directory, e.g.:

- Personal skills: `<workspace>/.user_skills/doubao-daily-checkin/`
- Built-in skills: `<workspace>/.skills/doubao-daily-checkin/`

Keep `SKILL.md` at the root of the folder so the skill can be discovered.

## Usage

Select the skill, then ask in natural language, for example:

> Finish today's Doubao check-in, claim every claimable task, complete whatever tasks you can along the way, then collect the credits.

> Do every task on today's campaign page and claim all the credits in one go.

Triggering the skill with no instruction also runs the full default sweep.

To check in only:

> Just check in today, no tasks.

The skill then runs: open the campaign page (`https://www.doubao.com/pc/agent-explore?enter_method=fixed_entry`) → check in once → read task-card states and accumulated credits → claim all "立即领" → enter each task context and produce the deliverable → claim each reward on the campaign page → report credits earned, total, distance to the next tier, and what was claimed, left or could not be automated.

## Core rules

- Task cards must be entered with **Ctrl + click on 去完成**; the composer then pre-fills the task prompt and attaches the matching skill tag, and only a message sent there counts as a campaign task.
- A plain click on 去完成 only opens a "试试这些灵感" overlay whose suggestion chips do nothing — do not retry that path.
- For PPT tasks, if Ctrl+click still only opens the overlay, Ctrl+click the matching chip inside the overlay instead.
- Entry succeeded only when all three appear: a new task page, a pre-filled composer, and the matching skill tag in the right-hand skill bar.
- The composer is contenteditable: click it → Ctrl+A → paste to replace, which is more reliable than typing character by character.
- A task is complete when a "任务已完成，立即领取活动激励额度 / 去领取" notification appears.
- Claim by clicking 去领取 in the notification, or by returning to the campaign page and clicking 领奖励 until "领取成功" appears.
- After each claim, re-read 累计获得额度 to confirm the number actually increased.
- Tasks that require inviting members or scanning a QR code by the account owner cannot be automated and are reported as such.

## Scope

- Operates only the user's own logged-in Doubao Work client; never handles accounts, passwords or verification codes.
- **Full sweep by default** (check-in + claim + tasks + credits); the scope shrinks only when the user explicitly asks to check in only.
- All credit and progress numbers come from live page values; nothing is inferred when the page does not show it.
- Completing a task in a normal chat or on the website is not counted by the campaign backend; tasks must be done inside the context opened from the client campaign page.

## Changelog

- 2026-11-24 (v2): default behaviour changed to a full sweep — check in, claim every "立即领", complete every "去完成", claim each credit and verify totals; added a "Default strategy" section and a stop-after-two-failures rule; no per-item prompting required.
- 2026-11-24 (v1): added the full "complete tasks and claim credits" flow (Ctrl+click entry, prompt rewriting, completion signals, claiming and credit re-verification).
- 2026-11-23: initial version with check-in and credit verification.

## Links

- GitHub: https://github.com/lichong94/doubao-daily-checkin
- Gitee: https://gitee.com/lichong2/doubao-daily-checkin
