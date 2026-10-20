# Test Cases — Đăng nhập / Login
**Model:** Llama 3.1 8B (4-bit quantized on Google Colab T4)
**Feature:** Đăng nhập / Login

---

 ## Coverage Summary
- Positive: 1, 2, 3, 4, 5
- Negative: 2, 3, 4
- Boundary: 1, 2, 3, 4, 5
- Edge case: 3, 4
- Security: 3, 5

| ID       | Test Case Description            | Category    | Pre-condition / Test Data                | Test Step                                      | Expected Result          | Priority |
|----------|----------------------------------|-------------|-------------------------------------------|------------------------------------------------|--------------------------|----------|
| LOGIN_001 | Đăng nhập với thông tin hợp lệ   | Positive    | User đã có tài khoản hợp lệ               | 1. Nhập đúng email và password<br>2. Submit form | Được redirect đến màn hình Home và hiển thị tên user ở header | High      |
| LOGIN_002 | Đăng nhập với email không tồn tại | Negative    | User nhập email chưa đăng ký             | 1. Nhập email chưa đăng ký<br>2. Submit form     | Hiển thị lỗi "Email không tồn tại"                       | High      |
| LOGIN_003 | Đăng nhập với sai password        | Negative    | User nhập đúng email nhưng sai password   | 1. Nhập đúng email<br>2. Nhập sai password<br>3. Submit form | Hiển thị lỗi "Mật khẩu không chính xác"<br>4. Tài khoản bị khóa tạm thời 15 phút | High      |
| LOGIN_004 | Đăng nhập với field bỏ trống      | Negative    | User không nhập email hoặc password      | 1. Không nhập email hoặc password<br>2. Tap nút Đăng nhập | Hiển thị validation error tại field bị trống<br>3. Nút Đăng nhập bị disabled | High      |
| LOGIN_005 | Đăng nhập với yêu cầu quên mật khẩu | Edge case  | User ở màn hình Login                    | 1. Tap "Quên mật khẩu?"                         | Redirect sang màn hình Reset Password                     | Medium    |

## Coverage Summary
- Positive: LOGIN_001, LOGIN_002, LOGIN_003, LOGIN_004, LOGIN_005
- Negative: LOGIN_002, LOGIN_003, LOGIN_004
- Boundary: LOGIN_001, LOGIN_002, LOGIN_003, LOGIN_004, LOGIN_005
- Edge case: LOGIN_003, LOGIN_004
- Security: LOGIN_003, LOGIN_005

---

### Explanation of Coverage Summary
- **Positive**: Covers successful login scenarios.
- **Negative**: Covers unsuccessful login scenarios such as incorrect credentials or missing fields.
- **Boundary**: Ensures edge cases are tested for valid and invalid inputs at boundaries.
- **Edge case**: Focuses on specific conditions like multiple failed attempts leading to temporary lockout.
- **Security**: Includes checks for security features such as temporary account lockout after multiple failed attempts. 

This ensures comprehensive testing across different aspects of the login functionality. --- 
## Coverage Summary
- Positive: 1, 2, 3, 4, 5
- Negative: 2, 3, 4
- Boundary: 1, 2, 3, 4, 5
- Edge case: 3, 4
- Security: 3, 5

| ID       | Test Case Description            | Category    | Pre-condition / Test Data                | Test Step                                      | Expected Result          | Priority |
|----------|----------------------------------|-------------|-------------------------------------------|------------------------------------------------|--------------------------|----------|
| LOGIN_001 | Đăng nhập với thông tin hợp lệ   | Positive    | User đã có tài khoản hợp lệ               | 1. Nhập đúng email và password<br>2. Submit form | Được redirect đến màn hình Home và hiển thị tên user ở header | High      |
| LOGIN_002 | Đăng nhập với email không tồn tại | Negative    | User nhập email chưa đăng ký             | 1. Nhập email chưa đăng ký<br>2. Submit form     | Hiển thị lỗi "Email không tồn tại"                       | High      |
| LOGIN_003 | Đăng nhập với sai password        | Negative    | User nhập đúng email nhưng sai password   | 1. Nhập đúng email<br>2. Nhập sai password<br>3. Submit form | Hiển thị lỗi "Mật khẩu không chính xác"<br>4. Tài khoản bị khóa tạm thời 15 phút | High      |
| LOGIN_004 | Đăng nhập với field bỏ trống      | Negative    | User không nhập email hoặc password      | 1. Không nhập email hoặc password<br>2. Tap nút Đăng nhập | Hiển thị validation error tại field bị trống<br>3. Nút Đăng nhập bị disabled | High      |
| LOGIN_005 | Đăng nhập với yêu cầu quên mật khẩu | Edge case  | User ở màn hình Login                    | 1. Tap "Quên mật khẩu?"                         | Redirect sang màn hình Reset Password                     | Medium    |

## Coverage Summary
- Positive: LOGIN_001, LOGIN_002, LOGIN_003, LOGIN_004, LOGIN_005
- Negative: LOGIN_002, LOGIN_003, LOGIN_004
- Boundary: LOGIN_001, LOGIN_002, LOGIN_003, LOGIN_004, LOGIN_005
- Edge case: LOGIN_003, LOGIN_004
- Security: LOGIN_003, LOGIN_005

---

### Explanation of Coverage Summary
- **Positive**: Tests successful login scenarios.
- **Negative**: Tests unsuccessful login scenarios with incorrect credentials or missing fields.
- **Boundary**: Ensures tests cover boundary conditions for valid and invalid inputs.
- **Edge case**: Focuses on specific conditions like multiple failed attempts leading to temporary lockout.
- **Security**: Includes checks for security features such as temporary account lockout after multiple failed attempts.

This ensures comprehensive testing across different aspects of the login functionality. ---