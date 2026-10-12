# BÀI TẬP 1: KHỞI TẠO WORKFLOW CHẠY UNIT TEST TỰ ĐỘNG CHO JAVA SPRING BOOT VỚI GRADLE

**Học phần:** Điện toán đám mây / CI-CD & DevOps (IT209)  
**Session:** 11 - CI/CD Pipeline với GitHub Actions  
**Thư mục bài làm:** `Lession11/Bai01`  
**GitHub Submission Path:** `homework/session_11/ex1/`

---

## 1. Mục tiêu bài thực hành
- Thiết lập quy trình Tích hợp liên tục (Continuous Integration - CI) tự động hóa kiểm thử mã nguồn dự án Java Spring Boot.
- Khởi tạo tệp cấu hình kịch bản GitHub Actions Workflow: `.github/workflows/ci.yml`.
- Tự động kích hoạt kiểm thử mỗi khi có commit mới được đẩy (`push`) lên nhánh `main`.
- Thiết lập môi trường thực thi trên máy ảo Ubuntu (`ubuntu-latest`) cài đặt Java JDK 17 (Temurin).
- Chạy toàn bộ bộ kiểm thử JUnit thông qua Gradle Wrapper (`./gradlew test`) và xác nhận trạng thái thành công (tích xanh - Success).

---

## 2. Cấu trúc thư mục dự án

```text
Bai01/
├── .github/
│   └── workflows/
│       └── ci.yml               # Kịch bản GitHub Actions Workflow (CI)
├── gradle/
│   └── wrapper/                 # Gradle Wrapper distribution & properties
├── src/
│   ├── main/java/               # Mã nguồn ứng dụng Spring Boot
│   └── test/java/               # Các bài kiểm thử Unit Test (JUnit 5 & Mockito)
│       └── org/example/student/
│           ├── StudentApplicationTests.java
│           ├── controller/
│           └── service/
│               └── StudentServiceTest.java
├── build.gradle                 # Cấu hình Gradle build & toolchain Java 17
├── settings.gradle              # Tên dự án
├── gradlew                      # Script Gradle wrapper cho Linux/macOS
├── gradlew.bat                  # Script Gradle wrapper cho Windows
└── README.md                    # Hướng dẫn chi tiết & báo cáo kiểm thử
```

---

## 3. Nội dung tệp kịch bản `.github/workflows/ci.yml`

```yaml
name: Java Spring Boot CI (Gradle)

on:
  push:
    branches: [ "main" ]

jobs:
  test:
    name: Run Unit Tests
    runs-on: ubuntu-latest

    steps:
      - name: Checkout mã nguồn
        uses: actions/checkout@v5

      - name: Thiết lập môi trường Java JDK 17
        uses: actions/setup-java@v5
        with:
          java-version: '17'
          distribution: 'temurin'

      - name: Cấp quyền cho Gradle Wrapper
        run: chmod +x gradlew

      - name: Thực thi JUnit Test với Gradle
        run: ./gradlew test
```

### Phân tích chi tiết các khối lệnh:
1. **`name: Java Spring Boot CI (Gradle)`**: Định danh tên hiển thị của workflow trên giao diện thẻ **Actions** của GitHub.
2. **`on.push.branches: [ "main" ]`**: Bộ lọc sự kiện (Event Trigger). Workflow chỉ tự động khởi chạy khi có sự kiện đẩy mã nguồn (`git push`) vào nhánh `main`.
3. **`jobs.test`**: Định nghĩa job thực thi bài kiểm thử:
   - **`runs-on: ubuntu-latest`**: Runner chạy trên môi trường Linux Ubuntu phiên bản mới nhất do GitHub lưu trữ.
4. **Các bước thực thi (`steps`):**
   - **`actions/checkout@v5`**: Tải toàn bộ mã nguồn của repository về môi trường làm việc của runner.
   - **`actions/setup-java@v5`**: Thiết lập môi trường máy ảo Java. Khai báo `java-version: '17'` và bản phân phối `temurin` (Eclipse Temurin OpenJDK) chuẩn hóa cho các dự án doanh nghiệp.
   - **`run: chmod +x gradlew`**: Gán quyền thực thi (executable) cho tệp script `gradlew`, tránh lỗi phân quyền `Permission denied` trên môi trường Linux runner.
   - **`run: ./gradlew test`**: Kích hoạt task kiểm thử của Gradle. Gradle tự động quét và thực thi các lớp kiểm thử JUnit 5 (`StudentApplicationTests`, `StudentServiceTest`,...) và xuất báo cáo kết quả.

---

## 4. Cấu hình tương thích `build.gradle` (Java 17)

Để đảm bảo Gradle sử dụng đúng phiên bản Java 17 mà `actions/setup-java@v5` cung cấp, `build.gradle` được cấu hình toolchain như sau:

```groovy
java {
    toolchain {
        languageVersion = JavaLanguageVersion.of(17)
    }
}

tasks.named('test') {
    useJUnitPlatform()
}
```

---

## 5. Các bước kiểm tra và nộp bài

### Bước 1: Khởi tạo Git & Đẩy lên GitHub
Nếu đẩy vào repository riêng hoặc branch bài tập:
```bash
git init
git add .
git commit -m "feat: setup Java Spring Boot CI workflow with Gradle and JUnit test"
git branch -M main
git remote add origin <URL_REPOSITORY_CUA_BAN>
git push -u origin main
```

### Bước 2: Xác nhận trên giao diện GitHub Actions
1. Truy cập vào GitHub Repository của bạn.
2. Nhấn vào tab **Actions**.
3. Bạn sẽ thấy Workflow **Java Spring Boot CI (Gradle)** tự động kích hoạt với commit vừa push.
4. Bấm vào workflow run để xem Job **Run Unit Tests**:
   - Tất cả các bước: Checkout, Thiết lập JDK 17, Cấp quyền gradlew, Thực thi JUnit Test đều hiển thị biểu tượng tích xanh (**Success**).

### Bước 3: Chụp ảnh minh chứng
- Chụp ảnh màn hình toàn bộ tab **Actions** hiển thị tên Workflow, commit message, thời gian thực thi và trạng thái tích xanh thành công để đính kèm vào bài nộp.

---

## 6. Kết quả kiểm thử thực tế (Local Test Run)

Lệnh thực thi trên môi trường Ubuntu OpenJDK 17:
```bash
chmod +x gradlew
./gradlew test
```

*Kết quả chạy kiểm thử thực tế:*
- **Tổng số bài test:** 15/15 tests
- **Thất bại (Failures):** 0
- **Bỏ qua (Skipped):** 0
- **Tỷ lệ thành công:** 100% (**BUILD SUCCESSFUL**)
- **Các class test đã vượt qua:**
  + `StudentApplicationTests`: Kiểm tra khởi tạo context Spring Boot
  + `StudentControllerTest`: Kiểm tra các endpoint REST API sinh viên
  + `StudentServiceTest`: Kiểm tra toàn diện tầng nghiệp vụ (Service layer) với Mockito

