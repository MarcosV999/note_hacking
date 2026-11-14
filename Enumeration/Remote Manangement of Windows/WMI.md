Windows Management Instrumentation (`WMI`) is Microsoft's implementation and also an extension of the Common Information Model (`CIM`), core functionality of the standardized Web-Based Enterprise Management (`WBEM`) for the Windows platform.
## Footprinting the Service
The initialization of the WMI communication always takes place on `TCP` port `135`, and after the successful establishment of the connection, the communication is moved to a random port. For example, the program [wmiexec.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/wmiexec.py) from the Impacket toolkit can be used for this.
```shell
wmiexec.py usuario:contraseña@$target
wmiexec.py Cry0l1t3:"P455w0rD!"@$target "hostname"
```