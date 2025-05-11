# link de referencia del ejemplo:
    https://aws.amazon.com/blogs/storage/running-wordpress-on-amazon-eks-with-amazon-efs-intelligent-tiering/

## 1. creación del cluster EKS con 2 nodos en el 'node group', cluster llamado 'myeks'

## 2. configuración del shell para acceder remotamente al cluster:

    aws eks update-kubeconfig --region us-east-1 --name myeks

    kubectl get nodes

ejemplo de salida:
    $ kubectl get nodes
    NAME                            STATUS   ROLES    AGE   VERSION
    ip-172-31-32-239.ec2.internal   Ready    <none>   24m   v1.32.3-eks-473151a
    ip-172-31-81-246.ec2.internal   Ready    <none>   24m   v1.32.3-eks-473151a
    $

## 3. crear un servicios AWS EFS y obtener el id, ejemplo: fs-0d2b5ff834bfe5f61
### este id debe ser actualizado en el archivo: 02wordpress-deployment.yaml

## 4. verificar tener instalado 'helm', sino ver esta referencia: https://docs.aws.amazon.com/eks/latest/userguide/helm.html

### para linux:

    curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
    chmod 700 get_helm.sh
    ./get_helm.sh

## 5. Instalar driver EFS para EKS:

    helm repo add aws-efs-csi-driver https://kubernetes-sigs.github.io/aws-efs-csi-driver/

## 6. ejecutar el manifiesto de configuración de EFS en EKS:

    kubectl apply -f private-ecr-driver.yaml

## 7. ejecutar el despliegue de mysql y wordpress en EKS:

nota: no se utilizan los archivos mysql-deployment.yaml propuesto en la página de referencia, y se utiliza 01mysql-deployment.yaml depurado de varias fuentes.

    kubectl apply -k ./

monitorear:
    
    kubectl get pods --watch
    kubectl get all -o wide

conectarse a un pod:

    kubectl exec -it <podname> /bin/bash

borrar wp:
    
    kubectl delete -k ./
