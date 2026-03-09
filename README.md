# mesh
PoC playbooks for automatic mesh deployment for operator based AAP

This repo contains playbooks for automatic deployment of an execution node. It is meant as a proof of concept. push/pull and hop/execution nodes combinations are possible using the controller.instance module but the PoC playbooks in this example use the standard push exec node.

The playbooks use extra_vars for specifying instance_group_name and instance_group making it suitable to use in a workflow.

The playbooks assume the env var CONTROLLER_HOST is defined

