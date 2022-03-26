# install-nvidia-tao-toolkit-and-a-sample-ai-model-on-jetson
install-nvidia-tao-toolkit-and-a-sample-ai-model-on-jetson は、NVIDIA TAO TOOLKIT とサンプル AI モデルを Jetson 上にインストールする手順概要です。  

## 動作環境
- NVIDIA 
    - JetPack
    - DeepStream
- Docker
- Docker Compose v2
- GNU Make  

## インストール

### TAOツールキットのインストール   

以下のコマンドで NVIDIA TAO TOOLKIT（tao-converter） をインストールできます。  
詳細については[TAO Toolkit Get Started](https://developer.nvidia.com/tao-toolkit-get-started)を参照してください。

```
wget -O jp46-20210820t231431z-001.zip https://developer.nvidia.com/jp46-20210820t231431z-001zip
mkdir jp46-20210820t231431z-001
unzip -d jp46-20210820t231431z-001 jp46-20210820t231431z-001.zip
```

### サンプルAIモデルのインストール（PeopleNet）  

サンプルAIモデルとして、PeopleNetをインストールします。以下のコマンドでインストールできます。  
詳細については、[PeopleNet](https://catalog.ngc.nvidia.com/orgs/nvidia/models/tlt_peoplenet)を参照してください。  

```
mkdir -p $HOME/peoplenet && \
wget https://api.ngc.nvidia.com/v2/models/nvidia/tlt_peoplenet/versions/pruned_v1.0/files/resnet34_peoplenet_pruned.etlt \
-O $HOME/peoplenet/resnet34_peoplenet_pruned.etlt
```  

### engine fileの生成(PeopleNet)

AIモデルをデバイスに最適化するため、以下のコマンドにより tao-converter でサンプルAIモデルの .etlt ファイルを engine file に変換します。
```
./jp46-20210820t231431z-001/tao-converter-jp46-trt8.0.1.6/tao-converter -k tlt_encode -d 3,544,960 $HOME/peoplenet/resnet34_peoplenet_pruned.etlt
```

## NVIDIA TAOについて
NVIDIA Train, Adapt, and Optimize (TAO) は、企業の AI アプリケーションやサービスの開発を簡素化し加速する AI モデル適応プラットフォームです。
  

## NVIDIA TAO TOOLKITについて
NVIDIA TAO TOOLKITは、AI/DLフレームワークの複雑さを抽象化するAIツールキットです。NVIDIAの運用品質の学習済みモデルを使用することで、コンピュータービジョンや対話型AI向けの運用カスタムモデルをすばやく作成します。高品質の学習済みモデルを使用するため、大規模なデータの収集やラベリングにかかるコスト、AI/ML モデルを一からトレーニングする負担を解消し、わずかな量のデータでも運用品質のモデルをすばやく構築できます。  


## engine fileについて
TensorRTで独自のモデルを使用するために必要な重みファイルです。  
NVIDIA TAO TOOLKIT により生成された engine file はデバイスごとに最適化されており、DeepStream に取り入れることが可能です。  
詳細については[Integrating TAO Models into DeepStream](https://docs.nvidia.com/tao/tao-toolkit/text/deepstream_tao_integration.html)を参照してください。  


## パラメータ変更について  

### DeepStreamのパラメータ変更

DeepStreamでAI推論を行う際は、以下の手順を参考に設定ファイルのパラメータ変更を行ってください。
詳細については[Configuration Groups](https://docs.nvidia.com/metropolis/deepstream/5.0DP/dev-guide/index.html#page/DeepStream_Development_Guide/deepstream_app_config.3.2.html#)を参照してください。  

* model-file, proto-file   
    * Caffe(Convolutional Architecture for Fast Feature Embedding)のモデルを使用するときに設定するパラメータです。  
    
* model-engine-file  
    * 生成したengine fileのパスを指定するパラメータです。  
    
* labelfile-path  
    * txtファイルに変更するラベルを記述するパラメータです。 

```
[property]

# comment out model-file and proto-file
# model-file=Primary_Detector/resnet10.caffemodel
# proto-file=Primary_Detector/resnet10.prototxt

# change model-engine-file and labelfile-path
model-engine-file=Primary_Detector/saved.engine
labelfile-path=Primary_Detector/labels.txt
```

また、DeepStreamを用いてリアルタイム解析を行う際は、設定ファイルのsource0を変更してください。  

* type
    * 以下からsourceのタイプを選択し、設定してください。  
    
    1: Camera (V4L2)  
    2: URI  
    3: MultiURI  
    4: RTSP  
    5: Camera (CSI) (Jetson only)  

```
[source0]

enable=1
#Type - 1=CameraV4L2 2=URI 3=MultiURI 4=RTSP
type=4
# Change url
url=rtsp://192.168.128.197:8554/stream
#camera-width=1080
#camera-height=720
#camera-fps-n=30
#camera-fps-d=1
#camera-v4l2-dev-node=0
```
