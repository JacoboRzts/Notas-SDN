# Aruba restrictions


The Switch 2930f of aruba allow the use of OpenFlow but has some restrictions to care about.


## Tables
Aruba use the 0 table as default but depending of what type of Flow you want to install it use different tables:
- 100 is for hardware flows which is the faster and main table almost in any case
- 200 is the software table which is not so usefull because the process is very slow but aruba use to drop any packet not matched.

