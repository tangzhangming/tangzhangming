## ffmpeg命令大全

### 查看视频信息
ffmpeg -i video.mp4

### 将视频分解成图片/
```
// 每帧1张图
ffmpeg -i video.mp4 ./01/image%d.jpg
// 每分钟截取一张图 -vf fps=1/60
ffmpeg -i video.mp4 -vf fps=1/60 img%03d.jpg
```

### 从视频抽出声音并存为Mp3

### 视频转换为m3u8切片
```
ffmpeg -i video.mp4 -profile:v baseline -level 3.0 -start_number 0 -hls_time 10 -hls_list_size 0 -f hls ./video_m3u8/index.m3u8
-profile:v baseline 大概意思是档次转成基本画质，有四种画质级别,分别是baseline, extended, main, high，从低到高
-level 3.0 大概也是视频画质级别吧，基本上是从1到5
-start_number 0 表示从0开始
-hls_time 10 标识每10秒切一个
```
