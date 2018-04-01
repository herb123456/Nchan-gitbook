# 訂閱與發佈時的驗證

這是一個滿重要的應用，想像一個場景，在聊天室裡，每個人有自己的channel id，如果沒有經過身份驗證，任何一個人都可以訂閱別人的channel，代表大家的聊天訊息會被監聽，或是沒有經過驗證，就可以隨意發送訊息給任意的人，這都是很危險的情況。

在Nchan裡它提供了`nchan_authorize_request`的設定，這不管是訂閱的路徑還是發佈的路徑都可以使用。

```
  upstream my_app {
    server 127.0.0.1:8080;
  }

  location ~ /pubsub/auth/(\w+)$ {
    nchan_channel_id $1;
    nchan_authorize_request /auth;
    nchan_pubsub;
    nchan_channel_group test;
  }

  location = /auth {
    proxy_pass http://my_app/pubsub_authorize;
    proxy_pass_request_body off;
    proxy_set_header Content-Length "";
    proxy_set_header X-Subscriber-Type $nchan_subscriber_type;
    proxy_set_header X-Publisher-Type $nchan_publisher_type;
    proxy_set_header X-Prev-Message-Id $nchan_prev_message_id;
    proxy_set_header X-Channel-Id $nchan_channel_id;
    proxy_set_header X-Original-URI $request_uri;
    proxy_set_header X-Forwarded-For $remote_addr;
  }
```

在這個官方提供的範例裡，可以看到定義了一個pubsub的路徑，並且對這路徑做訂閱與發佈的同時，會去`/auth`做驗證，`/auth`這個路徑則會把這個請求反向代理到另一個後端伺服器`myapp`的`/pubsub_authorize`路徑下做驗證，這會連同原本的header與cookie都一起帶過去，所以可以在後端伺服器使用session、cookie跟nchan所提供的變數一起驗證是否允許這個請求，如果允許，只需要讓http status code是2xx即可，其他的status code，nchan都會將後端所傳回的資訊一併帶回給client，並終止原本的請求。

