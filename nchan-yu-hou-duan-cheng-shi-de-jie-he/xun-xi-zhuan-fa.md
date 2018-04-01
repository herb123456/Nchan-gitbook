# 訊息轉發

訊息轉發可以讓後端伺服器接收傳來的訊息，可以儲存、處理甚至修改原有的訊息。

```
  location ~ /pub/(\w+)$ {
    #publisher endpoint
    nchan_channel_id $1;
    nchan_pubsub;
    nchan_publisher_upstream_request /upstream_pub;
  }

  location = /upstream_pub {
    proxy_pass http://127.0.0.1:9292/pub;
    proxy_set_header X-Publisher-Type $nchan_publisher_type;
    proxy_set_header X-Prev-Message-Id $nchan_prev_message_id;
    proxy_set_header X-Channel-Id $nchan_channel_id;
    proxy_set_header X-Original-URI $request_uri;
  } 
```

要將訊息轉發，是使用`nchan_publisher_upstream_request`的設定，原理也跟之前ㄧ樣都是使用Nginx的`proxy_pass`，將訊息傳遞到後端伺服器，不同的是，後端若處理完回應`200 OK`，nchan會把原始訊息替換成後端伺服器回應的內容，若回應`204 No Content`，則會將此訊息完全消除，若不想變動則可以回應`304 Not Modified`，這有助於過濾不雅字串，甚至將某個channel靜音的功效。

