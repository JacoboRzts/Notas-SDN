
# Software-Define Networks
Those are my notes for **Software-Defined Networks** using a Simulated environment with **Mininet** and a real environment with 5 **Aruba 2930F** Switch's both running the **OpenDayLight** controller with the OpenFlow plugins.

This notes are part of my social service at UASLP working with the Dr. Pedro David Arjona Villicaña.

## Experiments

- [Miguel's article experiments](experiments/article): This was the main experiment and includes a simulation, and physical test using two different topologies.
- [Aruba test](experiments/aruba): this experiments try to test the real capacity of the Aruba Switches when using OpenFlow and if has some differences with the Legacy use.
- [SDN Hybrid test using Spine Leaf](experiments/hybrid): The goal of this experiment is to use both legacy and Openflow technologies in order to improve the reliability and redundancy of the spine leaf topology.

## Concepts, guides and cheatsheets

There are some basic concepts to understand the OpenDayLight when using OpenFlow to control an SDN network:
- [Flows](concepts/flows)
- [Groups](concepts/groups)
- [Meters](concepts/meters)
- [Multipaths](concepts/multipaths)
- [Aruba](concepts/aruba.md)

Also there are some guide that help me to don't make the same mistake twice:

- [How to check the ODL connection on an Aruba Switch](how-to/check-odl)
- [How to connect the Switch from an PC using SSH or console cable](how-to/connect-switch)
- [How to install Mininet on a Docker container](how-to/install-mininet)
- [How to install OpenDayLight](how-to/install-odl)

Cheatsheets: 


- [docker](cheatsheets/docker)
- [OpenDayLight Endpoints](cheatsheets/endpoints)
- [Debian Linux Network commands](cheatsheets/linux-network)
- [OVSwitch commands](cheatsheets/ovswitch)

## Online documentation
- [ODL Openflow Flow Examples](https://docs.opendaylight.org/projects/openflowplugin/en/latest/users/flow-examples.html#)
- [Aruba Switch Configuration](https://arubanetworking.hpe.com/techdocs/AOS-S/16.10/OAG/content/oag.htm)
- [Groups Example](https://floodlight.atlassian.net/wiki/spaces/floodlightcontroller/pages/7995427/How+to+Work+with+Fast-Failover+OpenFlow+Groups#The-FAST-FAILOVER-Group)
- [Aruba Switch 2930F JL253A Specifications](https://www.hpe.com/us/en/collaterals/collateral.c05052929.html#toc-block3-l1)

