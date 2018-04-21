# 訂閱者上線與下線通知

上線與下線，其實只是該設定的其中一種應用，要達到此種效果，使用的是nchan提供的`nchan_subscribe_request`與`nchan_unsubscribe_request`這兩個設定。

顧名思義，就是在訂閱者訂閱時發送一個request到後端伺服器，進而達到上線追蹤的功能，但也不限定此種應用，要如何應用，全由通知的後端伺服器來決定。

```
  location ~ /sub/(\w+)$ {
    nchan_channel_id $1;
    nchan_subscribe_request /upstream/sub;
    nchan_unsubscribe_request /upstream/unsub;
    nchan_subscriber;
    nchan_channel_group test;
  }

  location = /upstream/unsub {
    proxy_pass http://127.0.0.1:9292/unsub;
    proxy_ignore_client_abort on;  #!!!important!!!!
    proxy_set_header X-Subscriber-Type $nchan_subscriber_type;
    proxy_set_header X-Channel-Id $nchan_channel_id;
    proxy_set_header X-Original-URI $request_uri;
  } 
  location = /upstream/sub {
    proxy_pass http://127.0.0.1:9292/sub;
    proxy_set_header X-Subscriber-Type $nchan_subscriber_type;
    proxy_set_header X-Message-Id $nchan_message_id;
    proxy_set_header X-Channel-Id $nchan_channel_id;
    proxy_set_header X-Original-URI $request_uri;
  } 
```

可以看到nchan\_subscribe\_request會在有人訂閱時，轉發一個request到`/upstream/sub`的位置，`/upstream/sub`這個位置又使用了Nginx自帶的`proxy_pass`設定，將原始請求反向代理到`http://127.0.0.1:9292/sub`這個位置，在此就可以使用Nginx的proxy各種參數，範例裡就把各種nchan的參數都放到header裡一起轉發給後端，讓後端程式做進一步判斷。

停止訂閱也是同樣的原理，但要注意的是`proxy_ignore_client_abort on`這個設定，如果沒有開啟，訂閱者一但立即離線，nchan也就不會轉發到後端伺服器，Nginx會認為整個連線已經結束，進而結束整個進程，所以必須加上忽略訂閱者斷線也要轉發到後端的設定。

