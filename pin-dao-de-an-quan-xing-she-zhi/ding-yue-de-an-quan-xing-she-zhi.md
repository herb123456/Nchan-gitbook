# 訂閱的安全設置

在訂閱的安全方面，官方建議三種做法：

1. 產生編碼過的Channel ID
2. 使用`nchan_authorize_request`做驗證
3. 使用`X-Accel-Redirect`隱藏Channel ID

### 產生編碼過的Channel ID

主要是防止他人輕易猜出



