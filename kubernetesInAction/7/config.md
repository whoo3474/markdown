이미지를 만들고나서 버전 업데이트 또는 변수를 변경하려면 이미지를 다시 만들어야 한다. 

하드코딩으로 환경변수를 설정을 한다면, 일괄적으로 변경시켜주려면 각각의 yaml파일을 변경을 매번 바꿔줘야한다.


## 도커 컨테이너의 환경 설정
### 일반적인 키와 값
```yaml
env:
    - name : DEMO_GREETING
      value: "Hello from the environment"
```

### ConfigMap
```yaml
env:
    - name : DEMO_GREETING
      valueFrom:
        configMapKeyRef: configmap-name
```

### Secrets
```yaml
env:
    - name : DEMO_GREETING
      valueFrom:
        secretKeyRef: secret-name
```