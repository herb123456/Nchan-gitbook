# 頻道 Channel ID

經過以上實作

可以發現channel id是nchan流通資訊的識別ID

所以在設定channel id時就特別重要

以下提供三種設定方式

```
location = /sub_by_ip {
    #channel id is the subscriber's IP address
    nchan_subscriber;
    nchan_channel_id $remote_addr;
  }

  location /sub_by_querystring {
    #channel id is the query string parameter chanid
    # GET /sub/sub_by_querystring?foo=bar&chanid=baz will have the channel id set to 'baz'
    nchan_subscriber;
    nchan_channel_id $arg_chanid;
  }

  location ~ /sub/(\w+)$ {
    #channel id is the word after /sub/
    # GET /sub/foobar_baz will have the channel id set to 'foobar_baz'
    # I hope you know your regular expressions...
    nchan_subscriber;
    nchan_channel_id $1; #first capture of the location match
  }
```

channel id 可以跟任何nginx的變數結合，像是第一個例子就是用ip當作channel id

也可以是query string或是header內的值

\(nginx 的所有變數可參考[官方](http://nginx.org/en/docs/varindex.html)\)

甚至是url的一部分

## 多頻道

nchan 甚至支援多 channel id

可以同時訂閱多個channel也可以同時發送訊息給多個channel

```
location ~ /multisub/(\w+)/(\w+)$ {
    nchan_subscriber;
    nchan_channel_id "$1" "$2" "common_channel";
    #GET /multisub/foo/bar will be subscribed to:
    # channels 'foo', 'bar', and 'common_channel',
    #and will receive messages from all of the above.
  }
```

這個設定就支援同時訂閱三個channel

一個固定的 common\_channel

另外兩個是變動的channel

但如果我要訂閱n個呢？

```
location ~ /multisub-split/(.*)$ {
    nchan_subscriber;
    nchan_channel_id "$1";
    nchan_channel_id_split_delimiter ",";
    #GET /multisub-split/foo,bar,baz,a will be subscribed to:
    # channels 'foo', 'bar', 'baz', and 'a'
    #and will receive messages from all of the above.
  }
```

可以用 nchan\_channel\_id\_split\_delimiter 指定分割符號

這樣訂閱的時候位置可以是

```
/multisub-split/foo,bar,baz,a
```

就同時訂閱了四個頻道

同樣的原理

只需要把nchan\_subscriber

替換成nchan\_publisher

就可以同時對多個頻道發佈訊息了

