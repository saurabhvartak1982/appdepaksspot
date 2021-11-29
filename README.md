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
![Portal Node Pools](images/portalnodepools.png)

## Option 1:
![Option 1](images/option1.png)

## Option 2:
![Option 2](images/option2.png)

## Option 3:
![Option 3](images/option3.png)

## Co-author information:
<b>Yusuf Rangwala (Global Black Belt - Cloud Native @ Microsoft)</b> worked with me on the prototype and the content used for this article. <br /> His GitHub id is: https://github.com/whereisyusuf
