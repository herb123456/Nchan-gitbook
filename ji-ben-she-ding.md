# 基礎設定

nchan提供了pub/sub的功能，所以最主要設定也會圍繞在這兩者之上

最基礎的設定，就是指定兩個固定位置，提供pub/sub的功能

```
#...
http {  
  server {
    #...

    location = /sub {
      nchan_subscriber;
      nchan_channel_id $arg_id;
    }

    location = /pub {
      nchan_publisher;
      nchan_channel_id $arg_id;
    }
  }
}
```

這裡使用query string\(get參數\)id當作channel id

所以若要發佈訊息只需要發出POST請求到 /pub?id=channel\_id

nchan就會自動把訊息傳送給有訂閱 /sub?id=channel\_id的訂閱者

下個章節就會實際實驗

由此設定不難看出

nchan 的關鍵字 nchan\_subscriber

就是讓這個位置變成訂閱點

而nchan\_publisher就是讓這個位置變成發佈點

