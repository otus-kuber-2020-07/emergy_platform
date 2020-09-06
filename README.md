# emergy_platform
emergy Platform repository

kubernetes-networks
- побавил проверки в kubernetes-intro\web-pod.yaml

Почему следующая конфигурация валидна, но не имеет смысла?
```
livenessProbe:
  exec:
    command:
      - 'sh'
      - '-c'
      - 'ps aux | grep my_web_server_process'
```
Потому что, если у нас запущен процесс веб сервера, это ещё не значет, что он запущен на необходимом порту и отдаёт то, что должен. Такая проверка имеет смысл, если у нас нет возможности запросить у приложения его статус.

- создал kubernetes-networks\web-deploy.yaml, сделал template на основе kubernetes-intro\web-pod.yaml
- удалил pod созданый до этого
- исправил readinessProbe, увеличил количество реплик, примерил посмотрел, поигрался с strategy
- создал kubernetes-networks\web-svc-cip.yaml, посмотрел правила iptables
- включил IPVS, посморел листинг VIP'ов, интерфейс kube-ipvs0, таблицы ipset
- создал kubernetes-networks\metallb-config.yaml и применил
- создал kubernetes-networks\web-svc-lb.yaml и применил
- добавил маршрут (ОС Windows) `route add 172.17.255.0 mask 255.255.255.0 192.168.99.102` - страничка открылась
- с заданием DNS через MetalLB - ничего не вышло
- установил ingress-nginx
- создал kubernetes-networks\nginx-lb.yaml, постучался по адресу 172.17.255.2
- создал kubernetes-networks\web-svc-headless.yaml, применил, убедился, что IP не назначен
- создал kubernetes-networks\web-ingress.yaml, и применил
```
k describe ingress/web
```
вижу адрес виртуалки minikube и ошибку (<error: endpoints "default-http-backend" not found>
```
Warning: extensions/v1beta1 Ingress is deprecated in v1.14+, unavailable in v1.22+; use networking.k8s.io/v1 Ingress
Name:             web
Namespace:        default
Address:          192.168.99.102
Default backend:  default-http-backend:80 (<error: endpoints "default-http-backend" not found>)
Rules:
  Host        Path  Backends
  ----        ----  --------
  *
              /web   web-svc:8000   172.17.0.5:8000,172.17.0.7:8000,172.17.0.8:8000)
Annotations:  nginx.ingress.kubernetes.io/rewrite-target: /
Events:
  Type    Reason  Age   From                      Message
  ----    ------  ----  ----                      -------
  Normal  CREATE  34s   nginx-ingress-controller  Ingress default/web
  Normal  UPDATE  24s   nginx-ingress-controller  Ingress default/web
  ```
  тем не менее, адрес http://172.17.255.2/web/index.html - открылся в браузере