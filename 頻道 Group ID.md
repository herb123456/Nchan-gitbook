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

可以在範例中看到 `/test_pubsub`位置跟`/pubsub`位置雖然有相同channel ID，但因為有不同Group ID，所以彼此是不同的channel，傳送到`pubsub`的訊息，`test_pubsub`並不會收到。

官方也給出了另一個有趣的應用，利用Group ID限制在這個Group的所有channel的資源利用：

```
#enable group accounting
  nchan_channel_group_accounting on;

  location ~ /pubsub/(\w+)$ {
    nchan_pubsub;
    nchan_channel_group "limited";
    nchan_channel_id $1;
  }

  location ~ /prelimited_pubsub/(\w+)$ {
    nchan_pubsub;
    nchan_channel_group "limited";
    nchan_channel_id $1;
    nchan_group_max_subscribers 100;
    nchan_group_max_messages_memory 50M;
  }

  location /group {
    nchan_channel_group limited;
    nchan_group_location;
    nchan_group_max_channels $arg_max_channels;
    nchan_group_max_messages $arg_max_messages;
    nchan_group_max_messages_memory $arg_max_messages_mem;
    nchan_group_max_messages_disk $arg_max_messages_disk;
    nchan_group_max_subscribers $arg_max_subs;
  }
```



