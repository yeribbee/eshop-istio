# istio helm chart



### eshop namespace istio injection

```bash
kubectl label namespaces eshop istio-injection=enabled
```
```bash
kubectl get namespaces -L istio-injection
```

<br>
<br>

### istio diff 무시

- istio manifest

```yaml
(...생략...)
syncPolicy:
  syncOptions:
    - CreateNamespace=true
(입력)
```

- 상기 (입력)란에 아래 코드 입력
- argocd(CD툴)에서는 istio의 ValidatingWebhookConfiguration 및 MutatingWebhookConfiguration의 caBundle값을 다이나믹하게 삭제되는 변화를 감지한다. Auto Pruning이 없는 상황에서 잦은 istio 상기 설정(caBundle, failurePolicy)의 삭제되어야 하는 부분 발생으로 OutofSync가 발생하게 되는 것이다. 이를 무시하기 위한 옵션을 아래와 같이 manifest에 추가해주게 되면 해당 변화에 대해서는 argocd가 감지하지 않는다.

- 참고1. https://argo-cd.readthedocs.io/en/release-1.8/user-guide/diffing/

- 참고2. https://github.com/argoproj/argo-cd/issues/4276


```yaml
ignoreDifferences:
  - group: admissionregistration.k8s.io
    kind: ValidatingWebhookConfiguration
    jsonPointers:
      - /webhooks/0/clientConfig/caBundle
      - /webhooks/0/failurePolicy
  - group: admissionregistration.k8s.io
    kind: MutatingWebhookConfiguration
    jsonPointers:
      - /webhooks/0/clientConfig/caBundle
      - /webhooks/1/clientConfig/caBundle
      - /webhooks/2/clientConfig/caBundle
      - /webhooks/3/clientConfig/caBundle
```

<br>
<br>

### istio helm setting

```bash
global:
  eiparn: <<EIP ARN1>>,<<EIP ARN2>>
  sslcert: <<ACM ARN>>
```

<br>
<br>

### istio 배포 수행 후 Route53 Record를 설정한다.

- eshop.<<개인도메인>>.click     
> alias switch on    
> Alias to Network Load Balancer 선택    
> Oregon(us-west-2) 선택    
> Network LoadBalancer 선택    

<br>
<br>