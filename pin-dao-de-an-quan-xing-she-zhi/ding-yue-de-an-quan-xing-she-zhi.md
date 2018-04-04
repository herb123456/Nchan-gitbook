# 訂閱的安全設置

在訂閱的安全方面，官方建議三種做法：

1. 產生編碼過的Channel ID
2. 使用`nchan_authorize_request`做驗證
3. 使用`X-Accel-Redirect`隱藏Channel ID

### 產生編碼過的Channel ID

主要是防止他人輕易猜出別人的Channel ID，不管是被竊聽或是被胡亂發送訊息，都有一定的風險存在，官方建議最好是產生一組隨機128bit長度的一次性token當作Channel ID，而且每次都會不一樣，但實務上只要做到無法讓人輕易猜出或短時間暴力嘗試就猜出來的方式即可。

### 使用`nchan_authorize_request`做驗證

這在之前章節[訂閱與發佈時的驗證](/nchan-yu-hou-duan-cheng-shi-de-jie-he/ding-yue-yu-fa-bu-shi-de-yan-zheng.md)已經提過

### 使用`X-Accel-Redirect`隱藏Channel ID

這算上一個使用`nchan_authorize_request`做驗證的進階版，使用`nchan_authorize_request`會先連線到某個帶Channel ID的位置，再去後端伺服器做驗證，所以如果沒有走wss\(websocket over ssl\)，channel ID也等於暴露在網路上。

而使用 `X-Accel-Redirect`則是先連線到某個後端的位置，驗證過後再使用`X-Accel-Redirect`的header資訊，導到對應的channel ID位置，以下是範例：

```
upstream upstream_app {
  server 127.0.0.1:8080;
}

server {
  listen 80; 

  location = /sub_upstream {
    proxy_pass http://upstream_app/subscriber_x_accel_redirect;
    proxy_set_header X-Forwarded-For $remote_addr;
  }

  location ~ /sub/internal/(\w+)$ {
    internal; #this location only accessible for internal nginx redirects
    nchan_subscriber;
    nchan_channel_id $1;
    nchan_channel_group test;
  }
}
```



