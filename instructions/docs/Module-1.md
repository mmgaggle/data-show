# Module - 1 : Deploying Ceph Nano into OpenShift

!!! Summary "Module Agenda"
    - **In this module you will be deploying a Ceph Nano service into OpenShift**

- From your workstation SSH into ``OpenShift Master Node`` with the user name **``cloud-user``** and ``SSH Private Key`` [(Need Help..Learn how to Login)](https://ksingh7.github.io/data-show/#accessing-the-lab)

```
chmod 400 <path to ssh_key.pem>
ssh -i <path to ssh_key.pem> cloud-user@<OpenShift Master Node SSH IP Address>
```  

!!! example "Prerequisite"
    - You must run all the commands logged in as user **cloud-user** on the **OpenShift Master Node** node, unless otherwise specified. 

## Create an OpenShift Project

In order to save your precious lab time, OpenShift Container Platform has already been installed and configured. Before you begin with some data science exercises, you will need to create an OpenShift project.

- SSH into OpenShift Master Node as ``cloud-user``

```
ssh -i <path to ssh_key.pem> cloud-user@<OpenShift Master Node IP Address>
```

- Clone hybrid-data-context repository

```
git clone https://github.com/mmgaggle/hybrid-data-context
cd hybrid-data-context/
```

- Login to OpenShift

```
oc login -u teamuser1 -p openshift
```

- Create a new project

```
oc new-project data-show
```

- Adjust security context for this project

```
sudo oc --as system:admin adm policy add-scc-to-user anyuid system:serviceaccount:data-show:default
```

- Use OpenShift [secrets](https://docs.openshift.com/container-platform/3.10/dev_guide/secrets.html) to store a set of credentials that the Ceph Nano service will create during its bootstraping process. Later we will show how these secrets can be exposed to applications in OpenShift through environmental variables.

```
oc create -f ceph-rgw-keys.yml -n data-show
```

- Create the Ceph Nano service and create a route to the object gateway

```
oc create -f ceph-nano.yml -n data-show
oc expose pod ceph-nano-0 --type=NodePort
```

- Next grab the endpoint, we will use this later to pass to our Jupyter Notebook

```
export RGW_API_ENDPOINT=http://$(oc describe pod ceph-nano-0 | grep IP | cut -d: -f2 | awk '{print $1}'):8000
```

- Verify the endpoint includes the IP address of the Ceph Nano pod, otherwise re-run the previous command.

```
echo $RGW_API_ENDPOINT
```

- You could also use the OpenShift Container Platform Console by visiting ``OpenShift Web Console URL`` that you can get from [Qwiklab Portal under Connection Details](https://ksingh7.github.io/data-show/#wait-for-lab-provisioning-to-complete).

- The user name and password to access the console is ``teamuser1`` and ``openshift`` respectively.

![](images/data-show-images/ocp-login-screen.png)

!!! summary "End of Module"
    **We have reached to the end of Module-1. At this point you have learned how to deploy Ceph Nano into OCP. In the next module we will build images as a base for our Jupyter Notebook application.**
