# Gemini Project Development Guidelines

本文件定義了專案的開發指導原則與具體工作流程。所有貢獻者都應嚴格遵守，以確保程式碼品質、可維護性與團隊協作效率。

---

## 第一部分：核心開發原則與紀律

本節內容源自 Kent Beck 的測試驅動開發 (TDD) 與 Tidy First 思想，是我們所有開發工作的最高指導原則。

### 1. 核心開發循環：TDD (Red-Green-Refactor)

所有功能開發與錯誤修復都必須遵循 TDD 循環：

-   **紅 (Red)**: 先寫一個小而專注、且會失敗的測試。這個測試精確定義了你期望實現的下一步功能。
-   **綠 (Green)**: 寫**最精簡**的程式碼來讓該測試通過。此階段不追求完美，只求快速讓測試變綠。
-   **重構 (Refactor)**: 在測試通過的安全網下，改善程式碼的結構、移除重複、提升可讀性。重構時絕不應改變外部行為。

### 2. 優先整理 (Tidy First)

在進行任何行為變更之前，先確保程式碼結構是易於修改的。

-   **嚴格分離變更類型**：
    1.  **結構性變更**: 不改變行為的程式碼整理（如：重新命名、抽取函式、移動檔案）。
    2.  **行為性變更**: 新增或修改功能。
-   **絕不混合**：這兩種變更絕不能出現在同一個 commit 中。
-   **先整理再開發**: 如果需要修改的程式碼不易處理，應先進行結構性變更，待其變得清晰後，再進行行為性變更。

### 3. Commit 紀律

Git commit 是專案歷史的基石，必須清晰、原子化。

-   **提交時機**: 只有在**所有測試都通過**且**沒有任何編譯/linter警告**時才能提交。
-   **原子化**: 每個 commit 都必須是單一的邏輯單元（例如：「新增一個失敗的測試」、「讓測試通過」、「重構 User 模型」）。
-   **小而頻繁**: 鼓勵小顆粒度、高頻率的提交，而不是一個包含所有內容的巨大提交。
-   **清晰的訊息**: Commit message 必須清楚說明該次提交是**結構性**還是**行為性**的變更。

### 4. 程式碼品質標準

-   **消除重複 (DRY)**:  ruthlessly eliminate duplication.
-   **意圖清晰**: 透過命名和結構清楚表達程式碼的意圖。
-   **保持簡單 (KISS)**: 永遠用能解決問題的最簡單方案。
-   **小單元**: 保持函式和類別小而專注於單一職責。

---

## 第二部分：Rails 專案實踐工作流程

本節將上述原則應用於本 Rails 專案，定義了一個從使用者行為到程式碼實現的具體工作流程。

### 1. 開發哲學：由外而內 (Outside-In)

1.  **始於 BDD**: 任何新功能都從一個高層次的 Cucumber `.feature` 檔案開始，用使用者視角的語言描述期望行為。
2.  **轉向 TDD**: 為了讓 BDD 測試通過，我們會深入到模型層，用 RSpec 為核心業務邏輯編寫更細緻的單元測試。
3.  **完成循環**: 核心邏輯通過單元測試後，再回到 BDD 層次，編寫必要的「膠水程式碼」（Routes, Controllers, Views）讓高層次的 Cucumber 測試通過。

### 2. 測試框架與語言

-   **行為驅動開發 (BDD)**: 使用 **Cucumber**。
    -   `.feature` 檔案應優先使用**繁體中文 (zh-TW)** 撰寫，以促進溝通。檔案開頭需加上 `# language: zh-TW`。
-   **測試驅動開發 (TDD)**: 使用 **RSpec** 進行單元和整合測試。

### 3. 標準開發流程 Commit 範例

開發任何新功能都應嚴格遵循以下 commit 順序：

1.  **Commit 1: 新增失敗的 BDD 測試**
    -   **目的**: 定義高層次的使用者行為。
    -   **內容**: 只包含一個新的 `.feature` 檔案。
    -   **訊息**: `test(bdd): Add Cucumber scenario for [feature name]`

2.  **Commit 2: 進行結構性變更 (如果需要)**
    -   **目的**: 準備基礎建設，如資料庫遷移。
    -   **內容**: 只包含 migration 檔案。
    -   **訊息**: `chore(db): Add [column_name] to [table_name]`

3.  **Commit 3: 新增失敗的 TDD 測試**
    -   **目的**: 定義需要被實作的核心邏輯。
    -   **內容**: 只包含 `_spec.rb` 檔案中的一個新測試案例。
    -   **訊息**: `test(tdd): Add RSpec unit test for [ClassName#method_name]`

4.  **Commit 4: 實作核心邏輯**
    -   **目的**: 寫最少的程式碼讓 TDD 測試通過。
    -   **內容**: 只包含讓上一步 TDD 測試通過的 model/service 程式碼。
    -   **訊息**: `feat(model): Implement [ClassName#method_name] method`

5.  **Commit 5: 連接 UI 與邏輯**
    -   **目的**: 寫膠水程式碼讓 BDD 測試通過。
    -   **內容**: 包含 Routes, Controller actions, View elements, Step definitions 等。
    -   **訊息**: `feat(ui): Wire up [feature name] feature`

6.  **Commit 6+: 重構 (如果需要)**
    -   **目的**: 在所有測試通過的前提下，改善程式碼結構。
    -   **內容**: 只包含重構相關的程式碼，且不能改變既有行為。
    -   **訊息**: `refactor(scope): [Description of refactoring]`

**開發循環**: BDD (Cucumber) -> TDD (RSpec) -> 實作 (Model -> UI) -> 重構

### 4. API Endpoint 開發流程範例

當開發一個不涉及 View 的 API Endpoint 時，流程與標準流程類似，但測試的重點從 UI 互動轉向 Request/Response 的驗證。我們使用 **RSpec 的 Request Specs** 來取代 Cucumber。

**場景**: 開發一個 `POST /api/v1/articles` endpoint 來建立文章。

1.  **Commit 1: 新增失敗的 Request Spec**
    -   **目的**: 定義 API 的合約（contract），包括路徑、參數、預期回傳的狀態碼與 JSON 結構。
    -   **內容**: 在 `spec/requests` 目錄下新增一個失敗的測試，驗證成功（201 Created）與失敗（422 Unprocessable Entity）的情境。
    -   **訊息**: `test(request): Add request spec for POST /api/v1/articles`

2.  **Commit 2: 進行結構性變更 (如果需要)**
    -   **目的**: 與標準流程相同。
    -   **訊息**: `chore(db): Add [column_name] to [table_name]`

3.  **Commit 3: 新增路由**
    -   **目的**: 讓 Request Spec 能找到對應的 controller action。
    -   **內容**: 在 `config/routes.rb` 中新增 API 相關的路由。
    -   **訊息**: `feat(routing): Add route for articles API`

4.  **Commit 4: 新增 Controller Action 與核心邏輯**
    -   **目的**: 寫最少的程式碼讓 Request Spec 通過。對於 API，Controller 的職責較重，可以直接在此實作邏輯（或呼叫 Service Object）。
    -   **內容**: 新增 Controller action，處理參數、呼叫 Model/Service、並回傳 JSON。
    -   **訊息**: `feat(api): Implement POST /api/v1/articles endpoint`

5.  **Commit 5+: 重構 (如果需要)**
    -   **目的**: 在測試通過後，改善 Controller 或將複雜邏輯抽取到 Service Object 中。
    -   **內容**: 重構 Controller 或新增 Service Object 及其單元測試。
    -   **訊息**: `refactor(api): Extract article creation logic to a service object`

**開發循環**: TDD (Request Spec) -> 實作 (Route -> Controller) -> 重構
