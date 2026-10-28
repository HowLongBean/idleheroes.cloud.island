# idleheroes.cloud.island
Cloud Island Simulator

## 圖片選擇頁面

開啟 [index.html](./index.html)，點擊圖片框即可從 [Landlord](./Landlord) 圖庫選取圖片。
屋主在左側，房客 1～4 依序橫向並排在右側，五個選框等寬；螢幕較窄時可在選框區域左右捲動。
彈出的圖庫視窗維持原本大小。
選定屋主後，右側房客選框才會啟用，可從 [Tenant](./Tenant) 選取該屋主允許的圖片。
四個房客各自保存選擇，互不影響，也可選擇相同圖片。
更換成另一位屋主時會清除全部四個已選房客；重新選擇同一位屋主或取消選擇不會清除房客。
選擇後會顯示圖片，再次點擊即可更換。關閉按鈕、Esc 或點擊視窗外可取消選擇。
頁面使用純 HTML、CSS 與 JavaScript，不需要安裝套件或後端；一般網址重新整理後會清除選擇，含分享參數的網址則會還原連結記錄的設定。

### 分享目前設定

1. 設定各組屋主與房客後，點擊 **產生分享網址**。
2. 網址會自動複製到作業系統剪貼簿，看到成功提示後即可直接貼上分享；網址欄位也會保留並自動全選。
3. 他人開啟連結時，會自動還原組數、順序、各組屋主與四個房客位置，包括尚未選擇的空白位置。

分享連結使用 `https://howlongbean.github.io/idleheroes.cloud.island/`，由 [index.html](./index.html) 的 `shareBaseUrl` 設定。
即使從本機檔案預覽，也會產生此正式網站的連結；必須將新版網頁與圖片部署到該網址，其他人才能使用。

- 網址的 `state` 查詢參數保存版本與圖片檔名，使用瀏覽器內建的 DEFLATE 壓縮及 Base64URL 編碼，格式為 `state=z1.…`，不依賴外部套件、暫存、帳號或後端。
- 新版網頁仍可還原舊版未壓縮的 JSON 分享連結；舊版網頁無法解讀新的壓縮連結，請先部署新版。
- 產生及還原壓縮網址需支援 `CompressionStream`／`DecompressionStream` 的現代瀏覽器；不支援時會顯示更新瀏覽器的提示。
- 自動複製使用瀏覽器 Clipboard API，受安全連線、使用者操作與剪貼簿權限限制。若不支援或被拒絕，會顯示提示並保留網址供手動複製，不會誤報複製成功。
- 只記錄目前的組別與圖片選擇，不包含原始碼中的顯示名稱或可選清單設定；還原時以網站目前的圖片設定為準。
- 更改選擇、新增或刪除組別後，舊的網址欄位會清除，請重新產生連結。已複製的網址仍代表產生當下的設定。
- 壓縮期間若設定改變，不會顯示過期的結果；還原期間會暫時停用操作按鈕，避免與載入的設定混合。
- 缺少分享參數時顯示預設一組；參數損壞、版本不支援、圖片不存在於設定清單或房客不允許選取時，會顯示錯誤並保留一組空白住宅，不會部分還原。
- 壓縮不是加密，取得網址的人都能還原設定。實際縮短幅度依組數及圖片重複程度而異，分享平台仍可能有網址長度限制。

### Group 與動態新增、刪除

每個 group 包含一位屋主與四個房客，使用 `group-template` 建立，預設只顯示一組。
點擊 **住宅新建** 可新增一組；點擊 **住宅拆遷** 只會刪除目前最後一組及其選擇。
只剩一組時會隱藏住宅拆遷按鈕，再次新增後會重新顯示。
在頁面完成初始化後呼叫 `addGroup()`，即可在下方新增完整的一組，不需複製 HTML 或綁定事件：

```js
const group = addGroup();

// 取得這組目前的選擇；尚未選擇時檔名為 null、屋主索引為 -1。
group.selectedFile;
group.selectedLandlordIndex;
group.tenantSlots.map(slot => slot.selectedFile);
```

- 每次呼叫都建立全新、未選取的 group，並回傳該組的狀態物件；`group.element` 是對應的 DOM 容器。
- 每組共用下方的圖片設定陣列，但屋主與房客的選擇、預覽、錯誤提示皆獨立。
- 更換屋主只會清除同一組的四個房客，不會改變其他組。
- 每組都有唯一的 ID，例如 `group-1`、`group-2`；組內元素以容器限定範圍查找，避免重複 ID。
- 所有組共用同一個圖庫視窗，選取結果只會套用到開啟它的組與選框。
- 也可呼叫 `deleteGroup()` 刪除最後一組：成功回傳 `true`；只剩一組時不會刪除，會在主控台提示並回傳 `false`。
- 刪除不會影響其他組的選擇；新增的組不會繼承已刪除組的狀態。組 ID 持續遞增，不會重複使用。
- 一般網址重新整理後回到預設一組；分享網址重新整理後會還原其參數記錄的設定。

### 調整屋主圖片名稱與順序

編輯 [index.html](./index.html) 中的 `landlordImages` 陣列：

```js
const landlordImages = [
  { filename: "alikita_trans.png", name: "Alikita" },
  { filename: "basilisk_trans.png", name: "Basilisk" },
  // 其餘圖片……
];
```

- 初始陣列已列出目前 Landlord 中的所有圖片，按檔名字母排序。
- `filename` 是 Landlord 中的完整圖片檔名；`name` 是顯示名稱，可改成中文或自訂名稱。
- `name` 會用於圖庫選項文字、選項的無障礙名稱、選取後的文字與圖片替代文字。修改名稱不會改變圖片路徑或房客索引。
- 將項目往前或往後移動，即可調整選項顯示順序；程式不會再次排序。
- 新增圖片時，先將檔案放進 Landlord，再將含 `filename` 與 `name` 的物件加入陣列；刪除圖片時也要移除對應項目。
- 檔名與副檔名的大小寫必須完全一致。清單不會自動掃描資料夾，也不會開啟本機檔案選擇器。

### 設定每位屋主可選的房客

[index.html](./index.html) 中的四組二維陣列分別控制各房客的選項：

| 選框 | 設定陣列 |
| --- | --- |
| 房客 1 | `tenantImages` |
| 房客 2 | `tenantImages2` |
| 房客 3 | `tenantImages3` |
| 房客 4 | `tenantImages4` |

每組陣列都以相同索引對應 `landlordImages`，索引從 0 開始。
例如 `tenantImages2[0]` 是 `landlordImages[0]` 的房客 2 清單，
`tenantImages2[1]` 則是下一位屋主的房客 2 清單；修改房客 2 不會影響其他房客。

`allTenantImages` 列出目前 Tenant 中的全部 61 張圖片，初始按檔名字母排序。
目前 `tenantImages[0]` 僅提供 `aranea.png` 與 `aranea_trans.png`，也就是第一位屋主的房客 1 選項。
其餘清單各自使用 `[...allTenantImages]`，允許選取全部房客圖片。
可將對應項目換成指定檔名的陣列，例如：

```js
const tenantImages = [
  ["annabelle.png", "alikita_trans.png"], // landlordImages[0]，僅能選這兩張
  ["betty.png"],                         // landlordImages[1]，僅能選這一張
  [],                                    // landlordImages[2]，不提供房客選項
  [...allTenantImages],                   // landlordImages[3]，可選全部
  // 保留其餘屋主對應的項目……
];
```

- 房客選項完全依照對應子陣列的內容及順序顯示，不會自動排序或補入其他圖片。
- 房客 2～4 的設定方式與上方房客 1 範例相同，分別編輯 `tenantImages2`、`tenantImages3`、`tenantImages4`。
- 調整 `landlordImages` 的順序、新增或移除屋主時，請同步調整四組房客陣列的對應項目。
- 子陣列為 `[]` 時只會停用對應的房客選框並顯示提示；缺少對應子陣列或格式錯誤時會在該選框顯示設定錯誤。
- 新增 Tenant 圖片時，請加入 `allTenantImages` 或指定屋主的子陣列；必須使用與檔案一致的完整檔名。
- 房客 1～4 在主頁選取後與圖庫選項中只顯示圖片，不顯示檔名文字；保留圖片替代文字與選項的無障礙名稱。屋主的自訂顯示名稱不受影響。

### 發布到 GitHub Pages

1. 將 index.html、Landlord 與 Tenant 資料夾提交並推送至 GitHub。
2. 在儲存庫的 **Settings → Pages → Build and deployment** 選擇 **Deploy from a branch**。
3. 選擇存放上述檔案的分支，資料夾選 **/(root)**，按 **Save**。
4. 部署完成後，開啟 Pages 顯示的網址。

圖片使用相對路徑，可直接部署至 GitHub Pages 的專案子路徑，不需修改網址。
