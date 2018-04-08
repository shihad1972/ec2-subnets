ec2-subnets
=========

Create ec2 subnets in AWS and optionally add them to a routing table

Requirements
------------

As this uses the AWS modules, boto is required for this role. Also, the version
of ansible must be 2.3 or higher for adding the subnets to routing tables

Role Variables
--------------

The following variables need set in this role:

  vpc_name: STRING

            The role will get the vpc_id from the Name of the vpc, so you 
            should add this. Alternatively, you can specify a vpc_id variable

  add_route_table: BOOLEAN

            Define to true if you want the subnets added to a specific routing
            table. You must have defined a tag route_table with the name
            of the routing table you wish to add the subnet to.

  vpc_subnets: List of dicts

            Data definition of the subnets. Requires cidr, az and a dict of
            res_tags. See below for further details
  region: AWS region

Dependencies
------------

The role has no external dependencies.

Example Playbook
----------------
This will create 2 subnets with no routing table addition for the "Booking"
application. The term Application is used to signify these subnets are not
internet facing.

    - hosts: localhost
      vars:
        vpc_name: OAT-Production
        region: eu-west-1
        vpc_subnets:
          - state: present
            az: eu-west-1a
            cidr: 172.21.20.0/24
            res_tags:
              Name: eu-west-1a Booking Application
              Role: Application
              Stack: Booking
          - state: present
            az: eu-west-1b
            cidr: 172.21.21.0/24
            res_tags:
              Name: eu-west-1b Booking Application
              Role: Application
              Stack: Booking
      roles:
         - { role: ec2-subnets }

The next example creates 2 subnets, adding them to the "eu-west-1 Presentation" routing
table

    - hosts: localhost
      vars:
        vpc_name: OAT-Production
        add_route_table: true
        routing_table: "eu-west-1 Presentation"
        vpc_subnets:
          - state: present
            az: eu-west-1a
            cidr: 172.21.20.0/24
            res_tags:
              Name: eu-west-1a Booking Presentation
              Role: Presentation
              Stack: Booking
              route_table: "{{ routing_table }}"
          - state: present
            az: eu-west-1b
            cidr: 172.21.21.0/24
            res_tags:
              Name: eu-west-1b Booking Presentation
              Role: Presentation
              Stack: Booking
              route_table: "{{ routing_table }}"
      roles:
         - { role: ec2-subnets }

License
-------

GPL

Author Information
------------------

Iain M Conochie <iain@shihad.org>

