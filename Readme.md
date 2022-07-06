# Requirements

- Ubuntu / Linux distro
- microk8s


# Start local env

### Setup cluster
    
    sudo snap install microk8s --classic
    microk8s status --wait-ready
    microk8s enable dashboard dns registry istio helm3 storage ingress

### Host

    Add **127.0.0.1       app.local** to /etc/hosts

### Create Cert
    
    microk8s helm3 repo add jetstack https://charts.jetstack.io
    microk8s helm3 repo update
    microk8s kubectl create namespace cert-manager
    microk8s kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.8.2/cert-manager.crds.yaml
    microk8s kubectl apply -f ca.yml -n microservices
    openssl genrsa -out ca.key 2048 ; openssl req -x509 -new -nodes -days 365 -key ca.key -out ca.crt -subj "/CN=app.local" -addext "subjectAltName = DNS:app.local" 
    microk8s kubectl create secret tls default-tls-secret -n microservices --key ca.key --cert ca.crt

### Up

    microk8s helm3 install one -n microservices base/ --values values.yml --debug
    microk8s helm3 install two -n microservices base/ --values values.yml --debug
    microk8s helm3 install three -n microservices base/ --values values.yml --debug
    microk8s kubectl apply -f ingress.yml -n microservices

### Monitoring

    microk8s dashboard-proxy



