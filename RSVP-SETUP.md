# 🔧 Kích hoạt RSVP nhận phản hồi về Google Sheet

Hướng dẫn để **lời chúc + xác nhận tham dự** của khách tự chảy về một Google Sheet của bạn.

> Hiện tại form vẫn hoạt động bình thường (lưu tạm trên máy khách). Sau khi làm xong các bước dưới, phản hồi sẽ được gửi thêm về Sheet của bạn kèm thời gian.

---

## Bước 1 — Tạo Google Sheet
Tạo một Google Sheet mới trong Google Drive của bạn (đặt tên tùy ý, ví dụ *"RSVP Đám cưới Thịnh & An"*).

## Bước 2 — Mở Apps Script và dán code
Trong Sheet: menu **Extensions → Apps Script**. Xóa hết code mẫu, dán đoạn sau vào rồi bấm 💾 lưu:

```javascript
function doPost(e){
  var ss = SpreadsheetApp.getActiveSpreadsheet();
  var sheet = ss.getSheetByName('RSVP') || ss.insertSheet('RSVP');
  if (sheet.getLastRow() === 0){
    sheet.appendRow(['Thời gian','Họ tên','Khách của','Tham dự','Số người','Lời chúc']);
  }
  var p = e.parameter;
  sheet.appendRow([
    new Date(),
    p.name    || '',
    p.side    || '',
    p.attend  || '',
    p.count   || '',
    p.message || ''
  ]);
  return ContentService.createTextOutput('ok');
}
```

## Bước 3 — Deploy thành Web App
1. Bấm **Deploy → New deployment**.
2. Ở ô *Select type* (biểu tượng ⚙️), chọn **Web app**.
3. Cấu hình:
   - **Execute as:** `Me` (tài khoản của bạn)
   - **Who has access:** `Anyone`
4. Bấm **Deploy**, chấp nhận cấp quyền (chọn tài khoản → *Advanced* → *Go to project (unsafe)* → *Allow* — đây là script của chính bạn nên an toàn).
5. Copy **Web app URL** (đường link kết thúc bằng `/exec`).

> 📌 **Dán URL đó vào đây để dùng sau:**
>
> ```
> RSVP_ENDPOINT = ________________________________________________/exec
> ```

## Bước 4 — Gắn URL vào website
Có 2 cách:

**Cách A (nhờ mình):** Gửi link `/exec` cho mình, mình dán vào code và push giúp.

**Cách B (tự làm):** Mở file `index.html`, tìm dòng:
```javascript
const RSVP_ENDPOINT='';
```
Dán URL vào giữa 2 dấu nháy:
```javascript
const RSVP_ENDPOINT='https://script.google.com/macros/s/AKfy.../exec';
```
Lưu → commit → push. Xong!

---

## Kiểm tra
- Mở website, điền form RSVP và bấm **Gửi Xác Nhận**.
- Mở lại Google Sheet → sẽ thấy một dòng mới (Thời gian, Họ tên, Khách của, Tham dự, Số người, Lời chúc).

## Lưu ý
- Mỗi lần **sửa code Apps Script**, phải **Deploy lại** (Deploy → Manage deployments → ✏️ Edit → *Version: New version* → Deploy) thì thay đổi mới có hiệu lực.
- Nếu đổi Sheet khác, chỉ cần lặp lại từ Bước 2 và cập nhật URL mới.
- URL `/exec` là công khai (ai có link đều gửi được dữ liệu vào Sheet) — bình thường với form RSVP, nhưng đừng để lộ URL ra nơi không cần thiết.
