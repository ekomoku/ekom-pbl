## PROJECT 7: DEVOPS TOOLING WEBSITE SOLUTION

In previous [Project 6(https://www.darey.io/docs/project-6-step-1/) you implemented a WordPress based solution that is ready to be filled with content and can be used as a full fledged website or blog. Moving further we will add some more value to our solutions that your DevOps team could utilize. We want to introduce a set of DevOps tools that will help our team in day to day activities in managing, developing, testing, deploying and monitoring different projects.

The tools we want our team to be able to use are well known and widely used by multiple DevOps teams, so we will introduce a single DevOps Tooling Solution that will consist of:

Jenkins – free and open source automation server used to build CI/CD pipelines.
Kubernetes – an open-source container-orchestration system for automating computer application deployment, scaling, and management.
Jfrog Artifactory – Universal Repository Manager supporting all major packaging formats, build tools and CI servers. Artifactory.
Rancher – an open source software platform that enables organizations to run and manage Docker and Kubernetes in production.
Grafana – a multi-platform open source analytics and interactive visualization web application.
Prometheus – An open-source monitoring system with a dimensional data model, flexible query language, efficient time series database and modern alerting approach.
Kibana – Kibana is a free and open user interface that lets you visualize your Elasticsearch data and navigate the Elastic Stack.
Note: Do not feel overwhelmed by all the tools and technologies listed above, we will gradually get ourselves familiar with them in upcoming projects!

Side Self Study
Read about Network-attached storage (NAS), Storage Area Network (SAN) and related protocols like NFS, (s)FTP, SMB, iSCSI. Explore what Block-level storage is and how it is used by Cloud Service providers, know the difference from Object storage.
On the example of AWS services understand the difference between Block Storage, Object Storage and Network File System.

Setup and technologies used in Project 7
As a member of a DevOps team, you will implement a tooling website solution which makes access to DevOps tools within the corporate infrastructure easily accessible.

In this project you will implement a solution that consists of following components:

Infrastructure: AWS
Webserver Linux: Red Hat Enterprise Linux 8
Database Server: Ubuntu 20.04 + MySQL
Storage Server: Red Hat Enterprise Linux 8 + NFS Server
Programming Language: PHP
Code Repository: GitHub

For Rhel 8 server use this ami ami-07b1305cd78401892


![Screenshot from 2023-04-30 15-13-15](https://user-images.githubusercontent.com/66005935/235357770-9bc12d21-230e-4b41-a158-f148d55975e3.png)

![Screenshot from 2023-04-30 15-14-03](https://user-images.githubusercontent.com/66005935/235357794-d532f120-27d8-4b4d-ae77-3797ef9c527d.png)

![Screenshot from 2023-04-30 15-14-03](https://user-images.githubusercontent.com/66005935/235357816-f491b291-d1e7-4dee-a20d-639d5dfa04f4.png)

On the diagram below you can see a common pattern where several stateless Web Servers share a common database and also access the same files using Network File Sytem (NFS) as a shared file storage. Even though the NFS server might be located on a completely separate hardware – for Web Servers it look like a local file system from where they can serve the same files.

![Screenshot from 2023-04-30 15-16-14](https://user-images.githubusercontent.com/66005935/235357885-72ac63f5-3de0-4a4d-ba0e-0edd8f1810e9.png)

It is important to know what storage solution is suitable for what use cases, for this – you need to answer following questions: what data will be stored, in what format, how this data will be accessed, by whom, from where, how frequently, etc. Base on this you will be able to choose the right storage system for your solution.

