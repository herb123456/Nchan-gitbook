# 發佈者\(Publisher\)設定

可以從之前章節發現，要讓某個位置成為發佈的端點，只需要加入`nchan_publisher`的關鍵字，至於要發送到哪個channel則是設定`nchan_channel_id`跟`nchan_channel_group`即可。

除了使用WebSocket發送訊息之外，Nchan還提供一般的HTTP POST方式發送訊息，以下用curl作為範例。

假設在`/pub`位置設定了publisher

```
location /pub {
    #example publisher location
    nchan_publisher;
    nchan_channel_id foo;
    nchan_channel_group test;
    nchan_message_buffer_length 50;
    nchan_message_timeout 5m;
}
```

現在用 curl 發送一個POST請求

```
$ curl --request POST --data "test message" http://127.0.0.1/pub
```

會立即得到以下回覆

```
queued messages: 1
last requested: 0 sec. ago
active subscribers: 0
last message id: 1522559555:0
```

queued messages的意思是，Nchan會為每個channel做一個buffer，讓每個訂閱者一訂閱這個頻道時，就會立即收到這個buffer裡的內容，有點類似離線訊息的概念，但他不會判斷是否接收過，只要一訂閱就會把buffer的訊息全部傳送給訂閱者，這個設定可由nchan\_message\_buffer\_length設定要buffer幾則訊息，也可以是0。

Nchan預設回覆的都是human readable的資訊，如果要讓程式能讀懂，可以指定Accept header：

```
$ curl --request POST --data "test message" -H "Accept: text/json" http://127.0.0.1/pub
```

回覆的訊息就會變成JSON格式了

```
{"messages": 2, "requested": 493, "subscribers": 0, "last_message_id": "1522560048:0" }
```

如果curl加入`-v`參數則可以發現，如果有訂閱者在訂閱的時候，Nchan會回覆http status code 201，若沒有任何訂閱者，Nchan則會回覆202，這也可以拿來判斷，這則訊息是否有人立即收到。

以下列出跟Publisher有關的設定，之後章節會再詳細介紹：

```
nchan_deflate_message_for_websocket
nchan_publisher
nchan_publisher_upstream_request
nchan_pubsub
nchan_authorize_request
nchan_message_buffer_length
nchan_message_timeout
nchan_redis_pass
nchan_store_messages
nchan_use_redis
nchan_access_control_allow_origin
nchan_channel_group
nchan_channel_event_string
nchan_channel_events_channel_id
nchan_max_channel_id_length
```



