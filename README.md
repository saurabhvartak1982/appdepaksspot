# Cloud Native Application Deployment Patterns in AKS using Spot Nodes

## Introduction:
![Introduction](images/intro.png) <br />

<b>For this prototype, 4 different AKS NodePools were created:</b> <br /><br />

a. <b>systempool:</b> System NodePool with Taint <b>CriticalAddonsOnly=true:NoSchedule</b><br />
b. <b>odnplabel:</b> OnDemand NodePool with Label <b>workloadtype=critical</b><br />
c. <b>odnptaintlabel:</b> OnDemand NodePool with Label <b>workloadtype=critical</b> and Taint <b>pooltype=primary:NoSchedule</b><br />
d. <b>spotnodepool:</b> Spot NodePool with Label <b>workloadtype=batch</b>. Spot NodePools have built-in Taint <b>kubernetes.azure.com/scalesetpriority=spot:NoSchedule</b><br /><br />

<b>Below were the commands that were used to create these AKS NodePools:</b> <br /><br />
az aks nodepool add --resource-group kedapoc-rg --cluster-name kedapocaks --name systempool --node-count 1 --node-taints CriticalAddonsOnly=true:NoSchedule --mode System<br /><br />

az aks nodepool add --resource-group kedapoc-rg --cluster-name kedapocaks --name odnplabel --node-count 1 --labels workloadtype=critical<br /><br />

az aks nodepool add --resource-group kedapoc-rg --cluster-name kedapocaks --name odnptaintlabel --node-count 1 --labels workloadtype=critical --node-taints pooltype=primary:NoSchedule<br /><br />

az aks nodepool add --resource-group kedapoc-rg --cluster-name kedapocaks --name spotnodepool --priority Spot --eviction-policy Delete --spot-max-price -1 --enable-cluster-autoscaler --min-count 1 --max-count 2 --labels workloadtype=batch<br /><br />

Below is how the NodePool are looking on the Azure Portal <br /><br />
![Portal Node Pools](images/portalnodepools.png) <br /><br />

Now there are 3 different options of patterns in which the application pods can be distributed across the OnDemand and Spot NodePools. The same are as documented below:

## Option 1: <br />
In this option we will make use of the below files: <br /><br />
<b>op1net5appdeploy.yaml</b> - This has the application deployment manifest which needs to be deployed on the OnDemand and Spot Nodes. This uses the namespace <b>op1ns</b>.<br />
<b>op1net5appsvc.yaml</b> - This has the service manifest for the above application. This uses the namespace <b>op1ns</b>.<br />
<b>nginx.yaml</b> - Any other application to be deployed on any available OnDemand NodePool. This uses the namespace <b>otherapp</b>.<br />

![Option 1](images/option1.png) <br /><br />

As explained in the above diagram, the pods of the application <b>net5app</b> get distributed across the OnDemand NodePool (in my case - <b>odnplabel</b>) and the Spot NodePool (in my case - <b>spotnodepool</b>). However, there is no control as to how many pods may get deployed to OnDemand and how many on Spot. The pods dont get deployed on the OnDemand NodePool <b>odnptaintlabel</b> as this NodePool has a taint <b>pooltype=primary:NoSchedule</b> which is not tolerated by the application defined in op1net5appdeploy.yaml. <br /><br />

Below is the output that I get when I do a <b>kubectl apply -f</b> on the files <b>op1net5appdeploy.yaml</b>, <b>op1net5appsvc.yaml</b> and <b>nginx.yaml</b>. For the net5app application, results may differ in your case. Please create namespaces <b>op1ns</b> and <otherapp> for trying out option 1.<br />
![Option 1 Pod Placement](images/option1podplacement.png) <br /><br />



## Option 2:
![Option 2](images/option2.png)

## Option 3:
![Option 3](images/option3.png)

## Co-author information:
<b>Yusuf Rangwala (Global Black Belt - Cloud Native @ Microsoft)</b> worked with me on the prototype and the content used for this article. <br /> His GitHub id is: https://github.com/whereisyusuf
