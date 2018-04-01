# 同時支援發佈與訂閱\(PubSub\)設定

Nchan也支援同時可以訂閱與發佈的設定，這對WebSocket全雙工的通道來說，是最方便的，連上一個位置，就可以訂閱訊息也可以發送訊息到同個channel。

```
  location = /pubsub {
    nchan_pubsub;
    nchan_channel_id foo;
    nchan_channel_group test;
  }
```

若要同時支援訂閱與發佈，只需要加上`nchan_pubsub`的關鍵字即可，在此模式下，所有的GET請求都會被當成訂閱者，POST請求都會當作發佈者，如果只想接受Websocket的連線，可以在`nchan_pubsub`關鍵字後面加上`websocket`，這樣用GET的請求就會直接回覆403 Forbidden了。

另外一種特殊設定方式，是可以指定這個位置訂閱者在哪個channel，發佈者在哪個channel，例如：

```
  location = /pubsub {
    nchan_pubsub;
    nchan_publisher_channel_id foo;
    nchan_subscriber_channel_id bar;
    nchan_channel_group test;
  }
```

對這個位置發佈訊息，會傳送到`bar`這個channel，對這位置做訂閱，會訂閱到`foo`這個channel。

