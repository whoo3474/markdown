
개발환경과 상용 환경이 있을때,

일반적인 상수들은 ConfigMap
키, 패스워드 같이 보안이 필요한것은 Secret
 

Pod생성시 Container의 환경변수로 넣을수 있는것이 장점

--------------------------------------------------------

# 1. 환경설정 - 상수, 
   ConfigMap은 키와 밸류로 구성되어있음.
   Secret은 Base64로 밸류를 구성함
   파드로 주입을 할때는 자동으로 Decoding되어 값이 보이게됌

   일반적인 Object는 쿠버네티스 DB에 저장되는데 (etcd)
   Secret은 메모리에 저장됌 - 메모리에 저장 되어있는게 보안에 좋음
   평문으로 etcd - kubenetes 쓰는 DB안에 저장은돼 -> 공격자 해킹 가능성

   Secret을 램에 올라가기 때문에 많이만들면 시스템 자원에 영향을 끼칠수 있음

   단 Secret자체는 base64로 변환되어 사용하기에 공격자에게 혼란을 줄수 있을 정도지, 안전한건 아님
   base64는 암호화가 아니라 인코딩임

#### ConfigMap
```yaml
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: ConfigMap
metadata:
  name: cm-dev
data:
  SSH: 'false'
  SSH2: 'false'
  SSH3: 'false'
  SSH4: 'false'
  User: dev
EOF
```

#### Secret
```yaml
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: sec-dev
data:
  Key: MTIzNA==
EOF
```

#### Pod
```yaml
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: pod-1
spec:
  containers:
  - name: container
    image: kubetm/init
    envFrom:
    - configMapKRef:
        name: cm-dev
    - secretRef:
        name: sec-dev
EOF
```



--------------------------


# 2. 환경설정 - 파일 (한번 주입하면 끝이기때문에, ConfigMap은 불변함)


```shell
echo "ConfigMap Content" >> file-c.txt
kubectl create configmap cm-file --from-file=./file.txt
```

```shell
// 해당 명령어를 상용시, base64로 변경되어 저장됌
echo "Secret Content" >> file-s.txt
kubectl create secret generic sec-file --from-file=./file.txt
```

```yaml
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: pod-file
spec:
  containers:
  - name: container
    image: kubetm/init
    env:
    - name: file-c
      valueFrom:
        configMapKeyRef:
          name: cm2-file
          key: file-c.txt
    - name: file-s
      valueFrom:
        secretKeyRef:
          name: sec2-file
          key: file-s.txt
EOF
```

-------------------

3. 볼륨 - 파일 (Mount란 원본과 연결되어 있는것이기 때문에, 같이 바뀜)

```yaml
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: pod-mount
spec:
  containers:
  - name: container
    image: kubetm/init
    volumeMounts:
    - name: file-volume
      mountPath: /mount
  volumes:
  - name: file-volume
    secret:
      name: sec-file
EOF
```


--------------------------------

### kubectl 
#### configMap
```shell
# file-c.txt 라는 파일로 cm-file라는 이름의 ConfigMap 생성
kubectl create configmap cm-file --from-file=./file-c.txt
# key1:value1 라는 상수로 cm-file라는 이름의 ConfigMap 생성
kubectl create configmap cm-file --from-literal=key1=value1
# 여러 key:value로 cm-file라는 이름의 ConfigMap 생성 
kubectl create configmap cm-file --from-literal=key1=value1 --from-literal=key2=value2
```

#### Secret Generic
```shell
# file-s.txt 라는 파일로 sec-file라는 이름의 Secret 생성
kubectl create secret generic sec-file --from-file=./file-s.txt
# key1:value1 라는 상수로 sec-file라는 이름의 Secret 생성
kubectl create secret generic sec-file --from-literal=key1=value1
```