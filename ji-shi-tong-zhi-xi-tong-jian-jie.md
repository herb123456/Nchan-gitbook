# 即時通知系統簡介

即時通知系統，顧名思義，就是在 發佈者（Publisher） 發佈訊息時能立即通知所有 訂閱者（Subscriber），實際例子就像Facebook的訊息牆，只要好友發了一則動態，我不必重新整理頁面，即可立即收到通知，我的好友即是發佈者，而我則是訂閱者。

在過去（約十幾年前），要達到這樣的效果，必須讓Client端不斷向伺服器發出詢問，才能在短時間內知道變化，此種方法耗費大量無謂的系統資源，最差情況是從上線到下線都無任何新訊息，但Client卻向伺服器發出了上萬次的請求，最終都得到無任何變化的回覆，此種方式稱為Polling。

而在Facebook初期，所使用的方式稱為Long polling，這與一般的Polling最大的差別在於，如果沒有最新訊息時，不會馬上回覆，而是要等到timeout或有新訊息的時候才回覆，所以變成Client詢問伺服器之後，如果沒有最新訊息，就會一直等下去，這條詢問的請求也不會中斷，這雖然解決了Polling浪費很多無謂請求的問題，但也造成伺服器連線佔用的問題，如果訊息量很大，也會跟Polling一樣，不斷送出請求，造成另類的DDoS。

而現代開發者，在Web上要達到此種效果，選用的技術會是WebSocket的新協定，此協定是獨立的，建立在TCP之上，並透過HTTP/1.1的101狀態碼，進行協定的切換，一開始Client會在HTTP頭部放上Upgrade資訊如下：

```
GET / HTTP/1.1
Upgrade: websocket
Connection: Upgrade
Host: example.com
Origin: http://example.com
Sec-WebSocket-Key: sN9cRrP/n9NdMgdcy2VJFQ==
Sec-WebSocket-Version: 13
```

支援WebSocket的伺服器收到該請求後，便會回覆請求已升級，並建立起WebSocket的連接。

```
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: fFBooB7FAkLlXgRSz0BT3v4hq5s=
Sec-WebSocket-Location: ws://example.com/
```

其中的Sec-WebSocket-Key與Sec-WebSocket-Accept，從名字看起來像是安全驗證機制，但其實只是防止一般的HTTP請求傳送給WebSocket伺服器裡造成的錯誤，並防止反向代理伺服器，暫存了先前的Upgrade請求，直接使用暫存的升級回覆，但那其實並沒有真正建立起WebSocket連接。

