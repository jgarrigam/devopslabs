 # Añadiendo nuevos nodos al cluster

Si necesitaramos añadir un tercer nodo al cluster, worker3 con ip 192.168.1.113, lo primero que tenemos que hacer es seguir los mismos pasos que en el resto de workers y que se encuentran detallados en [00-00-instalando-kubernetes.md](00-00-instalando-kubernetes.md).

Al llegar al paso en el que hay que unir el nodo al clúster ejecutando **kubeadm join** deberemos generar un token.

Antes de eso en el master vamos a permitir las comunicaciones desde el nuevo nodo al master. Por lo tanto en el master deberemos ejecutar:

```console
[root@kubemaster ~]# firewall-cmd --permanent --add-rich-rule 'rule family=ipv4 source address=192.168.1.113/32 accept'
success
[root@kubemaster ~]# firewall-cmd --reload
success
[root@kubemaster ~]#
```

Podemos ver si tenemos algún token activo:

```console
[kubeadmin@kubemaster ~]$ kubeadm token list
[kubeadmin@kubemaster ~]$
```

Creamos un token e imprimimos el comando que es necesario ejecutar para que el nodo se una al clúster:

```console
[kubeadmin@kubemaster ~]$ kubeadm token create --print-join-command
kubeadm join 192.168.1.110:6443 --token 6kbm9j.0x7096yia5urrztm --discovery-token-ca-cert-hash sha256:b975164eb25204da0e13256af91390ad02e3287df399a1a9586cabd55f84ed9a 
[kubeadmin@kubemaster ~]$
```

Ejecutamos el comando anterior en el nodo:

 ```console
 [root@kubenode3 ~]# kubeadm join 192.168.1.110:6443 --token 6kbm9j.0x7096yia5urrztm --discovery-token-ca-cert-hash sha256:b975164eb25204da0e13256af91390ad02e3287df399a1a9586cabd55f84ed9a
...

This node has joined the cluster:
* Certificate signing request was sent to apiserver and a response was received.
* The Kubelet was informed of the new secure connection details.

Run 'kubectl get nodes' on the control-plane to see this node join the cluster.

[root@kubenode3 ~]#
 ```

 El nodo se unirá al clúster:

 ```console
 [kubeadmin@kubemaster ~]$ kubectl get nodes
NAME                   STATUS   ROLES                  AGE    VERSION
kubemaster.acme.es   Ready    control-plane,master   42h    v1.23.3
kubenode1.acme.es    Ready    <none>                 42h    v1.23.3
kubenode2.acme.es    Ready    <none>                 154m   v1.23.3
kubenode3.acme.es    Ready    <none>                 24m    v1.23.3
[kubeadmin@kubemaster ~]$ 
 ```