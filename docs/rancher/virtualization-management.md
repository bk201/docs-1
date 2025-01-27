---
keywords:
  - Harvester
  - Rancher
---

## Virtualization Management

From Harvester v0.3.0, virtualization management with multi-cluster feature will be supported using the Rancher 2.6.x version

1. Firstly, you will need to install the Rancher v2.6.1 server or above, for testing purpose you can spin up a rancher server using the docker run command, e.g., 
    ```
    $ sudo docker run -d --restart=unless-stopped -p 80:80 -p 443:443 --privileged rancher/rancher:v2.6.1
    ```

    !!!note
        For production environment setup please refer to the official rancher [docs](https://rancher.com/docs/rancher/v2.6/en/quick-start-guide/deployment/)

2. Once the Rancher server is up and running, login and click the hamburger menu, choose the `Virtualization Management` tab, and select `Import Existing` to import the downstream Harvester cluster into the Rancher server
![](./assets/vm-menu.png)
3. Specify the cluster name and click create, then u will get the registration commands, copy the command and ssh to one of the Harvester management node and run this command accordingly.
![](./assets/harv-importing.png)
4. Once the agent node is ready you should be able to view and access the imported Harvester cluster from the Rancher server and mange your VMs accordingly.
![](./assets/harv-cluster-view.png)
5. From the Harvester UI, you can click the hamburger menu to navigate back to the Rancher multi-cluster page.
![](./assets/harv-back.png)

## Multi-tenancy

In Harvester, we have leveraged the existing Rancher [RBAC authorization](https://rancher.com/docs/rancher/v2.6/en/admin-settings/rbac/), users are able to view and manage a set of resources based on their cluster and project role permissions.

Within Rancher, each person authenticates as a user, which is a login that grants you access to Rancher. As mentioned in [Authentication](https://rancher.com/docs/rancher/v2.6/en/admin-settings/authentication/), users can either be local or external.

Once the user logs in to Rancher, their authorization, or their access rights within the system, is determined by global permissions, and cluster and project roles.

- [Global Permissions](https://rancher.com/docs/rancher/v2.6/en/admin-settings/rbac/global-permissions/):
    - Define user authorization outside the scope of any particular cluster.
- [Cluster and Project Roles](https://rancher.com/docs/rancher/v2.6/en/admin-settings/rbac/cluster-project-roles/):
    - Define user authorization inside the specific cluster or project where they are assigned the role.

Both global permissions and cluster and project roles are implemented on top of [Kubernetes RBAC](https://kubernetes.io/docs/reference/access-authn-authz/rbac/). Therefore, enforcement of permissions and roles is performed by Kubernetes.

- A cluster owner have full control over the cluster and all resources inside it, e.g. like hosts, VMs, volumes, image, networks, backups, and settings
- A project user can be assigned to a specific project with permission to manage the resources inside the project


### Example
The following example provides a good explanation of how the multi-tenant feature works:

1. Firstly, add new users via the Rancher `Users & Authentication` page, click `Create` to add two new separated users e.g. the `project-owner` and `project-readonly` respectively.
    - A `project-owner` is a user with permission to manage a list of resources of a particular project e.g, the default project.
    - A `project-readonly` is a user with read-only permission of a particular project e.g, the default project.
    ![](./assets/create-user.png)
2. Click one of the imported Harvester cluster and visiting the Harvester UI
    - click the `Projects/Namespaces` tab
    - select a project e.g, `default` and click the `Edit Config` menu to assign the users to this project with appropriate permissions,  e.g. the `project-owner` user will be assigned with project owner role.
   ![](./assets/add-member.png)
4. Continue to add the project-readonly user to the same project with read-only permissions and click save.
   ![](./assets/added-user.png)
5. Open an incognito Browser and login to different accounts e.g, the `project-owner`
6. After login with the `project-owner` user, Click the Virtualization Management menu, you should be able to view the cluster you have been assigned to.
7. Click the Images tab, you should be able to view a list of images uploaded to the harvester-public namespace previously, or you can upload your own image if needed.
8. Create a VM with one of the images that you have uploaded
9. Login with another user e.g, `project-readonly`, and this user will only have the read permission of this project.

!!!note
    A know issue of [Read-only user was able to manage API actions](https://github.com/harvester/harvester/issues/1406)
