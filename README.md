# KubeflowInstall
根据Kubeflow官方文档中的[安装指南](https://www.kubeflow.org/docs/started/installing-kubeflow/)，在本地部署KF应该到[kubeflow/manifests](https://github.com/kubeflow/manifests)当中安装。安装之前需要装好：
* Kubernets(Up to 1.25) with a default StorageClass
* kustomize [5.0.0](https://github.com/kubernetes-sigs/kustomize/releases/tag/kustomize%2Fv5.0.0)
* kubectl

### Minikube
[minikube](https://github.com/kubernetes/minikube)是Kubernets的本地版本，可以依据[文档](https://minikube.sigs.k8s.io/docs/start/)的内容直接进行安装。

安装完成后记得执行`minikube start`，等到完成后，执行`kubectl version`，若正常，应该能够同时看到client和server的版本信息。

### kustomize
直接根据链接下载对应版本并解压，加入环境变量即可。

### 安装kubectl
[参考链接](https://v1-25.docs.kubernetes.io/docs/tasks/tools/install-kubectl-linux/#install-kubectl-binary-with-curl-on-linux)

### 安装kfctl
在[这里](https://github.com/kubeflow/kfctl/releases)选择对应版本和平台，直接下载后解压即可。是可执行文件，添加到PATH


以上步骤完成后，将manifests clone到本地，在项目文件夹内执行
```bash
while ! kustomize build example | awk '!/well-defined/' | kubectl apply -f -; do echo "Retrying to apply resources"; sleep 10; done
```

安装中出现报错是正常现象。如果最后反复有那么几个安装不了，可以考虑记录下来先跳过，也可以按照指示后面的逐个安装部分尝试安装报错的部分（然而结果大概率还是报错）

此时运行`kubectl get po -A`，可以列出目前KF中所有Pod的状态。正常情况下，一段时间后所有Pod应该都处于Running状态。异常的状态包括Error和ImagePull出错等。


以下安装KF Pipeline
### 安装pipeline sdk
```
URL=https://storage.googleapis.com/ml-pipeline/release/latest/kfp.tar.gz
pip install "${URL}" --upgrade
```

### clone kubeflow pipeline
```
git clone --single-branch --branch 0.3.0 https://github.com/kubeflow/pipelines.git


一切就绪后，运行
```bash
kubectl port-forward svc/istio-ingressgateway -n istio-system 8080:80
```
将istio放到8080端口。随后访问`localhost:8080`即可访问仪表盘。如果上述方法不奏效，则执行
```bash
kubectl port-forward --address 0.0.0.0 svc/istio-ingressgateway -n istio-system 8080:80
```
通过访问`<ip-address>:8080`来访问。
