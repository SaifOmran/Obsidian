### Containers VS Virtual machines
- **Architecture:** Containers virtualize the OS, while VMs virtualize the hardware.
- **Size:** Containers are typically megabytes, whereas VMs are gigabytes.
- **Performance:** Containers boot in seconds; VMs take minutes to boot.
- **Security:** VMs offer stronger isolation because they are separated at the OS level, whereas containers are isolated at the process level.
- **Portability:** Containers are highly portable across different environments, making them ideal for DevOps and CI/CD, while VMs are generally better for legacy applications.
- **Saleability**: Containers are more saleable than VMs as you can run new container faster than running new VM.
### What is Docker?
- Docker is an open-source software platform that enables developers to build, test, and deploy applications quickly using containerization.
- It packages an application and all its dependencies (code, runtime, system tools, libraries, and settings) into a single, standardized unit called a container, ensuring the application runs consistently across any environment.
- It separates the application from the infrastructure.