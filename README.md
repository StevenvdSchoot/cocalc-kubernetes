# cocalc-kubernetes

This is a free open source AGPL licensed slightly modified version of [cocalc-docker](https://github.com/sagemathinc/cocalc-docker), but for running cocalc on a Kubernetes cluster.  There is one pod for the server and one pod for each project.

## Installation

See [server/README.md](./server/README.md) to get going!

## Discussion

cocalc-kubernetes is an open source (but AGPL) version of CoCalc that can be run on an existing generic Kubernetes cluster.

It is as similar as possible to [cocalc-docker](https://github.com/sagemathinc/cocalc-docker), with the following changes:
  1. It runs in a Kubernetes cluster rather than a plain Docker install, and
  2. Projects run as separate pods on the cluster rather than all in the same Docker container.

The benefit of this architecture include:
  - Projects can have individual resource limitations imposed by Kubernetes
  - The security issues with cocalc-docker (involving projects connecting to other project services on localhost) can be addressed by blocking outgoing network connections from projects
  - Projects run across a cluster, so the number of projects that one can run at once is a function of the number (and size) of nodes in the cluster, rather than cocalc-docker host.
  - This can be done with just a slight modifictaion and addition to the entirely open source (AGPL) codebase that cocalc-docker uses.
  
The drawbacks of this architecture over a more complicated architecture like the closed-source KuCalc (what https://cocalc.com uses) include:
  - It is unclear to what extent it can handle a large number of *simultaneous users*, since the entire server component (the database, hub, NFS file server, etc.) are all served from a single pod.
  - Project storage is just a single NFS server, so disk iops may be lower for client projects, which may or may not be an issue depending on the network and use of projects.
  - Snapshots and automated backups have to be handled outside CoCalc somehow.  This is the responsibility of the admin and is not part of cocalc-kubernetes at present.
  - The project image is much more minimal than the one provided by https://cocalc.com -- it has to be small enough to reasonably run in a normal way without pull taking too long.  The image in KuCalc is hundreds of gigabytes but mounts quickly using some sophisticated tricks.
  

