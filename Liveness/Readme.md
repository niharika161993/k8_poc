Liveness
If a container in the Pod has been running successfully for a while, but the application running inside this container suddenly stopped responding to our requests, then that container is no longer useful to us. This kind of situation can occur, for example, due to application deadlock or memory pressure. In such a case, it is recommended to restart the container to make the application available.

Rather than restarting it manually, we can use a Liveness Probe. Liveness Probe checks on an application's health, and if the health check fails, kubelet restarts the affected container automatically.

Liveness Probes can be set by defining:

Liveness command
Liveness HTTP request
TCP Liveness probe
gRPC Liveness probe
