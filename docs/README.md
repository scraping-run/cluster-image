####

#### 클러스터 이미지 컴파일

모든 이미지는 runtime 디렉토리의 여러 디렉토리를 기반으로 제작됩니다

##### k8s 이미지

1. containerd containerd 관련 구성 및 스크립트
2. docker docker 관련 구성 및 스크립트
3. rootfs 공통 rootfs 관련 구성 및 스크립트
4. .github/hack/containerd.sh 바이너리 다운로드 및 기타 구성 수정 스크립트
5. .github/hack/build.sh k8s 클러스터 이미지 컴파일 및 x86과 arm64 아키텍처 이미지 동시 빌드

containerd를 지원하며, 최종적으로 docker.io/labring/kubernetes:$VERSION이 최종 이미지 이름이 됩니다.
- docker.io/labring/kubernetes:$VERSION-amd64는 AMD64 아키텍처입니다
- docker.io/labring/kubernetes:$VERSION-arm64는 ARM64 아키텍처입니다

docker를 지원하며, 최종적으로 docker.io/labring/kubernetes-docker:$VERSION이 최종 이미지 이름이 됩니다.
- docker.io/labring/kubernetes-docker:$VERSION-amd64는 AMD64 아키텍처입니다
- docker.io/labring/kubernetes-docker:$VERSION-arm64는 ARM64 아키텍처입니다

>`$VERSION`＝`k8sVersion`＋`sealosVersion`, 자세한 내용은 https://github.com/labring/cluster-image/issues/131 참조
>>`v1.24`(이미지 태그 예시: v1.24-amd64/v1.24-arm64)
>>>k8s는 v1.24 최신 릴리스 버전, sealos는 최신 개발 버전 (ghcr.io/labring/sealos-patch:dev)

>>`v1.24.4`(이미지 태그 예시: v1.24.4-amd64/v1.24.4-arm64)
>>>k8s는 v1.24.4 이전 릴리스 버전, sealos는 최신 정식 버전

>>`v1.24.4-4.1.3`(이미지 태그 예시: v1.24.4-4.1.3-amd64/v1.24.4-4.1.3-arm64)
>>>k8s는 v1.24.4 이전 릴리스 버전, sealos는 4.1.3 이전 릴리스 버전

##### APP 이미지

모든 이미지는 applications/$NAME/$VERSION 디렉토리에 관련 구성을 저장합니다

- NAME은 이미지 이름입니다
- VERSION은 이미지 버전입니다

최종적으로 docker.io/labring/$NAME:$VERSION이 최종 이미지 이름이 됩니다.

- docker.io/labring/$NAME:$VERSION-amd64는 AMD64 아키텍처입니다
- docker.io/labring/$NAME:$VERSION-arm64는 ARM64 아키텍처입니다

applications/$NAME/$VERSION 아래에 init.sh를 저장하며, 주로 일부 바이너리를 별도로 저장해야 하는 경우에 사용됩니다.
init.sh는 일부 바이너리를 다운로드하는 데 사용되며, 예시 코드는 helm과 minio-operator 스크립트를 참조하세요

##### 구성 이미지

모든 이미지는 config/$NAME/$VERSION 디렉토리에 관련 구성을 저장합니다

- NAME은 이미지 이름입니다
- VERSION은 이미지 버전입니다

최종적으로 docker.io/labring/$NAME:$VERSION이 최종 이미지 이름이 됩니다.


##### 이미지 빌드 방법

1. 빌드 디렉토리에 manifests를 새로 만들고 해당 yaml을 넣으면 sealos build가 자동으로 이미지 버전을 파싱합니다
2. 빌드 디렉토리에 charts를 새로 만들고 해당 chart를 넣으면 sealos build가 자동으로 이미지 버전을 파싱합니다
3. 수동으로 이미지를 작성해야 하는 경우 빌드 디렉토리에 images/shim 디렉토리를 새로 만들고 imageList 파일을 작성합니다(파일명은 제한 없음)
4. Dockerfile 작성
    ```dockerfile
    FROM scratch 
    COPY manifests ./app/manifests
    COPY charts ./app/charts
    COPY registry ./registry
    CMD ["kubectl apply -f app/manifests/"]
    ```
   주로 APP을 구분하기 위한 것입니다