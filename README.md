For the article of this repo, please refer to: https://www.swtestacademy.com/deploy-full-stack-application-in-kubernetes/

## LAB INSTRUCTIONS


1.Database image

Navigate to postgress folder and type:

```bash
docker build -t fullstackappdb .
docker save fullstackappdb:latest > db.tar
sudo chown root:root db.tar
sudo mv db.tar /var/lib/rancher/k3s/agent/images
```
These commands build the PostgreSQL image, export it as a .tar file, and move it into the K3s image folder so it can be used by the cluster.

2.Backend image

Common Issue:
After running the backend container, you may encounter the following error:

```bash
Traceback (most recent call last): File "/app/main.py", line 13, in <module> db.init_app(app) File "/usr/local/lib/python3.7/site-packages/flask_sqlalchemy/extension.py", line 256, in init_app "A 'SQLAlchemy' instance has already been registered on this Flask app." RuntimeError: A 'SQLAlchemy' instance has already been registered on this Flask app. Import and use that instance instead.

```
This means the same SQLAlchemy instance was initialized twice on the same Flask app.
To fix this, go to your backend/main.py file and remove or comment out the line:db.init_app(app)
This line is not needed if the db object is already initialized with the app earlier.

Navigate to backend folder and type:

```bash
docker build -t backend .
docker save backend:latest > backend.tar
sudo mv backend.tar /var/lib/rancher/k3s/agent/images
```
These commands build and save the backend (Flask + Python) image, then make it available to the Kubernetes node.

3.Frontend image

If update frontend message
Open the frontend source code (frontend/src/App.js),
change the message from:

"Hi! I am a full-stack app!"

to

"Welcome to my full stack app!"

Then rebuild and reload the new image

Navigate to the frontend folder and type:

```bash
docker build -t frontend .
docker save frontend:latest > frontend.tar
sudo chown root:root frontend.tar
sudo mv frontend.tar /var/lib/rancher/k3s/agent/images
```
These commands build the frontend (React) image, export it as a .tar file, and move it to the K3s image folder.


4.Deploy Kubernetes

Navigate to the project root folder.

Apply the Kubernetes deployment file:

```bash
sudo kubectl get pods
```
This command deploys all components — PostgreSQL database, Flask backend, and React frontend — using a single deployment file (kubernetes-deploy.yml).
In this file, multiple replicas have been defined for the backend and frontend pods to make the application more resilient and fault-tolerant.The deatils are as followed.

```bash
metadata:
  name: postgres
  labels:
    app: database
spec:
  replicas: 1
```
```bash
metadata:
  name: backend
  labels:
    app: backend
spec:
  replicas: 2      
```
```bash
metadata:
  name: frontend
  labels:
    app: frontend
spec:
  replicas: 3 
```

Example output:

```bash
backend-79d6c5768-pqhms     1/1     Running
backend-79d6c5768-tstw4     1/1     Running
frontend-77599685cc-5b9pv   1/1     Running
frontend-77599685cc-bc5mt   1/1     Running
frontend-77599685cc-sc2xb   1/1     Running
postgres-7958f44b89-f4jn7   1/1     Running

```

5.Access the Application
Finally, open your browser and visit the following URL:


```bash
http://172.16.141.138:30000
```
You should see the web page displaying the message:

Welcome to my full stack app!




