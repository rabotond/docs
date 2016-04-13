.. _tutorial-basic:

Basic
-----

In this section, simple examples will be shown. The examples will focus on introducing the different resource types and will have two categories:

 - The helloworld examples will only show how a single node can be created and how very simple contextualisation information (e.g. message string) can be passed to a virtual machine (VM)
 - The ping examples will focus on to introduce how dependency can be created and how can connection between two nodes can be built up by passing the ip of a node to another.

Please, note that the following examples require a properly configured Occopus, therefore we suggest to continue this section if you already followed the instructions written in the :ref:`Installation <installation>` section.

EC2-Helloworld
~~~~~~~~~~~~~~
This tutorial builds an infrastructure containing a single node. The node will receive information (i.e. a message string) through contextualisation. The node will store this information in ``/tmp`` directory.

**Features**

In this example, the following feature(s) will be demonstrated:

 - creating a node with basic contextualisation
 - using the ec2 resource handler

**Prerequisites**

 - accessing a cloud through EC2 interface (access key, secret key, endpoint, regionname)
 - target cloud contains a base OS image with cloud-init support (image id, instance type)

**Download**

You can download the example as `tutorial.examples.ec2-helloworld <../../examples/ec2-helloworld.tgz>`_ .

**Steps**

#. Edit ``nodes/node_definitions.yaml``. For ``ec2_helloworld_node`` set the followings in its ``resource`` section:

   - ``endpoint`` is an url of an EC2 interface of a cloud (e.g. `https://ec2.eu-west-1.amazonaws.com`). 
   - ``regionname`` is the region name within an EC2 cloud (e.g. `eu-west-1`).
   - ``image_id`` is the image id (e.g. `ami-12345678`) on your EC2 cloud. Select an image containing a base os installation with cloud-init support!
   - ``instance_type`` is the instance type (e.g. `m1.small`) of your VM to be instantiated.
   - ``key_name``  optionally specifies the keypair (e.g. `my_ssh_keypair`) to be deployed on your VM. 
   - ``security_group`` optionally specifies security settings (you can define multiple security groups in the form of a list, e.g. `sg-93d46bf7`) of your VM.
   - ``subnet_id`` optionally specifies subnet identifier (e.g. `subnet-644e1e13`) to be attached to the VM. 

   For further explanation, read the :ref:`node definition's resource section <userdefinitionresourcesection>` of the User Guide. 

   .. code::

     'node_def:ec2_helloworld_node':
         -
             resource:
                 type: ec2
                 endpoint: replace_with_endpoint_of_ec2_interface_of_your_cloud
                 regionname: replace_with_regionname_of_your_ec2_interface
                 image_id: replace_with_id_of_your_image_on_your_target_cloud
                 instance_type: replace_with_instance_type_of_your_image_on_your_target_cloud
                 key_name: replace_with_key_name_on_your_target_cloud
                 security_group_ids:
                     -
                         replace_with_security_group_id1_on_your_target_cloud
                     -
                         replace_with_security_group_id2_on_your_target_cloud
                 subnet_id: replace_with_subnet_id_on_your_target_cloud

#. Make sure your authentication information is set correctly in your authentication file. You must set your access key and secret key in the authentication file. Setting authentication information is described :ref:`here <authentication>`.

#. Load the node definition for ``ec2_helloworld_node`` node into the database. 

   .. important::

      Occopus takes node definitions from its database when builds up the infrastructure, so importing is necessary whenever the node definition or any imported (e.g. contextualisation) file changes!
   
   .. code::

      occopus-import nodes/node_definitions.yaml

#. Start deploying the infrastructure. Make sure the proper virtualenv is activated!

   .. code::

      occopus-build infra-ec2-helloworld.yaml 

#. After successful finish, the node with ``ip address`` and ``node id`` are listed at the end of the logging messages and the identifier of the newly built infrastructure is printed. You can store the identifier of the infrastructure to perform further operations on your infra or alternatively you can query the identifier using the **occopus-maintain** command.

   .. code::

      List of nodes/ip addresses:
      helloworld:
          192.168.xxx.xxx (3116eaf5-89e7-405f-ab94-9550ba1d0a7c)
      14032858-d628-40a2-b611-71381bd463fa

#. Check the result on your virtual machine.

   .. code::
        
      ssh ...
      # cat /tmp/helloworld.txt
      Hello World! I have been created by Occopus

#. Finally, you may destroy the infrastructure using the infrastructure id returned by ``occopus-build``.

   .. code::

      occopus-destroy -i 14032858-d628-40a2-b611-71381bd463fa

EC2-Ping
~~~~~~~~
This tutorial builds an infrastructure containing two nodes. The ping-sender node will ping the ping-receiver node. The sender node will store the outcome of ping in ``/tmp`` directory.

**Features**

 - creating two nodes with dependencies (i.e. ordering of deployment)
 - querying a node's ip address and passing the address to another
 - using the ec2 resource handler

**Prerequisites**

 - accessing a cloud through EC2 interface (access key, secret key, endpoint, regionname)
 - target cloud contains a base OS image with cloud-init support (image id, instance type)

**Download**

You can download the example as `tutorial.examples.ec2-ping <../../examples/ec2-ping.tgz>`_ .

**Steps**

#. Edit ``nodes/node_definitions.yaml``. Both, for ``ec2_ping_receiver_node`` and for ``ec2_ping_sender_node`` set the followings in their ``resource`` section:

   - ``endpoint`` is an url of an EC2 interface of a cloud (e.g. `https://ec2.eu-west-1.amazonaws.com`).
   - ``regionname`` is the region name within an EC2 cloud (e.g. `eu-west-1`).
   - ``image_id`` is the image id (e.g. `ami-12345678`) on your EC2 cloud. Select an image containing a base os installation with cloud-init support!
   - ``instance_type`` is the instance type (e.g. `m1.small`) of your VM to be instantiated.
   - ``key_name``  optionally specifies the keypair (e.g. `my_ssh_keypair`) to be deployed on your VM.
   - ``security_group`` optionally specifies security settings (you can define multiple security groups in the form of a list, e.g. `sg-93d46bf7`) of your VM.
   - ``subnet_id`` optionally specifies subnet identifier (e.g. `subnet-644e1e13`) to be attached to the VM.

   For further explanation, read the :ref:`node definition's resource section <userdefinitionresourcesection>` of the User Guide.
   
   .. code::

     'node_def:ec2_ping_receiver_node':
         -
             resource:
                 type: ec2
                 endpoint: replace_with_endpoint_of_ec2_interface_of_your_cloud
                 regionname: replace_with_regionname_of_your_ec2_interface
                 image_id: replace_with_id_of_your_image_on_your_target_cloud
                 instance_type: replace_with_instance_type_of_your_image_on_your_target_cloud
                 key_name: replace_with_key_name_on_your_target_cloud
                 security_group_ids:
                     -
                         replace_with_security_group_id1_on_your_target_cloud
                     -
                         replace_with_security_group_id2_on_your_target_cloud
                 subnet_id: replace_with_subnet_id_on_your_target_cloud
             ...
     'node_def:ec2_ping_sender_node':
         -
             resource:
                 type: ec2
                 endpoint: replace_with_endpoint_of_ec2_interface_of_your_cloud
                 regionname: replace_with_regionname_of_your_ec2_interface
                 image_id: replace_with_id_of_your_image_on_your_target_cloud
                 instance_type: replace_with_instance_type_of_your_image_on_your_target_cloud
                 key_name: replace_with_key_name_on_your_target_cloud
                 security_group_ids:
                     -
                         replace_with_security_group_id1_on_your_target_cloud
                     -
                         replace_with_security_group_id2_on_your_target_cloud
                 subnet_id: replace_with_subnet_id_on_your_target_cloud
             ...

#. Make sure your authentication information is set correctly in your authentication file. You must set your access key and secret key in the authentication file. Setting authentication information is described :ref:`here <authentication>`.

#. Load the node definition for ``ec2_ping_receiver_node`` and ``ec2_ping_sender_node`` nodes into the database. 

   .. important::

      Occopus takes node definitions from its database when builds up the infrastructure, so importing is necessary whenever the node definition or any imported (e.g. contextualisation) file changes!
   
   .. code::

      occopus-import nodes/node_definitions.yaml

#. Start deploying the infrastructure. Make sure the proper virtualenv is activated!

   .. code::

      occopus-build infra-ec2-ping.yaml 

#. After successful finish, the node with ``ip address`` and ``node id`` are listed at the end of the logging messages and the identifier of the newly built infrastructure is printed. You can store the identifier of the infrastructure to perform further operations on your infra or alternatively you can query the identifier using the **occopus-maintain** command.

   .. code::
   
      List of ip addresses:
      ping_receiver:
          192.168.xxx.xxx (f639a4ad-e9cb-478d-8208-9700415b95a4)
      ping_sender:
          192.168.yyy.yyy (99bdeb76-2295-4be7-8f14-969ab9d222b8)

      30f566d1-9945-42be-b603-795d604b362f

#. Check the result on your virtual machine.

   .. code::

      ssh ...
      # cat /tmp/message.txt
      Hello World! I am the sender node created by Occopus.
      # cat /tmp/ping-result.txt
      PING 192.168.xxx.xxx (192.168.xxx.xxx) 56(84) bytes of data.
      64 bytes from 192.168.xxx.xxx: icmp_seq=1 ttl=64 time=2.74 ms
      64 bytes from 192.168.xxx.xxx: icmp_seq=2 ttl=64 time=0.793 ms
      64 bytes from 192.168.xxx.xxx: icmp_seq=3 ttl=64 time=0.865 ms
      64 bytes from 192.168.xxx.xxx: icmp_seq=4 ttl=64 time=0.882 ms
      64 bytes from 192.168.xxx.xxx: icmp_seq=5 ttl=64 time=0.786 ms

      --- 192.168.xxx.xxx ping statistics ---
      5 packets transmitted, 5 received, 0% packet loss, time 4003ms
      rtt min/avg/max/mdev = 0.786/1.215/2.749/0.767 ms

#. Finally, you may destroy the infrastructure using the infrastructure id returned by ``occopus-build``.

   .. code::

      occopus-destroy -i 30f566d1-9945-42be-b603-795d604b362f

Nova-Helloworld
~~~~~~~~~~~~~~~
This tutorial builds an infrastructure containing a single node. The node will receive information (i.e. a message string) through contextualisation. The node will store this information in ``/tmp`` directory.

**Features**

 - creating a node with basic contextualisation
 - using the nova resource handler

**Prerequisites**

 - accessing an OpenStack cloud through its Nova interface (username/pasword or X.509 VOMS proxy, endpoint, tenant_name)
 - target cloud contains a base OS image with cloud-init support (image_id, flavor_name)

**Download**

You can download the example as `tutorial.examples.nova-helloworld <../../examples/nova-helloworld.tgz>`_ .

**Steps**

#. Edit ``nodes/node_definitions.yaml``. For ``nova_helloworld_node`` set the followings in its ``resource`` section:

   - ``endpoint`` must point to the endpoint (url) of your target Nova cloud. 
   - ``tenant_name`` is the tenant on your target Nova cloud.
   - ``image_id`` is the image id on your Nova cloud. Select an image containing a base os installation with cloud-init support!
   - ``flavor_name`` is the type of flavor to be instantiated on your Nova cloud.
   - ``server_name`` optionally defines the hostname of VM.
   - ``key_name`` optionally sets the name of the keypair to be associated to the instance. Keypair name must exist on the target nova cloud before launching the VM. 
   - ``security_groups`` optionally specifies security settings (you can define multiple security groups in the form of a list) for your VM.
   - ``floating_ip`` optionally allocates new floating IP address to the VM.
   
   For further explanation, read the :ref:`node definition's resource section <userdefinitionresourcesection>` of the User Guide. 

   .. code::

     'node_def:nova_helloworld_node':
         -
             resource:
                 type: nova
                 endpoint: replace_with_endpoint_of_nova_interface_of_your_cloud
                 tenant_name: replace_with_tenant_to_use
                 image_id: replace_with_id_of_your_image_on_your_target_cloud
                 flavor_name: replace_with_id_of_the_flavor_on_your_target_cloud
                 server_name: myhelloworld
                 key_name: replace_with_name_of_keypair_or_remove
                 security_groups:
                     -
                         replace_with_security_group_to_add_or_remove_section
                 floating_ip: add_yes_if_you_need_floating_ip_or_remove

#. Make sure your authentication information is set correctly in your authentication file. You must set your username/password or in case of x509 voms authentication the path of your VOMS proxy in the authentication file. Setting authentication information is described :ref:`here <authentication>`.

#. Load the node definition for ``nova_helloworld_node`` node into the database. 
  
   .. important::

      Occopus takes node definitions from its database when builds up the infrastructure, so importing is necessary whenever the node definition or any imported (e.g. contextualisation) file changes!
   
   .. code::

      occopus-import nodes/node_definitions.yaml

#. Start deploying the infrastructure. Make sure the proper virtualenv is activated!

   .. code::

      occopus-build infra-nova-helloworld.yaml 

#. After successful finish, the node with ``ip address`` and ``node id`` are listed at the end of the logging messages and the identifier of the newly built infrastructure is printed. You can store the identifier of the infrastructure to perform further operations on your infra or alternatively you can query the identifier using the **occopus-maintain** command.

   .. code::

      List of nodes/ip addresses:
      helloworld:
          192.168.xxx.xxx (3116eaf5-89e7-405f-ab94-9550ba1d0a7c)
      14032858-d628-40a2-b611-71381bd463fa

#. Check the result on your virtual machine.

   .. code::
        
      ssh ...
      # cat /tmp/helloworld.txt
      Hello World! I have been created by Occopus

#. Finally, you may destroy the infrastructure using the infrastructure id returned by ``occopus-build``.

   .. code::

      occopus-destroy -i 14032858-d628-40a2-b611-71381bd463fa

Nova-Ping
~~~~~~~~~
This tutorial builds an infrastructure containing two nodes. The ping-sender node will
ping the ping-receiver node. The sender node will store the outcome of ping in ``/tmp`` directory.

**Features**

 - creating two nodes with dependencies (i.e. ordering of deployment)
 - querying a node's ip address and passing the address to another
 - using the nova resource handler

**Prerequisites**

 - accessing an OpenStack cloud through its Nova interface (username/pasword or X.509 VOMS proxy, endpoint, tenant_name)
 - target cloud contains a base OS image with cloud-init support (image_id, flavor_name)

**Download**

You can download the example as `tutorial.examples.nova-ping <../../examples/nova-ping.tgz>`_ .

**Steps**

#. Edit ``nodes/node_definitions.yaml``. Both, for ``nova_ping_receiver_node`` and for ``nova_ping_sender_node`` set the followings in their ``resource`` section:
   
   - ``endpoint`` must point to the endpoint (url) of your target Nova cloud. 
   - ``tenant_name`` is the tenant on your target Nova cloud.
   - ``image_id`` is the image id on your Nova cloud. Select an image containing a base os installation with cloud-init support!
   - ``flavor_name`` is the type of flavor to be instantiated on your Nova cloud.
   - ``server_name`` optionally defines the hostname of VM.
   - ``key_name`` optionally sets the name of the keypair to be associated to the instance. Keypair name must exist on the target nova cloud before launching the VM. 
   - ``security_groups`` optionally specifies security settings (you can define multiple security groups in the form of a list) for your VM.
   - ``floating_ip`` optionally allocates new floating IP address to the VM.

   For further explanation, read the :ref:`node definition's resource section <userdefinitionresourcesection>` of the User Guide. 

   .. code::

     'node_def:nova_ping_receiver_node':
	 -
	     resource:
		 type: nova
		 endpoint: replace_with_endpoint_of_nova_interface_of_your_cloud
		 tenant_name: replace_with_tenant_to_use
		 image_id: replace_with_id_of_your_image_on_your_target_cloud
		 server_name: my-ping-receiver
		 flavor_name: replace_with_id_of_the_flavor_on_your_target_cloud
		 key_name: replace_with_name_of_keypair_or_remove
		 security_groups:
		     -
			 replace_with_security_group_to_add_or_remove_section
		 floating_ip: add_yes_if_you_need_floating_ip_or_remove
             ...
     'node_def:nova_ping_sender_node':
	 -
	     resource:
		 type: nova
		 endpoint: replace_with_endpoint_of_nova_interface_of_your_cloud
		 tenant_name: replace_with_tenant_to_use
		 image_id: replace_with_id_of_your_image_on_your_target_cloud
		 flavor_name: replace_with_id_of_the_flavor_on_your_target_cloud
		 key_name: replace_with_name_of_keypair_or_remove
		 security_groups:
		     -
			 replace_with_security_group_to_add_or_remove_section
		 floating_ip: add_yes_if_you_need_floating_ip_or_remove

#. Make sure your authentication information is set correctly in your authentication file. You must set your username/password or in case of x509 voms authentication the path of your VOMS proxy in the authentication file. Setting authentication information is described :ref:`here <authentication>`.

#. Load the node definition for ``nova_ping_receiver_node`` and ``nova_ping_sender_node`` nodes into the database.
   
   .. important::

      Occopus takes node definitions from its database when builds up the infrastructure, so importing is necessary whenever the node definition or any imported (e.g. contextualisation) file changes!
   
   .. code::

      occopus-import nodes/node_definitions.yaml

#. Start deploying the infrastructure. Make sure the proper virtualenv is activated!

   .. code::

      occopus-build infra-nova-ping.yaml 

#. After successful finish, the node with ``ip address`` and ``node id`` are listed at the end of the logging messages and the identifier of the newly built infrastructure is printed. You can store the identifier of the infrastructure to perform further operations on your infra or alternatively you can query the identifier using the **occopus-maintain** command.

   .. code::
   
      List of ip addresses:
      ping_receiver:
          192.168.xxx.xxx (f639a4ad-e9cb-478d-8208-9700415b95a4)
      ping_sender:
          192.168.yyy.yyy (99bdeb76-2295-4be7-8f14-969ab9d222b8)

      30f566d1-9945-42be-b603-795d604b362f

#. Check the result on your virtual machine.

   .. code::

      ssh ...
      # cat /tmp/message.txt
      Hello World! I am the sender node created by Occopus.
      # cat /tmp/ping-result.txt
      PING 192.168.xxx.xxx (192.168.xxx.xxx) 56(84) bytes of data.
      64 bytes from 192.168.xxx.xxx: icmp_seq=1 ttl=64 time=2.74 ms
      64 bytes from 192.168.xxx.xxx: icmp_seq=2 ttl=64 time=0.793 ms
      64 bytes from 192.168.xxx.xxx: icmp_seq=3 ttl=64 time=0.865 ms
      64 bytes from 192.168.xxx.xxx: icmp_seq=4 ttl=64 time=0.882 ms
      64 bytes from 192.168.xxx.xxx: icmp_seq=5 ttl=64 time=0.786 ms

      --- 192.168.xxx.xxx ping statistics ---
      5 packets transmitted, 5 received, 0% packet loss, time 4003ms
      rtt min/avg/max/mdev = 0.786/1.215/2.749/0.767 ms

#. Finally, you may destroy the infrastructure using the infrastructure id returned by ``occopus-build``.

   .. code::

      occopus-destroy -i 30f566d1-9945-42be-b603-795d604b362f

OCCI-Helloworld
~~~~~~~~~~~~~~~
This tutorial builds an infrastructure containing a single node. The node will receive information (i.e. a message string) through contextualisation. The node will store this information in ``/tmp`` directory.

**Features**

 - creating a node with basic contextualisation
 - using the occi resource handler

**Prerequisites**

 - accessing an OCCI cloud through its OCCI interface (endpoint, X.509 VOMS proxy)
 - target cloud contains a base OS image with cloud-init support (os_tpl, resource_tpl)
 - properly installed occi command-line client utility (occi command)

**Download**

You can download the example as `tutorial.examples.occi-helloworld <../../examples/occi-helloworld.tgz>`_ .

**Steps**

#. Edit ``nodes/node_definitions.yaml``. For ``occi_helloworld_node`` set the followings in its ``resource`` section:

   - ``endpoint`` is an url of an Occi interface of a cloud (e.g. `https://carach5.ics.muni.cz:11443`) stored in the EGI AppDB. 
   - ``os_tpl`` is an image identifier for Occi (e.g. `os_tpl#uuid_egi_ubuntu_server_14_04_lts_fedcloud_warg_131`) stored in the EGI AppDB. Select an image containing a base os installation with cloud-init support!
   - ``resource_tpl`` is the instance type in Occi (e.g. `http://fedcloud.egi.eu/occi/compute/flavour/1.0#medium`) stored in the EGI AppDB.
   - ``link``  specifies the network (e.g. `https://carach5.ics.muni.cz:11443/network/24` and/or storage resources to be attached to the VM. 
   - ``public_key`` specifies the path to your ssh public key (e.g. `/home/user/.ssh/authorized_keys`) to be deployed on the target VM.

   For further explanation, read the :ref:`node definition's resource section <userdefinitionresourcesection>` of the User Guide. 

   .. code::

     'node_def:occi_helloworld_node':
         -
             resource:
                 type: occi
                 endpoint: replace_with_endpoint_of_occi_interface_from_egi_appdb
                 os_tpl: replace_with_occi_id_from_egi_appdb
                 resource_tpl: replace_with_template_id_from_egi_appdb
                 link:
                     -
                         replace_with_public_network_identifier_or_remove
                 public_key: replace_with_path_to_your_ssh_public_key

#. Make sure your authentication information is set correctly in your authentication file. You must set the path of your VOMS proxy in the authentication file. Setting authentication information is described :ref:`here <authentication>`.

#. Load the node definition for ``occi_helloworld_node`` node into the database. 
  
   .. important::

      Occopus takes node definitions from its database when builds up the infrastructure, so importing is necessary whenever the node definition or any imported (e.g. contextualisation) file changes!
   
   .. code::

      occopus-import nodes/node_definitions.yaml

#. Start deploying the infrastructure. Make sure the proper virtualenv is activated!

   .. code::

      occopus-build infra-occi-helloworld.yaml 

#. After successful finish, the node with ``ip address`` and ``node id`` are listed at the end of the logging messages and the identifier of the newly built infrastructure is printed. You can store the identifier of the infrastructure to perform further operations on your infra or alternatively you can query the identifier using the **occopus-maintain** command.

   .. code::

      List of nodes/ip addresses:
      helloworld:
          192.168.xxx.xxx (3116eaf5-89e7-405f-ab94-9550ba1d0a7c)
      14032858-d628-40a2-b611-71381bd463fa

#. Check the result on your virtual machine.

   .. code::
        
      ssh ...
      # cat /tmp/helloworld.txt
      Hello World! I have been created by Occopus

#. Finally, you may destroy the infrastructure using the infrastructure id returned by ``occopus-build``.

   .. code::

      occopus-destroy -i 14032858-d628-40a2-b611-71381bd463fa

OCCI-Ping
~~~~~~~~~
This tutorial builds an infrastructure containing two nodes. The ping-sender node will
ping the ping-receiver node. The sender node will store the outcome of ping in ``/tmp`` directory.

**Features**

 - creating two nodes with dependencies (i.e. ordering of deployment)
 - querying a node's ip address and passing the address to another
 - using the occi resource handler

**Prerequisites**

 - accessing an OCCI cloud through its OCCI interface (endpoint, X.509 VOMS proxy)
 - target cloud contains a base OS image with cloud-init support (os_tpl, resource_tpl)
 - properly installed occi command-line client utility (occi command)

**Download**

You can download the example as `tutorial.examples.occi-ping <../../examples/occi-ping.tgz>`_ .

**Steps**

#. Edit ``nodes/node_definitions.yaml``. Both, for ``occi_ping_receiver_node`` and for ``occi_ping_sender_node`` set the followings in their ``resource`` section:
   
   - ``endpoint`` is an url of an Occi interface of a cloud (e.g. `https://carach5.ics.muni.cz:11443`) stored in the EGI AppDB. 
   - ``os_tpl`` is an image identifier for Occi (e.g. `os_tpl#uuid_egi_ubuntu_server_14_04_lts_fedcloud_warg_131`) stored in the EGI AppDB. Select an image containing a base os installation with cloud-init support!
   - ``resource_tpl`` is the instance type in Occi (e.g. `http://fedcloud.egi.eu/occi/compute/flavour/1.0#medium`) stored in the EGI AppDB.
   - ``link``  specifies the network (e.g. `https://carach5.ics.muni.cz:11443/network/24` and/or storage resources to be attached to the VM. 
   - ``public_key`` specifies the path to your ssh public key (e.g. `/home/user/.ssh/authorized_keys`) to be deployed on the target VM.

   For further explanation, read the :ref:`node definition's resource section <userdefinitionresourcesection>` of the User Guide. 

   .. code::

     'node_def:occi_ping_receiver_node':
         -
             resource:
                 type: occi
                 endpoint: replace_with_endpoint_of_occi_interface_from_egi_appdb
                 os_tpl: replace_with_occi_id_from_egi_appdb
                 resource_tpl: replace_with_template_id_from_egi_appdb
                 link:
                     -
                         replace_with_public_network_identifier_or_remove
                 public_key: replace_with_path_to_your_ssh_public_key
             ...
     'node_def:occi_ping_sender_node':
         -
             resource:
                 type: occi
                 endpoint: replace_with_endpoint_of_occi_interface_from_egi_appdb
                 os_tpl: replace_with_occi_id_from_egi_appdb
                 resource_tpl: replace_with_template_id_from_egi_appdb
                 link:
                     -
                         replace_with_public_network_identifier_or_remove
                 public_key: replace_with_path_to_your_ssh_public_key
             ...

#. Make sure your authentication information is set correctly in your authentication file. You must set the path of your VOMS proxy in the authentication file. Setting authentication information is described :ref:`here <authentication>`.


#. Load the node definition for ``occi_ping_receiver_node`` and ``occi_ping_sender_node`` nodes into the database.
   
   .. important::

      Occopus takes node definitions from its database when builds up the infrastructure, so importing is necessary whenever the node definition or any imported (e.g. contextualisation) file changes!
   
   .. code::

      occopus-import nodes/node_definitions.yaml

#. Start deploying the infrastructure. Make sure the proper virtualenv is activated!

   .. code::

      occopus-build infra-occi-ping.yaml 

#. After successful finish, the node with ``ip address`` and ``node id`` are listed at the end of the logging messages and the identifier of the newly built infrastructure is printed. You can store the identifier of the infrastructure to perform further operations on your infra or alternatively you can query the identifier using the **occopus-maintain** command.

   .. code::
   
      List of ip addresses:
      ping_receiver:
          192.168.xxx.xxx (f639a4ad-e9cb-478d-8208-9700415b95a4)
      ping_sender:
          192.168.yyy.yyy (99bdeb76-2295-4be7-8f14-969ab9d222b8)

      30f566d1-9945-42be-b603-795d604b362f

#. Check the result on your virtual machine.

   .. code::

      ssh ...
      # cat /tmp/message.txt
      Hello World! I am the sender node created by Occopus.
      # cat /tmp/ping-result.txt
      PING 192.168.xxx.xxx (192.168.xxx.xxx) 56(84) bytes of data.
      64 bytes from 192.168.xxx.xxx: icmp_seq=1 ttl=64 time=2.74 ms
      64 bytes from 192.168.xxx.xxx: icmp_seq=2 ttl=64 time=0.793 ms
      64 bytes from 192.168.xxx.xxx: icmp_seq=3 ttl=64 time=0.865 ms
      64 bytes from 192.168.xxx.xxx: icmp_seq=4 ttl=64 time=0.882 ms
      64 bytes from 192.168.xxx.xxx: icmp_seq=5 ttl=64 time=0.786 ms

      --- 192.168.xxx.xxx ping statistics ---
      5 packets transmitted, 5 received, 0% packet loss, time 4003ms
      rtt min/avg/max/mdev = 0.786/1.215/2.749/0.767 ms

#. Finally, you may destroy the infrastructure using the infrastructure id returned by ``occopus-build``.

   .. code::

      occopus-destroy -i 30f566d1-9945-42be-b603-795d604b362f

Docker-Helloworld
~~~~~~~~~~~~~~~~~
This tutorial builds an infrastructure containing a single node implemented by a Docker container. The node will receive information (i.e. a message string) through contextualisation. The node will store this information in ``/root/message.txt`` file.

**Features**

 - creating a node with basic contextualisation
 - using the docker resource handler

**Prerequisites**

 - accessing a Docker host or a Swarm cluster (endpoint)
 - having a docker image to be instantiated or using the one predefined in this example (origin, image)
 - command to be executed on the image and the required environment variables or using the one predefined in this example (command, environment variables)

 .. important::

    Encrypted connection is not supported yet!

**Download**

You can download the example as `tutorial.examples.docker-helloworld <../../examples/docker-helloworld.tgz>`_ .

**Steps**

#. Edit ``nodes/node_definitions.yaml``. For ``docker_helloworld_node`` set the followings in its ``resource`` section:

   - ``endpoint`` is the endpoint of your docker cluster (e.g. `tcp://1.2.3.4:2375` or `unix://var/run/docker.sock`). 

   For further explanation, read the :ref:`node definition's resource section <userdefinitionresourcesection>` of the User Guide. 

   .. code::

     'node_def:docker_helloworld_node':
	 -
	     resource:
		 type: docker
		 endpoint: replace_with_your_docker_endpoint
		 origin: https://s3.lpds.sztaki.hu/docker/busybox_helloworld.tar
		 image: busybox_helloworld
		 network_mode: bridge
		 tag: latest

#. Make sure your authentication information is set correctly in your authentication file. The docker plugin in Occopus does not apply authentication, however a dummy authentication block is needed. The instructions for setting the authentication properly is described at the :ref:`authentication page <authentication>`. There you can download a default authentication file containing the docker section already.

#. Load the node definition for ``docker_helloworld_node`` node into the database. 

   .. important::

      Occopus takes node definitions from its database when builds up the infrastructure, so importing is necessary whenever the node definition or any imported (e.g. contextualisation) file changes!
   
   .. code::

      occopus-import nodes/node_definitions.yaml

#. Start deploying the infrastructure. Make sure the proper virtualenv is activated!

   .. code::

      occopus-build infra-docker-helloworld.yaml 

#. After successful finish, the node with ``ip address`` and ``node id`` are listed at the end of the logging messages and the identifier of the newly built infrastructure is printed. You can store the identifier of the infrastructure to perform further operations on your infra or alternatively you can query the identifier using the **occopus-maintain** command.

   .. code::

      List of nodes/ip addresses:
      helloworld:
          192.168.xxx.xxx (3116eaf5-89e7-405f-ab94-9550ba1d0a7c)
      14032858-d628-40a2-b611-71381bd463fa

#. Check the result on your virtual machine.

   .. code::
        
        # docker ps
        CONTAINER ID        IMAGE                       COMMAND                  CREATED             STATUS              PORTS               NAMES
        13bb8c94b5f4        busybox_helloworld:latest   "sh -c /root/start.sh"   3 seconds ago       Up 2 seconds                            admiring_joliot

        # docker exec -it 13bb8c94b5f4 cat /root/message.txt
        Hello World! I have been created by Occopus.

#. Finally, you may destroy the infrastructure using the infrastructure id returned by ``occopus-build``.

   .. code::

      occopus-destroy -i 14032858-d628-40a2-b611-71381bd463fa

Docker-Ping
~~~~~~~~~~~
This tutorial builds an infrastructure containing a two nodes implemented by Docker containers. The ping-sender node will ping the ping-receiver node to demonstrate the connection between the two nodes. The sender node will store the outcome of ping in ``/root/ping-result.txt`` file.

**Features**

 - creating two nodes with dependencies (i.e ordering or deployment)
 - querying a node's ip address and passing the address to another
 - using the docker resource handler

**Prerequisites**

 - accessing a Docker host or a Swarm cluster (endpoint)
 - having a docker image to be instantiated or using the one predefined in this example (origin, image)
 - command to be executed on the image and the required environment variables or using the one predefined in this example (command, env)

 .. important::

    Encrypted connection is not supported yet!

**Download**

You can download the example as `tutorial.examples.docker-ping <../../examples/docker-ping.tgz>`_ .

**Steps**

#. Edit ``nodes/node_definitions.yaml``. Both, for ``docker_ping_receiver_node`` and for ``docker_ping_sender_node`` set the followings in their ``resource`` section:
  
   - ``endpoint`` is the endpoint of your docker cluster (e.g. `tcp://1.2.3.4:2375` or `unix://var/run/docker.sock`). 

   For further explanation, read the :ref:`node definition's resource section <userdefinitionresourcesection>` of the User Guide. 

   .. code::

     'node_def:docker_ping_receiver_node':
       -
	     resource:
		 type: docker
		 endpoint: replace_with_your_docker_endpoint
		 origin: https://s3.lpds.sztaki.hu/docker/busybox_helloworld.tar
		 image: busybox_helloworld
		 network_mode: overlaynet
		 tag: latest
             ...
     'node_def:docker_ping_sender_node':
	 -
	     resource:
		 type: docker
		 endpoint: replace_with_your_docker_endpoint
		 origin: https://s3.lpds.sztaki.hu/docker/busybox_ping.tar
		 image: busybox_ping
		 network_mode: overlaynet
		 tag: latest

#. Make sure your authentication information is set correctly in your authentication file. The docker plugin in Occopus does not apply authentication, however a dummy authentication block is needed. Instructions for setting the authentication properly is described at the :ref:`authentication page <authentication>`. There you can download a default authentication file containing the docker section already.

#. Load the node definition for ``docker_ping_receiver_node`` and ``docker_ping_sender_node`` nodes into the database.

   .. important::

      Occopus takes node definitions from its database when builds up the infrastructure, so importing is necessary whenever the node definition or any imported (e.g. contextualisation) file changes!
   
   .. code::

      occopus-import nodes/node_definitions.yaml

#. Start deploying the infrastructure. Make sure the proper virtualenv is activated!

   .. code::

      occopus-build infra-docker-ping.yaml 

#. After successful finish, the nodes with ``ip address`` and ``node id`` are listed at the end of the logging messages and the identifier of the newly built infrastructure is printed. You can store the identifier of the infrastructure to perform further operations on your infra or alternatively you can query the identifier using the **occopus-maintain** command.

   .. code::

      List of nodes/ip addresses:
      ping_receiver:
        10.0.0.2 (552fe5b2-23a6-4c12-a4e2-077521027832)
      ping_sender:
        10.0.0.3 (eabc8d2f-401b-40cf-9386-4739ecd99fbd)    
      14032858-d628-40a2-b611-71381bd463fa

#. Check the result on your virtual machine.

   .. code::

        # ssh ...
        # docker ps
        CONTAINER ID        IMAGE                       COMMAND                  CREATED             STATUS              PORTS               NAMES
        4e83c45e8378        busybox_ping:latest         "sh -c /root/start.sh"   16 seconds ago      Up 15 seconds                           romantic_brown
        10b27bc4d978        busybox_helloworld:latest   "sh -c /root/start.sh"   17 seconds ago      Up 16 seconds                           jovial_mayer

        # docker exec -it 4e83c45e8378 cat /root/ping-result.txt
        PING 172.17.0.2 (172.17.0.2): 56 data bytes
        64 bytes from 172.17.0.2: seq=0 ttl=64 time=0.195 ms
        64 bytes from 172.17.0.2: seq=1 ttl=64 time=0.105 ms
        64 bytes from 172.17.0.2: seq=2 ttl=64 time=0.124 ms
        64 bytes from 172.17.0.2: seq=3 ttl=64 time=0.095 ms
        64 bytes from 172.17.0.2: seq=4 ttl=64 time=0.085 ms

        --- 172.17.0.2 ping statistics ---
        5 packets transmitted, 5 packets received, 0% packet loss
        round-trip min/avg/max = 0.085/0.120/0.195 ms

#. Finally, you may destroy the infrastructure using the infrastructure id returned by ``occopus-build``.

   .. code::

      occopus-destroy -i 14032858-d628-40a2-b611-71381bd463fa
