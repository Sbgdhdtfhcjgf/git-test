```yml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-data
spec:
  storageClassName: "local"
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/home/ubuntu/data"
```

````yml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-data
spec:
  storageClassName: "local"
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
````

```yml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: pvc-pod
  name: pvc-pod
spec:
  containers:
  - image: busybox:latest
    name: pvc-pod
    args:
      - sleep
      - "3600"
    volumeMounts:
    - name: data
      mountPath: "/data"
  volumes:
  - name: data
    persistentVolumeClaim:
      claimName: pvc-data
```

```yml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    run: nginx
spec:
  containers:
  - name: nginx
    image: nginx:latest
    readinessProbe:
      httpGet:
        path: /
        port: 80
      initialDelaySeconds: 5
      periodSeconds: 5
    livenessProbe:
      httpGet:
        path: /
        port: 80
```

```bash
cat << EOF > system.conf
planet=blue
moon=white
EOF

kubectl create configmap space-system --from-file=system.conf
```

````yml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: confvolume
  name: confvolume
spec:
  containers:
  - image: busybox:latest
    name: confvolume
    args:
      - sleep
      - "3600"
    volumeMounts:
      - name: system
        mountPath: /etc/system.conf
        subPath: system.conf
    resources: {}
  volumes:
  - name: system
    configMap:
      name: space-system
````

```yml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: secretvolume
  name: secretvolume
spec:
  containers:
  - image: busybox:latest
    name: secretvolume
    args:
      - sleep
      - "3600"
    volumeMounts:
      - name: admincred
        mountPath: /etc/admin-cred
        readOnly: true
  volumes:
  - name: admincred
    secret:
      secretName: admin-cred
```

```bash
      - name: PLANET
        valueFrom:
          configMapKeyRef:
            name: space
            key: planet
      - name: USERNAME
        valueFrom:
          secretKeyRef:
            name: admin-cred
            key: username
```

安装top命令

```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/high-availability-1.21+.yaml
```

```bash
kubectl create deployment scalable --image=nginx:latest
kubectl scale deployment scalable --replicas=4
kubectl create deployment autoscalable --image=nginx:latest
kubectl autoscale deployment autoscalable --min=2 --max=6 --cpu-percent=70
kubectl get hpa
```















