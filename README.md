# docker-centos-systemd
Dockerfile for systemd base image

Systemd is now included in both the centos:7 and centos:latest base containers,
but it is not active by default. This Dockerfile deletes a number of unit files
which might cause issues. From here, you are ready to build your base image.

     $ docker build --rm -t local/docker-centos-systemd .

In order to use the systemd enabled base container created above, you will
need to create your Dockerfile similar to the one below.

     FROM local/docker-centos-systemd
     RUN yum -y install httpd; yum clean all; systemctl enable httpd.service
     EXPOSE 80
     CMD ["/usr/sbin/init"]

Build this image:

     $ docker build --rm -t local/docker-centos-systemd-httpd

In order to run a container with systemd, you will need to mount the cgroups
volumes from the host. Below is an example command that will run the systemd
enabled httpd container created earlier.

     $ docker run -ti -v /sys/fs/cgroup:/sys/fs/cgroup:ro -p 80:80 local/docker-centos-systemd-httpd

This container is running with systemd in a limited context, with the cgroups filesystem mounted.
