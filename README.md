推理流程是：ZLMediaKit读流 → rk mpp解码 → v8检测并绘制 → rk mpp编码（h264）→ ZLMediakit推流  
自己使用的流是在PC上用Mediamtx做服务器：https://github.com/bluenviron/mediamtx/releases  
并用ffmpeg模拟的rtsp流：ffmpeg -re -stream_loop -1 -i test.mp4 -vcodec copy -acodec copy -b:v 5M -f rtsp -rtsp_transport tcp rtsp://localhost:8554/live1.sdp  
  
-----------------------------------------------
  
板端进入submodules目录并克隆ZLMediaKit和rk mpp代码  
'''
cd submodules 
'''
#ZLMediaKit中3rdpart中链接了别人的仓库，克隆后需要再更新子模块获取  
git clone https://github.com/ZLMediaKit/ZLMediaKit.git  
cd ./ZLMediaKit  
git submodule init  
git submodule update  
#编译ZLMediaKit  
cmake . -B build && cmake --build build  
  
#克隆rk mpp  
git clone https://github.com/rockchip-linux/mpp.git  
#编译rk mpp  
cd ../mpp  
cmake . -B build && cmake --build build  
  
---------------------------------------------  
  
#复制编译好的库到mpp_libs目录，在主目录下执行脚本  
./copy_mpp_libs.sh  
#编译整体项目代码，在build1(build)目录下  
cmake ..  
make  
#执行项目命令，可更改rtsp流为自己的流地址  
./build1/yolov8_stream_pool ./weights/yolov8s.float.rknn rtsp://192.168.43.203:8554/live1.sdp 264  
  
---------------------------------------------  
  
#在pc上读取推送的视频流rtsp端口为554，rtmp端口为1935,可以在源码目录src/yolov8_stream_pool.cpp中更改  
ffplay rtsp://板子ip:554/live/test  
ffplay rtmp://板子ip:1935/live/test  
