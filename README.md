# mesh
MVP for automatic mesh deployment for operator based AAP

This repo contains a playbook for automatic deployment of an execution node. It is meant as a proof of concept / mvp. push/pull and hop/execution nodes combinations are possible using the controller.instance module but the playbook in this example use the standard push exec node.

Also, being an MVP, its not perfect:
- It fails when the instance is already deployed. it should be more idempotent
- It will not install when it finds there is already an /etc/receptor/receptor.conf present on the execution node. Maybe it should cleanup and reinstall instead.
- It enables the subscription for AAP 2.6 hard coded. This might need to be paramerized

The playbook uses the inventory and inventory vars to determine how to deploy and confiure the mesh nodes. Each mesh node has an entry in the inventory. Hostvars will determine the various properties of the mesh nodes. The instance name is derived from the inventory_hostname. For the MVP an example inventory.yml is in the project and used as the inventory source.

An AAP workflow could be:
[create_vms_for mesh_nodes] -> [sync inventory] -> [deploy_mesh]

Used collections the EE:
- ansible.controller
- ansible.posix

The job template in AAP should have 2 credentials:
- Controller credontial
- Machine credential for the machines where the mesh is deployed on

See https://console.redhat.com/ansible/automation-hub/collections/published/ansible/controller/documentation

Notes:
- Using this method will create a hard dependency on ssh access to the mesh nodes from the controller (like with the container based installation). If this is a problem and manual installation is still not desired, this playbook can also be run from some installation node other than the controller
- If this is used for bootstrapping a full AAP installation this means that the controller must initially be a _hybrid_ node, because otherwise it can not run the mesh deployment from it (chicken and egg problem). After the mesh is deployed, the mesh role can be moved to a _control_ node, but maybe it's good to keep it hybrid for other mesh related jobs.
- the use of the command module to run the ansible-playbook on the mesh node is on purpose to make it as compatible with future versions as possible

Notes on lifecycling:
- Upgrading mesh nodes is basically running _sudo dnf update ansible-runner receptor -y_ and then restart the receptor service. This can easily be done automatically using a playbook, although you need to consider carefully where such a job should be able to run (most likely on a hybrid node again)
- Migrating to, for example, a new OS, would be a pattern where you add a new instance to the group and then removing the deprected one.
- Changing topology in-place in an automatic fashion should theoratically be possible, but one could/should consider the effort versus the one-time need.