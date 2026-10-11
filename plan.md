# dogfood-1150 · tracer-1153 silent-death leg 5 (clean-resume) — 执行计划

## Context

- 本卡为 dogfood-1150（`github.com/xiechimon/pacman-1150-dogfood`，项目 `zOizAlw_UGDkUE-pUYqFz`）的 tracer-1153 端到端验证第 5 腿（silent-death leg 5，clean-resume 程式）。
- 前序腿均已完成各自交付：leg 1 grind-green（PR #3/#8）、silent-death leg 1 红转绿闭环（PR #10）、leg 2 第二程待 ci-fix（PR #12）、leg 4 红转绿闭环（PR #14）。基座 README（origin/main `bfc10bd`）不含任何验证记录行——前四条分别在各腿 conv 分支上，**勿找补、勿合并它们**。
- 本步工作区即 build 工作树：`/home/measure/.pacman/workspaces/01a129e3-6cae-7f46-ad82-f47d555700ac/`，已检出 conv 追踪分支 `pacman/conv-01a129e3-6cae-7f46-ad82-f47d555700ac`（基点 origin/main，当前 0 ahead）。工作区干净，仅两个工作区级未跟踪文件：`AGENTS.md`（pacman 运行时注入）与本 `plan.md`——**两者都绝不提交**。
- gate 机制（`.github/workflows/gate.yml`，`push` + `pull_request` 两事件）：仓根 head 处存在 `fail.marker` → `exit 1`（红）；不存在 → 绿。本腿注入 marker 后 gate 红**属预期**（tracer 故意红腿 red-leg-5）。
- 环境已验证：gh 已登录（xiechimon），`git push --dry-run` 对 conv 分支可通（新分支），git 身份 Xmon Dai <xiechimon@qq.com>。

## Changes

全部改动落在本 build 工作树的 conv 分支 `pacman/conv-01a129e3-6cae-7f46-ad82-f47d555700ac` 上，**单提交**，只含两个文件：

1. **README.md** 末尾追加第五行验证记录。基线文件末行为 `a product.`（无尾换行），对齐 leg 4 diff 形态：补齐尾换行 + 空一行 + 新行，即：

   ```bash
   printf '\n2026-10-11: tracer-1153 silent-death leg 5 (clean-resume) — red-leg-5 marker injected, gate red on purpose\n' >> README.md
   ```

   行文案逐字照抄上文基准（em dash 为 UTF-8 `—`，与前几腿一致）；关键词必须齐备：`2026-10-11`、`tracer-1153`、`silent-death leg 5`、`clean-resume`、`red-leg-5`。

2. **fail.marker**（仓根新建），内容恰好一行、逐字照抄用户原文：

   ```bash
   printf 'tracer-1133 red-leg-5\n' > fail.marker
   ```

   注意：数字是 **1133** 而非 1153，用户原样给定，**勿自行修正**（与用户交代的「leg 4 同款」口径一致）。

3. **提交**：commit message `docs: add tracer-1153 silent-death leg 5 note + red-leg-5 marker`（对齐 leg 4 先例）。只显式 `git add README.md fail.marker`，**禁用 `git add -A`/`git add .`**（防把 AGENTS.md、plan.md 带入）。

4. **推送 + 开 PR**：`git push origin pacman/conv-01a129e3-6cae-7f46-ad82-f47d555700ac`；随后 `gh pr create --base main --head pacman/conv-01a129e3-6cae-7f46-ad82-f47d555700ac --title "dogfood: tracer-1153 silent-death leg 5"`，正文一两句说明本腿 clean-resume 程式（dogfood 程式卡，PR body 非交付物，无需正式 PR body 工艺）。

5. **停在审阅关口**：gate 因 fail.marker 变红属预期——不删 marker、不追加提交修绿、不自行 complete。若系统后续追加 [ci-fix] 修复步：在同一 conv 分支上删除 fail.marker 并推送（README 验证行不动），gate 转绿即闭环，届时勿再追加提交。

## Edge cases

- **分支纪律（最高优先）**：一切提交只落本 conv 分支；严禁基于 main 自建 `dogfood/*` 或任何自建分支——harness 只对该 conv 分支量 diff，自建分支会造成「构建零改动」假失败（#5/#6 卡历史事故）。
- **PR 必须从 conv 分支开**（PR #3 模式），否则看板 PR 探测不到。
- AGENTS.md 与 plan.md 为工作区级未跟踪文件，任何提交都不得包含（leg 4 先例：最终 tree 仅 `.github` + `README.md`（+ 期间的 fail.marker））。
- fail.marker 的 1133 拼写、README 行文案均逐字照抄，勿「顺手修正」。
- gate 红定性：收到本卡 CI 红 wake 属预期红腿（red-leg-5 注入所致），先核对是否为 marker 在 head 的新事件/新提交所致再定性，勿当真实缺陷；头提交之后的红才是新问题。
- [ci-fix] 修复轮只删 fail.marker、README 行一字不动；修复后 countAhead=2、hasChanges=true，仍停在审阅关口。
- 若 push/PR 两事件 gate 仍在跑：等两事件都出结论再核对，均 failure 才符合预期形态。

## Verification

执行步完成后逐项核验：

1. `git status` 干净（AGENTS.md/plan.md 仍为未跟踪）；`git rev-list --count origin/main..HEAD` = 1 → hasChanges=true，构建停在审阅关口。
2. `git show HEAD --stat`：仅 `README.md`（+3/−1，即补尾换行+空行+新行）与 `fail.marker`（+1）两个文件。
3. `git show HEAD:fail.marker` 输出恰为 `tracer-1133 red-leg-5`（单行 + 单个尾换行，无多余字节）。
4. `tail -2 README.md` 末行为基准文案，五个关键词齐全。
5. `gh pr view <新PR>`：headRefName = `pacman/conv-01a129e3-6cae-7f46-ad82-f47d555700ac`，title = `dogfood: tracer-1153 silent-death leg 5`，state OPEN，从 conv 分支开出。
6. `gh run list --branch pacman/conv-01a129e3-6cae-7f46-ad82-f47d555700ac`：push 与 pull_request 两事件 gate 均 `failure`（红 = 预期，证据腿）。
7. 不删 marker、不追加提交、不自行 complete；[ci-fix] 步到来时按其指示在同一 conv 分支删 fail.marker 并推送。
