# Aruba 2930F Experiments to test the differences between Legacy and Openflow

## Experiment

Run 10-20 packets using the next different parameters:
- Protocols: TCP and UDP
- Data size: 16, 64, 256, 1024
- Bandwidth: 16, 64, 256, 1024

All experiments have to be executed on 30 seconds and synchronized to capture all the input/output packets using Wireshark or the ODL stats feature.

## Scenarios

The will be some different scenarios to be tested:
1. Physical using OpenFlow
2. Physical using legacy features
3. Simulation

## Topology

The topology used will be extremely simple, only has be formed by two hosts connected into one single switch on ports 1 and 2 respectively and this switch has to be connected to the ODL controller using the port 24 of the switch.
