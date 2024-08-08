## Bentoml로 YOLOv8의 Docker Image 만들기 및 실행 가이드

(1) f:\venv 위치에 아래 명령으로 가상환경 만듬  
python -m venv bento_yolo310  
(2) 가상환경 들어가서 lib설치. f:\temp\BentoYolo의 requirements.txt 참조  
pip install jupyter  
pip install -r requrements.txt  
(3) f:\temp\BentoYolo\yolo8_bentoml.ipynb에서 bentoml model만들어 저장  
bentoml models list로 확인 가능  
(4) 같은 폴더의 service.py와 bentofile.yaml참고해서 bentoml의 bento를 만듬  
bentoml build  
(5) docker image 만듬 (docker image는 6.54Gb)   
bentoml containerize yolo_v8:tmkoy52vgocyxqb4    
(6) docker container 실행  
docker run --rm -p 3000:3000 yolo_v8:tmkoy52vgocyxqb4    
(7) yolo_tester.ipynb로 이미지 넘겨서 테스트 해봄  
  
(8) container새로 실행 시 마다 yolo8x.pt를 반복 다운 받음. 이를 해결  
- docker exec -it container_id /bin/bash  
- container_id는 "docker container ls"명령으로 확인   
- 들어가 보면 apis, env, models, src등의 폴더가 있음.  
- src에 들어가보면 bentofile.yaml, service.py, yolov8x.pt파일이 들어 있음.   
- 외부 disk의 폴더로 연결하여 이를 해결함.   
  - 경로를 보면, /home/bentoml/bento/src
  - f:\temp\aaa폴더를 만들어 이 경로와 연결
  - aaa폴더 밑에 service.py, bentofile.yaml을 복사해 놓음  
(9) docker container를 다시 실행  
docker run --rm -p 3000:3000 -v f:\temp\aaa:/home/bentoml/bento/src yolo_v8:tmkoy52vgocyxqb4  

### shell에서 호출
> curl -X POST http://localhost:3000/predict -H "accept:application/json" -H "Content-Type:multipart/form-data" -F "images=@apple.jpg;type=image/jpg"   
> curl -X POST http://localhost:3000/predict -H "accept:application/json" -H "Content-Type:multipart/form-data" -F "images=@apple.jpg;type=image/jpg" -F "images=@banana.jpg;type=image/jpg"
