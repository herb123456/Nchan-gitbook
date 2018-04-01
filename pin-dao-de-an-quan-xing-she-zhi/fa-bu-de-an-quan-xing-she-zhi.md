# 發佈的安全性設置

官方的建議，是將發佈的端點完全隱藏，只允許local存取發佈的位置。

```
http {
  server {
    #available only on localhost
    listen  127.0.0.1:8080;
    location ~ /pub/(\w+)$ {
      nchan_publisher;
      nchan_channel_group my_app_group;
      nchan_channel_id $1;
    }
  }

  server {
    #available to the world
    listen 80;

    location ~ /sub/(\w+)$ {
      nchan_subscriber;
      nchan_channel_group my_app_group;
      nchan_channel_id $1;
    }
  }
}
```

或是直接鎖IP的方式不對外公開

```
  server {
    #available to the world
    listen 80; 
    location ~ /pub/(\w+)$ {
      allow 127.0.0.1;
      deny all;
      nchan_publisher;
      nchan_channel_group my_app_group;
      nchan_channel_id $1;
    }
  }
```

我目前也是照著官方建議，這樣發佈訊息時就需要經過原本的後端程式，經過驗證與檢查還有發送app push之後才傳送到nchan做訊息的廣播。

