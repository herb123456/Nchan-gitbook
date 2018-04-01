# 頻道 Group ID

Group ID是用來輔助Channel ID的，在某些設計下，channel ID會有重複的情況，Group ID則是避免在Channel ID重複下，彼此收到同樣的訊息。

```
 location /test_pubsub {
    nchan_pubsub;
    nchan_channel_group "test";
    nchan_channel_id "foo";
  }

  location /pubsub {
    nchan_pubsub;
    nchan_channel_group "production";
    nchan_channel_id "foo";
    #same channel id, different channel group. Thus, different channel.
  }

  location /flexgroup_pubsub {
    nchan_pubsub;
    nchan_channel_group $arg_group;
    nchan_channel_id "foo";
    #group can be set with request variables too
  }
```

可以在範例中看到 `/test_pubsub`位置跟`/pubsub`位置雖然有相同channel ID，但因為有不同Group ID，所以彼此是不同的channel，傳送到`pubsub`的訊息，\`pubsub

