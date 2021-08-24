##Tài liệu hệ thống Paygate cho đối tác##

### 1. Thông tin chung ###
- Luồng API

```
   1. Đối tác gọi API list gói cước từ VTVcab để hiển thị
   2. Đối tác gọi API check tài khoản tồn tại từ VTVcab, nếu tồn tại mới cho thanh toán
   3. Sau khi KH thanh toán thành công, đối tác gọi API cộng gói cước từ VTVcab
```
            
- SECRET: Được cấp riêng
- payPartner: Được cấp riêng
- Các mã lỗi: 
		
	    SUCCESS: {
            CODE: 0,
            MESSAGE: 'SUCCESS'
        },
        USER_EXIST: {
           CODE: 1,
           MESSAGE: 'User tồn tại'
        },
        USER_NOT_EXIST: {
            CODE: 14,
            MESSAGE: 'User không tồn tại'
        },
        WRONG_SIGNATURE: {
           CODE: 5,
           MESSAGE: 'WRONG_SIGNATURE'
        },
        INVALID_PARAM: {
            CODE: 400,
            MESSAGE: 'INVALID_PARAM'
        },
        FAIL: {
            CODE: 61,
            MESSAGE: 'FAIL'
        },
        SYSTEM_ERROR: {
            CODE: 90,
            MESSAGE: 'SYSTEM_ERROR'
        }

### 2. Api Check User ###
- API: http://123.30.235.196:8009/partner/checkUser
- Method: POST, Content-Type: application/json
- Request:	
		
| Params    | Required | DataType | Example | Note
| --------- | -----:| --------- | -----:| --------- |
| payPartner  | true | string  | 'abc' |Đối tác: Được cấp riêng|
| username     |   true | string |'0123456789'|Tài khoản khách hàng thanh toán|  
| signature     |   true | string |xxxx|md5(payPartner.'$'.username.'$'.SECRET)|


- Response:
	    + User Exists: {CODE: 1, INFO:{username: '0123456789', packages: [{"_id": "1_1", "expire": 2080128939}]} } (dựa vào package để xác định user có gói cước chưa và thời hạn đến khi nào)
        + User Not Exists: {CODE: code} code != 1


### 3. API list package ###
- API: http://123.30.235.63:3003/api/v3/public/partnerPackageList?payPartner=abc
- Method: POST, Content-Type: application/json

- Request params: payPartner
- Response:

```
{
  "CODE": 0,
  "DATA": [
    {
      "code": "VIP_1_THANG",
      "id": "1_1",
      "price": 66000,
      "title": "Gói ON VIP 1 tháng"
    },
    {
      "code": "VIP_3_THANG",
      "id": "1_1",
      "price": 198000,
      "title": "Gói ON VIP 3 tháng"
    },
    {
      "code": "VIP_6_THANG",
      "id": "1_1",
      "price": 396000,
      "title": "Gói ON VIP 6 tháng"
    },
    {
      "code": "GD_1_THANG",
      "id": "1_2",
      "price": 88000,
      "title": "Gói ON GD 1 tháng"
    },
    {
      "code": "GD_3_THANG",
      "id": "1_2",
      "price": 264000,
      "title": "Gói ON GD 3 tháng"
    },
    {
      "code": "GD_6_THANG",
      "id": "1_2",
      "price": 528000,
      "title": "Gói ON GD 6 tháng"
    },
    {
      "code": "DA_1_THANG",
      "id": "2_6",
      "price": 50000,
      "title": "Gói ON DA 1 tháng"
    },
    {
      "code": "DA_3_THANG",
      "id": "2_6",
      "price": 150000,
      "title": "Gói ON DA 3 tháng"
    },
    {
      "code": "DA_6_THANG",
      "id": "2_6",
      "price": 300000,
      "title": "Gói ON DA 6 tháng"
    }
  ]
}
```

### 4. Api Cộng gói ###
- API: http://123.30.235.196:8009/partner/addPackage
- Method: POST, Content-Type: application/json
- Request:	
		
| Params    | Required | DataType | Example | Note|
| --------- | -----:| --------- | -----:| --------- |
| payPartner  | true | string  | 'abc' |Đối tác: Được cấp riêng|
| packageCode     |   true |string |'VIP_1_THANG'|Gói cước khách hàng thanh toán|
| username     |   true | string |'0123456789'|Tài khoản khách hàng thanh toán|
| amount     |   true | string |50000|Số tiền thanh toán|
| transId     |   true | string |'PAY_1569636721842576'|Mã ID Giao dịch: phải là duy nhất|
| logTime     |   true | string |'2020-03-03 15:00:00'|Thời gian KH thanh toán; theo định dạng: YYYY-MM-DD HH:mm:ss|  
| signature     |   true | string |xxxx|md5(payPartner.'$'.packageCode.'$'.username.'$'.amount.'$'.transId.'$'.logTime.'$'.SECRET)|


- Response:

	    + Success: {CODE: 0, MESSAGE:'SUCCESS', DATA: {orderID: 'BUY_423435363'}} 
      + Error: {CODE: code, MESSAGE:msg, SUB_MESSAGE:sub_msg} //code !=0, msg: message error, sub_msg: detail message error

#### Lưu ý ####

     - SECRET: Được cấp riêng
     - payPartner: Được cấp riêng