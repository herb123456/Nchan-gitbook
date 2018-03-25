# 使用Docker啟動

## 從nginx官方Docker file修改\(建議\)

自己修改dockerfile不僅自由度高，掌握度也比較高，而且也不難，讓我們先看看nginx官方dockerfile長怎樣吧。

![](/assets/Screen Shot 2018-03-25 at 1.26.30 PM.png)

首先他區分成mainline跟stable兩種版本

mainline是最新版本，stable則是穩定版本，我就拿stable版本來用吧

![](/assets/Screen Shot 2018-03-25 at 1.33.27 PM.png)

進去後可以看到有兩種版本

alpine跟stretch

alpine是最小的docker image

stretch則是debain stretch

應該大部分人都不習慣使用alpine

所以會選擇stretch

但考量到未來production環境是docker swarm或是Kubernetes的人

就可以開始習慣alpine了



## 使用別人製作好的image



