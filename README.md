# 📚 Mini Reading Tracker

<p align="center">
  <img src="https://img.shields.io/badge/Vue.js-35495E?style=for-the-badge&logo=vue.js&logoColor=4FC08D" alt="Vue.js" />
  <img src="https://img.shields.io/badge/Vite-B73BFE?style=for-the-badge&logo=vite&logoColor=FFD62E" alt="Vite" />
  <img src="https://img.shields.io/badge/Node.js-43853D?style=for-the-badge&logo=node.js&logoColor=white" alt="Node.js" />
  <img src="https://img.shields.io/badge/MySQL-00000F?style=for-the-badge&logo=mysql&logoColor=white" alt="MySQL" />
</p>

Một ứng dụng web giúp người dùng **tìm kiếm sách**, **lưu vào tủ sách cá nhân**, và **theo dõi tiến độ đọc** một cách trực quan. Dữ liệu sách được tích hợp từ Public API của [Open Library](https://openlibrary.org/).

### 📸 Giao diện ứng dụng

<p align="center">
  <img src="./image/search.png" width="48%" alt="Tìm kiếm sách" />
  <img src="./image/Library.png" width="48%" alt="Tủ sách cá nhân" />
</p>
<p align="center">
  <img src="./image/Reading.png" width="48%" alt="Đang đọc" />
  <img src="./image/Completed.png" width="48%" alt="Đã hoàn thành" />
</p>

---

## 📑 Mục lục
- [✨ Tính năng chính](#-tính-năng-chính)
- [🛠️ Công nghệ sử dụng](#️-công-nghệ-sử-dụng)
- [🚀 Hướng dẫn chạy Local](#-hướng-dẫn-chạy-local-môi-trường-phát-triển)
- [🏗️ Mô tả kiến trúc](#️-mô-tả-kiến-trúc)
- [📡 Danh sách API](#-danh-sách-api-backend-endpoints)
- [🌐 Triển khai (Deploy)](#-triển-khai-deploy)
- [💡 Giả định & Hướng cải thiện](#-giả-định--hướng-cải-thiện)

---

## ✨ Tính năng chính
- 🔍 **Tìm kiếm sách**: Dễ dàng tìm kiếm thông tin sách từ nguồn dữ liệu khổng lồ của Open Library.
- 📖 **Quản lý tủ sách**: Lưu trữ và phân loại sách theo các trạng thái (Muốn đọc, Đang đọc, Đã đọc).
- 📈 **Theo dõi tiến độ**: Cập nhật số trang đã đọc, đánh giá (rating) và thêm ghi chú cá nhân.
- ⚡ **Tự động hóa**: Tự động chuyển trạng thái hoàn thành khi đọc hết số trang sách.

---

## 🛠️ Công nghệ sử dụng

- **Frontend:** Vue.js 3, Vite, Vanilla CSS.
- **Backend:** Node.js, Axios.
- **Database:** MySQL.
- **Tích hợp API:** [Open Library Search API](https://openlibrary.org/dev/docs/api/search) & [Books API](https://openlibrary.org/dev/docs/api/books).

---

## 🚀 Hướng dẫn chạy Local (Môi trường phát triển)

### Yêu cầu hệ thống:
- [Node.js](https://nodejs.org/en/) (phiên bản 16 trở lên)
- [MySQL Server](https://dev.mysql.com/downloads/mysql/) đang hoạt động.

### Bước 1: Khởi tạo Database
1. Sử dụng file `structsql.txt` được cung cấp trong thư mục dự án và chạy script trong MySQL để tạo cấu trúc bảng.

### Bước 2: Chạy Backend (Node.js)
1. Mở Terminal và di chuyển vào thư mục backend:
   ```bash
   cd be
   ```
2. Cài đặt các gói phụ thuộc (nếu chưa cài):
   ```bash
   npm install
   ```
3. Cấu hình Database: Mở file `be/.env` và sửa đổi `DB_PASSWORD` / `DB_USER` sao cho khớp với tài khoản MySQL của bạn.
4. Chạy Server:
   ```bash
   npm start
   ```
   > **Lưu ý:** Terminal hiển thị `Node.js HTTP Server is running on http://localhost:3000` là thành công.

### Bước 3: Chạy Frontend (Vue.js)
1. Mở một Terminal khác, di chuyển vào thư mục frontend:
   ```bash
   cd frontend
   ```
2. Cài đặt các gói phụ thuộc:
   ```bash
   npm install
   ```
3. Khởi chạy Vite Dev Server:
   ```bash
   npm run dev
   ```
4. Truy cập ứng dụng qua đường dẫn được cung cấp (thường là `http://localhost:5173`).

---

## 🏗️ Mô tả kiến trúc

- **Kiến trúc Client-Server:**
  - Frontend (Vue) không gọi trực tiếp API của bên thứ 3 (Open Library) để tránh lộ logic và gặp lỗi CORS.
  - Backend Node.js đóng vai trò như một Proxy server: Gọi dữ liệu, lọc gọn chuỗi JSON khổng lồ trả về dạng đơn giản nhất, sau đó mới gửi lại cho Frontend.
- **Sơ đồ Database (Bảng `books`):**
  - Quản lý metadata sách: `open_library_work_id`, `title`, `author`, `cover_url`, `total_pages`,...
  - Quản lý tracking cá nhân: `status` (Enum: `WANT_TO_READ`, `READING`, `READ`), `current_page`, `rating`, `note`.
  - Tự động hóa mốc thời gian: `started_at`, `finished_at`.
  - Indexing: Được thiết lập chỉ mục (index) cho `status` và `created_at` để tối ưu tốc độ truy xuất.

---

## 📡 Danh sách API (Backend endpoints)

**Base URL:** `http://localhost:3000`

### 1. Proxy Open Library
- `GET /api/books/search?q={keyword}&page={n}&limit=20`: Tìm kiếm sách.
- `GET /api/books/:workId`: Lấy chi tiết tác phẩm (Ví dụ: `/api/books/OL27448W`).

### 2. Library CRUD
- `GET /api/library`: Lấy danh sách tủ sách cá nhân (Hỗ trợ query filter, VD: `?status=READING`).
- `GET /api/library/stats`: Lấy thống kê số lượng (Tổng số / Đang đọc / Đã đọc).
- `POST /api/library`: Thêm sách vào tủ. Trả về `409 Conflict` nếu trùng lặp `workId`.
- `PATCH /api/library/:id`: Cập nhật tiến độ đọc (`current_page`), trạng thái, đánh giá (`rating`), ghi chú (`note`).
  - *Logic tự động:* Chuyển trạng thái hoàn thành (`READ`) nếu số trang đọc bằng tổng số trang.
- `DELETE /api/library/:id`: Xoá sách khỏi tủ.

---

## 🌐 Triển khai (Deploy)

Dự án được triển khai (deploy) hoàn toàn miễn phí trên các nền tảng đám mây:

- **Frontend URL:** [https://mini-reading-tracker-iota.vercel.app/](https://mini-reading-tracker-iota.vercel.app/)
- **Backend URL:** [https://mini-reading-tracker-rj7q.onrender.com](https://mini-reading-tracker-rj7q.onrender.com)

### Các bước và cấu hình triển khai:

1. **Database (Railway):**
   - Tạo một dự án mới trên Railway và thêm MySQL plugin.
   - Lấy thông tin kết nối (Host, User, Password, Port).
   - Sử dụng công cụ MySQL Workbench kết nối vào database và chạy nội dung file `structsql.txt` để khởi tạo cấu trúc bảng.

2. **Backend Node.js (Render):**
   - Tạo mới một "Web Service" trên Render và liên kết với Github repository của dự án.
   - Cấu hình:
     - Root Directory: `be`
     - Build Command: `npm install`
     - Start Command: `npm start`
   - **Environment Variables:** Thiết lập các biến môi trường kết nối đến Railway MySQL (`DB_HOST`, `DB_USER`, `DB_PASSWORD`, `DB_NAME`, `DB_PORT`) và cấu hình `CORS_ORIGIN` trỏ về URL của Frontend để tránh lỗi CORS.

3. **Frontend Vue.js (Vercel):**
   - Import Github repository vào Vercel.
   - Cấu hình:
     - Root Directory: `frontend`
     - Framework Preset: `Vite`
     - Build Command: `npm run build`
     - Output Directory: `dist`
   - Cấu hình file biến môi trường (nếu có thiết lập) để API URL trỏ về Backend URL của Render.

---

## 💡 Giả định, Hạn chế & Hướng cải thiện

### 📌 Các giả định (Assumptions)
- **Thiếu hụt dữ liệu API:** API Open Library không phải lúc nào cũng trả về đủ thông tin (vd: `total_pages`). Hệ thống giả định và thiết lập logic bỏ qua kiểm tra tự động chuyển trạng thái (*auto-complete*) nếu bị thiếu dữ liệu này để tránh gây lỗi ứng dụng.
- **Người dùng đơn lẻ:** Hệ thống hiện tại được thiết kế phục vụ cho một người dùng duy nhất quản lý tủ sách cá nhân.

### ⚠️ Hạn chế (Limitations)
- **Hiệu suất API bên thứ 3:** Do sử dụng API công khai miễn phí của Open Library, một số truy vấn tìm kiếm sách có thể phản hồi chậm hoặc bị giới hạn tỉ lệ (rate limit).
- **Chưa có Authentication:** Không có chức năng Đăng nhập/Đăng ký nên chưa hỗ trợ nhiều người dùng với dữ liệu tách biệt, độc lập.

### 🚀 Hướng cải thiện (Nếu có thêm thời gian)
- **Xác thực và phân quyền (Authentication):** Bổ sung hệ thống đăng nhập bằng JWT để hỗ trợ nhiều người dùng, cho phép mỗi người có một không gian tủ sách riêng biệt và an toàn.
- **Tối ưu hiệu suất bằng Caching:** Triển khai Redis Cache tại Backend để lưu lại các kết quả tìm kiếm trước đó từ Open Library. Việc này sẽ giúp phản hồi tức thì cho các truy vấn trùng lặp và giảm tải cho API.
- **Trải nghiệm người dùng (UX):**
  - Thêm **Pagination (Phân trang)** hoặc **Infinite Scroll** để tối ưu hóa việc render danh sách thư viện cá nhân khi lượng dữ liệu lớn.
  - Bổ sung **Dashboard thống kê trực quan** bằng các biểu đồ (ví dụ: Chart.js) để thể hiện biểu đồ đọc sách theo tháng, thể loại sách yêu thích.
  - Cung cấp tính năng **Dark Mode** hiện đại.
