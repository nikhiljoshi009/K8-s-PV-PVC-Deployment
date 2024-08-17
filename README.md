# K8-s-PV-PVC-Deployment

1. **Create an EBS Volume:**
   - Use the AWS Console to create an EBS volume.

2. **Create PersistentVolume (PV):**
   - Create a `pv.yml` file and copy the volume ID into the `awsElasticBlockStore` section (line 12).
   - Here's an example `pv.yml` file:

     ```yaml
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
     ```

   - Apply the PV configuration:
     ```bash
     kubectl apply -f pv.yml
     ```
   - Verify the PersistentVolume:
     ```bash
     kubectl get pv
     ```

   - **Explanation:** This snippet creates a Persistent Volume where the EBS volume is attached with a capacity of 1GB.

3. **Create PersistentVolumeClaim (PVC):**
   - Create a `pvc.yml` file with the following content:

     ```yaml
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
     ```

   - Apply the PVC configuration:
     ```bash
     kubectl apply -f pvc.yml
     ```
   - Verify the PersistentVolumeClaim:
     ```bash
     kubectl get pvc
     ```

   - **Explanation:** This creates a Persistent Volume Claim that requests 1GB of storage from the PersistentVolume, and binds successfully.

4. **Create Deployment:**
   - Create a `deployment.yml` file with the following content:

     ```yaml
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
     ```

   - Apply the Deployment configuration:
     ```bash
     kubectl apply -f deployment.yml
     ```
   - Verify the Deployment:
     ```bash
     kubectl get deployment
     ```
   - Check ReplicaSets:
     ```bash
     kubectl get rs
     ```
     _Note: `kubectl get rs` is a quick way to check the status of your ReplicaSets in Kubernetes._
   - Verify Pods:
     ```bash
     kubectl get pods
     ```

   - **Explanation:** This creates a Deployment for the PV and PVC demonstration.

