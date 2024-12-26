1. Prepare Kubernetes Cluster
Ensure you have a functional Kubernetes cluster with kubectl configured to interact with the cluster.

2. Create a Namespace
Create a dedicated namespace for Nagios:

bash
Copy code
kubectl create namespace nagios
3. Create a Persistent Volume and Persistent Volume Claim
Nagios needs storage to save its configuration and monitoring data. Create a persistent volume (PV) and a persistent volume claim (PVC):

Persistent Volume YAML (nagios-pv.yaml):

yaml
Copy code
apiVersion: v1
kind: PersistentVolume
metadata:
  name: nagios-pv
  namespace: nagios
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /mnt/data/nagios
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: nagios-pvc
  namespace: nagios
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
Apply the configuration:

bash
Copy code
kubectl apply -f nagios-pv.yaml
4. Deploy Nagios
Deploy Nagios using a Deployment and Service.

Nagios Deployment YAML (nagios-deployment.yaml):

yaml
Copy code
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nagios
  namespace: nagios
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nagios
  template:
    metadata:
      labels:
        app: nagios
    spec:
      containers:
        - name: nagios
          image: jasonrivers/nagios:latest
          ports:
            - containerPort: 80
          volumeMounts:
            - name: nagios-data
              mountPath: /opt/nagios
      volumes:
        - name: nagios-data
          persistentVolumeClaim:
            claimName: nagios-pvc
---
apiVersion: v1
kind: Service
metadata:
  name: nagios
  namespace: nagios
spec:
  selector:
    app: nagios
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: NodePort
Apply the configuration:

bash
Copy code
kubectl apply -f nagios-deployment.yaml
5. Access Nagios
Check the assigned NodePort for Nagios:

bash
Copy code
kubectl get svc -n nagios
Access Nagios using the <node-ip>:<node-port> in your browser.

6. Configure Nagios
Login Credentials: The default credentials for the container are:

Username: nagiosadmin
Password: nagios
Add Monitoring Configuration: Modify the configuration files (nagios.cfg, contacts.cfg, and hosts.cfg) located in the mounted volume /mnt/data/nagios on the host machine.

7. Verify Nagios
Ensure the Nagios pod is running:

bash
Copy code
kubectl get pods -n nagios
Access the web interface and verify monitoring targets are being displayed.

Optional Enhancements
Ingress Controller: Configure an ingress for better access.
Scaling: Adjust replicas for high availability if needed.
External Database: Integrate with an external database for enterprise monitoring.
This setup provides a basic Nagios Core instance running in Kubernetes, which can be further customized for your monitoring needs.
