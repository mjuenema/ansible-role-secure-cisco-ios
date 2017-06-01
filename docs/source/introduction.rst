Introduction
------------

**!!!!! This project is currently in the planning stage !!!!!**                           
                                                                                          
An Ansible Role for hardening Cisco IOS routers or switches based on the                  
following recommendations.                                                                
                                                                                          
-  `Secure IOS Template by Team                                                           
   Cymru <https://www.cymru.com/Documents/secure-ios-template.html>`__                    
-  More to add...                                                                         
                                                                                          
**This is not a general purpose role for configuring Cisco IOS devices.**                                                                                
                                                                                          
The sole objective of this role is to ensure that the configuration of a                  
Cisco IOS router or switch meets a minimum security level. Configuring                    
interfaces, routing, and so forth is outside the scope of this role.                      
There may even be additional configuration options to further enhance                     
the security level which are currently not covered by this role.                          
                                                                                          
Important advice:

- Don't blindly trust this role. Try to understand what changes the role may apply. 
- Perform dry-runs. 
- Always test on non-production routers/switches first. 
- Use the ``--limit`` command line argument as well as the ``serial`` option for rolling updates. 
- See ``CONTRIBUTING.md`` for information on how to make this role better.                                                   
