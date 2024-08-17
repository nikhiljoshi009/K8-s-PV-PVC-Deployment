# K8-s-PV-PVC-Deployment 
1. Create one EBS volume using AWS Console
2. Create PV.yaml and Now, copy the volume ID and paste it into the PV YML file(12th line) 
3. In this snippet, we have created a Persistent Volume where the EBS volume is attached and 
created 1GB of capacity.
sodo vi pv.yml

apiVersion: v1
kind: PersistentVolume
metadata:
  name: myebsvol
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Recycle
  awsElasticBlockStore:
    volumeID: vol-071473af23e55a92f
    fsType: ext4

kubectl apply -f pv.yaml
kubectl get pv

4. PVC YML file - PVC.yml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: myebsvolclaim
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi

kubectl apply -f pvc.yml
kubectl get pvc

 we have created a Persistent Volume Claim in which PVC requests PV to get 
1GB, and as you can see the volume is bound successful. 

5. Deployment YML file - Deployment.yml
   sudo vi deploy.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: pvdeploy
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mypv
  template:
    metadata:
      labels:
        app: mypv
    spec:
      containers:
      - name: shell
        image: centos
        command: ["/bin/bash", "-c", "sleep 10000"]
        volumeMounts:
        - name: mypd
          mountPath: /tmp/persistent
      volumes:
      - name: mypd
        persistentVolumeClaim:
          claimName: myebsvolclaim


we have created a deployment for PV and PVC demonstration.

kubectl apply -f deployment.yml
kubectl get deployment
kubectl get rs   #  kubectl get rs is a quick way to check the status of your ReplicaSets in Kubernetes.
kubectl get pods
