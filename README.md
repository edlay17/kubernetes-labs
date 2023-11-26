## Task 4
namespace's limit is 
```
cpu: 2000m
memory: 1500Mi
``` 
php-apache deployment's limit is 
```
cpu: 250m
memory: 250Mi
``` 

worker pod's limit is
```
cpu: 200m
memory: 200Mi
```

CPU: (2000 - 200) / 250 = 7.2 => 7 MaxReplicas

MEMORY: (1500 - 200) / 250 = 5.2 => 5 MaxReplicas

=> result is 5 MaxReplicas

## Task 5
```
ed@space8:~/Dev/study/docker/lab5$ kubectl describe HorizontalPodAutoscaler php-apache-autoscaler -n zad5
Name:                                                  php-apache-autoscaler
Namespace:                                             zad5
Labels:                                                <none>
Annotations:                                           <none>
CreationTimestamp:                                     Sun, 26 Nov 2023 21:00:27 +0100
Reference:                                             Deployment/php-apache
Metrics:                                               ( current / target )
  resource cpu on pods  (as a percentage of request):  <unknown> / 50%
Min replicas:                                          1
Max replicas:                                          3
Deployment pods:                                       1 current / 0 desired
Conditions:
  Type           Status  Reason                   Message
  ----           ------  ------                   -------
  AbleToScale    True    SucceededGetScale        the HPA controller was able to get the target's current scale
  ScalingActive  False   FailedGetResourceMetric  the HPA was unable to compute the replica count: failed to get cpu utilization: unable to get metrics for resource cpu: unable to fetch metrics from resource metrics API: the server could not find the requested resource (get pods.metrics.k8s.io)
Events:
  Type     Reason                        Age   From                       Message
  ----     ------                        ----  ----                       -------
  Warning  FailedGetResourceMetric       2s    horizontal-pod-autoscaler  failed to get cpu utilization: unable to get metrics for resource cpu: unable to fetch metrics from resource metrics API: the server could not find the requested resource (get pods.metrics.k8s.io)
  Warning  FailedComputeMetricsReplicas  2s    horizontal-pod-autoscaler  invalid metrics (1 invalid out of 1), first error is: failed to get cpu resource metric value: failed to get cpu utilization: unable to get metrics for resource cpu: unable to fetch metrics from resource metrics API: the server could not find the requested resource (get pods.metrics.k8s.io)
```

## Task 6


```
ed@space8:~/Dev/study/docker/lab5$ kubectl get pods -n zad5
NAME                          READY   STATUS    RESTARTS   AGE
php-apache-74dccfb695-wbfvl   1/1     Running   0          36s
worker                        1/1     Running   0          96m
```

```
ed@space8:~/Dev/study/docker/lab5$ kubectl describe deploy php-apache -n zad5
Name:                   php-apache
Namespace:              zad5
CreationTimestamp:      Sun, 26 Nov 2023 21:49:33 +0100
Labels:                 <none>
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               run=php-apache
Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  run=php-apache
  Containers:
   php-apache:
    Image:      registry.k8s.io/hpa-example
    Port:       80/TCP
    Host Port:  0/TCP
    Limits:
      cpu:     250m
      memory:  250Mi
    Requests:
      cpu:        150m
      memory:     150Mi
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   php-apache-74dccfb695 (1/1 replicas created)
Events:
  Type    Reason             Age    From                   Message
  ----    ------             ----   ----                   -------
  Normal  ScalingReplicaSet  2m36s  deployment-controller  Scaled up replica set php-apache-74dccfb695 to 1
```

```
ed@space8:~/Dev/study/docker/lab5$ kubectl describe hpa php-apache-autoscaler -n zad5
Name:                                                  php-apache-autoscaler
Namespace:                                             zad5
Labels:                                                <none>
Annotations:                                           <none>
CreationTimestamp:                                     Sun, 26 Nov 2023 21:51:59 +0100
Reference:                                             Deployment/php-apache
Metrics:                                               ( current / target )
  resource cpu on pods  (as a percentage of request):  <unknown> / 50%
Min replicas:                                          1
Max replicas:                                          5
Deployment pods:                                       1 current / 0 desired
Conditions:
  Type           Status  Reason                   Message
  ----           ------  ------                   -------
  AbleToScale    True    SucceededGetScale        the HPA controller was able to get the target's current scale
  ScalingActive  False   FailedGetResourceMetric  the HPA was unable to compute the replica count: failed to get cpu utilization: unable to get metrics for resource cpu: unable to fetch metrics from resource metrics API: the server could not find the requested resource (get pods.metrics.k8s.io)
Events:
  Type     Reason                        Age                From                       Message
  ----     ------                        ----               ----                       -------
  Warning  FailedGetResourceMetric       12s (x6 over 87s)  horizontal-pod-autoscaler  failed to get cpu utilization: unable to get metrics for resource cpu: unable to fetch metrics from resource metrics API: the server could not find the requested resource (get pods.metrics.k8s.io)
  Warning  FailedComputeMetricsReplicas  12s (x6 over 87s)  horizontal-pod-autoscaler  invalid metrics (1 invalid out of 1), first error is: failed to get cpu resource metric value: failed to get cpu utilization: unable to get metrics for resource cpu: unable to fetch metrics from resource metrics API: the server could not find the requested resource (get pods.metrics.k8s.io)

```

```
ed@space8:~/Dev/study/docker/lab5$ kubectl run -i --tty load-generator --rm --image=busybox:1.28 --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://php-apache.zad5; done"
If you don't see a command prompt, try pressing enter.
OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!O
```

```
ed@space8:~/Dev/study/docker/lab5$ kubectl get hpa php-apache-autoscaler -n zad5 --watch
NAME                    REFERENCE               TARGETS         MINPODS   MAXPODS   REPLICAS   AGE
php-apache-autoscaler   Deployment/php-apache   <unknown>/50%   1         5         1          28s
php-apache-autoscaler   Deployment/php-apache   58%/50%         1         5         1          45s
php-apache-autoscaler   Deployment/php-apache   58%/50%         1         5         2          60s
php-apache-autoscaler   Deployment/php-apache   9%/50%          1         5         2          90s
php-apache-autoscaler   Deployment/php-apache   90%/50%         1         5         2          2m30s
php-apache-autoscaler   Deployment/php-apache   90%/50%         1         5         4          2m45s
php-apache-autoscaler   Deployment/php-apache   51%/50%         1         5         4          3m30s
php-apache-autoscaler   Deployment/php-apache   47%/50%         1         5         4          4m30s
php-apache-autoscaler   Deployment/php-apache   47%/50%         1         5         4          5m30s
php-apache-autoscaler   Deployment/php-apache   46%/50%         1         5         4          6m30s
php-apache-autoscaler   Deployment/php-apache   47%/50%         1         5         4          7m30s
php-apache-autoscaler   Deployment/php-apache   47%/50%         1         5         4          9m31s
```

```
ed@space8:~/Dev/study/docker/lab5$ kubectl get deployment php-apache -n zad5
NAME         READY   UP-TO-DATE   AVAILABLE   AGE
php-apache   4/4     4            4           49m
```

## Tasks:
```
1. Utworzyć plik yaml tworzący dla przestrzeni nazw zad5 zestaw ograniczeń na zasoby
(quota) o następujących parametrach:
- maksymalna liczba Pod-ów: 10
- dostępne zasoby CPU: 2 CPU (2000m)
- dostępna ilość pamięci RAM: 1,5Gi

2. Utworzyć plik yaml tworzący Pod-a w przestrzeni nazw zad5 o nazwie worker. Pod ma
bazować na obrazie nginx i mieć następujące ograniczenia na wykorzystywane zasoby:
limits:
memory: 200Mi
cpu: 200m
requests:
memory: 100Mi
cpu: 100m

3. Bazując na przykładzie application/php-apache.yaml z instrukcji do lab4 i/lub
z dokumentacji Kubernetes: hSps://kubernetes.io/docs/tasks/runapplicaTon/horizontal-pod-autoscale-walkthrough/ należy zmodyfikować wskazany
wyżej plik yaml tak by obiekty Deployment i Service utworzone zostały w przestrzeni
nazw zad4. Jednocześnie obiekt Deployment ma mieć następujące ograniczenia na
wykorzystywane zasoby:
limits:
memory: 250Mi
cpu: 250m
requests:
memory: 150Mi
cpu: 150m

4. Należy utworzyć plik yaml definiujący obiekt HorizontalPodAutoscaler, który pozwoli
na autoskalowanie wdrożenia (Deployment) php-apache z zastosowaniem
następujących parametrów:
minReplicas: 1
maxReplicas: ???????
targetCPUUtilizationPercentage: 50
Wartość maxReplicas należy określić samodzielnie, tak by nie przekroczyć parametrów
quoty dla przestrzeni nazw zad5. W sprawozdaniu należy uzasadnić dobór.

5. Należy utworzyć obiekty zdeklarowane w opracowanych plikach yaml. Następnie
potwierdzić ich poprawne uruchomienie za pomocą samodzielnie dobranego
polecenia (poleceń).

6. Ponownie, bazując na przykładach z instrukcji do lab5 i/lub linku podanego w punkcie
3, należy uruchomić aplikację generującą obciążenie dla aplikacji php-apache i tym
samym inicjalizujące proces autoskalowania wdrożenia tej aplikacji. Za pomocą
samodzielnie dobranych poleceń i wyniku ich działania proszę potwierdzić dobór
parametrów z punktu 4. ```