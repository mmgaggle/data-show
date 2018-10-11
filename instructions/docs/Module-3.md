# Module - 3 : Creating a Jupyter Notebook Application in OpenShift Container Platform

!!! Summary "Module Agenda"
    - **In this module you will be building images to use as a base for a Jupyter Notebook application.**

!!! example "Prerequisite"
    - You must run all the commands logged in as user **cloud-user** on the **OpenShift Master Node** node, unless otherwise specified. 

## Deploying and using a Jupyter Notebook application

Jupyter Notebooks are interesting in that they package documentation and code into a single artifact. A developer, data scientist, or data engineer can work through a notebook interactively, and immediately see the results of their analysis. One reason Jupyter Notebooks are popular is that they are fantastic for data visualization, you can see and tweak the code that leads to the visualizations, and execute it without leaving your browser tab.

## Creating a Jupyter Notebook application

To start off this module, we will use the base-notebook image we created in the previous module to roll out a Jupyter Notebook application.

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
    **We have reached to the end of Module-3. At this point you have deployed a Jupyter Notebook application and can complete the rest of the lab from your web browser using the route URL for the base-notebook application.**
