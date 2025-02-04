[![Flask Change Microservice Test](https://github.com/noahgift/flask-change-microservice/actions/workflows/main.yml/badge.svg)](https://github.com/noahgift/flask-change-microservice/actions/workflows/main.yml)

# flask-change-microservice
Small Flask Microservice that makes change

*Coursera Lab:  duke-coursera-ccb-lab2*

![coursera-lab](https://user-images.githubusercontent.com/58792/108137449-df0e0300-7089-11eb-8b11-74f478b71d11.png)

## Instructions
Deploy a Kubernetes Python Flask App
In this project, your goal is to run a Kubernetes Flask Microservice that returns the correct amount of change locally on your desktop machine.  This project uses [a simple Flask app](https://github.com/noahgift/flask-change-microservice) that returns correct change as the base project and converts it to Kubernetes.


Assets in Repo
`Makefile`:  [Builds project](https://github.com/noahgift/kubernetes-hello-world-python-flask/blob/main/Makefile)

`Dockerfile`:  [Container configuration](https://github.com/noahgift/kubernetes-hello-world-python-flask/blob/main/Dockerfile)

`app.py`:  [Flask app](https://github.com/noahgift/kubernetes-hello-world-python-flask/blob/main/app.py)

`kube-hello-change.yaml`: [Kubernetes YAML Config](https://github.com/noahgift/kubernetes-hello-world-python-flask/blob/main/kube-hello-change.yaml)

Get Started
Create Python virtual environment `python3 -m venv ~/.kube-hello && source ~/.kube-hello/bin/activate`

Run `make all` to install python libraries, lint project, including `Dockerfile` and run tests

Build and Run Docker Container
[Install Docker Desktop](https://www.docker.com/products/docker-desktop)

To build the image locally do the following.

`docker build -t flask-change:latest .` or run `make build` which has the same command.

To verify container run `docker image ls`

To run do the following:  `docker run -p 8080:8080 flask-change` or run `make run` which has the same command

In a separate terminal invoke the web service via curl, or run `make invoke` which has the same command

`curl http://127.0.0.1:8080/change/1/34

[
  {
    "5": "quarters"
  }, 
  {
    "1": "nickels"
  }, 
  {
    "4": "pennies"
  }
]`

Stop the running docker container by using `control-c` command

Running Kubernetes Locally
Verify Kubernetes is working via docker-desktop context

`(.kube-hello) ➜  kubernetes-hello-world-python-flask git:(main) kubectl get nodes
NAME             STATUS   ROLES    AGE   VERSION
docker-desktop   Ready    master   30d   v1.19.3`

Run the application in Kubernetes using the following command which tells Kubernetes to setup the load balanced service and run it:

`kubectl apply -f kube-hello-change.yaml` or run `make run-kube` which has the same command

You can see from the config file that a load-balancer along with three nodes is the configured application.

`apiVersion: v1kind: Servicemetadata:
  name: hello-flask-change-servicespec:
  selector:
    app: hello-python
  ports:
  - protocol: "TCP"
    port: 8080
    targetPort: 8080
  type: LoadBalancer

---
apiVersion: apps/v1kind: Deploymentmetadata:
  name: hello-pythonspec:
  selector:
    matchLabels:
      app: hello-python
  replicas: 3
  template:
    metadata:
      labels:
        app: hello-python
    spec:
      containers:
      - name: flask-change
        image: flask-change:latest
        imagePullPolicy: Never
        ports:
        - containerPort: 8080`

Verify the container is running

`kubectl get pods`

Here is the output:

NAME                            READY   STATUS    RESTARTS   AGE
flask-change-7b7d7f467b-26htf   1/1     Running   0          8s
flask-change-7b7d7f467b-fh6df   1/1     Running   0          7s
flask-change-7b7d7f467b-fpsxr   1/1     Running   0          6s

Describe the load balanced service:

`kubectl describe services hello-python-service`

You should see output similar to this:

Name:                     hello-python-service
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=hello-python
Type:                     LoadBalancer
IP Families:              <none>
IP:                       10.101.140.123
IPs:                      <none>
LoadBalancer Ingress:     localhost
Port:                     <unset>  8080/TCP
TargetPort:               8080/TCP
NodePort:                 <unset>  30301/TCP
Endpoints:                10.1.0.27:8080,10.1.0.28:8080,10.1.0.29:8080
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>

Invoke the endpoint to curl it:

`make invoke`

`curl http://127.0.0.1:8080/change/1/34`
`[
  {
    "5": "quarters"
  }, 
  {
    "1": "nickels"
  }, 
  {
    "4": "pennies"
  }
]`

Your Tasks
* Run the project locally by forking it and cloning your version.
* Invoke your new endpoint and verify it works.
* Create a Demo project in GitHub with a README.md that describes what you did.
* Create a Demo Video and reference it in your GitHub Project.

To cleanup the deployment do the following: `kubectl delete deployment hello-python`

References:

[Azure Kubernetes deployment strategy](https://azure.microsoft.com/en-us/overview/kubernetes-deployment-strategy/)

[Service Cluster Config YAML file](https://kubernetes.io/docs/tasks/access-application-cluster/service-access-application-cluster/)

[Kubernetes.io Hello World](https://kubernetes.io/blog/2019/07/23/get-started-with-kubernetes-using-python/)

## Invoke Endpoint

* Create virtualenv and source it: `python3 -m venv ~/.fcm && source ~/.venv/bin/fcm`
* Install and Test:  `make all`
* Run it:  `python app.py`
* Invoke it.  Options include curl, Postman, httpie.  These methods are documented below


### Curl

`curl http://127.0.0.1:8080/change/1/34`

```bash
[
  {
    "5": "quarters"
  }, 
  {
    "1": "nickels"
  }, 
  {
    "4": "pennies"
  }
]
```
### httpie

[Installation of httpie](https://httpie.io/docs#installation)

`http 127.0.0.1:8080/change/1/34`

```bash
HTTP/1.0 200 OK
Content-Length: 90
Content-Type: application/json
Date: Tue, 16 Mar 2021 16:49:11 GMT
Server: Werkzeug/1.0.1 Python/3.9.0

[
    {
        "5": "quarters"
    },
    {
        "1": "nickels"
    },
    {
        "4": "pennies"
    }
]
```


### Postman

[Install Postman](https://www.postman.com)
![postman](https://user-images.githubusercontent.com/58792/111342614-00461d00-8651-11eb-8433-d7d91d3e48b4.png)

### Requests

The [Python requests library](https://requests.readthedocs.io/en/latest/user/quickstart/) allows you to invoke a request as a "one-liner" or a script.

`python -c "import requests;r=requests.get('http://127.0.0.1:8080/change/1/34');print(r.json())"`

Result:

`[{'5': 'quarters'}, {'1': 'nickels'}, {'4': 'pennies'}]`

## Loadtest with Locust

* [Install Locust](https://github.com/locustio/locust)
* Create a `locustfile.py`
* Run loadtests
![Screen Shot 2021-03-16 at 3 02 59 PM](https://user-images.githubusercontent.com/58792/111367175-d7328600-866a-11eb-9a4d-3429710593ea.png)
![Screen Shot 2021-03-16 at 3 02 35 PM](https://user-images.githubusercontent.com/58792/111367176-d7328600-866a-11eb-9856-928d42e65a9a.png)
![Screen Shot 2021-03-16 at 3 01 22 PM](https://user-images.githubusercontent.com/58792/111367178-d7cb1c80-866a-11eb-8c29-6440a6179544.png)

## Create a Microservice

Open a terminal in Visual Studio Code.

cd into the projects directory:  `cd /home/coder/project`

install virtualenv:  ``python3 -m pip install virtualenv`

create a virtualenv: `/home/coder/.local/bin/virtualenv VENV`

source the virtualenv (activate it):  `source VENV/bin/activate`

cd into flask project:  `cd /home/coder/project/flask-change-microservice`

install software:  `make install`

run flask:  `python app.py`

In the Browser Preview menu type in 127.0.0.1:5000/change/1/34 This will make a web request to your Microservice.  Try a few other combinations and notice how the URL parameters capture the input and then pass the input to a function.

test app: `make test`

fix test


