# cluster-image

Sealos 클러스터 이미지는 Sealos 도구의 혁신적인 기능입니다. 이 기능을 통해 사용자는 Kubernetes 클라우드 네이티브 애플리케이션과 플러그인을 통합된 Docker 이미지로 패키징하여 클라우드 네이티브 생태계에서 다양한 애플리케이션과 플러그인의 배포 및 관리를 단순화하고 표준화할 수 있습니다.

- Rootfs 클러스터 이미지: [runtime 프로젝트](https://github.com/labring-actions/runtime)
- Github 이미지 목록: [cluster-image-docs](https://github.com/labring-actions/cluster-image-docs)
- DockerHub 이미지: [docker.io/labring](https://hub.docker.com/u/labring)

## 주요 기능

1. :package: 통합 패키징: Kubernetes 애플리케이션과 플러그인 종속성에 필요한 모든 리소스(YAML 파일, Helm charts, Docker 이미지, 바이너리 파일 포함)를 통합합니다.
2. :sparkles: 간편한 설치: 단일 이미지로 전체 애플리케이션을 원클릭 배포하는 기능을 제공하여 배포 복잡성과 시간을 크게 줄입니다.
3. :globe_with_meridians: 오프라인 배포: 네트워크 연결이 없는 환경에서도 클러스터 이미지 배포를 지원합니다.
4. :wrench: 높은 사용자 정의 가능: 다양한 환경과 요구사항에 맞는 배포를 위해 사용자 요구에 따른 맞춤형 패키징을 지원합니다.
5. :earth_africa: 다중 환경 호환성: 개발, 테스트, 프로덕션 환경 등 다양한 실행 환경에 적용 가능하며 일관성과 안정성을 보장합니다.
6. :arrows_counterclockwise: 빠른 반복 및 버전 업데이트: 클러스터 이미지의 애플리케이션은 대부분 `helm upgrade --install` 형식으로 설치되어 빠른 버전 반복과 업그레이드가 가능합니다.

## 이미지 유형

**kubernetes 이미지:**

| 이미지 주소                                    | 이미지 이름            | 설명                     |
| :------------------------------------------ | :------------------ | :----------------------- |
| `docker.io/labring/kubernetes:<tag>`        | `kubernetes`        | containerd 컨테이너 런타임 포함 |
| `docker.io/labring/kubernetes-docker:<tag>` | `kubernetes-docker` | docker 컨테이너 런타임 포함     |
| `docker.io/labring/kubernetes-crio::<tag>`  | `kubernetes-crio`   | crio-o 컨테이너 런타임 포함     |

**application 이미지:**

| 이미지 주소                          | 이미지 이름 | 설명            |
| --------------------------------- | -------- | --------------- |
| `docker.io/labring/helm:<tag>`    | helm     | helm 바이너리 파일  |
| `docker.io/labring/calico:<tag>`  | calico   | calico 네트워크 플러그인  |
| `docker.io/labring/openebs:<tag>` | openebs  | openebs 스토리지 플러그인 |
| ......                            |          | ......          |


## 빌드 방법

Sealos의 dockerhub 이미지는 `github action`을 사용하여 자동으로 빌드됩니다. 커뮤니티 사용자는 github ISSUE를 생성하여 빌드 작업을 트리거할 수 있으며, ISSUE에서 지원되는 명령과 매개변수를 사용하여 필요한 이미지와 버전을 빌드할 수 있습니다. 버전 매개변수는 애플리케이션 공식 웹사이트, helm 저장소 또는 github release 페이지를 참조하세요.

이미 기여된 이미지는 직접 클릭하여 Github ISSUE로 이동하여 새 버전을 빌드할 수 있습니다: [ :arrow_forward: ] [ISSUE 생성하기](https://github.com/labring/cluster-image/issues/new?assignees=&labels=&template=autobuild-apps.md&title=【Auto-build】helm).

새로운 nginx 클러스터 이미지 빌드를 예로 들면, 커뮤니티가 이미 `application` 디렉토리에 nginx 빌드 스크립트를 기여했으므로, ISSUE에서 `【Auto-build】nginx` 제목을 검색하고 댓글 상자에 다음 명령을 입력하면 됩니다. 이미지 이름(고정)과 이미지 버전(공식 버전과 일치)을 조합하여 새 버전의 nginx 클러스터 이미지를 빌드할 수 있으며, 빌드 완료 후 자동으로 DockerHub에 업로드됩니다.

예시 명령:

```bash
/imagebuild_apps nginx v1.23.1
```
보안상의 이유와 관리 편의를 위해 `accepted` 레이블이 없는 ISSUE에서 이미지 빌드를 트리거하거나 ISSUE 제목의 애플리케이션 이름이 명령의 애플리케이션 이름과 일치하지 않으면 이미지 빌드가 작동하지 않습니다.
이미지를 빌드해야 하는 경우 저장소 관리자에게 알리거나 @멘션하여 `accepted` 레이블을 추가하고 해당 ISSUE에서 이미지를 빌드하세요.
ISSUE에서 해당 애플리케이션을 찾을 수 없는 경우 템플릿을 사용하여 새 ISSUE를 추가하세요. 제목 형식은 반드시 올바르게 작성되어야 합니다.

### 프로젝트 구조

이미지 구성 저장 위치 및 디렉토리 구조는 다음과 같습니다:

```yaml
├── applications                # 모든 애플리케이션
│   ├── apisix                  # 애플리케이션 이름
│   │   ├── latest              # 애플리케이션 버전
│   │   │   ├── entrypoint.sh   # 설치 스크립트
│   │   │   ├── init.sh         # 종속성 다운로드
│   │   │   └── Kubefile        # 이미지 파일
│   │   └── README.md           # 사용 설명서
│   ├── argocd
│   │   ├── latest
│   │   │   ├── entrypoint.sh
│   │   │   ├── init.sh
│   │   │   └── Kubefile
│   │   └── README.md
```

**빌드 규칙 설명:**

- ISSUE에서 전달된 버전 매개변수가 `applications/<애플리케이션 이름>` 아래의 `<애플리케이션 버전>`과 일치하면 `<애플리케이션 버전>` 디렉토리의 `init.sh` 스크립트를 실행하고 해당 디렉토리 컨텍스트를 기반으로 빌드합니다.
- 고정 버전을 찾을 수 없으면 버전 정보가 `latest` 디렉토리의 `init.sh` 스크립트로 전달되고 해당 스크립트를 실행하며 해당 디렉토리 컨텍스트를 기반으로 빌드합니다.

**디렉토리 구조 설명:**

- `init.sh`: 이름은 변경할 수 없으며, 내용은 사용자 정의 가능합니다. 일반적으로 helm chart, yaml 파일 및 다른 아키텍처의 바이너리 파일(예: helm, kubectl-minio 관련)을 다운로드하는 데 사용됩니다.
- `Kubefile`: 이미지 빌드 구성 파일로, Docker/Kubefile 이름을 지원합니다.
- `entrypoint.sh`: 이름과 내용을 사용자 정의할 수 있으며, 일반적으로 kubectl apply 또는 helm install과 같은 애플리케이션 실제 배포 명령을 캡슐화합니다.

* `charts`: 이 디렉토리는 클러스터 이미지에 필요한 helm chart를 저장합니다. sealos는 스캔된 chart를 기반으로 이미지 목록을 구문 분석하고 Kubefile과 동일한 수준의 디렉토리에 registry 디렉토리를 빌드합니다.
* `manifests`: 이 디렉토리는 일부 yaml 파일을 저장합니다. sealos는 manifests 디렉토리의 모든 이미지를 스캔하고 Kubefile과 동일한 수준의 디렉토리에 registry 디렉토리를 빌드합니다.
* `images/shim`: 이 디렉토리는 주로 추가 이미지 목록을 저장합니다. 예를 들어 수동으로 `images_list.txt`를 생성하고 Kubefile과 동일한 수준의 디렉토리에 registry 디렉토리를 빌드합니다.
* `opt`: 이 디렉토리는 helm, kubectl-minio 등의 바이너리 파일을 저장합니다.
* `registry`: 기본적으로 자동 생성되며, 이 디렉토리는 Kubefile과 동일한 수준의 디렉토리에 배치되어야 합니다. 그렇지 않으면 master0의 프라이빗 레지스트리에 복사할 수 없으므로 이미지 제작 시 주의가 필요합니다. registry를 charts에 저장하지 마세요. 그렇지 않으면 helm 스캔이 느려져 OOM이 발생할 수 있습니다 [labring/sealos#1545](https://github.com/labring/sealos/issues/1545).
* 템플릿 렌더링: 템플릿이 필요한 경우 etc, charts, manifests에 `*.tmpl`로 끝나는 파일을 배치하면 `sealos run -e` 환경 변수로 렌더링한 후 tmpl이 제거됩니다. 예를 들어 렌더링 전 `aa.yaml.tmpl`은 렌더링 후 `aa.yaml`이 됩니다. 사용 시 파일 이름이 기존 파일과 충돌하지 않도록 주의하세요.

### ISSUE에서 지원하는 명령

Github ISSUE에서 지원하는 명령 목록은 다음과 같습니다:

| 명령                       | 설명                                   |
| -------------------------- | :------------------------------------- |
| `/imagebuild_apps`         | 클러스터 애플리케이션 이미지 빌드                       |
| `/imagebuild_dockerimages` | 표준 docker 이미지 빌드                     |
| `/imagesync`               | 이미지 동기화, 권한 제어가 있으며 로봇만 작업 가능 |

### 로컬 빌드 예시

코드를 로컬로 가져오기

```bash
$ git clone https://github.com/labring-actions/cluster-image.git
```
클러스터 이미지 초기화 스크립트가 있는 디렉토리로 이동
```
$ cd cluster-image/applications/nginx/latest
```
초기화 스크립트를 실행하여 관련 종속성 다운로드
```
$ bash init.sh amd64 nginx v1.25.6
```
sealos 명령을 실행하여 빌드
```
$ sealos build -t docker.io/labring/nginx:v1.25.6 .
```
빌드된 이미지 확인
```
$ sealos images
REPOSITORY                                                      TAG             IMAGE ID       CREATED        SIZE
docker.io/labring/nginx                                         v1.25.2         41328582759a   3 months ago   37.4 MB
```

## 로드맵

- wasm 이미지 지원

## 기여 방법

기여 프로세스는 다음과 같습니다:

1. `application` 경로 아래의 다른 애플리케이션 구현 로직을 참조하여 자신의 애플리케이션에 대한 `init.sh` 스크립트, `Kubefile` 및 `entrypoint.sh`를 작성합니다.
2. PR을 제출하여 코드를 github cluster-image 저장소에 병합합니다.
3. ISSUE를 생성하고 빌드를 실행합니다. 빌드가 성공하면 이미지가 자동으로 공식 dockerhub 저장소에 푸시됩니다.
4. 이미지를 로컬로 가져와서 sealos run 명령을 사용하여 애플리케이션을 설치하고 실행합니다.