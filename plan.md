# plan: README 第六行验证记录 — tracer-1153 silent-death leg 6（clean-resume-no-push）

## Context

- 项目 dogfood-1150（github.com/xiechimon/pacman-1150-dogfood），本构建 buildId `01a129ff-6c0f-7847-9c59-27693d47ece3`，工作树即本 cwd，conv 分支 `pacman/conv-01a129ff-6c0f-7847-9c59-27693d47ece3`，基点 origin/main=bfc10bd（init 单提交，树仅 .github/ + README.md，无 fail.marker）。当前 HEAD=bfc10bd、工作树干净（AGENTS.md 为运行时注入的未跟踪文件，永不入库）。
- 本卡目标：README.md 末尾补记第六行验证记录（2026-10-11 tracer-1153 silent-death leg 6 clean-resume-no-push）。「第六行」按用户口径计数：基点 bfc10bd 的 README 无任何验证行，前五条分别在各腿自己的 conv 分支/PR 上，勿去找补——本分支上这是唯一一条验证行。
- 本腿与 leg 1–5 的关键差异：无红腿注入（不建 fail.marker）、全程不开 PR、agent 全程不执行任何 `git push`；分支发布只发生在步收尾 harness 的自动 push（daemon 收尾 commit+push）。
- gate 规则（.github/workflows/gate.yml，push 与 pull_request 两事件都跑）：分支头存在 fail.marker→红；不存在→绿。本卡无 marker：步收尾自动 push 触发的 gate run 预期绿；无 PR，故无 pull_request 事件。
- 分支纪律（最高优先）：所有提交只落本工作树 conv 分支；严禁基于 main 另建 dogfood/* 或任何自建分支（harness 只对 conv 分支量 diff，自建分支=「构建零改动」假失败，历史事故 #5/#6 卡）；勿去共享基座克隆（~/.pacman/workspaces/zOizAlw_UGDkUE-pUYqFz/repo，当前检出 dogfood/tracer-1153-ci-note）提交。
- 兄弟腿现状（勿动它们及其分支/PR）：PR #16（leg 5，gate 红属预期、待其自身 [ci-fix] 轮删 marker 转绿闭环）、#14、#12、#10、#8、#7、#3、#1 均 OPEN 未合并。

## Changes

单文件单行改动，本卡唯一交付物：

1. README.md 末尾追加第六行验证行，内容原文照录（em-dash「—」U+2014，与 leg 5 验证行同款风格）：

   `2026-10-11: tracer-1153 silent-death leg 6 (clean-resume-no-push) — no fail.marker, no PR, no mid-step push; single commit, branch published by the step-final automatic push, gate green by design`

2. 追加方式：基点末行 `a product.` 无尾换行，需先补尾换行再空行，一条命令达成：

   `printf '\n\n%s\n' '<上行内容>' >> README.md`

   追加后文件末形态与 leg 5 分支同构（仅换 leg 6 内容且不带 marker）：`…it is evidence, not\na product.\n\n<第六行>\n`。不改已有内容。

3. 单提交：只显式 `git add README.md`（plan.md/AGENTS.md 等勿入本提交；若规划步收尾已自动把 plan.md 记为独立 plan 提交，保持其原样勿动），commit message：`docs: add tracer-1153 silent-death leg 6 note`。

4. 不建 fail.marker（本腿无红腿注入，conv 分支头无 marker，gate 预期绿）；不开 PR（禁 `gh pr create` 及任何开 PR 操作，PR 探测为空属预期）；agent 全程不执行 `git push`（任何变体、任何时点）——推送只发生在步收尾的自动 push。

## Edge cases

- 尾换行陷阱：基点 README 末行 `a product.` 无 \n，勿用编辑器/echo 等可能破坏 diff 形态的方式；追加后必须 `tail -c 1 README.md` 输出换行，否则重查。
- 关键词四件套缺一不可：`2026-10-11`、`tracer-1153`（勿笔误成 1133）、`silent-death leg 6`、`clean-resume-no-push`；em-dash 必须是 U+2014「—」，勿写成 `--` 或 `-`。
- PR 探测为空属预期，勿因「没有 PR」追加任何动作或试图开 PR 补救；收到相关 wake 先核对是否新事件/新提交所致再定性。
- 勿动兄弟腿及其分支/PR（含 #16 的预期红）；本地另有兄弟 conv 分支指针（pacman/conv-01a129b0-…、pacman/conv-01a129ae-… 同在 bfc10bd）与本卡无关，勿动。
- 本腿无 [ci-fix] 轮：无 marker、gate 预期绿；若步收尾 push 后 gate 反红，先核对是否新事件/新提交所致再定性（红才异常）。
- 预期终态：conv 分支 countAhead(conv,main)>0（含本卡 docs 提交）→ hasChanges=true，build 停审阅关口等用户审阅；勿自行 complete/merge。
- clean-resume 竞态教训（leg 5 实录）：并发/resume 场景下先查现状再动手，避免重复动作；本卡不 push、不开 PR，无此类面，但执行前仍先 `git status`/`git log` 核对基态。

## Verification

- `git status` 干净（AGENTS.md 保持未跟踪，不算脏）。
- `git diff origin/main --stat` 仅 README.md；diff 形态与 leg 5 同构减 marker：`-a product.` / `+a product.` / `+`（空行）/ `+<第六行>`。
- `tail -c 1 README.md` 输出换行；前文未被改动（`git diff origin/main -- README.md` 只动末尾）。
- 仓根无 fail.marker（`ls fail.marker` 报不存在）。
- `git log origin/main..HEAD` 中本卡 docs 提交只含 README.md 一个文件；本会话零人工 `git push`、零 `gh pr create`。
- 无新开 PR：`gh pr list --head pacman/conv-01a129ff-6c0f-7847-9c59-27693d47ece3` 为空属预期。
- 预期终态：countAhead(conv,main)>0 → hasChanges=true → build 停审阅关口；步收尾自动 push 后 gate（push 事件）绿。
