#### 과제
![image](https://github.com/cwangg897/infra-study/assets/79621675/3f723d73-8707-4cc2-9cfd-b0b7352c7afb)


#### MultiStage란?
![image](https://github.com/cwangg897/infra-study/assets/79621675/9b9c0627-bdfe-4223-9393-f71ad8460a04)
```
왜사용하나면 먼저 결과부터는 도커 이미지 크기를 줄일 수 있습니다
Multi-stage는 여러 개의 Image Base를 사용하여 Docker이미지를 생성하는 것이다
FROM키워드로 스테이지(작업단위)를 구분합니다
중요한것은 마지막 스테이지가 최종 도커 이미지입니다.
```

#### 이미지 크기 작으면 생기는 이점
```
이미지 크면 왜 안좋은가? 이미지도 용량을 차지해서 나중에 공간부족하다고 뜨기도하고
불필요하게 결과물만 가지면되는데 패키지나 의존성도 같이 가질필요가없음
1. 더 빠른 배포: 작은 이미지는 다운로드 및 업로드 시간이 감소하므로 배포 속도가 향상됩니다. 
2. 메모리 절약
3. 보안: 작은 이미지는 보안 측면에서 이점을 가질 수 있습니다. 불필요한 패키지 및 의존성을 포함하지 않으면 공격 벡터가 줄어들고 이미지의 취약성이 감소합니다.
4. 네트워크 대역폭 절약: 작은 이미지는 네트워크 대역폭을 덜 사용하므로 원격 서버 간 이미지 전송에 있어 이점을 제공합니다. 특히 클라우드 환경에서 중요합니다.

그래서 이미지 만들 때 멀티스테이지 사용하는것을 추천한다
```

#### 언제 사용하는게 적합?
```
크기 줄일려는것은 모두의 워너비인데 이 크기 줄이는게 누구에게 적합하냐면은
빌드를 하는 프로그램에게 적합하다 
stage1에서는 빌드에 필요한 도구 설치 + 빌드 수행
stage2에서는 빌드 수행 결과물을 복사하여 결과 실행 (이미지로 만들기)

stage는 병렬실행도 가능하다 따라서 빌드 속도 향상 병렬실행하려면 주의사항은 관계가없어야한다
```



#### Spring Boot프로젝트 멀티스테이지 이용하기
```yaml
베이스 이미지는 FROM openjdk:11-jdk로 하고 별칭 꼭 지어주기

FROM openjdk:11-jdk-alpine AS FIRST_STAGE
ARG JAR_FILE=build/libs/*.jar
COPY ${JAR_FILE} app.jar
ENTRYPOINT ["java", "-jar", "/app.jar"]

FROM openjdk:11-jdk-alpine 
COPY --from=FIRST_STAGE build/libs/app.jar ./build/libs/stage2/
ENTRYPOINT ["java", "-jar", "/app.jar"]
```
