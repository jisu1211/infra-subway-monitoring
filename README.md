## 1단계 - 웹 성능 테스트
### 요구사항
```
[X] 웹 성능 예산 작성 후 서버 목표 응답시간 도출
```

<br />

### 경쟁사와 비교분석
| mobile | 서울교통공사 | 카카오맵 | 네이버지도 | RunningMap |
|--------|--------------|----------|------------|------------|
| FCP    | 6.5s         | 1.7s     | 2.4s       | 14.8s      |
| SI     | 8.1s         | 7.5s     | 4.7s       | 14.8s      |
| LCP    | 11.2s        | 5.4s     | 7.5s       | 15.4s      |
| TTI    | 8.4s         | 5s       | 6.7s       | 15.5s      |
| TBT    | 1220ms       | 80ms     | 560ms      | 580ms      |
| CLS    | 0            | 0.005    | 0.03       | 0.042      |

| desktop | 서울교통공사 | 카카오맵 | 네이버지도 | RunningMap |
|---------|--------------|----------|------------|------------|
| FCP     | 1.5s         | 0.5s     | 0.6s       | 2.7s       |
| SI      | 2.5s         | 2.3s     | 2.2s       | 2.7s       |
| LCP     | 3.7s         | 1.1s     | 1.1s       | 2.8s       |
| TTI     | 2.2s         | 0.1s     | 0.7s       | 2.8s       |
| TBT     | 490ms        | 0ms      | 0ms        | 30ms       |
| CLS     | 0.001        | 0.029    | 0.006      | 0.004      |

[용어 정리]
> * FCP(First Contentful Paint): 첫 번째 텍스트 또는 이미지가 표시되는 시간을 나타낸다.
> * SI(Speed Index): 페이지 콘텐츠가 얼마나 빨리 표시되는지를 보여준다.
> * LCP(Largest Contentful Paint): 최대 텍스트 또는 이미지가 표시되는 시간을 나타낸다.
> * TTI(Time to Interactive): 완전히 페이지와 상호작용할 수 있게 될 때까지 걸리는 시간이다. 
> * TBT(Total Blocking Time): FCP와 상호작용 시간 사이의 모든 시간의 합으로 작업 지속 시간이 50ms를 넘으면 밀리초 단위로 표현된다.
> * CLS(Cumulative Layout Shift): 표시 영역 안에 보이는 요소의 이동을 측정한다.

<br />

### 웹 성능 예산 산정

1. 웹 성능예산은 어느정도가 적당하다고 생각하시나요
* 1차적으로 경쟁사인 `카카오맵`, `네이버지도`와 비슷한 수준의 성능 예산을 잡아야 한다고 생각합니다. 모바일 환경에서 특히 성능이 차이가 발생하므로 해당 부분에서 개선이 좀 더 필요할 것으로 보입니다.
* 1차적인 성능 목표를 달성하고 나면 이후에 2차적으로 경쟁사보다 더 빠른 성능 목표를 산정할 수 있습니다.

<br />

| mobile | FCP      | SI       | LCP      | TTI    |
|--------|----------|----------|----------|--------|
| 현재   | 14.8s    | 14.8s    | 15.4s    | 15.5s  |
| 개선   | 1.7-1.8s | 3.4-4.7s | 2.5-5.4s | 3.8-5s |

| desktop | FCP  | SI   | LCP  | TTI  |
|---------|------|------|------|------|
| 현재    | 2.7s | 2.7s | 2.8s | 2.8s |
| 개선    | 0.5s | 2.2s | 1.1s | 0.1s |

* PageSpeed에서 `빠름`으로 판단하는 수치(`Lighthouse 성능 감사` 90-100)와 `카카오맵`, `네이버지도` 중 더 빠른 속도를 목표로 하여 작성하였습니다. 

<br />

2. 웹 성능예산을 바탕으로 현재 지하철 노선도 서비스의 서버 목표 응답시간 가설을 세워보세요.
* 먼저, PageSpeed에서 추천하는 개선사항들을 먼저 시도해볼 것입니다. 
  * 텍스트 압축 사용: 총 네트워크 바이트를 최소화하려면 텍스트 기반 리소스를 압축(gzip, deflate, brotli)해야 한다.
  * 사용하지 않는 자바스크립트 줄이기
  * 렌더링 차단 리소스 제거하기
  * 콘텐츠가 포함된 최대 페인트 이미지 미리 로드
  * 사용하지 않는 CSS 줄이기
* 그래도 개선되지 않는 부분이 있다면 개별적으로 개선점을 찾아서 시도해볼 것입니다. 
  * [FCP 점수를 향상시키는 방법](https://web.dev/first-contentful-paint/?utm_source=lighthouse&utm_medium=lr)
  * [SI 점수를 향상시키는 방법](https://web.dev/speed-index/?utm_source=lighthouse&utm_medium=lr)
  * [LCP 점수를 향상시키는 방법](https://web.dev/lcp/#how-to-improve-largest-contentful-paint-on-your-site)
  * [TTI 점수를 향상시키는 방법](https://web.dev/interactive/?utm_source=lighthouse&utm_medium=lr)

---

## 2단계 - 부하 테스트 
### 요구사항
```
[X] 부하 테스트
    [X] 테스트 전제조건 정리
        [X] 대상 시스템 범위
        [X] 목푯값 설정 (latency, throughput, 부하 유지기간)
        [X] 부하 테스트 시 저장될 테이터 건수 및 크기
    [X] 아래 시나리오 중 하나를 선택하여 스크립트 작성
        [X] 접속 빈도가 높은 페이지
        [ ] 데이터를 갱신하는 페이지
        [ ] 데이터를 조회하는데 여러 데이터를 참조하는 페이지
    [X] Smoke, Load, Stress 테스트 후 결과를 기록
```

### 테스트 계획하기
* 전제 조건 정리
  * 테스트하려는 Target 시스템의 범위를 정한다.
  * 부하 테스트 시에 저장될 데이터 건수와 크기를 결정한다. 서비스 이용자 수, 사용자의 행동 패턴, 사용 기간 등을 고려하여 계산한다.
  * 목푯값에 대한 성능 유지기간을 정해야 한다.
  * 서버에 같이 동작하고 있는 다른 시스템, 제약 사항 등을 파악한다. 
* 목푯값 설정
  * 우선 예상 1일 사용자 수(DAU)를 정한다.
  * 피크 시간대의 집중률을 예상해본다. (최대 트래픽 / 평소 트래픽)
  * 1명당 1일 평균 접속 혹은 요청 수를 예상해본다.
  * 이를 바탕으로 Throughput을 계산한다. 
    * Throughput: 1일 평균 rps ~ 1일 최대 rps
      * 1일 사용자 수(DAU) * 1명당 1일 평균 접속 수 = 1일 총 접속 수
      * 1일 총 접속 수 / 86,000 (초/일) = 1일 평균 rps
      * 1일 평균 rps * (최대 트래픽 / 평소 트래픽) = 1일 최대 rps
    * Latency: 일반적으로 50 ~ 100ms 이하로 잡는 것이 좋다.
    * 사용자가 검색하는 데이터의 양, 갱신하는 데이터의 양 등을 파악한다.
* VUser 구하기
  * Request Rate: measured by the number of requests per second (RPS)
  * VU: the number of virtual users
  * R: the number of requests per VU iteration
  * T: a value larger than the time needed to complete a VU iteration
  ```
  T = (R * http_req_duration) (+ ls) ; 내부망에서 테스트할 경우 예상 latency를 추가한다.
  VUser = (목표 rps * T) / R
  ```

### 테스트 종류
* Smoke Test
  * 최소한의 부하로 구성된 테스트로, 테스트 시나리오에 오류가 없는지 확인할 수 있다.
  * 최소 부하 상태에서 시스템에 오류가 발생하지 않는지 확인할 수 있다.
  * VUser를 1 ~ 2로 구성하여 테스트한다.
* Load Test
  * 서비스의 평소 트래픽과 최대 트래픽의 상황에서 성능이 어떤지 확인한다. 이 때 기능이 정상 동작하는지도 확인한다.
  * 애플리케이션 배포 및 인프라 변경(scale out, DB failover 등)시에 성능 변화를 확인한다.
  * 외부 요인(결제 등)에 따른 예외 상황을 확인한다.
* Stress Test
  * 서비스가 극한의 상황에서 어떻게 동작하는지 확인한다.
  * 장기간 부하 발생에 대한 한계치를 확인하고 기능이 정상 동작하는지 확인한다.
  * 최대 사용자 또는 최대 처리량을 확인한다.
  * 스트레스 테스트 이후 시스템이 수동 개입없이 복구되는지 확인한다.

### 부하 테스트
1. 부하테스트 전제조건은 어느정도로 설정하셨나요
* 대상 시스템 범위
  * Reverse Proxy Nginx, Tomcat, MySQL
* 목푯값 설정 (latency, throughput, 부하 유지기간)
  * DAU 지정하기
    > [네이버 지도 MAU가 1,392만 명이고 카카오 맵 MAU가 729만명입니다.](https://blog.naver.com/rkwkrhspm/222515422896) \
      위의 내용을 참고하여 RunningMap이 이제 막 오픈하는 서비스라고 가정했을때 네이버 지도보다 좀 더 낮은 카카오 맵의 MAU를 기준으로 테스트를 하고자 합니다. \
      DAU: 약 25만 명
  * 피크 시간대의 집중률 예상하기
    > [출퇴근 시간인 피크 시간대는 100만 명 이상, 낮 시간대는 50만 명 이하 정도로 파악됩니다.](https://www.bigdata-map.kr/datastory/traffic/seoul) \
      위의 내용을 참고하여 평소 트래픽 40만 명, 최대 트래픽 120만 명 정도로 지정하였습니다. \
      최대 트래픽 / 평소 트래픽 = 120만 / 40만 = 3 
  * 1명당 1일 평균 접속 혹은 요청 수 예상하기
    > [카카오맵은 아니지만 카카오 지하철의 일 평균 실행 횟수가 3회입니다.](https://ko.lab.appa.pe/2016-09/kakao-korea.html) \
      위의 내용을 참고하여 1명당 1일 평균 접속 수를 지정하였습니다. \
      1명당 1일 평균 접속 수: 3회 \
      1명당 1일 평균 요청 수: 3회 (메인 페이지 -> 경로 검색 페이지 -> 경로 검색 요청)
  * Throughput 계산하기
    > 1일 총 접속수: 1일 사용자 수(DAU) * 1명당 1일 평균 접속 수 = 25만 * 3 = 75만 \
      1일 평균 rps: 1일 총 접속 수 / 86,000 = 75만 / 86000 = 8.72 \
      1일 최대 rps: 1일 평균 rps * (최대 트래픽 / 평소 트래픽) = 8.72 * 3 = 26.16
  * Latency 지정하기
    > 서버가 클라이언트로부터 요청을 받고 응답을 보내주기까지 걸리는 시간 \
      Latency: 100ms 이내
  * 부하 유지기간 지정하기
    > Smoke 테스트는 1분, Load 테스트는 30분, Stress 테스트는 1시간(1), 1시간 20분(2) 동안 수행하였습니다.
* 부하 테스트 시 저장될 데이터 건수 및 크기
  > Line: 23건(16384 bytes) \
  > Section: 340건(49152 bytes) \
  > Station: 616건(65536 bytes)
* VUser 구하기
  > R(메인 페이지 -> 경로 검색 페이지 -> 경로 검색 요청) = 3 \
  > T = (3 * 0.2) + 1 = 1.6s \
  > VUser(평균) = (8.72 * 1.6) / 3 = 4.65 \
  > VUser(최대) = (26.16 * 1.6) / 3 = 13.95

2. Smoke, Load, Stress 테스트 스크립트와 결과를 공유해주세요
* `src/main/resources/loadtest`에 포함하였습니다.
* stress 테스트에서 max VUser를 `150`으로 했을 때는 지정한 시간 내에 정상적으로 모든 요청이 종료되었으나, max VUser를 `250`으로
  하고, 시간도 1시간에서 `1시간 20분`으로 늘려서 진행하자 실패한 요청은 없으나 요청이 매우 지연되어 `1시간 40분` 동안 수행되었습니다. 
  `stress-test-k6-2.png`에서 요청이 지연되었다는 로그를 확인할 수 있습니다. 
---

## 3단계 - 로깅, 모니터링
### 요구사항
```
[X] 애플리케이션 진단하기 실습을 진행해보고 문제가 되는 코드를 수정
[X] 로그 설정하기
    [X] Application Log 파일로 저장하기
        * 회원가입, 로그인 등의 이벤트에 로깅을 설정
        * 경로찾기 등의 이벤트 로그를 JSON으로 수집
    [X] Nginx Access Log 설정하기
[X] CloundWatch로 모니터링
    [X] Cloudwatch로 로그 수집하기
    [X] Cloudwatch로 메트릭 수집하기
    [X] USE 방법론을 활용하기 용이하도록 대시보드 구성
```

### 로깅
#### 주의점
* Avoid side effects
  * logging으로 인해 애플리케이션 기능의 동작에 영향을 미치지 않아야 한다.
* Be concise and descriptive
  * 각 Logging에는 데이터와 설명이 모두 포함되어야 한다.
* Log method arguments and return values
  * 메소드의 input과 output을 로그로 남기면 debugger를 사용해 디버깅하지 않아도 된다. 특히 debugger를 사용할 수 없는 상황에서는 
    상당히 유용하게 사용할 수 있다.
  * 중복 코드의 발생은 AOP를 통해 해결할 수 있다.
* Delete personal information
  * 로그에 개인정보를 남기지 않는다.
#### Logging level
* `ERROR`: 예상하지 못한 심각한 문제가 발생하여 즉시 조사해야 함.
  * ERROR 레벨로 설정하면 ERROR 레벨의 로그만 출력된다.
* `WARN`: 로직상 유효성 확인, 예상 가능한 문제로 인한 예외처리 등을 남김, 서비스는 운영될 수 있지만, 주의해야 함.
* `INFO`: 운영에 참고할만한 사항으로, 중요한 비즈니스 프로세스가 완료됨.
* `DEBUG`/`TRACE`: 개발 단계에서만 사용하고 운영 단계에서는 사용하지 않음.
  * DEBUG 레벨로 설정하면 DEBUG 레벨보다 높은 로그의 메시지가 모두(DEBUG, INFO, WARN, ERROR) 출력된다.

<br />

1. 각 서버내 로깅 경로를 알려주세요
* nginx: `/var/log/nginx/access.log`
* application: `/home/ubuntu/nextstep/infra-subway-deploy/log`

2. Cloudwatch 대시보드 URL을 알려주세요
* [CloudWatch Dashboard 경로](https://ap-northeast-2.console.aws.amazon.com/cloudwatch/home?region=ap-northeast-2#dashboards:name=jisu1211-dashboard)
