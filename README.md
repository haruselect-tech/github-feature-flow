# 🛠️ github-feature-flow

> Issue → Branch → Commit → PR → CI 一條龍。給 vibe coder 的安全帶 —— 不再手滑 `push origin main`。

一個 [Claude Code](https://claude.com/claude-code) skill，把標準的 GitHub 功能開發流程包起來。你只管 vibe，流程它幫你走完：

```
① 開 Issue（自動加 label）
② 建分支  feat/N-description
③ Commit（先擋 main、只 stage 該檔）
④ Push + 開 PR（Closes #N）
⑤ 本地 build 驗證 + 確認 CI 綠燈 ✅
```

它幫你守住三條護欄：

- ✅ 不會手滑 `push` 到 `main`
- ✅ 不會誤把 `.env` `commit` 進去
- ✅ PR 一 merge，對應 issue 自動關閉

---

## 前置需求

- [Claude Code](https://claude.com/claude-code)
- [GitHub CLI](https://cli.github.com/)（`gh`）—— 已安裝且登入：
  ```bash
  gh auth status   # 未登入請先 gh auth login
  ```

> 這個 skill 透過 `gh` CLI 操作 GitHub，**不需要任何 MCP server**，任何裝了 `gh` 的環境都能用。

---

## 安裝

在你的 Claude Code 終端機執行：

```text
/plugin marketplace add haruselect-tech/github-feature-flow
/plugin install github-feature-flow@haruselect-tech
```

`/plugin` 會跳出互動面板，讓你選擇安裝範圍（個人 `user` / 團隊 `project`）。

## 使用

裝好之後，直接用自然語言觸發即可，例如：

- 「幫我開個 issue 做商品搜尋，然後建分支」
- 「推上去開 PR」
- 「檢查一下 CI 過了沒」

或手動呼叫：

```text
/github-feature-flow
```

Claude 會依序帶你走完 Issue → Branch → Commit → PR → CI 五個步驟。

---

## 授權

[MIT](./LICENSE) © haruselect-tech
