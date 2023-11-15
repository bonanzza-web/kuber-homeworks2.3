# Домашнее задание к занятию «Конфигурация приложений»

### Цель задания

В тестовой среде Kubernetes необходимо создать конфигурацию и продемонстрировать работу приложения.

------

### Чеклист готовности к домашнему заданию

1. Установленное K8s-решение (например, MicroK8s).
2. Установленный локальный kubectl.
3. Редактор YAML-файлов с подключённым GitHub-репозиторием.

------

### Инструменты и дополнительные материалы, которые пригодятся для выполнения задания

1. [Описание](https://kubernetes.io/docs/concepts/configuration/secret/) Secret.
2. [Описание](https://kubernetes.io/docs/concepts/configuration/configmap/) ConfigMap.
3. [Описание](https://github.com/wbitt/Network-MultiTool) Multitool.

------

### Задание 1. Создать Deployment приложения и решить возникшую проблему с помощью ConfigMap. Добавить веб-страницу

1. Создать Deployment приложения, состоящего из контейнеров nginx и multitool.
2. Решить возникшую проблему с помощью ConfigMap.
3. Продемонстрировать, что pod стартовал и оба конейнера работают.
4. Сделать простую веб-страницу и подключить её к Nginx с помощью ConfigMap. Подключить Service и показать вывод curl или в браузере.
5. Предоставить манифесты, а также скриншоты или вывод необходимых команд.

------

### Ответы:    

```
ubuntu@server-kube:~$ micro apply -f deploy.yaml
deployment.apps/nginx-mult created
ubuntu@server-kube:~$ micro get po -w
NAME                          READY   STATUS    RESTARTS   AGE
nginx-mult-69d845bf57-rlwqc   2/2     Running   0          6s
nginx-mult-69d845bf57-5ldxj   2/2     Running   0          6s
^Cubuntu@server-kube:~$ ^C
ubuntu@server-kube:~$ micro describe po nginx-mult-69d845bf57-rlwqc
Name:             nginx-mult-69d845bf57-rlwqc
Namespace:        default
Priority:         0
Service Account:  default
Node:             server-kube/10.0.3.11
Start Time:       Wed, 15 Nov 2023 12:43:17 +0000
Labels:           app=nginx-mult
                  pod-template-hash=69d845bf57
Annotations:      cni.projectcalico.org/containerID: cda7bc202be628f225b8b150c29934f301d9078c430c0ca3dee06c4cefad5d21
                  cni.projectcalico.org/podIP: 10.1.55.217/32
                  cni.projectcalico.org/podIPs: 10.1.55.217/32
Status:           Running
IP:               10.1.55.217
IPs:
  IP:           10.1.55.217
Controlled By:  ReplicaSet/nginx-mult-69d845bf57
Containers:
  nginx:
    Container ID:   containerd://70b4ccf24a0b53202b76756e07962a20b3a732dc2ada8244b8ee76051412f0b9
    Image:          nginx:latest
    Image ID:       docker.io/library/nginx@sha256:86e53c4c16a6a276b204b0fd3a8143d86547c967dc8258b3d47c3a21bb68d3c6
    Port:           8080/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Wed, 15 Nov 2023 12:43:19 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /etc/nginx/nginx.conf from nginx-conf-vol (rw,path="nginx.conf")
      /usr/share/nginx/html/index.html from nginx-page (rw,path="index.html")
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-lfjql (ro)
  multitools:
    Container ID:   containerd://e69a9b9f061daee39430a5d45421b8eebd9e16d1590e2fd802f0c7177600c011
    Image:          wbitt/network-multitool
    Image ID:       docker.io/wbitt/network-multitool@sha256:d1137e87af76ee15cd0b3d4c7e2fcd111ffbd510ccd0af076fc98dddfc50a735
    Port:           8085/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Wed, 15 Nov 2023 12:43:21 +0000
    Ready:          True
    Restart Count:  0
    Environment:
      HTTP_PORT:  <set to the key 'multiport' of config map 'nginx-config'>  Optional: false
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-lfjql (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  nginx-conf-vol:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      nginx-config
    Optional:  false
  nginx-page:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      nginx-page
    Optional:  false
  kube-api-access-lfjql:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  115s  default-scheduler  Successfully assigned default/nginx-mult-69d845bf57-rlwqc to server-kube
  Normal  Pulling    114s  kubelet            Pulling image "nginx:latest"
  Normal  Pulled     113s  kubelet            Successfully pulled image "nginx:latest" in 1.189s (1.189s including waiting)
  Normal  Created    113s  kubelet            Created container nginx
  Normal  Started    113s  kubelet            Started container nginx
  Normal  Pulling    113s  kubelet            Pulling image "wbitt/network-multitool"
  Normal  Pulled     112s  kubelet            Successfully pulled image "wbitt/network-multitool" in 1.028s (1.028s including waiting)
  Normal  Created    111s  kubelet            Created container multitools
  Normal  Started    111s  kubelet            Started container multitools
ubuntu@server-kube:~$ micro get svc -o wide
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE    SELECTOR
kubernetes   ClusterIP   10.152.183.1     <none>        443/TCP        2d     <none>
nginx-svc    NodePort    10.152.183.193   <none>        80:30698/TCP   157m   app=nginx-mult
ubuntu@server-kube:~$ curl localhost:30698
<!DOCTYPE html>
<html lang="ru">
  <body>
    <header>
    <div>
    <h1 style="">Hello netology!!!</h1>
    <h2>It's my k8s homework (configuration)</h2>
    <h3>I think it's good job</h3>
    </div>
    <style type="text/css">
      h1, h2, h3 {
      width: 100%;
      text-align: center;
      color: #333366;
      }
      div {
        width: 100%;
      height: 100%;
      margin-top: 300px;
      }
  </style>
    </header>
  </body>
</html>


```

------

### Задание 2. Создать приложение с вашей веб-страницей, доступной по HTTPS 

1. Создать Deployment приложения, состоящего из Nginx.
2. Создать собственную веб-страницу и подключить её как ConfigMap к приложению.
3. Выпустить самоподписной сертификат SSL. Создать Secret для использования сертификата.
4. Создать Ingress и необходимый Service, подключить к нему SSL в вид. Продемонстировать доступ к приложению по HTTPS. 
4. Предоставить манифесты, а также скриншоты или вывод необходимых команд.

------

### Ответы:    

```
ubuntu@server-kube:~$ openssl req -x509 -days 365 -newkey rsa:4096 -sha256 -nodes -keyout tlskey.key -out tls-sert.crt -subj "/CN=nginx-ubunta.ru"
...+....+...+..+....+......+........+...+....+...+...........+.......+...+.....+.......+.....+...+.+......+...+.....+......+.+............+..+....+.....+.+........+.+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*....+......+.....+....+..............+....+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*......+...+..+.+........+....+......+...........+.+..+...+............+.+...............+.........+......+......+.....+..........+.....+.........+..........+...........+.+.....+.......+...........+.........+............+.+..+......+......+.+..+......+.....................+....+.........+......+..+....+.........+..+....+......+.....+..............................+.........+.....................+.......+..+....+......+...+..+................+..............+...+......+..........+.....+......+.+..+...+....+...+..+.+...............+..............+...+..........+...+...+..+.........+..................+.+.....+.........+...+..........+...+............+...+......+.....+.........+.+...+...........+.+...+...........+.+......+..+..........+..+...+..........+...+........+......+...+............+.....................+...................+.....+...+.+.........+..+...................+............+.....+....+...+......+.........+...+.....+...+.+.....+.........+.+......+..+.+..+.......+...+..+..........+..+.....................+.............+.....+.+...+.....+...............+.+...........+.+...............+..+.............+.....+...+............+....+.........+.....+...............+....+...+...+............+.....+....+...+..+...+...+...+.+..............+......+...+..........+........+...+....+...............+.....+......+.+......+............+........+.......+....................+.......+........+................+..+....+...+..+...+.......+.....+....+.....+......+.+.........+..................+......+................................+...+.......+..+.+.....+............+...+......+.+.....+....+...+........+............+.+......+...........+..........+............+......+......+..............+.+.....+......+.........+......+.......+...+...+...........+......+............+............+.+.................+.......+......+........+......+..................+.+..+....+...+..+...+.+..+...+.+...+............+......+........+......+...+.+...........+.....................+.......+.....+...+............+.+.....+....+............+...+...+........+...............+......+.+.....+...+.+...+...+...+.............................+................+.....+.+.........+..........................+...+......+..................................+........+...+.......+............+..................+.....+...+....+.....+..........+.....................+......+...+..+....+.....+..........+.........+......+.....+......+...+...............+.............+........+......+..........+...+..+.............+......+..............+......+.........+.+.........+..+.........+....+..+...............+.+..............+................+.........+........+............+.+...+.....+...................+...+............+.......................+....+........+............+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
........+....+.....+...+......+......+.........+......+......+...+......+....+.........+......+......+...........+..........+..+.+..+...+.+.....+................+.....+...+....+..+.........+....+...+........+......+....+.........+..+...+..........+........+...+......+.+...+.................+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*...........+...+.+.....+............+.......+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*................+........+...+.......+...+..+....+.....+...............+...+.+.....+....+..+.............+...+........+....+...+...+......+...+..+.........+.......+...+..............+.+...........+.+...+......+....................+.+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
-----
ubuntu@server-kube:~$ micro create secret tls nginx-tls --cert=tls-sert.crt --key=tlskey.key
secret/nginx-tls created
ubuntu@server-kube:~$ micro get secret
NAME        TYPE                DATA   AGE
nginx-tls   kubernetes.io/tls   2      28s

```

------

### Правила приёма работы

1. Домашняя работа оформляется в своём GitHub-репозитории в файле README.md. Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.
2. Файл README.md должен содержать скриншоты вывода необходимых команд `kubectl`, а также скриншоты результатов.
3. Репозиторий должен содержать тексты манифестов или ссылки на них в файле README.md.

------