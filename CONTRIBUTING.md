# How to contribute

Contributions to this project are very, very welcome. It is a complex undertaking and I'd love to share the work.

Ideally proceed like this.

1. Open a new Issue so we can discuss your idea. 
2. Fork the repository.
3. Implement and test your changes in your fork.
3. Create a Pull Request against the `develop` branch not `master` as I use [gitflow](https://github.com/nvie/gitflow).

Different people have different ideas about how a Cisco router or switch
should be configured. Personally I am much more a Systems Engineer than a Network Engineer so I don't claim
to be the ultimate authority on the subject either. Inevitably there is going to be some trade-off between flexibility and 
simplicity that must be balanced to keep this Ansible role useful.

I test this role on the following devices
* [Cisco IE3000 Industrial Ethernet Switch](http://www.cisco.com/c/en/us/support/switches/ie-3000-8tc-industrial-ethernet-switch/model.html) running IOS 15.0(2)EY3. Unfortunately its 8 port Ethernet switch is faulty but is my preferred test 
  unit because it is fanless and quiet!
* Cisco 1800 router. I only use this occasionally because it is very noisy. 
* Cisco 2900 switch. I only use this occasionally because it is very noisy.

Markus Juenemann, May-2017
