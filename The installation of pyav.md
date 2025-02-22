因为在读取TRECVid2007 SBD的数据集的时候出现了问题<br />
于是尝试使用pyav这个包<br />
pyav的介绍来自：https://docs.mikeboers.com/pyav/develop/installation.html#dependencies<br />

>
安装步骤来自：https://gist.github.com/willpatera/f5f205d2311d92142490cda798d6e2b8<br />
>
#### 删除现有的包
```
sudo apt-get -y remove ffmpeg x264 libav-tools libvpx-dev libx264-dev yasm
```
#### 安装新包（依赖）
```
sudo apt-get update
sudo apt-get -y install build-essential checkinstall git libfaac-dev libgpac-dev \
  libmp3lame-dev libopencore-amrnb-dev libopencore-amrwb-dev librtmp-dev libtheora-dev \
    libvorbis-dev pkg-config texi2html yasm zlib1g-dev libasound-dev
```
#### 安装yasm
```
wget http://www.tortall.net/projects/yasm/releases/yasm-1.2.0.tar.gz
tar xvzf yasm-1.2.0.tar.gz
cd yasm-1.2.0
./configure
make
sudo make install
```
由于不知道是什么原因，如果接着安装libx264，会出现
```
Found no assembler
Minimum version is NASM version 2.13.03
```
于是**安装nasm**,安装方法来自：https://blog.csdn.net/heng615975867/article/details/79388439
```
wget http://www.nasm.us/pub/nasm/releasebuilds/2.13.03/nasm-2.13.03.tar.xz
./configure  --prefix=/usr 
make
sudo make install
```
#### 安装libx264
```
cd ..
git clone --depth 1 http://git.videolan.org/git/x264.git
cd x264
./configure --enable-shared
make
sudo checkinstall --pkgname=x264 --pkgversion="3:$(./version.sh | \
  awk -F'[" ]' '/POINT/{print $4"+git"$5}')" --backup=no --deldoc=yes \
    --fstrans=no --default
```

#### 安装AAC Audio Decoder
```
cd
wget http://downloads.sourceforge.net/opencore-amr/fdk-aac-0.1.0.tar.gz
tar xzvf fdk-aac-0.1.0.tar.gz
cd fdk-aac-0.1.0
./configure --enable-shared
make
sudo checkinstall --pkgname=fdk-aac --pkgversion="0.1.0" --backup=no \
  --deldoc=yes --fstrans=no --default
```
#### 安装 VP8 video encoder and decoder
```
cd
git clone --depth 1 https://chromium.googlesource.com/webm/libvpx 
cd libvpx
./configure --enable-shared
make
sudo checkinstall --pkgname=libvpx --pkgversion="1:$(date +%Y%m%d%H%M)-git" --backup=no \
  --deldoc=yes --fstrans=no --default
```
#### 为x264增加lavf support
```
cd ~/x264#切换到x264目录下
make distclean
./configure --enable-static --enable-shared --enable-pic
make
sudo checkinstall --pkgname=x264 --pkgversion="3:$(./version.sh | \
  awk -F'[" ]' '/POINT/{print $4"+git"$5}')" --backup=no --deldoc=yes \
  --fstrans=no --default
```
#### 安装XVID
```
cd ~/
wget 'http://mirror.ryansanden.com/ffmpeg-d00bc6a8/xvidcore-1.3.2.tar.gz'
tar -xzf xvidcore-1.3.2.tar.gz
cd xvidcore/build/generic
./configure --prefix='/usr/local'
make          # 15 sec
sudo make install
```
又不知道什么原因，在后续安装FFmpeg的时候出现了错误，如下
```
ERROR: libxvid not found
```
解决方法来自：http://blog.sina.com.cn/s/blog_99cc8f0d0101sx9j.html
```
sudo apt-get install libxvidcore-dev
```
#### 安装FFmpeg
```
cd
git clone --depth 1 https://github.com/FFmpeg/FFmpeg.git
cd FFmpeg
./configure --enable-shared --enable-gpl --enable-libmp3lame --enable-libopencore-amrnb \
  --enable-libopencore-amrwb --enable-librtmp --enable-libtheora --enable-libvorbis \
  --enable-libx264 --enable-nonfree --enable-version3  --enable-libxvid
make
sudo make install
```
#### 完成
```
sudo ldconfig
```
#### 安装pyav
```
sudo pip install av
```
