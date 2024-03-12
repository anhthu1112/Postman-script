# Postman script

## I. Lý thuyết
### Nêu một số phương pháp thiết kế testcase, cho ví dụ

- Kỹ thuật phân vùng tương đương

Kỹ thuật phân chia các giá trị đầu vào thành các phân vùng hợp lệ và không hợp lệ, sau đó chũng ta sẽ viết kịch bản kiểm thử cho từng phần, chọn giá trị địa diện từ mỗi phân vùng làm dữ liệu thử nghiệm 

  * Thực hiện: Xác định các lớp tương đương nguyên tắc 
     * 1 lớp giá trị lớn hơn 
     * 1 lớp giá trị nhỏ hơn 
     * n lớp giá trị hợp lệ 

VD: Nhập giá tiền là một sô nguyên dương 5 ký tự 
  * Điều kiện đầu vào: Giá tiền 
  * Các lớp tương đương hợp lệ: Số nguyên dương, 5 ký tự 
  * Các lớp không hợp lệ: ký tự chữ, ký tự đặc biệt, số âm, nhỏ hơn 5 ký tự, lớn hơn 5 ký tự 

- Phân tích giá trị biên 

Là kỹ thuật kiểm thử phần mềm có liên quan dến việc xác định biên(ranh giới) của điều kiện mô  tả cho các giá trị đầu vào và chọn giá trị ở biên và bên cạnh giá trị biên làm dữ liệu kiểm thử

 * Thực hiện: 
   * Tìm ra đường biên
   * Quyết định giá trị biên 
   * Quyết định giá trị test 

Lưu ý: Luôn test giá trị "0" nếu nó nằm trong vùng kiểm tra và khi nó nằm ngoài vùng bởi 0 giá trị đặc biệt 

VD: phân tích giá trị biên cho điểm nằm trong khoảng [0,100]
Điểm ừ 0 - 100 có giá trị biên là: 
  * Giá trị nhỏ nhất: 0 
  * Giá trị lớn nhất: 100 
  * Giá trị nhỏ hơn giá trị nhỏ nhất: -1 
  * Giá trị lớn hơn giá trị lớn nhất: 101 
  * Giá trị trung bình: 50 

- Bảng quyết định 

Dùng bản để hiển thị danh sách các thao tác phần mềm được quyết định trên các điều kiện khác nhau. Chú trọng vào nhiều điều kiện để thực hiện test. Đây là phương pháp tốt nhấ để áp dụng cho trường hợp cần nhiều sự kết hợp 

Bảng quyết định hỗ trợ lựa chọn test case một các co hệ thống và có nhiều ợi ích trong việc nhận biết vấn đề tiềm ẩn và sự không rõ ràng trong bản đặc tả yêu cầu. Phương pháp này quyết định số testcase tối thiểu với độ bao phủ tối đa 

  * Thực hiện: 
    * Liệt kê tât cả các điều kiện đầu vào 
    * Tính số lượng kết hợp có thể 
    * Đặt tất cả kết hợp trong bảng 
    * Giảm thiểu các case kết hợp và quyết định các testcase 



## II. Bài tập: API tạo mới KH: POST /admin/customer.json
### Viết testcase kiểm tra status code = 400 khi không truyền họ, tên và email bằng Postmanscript

Truyền request bỏ trống email 

"pm.test(""response have field empty email"", function(){
    pm.expect(pm.response.json().errors[0].message).to.eq(""is invalid email format"")
})"

truyền request thiếu trường email/last_name/first_name

pm.test("response have miss field email/first name/last name", function(){
    pm.expect(pm.response.json().errors[0].message).to.eq("Customer must have a first name, phone number or email address");
});

### Tạo mới KH với họ, tên, email ngẫu nhiên

Method : POST 
URL:{{baseUrl}}/admin/customers.json
Resquest body: 

{
  "customer": {
    "first_name": "{{$randomFirstName}}",
    "last_name": "{{$randomLastName}}",
    "email": "{{$randomFirstName}}@example.com",
    "verified_email": true,
    "addresses": [
      {
        "address1": "123 Oak St",
        "city": "Ottawa",
        "province": "ON",
        "zip": "123 ABC",
        "last_name": "Lastnameson",
        "first_name": "Mother",
        "country": "CA"
      }
    ]
  }
}

### Kiểm tra status trả về 200 thành công khi truyền thông tin hợp lệ

pm.test("status code 200", function(){
    pm.expect(pm.response.code).to.eq(200);
});
### Kiểm tra response body trả về là Json

pm.test("response is an object", function(){
    pm.expect(pm.response.json()).to.be.an("object");
});

### Kiểm tra kiểu dữ liệu của id là number, email là string, first_name là string, last_name là string trong response body

có 2 cách kiểm tra 

Cách 1: Kiểm tra từng trường 

pm.test("response have field which is number", function(){
    pm.expect(pm.response.json().customer.id).to.be.a("number");
});

pm.test("response have field email which is string", function(){
    pm.expect(pm.response.json().customer.email).to.be.a("string");
});

cách 2: Kiểm tra schema

pm.test("response have JSON scheme", function(){
    var schema = {
        type: "object",
        properties: {
            id: {
                type: 'number'
            },
            email: {
                type: 'string'
            },
            first_name: {
                type: 'string'
            },
            last_name: {
                type: 'string'
            }

        }
    }
    pm.expect(pm.response.json()).to.have.jsonSchema(schema);

});
### Tìm cách kiểm tra emai trong kết quả giống với email khi gửi lên (khó, có thể để sau)

pm.test("response have the same email", function(){
    pm.expect(pm.response.json().customer.email).to.equal("Alexyssssss@example.com");
});

## 3. Học 1 bài khoá CoderX"