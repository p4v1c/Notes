create : 
centreon -u admin -p Admin123456789* -o HOSTTEMPLATE -a IMPORT -v "Linux-SNMP;/usr/share/centreon/www/modules/centreon-os-templates/templates/generic/snmp_os/linux/Linux-SNMP.xml"

centreon -u admin -p Admin123456789* -o HOSTTEMPLATE -a IMPORT -v "Windows-SNMP;/usr/share/centreon/www/modules/centreon-os-templates/templates/generic/snmp_os/windows/Windows-SNMP.xml"

add : 

centreon -u <username> -p <password> -o HOST -a ADD -v "host_name;alias;192.168.1.1;generic-host;central;2;snmp;windows;public;2;161"

centreon -u <username> -p <password> -o HOST -a ADD -v "host_name;alias;192.168.1.2;generic-host;central;2;snmp;linux;public;2;161"
