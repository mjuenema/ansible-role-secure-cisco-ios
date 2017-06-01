Requirements                                                                              
------------                                                                              
                                                                                          
Ansible needs SSH access to the target Cisco IOS routers or switches. A                   
minimal Cisco IOS configuration is shown below.                                           
                                                                                          
.. code-block::                                                                     
                                                                                          
    ! Minimal Cisco IOS configuration to allow Ansible to connect.                        
    ! Copy and paste this into config mode.                                               
    ! Adjust HOSTNAME, DOMAINNAME, ENABLESECRET, USERNAME and PASSWORD to your needs.     
    !                                                                                     
    hostname HOSTNAME                                                                     
    ip domain-name DOMAINNAME                                                             
    crypto key generate rsa                                                               
    service password-encryption                                                           
    enable secret 0 ENABLESECRET                                                          
    username USERNAME password 0 PASSWORD                                                 
    aaa new-model                                                                         
    aaa authentication login default local-case                                           
    aaa authentication enable default enable                                              
    aaa authorization commands 15 default local                                           
    line vty 0 3                                                                          
      transport input ssh                                                                 
      login local               
