# This document specify how the Hybrid experiment have to be implemented

## Experiment

This experiment is going to use the same both experiments and escenarios as the Article.

## Topology

The topology has to be a Spine Leaf topology with the difference that the 3 leaf switches must be use Legacy features of the switch with an LACP link to the spines, meanwhile the 2 spine switches must be using the OpenFlow.

> IDEA: Different methodologies could be tested using mininet, and hybrid just like the physical and using the groups features (Mininet uses OVSwitch).
