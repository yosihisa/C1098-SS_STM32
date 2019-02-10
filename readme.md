# C1098-SS driver for STM32  
This is a driver for STM32 of camera C1098 of Silent System.  
サイレントシステム社のカメラ C1098のSTM32用のドライバです。


## 使い方・サンプルコード
まず「C1098.c」「C1098.h」の2つのファイルをプロジェクトに追加し、C1098をインクルードします。  
```
#include "main.h"
```
初期設定ではSTM32F4シリーズで動くようになっています。  
違うシリーズで動かす必要のある場合はC1098.hの7行目 `#include "stm32f4xx_hal.h"`を適切に変更してください。  
<br>
次に画像を保存するための領域を確保します。  
```
#define MAX_SIZE 40*1000 //40KBくらい

uint8_t  jpeg_data[MAX_SIZE];
uint32_t jpeg_data_size;
```
実際に画像データを受信するまでの流れは  
カメラの設定→撮影→転送  
になります。
```
CAMERARESULT camrea_res;
c1098_handle camera;

//カメラの設定
camera.uart_port = &huart4; //カメラを接続しているUARTポート
camera.packet_size = 256;   //1度に転送するデータ量 ※1
camera.baudrate = 460800;   //転送速度
camera.resolution = VGA;    //画質

camrea_res = camera_init(&camera);  //カメラを初期化

while(snap_shot(&camera) != CAMERA_OK); //撮影

camrea_res = get_picture(&camera, &jpeg_data, MAX_SIZE, &data_size); //転送
```
転送速度や画質についてはC1098のデータシートを確認してください。  
https://www.silentsystem.jp/download/C1098UM.pdf

## バグについて  
※1 `camera.packet_size`に指定できる値は256以下の4の倍数です。  
詳細はこのページをご覧ください。 https://www.silentsystem.jp/c1098bugs.htm  

このライブラリでは256より大きい値や4の倍数以外の値を指定した時の処理については実装していないので注意してください。  

## 動作確認環境  
CubeMX 5.0.1 + TrueStudio 9.2.0  
