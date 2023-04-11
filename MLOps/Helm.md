Helm
참고: https://kubetm.github.io/helm/

# Helm
- Kubernetes의 package manager
- 도입 배경
    - kubectl 명령을 통해서 App을 생성/배포
    - 각 App 마다 yaml 파일 생성
    - 각 배포 환경마다 yaml 파일이 생성됨 >> 관리하는 yaml 파일이 점점 어려움
- yaml 파일을 동적으로 관리
    - yaml 파일은 정적 파일이라서 리소스 별로 yaml 파일을 만들어야 함
    - 많은 리소스 관리하기에 유지보수가 어려움
    - 하나의 template을 통해서 yaml 파일을 동적으로 생성하게 함
    - 변수를 줌으로써 yaml 파일을 추가하는 과정을 줄임
- v3 버전은 client에서 kubernetes cluster의 API Server에 직접적으로 통신함
- Helm을 지원하는 Opensource들이 많음
    - 사용자 환경에 따라서 옵션 값을 변경하여 활용할 수 있음

### Getting started
```bash
# repository
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo list
helm search repo bitnami | grep tomcat
helm repo update
helm repo remove bitnami

# 배포
helm install my-tomcat bitnami/tomcat --version 10.5.17 --set persistence.enabled=false

helm list
helm status my-tomcat
helm uninstall my-tomcat
kubectl get pods

# 다운로드
helm pull bitnami/tomcat --version 10.5.17
tar -xf ./tomcat-10.5.17.tgz
helm install my-tomcat . --set persistence.enabled=false
```


## Chart Template

```shell
# 차트 생성 
# Chart.yaml, values.yaml
# templates (NOTES,txt, _helpers.tpl, 각종 yaml 파일)
helm create mychart

# 차트 조회
# 주석을 제외하고 보여줌
helm show values . # valyes.yaml 조회
helm show charts . # Charts.yaml 조회
helm show readme .
helm show all .

# 템플릿 조회 (실제 배포 전에 생각한 대로 배포되는 지 확인)
helm template mychart .

# 차트 배포
helm install mychart .

# 파일 수정 후 재배포
helm upgrade mychart .

# 릴리즈 조회
helm get manifest mychart
helm status mychart
helm get notes mychart
helm get values mychart  # install 명령 때 사용된 values.yaml
```

#### 내장 객체
- templates 내 yaml 파일을 확인해보면 values.yaml, Chart.yaml을 가져올 값을 확인 가능
    - `{{ .Values.replicaCount }}` `{{ .Chart.Name }}`
- 차트 배포 시에 `Release` 변수 값을 지정할 수 있음

#### 변수 주입
- values.yaml 기본으로 가져옴
- 다른 values_xxx.yaml 로 차트를 배포하면 `helm install mychart . -f values_xxx.yaml` values.yaml에 values_xxx.yaml 값이 오버라이딩 되어 사용됨 
- `-set env=test` 과 함께 배포한다면 가장 우선순위가 됨

```yaml
# values.yaml
env: dev
log: debug

# values_prod.yaml
env: prod

data:
    env: {{ .Values.env }}  # prod 
    log: {{ .Values.log }}  # debug
```

#### 사용자 정의 변수
- helpers.tpl: 사용자 정의 키워드와 그에 대한 값을 정의 (다른 templates 내 yaml 파일에서 사용함)
    ```yaml
    {{/*
    Expand the name of the chart.
    */}}
    {{- define "mychart.name" -}}
    {{- default .Chart.Name .Values.nameOverride | trunc 63 | trimSuffix "-" }}
    {{- end }}

    {{/*
    Common labels
    */}}
    {{- define "mychart.labels" -}}
    helm.sh/chart: {{ include "mychart.chart" . }}
    {{ include "mychart.selectorLabels" . }}
    {{- if .Chart.AppVersion }}
    app.kubernetes.io/version: {{ .Chart.AppVersion | quote }}
    {{- end }}
    app.kubernetes.io/managed-by: {{ .Release.Service }}
    {{- end }}
    ```
- service.yaml
    ```yaml
    apiVersion: v1
    kind: Service
    metadata:
        # _helpers.tpl에서 정의한 값을 사용
        # .을 통해서 범위를 지정할 수 있음
        name: {{ include "mychart.fullname" . }} 
        labels:
            {{- include "mychart.labels" . | nindent 4 }}
    ```

### Template 만들기
- -을 앞에 지정하면 앞에 개행문자+띄어쓰기가 무시됨
- function & pipeline
    ```yaml
    # values.yaml
    func:
      enabled: true
    
    # _helper.tpl
    {{- define "mychart.name" -}}
    mychart
    {{- end }}

    # {{ function 인자1 인자2 }}
    {{ quote .Values.func.enabled }}  # "true"
    "{{ .Values.func.enabled }}"  # "true"
    {{ include "mychart.name" . }}  # mychart

    # {{ Values | functions }} >> Value 값에 function을 연속적으로 적용한다
    {{ .Values.func.enabled | quote }}  # "true"
    {{ .Values.func.enabled | upper | quote }}  # "True"
    ```

- flow controll
    ```yaml
    # values.yaml
    dev:
        env: dev
        log: info
    qa:
        env: qa
        log: info


    # 조건문
    # and(&&) or(||) ne(!=) not(!) eq(=) ge(>=) gt(>) le(<=) lt(<) 
    # 0, "", [], {}, Null >> false
    {{- if eq .Values.dev.env "dev" }}
        log: debug
    {{- else if .Values.dev.env }}
        log: {{ .Values.dev.log }}
    {{- else }}
        log: error
    {{- end }}

    # With
    {{- with .Values.dev }}
        env: {{ .env }}
    {{- end }}

    # 반복문
    range:
        {{- range .Values.list }}  # list = [a, b, c]
        - {{ . }}
        {{- end }}
    # 결과
    # - a
    # - b
    # - c
    ```

- various function
    ```yaml
    # print
    print:  {{ print "Hard Cording" }}
    printf: {{ printf "%s-%s" .Values.print.a .Values.print.b }}

    # ternary 변수값이 true면 첫번째, false면 두번째 값이 출력
    case1: {{ .Values.ternary.case1 | ternary "1" "2" }} 
    case2: {{ .Values.ternary.case2 | ternary "1" "2" }}

    # indent: 모든 라인에 n 칸을 띄어라 (yaml 형식을 맞추기 위해서)
    # nindent: 처음에 개행문자를 추가함 (앞에 -이 있는 경우, 엔터, 개행문자 모두 무시)
      indent: 
    {{ .Values.data | toYaml | indent 4 }}
      nindent1: {{ .Values.data | toYaml | nindent 4 }}
      nindent2:
      {{- .Values.data | toYaml | nindent 4 }} # 가장 선호됨
          
    # default: 값이 없다면 default 값을 줌
    # Number:0, String: "", List: [], Object: {}, Boolean: false, Null
    nil:     {{ .Values.default.nil     | default "default" }}
    list:    {{ .Values.default.list    | default (list "default1" "default2") | toYaml | nindent 6}}
    object:  {{ .Values.default.object  | default "default:1" | toYaml | nindent 6 }}
    number:  {{ .Values.default.number  | default 1 }}
    string:  {{ .Values.default.string  | default "default" }}
    boolean: {{ .Values.default.boolean | default true }}

    # trim: 값에 공백이 있다면 공백을 제거 (아래 결과 모두 hello)
    trim:       {{ trim "  hello " }}
    trimPrefix: {{ trimPrefix "-" "-hello" }}
    trimSuffix: {{ trimSuffix "-" "hello-" }}

    # random (몇 개를 추출할 것인가)
    randAlphaNum: {{ randAlphaNum 5 }}   # 0-9a-zA-Z
    randAlpha:    {{ randAlpha 5 }}      # a-zA-Z
    randNumeric:  {{ randNumeric 5 }}    # 0-9
    randAscii:    {{ randAscii 5 }}      # ASCII characters

    trunc:    {{ trunc 5 "hello world" }}
    replace:  {{ "hello world" | replace " " "-" }}
    contains: {{ contains "cat" "catch" }}
    b64enc:   {{ b64enc "hello" }}
    ```

- 지역변수 활용
    ```yaml
    dev:
        {{- $relname := .Release.Name -}}  # 변수 선언
        {{- with .Values.dev }}
        env: {{ .env }}
        release: {{ $relname }}  # 변수 사용
        log: {{ .log }}
        {{- end }}

    data:
        # for (int i ; i=list.length ; i++) { printf "i : list[i]" };
        index:
        {{- range $index, $value := .Values.data }}
        {{ $index }}: {{ $value }}
        {{- end }}

        # for (Map<key, value> map : list) { printf "map.key() : map.value()" };
        key-value:
        {{- range $key, $value := .Values.dev }}
        {{ $key }}: {{ $value | quote }}
        {{- end }}
    ```

## 오픈소스 까보기
### tomcat
- Chart.yaml, Values.yaml 값을 가지고 temapltes 내 값을 채워 원하는 Script를 작성
- 새로운 함수
    - dict
    ```yaml
    {{- $myDict := dict "key1" "value1"}}
      dict: {{ get $myDict "key" }} # value1
    ```
    
    - include: tpl 내 변수의 하위 내용을 모두 가져옴, yaml 파일에서는 toYaml 함수
    ```yaml
    # _helper.tpl (dict의 key1의 값을 사용한다)
    {{- define "mychart.include" -}}
    key: {{ .key1 }}
    dict: {{ get . "key1" }}
    {{- end}}
    
    # templates.yaml (dict를 정의 {"key1":"value1"} 후 _helper.tpl에 있는 내용 가져옴)
    include1: {{- include "mychart.include" (dict "key1" "value1") | indent 4 }}

    # key: value1
    # dict: value1
    ```

    - typeIs: 변수의 타입을 확인
    ```yaml
    {{- if typeIs "string" .Values.typeIs1 }}
    typeIs1: {{ .Values.typeIs1 }}  # "text"
    ```

    - tpl: value.yaml에서 변수로 사용한 값을 가져옴
    ```yaml
    # values.yaml
    defaultLevel: info
    dev: 
        env: dev
        log: "{{ Values.defaultLevel }}"

    # templates.yaml
    tpl: "{{ tpl .Values.dev.log . }}"
    ```

    - coalesce: NULL 값이 아닌 값을 찾음
    ```yaml
    coalesce1: {{ coalesce .Values.data1 .Values.data2 "text1" }}
    ```