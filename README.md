## Quay 3.7 New Feature

### 1. New configuration fields

- FEATURE_QUOTA_MANAGEMENT : 이 기능을 통해 사용자는 스토리지 소비를 보고하고 구성된 스토리지 할당량 제한을 설정할 수 있습니다. 
- DEFAULT_SYSTEM_REJECT_QUOTA_BYTES : 모든 조직 사용자에게 적용할 할당량 크기를 제한할 수 있습니다.

- FEATURE_PROXY_CACHE : Red Hat Quay를 사용하여 원격 조직이 사용할 수 있도록 지원됩니다. 이 기능을 사용하면 Red Hat Quay는 업스트립 레지스트리의 pull-rate 제한을 우회하는 Proxy cache 역할을 합니다.

### 1.1 Quota management configuration

Quota management는 `FEATURE_QUOTA_MANAGEMENT` 속성에 따라 지원되며 기본적으로는 비 활성화 되어 있습니다. 해당 설정을 활성화하려면 `config.yaml`에서 `true`로 설정합니다.

```bash
FEATURE_QUOTA_MANAGEMENT: true
```

### 1.2 Using Red Hat Quay to proxy a remote organization configuration

Red Hat Quay를 사용하여 원격 조직을 proxy하는 것은 `FEATURE_PROXY_CACHE` 속성에서 지원됩니다. 해당 설정을 활성화하려면 `config.yaml`에서 `true`로 설정합니다.

```bash
FEATURE_PROXY_CACHE: true
```

### 1.3 Red Hat Quay build enhancements

가상화된 플랫폼에서 빌드를 실행할 수 있습니다. 이전 빌드 구성을 실행하기 위한 이전 버전과의 호환성도 사용 가능합니다. 해당 설정을 활성화하려면 `config.yaml`에서 `true`로 설정합니다.

```bash
FEATURE_BUILD_SUPPORT: true
```

### 1.4 Geo-replication using the Red Hat Quay Operator

Geo-Replication을 사용한 Red Hat Quay 배포는 이제 Operator 배포에 의해 지원됩니다. 해당 설정을 활성화하려면 `config.yaml`에서 `true`로 설정합니다.

```bash
FEATURE_STORAGE_REPLICATION: true
```



### 2. Enabling new functionality

Red Hat Quay 3.7의 새로운 기능을 활용하려면 다음 기능 중 일부 또는 모든 기능을 활성화 할 수 있습니다.

```bash
...
FEATURE_QUOTA_MANAGEMENT: true
FEATURE_BUILD_SUPPORT: true
FEATURE_PROXY_CACHE: true
FEATURE_STORAGE_REPLICATION: true
DEFAULT_SYSTEM_REJECT_QUOTA_BYTES: 2048000000 // 약 2G
...
```

### 2.1 Deploying the Operator using the initial configuration

해당 가이드에서는 최초 배포된 설정을 추출하여, 몇 가지 옵션을 업데이트하여 반영하였습니다.

- `config.yaml` 추출하기

  해당 secret 정보는 설치된 Quay Operator의 이미지 레지스트리에서 정보를 확인 할 수 있습니다. (Config Bundle Secret)

  ![image-20220527223650577](https://github.com/justone0127/Quay-3.7-New-Features/blob/main/images/01_secret_info.png?raw=true)

  ```bash
  $ oc get secret -n quay example-registry-quay-config-bundle-jhkm6 -o "jsonpath={$.data['config\.yaml']}" |                                            base64 -d > config.yaml
  ```

- `config.yaml` 수정

  ```bash
  --- 생략 ---
  FEATURE_QUOTA_MANAGEMENT: true
  DEFAULT_SYSTEM_REJECT_QUOTA_BYTES: 102400000
  ```

- configuration 파일을 사용해서 Secret 생성

  ```bash
  $ oc create secret generic -n quay --from-file config.yaml=./config.yaml init-config-bundle-secret
  ```

- 변경된 `config.yaml`로 반영

  콘솔에서 생성한 이미지 레지스트리에서 `configBundleSecret` 부분을 수정하여 반영합니다.

  ![image-20220527224042610](https://github.com/justone0127/Quay-3.7-New-Features/blob/main/images/02_config_update.png)

- `FEATURE_QUOTA_MANAGEMENT` 설정 전

  ![03_before_quota_settings](https://github.com/justone0127/Quay-3.7-New-Features/blob/main/images/03_before_quota_settings.png)

- `FEATURE_QUOTA_MANAGEMENT` 설정 후

  ![04_after_quota_settings](https://github.com/justone0127/Quay-3.7-New-Features/blob/main/images/04_after_quota_settings.png)

- Image Push 테스트

  다음과 같이 레파지토리의 Quto 소비량을 확인할 수 있습니다.

  ![image-20220527230440915](https://github.com/justone0127/Quay-3.7-New-Features/blob/main/images/05_quota_consumed.png)



