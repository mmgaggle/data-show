# Module - 2 : Building images to use as a base for a Jupyter Notebook application

!!! Summary "Module Agenda"
    - **In this module you will be building images to use as a base for a Jupyter Notebook application.**

!!! example "Prerequisite"
    - You must run all the commands logged in as user **cloud-user** on the **OpenShift Master Node** node, unless otherwise specified. 

## Building a OpenShift Spark Image

The [radanalytics.io](https://radanalytics.io) community is focused on empowering intelligent application development on the OpenShift Platform. One of the artifacts maintained by the community is incomplete Openshift Spark builder images [(openshift-spark-inc)](https://hub.docker.com/r/radanalyticsio/openshift-spark-inc/) that can be combined with a Spark tarball to create usable OpenShift Spark images (openshift-spark). We have created a custom Spark 2.3.2 tarball that includes Hadoop 2.8.5 for the purposes of this tutorial, and the following commands will combine it with the [radanalyticsio](https://radanalytics.io) incomplete OpenShift Spark builder images.

```
oc new-build --name=openshift-spark \
             --docker-image=radanalyticsio/openshift-spark-inc:latest \
             -e SPARK_URL=http://mmgaggle-bd.s3.amazonaws.com/spark-2.3.2-bin-hadoop-2.8.5.tgz \
             -e SPARK_MD5_URL=http://mmgaggle-bd.s3.amazonaws.com/spark-2.3.2-bin-hadoop-2.8.5.tgz.md5 \
             --binary
oc start-build openshift-spark
```

- You can observe the image building process by tailing the buildconfig log

```
oc logs -f buildconfig/openshift-spark
```

## Building a Jupyter Notebook Image

Once the openshift-spark image is built, you can use it as a base image for building a base-notebook image. Use the base-notebook repository from [radanalyticsio](https://radanalytics.io) as a starting point. You may want to consider forking this repository and using your own copy if you want to build a set of commonly used libraries into the image.

```
oc new-build https://github.com/radanalyticsio/base-notebook \
             --docker-image="docker-registry.default.svc:5000/data-show/openshift-spark" \
             --strategy=docker
```

- Again, you can observe the image building process by tailing the buildconfig log.

```
oc logs -f buildconfig/base-notebook
```

## Creating a Jupyter Notebook application

Once the base-notebook image is built, you can use it to deploy a Jupyter notebook application.

```
oc new-app -i data-show/base-notebook:latest \
           -e JUPYTER_NOTEBOOK_PASSWORD=developer \
           -e RGW_API_ENDPOINT=${RGW_API_ENDPOINT} \
           -e JUPYTER_NOTEBOOK_X_INCLUDE=https://raw.githubusercontent.com/mmgaggle/hybrid-data-context/master/hybrid-data-context.ipynb
```

- Now expose the Ceph Nano credentials via the OpenShift secret we created earlier to the Jupyter notebook application environment.

```
oc env --from=secret/ceph-rgw-keys dc/base-notebook
```

- Finally, expose a route to the Jupyter notebook so we can access it from our browser.

```
oc expose svc/base-notebook
```

- You can inquire about the status of the deployment from the cli, or through the OpenShift console.

```
oc status
```

- Wait for it to finish deploying, and then you can load the route in your browser using the route URL from the cli or from the OpenShift console.


!!! summary "End of Module"
    **We have reached to the end of Module-2. At this point you have deployed a Jupyter Notebook application and can complete the rest of the lab from the interactive notebook.
