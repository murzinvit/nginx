### nginx

Конфигурационный файл: `nginx.conf` </br>
Расположение файла конфигурации: `/usr/local/nginx/conf`, `/etc/nginx`, `/usr/local/etc/nginx` </br>

Перезагрузка конфигурации: `nginx -s reload` </br>
Завершение через nginx c ожидание завершения обслуживания: `nginx -s quit` </br>
Завершение через ОС c ожидание завершения обслуживания: `kill -s QUIT 1628` </br>

---

```
kubectl apply -f - <<EOF
apiVersion: v1
kind: PersistentVolume
metadata:
  name: all-nfs-volume
spec:
  capacity:
    storage: 2Gi
  accessModes:
    - ReadWriteMany
  persistentVolumeReclaimPolicy: Retain
  nfs:
    server: 10.10.1.226
    path: /var/nfs
EOF
``` 
```
kubectl apply -f - <<EOF
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: nginx-storage
spec:
  accessModes:
  - ReadWriteMany
  resources:
    requests:
      storage: 1Gi
EOF
```


```
kubectl apply -f - <<EOF
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    name: nginx-pod-lbl
spec:
  containers:
  - name: nginx
    image: nginx:latest
    imagePullPolicy: IfNotPresent
    ports:
    - name: nginx
      containerPort: 80
      protocol: TCP
    volumeMounts:
    - name: nginx-stor
      mountPath: /mnt
  volumes:
  - name: nginx-stor
    persistentVolumeClaim:
       claimName: nginx-storage
EOF
```
```
kubectl apply -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
  labels:
    name: nginx-service-lbl
spec:
  ports:
  - port: 80
    targetPort: 80
  selector:
    app: nginx-pod-lbl
  type: NodePort
EOF
```












---
work notes: </br>
https://nginx.org/ru/docs/beginners_guide.html </br>

All: </br>
https://serveradmin.ru/ustanovka-i-nastroyka-zabbix-3-4-na-centos-7/ </br>
https://serveradmin.ru/ustanovka-i-nastroyka-gitlab/ </br>
https://serveradmin.ru/monitoring-proizvoditelnosti-bekenda-s-pomoshhyu-elk-stack/ </br>
https://serveradmin.ru/gitlab-container-registry-za-nginx-reverse-proxy/ </br>
https://serveradmin.ru/ci-cd-proekta-na-wordpress/ </br>
https://habr.com/ru/post/174467/ </br>
https://habr.com/ru/company/domclick/blog/577964/ </br>
https://abc-server.com/ru/blog/administration/nginx/ </br>
