## RPi Cluster Setup

Instructions explaining how I created a basic RPI cluster based on combined instructions from various [Alex Ellis](https://www.alexellis.io/) presentations.

### Tools
- 3x Raspberry Pi 4 (1x4gb for primary node and 2x2gb for supporting nodes)
- USB Powerport charging station
- Pack of 12" USB C cables
- Pack of 12" network cables
- Powered network switch
- 3x 32gb SD cards
- Laptop with Linux or Windows 10 running Linux subsystem

### Process

Keygen - I had to use the following or I got errors running the k3sup commands

    ssh-keygen -m PEM

k3sup install (on laptop):
    
    curl -sSLf https://get.k3sup.dev | sudo sh

arkade install:

    curl -sLS https://dl.get-arkade.dev | sudo sh

setup SSH keys on RPi (complete for each node):

    ssh-copy-id pi@192.168.xxx.xxx

setup k3s on primary node:

    k3sup install --ip 192.168.xxx.xxx --user pi --no-extras

Test:

    export KUBECONFIG=/home/[name]/kubeconfig
    kubectl get node -o wide

setup k3s on sub nodes (complete for all):

    k3sup join --user pi --server-ip 192.168.xxx.xxx --ip 192.168.xxx.yyy

install OpenFAAS

    arkade install openfaas

install openFAAS CLI

    curl -sSL https://cli.openfaas.com | sudo sh

check services

    kubectl get svc -n openfaas

get password

    kubectl get secret -n openfaas basic-auth -o jsonpath={.data.basic-auth-password} | base64 --decode; echo

forward port

    kubectl port-forward svc/gateway -n openfaas 31112:8080

Load OpenFAAS dashboard

[localhost:31112](http://localhost:31112/ui/)



### Additional Notes

Kill port in use:

    fuser -k 31112/tcp