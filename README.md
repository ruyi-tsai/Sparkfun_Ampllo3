# How to train an face detection on Sparkfun Edge board 
## step 1 : Download TensorFlow face detection and SDK
```
cd /media/sf_TinyML
mkdir Sparkfun_Ampllo3
cd Sparkfun_Ampllo3
```
Here we download directly on ubuntu machine (virtualBox machine) 
```
export fileid=1KwIkT8VIIzYgFjRJOaXlzHuLsxQvwhwE
export filename=tensorflow.tar.gz
wget --save-cookies cookies.txt 'https://docs.google.com/uc?export=download&id='$fileid -O- \
     | sed -rn 's/.*confirm=([0-9A-Za-z_]+).*/\1/p' > confirm.txt
wget --load-cookies cookies.txt -O $filename \
     'https://docs.google.com/uc?export=download&id='$fileid'&confirm='$(<confirm.txt)
tar zxf tensorflow.tar.gz
```
```
export fileid=1zVmgbRTjdAo1Vjb-TrgI40xtpjKqp1sW
export filename=SparkFun_Edge_SDK.tar.gz
wget --save-cookies cookies.txt 'https://docs.google.com/uc?export=download&id='$fileid -O- \
     | sed -rn 's/.*confirm=([0-9A-Za-z_]+).*/\1/p' > confirm.txt

wget --load-cookies cookies.txt -O $filename \
     'https://docs.google.com/uc?export=download&id='$fileid'&confirm='$(<confirm.txt)
tar zxf SparkFun_Edge_SDK.tar.gz

```
## step 2 : Setup tool chain
```
vim ~/.bashrc
###Then add these two "export xxx" at the end of  ~/.bashrc 
export PROJ_BSP=/media/sf_TinyML/Sparkfun_Ampllo3/SparkFun_Edge_SDK/AmbiqSuite-Rel2.2.0/boards/SparkFun_Edge_BSP-master
export PATH=/media/sf_TinyML/Sparkfun_Ampllo3/tensorflow/tensorflow/lite/micro/tools/make/downloads/gcc_embedded/bin:$PATH
```
## step 3 : Build test example on Sparkfun board
```
cd $PROJ_BSP/examples/example1_edge_test/gcc
make clean
make
```
It will output 'example1_edge_test.bin' file at $PROJ_BSP/examples/example1_edge_test/gcc/bin/

## step 4 : Genearte the firmware image 'xxx_wire.bin'(include header )
```
cd /media/sf_TinyML/Sparkfun_Ampllo3/SparkFun_Edge_SDK/apollo3_scripts
cp $PROJ_BSP/examples/example1_edge_test/gcc/bin/example1_edge_test.bin  .
./build_fw.sh example1_edge_test.bin
```
then it will output example1_edge_test_wire.bin

## step 5 : burn fw to Sparkfun Edge board
######  1. hold down button 14
######  2. press and release rest button
######  3. enter command line (on anaconda powershell prompt)
```
cd D:/TinyML/sf_TinyML/Sparkfun_Ampllo3/SparkFun_Edge_SDK/apollo3_scripts
python uart_wired_update.py COM6 -f example1_edge_test_wire.bin
```
COM6 is your device name in Windows
![image](https://github.com/ruyi-tsai/Sparkfun_Ampllo3/blob/main/test_image1.png)
######  4. press and release rest button

## step 6 : build person detection  project
```
cd /media/sf_TinyML/Sparkfun_Ampllo3/tensorflow
sudo make -f tensorflow/lite/micro/tools/make/Makefile  TARGET=sparkfun_edge  person_detection_bin
cp tensorflow/lite/micro/tools/make/gen/sparkfun_edge_cortex-m4/bin/person_detection.bin /media/sf_TinyML/Sparkfun_Ampllo3/SparkFun_Edge_SDK/apollo3_scripts
cd /media/sf_TinyML/Sparkfun_Ampllo3/SparkFun_Edge_SDK/apollo3_scripts
./build_fw.sh person_detection.bin
```
run  anaconda powershell prompt
```
cd D:/TinyML/sf_TinyML/Sparkfun_Ampllo3/SparkFun_Edge_SDK/apollo3_scripts
python uart_wired_update.py COM6 -f person_detection_wire.bin
```
## step 7 : test person detect
run  anaconda powershell prompt
```
cd D:/to_bitmap
python dump_serial.py
```
COM_PORT = 'COM6'    # ?????????????????????
BAUD_RATES = 115200   # ??????????????????

![image](https://github.com/ruyi-tsai/Sparkfun_Ampllo3/blob/main/person_detection.png)
