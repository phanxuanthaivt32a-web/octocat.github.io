# Dashboard Trạm BTS — Hướng dẫn cài đặt trên Android

## Các file cần thiết (trong thư mục `outputs`)

| File | Mục đích |
|---|---|
| `bts-dashboard.html` | **File chính** — mở là chạy được |
| `manifest.webmanifest` | Cấu hình PWA (tên, icon…) |
| `sw.js` | Service worker — cache offline |
| `icon.svg` | Icon app |

**Quan trọng:** cần upload **cả 4 file** lên cùng một thư mục online thì PWA mới hoạt động.

---

## CÁCH 1 — Nhanh nhất (5 phút): Netlify Drop → cài lên điện thoại

### Bước 1 — Lên máy tính, đưa file lên web miễn phí
1. Mở https://app.netlify.com/drop (không cần đăng ký).
2. **Kéo cả thư mục `outputs`** (chứa 4 file kể trên) thả vào ô trên trang Netlify.
3. Vài giây sau Netlify trả về một URL kiểu `https://abc-xyz-12345.netlify.app`.
4. URL đầy đủ của dashboard sẽ là: `https://abc-xyz-12345.netlify.app/bts-dashboard.html`
5. Copy URL này.

### Bước 2 — Trên điện thoại Android
1. Mở **Chrome** (phải là Chrome, không phải Cốc Cốc / Samsung Browser).
2. Dán URL từ bước trên vào Chrome, truy cập.
3. Sau vài giây, Chrome hiện thanh "Cài đặt Dashboard" hoặc bạn bấm nút **⋮** (3 chấm trên cùng góc phải) → chọn **"Thêm vào Màn hình chính"** / **"Cài đặt ứng dụng"**.
4. Xác nhận "Cài đặt" — icon dashboard xuất hiện trên màn hình chính như một app thực sự.
5. Mở icon đó → app chạy fullscreen, không có thanh URL của Chrome, dùng được cả khi không có Internet (sau lần đầu mở online).

### Bước 3 — Sử dụng
1. Mở app, bấm **6 slot upload** trên giao diện:
   - File 1: Báo cáo trạm (CSV)
   - File 2: Chi phí duy trì trạm (CSV)
   - File 3-5: MTCL Tháng 2 / 3 / 4 (CSV hoặc XLSX)
   - File 6: Phân công nhân sự (CSV hoặc XLSX)
2. Mỗi file upload xong, dashboard cập nhật ngay. Có file nào dùng file đó, không bắt buộc đủ 6.
3. Dữ liệu **lưu trên máy bạn** (localStorage cho File 1-2; bộ nhớ phiên cho MTCL/PC) — không gửi lên server.

---

## CÁCH 2 — Tạo file APK thực sự (cài như app thường, không cần Chrome)

Sau khi đã có URL từ Cách 1, dùng PWABuilder của Microsoft để build APK:

1. Lên máy tính, mở https://www.pwabuilder.com
2. Dán URL `https://abc-xyz-12345.netlify.app/bts-dashboard.html` vào ô tìm kiếm → bấm **Start**.
3. PWABuilder phân tích → hiện điểm số PWA. Bấm **Package For Stores**.
4. Chọn **Android** → bấm **Generate Package**.
5. Có thể dùng tuỳ chọn mặc định (chỉ cần sửa Package ID kiểu `com.bts.dashboard`).
6. Tải file `.zip` về → giải nén → bên trong có file `.apk` (signed) hoặc `.aab`.
7. Copy file `.apk` sang điện thoại (qua USB / Zalo / Drive).
8. Trên điện thoại: mở file `.apk` → cài đặt (cần bật **"Cài đặt từ nguồn không xác định"** trong Cài đặt → Bảo mật).

**Lưu ý APK:** đây là Trusted Web Activity, vẫn cần URL Netlify online để app fetch HTML/JS. Tuy nhiên sau lần đầu tiên, service worker cache toàn bộ → dùng offline được.

---

## CÁCH 3 — Không có Internet để host: Mở trực tiếp file HTML

Nếu không tiện upload online, bạn vẫn xem được dashboard:

1. Copy 4 file vào điện thoại (qua USB hoặc Zalo).
2. Cài app **Files by Google** hoặc trình duyệt file mặc định.
3. Bấm chọn `bts-dashboard.html` → mở bằng Chrome.
4. Chạy được nhưng **không cài được thành app** (PWA chỉ hoạt động qua HTTPS).

---

## 10 tab phân tích trong dashboard

| # | Tab | Nội dung |
|---|---|---|
| 1 | 📊 Tổng quan | Top 10 lãi / lỗ, phân bố biên lợi nhuận |
| 2 | 🏆 Xếp hạng trạm | Bảng toàn bộ trạm, lọc theo phường / loại |
| 3 | 📡 KPI / MTCL | PRB, RSRP, CQI, Accessibility, Retainability, Throughput, lưu lượng (mới) |
| 4 | 🛒 Khuyến nghị bán Sim | NÊN BÁN / CÂN NHẮC / KHÔNG NÊN theo 4 tiêu chí (mới) |
| 5 | 👥 Theo nhân sự | View tổng hợp theo từng người VHKT + breakdown CSHT (mới) |
| 6 | 🆕 Trạm mới GLI | Site GLIxxx chưa có số liệu — đã tách riêng (mới) |
| 7 | ⚠️ Bất thường CP | Z-score > 2 trên chi phí |
| 8 | 📈 Xu hướng | Doanh thu / chi phí theo tháng, top 8 phường |
| 9 | 🥧 Cơ cấu chi phí | Pie tỉ trọng + chi phí TB theo loại trạm |
| 10 | 💡 Khuyến nghị | Sinh tự động, sắp xếp theo mức tiết kiệm tiềm năng |

Nút **⬇️ Excel** ở header xuất báo cáo `.xlsx` 8 sheet đầy đủ.

---

## Ngưỡng đánh giá (chỉnh được trong code)

```js
TH = {
  prbHigh: 80,      // ≥ 80% PRB → quá tải → KHÔNG NÊN bán
  prbLow: 30,       // < 30% PRB → còn dư nhiều tải
  prbSweet: 70,     // 30-70% → tối ưu để bán
  rsrpGood: -95,    // ≥ -95 dBm → vùng phủ tốt
  rsrpBad: -110,    // ≤ -110 dBm → vùng phủ yếu
  cqiGood: 8,
  accGood: 98,      // Accessibility ≥ 98%
  retGood: 98,      // Retainability ≥ 98%
  tpGood: 10        // Throughput ≥ 10 Mbps
}
```

Muốn đổi ngưỡng, mở `bts-dashboard.html` bằng Notepad, tìm `const TH = {` và sửa số.

---

## Cập nhật dữ liệu hàng tháng

Trong app/dashboard, bấm **↻ Tải lại** → upload file mới (có thể đè lên dữ liệu cũ). Site GLI mới sẽ tự động được tách ra tab riêng.
