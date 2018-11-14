# YOLO_custom_training
Yolo custom training using yolo-mark
## 자동화방법 연구중

환경
OS : ubuntu 16.04
python version : 3.5.2
CUDA version : 9.0
cuDNN version : 7.0.5
darknet version : ?
.Train
yolo-mark 다운

git clone https://github.com/AlexeyAB/Yolo_mark

YOLO-MARK는 이미지파일들에 직접 본인이 Bounding Box를 그려줌으로써 Box의 좌표를 지정해줄 수 있다

cd Yolo_mark
cmake .
make
./linux_mark.sh
Yolo_mark/x64/Release/data/img
경로안에 기존에 있던 이미지파일들을 삭제한다. 그리고 그 안에 학습시킬 이미지파일을 넣는다.



yolo_mark/x64/Release/data  -경로에서

vi obj.data


 classes 수를 지정해준다.

yolo_mark/x64/Release/data  -경로에서

vi obj.names

 

분류할 class 이름을 적어준다.

Yolo_mark 디렉토리로 이동 후 , 실행한다.

sh linux_mark.sh
Yolo-mark 실행화면

Bounding Box를 직접 그려넣어주면, jpg파일 옆에 txt파일이 생성된 것을 확인할 수 있다.

이 txt파일 안에는 그려넣어준 Box의 좌표값이 들어있다.

yolo_mark/x64/Release 경로로 이동한다.

    vi yolo-obj.cfg

    맨아래 [region]에 classes를 설정한다.

    그리고 그 위 [convolutional] 아래

    filters = 5 * (classes+5) 로 설정한다.


convolutional layer 설치 – darknet 디렉토리안에 받아 준다.

http://pjreddie.com/media/files/darknet19_448.conv.23

yolo-obj.cfg파일도 darknet 디렉토리로 이동해 준다.

 yolo_mark/x64/Release/data 경로안에 있는 image 디렉토리와 obj.names , obj.data , train.txt를 darknet/data 경로로 이동해 준다.

데이터 학습하기

cd darknet
./darknet detector train data/obj.data yolo-obj.cfg darknet19_448.conv.23


avg는 loss 즉, 손실율의 평균인데 , 가장 낮아질때까지 학습을 계속 진행하면 된다.

학습을 진행하게되면 Backup디렉토리 안에 자동으로 학습된 가중치파일이 저장이 된다.



최종 학습



$ cd darknet
$ ./darknet detector train data/obj.data yolo-obj.cfg darknet19_448.conv.23
위의 명령을 처리하는 코드
darknet/examples/detector.c 내의
void train_detector(char *datacfg, char *cfgfile, char *weightfile, int *gpus, int ngpus, int clear) 함수
datacfg : "data/obj.data" 에 해당
darknet/src/option_list.c 내의 
list *read_data_cfg(char *filename) 함수에서 처리됨
cfgfile : "yolo-obj.cfg" 에 해당
weightfile : "darknet19_448.conv.23" 에 해당
datacfg 는 darknet/src/option_list.c 내의 
list *read_data_cfg(char *datacfg) 함수에서 처리되어 옵션 리스트를 가지게 됨.
cfgfile 은 darknet/src/parser.c 내의 
network *parse_network_cfg(char *cfgfile) 함수에서 처리되어 cfgfile내에 나열된 convolution layer들을 하나씩 붙여서 network를 와꾸를 만듦.
weightfile 은 darknet/src/parser.c 내의
void load_weights_upto(network *net, char *weightfile, int start, int cutoff) 함수에서 처리됨.
와꾸가 만들어진 net의 실제 weight 값 들에 weightfile의 내용물들이 채워지는 형태.
따라서, net의 와꾸와 weightfile의 순서가 다르면 에러가 남.
중간 결과 weight 들의 저장
datacfg 에 적혀 있는 backup directory에 일정 epoch 마다 network weight 들이 저장 됨.
