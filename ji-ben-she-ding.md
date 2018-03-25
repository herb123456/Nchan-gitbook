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

這裡使用get參數id當作channel id

所以若要發佈訊息只需要發出POST請求到 /pub?id=channel\_id

nchan就會自動把訊息傳送給有訂閱 /sub?id=channel\_id的訂閱者

## 基礎設定實作

我們把剛剛提到的基礎設定放進nginx，並用docker跑起來實驗看看

直接修改 nginx.vh.default.conf 並加入

```
location = /sub {
    nchan_subscriber;
    nchan_channel_id $arg_id;
}

location = /pub {
    nchan_publisher;
    nchan_channel_id $arg_id;
}
```

準備一個可以用WebSocket訂閱的簡單html

```
<!DOCTYPE html>
    <meta charset="utf-8" />
    <title>WebSocket Test</title>
    <script language="javascript" type="text/javascript">
        var output;
            function init() {
                output = document.getElementById("output");
                testWebSocket();
            }
            function testWebSocket() {
                var url = document.getElementById("url");
                var channelId = document.getElementById("channelID");
                var fullUrl = "ws://" + url.value + "/sub?id=" + channelId.value;
                websocket = new WebSocket(fullUrl);
                websocket.onopen = function(evt) {
                    onOpen(evt)
                    };
                websocket.onclose = function(evt) {
                    onClose(evt)
                };
                websocket.onmessage = function(evt) {
                    onMessage(evt)
                };
                websocket.onerror = function(evt) {
                    onError(evt)
                };
            }
            function onOpen(evt) {
                writeToScreen("CONNECTED");
            }
            function onClose(evt) {
                writeToScreen("DISCONNECTED");
            }
            function onMessage(evt) {
                writeToScreen('RESPONSE: ' + evt.data+'');
            }
            function onError(evt) {
                writeToScreen('ERROR: ' + evt.data);
            }
            function writeToScreen(message) {
                var pre = document.createElement("p"); pre.style.wordWrap = "break-word"; pre.innerHTML = message; output.appendChild(pre);
            }
        </script>
        <h2>WebSocket Test</h2>

連線位置：<input type="text" id="url">
<br><br>
Channel ID：<input type="text" id="channelID">
<br><br>
<button id="connect" onClick="init()">連線</button>
        <div id="output"></div>
```

重新run docker並覆蓋設定與index.html

```
$ docker run -p 80:80 -v $(pwd)/nginx.vh.default.conf:/etc/nginx/conf.d/default.conf -v $(pwd)/index.html:/usr/share/nginx/html/index.html herb123456/nchan-alpine
```

注意 $\(pwd\) 可以換成自己的絕對路徑

在網址列上輸入自己ip

如果是本機則輸入127.0.0.1或localhsot即可看到以下畫面

![](/assets/Screen Shot 2018-03-25 at 4.37.56 PM.png)

在連線位置上打自己的ip

channel id則可以任意輸入

之後按連線

如果成功則會看到 CONNECTED

![](/assets/Screen Shot 2018-03-25 at 4.39.24 PM.png)

這時候只需要對這個channel id發送POS請求即可傳送訊息

```
 $ curl --request POST --data "test" "http://172.105.204.205/pub?id=abcd"
queued messages: 1
last requested: 99 sec. ago
active subscribers: 1
last message id: 1521967261:0
```

頁面上也會同步出現剛剛打的訊息 test

![](/assets/Screen Shot 2018-03-25 at 4.41.25 PM.png)

