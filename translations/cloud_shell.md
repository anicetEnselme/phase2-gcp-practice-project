# LAB: Architecting with Google Kubernetes Engine - Workloads: Creating a GKE Cluster via GCP Console

## Objectives

In this lab, you learn how to perform the following tasks:

	- Use the GCP Console to build and manipulate GKE clusters

	- Use the GCP Console to deploy a Pod

	- Use the GCP Console to examine the cluster and Pods

## Steps

1. Use the GCP Console to deploy a GKE cluster

	- Set the environment variable for the zone and cluster name:

		export my_zone=us-central1-a

		export cluster_name=standard-cluster-1

	- Create a Kubernetes cluster with the following command:

		gcloud container clusters create $cluster_name --num-nodes 3 --zone $my_zone --enable-ip-alias

2. Modify GKE clusters

	- Modify standard-cluster-1 to have four nodes with the following command:

		gcloud container clusters resize $cluster_name --zone $my_zone --size=4

3. Connect to a GKE cluster

	- Create a kubeconfig file with the credentials of the current user:

		gcloud container clusters get-credentials $cluster_name --zone $my_zone

	- Open the kubeconfig file with the nano text editor:

		nano ~/.kube/config

4. Deploy a sample workload

	- Deploy the latest version of nginx as a Pod named nginx-1 by runing the following command:

		kubectl create deployment nginx-1 --image nginx:latest

	- To get all pods deployed into our cluster, run the following command:

		kubectl get pods

	- Enter a pod name into a variable:

		export nginx_pod_name=nginx-1-34c7gslb84-nvwsc

5. View details about workloads in the GCP Console

	- To view the resource usage across the nodes of the cluster run the following command:

		kubectl top nodes

	- To view the resource usage across the workload deployed into the cluster run the following command:

		kubectl top pods

	- To view the complete details of the Pod you just created, run:

		kubectl describe pod $my_nginx_pod


