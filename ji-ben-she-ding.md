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



