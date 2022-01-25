Step-1 verify Prerequisites
除了支持部署在 VM 和 BM 上，KubeSphere 还支持安装在云托管和本地现有的 Kubernetes 集群上。
### 1.Prerequisites
>Kubernetes Version: 1.17.x, 1.18.x, 1.19.x, 1.20.x;

> CPU > 1 Core, Memory > 2 G;

> An existing default Storage Class in your Kubernetes clusters.

通过`kubectl version`在集群节点上运行，确保您的 Kubernetes 版本兼容。
> 注意Server Version线，如果GitVersion大于v1.17.0，就可以了

### 2.检查可用资源是否满足集群中的最低先决条件

```shell
kubectl describe nodes/node01 | grep --color=always  "memory:" | tail -1
```
### 3.检查集群中是否有默认存储类
```shell
kubectl get sc
## 在集群中找不到存储类
```
### 4.在集群中安装openebs storage Class
```shell

kubectl create namespace openebs
helm repo add openebs https://openebs.github.io/charts
helm repo update
helm install openebs --namespace openebs openebs/openebs --wait
```
### 5.将openebs设置为集群的默认存储类
```shell

kubectl patch storageclasses.storage.k8s.io openebs-hostpath -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```
### 6.检查集群中是否有默认存储类

```shell
kubectl get sc
```

如果您的 Kubernetes 集群环境满足上述所有要求，那么您可以开始安装 KubeSphere。
### 7.安装kubesphere ks-installer。
```shell
kubectl apply -f https://github.com/kubesphere/ks-installer/releases/download/v3.1.1/kubesphere-installer.yaml
kubectl apply -f https://github.com/kubesphere/ks-installer/releases/download/v3.1.1/cluster-configuration.yaml
```
### 8.验证kubesphere安装 ```kubectl get pods -n kubesphere-system```
### 9.检查安装日志
```shell
kubectl logs -n kubesphere-system $(kubectl get pod -n kubesphere-system -l app=ks-install -o jsonpath='{.items[0].metadata.name}') -f



```
### 10.当 KubeSphere 的所有 Pod 都在运行时，表示安装成功。通过以下命令检查控制台服务的端口（默认为 30880）。然后您可以使用默认帐户访问控制台admin/P@88w0rd。
```shell
**************************************************
Waiting for all tasks to be completed ...
task openpitrix status is successful  (1/4)
task network status is successful  (2/4)
task multicluster status is successful  (3/4)
task monitoring status is successful  (4/4)
**************************************************
Collecting installation results ...
#####################################################
###              Welcome to KubeSphere!           ###
#####################################################

Console: http://172.17.0.41:30880
Account: admin
Password: P@88w0rd

NOTES：
  1. After you log into the console, please check the
     monitoring status of service components in
     "Cluster Management". If any service is not
     ready, please wait patiently until all components 
     are up and running.
  2. Please change the default password after login.

#####################################################
https://kubesphere.io             2022-01-25 07:50:03
#####################################################

```