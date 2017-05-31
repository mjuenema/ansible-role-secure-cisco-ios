Local users                                                                               
===========                                                                               
                                                                                          
A list of local users to add in addition to                                               
``secure_cisco_ios_provider_username``. This must be a list of                            
dictionaries as shown in the example below. It is strongly recommended                    
to store ``secure_cisco_ios_local_users`` in an encrypted                                 
`Vault <http://docs.ansible.com/ansible/playbooks_vault.html>`__.                         
                                                                                          
.. code-block:: yaml                                                                      
                                                                                          
    secure_cisco_ios_local_users: []                                                      
                                                                                          
**Example**                                                                               
                                                                                          
.. code-block:: yaml                                                                      
                                                                                          
    secure_cisco_ios_local_users:                                                         
      - {'username': 'USER1', 'password': 'PASSWORD1'}                                    
      - {'username': 'USER2', 'secret': 'SECRET2'}                                        
      - {'username': 'USER3', 'secret': 'SECRET3', 'privilege': 15 }                      
      - {'username': 'USER4', 'nopassword': true}                                         
                                                                                          
The example above will result in the following Cisco IOS configuration.                   
The password hashing is performed automatically through the filters in                    
`ansible-filter-cisco-hash <https://github.com/mjuenema/ansible-filter-cisco-hash>`__.    
                                                                                          
.. code-block:: yaml                                                                      
                                                                                          
    ! Local users as defined in the example above                                         
    username USER1 password 7 HASH_OF_PASSWORD1                                           
    username USER2 secret 5 HASH_OF_SECRET2                                               
    username USER3 privilege 15 secret 5 HASH_OF_SECRET3                                  
    username USER4 nopassword                                                             
                                                                                          
More complex ``username`` definitons must be performed through                            
``secure_cisco_ios_custom_lines``.                                                        
                                                                                          
It is also possible to ensure that certain local users are not                            
configured by providing a list of names that must be absent.                              
                                                                                          
.. code-block:: yaml                                                                      
                                                                                          
    secure_cisco_ios_absent_local_users: []                                               
                                                                                          
**Example**                                                                               
                                                                                          
.. code-block:: yaml                                                                      
                                                                                          
    secure_cisco_ios_absent_local_users:                                                  
      - ABSENT_USER1                                                                      
      - ABSENT_USER2                
