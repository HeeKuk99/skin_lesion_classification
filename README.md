# skin_lesion_classification
프로젝트 주제 : 피부 병변 분류 최적 모델  
프로젝트 기간 : 2023.9.1 ~ 2023.12.7  
프로젝트 목표 :  
1.	 주요 피부 병변을 분류하는 최적의 알고리즘을 개발하여 피부 질환 감별에 있어 중요한 피부 조직 검사를 시행하지 않은 상태에서도 보다 정확한 진단을 가능케 한다.
2.	 피부암의 조기 진단 및 분류를 자동화하고, 의료 전문가에게 정확하고 효과적인 의사결정을 지원한다. 의료 전문가 개인의 경험 및 예측에 의존적인 기존 진단에 비하여 진단 및 치료 결정의 정확성을 향상시킨다.
3.	 진료와 검사에 소요되는 시간과 노력을 축소해 환자의 편의성을 증대하고, 병원의 자원 절약을 유도하며 원격 의료로 피부 질환을 진단할 수 있는 가능성을 높인다.

  -	 해당 프로젝트는 피부암 진단의 정확성을 향상시키고 의료 전문가들에게 의사 결정 지원 도구를 제공한다. 피부 병변을 촬영한 이미지를 이용해 해당 병변을 분류하는 것이 목표이며, 피부 병변 이미지 데이터의 수집 및 전처리, 딥러닝 및 이미지 처리 알고리즘의 구현, 다양한 실험을 통한 최적의 분류 모델 탐구를 진행한다.  
## 프로젝트 과정
 Inputs은 Labelme를 이용해 병변 부분을 polygonal annotation한 후 얻어낸 마스크 이미지와 원래 이미지를 128*128 size와 256*256 size로 리사이징한 이미지이다. 해당 inputs으로 위 이미지에 보이는 모델을 학습시킨다. 최종적으로 test image를 넣었을 때 해당 병변이 어떤 피부 병변인지 classification하는 알고리즘을 학습시킨다.  
![image](https://github.com/user-attachments/assets/6dd80b85-d348-4003-87fd-6b1721984586)  
 초기에는 진단명과 발병 위치에 대한 정보가 모두 포함된 이미지를 선별하고자 하였다. 또한 품질이 낮거나 특징적이지 못한 이미지를 감지하여 육안으로 분별하고, Anaconda를 이용하여 Labelme를 실행한 후 병변 부분을 segmentation하고, 파이썬 코드로 데이터를 사용 의도에 맞게 변환하고자 하였다. 실제로 이용하고자 하는 정보를 모두 가지고 있는 이미지를 선별하였고, Labelme를 이용해 annotation한 후 label2voc.py를 이용해 mask 이미지를 생성하였고 데이터셋을 구축하였다.
 그러나 해당 데이터셋을 사용해 1차적으로 알고리즘을 학습하는 중에 병변 별로 모인 데이터 양의 차이가 심하다는 것을 인지하였고, 이것이 모델 학습에 부정적인 영향을 끼칠 것을 우려해 병변의 발생 위치나 양성/악성 정보 존재 여부에 상관 없이 병변 별 데이터 균형에 초점을 맞추어 이미지를 재선별하여 새롭게 데이터셋을 구축하였다.
 초기 데이터셋과 새로운 데이터셋 모두 데이터 분할을 거쳤고 Colab 환경에서 Classification 모델을 학습시키는데 사용되었다. 또한 이 데이터셋들을 사용해 test accuracy를 기준으로 최적의 모델을 찾기 위해 여러 비교 분석을 진행하였다.  

## 실험 결과
### 1.	Segmentation 유무에 따른 test accuracy 비교
 동일 모델 기준으로 segmentation 과정을 거치지 않은 이미지와 segmentation한 후 생성된 mask 이미지를 사용해 모델을 학습시켰을 때 loss와 test accuracy는 아래 표에 기재된 것과 같다. Segmentation 과정을 거치지 않은 이미지인 경우에 test accuracy가 높은 것을 확인할 수 있다.  
<img width="400" alt="image" src="https://github.com/user-attachments/assets/53c4c269-9af4-498f-84cd-22f909b9dec2">  
  
-	Segmentation O  
 ![image](https://github.com/user-attachments/assets/652aa174-67f3-4803-bd66-cd254df41ff3)  
-	Segmentation X  
 ![image](https://github.com/user-attachments/assets/5853b62b-adb5-4e6b-88f3-3ab90cd63e8f)  
 추가적으로 segmentation한 후 생성된 mask와 segmentation을 과정을 거치지 않은 이미지를 혼합한 데이터셋을 만들어 모델을 학습시켜 보았는데, 결과는 다음과 같다.  
![image](https://github.com/user-attachments/assets/63229d4b-82fa-45d7-ba23-392de92d0c3a)  
-	Segmentation O + Segmentation X  
 ![image](https://github.com/user-attachments/assets/b38ce33c-4489-4012-8fe5-03e4e5ed2d6d)  
### 2.	이미지 해상도에 따른 test accuracy 비교
 모델과 불러온 데이터 사이즈가 동일한 경우로, 코드 내에서 이미지 사이즈를 변화시킨 후 모델에 적용한 후 loss과 test accuracy를 비교하였다. 이때 불러온 데이터 사이즈는 128*128이었는데, 이미 resizing을 거친 이미지이기 때문에 코드 내에서 따로 해상도가 변경되지 않았을 때 가장 test accuracy가 높았던 것으로 추측한다.  
 ![image](https://github.com/user-attachments/assets/42960f1e-746e-4efd-b7b1-1f0935195f1f)  
### 3.	class간의 data 균형에 따른 test accuracy 비교
-	초기 데이터셋의 병변 별 이미지 수  
![image](https://github.com/user-attachments/assets/28372f69-3ca3-42b4-b548-acd58fbfb576)  
-	새로운 데이터셋의 병변 별 이미지 수  
![image](https://github.com/user-attachments/assets/80ef8dfb-2805-4a25-9f8c-2ffeb3689c15)  
 초기에 구성한 데이터셋은 병변 별 데이터 수가 불균형했다. 데이터가 많은 병변은 몇백 장에 달했지만, 적은 병변은 열두 장에 불과했다. 이러한 불균형이 모델 학습에 부정적인 영향을 미칠 것이라 생각하여 추가적으로 데이터셋을 구성했다. 이전에 진행했던 비교 분석에서 segmentation 과정을 거치지 않은 이미지의 test accuracy가 높은 것을 확인했기 때문에 새롭게 구성한 데이터셋은 선별 후 리사이징만 시행한 이미지로 구성되어 있다. 
 이렇게 균형이 맞춰지지 않은 초기 데이터셋과 균형을 맞춘 데이터셋을 각각 epochs 10, batch 2, learning rate 0.000007로 학습 진행한 후 loss와 test accuracy를 비교해보았다. 
 ![image](https://github.com/user-attachments/assets/e52c4736-2355-4de4-b195-3e3bc14c1aea)  
-	Balanced X  
 ![image](https://github.com/user-attachments/assets/095ef5e8-e8b6-486e-a6a5-18a02188127b)  
-	Balanced O  
 ![image](https://github.com/user-attachments/assets/79011bae-f341-4244-a984-bb2695ac49b7)  
 그 결과 불균형한 초기 데이터셋의 test accuracy가 더 높게 나왔다. 초기에 구성했던 데이터셋은 nevus의 데이터 양이 압도적으로 많았고, 학습과 평가 또한 편중된 nevus를 중심으로 이루어졌을 것이다. 때문에 다른 병변들은 충분히 학습되지 않았더라도 nevus의 학습 정도만 좋다면 더 높은 결과값이 나올 것이다. 따라서 좋은 학습을 위해서는 병변 간 데이터 균형을 고려한 새로운 데이터셋을 이용하는 것이 적합할 것이라고 판단하였다.
### 4.	input data의 size에 따른 test accuracy 비교
 input data size는 128*128픽셀, 256*256픽셀이고 epochs 200, batch 8, learning rate 0.000007로 각각 학습을 진행하였다. input data size가 256*256일 때 test accuracy가 더 높게 나온 것으로 확인되었다.  
![image](https://github.com/user-attachments/assets/fa02d73b-ac9a-41ca-a76f-4e6b313c753a)  
-	128*128  
![image](https://github.com/user-attachments/assets/1b99d59e-d39b-4b6c-802d-5d47cf1aaf4a)
-	256*256  
![image](https://github.com/user-attachments/assets/9e50c56e-0b36-418b-9df1-6b5568647c04)  
이후에 두 input data size에 대해 learning rate는 0.000007로 고정하고 epohcs와 batch size를 변경해가며 모델을 학습시키고 test accuracy 값을 확인했는데, 이 경우에도 전반적으로 input size가 256*256일 때 test accuracy가 높게 나온 것을 확인하였다.  
![image](https://github.com/user-attachments/assets/9febab2f-6733-4c98-9926-9cc7f0cb1807)
![image](https://github.com/user-attachments/assets/cc8795ac-99c4-4004-a480-c6f5b35eaeb7)  
### 5.	batch size(hyper parameter)에 따른 test accuracy 비교
input data size는 4.에서 test accuracy가 상대적으로 높았던 256*256으로 설정하였고,  epochs 200, learning rate 0.000007으로 고정하고 batch size를 2^1에서 2^6까지 변화시켜가며 실험을 진행했다. 본 비교 분석에서는 batch size가 8일 때 test accuracy가 가장 높은 것으로 나타났다.  
![image](https://github.com/user-attachments/assets/83ae33ce-c024-492c-b754-03fd26b66c2a)  
-	2  
 ![image](https://github.com/user-attachments/assets/6f011f4a-1aef-4268-9f9a-2f49a60a6df4)  
-	4  
 ![image](https://github.com/user-attachments/assets/19cd28d7-94f9-47ce-b680-09ecbeb5398e)  
-	8  
 ![image](https://github.com/user-attachments/assets/8c0a43d2-cfab-4216-bd62-e74d727c5599)  
-	16  
 ![image](https://github.com/user-attachments/assets/5d57694e-421d-482b-912e-9361455055dc)  
-	32  
 ![image](https://github.com/user-attachments/assets/481cdfd7-8de0-46c5-95cd-d21cc5d040a3)  
-	64  
 ![image](https://github.com/user-attachments/assets/c1deb3e4-d7f4-464f-946d-a02a6919038e)  
### 6.	learning rate(hyper parameter)에 따른 test accuracy 비교
input data size는 4.에서 test accuracy가 상대적으로 높았던 256*256, batch size는 5.에서 test accuracy가 상대적으로 높았던 8로 설정하였고,  epochs 10으로 고정하고 learning rate 0.001, 0.0001, 0.00007, 0.000007에 대하여 비교 분석을 실시하였다. 해당 비교 분석에서는 learning rate가 0.000007일 때 가장 높은 test accuracy를 보였다.  
![image](https://github.com/user-attachments/assets/9da36ebc-be09-4220-b890-0e4531383916)  
-	0.001  
 ![image](https://github.com/user-attachments/assets/831abdde-266a-405d-8d33-a0b1b7d53e1f)  
-	0.0001  
 ![image](https://github.com/user-attachments/assets/5e0aa32b-5812-4906-9be5-36dab5b722d8)  
-	0.00007  
 ![image](https://github.com/user-attachments/assets/90aa17fe-d536-444a-97ac-e2b54397d00d)  
-	0.000007  
 ![image](https://github.com/user-attachments/assets/73e7664e-a9e4-41d8-984d-6a12ea25ffb4)  
### 7.	epochs(hyper parameter)에 따른 test accuracy 
input data size는 4.에서 test accuracy가 상대적으로 높았던 256*256, batch size는 5.에서 test accuracy가 상대적으로 높았던 8, learning rate는 6.에서 상대적으로 높았던 0.000007로 설정하고, epochs 2, 100, 200, 300에 대하여 비교 분석을 실시하였다. 그 결과 epochs이 200일 때 test accuracy가 가장 높은 것으로 나타났다.  
![image](https://github.com/user-attachments/assets/a71abc47-aad9-4fec-b1a6-3bcab53008e9)  
  
-	10  
 ![image](https://github.com/user-attachments/assets/afbbfe4e-1d24-422c-8d6e-3418d22a7458)  
-	100  
 ![image](https://github.com/user-attachments/assets/2fe2714a-7554-4bfe-b71c-94260b769ae8)  
-	200  
 ![image](https://github.com/user-attachments/assets/322c43c5-0952-44ea-af9f-b1e8b70aca9c)  
-	300  
 ![image](https://github.com/user-attachments/assets/69871dbe-50ce-4585-979c-7b7e82dc2686)  
## Conclusion
-	 Input data size와 여러 hyper parameters 등을 변화시켜 학습한 결과 input data size 256*256, epochs 10, batch size 8, learning rate 0.000007에서 가장 높은 test accuracy 값을 보였다. 그러나 병변 진단에 있어서는 성공적인 결과가 도출되었다고 할 수는 없다. 더 좋은 성능을 위해서는 여러 요인을 변화시켜서 학습했던 결과를 바탕으로 model layer를 추가적으로 변경해야 할 것이다.

## Reference
-	 Mahbod, A., Schaefer, G., Wang, C., Dorffner, G., Ecker, R. & Ellinger, I. (2020). Transfer learning using a multi-scale and multi-network ensemble for skin lesion classification. https://doi.org/10.1016/j.cmpb.2020.105475.
-	GitHub - j05t/lesion-analysis: Skin Lesion Analysis Towards Melanoma Detection
-	GitHub - dasoto/CNN to identify malign moles on skin
-	Song Hyun Han, Soon Heum Kim, Cheol Keun Kim, Dong In Jo (2020). Multiple nonmelanocytic skin cancers in multiple regions. https://www.ncbi.nlm.nih.gov/pmc/articles/PMC7349131/ 

