# kub
2. Create a Namespace
kubectl create namespace nagios

3. Create a Persistent Volume and Persistent Volume Claim
kubectl apply -f nagios-pv.yaml

4. Deploy Nagios
kubectl apply -f nagios-deployment.yaml

5. Access Nagios
kubectl get svc -n nagios


6. Configure Nagios
Login Credentials: The default credentials for the container are:

Username: nagiosadmin
Password: nagios

Add Monitoring Configuration: Modify the configuration files (nagios.cfg, contacts.cfg, and hosts.cfg) located in the mounted volume /mnt/data/nagios on the host machine.


7. Verify Nagios
kubectl get pods -n nagios



