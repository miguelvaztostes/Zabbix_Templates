# Template Net HP Comware (HH3C)
zabbix_export:
  version: '6.2'
  date: '2022-12-08T18:31:11Z'
  template_groups:
    -
      uuid: 36bff6c29af64692839d077febfc7079
      name: 'Templates/Network devices'
  templates:
    -
      uuid: 57aeccd43b744942b9555269b79a96ad
      template: 'HP Comware HH3C SNMP'
      name: 'HP Comware HH3C SNMP'
      description: |
        Template Net HP Comware (HH3C)
        
        MIBs used:
        EtherLike-MIB
        SNMPv2-MIB
        ENTITY-MIB
        HH3C-ENTITY-EXT-MIB
        IF-MIB
        
        Known Issues:
        
          Description: No temperature sensors. All entities of them return 0 for HH3C-ENTITY-EXT-MIB::hh3cEntityExtTemperature
          Version: 1910-48 Switch Software Version 5.20.99, Release 1116 Copyright(c)2010-2016 Hewlett Packard Enterprise Development LP
          Device: HP 1910-48
        
        Template tooling version used: 0.41
      groups:
        -
          name: 'Templates/Network devices'
      items:
        -
          uuid: fbefc108101b48e298a01037d5b3db50
          name: 'ICMP ping'
          type: SIMPLE
          key: icmpping
          history: 1w
          valuemap:
            name: 'Service state'
          tags:
            -
              tag: component
              value: health
            -
              tag: component
              value: network
          triggers:
            -
              uuid: 1bd6a4748e29498e99ccd3b23d83cdf7
              expression: 'max(/HP Comware HH3C SNMP/icmpping,#3)=0'
              name: 'Unavailable by ICMP ping'
              priority: HIGH
              description: 'Last three attempts returned timeout.  Please check device connectivity.'
              manual_close: 'YES'
              tags:
                -
                  tag: scope
                  value: availability
        -
          uuid: 49b86b6d17394b859b02ea79c6d28b30
          name: 'ICMP loss'
          type: SIMPLE
          key: icmppingloss
          history: 1w
          value_type: FLOAT
          units: '%'
          tags:
            -
              tag: component
              value: health
            -
              tag: component
              value: network
          triggers:
            -
              uuid: d41edaea369d40d2944af72a3ee76850
              expression: 'min(/HP Comware HH3C SNMP/icmppingloss,5m)>{$ICMP_LOSS_WARN} and min(/HP Comware HH3C SNMP/icmppingloss,5m)<100'
              name: 'High ICMP ping loss'
              opdata: 'Loss: {ITEM.LASTVALUE1}'
              priority: WARNING
              dependencies:
                -
                  name: 'Unavailable by ICMP ping'
                  expression: 'max(/HP Comware HH3C SNMP/icmpping,#3)=0'
              tags:
                -
                  tag: scope
                  value: availability
                -
                  tag: scope
                  value: performance
        -
          uuid: 1560f8f1509040a783e4149845ce38a3
          name: 'ICMP response time'
          type: SIMPLE
          key: icmppingsec
          history: 1w
          value_type: FLOAT
          units: s
          tags:
            -
              tag: component
              value: health
            -
              tag: component
              value: network
          triggers:
            -
              uuid: 2698115b6f014e8cbdb11c2d5eeb8f08
              expression: 'avg(/HP Comware HH3C SNMP/icmppingsec,5m)>{$ICMP_RESPONSE_TIME_WARN}'
              name: 'High ICMP ping response time'
              opdata: 'Value: {ITEM.LASTVALUE1}'
              priority: WARNING
              dependencies:
                -
                  name: 'High ICMP ping loss'
                  expression: 'min(/HP Comware HH3C SNMP/icmppingloss,5m)>{$ICMP_LOSS_WARN} and min(/HP Comware HH3C SNMP/icmppingloss,5m)<100'
                -
                  name: 'Unavailable by ICMP ping'
                  expression: 'max(/HP Comware HH3C SNMP/icmpping,#3)=0'
              tags:
                -
                  tag: scope
                  value: availability
                -
                  tag: scope
                  value: performance
        -
          uuid: fa164b01a218494dab454126b4f561a9
          name: 'SNMP traps (fallback)'
          type: SNMP_TRAP
          key: snmptrap.fallback
          history: 2w
          trends: '0'
          value_type: LOG
          description: 'The item is used to collect all SNMP traps unmatched by other snmptrap items'
          logtimefmt: 'hh:mm:sszyyyy/MM/dd'
          tags:
            -
              tag: component
              value: network
        -
          uuid: 6f340c49d4364b8096a49c3fa52e2745
          name: 'System contact details'
          type: SNMP_AGENT
          snmp_oid: 1.3.6.1.2.1.1.4.0
          key: 'system.contact[sysContact.0]'
          delay: 15m
          history: 2w
          trends: '0'
          value_type: CHAR
          description: |
            MIB: SNMPv2-MIB
            The textual identification of the contact person for this managed node, together with information on how to contact this person.  If no contact information is known, the value is the zero-length string.
          inventory_link: CONTACT
          preprocessing:
            -
              type: DISCARD_UNCHANGED_HEARTBEAT
              parameters:
                - 12h
          tags:
            -
              tag: component
              value: system
        -
          uuid: 85e404c0181143f28b1187acb08cfa04
          name: 'System description'
          type: SNMP_AGENT
          snmp_oid: 1.3.6.1.2.1.1.1.0
          key: 'system.descr[sysDescr.0]'
          delay: 15m
          history: 2w
          trends: '0'
          value_type: CHAR
          description: |
            MIB: SNMPv2-MIB
            A textual description of the entity. This value should
            include the full name and version identification of the system's hardware type, software operating-system, and
            networking software.
          preprocessing:
            -
              type: DISCARD_UNCHANGED_HEARTBEAT
              parameters:
                - 12h
          tags:
            -
              tag: component
              value: system
        -
          uuid: 3e4feed03de941c98e88e17b3e81b5e4
          name: 'System location'
          type: SNMP_AGENT
          snmp_oid: 1.3.6.1.2.1.1.6.0
          key: 'system.location[sysLocation.0]'
          delay: 15m
          history: 2w
          trends: '0'
          value_type: CHAR
          description: |
            MIB: SNMPv2-MIB
            The physical location of this node (e.g., `telephone closet, 3rd floor').  If the location is unknown, the value is the zero-length string.
          inventory_link: LOCATION
          preprocessing:
            -
              type: DISCARD_UNCHANGED_HEARTBEAT
              parameters:
                - 12h
          tags:
            -
              tag: component
              value: system
        -
          uuid: b57179ad3e5f41f5805e93ff9e68d631
          name: 'System name'
          type: SNMP_AGENT
          snmp_oid: 1.3.6.1.2.1.1.5.0
          key: system.name
          delay: 15m
          history: 2w
          trends: '0'
          value_type: CHAR
          description: |
            MIB: SNMPv2-MIB
            An administratively-assigned name for this managed node.By convention, this is the node's fully-qualified domain name.  If the name is unknown, the value is the zero-length string.
          inventory_link: NAME
          preprocessing:
            -
              type: DISCARD_UNCHANGED_HEARTBEAT
              parameters:
                - 12h
          tags:
            -
              tag: component
              value: system
          triggers:
            -
              uuid: cf1d0aa58a194904902899dbba814514
              expression: 'last(/HP Comware HH3C SNMP/system.name,#1)<>last(/HP Comware HH3C SNMP/system.name,#2) and length(last(/HP Comware HH3C SNMP/system.name))>0'
              name: 'System name has changed (new name: {ITEM.VALUE})'
              priority: INFO
              description: 'System name has changed. Ack to close.'
              manual_close: 'YES'
              tags:
                -
                  tag: scope
                  value: notice
                -
                  tag: scope
                  value: security
        -
          uuid: 72139a34e0cf4357b86a52cf8430ee8c
          name: 'System object ID'
          type: SNMP_AGENT
          snmp_oid: 1.3.6.1.2.1.1.2.0
          key: 'system.objectid[sysObjectID.0]'
          delay: 15m
          history: 2w
          trends: '0'
          value_type: CHAR
          description: |
            MIB: SNMPv2-MIB
            The vendor's authoritative identification of the network management subsystem contained in the entity.  This value is allocated within the SMI enterprises subtree (1.3.6.1.4.1) and provides an easy and unambiguous means for determining`what kind of box' is being managed.  For example, if vendor`Flintstones, Inc.' was assigned the subtree1.3.6.1.4.1.4242, it could assign the identifier 1.3.6.1.4.1.4242.1.1 to its `Fred Router'.
          preprocessing:
            -
              type: DISCARD_UNCHANGED_HEARTBEAT
              parameters:
                - 12h
          tags:
            -
              tag: component
              value: system
        -
          uuid: ac039d3041cc439eb1c3184ed78554d0
          name: Uptime
          type: SNMP_AGENT
          snmp_oid: 1.3.6.1.2.1.1.3.0
          key: 'system.uptime[sysUpTime.0]'
          delay: 30s
          history: 2w
          trends: 0d
          units: uptime
          description: |
            MIB: SNMPv2-MIB
            The time (in hundredths of a second) since the network management portion of the system was last re-initialized.
          preprocessing:
            -
              type: MULTIPLIER
              parameters:
                - '0.01'
          tags:
            -
              tag: component
              value: system
          triggers:
            -
              uuid: c1372deba2b148f4ac8be6493f0b9868
              expression: 'last(/HP Comware HH3C SNMP/system.uptime[sysUpTime.0])<10m'
              name: '{HOST.NAME} has been restarted (uptime < 10m)'
              priority: WARNING
              description: 'Uptime is less than 10 minutes'
              manual_close: 'YES'
              dependencies:
                -
                  name: 'No SNMP data collection'
                  expression: 'max(/HP Comware HH3C SNMP/zabbix[host,snmp,available],{$SNMP.TIMEOUT})=0'
              tags:
                -
                  tag: scope
                  value: notice
        -
          uuid: c407deea6e0f41b18d84ccbe7fcbca63
          name: 'SNMP agent availability'
          type: INTERNAL
          key: 'zabbix[host,snmp,available]'
          history: 7d
          description: |
            Availability of SNMP checks on the host. The value of this item corresponds to availability icons in the host list.
            Possible value:
            0 - not available
            1 - available
            2 - unknown
          valuemap:
            name: zabbix.host.available
          tags:
            -
              tag: component
              value: health
            -
              tag: component
              value: network
          triggers:
            -
              uuid: bc6e9c399c3d4d43aaeb5a1b03deb7d4
              expression: 'max(/HP Comware HH3C SNMP/zabbix[host,snmp,available],{$SNMP.TIMEOUT})=0'
              name: 'No SNMP data collection'
              opdata: 'Current state: {ITEM.LASTVALUE1}'
              priority: WARNING
              description: 'SNMP is not available for polling. Please check device connectivity and SNMP settings.'
              manual_close: 'YES'
              dependencies:
                -
                  name: 'Unavailable by ICMP ping'
                  expression: 'max(/HP Comware HH3C SNMP/icmpping,#3)=0'
              tags:
                -
                  tag: scope
                  value: availability
      discovery_rules:
        -
          uuid: ab903dd9cb4b49daba7a1bb0c2c65a1f
          name: 'Entity Discovery'
          type: SNMP_AGENT
          snmp_oid: 'discovery[{#ENT_CLASS},1.3.6.1.2.1.47.1.1.1.1.5,{#ENT_NAME},1.3.6.1.2.1.47.1.1.1.1.7]'
          key: entity.discovery
          delay: 1h
          filter:
            conditions:
              -
                macro: '{#ENT_CLASS}'
                value: '3'
                formulaid: A
          item_prototypes:
            -
              uuid: 88a392e093444292b3c58c5f9d5305a5
              name: '{#ENT_NAME}: Firmware version'
              type: SNMP_AGENT
              snmp_oid: '1.3.6.1.2.1.47.1.1.1.1.9.{#SNMPINDEX}'
              key: 'system.hw.firmware[entPhysicalFirmwareRev.{#SNMPINDEX}]'
              delay: 1h
              history: 2w
              trends: '0'
              value_type: CHAR
              description: 'MIB: ENTITY-MIB'
              preprocessing:
                -
                  type: DISCARD_UNCHANGED_HEARTBEAT
                  parameters:
                    - 1d
              tags:
                -
                  tag: component
                  value: system
              trigger_prototypes:
                -
                  uuid: 17e0f72418f4432d852b5c39ddf675bd
                  expression: 'last(/HP Comware HH3C SNMP/system.hw.firmware[entPhysicalFirmwareRev.{#SNMPINDEX}],#1)<>last(/HP Comware HH3C SNMP/system.hw.firmware[entPhysicalFirmwareRev.{#SNMPINDEX}],#2) and length(last(/HP Comware HH3C SNMP/system.hw.firmware[entPhysicalFirmwareRev.{#SNMPINDEX}]))>0'
                  name: '{#ENT_NAME}: Firmware has changed'
                  opdata: 'Current value: {ITEM.LASTVALUE1}'
                  priority: INFO
                  description: 'Firmware version has changed. Ack to close'
                  manual_close: 'YES'
                  tags:
                    -
                      tag: scope
                      value: notice
            -
              uuid: cc241e313bde48e1bd23f3eefb667439
              name: '{#ENT_NAME}: Hardware model name'
              type: SNMP_AGENT
              snmp_oid: '1.3.6.1.2.1.47.1.1.1.1.2.{#SNMPINDEX}'
              key: 'system.hw.model[entPhysicalDescr.{#SNMPINDEX}]'
              delay: 1h
              history: 2w
              trends: '0'
              value_type: CHAR
              description: 'MIB: ENTITY-MIB'
              preprocessing:
                -
                  type: DISCARD_UNCHANGED_HEARTBEAT
                  parameters:
                    - 1d
              tags:
                -
                  tag: component
                  value: system
            -
              uuid: 9ebc80e149fc4db7a4fb7df2322aad86
              name: '{#ENT_NAME}: Hardware serial number'
              type: SNMP_AGENT
              snmp_oid: '1.3.6.1.2.1.47.1.1.1.1.11.{#SNMPINDEX}'
              key: 'system.hw.serialnumber[entPhysicalSerialNum.{#SNMPINDEX}]'
              delay: 1h
              history: 2w
              trends: '0'
              value_type: CHAR
              description: 'MIB: ENTITY-MIB'
              preprocessing:
                -
                  type: DISCARD_UNCHANGED_HEARTBEAT
                  parameters:
                    - 1d
              tags:
                -
                  tag: component
                  value: system
              trigger_prototypes:
                -
                  uuid: d3633f4584344570a1c0570f0b082590
                  expression: 'last(/HP Comware HH3C SNMP/system.hw.serialnumber[entPhysicalSerialNum.{#SNMPINDEX}],#1)<>last(/HP Comware HH3C SNMP/system.hw.serialnumber[entPhysicalSerialNum.{#SNMPINDEX}],#2) and length(last(/HP Comware HH3C SNMP/system.hw.serialnumber[entPhysicalSerialNum.{#SNMPINDEX}]))>0'
                  name: '{#ENT_NAME}: Device has been replaced (new serial number received)'
                  priority: INFO
                  description: 'Device serial number has changed. Ack to close'
                  manual_close: 'YES'
                  tags:
                    -
                      tag: scope
                      value: notice
            -
              uuid: a63db3df3939447397df5bf9a58edde1
              name: '{#ENT_NAME}: Hardware version(revision)'
              type: SNMP_AGENT
              snmp_oid: '1.3.6.1.2.1.47.1.1.1.1.8.{#SNMPINDEX}'
              key: 'system.hw.version[entPhysicalHardwareRev.{#SNMPINDEX}]'
              delay: 1h
              history: 2w
              trends: '0'
              value_type: CHAR
              description: 'MIB: ENTITY-MIB'
              preprocessing:
                -
                  type: DISCARD_UNCHANGED_HEARTBEAT
                  parameters:
                    - 1d
              tags:
                -
                  tag: component
                  value: system
            -
              uuid: a54f57e58f494300b99161b1fb8f2edf
              name: '{#ENT_NAME}: Operating system'
              type: SNMP_AGENT
              snmp_oid: '1.3.6.1.2.1.47.1.1.1.1.10.{#SNMPINDEX}'
              key: 'system.sw.os[entPhysicalSoftwareRev.{#SNMPINDEX}]'
              delay: 1h
              history: 2w
              trends: '0'
              value_type: CHAR
              description: 'MIB: ENTITY-MIB'
              preprocessing:
                -
                  type: DISCARD_UNCHANGED_HEARTBEAT
                  parameters:
                    - 1d
              tags:
                -
                  tag: component
                  value: os
              trigger_prototypes:
                -
                  uuid: 9e6cccf7daec4405ac3bb1a27670fcee
                  expression: 'last(/HP Comware HH3C SNMP/system.sw.os[entPhysicalSoftwareRev.{#SNMPINDEX}],#1)<>last(/HP Comware HH3C SNMP/system.sw.os[entPhysicalSoftwareRev.{#SNMPINDEX}],#2) and length(last(/HP Comware HH3C SNMP/system.sw.os[entPhysicalSoftwareRev.{#SNMPINDEX}]))>0'
                  name: '{#ENT_NAME}: Operating system description has changed'
                  priority: INFO
                  description: 'Operating system description has changed. Possible reasons that system has been updated or replaced. Ack to close.'
                  manual_close: 'YES'
                  dependencies:
                    -
                      name: 'System name has changed (new name: {ITEM.VALUE})'
                      expression: 'last(/HP Comware HH3C SNMP/system.name,#1)<>last(/HP Comware HH3C SNMP/system.name,#2) and length(last(/HP Comware HH3C SNMP/system.name))>0'
                  tags:
                    -
                      tag: scope
                      value: notice
        -
          uuid: fdd2fcda49ab4f00861f1a2dcee1dcad
          name: 'FAN Discovery'
          type: SNMP_AGENT
          snmp_oid: 'discovery[{#ENT_CLASS},1.3.6.1.2.1.47.1.1.1.1.5,{#ENT_NAME},1.3.6.1.2.1.47.1.1.1.1.7,{#ENT_DESCR},1.3.6.1.2.1.47.1.1.1.1.2]'
          key: fan.discovery
          delay: 1h
          filter:
            conditions:
              -
                macro: '{#ENT_CLASS}'
                value: '7'
                formulaid: A
          description: 'Discovering all entities of PhysicalClass - 7: fan(7)'
          item_prototypes:
            -
              uuid: 9cacbc1e037a4c0abdf0862014484a3c
              name: '{#ENT_NAME}: Fan status'
              type: SNMP_AGENT
              snmp_oid: '1.3.6.1.4.1.25506.2.6.1.1.1.1.19.{#SNMPINDEX}'
              key: 'sensor.fan.status[hh3cEntityExtErrorStatus.{#SNMPINDEX}]'
              delay: 3m
              history: 2w
              trends: 0d
              description: |
                MIB: HH3C-ENTITY-EXT-MIB
                Indicate the error state of this entity object.
                fanError(41) means that the fan stops working.
              valuemap:
                name: 'HH3C-ENTITY-EXT-MIB::hh3cEntityExtErrorStatus'
              tags:
                -
                  tag: component
                  value: fan
              trigger_prototypes:
                -
                  uuid: b0821c8e75e14e53906c3a3f209a2785
                  expression: 'count(/HP Comware HH3C SNMP/sensor.fan.status[hh3cEntityExtErrorStatus.{#SNMPINDEX}],#1,"eq","{$FAN_CRIT_STATUS:\"fanError\"}")=1 or count(/HP Comware HH3C SNMP/sensor.fan.status[hh3cEntityExtErrorStatus.{#SNMPINDEX}],#1,"eq","{$FAN_CRIT_STATUS:\"hardwareFaulty\"}")=1'
                  name: '{#ENT_NAME}: Fan is in critical state'
                  opdata: 'Current state: {ITEM.LASTVALUE1}'
                  priority: AVERAGE
                  description: 'Please check the fan unit'
                  tags:
                    -
                      tag: scope
                      value: availability
                    -
                      tag: scope
                      value: performance
        -
          uuid: 268421e66ba94cecac8fdeac7dfbffbb
          name: 'Module Discovery'
          type: SNMP_AGENT
          snmp_oid: 'discovery[{#SNMPVALUE},1.3.6.1.2.1.47.1.1.1.1.2,{#MODULE_NAME},1.3.6.1.2.1.47.1.1.1.1.7]'
          key: module.discovery
          delay: 1h
          filter:
            evaltype: OR
            conditions:
              -
                macro: '{#SNMPVALUE}'
                value: '^(MODULE|Module) (LEVEL|level)1$'
                formulaid: A
              -
                macro: '{#SNMPVALUE}'
                value: '(Fabric|FABRIC) (.+) (Module|MODULE)'
                formulaid: B
          description: 'Filter limits results to ''Module level1'' or Fabric Modules'
          item_prototypes:
            -
              uuid: edbaa4c5e6e744b3bd4d7ab85b42c1ca
              name: '{#MODULE_NAME}: CPU utilization'
              type: SNMP_AGENT
              snmp_oid: '1.3.6.1.4.1.25506.2.6.1.1.1.1.6.{#SNMPINDEX}'
              key: 'system.cpu.util[hh3cEntityExtCpuUsage.{#SNMPINDEX}]'
              history: 7d
              value_type: FLOAT
              units: '%'
              description: |
                MIB: HH3C-ENTITY-EXT-MIB
                The CPU usage for this entity. Generally, the CPU usage
                will calculate the overall CPU usage on the entity, and it
                is not sensible with the number of CPU on the entity
              tags:
                -
                  tag: component
                  value: cpu
              trigger_prototypes:
                -
                  uuid: 37a4a248c89240fe82c5fd6622123aa0
                  expression: 'min(/HP Comware HH3C SNMP/system.cpu.util[hh3cEntityExtCpuUsage.{#SNMPINDEX}],5m)>{$CPU.UTIL.CRIT}'
                  name: '{#MODULE_NAME}: High CPU utilization (over {$CPU.UTIL.CRIT}% for 5m)'
                  opdata: 'Current utilization: {ITEM.LASTVALUE1}'
                  priority: WARNING
                  description: 'CPU utilization is too high. The system might be slow to respond.'
                  tags:
                    -
                      tag: scope
                      value: performance
            -
              uuid: 3021126f556f4c62b335c8108ba1cff1
              name: '{#MODULE_NAME}: Memory utilization'
              type: SNMP_AGENT
              snmp_oid: '1.3.6.1.4.1.25506.2.6.1.1.1.1.8.{#SNMPINDEX}'
              key: 'vm.memory.util[hh3cEntityExtMemUsage.{#SNMPINDEX}]'
              history: 7d
              value_type: FLOAT
              units: '%'
              description: |
                MIB: HH3C-ENTITY-EXT-MIB
                The memory usage for the entity. This object indicates what
                percent of memory are used.
              tags:
                -
                  tag: component
                  value: memory
              trigger_prototypes:
                -
                  uuid: e8d3cb45afea4cbd935c9ea668746260
                  expression: 'min(/HP Comware HH3C SNMP/vm.memory.util[hh3cEntityExtMemUsage.{#SNMPINDEX}],5m)>{$MEMORY.UTIL.MAX}'
                  name: '{#MODULE_NAME}: High memory utilization (>{$MEMORY.UTIL.MAX}% for 5m)'
                  priority: AVERAGE
                  description: 'The system is running out of free memory.'
                  tags:
                    -
                      tag: scope
                      value: capacity
                    -
                      tag: scope
                      value: performance
          graph_prototypes:
            -
              uuid: 2f7801aa39bd486bb9d2ff751c98559b
              name: '{#MODULE_NAME}: CPU utilization'
              ymin_type_1: FIXED
              ymax_type_1: FIXED
              graph_items:
                -
                  drawtype: GRADIENT_LINE
                  color: 1A7C11
                  item:
                    host: 'HP Comware HH3C SNMP'
                    key: 'system.cpu.util[hh3cEntityExtCpuUsage.{#SNMPINDEX}]'
            -
              uuid: ff71369f73174bc0baf98f6da6f82d97
              name: '{#MODULE_NAME}: Memory utilization'
              ymin_type_1: FIXED
              ymax_type_1: FIXED
              graph_items:
                -
                  drawtype: GRADIENT_LINE
                  color: 1A7C11
                  item:
                    host: 'HP Comware HH3C SNMP'
                    key: 'vm.memory.util[hh3cEntityExtMemUsage.{#SNMPINDEX}]'
        -
          uuid: ef05164a28be4a37950e7edc17dd389b
          name: 'Network interfaces discovery'
          type: SNMP_AGENT
          snmp_oid: 'discovery[{#IFOPERSTATUS},1.3.6.1.2.1.2.2.1.8,{#IFADMINSTATUS},1.3.6.1.2.1.2.2.1.7,{#IFALIAS},1.3.6.1.2.1.31.1.1.1.18,{#IFNAME},1.3.6.1.2.1.31.1.1.1.1,{#IFDESCR},1.3.6.1.2.1.2.2.1.2,{#IFTYPE},1.3.6.1.2.1.2.2.1.3]'
          key: net.if.discovery
          delay: 1h
          filter:
            evaltype: AND
            conditions:
              -
                macro: '{#IFADMINSTATUS}'
                value: '{$NET.IF.IFADMINSTATUS.MATCHES}'
                formulaid: A
              -
                macro: '{#IFADMINSTATUS}'
                value: '{$NET.IF.IFADMINSTATUS.NOT_MATCHES}'
                operator: NOT_MATCHES_REGEX
                formulaid: B
              -
                macro: '{#IFOPERSTATUS}'
                value: '{$NET.IF.IFOPERSTATUS.MATCHES}'
                formulaid: I
              -
                macro: '{#IFOPERSTATUS}'
                value: '{$NET.IF.IFOPERSTATUS.NOT_MATCHES}'
                operator: NOT_MATCHES_REGEX
                formulaid: J
              -
                macro: '{#IFNAME}'
                value: '{$NET.IF.IFNAME.MATCHES}'
                formulaid: G
              -
                macro: '{#IFNAME}'
                value: '{$NET.IF.IFNAME.NOT_MATCHES}'
                operator: NOT_MATCHES_REGEX
                formulaid: H
              -
                macro: '{#IFDESCR}'
                value: '{$NET.IF.IFDESCR.MATCHES}'
                formulaid: E
              -
                macro: '{#IFDESCR}'
                value: '{$NET.IF.IFDESCR.NOT_MATCHES}'
                operator: NOT_MATCHES_REGEX
                formulaid: F
              -
                macro: '{#IFALIAS}'
                value: '{$NET.IF.IFALIAS.MATCHES}'
                formulaid: C
              -
                macro: '{#IFALIAS}'
                value: '{$NET.IF.IFALIAS.NOT_MATCHES}'
                operator: NOT_MATCHES_REGEX
                formulaid: D
              -
                macro: '{#IFTYPE}'
                value: '{$NET.IF.IFTYPE.MATCHES}'
                formulaid: K
              -
                macro: '{#IFTYPE}'
                value: '{$NET.IF.IFTYPE.NOT_MATCHES}'
                operator: NOT_MATCHES_REGEX
                formulaid: L
          description: 'Discovering interfaces from IF-MIB.'
          item_prototypes:
            -
              uuid: 22cd0f5dd18b4d68ba2695a681819c45
              name: 'Interface {#IFNAME}({#IFALIAS}): Inbound packets discarded'
              type: SNMP_AGENT
              snmp_oid: '1.3.6.1.2.1.2.2.1.13.{#SNMPINDEX}'
              key: 'net.if.in.discards[ifInDiscards.{#SNMPINDEX}]'
              delay: 3m
              history: 7d
              description: |
                MIB: IF-MIB
                The number of inbound packets which were chosen to be discarded
                even though no errors had been detected to prevent their being deliverable to a higher-layer protocol.
                One possible reason for discarding such a packet could be to free up buffer space.
                Discontinuities in the value of this counter can occur at re-initialization of the management system,
                and at other times as indicated by the value of ifCounterDiscontinuityTime.
              preprocessing:
                -
                  type: CHANGE_PER_SECOND
                  parameters:
                    - ''
              tags:
                -
                  tag: component
                  value: network
                -
                  tag: description
                  value: '{#IFALIAS}'
                -
                  tag: interface
                  value: '{#IFNAME}'
            -
              uuid: 442bc76f9f4441f6b3c7ff60b9359e2b
              name: 'Interface {#IFNAME}({#IFALIAS}): Inbound packets with errors'
              type: SNMP_AGENT
              snmp_oid: '1.3.6.1.2.1.2.2.1.14.{#SNMPINDEX}'
              key: 'net.if.in.errors[ifInErrors.{#SNMPINDEX}]'
              delay: 3m
              history: 7d
              description: |
                MIB: IF-MIB
                For packet-oriented interfaces, the number of inbound packets that contained errors preventing them from being deliverable to a higher-layer protocol.  For character-oriented or fixed-length interfaces, the number of inbound transmission units that contained errors preventing them from being deliverable to a higher-layer protocol. Discontinuities in the value of this counter can occur at re-initialization of the management system, and at other times as indicated by the value of ifCounterDiscontinuityTime.
              preprocessing:
                -
                  type: CHANGE_PER_SECOND
                  parameters:
                    - ''
              tags:
                -
                  tag: component
                  value: network
                -
                  tag: description
                  value: '{#IFALIAS}'
                -
                  tag: interface
                  value: '{#IFNAME}'
            -
              uuid: ac12b0e85f3b49239838c6625d62d8a9
              name: 'Interface {#IFNAME}({#IFALIAS}): Bits received'
              type: SNMP_AGENT
              snmp_oid: '1.3.6.1.2.1.31.1.1.1.6.{#SNMPINDEX}'
              key: 'net.if.in[ifHCInOctets.{#SNMPINDEX}]'
              delay: 3m
              history: 7d
              units: bps
              description: |
                MIB: IF-MIB
                The total number of octets received on the interface, including framing characters. This object is a 64-bit version of ifInOctets. Discontinuities in the value of this counter can occur at re-initialization of the management system, and at other times as indicated by the value of ifCounterDiscontinuityTime.
              preprocessing:
                -
                  type: CHANGE_PER_SECOND
                  parameters:
                    - ''
                -
                  type: MULTIPLIER
                  parameters:
                    - '8'
              tags:
                -
                  tag: component
                  value: network
                -
                  tag: description
                  value: '{#IFALIAS}'
                -
                  tag: interface
                  value: '{#IFNAME}'
            -
              uuid: ed6e19e8821349f380a3c0d4fdd2f4e0
              name: 'Interface {#IFNAME}({#IFALIAS}): Outbound packets discarded'
              type: SNMP_AGENT
              snmp_oid: '1.3.6.1.2.1.2.2.1.19.{#SNMPINDEX}'
              key: 'net.if.out.discards[ifOutDiscards.{#SNMPINDEX}]'
              delay: 3m
              history: 7d
              description: |
                MIB: IF-MIB
                The number of outbound packets which were chosen to be discarded
                even though no errors had been detected to prevent their being deliverable to a higher-layer protocol.
                One possible reason for discarding such a packet could be to free up buffer space.
                Discontinuities in the value of this counter can occur at re-initialization of the management system,
                and at other times as indicated by the value of ifCounterDiscontinuityTime.
              preprocessing:
                -
                  type: CHANGE_PER_SECOND
                  parameters:
                    - ''
              tags:
                -
                  tag: component
                  value: network
                -
                  tag: description
                  value: '{#IFALIAS}'
                -
                  tag: interface
                  value: '{#IFNAME}'
            -
              uuid: d077353aafbb43678bed7dba795923ce
              name: 'Interface {#IFNAME}({#IFALIAS}): Outbound packets with errors'
              type: SNMP_AGENT
              snmp_oid: '1.3.6.1.2.1.2.2.1.20.{#SNMPINDEX}'
              key: 'net.if.out.errors[ifOutErrors.{#SNMPINDEX}]'
              delay: 3m
              history: 7d
              description: |
                MIB: IF-MIB
                For packet-oriented interfaces, the number of outbound packets that contained errors preventing them from being deliverable to a higher-layer protocol.  For character-oriented or fixed-length interfaces, the number of outbound transmission units that contained errors preventing them from being deliverable to a higher-layer protocol. Discontinuities in the value of this counter can occur at re-initialization of the management system, and at other times as indicated by the value of ifCounterDiscontinuityTime.
              preprocessing:
                -
                  type: CHANGE_PER_SECOND
                  parameters:
                    - ''
              tags:
                -
                  tag: component
                  value: network
                -
                  tag: description
                  value: '{#IFALIAS}'
                -
                  tag: interface
                  value: '{#IFNAME}'
            -
              uuid: e76bb270115240d8b0fdb17b1b010418
              name: 'Interface {#IFNAME}({#IFALIAS}): Bits sent'
              type: SNMP_AGENT
              snmp_oid: '1.3.6.1.2.1.31.1.1.1.10.{#SNMPINDEX}'
              key: 'net.if.out[ifHCOutOctets.{#SNMPINDEX}]'
              delay: 3m
              history: 7d
              units: bps
              description: |
                MIB: IF-MIB
                The total number of octets transmitted out of the interface, including framing characters. This object is a 64-bit version of ifOutOctets.Discontinuities in the value of this counter can occur at re-initialization of the management system, and at other times as indicated by the value of ifCounterDiscontinuityTime.
              preprocessing:
                -
                  type: CHANGE_PER_SECOND
                  parameters:
                    - ''
                -
                  type: MULTIPLIER
                  parameters:
                    - '8'
              tags:
                -
                  tag: component
                  value: network
                -
                  tag: description
                  value: '{#IFALIAS}'
                -
                  tag: interface
                  value: '{#IFNAME}'
            -
              uuid: 61b52d6ce511476a8baeaf302476e831
              name: 'Interface {#IFNAME}({#IFALIAS}): Speed'
              type: SNMP_AGENT
              snmp_oid: '1.3.6.1.2.1.31.1.1.1.15.{#SNMPINDEX}'
              key: 'net.if.speed[ifHighSpeed.{#SNMPINDEX}]'
              delay: 5m
              history: 7d
              trends: 0d
              units: bps
              description: |
                MIB: IF-MIB
                An estimate of the interface's current bandwidth in units of 1,000,000 bits per second. If this object reports a value of `n' then the speed of the interface is somewhere in the range of `n-500,000' to`n+499,999'.  For interfaces which do not vary in bandwidth or for those where no accurate estimation can be made, this object should contain the nominal bandwidth. For a sub-layer which has no concept of bandwidth, this object should be zero.
              preprocessing:
                -
                  type: MULTIPLIER
                  parameters:
                    - '1000000'
                -
                  type: DISCARD_UNCHANGED_HEARTBEAT
                  parameters:
                    - 1h
              tags:
                -
                  tag: component
                  value: network
                -
                  tag: description
                  value: '{#IFALIAS}'
                -
                  tag: interface
                  value: '{#IFNAME}'
            -
              uuid: 4391767dc38e4f4fbc1cd2dc186beb82
              name: 'Interface {#IFNAME}({#IFALIAS}): Operational status'
              type: SNMP_AGENT
              snmp_oid: '1.3.6.1.2.1.2.2.1.8.{#SNMPINDEX}'
              key: 'net.if.status[ifOperStatus.{#SNMPINDEX}]'
              history: 7d
              trends: '0'
              description: |
                MIB: IF-MIB
                The current operational state of the interface.
                - The testing(3) state indicates that no operational packet scan be passed
                - If ifAdminStatus is down(2) then ifOperStatus should be down(2)
                - If ifAdminStatus is changed to up(1) then ifOperStatus should change to up(1) if the interface is ready to transmit and receive network traffic
                - It should change todormant(5) if the interface is waiting for external actions (such as a serial line waiting for an incoming connection)
                - It should remain in the down(2) state if and only if there is a fault that prevents it from going to the up(1) state
                - It should remain in the notPresent(6) state if the interface has missing(typically, hardware) components.
              valuemap:
                name: 'IF-MIB::ifOperStatus'
              tags:
                -
                  tag: component
                  value: network
                -
                  tag: description
                  value: '{#IFALIAS}'
                -
                  tag: interface
                  value: '{#IFNAME}'
              trigger_prototypes:
                -
                  uuid: d0a5ea31836946f28abe357440ebdbea
                  expression: '{$IFCONTROL:"{#IFNAME}"}=1 and last(/HP Comware HH3C SNMP/net.if.status[ifOperStatus.{#SNMPINDEX}])=2 and (last(/HP Comware HH3C SNMP/net.if.status[ifOperStatus.{#SNMPINDEX}],#1)<>last(/HP Comware HH3C SNMP/net.if.status[ifOperStatus.{#SNMPINDEX}],#2))'
                  recovery_mode: RECOVERY_EXPRESSION
                  recovery_expression: 'last(/HP Comware HH3C SNMP/net.if.status[ifOperStatus.{#SNMPINDEX}])<>2 or {$IFCONTROL:"{#IFNAME}"}=0'
                  name: 'Interface {#IFNAME}({#IFALIAS}): Link down'
                  opdata: 'Current state: {ITEM.LASTVALUE1}'
                  priority: AVERAGE
                  description: |
                    This trigger expression works as follows:
                    1. Can be triggered if operations status is down.
                    2. {$IFCONTROL:"{#IFNAME}"}=1 - user can redefine Context macro to value - 0. That marks this interface as not important. No new trigger will be fired if this interface is down.
                    3. {TEMPLATE_NAME:METRIC.diff()}=1) - trigger fires only if operational status was up(1) sometime before. (So, do not fire 'ethernal off' interfaces.)
                    
                    WARNING: if closed manually - won't fire again on next poll, because of .diff.
                  manual_close: 'YES'
                  tags:
                    -
                      tag: scope
                      value: availability
            -
              uuid: 2733e5694b544bb98ff981b2ed8371ec
              name: 'Interface {#IFNAME}({#IFALIAS}): Interface type'
              type: SNMP_AGENT
              snmp_oid: '1.3.6.1.2.1.2.2.1.3.{#SNMPINDEX}'
              key: 'net.if.type[ifType.{#SNMPINDEX}]'
              delay: 1h
              history: 7d
              trends: 0d
              description: |
                MIB: IF-MIB
                The type of interface.
                Additional values for ifType are assigned by the Internet Assigned Numbers Authority (IANA),
                through updating the syntax of the IANAifType textual convention.
              valuemap:
                name: 'IF-MIB::ifType'
              preprocessing:
                -
                  type: DISCARD_UNCHANGED_HEARTBEAT
                  parameters:
                    - 1d
              tags:
                -
                  tag: component
                  value: network
                -
                  tag: description
                  value: '{#IFALIAS}'
                -
                  tag: interface
                  value: '{#IFNAME}'
          trigger_prototypes:
            -
              uuid: b672a06043664dafa8f69d7d530a8723
              expression: |
                change(/HP Comware HH3C SNMP/net.if.speed[ifHighSpeed.{#SNMPINDEX}])<0 and last(/HP Comware HH3C SNMP/net.if.speed[ifHighSpeed.{#SNMPINDEX}])>0
                and (
                last(/HP Comware HH3C SNMP/net.if.type[ifType.{#SNMPINDEX}])=6 or
                last(/HP Comware HH3C SNMP/net.if.type[ifType.{#SNMPINDEX}])=7 or
                last(/HP Comware HH3C SNMP/net.if.type[ifType.{#SNMPINDEX}])=11 or
                last(/HP Comware HH3C SNMP/net.if.type[ifType.{#SNMPINDEX}])=62 or
                last(/HP Comware HH3C SNMP/net.if.type[ifType.{#SNMPINDEX}])=69 or
                last(/HP Comware HH3C SNMP/net.if.type[ifType.{#SNMPINDEX}])=117
                )
                and
                (last(/HP Comware HH3C SNMP/net.if.status[ifOperStatus.{#SNMPINDEX}])<>2)
              recovery_mode: RECOVERY_EXPRESSION
              recovery_expression: |
                (change(/HP Comware HH3C SNMP/net.if.speed[ifHighSpeed.{#SNMPINDEX}])>0 and last(/HP Comware HH3C SNMP/net.if.speed[ifHighSpeed.{#SNMPINDEX}],#2)>0) or
                (last(/HP Comware HH3C SNMP/net.if.status[ifOperStatus.{#SNMPINDEX}])=2)
              name: 'Interface {#IFNAME}({#IFALIAS}): Ethernet has changed to lower speed than it was before'
              opdata: 'Current reported speed: {ITEM.LASTVALUE1}'
              priority: INFO
              description: 'This Ethernet connection has transitioned down from its known maximum speed. This might be a sign of autonegotiation issues. Ack to close.'
              manual_close: 'YES'
              dependencies:
                -
                  name: 'Interface {#IFNAME}({#IFALIAS}): Link down'
                  expression: '{$IFCONTROL:"{#IFNAME}"}=1 and last(/HP Comware HH3C SNMP/net.if.status[ifOperStatus.{#SNMPINDEX}])=2 and (last(/HP Comware HH3C SNMP/net.if.status[ifOperStatus.{#SNMPINDEX}],#1)<>last(/HP Comware HH3C SNMP/net.if.status[ifOperStatus.{#SNMPINDEX}],#2))'
                  recovery_expression: 'last(/HP Comware HH3C SNMP/net.if.status[ifOperStatus.{#SNMPINDEX}])<>2 or {$IFCONTROL:"{#IFNAME}"}=0'
              tags:
                -
                  tag: scope
                  value: performance
            -
              uuid: 98c74ed2d41941218802f4c472331f46
              expression: |
                (avg(/HP Comware HH3C SNMP/net.if.in[ifHCInOctets.{#SNMPINDEX}],15m)>({$IF.UTIL.MAX:"{#IFNAME}"}/100)*last(/HP Comware HH3C SNMP/net.if.speed[ifHighSpeed.{#SNMPINDEX}]) or
                avg(/HP Comware HH3C SNMP/net.if.out[ifHCOutOctets.{#SNMPINDEX}],15m)>({$IF.UTIL.MAX:"{#IFNAME}"}/100)*last(/HP Comware HH3C SNMP/net.if.speed[ifHighSpeed.{#SNMPINDEX}])) and
                last(/HP Comware HH3C SNMP/net.if.speed[ifHighSpeed.{#SNMPINDEX}])>0
              recovery_mode: RECOVERY_EXPRESSION
              recovery_expression: |
                avg(/HP Comware HH3C SNMP/net.if.in[ifHCInOctets.{#SNMPINDEX}],15m)<(({$IF.UTIL.MAX:"{#IFNAME}"}-3)/100)*last(/HP Comware HH3C SNMP/net.if.speed[ifHighSpeed.{#SNMPINDEX}]) and
                avg(/HP Comware HH3C SNMP/net.if.out[ifHCOutOctets.{#SNMPINDEX}],15m)<(({$IF.UTIL.MAX:"{#IFNAME}"}-3)/100)*last(/HP Comware HH3C SNMP/net.if.speed[ifHighSpeed.{#SNMPINDEX}])
              name: 'Interface {#IFNAME}({#IFALIAS}): High bandwidth usage (>{$IF.UTIL.MAX:"{#IFNAME}"}%)'
              opdata: 'In: {ITEM.LASTVALUE1}, out: {ITEM.LASTVALUE3}, speed: {ITEM.LASTVALUE2}'
              priority: WARNING
              description: 'The network interface utilization is close to its estimated maximum bandwidth.'
              manual_close: 'YES'
              dependencies:
                -
                  name: 'Interface {#IFNAME}({#IFALIAS}): Link down'
                  expression: '{$IFCONTROL:"{#IFNAME}"}=1 and last(/HP Comware HH3C SNMP/net.if.status[ifOperStatus.{#SNMPINDEX}])=2 and (last(/HP Comware HH3C SNMP/net.if.status[ifOperStatus.{#SNMPINDEX}],#1)<>last(/HP Comware HH3C SNMP/net.if.status[ifOperStatus.{#SNMPINDEX}],#2))'
                  recovery_expression: 'last(/HP Comware HH3C SNMP/net.if.status[ifOperStatus.{#SNMPINDEX}])<>2 or {$IFCONTROL:"{#IFNAME}"}=0'
              tags:
                -
                  tag: scope
                  value: performance
            -
              uuid: d7d8ad2b63ac41e6b84852c312c2a8d9
              expression: |
                min(/HP Comware HH3C SNMP/net.if.in.errors[ifInErrors.{#SNMPINDEX}],5m)>{$IF.ERRORS.WARN:"{#IFNAME}"}
                or min(/HP Comware HH3C SNMP/net.if.out.errors[ifOutErrors.{#SNMPINDEX}],5m)>{$IF.ERRORS.WARN:"{#IFNAME}"}
              recovery_mode: RECOVERY_EXPRESSION
              recovery_expression: |
                max(/HP Comware HH3C SNMP/net.if.in.errors[ifInErrors.{#SNMPINDEX}],5m)<{$IF.ERRORS.WARN:"{#IFNAME}"}*0.8
                and max(/HP Comware HH3C SNMP/net.if.out.errors[ifOutErrors.{#SNMPINDEX}],5m)<{$IF.ERRORS.WARN:"{#IFNAME}"}*0.8
              name: 'Interface {#IFNAME}({#IFALIAS}): High error rate (>{$IF.ERRORS.WARN:"{#IFNAME}"} for 5m)'
              opdata: 'errors in: {ITEM.LASTVALUE1}, errors out: {ITEM.LASTVALUE2}'
              priority: WARNING
              description: 'Recovers when below 80% of {$IF.ERRORS.WARN:"{#IFNAME}"} threshold'
              manual_close: 'YES'
              dependencies:
                -
                  name: 'Interface {#IFNAME}({#IFALIAS}): Link down'
                  expression: '{$IFCONTROL:"{#IFNAME}"}=1 and last(/HP Comware HH3C SNMP/net.if.status[ifOperStatus.{#SNMPINDEX}])=2 and (last(/HP Comware HH3C SNMP/net.if.status[ifOperStatus.{#SNMPINDEX}],#1)<>last(/HP Comware HH3C SNMP/net.if.status[ifOperStatus.{#SNMPINDEX}],#2))'
                  recovery_expression: 'last(/HP Comware HH3C SNMP/net.if.status[ifOperStatus.{#SNMPINDEX}])<>2 or {$IFCONTROL:"{#IFNAME}"}=0'
              tags:
                -
                  tag: scope
                  value: availability
                -
                  tag: scope
                  value: performance
          graph_prototypes:
            -
              uuid: 220602812c8e4507a236a945fe3ec539
              name: 'Interface {#IFNAME}({#IFALIAS}): Network traffic'
              graph_items:
                -
                  drawtype: GRADIENT_LINE
                  color: 1A7C11
                  item:
                    host: 'HP Comware HH3C SNMP'
                    key: 'net.if.in[ifHCInOctets.{#SNMPINDEX}]'
                -
                  sortorder: '1'
                  drawtype: BOLD_LINE
                  color: 2774A4
                  item:
                    host: 'HP Comware HH3C SNMP'
                    key: 'net.if.out[ifHCOutOctets.{#SNMPINDEX}]'
                -
                  sortorder: '2'
                  color: F63100
                  yaxisside: RIGHT
                  item:
                    host: 'HP Comware HH3C SNMP'
                    key: 'net.if.out.errors[ifOutErrors.{#SNMPINDEX}]'
                -
                  sortorder: '3'
                  color: A54F10
                  yaxisside: RIGHT
                  item:
                    host: 'HP Comware HH3C SNMP'
                    key: 'net.if.in.errors[ifInErrors.{#SNMPINDEX}]'
                -
                  sortorder: '4'
                  color: FC6EA3
                  yaxisside: RIGHT
                  item:
                    host: 'HP Comware HH3C SNMP'
                    key: 'net.if.out.discards[ifOutDiscards.{#SNMPINDEX}]'
                -
                  sortorder: '5'
                  color: 6C59DC
                  yaxisside: RIGHT
                  item:
                    host: 'HP Comware HH3C SNMP'
                    key: 'net.if.in.discards[ifInDiscards.{#SNMPINDEX}]'
        -
          uuid: 21d413e388294d288edc95d98eb27980
          name: 'EtherLike-MIB Discovery'
          type: SNMP_AGENT
          snmp_oid: 'discovery[{#SNMPVALUE},1.3.6.1.2.1.10.7.2.1.19,{#IFOPERSTATUS},1.3.6.1.2.1.2.2.1.8,{#IFALIAS},1.3.6.1.2.1.31.1.1.1.18,{#IFNAME},1.3.6.1.2.1.31.1.1.1.1,{#IFDESCR},1.3.6.1.2.1.2.2.1.2]'
          key: net.if.duplex.discovery
          delay: 1h
          filter:
            evaltype: AND
            conditions:
              -
                macro: '{#IFOPERSTATUS}'
                value: '1'
                formulaid: A
              -
                macro: '{#SNMPVALUE}'
                value: (2|3)
                formulaid: B
          description: 'Discovering interfaces from IF-MIB and EtherLike-MIB. Interfaces with up(1) Operational Status are discovered.'
          item_prototypes:
            -
              uuid: ee270fb1f0c54a5c91c581ee28c7a066
              name: 'Interface {#IFNAME}({#IFALIAS}): Duplex status'
              type: SNMP_AGENT
              snmp_oid: '1.3.6.1.2.1.10.7.2.1.19.{#SNMPINDEX}'
              key: 'net.if.duplex[dot3StatsDuplexStatus.{#SNMPINDEX}]'
              history: 7d
              description: |
                MIB: EtherLike-MIB
                The current mode of operation of the MAC
                entity.  'unknown' indicates that the current
                duplex mode could not be determined.
                
                Management control of the duplex mode is
                accomplished through the MAU MIB.  When
                an interface does not support autonegotiation,
                or when autonegotiation is not enabled, the
                duplex mode is controlled using
                ifMauDefaultType.  When autonegotiation is
                supported and enabled, duplex mode is controlled
                using ifMauAutoNegAdvertisedBits.  In either
                case, the currently operating duplex mode is
                reflected both in this object and in ifMauType.
                
                Note that this object provides redundant
                information with ifMauType.  Normally, redundant
                objects are discouraged.  However, in this
                instance, it allows a management application to
                determine the duplex status of an interface
                without having to know every possible value of
                ifMauType.  This was felt to be sufficiently
                valuable to justify the redundancy.
                Reference: [IEEE 802.3 Std.], 30.3.1.1.32,aDuplexStatus.
              valuemap:
                name: 'EtherLike-MIB::dot3StatsDuplexStatus'
              tags:
                -
                  tag: component
                  value: network
                -
                  tag: description
                  value: '{#IFALIAS}'
                -
                  tag: interface
                  value: '{#IFNAME}'
              trigger_prototypes:
                -
                  uuid: 0ff624a6250b431498e4747678149e4b
                  expression: 'last(/HP Comware HH3C SNMP/net.if.duplex[dot3StatsDuplexStatus.{#SNMPINDEX}])=2'
                  name: 'Interface {#IFNAME}({#IFALIAS}): In half-duplex mode'
                  priority: WARNING
                  description: 'Please check autonegotiation settings and cabling'
                  manual_close: 'YES'
                  tags:
                    -
                      tag: scope
                      value: performance
          preprocessing:
            -
              type: JAVASCRIPT
              parameters:
                - |
                  try {
                      var data = JSON.parse(value);
                  }
                  catch (error) {
                      throw 'Failed to parse JSON of EtherLike-MIB discovery.';
                  }
                  var fields = ['{#SNMPVALUE}','{#IFOPERSTATUS}','{#IFALIAS}','{#IFNAME}','{#IFDESCR}'];
                  data.forEach(function (element) {
                      fields.forEach(function (field) {
                          element[field] = element[field] || '';
                      });
                  });
                  return JSON.stringify(data);
        -
          uuid: 75f751bae6e64b9892c9283cefa8db80
          name: 'PSU Discovery'
          type: SNMP_AGENT
          snmp_oid: 'discovery[{#ENT_CLASS},1.3.6.1.2.1.47.1.1.1.1.5,{#ENT_NAME},1.3.6.1.2.1.47.1.1.1.1.7,{#ENT_DESCR},1.3.6.1.2.1.47.1.1.1.1.2]'
          key: psu.discovery
          delay: 1h
          filter:
            conditions:
              -
                macro: '{#ENT_CLASS}'
                value: '6'
                formulaid: A
          description: 'Discovering all entities of PhysicalClass - 6: powerSupply(6)'
          item_prototypes:
            -
              uuid: 1ed2aa667cc94e6f9fb3abb9cb8de9b2
              name: '{#ENT_NAME}: Power supply status'
              type: SNMP_AGENT
              snmp_oid: '1.3.6.1.4.1.25506.2.6.1.1.1.1.19.{#SNMPINDEX}'
              key: 'sensor.psu.status[hh3cEntityExtErrorStatus.{#SNMPINDEX}]'
              delay: 3m
              history: 2w
              trends: 0d
              description: |
                MIB: HH3C-ENTITY-EXT-MIB
                Indicate the error state of this entity object.
                psuError(51) means that the Power Supply Unit is in the state of fault.
                rpsError(61) means the Redundant Power Supply is in the state of fault.
              valuemap:
                name: 'HH3C-ENTITY-EXT-MIB::hh3cEntityExtErrorStatus'
              tags:
                -
                  tag: component
                  value: power
              trigger_prototypes:
                -
                  uuid: 0c3cb88352e2417fb2b80bb78448fb35
                  expression: 'count(/HP Comware HH3C SNMP/sensor.psu.status[hh3cEntityExtErrorStatus.{#SNMPINDEX}],#1,"eq","{$PSU_CRIT_STATUS:\"psuError\"}")=1 or count(/HP Comware HH3C SNMP/sensor.psu.status[hh3cEntityExtErrorStatus.{#SNMPINDEX}],#1,"eq","{$PSU_CRIT_STATUS:\"rpsError\"}")=1 or count(/HP Comware HH3C SNMP/sensor.psu.status[hh3cEntityExtErrorStatus.{#SNMPINDEX}],#1,"eq","{$PSU_CRIT_STATUS:\"hardwareFaulty\"}")=1'
                  name: '{#ENT_NAME}: Power supply is in critical state'
                  opdata: 'Current state: {ITEM.LASTVALUE1}'
                  priority: AVERAGE
                  description: 'Please check the power supply unit for errors'
                  tags:
                    -
                      tag: scope
                      value: availability
                    -
                      tag: scope
                      value: performance
        -
          uuid: 14606d314fc24002a0a4cda2fa009d45
          name: 'Temperature Discovery'
          type: SNMP_AGENT
          snmp_oid: 'discovery[{#SNMPVALUE},1.3.6.1.2.1.47.1.1.1.1.2,{#MODULE_NAME},1.3.6.1.2.1.47.1.1.1.1.7]'
          key: temp.discovery
          delay: 1h
          filter:
            evaltype: OR
            conditions:
              -
                macro: '{#SNMPVALUE}'
                value: '^(MODULE|Module) (LEVEL|level)1$'
                formulaid: A
              -
                macro: '{#SNMPVALUE}'
                value: '(Fabric|FABRIC) (.+) (Module|MODULE)'
                formulaid: B
              -
                macro: '{#SNMPVALUE}'
                value: '(T|t)emperature.*(s|S)ensor'
                formulaid: C
          description: 'Discovering modules temperature (same filter as in Module Discovery) plus and temperature sensors'
          item_prototypes:
            -
              uuid: 4b18458608f74e0ca48af5cb674e324d
              name: '{#SNMPVALUE}: Temperature'
              type: SNMP_AGENT
              snmp_oid: '1.3.6.1.4.1.25506.2.6.1.1.1.1.12.{#SNMPINDEX}'
              key: 'sensor.temp.value[hh3cEntityExtTemperature.{#SNMPINDEX}]'
              delay: 3m
              value_type: FLOAT
              units: °C
              description: |
                MIB: HH3C-ENTITY-EXT-MIB
                The temperature for the {#SNMPVALUE}.
              tags:
                -
                  tag: component
                  value: temperature
              trigger_prototypes:
                -
                  uuid: bbad03018e8e408c952cca2615b4eab3
                  expression: 'avg(/HP Comware HH3C SNMP/sensor.temp.value[hh3cEntityExtTemperature.{#SNMPINDEX}],5m)>{$TEMP_CRIT:""}'
                  recovery_mode: RECOVERY_EXPRESSION
                  recovery_expression: 'max(/HP Comware HH3C SNMP/sensor.temp.value[hh3cEntityExtTemperature.{#SNMPINDEX}],5m)<{$TEMP_CRIT:""}-3'
                  name: '{#SNMPVALUE}: Temperature is above critical threshold: >{$TEMP_CRIT:""}'
                  opdata: 'Current value: {ITEM.LASTVALUE1}'
                  priority: HIGH
                  description: 'This trigger uses temperature sensor values as well as temperature sensor status if available'
                  tags:
                    -
                      tag: scope
                      value: availability
                    -
                      tag: scope
                      value: performance
                -
                  uuid: cda6a4e305fe41239462d2f85acc5590
                  expression: 'avg(/HP Comware HH3C SNMP/sensor.temp.value[hh3cEntityExtTemperature.{#SNMPINDEX}],5m)>{$TEMP_WARN:""}'
                  recovery_mode: RECOVERY_EXPRESSION
                  recovery_expression: 'max(/HP Comware HH3C SNMP/sensor.temp.value[hh3cEntityExtTemperature.{#SNMPINDEX}],5m)<{$TEMP_WARN:""}-3'
                  name: '{#SNMPVALUE}: Temperature is above warning threshold: >{$TEMP_WARN:""}'
                  opdata: 'Current value: {ITEM.LASTVALUE1}'
                  priority: WARNING
                  description: 'This trigger uses temperature sensor values as well as temperature sensor status if available'
                  dependencies:
                    -
                      name: '{#SNMPVALUE}: Temperature is above critical threshold: >{$TEMP_CRIT:""}'
                      expression: 'avg(/HP Comware HH3C SNMP/sensor.temp.value[hh3cEntityExtTemperature.{#SNMPINDEX}],5m)>{$TEMP_CRIT:""}'
                      recovery_expression: 'max(/HP Comware HH3C SNMP/sensor.temp.value[hh3cEntityExtTemperature.{#SNMPINDEX}],5m)<{$TEMP_CRIT:""}-3'
                  tags:
                    -
                      tag: scope
                      value: availability
                    -
                      tag: scope
                      value: performance
                -
                  uuid: 340a9c397bff4ac5a37a2ada9d0c1e69
                  expression: 'avg(/HP Comware HH3C SNMP/sensor.temp.value[hh3cEntityExtTemperature.{#SNMPINDEX}],5m)<{$TEMP_CRIT_LOW:""}'
                  recovery_mode: RECOVERY_EXPRESSION
                  recovery_expression: 'min(/HP Comware HH3C SNMP/sensor.temp.value[hh3cEntityExtTemperature.{#SNMPINDEX}],5m)>{$TEMP_CRIT_LOW:""}+3'
                  name: '{#SNMPVALUE}: Temperature is too low: <{$TEMP_CRIT_LOW:""}'
                  opdata: 'Current value: {ITEM.LASTVALUE1}'
                  priority: AVERAGE
                  tags:
                    -
                      tag: scope
                      value: availability
                    -
                      tag: scope
                      value: performance
      tags:
        -
          tag: class
          value: network
        -
          tag: target
          value: hp
        -
          tag: target
          value: hp-comware
      macros:
        -
          macro: '{$CPU.UTIL.CRIT}'
          value: '90'
        -
          macro: '{$FAN_CRIT_STATUS:"fanError"}'
          value: '41'
        -
          macro: '{$FAN_CRIT_STATUS:"hardwareFaulty"}'
          value: '91'
        -
          macro: '{$ICMP_LOSS_WARN}'
          value: '20'
        -
          macro: '{$ICMP_RESPONSE_TIME_WARN}'
          value: '0.15'
        -
          macro: '{$IF.ERRORS.WARN}'
          value: '2'
        -
          macro: '{$IF.UTIL.MAX}'
          value: '90'
        -
          macro: '{$IFCONTROL}'
          value: '1'
        -
          macro: '{$MEMORY.UTIL.MAX}'
          value: '90'
        -
          macro: '{$NET.IF.IFADMINSTATUS.MATCHES}'
          value: '^.*'
          description: 'Ignore notPresent(6)'
        -
          macro: '{$NET.IF.IFADMINSTATUS.NOT_MATCHES}'
          value: ^2$
          description: 'Ignore down(2) administrative status'
        -
          macro: '{$NET.IF.IFALIAS.MATCHES}'
          value: '.*'
        -
          macro: '{$NET.IF.IFALIAS.NOT_MATCHES}'
          value: CHANGE_IF_NEEDED
        -
          macro: '{$NET.IF.IFDESCR.MATCHES}'
          value: '.*'
        -
          macro: '{$NET.IF.IFDESCR.NOT_MATCHES}'
          value: CHANGE_IF_NEEDED
        -
          macro: '{$NET.IF.IFNAME.MATCHES}'
          value: '^.*$'
        -
          macro: '{$NET.IF.IFNAME.NOT_MATCHES}'
          value: '(^Software Loopback Interface|^NULL[0-9.]*$|^[Ll]o[0-9.]*$|^[Ss]ystem$|^Nu[0-9.]*$|^veth[0-9a-z]+$|docker[0-9]+|br-[a-z0-9]{12})'
          description: 'Filter out loopbacks, nulls, docker veth links and docker0 bridge by default'
        -
          macro: '{$NET.IF.IFOPERSTATUS.MATCHES}'
          value: '^.*$'
        -
          macro: '{$NET.IF.IFOPERSTATUS.NOT_MATCHES}'
          value: ^6$
          description: 'Ignore notPresent(6)'
        -
          macro: '{$NET.IF.IFTYPE.MATCHES}'
          value: '.*'
        -
          macro: '{$NET.IF.IFTYPE.NOT_MATCHES}'
          value: CHANGE_IF_NEEDED
        -
          macro: '{$PSU_CRIT_STATUS:"hardwareFaulty"}'
          value: '91'
        -
          macro: '{$PSU_CRIT_STATUS:"psuError"}'
          value: '51'
        -
          macro: '{$PSU_CRIT_STATUS:"rpsError"}'
          value: '61'
        -
          macro: '{$SNMP.TIMEOUT}'
          value: 5m
        -
          macro: '{$TEMP_CRIT}'
          value: '60'
        -
          macro: '{$TEMP_CRIT_LOW}'
          value: '5'
        -
          macro: '{$TEMP_WARN}'
          value: '50'
      dashboards:
        -
          uuid: bb5d0711937d47d989653b53b7772cea
          name: 'Network interfaces'
          pages:
            -
              widgets:
                -
                  type: GRAPH_PROTOTYPE
                  width: '24'
                  height: '5'
                  fields:
                    -
                      type: INTEGER
                      name: source_type
                      value: '2'
                    -
                      type: INTEGER
                      name: columns
                      value: '1'
                    -
                      type: INTEGER
                      name: rows
                      value: '1'
                    -
                      type: GRAPH_PROTOTYPE
                      name: graphid
                      value:
                        host: 'HP Comware HH3C SNMP'
                        name: 'Interface {#IFNAME}({#IFALIAS}): Network traffic'
      valuemaps:
        -
          uuid: ec3b9adaa2ad448db5a613cfb6e02fd3
          name: 'EtherLike-MIB::dot3StatsDuplexStatus'
          mappings:
            -
              value: '1'
              newvalue: unknown
            -
              value: '2'
              newvalue: halfDuplex
            -
              value: '3'
              newvalue: fullDuplex
        -
          uuid: d7832aa00dd743bb8451cabff4e90e60
          name: 'HH3C-ENTITY-EXT-MIB::hh3cEntityExtErrorStatus'
          mappings:
            -
              value: '1'
              newvalue: notSupported
            -
              value: '2'
              newvalue: normal
            -
              value: '3'
              newvalue: postFailure
            -
              value: '4'
              newvalue: entityAbsent
            -
              value: '11'
              newvalue: poeError
            -
              value: '21'
              newvalue: stackError
            -
              value: '22'
              newvalue: stackPortBlocked
            -
              value: '23'
              newvalue: stackPortFailed
            -
              value: '31'
              newvalue: sfpRecvError
            -
              value: '32'
              newvalue: sfpSendError
            -
              value: '33'
              newvalue: sfpBothError
            -
              value: '41'
              newvalue: fanError
            -
              value: '51'
              newvalue: psuError
            -
              value: '61'
              newvalue: rpsError
            -
              value: '71'
              newvalue: moduleFaulty
            -
              value: '81'
              newvalue: sensorError
            -
              value: '91'
              newvalue: hardwareFaulty
        -
          uuid: 47c29a4e28094f59a3666ab1bc7bbba9
          name: 'IF-MIB::ifOperStatus'
          mappings:
            -
              value: '1'
              newvalue: up
            -
              value: '2'
              newvalue: down
            -
              value: '4'
              newvalue: unknown
            -
              value: '5'
              newvalue: dormant
            -
              value: '6'
              newvalue: notPresent
            -
              value: '7'
              newvalue: lowerLayerDown
        -
          uuid: 023e30ad64bc402abf989de29c2b7c5e
          name: 'IF-MIB::ifType'
          mappings:
            -
              value: '1'
              newvalue: other
            -
              value: '2'
              newvalue: regular1822
            -
              value: '3'
              newvalue: hdh1822
            -
              value: '4'
              newvalue: ddnX25
            -
              value: '5'
              newvalue: rfc877x25
            -
              value: '6'
              newvalue: ethernetCsmacd
            -
              value: '7'
              newvalue: iso88023Csmacd
            -
              value: '8'
              newvalue: iso88024TokenBus
            -
              value: '9'
              newvalue: iso88025TokenRing
            -
              value: '10'
              newvalue: iso88026Man
            -
              value: '11'
              newvalue: starLan
            -
              value: '12'
              newvalue: proteon10Mbit
            -
              value: '13'
              newvalue: proteon80Mbit
            -
              value: '14'
              newvalue: hyperchannel
            -
              value: '15'
              newvalue: fddi
            -
              value: '16'
              newvalue: lapb
            -
              value: '17'
              newvalue: sdlc
            -
              value: '18'
              newvalue: ds1
            -
              value: '19'
              newvalue: e1
            -
              value: '20'
              newvalue: basicISDN
            -
              value: '21'
              newvalue: primaryISDN
            -
              value: '22'
              newvalue: propPointToPointSerial
            -
              value: '23'
              newvalue: ppp
            -
              value: '24'
              newvalue: softwareLoopback
            -
              value: '25'
              newvalue: eon
            -
              value: '26'
              newvalue: ethernet3Mbit
            -
              value: '27'
              newvalue: nsip
            -
              value: '28'
              newvalue: slip
            -
              value: '29'
              newvalue: ultra
            -
              value: '30'
              newvalue: ds3
            -
              value: '31'
              newvalue: sip
            -
              value: '32'
              newvalue: frameRelay
            -
              value: '33'
              newvalue: rs232
            -
              value: '34'
              newvalue: para
            -
              value: '35'
              newvalue: arcnet
            -
              value: '36'
              newvalue: arcnetPlus
            -
              value: '37'
              newvalue: atm
            -
              value: '38'
              newvalue: miox25
            -
              value: '39'
              newvalue: sonet
            -
              value: '40'
              newvalue: x25ple
            -
              value: '41'
              newvalue: iso88022llc
            -
              value: '42'
              newvalue: localTalk
            -
              value: '43'
              newvalue: smdsDxi
            -
              value: '44'
              newvalue: frameRelayService
            -
              value: '45'
              newvalue: v35
            -
              value: '46'
              newvalue: hssi
            -
              value: '47'
              newvalue: hippi
            -
              value: '48'
              newvalue: modem
            -
              value: '49'
              newvalue: aal5
            -
              value: '50'
              newvalue: sonetPath
            -
              value: '51'
              newvalue: sonetVT
            -
              value: '52'
              newvalue: smdsIcip
            -
              value: '53'
              newvalue: propVirtual
            -
              value: '54'
              newvalue: propMultiplexor
            -
              value: '55'
              newvalue: ieee80212
            -
              value: '56'
              newvalue: fibreChannel
            -
              value: '57'
              newvalue: hippiInterface
            -
              value: '58'
              newvalue: frameRelayInterconnect
            -
              value: '59'
              newvalue: aflane8023
            -
              value: '60'
              newvalue: aflane8025
            -
              value: '61'
              newvalue: cctEmul
            -
              value: '62'
              newvalue: fastEther
            -
              value: '63'
              newvalue: isdn
            -
              value: '64'
              newvalue: v11
            -
              value: '65'
              newvalue: v36
            -
              value: '66'
              newvalue: g703at64k
            -
              value: '67'
              newvalue: g703at2mb
            -
              value: '68'
              newvalue: qllc
            -
              value: '69'
              newvalue: fastEtherFX
            -
              value: '70'
              newvalue: channel
            -
              value: '71'
              newvalue: ieee80211
            -
              value: '72'
              newvalue: ibm370parChan
            -
              value: '73'
              newvalue: escon
            -
              value: '74'
              newvalue: dlsw
            -
              value: '75'
              newvalue: isdns
            -
              value: '76'
              newvalue: isdnu
            -
              value: '77'
              newvalue: lapd
            -
              value: '78'
              newvalue: ipSwitch
            -
              value: '79'
              newvalue: rsrb
            -
              value: '80'
              newvalue: atmLogical
            -
              value: '81'
              newvalue: ds0
            -
              value: '82'
              newvalue: ds0Bundle
            -
              value: '83'
              newvalue: bsc
            -
              value: '84'
              newvalue: async
            -
              value: '85'
              newvalue: cnr
            -
              value: '86'
              newvalue: iso88025Dtr
            -
              value: '87'
              newvalue: eplrs
            -
              value: '88'
              newvalue: arap
            -
              value: '89'
              newvalue: propCnls
            -
              value: '90'
              newvalue: hostPad
            -
              value: '91'
              newvalue: termPad
            -
              value: '92'
              newvalue: frameRelayMPI
            -
              value: '93'
              newvalue: x213
            -
              value: '94'
              newvalue: adsl
            -
              value: '95'
              newvalue: radsl
            -
              value: '96'
              newvalue: sdsl
            -
              value: '97'
              newvalue: vdsl
            -
              value: '98'
              newvalue: iso88025CRFPInt
            -
              value: '99'
              newvalue: myrinet
            -
              value: '100'
              newvalue: voiceEM
            -
              value: '101'
              newvalue: voiceFXO
            -
              value: '102'
              newvalue: voiceFXS
            -
              value: '103'
              newvalue: voiceEncap
            -
              value: '104'
              newvalue: voiceOverIp
            -
              value: '105'
              newvalue: atmDxi
            -
              value: '106'
              newvalue: atmFuni
            -
              value: '107'
              newvalue: atmIma
            -
              value: '108'
              newvalue: pppMultilinkBundle
            -
              value: '109'
              newvalue: ipOverCdlc
            -
              value: '110'
              newvalue: ipOverClaw
            -
              value: '111'
              newvalue: stackToStack
            -
              value: '112'
              newvalue: virtualIpAddress
            -
              value: '113'
              newvalue: mpc
            -
              value: '114'
              newvalue: ipOverAtm
            -
              value: '115'
              newvalue: iso88025Fiber
            -
              value: '116'
              newvalue: tdlc
            -
              value: '117'
              newvalue: gigabitEthernet
            -
              value: '118'
              newvalue: hdlc
            -
              value: '119'
              newvalue: lapf
            -
              value: '120'
              newvalue: v37
            -
              value: '121'
              newvalue: x25mlp
            -
              value: '122'
              newvalue: x25huntGroup
            -
              value: '123'
              newvalue: trasnpHdlc
            -
              value: '124'
              newvalue: interleave
            -
              value: '125'
              newvalue: fast
            -
              value: '126'
              newvalue: ip
            -
              value: '127'
              newvalue: docsCableMaclayer
            -
              value: '128'
              newvalue: docsCableDownstream
            -
              value: '129'
              newvalue: docsCableUpstream
            -
              value: '130'
              newvalue: a12MppSwitch
            -
              value: '131'
              newvalue: tunnel
            -
              value: '132'
              newvalue: coffee
            -
              value: '133'
              newvalue: ces
            -
              value: '134'
              newvalue: atmSubInterface
            -
              value: '135'
              newvalue: l2vlan
            -
              value: '136'
              newvalue: l3ipvlan
            -
              value: '137'
              newvalue: l3ipxvlan
            -
              value: '138'
              newvalue: digitalPowerline
            -
              value: '139'
              newvalue: mediaMailOverIp
            -
              value: '140'
              newvalue: dtm
            -
              value: '141'
              newvalue: dcn
            -
              value: '142'
              newvalue: ipForward
            -
              value: '143'
              newvalue: msdsl
            -
              value: '144'
              newvalue: ieee1394
            -
              value: '145'
              newvalue: if-gsn
            -
              value: '146'
              newvalue: dvbRccMacLayer
            -
              value: '147'
              newvalue: dvbRccDownstream
            -
              value: '148'
              newvalue: dvbRccUpstream
            -
              value: '149'
              newvalue: atmVirtual
            -
              value: '150'
              newvalue: mplsTunnel
            -
              value: '151'
              newvalue: srp
            -
              value: '152'
              newvalue: voiceOverAtm
            -
              value: '153'
              newvalue: voiceOverFrameRelay
            -
              value: '154'
              newvalue: idsl
            -
              value: '155'
              newvalue: compositeLink
            -
              value: '156'
              newvalue: ss7SigLink
            -
              value: '157'
              newvalue: propWirelessP2P
            -
              value: '158'
              newvalue: frForward
            -
              value: '159'
              newvalue: rfc1483
            -
              value: '160'
              newvalue: usb
            -
              value: '161'
              newvalue: ieee8023adLag
            -
              value: '162'
              newvalue: bgppolicyaccounting
            -
              value: '163'
              newvalue: frf16MfrBundle
            -
              value: '164'
              newvalue: h323Gatekeeper
            -
              value: '165'
              newvalue: h323Proxy
            -
              value: '166'
              newvalue: mpls
            -
              value: '167'
              newvalue: mfSigLink
            -
              value: '168'
              newvalue: hdsl2
            -
              value: '169'
              newvalue: shdsl
            -
              value: '170'
              newvalue: ds1FDL
            -
              value: '171'
              newvalue: pos
            -
              value: '172'
              newvalue: dvbAsiIn
            -
              value: '173'
              newvalue: dvbAsiOut
            -
              value: '174'
              newvalue: plc
            -
              value: '175'
              newvalue: nfas
            -
              value: '176'
              newvalue: tr008
            -
              value: '177'
              newvalue: gr303RDT
            -
              value: '178'
              newvalue: gr303IDT
            -
              value: '179'
              newvalue: isup
            -
              value: '180'
              newvalue: propDocsWirelessMaclayer
            -
              value: '181'
              newvalue: propDocsWirelessDownstream
            -
              value: '182'
              newvalue: propDocsWirelessUpstream
            -
              value: '183'
              newvalue: hiperlan2
            -
              value: '184'
              newvalue: propBWAp2Mp
            -
              value: '185'
              newvalue: sonetOverheadChannel
            -
              value: '186'
              newvalue: digitalWrapperOverheadChannel
            -
              value: '187'
              newvalue: aal2
            -
              value: '188'
              newvalue: radioMAC
            -
              value: '189'
              newvalue: atmRadio
            -
              value: '190'
              newvalue: imt
            -
              value: '191'
              newvalue: mvl
            -
              value: '192'
              newvalue: reachDSL
            -
              value: '193'
              newvalue: frDlciEndPt
            -
              value: '194'
              newvalue: atmVciEndPt
            -
              value: '195'
              newvalue: opticalChannel
            -
              value: '196'
              newvalue: opticalTransport
            -
              value: '197'
              newvalue: propAtm
            -
              value: '198'
              newvalue: voiceOverCable
            -
              value: '199'
              newvalue: infiniband
            -
              value: '200'
              newvalue: teLink
            -
              value: '201'
              newvalue: q2931
            -
              value: '202'
              newvalue: virtualTg
            -
              value: '203'
              newvalue: sipTg
            -
              value: '204'
              newvalue: sipSig
            -
              value: '205'
              newvalue: docsCableUpstreamChannel
            -
              value: '206'
              newvalue: econet
            -
              value: '207'
              newvalue: pon155
            -
              value: '208'
              newvalue: pon622
            -
              value: '209'
              newvalue: bridge
            -
              value: '210'
              newvalue: linegroup
            -
              value: '211'
              newvalue: voiceEMFGD
            -
              value: '212'
              newvalue: voiceFGDEANA
            -
              value: '213'
              newvalue: voiceDID
            -
              value: '214'
              newvalue: mpegTransport
            -
              value: '215'
              newvalue: sixToFour
            -
              value: '216'
              newvalue: gtp
            -
              value: '217'
              newvalue: pdnEtherLoop1
            -
              value: '218'
              newvalue: pdnEtherLoop2
            -
              value: '219'
              newvalue: opticalChannelGroup
            -
              value: '220'
              newvalue: homepna
            -
              value: '221'
              newvalue: gfp
            -
              value: '222'
              newvalue: ciscoISLvlan
            -
              value: '223'
              newvalue: actelisMetaLOOP
            -
              value: '224'
              newvalue: fcipLink
            -
              value: '225'
              newvalue: rpr
            -
              value: '226'
              newvalue: qam
            -
              value: '227'
              newvalue: lmp
            -
              value: '228'
              newvalue: cblVectaStar
            -
              value: '229'
              newvalue: docsCableMCmtsDownstream
            -
              value: '230'
              newvalue: adsl2
            -
              value: '231'
              newvalue: macSecControlledIF
            -
              value: '232'
              newvalue: macSecUncontrolledIF
            -
              value: '233'
              newvalue: aviciOpticalEther
            -
              value: '234'
              newvalue: atmbond
            -
              value: '235'
              newvalue: voiceFGDOS
            -
              value: '236'
              newvalue: mocaVersion1
            -
              value: '237'
              newvalue: ieee80216WMAN
            -
              value: '238'
              newvalue: adsl2plus
            -
              value: '239'
              newvalue: dvbRcsMacLayer
            -
              value: '240'
              newvalue: dvbTdm
            -
              value: '241'
              newvalue: dvbRcsTdma
            -
              value: '242'
              newvalue: x86Laps
            -
              value: '243'
              newvalue: wwanPP
            -
              value: '244'
              newvalue: wwanPP2
            -
              value: '245'
              newvalue: voiceEBS
            -
              value: '246'
              newvalue: ifPwType
            -
              value: '247'
              newvalue: ilan
            -
              value: '248'
              newvalue: pip
            -
              value: '249'
              newvalue: aluELP
            -
              value: '250'
              newvalue: gpon
            -
              value: '251'
              newvalue: vdsl2
            -
              value: '252'
              newvalue: capwapDot11Profile
            -
              value: '253'
              newvalue: capwapDot11Bss
            -
              value: '254'
              newvalue: capwapWtpVirtualRadio
            -
              value: '255'
              newvalue: bits
            -
              value: '256'
              newvalue: docsCableUpstreamRfPort
            -
              value: '257'
              newvalue: cableDownstreamRfPort
            -
              value: '258'
              newvalue: vmwareVirtualNic
            -
              value: '259'
              newvalue: ieee802154
            -
              value: '260'
              newvalue: otnOdu
            -
              value: '261'
              newvalue: otnOtu
            -
              value: '262'
              newvalue: ifVfiType
            -
              value: '263'
              newvalue: g9981
            -
              value: '264'
              newvalue: g9982
            -
              value: '265'
              newvalue: g9983
            -
              value: '266'
              newvalue: aluEpon
            -
              value: '267'
              newvalue: aluEponOnu
            -
              value: '268'
              newvalue: aluEponPhysicalUni
            -
              value: '269'
              newvalue: aluEponLogicalLink
            -
              value: '270'
              newvalue: aluGponOnu
            -
              value: '271'
              newvalue: aluGponPhysicalUni
            -
              value: '272'
              newvalue: vmwareNicTeam
            -
              value: '277'
              newvalue: docsOfdmDownstream
            -
              value: '278'
              newvalue: docsOfdmaUpstream
            -
              value: '279'
              newvalue: gfast
            -
              value: '280'
              newvalue: sdci
            -
              value: '281'
              newvalue: xboxWireless
            -
              value: '282'
              newvalue: fastdsl
            -
              value: '283'
              newvalue: docsCableScte55d1FwdOob
            -
              value: '284'
              newvalue: docsCableScte55d1RetOob
            -
              value: '285'
              newvalue: docsCableScte55d2DsOob
            -
              value: '286'
              newvalue: docsCableScte55d2UsOob
            -
              value: '287'
              newvalue: docsCableNdf
            -
              value: '288'
              newvalue: docsCableNdr
            -
              value: '289'
              newvalue: ptm
            -
              value: '290'
              newvalue: ghn
        -
          uuid: 814a713abe0740bc93531596474b80b2
          name: 'Service state'
          mappings:
            -
              value: '0'
              newvalue: Down
            -
              value: '1'
              newvalue: Up
        -
          uuid: f6c6d62117c749a482792e753486a2be
          name: zabbix.host.available
          mappings:
            -
              value: '0'
              newvalue: 'not available'
            -
              value: '1'
              newvalue: available
            -
              value: '2'
              newvalue: unknown
fmrp_zbx_export_templates.yaml
Exibindo fmrp_zbx_export_templates.yaml.
