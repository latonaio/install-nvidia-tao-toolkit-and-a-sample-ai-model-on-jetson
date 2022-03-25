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

## NVIDIA TAOについて
NVIDIA Train, Adapt, and Optimize (TAO) は、企業の AI アプリケーションやサービスの開発を簡素化し加速する AI モデル適応プラットフォームです。
  

## NVIDIA TAO TOOLKITについて
NVIDIA TAO TOOLKITは、AI/DLフレームワークの複雑さを抽象化するAIツールキットです。NVIDIAの運用品質の学習済みモデルを使用することで、コンピュータービジョンや対話型AI向けの運用カスタムモデルをすばやく作成します。高品質の学習済みモデルを使用するため、大規模なデータの収集やラベリングにかかるコスト、AI/ML モデルを一からトレーニングする負担を解消し、わずかな量のデータでも運用品質のモデルをすばやく構築できます。
