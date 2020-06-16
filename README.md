Run the below commands in the cluster

curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh

sudo snap install fluxctl --classic

kubectl -n kube-system create sa tiller

kubectl create clusterrolebinding tiller-cluster-rule \\
    --clusterrole=cluster-admin \\
    --serviceaccount=kube-system:tiller


helm repo add fluxcd https://charts.fluxcd.io

kubectl apply -f https://raw.githubusercontent.com/fluxcd/helm-operator/master/deploy/crds.yaml

kubectl create namespace flux

helm upgrade -i flux fluxcd/flux \\
   --set git.url=git@github.com:ctmuthu/flux-get-started \\
   --namespace flux

helm upgrade -i helm-operator fluxcd/helm-operator --wait \\
--namespace flux \\
--set git.ssh.secretName=flux-git-deploy \\
--set helm.versions=v3


Fetch the deploy key using the below command and update the repo:

fluxctl identity --k8s-fwd-ns flux