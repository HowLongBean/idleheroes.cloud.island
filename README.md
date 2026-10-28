# idleheroes.cloud.island
Cloud Island Simulator

## 圖片選擇頁面

開啟 [index.html](./index.html)，點擊圖片框即可從 [Landlord](./Landlord) 圖庫選取圖片。
選擇後會顯示圖片，再次點擊即可更換。關閉按鈕、Esc 或點擊視窗外可取消選擇。
頁面使用純 HTML、CSS 與 JavaScript，不需要安裝套件或後端；重新整理後會清除選擇。

### 調整圖片順序

編輯 [index.html](./index.html) 中的 `landlordImages` 陣列：

```js
const landlordImages = [
  "alikita_trans.png",
  "basilisk_trans.png",
  // 其餘圖片……
];
```

- 初始陣列已列出目前 Landlord 中的所有圖片，按檔名字母排序。
- 將項目往前或往後移動，即可調整選項顯示順序；程式不會再次排序。
- 新增圖片時，先將檔案放進 Landlord，再將完整檔名加入陣列；刪除圖片時也要移除對應項目。
- 檔名與副檔名的大小寫必須完全一致。清單不會自動掃描資料夾，也不會開啟本機檔案選擇器。

### 發布到 GitHub Pages

1. 將 index.html 與 Landlord 資料夾提交並推送至 GitHub。
2. 在儲存庫的 **Settings → Pages → Build and deployment** 選擇 **Deploy from a branch**。
3. 選擇存放上述檔案的分支，資料夾選 **/(root)**，按 **Save**。
4. 部署完成後，開啟 Pages 顯示的網址。

圖片使用相對路徑，可直接部署至 GitHub Pages 的專案子路徑，不需修改網址。
