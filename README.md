# Bài 2: Biên dịch đóng gói tệp ứng dụng .jar với Gradle và lưu trữ Artifacts

## 1. Mục tiêu
- Thực thi câu lệnh biên dịch và đóng gói ứng dụng Spring Boot thành tệp thực thi `.jar` bằng Gradle (`./gradlew bootJar`).
- Lưu trữ thành phẩm biên dịch (`.jar`) từ thư mục `build/libs/` lên GitHub Actions bằng action `actions/upload-artifact@v4`.

---

## 2. Kịch bản CI (`.github/workflows/ci.yml`)

Kịch bản workflow đã được bổ sung 2 bước đóng gói và lưu trữ Artifact:

```yaml
name: Java CI with Gradle

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:
  test:
    name: Build, Test & Package Application
    runs-on: ubuntu-latest

    steps:
      - name: Checkout mã nguồn
        uses: actions/checkout@v4

      - name: Thiết lập môi trường Java JDK 17
        uses: actions/setup-java@v5
        with:
          java-version: '17'
          distribution: 'temurin'

      - name: Cấp quyền thực thi cho Gradle Wrapper
        run: chmod +x gradlew

      - name: Chạy kiểm thử tự động với Gradle
        run: ./gradlew test

      # --- Bước bổ sung theo Bài 2 ---
      - name: Đóng gói tệp JAR với Gradle
        run: ./gradlew bootJar

      - name: Lưu trữ thành phẩm JAR Artifact
        uses: actions/upload-artifact@v4
        with:
          name: app-jar
          path: build/libs/*.jar
```

---

## 3. Giải thích các bước thực hiện

1. **`run: ./gradlew bootJar`**:
   - Câu lệnh của Spring Boot Gradle Plugin dùng để đóng gói ứng dụng thành một tệp thực thi độc lập (executable JAR/fat JAR/uber JAR).
   - Tệp `.jar` sau khi build thành công sẽ được sinh ra tại thư mục `build/libs/`.

2. **`uses: actions/upload-artifact@v4`**:
   - Action chính thức của GitHub để tải lên các tệp/thư mục sản phẩm (artifacts) từ môi trường máy ảo (runner) lưu lên GitHub.
   - **`name: app-jar`**: Đặt tên cho artifact hiển thị trên giao diện GitHub Actions.
   - **`path: build/libs/*.jar`**: Cấu hình mẫu đường dẫn tìm tất cả các tệp `.jar` trong thư mục `build/libs/` để tải lên.

---

## 4. Hướng dẫn kiểm tra và nộp bài

1. **Đẩy commit lên GitHub**:
   ```bash
   git add .
   git commit -m "feat: add bootJar step and upload-artifact in CI workflow"
   git push origin main
   ```

2. **Kiểm tra trên GitHub Actions**:
   - Truy cập vào Repository trên GitHub -> chọn tab **Actions**.
   - Nhấn vào Workflow run mới nhất vừa được kích hoạt bởi commit vừa push.
   - Đợi Job chạy hoàn tất (biểu tượng tích xanh ✔️).
   - Vào mục **Summary** của Workflow run.
   - Tại phần **Artifacts** ở dưới cùng trang Summary, kiểm tra tệp **`app-jar`** xuất hiện và có thể tải về (download) dưới dạng `.zip`.

3. **Chụp ảnh màn hình**:
   - Chụp ảnh màn hình trang **Summary** hiển thị:
     - Trạng thái Workflow hoàn thành thành công.
     - Khu vực **Artifacts** có artifact **`app-jar`**.
   - Lưu ảnh vào thư mục `homework/session_11/ex2/` (ví dụ: `homework/session_11/ex2/artifacts-summary.png`).
