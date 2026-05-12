# Dashboard Trạm BTS — Hướng dẫn sử dụng

## File trong gói

| File | Mục đích |
|---|---|
| `bts-dashboard.html` | **File chính.** Mở file này trong trình duyệt là chạy được ngay. |
| `manifest.webmanifest` | Khai báo PWA (tên app, icon…) — chỉ cần khi cài như app. |
| `sw.js` | Service worker — cache offline khi cài PWA. |
| `icon.svg` | Icon app. |

## Cách 1 — Dùng nhanh, không cài app (Khuyến nghị thử trước)

1. Mở `bts-dashboard.html` bằng Chrome / Edge / Safari trên máy tính HOẶC điện thoại.
2. Bấm chọn **2 file CSV** đã export từ Excel:
   - File 1: Báo cáo trạm (`baocaotramthang_*.csv`)
   - File 2: Chi phí duy trì trạm (`CP duy trì trạm…csv`)
3. Dashboard hiện ra với 6 tab phân tích.
4. Dữ liệu được lưu trong trình duyệt (localStorage) — lần sau mở lại không cần upload nữa.

**Tất cả xử lý chạy trên máy bạn. Không có dữ liệu nào gửi ra ngoài.**

## Cách 2 — Cài lên điện thoại Android (PWA / "app")

### Bước 1: Upload toàn bộ thư mục `outputs` lên một web host miễn phí
- **GitHub Pages** (khuyến nghị): https://pages.github.com — push 4 file lên repo, bật Pages.
- **Netlify Drop**: https://app.netlify.com/drop — kéo thả thư mục `outputs`, có URL ngay.
- **Vercel**: https://vercel.com — tương tự.

Sau khi host xong, bạn có URL kiểu `https://username.github.io/bts-dashboard/bts-dashboard.html`.

### Bước 2: Trên điện thoại Android
1. Mở URL bằng **Chrome**.
2. Chrome sẽ hiện banner "Cài Dashboard vào điện thoại" → bấm Cài.
3. Hoặc thủ công: menu Chrome (⋮) → **Thêm vào Màn hình chính** / "Add to Home Screen".
4. Icon Dashboard xuất hiện như một app riêng, mở fullscreen, chạy offline.

### Bước 3: Trên iPhone
1. Mở URL bằng **Safari**.
2. Bấm nút Share → **Thêm vào Màn hình chính** / "Add to Home Screen".

## Cách 3 — Đóng gói thành file APK thật

Nếu bạn *thực sự* cần file `.apk` để cài thủ công (không qua Chrome), có 2 cách:

### A. Dịch vụ tự động (dễ nhất — không cần code)
Sau khi đã host dashboard online (Cách 2 bước 1), dùng một trong các dịch vụ:
- **PWABuilder** (Microsoft, miễn phí): https://www.pwabuilder.com → dán URL → Generate Android Package → tải file `.apk` hoặc `.aab`.
- **WebIntoApp**: https://www.webintoapp.com — dán URL, đặt tên app, tải APK.
- **AppsGeyser**: https://appsgeyser.com

Các dịch vụ này wrap PWA của bạn trong WebView → tạo APK installable.

### B. Tự build (cho dev)
- Dùng **Capacitor** (Ionic): `npm i @capacitor/core @capacitor/cli`, copy dashboard vào `www/`, `npx cap add android`, `npx cap open android` → Android Studio build APK.
- Hoặc dùng **Bubblewrap** (Google) cho Trusted Web Activity.

## Cấu trúc phân tích

Dashboard có 6 tab:

1. **Tổng quan** — Top 10 lãi nhất / Top 10 lỗ nặng / Phân bố biên lợi nhuận.
2. **Xếp hạng trạm** — Bảng toàn bộ 1322 trạm, sắp xếp + lọc theo phường / loại / lãi-lỗ.
3. **Bất thường** — Chi phí có z-score > 2 (cao bất thường) + so sánh chênh lệch giữa File 1 và File 2.
4. **Xu hướng** — Doanh thu / chi phí theo tháng, top 8 phường tăng trưởng nhanh.
5. **Cơ cấu chi phí** — Pie tỉ trọng các khoản chi phí + chi phí trung bình theo loại trạm (MACRO / CRAN / MORAN).
6. **Khuyến nghị** — Sinh tự động, sắp xếp theo mức tiết kiệm tiềm năng.

## Lưu ý về dữ liệu

- **File 1** (Báo cáo trạm) chứa Doanh thu, lưu lượng, số TB — đầy đủ 4 tháng.
- **File 2** (CP duy trì) chứa chi phí chi tiết — chỉ có 2 tháng.
- Cột "Chi phí trạm" trong File 1 thường thấp hơn tổng chi phí trong File 2 ~30% (do File 1 chỉ tính một số khoản). Dashboard sẽ ưu tiên dùng File 2 nếu có.
- Vài trạm bị thiếu dữ liệu chi phí trong File 1 — dashboard sẽ flag.

## Cập nhật dữ liệu hàng tháng

Mỗi tháng:
1. Export lại 2 file CSV mới từ Excel (đè tên cũ hoặc đặt tên mới).
2. Mở dashboard → bấm **↻ Tải lại CSV**.
3. Chọn 2 file mới — dashboard cập nhật tức thì.
