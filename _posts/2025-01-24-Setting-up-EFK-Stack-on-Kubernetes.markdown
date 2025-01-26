---
title: "Setting up EFK Stack on Kubernetes"
layout: post
date: 2025-01-25 12:20
image: ../assets/images/efk/efk-main.jpg
headerImage: true
tag:
    - efk
    - k8s
    - logging
category: blog
author: guneycansanli
description: Setting up EFK Stack on Kubernetes
---

# How to Setup EFK Stack on Kubernetes: A Step-by-Step Guide

### Introduction

EFK (Elasticsearch, Fluentd, Kibana) is a powerful open-source stack for centralized log aggregation, analysis, and visualization. When managing multiple applications and services on Kubernetes, centralizing logs ensures better monitoring and troubleshooting. 

This guide walks you through setting up the EFK stack on a Kubernetes cluster.

---

## What is the EFK Stack?

- **Elasticsearch**: A distributed search and analytics engine that stores and retrieves large log volumes. It's designed for scalability and efficiency.
- **Fluentd**: A flexible log collector that unifies data collection and forwards logs to various destinations like Elasticsearch.
- **Kibana**: A user-friendly interface for querying, visualizing, and analyzing log data.

Elasticsearch is optimized for managing unstructured log data, Fluentd serves as the log shipper, and Kibana provides visualization tools for better insights.

---

## Architecture of EFK Stack

The EFK stack architecture for Kubernetes involves:

1. **Fluentd**: Deployed as a DaemonSet to gather logs from all nodes. It forwards logs to the Elasticsearch endpoint.
2. **Elasticsearch**: Deployed as a StatefulSet for persisting log data. It provides a service endpoint for Fluentd and Kibana.
3. **Kibana**: Deployed as a Deployment to visualize the data stored in Elasticsearch.

### High-Level Architecture

The deployment includes Fluentd for log collection, Elasticsearch for storage, and Kibana for visualization.


## Step-by-Step Setup

- You can clone **https://github.com/guneycansanli/k8s-training.git** and fond all yamls under **kubernetes-efk-yamls** directory.

- Note: All the EFK components get deployed in the default namespace.

```bash
git clone https://github.com/guneycansanli/k8s-training.git
```


### 1. Deploy Elasticsearch StatefulSet

- This command installs the latest stable version of Argo Rollouts in the argo-rollouts namespace

#### Create a Headless Service

- Define the headless service (`es-svc.yaml`) for internal communication between Elasticsearch pods:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: elasticsearch
spec:
  selector:
    app: elasticsearch
  clusterIP: None
  ports:
    - port: 9200
      name: rest
    - port: 9300
      name: inter-node
```

![efk][1]

- Apply the service:

```bash
kubectl apply -f es-svc.yaml
```

- Before we begin creating the statefulset for elastic search, let’s recall that a statefulset requires a storage class defined beforehand using which it can create volumes whenever required.
- Since I work with my local cluster in my home lab , Local Clusters Lack Native Storage Backends
Local Kubernetes clusters (e.g., minikube, kubeadm setups) don't come with built-in storage backends. Instead Storage is tied to the local filesystem of the worker nodes. You have to manually specify the storage paths or directories that the PVs map to, using mechanisms like hostPath or local volumes.
- You need to crate StorageClass

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: local-storage
provisioner: kubernetes.io/no-provisioner
volumeBindingMode: WaitForFirstConsumer
```

```bash
kubectl apply -f local-storage-class.yaml
```

- We also need to have PersistentVolume to Bound 

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: local-pv-worker-0
spec:
  capacity:
    storage: 2Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: local-storage
  local:
    path: /opt/k8s/es-cluster-0
  nodeAffinity:
    required:
      nodeSelectorTerms:
      - matchExpressions:
        - key: kubernetes.io/hostname
          operator: In
          values:
            - kworker1
```

```bash
kubectl apply -f local-persistent-volume-kworker1.yaml
```

- Note: Make sure path directory already exist and also You may have some issues if you crate PV on worker or master node.
- I have crated 3 diffrent PV so ElasticSearch istences can claim (We have 3 nodes/replicas in cluster)
- Deploy Elasticsearch
- Use the following StatefulSet (**es-sts.yaml**) to deploy Elasticsearch:

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: es-cluster
spec:
  serviceName: elasticsearch
  replicas: 3
  selector:
    matchLabels:
      app: elasticsearch
  template:
    metadata:
      labels:
        app: elasticsearch
    spec:
      containers:
      - name: elasticsearch
        image: docker.elastic.co/elasticsearch/elasticsearch:7.5.0
        resources:
          limits:
            cpu: 1000m
          requests:
            cpu: 100m
        ports:
        - containerPort: 9200
          name: rest
          protocol: TCP
        - containerPort: 9300
          name: inter-node
          protocol: TCP
        volumeMounts:
        - name: data
          mountPath: /usr/share/elasticsearch/data
        env:
          - name: cluster.name
            value: k8s-logs
          - name: node.name
            valueFrom:
              fieldRef:
                fieldPath: metadata.name
          - name: discovery.seed_hosts
            value: "es-cluster-0.elasticsearch,es-cluster-1.elasticsearch,es-cluster-2.elasticsearch"
          - name: cluster.initial_master_nodes
            value: "es-cluster-0,es-cluster-1,es-cluster-2"
          - name: ES_JAVA_OPTS
            value: "-Xms512m -Xmx512m"
      initContainers:
      - name: fix-permissions
        image: busybox
        command: ["sh", "-c", "chown -R 1000:1000 /usr/share/elasticsearch/data"]
        securityContext:
          privileged: true
        volumeMounts:
        - name: data
          mountPath: /usr/share/elasticsearch/data
      - name: increase-vm-max-map
        image: busybox
        command: ["sysctl", "-w", "vm.max_map_count=262144"]
        securityContext:
          privileged: true
      - name: increase-fd-ulimit
        image: busybox
        command: ["sh", "-c", "ulimit -n 65536"]
        securityContext:
          privileged: true
      tolerations:
      - key: "node-role.kubernetes.io/control-plane"
        operator: "Exists"
        effect: "NoSchedule"
      nodeSelector:
        kubernetes.io/hostname: kworker1
  volumeClaimTemplates:
  - metadata:
      name: data
      labels:
        app: elasticsearch
    spec:
      accessModes: [ "ReadWriteOnce" ]
      storageClassName: "local-storage"
      resources:
        requests:
          storage: 2Gi
```

- Apply the StatefulSet:

```bash
kubectl apply -f es-sts.yaml
```

### Verify Elasticsearch Deployment

- After the Elastisearch pods come into the running state, let us try and verify the Elasticsearch statefulset. The easiest method to do this is to check the status of the cluster. In order to check the status, port-forward the Elasticsearch pod’s 9200 port.

```bash
kubectl port-forward es-cluster-0 9200:9200
```

- You can sned curl to health check.

```bash
curl http://localhost:9200/_cluster/health/?pretty
```

![efk][2]

![efk][3]


## Deploy Kibana Deployment & Service

Kibana can be created as a simple Kubernetes deployment. If you check the following Kibana deployment manifest file, we have an env var ELASTICSEARCH_URL defined to configure the Elasticsearch cluster endpoint. Kibana uses the endpoint URL to connect to elasticsearch.

- Create the Kibana deployment manifest as kibana-deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: kibana
  labels:
    app: kibana
spec:
  replicas: 1
  selector:
    matchLabels:
      app: kibana
  template:
    metadata:
      labels:
        app: kibana
    spec:
      containers:
      - name: kibana
        image: docker.elastic.co/kibana/kibana:7.5.0
        resources:
          limits:
            cpu: 1000m
          requests:
            cpu: 100m
        env:
          - name: ELASTICSEARCH_URL
            value: http://elasticsearch:9200
        ports:
        - containerPort: 5601
```

- Create the manifest.
```bash
kubectl create -f kibana-deployment.yaml
```

- To access the Kibana UI via the node's IP address, we'll create a NodePort service. This is a simple way to expose the service for demonstration purposes. A NodePort service assigns a specific port on each cluster node, allowing you to access the application using the node's IP and the assigned port.In real-world projects, however, it's more common to use Kubernetes Ingress in combination with a ClusterIP service. This setup provides better control, security, and scalability by routing external traffic through the ingress, while the service itself remains accessible only within the cluster.

- Save the following manifest as kibana-svc.yaml

```yaml
apiVersion: v1
kind: Service
metadata:
  name: kibana-np
spec:
  selector: 
    app: kibana
  type: NodePort  
  ports:
    - port: 8080
      targetPort: 5601 
      nodePort: 30000
```

- Create the kibana-svc

```bash
kubectl create -f kibana-svc.yaml
```

- Now you will be able to access Kibana over http://<node-ip>:3000

![efk][4]

- You may need open port to cluster

### Verify Kibana Deployment

- After the pods come into the running state, let us try and verify Kibana deployment. The easiest method to do this is through the UI access of the cluster.

To check the status, port-forward the Kibana pod’s 5601 port. If you have created the nodePort service, you can also use that.

```bash
kubectl port-forward <kibana-pod-name> 5601:5601
```

![efk][5]

![efk][6]

---

## Deploy Fluentd Kubernetes Manifests

### Deploying Fluentd as a DaemonSet
Fluentd is typically deployed as a DaemonSet to ensure it can collect logs from all nodes in the cluster. To accomplish this, it also needs elevated permissions to access and retrieve pod metadata across all namespaces.

### Service Accounts in Kubernetes
To grant specific permissions to components within a Kubernetes cluster, service accounts are used in conjunction with cluster roles and cluster role bindings. Let’s proceed to create the necessary service account and define appropriate roles.

#### Creating a Fluentd Cluster Role
A cluster role in Kubernetes defines rules that outline a specific set of permissions. For Fluentd, we need to grant permissions to access both pods and namespaces.

---

1. Create a manifest fluentd-role.yaml

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: fluentd
  labels:
    app: fluentd
rules:
- apiGroups:
  - ""
  resources:
  - pods
  - namespaces
  verbs:
  - get
  - list
  - watch
```

- Apply the manifest
```bash
kubectl create -f fluentd-role.yaml
```

2. Create Fluentd Service Account
  A service account in kubernetes is an entity to provide identity to a pod. Here, we want to create a service account to be used with fluentd pods.

- Create a manifest **fluentd-sa.yaml**

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: fluentd
  labels:
    app: fluentd
```

- Apply the manifest

```bash
kubectl create -f fluentd-sa.yaml
```

3. Fluentd Cluster Role Binding

- Create a manifest fluentd-rb.yaml

```yaml
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: fluentd
roleRef:
  kind: ClusterRole
  name: fluentd
  apiGroup: rbac.authorization.k8s.io
subjects:
- kind: ServiceAccount
  name: fluentd
  namespace: default
```

- Apply the manifest
```bash
kubectl create -f fluentd-rb.yaml
```

4. Deploy Fluentd DaemonSet

- Save the following as fluentd-ds.yaml

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd
  labels:
    app: fluentd
spec:
  selector:
    matchLabels:
      app: fluentd
  template:
    metadata:
      labels:
        app: fluentd
    spec:
      serviceAccount: fluentd
      serviceAccountName: fluentd
      containers:
      - name: fluentd
        image: fluent/fluentd-kubernetes-daemonset:v1.4.2-debian-elasticsearch-1.1
        env:
          - name:  FLUENT_ELASTICSEARCH_HOST
            value: "elasticsearch.default.svc.cluster.local"
          - name:  FLUENT_ELASTICSEARCH_PORT
            value: "9200"
          - name: FLUENT_ELASTICSEARCH_SCHEME
            value: "http"
          - name: FLUENTD_SYSTEMD_CONF
            value: disable
          - name: FLUENT_CONTAINER_TAIL_PARSER_TYPE
            value: /^(?<time>.+) (?<stream>stdout|stderr) [^ ]* (?<log>.*)$/
        resources:
          limits:
            memory: 512Mi
          requests:
            cpu: 100m
            memory: 200Mi
        volumeMounts:
        - name: varlog
          mountPath: /var/log
        - name: varlibdockercontainers
          mountPath: /var/lib/docker/containers
          readOnly: true
      terminationGracePeriodSeconds: 30
      volumes:
      - name: varlog
        hostPath:
          path: /var/log
      - name: varlibdockercontainers
        hostPath:
          path: /var/lib/docker/containers
```

- Apply

```bash
kubectl create -f fluentd-ds.yaml
```

5. Verify Fluentd Setup
6. In order to verify the fluentd installation, let us start a pod that creates logs continuously. We will then try to see these logs inside Kibana.

- Save the following as test-pod.yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: counter
spec:
  containers:
  - name: count
    image: busybox
    args: [/bin/sh, -c,'i=0; while true; do echo "Thanks for visiting devopscube! $i"; i=$((i+1)); sleep 1; done']
```

- Apply

```bash
kubectl create -f test-pod.yaml
```

- At the end We should have below resources

![efk][7]

- We can verify logs are in Kibana now
- We need to do **port-forward** again
- Step 1: Open kibana UI using proxy or the nodeport service endpoint. Head to management console inside it.

![efk][8]

- Step 2: Select the “Index Patterns” option under Kibana section.
- Step 3: Create a new Index Patten using the pattern – “logstash-*” and
- Step 4: Select “@timestamp” in the timestamps option.
- Now We should able to see indexes/logs

![efk][9]

![efk][10]


---



* * *

---

Thanks for reading...

---

---

---

---

> :+1: :+1: :+1: :+1: :+1: :+1: :+1: :+1:

---

Guneycan Sanli.

---

[1]: ../assets/images/efk/efk1.jpg
[2]: ../assets/images/efk/efk2.jpg
[3]: ../assets/images/efk/efk3.jpg
[4]: ../assets/images/efk/efk4.jpg
[5]: ../assets/images/efk/efk5.jpg
[6]: ../assets/images/efk/efk6.jpg
[7]: ../assets/images/efk/efk7.jpg
[8]: ../assets/images/efk/efk8.jpg
[9]: ../assets/images/efk/efk9.jpg
[10]: ../assets/images/efk/efk10.jpg


