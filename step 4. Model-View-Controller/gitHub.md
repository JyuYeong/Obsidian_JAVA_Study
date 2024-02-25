# 1. 깃허브란?
소프트웨어 개발 및 버전 관리를 위한 웹 기반 호스팅 서비스이다.
Git이라는 분산 버전 관리 시스템을 기반으로 한다.

# 2. gitHub 협업을 위한 기본 지식
## 2.1. Repository(저장소)
### 2.1.1. Repository의 구성
- 로컬 저장소(Local Repository)
	- Working Directory
	- Staging Area
	- Local Repository
- 원격 저장소(Remote Repository)
### 2.1.2. Local Repository
내 PC에 있는 개인 전용 저장소
#### 2.1.2.1. Working Directory
로컬 작업 공간
#### 2.1.2.2. Staging Area
커밋(commit) 시 반영될 파일 보관 공간
#### 2.1.2.3. Local Repository
로컬에 저장된 파일을 push할 경우 원격 저장소에 반영된다.
### 2.1.3. Remote Repository
원격 저장소 전용 서버에서 관리되는, 여러 사람이 함께 공유하기 위한 저장소

## 2.2. gitHub의 동작 원리
![[gitHub Repository|600]]
이클립스<font color="#7f7f7f">(Eclipse)</font>를 기준으로
- 프로젝트를 원격 저장소<font color="#7f7f7f">(Git)</font>에 올리기 위해서는
	1. <font color="#92cddc">commit</font>을 통해 지역 저장소에 1차적으로 배포
	2. <font color="#92cddc">push</font>를 통해 원격 저장소<font color="#7f7f7f">(Git)</font>에 배포
- 원격 저장소에서 프로젝트로 가져오기 위해서는
	1. <font color="#d99694">fetch</font>를 통해 지역 저장소에 1차적으로 저장
	2. <font color="#d99694">pull</font>을 통해 프로젝트로 저장

> [!caution]
> push 전에는 꼭 fetch와 pull을 하자.

