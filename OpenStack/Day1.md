- Openstack is open source software that used for creating cloud (public or private).
- Openstack is Linux based, so it is installed on Linux OS and the server becomes cloud server.
- AWS uses private packages to make its cloud, it is not using openstack.
- openstack developed in 2010, AWS 2006.
- Instead of unknown and private packages of AWS, openstack was developed to become opensource platform to create and manage cloud, regardless of the cloud size (good performance for any cloud infrastructure size), also becoming easy to implement and scalable.
- first release of openstack was named "Austin".
- ==VMware vcloud director (VCD) and openstack are both cloud management platforms==, but they serve different ecosystems: VCD specializes in multi-tenant management for VMware SDDC environments, while OpenStack is an open-source, flexible IaaS framework for heterogeneous environments.
- Openstack can be managed through GUI or CLI (openstack client tool).
---
# OpenStack components
- ==Nova== -> core component provides compute service (like EC2) and provides root disk service.
- ==Neutron== -> core component provides networking service (like VCP).
- ==Cinder== -> optional component provide data storage service (like EBS).
- ==Horizon== -> optional component which provides the dashboard to manage the cloud.
- ==Keystone== -> core component provides authentication and authorization services (like IAM).
- ==Glance== -> core component provides image management service (like AMI).
- ==Swift== -> optional component provides object storage service (like S3).
>Each service has API, backed and system services are needed to perform.
---
# Concepts
### Messaging queue
- A message queue is an intermediary system that stores messages temporarily and delivers them from producers to consumers in a reliable and asynchronous manner.
### Token
- A temporary credential issued by Keystone that proves your identity and permissions when you interact with OpenStack services.
### Domain
- Logical container used to organize users, groups, roles and projects.
### Project
- Logical container where cloud resources are created and managed (subdomain) and each project is managed by admin or user.
### Role
- Defines what actions a user is allowed to perform within a project.
> Domain -> user -> role -> project.