---
name: github-feature-flow
description: >
  完整的 GitHub 功能開發標準流程：開 Issue（加 label）→ 建對應編號分支（feat/N-description）
  → 開發 commit → push → 開 PR（Closes #N）→ 本地 build 驗證 → 確認 CI。
  使用這個 skill 的時機：開始新功能開發、建 issue 然後開分支、push 之後要檢查 CI、
  詢問要怎麼走開發流程、或說「開 issue」「建分支」「檢查 CI」「推上去」「開 PR」等關鍵詞。
  這個流程適用於任何 GitHub 私有或公開 repo，透過 GitHub CLI（gh）執行。
allowed-tools: Bash
---

# GitHub Feature Development Flow

這個 skill 記錄標準的 GitHub Issue → Branch → PR → CI 開發流程。
每次開始新功能時，確實依序走完五個步驟。

> **前置需求**：安裝並登入 [GitHub CLI](https://cli.github.com/)。
> ```bash
> gh --version          # 確認已安裝
> gh auth status        # 確認已登入；未登入跑 gh auth login
> ```
> repo 的 owner/name 會自動從當前 git remote 取得，指令通常不必手動指定。

---

## Step 1：開 Issue

```bash
gh issue create \
  --title "簡短描述功能或問題" \
  --label "enhancement" \
  --body "$(cat <<'EOF'
## 需求
- 說明要做什麼

## 驗收標準
- [ ] 項目 1
- [ ] 項目 2
EOF
)"
```

- **title**：簡短描述功能或問題（中英文皆可）
- **label**：`enhancement` 新功能、`bug` 修 bug、`documentation` 文件
- **body**：需求說明 + 驗收標準（用 checklist `- [ ]` 格式）

建立後 `gh` 會印出 issue URL，**記下結尾的 issue 編號**（例如 `.../issues/9` → #9），後續步驟都會用到。

---

## Step 2：建分支

命名規則：`feat/{issue-number}-{kebab-case-description}`

```bash
git checkout -b feat/9-product-search
```

規則：
- 前綴 `feat/`（新功能）或 `fix/`（修 bug）
- 接 issue 編號
- 再接 3–5 個英文單字的 kebab-case 描述，對應 issue 標題
- 不要有中文或特殊符號

---

## Step 3：開發與 commit

### ⚠️ commit 前一定先確認當前分支

**每次 commit 前先跑 `git branch --show-current`，確認不是 `main`。**

```bash
git branch --show-current   # 必須是 feat/N-... ，不能是 main
```

> ❗**不要把分支檢查跟 `git commit` 串在同一行（`git branch --show-current && git add ... && git commit ...`）。**
> 串在一起等於沒檢查——印出 `main` 的同時 commit 已經下去了。**先單獨跑檢查、看到結果確認後，再執行 commit。**
>
> 機制保險：可在 `.git/hooks/pre-commit` 裝一個擋 main 的 hook（在 main 上 commit 直接 exit 1），要繞過才用 `git commit --no-verify`。

最常踩雷的情境：**PR merge 之後**。merge 完 session 通常已經回到 `main`，
這時若要再補一個小修正，直覺地 `git add` + `git commit` 就會**直接落在 main 上並 push 到遠端**
（main 沒設保護就會成功），等於跳過整個 PR 流程。

> 後續修正 = 一個新功能單位 → 要嘛回原分支（PR 未 merge 時 `git checkout feat/N-...`），
> 要嘛**開一條新分支走完整流程**（PR 已 merge 時 `git checkout -b fix/M-...`）。
> 絕不在 main 上直接 commit。

確認分支正確後，**只 stage 相關檔案**，不要用 `git add -A` / `git add .`：

```bash
git add path/to/changed/file.ts another/file.tsx
git commit -m "$(cat <<'EOF'
feat: 一行描述做了什麼

- 細節說明（可選）
EOF
)"
```

Commit message 規則：
- `feat:` 新功能 / `fix:` 修 bug / `refactor:` 重構 / `docs:` 文件
- 第一行 ≤ 72 字元
- 若有多個修改點，用 bullet list 說明

---

## Step 4：Push + 開 PR

```bash
git push -u origin feat/9-product-search
```

推完後用 `gh pr create` 開 PR：

```bash
gh pr create \
  --base main \
  --head feat/9-product-search \
  --title "與 issue 標題保持一致" \
  --body "$(cat <<'EOF'
Closes #9

## Summary
- 做了什麼（bullet list）

## Test plan
- [ ] 驗收項目 1
- [ ] 驗收項目 2
EOF
)"
```

- **title**：與 issue 標題保持一致
- **body 第一行必須是 `Closes #9`**（PR merge 後 issue 會自動關閉）

---

## Step 5：確認 CI

正確的 CI 確認順序：

1. **先本地 build 驗證**（最快，push 前就能抓到錯誤）。依專案類型執行對應 build 指令，例如：
   ```bash
   pnpm build          # 或 npm run build / yarn build
   # monorepo 範例：pnpm --filter <package> build
   # 其他：cargo build / go build ./... / mvn package
   ```

2. **Push 後確認 GitHub Actions**（用 `gh`，不要用舊版 Commit Status API）：
   ```bash
   gh pr checks                 # 列出這個 PR 的 Actions check 狀態
   gh run list --limit 5        # 最近幾次 workflow run
   gh run view --log-failed     # 直接看失敗的 log
   ```
   > ⚠️ GitHub Actions 用的是 **Check Runs API**；舊的 Commit Status API（某些工具的 `pr status`）讀不到 Actions 結果，會回空。一律用 `gh pr checks`。

3. **如果 CI 失敗**，先看 log → 修 → 重新 commit → push，不要乾等 — 直接分析 build log。

---

## 常見陷阱

| 問題 | 原因 | 解法 |
|---|---|---|
| 直接 push 到 `main` | 忘記建分支 | 永遠先 `git checkout -b feat/N-name` |
| 後續修正誤落 `main` | PR merge 後 session 已在 main，直接 commit | commit 前先 `git branch --show-current`；要補修正先開新分支 |
| CI 狀態查不到 / 回空 | 看的是 Commit Status API，不是 Actions | 改用 `gh pr checks` / `gh run view` |
| build 找不到套件 | 傳遞依賴未顯式宣告 | 把它加成直接依賴（例：`pnpm add <pkg>`） |
| commit 前誤 stage 了 `.env` | `git add .` 太粗糙 | 永遠指定檔案路徑 |
| PR merge 後 issue 沒自動關 | PR body 沒有 `Closes #N` | 每次開 PR 都檢查 body 第一行 |
