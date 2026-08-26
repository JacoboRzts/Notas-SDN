# Aruba Switch 2930F JL263A

This switches has the capabilities to use OpenFlow but has some features different that are important.

## Tables 

Aruba uses 3 main tables (in theory it allow us to use up to 255) to save and organize the Flows uploaded: 
- 0 This table is used to store a single flow that redirects all packets to the


## Groups
Aruba allow the use of groups but no all of them are fully supported:
- **Indirect**: This type is fully supported but is has not been tested in this project.
- **All**: This type is fully supported but is has not been tested in this project.
- **Select**: this type is only supported using software so you can't configure a flow that trigger a group in the 100 table, you must pass to the 200 table and then trigger a Select group. This is was tested and the results give about 60% of packet loss which make this option non-viable.
- **Fast-Failover**: As the select type, this type is only supported on this type using software tables. Not tested. 


## Meters 


---
[Aruba Guide](https://arubanetworking.hpe.com/techdocs/AOS-S/16.10/OAG/content/oag.htm)