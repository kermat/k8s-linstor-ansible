# Kubernetes w/ LINSTOR Ansible Playbook

Build a Kubernetes cluster w/ LINSTOR installed using Ansible.

Requirements:

  - An account at https://my.linbit.com (contact sales@linbit.com).
  - Deployment environment must have Ansible `2.7.0+` and `python-netaddr`.

# Usage

Adjust the variables in `hosts.ini` accordingly. For example:
```
... snip ...
# all vars
[multi:vars]
ansible_user=root
become=yes
ansible_ssh_common_args='-o StrictHostKeyChecking=no'
# Linstor variables
drbd_backing_disk=/dev/sdb
# my.linbit.com customer portal credentials
lb_user="fthomas"
lb_pass="Password1!"
lb_email="fthomas@somewhere.com"
```
You can only list a single host as a `k8s_master`. That node will be the node you can administer Kubernetes from.

Bring up cluster using Ansible:

```sh
ansible-playbook -i hosts.ini playbook.yaml
```
You can bring up a Kuberenetes cluster without LINSTOR via `--skip-tags linstor`. Or, you can install LINSTOR into an existing kubernetes cluster using `--tags linstor`.

If you don't want to put LINBIT credentials into your `host.ini`, you can run the playbook like this instead:

```sh
ansible-playbook -e lb_user="username" -e lb_pass="password" playbook.yaml
```

If you're target systems require a sudo password to become root, you can provide the sudo password when running the playbook like this:

```sh
ansible-playbook -i hosts.ini playbook.yaml -e "ansible_become_pass=superSecretSudoPW1!"
```

# Testing Installation

Shell into the `k8s_master` and watch pods coming up:

```sh
watch kubectl get pods
```

Once deployed, there is a pile of yaml in root's home under, `linstor-csi-k8s-yaml`, that can be used to deploy applications:

```sh
kubectl apply -f linstor-csi-k8s-yaml/mysql-replicated-stateful-set/
```

You should see a mysql cluster starting up with LINSTOR volumes being created as it's persistent volumes:

```sh
watch "kubectl get pods; kubectl get pv"
```
