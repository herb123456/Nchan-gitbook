# 訂閱者\(Subscriber\)設定

Nchan提供多種訂閱方式，包括[WebSocket](https://nchan.io/#websocket)、[EventSource](https://nchan.io/#eventsource)、[Long Poll](https://nchan.io/#long-polling)、[Interval-Poll](https://nchan.io/#interval-polling)、[HTTP Chunked Transfer](https://nchan.io/#http-chunked-transfer)、[HTTP multipart/mixed，](https://nchan.io/#http-multipart-mixed)但我只有使用過WebSocket，所以會以WebSocket為主，若對其他方式有興趣的可以到[nchan官方](https://nchan.io)網站上查看。

經過前面幾個章節，大家一定都已經知道，要讓某個位置變成可訂閱，只需要加上`nchan_subscriber`的關鍵字

```
  location /sub {
    #example subscriber location
    nchan_subscriber;
    nchan_channel_id foo;
    nchan_channel_group test;
    nchan_subscriber_first_message oldest;
  }
```

前三個設定，在之前都有介紹過，最後一個

