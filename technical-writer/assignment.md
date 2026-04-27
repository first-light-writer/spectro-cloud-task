
# Debug Operations in Kubernetes

This topic discusses Kubernetes commands that you can use to debug a Kubernetes cluster. You issue these commands from ``kubectl``, which is the command-line interface (CLI) that interacts with a Kubernetes cluster.  The CLI sends your requests to the Kubernetes API server to perform operations on the cluster.

## List all pods 

Most debugging sessions begin by issuing the ``kubectl get`` command to list the pods in the cluster. The output lists the status of the pods, which helps you identify which pod to investigate. 

To list the pods in the current namespace, issue the following command:

```shell
kubectl get pods
```

```shell {2}
NAME             READY   STATUS    RESTARTS      AGE
spectro-nginx1   1/1     Running   1 (51s ago)   24h
spectro-nginx2   1/1     Running   1 (51s ago)   24h
spectro-nginx3   1/1     Running   1 (51s ago)   24h
```

The output lists the three pods in the current namespace of the cluster. The remaining examples in this topic use commands to debug the `spectro-nginx1` pod.

:::tip

A Kubernetes cluster uses namespaces to logically group pods and other resources. When you issue ``kubectl`` commands, the CLI assumes that the resources are located in the current namespace. If the resources are in a different namespace, you must include the ``--namespace`` option when issuing a command.

For example, suppose you wanted to list pods in the ``spectro-sandbox`` namespace, which is not the current namespace. You would issue the following command:

```shell
kubectl get pods --namespace spectro-sandbox
```

If you did not want to specify the ``--namespace`` option every time you issued a command, you could use the ``kubectl config set-context`` command to change the current namespace. For example:


```shell
kubectl config set-context --current --namespace=spectro-sandbox
```

:::

## List logs for a container

After determining which pod you want to debug, you can use the ``kubectl logs`` command to print the logs for a container in that pod. If a pod contains a single container, you do not need to include the ``-c`` option to specify a container.

**Example**: List the logs for the container in the ``spectro-nginx1`` pod:

```shell
kubectl logs spectro-nginx1
```

```shell
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2026/04/26 14:38:08 [notice] 1#1: using the "epoll" event method
2026/04/26 14:38:08 [notice] 1#1: nginx/1.29.8
2026/04/26 14:38:08 [notice] 1#1: built by gcc 14.2.0 (Debian 14.2.0-19)
2026/04/26 14:38:08 [notice] 1#1: OS: Linux 6.6.87.2-microsoft-standard-WSL2
2026/04/26 14:38:08 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2026/04/26 14:38:08 [notice] 1#1: start worker processes
2026/04/26 14:38:08 [notice] 1#1: start worker process 29
```

## Issue commands in a container

You can use the ``kubectl exec`` command to issue commands in a container. For example, you can issue a command that lists the logs of the application in the container. This lets you debug the container from the inside, such as reviewing log files or configurations, and explore the environment of the container itself.

**Example**: Issue the `date` LINUX command in the first container of the `spectro-nginx1` pod:

```shell
kubectl exec spectro-nginx1 -- date
```

```shell
Sun Apr 26 14:49:39 UTC 2026
```

**Example**: Open the log file for the NGINX web server in the first container of the `spectro-nginx1` pod:

```shell
kubectl exec spectro-nginx1 -- nginx -s reopen
```

```shell
2026/04/26 14:50:48 [notice] 55#55: signal process started
```

:::note

You can issue a ``kubectl debug`` command to create a debugging environment before you start troubleshooting a pod. The ``kubectl debug`` command creates a temporary copy of a running pod that does not terminate if an error occurs in the pod's containers. 

:::

## References

- [What is Kubernetes](https://kubernetes.io/docs/concepts/overview/)
- [kubectl reference](https://kubernetes.io/docs/reference/kubectl/generated/)