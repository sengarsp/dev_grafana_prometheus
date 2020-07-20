# Deploying Granafa & Prometheus Over Kubernetes (Persistent Volume)
!!!! Hello Everyone !!!!

Here I am sharing my small project (task ) .which is Deploying Grafana & prometheus Over Kubernetes (Persistent Volume ).
# Problem :- 
ntegrate Prometheus and Grafana and perform in following way:

1. Deploy them as pods on top of Kubernetes by creating resources Deployment, Replica Set, Pods or Services.

2. And make their data to be remain persistent.

3. And both of them should be exposed to outside world.

As, we all know when we launch pod in Kubernetes (K8’s) , docker or any other container technology. It work fine but the problem is data loss. By data loss, I mean if our pod stop working and after that when i relaunched it again then it starts from zero, all the setting or configuration i have done everything lost. So solution for this problem , we need to use Persistent Volumes (PV), Persistent Volumes Claims ,Config Map.
# Prometheus-
Prometheus is a free software application used for event monitoring and alerting. It records real-time metrics in a time series database built using a HTTP pull model, with flexible queries and real-time alerting.
# Grafana- 
Grafana is open source visualization and analytics software. It allows you to query, visualize, alert on, and explore your metrics no matter where they are stored. In plain English, it provides you with tools to turn your time-series database (TSDB) data into beautiful graphs and visualizations.
# Persistent Volumes(PV) - 
A PersistentVolume (PV) is a piece of storage in the cluster that has been provisioned by an administrator or dynamically provisioned using Storage Classes.

Persistent Volumes Claim(PVC)- as named, is a request for storage by a user.
# Config Map-
 A Config Map is an API object used to store non-confidential data in key-value pairs. Pods can consume Config Maps as environment variables, command-line arguments, or as configuration files in a volume.
 # Solution :-
   # Step 1:- Here We need to make a deployment of Grafana.
      # Grafana:-
        file is avaible in devops task 5 Folder file name (grafana.yml)
       # Explanation:-
       Here U need to expose the port i,e 3000 , because Grafana runs on port 3000, in k8s to expose the port we need to use services i,e NodePort, for making data permanent here we use kind PersistentVolumeClaim , and gave access ReadWriteOnce, After that we have to make deployment of grafana in k8s kind is Deployment , spec for specification , gave image name i,e(vimal13/grafana) , continerport (3000), for making data permanent here we have to mount the dir i,e (var/lib/grafana) in the PersistentVolumeClaim.
   
   # Step 2:- Here We need to make a deployment of Prometheus .
      # Prometheus :-
      file is avaible in devops task 5 Folder file name (prom.yml)
      # Explanation:
      Here U need to expose port no. 9090 , prometheus runs on port no. 9090, in k8s for exposing the port , kind is NodePort, to make the config file, Here we need to configure he kind ConfigMap, in the config map set your node ipi,e (IP:9100) port no. 9100 for node_exporter, for making data permanent here we use kind PersistentVolumeClaim , and gave access ReadWriteOnce, After that we have to make deployment in k8s , kind is Deployment , spec for specification, gave image name i,e(vimal13/prometheus), conatinerPort(9090), Now here we have to mount to things first ConfigMap location (/etc/prometheus/prometheus.yml) and second PersistentVolumeClaim location (/prometheus/data) for making our data permament.
   # Step 3:- Configure kustomization file.
     file is avaible in devops task 5 Folder file name (kustomization.yml)
     # Explanation:-
     specifies the common resources and common customizations to those resources, Here where need to use kink Kustomization in k8s, gave resources and it executes the resources, Here resources are yaml files .

# Step 4:-
To Run the setup type. 
   kubectl apply -k .
 ![cmd](https://user-images.githubusercontent.com/55234454/87907826-f39e0480-ca82-11ea-98e8-ff1d0feba4db.jpg)
 # Step 5:- output of Sevices :-
  open your command prompt type >
    kubectl get svc
    ![0 (6)](https://user-images.githubusercontent.com/55234454/87907904-1cbe9500-ca83-11ea-973f-d2007948e3e9.jpg)
  # Step 6:- output of prometheus .
  ![Screenshot (367)](https://user-images.githubusercontent.com/55234454/87907966-41b30800-ca83-11ea-9181-786246ebc3e9.png)
# Step 7:- Setup of Grafana.
![Screenshot (358)](https://user-images.githubusercontent.com/55234454/87908107-8343b300-ca83-11ea-93f6-3a16c326a7d6.png)
>To login:
  >username: admin
  >password: admin
  >Set your new password to login > submit.
  ![Screenshot (361)](https://user-images.githubusercontent.com/55234454/87908241-b4bc7e80-ca83-11ea-9198-5dc98823bb3e.png)
Click on Data Sources for setup prometheus.
  ![Screenshot (369)](https://user-images.githubusercontent.com/55234454/87908332-d453a700-ca83-11ea-93d0-7b2d8bf9b772.png)
Type name , Gave the URL of prometheus.
Here my URL is https://192.168.99.101:31823
 ![Screenshot (370)](https://user-images.githubusercontent.com/55234454/87908393-ed5c5800-ca83-11ea-9aab-6c7166a83e59.png)
 ![Screenshot (372)](https://user-images.githubusercontent.com/55234454/87908433-fea56480-ca83-11ea-84ca-f24ed16ad22d.png)
 Now Here our prometheus configured in the Grafana.
 # Step 8:- create Dashboard in Grafana.
    Goto Grafana Homepage.
    Click on the + icon to create Dashboard , Here u need to configure the Panel for Dashboard.
 ![Screenshot (372)](https://user-images.githubusercontent.com/55234454/87908590-4d52fe80-ca84-11ea-8f93-65cee3e60a44.png)
      Click on the + icon to create Dashboard , Here u need to configure the Panel for Dashboard.
     Click on Add panel 
     Select prometheus (shyamnedra).
     Give the query (node_disk_io_wieghted_seconds_total) for usage of disk > Gauge Visualization.
![Screenshot (376)](https://user-images.githubusercontent.com/55234454/87908646-6bb8fa00-ca84-11ea-84a7-1918d3c099a6.png)
   > save it
   > add a new panel
   > Add query node_cpu_seconds_total(for CPU usage)> select Gauge Visualization.
  ![Screenshot (378)](https://user-images.githubusercontent.com/55234454/87908845-c2becf00-ca84-11ea-9bef-e2fb1dd2074d.png)
    > Save it.
 > After that add new panel > configure using following image:
 > Add query sum by (job)(prometheus_http_requests_total{code="200"}) , it means we need to see how many http_request on prometheus, sum if for sum the values , code=200 means valid request,
![Screenshot (380)](https://user-images.githubusercontent.com/55234454/87908915-e2ee8e00-ca84-11ea-89d5-4cb3f5516f3e.png)
  > After that add new panel > configure using following image:
  > Add query node_disk_read_bytes_total(For Disk Usage), Select Bar gauge Visualization.
  > Save it
  > Output after setup.
  ![Screenshot (381)](https://user-images.githubusercontent.com/55234454/87909022-129d9600-ca85-11ea-8b9d-6a7de5aa2d8f.png)
# Step9 :- Now let's test whether our data is permanent or not.
    Open command prompt and delete the pod of grafana.
    kubectl delete pod <pod_name>
    ![0 (9)](https://user-images.githubusercontent.com/55234454/87909134-45e02500-ca85-11ea-8a06-d65d90341b7a.jpg)
 # Explanation:-
Here U can see our pod has been deleted, and after that it starts new pod this is because we already use deployment, if pod is deleted deployment start new ones, here U can see for some second our data , dashboard has gone.
![Screenshot (381)](https://user-images.githubusercontent.com/55234454/87909207-70ca7900-ca85-11ea-8382-76d1f4beaa1c.png)
# Final output:-
  ![Screenshot (381)](https://user-images.githubusercontent.com/55234454/87909207-70ca7900-ca85-11ea-8382-76d1f4beaa1c.png)
 # tools
  > Redhat
  > Grfana
  > prometheus
  > cmd 
 # Author 
   Shyamendra Pratap Singh Sengar
 
 ![Screenshot (372)](https://user-images.githubusercontent.com/55234454/87908433-fea56480-ca83-11ea-84ca-f24ed16ad22d.png)


