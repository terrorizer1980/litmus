# Contents

## Docker tips and tricks - litmus_image

List all docker images, including stopped ones

```
docker container ls -a
CONTAINER ID        IMAGE                      COMMAND                  CREATED              STATUS                     PORTS                  NAMES
e7bc7e5b3d9b        waffleimage/oraclelinux7   "/bin/sh -c /usr/sbi…"   About a minute ago   Up About a minute          0.0.0.0:2225->22/tcp   waffleimage_oraclelinux7_-2225
ae94def06077        waffleimage/oraclelinux6   "/bin/sh -c /sbin/in…"   3 minutes ago        Up 3 minutes               0.0.0.0:2224->22/tcp   waffleimage_oraclelinux6_-2224
80b22735494e        waffleimage/centos6        "/bin/sh -c /sbin/in…"   5 minutes ago        Up 5 minutes               0.0.0.0:2223->22/tcp   waffleimage_centos6_-2223
b7923a25f95b        ubuntu:14.04               "/bin/bash"              6 weeks ago          Exited (255) 4 weeks ago   0.0.0.0:2222->22/tcp   ubuntu_14.04-2222
```

stop and remove an image

```
docker rm -f ubuntu_14.04-2222
```

connect via ssh to the docker image (don't add to the known hosts file, don't check keys)
```
ssh -o UserKnownHostsFile=/dev/null -o StrictHostKeyChecking=no root@localhost -p 2222
```

attach to the docker image and detach
```
docker attach centos6
# to deattach <ctrl + p> then <ctrl + q>
```
NB you cannot attach to a docker image that is running systemd/upstart ie the litmus_image images