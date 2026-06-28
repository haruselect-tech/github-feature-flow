<div align="center">

# github-feature-flow

### GitHub Flow for Claude Code

Issue → Branch → Commit → Pull Request → CI

<br/>

**保持開發心流，把 GitHub Flow 交給 Skill。**

<br/>

[![Claude Code Skill](https://img.shields.io/badge/Claude_Code-Skill-7C5CFF?logo=anthropic\&logoColor=white)](https://claude.com/claude-code)
[![Runs on gh CLI](https://img.shields.io/badge/runs_on-gh_CLI-2088FF?logo=github\&logoColor=white)](https://cli.github.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-3fb950.svg)](./LICENSE)

</div>

---

## 快速開始

安裝 Skill。

```text
/plugin marketplace add haruselect-tech/github-feature-flow
/plugin install github-feature-flow@haruselect-tech
```

接著直接跟 Claude 說：

> 「幫我開個 issue 做商品搜尋，建分支開始寫。」

> 「Commit 一下。」

> 「推上去開 PR。」

> 「CI 過了嗎？」

剩下的流程，Skill 會自動完成。

> [!NOTE]
>
> 需要先安裝並登入 GitHub CLI。
>
> ```bash
> gh auth status
> ```
>
> 本 Skill 透過 `gh` 操作 GitHub，不需要任何 MCP Server。

---

## 為什麼需要它

Vibe Coding 很快。

GitHub Flow 沒那麼快。

真正的問題通常不是不會 Git，而是人在心流裡，很容易跳過那些「知道該做、卻不想做」的步驟。

例如：

* 忘了建立 Issue。
* 直接在 `main` 上 Commit。
* 順手用了 `git add .`。
* 忘了建立 Pull Request。
* CI 沒確認就 Merge。

這個 Skill 把整套 GitHub Flow 自動化。

你專心開發，它負責流程。

---

## 它會幫你做什麼

* 建立 GitHub Issue
* 建立 Feature Branch
* Commit 前確認目前不是 `main`
* 只加入本次修改的檔案
* Push 並建立 Pull Request
* 自動加入 `Closes #N`
* 檢查 GitHub Actions
* 保持 `main` 永遠乾淨

---

## 設計原則

這個 Skill 只做一件事：

**讓每一個功能，都遵循一致的 GitHub Flow。**

因此它遵守幾個原則：

* 一個功能，一條 Branch。
* 所有修改都經過 Pull Request。
* Commit 只包含相關檔案。
* 儘早驗證 CI。
* 不教 Git，只負責 Workflow。

---

## 使用方式

直接使用自然語言即可。

| 你說           | Skill 會做               |
| ------------ | ---------------------- |
| 開個 issue 做 X | 建立 Issue               |
| 建分支開始寫       | 建立 Feature Branch      |
| Commit 一下    | 檢查 Branch → Commit     |
| 推上去開 PR      | Push → 建立 Pull Request |
| CI 過了嗎？      | 檢查 GitHub Actions      |

也可以直接執行完整流程：

```text
/github-feature-flow
```

---

## How it works

### 建立 Issue

建立 GitHub Issue，加入 Label 與 Checklist。

---

### 建立 Branch

從 `main` 建立新的功能分支。

```text
feat/<issue-number>-description
```

---

### Commit

Commit 前一定會先確認目前所在 Branch。

如果目前是 `main`，流程立即停止。

只加入與此次修改相關的檔案，不使用 `git add .`。

---

### 建立 Pull Request

建立 Pull Request，並自動加入：

```text
Closes #N
```

Merge 後，GitHub 會自動關閉對應 Issue。

---

### 檢查 CI

先完成本機 Build。

再透過：

```bash
gh pr checks
```

確認 GitHub Actions 全部通過。

---

## 為什麼不用 MCP？

因為沒有必要。

只要有 GitHub CLI (`gh`) 就能完成整個流程。

少一層依賴，代表：

* 更容易安裝
* 更容易維護
* 更容易分享
* 更容易跨平台使用

---

## License

MIT © haruselect-tech

<div align="center">

Made for developers who love flow and clean Git history.

</div>
