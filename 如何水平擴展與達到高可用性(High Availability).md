# 如何水平擴展與達到高可用性\(High Availability\)

Nchan可將訊息儲存在檔案、記憶體與Redis，速度最快的是儲存在記憶體裡，但如果重開機訊息就會不見，而且如果需要兩台做Load Balance，彼此之間的訊息也無法同步，很可能造成A連線上第一台，B卻往第二台發送訊息，卻永遠發送不到的狀況發生，所以要做到水平擴展，勢必要跟Redis做結合，而在Nchan要做到這件事，也相當容易。

```
http {
  nchan_redis_url "redis://redis_server:6379";
  server {
    listen 80;

    location ~ /redis_sub/(\w+)$ {
      nchan_subscriber;
      nchan_channel_id $1;
      nchan_use_redis on; 
    }
    location ~ /redis_pub/(\w+)$ {
      nchan_publisher;
      nchan_channel_id $1;
      nchan_use_redis on;
    }
  }
} 
```

只需要設定Redis master的位置，就會自動發現其他的slave機器，並在pub與sub的位置開啟使用Redis即可。

當然Nchan也支援Redis cluster

```
http {
  upstream redis_cluster {
    nchan_redis_server redis://127.0.0.1:7000;
    nchan_redis_server redis://127.0.0.1:7001;
    nchan_redis_server redis://127.0.0.1:7002;
    # you don't need to specify all the nodes, they will be autodiscovered
    # however, it's recommended that you do specify at least a few master nodes.
  }
  server {
    listen 80;

    location ~ /sub/(\w+)$ {
      nchan_subscriber;
      nchan_channel_id $1;
      nchan_redis_pass redis_cluster;
    }
    location ~ /pub/(\w+)$ {
      nchan_publisher;
      nchan_channel_id $1;
      nchan_redis_pass redis_cluster;
    }
  }
} 
```



