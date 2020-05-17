# prbz-overview on Open Platform

## PersistentVolumeClaim

In your Open Platform project, in order to make use of it's HTTP response cache, we need to create a PersistentVolumeClaim with prbz-overview-pvc.yaml as below:

    oc create -f prbz-overview-pvc.yaml 

The prbz-overview-pvc.yaml is defined as follow:
   

    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      annotations:
        volume.beta.kubernetes.io/storage-provisioner: kubernetes.io/glusterfs
      finalizers:
        - kubernetes.io/pvc-protection
      name: prbz-overview-pvc1
      labels:
        application: prbz-overview-pvc
    spec:
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 1Gi
          
Noticed that we will reuse the PVC name **prbz-overview-pvc1** as the volume persistentVolumeClaim claimName in DeploymentConfig.

## Secrets
Like the standalone mode, we firstly need to defined Aphrodite metadata as below:

    oc create secret generic aphrodite-secret --from-file=aphrodite.properties.json.example

## ConfigMaps
After we created the secret, we also need to define several configuration. 

    oc create configmap aphrodite-configmap --from-literal=aphrodite.config=/etc/secret/aphrodite.properties.json.example --from-literal=prbz_dev=false --from-literal=cacheDir=/home/jboss --from-literal=cacheName=github-cache  --from-literal=cacheSize=20

Noticed that we will reuse **/etc/secret** as the mount volume path later in DeploymentConfig.

## JBoss EAP 7.1 based application
Next, we need to create several Openshift resource for our application from eap71-basic-s2i.json 

    oc create -f eap71-basic-s2i.json
