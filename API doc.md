## Tài liệu kết nối hệ thống Paygate cho đối tác ##

### 1. Thông tin chung ###

```
   - URL TEST: https://pm-sandbox.gviet.vn:2843
   - METHOD: POST
   - HEADERS REQUEST: Content-Type: application/json
```
 
- Luồng Mua

```
   1. Sau khi KH nhập tài khoản VTVcab, Đối tác gọi API lấy gói cước (getUserPlan) để hiển thị gói user đang sử dụng và các gói user có thể thanh toán
   2. Sau khi KH thanh toán thành công, đối tác gọi API cộng gói cước (addPackage) để cộng gói dịch vụ cho user
```

- Luồng Gia hạn

```
   1. ...
   2. ...
```

- Các mã lỗi: 
		
	        
	     {
            SUCCESS: {
              CODE: 1000,
              MESSAGE: 'SUCCESS'
            },
            FAIL: {
              CODE: -1000,
              MESSAGE: 'FAIL'
            },
        
            // TRAN GROUP
            TRANSACTION_DUPPLICATE: {
              CODE: 10,
              MESSAGE: 'TRANSACTION_DUPPLICATE'
            },
            TRANSACTION_PENDING: {
              CODE: 11,
              MESSAGE: 'TRANSACTION_PENDING'
            },
        
            // OTHER GROUP
            IP_NOT_ALLOW: {
              CODE: 90,
              MESSAGE: 'IP_NOT_ALLOW'
            },
            WRONG_HASH: {
              CODE: 91,
              MESSAGE: 'WRONG_HASH'
            },
            PARTNER_NOT_FOUND: {
              CODE: 93,
              MESSAGE: 'PARTNER_NOT_FOUND'
            },
            WRONG_PASS: {
              CODE: 94,
              MESSAGE: 'WRONG_PASS'
            },
            INVALID_PARAMS: {
              CODE: 400,
              MESSAGE: 'INVALID_PARAMS'
            },
            INVALID_MONEY: {
              CODE: 401,
              MESSAGE: 'INVALID_MONEY'
            },
            INVALID_METHOD: {
              CODE: 402,
              MESSAGE: 'INVALID_METHOD'
            },
            REQUEST_TIMEOUT: {
              CODE: 408,
              MESSAGE: 'REQUEST_TIMEOUT'
            },
            REQUEST_ERROR: {
              CODE: 409,
              MESSAGE: 'REQUEST_ERROR'
            },
            SYSTEM_ERROR: {
              CODE: 9999,
              MESSAGE: 'SYSTEM_ERROR'
            }
         }

### 2. Api Lấy gói cước (getUserPlan) ###
- Endpoint: /paygate/partner/getUserPlan
- Request:	Body JSON
		
| Params    | Required | DataType |    Example | Note                                                       |
| :-------- | -------: | -------: | ---------: | ---------------------------------------------------------- |
| partnerId |     true |   string |        abc | Mã Đối tác: Được cấp riêng                                 |
| username  |     true |   string | 0123456789 | Tài khoản khách hàng mua gói VTVCab                        |
| packageId |    false |   string |          1 | Mã gói cước (có thẻ dùng cho gian hạn)                     |
| signature |     true |   string |       xxxx | md5(partnerId.$.username.$.SECRET), SECRET: Được cấp riêng |

- Response: JSON

```        
+ Success: {CODE: 1000, MESSAGE: 'SUCCESS', DATA: data} 
+ Error: {CODE: code, MESSAGE: msg, SUB_MESSAGE: sub_msg} //code != 1000, msg: message error, sub_msg: detail message error             
         
```

  *** Success Example: ***

```
{
  "CODE": 1000,
  "MESSAGE": 'SUCCESS',
  "DATA": {
    uid: '391',
    username: '0143100111',
    packagesUsed: [
      {

        "id": "1",
        "name": "Gói ON VIP",
        "expiredAt": "06/10/2021 15:06:45"
      }
    ],
    packagesAll: [
      {
        "id": "1",
        "name": "Gói ON VIP",
        "description": "150 kênh đặc sắc: VTVcab, VTV, HTV, SCTV, Box,...\nThể thao chất: La Liga, Serie A, Ligue 1, Bundesliga, NBA, VBA, ATP Tour,...\nKho phim bom tấn & phim bộ khổng lồ\nXem 1 thiết bị, đăng nhập 5 thiết bị",
        "times": [
          {
            "time": "P1D",
            "name": "1 Ngày",
            "oldPrice": 5000, // Giá trước KM
            "price": 5000, // Giá sau KM
            "info": "",
            "code": "1__P1D"
          },
          {
            "time": "P1W",
            "name": "1 Tuần",
            "oldPrice": 25000,
            "price": 25000,
            "info": "",
            "code": "1__P1W"
          },
          {
            "time": "P1M",
            "name": "1 Tháng",
            "oldPrice": 66000,
            "price": 66000,
            "info": "",
            "code": "1__P1M"
          },
          {
            "time": "P3M",
            "name": "3 Tháng",
            "oldPrice": 198000,
            "price": 198000,
            "info": "Tặng 1 tháng",
            "code": "1__P3M"
          }
        ]
      },
    ],
  }
}
```
##### Lưu ý: #####
   + Nếu user không tồn tại thì DATA = { packagesAll: [{...}, ...]}

### 3. API cộng gói cước (addPackage)  ###
- Endpoint: /paygate/partner/addPackage
- Request:	Body JSON

		
| Params      | Required | DataType |              Example | Note                                                                                                  |
| :---------- | -------: | -------: | -------------------: | ----------------------------------------------------------------------------------------------------- |
| partnerId   |     true |   string |                  abc | Mã Đối tác: Được cấp riêng                                                                            |
| packageCode |     true |   string |               1__P3M | Gói cước cụ thể khách hàng mua gói VTVCab                                                             |
| uid         |    false |   string |                  123 | Mã khách hàng mua gói VTVCab                                                                          |
| username    |     true |   string |           0123456789 | Tài khoản khách hàng mua gói VTVCab                                                                   |
| amount      |     true |   string |                50000 | Số tiền thanh toán                                                                                    |
| transId     |     true |   string | EXP_1569636721842576 | Mã ID Giao dịch: phải là duy nhất                                                                     |
| payTime     |     true |   string |  2021-09-03 15:00:00 | Thời gian KH thanh toán; theo định dạng: YYYY-MM-DD HH:mm:ss                                          |
| signature   |     true |   string |                 xxxx | md5(partnerId.$.packageCode.$.username.$.amount.$.transId.$.payTime.$.SECRET), SECRET: Được cấp riêng |


- Response:

```
  + Success: {CODE: 1000, MESSAGE:'SUCCESS', DATA: { orderId: 'PAY_1569636721842511'}} 
  + Error: {CODE: code, MESSAGE:msg, SUB_MESSAGE:sub_msg} //code != 1000, msg: message error, sub_msg: detail message error
```

##### Lưu ý: #####
   + packageCode: lấy trường code trong gói cước (trong response api lấy gói cước)
    
    