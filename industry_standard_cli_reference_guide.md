# The SONiC Industry Standard CLI Reference Guide 

# Table Of Commands 

[aaa authentication failthrough](#aaa-authentication-failthrough)

[aaa authentication login default](#aaa-authentication-login-default)

[aaa authentication login-method](#aaa-authentication-login-method)

[aaa authorization login default](#aaa-authorization-login-default)

[aaa name-service group](#aaa-name-service-group)

[aaa name-service netgroup](#aaa-name-service-netgroup)

[aaa name-service passwd](#aaa-name-service-passwd)

[aaa name-service shadow](#aaa-name-service-shadow)

[aaa name-service sudoers](#aaa-name-service-sudoers)

[access-list](#access-list)

[activate](#activate)

[addpath-tx-all-paths](#addpath-tx-all-paths)

[addpath-tx-bestpath-per-as](#addpath-tx-bestpath-per-as)

[address-family ipv4](#address-family-ipv4)

[address-family ipv6](#address-family-ipv6)

[address-family l2vpn](#address-family-l2vpn)

[advertise ipv4 unicast](#advertise-ipv4-unicast)

[advertise ipv6 unicast](#advertise-ipv6-unicast)

[advertise-all-vni](#advertise-all-vni)

[advertise-default-gw](#advertise-default-gw)

[advertise-svi-ip](#advertise-svi-ip)

[advertisement-interval](#advertisement-interval)

[aggregate-address](#aggregate-address)

[aging-interval](#aging-interval)

[allowas-in](#allowas-in)

[always-compare-med](#always-compare-med)

[area](#area)

[as-override](#as-override)

[attribute-unchanged](#attribute-unchanged)

[authentication](#authentication)

[authentication rest](#authentication-rest)

[authentication telemetry](#authentication-telemetry)

[auto-cost](#auto-cost)

[autoneg](#autoneg)

[autort](#autort)

[bestpath as-path confed](#bestpath-as-path-confed)

[bestpath as-path ignore](#bestpath-as-path-ignore)

[bestpath as-path multipath-relax](#bestpath-as-path-multipath-relax)

[bestpath compare-routerid](#bestpath-compare-routerid)

[bestpath med](#bestpath-med)

[bfd](#bfd)

[bgp as-path-list](#bgp-as-path-list)

[bgp community-list](#bgp-community-list)

[bgp extcommunity-list](#bgp-extcommunity-list)

[binding](#binding)

[call](#call)

[capability dynamic](#capability-dynamic)

[capability extended-nexthop](#capability-extended-nexthop)

[capability orf prefix-list](#capability-orf-prefix-list)

[cbs](#cbs)

[channel-group](#channel-group)

[cir](#cir)

[class](#class)

[class-map](#class-map)

[clear audit-log](#clear-audit-log)

[clear bfd peer](#clear-bfd-peer)

[clear bgp all](#clear-bgp-all)

[clear bgp ipv4](#clear-bgp-ipv4)

[clear bgp ipv6](#clear-bgp-ipv6)

[clear bgp l2vpn evpn](#clear-bgp-l2vpn-evpn)

[clear buffer_pool](#clear-buffer_pool)

[clear counters](#clear-counters)

[clear counters service-policy](#clear-counters-service-policy)

[clear counters service-policy interface](#clear-counters-service-policy-interface)

[clear counters service-policy policy-map](#clear-counters-service-policy-policy-map)

[clear counters tam](#clear-counters-tam)

[clear ip access-list counters](#clear-ip-access-list-counters)

[clear ip arp](#clear-ip-arp)

[clear ip arp interface](#clear-ip-arp-interface)

[clear ip dhcp-relay](#clear-ip-dhcp-relay)

[clear ip helper-address statistics](#clear-ip-helper-address-statistics)

[clear ip mroute](#clear-ip-mroute)

[clear ip ospf](#clear-ip-ospf)

[clear ip pim](#clear-ip-pim)

[clear ip sla](#clear-ip-sla)

[clear ip sla all](#clear-ip-sla-all)

[clear ipv6 access-list counters](#clear-ipv6-access-list-counters)

[clear ipv6 dhcp-relay](#clear-ipv6-dhcp-relay)

[clear ipv6 neighbors](#clear-ipv6-neighbors)

[clear ipv6 neighbors interface](#clear-ipv6-neighbors-interface)

[clear logging](#clear-logging)

[clear mac access-list counters](#clear-mac-access-list-counters)

[clear mac address-table dynamic](#clear-mac-address-table-dynamic)

[clear mac dampening-disabled-ports](#clear-mac-dampening-disabled-ports)

[clear nat](#clear-nat)

[clear priority-group](#clear-priority-group)

[clear queue](#clear-queue)

[clear radius-server statistics](#clear-radius-server-statistics)

[clear snmp counters](#clear-snmp-counters)

[clear threshold](#clear-threshold)

[client-to-client reflection](#client-to-client-reflection)

[cluster-id](#cluster-id)

[coalesce-time](#coalesce-time)

[collector](#collector)

[compatible](#compatible)

[confederation](#confederation)

[configure terminal](#configure-terminal)

[copp-action](#copp-action)

[copy](#copy)

[core enable](#core-enable)

[counters](#counters)

[crm](#crm)

[dampening](#dampening)

[default interface](#default-interface)

[default interface range](#default-interface-range)

[default ipv4-unicast](#default-ipv4-unicast)

[default local-preference](#default-local-preference)

[default show-hostname](#default-show-hostname)

[default shutdown](#default-shutdown)

[default subgroup-pkt-queue-max](#default-subgroup-pkt-queue-max)

[default-information](#default-information)

[default-metric](#default-metric)

[default-originate](#default-originate)

[default-originate ipv4](#default-originate-ipv4)

[default-originate ipv6](#default-originate-ipv6)

[delay-restore](#delay-restore)

[description](#description)

[destination](#destination)

[destination CPU](#destination-CPU)

[destination erspan](#destination-erspan)

[detect-multiplier](#detect-multiplier)

[deterministic-med](#deterministic-med)

[disable-connected-check](#disable-connected-check)

[disable-ebgp-connected-route-check](#disable-ebgp-connected-route-check)

[distance](#distance)

[distance bgp](#distance-bgp)

[dont-capability-negotiate](#dont-capability-negotiate)

[dot1p](#dot1p)

[downstream](#downstream)

[drop-monitor](#drop-monitor)

[dscp](#dscp)

[dup-addr-detection](#dup-addr-detection)

[dup-addr-detection freeze](#dup-addr-detection-freeze)

[ebgp-multihop](#ebgp-multihop)

[echo-interval](#echo-interval)

[echo-mode](#echo-mode)

[ecn](#ecn)

[enable](#enable)

[enforce-first-as](#enforce-first-as)

[enforce-multihop](#enforce-multihop)

[enterprise-id](#enterprise-id)

[errdisable recovery cause](#errdisable-recovery-cause)

[errdisable recovery interval](#errdisable-recovery-interval)

[factory](#factory)

[fast-external-failover](#fast-external-failover)

[fast-reboot](#fast-reboot)

[fec](#fec)

[filter-list](#filter-list)

[flow-group](#flow-group)

[frequency](#frequency)

[graceful-restart enable](#graceful-restart-enable)

[graceful-restart preserve-fw-state](#graceful-restart-preserve-fw-state)

[graceful-restart restart-time](#graceful-restart-restart-time)

[graceful-restart stalepath-time](#graceful-restart-stalepath-time)

[graceful-shutdown](#graceful-shutdown)

[green](#green)

[hardware](#hardware)

[hostname](#hostname)

[icmp-echo](#icmp-echo)

[ifa](#ifa)

[image install](#image-install)

[image remove](#image-remove)

[image set-default](#image-set-default)

[import vrf](#import-vrf)

[interface](#interface)

[interface CPU](#interface-CPU)

[interface Loopback](#interface-Loopback)

[interface Management](#interface-Management)

[interface PortChannel](#interface-PortChannel)

[interface breakout](#interface-breakout)

[interface range](#interface-range)

[interface vxlan](#interface-vxlan)

[interface-naming](#interface-naming)

[ip access-group](#ip-access-group)

[ip access-list](#ip-access-list)

[ip address](#ip-address)

[ip anycast-address](#ip-anycast-address)

[ip anycast-mac-address](#ip-anycast-mac-address)

[ip arp](#ip-arp)

[ip dhcp-relay](#ip-dhcp-relay)

[ip dhcp-relay link-select](#ip-dhcp-relay-link-select)

[ip dhcp-relay max-hop-count](#ip-dhcp-relay-max-hop-count)

[ip dhcp-relay policy-action](#ip-dhcp-relay-policy-action)

[ip dhcp-relay source-interface](#ip-dhcp-relay-source-interface)

[ip dhcp-relay vrf-select](#ip-dhcp-relay-vrf-select)

[ip forward-protocol udp enable](#ip-forward-protocol-udp-enable)

[ip forward-protocol udp exclude](#ip-forward-protocol-udp-exclude)

[ip forward-protocol udp include](#ip-forward-protocol-udp-include)

[ip forward-protocol udp rate-limit](#ip-forward-protocol-udp-rate-limit)

[ip helper-address](#ip-helper-address)

[ip helper-address vrf](#ip-helper-address-vrf)

[ip igmp](#ip-igmp)

[ip igmp snooping](#ip-igmp-snooping)

[ip load-share hash ipv4](#ip-load-share-hash-ipv4)

[ip load-share hash ipv6](#ip-load-share-hash-ipv6)

[ip load-share hash seed](#ip-load-share-hash-seed)

[ip name-server](#ip-name-server)

[ip name-server source-interface](#ip-name-server-source-interface)

[ip ospf](#ip-ospf)

[ip ospf area](#ip-ospf-area)

[ip ospf authentication](#ip-ospf-authentication)

[ip ospf authentication-key](#ip-ospf-authentication-key)

[ip ospf bfd](#ip-ospf-bfd)

[ip ospf cost](#ip-ospf-cost)

[ip ospf dead-interval](#ip-ospf-dead-interval)

[ip ospf hello-interval](#ip-ospf-hello-interval)

[ip ospf message-digest-key](#ip-ospf-message-digest-key)

[ip ospf mtu-ignore](#ip-ospf-mtu-ignore)

[ip ospf network](#ip-ospf-network)

[ip ospf priority](#ip-ospf-priority)

[ip ospf retransmit-interval](#ip-ospf-retransmit-interval)

[ip ospf transmit-delay](#ip-ospf-transmit-delay)

[ip pim](#ip-pim)

[ip pim bfd](#ip-pim-bfd)

[ip pim drpriority](#ip-pim-drpriority)

[ip pim hello](#ip-pim-hello)

[ip pim sparse-mode](#ip-pim-sparse-mode)

[ip prefix-list](#ip-prefix-list)

[ip route](#ip-route)

[ip route vrf](#ip-route-vrf)

[ip sla](#ip-sla)

[ip unnumbered](#ip-unnumbered)

[ip vrf](#ip-vrf)

[ip vrf forwarding](#ip-vrf-forwarding)

[ip vrf mgmt](#ip-vrf-mgmt)

[ipv6 access-group](#ipv6-access-group)

[ipv6 access-list](#ipv6-access-list)

[ipv6 address](#ipv6-address)

[ipv6 anycast-address](#ipv6-anycast-address)

[ipv6 dhcp-relay](#ipv6-dhcp-relay)

[ipv6 dhcp-relay max-hop-count](#ipv6-dhcp-relay-max-hop-count)

[ipv6 dhcp-relay source-interface](#ipv6-dhcp-relay-source-interface)

[ipv6 dhcp-relay vrf-select](#ipv6-dhcp-relay-vrf-select)

[ipv6 enable](#ipv6-enable)

[ipv6 nd](#ipv6-nd)

[ipv6 neighbor](#ipv6-neighbor)

[ipv6 prefix-list](#ipv6-prefix-list)

[ipv6 route](#ipv6-route)

[ipv6 route vrf](#ipv6-route-vrf)

[kdump enable](#kdump-enable)

[kdump memory](#kdump-memory)

[kdump num-dumps](#kdump-num-dumps)

[keepalive-interval](#keepalive-interval)

[ldap-server](#ldap-server)

[ldap-server host](#ldap-server-host)

[ldap-server map](#ldap-server-map)

[ldap-server nss](#ldap-server-nss)

[ldap-server pam](#ldap-server-pam)

[ldap-server source-interface](#ldap-server-source-interface)

[ldap-server sudo](#ldap-server-sudo)

[ldap-server vrf](#ldap-server-vrf)

[line vty](#line-vty)

[link state track](#link-state-track)

[listen limit](#listen-limit)

[listen range](#listen-range)

[lldp](#lldp)

[lldp enable](#lldp-enable)

[lldp multiplier](#lldp-multiplier)

[lldp system-description](#lldp-system-description)

[lldp system-name](#lldp-system-name)

[lldp timer](#lldp-timer)

[lldp tlv-select](#lldp-tlv-select)

[local-as](#local-as)

[log-adjacency-changes](#log-adjacency-changes)

[log-neighbor-changes](#log-neighbor-changes)

[logger](#logger)

[logging server](#logging-server)

[mac access-group](#mac-access-group)

[mac access-list](#mac-access-list)

[mac address-table](#mac-address-table)

[mac address-table aging-time](#mac-address-table-aging-time)

[mac address-table dampening-interval](#mac-address-table-dampening-interval)

[mac address-table dampening-threshold](#mac-address-table-dampening-threshold)

[map](#map)

[match access-group](#match-access-group)

[match as-path](#match-as-path)

[match community](#match-community)

[match dei](#match-dei)

[match destination-address](#match-destination-address)

[match dscp](#match-dscp)

[match ethertype](#match-ethertype)

[match evpn](#match-evpn)

[match ext-community](#match-ext-community)

[match interface](#match-interface)

[match ip address prefix-list](#match-ip-address-prefix-list)

[match ip next-hop prefix-list](#match-ip-next-hop-prefix-list)

[match ip protocol](#match-ip-protocol)

[match ipv6 address prefix-list](#match-ipv6-address-prefix-list)

[match l4-port](#match-l4-port)

[match local-preference](#match-local-preference)

[match metric](#match-metric)

[match origin](#match-origin)

[match pcp](#match-pcp)

[match peer](#match-peer)

[match protocol](#match-protocol)

[match source-address](#match-source-address)

[match source-protocol](#match-source-protocol)

[match source-vrf](#match-source-vrf)

[match tag](#match-tag)

[match tcp-flags](#match-tcp-flags)

[match vlan](#match-vlan)

[max-med](#max-med)

[max-metric](#max-metric)

[maximum-paths](#maximum-paths)

[maximum-paths ibgp](#maximum-paths-ibgp)

[maximum-prefix](#maximum-prefix)

[mclag](#mclag)

[mclag domain](#mclag-domain)

[mclag gateway-mac](#mclag-gateway-mac)

[mclag-separate-ip](#mclag-separate-ip)

[mclag-system-mac](#mclag-system-mac)

[meter-type](#meter-type)

[mirror-session](#mirror-session)

[mtu](#mtu)

[nat](#nat)

[nat-zone](#nat-zone)

[neigh-suppress](#neigh-suppress)

[neighbor](#neighbor)

[network](#network)

[network import-check](#network-import-check)

[next-hop-self](#next-hop-self)

[no](#no)

[ntp authenticate](#ntp-authenticate)

[ntp authentication-key](#ntp-authentication-key)

[ntp server](#ntp-server)

[ntp source-interface](#ntp-source-interface)

[ntp trusted-key](#ntp-trusted-key)

[ntp vrf](#ntp-vrf)

[ospf](#ospf)

[ospf abr-type](#ospf-abr-type)

[ospf router-id](#ospf-router-id)

[override-capability](#override-capability)

[passive](#passive)

[passive-interface](#passive-interface)

[password](#password)

[pbs](#pbs)

[peer](#peer)

[peer-group](#peer-group)

[peer-ip](#peer-ip)

[peer-link](#peer-link)

[pfc-priority](#pfc-priority)

[ping](#ping)

[ping vrf](#ping-vrf)

[ping vrf mgmt](#ping-vrf-mgmt)

[ping6](#ping6)

[ping6 vrf](#ping6-vrf)

[ping6 vrf mgmt](#ping6-vrf-mgmt)

[pir](#pir)

[police](#police)

[policy-map](#policy-map)

[pool](#pool)

[port](#port)

[port-group](#port-group)

[portchannel graceful-shutdown](#portchannel-graceful-shutdown)

[preempt](#preempt)

[prefix-list](#prefix-list)

[priority](#priority)

[priority-flow-control](#priority-flow-control)

[priority-flow-control watchdog action](#priority-flow-control-watchdog-action)

[priority-flow-control watchdog counter-poll](#priority-flow-control-watchdog-counter-poll)

[priority-flow-control watchdog detect-time](#priority-flow-control-watchdog-detect-time)

[priority-flow-control watchdog off](#priority-flow-control-watchdog-off)

[priority-flow-control watchdog on](#priority-flow-control-watchdog-on)

[priority-flow-control watchdog polling-interval](#priority-flow-control-watchdog-polling-interval)

[priority-flow-control watchdog restore-time](#priority-flow-control-watchdog-restore-time)

[ptp announce-timeout](#ptp-announce-timeout)

[ptp domain](#ptp-domain)

[ptp domain-profile](#ptp-domain-profile)

[ptp ipv6-scope](#ptp-ipv6-scope)

[ptp log-announce-interval](#ptp-log-announce-interval)

[ptp log-min-delay-req-interval](#ptp-log-min-delay-req-interval)

[ptp log-sync-interval](#ptp-log-sync-interval)

[ptp mode](#ptp-mode)

[ptp network-transport](#ptp-network-transport)

[ptp port add](#ptp-port-add)

[ptp port del](#ptp-port-del)

[ptp port master-table](#ptp-port-master-table)

[ptp priority1](#ptp-priority1)

[ptp priority2](#ptp-priority2)

[ptp two-step](#ptp-two-step)

[qos map dot1p-tc](#qos-map-dot1p-tc)

[qos map dscp-tc](#qos-map-dscp-tc)

[qos map pfc-priority-queue](#qos-map-pfc-priority-queue)

[qos map tc-dot1p](#qos-map-tc-dot1p)

[qos map tc-dscp](#qos-map-tc-dscp)

[qos map tc-pg](#qos-map-tc-pg)

[qos map tc-queue](#qos-map-tc-queue)

[qos scheduler-policy](#qos-scheduler-policy)

[qos wred-policy](#qos-wred-policy)

[qos-map dot1p-tc](#qos-map-dot1p-tc)

[qos-map dscp-tc](#qos-map-dscp-tc)

[qos-map pfc-priority-queue](#qos-map-pfc-priority-queue)

[qos-map tc-dot1p](#qos-map-tc-dot1p)

[qos-map tc-dscp](#qos-map-tc-dscp)

[qos-map tc-pg](#qos-map-tc-pg)

[qos-map tc-queue](#qos-map-tc-queue)

[qos-mode](#qos-mode)

[queue](#queue)

[radius-server auth-type](#radius-server-auth-type)

[radius-server host](#radius-server-host)

[radius-server key](#radius-server-key)

[radius-server nas-ip](#radius-server-nas-ip)

[radius-server retransmit](#radius-server-retransmit)

[radius-server source-ip](#radius-server-source-ip)

[radius-server statistics](#radius-server-statistics)

[radius-server timeout](#radius-server-timeout)

[rd](#rd)

[read-quanta](#read-quanta)

[reboot](#reboot)

[receive-interval](#receive-interval)

[redistribute](#redistribute)

[refresh](#refresh)

[remark](#remark)

[remote-as](#remote-as)

[remove-private-as](#remove-private-as)

[renew dhcp-lease](#renew-dhcp-lease)

[request-data-size](#request-data-size)

[route-map](#route-map)

[route-map delay-timer](#route-map-delay-timer)

[route-reflector allow-outbound-policy](#route-reflector-allow-outbound-policy)

[route-reflector-client](#route-reflector-client)

[route-server-client](#route-server-client)

[route-target](#route-target)

[router bgp](#router-bgp)

[router ospf](#router-ospf)

[router-id](#router-id)

[sampler](#sampler)

[scheduler-policy](#scheduler-policy)

[send-community](#send-community)

[seq](#seq)

[service-policy](#service-policy)

[session](#session)

[session-timeout](#session-timeout)

[set as-path](#set-as-path)

[set community](#set-community)

[set copp-action](#set-copp-action)

[set dscp](#set-dscp)

[set extcommunity](#set-extcommunity)

[set interface](#set-interface)

[set ip](#set-ip)

[set ipv6](#set-ipv6)

[set ipv6 next-hop](#set-ipv6-next-hop)

[set local-preference](#set-local-preference)

[set metric](#set-metric)

[set mirror-session](#set-mirror-session)

[set origin](#set-origin)

[set pcp](#set-pcp)

[set traffic-class](#set-traffic-class)

[set trap-action](#set-trap-action)

[set trap-priority](#set-trap-priority)

[set trap-queue](#set-trap-queue)

[sflow agent-id](#sflow-agent-id)

[sflow collector](#sflow-collector)

[sflow enable](#sflow-enable)

[sflow polling-interval](#sflow-polling-interval)

[sflow sampling-rate](#sflow-sampling-rate)

[show PortChannel summary](#show-PortChannel-summary)

[show Vlan](#show-Vlan)

[show aaa](#show-aaa)

[show access-group](#show-access-group)

[show audit-log](#show-audit-log)

[show authentication](#show-authentication)

[show authentication rest](#show-authentication-rest)

[show authentication telemetry](#show-authentication-telemetry)

[show bfd peer](#show-bfd-peer)

[show bfd peer counters](#show-bfd-peer-counters)

[show bfd peers](#show-bfd-peers)

[show bgp all](#show-bgp-all)

[show bgp as-path-access-list](#show-bgp-as-path-access-list)

[show bgp community-list](#show-bgp-community-list)

[show bgp ext-community-list](#show-bgp-ext-community-list)

[show bgp ipv4](#show-bgp-ipv4)

[show bgp ipv6](#show-bgp-ipv6)

[show bgp l2vpn evpn route](#show-bgp-l2vpn-evpn-route)

[show bgp l2vpn evpn route detail](#show-bgp-l2vpn-evpn-route-detail)

[show bgp l2vpn evpn route detail type](#show-bgp-l2vpn-evpn-route-detail-type)

[show bgp l2vpn evpn route rd](#show-bgp-l2vpn-evpn-route-rd)

[show bgp l2vpn evpn route type](#show-bgp-l2vpn-evpn-route-type)

[show bgp l2vpn evpn route vni](#show-bgp-l2vpn-evpn-route-vni)

[show bgp l2vpn evpn summary](#show-bgp-l2vpn-evpn-summary)

[show bgp l2vpn evpn vni](#show-bgp-l2vpn-evpn-vni)

[show buffer_pool](#show-buffer_pool)

[show class-map](#show-class-map)

[show clock](#show-clock)

[show configuration](#show-configuration)

[show copp](#show-copp)

[show core config](#show-core-config)

[show core info](#show-core-info)

[show core list](#show-core-list)

[show crm](#show-crm)

[show database map](#show-database-map)

[show errdisable recovery](#show-errdisable-recovery)

[show evpn](#show-evpn)

[show evpn arp-cache vni](#show-evpn-arp-cache-vni)

[show evpn arp-cache vni all](#show-evpn-arp-cache-vni-all)

[show evpn mac vni](#show-evpn-mac-vni)

[show evpn mac vni all](#show-evpn-mac-vni-all)

[show evpn next-hops vni](#show-evpn-next-hops-vni)

[show evpn next-hops vni all](#show-evpn-next-hops-vni-all)

[show evpn rmac vni](#show-evpn-rmac-vni)

[show evpn rmac vni all](#show-evpn-rmac-vni-all)

[show evpn vni](#show-evpn-vni)

[show evpn vni detail](#show-evpn-vni-detail)

[show hosts](#show-hosts)

[show image](#show-image)

[show interface](#show-interface)

[show interface breakout](#show-interface-breakout)

[show interface-naming](#show-interface-naming)

[show ip access-group](#show-ip-access-group)

[show ip access-lists](#show-ip-access-lists)

[show ip arp](#show-ip-arp)

[show ip arp interface](#show-ip-arp-interface)

[show ip dhcp-relay](#show-ip-dhcp-relay)

[show ip forward-protocol](#show-ip-forward-protocol)

[show ip helper-address](#show-ip-helper-address)

[show ip helper-address statistics](#show-ip-helper-address-statistics)

[show ip igmp](#show-ip-igmp)

[show ip interfaces](#show-ip-interfaces)

[show ip load-share](#show-ip-load-share)

[show ip mroute](#show-ip-mroute)

[show ip ospf](#show-ip-ospf)

[show ip ospf route](#show-ip-ospf-route)

[show ip pim](#show-ip-pim)

[show ip prefix-list](#show-ip-prefix-list)

[show ip route](#show-ip-route)

[show ip sla](#show-ip-sla)

[show ip static-anycast-gateway](#show-ip-static-anycast-gateway)

[show ip vrf](#show-ip-vrf)

[show ip vrf mgmt](#show-ip-vrf-mgmt)

[show ipv6 access-group](#show-ipv6-access-group)

[show ipv6 access-lists](#show-ipv6-access-lists)

[show ipv6 dhcp-relay](#show-ipv6-dhcp-relay)

[show ipv6 interfaces](#show-ipv6-interfaces)

[show ipv6 neighbors](#show-ipv6-neighbors)

[show ipv6 neighbors interface](#show-ipv6-neighbors-interface)

[show ipv6 prefix-list](#show-ipv6-prefix-list)

[show ipv6 route](#show-ipv6-route)

[show ipv6 static-anycast-gateway](#show-ipv6-static-anycast-gateway)

[show kdump files](#show-kdump-files)

[show kdump log](#show-kdump-log)

[show kdump memory](#show-kdump-memory)

[show kdump num-dumps](#show-kdump-num-dumps)

[show kdump status](#show-kdump-status)

[show ldap-server](#show-ldap-server)

[show link state tracking](#show-link-state-tracking)

[show lldp neighbor](#show-lldp-neighbor)

[show lldp table](#show-lldp-table)

[show logging](#show-logging)

[show logging count](#show-logging-count)

[show logging lines](#show-logging-lines)

[show logging servers](#show-logging-servers)

[show mac access-group](#show-mac-access-group)

[show mac access-lists](#show-mac-access-lists)

[show mac address-table](#show-mac-address-table)

[show mac address-table Vlan](#show-mac-address-table-Vlan)

[show mac address-table address](#show-mac-address-table-address)

[show mac address-table aging-time](#show-mac-address-table-aging-time)

[show mac address-table count](#show-mac-address-table-count)

[show mac address-table dynamic](#show-mac-address-table-dynamic)

[show mac address-table interface](#show-mac-address-table-interface)

[show mac address-table static](#show-mac-address-table-static)

[show mac dampening](#show-mac-dampening)

[show mac dampening-disabled-ports](#show-mac-dampening-disabled-ports)

[show mclag brief](#show-mclag-brief)

[show mclag interface](#show-mclag-interface)

[show mclag separate-ip-interfaces](#show-mclag-separate-ip-interfaces)

[show mirror-session](#show-mirror-session)

[show nat](#show-nat)

[show neighbor-suppress-status](#show-neighbor-suppress-status)

[show ntp associations](#show-ntp-associations)

[show ntp global](#show-ntp-global)

[show ntp server](#show-ntp-server)

[show platform environment](#show-platform-environment)

[show platform fanstatus](#show-platform-fanstatus)

[show platform psustatus](#show-platform-psustatus)

[show platform psusummary](#show-platform-psusummary)

[show platform syseeprom](#show-platform-syseeprom)

[show platform temperature](#show-platform-temperature)

[show platform temperature detail](#show-platform-temperature-detail)

[show policy-map](#show-policy-map)

[show port-group](#show-port-group)

[show priority-flow-control](#show-priority-flow-control)

[show priority-group](#show-priority-group)

[show ptp](#show-ptp)

[show ptp clock](#show-ptp-clock)

[show ptp parent](#show-ptp-parent)

[show ptp port](#show-ptp-port)

[show ptp time-property](#show-ptp-time-property)

[show qos](#show-qos)

[show qos map dot1p-tc](#show-qos-map-dot1p-tc)

[show qos map dscp-tc](#show-qos-map-dscp-tc)

[show qos map pfc-priority-queue](#show-qos-map-pfc-priority-queue)

[show qos map tc-dot1p](#show-qos-map-tc-dot1p)

[show qos map tc-dscp](#show-qos-map-tc-dscp)

[show qos map tc-pg](#show-qos-map-tc-pg)

[show qos map tc-queue](#show-qos-map-tc-queue)

[show qos scheduler-policy](#show-qos-scheduler-policy)

[show qos wred-policy](#show-qos-wred-policy)

[show queue](#show-queue)

[show radius-server](#show-radius-server)

[show reboot-cause](#show-reboot-cause)

[show route-map](#show-route-map)

[show running-configuration](#show-running-configuration)

[show running-configuration bfd](#show-running-configuration-bfd)

[show running-configuration bgp](#show-running-configuration-bgp)

[show running-configuration bgp as-path-acess-list](#show-running-configuration-bgp-as-path-acess-list)

[show running-configuration bgp community-list](#show-running-configuration-bgp-community-list)

[show running-configuration bgp extcommunity-list](#show-running-configuration-bgp-extcommunity-list)

[show running-configuration bgp neighbor](#show-running-configuration-bgp-neighbor)

[show running-configuration bgp peer-group](#show-running-configuration-bgp-peer-group)

[show running-configuration class-map](#show-running-configuration-class-map)

[show running-configuration hardware](#show-running-configuration-hardware)

[show running-configuration hardware access-list](#show-running-configuration-hardware-access-list)

[show running-configuration interface](#show-running-configuration-interface)

[show running-configuration interface Loopback](#show-running-configuration-interface-Loopback)

[show running-configuration interface Management](#show-running-configuration-interface-Management)

[show running-configuration interface PortChannel](#show-running-configuration-interface-PortChannel)

[show running-configuration interface Vlan](#show-running-configuration-interface-Vlan)

[show running-configuration interface vxlan](#show-running-configuration-interface-vxlan)

[show running-configuration ip access-list](#show-running-configuration-ip-access-list)

[show running-configuration ip prefix-list](#show-running-configuration-ip-prefix-list)

[show running-configuration ipv6 access-list](#show-running-configuration-ipv6-access-list)

[show running-configuration ipv6 prefix-list](#show-running-configuration-ipv6-prefix-list)

[show running-configuration line vty](#show-running-configuration-line-vty)

[show running-configuration link state tracking](#show-running-configuration-link-state-tracking)

[show running-configuration mac access-list](#show-running-configuration-mac-access-list)

[show running-configuration mirror-session](#show-running-configuration-mirror-session)

[show running-configuration nat](#show-running-configuration-nat)

[show running-configuration ospf](#show-running-configuration-ospf)

[show running-configuration policy-map](#show-running-configuration-policy-map)

[show running-configuration route-map](#show-running-configuration-route-map)

[show running-configuration spanning-tree](#show-running-configuration-spanning-tree)

[show running-configuration tam](#show-running-configuration-tam)

[show service-policy](#show-service-policy)

[show service-policy interface](#show-service-policy-interface)

[show service-policy policy-map](#show-service-policy-policy-map)

[show service-policy summary](#show-service-policy-summary)

[show sflow](#show-sflow)

[show sflow interface](#show-sflow-interface)

[show snmp counters](#show-snmp-counters)

[show snmp-server](#show-snmp-server)

[show snmp-server community](#show-snmp-server-community)

[show snmp-server group](#show-snmp-server-group)

[show snmp-server host](#show-snmp-server-host)

[show snmp-server user](#show-snmp-server-user)

[show snmp-server view](#show-snmp-server-view)

[show spanning-tree](#show-spanning-tree)

[show spanning-tree bpdu-guard](#show-spanning-tree-bpdu-guard)

[show spanning-tree counters](#show-spanning-tree-counters)

[show spanning-tree inconsistentports](#show-spanning-tree-inconsistentports)

[show ssh-server vrf](#show-ssh-server-vrf)

[show ssh-server vrfs](#show-ssh-server-vrfs)

[show storm-control](#show-storm-control)

[show storm-control interface](#show-storm-control-interface)

[show switch-profiles](#show-switch-profiles)

[show switch-resource drop-monitor](#show-switch-resource-drop-monitor)

[show swsslog-configuration](#show-swsslog-configuration)

[show system](#show-system)

[show system cpu](#show-system-cpu)

[show system memory](#show-system-memory)

[show system processes](#show-system-processes)

[show system processes cpu](#show-system-processes-cpu)

[show system processes mem-usage](#show-system-processes-mem-usage)

[show system processes mem-util](#show-system-processes-mem-util)

[show system processes pid](#show-system-processes-pid)

[show tacacs-server](#show-tacacs-server)

[show tacacs-server global](#show-tacacs-server-global)

[show tacacs-server host](#show-tacacs-server-host)

[show tam collectors](#show-tam-collectors)

[show tam drop-monitor](#show-tam-drop-monitor)

[show tam drop-monitor sessions](#show-tam-drop-monitor-sessions)

[show tam features](#show-tam-features)

[show tam flowgroups](#show-tam-flowgroups)

[show tam ifa](#show-tam-ifa)

[show tam ifa sessions](#show-tam-ifa-sessions)

[show tam samplers](#show-tam-samplers)

[show tam switch](#show-tam-switch)

[show tam tail-stamping](#show-tam-tail-stamping)

[show tam tail-stamping sessions](#show-tam-tail-stamping-sessions)

[show tech-support](#show-tech-support)

[show threshold breaches](#show-threshold-breaches)

[show threshold buffer-pool](#show-threshold-buffer-pool)

[show threshold priority-group](#show-threshold-priority-group)

[show threshold queue](#show-threshold-queue)

[show tpcm](#show-tpcm)

[show udld global](#show-udld-global)

[show udld interface](#show-udld-interface)

[show udld neighbors](#show-udld-neighbors)

[show udld statistics](#show-udld-statistics)

[show udld statistics interface](#show-udld-statistics-interface)

[show uptime](#show-uptime)

[show users](#show-users)

[show version](#show-version)

[show vrrp](#show-vrrp)

[show vrrp6](#show-vrrp6)

[show vxlan interface](#show-vxlan-interface)

[show vxlan remote mac](#show-vxlan-remote-mac)

[show vxlan remote mac count](#show-vxlan-remote-mac-count)

[show vxlan remote vni](#show-vxlan-remote-vni)

[show vxlan remote vni count](#show-vxlan-remote-vni-count)

[show vxlan tunnel](#show-vxlan-tunnel)

[show vxlan tunnel count](#show-vxlan-tunnel-count)

[show vxlan vlanvnimap](#show-vxlan-vlanvnimap)

[show vxlan vlanvnimap count](#show-vxlan-vlanvnimap-count)

[show vxlan vrfvnimap](#show-vxlan-vrfvnimap)

[show vxlan vrfvnimap count](#show-vxlan-vrfvnimap-count)

[show warm-restart](#show-warm-restart)

[show watermark interval](#show-watermark-interval)

[show watermark telemetry](#show-watermark-telemetry)

[show ztp-status](#show-ztp-status)

[shutdown](#shutdown)

[snmp-server agentaddress](#snmp-server-agentaddress)

[snmp-server community](#snmp-server-community)

[snmp-server contact](#snmp-server-contact)

[snmp-server enable](#snmp-server-enable)

[snmp-server engine](#snmp-server-engine)

[snmp-server group](#snmp-server-group)

[snmp-server host](#snmp-server-host)

[snmp-server location](#snmp-server-location)

[snmp-server user](#snmp-server-user)

[snmp-server view](#snmp-server-view)

[soft-reconfiguration](#soft-reconfiguration)

[solo](#solo)

[source-address](#source-address)

[source-interface](#source-interface)

[source-interface Ethernet](#source-interface-Ethernet)

[source-interface PortChannel](#source-interface-PortChannel)

[source-interface Vlan](#source-interface-Vlan)

[source-ip](#source-ip)

[source-port](#source-port)

[source-vrf](#source-vrf)

[spanning-tree bpdufilter](#spanning-tree-bpdufilter)

[spanning-tree bpduguard](#spanning-tree-bpduguard)

[spanning-tree cost](#spanning-tree-cost)

[spanning-tree edge-port](#spanning-tree-edge-port)

[spanning-tree enable](#spanning-tree-enable)

[spanning-tree forward-time](#spanning-tree-forward-time)

[spanning-tree guard](#spanning-tree-guard)

[spanning-tree hello-time](#spanning-tree-hello-time)

[spanning-tree link-type](#spanning-tree-link-type)

[spanning-tree loopguard](#spanning-tree-loopguard)

[spanning-tree max-age](#spanning-tree-max-age)

[spanning-tree mode](#spanning-tree-mode)

[spanning-tree port](#spanning-tree-port)

[spanning-tree port-priority](#spanning-tree-port-priority)

[spanning-tree portfast](#spanning-tree-portfast)

[spanning-tree priority](#spanning-tree-priority)

[spanning-tree uplinkfast](#spanning-tree-uplinkfast)

[spanning-tree vlan](#spanning-tree-vlan)

[speed](#speed)

[ssh-server vrf](#ssh-server-vrf)

[static](#static)

[storm-control broadcast](#storm-control-broadcast)

[storm-control unknown-multicast](#storm-control-unknown-multicast)

[storm-control unknown-unicast](#storm-control-unknown-unicast)

[strict-capability-match](#strict-capability-match)

[switch-id](#switch-id)

[switch-resource](#switch-resource)

[switchport access](#switchport-access)

[switchport trunk](#switchport-trunk)

[swsslog](#swsslog)

[table-map](#table-map)

[tacacs-server auth-type](#tacacs-server-auth-type)

[tacacs-server host](#tacacs-server-host)

[tacacs-server key](#tacacs-server-key)

[tacacs-server source-interface](#tacacs-server-source-interface)

[tacacs-server timeout](#tacacs-server-timeout)

[tail-stamping](#tail-stamping)

[tam](#tam)

[tcp-connect](#tcp-connect)

[tcp-timeout](#tcp-timeout)

[terminal length](#terminal-length)

[threshold](#threshold)

[threshold buffer-pool](#threshold-buffer-pool)

[threshold priority-group](#threshold-priority-group)

[threshold queue](#threshold-queue)

[timeout](#timeout)

[timers](#timers)

[tos](#tos)

[tpcm install](#tpcm-install)

[tpcm uninstall](#tpcm-uninstall)

[tpcm upgrade](#tpcm-upgrade)

[traceroute](#traceroute)

[traceroute vrf](#traceroute-vrf)

[traceroute vrf mgmt](#traceroute-vrf-mgmt)

[traceroute6](#traceroute6)

[traceroute6 vrf](#traceroute6-vrf)

[traceroute6 vrf mgmt](#traceroute6-vrf-mgmt)

[track-interface](#track-interface)

[traffic-class](#traffic-class)

[transmit-interval](#transmit-interval)

[ttl](#ttl)

[ttl-security hops](#ttl-security-hops)

[type](#type)

[udld aggressive](#udld-aggressive)

[udld enable](#udld-enable)

[udld message-time](#udld-message-time)

[udld multiplier](#udld-multiplier)

[udp-timeout](#udp-timeout)

[unsuppress-map](#unsuppress-map)

[update-delay](#update-delay)

[update-source](#update-source)

[use-v2-checksum](#use-v2-checksum)

[username](#username)

[version](#version)

[vip](#vip)

[vni](#vni)

[vrrp](#vrrp)

[warm-reboot](#warm-reboot)

[warm-restart bgp](#warm-restart-bgp)

[warm-restart bgp enable](#warm-restart-bgp-enable)

[warm-restart bgp eoiu](#warm-restart-bgp-eoiu)

[warm-restart swss](#warm-restart-swss)

[warm-restart swss enable](#warm-restart-swss-enable)

[warm-restart system](#warm-restart-system)

[warm-restart teamd](#warm-restart-teamd)

[warm-restart teamd enable](#warm-restart-teamd-enable)

[watermark interval](#watermark-interval)

[watermark telemetry](#watermark-telemetry)

[weight](#weight)

[write](#write)

[write erase](#write-erase)

[write erase boot](#write-erase-boot)

[write erase install](#write-erase-install)

[write-multiplier](#write-multiplier)

[write-quanta](#write-quanta)

[ztp](#ztp)

# CLIs By Feature 

##  COREDUMP  

[core enable](#core-enable)

[show core list](#show-core-list)

[show core config](#show-core-config)

[show core info](#show-core-info)

##  ERRDISABLE  

[show errdisable recovery](#show-errdisable-recovery)

[errdisable recovery cause](#errdisable-recovery-cause)

[errdisable recovery interval](#errdisable-recovery-interval)

##  HASH  

[show ip load-share](#show-ip-load-share)

##  IP Helper  

[show ip forward-protocol](#show-ip-forward-protocol)

[show ip helper-address](#show-ip-helper-address)

[show ip helper-address statistics](#show-ip-helper-address-statistics)

[clear ip helper-address statistics](#clear-ip-helper-address-statistics)

[ip forward-protocol udp enable](#ip-forward-protocol-udp-enable)

[ip forward-protocol udp include](#ip-forward-protocol-udp-include)

[ip forward-protocol udp exclude](#ip-forward-protocol-udp-exclude)

[ip forward-protocol udp rate-limit](#ip-forward-protocol-udp-rate-limit)

[ip helper-address](#ip-helper-address)

[ip helper-address vrf](#ip-helper-address-vrf)

[ip helper-address](#ip-helper-address)

[ip helper-address vrf](#ip-helper-address-vrf)

[ip helper-address](#ip-helper-address)

[ip helper-address vrf](#ip-helper-address-vrf)

##  IPSLA  

[show ip sla](#show-ip-sla)

##  IPv4 Unnumbered Interface  

[ip unnumbered](#ip-unnumbered)

[ip unnumbered](#ip-unnumbered)

[ip unnumbered](#ip-unnumbered)

##  KDUMP  

[kdump enable](#kdump-enable)

[kdump memory](#kdump-memory)

[kdump num-dumps](#kdump-num-dumps)

[show kdump status](#show-kdump-status)

[show kdump memory](#show-kdump-memory)

[show kdump num-dumps](#show-kdump-num-dumps)

[show kdump files](#show-kdump-files)

[show kdump log](#show-kdump-log)

##  LLDP  

[lldp enable](#lldp-enable)

[lldp](#lldp)

[lldp timer](#lldp-timer)

[lldp multiplier](#lldp-multiplier)

[lldp system-name](#lldp-system-name)

[lldp system-description](#lldp-system-description)

[lldp tlv-select](#lldp-tlv-select)

[lldp](#lldp)

[lldp enable](#lldp-enable)

##  NAT  

[show configuration](#show-configuration)

##  OSPFv2  

[router ospf](#router-ospf)

[show configuration](#show-configuration)

[area](#area)

[auto-cost](#auto-cost)

[compatible](#compatible)

[default-information](#default-information)

[default-metric](#default-metric)

[distance](#distance)

[log-adjacency-changes](#log-adjacency-changes)

[max-metric](#max-metric)

[network](#network)

[ospf](#ospf)

[ospf abr-type](#ospf-abr-type)

[ospf router-id](#ospf-router-id)

[passive-interface](#passive-interface)

[redistribute](#redistribute)

[refresh](#refresh)

[timers](#timers)

[write-multiplier](#write-multiplier)

[ip ospf](#ip-ospf)

[ip ospf area](#ip-ospf-area)

[ip ospf authentication](#ip-ospf-authentication)

[ip ospf authentication-key](#ip-ospf-authentication-key)

[ip ospf bfd](#ip-ospf-bfd)

[ip ospf cost](#ip-ospf-cost)

[ip ospf hello-interval](#ip-ospf-hello-interval)

[ip ospf message-digest-key](#ip-ospf-message-digest-key)

[ip ospf mtu-ignore](#ip-ospf-mtu-ignore)

[ip ospf network](#ip-ospf-network)

[ip ospf priority](#ip-ospf-priority)

[ip ospf retransmit-interval](#ip-ospf-retransmit-interval)

[ip ospf transmit-delay](#ip-ospf-transmit-delay)

[ip ospf](#ip-ospf)

[ip ospf area](#ip-ospf-area)

[ip ospf authentication](#ip-ospf-authentication)

[ip ospf authentication-key](#ip-ospf-authentication-key)

[ip ospf bfd](#ip-ospf-bfd)

[ip ospf cost](#ip-ospf-cost)

[ip ospf hello-interval](#ip-ospf-hello-interval)

[ip ospf message-digest-key](#ip-ospf-message-digest-key)

[ip ospf mtu-ignore](#ip-ospf-mtu-ignore)

[ip ospf network](#ip-ospf-network)

[ip ospf priority](#ip-ospf-priority)

[ip ospf retransmit-interval](#ip-ospf-retransmit-interval)

[ip ospf transmit-delay](#ip-ospf-transmit-delay)

[ip ospf](#ip-ospf)

[ip ospf area](#ip-ospf-area)

[ip ospf authentication](#ip-ospf-authentication)

[ip ospf authentication-key](#ip-ospf-authentication-key)

[ip ospf bfd](#ip-ospf-bfd)

[ip ospf cost](#ip-ospf-cost)

[ip ospf hello-interval](#ip-ospf-hello-interval)

[ip ospf message-digest-key](#ip-ospf-message-digest-key)

[ip ospf mtu-ignore](#ip-ospf-mtu-ignore)

[ip ospf network](#ip-ospf-network)

[ip ospf priority](#ip-ospf-priority)

[ip ospf retransmit-interval](#ip-ospf-retransmit-interval)

[ip ospf transmit-delay](#ip-ospf-transmit-delay)

[ip ospf](#ip-ospf)

[ip ospf area](#ip-ospf-area)

[ip ospf authentication](#ip-ospf-authentication)

[ip ospf authentication-key](#ip-ospf-authentication-key)

[ip ospf bfd](#ip-ospf-bfd)

[ip ospf cost](#ip-ospf-cost)

[ip ospf hello-interval](#ip-ospf-hello-interval)

[ip ospf message-digest-key](#ip-ospf-message-digest-key)

[ip ospf mtu-ignore](#ip-ospf-mtu-ignore)

[ip ospf network](#ip-ospf-network)

[ip ospf priority](#ip-ospf-priority)

[ip ospf retransmit-interval](#ip-ospf-retransmit-interval)

[ip ospf transmit-delay](#ip-ospf-transmit-delay)

[show ip ospf](#show-ip-ospf)

[show ip ospf route](#show-ip-ospf-route)

##  VRRP  

[show vrrp](#show-vrrp)

[show vrrp6](#show-vrrp6)

##  ZTP  

[show ztp-status](#show-ztp-status)

[ztp](#ztp)

## aaa authentication failthrough 

### Description 

```
Configures AAA authentication failthrough

```
### Parent Commands (Modes) 

```
configure terminal

```
### Syntax 

```
aaa authentication failthrough <enable>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| enable | enable or disable  | Select [enable(enable) disable(disable) ]  |


### Examples 

```
sonic(config)# aaa authentication failthrough enable


```
## aaa authentication login default 
### Description 
```
Configures AAA login authentication default list to authentication first with tacacs+, and, if none respond, (or failthrough is configured) with local next
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
aaa authentication login default { { [ group { { [ ldap [ local ] ] } | { [ radius [ local ] ] } | { [ tacacs+ [ local ] ] } } ] } | { [ local { [ group { [ ldap ] | [ radius ] | [ tacacs+ ] } ] } ] } }
no aaa authentication login default
```
### Examples 
```
sonic(config)# aaa authentication login default group tacacs+ local
```
## aaa authentication login-method 
### Description 
```
AAA authentication login method preference 
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
aaa authentication login-method { { [ local [ <tacacs+> ] [ <radius> ] ] } | { [ tacacs+ [ <local> ] ] } | { [ radius [ <local> ] ] } }
no aaa authentication login-method
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| tacacs+ |   | Select [tacacs+(tacacs+) ]  |
| radius |   | Select [radius(radius) ]  |
| local |   | Select [local(local) ]  |
## aaa authorization login default 
### Description 
```
Configures AAA login authorization default list to use ldap
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
aaa authorization login default { { [ group ldap ] } | [ local ] }
no aaa authorization login default
```
### Examples 
```
sonic(config)# aaa authorization login default group ldap
```
## aaa name-service group 
### Description 
```
Configures AAA group name-service to use ldap
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
aaa name-service group { { [ group ldap ] } | [ local ] | [ login ] }
no aaa name-service group
```
### Examples 
```
sonic(config)# aaa name-service group group ldap
```
## aaa name-service netgroup 
### Description 
```
Configures AAA netgroup name-service to use ldap
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
aaa name-service netgroup { { [ group ldap ] } | [ local ] }
no aaa name-service netgroup
```
### Examples 
```
sonic(config)# aaa name-service netgroup group ldap
```
## aaa name-service passwd 
### Description 
```
Configures AAA passwd name-service to use ldap
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
aaa name-service passwd { { [ group ldap ] } | [ local ] | [ login ] }
no aaa name-service passwd
```
### Examples 
```
sonic(config)# aaa name-service passwd group ldap
```
## aaa name-service shadow 
### Description 
```
Configures AAA shadow name-service to use ldap
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
aaa name-service shadow { { [ group ldap ] } | [ local ] | [ login ] }
no aaa name-service shadow
```
### Examples 
```
sonic(config)# aaa name-service shadow group ldap
```
## aaa name-service sudoers 
### Description 
```
Configures AAA sudoers name-service to use ldap
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
aaa name-service sudoers { { [ group ldap ] } | [ local ] }
no aaa name-service sudoers
```
### Examples 
```
sonic(config)# aaa name-service sudoers group ldap
```
## access-list 

### Description 

```
Configure ACL parameters 


```
### Parent Commands (Modes) 

```
hardware

```
### Syntax 

```
access-list

```
## activate 
### Description 
```
This command enables/activates a particular address-family for a BGP
neighbor
```
### Parent Commands (Modes) 
```
address-family l2vpn evpn
```
### Syntax 
```
activate
no activate
```
### Usage Guidelines 
```
Use this command to activate an address-family for a BGP neighbor.
This command can be executed multiple times to enable multiple address
familities for a BGP neighbor
```
### Examples 
#### Following command enables l2vpn evpn address family            for a BGP neighbor 20.20.20.2 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 20.20.20.2
sonic(config-router-bgp-neighbor)# remote-as 300
sonic(config-router-bgp-neighbor)# address-family l2vpn evpn
sonic(config-router-bgp-neighbor-af)# activate
```
## activate 
### Description 
```
This command enables/activates a particular address-family for a BGP
peer-group
```
### Parent Commands (Modes) 
```
address-family l2vpn evpn
```
### Syntax 
```
activate
no activate
```
### Usage Guidelines 
```
Use this command to activate an address-family for a BGP peer-group.
This command can be executed multiple times to enable multiple address
familities for a BGP peer-group
```
### Examples 
#### Following command enables l2vpn evpn address family            for a BGP peer-group PG_External 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# remote-as 300
sonic(config-router-bgp-pg)# address-family l2vpn evpn
sonic(config-router-bgp-pg-af)# activate
```
## activate 
### Description 
```
Enable the Address Family for this Neighbor 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
activate
no activate
```
## activate 
### Description 
```
Enable the Address Family for this Neighbor 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
activate
no activate
```
## activate 
### Description 
```
This command enables/activates a particular address-family for a BGP
neighbor
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
activate
no activate
```
### Usage Guidelines 
```
Use this command to activate an address-family for a BGP neighbor.
This command can be executed multiple times to enable multiple address
familities for a BGP neighbor
```
### Examples 
#### Following command enables ipv4 unicast address family            for a BGP neighbor 20.20.20.2 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 20.20.20.2
sonic(config-router-bgp-neighbor)# remote-as 300
sonic(config-router-bgp-neighbor)# address-family ipv4 unicast
sonic(config-router-bgp-neighbor-af)# activate
```
## activate 
### Description 
```
This command enables/activates a particular address-family for a BGP
peer-group
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
activate
no activate
```
### Usage Guidelines 
```
Use this command to activate an address-family for a BGP peer-group.
This command can be executed multiple times to enable multiple address
familities for a BGP peer-group
```
### Examples 
#### Following command enables ipv4 unicast address family            for a BGP peer-group PG_External 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# remote-as 300
sonic(config-router-bgp-pg)# address-family ipv4 unicast
sonic(config-router-bgp-pg-af)# activate
```
## addpath-tx-all-paths 
### Description 
```
Use addpath to advertise all paths to a neighbor 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
addpath-tx-all-paths
no addpath-tx-all-paths
```
## addpath-tx-all-paths 
### Description 
```
Use addpath to advertise all paths to a neighbor 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
addpath-tx-all-paths
no addpath-tx-all-paths
```
## addpath-tx-all-paths 
### Description 
```
This command enables BGP to advertise all paths to a neighbor.
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
addpath-tx-all-paths
no addpath-tx-all-paths
```
### Usage Guidelines 
```
Use this command for BGP add path feature configuration. This command
allows all BGP paths to be advertised to a neighbor
```
### Examples 
#### Following command configures BGP to advertise          all paths to a neighbor 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 20.20.20.2
sonic(config-router-bgp-neighbor)# remote-as 300
sonic(config-router-bgp-neighbor)# address-family ipv4 unicast
sonic(config-router-bgp-neighbor-af)# addpath-tx-all-paths
```
## addpath-tx-all-paths 
### Description 
```
This command enables BGP to advertise all paths to neighbors in a
peer-group.
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
addpath-tx-all-paths
no addpath-tx-all-paths
```
### Usage Guidelines 
```
Use this command for BGP add path feature configuration. This command
allows all BGP paths to be advertised to neighbors in a peer-group
```
### Examples 
#### Following command configures BGP to advertise all paths          to neighbors in a peer-group 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# address-family ipv4 unicast
sonic(config-router-bgp-pg-af)# addpath-tx-all-paths
```
## addpath-tx-bestpath-per-as 
### Description 
```
Use addpath to advertise the bestpath per each neighboring AS 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
addpath-tx-bestpath-per-as
no addpath-tx-bestpath-per-as
```
## addpath-tx-bestpath-per-as 
### Description 
```
Use addpath to advertise the bestpath per each neighboring AS 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
addpath-tx-bestpath-per-as
no addpath-tx-bestpath-per-as
```
## addpath-tx-bestpath-per-as 
### Description 
```
This command enables BGP to advertise best paths to a neighbor.
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
addpath-tx-bestpath-per-as
no addpath-tx-bestpath-per-as
```
### Usage Guidelines 
```
Use this command for BGP add path feature configuration. This command
allows best BGP path to be advertised to a neighbor
```
### Examples 
#### Following command configures BGP to advertise best path to a neighbor 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 20.20.20.2
sonic(config-router-bgp-neighbor)# remote-as 300
sonic(config-router-bgp-neighbor)# address-family ipv4 unicast
sonic(config-router-bgp-neighbor-af)# addpath-tx-bestpath-per-as
```
## addpath-tx-bestpath-per-as 
### Description 
```
This command enables BGP to advertise best paths to neighbors in a
peer-group.
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
addpath-tx-bestpath-per-as
no addpath-tx-bestpath-per-as
```
### Usage Guidelines 
```
Use this command for BGP add path feature configuration. This command
allows best BGP path to be advertised to neighbors in a peer-group
```
### Examples 
#### Following command configures BGP to advertise best path          to neighbors in a peer-group 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# address-family ipv4 unicast
sonic(config-router-bgp-pg-af)# addpath-tx-bestpath-per-as
```
## address-family ipv4 
### Description 
```
This command enters into IPv4 Unicast address-family configure CLI
context
```
### Parent Commands (Modes) 
```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }
```
### Syntax 
```
address-family ipv4 unicast
no address-family ipv4 unicast
```
### Usage Guidelines 
```
Use this command to switch to IPv4 Unicast address family CLI context to
configure parameters specific to this address family
```
### Examples 
#### Below command switches the CLI context to IPv4 Unicast          address family 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# address-family ipv4 unicast
sonic(config-router-bgp-af)#
```
## address-family ipv4 
### Description 
```
This command enters into IPv4 Unicast address-family configuration CLI
context for a BGP neighbor
```
### Parent Commands (Modes) 
```
neighbor { <ip> | { interface { Ethernet | PortChannel | Vlan } } }
```
### Syntax 
```
address-family ipv4 unicast
no address-family ipv4 unicast
```
### Usage Guidelines 
```
Use this command to switch to IPv4 Unicast address family CLI context of
a BGP neighbor to configure parameters specific to this address family
```
### Examples 
#### Below command switches the CLI context to IPv4 Unicast          address family for neighbor 30.30.30.3 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# neighbor 30.30.30.3
sonic(config-router-bgp-neighbor)# address-family ipv4 unicast
sonic(config-router-bgp-neighbor-af)#
```
## address-family ipv4 
### Description 
```
This command switches the CLI context into IPv4 Unicast address-family
mode for a BGP peer-group
```
### Parent Commands (Modes) 
```
peer-group <template-str>
```
### Syntax 
```
address-family ipv4 unicast
no address-family ipv4 unicast
```
### Usage Guidelines 
```
Use this command to switch to IPv4 Unicast address family CLI context of
a BGP peer-group to configure parameters specific to this address family
```
### Examples 
#### Below command switches the CLI context to IPv4 Unicast          address family for peer-group PG_Ext 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# address-family ipv4 unicast
sonic(config-router-bgp-pg-af)#
```
## address-family ipv6 
### Description 
```
This command execution enters into IPv6 Unicast address-family configure CLI
context
```
### Parent Commands (Modes) 
```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }
```
### Syntax 
```
address-family ipv6 unicast
no address-family ipv6 unicast
```
### Usage Guidelines 
```
Use this command to switch to IPv6 Unicast address family CLI context to
configure parameters specific to this address family
```
### Examples 
#### Below command switches the CLI context to IPv6 Unicast          address family 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# address-family ipv6 unicast
sonic(config-router-bgp-af)#
```
## address-family ipv6 
### Description 
```
This command execution enters into IPv6 Unicast address-family configuration CLI
context for a BGP neighbor
```
### Parent Commands (Modes) 
```
neighbor { <ip> | { interface { Ethernet | PortChannel | Vlan } } }
```
### Syntax 
```
address-family ipv6 unicast
no address-family ipv6 unicast
```
### Usage Guidelines 
```
Use this command to switch to IPv6 Unicast address family CLI context of
a BGP neighbor to configure parameters specific to this address family
```
### Examples 
#### Below command switches the CLI context to IPv6 Unicast          address family for neighbor 30.30.30.3 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# neighbor 30.30.30.3
sonic(config-router-bgp-neighbor)# address-family ipv6 unicast
sonic(config-router-bgp-neighbor-af)#
```
## address-family ipv6 
### Description 
```
This command switches the CLI context into IPv6 Unicast address-family
mode for a BGP peer-group
```
### Parent Commands (Modes) 
```
peer-group <template-str>
```
### Syntax 
```
address-family ipv6 unicast
no address-family ipv6 unicast
```
### Usage Guidelines 
```
Use this command to switch to IPv6 Unicast address family CLI context of
a BGP peer-group to configure parameters specific to this address family
```
### Examples 
#### Below command switches the CLI context to IPv6 Unicast          address family for peer-group PG_Ext 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# address-family ipv6 unicast
sonic(config-router-bgp-pg-af)#
```
## address-family l2vpn 
### Description 
```
This command execution enters into l2vpn evpn address-family configure CLI
context
```
### Parent Commands (Modes) 
```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }
```
### Syntax 
```
address-family l2vpn evpn
no address-family l2vpn evpn
```
### Usage Guidelines 
```
Use this command to switch to l2vpn evpn address family CLI context to
configure parameters specific to this address family
```
### Examples 
#### Below command switches the CLI context to l2vpn evpn          address family 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# address-family l2vpn evpn
sonic(config-router-bgp-af)#
```
## address-family l2vpn 
### Description 
```
This command execution enters into L2VPN EVPN address-family configuration CLI
context for a BGP neighbor
```
### Parent Commands (Modes) 
```
neighbor { <ip> | { interface { Ethernet | PortChannel | Vlan } } }
```
### Syntax 
```
address-family l2vpn evpn
no address-family l2vpn evpn
```
### Usage Guidelines 
```
Use this command to switch to L2VPN EVPN address family CLI context of
a BGP neighbor to configure parameters specific to this address family
```
### Examples 
#### Below command switches the CLI context to L2VPN EVPN          address family for neighbor 30.30.30.3 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# neighbor 30.30.30.3
sonic(config-router-bgp-neighbor)# address-family l2vpn evpn
sonic(config-router-bgp-neighbor-af)#
```
## address-family l2vpn 
### Description 
```
This command switches the CLI context into L2VPN EVPN address-family
mode for a BGP peer-group
```
### Parent Commands (Modes) 
```
peer-group <template-str>
```
### Syntax 
```
address-family l2vpn evpn
no address-family l2vpn evpn
```
### Usage Guidelines 
```
Use this command to switch to L2VPN EVPN address family CLI context of
a BGP peer-group to configure parameters specific to this address family
```
### Examples 
#### Below command switches the CLI context to L2VPN EVPN          address family for peer-group PG_Ext 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# address-family l2vpn evpn
sonic(config-router-bgp-pg-af)#
```
## advertise ipv4 unicast 
### Description 
```
This command enables tenant VRFs to announce IPv4 prefixes as EVPN type-5 routes
```
### Parent Commands (Modes) 
```
address-family l2vpn evpn
```
### Syntax 
```
advertise ipv4 unicast
no advertise ipv4 unicast
```
### Usage Guidelines 
```
[no] advertise ipv4 unicast
```
### Examples 
#### Following command enables vrf Vrf1 to announce IPv4 prefixes 
```
sonic# configure terminal
sonic(config)# router bgp 100 vrf Vrf1
sonic(config-router-bgp)# address-family l2vpn evpn
sonic(config-router-bgp-af)# advertise ipv4 unicast
```
## advertise ipv6 unicast 
### Description 
```
This command enables tenant VRFs to announce IPv6 prefixes as EVPN type-5 routes
```
### Parent Commands (Modes) 
```
address-family l2vpn evpn
```
### Syntax 
```
advertise ipv6 unicast
no advertise ipv6 unicast
```
### Usage Guidelines 
```
[no] advertise ipv6 unicast
```
### Examples 
#### Following command enables vrf Vrf1 to announce IPv6 prefixes 
```
sonic# configure terminal
sonic(config)# router bgp 100 vrf Vrf1
sonic(config-router-bgp)# address-family l2vpn evpn
sonic(config-router-bgp-af)# advertise ipv6 unicast
```
## advertise-all-vni 
### Description 
```
This command enables BGP control plane for all locally-configured VNIs
```
### Parent Commands (Modes) 
```
address-family l2vpn evpn
```
### Syntax 
```
advertise-all-vni
no advertise-all-vni
```
### Usage Guidelines 
```
[no] advertise-all-vni
```
### Examples 
#### Following command enables BGP control plane for all VNIs 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# address-family l2vpn evpn
sonic(config-router-bgp-af)# advertise-all-vni
```
## advertise-default-gw 
### Description 
```
This command enables gateways VTEPs to advertise their IP/MAC addresses
```
### Parent Commands (Modes) 
```
address-family l2vpn evpn
```
### Syntax 
```
advertise-default-gw
no advertise-default-gw
```
### Usage Guidelines 
```
[no] advertise-default-gw
```
### Examples 
#### Enable gateways VTEPs to advertise theie IP/MAC addresses 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# address-family l2vpn evpn
sonic(config-router-bgp-af)# advertise-default-gw
```
## advertise-default-gw 
### Description 
```
This command enables gateway advertisement for a particular VNI
```
### Parent Commands (Modes) 
```
vni <vninum>
```
### Syntax 
```
advertise-default-gw
no advertise-default-gw
```
### Usage Guidelines 
```
[no] advertise-default-gw
```
### Examples 
#### Enable VNI 100 gateway to advertise theie IP/MAC addresses 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# address-family l2vpn evpn
sonic(config-router-bgp-af)# vni 100
sonic(config-router-bgp-af-vni)# advertise-default-gw
```
## advertise-svi-ip 
### Description 
```
This command enables svi mac-ip routes advertisement into EVPN
```
### Parent Commands (Modes) 
```
address-family l2vpn evpn
```
### Syntax 
```
advertise-svi-ip
no advertise-svi-ip
```
### Usage Guidelines 
```
[no] advertise-svi-ip
```
### Examples 
#### Enable to advertise SVI MACIP routes in EVPN 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# address-family l2vpn evpn
sonic(config-router-bgp-af)# advertise-svi-ip
```
## advertise-svi-ip 
### Description 
```
This command enables svi mac-ip routes advertisement into EVPN for a particular VNI
```
### Parent Commands (Modes) 
```
vni <vninum>
```
### Syntax 
```
advertise-svi-ip
no advertise-svi-ip
```
### Usage Guidelines 
```
[no] advertise-svi-ip
```
### Examples 
#### Enable VNI 100 gateway to advertise SVI MACIP route in EVPN 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# address-family l2vpn evpn
sonic(config-router-bgp-af)# vni 100
sonic(config-router-bgp-af-vni)# advertise-svi-ip
```
## advertisement-interval 
### Description 
```
This command sets the minimum interval between sending BGP routing
updates to a neighbor
```
### Parent Commands (Modes) 
```
neighbor { <ip> | { interface { Ethernet | PortChannel | Vlan } } }
```
### Syntax 
```
advertisement-interval <tval>
no advertisement-interval
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| tval |   | Integer  |
### Usage Guidelines 
```
Use this command to set the minmum advertisement interval for BGP
updates
```
### Examples 
#### Following command sets the minimum advertisement          interval to 10sec 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 30.30.30.3
sonic(config-router-bgp-neighbor)# advertisement-interval 10
```
## advertisement-interval 
### Description 
```
This command sets the minimum interval between sending BGP routing
updates to neighbors in a peer-group
```
### Parent Commands (Modes) 
```
peer-group <template-str>
```
### Syntax 
```
advertisement-interval <tval>
no advertisement-interval
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| tval |   | Integer  |
### Usage Guidelines 
```
Use this command to set the minmum advertisement interval for BGP
updates
```
### Examples 
#### Following command sets the minimum advertisement          interval to 10sec 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# advertisement-interval 10
```
## advertisement-interval 
### Description 
```
Configure advertisment-interval for IPv4 VRRP instance
```
### Parent Commands (Modes) 
```
vrrp ipv4
```
### Syntax 
```
advertisement-interval <adv_interval>
no advertisement-interval
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| adv_interval |   | Integer  |
### Examples 
#### Configure advertisment-interval for IPv4 VRRP instance 
```
sonic(config)# interface Ethernet4
sonic(conf-if-Ethernet4)#
sonic(conf-if-Ethernet4)# vrrp 1 address-family ipv4
sonic(conf-if-Ethernet4-vrrp-ipv4-1)#advertisement-interval 5
```
## advertisement-interval 
### Description 
```
Configure advertisment-interval for IPv6 VRRP instance
```
### Parent Commands (Modes) 
```
vrrp ipv6
```
### Syntax 
```
advertisement-interval <adv_interval>
no advertisement-interval
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| adv_interval |   | Integer  |
### Examples 
#### Configure advertisment-interval for IPv6 VRRP instance 
```
sonic(config)# interface Ethernet4
sonic(conf-if-Ethernet4)#
sonic(conf-if-Ethernet4)# vrrp 1 address-family ipv6
sonic(conf-if-Ethernet4-vrrp-ipv6-1)#advertisement-interval 2
```
## aggregate-address 
### Description 
```
Configure BGP aggregate entries 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
aggregate-address <prefix> { [ as-set ] [ summary-only ] { [ route-map <rtemap> ] } }
no aggregate-address <prefix> { [ as-set ] [ summary-only ] { [ route-map <rtemap> ] } }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| prefix | A::B/mask  | String  |
| rtemap | WORD  | String  |
## aggregate-address 
### Description 
```
This command configures an aggregate address and enables aggregation of
routes that falls in the aggregate address subnet
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
aggregate-address <prefix> { [ as-set ] [ summary-only ] { [ route-map <rtemap> ] } }
no aggregate-address <prefix> { [ as-set ] [ summary-only ] { [ route-map <rtemap> ] } }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| prefix | A.B.C.D/mask  | String  |
| rtemap | WORD  | String  |
### Usage Guidelines 
```
This command enables user to turn on aggregation of BGP routes.
"summary-only" option filters out all the aggregates routes and only the
aggregate address will be advertised by BGP. "as-set" option will make
sure that AS Path of individual aggregated routes are also included in
the resulting aggregate route. "route-map" option gives user a finer
control over the route's attributes
```
### Examples 
#### This configuration example setup the aggregate-address          under ipv4 address-family 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# address-family ipv4 unicast
sonic(config-router-bgp-af)# aggregate-address 17.35.0.0/16
```
## aging-interval 
### Description 
```
This command configures aging interval.
```
### Parent Commands (Modes) 
```
drop-monitor
```
### Syntax 
```
aging-interval <aging_interval>
no aging-interval
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| aging_interval | Aging interval  | Integer  |
### Usage Guidelines 
```
This command configures aging interval. If the Drop Monitor feature doesn't notice packet drops for this duration, it considers packet drops to have stopped.
```
### Examples 
#### aging-interval <value> 
```
sonic# configure terminal
sonic(config)# tam
sonic(config-tam)# drop-monitor
sonic(config-tam-dm)# aging-interval 10
sonic(config-tam-dm)# end
sonic# show tam drop-monitor
Status               : Inactive
Switch ID            : 9876
Number of sessions   : 3
Number of collectors : 2
Aging Interval       : 10
sonic#
```
## allowas-in 
### Description 
```
This command allows BGP neighbor to accept as-path with it's own AS number
present in it.
```
### Parent Commands (Modes) 
```
address-family l2vpn evpn
```
### Syntax 
```
allowas-in { [ <value> ] | [ origin ] } ]
no allowas-in { [ <value> ] | [ origin ] } ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| value |   | Integer  |
### Usage Guidelines 
```
Accepting own AS in an as-path usually results in AS loop. But
sometimes, users add AS number to influence the BGP route selection
process. This command enables user to control when a route with as-path
containing own AS number should be accepted or not. The command also
provides flexibility in terms of maximum number of occurrences of AS
number in as-apth.
```
### Examples 
#### Following command enable BGP neighbor to accept as-path          with it's owne AS number repeated 5 or less number of times 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 20.20.20.2
sonic(config-router-bgp-neighbor)# remote-as 300
sonic(config-router-bgp-neighbor)# address-family l2vpn evpn
sonic(config-router-bgp-neighbor-af)# allowas-in 5
```
## allowas-in 
### Description 
```
This command allows neighbors in a BGP peer-group to accept as-path
with it's own AS number present in it.
```
### Parent Commands (Modes) 
```
address-family l2vpn evpn
```
### Syntax 
```
allowas-in { [ <value> ] | [ origin ] } ]
no allowas-in { [ <value> ] | [ origin ] } ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| value |   | Integer  |
### Usage Guidelines 
```
Accepting own AS in an as-path usually results in AS loop. But
sometimes, users add AS number to influence the BGP route selection
process. This command enables user to control when a route with as-path
containing own AS number should be accepted or not. The command also
provides flexibility in terms of maximum number of occurrences of AS
number in as-apth.
```
### Examples 
#### Following command configured allowas-in for a BGP peer-group PG_External 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# remote-as 300
sonic(config-router-bgp-pg)# address-family l2vpn evpn
sonic(config-router-bgp-pg-af)# allowas-in
```
## allowas-in 
### Description 
```
Allow local AS number in as-path 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
allowas-in { [ <value> ] | [ origin ] } ]
no allowas-in { [ <value> ] | [ origin ] } ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| value |   | Integer  |
## allowas-in 
### Description 
```
Allow local AS number in as-path 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
allowas-in { [ <value> ] | [ origin ] } ]
no allowas-in { [ <value> ] | [ origin ] } ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| value |   | Integer  |
## allowas-in 
### Description 
```
This command allows BGP neighbor to accept as-path with it's own AS number
present in it.
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
allowas-in { [ <value> ] | [ origin ] } ]
no allowas-in { [ <value> ] | [ origin ] } ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| value |   | Integer  |
### Usage Guidelines 
```
Accepting own AS in an as-path usually results in AS loop. But
sometimes, users add AS number to influence the BGP route selection
process. This command enables user to control when a route with as-path
containing own AS number should be accepted or not. The command also
provides flexibility in terms of maximum number of occurrences of AS
number in as-apth.
```
### Examples 
#### Following command enable BGP neighbor to accept as-path          with it's owne AS number repeated 5 or less number of times 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 20.20.20.2
sonic(config-router-bgp-neighbor)# remote-as 300
sonic(config-router-bgp-neighbor)# address-family ipv4 unicast
sonic(config-router-bgp-neighbor-af)# allowas-in 5
```
## allowas-in 
### Description 
```
This command allows neighbors in a BGP peer-group to accept as-path
with it's own AS number present in it.
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
allowas-in { [ <value> ] | [ origin ] } ]
no allowas-in { [ <value> ] | [ origin ] } ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| value |   | Integer  |
### Usage Guidelines 
```
Accepting own AS in an as-path usually results in AS loop. But
sometimes, users add AS number to influence the BGP route selection
process. This command enables user to control when a route with as-path
containing own AS number should be accepted or not. The command also
provides flexibility in terms of maximum number of occurrences of AS
number in as-apth.
```
### Examples 
#### Following command configured allowas-in for a BGP peer-group PG_External 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# remote-as 300
sonic(config-router-bgp-pg)# address-family ipv4 unicast
sonic(config-router-bgp-pg-af)# allowas-in
```
## always-compare-med 
### Description 
```
Always compare the MED on routes, even when they were received from
different neighbouring ASes. Setting this option makes the order of
preference of routes more defined, and should eliminate MED induced
oscillations.
```
### Parent Commands (Modes) 
```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }
```
### Syntax 
```
always-compare-med
no always-compare-med
```
### Usage Guidelines 
```
Use this command to instruct BGP to always compare MED values for routes
even if they are from different ASes
```
### Examples 
#### Below command enables always-compare-med parameter 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# always-compare-med
```
## area 
### Description 
```
Configures area parameters within an OSPFv2 router
```
### Parent Commands (Modes) 
```
router ospf [ vrf <vrf-name> ]
```
### Syntax 
```
area <areaid> { { [ authentication [ message-digest ] ] } | { [ default-cost <defaultcost> ] } | { [ filter-list { prefix { <prefixlistname> { in | out } } } ] } | { [ range { <rangeprefix> { { [ advertise { [ cost <metriccost> ] } ] } | { [ cost <metriccost> ] } | [ not-advertise ] | { [ substitute <rangenetworkprefix> ] } ] } } ] } | { [ stub [ no-summary ] ] } | { [ virtual-link { <vlinkip> { { [ authentication { [ null ] | [ message-digest ] ] } ] } | { [ authentication-key { <auth-key> [ encrypted ] } ] } | { [ message-digest-key { <keyid> { md5 { <md5key> [ encrypted ] } } } ] } | { [ dead-interval <deadinterval> ] } | { [ hello-interval <hellointerval> ] } | { [ retransmit-interval <retransmitinterval> ] } | { [ transmit-delay <transmitinterval> ] } ] } } ] } | { [ shortcut { default | disable | enable } ] } } ]
no area <areaid> { { [ authentication [ message-digest ] ] } | [ default-cost ] | { [ filter-list { prefix { in | out } } ] } | { [ range { <rangeprefix> { { [ advertise [ cost ] ] } | [ cost ] | [ not-advertise ] | [ substitute ] ] } } ] } | { [ stub [ no-summary ] ] } | { [ virtual-link { <vlinkip> { { [ authentication { [ null ] | [ message-digest ] ] } ] } | { [ message-digest-key { <keyid> md5 } ] } | [ authentication-key ] | [ dead-interval ] | [ hello-interval ] | [ retransmit-interval ] | [ transmit-delay ] ] } } ] } | [ shortcut ] } ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| areaid | A.B.C.D or 0..4294967295  | String  |
| defaultcost |   | Integer  |
| prefixlistname | String  | String  |
| rangeprefix | A.B.C.D/mask  | String  |
| metriccost |   | Integer  |
| rangenetworkprefix | A.B.C.D/mask  | String  |
| vlinkip | A.B.C.D  | String  |
| auth-key | String  | String  |
| keyid |   | Integer  |
| md5key | String  | String  |
| deadinterval |   | Integer  |
| hellointerval |   | Integer  |
| retransmitinterval |   | Integer  |
| transmitinterval |   | Integer  |
### Usage Guidelines 
```
Use this command to configure area related parameters within an OSPFv2 router.
Below command options can be used
  area <area-id>
    Configures an area within a router by specifying an area-id in positive
    integer or dotted format.
  area <area-id> authentication [message-digest]
    Configures area level authentication mode. Authentocation mode can be clear
    text authentication or message-digest authentication.
  area <area-id> default-cost <cost-value>
    Configures NSSA or stub-area summary default cost.
  area <area-id>> filter-list prefix <prefix-list-name> in|out
    Configures prefix list for inter area prefix filtering. Inter area prefix
    propagation policies are configured using this command. Option 'in'
    is used for filetring incoming prefixes from the area and 'out' is used
    for filtering outgoing prefixes from the area.
  area <area-id> range <ip-prefix> [ cost <cost-value> | advertise cost <cost-value> | not-advertise | substitute <ip-prefix> ]
    Configures address ranges for inter area address propagation. Inter area
    prefix propagation policies can also be configured using this command.
    This command enables to configure prefix advertising rules directly without
    using andy prefix list. Cost of an advertized prefix can be modified using
    this command. Similarly a advertized prefix can be substituted by another prefix.
  area <area-id> stub [ no-summary ]
    Configures area to be stub area with stub options.
  area <area-id> shortcut enable|disable|default
    Configures area shortcut options.
  area <area-id> virtual-link <remote-router-id>
  area <area-id> virtual-link <remote-router-id> authentication [ null | message-digest ]
  area <area-id> virtual-link <remote-router-id> authentication-key <key> [ encrpted ]
  area <area-id> virtual-link <remote-router-id> message-digest-key <key-id> md5 <key> [ encrpted ]
  area <area-id> virtual-link <remote-router-id> dead-interval <time-value>
  area <area-id> virtual-link <remote-router-id> hello-interval <time-value>
  area <area-id> virtual-link <remote-router-id> retransmit-interval <time-value>
  area <area-id> virtual-link <remote-router-id> transmit-delay <time-value>
    Configures virtual link and its parameters in an area. Virtual link configurations
    are allowed on non-backbone area. Virtual links can have clear text password,
    message-digest based passwords or no password configured at all. When clear text
    and message digest passsword is configured, corresponding authentication-key or
    message-digest-key parameters must be configured. Timer parameters can also be
    configured for any Virtual links. Authentication key or password will be saved
    in encrypted form in configuration. User shall always provide actual password
    while configuring authentication keys. It is not recomended to use encrypted
    option of authentication key.
```
### Examples 
#### Area command examples 
```
sonic-cli(config-router-ospf)# area 19
sonic-cli(config-router-ospf)# area 19.1.1.19
sonic-cli(config-router-ospf)# area 19 authentication
sonic-cli(config-router-ospf)# area 19 authentication message-digest
sonic-cli(config-router-ospf)# area 19 filter-list prefix plist-area10_in  in
sonic-cli(config-router-ospf)# area 19 filter-list prefix plist-area10_out out
sonic-cli(config-router-ospf)# area 19 range 10.1.1.2/24
sonic-cli(config-router-ospf)# area 19 range 10.1.1.0/24 cost 48
sonic-cli(config-router-ospf)# area 19 range 10.2.2.0/24 not-advertise
sonic-cli(config-router-ospf)# area 19 range 10.3.3.0/24 substitute 192.3.3.0/24
sonic-cli(config-router-ospf)# area 19 stub
sonic-cli(config-router-ospf)# area 19 stub no summary
sonic-cli(config-router-ospf)# area 19 shortcut enable
sonic-cli(config-router-ospf)# area 19 virtual-link 1.1.1.9
sonic-cli(config-router-ospf)# area 19 virtual-link 1.1.1.9 authentication
sonic-cli(config-router-ospf)# area 19 virtual-link 1.1.1.9 authentication null
sonic-cli(config-router-ospf)# area 19 virtual-link 1.1.1.9 authentication message-digest
sonic-cli(config-router-ospf)# area 19 virtual-link 1.1.1.9 authentication-key password
sonic-cli(config-router-ospf)# area 19 virtual-link 1.1.1.9 message-digest-key 19 md5 md5password
sonic-cli(config-router-ospf)# area 19 virtual-link 1.1.1.9 dead-interval 60
sonic-cli(config-router-ospf)# area 19 virtual-link 1.1.1.9 hello-interval 20
sonic-cli(config-router-ospf)# area 19 virtual-link 1.1.1.9 retransmit-interval 15
sonic-cli(config-router-ospf)# area 19 virtual-link 1.1.1.9 transmit-delay 10
```
### Features this CLI belongs to 
*  OSPFv2
## as-override 
### Description 
```
Override ASNs in outbound updates if aspath equals remote-as 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
as-override
no as-override
```
## as-override 
### Description 
```
Override ASNs in outbound updates if aspath equals remote-as 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
as-override
no as-override
```
## as-override 
### Description 
```
This command instructs BGP to override AS Numbers in outbound updates if
aspath equals remote-as
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
as-override
no as-override
```
### Usage Guidelines 
```
Use this command to override the as number in outbound updates
```
### Examples 
#### Following command configures BGP to override AS number          in as-path 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 20.20.20.2
sonic(config-router-bgp-neighbor)# remote-as 300
sonic(config-router-bgp-neighbor)# address-family ipv4 unicast
sonic(config-router-bgp-neighbor-af)# as-override
```
## as-override 
### Description 
```
This command instructs BGP to override AS Numbers in outbound updates if
aspath equals remote-as
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
as-override
no as-override
```
### Usage Guidelines 
```
Use this command to override the as number in outbound updates
```
### Examples 
#### Following command configures BGP to override AS number          in as-path 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# address-family ipv4 unicast
sonic(config-router-bgp-pg-af)# as-override
```
## attribute-unchanged 
### Description 
```
This command instructs BGP to propagate route attributes (as-path,
next-hop, med) unchanged to this neighbor
```
### Parent Commands (Modes) 
```
address-family l2vpn evpn
```
### Syntax 
```
attribute-unchanged [ as-path ] [ med ] [ next-hop ]
no attribute-unchanged
```
### Usage Guidelines 
```
Use this command to propagate BGP route attributes unchanged to this
neighbor. User can control which attributes (as-path, next-hop, med) i
will be propagated unchanged.
```
### Examples 
#### Following command configures BGP to propagate next-hop          and as-path unchanged to neighbor 20.20.20.2 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 20.20.20.2
sonic(config-router-bgp-neighbor)# remote-as 300
sonic(config-router-bgp-neighbor)# address-family l2vpn evpn
sonic(config-router-bgp-neighbor-af)# attribute-unchanged as-path next-hop
```
## attribute-unchanged 
### Description 
```
This command instructs BGP to propagate route attributes (as-path,
next-hop, med) unchanged to neighbors in a peer-group
```
### Parent Commands (Modes) 
```
address-family l2vpn evpn
```
### Syntax 
```
attribute-unchanged [ as-path ] [ med ] [ next-hop ]
no attribute-unchanged
```
### Usage Guidelines 
```
Use this command to propagate BGP route attributes unchanged to
neighbors in a peer-group. User can control which attributes (as-path,
next-hop, med) will be propagated unchanged.
```
### Examples 
#### Following command configures BGP to propagate next-hop           and as-path unchanged to neighbors in a peer-group 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# address-family l2vpn evpn
sonic(config-router-bgp-pg-af)# attribute-unchanged as-path next-hop
```
## attribute-unchanged 
### Description 
```
BGP attribute is propagated unchanged to this neighbor 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
attribute-unchanged [ as-path ] [ med ] [ next-hop ]
no attribute-unchanged
```
## attribute-unchanged 
### Description 
```
BGP attribute is propagated unchanged to this neighbor 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
attribute-unchanged [ as-path ] [ med ] [ next-hop ]
no attribute-unchanged
```
## attribute-unchanged 
### Description 
```
This command instructs BGP to propagate route attributes (as-path,
next-hop, med) unchanged to this neighbor
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
attribute-unchanged [ as-path ] [ med ] [ next-hop ]
no attribute-unchanged
```
### Usage Guidelines 
```
Use this command to propagate BGP route attributes unchanged to this
neighbor. User can control which attributes (as-path, next-hop, med) i
will be propagated unchanged.
```
### Examples 
#### Following command configures BGP to propagate next-hop          and as-path unchanged to neighbor 20.20.20.2 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 20.20.20.2
sonic(config-router-bgp-neighbor)# remote-as 300
sonic(config-router-bgp-neighbor)# address-family ipv4 unicast
sonic(config-router-bgp-neighbor-af)# attribute-unchanged as-path next-hop
```
## attribute-unchanged 
### Description 
```
This command instructs BGP to propagate route attributes (as-path,
next-hop, med) unchanged to neighbors in a peer-group
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
attribute-unchanged [ as-path ] [ med ] [ next-hop ]
no attribute-unchanged
```
### Usage Guidelines 
```
Use this command to propagate BGP route attributes unchanged to
neighbors in a peer-group. User can control which attributes (as-path,
next-hop, med) will be propagated unchanged.
```
### Examples 
#### Following command configures BGP to propagate next-hop          and as-path unchanged to neighbors in a peer-group 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# address-family ipv4 unicast
sonic(config-router-bgp-pg-af)# attribute-unchanged as-path next-hop
```
## authentication 

### Description 

```
Configure authentication modes 


```
### Parent Commands (Modes) 

```
configure terminal

```
### Syntax 

```
authentication

```
## authentication rest 

### Description 

```
rest authentication modes 


```
### Parent Commands (Modes) 

```
configure terminal

```
### Syntax 

```
authentication rest <client-auth>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| client-auth | Authentication Types  | String  |


## authentication telemetry 

### Description 

```
telemetry authentication modes 


```
### Parent Commands (Modes) 

```
configure terminal

```
### Syntax 

```
authentication telemetry <client-auth>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| client-auth | Authentication Types  | String  |


## auto-cost 
### Description 
```
Configures interface auto cost reference bandwidth
```
### Parent Commands (Modes) 
```
router ospf [ vrf <vrf-name> ]
```
### Syntax 
```
auto-cost reference-bandwidth <ref-bandwidth>
no auto-cost reference-bandwidth
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| ref-bandwidth | 1-4294967  | Integer  |
### Usage Guidelines 
```
Use this command to configure interface auto cost reference bandwidth. Whenever
interface cost is not configured explicitely, refrence bandwidth value will be
used to calculate the interface cost.
```
### Examples 
#### configuring auto cost reference bandwidth 
```
sonic-cli(config-router-ospf)# auto-cost reference-bandwidth 10000
```
### Features this CLI belongs to 
*  OSPFv2
## autoneg 
### Description 
```
Configure autoneg 
```
### Parent Commands (Modes) 
```
interface Management <mgmt-if-id>
```
### Syntax 
```
autoneg <autoneg>
no autoneg
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| autoneg | on or off  | Select [on(true) off(false) ]  |
## autort 
### Description 
```
This command enables automatic derivation of route-distinguisher and route-targets
```
### Parent Commands (Modes) 
```
address-family l2vpn evpn
```
### Syntax 
```
autort rfc8365-compatible
no autort rfc8365-compatible
```
### Usage Guidelines 
```
[no] autort {autort-method}
```
### Examples 
#### Following command enables automatic derviation using rfc8365 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# address-family l2vpn evpn
sonic(config-router-bgp-af)# autort rfc8365-compatible
```
## bestpath as-path confed 
### Description 
```
This command specifies that the length of confederation path sets and
sequences should should be taken into account during the BGP best path
decision process.
```
### Parent Commands (Modes) 
```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }
```
### Syntax 
```
bestpath as-path confed
no bestpath as-path confed
```
### Usage Guidelines 
```
Use this command to consider confederation set and sequence path length
for best-path selection process
```
### Examples 
#### Below command instructs BGP to consider confederation          path length in as-path length comparison during best-path selection process 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# bestpath as-path confed
```
## bestpath as-path ignore 
### Description 
```
This command influences best-path selection algorithm by not comparing
as-path attribute
```
### Parent Commands (Modes) 
```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }
```
### Syntax 
```
bestpath as-path ignore
no bestpath as-path ignore
```
### Usage Guidelines 
```
Use this command to ignore as-path comparison during best-path selection
process.
```
### Examples 
#### Below command instructs BGP to ignore as-path comparison          during best-path selection process 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# bestpath as-path ignore
```
## bestpath as-path multipath-relax 
### Description 
```
This command specifies that BGP decision process should consider paths
of equal AS_PATH length candidates for multipath computation. Without
the knob, the entire AS_PATH must match for multipath computation.
```
### Parent Commands (Modes) 
```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }
```
### Syntax 
```
bestpath as-path multipath-relax [ as-set ]
no bestpath as-path multipath-relax [ as-set ]
```
### Usage Guidelines 
```
Use this command to ignore as-path check for paths for the same prefix
thereby making all the paths equal irrespctive of their as-path
```
### Examples 
#### Below command relaxes as-path comparison for multipath          case during best-path selection process 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# bestpath as-path multipath-relax
```
## bestpath compare-routerid 
### Description 
```
This command influences best-path selection algorithm by comparing
router-ids for identical eBGP routes
Ensure that when comparing routes where both are equal on most metrics,
including local-pref, AS_PATH length, IGP cost, MED, that the tie is
broken based on router-ID.
If this option is enabled, then the already-selected check, where
already selected eBGP routes are preferred, is skipped.
If a route has an ORIGINATOR_ID attribute because it has been reflected,
that ORIGINATOR_ID will be used. Otherwise, the router-ID of the peer
the route was received from will be used.
The advantage of this is that the route-selection (at this point) will
be more deterministic. The disadvantage is that a few or even one
lowest-ID router may attract all traffic to otherwise-equal paths
because of this check. It may increase the possibility of MED or IGP
oscillation, unless other measures were taken to avoid these. The exact
behaviour will be sensitive to the iBGP and reflection topology.
```
### Parent Commands (Modes) 
```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }
```
### Syntax 
```
bestpath compare-routerid
no bestpath compare-routerid
```
### Usage Guidelines 
```
Use this command to compare router-ids as tie-breaker for identical
eBGP paths
```
### Examples 
#### Below command instructs BGP to compare router-ids for          identical eBGP paths 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# bestpath compare-routerid
```
## bestpath med 
### Description 
```
This command influences best-path selection algorithm by how missing
MEDs are treated as well as whether MEDs should be compared for
confederation paths.
```
### Parent Commands (Modes) 
```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }
```
### Syntax 
```
bestpath med { { missing-as-worst [ confed ] } | { confed [ missing-as-worst ] } }
no bestpath med { { [ missing-as-worst [ confed ] ] } | { [ confed [ missing-as-worst ] ] } } ]
```
### Usage Guidelines 
```
Use this command to consider MED for confederation paths for best-path
selection process. Also, if MED is missing, should it be considered as
worst MED.
```
### Examples 
#### Below command instructs BGP to consider missing MED as          worst and compare MED value for confederation paths 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# bestpath med missing-as-worst confed
```
## bfd 
### Description 
```
This command enables BFD liveliness check for a BGP neighbor
```
### Parent Commands (Modes) 
```
neighbor { <ip> | { interface { Ethernet | PortChannel | Vlan } } }
```
### Syntax 
```
bfd [ check-control-plane-failure ]
no bfd [ check-control-plane-failure ]
```
### Usage Guidelines 
```
Use this command to enable BFD for a BGP neighbor.
check-control-plane-failure links Data Plane status to the BGP control
control plane
```
### Examples 
#### Following command enables BFD for BGP neighbor          30.30.30.3 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 30.30.30.3
sonic(config-router-bgp-neighbor)# bfd
```
## bfd 
### Description 
```
This command enables BFD liveliness check for BGP neighbors in a
peer-group
```
### Parent Commands (Modes) 
```
peer-group <template-str>
```
### Syntax 
```
bfd [ check-control-plane-failure ]
no bfd [ check-control-plane-failure ]
```
### Usage Guidelines 
```
Use this command to enable BFD for a BGP peer-group.
check-control-plane-failure links Data Plane status to the BGP control
control plane
```
### Examples 
#### Following command enables BFD for BGP peer-group          PG_Ext 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# bfd
```
## bfd 

### Description 

```
Configure BFD peers 


```
### Parent Commands (Modes) 

```
configure terminal

```
### Syntax 

```
bfd

```
## bgp as-path-list 
### Description 
```
This command creates BGP AS Path list
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
bgp as-path-list <as-path-list-name> { { deny <regx-id> } | { permit <regx-id> } }
no bgp as-path-list <as-path-list-name> { { [ deny <regx-id> ] } | { [ permit <regx-id> ] } ] }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| as-path-list-name | WORD  | String  |
| regx-id | String  | String  |
### Usage Guidelines 
```
Use this command to create BGP AS Path list. User can enter a regular
expression of AS Paths that provides flexible and powerful match
support. The command also provides any and all options to allow matching
all or any entry in as-path-list
```
### Examples 
#### The following command creates two entries for a as-path list named          asp_private 
```
sonic# configure terminal
sonic(config)# bgp as-path-list asp_private permit ^65000.*6510565109$
sonic(config)# bgp as-path-list asp_private deny 65107.*65200
```
## bgp community-list 
### Description 
```
This command creates BGP community list
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
bgp community-list { { standard { <community-list-name> { deny | permit } { { <aann> [ local-as ] [ no-advertise ] [ no-export ] [ no-peer ] { [ any ] | [ all ] ] } } | { local-as [ <aann> ] [ no-advertise ] [ no-export ] [ no-peer ] { [ any ] | [ all ] ] } } | { no-advertise [ <aann> ] [ local-as ] [ no-export ] [ no-peer ] { [ any ] | [ all ] ] } } | { no-export [ <aann> ] [ local-as ] [ no-advertise ] [ no-peer ] { [ any ] | [ all ] ] } } | { no-peer [ <aann> ] [ local-as ] [ no-advertise ] [ no-export ] { [ any ] | [ all ] ] } } } } } | { expanded { <community-list-name> { deny | permit } { <line> { [ any ] | [ all ] ] } } } } }
no bgp community-list { { standard { <community-list-name> { { [ deny { <aann> | local-as | no-advertise | no-export | no-peer } ] } | { [ permit { <aann> | local-as | no-advertise | no-export | no-peer } ] } ] } } } | { expanded { <community-list-name> { { [ deny <line> ] } | { [ permit <line> ] } ] } } } }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| community-list-name | WORD  | String  |
| aann | AA:NN  | String  |
| line | Line  | String  |
### Usage Guidelines 
```
Use this command to create BGP community list. The command provides
options to create expanded or standard community and accepts community
in AA:NN, IP:NN and well-known communities format. This command also
provides any and all constructs to enable user to design community
filters with clause match any or all. For expanded community, user can
specify a regular expression of communities.
```
### Examples 
#### The following command creates a community list named          CommList_RT 
```
sonic# configure terminal
sonic(config)# bgp community-list standard CommList_RT 100:200
sonic(config)# bgp community-list standard CommList_RT no-export
sonic(config)# bgp community-list standard CommList_RT no-peer
sonic(config)# bgp community-list standard CommList_RT 65100:3456
```
## bgp extcommunity-list 
### Description 
```
This command creates BGP extended community list entries
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
bgp extcommunity-list { { standard { <extcommunity-list-name> { deny | permit } { { rt { <aa> | <ipaddrnn> } { [ any ] | [ all ] ] } } | { soo { <aa> | <ipaddrnn> } { [ any ] | [ all ] ] } } } } } | { expanded { <extcommunity-list-name> { deny | permit } { <line> { [ any ] | [ all ] ] } } } } }
no bgp extcommunity-list { { standard { <extcommunity-list-name> { { [ deny { { rt { <aa> | <ipaddrnn> } } | { soo { <aa> | <ipaddrnn> } } } ] } | { [ permit { { rt { <aa> | <ipaddrnn> } } | { soo { <aa> | <ipaddrnn> } } } ] } ] } } } | { expanded { <extcommunity-list-name> { { [ deny <line> ] } | { [ permit <line> ] } ] } } } }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| extcommunity-list-name | WORD  | String  |
| aa | AA:NN  | String  |
| ipaddrnn | A.B.C.D:[1..65535]  | String  |
| line | Line  | String  |
### Usage Guidelines 
```
Use this command to create BGP extended community list entries. The command provides
options to create expanded or standard extended community list entries.
For standard extended community, user can create rt or soo type of
communities and command will accept communities in AA:NN or IP:NN
formats. For expanded extended community, the command will accept a
regular expression of communities, which is very flexible and powerful
for matching communities in routes. This command also provides option
for matching all or any extended communities.
```
### Examples 
#### The following command creates netries in a extended community list named          ExtComm_AllowInt 
```
sonic# configure terminal
sonic(config)# bgp extcommunity-list standard ExtComm_AllowInt rt 19.32.56.167:65011 all
sonic(config)# bgp extcommunity-list standard ExtComm_AllowInt rt 31.67.182.214:3001 all
sonic(config)# bgp extcommunity-list standard ExtComm_AllowInt soo 4001:65010 all
sonic(config)# bgp extcommunity-list standard ExtComm_AllowInt soo 98.13.175.21:65101 all
```
## binding 
### Description 
```
Create binding between an ACL and a NAT pool 
```
### Parent Commands (Modes) 
```
nat
```
### Syntax 
```
binding <binding-name> <pool-name> [ <acl-name> ] [ <natType> ] [ twice-nat-id <twice-nat-id-value> ]
no binding <bind-name>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| binding-name | String  | String  |
| pool-name | String  | String  |
| acl-name | String  | String  |
| natType | NAT type  | Select [snat dnat ]  |
| twice-nat-id-value |   | Integer  |
## call 
### Description 
```
Jump to another route-map after match_set 
```
### Parent Commands (Modes) 
```
route-map <route-map-name> { permit | deny } <seq-nu>
```
### Syntax 
```
call <match-call>
no call
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| match-call | WORD  | String  |
## capability dynamic 
### Description 
```
This command allows bgp to advertise dynamic capability to a neighbor
```
### Parent Commands (Modes) 
```
neighbor { <ip> | { interface { Ethernet | PortChannel | Vlan } } }
```
### Syntax 
```
capability dynamic
no capability dynamic
```
### Usage Guidelines 
```
Use this command to turn on dynamic capability for a BGP neighbor
```
### Examples 
#### Following command enables dynamic capability           for BGP neighbor 30.30.30.3 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 30.30.30.3
sonic(config-router-bgp-neighbor)# capability dynamic
```
## capability dynamic 
### Description 
```
This command allows bgp to advertise dynamic capability to neighbors in
a peer-group
```
### Parent Commands (Modes) 
```
peer-group <template-str>
```
### Syntax 
```
capability dynamic
no capability dynamic
```
### Usage Guidelines 
```
Use this command to turn on dynamic capability for a BGP peer-group
```
### Examples 
#### Following command enables dynamic capability           for BGP peer-group PG_Ext 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# capability dynamic
```
## capability extended-nexthop 
### Description 
```
This command allows bgp to negotiate the extended-nexthop capability with
it's peer. If you are peering over a v6 LL address then this capability
is turned on automatically. If you are peering over a v6 Global Address
then turning on this command will allow BGP to install v4 routes with v6
nexthops if you do not have v4 configured on interfaces.
```
### Parent Commands (Modes) 
```
neighbor { <ip> | { interface { Ethernet | PortChannel | Vlan } } }
```
### Syntax 
```
capability extended-nexthop
no capability extended-nexthop
```
### Usage Guidelines 
```
Use this command to turn on extended-nexthop capability for a BGP
neighbor
```
### Examples 
#### Following command enables extended-nexthop capability           for BGP neighbor 30.30.30.3 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 30.30.30.3
sonic(config-router-bgp-neighbor)# capability extended-nexthop
```
## capability extended-nexthop 
### Description 
```
This command allows bgp to negotiate the extended-nexthop capability with
it's peer in a peer-group. If you are peering over a v6 LL address then
this capability is turned on automatically. If you are peering over a v6
Global Address then turning on this command will allow BGP to install v4
routes with v6 nexthops if you do not have v4 configured on interfaces.
```
### Parent Commands (Modes) 
```
peer-group <template-str>
```
### Syntax 
```
capability extended-nexthop
no capability extended-nexthop
```
### Usage Guidelines 
```
Use this command to turn on extended-nexthop capability for a BGP
peer-group
```
### Examples 
#### Following command enables extended-nexthop capability           for BGP peer-group PG_Ext 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# capability extended-nexthop
```
## capability orf prefix-list 
### Description 
```
Advertise prefixlist ORF capability to this neighbor 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
capability orf prefix-list { send | receive | both }
no capability orf prefix-list { [ send ] | [ receive ] | [ both ] } ]
```
## capability orf prefix-list 
### Description 
```
Advertise capability to this neighbor 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
capability orf prefix-list { send | receive | both }
no capability orf prefix-list { [ send ] | [ receive ] | [ both ] } ]
```
## capability orf prefix-list 
### Description 
```
This command enables BGP to advertise Outbound Route Filtering (ORF) capability
to this neighbor
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
capability orf prefix-list { send | receive | both }
no capability orf prefix-list { [ send ] | [ receive ] | [ both ] } ]
```
### Usage Guidelines 
```
Use this command to advertise Outbound Route Filtering capability to
neighbor. This capability can be enabled in inbound and outbound
direction separately
```
### Examples 
#### Following command enables BGP to advertise ORF          capability to neighbor 20.20.20.2 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 20.20.20.2
sonic(config-router-bgp-neighbor)# remote-as 300
sonic(config-router-bgp-neighbor)# address-family ipv4 unicast
sonic(config-router-bgp-neighbor-af)# capability orf prefix-list send
```
## capability orf prefix-list 
### Description 
```
This command enables BGP to advertise Outbound Route Filtering (ORF) capability
to neighbors in a peer-group
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
capability orf prefix-list { send | receive | both }
no capability orf prefix-list { [ send ] | [ receive ] | [ both ] } ]
```
### Usage Guidelines 
```
Use this command to advertise Outbound Route Filtering capability to
neighbors in a peer-group. This capability can be enabled in inbound
and outbound direction separately
```
### Examples 
#### Following command enables BGP to advertise ORF          capability to neighbors in a peer-group 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# address-family ipv4 unicast
sonic(config-router-bgp-pg-af)# capability orf prefix-list send
```
## cbs 
### Description 
```
Committed Burst Size 
```
### Parent Commands (Modes) 
```
queue <sequence>
```
### Syntax 
```
cbs <bc>
no cbs
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| bc |   | Integer  |
## channel-group 
### Description 
```
Configure PortChannel parameters 
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
channel-group <lag-id>
no channel-group
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| lag-id |   | Integer  |
## channel-group 
### Description 
```
Configure PortChannel parameters 
```
### Parent Commands (Modes) 
```
interface range iface_range_num
```
### Syntax 
```
channel-group <lag-id>
no channel-group
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| lag-id |   | Integer  |
## cir 
### Description 
```
Committed Information Rate 
```
### Parent Commands (Modes) 
```
queue <sequence>
```
### Syntax 
```
cir <cir>
no cir
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| cir |   | Integer  |
## class 
### Description 
```
Configures flow match criteria and its actions 
```
### Parent Commands (Modes) 
```
policy-map <fbs-policy-name> type { qos | monitoring | forwarding | copp }
```
### Syntax 
```
class <fbs-class-name> [ priority <fbs-flow-priority> ]
no class <fbs-class-name>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| fbs-class-name | WORD  | String  |
| fbs-flow-priority |   | Integer  |
## class-map 
### Description 
```
Configures Class-map
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
class-map <fbs-class-name> match-type { acl | { fields match-all } | copp }
no class-map <fbs-class-name>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| fbs-class-name | WORD  | String  |
### Usage Guidelines 
```
Class-map name can be of maximum 63 characters. The name must begin with A-Z, a-z or 0-9. Underscore and hypens can be used except as the first character.
```
### Examples 
#### Creates class-map with match type fields 
```
sonic(config)# class-map class_permit_ip match-type fields
```
## clear audit-log 

### Description 

```
Clear audit log 


```
### Syntax 

```
clear audit-log

```
## clear bfd peer 

### Description 

```
Clears counters of the specific Bidirectional Forwarding detection(BFD) peer with the filters. BFD packet counters will be reset to 0.


```
### Syntax 

```
clear bfd peer { <peer_ipv4> | <peer_ipv6> } [ vrf <vrfname> ] [ multihop ] [ local-address { <local_ipv4> | <local_ipv4> } ] [ interface <interfacename> ] counters

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| peer_ipv4 | A.B.C.D  | String  |
| peer_ipv6 | A::B  | String  |
| vrfname | String  | String  |
| local_ipv4 | A.B.C.D  | String  |
| interfacename | String  | String  |


### Examples 
#### Clear BFD peer counter 

```
device# clear bfd peer 192.168.2.1 interface Ethernet0 counters


```
## clear bgp all 

### Description 

```
This command clears/resets all BGP information including neighbors,
peer-group etc.


```
### Syntax 

```
clear bgp all [ vrf <vrf-name> ] { { [ * { [ in ] | [ out ] | { [ soft { [ in ] | [ out ] ] } ] } ] } ] } | { [ <as-num-dot> { [ in ] | [ out ] | { [ soft { [ in ] | [ out ] ] } ] } ] } ] } | { [ <ipv4> { [ in ] | [ out ] | { [ soft { [ in ] | [ out ] ] } ] } ] } ] } | { [ <ipv6> { [ in ] | [ out ] | { [ soft { [ in ] | [ out ] ] } ] } ] } ] } | { dampening { [ <ip-addr> ] | [ <ip-prefix> ] ] } } | { [ external { [ in ] | [ out ] | { [ soft { [ in ] | [ out ] ] } ] } ] } ] } | { interface { Ethernet | PortChannel | Vlan } } | { peer-group <peer-group-name> { [ in ] | [ out ] | { [ soft { [ in ] | [ out ] ] } ] } ] } } }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrf-name | WORD  | String  |
| as-num-dot | 1-4294967295  | Integer  |
| ipv4 | A.B.C.D  | String  |
| ipv6 | A::B  | String  |
| ip-addr | A.B.C.D  | String  |
| ip-prefix | A.B.C.D/mask  | String  |
| peer-group-name | WORD  | String  |


### Usage Guidelines 

```
Use this command to clear BGP information. Following is a partial list of
information with command syntax that can be cleared.
- clear bgp all *
  This command clears all BGP neighbors in the all address-families activated
- clear bgp all A.B.C.D/A::B
  Clear peers with address of peer_ip and this address-family activated.
- clear bgp all A.B.C.D/A::B soft {in | out}
  'in" option will send route-refresh request unless using 'soft-reconfiguration inbound.
  'out' option will resend all outbound updates


```
### Examples 
#### Below command resets a BGP neighbor 14.14.14.1 

```
leaf4# clear bgp all 14.14.14.1


```
## clear bgp ipv4 

### Description 

```
This command clears/resets BGP information including neighbors,
peer-group,dampening etc.


```
### Syntax 

```
clear bgp ipv4 unicast [ vrf <vrf-name> ] { * | <as-num-dot> | external | <ipv4-addr> | <ipv6-addr> | <ip-prefix> | { interface { Ethernet | PortChannel | Vlan } } | { peer-group [ <peer-group-name> ] } | { dampening { [ <ip-addr> ] | [ <ip-prefix> ] ] } } } { [ in ] | [ out ] | { [ soft { [ in ] | [ out ] ] } ] } } ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrf-name | WORD  | String  |
| as-num-dot | 1-4294967295  | Integer  |
| ipv4-addr | A.B.C.D  | String  |
| ipv6-addr | A::B  | String  |
| ip-prefix | A.B.C.D/mask  | String  |
| peer-group-name | WORD  | String  |
| ip-addr | A.B.C.D  | String  |


### Usage Guidelines 

```
Use this command to clear BGP information. Following is a partial list of
information with command syntax that can be cleared.
- clear bgp {ipv4 | ipv6} unicast *
  This command clears all BGP neighbors with this address-family and
  sub-address-family activated
- clear bgp {ipv4 | ipv6} unicast peer_ip
  Clear peers with address of peer_ip and this address-family activated.
- clear bgp ipv4 unicast dampening { address | prefix | in | out | soft }
  Clear dampened routes.
- clear bgp {ipv6 | ipv4} unicast soft {in | out}
  'in" option will send route-refresh request unless using 'soft-reconfiguration inbound.
  'out' option will resend all outbound updates


```
### Examples 
#### Below command resets a BGP neighbor 14.14.14.1 

```
leaf4# clear bgp ipv4 unicast 14.14.14.1


```
## clear bgp ipv6 

### Description 

```
Clear BGP IPv6 


```
### Syntax 

```
clear bgp ipv6 unicast [ vrf <vrf-name> ] { * | <as-num-dot> | external | <ipv4-addr> | <ipv6-addr> | <ip-prefix> | { interface { Ethernet | PortChannel | Vlan } } | { peer-group [ <peer-group-name> ] } | { dampening { [ <ip-addr> ] | [ <ip-prefix> ] ] } } } { [ in ] | [ out ] | { [ soft { [ in ] | [ out ] ] } ] } } ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrf-name | WORD  | String  |
| as-num-dot | 1-4294967295  | Integer  |
| ipv4-addr | A.B.C.D  | String  |
| ipv6-addr | A::B  | String  |
| ip-prefix | A.B.C.D/mask  | String  |
| peer-group-name | WORD  | String  |
| ip-addr | A.B.C.D  | String  |


## clear bgp l2vpn evpn 

### Description 

```
This command clears/resets BGP information for EVPN address-family on neighbors


```
### Syntax 

```
clear bgp l2vpn evpn { { [ <as-num-dot> { [ in ] | [ out ] | { [ soft { [ in ] | [ out ] ] } ] } ] } ] } | { [ * { [ in ] | [ out ] | { [ soft { [ in ] | [ out ] ] } ] } ] } ] } | { [ external { [ in ] | [ out ] | { [ soft { [ in ] | [ out ] ] } ] } ] } ] } | { [ interface { <ifname> { [ in ] | [ out ] | { [ soft { [ in ] | [ out ] ] } ] } ] } } ] } | { [ peer-group { <peer-group name> { [ in ] | [ out ] | { [ soft { [ in ] | [ out ] ] } ] } ] } } ] } | { [ <neighbor-ipv6> { [ in ] | [ out ] | { [ soft { [ in ] | [ out ] ] } ] } ] } ] } | { [ <neighbor-ipv4> { [ in ] | [ out ] | { [ soft { [ in ] | [ out ] ] } ] } ] } ] } | [ in ] | [ out ] | { [ soft [ in ] [ out ] ] } }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| as-num-dot | 1-4294967295  | Integer  |
| ifname | String  | String  |
| peer-group name | String  | String  |
| neighbor-ipv6 | A::B  | String  |
| neighbor-ipv4 | A.B.C.D  | String  |


### Usage Guidelines 

```
Use this command to clear BGP information. Following is a partial list of
information with command syntax that can be cleared.
- clear bgp l2vpn evpn *
  This command clears all BGP neighbors with address-family l2vpn evpn activated
- clear bgp l2vpn evpn {peer_ip} *
  Clear peers with address of peer_ip and address-family l2vpn evpn activated.
- clear bgp l2vpn evpn soft {in | out}
  'in" option will send route-refresh request unless using 'soft-reconfiguration inbound.
  'out' option will resend all outbound updates


```
### Examples 
#### Below command resets all BGP neighbors with EVPN activated 

```
sonic# clear bgp l2vpn evpn *


```
## clear buffer_pool 

### Description 

```
This command is used to clear user/persistant watermark counters recorded by the system.


```
### Syntax 

```
clear buffer_pool { watermark | persistent-watermark }

```
### Usage Guidelines 

```
Use this command to clear user/persistant watermark counters recorded by the system.


```
### Examples 
#### Clear buffer_pool user/persistant watermark counters 

```
sonic-cli# clear buffer_pool watermark
sonic-cli# clear buffer_pool persistent-watermark


```
## clear counters 

### Description 

```
Clear counters 


```
### Syntax 

```
clear counters interface { all | { Eth <ifId> } | { Ethernet <ifId> } | { PortChannel <ifId> } }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| ifId | EthernetSLOTPORT  |   |


## clear counters service-policy 

### Description 

```
Clears flow based services applied policy-map applied at Switch/Global level


```
### Syntax 

```
clear counters service-policy { Switch | CtrlPlane } [ type { qos | monitoring | forwarding | copp } ]

```
### Usage Guidelines 

```
Policy-map type argument is optional. If policy-map type not specified it clear fbs policies counters for given interfaces for all policies matching that interface.


```
### Examples 
#### clear counters service-policy Switch 

```
sonic# clear counters service-policy Switch type qos


```
## clear counters service-policy interface 

### Description 

```
Clears flow based services applied policies counters by interface


```
### Syntax 

```
clear counters service-policy interface { <eth-if-id> | <po-if-id> | <vlan-if-id> } [ type { qos | monitoring | forwarding | copp } ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| eth-if-id | EthernetNUM  |   |
| po-if-id | PortChannelNUM  |   |
| vlan-if-id | VlanNUM  |   |


### Usage Guidelines 

```
Policy-map type argument is optional. If policy-map type not specified it clear fbs policies counters for given interfaces for all policies matching that interface.


```
### Examples 
#### clear counters service-policy interface 

```
clear counters service-policy interface Vlan 100 type qos


```
## clear counters service-policy policy-map 

### Description 

```
Clears flow based services applied policies counters by policy name


```
### Syntax 

```
clear counters service-policy policy-map <fbs-policy-name> { { [ interface { <eth-if-id> | <po-if-id> | <vlan-if-id> } ] } | [ Switch ] | [ CtrlPlane ] } ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| fbs-policy-name | WORD  | String  |
| eth-if-id | EthernetNUM  |   |
| po-if-id | PortChannelNUM  |   |
| vlan-if-id | VlanNUM  |   |


### Usage Guidelines 

```
Interface argument is optional. If not specified it clear fbs policies counters for given policy-map for all interfaces


```
### Examples 
#### clear counters service-policy policy_name 

```
clear counters service-policy policy-map policy_vrf interface Vlan 100


```
## clear counters tam 

### Description 

```
This command is used to clear flowgroup counters.


```
### Syntax 

```
clear counters tam { flow-groups { all | <name> } }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| name | WORD  | String  |


### Usage Guidelines 

```
Use this command to clear flowgroup counters.


```
### Examples 
#### clear counters tam flow-groups 

```
sonic-cli# clear counters tam flow-groups all
sonic-cli# clear counters tam flow-groups flowgroup_name


```
## clear ip access-list counters 

### Description 

```
Clear IPv4 ACL rules statistics


```
### Syntax 

```
clear ip access-list counters [ <access-list-name> { { [ interface { Ethernet | <PortChannel> | <Vlan> } ] } | [ Switch ] ] } ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| access-list-name | WORD  | String  |
| PortChannel | PortChannelNUM  |   |
| Vlan | VlanNUM  |   |


### Usage Guidelines 

```
ACL name and interface names are optional. If ACL name is not specified then all IPv4 ACLs statistics will be cleared.


```
### Examples 
#### clear ip access-list counters 

```
sonic# clear ip access-list counters ipacl-example


```
### Alternate command 
#### click 

```
admin@sonic:~$ show acl rule [name] [rule_name] -c


```
## clear ip arp 

### Description 

```
To delete dynamically learned IPv4 entries from the ARP table, use the clear ip
arp command. To specify the entries to be deleted, enter an interface, port
channel, or VLAN, an IPv4 address, or a combination to match.
Use the show ip arp command to verify that the ARP entries have been deleted.


```
### Syntax 

```
clear ip arp [ <ip-addr> ] ] [ vrf { <vrfname> | mgmt | all } ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| ip-addr | A.B.C.D  | String  |
| vrfname | VRF name (prefixed by Vrf, Max: 15 characters)  | String  |


### Usage Guidelines 

```
sonic# clear ip arp [interface {Ethernet < port-number >| PortChannel < number >| Vlan < vlan-id >| Management < port-number >}] [< ipv4-address >]


```
### Examples 
#### clear ip arp information 

```
sonic# show ip arp
---------------------------------------------------------------------
Address         Hardware address    Interface        Egress Interface
---------------------------------------------------------------------
192.168.1.4     00:01:02:03:44:55   Ethernet8         -
192.168.2.4     00:01:02:03:ab:cd   PortChannel200    -
192.168.3.6     00:01:02:03:04:05   Vlan100           Ethernet4
10.11.48.254    00:01:e8:8b:44:71   eth0              -
10.14.8.102     00:01:e8:8b:44:71   eth0              -
sonic# clear ip arp
sonic# show ip arp
sonic#


```
#### clear ip arp interface information 

```
sonic# show ip arp
---------------------------------------------------------------------
Address         Hardware address    Interface        Egress Interface
---------------------------------------------------------------------
192.168.1.4     00:01:02:03:44:55   Ethernet8         -
192.168.2.4     00:01:02:03:ab:cd   PortChannel200    -
192.168.3.6     00:01:02:03:04:05   Vlan100           Ethernet4
10.11.48.254    00:01:e8:8b:44:71   eth0              -
10.14.8.102     00:01:e8:8b:44:71   eth0              -
sonic# clear ip arp interface Vlan 100
sonic# show ip arp
---------------------------------------------------------------------
Address         Hardware address    Interface        Egress Interface
---------------------------------------------------------------------
192.168.1.4     00:01:02:03:44:55   Ethernet8         -
192.168.2.4     00:01:02:03:ab:cd   PortChannel200    -
10.11.48.254    00:01:e8:8b:44:71   eth0              -
10.14.8.102     00:01:e8:8b:44:71   eth0              -


```
#### clear ip arp ip information 

```
sonic# show ip arp
---------------------------------------------------------------------
Address         Hardware address    Interface        Egress Interface
---------------------------------------------------------------------
192.168.1.4     00:01:02:03:44:55   Ethernet8         -
192.168.2.4     00:01:02:03:ab:cd   PortChannel200    -
192.168.3.6     00:01:02:03:04:05   Vlan100           Ethernet4
10.11.48.254    00:01:e8:8b:44:71   eth0              -
10.14.8.102     00:01:e8:8b:44:71   eth0              -
sonic# clear ip arp 192.168.1.4
sonic# show ip arp
---------------------------------------------------------------------
Address         Hardware address    Interface        Egress Interface
---------------------------------------------------------------------
192.168.2.4     00:01:02:03:ab:cd   PortChannel200    -
192.168.3.6     00:01:02:03:04:05   Vlan100           Ethernet4
10.11.48.254    00:01:e8:8b:44:71   eth0              -
10.14.8.102     00:01:e8:8b:44:71   eth0              -


```
## clear ip arp interface 

### Description 

```
clear ARP entries for this interface 


```
### Syntax 

```
clear ip arp interface { <phy-if-name> | <mgmt-if-name> | <po-if-name> | <vlan-if-name> }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| phy-if-name | EthernetNUM  |   |
| mgmt-if-name |   |   |
| po-if-name | PortChannelNUM  |   |
| vlan-if-name | VlanNUM  |   |


## clear ip dhcp-relay 

### Description 

```
Clear ipv4 dhcp-relay statistics 


```
### Syntax 

```
clear ip dhcp-relay { statistics <ifName> }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| ifName | String  | String  |


## clear ip helper-address statistics 

### Description 

```
Clears IP helper statistics on interface.


```
### Syntax 

```
clear ip helper-address statistics [ <iface> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| iface | Interface Type - Ranges  |   |


### Usage Guidelines 

```
clear ip helper-address statistics [ <interface-name> ]


```
### Examples 
#### Example for clearing statistics on all interfaces 

```
clear ip helper-address statistics


```
#### Example for clearing statistics on specific interface 

```
clear ip helper-address statistics Ethernet0


```
### Features this CLI belongs to 
*  IP Helper


### Alternate command 
#### click 

```
clear ip helper_address statistics


```
## clear ip mroute 

### Description 

```
This command is useful for debugging and resets multicast route information.


```
### Syntax 

```
clear ip mroute [ vrf <vrf-name> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrf-name | WORD  | String  |


### Usage Guidelines 

```
* clear ip mroute [vrf {<vrf-name> | all}] ==> To reset all IP Multicast routes for the particular VRF


```
### Examples 
#### clear ip mroute  [vrf {<vrf-name> | all}] 

```
sonic# clear ip mroute
sonic#


```
## clear ip ospf 

### Description 

```
This command clears/resets OSPFv2 sessions on an OSPFv2 interface.


```
### Syntax 

```
clear ip ospf { { interface [ <interface-name> ] } | { vrf { <vrf-name> { interface [ <interface-name> ] } } } }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| interface-name | Interface Type - Ranges  |   |
| vrf-name | String  | String  |


### Usage Guidelines 

```
Use this command to clear OSPFv2 sessions. Following is a partial list of
information with command syntax that can be cleared.
- clear ip ospf interface IF-NAME
  This command clears all OSPFv2 sessions on an OSPFv2 interface
- clear ip ospf vrf VRF-NAME interface IF-NAME
  This command clears all OSPFv2 sessions on interface in VRF VRF-NAME


```
### Examples 
#### Below commands clear sessions on Ethernt64 

```
sonic# clear ip ospf interface  Ethernet64
sonic# clear ip ospf vrf default interface Ethernet64


```
## clear ip pim 

### Description 

```
These commands are useful for debugging. One resets the PIM interfaces. The other rescans the Outgoing Interface Lists (OILs).


```
### Syntax 

```
clear ip pim [ vrf <vrf-name> ] { [ interfaces ] | [ oil ] }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrf-name | WORD  | String  |


### Usage Guidelines 

```
* clear ip pim [vrf <vrf-name>] interfaces ==> To reset all PIM interfaces of a particular VRF
* clear ip pim [vrf <vrf-name>] oil ==> To rescan PIM OIL (Outgoing Interfaces List) of all multicast entries of a particular VRF


```
### Examples 
#### clear ip pim [vrf <vrf-name> ] interfaces 

```
sonic# clear ip pim interfaces
sonic#
---------------------------------------
sonic# clear ip pim vrf Vrf2 interfaces
sonic#


```
#### clear ip pim [vrf <vrf-name> ] oil 

```
sonic# clear ip pim oil
sonic#
---------------------------------------
sonic# clear ip pim vrf Vrf2 oil
sonic#


```
## clear ip sla 

### Description 

```
Clears counters for an IP SLA instances


```
### Syntax 

```
clear ip sla <id>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| id |   | Integer  |


### Examples 
#### Clears counters for an IP SLA instances 

```
sonic(config)# no clear ip sla 10


```
## clear ip sla all 

### Description 

```
Clears counters for all IP SLA instances


```
### Syntax 

```
clear ip sla all

```
### Examples 
#### Clears counters for all IP SLA instances 

```
sonic(config)# no clear ip sla all


```
## clear ipv6 access-list counters 

### Description 

```
Clear IPv6 ACL rules statistics


```
### Syntax 

```
clear ipv6 access-list counters [ <access-list-name> { { [ interface { Ethernet | <PortChannel> | <Vlan> } ] } | [ Switch ] ] } ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| access-list-name | WORD  | String  |
| PortChannel | PortChannelNUM  |   |
| Vlan | VlanNUM  |   |


### Usage Guidelines 

```
ACL name and interface names are optional. If ACL name is not specified then all IPv6 ACLs statistics will be cleared.


```
### Examples 
#### clear ipv6 access-list counters 

```
sonic# clear ipv6 access-list counters ipv6acl-example


```
### Alternate command 
#### click 

```
admin@sonic:~$ show acl rule [name] [rule_name] -c


```
## clear ipv6 dhcp-relay 

### Description 

```
Clear ipv6 dhcp-relay statistics 


```
### Syntax 

```
clear ipv6 dhcp-relay { statistics <ifName> }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| ifName | String  | String  |


## clear ipv6 neighbors 

### Description 

```
To delete dynamically learned IPv6 entries from the NDP table, use the 'clear ipv6 neighbor' command.
To specify the entries to be deleted, enter an interface, port channel, or VLAN, an IPv6 address, or a combination to match.
Use the 'show ipv6 neighbors' to verify that the IPv6 entries have been deleted.


```
### Syntax 

```
clear ipv6 neighbors [ <ip-addr> ] ] [ vrf { <vrfname> | mgmt | all } ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| ip-addr | A::B  | String  |
| vrfname | VRF name (prefixed by Vrf, Max: 15 characters)  | String  |


### Usage Guidelines 

```
sonic# clear ipv6 neighbors [interface {Ethernet < port-number >| PortChannel < number >| Vlan < vlan-id >| Management < port-number >}] [< ipv6-address >]


```
### Examples 
#### clear ipv6 neighbors information 

```
sonic# show ipv6 neighbors
---------------------------------------------------------------------------------
Address                     Hardware address    Interface        Egress Interface
---------------------------------------------------------------------------------
20::1                       00:01:02:03:44:55   Ethernet8         -
20::2                       00:01:02:03:ab:cd   PortChannel200    -
20::3                       00:01:02:03:04:05   Vlan100           Ethernet4
fe80::e6f0:4ff:fe79:34c7    00:01:e8:8b:44:71   eth0              -
sonic# clear ipv6 neighbors
sonic# show ipv6 neighbors


```
#### clear ipv6 neighbors interface information 

```
sonic# show ipv6 neighbors
---------------------------------------------------------------------------------
Address                     Hardware address    Interface        Egress Interface
---------------------------------------------------------------------------------
20::1                       00:01:02:03:44:55   Ethernet8         -
20::2                       00:01:02:03:ab:cd   PortChannel200    -
20::3                       00:01:02:03:04:05   Vlan100           Ethernet4
fe80::e6f0:4ff:fe79:34c7    00:01:e8:8b:44:71   eth0              -
sonic# clear ipv6 neighbors interface Vlan 100
sonic# show ipv6 neighbors
---------------------------------------------------------------------------------
Address                     Hardware address    Interface        Egress Interface
---------------------------------------------------------------------------------
20::1                       00:01:02:03:44:55   Ethernet8         -
20::2                       00:01:02:03:ab:cd   PortChannel200    -
fe80::e6f0:4ff:fe79:34c7    00:01:e8:8b:44:71   eth0              -


```
#### clear ipv6 neighbors ip information 

```
sonic# show ipv6 neighbors
---------------------------------------------------------------------------------
Address                     Hardware address    Interface        Egress Interface
---------------------------------------------------------------------------------
20::1                       00:01:02:03:44:55   Ethernet8         -
20::2                       00:01:02:03:ab:cd   PortChannel200    -
20::3                       00:01:02:03:04:05   Vlan100           Ethernet4
fe80::e6f0:4ff:fe79:34c7    00:01:e8:8b:44:71   eth0              -
sonic# clear ipv6 neighbors fe80::e6f0:4ff:fe79:34c7
sonic# show ipv6 neighbors
---------------------------------------------------------------------------------
Address                     Hardware address    Interface        Egress Interface
---------------------------------------------------------------------------------
20::1                       00:01:02:03:44:55   Ethernet8         -
20::2                       00:01:02:03:ab:cd   PortChannel200    -
20::3                       00:01:02:03:04:05   Vlan100           Ethernet4


```
## clear ipv6 neighbors interface 

### Description 

```
clear NDP entries for this interface 


```
### Syntax 

```
clear ipv6 neighbors interface { <phy-if-name> | <mgmt-if-name> | <po-if-name> | <vlan-if-name> }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| phy-if-name | EthernetNUM  |   |
| mgmt-if-name |   |   |
| po-if-name | PortChannelNUM  |   |
| vlan-if-name | VlanNUM  |   |


## clear logging 

### Description 

```
Clear logging 


```
### Syntax 

```
clear logging

```
## clear mac access-list counters 

### Description 

```
Clear MAC ACL rules statistics


```
### Syntax 

```
clear mac access-list counters [ <access-list-name> { { [ interface { Ethernet | <PortChannel> | <Vlan> } ] } | [ Switch ] ] } ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| access-list-name | WORD  | String  |
| PortChannel | PortChannelNUM  |   |
| Vlan | VlanNUM  |   |


### Usage Guidelines 

```
ACL name and interface names are optional. If ACL name is not specified then all MAC ACLs statistics will be cleared.


```
### Examples 
#### clear mac access-list counters 

```
sonic# clear mac access-list counters


```
### Alternate command 
#### click 

```
admin@sonic:~$ show acl rule [name] [rule_name] -c


```
## clear mac address-table dynamic 

### Description 

```
Clear MAC address-table for dynamic commands 


```
### Syntax 

```
clear mac address-table dynamic { all | { address <mac-addr> } | { Vlan <vlan-id> } | { interface { Ethernet | PortChannel } } }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| mac-addr | nn:nn:nn:nn:nn:nn  | String  |
| vlan-id |   | Integer  |


## clear mac dampening-disabled-ports 

### Description 

```
Clear MAC dampening disabled ports 


```
### Syntax 

```
clear mac dampening-disabled-ports { all | PORT_ID | PO_ID }

```
## clear nat 

### Description 

```
Clear NAT 


```
### Syntax 

```
clear nat { translations | statistics }

```
## clear priority-group 

### Description 

```
This command clears priority-group watermarks, persistent-watermarks and breaches etc.


```
### Syntax 

```
clear priority-group { { watermark { { headroom { [ interface { <phy-intf-name> } ] } } | { shared { [ interface { <phy-intf-name> } ] } } } } | { persistent-watermark { { headroom { [ interface { <phy-intf-name> } ] } } | { shared { [ interface { <phy-intf-name> } ] } } } } }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| phy-intf-name | EthernetNUM  |   |


### Usage Guidelines 

```
Use this command to clear priority-group watermarks, persistent-watermarks and breches etc.
There are various CLI options available to clear infomration for unicast/multicast on priority-group, interface.
- clear priority-group (watermark|persitent-watermark) (headoom|shared) (interfcae Ethernet [ifname]))
  This command will display all queue watermarks


```
### Examples 
#### Below are some of the sample clear commands 

```
sonic# clear priority-group watermark shared interface Ethernet 0
sonic# clear priority-group persistent-watermark headroom


```
## clear queue 

### Description 

```
This command clears queue counters, watermarks, persistent-watermarks and breaches etc.


```
### Syntax 

```
clear queue { { counters { [ interface { { <phy-intf-name> { [ queue <queue-id> ] } } | { CPU { [ queue <queue-id> ] } } } ] } } | { watermark { { unicast { [ interface { <phy-intf-name> } ] } } | { multicast { [ interface { <phy-intf-name> } ] } } | CPU } } | { persistent-watermark { { unicast { [ interface { <phy-intf-name> } ] } } | { multicast { [ interface { <phy-intf-name> } ] } } | CPU } } }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| phy-intf-name | EthernetNUM  |   |
| queue-id |   | Integer  |


### Usage Guidelines 

```
Use this command to clear queue counters, watermarks, persistent-watermarks and breches etc.
There are various CLI options available toclear infomration for queue, interface and all interfaces.
- clear queue counters (interfcae Ethernet|CPU [ifname] (queue [id]))
  This command will display all queue counters
- clear queue (watermark|persitent-watermark) (unicast| multicast) (interfcae Ethernet|CPU [ifname]))
  This command will display all queue watermarks


```
### Examples 
#### Below are some of the sample clear commands 

```
sonic# clear queue counters interface Ethernet 0 queue 0
sonic# clear queue watermark unicast
sonic# clear queue persistent-watermark multicast interface Ethernet 0


```
## clear radius-server statistics 

### Description 

```
Clear RADIUS statistics 


```
### Syntax 

```
clear radius-server statistics

```
## clear snmp counters 

### Description 

```
Clears the Simple Network Management Protocol (SNMP) counters statistics


```
### Syntax 

```
clear snmp counters

```
### Usage Guidelines 

```
Use this command to clear the global SNMP counters statistics.


```
### Examples 

```

         sonic# clear snmp counters


```
## clear threshold 

### Description 

```
This command is used to clear all or a given threshold breach event(s) recorded by the system.


```
### Syntax 

```
clear threshold breach { all | <eventId> }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| eventId |   | Integer  |


### Usage Guidelines 

```
Use this command to clear all or a given threshold breach event(s) recorded by the system.


```
### Examples 
#### Clear threshold breach 

```
sonic-cli# clear threshold breach all
sonic-cli# clear threshold breach eventId


```
## client-to-client reflection 
### Description 
```
This command configures client to client route reflection
```
### Parent Commands (Modes) 
```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }
```
### Syntax 
```
client-to-client reflection
no client-to-client reflection
```
### Usage Guidelines 
```
Use this comand to configure client to client route reflection
```
### Examples 
#### Below command configures client to client reflection 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# client-to-client reflection
```
## cluster-id 
### Description 
```
This command configures cluster ID for BGP router
```
### Parent Commands (Modes) 
```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }
```
### Syntax 
```
cluster-id <intval-ip>
no cluster-id
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| intval-ip | A.B.C.D or [1..4294967295]  | String  |
### Usage Guidelines 
```
A cluster is a collection of route reflectors and their clients, and is
used by route reflectors to avoid looping. Use this command to configure
cluster-ID (an IP address or a number) on a BGP router
```
### Examples 
#### Below command configures cluster ID 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# cluster-id 23.79.154.17
```
## coalesce-time 
### Description 
```
This command configures subgroup coalesce timer interval in millseconds
```
### Parent Commands (Modes) 
```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }
```
### Syntax 
```
coalesce-time <coaltime>
no coalesce-time
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| coaltime | 1-4294967295  | Integer  |
### Usage Guidelines 
```
Use this command to configure subgroup coalesce timer interval
```
### Examples 
#### Below command configures the coalesce timer interval to          2000 msec 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# coalesce-time 2000
```
## collector 
### Description 
```
This command configures collector information.
```
### Parent Commands (Modes) 
```
tam
```
### Syntax 
```
collector <name> ip <ip_address> port { <port_number> { [ protocol <protocol_type> ] } }
no collector <name>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| name | WORD  | String  |
| ip_address | A.B.C.D/A::B  | String  |
| port_number |   | Integer  |
| protocol_type | Transport Protocol Method  | Select [UDP(UDP) TCP(TCP) ]  |
### Usage Guidelines 
```
This command configures collector information. A collector is typically a machine reachable from the switch, where the telemetry reports are sent.
```
### Examples 
#### collector <name> type {ipv4 | ipv6} ip <ip-address> port <port-number> [protocol { UDP | TCP }]  
```
sonic(config-tam)# collector c2 ip 2.2.2.2 port 7676 protocol UDP
sonic(config-tam)#
sonic(config-tam)# show tam collectors
Name               IP Address       Port    Protocol
----------------   --------------   ------  --------
c2                 2.2.2.2          7676    UDP
```
## compatible 
### Description 
```
Configures OSPFv2 RFC1583 compatibility
```
### Parent Commands (Modes) 
```
router ospf [ vrf <vrf-name> ]
```
### Syntax 
```
compatible rfc1583
no compatible rfc1583
```
### Usage Guidelines 
```
Use this command to configure RFC1583 compatibility.
```
### Examples 
#### Configures RFC1583 compatibility 
```
sonic-cli(config-router-ospf)# compatible rfc1583
```
### Features this CLI belongs to 
*  OSPFv2
## confederation 
### Description 
```
This command configures the list of AS numbers that are part of the
confederation. This command also allows user to configure configure the
router's confederation ID
```
### Parent Commands (Modes) 
```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }
```
### Syntax 
```
confederation { { identifier <id-as> } | { peers <peer-as> } }
no confederation { { identifier <id-as> } | peers }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| id-as | 1-4294967295  | Integer  |
| peer-as | 1-4294967295  | Integer  |
### Usage Guidelines 
```
Use this command to create confederation peers and local confederation
Id
```
### Examples 
#### Below command configures local confederation Id and          creates confederation peers 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# confederation identifier 65000
sonic(config-router-bgp)# confederation peers 65100
sonic(config-router-bgp)# confederation peers 65200
sonic(config-router-bgp)# confederation peers 65300
```
## configure terminal 

### Description 

```
Configure from the terminal 


```
### Syntax 

```
configure terminal

```
## copp-action 
### Description 
```
Configure CoPP action group
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
copp-action <copp-action-name>
no copp-action <copp-action-name>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| copp-action-name | WORD  | String  |
### Examples 
```
sonic(config)# classifier copp-system-arp match-type copp
sonic(config-classifier)#
```
## copy 

### Description 

```
Perform file copy operations 


```
### Syntax 

```
copy { { <copy_config_url> { running-configuration [ overwrite ] } } | { running-configuration <filepath> } | { startup-configuration { running-configuration [ overwrite ] } } }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| copy_config_url | file:  | String  |
| filepath | file:  | String  |


## core enable 

### Description 

```
Enable the cability to generate a core file when an application crash
is detected by the kernel.


```
### Parent Commands (Modes) 

```
configure terminal

```
### Syntax 

```
core enable

```
### Usage Guidelines 

```
sonic(config)# core enable


```
### Examples 

```
sonic# configure terminal
sonic(config)# core enable


```
### Features this CLI belongs to 
*  COREDUMP


### Alternate command 
#### click 

```
config core enable


```
## counters 

### Description 

```
Set ACL counters mode 


```
### Parent Commands (Modes) 

```
access-list

```
### Syntax 

```
counters { per-entry | per-interface-entry }

```
## crm 
### Description 
```
Configure critical resource monitoring 
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
crm { { polling { interval <crm-subcmd-data> } } | { thresholds { { all { { type <crm-subcmd-data> } | { high <crm-subcmd-data> } | { low <crm-subcmd-data> } } } | { acl { { group { { type <crm-subcmd-data> } | { high <crm-subcmd-data> } | { low <crm-subcmd-data> } | { entry { { type <crm-subcmd-data> } | { high <crm-subcmd-data> } | { low <crm-subcmd-data> } } } | { counter { { type <crm-subcmd-data> } | { high <crm-subcmd-data> } | { low <crm-subcmd-data> } } } } } | { table { { type <crm-subcmd-data> } | { high <crm-subcmd-data> } | { low <crm-subcmd-data> } } } } } | { dnat { { type <crm-subcmd-data> } | { high <crm-subcmd-data> } | { low <crm-subcmd-data> } } } | { snat { { type <crm-subcmd-data> } | { high <crm-subcmd-data> } | { low <crm-subcmd-data> } } } | { fdb { { type <crm-subcmd-data> } | { high <crm-subcmd-data> } | { low <crm-subcmd-data> } } } | { ipmc { { type <crm-subcmd-data> } | { high <crm-subcmd-data> } | { low <crm-subcmd-data> } } } | { ipv4 { { neighbor { { type <crm-subcmd-data> } | { high <crm-subcmd-data> } | { low <crm-subcmd-data> } } } | { nexthop { { type <crm-subcmd-data> } | { high <crm-subcmd-data> } | { low <crm-subcmd-data> } } } | { route { { type <crm-subcmd-data> } | { high <crm-subcmd-data> } | { low <crm-subcmd-data> } } } } } | { ipv6 { { neighbor { { type <crm-subcmd-data> } | { high <crm-subcmd-data> } | { low <crm-subcmd-data> } } } | { nexthop { { type <crm-subcmd-data> } | { high <crm-subcmd-data> } | { low <crm-subcmd-data> } } } | { route { { type <crm-subcmd-data> } | { high <crm-subcmd-data> } | { low <crm-subcmd-data> } } } } } | { nexthop { { group { { member { { type <crm-subcmd-data> } | { high <crm-subcmd-data> } | { low <crm-subcmd-data> } } } | { object { { type <crm-subcmd-data> } | { high <crm-subcmd-data> } | { low <crm-subcmd-data> } } } } } } } } } }
no crm { all | { polling interval } | { thresholds { all | { acl { { group { type | high | low | { entry { type | high | low } } | { counter { type | high | low } } } } | { table { type | high | low } } } } | { dnat { type | high | low } } | { snat { type | high | low } } | { fdb { type | high | low } } | { ipmc { type | high | low } } | { ipv4 { { neighbor { type | high | low } } | { nexthop { type | high | low } } | { route { type | high | low } } } } | { ipv6 { { neighbor { type | high | low } } | { nexthop { type | high | low } } | { route { type | high | low } } } } | { nexthop { { group { { member { type | high | low } } | { object { type | high | low } } } } } } } } }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| crm-subcmd-data |   | Integer  |
## dampening 
### Description 
```
This command enables BGP route-flap dampening and specifies dampening
parameters
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
dampening [ <halflife> { [ <reusethr> { <suppressthr> <maxsuppress> } ] } ]
no dampening
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| halflife |   | Integer  |
| reusethr |   | Integer  |
| suppressthr |   | Integer  |
| maxsuppress |   | Integer  |
### Usage Guidelines 
```
Use this command to configure route flap dampening feature for BGP
routes. Route flap dampening algorithm is compatible with RFC 2439. The
use of this command is not recommended nowadays. Currently
implementation works only for IPv4 address family
```
### Examples 
#### The below configuration example enables route-flap          dampeninmg feature 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# address-family ipv4 unicast
sonic(config-router-bgp-af)# dampening 10 1200 2000 40
```
## default interface 

### Description 

```
Use this command to restore a selected port to its factory default configuration.


```
### Parent Commands (Modes) 

```
configure terminal

```
### Syntax 

```
default interface <iface>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| iface | EthernetNUM  |   |


### Usage Guidelines 

```
default interface if-name


```
### Examples 

```
sonic# configure terminal
sonic(config)# default interface Ethernet 8


```
## default interface range 

### Description 

```
Apply default configuration on an interface range 


```
### Parent Commands (Modes) 

```
configure terminal

```
### Syntax 

```
default interface range iface_range_num

```
## default ipv4-unicast 
### Description 
```
This command activate IPv4 unicast address-family for a BGP peer by
default
```
### Parent Commands (Modes) 
```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }
```
### Syntax 
```
default ipv4-unicast
no default ipv4-unicast
```
### Usage Guidelines 
```
Use this command to activate IPv4 unicast address family on BGP
neighbors by default
```
### Examples 
#### Below command enables IPv4 unicast address family on all          BGP neighbors by default 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# default ipv4-unicast
```
## default local-preference 
### Description 
```
This command the default value for Local Preference parameter
default
```
### Parent Commands (Modes) 
```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }
```
### Syntax 
```
default local-preference <lprftime>
no default local-preference
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| lprftime |   | Integer  |
### Usage Guidelines 
```
Use this command to set the deafult value of Local Preference parameter
```
### Examples 
#### Below command sets default local preference to 200 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# default local-preference 200
```
## default show-hostname 
### Description 
```
This command instructs BGP to display hostname in certain display
commands
```
### Parent Commands (Modes) 
```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }
```
### Syntax 
```
default show-hostname
no default show-hostname
```
### Usage Guidelines 
```
Use this command to instruct BGP to display hostname in certain display
command outputs.
```
### Examples 
#### Below command sets BGP to display hostname in certain          display commands 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# default show-hostname
```
## default shutdown 
### Description 
```
This command instructs BGP to make newly created neighbors in shutdown
state
```
### Parent Commands (Modes) 
```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }
```
### Syntax 
```
default shutdown
no default shutdown
```
### Usage Guidelines 
```
By default, newly created BGP neighbors are in admin enabled state. Use
this command to keep newly created BGP neighbors in admin shutdown
state.
```
### Examples 
#### Below command configures BGP to keep newly created          neighbors in shutdown state 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# default shutdown
```
## default subgroup-pkt-queue-max 
### Description 
```
This command configures the maximum packet queue length for update
groups
```
### Parent Commands (Modes) 
```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }
```
### Syntax 
```
default subgroup-pkt-queue-max <maxval>
no default subgroup-pkt-queue-max
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| maxval |   | Integer  |
### Usage Guidelines 
```
Use this command to set a default maximum packet queue length for update
groups
```
### Examples 
#### Below command configures maximum packet queue length to          50 for update groups 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# default subgroup-pkt-queue-max 50
```
## default-information 
### Description 
```
Configures default information origination policy rules.
```
### Parent Commands (Modes) 
```
router ospf [ vrf <vrf-name> ]
```
### Syntax 
```
default-information originate { [ always ] { [ metric <metricval> ] } { [ metric-type <mertrictype> ] } { [ route-map <routemapname> ] } }
no default-information originate { [ always ] [ metric ] [ metric-type ] [ route-map ] }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| metricval |   | Integer  |
| mertrictype |   | Integer  |
| routemapname | WORD  | String  |
### Usage Guidelines 
```
Use this command to configures default route information origination parameters. Metric
value and metric type can be explicitely specified for default routes. Route map rules
can also be applied on default routes.
```
### Examples 
#### Configures default route origination parameters 
```
sonic-cli(config-router-ospf)# default-information originate always
sonic-cli(config-router-ospf)# default-information originate metric 80
sonic-cli(config-router-ospf)# default-information originate metric-type 1
sonic-cli(config-router-ospf)# default-information originate route-map rmap_droute
```
### Features this CLI belongs to 
*  OSPFv2
## default-metric 
### Description 
```
Configures default metric for redistributed routes.
```
### Parent Commands (Modes) 
```
router ospf [ vrf <vrf-name> ]
```
### Syntax 
```
default-metric <defaultmetric>
no default-metric
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| defaultmetric |   | Integer  |
### Usage Guidelines 
```
Use this command to configures metric for redistributed routes.
```
### Examples 
#### Configures default metric for redistributed routes. 
```
sonic-cli(config-router-ospf)# default-metric 28
```
### Features this CLI belongs to 
*  OSPFv2
## default-originate 
### Description 
```
Originate default route to this neighbor 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
default-originate [ route-map <rtemap> ]
no default-originate [ route-map <rtemap> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| rtemap | WORD  | String  |
## default-originate 
### Description 
```
Originate default route to this neighbor 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
default-originate [ route-map <rtemap> ]
no default-originate [ route-map <rtemap> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| rtemap | WORD  | String  |
## default-originate 
### Description 
```
This command enables BGP to originate default route to this neighbor
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
default-originate [ route-map <rtemap> ]
no default-originate [ route-map <rtemap> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| rtemap | WORD  | String  |
### Usage Guidelines 
```
Use this command to originate a default route to this neighbor. User can
optionally use route-map along with this command to to specify criteria
to originate default
```
### Examples 
#### Following command enables BGP to originate a default          route to neighbor 20.20.20.2 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 20.20.20.2
sonic(config-router-bgp-neighbor)# remote-as 300
sonic(config-router-bgp-neighbor)# address-family ipv4 unicast
sonic(config-router-bgp-neighbor-af)# default-originate
```
## default-originate 
### Description 
```
This command enables BGP to originate default route to neighbors in a
peer-group
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
default-originate [ route-map <rtemap> ]
no default-originate [ route-map <rtemap> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| rtemap | WORD  | String  |
### Usage Guidelines 
```
Use this command to originate default route to neighbors in a peer-group. User can
optionally use route-map along with this command to to specify criteria
to originate default
```
### Examples 
#### Following command enables BGP to originate default          route to neighbors in peer-group PG_Ext 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# address-family ipv4 unicast
sonic(config-router-bgp-pg-af)# default-originate
```
## default-originate ipv4 
### Description 
```
This command enables border leaf to originate IPv4 default type-5 EVPN routes
```
### Parent Commands (Modes) 
```
address-family l2vpn evpn
```
### Syntax 
```
default-originate ipv4
no default-originate ipv4
```
### Usage Guidelines 
```
[no] default-originate ipv4
```
### Examples 
#### Following command enables origination of default IPv4 type-5 EVPN routes in vrf Vrf1 
```
sonic# configure terminal
sonic(config)# router bgp 100 vrf Vrf1
sonic(config-router-bgp)# address-family l2vpn evpn
sonic(config-router-bgp-af)# default-originate ipv4
```
## default-originate ipv6 
### Description 
```
This command enables border leaf to originate IPv6 default type-5 EVPN routes
```
### Parent Commands (Modes) 
```
address-family l2vpn evpn
```
### Syntax 
```
default-originate ipv6
no default-originate ipv6
```
### Usage Guidelines 
```
[no] default-originate ipv6
```
### Examples 
#### Following command enables origination of default IPv6 type-5 EVPN routes in vrf Vrf1 
```
sonic# configure terminal
sonic(config)# router bgp 100 vrf Vrf1
sonic(config-router-bgp)# address-family l2vpn evpn
sonic(config-router-bgp-af)# default-originate ipv6
```
## delay-restore 
### Description 
```
Configures MCLAG delay restore time in seconds
```
### Parent Commands (Modes) 
```
mclag domain <mclag-domain-id>
```
### Syntax 
```
delay-restore <DL>
no delay-restore
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| DL | Delay restore time in seconds  | Integer  |
### Usage Guidelines 
```
Use this command to change the default MCLAG delay restore time
```
### Examples 
#### Configures MCLAG delay restore time to 180 seconds for MCLAG domain 100 
```
sonic-cli(config-mclag-domain-100)#delay-restore 180
```
## description 
### Description 
```
This command configures a display string for a BGP neighbor
```
### Parent Commands (Modes) 
```
neighbor { <ip> | { interface { Ethernet | PortChannel | Vlan } } }
```
### Syntax 
```
description <String>
no description [ <String> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| String | String  | String  |
### Usage Guidelines 
```
Use this command to configure a descriptive string for a BGP neighbor
```
### Examples 
#### Following command configures description for BGP          neighbor 30.30.30.3 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 30.30.30.3
sonic(config-router-bgp-neighbor)# remote-as 65100
sonic(config-router-bgp-neighbor)# description to_nyc_dc1
```
## description 
### Description 
```
This command configures a display string for a BGP peer-group
```
### Parent Commands (Modes) 
```
peer-group <template-str>
```
### Syntax 
```
description <String>
no description [ <String> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| String | String  | String  |
### Usage Guidelines 
```
Use this command to configure a descriptive string for a BGP peer-group
```
### Examples 
#### Following command configures description for BGP          peer-group PG_Ext 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# description My_PG_East_Cost_Nbrs
```
## description 
### Description 
```
Configures policy-map description
```
### Parent Commands (Modes) 
```
policy-map <fbs-policy-name> type { qos | monitoring | forwarding | copp }
```
### Syntax 
```
description <description-value>
no description
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| description-value | String (Max: 256 characters)  | String  |
### Usage Guidelines 
```
A string describing the policy-map max 256 characters. Description should be in double quotes if it has spaces
```
### Examples 
#### add description for policy-map 
```
sonic(config-policy-map)# description "Vrf policy information"
```
## description 
### Description 
```
Configures Class-map description
```
### Parent Commands (Modes) 
```
class-map <fbs-class-name> match-type { acl | { fields match-all } | copp }
```
### Syntax 
```
description <description-value>
no description
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| description-value | String (Max: 256 characters)  | String  |
### Usage Guidelines 
```
```
### Examples 
#### add description for class-map 
```
sonic(config-class-map)# description"ip match type class-map"
```
## description 
### Description 
```
Configures Class-map description
```
### Parent Commands (Modes) 
```
class-map <fbs-class-name> match-type { acl | { fields match-all } | copp }
```
### Syntax 
```
description <description-value>
no description
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| description-value | String (Max: 256 characters)  | String  |
### Usage Guidelines 
```
```
### Examples 
#### add description for class-map 
```
sonic(config-class-map)# description"ip match type class-map"
```
## description 
### Description 
```
Configures Class-map description
```
### Parent Commands (Modes) 
```
class-map <fbs-class-name> match-type { acl | { fields match-all } | copp }
```
### Syntax 
```
description <description-value>
no description
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| description-value | String (Max: 256 characters)  | String  |
### Usage Guidelines 
```
```
### Examples 
#### add description for class-map 
```
sonic(config-class-map)# description"ip match type class-map"
```
## description 
### Description 
```
Updates description for qos flows
```
### Parent Commands (Modes) 
```
class <fbs-class-name> [ priority <fbs-flow-priority> ]
```
### Syntax 
```
description <description-value>
no description
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| description-value | String (Max: 256 characters)  | String  |
### Usage Guidelines 
```
A string describing the qos flow max 256 characters. Description should be in double quotes if it has spaces
```
### Examples 
#### Configures description for qos flows 
```
sonic(config)# policy-map policy_qos type qos
sonic(config-policy-map)# class class_permit_ip priority 10
sonic(config-policy-map-flow)# description "qos flow1"
```
## description 
### Description 
```
Set description to a link state tracking group.
```
### Parent Commands (Modes) 
```
link state track <grp-name>
```
### Syntax 
```
description <grp-descr>
no description
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| grp-descr | String  | String  |
### Examples 
#### Set description 
```
sonic(config-link-track)# description "Example description"
```
### Alternate command 
#### click 
```
admin@sonic:~$ sudo config linktrack update <name> --description <description>
```
## description 
### Description 
```
Textual description 
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
description <desc>
no description
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| desc | STRING  | String  |
## description 
### Description 
```
Textual description 
```
### Parent Commands (Modes) 
```
interface Loopback <lo-id>
```
### Syntax 
```
description <desc>
no description
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| desc | STRING  | String  |
## description 
### Description 
```
Textual description 
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
description <desc>
no description
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| desc | STRING  | String  |
## description 
### Description 
```
Textual description 
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
description <desc>
no description
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| desc | STRING  | String  |
## description 
### Description 
```
Textual description 
```
### Parent Commands (Modes) 
```
interface Management <mgmt-if-id>
```
### Syntax 
```
description <desc>
no description
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| desc | String  | String  |
## description 
### Description 
```
Textual description 
```
### Parent Commands (Modes) 
```
interface range iface_range_num
```
### Syntax 
```
description <desc>
no description
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| desc | STRING  | String  |
## description 
### Description 
```
Textual description 
```
### Parent Commands (Modes) 
```
interface range create vlan_range_num
interface range vlan_range_num
```
### Syntax 
```
description <desc>
no description
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| desc | STRING  | String  |
## description 
### Description 
```
Textual description 
```
### Parent Commands (Modes) 
```
interface range create po_range_num { [ mode <PoMode> ] } { [ min-links <min-links-value> ] } [ fallback ] [ fast_rate ]
interface range po_range_num
```
### Syntax 
```
description <desc>
no description
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| desc | STRING  | String  |
## destination 

### Description 

```
Configure SPAN mirror session. Supports mirroring to physical interface.
Source interface can be either port or portchannel.
Supports mirroring in rx/tx or both directions.
Mirror session can be used in ACL configurations.


```
### Parent Commands (Modes) 

```
mirror-session <session-name>

```
### Syntax 

```
destination <phy-if-id> [ source { <phy-if-name> | <po-if-name> } ] [ direction <sess-direction> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| phy-if-id | EthernetNUM  |   |
| phy-if-name | EthernetNUM  |   |
| po-if-name | PortChannelNUM  |   |
| sess-direction | Port mirror session direction  | Select [rx(rx) tx(tx) both(both) ]  |


### Examples 

```
sonic(config)# mirror-session Mirror1
sonic(config-mirror-Mirror1)# destination Ethernet0 source Ethernet4 direction rx
Success
sonic(config-mirror-Mirror1)# exit


```
## destination CPU 

### Description 

```
Configure SPAN mirror session to CPU port.
Source interface can be either port or portchannel.
Supports mirroring in rx/tx or both directions.
Mirror session can be used in ACL configurations.


```
### Parent Commands (Modes) 

```
mirror-session <session-name>

```
### Syntax 

```
destination CPU [ source { <phy-if-name> | <po-if-name> } ] [ direction <sess-direction> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| phy-if-name | EthernetNUM  |   |
| po-if-name | PortChannelNUM  |   |
| sess-direction | Port mirror session direction  | Select [rx(rx) tx(tx) both(both) ]  |


### Examples 

```
sonic(config)# mirror-session Mirror1
sonic(config-mirror-Mirror1)# destination Ethernet0 source Ethernet4 direction rx
Success
sonic(config-mirror-Mirror2)#


```
## destination erspan 

### Description 

```
Configure ERSPAN mirror session. Supports mirroring to any destination IP.
Source interface can be either port or portchannel.
Supports mirroring in rx/tx or both directions.
Mirror session can be used in ACL configurations.


```
### Parent Commands (Modes) 

```
mirror-session <session-name>

```
### Syntax 

```
destination erspan [ dst-ip <dst_ip> ] [ src-ip <src_ip> ] [ dscp <ip_dscp> ] [ gre <ip_gre> ] [ ttl <ip_ttl> ] [ queue <queue_val> ] [ source { <phy-if-name> | <po-if-name> } ] [ direction <sess-direction> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| dst_ip | A.B.C.D  | String  |
| src_ip | A.B.C.D  | String  |
| ip_dscp |   | Integer  |
| ip_gre | Hexadecimal Type  | String  |
| ip_ttl |   | Integer  |
| queue_val |   | Integer  |
| phy-if-name | EthernetNUM  |   |
| po-if-name | PortChannelNUM  |   |
| sess-direction | Port mirror session direction  | Select [rx(rx) tx(tx) both(both) ]  |


### Examples 

```
sonic(config)# mirror-session Mirror2
sonic(config-mirror-Mirror2)# destination erspan dst-ip 10.1.1.1 src-ip 11.1.1.1 dscp 10 ttl 10 gre 0x88ee queue 10 source Ethernet4 direction rx
Success
sonic(config-mirror-Mirror2)#


```
## detect-multiplier 

### Description 

```
Configure detection multiplier for Bidirectional Forwarding detection(BFD) peer for timeout.


```
### Parent Commands (Modes) 

```
peer <peer_ipv4>
peer <peer_ipv6>
peer [ interface ] <interfacename>
peer [ local-address ] <local_ipv4>
peer [ local-address ] <local_ipv6>
peer [ multihop ]
peer [ vrf ] <vrfname>

```
### Syntax 

```
detect-multiplier <multiplier>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| multiplier |   | Integer  |


### Usage Guidelines 

```
Default value is 3.


```
### Examples 
#### Configure detection multiplier 

```
device()#configure terminal
device(config)#bfd
device(conf-bfd)# peer 192.168.0.5 interface Ethernet0
device(conf-bfd-peer)# detect-multiplier 5


```
## deterministic-med 
### Description 
```
This command enables to carry out route-selection in a way that produces
deterministic results locally, even in the face of MED and the lack of a
well-defined order of preference it can induce on routes.
```
### Parent Commands (Modes) 
```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }
```
### Syntax 
```
deterministic-med
no deterministic-med
```
### Usage Guidelines 
```
Carry out route-selection in a way that produces deterministic answers
locally, even in the face of MED and the lack of a well-defined order of
preference it can induce on routes. Without this option the preferred
route with MED may be determined largely by the order that routes were
received in.
Setting this option will have a performance cost that may be noticeable
when there are many routes for each destination. Currently in BGP it is
implemented in a way that scales poorly as the number of routes per
destination increases.
By default deterministic-med is disabled
```
### Examples 
#### Below command enables deterministics-med 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# deterministic-med
```
## disable-connected-check 
### Description 
```
This command disables the restriction that eBGP peers must be directly
connected
```
### Parent Commands (Modes) 
```
neighbor { <ip> | { interface { Ethernet | PortChannel | Vlan } } }
```
### Syntax 
```
disable-connected-check
no disable-connected-check
```
### Usage Guidelines 
```
Use this command to allow peerings between directly connected eBGP peers
using loopback addresses.
```
### Examples 
#### Following command disables connected check           for BGP neighbor 30.30.30.3 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 30.30.30.3
sonic(config-router-bgp-neighbor)# disable-connected-check
```
## disable-connected-check 
### Description 
```
This command disables the restriction that eBGP peers must be directly
connected
```
### Parent Commands (Modes) 
```
peer-group <template-str>
```
### Syntax 
```
disable-connected-check
no disable-connected-check
```
### Usage Guidelines 
```
Use this command to allow peerings between directly connected eBGP peers
using loopback addresses.
```
### Examples 
#### Following command disables connected check           for BGP peer-group PG_Ext 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# disable-connected-check
```
## disable-ebgp-connected-route-check 
### Description 
```
This command disables eBGP connected route check
```
### Parent Commands (Modes) 
```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }
```
### Syntax 
```
disable-ebgp-connected-route-check
no disable-ebgp-connected-route-check
```
### Usage Guidelines 
```
Use this command to disable checking if next-hop is conencted on ebgp
sessions. When BGP peering is between the loopback interfaces, user
should enable this option.
```
### Examples 
#### Below command disables eBGP connected route check 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# disable-ebgp-connected-route-check
```
## distance 
### Description 
```
Configures distance value to OSPFv2 routes.
```
### Parent Commands (Modes) 
```
router ospf [ vrf <vrf-name> ]
```
### Syntax 
```
distance { [ <admindistance> ] | { [ ospf { [ external <extdistance> ] } { [ inter-area <interdistance> ] } { [ intra-area <intradistance> ] } ] } }
no distance [ ospf { [ external ] | [ inter-area ] | [ intra-area ] } ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| admindistance |   | Integer  |
| extdistance |   | Integer  |
| interdistance |   | Integer  |
| intradistance |   | Integer  |
### Usage Guidelines 
```
Use this command to configure route distance value to OSPFv2 routes. Distance value
can also be configured based on route types link inter-area routes, intra-area routes
and external routes.
```
### Examples 
#### Configures distance value to OSPFv2 routes. 
```
sonic-cli(config-router-ospf)# distance ospf 40
sonic-cli(config-router-ospf)# distance intra-area 10 inter-area 20 external 30
```
### Features this CLI belongs to 
*  OSPFv2
## distance bgp 
### Description 
```
Define an administrative distance 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
distance bgp <external> { <internal> <local> }
no distance bgp
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| external |   | Integer  |
| internal |   | Integer  |
| local |   | Integer  |
## distance bgp 
### Description 
```
This command changes distance value of BGP. The command allows finer
control to change the distance values for external routes, internal
routes and local routes separately
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
distance bgp <external> { <internal> <local> }
no distance bgp
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| external |   | Integer  |
| internal |   | Integer  |
| local |   | Integer  |
### Usage Guidelines 
```
Use this command to configure administrative distance for BGP route
types
```
### Examples 
#### This configuration example sets administrative distance          of 100, 50 and 10 for external, internal and local BGP routes          respectively under ipv4 address-family 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# address-family ipv4 unicast
sonic(config-router-bgp-af)# distance bgp 100 50 10
```
## dont-capability-negotiate 
### Description 
```
This command suppresses sending Capability Negotiation as OPEN message
optional parameter to the peer. This command only affects the peer is
configured other than IPv4 unicast configuration.
When remote peer does not have capability negotiation feature, remote
peer will not send any capabilities at all. In that case, bgp configures
the peer with configured capabilities.
You may prefer locally configured capabilities more than the negotiated
capabilities even though remote peer sends capabilities. If the peer is
configured by override-capability, BGP ignores received capabilities
then override negotiated capabilities with configured values.
Additionally the user should be reminded that this feature
fundamentally disables the ability to use widely deployed BGP features -
BGP unnumbered, hostname support, AS4, Addpath, Route Refresh, ORF,
Dynamic Capabilities, and graceful restart.
```
### Parent Commands (Modes) 
```
neighbor { <ip> | { interface { Ethernet | PortChannel | Vlan } } }
```
### Syntax 
```
dont-capability-negotiate
no dont-capability-negotiate
```
### Usage Guidelines 
```
Use this command to disable capability negotiation for a BGP neighbor
```
### Examples 
#### Following command disables capability negotiation           for BGP neighbor 30.30.30.3 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 30.30.30.3
sonic(config-router-bgp-neighbor)# dont-capability-negotiate
```
## dont-capability-negotiate 
### Description 
```
This command suppresses sending Capability Negotiation as OPEN message
optional parameter to neighbors in a peer-group. This command only
affects the peer is configured other than IPv4 unicast configuration.
When remote peer does not have capability negotiation feature, remote
peer will not send any capabilities at all. In that case, bgp configures
the peer with configured capabilities.
You may prefer locally configured capabilities more than the negotiated
capabilities even though remote peer sends capabilities. If the peer is
configured by override-capability, BGP ignores received capabilities
then override negotiated capabilities with configured values.
Additionally the user should be reminded that this feature
fundamentally disables the ability to use widely deployed BGP features -
BGP unnumbered, hostname support, AS4, Addpath, Route Refresh, ORF,
Dynamic Capabilities, and graceful restart.
```
### Parent Commands (Modes) 
```
peer-group <template-str>
```
### Syntax 
```
dont-capability-negotiate
no dont-capability-negotiate
```
### Usage Guidelines 
```
Use this command to disable capability negotiation for BGP neighbors in
a peer-group
```
### Examples 
#### Following command disables capability negotiation           for BGP peer-group PG_Ext 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# dont-capability-negotiate
```
## dot1p 
### Description 
```
This command to add DOT1P to Traffic class entry in map.
```
### Parent Commands (Modes) 
```
qos map dot1p-tc <name>
```
### Syntax 
```
dot1p <dot1p_list> { traffic-class <tc> }
no dot1p <dot1p_list>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| dot1p_list |   | String  |
| tc |   | Integer  |
### Usage Guidelines 
```
Use this command to add entry to map DOT1P to Traffic class.
```
### Examples 
#### The following command add entry to map DOT1P to Traffic class 
```
sonic# configure terminal
sonic(config)# dot1p 1 traffic-class 0
sonic(config)# dot1p 2 traffic-class 0
sonic(config)# dot1p 3 traffic-class 1
```
## downstream 
### Description 
```
Configure all mclags as downstream interfaces.
```
### Parent Commands (Modes) 
```
link state track <grp-name>
```
### Syntax 
```
downstream all-mclag
no downstream all-mclag
```
### Examples 
#### Configure all-mclags as downstream interfaces 
```
sonic(config-link-track)# downstream all-mclag
```
### Alternate command 
#### click 
```
admin@sonic:~$ sudo config linktrack update <name> --downstream all-mclag
```
## drop-monitor 

### Description 

```
Configures drop-monitor feature 


```
### Parent Commands (Modes) 

```
tam

```
### Syntax 

```
drop-monitor

```
## drop-monitor 
### Description 
```
This CLI is used to create flow configuration for drop-monitor
```
### Parent Commands (Modes) 
```
switch-resource
```
### Syntax 
```
drop-monitor flows <flows-name>
no drop-monitor flows
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| flows-name | min or none  | Select [min(min) none(none) ]  |
### Usage Guidelines 
```
This command is used to flow configurations for Drop-Monitor in the EM Entry Table
```
### Examples 
#### Configure Drop Monitor Flow Scale configuration 
```
sonic(config-switch-resource)# drop-monitor flows min
```
## dscp 
### Description 
```
This command to add DSCP to Traffic class entry in map.
```
### Parent Commands (Modes) 
```
qos map dscp-tc <name>
```
### Syntax 
```
dscp <dscp_list> { traffic-class <tc> }
no dscp <dscp_list>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| dscp_list |   | String  |
| tc |   | Integer  |
### Usage Guidelines 
```
Use this command to add entry to map DSCP to Traffic class.
```
### Examples 
#### The following command add entry to map DSCP to Traffic class 
```
sonic# configure terminal
sonic(config)# dscp 1 traffic-class 0
sonic(config)# dscp 2 traffic-class 0
sonic(config)# dscp 3 traffic-class 1
```
## dup-addr-detection 
### Description 
```
This command allows to set the threshold for address moves, including maximum moves allowed and maximum time interval
```
### Parent Commands (Modes) 
```
address-family l2vpn evpn
```
### Syntax 
```
dup-addr-detection [ max-moves { <nummoves> { time <timevalue> } } ]
no dup-addr-detection [ max-moves { <nummoves> { time <timevalue> } } ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| nummoves | Number of moves 2-1000, default 5  | Integer  |
| timevalue | Time in seconds 2-1800, default 180  | Integer  |
### Usage Guidelines 
```
[no] dup-addr-detection max-moves {max-moves-number} time {timer-value}
```
### Examples 
#### Following command set duplicate address detection threshold to           maximum moves of 10 in time-interval of 1200 seconds 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# address-family l2vpn evpn
sonic(config-router-bgp-af)# dup-addr-detection max-moves 10 time 1200
```
## dup-addr-detection freeze 
### Description 
```
This command allows to specify the action to be taken on duplicate address detection.
It allows to configure freezing the address permanently or for a specified duration
```
### Parent Commands (Modes) 
```
address-family l2vpn evpn
```
### Syntax 
```
dup-addr-detection freeze { permanent | <time> }
no dup-addr-detection freeze { permanent | <time> }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| time | Time in seconds 30-3600, default 180  | Integer  |
### Usage Guidelines 
```
[no] dup-addr-detection freeze permanent|{freeze-time}
```
### Examples 
#### Following command specifies the action to permanently freeze the address           unless user intervenes 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# address-family l2vpn evpn
sonic(config-router-bgp-af)# dup-addr-detection freeze permanent
```
## ebgp-multihop 
### Description 
```
This command enable multihop attribute for EBGP neighbors
```
### Parent Commands (Modes) 
```
neighbor { <ip> | { interface { Ethernet | PortChannel | Vlan } } }
```
### Syntax 
```
ebgp-multihop [ <hop-count> ]
no ebgp-multihop [ <hop-count> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| hop-count |   | Integer  |
### Usage Guidelines 
```
EBGP neighbors that are multiple hops away need this configuration. User
can optionally set the maximum hops that BGP neighbors can be apart.
```
### Examples 
#### Following command configures ebgp-multihop for neighbor          30.30.30.3 with maximum hops = 10 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 30.30.30.3
sonic(config-router-bgp-neighbor)# remote-as 65100
sonic(config-router-bgp-neighbor)# ebgp-multihop 10
```
## ebgp-multihop 
### Description 
```
This command enables multihop for EBGP peer-group
```
### Parent Commands (Modes) 
```
peer-group <template-str>
```
### Syntax 
```
ebgp-multihop [ <hop-count> ]
no ebgp-multihop [ <hop-count> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| hop-count |   | Integer  |
### Usage Guidelines 
```
peer-group with eBGP neighbors as members that are multiple hops away
need this configuration. User can optionally set the maximum hops that
BGP neighbors in peer-group can be apart.
```
### Examples 
#### Following command configures ebgp-mltihop for peer-group          PG_Ext with max hop = 10 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# ebgp-multihop 10
```
## echo-interval 

### Description 

```
Configure desired echo packet transmit interval for Bidirectional Forwarding detection(BFD) peer.


```
### Parent Commands (Modes) 

```
peer <peer_ipv4>
peer <peer_ipv6>
peer [ interface ] <interfacename>
peer [ local-address ] <local_ipv4>
peer [ local-address ] <local_ipv6>
peer [ multihop ]
peer [ vrf ] <vrfname>

```
### Syntax 

```
echo-interval <echo_interval>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| echo_interval |   | Integer  |


### Usage Guidelines 

```
Default value is 50 milliseconds.


```
### Examples 
#### Configure packet echo transmit interval 

```
device()#configure terminal
device(config)#bfd
device(conf-bfd)# peer 192.168.0.5 interface Ethernet0
device(conf-bfd-peer)# echo-interval 200


```
## echo-mode 
### Description 
```
Enable echo-mode for Bidirectional Forwarding detection(BFD) peer.
```
### Parent Commands (Modes) 
```
peer <peer_ipv4>
peer <peer_ipv6>
peer [ interface ] <interfacename>
peer [ local-address ] <local_ipv4>
peer [ local-address ] <local_ipv6>
peer [ multihop ]
peer [ vrf ] <vrfname>
```
### Syntax 
```
echo-mode
no echo-mode
```
### Usage Guidelines 
```
This command can be used to enable echo mode for BFD single-hop peer, echo mode is not supported for multi-hop peers.
```
### Examples 
#### Enable echo mode 
```
device()#configure terminal
device(config)#bfd
device(conf-bfd)# peer 192.168.0.5 interface Ethernet0
device(conf-bfd-peer)# echo-mode
```
## ecn 
### Description 
```
This command to enable ECN based on color.
This commands support options as none/green/all.
Option "none" to disable ECN. Option "green" for ECN for green color alone.
Option "all" to enable ECN on all configured colors.
```
### Parent Commands (Modes) 
```
qos wred-policy <name>
```
### Syntax 
```
ecn <ecn>
no ecn
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| ecn | ECN Options  | Select [none(ecn_none) green(ecn_green) all(ecn_all) ]  |
### Usage Guidelines 
```
Use this command to configure ECN for color.
```
### Examples 
#### The following command to enable ECN for green color packets 
```
sonic(conf-wred-wred-green)# ecn green
```
## enable 
### Description 
```
This command activates IFA feature on the switch.
```
### Parent Commands (Modes) 
```
ifa
```
### Syntax 
```
enable
no enable
```
### Usage Guidelines 
```
This command activates IFA feature on the switch. IFA activated switches act as intermediate nodes for all IFA-tagged flows transiting the switch.
```
### Examples 
#### sonic (config-tam-ifa)# enable 
```
sonic(config)# tam
sonic(config-tam)# ifa
sonic(config-tam-ifa)# enable
sonic(config-tam-ifa)# end
sonic# show tam ifa
Status               : Active
Switch ID            : 9876
Enterprise ID        : 8798
Version              : 2.0
Number of sessions   : 0
Number of collectors : 0
sonic#
```
## enable 
### Description 
```
This command activates Drop Monitor feature on the switch.
```
### Parent Commands (Modes) 
```
drop-monitor
```
### Syntax 
```
enable
no enable
```
### Usage Guidelines 
```
This command activates Drop Monitor feature on the switch.
```
### Examples 
#### sonic (config-tam-dm)# enable 
```
sonic(config-tam-dm)# enable
sonic(config-tam-dm)# end
sonic# show tam drop-monitor
Status               : Active
Switch ID            : 9876
Number of sessions   : 2
Number of collectors : 1
Aging Interval       : 30
sonic#
```
## enable 
### Description 
```
This command activates Tail Stamping feature on the switch.
```
### Parent Commands (Modes) 
```
tail-stamping
```
### Syntax 
```
enable
no enable
```
### Usage Guidelines 
```
This command activates Tail Stamping feature on the switch.
```
### Examples 
#### sonic (config-tam-ts)# enable 
```
sonic# configure terminal
sonic(config)# tam
sonic(config-tam)# tail-stamping
sonic(config-tam-ts)# enable
sonic(config-tam-ts)# end
sonic# show tam tail-stamping
Status               : Active
Switch ID            : 9876
Number of sessions   : 0
sonic#
```
## enable 
### Description 
```
Enables NAT feature 
```
### Parent Commands (Modes) 
```
nat
```
### Syntax 
```
enable
no enable
```
## enforce-first-as 
### Description 
```
This command enforces that first AS in as-path of a route received from
BGP peer must be peer's AS number
```
### Parent Commands (Modes) 
```
neighbor { <ip> | { interface { Ethernet | PortChannel | Vlan } } }
```
### Syntax 
```
enforce-first-as
no enforce-first-as
```
### Usage Guidelines 
```
Use this command to enforce that first AS in as-path of route from eBGP
peer must be peer's local AS number
```
### Examples 
#### Following command enables enforcement of first AS number           for BGP neighbor 30.30.30.3 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 30.30.30.3
sonic(config-router-bgp-neighbor)# enforce-first-as
```
## enforce-first-as 
### Description 
```
This command enforces that first AS in as-path of a route received from
BGP peer must be peer's AS number
```
### Parent Commands (Modes) 
```
peer-group <template-str>
```
### Syntax 
```
enforce-first-as
no enforce-first-as
```
### Usage Guidelines 
```
Use this command to enforce that first AS in as-path of route from eBGP
peer must be peer's local AS number
```
### Examples 
#### Following command enables enforcement of first AS number           for BGP peer-group PG_Ext 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# enforce-first-as
```
## enforce-multihop 
### Description 
```
This command enforces that eBGP neighbors perform multihop
```
### Parent Commands (Modes) 
```
neighbor { <ip> | { interface { Ethernet | PortChannel | Vlan } } }
```
### Syntax 
```
enforce-multihop
no enforce-multihop
```
### Usage Guidelines 
```
Use this command to enforce eBGP neighbors perform multihop
```
### Examples 
#### Following command enables enforce-multihop           for BGP neighbor 30.30.30.3 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 30.30.30.3
sonic(config-router-bgp-neighbor)# enforce-multihop
```
## enforce-multihop 
### Description 
```
This command enforces that eBGP neighbors in a peer-group perform multihop
```
### Parent Commands (Modes) 
```
peer-group <template-str>
```
### Syntax 
```
enforce-multihop
no enforce-multihop
```
### Usage Guidelines 
```
Use this command to enforce eBGP neighbors in a peer-group perform multihop
```
### Examples 
#### Following command enables enforce-multihop           for BGP peer-group PG_Ext 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# enforce-multihop
```
## enterprise-id 
### Description 
```
This command configures a 32-bit identifier that is used in the IPFIX telemetry reports.
```
### Parent Commands (Modes) 
```
tam
```
### Syntax 
```
enterprise-id <id>
no enterprise-id
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| id | 1-4294967295  | Integer  |
### Usage Guidelines 
```
This command configures a 32-bit identifier that is used in the IPFIX telemetry reports. When not configured, the last 16-bits from the system mac address are used.
```
### Examples 
#### enterprise-id <number> 
```
sonic# configure terminal
sonic(config)# tam
sonic(config-tam)# enterprise-id 5678
sonic(config-tam)# exit
sonic(config)# exit
sonic# show tam switch
TAM Device information
----------------------
Switch ID      : 1234
Enterprise ID  : 5678
sonic#
```
## errdisable recovery cause 
### Description 
```
Enables error disable recovery for the given cause.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
errdisable recovery cause { [ udld ] | [ bpduguard ] }
no errdisable recovery cause { [ udld ] | [ bpduguard ] }
```
### Usage Guidelines 
```
Use this command to enable error disable recovery for the given cause.
```
### Examples 
#### Enable error disable recovery for udld. 
```
sonic-cli(config)# errdisable recovery cause udld
```
### Features this CLI belongs to 
*  ERRDISABLE
## errdisable recovery interval 
### Description 
```
Configures error disable recovery interval.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
errdisable recovery interval <interval>
no errdisable recovery interval
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| interval |   | Integer  |
### Usage Guidelines 
```
Use this command to set errdisable recovery interval. Default value is 300.
```
### Examples 
#### Configure error disable recovery interval. 
```
sonic-cli(config)# errdisable recovery interval 200
```
### Features this CLI belongs to 
*  ERRDISABLE
## factory 

### Description 

```
This command is used to set the factory default configuration profile of a SONiC switch. This
command removes the currently running switch configuration and creates a new startup configuration
file using the input default configuration profile name. The newly created startup configuration
is also applied as part of this command. This command may result in a loss of switch connectivity
as it results in a restart of all SONiC application services.


```
### Parent Commands (Modes) 

```
configure terminal

```
### Syntax 

```
factory default profile <config_profile> [ confirm ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| config_profile | String  | String  |


### Usage Guidelines 

```
factory default profile profile-name


```
### Examples 

```
sonic# configure terminal
sonic(config)# factory default profile l2


```
## fast-external-failover 
### Description 
```
This command causes bgp to take down ebgp peers immediately when a
link flaps.
```
### Parent Commands (Modes) 
```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }
```
### Syntax 
```
fast-external-failover
no fast-external-failover
```
### Usage Guidelines 
```
Use this command to control how sensitive eBGP neighborship is to the
underlying link failure.
```
### Examples 
#### Below command enables fast external failover feature for          BGP neighbors 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# fast-external-failover
```
## fast-reboot 

### Description 

```
fast-reboot [options] (-h shows help) 


```
### Syntax 

```
fast-reboot [ <options> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| options | options  | String  |


## fec 
### Description 
```
Configure FEC (forward error correction) 
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
fec <fec>
no fec
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| fec | FEC (forward error correction) mode  | Select [FC RS off ]  |
## fec 
### Description 
```
Configure FEC (forward error correction) 
```
### Parent Commands (Modes) 
```
interface range iface_range_num
```
### Syntax 
```
fec <fec>
no fec
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| fec | FEC (forward error correction) mode  | Select [FC RS off ]  |
## filter-list 
### Description 
```
Establish BGP filters 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
filter-list <fname> { in | out }
no filter-list <fname> { in | out }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| fname | WORD  | String  |
## filter-list 
### Description 
```
Establish BGP filters 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
filter-list <fname> { in | out }
no filter-list <fname> { in | out }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| fname | WORD  | String  |
## filter-list 
### Description 
```
This command configures a filter list for a BGP neighbor
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
filter-list <fname> { in | out }
no filter-list <fname> { in | out }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| fname | WORD  | String  |
### Usage Guidelines 
```
Use this command to define policy (route filtering) for a BGP neighbor
in outbound or/and inbound direction.
```
### Examples 
#### Following command configures a filter-list          fl_allow_remote in inbound direction for neighbor 20.20.20.2 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 20.20.20.2
sonic(config-router-bgp-neighbor)# remote-as 300
sonic(config-router-bgp-neighbor)# address-family ipv4 unicast
sonic(config-router-bgp-neighbor-af)# filter-list fl_allow_remote in
```
## filter-list 
### Description 
```
This command configures a filter list for BGP peer-group
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
filter-list <fname> { in | out }
no filter-list <fname> { in | out }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| fname | WORD  | String  |
### Usage Guidelines 
```
Use this command to define policy (route filtering) for a BGP peer-group
in outbound or/and inbound direction.
```
### Examples 
#### Following command configures a filter-list          fl_allow_remote in inbound direction for peer-group PG_Ext 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# address-family ipv4 unicast
sonic(config-router-bgp-pg-af)# filter-list fl_allow_remote in
```
## flow-group 
### Description 
```
This command configures flow group by specifying a set of match criteria that defines a set of flows that are of interest.
```
### Parent Commands (Modes) 
```
tam
```
### Syntax 
```
flow-group <name> [ src-ip <src_ip> ] [ dst-ip <dst_ip> ] [ l4-src-port <l4_src_port> ] [ l4-dst-port <l4_dst_port> ] [ priority <priority_value> ] [ protocol <protocol_value> ]
no flow-group <name>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| name | WORD  | String  |
| src_ip | A.B.C.D/mask or A::B/mask  | String  |
| dst_ip | A.B.C.D/mask or A::B/mask  | String  |
| l4_src_port |   | Integer  |
| l4_dst_port |   | Integer  |
| priority_value |   | Integer  |
| protocol_value | Transport Protocol Method  | Select [UDP(IP_UDP) TCP(IP_TCP) ]  |
### Usage Guidelines 
```
This command configures flow group by specifying a set of match criteria that defines a set of flows that are of interest.
```
### Examples 
#### flow-group <name> [src-ip <src_ip>] [dst-ip <dst_ip>] [src-l4-port <src_l4_port>] [dst-l4-port <dst_l4_port>] [protocol <protocol>] [priority <priority_value>] 
```
sonic(config-tam)# flow-group f9 type ipv4 src-ip 192.1.2.3 dst-ip 172.6.5.4
sonic# show tam flowgroups
Flow Group Name    : f9
   Id              : 60
   Priority        : 100
   SRC IP          : 192.1.2.3/32
   DST IP          : 172.6.5.4/32
Packet Count       : 5432
Flow Group Name    : DEMO
   Id              : 1
   Priority        : 100
   SRC IP          : 1.1.1.1/32
   DST IP          : 4.4.4.4/32
Packet Count       : 454
sonic#
```
## flow-group 
### Description 
```
This command attaches an existing flow group to an interface.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
flow-group <name>
no flow-group <name>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| name | WORD  | String  |
### Usage Guidelines 
```
This command attaches an existing flow group to an interface.
```
### Examples 
#### flow-group <name> 
```
sonic# configure terminal
sonic(config)# interface Ethernet 46
sonic(conf-if-Ethernet46)# flow-group f1
sonic(conf-if-Ethernet46)# end
sonic# show tam flowgroups
Flow Group Name    : DEMO
   Id              : 1
   Priority        : 100
   SRC MAC         : 11:22:33:44:55:66
   SRC IP          : 1.1.1.1/32
   DST IP          : 4.4.4.4/32
Packet Count       :
Flow Group Name    : f88
   Id              : 409
   Priority        : 100
   SRC IP          : 1.1.1.1/32
   DST IP          : 5.5.5.5/32
Packet Count       :
Flow Group Name    : f1
   Id              : 717
   Priority        : 100
   SRC MAC         : 00:00:00:00:00:01
   DST MAC         : 00:00:00:00:00:02
   SRC IP          : 3.3.3.3/32
   DST IP          : 4.4.4.4/32
   Ingress Intf    : Ethernet46
Packet Count       :
sonic#
```
## frequency 
### Description 
```
Configure frequency of probe for an IP SLA instance
```
### Parent Commands (Modes) 
```
ip sla <sla-id>
```
### Syntax 
```
frequency <freq-value>
no frequency
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| freq-value |   | Integer  |
### Examples 
#### Configure frequency of probe for an IP SLA instance 
```
sonic(config)# ip sla 10
sonic(conf-ipsla-10)# frequency 45
```
## graceful-restart enable 
### Description 
```
This command enables Graceful Restart for an instance of BGP
```
### Parent Commands (Modes) 
```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }
```
### Syntax 
```
graceful-restart enable
no graceful-restart enable
```
### Usage Guidelines 
```
Use this command to enable BGP Graceful Restart globally in an instance
of BGP. Changing the Graceful restart parameter will talke effect only
on the fly will not take effect immediately. It will require all the
BGP neighbors to be reset to take effect. This is because Graceful Restart
capability must be negotiated with neighbors to make this feature
functional.
```
### Examples 
#### Below command enables Graceful Restart for BGP instance on          default-VRF 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# graceful-restart enable
```
## graceful-restart preserve-fw-state 
### Description 
```
This command instructs BGP to preserve forwarding state during Graceful
Restart for an instance of BGP
```
### Parent Commands (Modes) 
```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }
```
### Syntax 
```
graceful-restart preserve-fw-state
no graceful-restart preserve-fw-state
```
### Usage Guidelines 
```
Use this command to enable BGP to preserve forwarding state of BGP
during Graceful Restart.
```
### Examples 
#### Below command enables preservation of forwarding state          during Graceful Restart for BGP instance on default-VRF 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# graceful-restart preserve-fw-state
```
## graceful-restart restart-time 
### Description 
```
This command configures restart timer interval for BGP
```
### Parent Commands (Modes) 
```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }
```
### Syntax 
```
graceful-restart restart-time <restart-time>
no graceful-restart restart-time
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| restart-time |   | Integer  |
### Usage Guidelines 
```
Use this command to configure BGP restart timer interval in seconds.
This is optional parameter and determines how long peer routers
will wait to delete stale routes before a BGP open message is received.
The default value is 120 seconds.
```
### Examples 
#### Below command configures restart timer interval to          180 seconds for BGP instance on default-VRF 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# graceful-restart restart-time 180
```
## graceful-restart stalepath-time 
### Description 
```
This command configures stale path timer interval for BGP
```
### Parent Commands (Modes) 
```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }
```
### Syntax 
```
graceful-restart stalepath-time <stalepath-time>
no graceful-restart stalepath-time
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| stalepath-time |   | Integer  |
### Usage Guidelines 
```
This command is used to set the maximum time to hold on to
the stale paths of a gracefully restarted peer. All stale
paths are deleted after the expiration of this timer. This
is an optional parameter. The default is 360 seconds
```
### Examples 
#### Below command configures stale path timer interval to          300 seconds for BGP instance on default-VRF 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# graceful-restart stalepath-time 300
```
## graceful-shutdown 
### Description 
```
This command enables Graceful shutdown feature
```
### Parent Commands (Modes) 
```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }
```
### Syntax 
```
graceful-shutdown
no graceful-shutdown
```
### Usage Guidelines 
```
Use this command to gracefully remove a BGP router from service. This
command will instruct BGP to enter into graceful shutdown mode by
resending routes with GSHUT community to all it's neighbors. This will
enable all it's neighbor to route traffic around it so that router can
be taken out of service without impact data forwarding
```
### Examples 
#### Below command disables eBGP connected route check 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# graceful-shutdown
```
## graceful-shutdown 
### Description 
```
Enable graceful shutdown for the portchannel 
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
graceful-shutdown
no graceful-shutdown
```
## graceful-shutdown 
### Description 
```
Enable graceful shutdown for the portchannels 
```
### Parent Commands (Modes) 
```
interface range create po_range_num { [ mode <PoMode> ] } { [ min-links <min-links-value> ] } [ fallback ] [ fast_rate ]
interface range po_range_num
```
### Syntax 
```
graceful-shutdown
no graceful-shutdown
```
## green 

### Description 

```
This command to configure WRED minimum, maximum thresholds
and drop probability for color green.


```
### Parent Commands (Modes) 

```
qos wred-policy <name>

```
### Syntax 

```
green minimum-threshold { <min-threshold> { maximum-threshold <max-threshold> { drop-probability <max-drop-rate> } } }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| min-threshold |   | Integer  |
| max-threshold |   | Integer  |
| max-drop-rate |   | Integer  |


### Usage Guidelines 

```
Use this command to configure WRED minimum, maximum and drop probability for green color packets.


```
### Examples 
#### The following command to configure WRED thresholds for green color           packets 

```
sonic(conf-wred-wred-green)# random-detect color green minimum-threshold 100 maximum-threshold 200 drop-probability 50


```
## hardware 

### Description 

```
Configure ASIC parameters 


```
### Parent Commands (Modes) 

```
configure terminal

```
### Syntax 

```
hardware

```
## hostname 
### Description 
```
Configures hostname of the switch.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
hostname <hostname-val>
no hostname
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| hostname-val | WORD  | String  |
### Usage Guidelines 
```
sonic-cli(config)# hostname <host-name>
```
### Examples 
#### Set hostname to R1 
```
sonic-cli(config)# hostname R1
sonic-cli(config)#
```
## icmp-echo 
### Description 
```
Configure operation type as ICMP and destination IP for an IP SLA instance
```
### Parent Commands (Modes) 
```
ip sla <sla-id>
```
### Syntax 
```
icmp-echo <addr>
no icmp-echo
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| addr | String  | String  |
### Examples 
#### Configure operation type ICMP from an IP SLA instance 
```
sonic(config)# ip sla 10
sonic(conf-ipsla-10)# icmp-echo 30.30.1.2
```
## ifa 

### Description 

```
Configures inband flow analyzer feature 


```
### Parent Commands (Modes) 

```
tam

```
### Syntax 

```
ifa

```
## image install 

### Description 

```
Install image 


```
### Syntax 

```
image install <img-path>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| img-path | String  | String  |


## image remove 

### Description 

```
Remove an image  


```
### Syntax 

```
image remove { all | <image> }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| image | String  | String  |


## image set-default 

### Description 

```
Set default boot image 


```
### Syntax 

```
image set-default <img-name>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| img-name | String  | String  |


## import vrf 
### Description 
```
Import routes from another VRF 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
import vrf { { route-map <route-map-name> } | <import-vrf-name> }
no import vrf { { [ route-map <route-map-name> ] } | <import-vrf-name> }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| route-map-name | WORD  | String  |
| import-vrf-name | WORD  | String  |
## import vrf 
### Description 
```
This command imports all routes or selective routes based on route-map from other VRF.
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
import vrf { { route-map <route-map-name> } | <import-vrf-name> }
no import vrf { { route-map <route-map-name> } | <import-vrf-name> }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| route-map-name | WORD  | String  |
| import-vrf-name | WORD  | String  |
### Usage Guidelines 
```
Use this command to import routes from other VRF.
```
### Examples 
#### This configuration example imports the routes from other VRF under ipv4 address-family 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# address-family ipv4 unicast
sonic(config-router-bgp-af)# import vrf Vrf1
                or
sonic(config-router-bgp-af)# import vrf route-map map1
```
## interface 
### Description 
```
Configure interfaces 
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
interface { <phy-if-name> | <vlan-if-name> }
no interface { <vlan_range_num> | <po_range_num> }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| phy-if-name | EthernetNUM  |   |
| vlan-if-name | VlanNUM  |   |
## interface CPU 

### Description 

```
CPU Interface commands 


```
### Parent Commands (Modes) 

```
configure terminal

```
### Syntax 

```
interface CPU

```
## interface Loopback 
### Description 
```
Loopback interface configuration 
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
interface Loopback <lo-id>
no interface Loopback <lo-id>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| lo-id |   | Integer  |
## interface Management 

### Description 

```
Management interface commands 


```
### Parent Commands (Modes) 

```
configure terminal

```
### Syntax 

```
interface Management <mgmt-if-id>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| mgmt-if-id |   | Integer  |


## interface PortChannel 

### Description 

```
PortChannel interface configuration 


```
### Parent Commands (Modes) 

```
configure terminal

```
### Syntax 

```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| lag-id |   | Integer  |
| PoMode | PortChannel Mode  | Select [active on ]  |
| min-links-value |   | Integer  |


## interface breakout 
### Description 
```
Breakout a port
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
interface breakout port <slotport> mode <breakout_mode>
no interface breakout port <slotport>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| slotport | Front-panel port (slot/port)  |   |
| breakout_mode | port breakout mode  | Select [1x100G 1x40G 2x100G 2x50G 4x100G 4x25G 4x10G 1x400G 2x200G 4x50G ]  |
### Usage Guidelines 
```
Use this command breakout a port or undo a breakout configuration.
  interface breakout port front-panel-port mode breakout-mode
  no interface breakout port front-panel-port
```
### Examples 
```
sonic# configure terminal
sonic(config)# interface breakout port 1/1 mode 4x25
Dynamic Port Breakout in-progress, use 'show interface breakout port 1/1' to check status.
sonic(config)# no interface breakout port 1/1
Dynamic Port Breakout in-progress, use 'show interface breakout port 1/1' to check status.
```
## interface range 

### Description 

```
Config interface range 


```
### Parent Commands (Modes) 

```
configure terminal

```
### Syntax 

```
interface range { iface_range_num | vlan_range_num | po_range_num | { create { vlan_range_num | { po_range_num { [ mode <PoMode> ] } { [ min-links <min-links-value> ] } [ fallback ] [ fast_rate ] } } } }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| PoMode | PortChannel Mode  | Select [active on ]  |
| min-links-value |   | Integer  |


## interface vxlan 
### Description 
```
Command to enter VXLAN configuration mode.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
interface vxlan <vxlan-if-name>
no interface vxlan <vxlan-if-name>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| vxlan-if-name |   | String  |
### Usage Guidelines 
```
(config)# interface vxlan VTEPNAME
VTEPNAME - string prefixed with 'vtep' with max size of 10 chars.
```
### Examples 
#### configuration mode for vxlan 
```
sonic(config)# interface vxlan vtep1
sonic(config-if-vtep1)#
```
## interface-naming 
### Description 
```
Interface naming 
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
interface-naming standard
no interface-naming standard
```
## ip access-group 
### Description 
```
Configure Internet Protocol v4 (IPv4) ACL 
```
### Parent Commands (Modes) 
```
line vty
```
### Syntax 
```
ip access-group <access-list-name> in
no ip access-group <access-list-name> in
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| access-list-name | WORD  | String  |
## ip access-group 
### Description 
```
Apply IPv4 ACL to an interface.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
ip access-group <access-list-name> { in | out }
no ip access-group <access-list-name> { in | out }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| access-list-name | WORD  | String  |
### Usage Guidelines 
```
ACL must be created first and must be of type IPv4 to be applied. Only 1 ACL of a given type can be applied per interface and per direction.
```
### Examples 
#### Apply IPv4 ACL to an interface 
```
sonic(conf-if-po1)# ip access-group ipacl-example out
```
## ip access-group 
### Description 
```
Apply IPv4 ACL to an interface.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
ip access-group <access-list-name> { in | out }
no ip access-group <access-list-name> { in | out }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| access-list-name | WORD  | String  |
### Usage Guidelines 
```
ACL must be created first and must be of type IPv4 to be applied. Only 1 ACL of a given type can be applied per interface and per direction.
```
### Examples 
#### Apply IPv4 ACL to an interface 
```
sonic(conf-if-po1)# ip access-group ipacl-example out
```
## ip access-group 
### Description 
```
Apply IPv4 ACL to an interface.
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ip access-group <access-list-name> { in | out }
no ip access-group <access-list-name> { in | out }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| access-list-name | WORD  | String  |
### Usage Guidelines 
```
ACL must be created first and must be of type IPv4 to be applied. Only 1 ACL of a given type can be applied per interface and per direction.
```
### Examples 
#### Apply IPv4 ACL to an interface 
```
sonic(conf-if-po1)# ip access-group ipacl-example out
```
## ip access-group 
### Description 
```
Apply IPv4 ACL globally
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
ip access-group <access-list-name> { in | out }
no ip access-group <access-list-name> { in | out }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| access-list-name | WORD  | String  |
### Usage Guidelines 
```
ACL must be created first and must be of type IPv4 to be applied. Only 1 ACL of a given type can be applied globally per direction.
```
### Examples 
#### Apply IPv4 ACL globally 
```
sonic(config)# ip access-group ipacl-example in
```
## ip access-list 
### Description 
```
Create IPv4 ACL
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
ip access-list <access-list-name>
no ip access-list <access-list-name>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| access-list-name | WORD  | String  |
### Usage Guidelines 
```
ACL name can be of maximum 63 characters. The name must begin with A-Z, a-z or 0-9. Underscore and hypens can be used except as the first character. ACL name must be unique across all ACL types.
```
### Examples 
#### Create IPv4 ACL 
```
sonic(config)# ip access-list ipacl-example
```
## ip address 
### Description 
```
IP address 
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
ip address <addr> { [ secondary ] ] }
no ip address [ <addr> { [ secondary ] ] } ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| addr | A.B.C.D/mask  | String  |
## ip address 
### Description 
```
IP address 
```
### Parent Commands (Modes) 
```
interface Management <mgmt-if-id>
```
### Syntax 
```
ip address <addr> { { [ gwaddr <gw_addr> ] } | [ secondary ] ] }
no ip address [ <addr> { [ secondary ] ] } ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| addr | A.B.C.D/mask  | String  |
| gw_addr | A.B.C.D  | String  |
## ip address 
### Description 
```
IP address 
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ip address <addr> { [ secondary ] ] }
no ip address [ <addr> { [ secondary ] ] } ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| addr | A.B.C.D/mask  | String  |
## ip address 
### Description 
```
IP address 
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
ip address <addr> { [ secondary ] ] }
no ip address [ <addr> { [ secondary ] ] } ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| addr | A.B.C.D/mask  | String  |
## ip address 
### Description 
```
IP address 
```
### Parent Commands (Modes) 
```
interface Loopback <lo-id>
```
### Syntax 
```
ip address <addr> { [ secondary ] ] }
no ip address [ <addr> { [ secondary ] ] } ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| addr | A.B.C.D/mask  | String  |
## ip anycast-address 
### Description 
```
Configures IPv4 Static Anycast Gateway Address for an Interface.
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ip anycast-address <anycast-addr>
no ip anycast-address <anycast-addr>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| anycast-addr | A.B.C.D/mask  | String  |
### Examples 
#### Configures IPv4 Static Anycast Gateway Address for an Interface 
```
sonic(conf-if-Vlan5)# ip anycast-address 50.0.0.1/24
```
## ip anycast-address 

### Description 

```
Enable/Disable IPv4 Static Anycast Gateway functionality. By default it is enabled.


```
### Parent Commands (Modes) 

```
configure terminal

```
### Syntax 

```
ip anycast-address { enable | disable }

```
### Examples 
#### Enable IPv4 Static Anycast Gateway 

```
sonic(config)# ip anycast-address enable


```
#### Disable IPv4 Static Anycast Gateway 

```
sonic(config)# ip anycast-address disable


```
## ip anycast-mac-address 
### Description 
```
Configures MAC address for all the Static Anycast Gateway Addresses on the system.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
ip anycast-mac-address <anycast-mac>
no ip anycast-mac-address <anycast-mac>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| anycast-mac | nn:nn:nn:nn:nn:nn  | String  |
### Examples 
#### Configures MAC address for all the Static Anycast Gateway Addresses on the system 
```
sonic(conf-if-Vlan5)# ip anycast-mac-address 00:22:33:44:55:66
```
## ip arp 
### Description 
```
Configure static ARP.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
ip arp <static-ip> <neigh>
no ip arp <static-ip> <neigh>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| static-ip | A.B.C.D  | String  |
| neigh | nn:nn:nn:nn:nn:nn  | String  |
### Usage Guidelines 
```
Use this command to configure static ARP.
```
### Examples 
#### Configure static ARP 
```
sonic-cli(config)# interface Ethernet 0
sonic-cli(conf-if-Ethernet0)# arp ip_addr mac
```
## ip arp 
### Description 
```
Configure static ARP.
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ip arp <static-ip> <neigh>
no ip arp <static-ip> <neigh>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| static-ip | A.B.C.D  | String  |
| neigh | nn:nn:nn:nn:nn:nn  | String  |
### Usage Guidelines 
```
Use this command to configure static ARP.
```
### Examples 
#### Configure static ARP 
```
sonic-cli(config)# interface Ethernet 0
sonic-cli(conf-if-Ethernet0)# arp ip_addr mac
```
## ip arp 
### Description 
```
Configure static ARP.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
ip arp <static-ip> <neigh>
no ip arp <static-ip> <neigh>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| static-ip | A.B.C.D  | String  |
| neigh | nn:nn:nn:nn:nn:nn  | String  |
### Usage Guidelines 
```
Use this command to configure static ARP.
```
### Examples 
#### Configure static ARP 
```
sonic-cli(config)# interface Ethernet 0
sonic-cli(conf-if-Ethernet0)# arp ip_addr mac
```
## ip arp 
### Description 
```
Configures time in seconds for ARP cache entry to timeout.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
ip arp timeout <value>
no ip arp timeout
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| value | value in seconds  | Integer  |
### Usage Guidelines 
```
Use this command to configure IPv4 ARP cache entry timeout.
```
### Examples 
#### Configure IPv4 ARP timeout 
```
sonic-cli(config)# ip arp timeout 200
```
## ip dhcp-relay 
### Description 
```
Configures DHCPv4 relay on an interface.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
ip dhcp-relay <ipaddr1> { { [ vrf-name <vrfName> ] } { [ <ipaddr2> { [ <ipaddr3> [ <ipaddr4> ] ] } ] } }
no ip dhcp-relay [ <ipaddr1> { [ <ipaddr2> { [ <ipaddr3> [ <ipaddr4> ] ] } ] } ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| ipaddr1 | A.B.C.D  | String  |
| vrfName | WORD  | String  |
| ipaddr2 | A.B.C.D  | String  |
| ipaddr3 | A.B.C.D  | String  |
| ipaddr4 | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to configure a DHCPv4 relay on an interface.
```
### Examples 
#### Configure ip DHCPv4 relay 
```
sonic-cli(config)# interface Ethernet 12
sonic-cli(conf-if-Ethernet0)# ip dhcp-relay 9.0.0.9
```
## ip dhcp-relay 
### Description 
```
Configures DHCPv4 relay on an interface.
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ip dhcp-relay <ipaddr1> { { [ vrf-name <vrfName> ] } { [ <ipaddr2> { [ <ipaddr3> [ <ipaddr4> ] ] } ] } }
no ip dhcp-relay [ <ipaddr1> { [ <ipaddr2> { [ <ipaddr3> [ <ipaddr4> ] ] } ] } ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| ipaddr1 | A.B.C.D  | String  |
| vrfName | WORD  | String  |
| ipaddr2 | A.B.C.D  | String  |
| ipaddr3 | A.B.C.D  | String  |
| ipaddr4 | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to configure a DHCPv4 relay on an interface.
```
### Examples 
#### Configure ip DHCPv4 relay 
```
sonic-cli(config)# interface Ethernet 12
sonic-cli(conf-if-Ethernet0)# ip dhcp-relay 9.0.0.9
```
## ip dhcp-relay 
### Description 
```
Configures DHCPv4 relay on an interface.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
ip dhcp-relay <ipaddr1> { { [ vrf-name <vrfName> ] } { [ <ipaddr2> { [ <ipaddr3> [ <ipaddr4> ] ] } ] } }
no ip dhcp-relay [ <ipaddr1> { [ <ipaddr2> { [ <ipaddr3> [ <ipaddr4> ] ] } ] } ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| ipaddr1 | A.B.C.D  | String  |
| vrfName | WORD  | String  |
| ipaddr2 | A.B.C.D  | String  |
| ipaddr3 | A.B.C.D  | String  |
| ipaddr4 | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to configure a DHCPv4 relay on an interface.
```
### Examples 
#### Configure ip DHCPv4 relay 
```
sonic-cli(config)# interface Ethernet 12
sonic-cli(conf-if-Ethernet0)# ip dhcp-relay 9.0.0.9
```
## ip dhcp-relay link-select 
### Description 
```
Configure the link-selection suboption on an interface.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
ip dhcp-relay link-select
no ip dhcp-relay link-select
```
### Usage Guidelines 
```
Use this command to configure the link-selection suboption on an interface.
```
### Examples 
#### Configure dhcp relay link selection 
```
sonic-cli(config)# interface Ethernet 12
sonic-cli(conf-if-Ethernet0)# ip dhcp-relay link-select
```
## ip dhcp-relay link-select 
### Description 
```
Configure the link-selection suboption on an interface.
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ip dhcp-relay link-select
no ip dhcp-relay link-select
```
### Usage Guidelines 
```
Use this command to configure the link-selection suboption on an interface.
```
### Examples 
#### Configure dhcp relay link selection 
```
sonic-cli(config)# interface Ethernet 12
sonic-cli(conf-if-Ethernet0)# ip dhcp-relay link-select
```
## ip dhcp-relay link-select 
### Description 
```
Configure the link-selection suboption on an interface.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
ip dhcp-relay link-select
no ip dhcp-relay link-select
```
### Usage Guidelines 
```
Use this command to configure the link-selection suboption on an interface.
```
### Examples 
#### Configure dhcp relay link selection 
```
sonic-cli(config)# interface Ethernet 12
sonic-cli(conf-if-Ethernet0)# ip dhcp-relay link-select
```
## ip dhcp-relay max-hop-count 
### Description 
```
Configures the maximum hop count for the DHCP packet.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
ip dhcp-relay max-hop-count <hop-count>
no ip dhcp-relay max-hop-count
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| hop-count |   | Integer  |
### Usage Guidelines 
```
Use this command to configure the maximum hop count for the DHCP packet.
```
### Examples 
#### Configure dhcp relay max hop count 
```
sonic-cli(config)# interface Ethernet 12
sonic-cli(conf-if-Ethernet0)# ip dhcp-relay max-hop-count 9
```
## ip dhcp-relay max-hop-count 
### Description 
```
Configures the maximum hop count for the DHCP packet.
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ip dhcp-relay max-hop-count <hop-count>
no ip dhcp-relay max-hop-count
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| hop-count |   | Integer  |
### Usage Guidelines 
```
Use this command to configure the maximum hop count for the DHCP packet.
```
### Examples 
#### Configure dhcp relay max hop count 
```
sonic-cli(config)# interface Ethernet 12
sonic-cli(conf-if-Ethernet0)# ip dhcp-relay max-hop-count 9
```
## ip dhcp-relay max-hop-count 
### Description 
```
Configures the maximum hop count for the DHCP packet.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
ip dhcp-relay max-hop-count <hop-count>
no ip dhcp-relay max-hop-count
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| hop-count |   | Integer  |
### Usage Guidelines 
```
Use this command to configure the maximum hop count for the DHCP packet.
```
### Examples 
#### Configure dhcp relay max hop count 
```
sonic-cli(config)# interface Ethernet 12
sonic-cli(conf-if-Ethernet0)# ip dhcp-relay max-hop-count 9
```
## ip dhcp-relay policy-action 
### Description 
```
Configure the policy for handling of DHCPv4 relay options.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
ip dhcp-relay policy-action <policyAction>
no ip dhcp-relay policy-action
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| policyAction | String(Max: 8 characters)  | String  |
### Usage Guidelines 
```
Use this command to configure the policy for handling of DHCPv4 relay options.
```
### Examples 
#### Configure dhcp relay policy action 
```
sonic-cli(config)# interface Ethernet 12
sonic-cli(conf-if-Ethernet0)# ip dhcp-relay policy-action REPLACE
```
## ip dhcp-relay policy-action 
### Description 
```
Configure the policy for handling of DHCPv4 relay options.
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ip dhcp-relay policy-action <policyAction>
no ip dhcp-relay policy-action
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| policyAction | String(Max: 8 characters)  | String  |
### Usage Guidelines 
```
Use this command to configure the policy for handling of DHCPv4 relay options.
```
### Examples 
#### Configure dhcp relay policy action 
```
sonic-cli(config)# interface Ethernet 12
sonic-cli(conf-if-Ethernet0)# ip dhcp-relay policy-action REPLACE
```
## ip dhcp-relay policy-action 
### Description 
```
Configure the policy for handling of DHCPv4 relay options.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
ip dhcp-relay policy-action <policyAction>
no ip dhcp-relay policy-action
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| policyAction | String(Max: 8 characters)  | String  |
### Usage Guidelines 
```
Use this command to configure the policy for handling of DHCPv4 relay options.
```
### Examples 
#### Configure dhcp relay policy action 
```
sonic-cli(config)# interface Ethernet 12
sonic-cli(conf-if-Ethernet0)# ip dhcp-relay policy-action REPLACE
```
## ip dhcp-relay source-interface 
### Description 
```
Configures the source IP address to be used for relaying the DHCP packets.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
ip dhcp-relay source-interface <ifName>
no ip dhcp-relay source-interface
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| ifName | String  | String  |
### Usage Guidelines 
```
Use this command to configure the source-interface.
```
### Examples 
#### Configure dhcp relay source interface 
```
sonic-cli(config)# interface Ethernet 12
sonic-cli(conf-if-Ethernet0)# ip dhcp-relay source-interface Ethernet36
```
## ip dhcp-relay source-interface 
### Description 
```
Configures the source IP address to be used for relaying the DHCP packets.
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ip dhcp-relay source-interface <ifName>
no ip dhcp-relay source-interface
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| ifName | String  | String  |
### Usage Guidelines 
```
Use this command to configure the source-interface.
```
### Examples 
#### Configure dhcp relay source interface 
```
sonic-cli(config)# interface Ethernet 12
sonic-cli(conf-if-Ethernet0)# ip dhcp-relay source-interface Ethernet36
```
## ip dhcp-relay source-interface 
### Description 
```
Configures the source IP address to be used for relaying the DHCP packets.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
ip dhcp-relay source-interface <ifName>
no ip dhcp-relay source-interface
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| ifName | String  | String  |
### Usage Guidelines 
```
Use this command to configure the source-interface.
```
### Examples 
#### Configure dhcp relay source interface 
```
sonic-cli(config)# interface Ethernet 12
sonic-cli(conf-if-Ethernet0)# ip dhcp-relay source-interface Ethernet36
```
## ip dhcp-relay vrf-select 
### Description 
```
Configure the VRF selection option.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
ip dhcp-relay vrf-select
no ip dhcp-relay vrf-select
```
### Usage Guidelines 
```
Use this command to configure the VRF selection option.
```
### Examples 
#### Configure dhcp relay vrf selection 
```
sonic-cli(config)# interface Ethernet 12
sonic-cli(conf-if-Ethernet0)# ip dhcp-relay vrf-select
```
## ip dhcp-relay vrf-select 
### Description 
```
Configure the VRF selection option.
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ip dhcp-relay vrf-select
no ip dhcp-relay vrf-select
```
### Usage Guidelines 
```
Use this command to configure the VRF selection option.
```
### Examples 
#### Configure dhcp relay vrf selection 
```
sonic-cli(config)# interface Ethernet 12
sonic-cli(conf-if-Ethernet0)# ip dhcp-relay vrf-select
```
## ip dhcp-relay vrf-select 
### Description 
```
Configure the VRF selection option.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
ip dhcp-relay vrf-select
no ip dhcp-relay vrf-select
```
### Usage Guidelines 
```
Use this command to configure the VRF selection option.
```
### Examples 
#### Configure dhcp relay vrf selection 
```
sonic-cli(config)# interface Ethernet 12
sonic-cli(conf-if-Ethernet0)# ip dhcp-relay vrf-select
```
## ip forward-protocol udp enable 
### Description 
```
Enables IP helper relay functionality for specified UDP packets
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
ip forward-protocol udp enable
no ip forward-protocol udp enable
```
### Usage Guidelines 
```
[no] ip forward-protocol udp enable
```
### Examples 
#### Enabling IP forwarding 
```
sonic-cli# configure terminal
sonic-cli(config)# ip forward-protocol udp enable
sonic-cli(config)#
```
#### Disabling IP forwarding 
```
sonic-cli# configure terminal
sonic-cli(config)# no ip forward-protocol udp enable
sonic-cli(config)#
```
### Features this CLI belongs to 
*  IP Helper
### Alternate command 
#### click 
```
config ip forward_protocol udp enable
config ip forward_protocol udp disable
```
## ip forward-protocol udp exclude 

### Description 

```
Exclude UDP Port from list of IP packets that have to be relayed


```
### Parent Commands (Modes) 

```
configure terminal

```
### Syntax 

```
ip forward-protocol udp exclude { <port> | <port> }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| port | Well known UDP ports  | Select [tftp(69) dns(53) ntp(37) netbios-name-server(137) netbios-datagram-server(138) tacacs(49) ]  |


### Usage Guidelines 

```
ip forward-protocol udp exclude { tftp|dns|ntp|netbios-name-server|netbios-datagram-server|tacacs|<custom-port> }


```
### Examples 
#### Exclude a custom UDP port 

```
sonic-cli# configure terminal
sonic-cli(config)# ip forward-protocol udp exclude 12200
sonic-cli(config)#


```
#### Exclude a well known supported-by-default port 

```
sonic-cli# configure terminal
sonic-cli(config)# ip forward-protocol udp exclude tftp
sonic-cli(config)#


```
### Features this CLI belongs to 
*  IP Helper


### Alternate command 
#### click 

```
config ip forward_protocol udp remove {[tftp/dns/ntp/netbios-name-server/netbios-datagram-server/tacacs] | <port> }


```
## ip forward-protocol udp include 

### Description 

```
Include UDP Port for which IP packets have to be relayed


```
### Parent Commands (Modes) 

```
configure terminal

```
### Syntax 

```
ip forward-protocol udp include { <port> | <port> }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| port | Well known UDP ports  | Select [tftp(69) dns(53) ntp(37) netbios-name-server(137) netbios-datagram-server(138) tacacs(49) ]  |


### Usage Guidelines 

```
ip forward-protocol udp include { tftp|dns|ntp|netbios-name-server|netbios-datagram-server|tacacs|<custom-port> }


```
### Examples 
#### Include a custom UDP port 

```
sonic-cli# configure terminal
sonic-cli(config)# ip forward-protocol udp include 12200
sonic-cli(config)#


```
#### Include a well known supported-by-default port 

```
sonic-cli# configure terminal
sonic-cli(config)# ip forward-protocol udp include tftp
sonic-cli(config)#


```
### Features this CLI belongs to 
*  IP Helper


### Alternate command 
#### click 

```
config ip forward_protocol udp add {[tftp/dns/ntp/netbios-name-server/netbios-datagram-server/tacacs] | <port> }


```
## ip forward-protocol udp rate-limit 
### Description 
```
Rate limit CPU bound packets
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
ip forward-protocol udp rate-limit <rate>
no ip forward-protocol udp rate-limit
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| rate | 600 to 10,000 pps (default 600 pps)  | Integer  |
### Usage Guidelines 
```
[no] ip forward-protocol udp rate-limit <rate>
```
### Examples 
#### Configure rate limit 
```
sonic-cli# configure terminal
sonic-cli(config)# ip forward-protocol udp rate-limit 1000
sonic-cli(config)#
```
#### Unconfigure rate limit 
```
sonic-cli# configure terminal
sonic-cli(config)# no ip forward-protocol udp rate-limit
sonic-cli(config)#
```
### Features this CLI belongs to 
*  IP Helper
### Alternate command 
#### click 
```
config ip forward_protocol udp rate_limit <value-in-pps>
```
## ip helper-address 
### Description 
```
Configures IP helper server address for an interface.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
ip helper-address <addr>
no ip helper-address <addr>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| addr | A.B.C.D  | String  |
### Usage Guidelines 
```
[no] ip helper-address <ip-address>
```
### Examples 
#### Configure IP helper server address 
```
sonic-cli# configure terminal
sonic-cli(config)# interface Ethernet0
sonic-cli(conf-if-Ethernet0)# ip helper-address 3.3.3.3
sonic-cli(conf-if-Ethernet0)#
```
#### Unconfigure IP Helper server address 
```
sonic-cli# configure terminal
sonic-cli(config)# interface Ethernet0
sonic-cli(conf-if-Ethernet0)# no ip helper-address 3.3.3.3
sonic-cli(conf-if-Ethernet0)#
```
### Features this CLI belongs to 
*  IP Helper
### Alternate command 
#### click 
```
config interface ip helper_address add <interface-name> <ip-address>
```
## ip helper-address 
### Description 
```
Configures IP helper server address for an interface.
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ip helper-address <addr>
no ip helper-address <addr>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| addr | A.B.C.D  | String  |
### Usage Guidelines 
```
[no] ip helper-address <ip-address>
```
### Examples 
#### Configure IP helper server address 
```
sonic-cli# configure terminal
sonic-cli(config)# interface Vlan100
sonic-cli(conf-if-Vlan100)# ip helper-address 3.3.3.3
sonic-cli(conf-if-Vlan100)#
```
#### Unconfigure IP Helper server address 
```
sonic-cli# configure terminal
sonic-cli(config)# interface Vlan100
sonic-cli(conf-if-Vlan100)# no ip helper-address 3.3.3.3
sonic-cli(conf-if-Vlan100)#
```
### Features this CLI belongs to 
*  IP Helper
### Alternate command 
#### click 
```
config interface ip helper_address add <interface-name> <ip-address>
```
## ip helper-address 
### Description 
```
Configures IP helper server address for an interface.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
ip helper-address <addr>
no ip helper-address <addr>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| addr | A.B.C.D  | String  |
### Usage Guidelines 
```
[no] ip helper-address <ip-address>
```
### Examples 
#### Configure IP helper server address 
```
sonic-cli# configure terminal
sonic-cli(config)# interface PortChannel100
sonic-cli(conf-if-PortChannel100)# ip helper-address 3.3.3.3
sonic-cli(conf-if-PortChannel100)#
```
#### Unconfigure IP Helper server address 
```
sonic-cli# configure terminal
sonic-cli(config)# interface PortChannel100
sonic-cli(conf-if-PortChannel100)# no ip helper-address 3.3.3.3
sonic-cli(conf-if-PortChannel100)#
```
### Features this CLI belongs to 
*  IP Helper
### Alternate command 
#### click 
```
config interface ip helper_address add <interface-name> <ip-address>
```
## ip helper-address vrf 
### Description 
```
Configures IP helper server address in non-default vrf for an interface.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
ip helper-address vrf <vrfname> <addr>
no ip helper-address vrf <vrfname> <addr>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrfname | VRF name (prefixed by Vrf, Max: 15 characters)  | String  |
| addr | A.B.C.D  | String  |
### Usage Guidelines 
```
[no] ip helper-address vrf <vrf-name> <ip-address>
```
### Examples 
#### Configure IP Helper server address 
```
sonic-cli# configure terminal
sonic-cli(config)# interface Ethernet0
sonic-cli(conf-if-Ethernet0)# ip helper-address vrf Vrf1 4.4.4.4
sonic-cli(conf-if-Ethernet0)#
```
#### Unconfigure IP Helper server address 
```
sonic-cli# configure terminal
sonic-cli(config)# interface Ethernet0
sonic-cli(conf-if-Ethernet0)# no ip helper-address vrf Vrf1 4.4.4.4
sonic-cli(conf-if-Ethernet0)#
```
### Features this CLI belongs to 
*  IP Helper
### Alternate command 
#### click 
```
config interface ip helper_address add <interface-name> <ip-address> [-vrf <vrf-name>]
```
## ip helper-address vrf 
### Description 
```
Configures IP helper server address in non-default vrf for an interface.
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ip helper-address vrf <vrfname> <addr>
no ip helper-address vrf <vrfname> <addr>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrfname | VRF name (prefixed by Vrf, Max: 15 characters)  | String  |
| addr | A.B.C.D  | String  |
### Usage Guidelines 
```
[no] ip helper-address vrf <vrf-name> <ip-address>
```
### Examples 
#### Configure IP Helper server address 
```
sonic-cli# configure terminal
sonic-cli(config)# interface Vlan100
sonic-cli(conf-if-Vlan100)# ip helper-address vrf Vrf1 4.4.4.4
sonic-cli(conf-if-Vlan100)#
```
#### Unconfigure IP Helper server address 
```
sonic-cli# configure terminal
sonic-cli(config)# interface Vlan100
sonic-cli(conf-if-Vlan100)# no ip helper-address vrf Vrf1 4.4.4.4
sonic-cli(conf-if-Vlan100)#
```
### Features this CLI belongs to 
*  IP Helper
### Alternate command 
#### click 
```
config interface ip helper_address add <interface-name> <ip-address> [-vrf <vrf-name>]
```
## ip helper-address vrf 
### Description 
```
Configures IP helper server address in non-default vrf for an interface.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
ip helper-address vrf <vrfname> <addr>
no ip helper-address vrf <vrfname> <addr>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrfname | VRF name (prefixed by Vrf, Max: 15 characters)  | String  |
| addr | A.B.C.D  | String  |
### Usage Guidelines 
```
[no] ip helper-address vrf <vrf-name> <ip-address>
```
### Examples 
#### Configure IP Helper server address 
```
sonic-cli# configure terminal
sonic-cli(config)# interface PortChannel100
sonic-cli(conf-if-PortChannel100)# ip helper-address vrf Vrf1 4.4.4.4
sonic-cli(conf-if-PortChannel100)#
```
#### Unconfigure IP Helper server address 
```
sonic-cli# configure terminal
sonic-cli(config)# interface PortChannel100
sonic-cli(conf-if-PortChannel100)# no ip helper-address vrf Vrf1 4.4.4.4
sonic-cli(conf-if-PortChannel100)#
```
### Features this CLI belongs to 
*  IP Helper
### Alternate command 
#### click 
```
config interface ip helper_address add <interface-name> <ip-address> [-vrf <vrf-name>]
```
## ip igmp 
### Description 
```
Enable IGMP operation 
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ip igmp
no ip igmp
```
## ip igmp snooping 
### Description 
```
Commands to configure or unconfigure IGMP snooping parameters on a VLAN
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ip igmp snooping { [ querier ] | [ fast-leave ] | { [ query-interval <query-interval-val> ] } | { [ last-member-query-interval <last-mem-query-interval-val> ] } | { [ query-max-response-time <query-max-response-val> ] } | { [ version <igmps-version-val> ] } | { [ mrouter { interface <mrouter-if-name> } ] } | { [ static-group { <group-addr> { interface <grp-if-name> } } ] } } ]
no ip igmp snooping { [ querier ] | [ fast-leave ] | [ query-interval ] | [ last-member-query-interval ] | [ query-max-response-time ] | [ version ] | { [ mrouter { interface <mrouter-if-name> } ] } | { [ static-group { <group-addr> { interface <grp-if-name> } } ] } } ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| query-interval-val | Query Interval value (Default: 125 s)  | Integer  |
| last-mem-query-interval-val | Last Member Query Interval value (Default: 1000 ms)  | Integer  |
| query-max-response-val | Query Reponse Time (Default: 10 s)  | Integer  |
| igmps-version-val | IGMPS version 1 or 2 or 3 (Default: 2)  | Integer  |
| mrouter-if-name | String  | String  |
| group-addr | A.B.C.D  | String  |
| grp-if-name | String  | String  |
### Examples 
#### Enable IGMP Snooping on VLAN 
```
sonic(config)# interface Vlan 200
sonic(conf-if-Vlan200)# ip igmp snooping
```
#### Disable IGMP Snooping on VLAN 
```
sonic(config)# interface Vlan 200
sonic(conf-if-Vlan200)# no ip igmp snooping
```
#### Enable IGMP querier on VLAN, by default querier is disabled 
```
sonic(config)# interface Vlan 200
sonic(conf-if-Vlan200)# ip igmp snooping querier
```
#### Disable IGMP querier on VLAN 
```
sonic(config)# interface Vlan 200
sonic(conf-if-Vlan200)# no ip igmp snooping querier
```
#### Enable IGMP fast-leave on VLAN, by default fast-leave is disabled 
```
sonic(config)# interface Vlan 200
sonic(conf-if-Vlan200)# ip igmp snooping fast-leave
```
#### Disable IGMP fast-leave on VLAN 
```
sonic(config)# interface Vlan 200
sonic(conf-if-Vlan200)# no ip igmp snooping fast-leave
```
#### Configure IGMP query-interval, default interval is 125 seconds, range is from 1 to 18000 seconds 
```
sonic(config)# interface Vlan 200
sonic(conf-if-Vlan200)# ip igmp snooping query-interval 20
```
#### Unconfigure non-default IGMP query-interval configured, value set to default interval 125 seconds 
```
sonic(config)# interface Vlan 200
sonic(conf-if-Vlan200)# no ip igmp snooping query-interval
```
#### Configure last member query interval, default is 1000ms, the valid range is from 100ms to 25500ms 
```
sonic(config)# interface Vlan 200
sonic(conf-if-Vlan200)# ip igmp snooping last-member-query-interval 2000
```
#### Unconfigure non-default last member query interval configured, value set to default 1s 
```
sonic(config)# interface Vlan 200
sonic(conf-if-Vlan200)# no ip igmp snooping last-member-query-interval
```
#### Configure max response interval, default is 10s, the valid range is from 1 to 25s 
```
sonic(config)# interface Vlan 200
sonic(conf-if-Vlan200)# ip igmp snooping query-max-response-time 12
```
#### Unconfigure non-default max response interval configured, value set to default 10s 
```
sonic(config)# interface Vlan 200
sonic(conf-if-Vlan200)# no ip igmp snooping query-max-response-time
```
#### Configure IGMP version, default is Version2, the valid range is from 1 to 3 
```
sonic(config)# interface Vlan 200
sonic(conf-if-Vlan200)# ip igmp snooping version 3
```
#### Unconfigure non-default IGMP version configured, value set to default Version2 
```
sonic(config)# interface Vlan 200
sonic(conf-if-Vlan200)# no ip igmp snooping version
```
#### Configure static multicast router(mrouter) port 
```
sonic(config)# interface Vlan 200
sonic(conf-if-Vlan200)# ip igmp snooping mrouter interface Ethernet4
```
#### Unconfigure static multicast router(mrouter) port 
```
sonic(config)# interface Vlan 200
sonic(conf-if-Vlan200)# no ip igmp snooping mrouter interface Ethernet4
```
#### Configure static multicast group 
```
sonic(config)# interface Vlan 200
sonic(conf-if-Vlan200)# ip igmp snooping static-group 225.0.0.1 interface PortChannel2
```
#### Unconfigure static multicast group 
```
sonic(config)# interface Vlan 200
sonic(conf-if-Vlan200)#no ip igmp snooping static-group 225.0.0.1 interface PortChannel2
```
## ip load-share hash ipv4 
### Description 
```
Configure IP ECMP hash
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
ip load-share hash ipv4 { ipv4-src-ip | ipv4-dst-ip | ipv4-ip-proto | ipv4-l4-src-port | ipv4-l4-dst-port }
no ip load-share hash ipv4 { ipv4-src-ip | ipv4-dst-ip | ipv4-ip-proto | ipv4-l4-src-port | ipv4-l4-dst-port }
```
### Usage Guidelines 
```
Use this command to configure IP ECMP load share hash
```
### Examples 
#### Configure ip ecmp load share hash 
```
sonic-cli(config)# ip load-share hash ipv4 ipv4-src-ip
```
## ip load-share hash ipv6 
### Description 
```
Configure IP ECMP hash
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
ip load-share hash ipv6 { ipv6-src-ip | ipv6-dst-ip | ipv6-next-hdr | ipv6-l4-src-port | ipv6-l4-dst-port }
no ip load-share hash ipv6 { ipv6-src-ip | ipv6-dst-ip | ipv6-next-hdr | ipv6-l4-src-port | ipv6-l4-dst-port }
```
### Usage Guidelines 
```
Use this command to configure IP ECMP load share hash
```
### Examples 
#### Configure ip ecmp load share hash 
```
sonic-cli(config)# ip load-share hash ipv6 ipv6-src-ip
```
## ip load-share hash seed 
### Description 
```
Configure IP ECMP hash seed
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
ip load-share hash seed <seed-val>
no ip load-share hash seed
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| seed-val | IP ECMP Load share seed value 0 to 16777215  | Integer  |
### Usage Guidelines 
```
Use this command to configure IP ECMP load share hash seed
```
### Examples 
#### Configure ip ecmp load share hash seed 
```
sonic-cli(config)# ip load-share hash seed 200
```
## ip name-server 
### Description 
```
Configure the name server address 
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
ip name-server <nameserver> { [ vrf { mgmt } ] }
no ip name-server <nameserver>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| nameserver | A.B.C.D/A::B  | String  |
## ip name-server source-interface 
### Description 
```
Configure source interface to pick the source IP, used for the DNS query 
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
ip name-server source-interface { Ethernet | Loopback | Management | PortChannel | Vlan }
no ip name-server source-interface
```
## ip ospf 

### Description 

```
Configures OSPFv2 parameters within an IPv4 interface.


```
### Parent Commands (Modes) 

```
interface <phy-if-name>

```
### Syntax 

```
ip ospf

```
### Usage Guidelines 

```
Use this command to configure OSPFv2 parameters under an IPv4 interface. IPv4 interface
can be Ethernet interface, VLAN interface, Portchannel interface or a Loopback interface.
Every OSPFv2 parameter on an interface can be associated with its specific IPv4 addresss
by explicitely specifying the IPv4 address after the parameter. Specifying interface IPv4
address is an optional.


```
### Examples 
#### Configures OSPFv2 parameters within an IPv4 interface 

```
sonic-cli(config-router-ospf)# no ip ospf interface


```
### Features this CLI belongs to 
*  OSPFv2


## ip ospf 

### Description 

```
Configures OSPFv2 parameters within an IPv4 interface.


```
### Parent Commands (Modes) 

```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]

```
### Syntax 

```
ip ospf

```
### Usage Guidelines 

```
Use this command to configure OSPFv2 parameters under an IPv4 interface. IPv4 interface
can be Ethernet interface, VLAN interface, Portchannel interface or a Loopback interface.
Every OSPFv2 parameter on an interface can be associated with its specific IPv4 addresss
by explicitely specifying the IPv4 address after the parameter. Specifying interface IPv4
address is an optional.


```
### Examples 
#### Configures OSPFv2 parameters within an IPv4 interface 

```
sonic-cli(config-router-ospf)# no ip ospf interface


```
### Features this CLI belongs to 
*  OSPFv2


## ip ospf 

### Description 

```
Configures OSPFv2 parameters within an IPv4 interface.


```
### Parent Commands (Modes) 

```
interface <vlan-if-name>

```
### Syntax 

```
ip ospf

```
### Usage Guidelines 

```
Use this command to configure OSPFv2 parameters under an IPv4 interface. IPv4 interface
can be Ethernet interface, VLAN interface, Portchannel interface or a Loopback interface.
Every OSPFv2 parameter on an interface can be associated with its specific IPv4 addresss
by explicitely specifying the IPv4 address after the parameter. Specifying interface IPv4
address is an optional.


```
### Examples 
#### Configures OSPFv2 parameters within an IPv4 interface 

```
sonic-cli(config-router-ospf)# no ip ospf interface


```
### Features this CLI belongs to 
*  OSPFv2


## ip ospf 

### Description 

```
Configures OSPFv2 parameters within an IPv4 interface.


```
### Parent Commands (Modes) 

```
interface Loopback <lo-id>

```
### Syntax 

```
ip ospf

```
### Usage Guidelines 

```
Use this command to configure OSPFv2 parameters under an IPv4 interface. IPv4 interface
can be Ethernet interface, VLAN interface, Portchannel interface or a Loopback interface.
Every OSPFv2 parameter on an interface can be associated with its specific IPv4 addresss
by explicitely specifying the IPv4 address after the parameter. Specifying interface IPv4
address is an optional.


```
### Examples 
#### Configures OSPFv2 parameters within an IPv4 interface 

```
sonic-cli(config-router-ospf)# no ip ospf interface


```
### Features this CLI belongs to 
*  OSPFv2


## ip ospf area 
### Description 
```
Configures OSPFv2 interface area identifier.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
ip ospf area <area-id> [ <ip-address> ]
no ip ospf area [ <ip-address> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| area-id | A.B.C.D or 0..4294967295  | String  |
| ip-address | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to associate an interface into an OSPFv2 area. Area identifier
can be configured only when there is an already configured OSPFv2 router within the
interface VRF and there are no network commands configured within that router.
Area identifier configuration on an interface will get auto unconfigured while
OSPFv2 router gets unconfigured from the VRF.
```
### Examples 
#### Configures OSPFv2 interface area identifier 
```
sonic-cli(config-router-ospf)# ip ospf area 19
sonic-cli(config-router-ospf)# ip ospf area 19.0.0.1
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf area 
### Description 
```
Configures OSPFv2 interface area identifier.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
ip ospf area <area-id> [ <ip-address> ]
no ip ospf area [ <ip-address> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| area-id | A.B.C.D or 0..4294967295  | String  |
| ip-address | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to associate an interface into an OSPFv2 area. Area identifier
can be configured only when there is an already configured OSPFv2 router within the
interface VRF and there are no network commands configured within that router.
Area identifier configuration on an interface will get auto unconfigured while
OSPFv2 router gets unconfigured from the VRF.
```
### Examples 
#### Configures OSPFv2 interface area identifier 
```
sonic-cli(config-router-ospf)# ip ospf area 19
sonic-cli(config-router-ospf)# ip ospf area 19.0.0.1
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf area 
### Description 
```
Configures OSPFv2 interface area identifier.
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ip ospf area <area-id> [ <ip-address> ]
no ip ospf area [ <ip-address> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| area-id | A.B.C.D or 0..4294967295  | String  |
| ip-address | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to associate an interface into an OSPFv2 area. Area identifier
can be configured only when there is an already configured OSPFv2 router within the
interface VRF and there are no network commands configured within that router.
Area identifier configuration on an interface will get auto unconfigured while
OSPFv2 router gets unconfigured from the VRF.
```
### Examples 
#### Configures OSPFv2 interface area identifier 
```
sonic-cli(config-router-ospf)# ip ospf area 19
sonic-cli(config-router-ospf)# ip ospf area 19.0.0.1
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf area 
### Description 
```
Configures OSPFv2 interface area identifier.
```
### Parent Commands (Modes) 
```
interface Loopback <lo-id>
```
### Syntax 
```
ip ospf area <area-id> [ <ip-address> ]
no ip ospf area [ <ip-address> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| area-id | A.B.C.D or 0..4294967295  | String  |
| ip-address | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to associate an interface into an OSPFv2 area. Area identifier
can be configured only when there is an already configured OSPFv2 router within the
interface VRF and there are no network commands configured within that router.
Area identifier configuration on an interface will get auto unconfigured while
OSPFv2 router gets unconfigured from the VRF.
```
### Examples 
#### Configures OSPFv2 interface area identifier 
```
sonic-cli(config-router-ospf)# ip ospf area 19
sonic-cli(config-router-ospf)# ip ospf area 19.0.0.1
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf authentication 
### Description 
```
Configures OSPFv2 interface authentication type.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
ip ospf authentication { { [ message-digest [ <ip-address> ] ] } | { [ null [ <ip-address> ] ] } } ] [ <ip-address> ]
no ip ospf authentication { { [ message-digest [ <ip-address> ] ] } | { [ null [ <ip-address> ] ] } } ] [ <ip-address> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| ip-address | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to enable OSPFv2 authentication type for OSPFv2 messages.
Authentocation types can be clear text authentication, message-digest authentication
or no authentication. Interface mode authentication type would override router mode
area authentication type. Based on the configured authentication type, corresponding
configured authentication key would be used for OPSFV2 message authentication.
```
### Examples 
#### Enable OSPFv2 authentication 
```
sonic-cli(config-router-ospf)# ip ospf authentication
sonic-cli(config-router-ospf)# ip ospf authentication message-digest
sonic-cli(config-router-ospf)# ip ospf authentication null
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf authentication 
### Description 
```
Configures OSPFv2 interface authentication type.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
ip ospf authentication { { [ message-digest [ <ip-address> ] ] } | { [ null [ <ip-address> ] ] } } ] [ <ip-address> ]
no ip ospf authentication { { [ message-digest [ <ip-address> ] ] } | { [ null [ <ip-address> ] ] } } ] [ <ip-address> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| ip-address | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to enable OSPFv2 authentication type for OSPFv2 messages.
Authentocation types can be clear text authentication, message-digest authentication
or no authentication. Interface mode authentication type would override router mode
area authentication type. Based on the configured authentication type, corresponding
configured authentication key would be used for OPSFV2 message authentication.
```
### Examples 
#### Enable OSPFv2 authentication 
```
sonic-cli(config-router-ospf)# ip ospf authentication
sonic-cli(config-router-ospf)# ip ospf authentication message-digest
sonic-cli(config-router-ospf)# ip ospf authentication null
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf authentication 
### Description 
```
Configures OSPFv2 interface authentication type.
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ip ospf authentication { { [ message-digest [ <ip-address> ] ] } | { [ null [ <ip-address> ] ] } } ] [ <ip-address> ]
no ip ospf authentication { { [ message-digest [ <ip-address> ] ] } | { [ null [ <ip-address> ] ] } } ] [ <ip-address> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| ip-address | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to enable OSPFv2 authentication type for OSPFv2 messages.
Authentocation types can be clear text authentication, message-digest authentication
or no authentication. Interface mode authentication type would override router mode
area authentication type. Based on the configured authentication type, corresponding
configured authentication key would be used for OPSFV2 message authentication.
```
### Examples 
#### Enable OSPFv2 authentication 
```
sonic-cli(config-router-ospf)# ip ospf authentication
sonic-cli(config-router-ospf)# ip ospf authentication message-digest
sonic-cli(config-router-ospf)# ip ospf authentication null
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf authentication 
### Description 
```
Configures OSPFv2 interface authentication type.
```
### Parent Commands (Modes) 
```
interface Loopback <lo-id>
```
### Syntax 
```
ip ospf authentication { { [ message-digest [ <ip-address> ] ] } | { [ null [ <ip-address> ] ] } } ] [ <ip-address> ]
no ip ospf authentication { { [ message-digest [ <ip-address> ] ] } | { [ null [ <ip-address> ] ] } } ] [ <ip-address> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| ip-address | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to enable OSPFv2 authentication type for OSPFv2 messages.
Authentocation types can be clear text authentication, message-digest authentication
or no authentication. Interface mode authentication type would override router mode
area authentication type. Based on the configured authentication type, corresponding
configured authentication key would be used for OPSFV2 message authentication.
```
### Examples 
#### Enable OSPFv2 authentication 
```
sonic-cli(config-router-ospf)# ip ospf authentication
sonic-cli(config-router-ospf)# ip ospf authentication message-digest
sonic-cli(config-router-ospf)# ip ospf authentication null
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf authentication-key 
### Description 
```
Configures OSPFv2 interface clear text authentication key.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
ip ospf authentication-key <authkey> { { [ encrypted [ <ip-address> ] ] } [ <ip-address> ] }
no ip ospf authentication-key [ <ip-address> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| authkey | String  | String  |
| ip-address | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to configure OSPFv2 clear text authentication key. Clear text
authentication key can be up to eight characters long. User provided actual
password will be displayed as encrypted string in show configuration comamnd
displays and saved as encrypted string in configuration file. It is recomended
to use only actual password and not to use encrypted string as password.
```
### Examples 
#### Enable OSPFv2 authentication 
```
sonic-cli(config-router-ospf)# ip ospf authentication-key pa$$woRd
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf authentication-key 
### Description 
```
Configures OSPFv2 interface clear text authentication key.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
ip ospf authentication-key <authkey> { { [ encrypted [ <ip-address> ] ] } [ <ip-address> ] }
no ip ospf authentication-key [ <ip-address> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| authkey | String  | String  |
| ip-address | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to configure OSPFv2 clear text authentication key. Clear text
authentication key can be up to eight characters long. User provided actual
password will be displayed as encrypted string in show configuration comamnd
displays and saved as encrypted string in configuration file. It is recomended
to use only actual password and not to use encrypted string as password.
```
### Examples 
#### Enable OSPFv2 authentication 
```
sonic-cli(config-router-ospf)# ip ospf authentication-key pa$$woRd
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf authentication-key 
### Description 
```
Configures OSPFv2 interface clear text authentication key.
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ip ospf authentication-key <authkey> { { [ encrypted [ <ip-address> ] ] } [ <ip-address> ] }
no ip ospf authentication-key [ <ip-address> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| authkey | String  | String  |
| ip-address | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to configure OSPFv2 clear text authentication key. Clear text
authentication key can be up to eight characters long. User provided actual
password will be displayed as encrypted string in show configuration comamnd
displays and saved as encrypted string in configuration file. It is recomended
to use only actual password and not to use encrypted string as password.
```
### Examples 
#### Enable OSPFv2 authentication 
```
sonic-cli(config-router-ospf)# ip ospf authentication-key pa$$woRd
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf authentication-key 
### Description 
```
Configures OSPFv2 interface clear text authentication key.
```
### Parent Commands (Modes) 
```
interface Loopback <lo-id>
```
### Syntax 
```
ip ospf authentication-key <authkey> { { [ encrypted [ <ip-address> ] ] } [ <ip-address> ] }
no ip ospf authentication-key [ <ip-address> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| authkey | String  | String  |
| ip-address | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to configure OSPFv2 clear text authentication key. Clear text
authentication key can be up to eight characters long. User provided actual
password will be displayed as encrypted string in show configuration comamnd
displays and saved as encrypted string in configuration file. It is recomended
to use only actual password and not to use encrypted string as password.
```
### Examples 
#### Enable OSPFv2 authentication 
```
sonic-cli(config-router-ospf)# ip ospf authentication-key pa$$woRd
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf bfd 
### Description 
```
Configures OSPFv2 interface BFD.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
ip ospf bfd
no ip ospf bfd
```
### Usage Guidelines 
```
Use this command to configure OSPFv2 interface BFD. Enabling BFD will
establish a BFD session between the OSPF neighbors. Any failure in BFD
session will bring down the OSPFv2 session.
```
### Examples 
#### Configures OSPFv2 interface BFD 
```
sonic-cli(config-router-ospf)# ip ospf bfd
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf bfd 
### Description 
```
Configures OSPFv2 interface BFD.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
ip ospf bfd
no ip ospf bfd
```
### Usage Guidelines 
```
Use this command to configure OSPFv2 interface BFD. Enabling BFD will
establish a BFD session between the OSPF neighbors. Any failure in BFD
session will bring down the OSPFv2 session.
```
### Examples 
#### Configures OSPFv2 interface BFD 
```
sonic-cli(config-router-ospf)# ip ospf bfd
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf bfd 
### Description 
```
Configures OSPFv2 interface BFD.
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ip ospf bfd
no ip ospf bfd
```
### Usage Guidelines 
```
Use this command to configure OSPFv2 interface BFD. Enabling BFD will
establish a BFD session between the OSPF neighbors. Any failure in BFD
session will bring down the OSPFv2 session.
```
### Examples 
#### Configures OSPFv2 interface BFD 
```
sonic-cli(config-router-ospf)# ip ospf bfd
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf bfd 
### Description 
```
Configures OSPFv2 interface BFD.
```
### Parent Commands (Modes) 
```
interface Loopback <lo-id>
```
### Syntax 
```
ip ospf bfd
no ip ospf bfd
```
### Usage Guidelines 
```
Use this command to configure OSPFv2 interface BFD. Enabling BFD will
establish a BFD session between the OSPF neighbors. Any failure in BFD
session will bring down the OSPFv2 session.
```
### Examples 
#### Configures OSPFv2 interface BFD 
```
sonic-cli(config-router-ospf)# ip ospf bfd
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf cost 
### Description 
```
Configures OSPFv2 interface cost.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
ip ospf cost <interface-cost> [ <ip-address> ]
no ip ospf cost [ <ip-address> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| interface-cost |   | Integer  |
| ip-address | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to configure OSPFv2 interface cost.
```
### Examples 
#### Configures OSPFv2 interface cost 
```
sonic-cli(config-router-ospf)# ip ospf cost 38
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf cost 
### Description 
```
Configures OSPFv2 interface cost.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
ip ospf cost <interface-cost> [ <ip-address> ]
no ip ospf cost [ <ip-address> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| interface-cost |   | Integer  |
| ip-address | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to configure OSPFv2 interface cost.
```
### Examples 
#### Configures OSPFv2 interface cost 
```
sonic-cli(config-router-ospf)# ip ospf cost 38
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf cost 
### Description 
```
Configures OSPFv2 interface cost.
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ip ospf cost <interface-cost> [ <ip-address> ]
no ip ospf cost [ <ip-address> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| interface-cost |   | Integer  |
| ip-address | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to configure OSPFv2 interface cost.
```
### Examples 
#### Configures OSPFv2 interface cost 
```
sonic-cli(config-router-ospf)# ip ospf cost 38
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf cost 
### Description 
```
Configures OSPFv2 interface cost.
```
### Parent Commands (Modes) 
```
interface Loopback <lo-id>
```
### Syntax 
```
ip ospf cost <interface-cost> [ <ip-address> ]
no ip ospf cost [ <ip-address> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| interface-cost |   | Integer  |
| ip-address | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to configure OSPFv2 interface cost.
```
### Examples 
#### Configures OSPFv2 interface cost 
```
sonic-cli(config-router-ospf)# ip ospf cost 38
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf dead-interval 
### Description 
```
Configures OSPFv2 adjacency dead interval 
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
ip ospf dead-interval { { [ <deadinterval> [ <ip-address> ] ] } | { [ minimal { [ hello-multiplier { <hellomultiplier> [ <ip-address> ] } ] } ] } }
no ip ospf dead-interval { [ <ip-address> ] | { [ minimal { [ hello-multiplier [ <ip-address> ] ] } ] } } ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| deadinterval |   | Integer  |
| ip-address | A.B.C.D  | String  |
| hellomultiplier |   | Integer  |
## ip ospf dead-interval 
### Description 
```
Configures OSPFv2 adjacency dead interval 
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
ip ospf dead-interval { { [ <deadinterval> [ <ip-address> ] ] } | { [ minimal { [ hello-multiplier { <hellomultiplier> [ <ip-address> ] } ] } ] } }
no ip ospf dead-interval { [ <ip-address> ] | { [ minimal { [ hello-multiplier [ <ip-address> ] ] } ] } } ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| deadinterval |   | Integer  |
| ip-address | A.B.C.D  | String  |
| hellomultiplier |   | Integer  |
## ip ospf dead-interval 
### Description 
```
Configures OSPFv2 adjacency dead interval 
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ip ospf dead-interval { { [ <deadinterval> [ <ip-address> ] ] } | { [ minimal { [ hello-multiplier { <hellomultiplier> [ <ip-address> ] } ] } ] } }
no ip ospf dead-interval { [ <ip-address> ] | { [ minimal { [ hello-multiplier [ <ip-address> ] ] } ] } } ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| deadinterval |   | Integer  |
| ip-address | A.B.C.D  | String  |
| hellomultiplier |   | Integer  |
## ip ospf dead-interval 
### Description 
```
Configures OSPFv2 adjacency dead interval 
```
### Parent Commands (Modes) 
```
interface Loopback <lo-id>
```
### Syntax 
```
ip ospf dead-interval { { [ <deadinterval> [ <ip-address> ] ] } | { [ minimal { [ hello-multiplier { <hellomultiplier> [ <ip-address> ] } ] } ] } }
no ip ospf dead-interval { [ <ip-address> ] | { [ minimal { [ hello-multiplier [ <ip-address> ] ] } ] } } ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| deadinterval |   | Integer  |
| ip-address | A.B.C.D  | String  |
| hellomultiplier |   | Integer  |
## ip ospf hello-interval 
### Description 
```
Configures OSPFv2 interface neighbour hello interval.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
ip ospf hello-interval [ <hellointerval> [ <ip-address> ] ]
no ip ospf hello-interval [ <ip-address> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| hellointerval |   | Integer  |
| ip-address | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to configure OSPFv2 interface neighbour hello interval.
```
### Examples 
#### Configures OSPFv2 interface neighbour hello interval 
```
sonic-cli(config-router-ospf)# ip ospf thello-interval 20
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf hello-interval 
### Description 
```
Configures OSPFv2 interface neighbour hello interval.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
ip ospf hello-interval [ <hellointerval> [ <ip-address> ] ]
no ip ospf hello-interval [ <ip-address> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| hellointerval |   | Integer  |
| ip-address | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to configure OSPFv2 interface neighbour hello interval.
```
### Examples 
#### Configures OSPFv2 interface neighbour hello interval 
```
sonic-cli(config-router-ospf)# ip ospf thello-interval 20
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf hello-interval 
### Description 
```
Configures OSPFv2 interface neighbour hello interval.
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ip ospf hello-interval [ <hellointerval> [ <ip-address> ] ]
no ip ospf hello-interval [ <ip-address> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| hellointerval |   | Integer  |
| ip-address | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to configure OSPFv2 interface neighbour hello interval.
```
### Examples 
#### Configures OSPFv2 interface neighbour hello interval 
```
sonic-cli(config-router-ospf)# ip ospf thello-interval 20
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf hello-interval 
### Description 
```
Configures OSPFv2 interface neighbour hello interval.
```
### Parent Commands (Modes) 
```
interface Loopback <lo-id>
```
### Syntax 
```
ip ospf hello-interval [ <hellointerval> [ <ip-address> ] ]
no ip ospf hello-interval [ <ip-address> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| hellointerval |   | Integer  |
| ip-address | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to configure OSPFv2 interface neighbour hello interval.
```
### Examples 
#### Configures OSPFv2 interface neighbour hello interval 
```
sonic-cli(config-router-ospf)# ip ospf thello-interval 20
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf message-digest-key 
### Description 
```
Configures OSPFv2 interface message digest authentication key.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
ip ospf message-digest-key <keyid> { md5 { <md5key> { [ encrypted [ <ip-address> ] ] } [ <ip-address> ] } }
no ip ospf message-digest-key <keyid> { md5 [ <ip-address> ] }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| keyid |   | Integer  |
| md5key | String  | String  |
| ip-address | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to configure OSPFv2 message digest authentication key. Message
digest authentication key can be up to sixteen characters long. User provided
actual password will be displayed as encrypted string in show configuration comamnd
displays and saved as encrypted string in configuration file. It is recomended
to use only actual password and not to use encrypted string as password.
```
### Examples 
#### Enable OSPFv2 authentication 
```
sonic-cli(config-router-ospf)# ip ospf message-digest-key 10 md5 mDpa$$woRd
sonic-cli(config-router-ospf)# ip ospf message-digest-key 19 md5 mDpa$$woRd
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf message-digest-key 
### Description 
```
Configures OSPFv2 interface message digest authentication key.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
ip ospf message-digest-key <keyid> { md5 { <md5key> { [ encrypted [ <ip-address> ] ] } [ <ip-address> ] } }
no ip ospf message-digest-key <keyid> { md5 [ <ip-address> ] }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| keyid |   | Integer  |
| md5key | String  | String  |
| ip-address | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to configure OSPFv2 message digest authentication key. Message
digest authentication key can be up to sixteen characters long. User provided
actual password will be displayed as encrypted string in show configuration comamnd
displays and saved as encrypted string in configuration file. It is recomended
to use only actual password and not to use encrypted string as password.
```
### Examples 
#### Enable OSPFv2 authentication 
```
sonic-cli(config-router-ospf)# ip ospf message-digest-key 10 md5 mDpa$$woRd
sonic-cli(config-router-ospf)# ip ospf message-digest-key 19 md5 mDpa$$woRd
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf message-digest-key 
### Description 
```
Configures OSPFv2 interface message digest authentication key.
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ip ospf message-digest-key <keyid> { md5 { <md5key> { [ encrypted [ <ip-address> ] ] } [ <ip-address> ] } }
no ip ospf message-digest-key <keyid> { md5 [ <ip-address> ] }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| keyid |   | Integer  |
| md5key | String  | String  |
| ip-address | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to configure OSPFv2 message digest authentication key. Message
digest authentication key can be up to sixteen characters long. User provided
actual password will be displayed as encrypted string in show configuration comamnd
displays and saved as encrypted string in configuration file. It is recomended
to use only actual password and not to use encrypted string as password.
```
### Examples 
#### Enable OSPFv2 authentication 
```
sonic-cli(config-router-ospf)# ip ospf message-digest-key 10 md5 mDpa$$woRd
sonic-cli(config-router-ospf)# ip ospf message-digest-key 19 md5 mDpa$$woRd
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf message-digest-key 
### Description 
```
Configures OSPFv2 interface message digest authentication key.
```
### Parent Commands (Modes) 
```
interface Loopback <lo-id>
```
### Syntax 
```
ip ospf message-digest-key <keyid> { md5 { <md5key> { [ encrypted [ <ip-address> ] ] } [ <ip-address> ] } }
no ip ospf message-digest-key <keyid> { md5 [ <ip-address> ] }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| keyid |   | Integer  |
| md5key | String  | String  |
| ip-address | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to configure OSPFv2 message digest authentication key. Message
digest authentication key can be up to sixteen characters long. User provided
actual password will be displayed as encrypted string in show configuration comamnd
displays and saved as encrypted string in configuration file. It is recomended
to use only actual password and not to use encrypted string as password.
```
### Examples 
#### Enable OSPFv2 authentication 
```
sonic-cli(config-router-ospf)# ip ospf message-digest-key 10 md5 mDpa$$woRd
sonic-cli(config-router-ospf)# ip ospf message-digest-key 19 md5 mDpa$$woRd
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf mtu-ignore 
### Description 
```
Disables OSPFv2 MTU mismatch detection.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
ip ospf mtu-ignore [ <ip-address> ]
no ip ospf mtu-ignore [ <ip-address> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| ip-address | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to disable OSPFv2 MTU mismatch detection. MTU mismatch
detection is enabled by default.
```
### Examples 
#### Disables OSPFv2 MTU mismatch detection 
```
sonic-cli(config-router-ospf)# ip ospf mtu-ignore
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf mtu-ignore 
### Description 
```
Disables OSPFv2 MTU mismatch detection.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
ip ospf mtu-ignore [ <ip-address> ]
no ip ospf mtu-ignore [ <ip-address> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| ip-address | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to disable OSPFv2 MTU mismatch detection. MTU mismatch
detection is enabled by default.
```
### Examples 
#### Disables OSPFv2 MTU mismatch detection 
```
sonic-cli(config-router-ospf)# ip ospf mtu-ignore
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf mtu-ignore 
### Description 
```
Disables OSPFv2 MTU mismatch detection.
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ip ospf mtu-ignore [ <ip-address> ]
no ip ospf mtu-ignore [ <ip-address> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| ip-address | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to disable OSPFv2 MTU mismatch detection. MTU mismatch
detection is enabled by default.
```
### Examples 
#### Disables OSPFv2 MTU mismatch detection 
```
sonic-cli(config-router-ospf)# ip ospf mtu-ignore
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf mtu-ignore 
### Description 
```
Disables OSPFv2 MTU mismatch detection.
```
### Parent Commands (Modes) 
```
interface Loopback <lo-id>
```
### Syntax 
```
ip ospf mtu-ignore [ <ip-address> ]
no ip ospf mtu-ignore [ <ip-address> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| ip-address | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to disable OSPFv2 MTU mismatch detection. MTU mismatch
detection is enabled by default.
```
### Examples 
#### Disables OSPFv2 MTU mismatch detection 
```
sonic-cli(config-router-ospf)# ip ospf mtu-ignore
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf network 
### Description 
```
Configures OSPFv2 interface network type.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
ip ospf network { broadcast | point-to-point }
no ip ospf network
```
### Usage Guidelines 
```
Use this command to configure OSPFv2 interface network type. Broadcast and
Point-to-point networks types are supported. By default Network type will be
broacast.
```
### Examples 
#### Configures OSPFv2 interface network type 
```
sonic-cli(config-router-ospf)# ip ospf network point-to-point
sonic-cli(config-router-ospf)# ip ospf network broadcast
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf network 
### Description 
```
Configures OSPFv2 interface network type.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
ip ospf network { broadcast | point-to-point }
no ip ospf network
```
### Usage Guidelines 
```
Use this command to configure OSPFv2 interface network type. Broadcast and
Point-to-point networks types are supported. By default Network type will be
broacast.
```
### Examples 
#### Configures OSPFv2 interface network type 
```
sonic-cli(config-router-ospf)# ip ospf network point-to-point
sonic-cli(config-router-ospf)# ip ospf network broadcast
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf network 
### Description 
```
Configures OSPFv2 interface network type.
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ip ospf network { broadcast | point-to-point }
no ip ospf network
```
### Usage Guidelines 
```
Use this command to configure OSPFv2 interface network type. Broadcast and
Point-to-point networks types are supported. By default Network type will be
broacast.
```
### Examples 
#### Configures OSPFv2 interface network type 
```
sonic-cli(config-router-ospf)# ip ospf network point-to-point
sonic-cli(config-router-ospf)# ip ospf network broadcast
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf network 
### Description 
```
Configures OSPFv2 interface network type.
```
### Parent Commands (Modes) 
```
interface Loopback <lo-id>
```
### Syntax 
```
ip ospf network { broadcast | point-to-point }
no ip ospf network
```
### Usage Guidelines 
```
Use this command to configure OSPFv2 interface network type. Broadcast and
Point-to-point networks types are supported. By default Network type will be
broacast.
```
### Examples 
#### Configures OSPFv2 interface network type 
```
sonic-cli(config-router-ospf)# ip ospf network point-to-point
sonic-cli(config-router-ospf)# ip ospf network broadcast
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf priority 
### Description 
```
Configures OSPFv2 adjacency router priority.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
ip ospf priority <priorityval> [ <ip-address> ]
no ip ospf priority [ <ip-address> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| priorityval |   | Integer  |
| ip-address | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to configure OSPFv2 adjacency router priority.
```
### Examples 
#### Configures OSPFv2 adjacency router priority 
```
sonic-cli(config-router-ospf)# ip ospf priority 19
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf priority 
### Description 
```
Configures OSPFv2 adjacency router priority.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
ip ospf priority <priorityval> [ <ip-address> ]
no ip ospf priority [ <ip-address> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| priorityval |   | Integer  |
| ip-address | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to configure OSPFv2 adjacency router priority.
```
### Examples 
#### Configures OSPFv2 adjacency router priority 
```
sonic-cli(config-router-ospf)# ip ospf priority 19
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf priority 
### Description 
```
Configures OSPFv2 adjacency router priority.
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ip ospf priority <priorityval> [ <ip-address> ]
no ip ospf priority [ <ip-address> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| priorityval |   | Integer  |
| ip-address | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to configure OSPFv2 adjacency router priority.
```
### Examples 
#### Configures OSPFv2 adjacency router priority 
```
sonic-cli(config-router-ospf)# ip ospf priority 19
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf priority 
### Description 
```
Configures OSPFv2 adjacency router priority.
```
### Parent Commands (Modes) 
```
interface Loopback <lo-id>
```
### Syntax 
```
ip ospf priority <priorityval> [ <ip-address> ]
no ip ospf priority [ <ip-address> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| priorityval |   | Integer  |
| ip-address | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to configure OSPFv2 adjacency router priority.
```
### Examples 
#### Configures OSPFv2 adjacency router priority 
```
sonic-cli(config-router-ospf)# ip ospf priority 19
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf retransmit-interval 
### Description 
```
Configures OSPFv2 interface LSA retransmit interval.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
ip ospf retransmit-interval <retransmitinterval> [ <ip-address> ]
no ip ospf retransmit-interval [ <ip-address> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| retransmitinterval |   | Integer  |
| ip-address | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to configure OSPFv2 interface LSA retransmit interval.
```
### Examples 
#### Configures OSPFv2 interface  LSA retransmit interval 
```
sonic-cli(config-router-ospf)# ip ospf retransmit-interval 35
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf retransmit-interval 
### Description 
```
Configures OSPFv2 interface LSA retransmit interval.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
ip ospf retransmit-interval <retransmitinterval> [ <ip-address> ]
no ip ospf retransmit-interval [ <ip-address> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| retransmitinterval |   | Integer  |
| ip-address | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to configure OSPFv2 interface LSA retransmit interval.
```
### Examples 
#### Configures OSPFv2 interface  LSA retransmit interval 
```
sonic-cli(config-router-ospf)# ip ospf retransmit-interval 35
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf retransmit-interval 
### Description 
```
Configures OSPFv2 interface LSA retransmit interval.
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ip ospf retransmit-interval <retransmitinterval> [ <ip-address> ]
no ip ospf retransmit-interval [ <ip-address> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| retransmitinterval |   | Integer  |
| ip-address | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to configure OSPFv2 interface LSA retransmit interval.
```
### Examples 
#### Configures OSPFv2 interface  LSA retransmit interval 
```
sonic-cli(config-router-ospf)# ip ospf retransmit-interval 35
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf retransmit-interval 
### Description 
```
Configures OSPFv2 interface LSA retransmit interval.
```
### Parent Commands (Modes) 
```
interface Loopback <lo-id>
```
### Syntax 
```
ip ospf retransmit-interval <retransmitinterval> [ <ip-address> ]
no ip ospf retransmit-interval [ <ip-address> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| retransmitinterval |   | Integer  |
| ip-address | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to configure OSPFv2 interface LSA retransmit interval.
```
### Examples 
#### Configures OSPFv2 interface  LSA retransmit interval 
```
sonic-cli(config-router-ospf)# ip ospf retransmit-interval 35
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf transmit-delay 
### Description 
```
Configures OSPFv2 interface LSA transmit delay interval.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
ip ospf transmit-delay <transmitdelay> [ <ip-address> ]
no ip ospf transmit-delay [ <ip-address> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| transmitdelay |   | Integer  |
| ip-address | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to configure OSPFv2 interface LSA transmit delay interval.
```
### Examples 
#### Configures OSPFv2 interface  LSA transmit delay interval 
```
sonic-cli(config-router-ospf)# ip ospf transmit-delay 35
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf transmit-delay 
### Description 
```
Configures OSPFv2 interface LSA transmit delay interval.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
ip ospf transmit-delay <transmitdelay> [ <ip-address> ]
no ip ospf transmit-delay [ <ip-address> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| transmitdelay |   | Integer  |
| ip-address | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to configure OSPFv2 interface LSA transmit delay interval.
```
### Examples 
#### Configures OSPFv2 interface  LSA transmit delay interval 
```
sonic-cli(config-router-ospf)# ip ospf transmit-delay 35
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf transmit-delay 
### Description 
```
Configures OSPFv2 interface LSA transmit delay interval.
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ip ospf transmit-delay <transmitdelay> [ <ip-address> ]
no ip ospf transmit-delay [ <ip-address> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| transmitdelay |   | Integer  |
| ip-address | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to configure OSPFv2 interface LSA transmit delay interval.
```
### Examples 
#### Configures OSPFv2 interface  LSA transmit delay interval 
```
sonic-cli(config-router-ospf)# ip ospf transmit-delay 35
```
### Features this CLI belongs to 
*  OSPFv2
## ip ospf transmit-delay 
### Description 
```
Configures OSPFv2 interface LSA transmit delay interval.
```
### Parent Commands (Modes) 
```
interface Loopback <lo-id>
```
### Syntax 
```
ip ospf transmit-delay <transmitdelay> [ <ip-address> ]
no ip ospf transmit-delay [ <ip-address> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| transmitdelay |   | Integer  |
| ip-address | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to configure OSPFv2 interface LSA transmit delay interval.
```
### Examples 
#### Configures OSPFv2 interface  LSA transmit delay interval 
```
sonic-cli(config-router-ospf)# ip ospf transmit-delay 35
```
### Features this CLI belongs to 
*  OSPFv2
## ip pim 
### Description 
```
The following PIM (IPv4) global configurations are
executed in configuration-view. All these commands can be executed per VRF. If
VRF is not specified, then it will be executed in "default" VRF context. Once
PIM global configurations are done on a particular non-default VRF, that VRF
cannot be deleted from the system until relevant PIM configurations are cleared.
--------------------
Join Prune Interval:
--------------------
Configure the frequency of join/prune messages on the specified interface.
-----------------
Keep Alive Timer:
-----------------
Period after last (S,G) data packet during which (S,G) Join state will be even in the absence of (S,G) Join messages.
------------------------------
SSM Prefix-list configuration:
------------------------------
Apart from standard PIM-SSM multicast range (232.0.0.0/8),
user can qualify other multicast group address as PIM-SSM range using
IP-prefix-list. User should create the corresponding IP-prefix-list using
existing "ip prefix-list" CLI command and then associate that prefix-list to
PIM through this CLI command. This IP-prefix-list cannot be deleted from the
system until after removal of any PIM global configuration referring to the
prefix list.
-----
ECMP:
-----
If PIM has the a choice of ECMP nexthops for a particular RPF, PIM will cause
S,G flows to be spread out amongst the nexthops. If this command is not
specified then the first nexthop found will be used
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
ip pim [ vrf <vrf-name> ] { { ecmp [ rebalance ] } | { join-prune-interval <jpi> } | { keep-alive-timer <kat> } | { ssm { prefix-list <pln> } } }
no ip pim [ vrf <vrf-name> ] { { ecmp [ rebalance ] } | jpi | kat | { ssm pln } }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrf-name | WORD  | String  |
| jpi |   | Integer  |
| kat |   | Integer  |
| pln | String  | String  |
### Usage Guidelines 
```
* ip pim [vrf <vrf-name>] join-prune-interval <value : (60-600 seconds)>
* ip pim [vrf <vrf-name>] keep-alive-timer <value : (31-60000 seconds)>
* ip pim [vrf <vrf-name>] ssm prefix-list <prefix-list-name>
* ip pim [vrf <vrf-name>] ecmp [rebalance]
```
### Examples 
#### List of PIM global level commands 
```
sonic# configure terminal
sonic(config)#
sonic(config)# ip pim
  vrf                  VRF name
  join-prune-interval  Configure Join Prune Interval
  keep-alive-timer     Configure Keep Alive timer
  ssm                  Configure SSM
  ecmp                 Enable ECMP
sonic(config)# ip pim vrf Vrf1
  join-prune-interval  Configure Join Prune Interval
  keep-alive-timer     Configure Keep Alive timer
  ssm                  Configure SSM
  ecmp                 Enable ECMP
sonic(config)# no ip pim
  vrf                  VRF name
  join-prune-interval  Set Join Prune Interval to default
  keep-alive-timer     Set Keep Alive timer to default
  ssm                  Remove SSM configuration
  ecmp                 Disable ECMP
sonic(config)# no ip pim vrf Vrf1
  join-prune-interval  Set Join Prune Interval to default
  keep-alive-timer     Set Keep Alive timer to default
  ssm                  Remove SSM configuration
  ecmp                 Disable ECMP
```
#### ip pim [vrf <vrf-name>] join-prune-interval <value : (60-600 seconds)> 
```
sonic(config)# ip pim join-prune-interval
      <0..600>  Range 60-600 seconds
  -------------------------------------------------------
  sonic(config)# ip pim join-prune-interval 70
  sonic(config)#
  -------------------------------------------------------
  sonic(config)# ip pim vrf Vrf1 join-prune-interval 75
  sonic(config)#
```
#### ip pim [vrf <vrf-name>] keep-alive-timer <value : (31-60000 seconds)> 
```
sonic(config)# ip pim keep-alive-timer
    <31..60000>  Range 31-60000 seconds
sonic(config)#
sonic(config)# ip pim keep-alive-timer 35
sonic(config)# ip pim vrf Vrf1 keep-alive-timer 45
sonic(config)#
```
#### ip pim [vrf <vrf-name>] ssm prefix-list <vrefix-list-name> 
```
sonic(config)# ip pim ssm
  prefix-list  Configure prefix list name
sonic(config)# ip pim ssm prefix-list
  String  Prefix list name
sonic(config)# ip pim ssm prefix-list pim_ssm_pfx_list
sonic(config)# ip pim vrf Vrf1 ssm prefix-list pim_ssm_pfx_list
sonic(config)#
```
#### ip pim ecmp 
```
sonic(config)# ip pim ecmp
sonic(config)# ip pim vrf Vrf1 ecmp
sonic(config)#
```
#### ip pim ecmp rebalance 
```
sonic(config)# ip pim ecmp rebalance
sonic(config)# ip pim vrf Vrf1 ecmp rebalance
sonic(config)#
```
## ip pim bfd 
### Description 
```
This command enables BFD processing for PIM on the given interface.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
ip pim bfd
no ip pim bfd
```
### Usage Guidelines 
```
ip pim bfd
```
### Examples 
#### Enable BFD on interface Vlan100 
```
sonic# configure terminal
sonic(config)# interface Vlan 100
sonic(conf-if-Vlan100)# ip pim bfd
sonic(conf-if-Vlan100)#
```
## ip pim bfd 
### Description 
```
This command enables BFD processing for PIM on the given interface.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
ip pim bfd
no ip pim bfd
```
### Usage Guidelines 
```
ip pim bfd
```
### Examples 
#### Enable BFD on interface Vlan100 
```
sonic# configure terminal
sonic(config)# interface Vlan 100
sonic(conf-if-Vlan100)# ip pim bfd
sonic(conf-if-Vlan100)#
```
## ip pim bfd 
### Description 
```
This command enables BFD processing for PIM on the given interface.
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ip pim bfd
no ip pim bfd
```
### Usage Guidelines 
```
ip pim bfd
```
### Examples 
#### Enable BFD on interface Vlan100 
```
sonic# configure terminal
sonic(config)# interface Vlan 100
sonic(conf-if-Vlan100)# ip pim bfd
sonic(conf-if-Vlan100)#
```
## ip pim drpriority 
### Description 
```
Set the DR Priority for the PIM interface. This command a
allows user to set the priority of a node for becoming the DR on a network to
which the interface is attached. A higher value means a higher chances of being
elected.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
ip pim drpriority <drprio>
no ip pim drpriority
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| drprio | 1-4294967295  | Integer  |
### Usage Guidelines 
```
ip pim drpriority <value>
```
### Examples 
#### Set DR priority value of interface Vlan100 to 10 
```
sonic# configure terminal
sonic(config)# interface Vlan 100
sonic(conf-if-Vlan100)# ip pim drpriority
  <1..4294967295>  Range 1_4294967295
sonic(conf-if-Vlan100)# ip pim drpriority 10
sonic(conf-if-Vlan100)#
```
## ip pim drpriority 
### Description 
```
Set the DR Priority for the PIM interface. This command a
allows user to set the priority of a node for becoming the DR on a network to
which the interface is attached. A higher value means a higher chances of being
elected.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
ip pim drpriority <drprio>
no ip pim drpriority
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| drprio | 1-4294967295  | Integer  |
### Usage Guidelines 
```
ip pim drpriority <value>
```
### Examples 
#### Set DR priority value of interface Vlan100 to 10 
```
sonic# configure terminal
sonic(config)# interface Vlan 100
sonic(conf-if-Vlan100)# ip pim drpriority
  <1..4294967295>  Range 1_4294967295
sonic(conf-if-Vlan100)# ip pim drpriority 10
sonic(conf-if-Vlan100)#
```
## ip pim drpriority 
### Description 
```
Set the DR Priority for the PIM interface. This command a
allows user to set the priority of a node for becoming the DR on a network to
which the interface is attached. A higher value means a higher chances of being
elected.
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ip pim drpriority <drprio>
no ip pim drpriority
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| drprio | 1-4294967295  | Integer  |
### Usage Guidelines 
```
ip pim drpriority <value>
```
### Examples 
#### Set DR priority value of interface Vlan100 to 10 
```
sonic# configure terminal
sonic(config)# interface Vlan 100
sonic(conf-if-Vlan100)# ip pim drpriority
  <1..4294967295>  Range 1_4294967295
sonic(conf-if-Vlan100)# ip pim drpriority 10
sonic(conf-if-Vlan100)#
```
## ip pim hello 
### Description 
```
Periodic interval for Hello messages to keep the PIM neighbor session alive.
This configuration internally configures the default hold-time (3.5 *
Hello-interval); the period to keep the PIM neighbor session alive
without receiving hello messages from that particular neighbor.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
ip pim hello <hello>
no ip pim hello
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| hello |   | Integer  |
### Usage Guidelines 
```
ip pim hello <value>
```
### Examples 
#### Set the Hello interval of interface Vlan100 to 30 
```
sonic# configure terminal
sonic(conf-if-Vlan100)# ip pim hello
  <1..180>  Range 1 to 180 in seconds
sonic(conf-if-Vlan100)# ip pim hello 30
sonic(conf-if-Vlan100)#
```
## ip pim hello 
### Description 
```
Periodic interval for Hello messages to keep the PIM neighbor session alive.
This configuration internally configures the default hold-time (3.5 *
Hello-interval); the period to keep the PIM neighbor session alive
without receiving hello messages from that particular neighbor.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
ip pim hello <hello>
no ip pim hello
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| hello |   | Integer  |
### Usage Guidelines 
```
ip pim hello <value>
```
### Examples 
#### Set the Hello interval of interface Vlan100 to 30 
```
sonic# configure terminal
sonic(conf-if-Vlan100)# ip pim hello
  <1..180>  Range 1 to 180 in seconds
sonic(conf-if-Vlan100)# ip pim hello 30
sonic(conf-if-Vlan100)#
```
## ip pim hello 
### Description 
```
Periodic interval for Hello messages to keep the PIM neighbor session alive.
This configuration internally configures the default hold-time (3.5 *
Hello-interval); the period to keep the PIM neighbor session alive
without receiving hello messages from that particular neighbor.
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ip pim hello <hello>
no ip pim hello
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| hello |   | Integer  |
### Usage Guidelines 
```
ip pim hello <value>
```
### Examples 
#### Set the Hello interval of interface Vlan100 to 30 
```
sonic# configure terminal
sonic(conf-if-Vlan100)# ip pim hello
  <1..180>  Range 1 to 180 in seconds
sonic(conf-if-Vlan100)# ip pim hello 30
sonic(conf-if-Vlan100)#
```
## ip pim sparse-mode 
### Description 
```
Enable PIM sparse-mode on the given interface
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
ip pim sparse-mode
no ip pim sparse-mode
```
### Usage Guidelines 
```
ip pim sparse-mode
```
### Examples 
#### Enable PIM sparse-mode on interface Vlan100 
```
sonic# configure terminal
sonic(config)# interface Vlan 100
sonic(conf-if-Vlan100)# ip pim sparse-mode
sonic(conf-if-Vlan100)#
```
## ip pim sparse-mode 
### Description 
```
Enable PIM sparse-mode on the given interface
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
ip pim sparse-mode
no ip pim sparse-mode
```
### Usage Guidelines 
```
ip pim sparse-mode
```
### Examples 
#### Enable PIM sparse-mode on interface Vlan100 
```
sonic# configure terminal
sonic(config)# interface Vlan 100
sonic(conf-if-Vlan100)# ip pim sparse-mode
sonic(conf-if-Vlan100)#
```
## ip pim sparse-mode 
### Description 
```
Enable PIM sparse-mode on the given interface
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ip pim sparse-mode
no ip pim sparse-mode
```
### Usage Guidelines 
```
ip pim sparse-mode
```
### Examples 
#### Enable PIM sparse-mode on interface Vlan100 
```
sonic# configure terminal
sonic(config)# interface Vlan 100
sonic(conf-if-Vlan100)# ip pim sparse-mode
sonic(conf-if-Vlan100)#
```
## ip prefix-list 
### Description 
```
Build a prefix list 
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
ip prefix-list <prefix-name> { seq { <seq-no> { { permit { <ipv4-prefix> { [ ge <ge-min-prefix-length> ] } { [ le <le-max-prefix-length> ] } } } | { deny { <ipv4-prefix> { [ ge <ge-min-prefix-length> ] } { [ le <le-max-prefix-length> ] } } } } } }
no ip prefix-list <prefix-name> { [ seq { <seq-no> { { permit { <ipv4-prefix> { [ ge <ge-min-prefix-length> ] } { [ le <le-max-prefix-length> ] } } } | { deny { <ipv4-prefix> { [ ge <ge-min-prefix-length> ] } { [ le <le-max-prefix-length> ] } } } } } ] }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| prefix-name | WORD  | String  |
| seq-no | 1-4294967295  | Integer  |
| ipv4-prefix | A.B.C.D/mask  | String  |
| ge-min-prefix-length |   | Integer  |
| le-max-prefix-length |   | Integer  |
## ip route 
### Description 
```
Specify static route 
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
ip route <prefix> { { interface { <ifname> { { [ nexthop-vrf { <next-hop-vrf> { [ tag { <tag-val> [ <pref> ] } ] } [ <pref> ] } ] } | { [ tag { <tag-val> [ <pref> ] } ] } | [ <pref> ] ] } } } | { blackhole { [ tag { <tag-val> [ <pref> ] } ] } [ <pref> ] } | { <next-hop-addr> { { [ interface { <ifname> { { [ nexthop-vrf { <next-hop-vrf> { [ tag { <tag-val> { [ track { <trackid> [ <pref> ] } ] } [ <pref> ] } ] } [ <pref> ] { [ track { <trackid> [ <pref> ] } ] } } ] } | { [ track { <trackid> [ <pref> ] } ] } | { [ tag { <tag-val> { [ track { <trackid> [ <pref> ] } ] } [ <pref> ] } ] } | [ <pref> ] ] } } ] } | { [ tag { <tag-val> { [ track { <trackid> [ <pref> ] } ] } [ <pref> ] } ] } | [ <pref> ] | { [ track { <trackid> [ <pref> ] } ] } | { [ nexthop-vrf { <next-hop-vrf> { [ tag { <tag-val> { [ track { <trackid> [ <pref> ] } ] } [ <pref> ] } ] } [ <pref> ] { [ track { <trackid> [ <pref> ] } ] } } ] } ] } } }
no ip route <prefix> { { interface { <ifname> { [ nexthop-vrf <next-hop-vrf> ] } } } | blackhole | { <next-hop-addr> { { [ interface { <ifname> { [ nexthop-vrf <next-hop-vrf> ] } } ] } | { [ nexthop-vrf <next-hop-vrf> ] } ] } } }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| prefix | A.B.C.D/mask  | String  |
| ifname | Interface Type - Ranges  |   |
| next-hop-vrf | WORD  | String  |
| tag-val | 1-4294967295  | Integer  |
| pref |   | Integer  |
| next-hop-addr | A.B.C.D  | String  |
| trackid |   | Integer  |
## ip route vrf 
### Description 
```
Configure IP Route for a VRF instance 
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
ip route vrf <vrfname> { <prefix> { { interface { <ifname> { { [ nexthop-vrf { <next-hop-vrf> { [ tag { <tag-val> [ <pref> ] } ] } [ <pref> ] } ] } | { [ tag { <tag-val> [ <pref> ] } ] } | [ <pref> ] ] } } } | { blackhole { [ tag { <tag-val> [ <pref> ] } ] } [ <pref> ] } | { <next-hop-addr> { { [ interface { <ifname> { { [ nexthop-vrf { <next-hop-vrf> { [ tag { <tag-val> { [ track { <trackid> [ <pref> ] } ] } [ <pref> ] } ] } [ <pref> ] { [ track { <trackid> [ <pref> ] } ] } } ] } | { [ track { <trackid> [ <pref> ] } ] } | { [ tag { <tag-val> { [ track { <trackid> [ <pref> ] } ] } [ <pref> ] } ] } | [ <pref> ] ] } } ] } | { [ tag { <tag-val> { [ track { <trackid> [ <pref> ] } ] } [ <pref> ] } ] } | [ <pref> ] | { [ track { <trackid> [ <pref> ] } ] } | { [ nexthop-vrf { <next-hop-vrf> { [ tag { <tag-val> { [ track { <trackid> [ <pref> ] } ] } [ <pref> ] } ] } [ <pref> ] { [ track { <trackid> [ <pref> ] } ] } } ] } ] } } } }
no ip route vrf <vrfname> { <prefix> { { interface { <ifname> { [ nexthop-vrf <next-hop-vrf> ] } } } | blackhole | { <next-hop-addr> { { [ interface { <ifname> { [ nexthop-vrf <next-hop-vrf> ] } } ] } | { [ nexthop-vrf <next-hop-vrf> ] } ] } } } }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrfname | WORD  | String  |
| prefix | A.B.C.D/mask  | String  |
| ifname | Interface Type - Ranges  |   |
| next-hop-vrf | WORD  | String  |
| tag-val | 1-4294967295  | Integer  |
| pref |   | Integer  |
| next-hop-addr | A.B.C.D  | String  |
| trackid |   | Integer  |
## ip sla 
### Description 
```
Configure IP SLA instance
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
ip sla <sla-id>
no ip sla <sla-id>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| sla-id |   | Integer  |
### Examples 
#### Configure IP SLA instance 
```
sonic(config)# ip sla 10
sonic(conf-ipsla-10)#
```
## ip unnumbered 
### Description 
```
Configure IPv4 unnumbered interface by borrowing IPv4 address from the Donor interface.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
ip unnumbered <donor-interface>
no ip unnumbered
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| donor-interface |   |   |
### Usage Guidelines 
```
Use this command to configure IPv4 unnumbered interface at the interface level.
```
### Examples 
#### Configure IPv4 unnumbered interface 
```
sonic-cli(config)# interface Ethernet 0
sonic-cli(conf-if-Ethernet0)# ip unnumbered Loopback1
```
### Features this CLI belongs to 
*  IPv4 Unnumbered Interface
## ip unnumbered 
### Description 
```
Configure IPv4 unnumbered interface by borrowing IPv4 address from the Donor interface.
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ip unnumbered <donor-interface>
no ip unnumbered
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| donor-interface |   |   |
### Usage Guidelines 
```
Use this command to configure IPv4 unnumbered interface at the interface level.
```
### Examples 
#### Configure IPv4 unnumbered interface 
```
sonic-cli(config)# interface Ethernet 0
sonic-cli(conf-if-Ethernet0)# ip unnumbered Loopback1
```
### Features this CLI belongs to 
*  IPv4 Unnumbered Interface
## ip unnumbered 
### Description 
```
Configure IPv4 unnumbered interface by borrowing IPv4 address from the Donor interface.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
ip unnumbered <donor-interface>
no ip unnumbered
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| donor-interface |   |   |
### Usage Guidelines 
```
Use this command to configure IPv4 unnumbered interface at the interface level.
```
### Examples 
#### Configure IPv4 unnumbered interface 
```
sonic-cli(config)# interface Ethernet 0
sonic-cli(conf-if-Ethernet0)# ip unnumbered Loopback1
```
### Features this CLI belongs to 
*  IPv4 Unnumbered Interface
## ip vrf 
### Description 
```
Configure a data VRF.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
ip vrf <vrf-name>
no ip vrf <vrf-name>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrf-name | WORD  | String  |
### Usage Guidelines 
```
sonic(config)# ip vrf VRF
VRF: Name of VRF (Max: 15 characters, prefixed with Vrf)
```
### Examples 
#### Configure a data VRF 
```
sonic# configure terminal
sonic(config)# ip vrf Vrf_red
```
## ip vrf forwarding 
### Description 
```
Assign an interface to a VRF. VRF is data/mgmt VRF and interface can be
ethernet, loopback, portchannel or vlan interface.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
ip vrf forwarding { mgmt | <vrf-name> }
no ip vrf forwarding { mgmt | <vrf-name> }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrf-name | WORD  | String  |
### Usage Guidelines 
```
sonic(conf-if-INTF)# ip vrf forwarding VRF
VRF:  Name of VRF
INTF: View identifier of Ethernet, Loopback, PortChannel or Vlan interface
```
### Examples 
#### Assign interface to VRF 
```
sonic(config)# interface Ethernet 8
sonic(conf-if-Ethernet8)# ip vrf forwarding Vrf_red
sonic(config)# interface Loopback 8
sonic(conf-if-lo8)# ip vrf forwarding Vrf_red
sonic(config)# interface PortChannel 8
sonic(conf-if-po8)# ip vrf forwarding Vrf_red
sonic(config)# interface Vlan 8
sonic(conf-if-Vlan8)# ip vrf forwarding Vrf_red
sonic(config)# interface Vlan 100
sonic(conf-if-Vlan8)# ip vrf forwarding mgmt
```
## ip vrf forwarding 
### Description 
```
Configure forwarding table 
```
### Parent Commands (Modes) 
```
interface range iface_range_num
```
### Syntax 
```
ip vrf forwarding { mgmt | <vrf-name> }
no ip vrf forwarding { mgmt | <vrf-name> }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrf-name | WORD  | String  |
## ip vrf forwarding 
### Description 
```
Assign an interface to a VRF. VRF is data/mgmt VRF and interface can be
ethernet, loopback, portchannel or vlan interface.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
ip vrf forwarding { mgmt | <vrf-name> }
no ip vrf forwarding { mgmt | <vrf-name> }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrf-name | WORD  | String  |
### Usage Guidelines 
```
sonic(conf-if-INTF)# ip vrf forwarding VRF
VRF:  Name of VRF
INTF: View identifier of Ethernet, Loopback, PortChannel or Vlan interface
```
### Examples 
#### Assign interface to VRF 
```
sonic(config)# interface Ethernet 8
sonic(conf-if-Ethernet8)# ip vrf forwarding Vrf_red
sonic(config)# interface Loopback 8
sonic(conf-if-lo8)# ip vrf forwarding Vrf_red
sonic(config)# interface PortChannel 8
sonic(conf-if-po8)# ip vrf forwarding Vrf_red
sonic(config)# interface Vlan 8
sonic(conf-if-Vlan8)# ip vrf forwarding Vrf_red
sonic(config)# interface Vlan 100
sonic(conf-if-Vlan8)# ip vrf forwarding mgmt
```
## ip vrf forwarding 
### Description 
```
Configure forwarding table 
```
### Parent Commands (Modes) 
```
interface range create po_range_num { [ mode <PoMode> ] } { [ min-links <min-links-value> ] } [ fallback ] [ fast_rate ]
interface range po_range_num
```
### Syntax 
```
ip vrf forwarding { mgmt | <vrf-name> }
no ip vrf forwarding { mgmt | <vrf-name> }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrf-name | WORD  | String  |
## ip vrf forwarding 
### Description 
```
Assign an interface to a VRF. VRF is data/mgmt VRF and interface can be
ethernet, loopback, portchannel or vlan interface.
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ip vrf forwarding { mgmt | <vrf-name> }
no ip vrf forwarding { mgmt | <vrf-name> }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrf-name | WORD  | String  |
### Usage Guidelines 
```
sonic(conf-if-INTF)# ip vrf forwarding VRF
VRF:  Name of VRF
INTF: View identifier of Ethernet, Loopback, PortChannel or Vlan interface
```
### Examples 
#### Assign interface to VRF 
```
sonic(config)# interface Ethernet 8
sonic(conf-if-Ethernet8)# ip vrf forwarding Vrf_red
sonic(config)# interface Loopback 8
sonic(conf-if-lo8)# ip vrf forwarding Vrf_red
sonic(config)# interface PortChannel 8
sonic(conf-if-po8)# ip vrf forwarding Vrf_red
sonic(config)# interface Vlan 8
sonic(conf-if-Vlan8)# ip vrf forwarding Vrf_red
sonic(config)# interface Vlan 100
sonic(conf-if-Vlan8)# ip vrf forwarding mgmt
```
## ip vrf forwarding 
### Description 
```
Configure forwarding table 
```
### Parent Commands (Modes) 
```
interface range create vlan_range_num
interface range vlan_range_num
```
### Syntax 
```
ip vrf forwarding { mgmt | <vrf-name> }
no ip vrf forwarding { mgmt | <vrf-name> }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrf-name | WORD  | String  |
## ip vrf forwarding 
### Description 
```
Assign an interface to a VRF. VRF is data/mgmt VRF and interface can be
ethernet, loopback, portchannel or vlan interface.
```
### Parent Commands (Modes) 
```
interface Loopback <lo-id>
```
### Syntax 
```
ip vrf forwarding { mgmt | <vrf-name> }
no ip vrf forwarding { mgmt | <vrf-name> }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrf-name | WORD  | String  |
### Usage Guidelines 
```
sonic(conf-if-INTF)# ip vrf forwarding VRF
VRF:  Name of VRF
INTF: View identifier of Ethernet, Loopback, PortChannel or Vlan interface
```
### Examples 
#### Assign interface to VRF 
```
sonic(config)# interface Ethernet 8
sonic(conf-if-Ethernet8)# ip vrf forwarding Vrf_red
sonic(config)# interface Loopback 8
sonic(conf-if-lo8)# ip vrf forwarding Vrf_red
sonic(config)# interface PortChannel 8
sonic(conf-if-po8)# ip vrf forwarding Vrf_red
sonic(config)# interface Vlan 8
sonic(conf-if-Vlan8)# ip vrf forwarding Vrf_red
sonic(config)# interface Vlan 100
sonic(conf-if-Vlan8)# ip vrf forwarding mgmt
```
## ip vrf mgmt 
### Description 
```
Configure management VRF.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
ip vrf mgmt
no ip vrf mgmt
```
### Usage Guidelines 
```
sonic(config)# ip vrf mgmt
```
### Examples 
#### Configure management VRF 
```
sonic# configure terminal
sonic(config)# ip vrf mgmt
```
## ipv6 access-group 
### Description 
```
Configure Internet Protocol v6 (IPv6) ACL 
```
### Parent Commands (Modes) 
```
line vty
```
### Syntax 
```
ipv6 access-group <access-list-name> in
no ipv6 access-group <access-list-name> in
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| access-list-name | WORD  | String  |
## ipv6 access-group 
### Description 
```
Apply IPv6 ACL to an interface.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
ipv6 access-group <access-list-name> { in | out }
no ipv6 access-group <access-list-name> { in | out }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| access-list-name | WORD  | String  |
### Usage Guidelines 
```
ACL must be created first and must be of type IPv6 to be applied. Only 1 ACL of a given type can be applied per interface and per direction.
```
### Examples 
#### Apply IPv6 ACL to an interface 
```
sonic(conf-if-Eth1/1)# ipv6 access-group ipv6acl-example in
```
## ipv6 access-group 
### Description 
```
Apply IPv6 ACL to an interface.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
ipv6 access-group <access-list-name> { in | out }
no ipv6 access-group <access-list-name> { in | out }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| access-list-name | WORD  | String  |
### Usage Guidelines 
```
ACL must be created first and must be of type IPv6 to be applied. Only 1 ACL of a given type can be applied per interface and per direction.
```
### Examples 
#### Apply IPv6 ACL to an interface 
```
sonic(conf-if-Eth1/1)# ipv6 access-group ipv6acl-example in
```
## ipv6 access-group 
### Description 
```
Apply IPv6 ACL to an interface.
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ipv6 access-group <access-list-name> { in | out }
no ipv6 access-group <access-list-name> { in | out }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| access-list-name | WORD  | String  |
### Usage Guidelines 
```
ACL must be created first and must be of type IPv6 to be applied. Only 1 ACL of a given type can be applied per interface and per direction.
```
### Examples 
#### Apply IPv6 ACL to an interface 
```
sonic(conf-if-Eth1/1)# ipv6 access-group ipv6acl-example in
```
## ipv6 access-group 
### Description 
```
Apply IPv6 ACL globally
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
ipv6 access-group <access-list-name> { in | out }
no ipv6 access-group <access-list-name> { in | out }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| access-list-name | WORD  | String  |
### Usage Guidelines 
```
ACL must be created first and must be of type IPv6 to be applied. Only 1 ACL of a given type can be applied globally per direction.
```
### Examples 
#### Apply IPv4 ACL globally 
```
sonic(config)# ipv6 access-group ipv6acl-example out
```
## ipv6 access-list 
### Description 
```
Create IPv6 ACL
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
ipv6 access-list <access-list-name>
no ipv6 access-list <access-list-name>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| access-list-name | WORD  | String  |
### Usage Guidelines 
```
ACL name can be of maximum 63 characters. The name must begin with A-Z, a-z or 0-9. Underscore and hypens can be used except as the first character. ACL name must be unique across all ACL types.
```
### Examples 
#### Create IPv6 ACL 
```
sonic(config)# ipv6 access-list ipv6acl-example
```
## ipv6 address 
### Description 
```
IPv6 address 
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
ipv6 address <addr>
no ipv6 address [ <addr> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| addr | A::B/mask  | String  |
## ipv6 address 
### Description 
```
IPv6 address 
```
### Parent Commands (Modes) 
```
interface Management <mgmt-if-id>
```
### Syntax 
```
ipv6 address <addr> [ gwaddr <gw_addr> ]
no ipv6 address [ <addr> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| addr | A::B/mask  | String  |
| gw_addr | A::B  | String  |
## ipv6 address 
### Description 
```
IPv6 address 
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
ipv6 address <addr>
no ipv6 address [ <addr> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| addr | A::B/mask  | String  |
## ipv6 address 
### Description 
```
IPv6 address 
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ipv6 address <addr>
no ipv6 address [ <addr> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| addr | A::B/mask  | String  |
## ipv6 address 
### Description 
```
IPv6 address 
```
### Parent Commands (Modes) 
```
interface Loopback <lo-id>
```
### Syntax 
```
ipv6 address <addr>
no ipv6 address [ <addr> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| addr | A::B/mask  | String  |
## ipv6 anycast-address 
### Description 
```
Configures IPv6 Static Anycast Gateway Address for an Interface.
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ipv6 anycast-address <anycast-addr>
no ipv6 anycast-address <anycast-addr>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| anycast-addr | A::B/mask  | String  |
### Examples 
#### Configures IPv6 Static Anycast Gateway Address for an Interface 
```
sonic(conf-if-Vlan5)# ipv6 anycast-address 50::1/64
```
## ipv6 anycast-address 

### Description 

```
Enable/Disable IPv6 Static Anycast Gateway functionality. By default it is enabled.


```
### Parent Commands (Modes) 

```
configure terminal

```
### Syntax 

```
ipv6 anycast-address { enable | disable }

```
### Examples 
#### Enable IPv6 Static Anycast Gateway 

```
sonic(config)# ipv6 anycast-address enable


```
#### Disable IPv6 Static Anycast Gateway 

```
sonic(config)# ipv6 anycast-address disable


```
## ipv6 dhcp-relay 
### Description 
```
Configure DHCPv6 relay on an interface.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
ipv6 dhcp-relay <ipaddr1> { { [ vrf-name <vrfName> ] } { [ <ipaddr2> { [ <ipaddr3> [ <ipaddr4> ] ] } ] } }
no ipv6 dhcp-relay [ <ipaddr1> { [ <ipaddr2> { [ <ipaddr3> [ <ipaddr4> ] ] } ] } ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| ipaddr1 | A::B  | String  |
| vrfName | WORD  | String  |
| ipaddr2 | A::B  | String  |
| ipaddr3 | A::B  | String  |
| ipaddr4 | A::B  | String  |
### Usage Guidelines 
```
Use this command to configure a DHCPv6 relay on an interface.
```
### Examples 
#### Configure IP DHCPv6 relay 
```
sonic-cli(config)# interface Ethernet 12
sonic-cli(conf-if-Ethernet0)# ipv6 dhcp-relay 9000::1
```
## ipv6 dhcp-relay 
### Description 
```
Configure DHCPv6 relay on an interface.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
ipv6 dhcp-relay <ipaddr1> { { [ vrf-name <vrfName> ] } { [ <ipaddr2> { [ <ipaddr3> [ <ipaddr4> ] ] } ] } }
no ipv6 dhcp-relay [ <ipaddr1> { [ <ipaddr2> { [ <ipaddr3> [ <ipaddr4> ] ] } ] } ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| ipaddr1 | A::B  | String  |
| vrfName | WORD  | String  |
| ipaddr2 | A::B  | String  |
| ipaddr3 | A::B  | String  |
| ipaddr4 | A::B  | String  |
### Usage Guidelines 
```
Use this command to configure a DHCPv6 relay on an interface.
```
### Examples 
#### Configure IP DHCPv6 relay 
```
sonic-cli(config)# interface Ethernet 12
sonic-cli(conf-if-Ethernet0)# ipv6 dhcp-relay 9000::1
```
## ipv6 dhcp-relay 
### Description 
```
Configure DHCPv6 relay on an interface.
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ipv6 dhcp-relay <ipaddr1> { { [ vrf-name <vrfName> ] } { [ <ipaddr2> { [ <ipaddr3> [ <ipaddr4> ] ] } ] } }
no ipv6 dhcp-relay [ <ipaddr1> { [ <ipaddr2> { [ <ipaddr3> [ <ipaddr4> ] ] } ] } ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| ipaddr1 | A::B  | String  |
| vrfName | WORD  | String  |
| ipaddr2 | A::B  | String  |
| ipaddr3 | A::B  | String  |
| ipaddr4 | A::B  | String  |
### Usage Guidelines 
```
Use this command to configure a DHCPv6 relay on an interface.
```
### Examples 
#### Configure IP DHCPv6 relay 
```
sonic-cli(config)# interface Ethernet 12
sonic-cli(conf-if-Ethernet0)# ipv6 dhcp-relay 9000::1
```
## ipv6 dhcp-relay max-hop-count 
### Description 
```
Configure the maximum hop count for the DHCPv6 packet.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
ipv6 dhcp-relay max-hop-count <hop-count>
no ipv6 dhcp-relay max-hop-count
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| hop-count |   | Integer  |
### Usage Guidelines 
```
Use this command to configure the maximum hop count for the DHCPv6 packet.
```
### Examples 
#### Configure dhcpv6 relay max hop count 
```
sonic-cli(config)# interface Ethernet 12
sonic-cli(conf-if-Ethernet0)# ipv6 dhcp-relay max-hop-count 9
```
## ipv6 dhcp-relay max-hop-count 
### Description 
```
Configure the maximum hop count for the DHCPv6 packet.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
ipv6 dhcp-relay max-hop-count <hop-count>
no ipv6 dhcp-relay max-hop-count
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| hop-count |   | Integer  |
### Usage Guidelines 
```
Use this command to configure the maximum hop count for the DHCPv6 packet.
```
### Examples 
#### Configure dhcpv6 relay max hop count 
```
sonic-cli(config)# interface Ethernet 12
sonic-cli(conf-if-Ethernet0)# ipv6 dhcp-relay max-hop-count 9
```
## ipv6 dhcp-relay max-hop-count 
### Description 
```
Configure the maximum hop count for the DHCPv6 packet.
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ipv6 dhcp-relay max-hop-count <hop-count>
no ipv6 dhcp-relay max-hop-count
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| hop-count |   | Integer  |
### Usage Guidelines 
```
Use this command to configure the maximum hop count for the DHCPv6 packet.
```
### Examples 
#### Configure dhcpv6 relay max hop count 
```
sonic-cli(config)# interface Ethernet 12
sonic-cli(conf-if-Ethernet0)# ipv6 dhcp-relay max-hop-count 9
```
## ipv6 dhcp-relay source-interface 
### Description 
```
Configure the source IPv6 address to be used for relaying the DHCPv6 packets.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
ipv6 dhcp-relay source-interface <ifName>
no ipv6 dhcp-relay source-interface
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| ifName | String  | String  |
### Usage Guidelines 
```
Use this command to configure the source-interface.
```
### Examples 
#### Configure dhcp relay source interface 
```
sonic-cli(config)# interface Ethernet 12
sonic-cli(conf-if-Ethernet0)# ipv6 dhcp-relay source-interface Ethernet36
```
## ipv6 dhcp-relay source-interface 
### Description 
```
Configure the source IPv6 address to be used for relaying the DHCPv6 packets.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
ipv6 dhcp-relay source-interface <ifName>
no ipv6 dhcp-relay source-interface
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| ifName | String  | String  |
### Usage Guidelines 
```
Use this command to configure the source-interface.
```
### Examples 
#### Configure dhcp relay source interface 
```
sonic-cli(config)# interface Ethernet 12
sonic-cli(conf-if-Ethernet0)# ipv6 dhcp-relay source-interface Ethernet36
```
## ipv6 dhcp-relay source-interface 
### Description 
```
Configure the source IPv6 address to be used for relaying the DHCPv6 packets.
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ipv6 dhcp-relay source-interface <ifName>
no ipv6 dhcp-relay source-interface
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| ifName | String  | String  |
### Usage Guidelines 
```
Use this command to configure the source-interface.
```
### Examples 
#### Configure dhcp relay source interface 
```
sonic-cli(config)# interface Ethernet 12
sonic-cli(conf-if-Ethernet0)# ipv6 dhcp-relay source-interface Ethernet36
```
## ipv6 dhcp-relay vrf-select 
### Description 
```
Configure the VRF selection option.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
ipv6 dhcp-relay vrf-select
no ipv6 dhcp-relay vrf-select
```
### Usage Guidelines 
```
Use this command to configure the VRF selection option.
```
### Examples 
#### Configure dhcp relay vrf selection 
```
sonic-cli(config)# interface Ethernet 12
sonic-cli(conf-if-Ethernet0)# ipv6 dhcp-relay vrf-select
```
## ipv6 dhcp-relay vrf-select 
### Description 
```
Configure the VRF selection option.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
ipv6 dhcp-relay vrf-select
no ipv6 dhcp-relay vrf-select
```
### Usage Guidelines 
```
Use this command to configure the VRF selection option.
```
### Examples 
#### Configure dhcp relay vrf selection 
```
sonic-cli(config)# interface Ethernet 12
sonic-cli(conf-if-Ethernet0)# ipv6 dhcp-relay vrf-select
```
## ipv6 dhcp-relay vrf-select 
### Description 
```
Configure the VRF selection option.
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ipv6 dhcp-relay vrf-select
no ipv6 dhcp-relay vrf-select
```
### Usage Guidelines 
```
Use this command to configure the VRF selection option.
```
### Examples 
#### Configure dhcp relay vrf selection 
```
sonic-cli(config)# interface Ethernet 12
sonic-cli(conf-if-Ethernet0)# ipv6 dhcp-relay vrf-select
```
## ipv6 enable 
### Description 
```
Enable IPv6 
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
ipv6 enable
no ipv6 enable
```
## ipv6 enable 
### Description 
```
Enable IPv6 
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
ipv6 enable
no ipv6 enable
```
## ipv6 enable 
### Description 
```
Enable IPv6 
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ipv6 enable
no ipv6 enable
```
## ipv6 enable 
### Description 
```
Enable IPv6 
```
### Parent Commands (Modes) 
```
interface Loopback <lo-id>
```
### Syntax 
```
ipv6 enable
no ipv6 enable
```
## ipv6 enable 
### Description 
```
Enable IPv6 
```
### Parent Commands (Modes) 
```
interface range iface_range_num
```
### Syntax 
```
ipv6 enable
no ipv6 enable
```
## ipv6 enable 
### Description 
```
Enable IPv6 
```
### Parent Commands (Modes) 
```
interface range create vlan_range_num
interface range vlan_range_num
```
### Syntax 
```
ipv6 enable
no ipv6 enable
```
## ipv6 enable 
### Description 
```
Enable IPv6 
```
### Parent Commands (Modes) 
```
interface range create po_range_num { [ mode <PoMode> ] } { [ min-links <min-links-value> ] } [ fallback ] [ fast_rate ]
interface range po_range_num
```
### Syntax 
```
ipv6 enable
no ipv6 enable
```
## ipv6 nd 
### Description 
```
Configures time in seconds for IPv6 ND cache entry to expire.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
ipv6 nd cache { expire <value> }
no ipv6 nd cache expire
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| value | value in seconds  | Integer  |
### Usage Guidelines 
```
Use this command to configure IPv6 ND cache entry timeout.
```
### Examples 
#### Configure IPv6 ND timeout 
```
sonic-cli(config)# ipv6 nd cache expire 500
```
## ipv6 neighbor 
### Description 
```
Configure static ND.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
ipv6 neighbor <static-ip> <neigh>
no ipv6 neighbor <static-ip> <neigh>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| static-ip | A::B  | String  |
| neigh | nn:nn:nn:nn:nn:nn  | String  |
### Usage Guidelines 
```
Use this command to configure static ND.
```
### Examples 
#### Configure static ARP 
```
sonic-cli(config)# interface Ethernet 0
sonic-cli(conf-if-Ethernet0)# ip neighbor ip_addr mac
```
## ipv6 neighbor 
### Description 
```
Configure static ND.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
ipv6 neighbor <static-ip> <neigh>
no ipv6 neighbor <static-ip> <neigh>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| static-ip | A.B.C.D or A:B:C:D:E:F:G:H  | String  |
| neigh | nn:nn:nn:nn:nn:nn  | String  |
### Usage Guidelines 
```
Use this command to configure static ND.
```
### Examples 
#### Configure static ARP 
```
sonic-cli(config)# interface Ethernet 0
sonic-cli(conf-if-Ethernet0)# ip neighbor ip_addr mac
```
## ipv6 neighbor 
### Description 
```
Configure static ND.
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
ipv6 neighbor <static-ip> <neigh>
no ipv6 neighbor <static-ip> <neigh>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| static-ip | A::B  | String  |
| neigh | nn:nn:nn:nn:nn:nn  | String  |
### Usage Guidelines 
```
Use this command to configure static ND.
```
### Examples 
#### Configure static ARP 
```
sonic-cli(config)# interface Ethernet 0
sonic-cli(conf-if-Ethernet0)# ip neighbor ip_addr mac
```
## ipv6 prefix-list 
### Description 
```
Build a prefix list 
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
ipv6 prefix-list <prefix-name> { seq { <seq-no> { { permit { <ipv6-prefix> { [ ge <ge-min-prefix-length> ] } { [ le <le-max-prefix-length> ] } } } | { deny { <ipv6-prefix> { [ ge <ge-min-prefix-length> ] } { [ le <le-max-prefix-length> ] } } } } } }
no ipv6 prefix-list <prefix-name> { [ seq { <seq-no> { { permit { <ipv6-prefix> { [ ge <ge-min-prefix-length> ] } { [ le <le-max-prefix-length> ] } } } | { deny { <ipv6-prefix> { [ ge <ge-min-prefix-length> ] } { [ le <le-max-prefix-length> ] } } } } } ] }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| prefix-name | WORD  | String  |
| seq-no | 1-4294967295  | Integer  |
| ipv6-prefix | A::B/mask  | String  |
| ge-min-prefix-length |   | Integer  |
| le-max-prefix-length |   | Integer  |
## ipv6 route 
### Description 
```
Specify static route 
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
ipv6 route <prefix> { { interface { <ifname> { { [ nexthop-vrf { <next-hop-vrf> { [ tag { <tag-val> [ <pref> ] } ] } [ <pref> ] } ] } | { [ tag { <tag-val> [ <pref> ] } ] } | [ <pref> ] ] } } } | { blackhole { [ tag { <tag-val> [ <pref> ] } ] } [ <pref> ] } | { <next-hop-addr> { { [ interface { <ifname> { { [ nexthop-vrf { <next-hop-vrf> { [ tag { <tag-val> { [ track { <trackid> [ <pref> ] } ] } [ <pref> ] } ] } [ <pref> ] { [ track { <trackid> [ <pref> ] } ] } } ] } | { [ track { <trackid> [ <pref> ] } ] } | { [ tag { <tag-val> { [ track { <trackid> [ <pref> ] } ] } [ <pref> ] } ] } | [ <pref> ] ] } } ] } | { [ tag { <tag-val> { [ track { <trackid> [ <pref> ] } ] } [ <pref> ] } ] } | [ <pref> ] | { [ track { <trackid> [ <pref> ] } ] } | { [ nexthop-vrf { <next-hop-vrf> { [ tag { <tag-val> { [ track { <trackid> [ <pref> ] } ] } [ <pref> ] } ] } [ <pref> ] { [ track { <trackid> [ <pref> ] } ] } } ] } ] } } }
no ipv6 route <prefix> { { interface { <ifname> { [ nexthop-vrf <next-hop-vrf> ] } } } | blackhole | { <next-hop-addr> { { [ interface { <ifname> { [ nexthop-vrf <next-hop-vrf> ] } } ] } | { [ nexthop-vrf <next-hop-vrf> ] } ] } } }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| prefix | A::B/mask  | String  |
| ifname | Interface Type - Ranges  |   |
| next-hop-vrf | WORD  | String  |
| tag-val | 1-4294967295  | Integer  |
| pref |   | Integer  |
| next-hop-addr | A::B  | String  |
| trackid |   | Integer  |
## ipv6 route vrf 
### Description 
```
Configure IP Route for a VRF instance 
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
ipv6 route vrf <vrfname> { <prefix> { { interface { <ifname> { { [ nexthop-vrf { <next-hop-vrf> { [ tag { <tag-val> [ <pref> ] } ] } [ <pref> ] } ] } | { [ tag { <tag-val> [ <pref> ] } ] } | [ <pref> ] ] } } } | { blackhole { [ tag { <tag-val> [ <pref> ] } ] } [ <pref> ] } | { <next-hop-addr> { { [ interface { <ifname> { { [ nexthop-vrf { <next-hop-vrf> { [ tag { <tag-val> { [ track { <trackid> [ <pref> ] } ] } [ <pref> ] } ] } [ <pref> ] { [ track { <trackid> [ <pref> ] } ] } } ] } | { [ track { <trackid> [ <pref> ] } ] } | { [ tag { <tag-val> { [ track { <trackid> [ <pref> ] } ] } [ <pref> ] } ] } | [ <pref> ] ] } } ] } | { [ tag { <tag-val> { [ track { <trackid> [ <pref> ] } ] } [ <pref> ] } ] } | [ <pref> ] | { [ track { <trackid> [ <pref> ] } ] } | { [ nexthop-vrf { <next-hop-vrf> { [ tag { <tag-val> { [ track { <trackid> [ <pref> ] } ] } [ <pref> ] } ] } [ <pref> ] { [ track { <trackid> [ <pref> ] } ] } } ] } ] } } } }
no ipv6 route vrf <vrfname> { <prefix> { { interface { <ifname> { [ nexthop-vrf <next-hop-vrf> ] } } } | blackhole | { <next-hop-addr> { { [ interface { <ifname> { [ nexthop-vrf <next-hop-vrf> ] } } ] } | { [ nexthop-vrf <next-hop-vrf> ] } ] } } } }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrfname | WORD  | String  |
| prefix | A::B/mask  | String  |
| ifname | Interface Type - Ranges  |   |
| next-hop-vrf | WORD  | String  |
| tag-val | 1-4294967295  | Integer  |
| pref |   | Integer  |
| next-hop-addr | A::B  | String  |
| trackid |   | Integer  |
## kdump enable 

### Description 

```
Enable or disable KDUMP operation. These commands require a reboot to complete.


```
### Parent Commands (Modes) 

```
configure terminal

```
### Syntax 

```
kdump enable

```
### Usage Guidelines 

```
Use the command "kdump enable" to enable the kdump operation.
Use the command "no kdump" to disable the kdump operation.


```
### Examples 

```
sonic# configure terminal
sonic(config)# enable kdump
KDUMP configuration has been updated in the startup configuration
Kdump configuration changes will be applied after the system reboots
sonic(config)# no kdump
KDUMP configuration has been updated in the startup configuration
ALERT! A system reboot is highly recommended.
Kdump configuration changes will be applied after the system reboots


```
### Features this CLI belongs to 
*  KDUMP


### Alternate command 
#### click 

```
config kdump enable


```
## kdump memory 
### Description 
```
Set or reset to default the amount of memory reserved for kdump.
These commands require a reboot to complete.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
kdump memory <kdump_memory>
no kdump memory
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| kdump_memory |   | String  |
### Usage Guidelines 
```
Use the commands "kdump memory <X>" or "no kdump memory" to set or reset to default the amount of memory reserved for kdump.
```
### Examples 
```
sonic# configure terminal
sonic(config)# kdump memory 512M
KDUMP configuration has been updated in the startup configuration
kdump updated memory will be only operational after the system reboots
sonic(config)# no kdump memory
```
### Features this CLI belongs to 
*  KDUMP
### Alternate command 
#### click 
```
config kdump memory
```
## kdump num-dumps 
### Description 
```
Set or reset to default value the maximum number of kernel core files stored locally.
These commands typically require a reboot to complete.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
kdump num-dumps <kdump_num_dumps>
no kdump num-dumps
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| kdump_num_dumps | Maximum number of kernel core files stored locally  | Integer  |
### Usage Guidelines 
```
Use the commands "kdump num-dumps <X>" or "no kdump num-dumps" to set or reset to default the maximum number of kernel core files stored locally.
```
### Examples 
```
sonic# configure terminal
sonic(config)# kdump num-dumps 5
sonic(config)# no kdump num-dumps
```
### Features this CLI belongs to 
*  KDUMP
### Alternate command 
#### click 
```
config kdump num_dumps
```
## keepalive-interval 

### Description 

```
Configures MCLAG keepalive interval in seconds


```
### Parent Commands (Modes) 

```
mclag domain <mclag-domain-id>

```
### Syntax 

```
keepalive-interval <KA>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| KA | Keepalive Interval  | Integer  |


### Usage Guidelines 

```
Use this command to change the default MCLAG keepalive interval


```
### Examples 
#### Configures MCLAG session interval to 10 for MCLAG domain 100 

```
sonic-cli(config-mclag-domain-100)#keepalive-interval 10


```
## ldap-server 
### Description 
```
Configure time limit for LDAP global server
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
ldap-server { { timelimit <timelimit_val> } | { bind-timelimit <bind_timelimit_val> } | { idle-timelimit <idle_timelimit_val> } | { retry <retry_val> } | { port <port_val> } | { scope <scope_val> } | { version <ldap_version_val> } | { base <ldap_base_val> } | { ssl <ssl_val> } | { binddn <binddn_val> } | { bindpw <bindpw_val> } | { pam-filter <pam_filter_val> } | { pam-login-attribute <pam_login_val> } | { pam-group-dn <pam_group_val> } | { pam-member-attribute <pam_member_val> } | { sudoers-base <sudoers_val> } | { nss-base-passwd <nss_base_passwd_val> } | { nss-base-group <nss_base_group_val> } | { nss-base-shadow <nss_base_shadow_val> } | { nss-base-netgroup <nss_base_netgroup_val> } | { nss-base-sudoers <nss_sudoers_val> } | { nss-initgroups-ignoreusers <nss-initgroups_val> } }
no ldap-server { timelimit | bind-timelimit | idle-timelimit | retry | port | scope | version | base | ssl | binddn | bindpw | pam-filter | pam-login-attribute | pam-group-dn | pam-member-attribute | sudoers-base | nss-base-passwd | nss-base-group | nss-base-shadow | nss-base-netgroup | nss-base-sudoers | nss-initgroups-ignoreusers }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| timelimit_val |   | Integer  |
| bind_timelimit_val |   | Integer  |
| idle_timelimit_val |   | Integer  |
| retry_val |   | Integer  |
| port_val |   | Integer  |
| scope_val |   | Select [sub(sub) one(one) base(base) ]  |
| ldap_version_val |   | Integer  |
| ldap_base_val | String Base distinguished name  | String  |
| ssl_val |   | Select [on(on) off(off) start_tls(start_tls) ]  |
| binddn_val | String Distinguished name to bind  | String  |
| bindpw_val | String Credentials to bind (Valid Chars: ASCII printable except SPACE)  | String  |
| pam_filter_val | String PAM filter name  | String  |
| pam_login_val | String PAM login attribute (Default: uid)  | String  |
| pam_group_val | String PAM group distinguished name  | String  |
| pam_member_val | String PAM member attribute value  | String  |
| sudoers_val | String Sudo base distinguished name  | String  |
| nss_base_passwd_val | String NSS search base for password map  | String  |
| nss_base_group_val | String NSS search base group  | String  |
| nss_base_shadow_val | String NSS search base for shadow map  | String  |
| nss_base_netgroup_val | String NSS search base for netgroup map  | String  |
| nss_sudoers_val | String NSS search base for sudoers map  | String  |
| nss-initgroups_val | String Init groups ignore users value  | String  |
### Examples 
```
sonic-cli(config)# ldap-server timelimit 13
```
```
sonic-cli(config)# ldap-server bind-timelimit 10
```
```
sonic-cli(config)# ldap-server idle-timelimit 12
```
```
sonic-cli(config)# ldap-server retry 8
```
```
sonic-cli(config)# ldap-server port 81
```
```
sonic-cli(config)# ldap-server scope sub
```
```
sonic-cli(config)# ldap-server version 2
```
```
sonic-cli(config)# ldap-server base basetest
```
```
sonic-cli(config)# ldap-server ssl on
```
```
sonic-cli(config)# ldap-server binddn dnname
```
```
sonic-cli(config)# ldap-server bindpw testpasswd
```
```
sonic-cli(config)# ldap-server pam-filter testfilter
```
```
sonic-cli(config)# ldap-server pam-login-attribute loginattrstring
```
```
sonic-cli(config)# ldap-server pam-group-dn grpdn
```
```
sonic-cli(config)# ldap-server pam-member-attribute attrstring
```
```
sonic-cli(config)# ldap-server sudoers-base dnqrystr
```
```
sonic-cli(config)# ldap-server nss-base-passwd dnsearchstr
```
```
sonic-cli(config)# ldap-server nss-base-group grpmap
```
```
sonic-cli(config)# ldap-server nss-base-shadow grpmap
```
```
sonic-cli(config)# ldap-server nss-base-netgroup netgrpstr
```
```
sonic-cli(config)# ldap-server nss-base-sudoers sudomap
```
```
sonic-cli(config)# ldap-server nss-initgroups-ignoreusers grpstr
```
## ldap-server host 
### Description 
```
Configure host for LDAP server
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
ldap-server host <host_val> [ use-type <use_type_val> ] [ port <server_port_val> ] [ priority <priority_val> ] [ ssl <ssl_val> ] [ retry <retry_val> ]
no ldap-server host <host_val> { [ use-type ] | [ port ] | [ priority ] | [ ssl ] | [ retry ] } ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| host_val | WORD  | String  |
| use_type_val |   | Select [all(all) nss(nss) sudo(sudo) pam(pam) nss_sudo(nss_sudo) nss_pam(nss_pam) sudo_pam(sudo_pam) ]  |
| server_port_val |   | Integer  |
| priority_val |   | Integer  |
| ssl_val |   | Select [on(on) off(off) start_tls(start_tls) ]  |
| retry_val |   | Integer  |
### Examples 
```
sonic-cli(config)# ldap-server host 4.5.6.7 use-type nss port 300 priority 12 ssl on retry 5
```
## ldap-server map 
### Description 
```
Configure LDAP server map
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
ldap-server map { { [ attribute <attribute_from_val> { to <attribute_to_val> } ] } | { [ objectclass <objectclass_from_val> { to <objectclass_to_val> } ] } | { [ default-attribute-value <default_from_val> { to <default_to_val> } ] } | { [ override-attribute-value <override_from_val> { to <override_to_val> } ] } }
no ldap-server map { { [ attribute <attribute_from_val> ] } | { [ objectclass <objectclass_from_val> ] } | { [ default-attribute-value <default_from_val> ] } | { [ override-attribute-value <override_from_val> ] } }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| attribute_from_val | String Attribute map key  | String  |
| attribute_to_val | String Attribute map value  | String  |
| objectclass_from_val | String Objectclass map key  | String  |
| objectclass_to_val | String Objectclass map value  | String  |
| default_from_val | String Default attribute map key  | String  |
| default_to_val | String Default attribute map value  | String  |
| override_from_val | String Override attribute value map key  | String  |
| override_to_val | String Override attribute value map value  | String  |
### Examples 
```
sonic-cli(config)# ldap-server map objectclass objkey to objectVal
```
## ldap-server nss 
### Description 
```
Configure time limit for LDAP NSS server
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
ldap-server nss { { timelimit <timelimit_val> } | { bind-timelimit <bind_timelimit_val> } | { idle-timelimit <idle_timelimit_val> } | { retry <retry_val> } | { port <port_val> } | { scope <scope_val> } | { version <ldap_version_val> } | { base <ldap_base_val> } | { ssl <ssl_val> } | { binddn <binddn_val> } | { bindpw <bindpw_val> } | { nss-base-passwd <nss_base_passwd_val> } | { nss-base-group <nss_base_group_val> } | { nss-base-shadow <nss_base_shadow_val> } | { nss-base-netgroup <nss_base_netgroup_val> } | { nss-base-sudoers <nss_sudoers_val> } | { nss-initgroups-ignoreusers <nss-initgroups_val> } }
no ldap-server nss { timelimit | bind-timelimit | idle-timelimit | retry | port | scope | version | base | ssl | binddn | bindpw | nss-base-passwd | nss-base-group | nss-base-shadow | nss-base-netgroup | nss-base-sudoers | nss-initgroups-ignoreusers }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| timelimit_val |   | Integer  |
| bind_timelimit_val |   | Integer  |
| idle_timelimit_val |   | Integer  |
| retry_val |   | Integer  |
| port_val |   | Integer  |
| scope_val |   | Select [sub(sub) one(one) base(base) ]  |
| ldap_version_val |   | Integer  |
| ldap_base_val | String Base distinguished name  | String  |
| ssl_val |   | Select [on(on) off(off) start_tls(start_tls) ]  |
| binddn_val | String Distinguished name to bind  | String  |
| bindpw_val | String Credentials to bind (Valid Chars: ASCII printable except SPACE)  | String  |
| nss_base_passwd_val | String NSS search base for password map  | String  |
| nss_base_group_val | String NSS search base group  | String  |
| nss_base_shadow_val | String NSS search base for shadow map  | String  |
| nss_base_netgroup_val | String NSS search base for netgroup map  | String  |
| nss_sudoers_val | String NSS search base for sudoers map  | String  |
| nss-initgroups_val | String Init groups ignore users value  | String  |
### Examples 
```
sonic-cli(config)# ldap-server nss timelimit 13
```
```
sonic-cli(config)# ldap-server nss bind-timelimit 10
```
```
sonic-cli(config)# ldap-server nss idle-timelimit 12
```
```
sonic-cli(config)# ldap-server nss retry 8
```
```
sonic-cli(config)# ldap-server nss port 81
```
```
sonic-cli(config)# ldap-server nss scope sub
```
```
sonic-cli(config)# ldap-server nss version 2
```
```
sonic-cli(config)# ldap-server nss base basetest
```
```
sonic-cli(config)# ldap-server nss ssl on
```
```
sonic-cli(config)# ldap-server nss binddn dnname
```
```
sonic-cli(config)# ldap-server nss bindpw testpasswd
```
```
sonic-cli(config)# ldap-server nss nss-base-passwd dnsearchstr
```
```
sonic-cli(config)# ldap-server nss nss-base-group grpmap
```
```
sonic-cli(config)# ldap-server nss nss-base-shadow grpmap
```
```
sonic-cli(config)# ldap-server nss nss-base-netgroup netgrpstr
```
```
sonic-cli(config)# ldap-server nss nss-base-sudoers sudomap
```
```
sonic-cli(config)# ldap-server nss nss-initgroups-ignoreusers grpstr
```
## ldap-server pam 
### Description 
```
Configure time limit for LDAP PAM server
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
ldap-server pam { { timelimit <timelimit_val> } | { bind-timelimit <bind_timelimit_val> } | { retry <retry_val> } | { port <port_val> } | { scope <scope_val> } | { version <ldap_version_val> } | { base <ldap_base_val> } | { ssl <ssl_val> } | { binddn <binddn_val> } | { bindpw <bindpw_val> } | { pam-filter <pam_filter_val> } | { pam-login-attribute <pam_login_val> } | { pam-group-dn <pam_group_val> } | { pam-member-attribute <pam_member_val> } | { nss-base-passwd <nss_base_passwd_val> } }
no ldap-server pam { timelimit | bind-timelimit | retry | port | scope | version | base | ssl | binddn | bindpw | pam-filter | pam-login-attribute | pam-group-dn | pam-member-attribute | nss-base-passwd }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| timelimit_val |   | Integer  |
| bind_timelimit_val |   | Integer  |
| retry_val |   | Integer  |
| port_val |   | Integer  |
| scope_val |   | Select [sub(sub) one(one) base(base) ]  |
| ldap_version_val |   | Integer  |
| ldap_base_val | String Base distinguished name  | String  |
| ssl_val |   | Select [on(on) off(off) start_tls(start_tls) ]  |
| binddn_val | String Distinguished name to bind  | String  |
| bindpw_val | String Credentials to bind (Valid Chars: ASCII printable except SPACE)  | String  |
| pam_filter_val | String PAM filter name  | String  |
| pam_login_val | String PAM login attribute (Default: uid)  | String  |
| pam_group_val | String PAM group distinguished name  | String  |
| pam_member_val | String PAM member attribute value  | String  |
| nss_base_passwd_val | String NSS search base for password map  | String  |
### Examples 
```
sonic-cli(config)# ldap-server pam timelimit 13
```
```
sonic-cli(config)# ldap-server pam bind-timelimit 10
```
```
sonic-cli(config)# ldap-server pam retry 8
```
```
sonic-cli(config)# ldap-server pam port 81
```
```
sonic-cli(config)# ldap-server pam scope sub
```
```
sonic-cli(config)# ldap-server pam version 2
```
```
sonic-cli(config)# ldap-server pam base basetest
```
```
sonic-cli(config)# ldap-server pam ssl on
```
```
sonic-cli(config)# ldap-server pam binddn dnname
```
```
sonic-cli(config)# ldap-server pam bindpw testpasswd
```
```
sonic-cli(config)# ldap-server pam pam-filter testfilter
```
```
sonic-cli(config)# ldap-server pam pam-login-attribute loginattrstring
```
```
sonic-cli(config)# ldap-server pam pam-group-dn grpdn
```
```
sonic-cli(config)# ldap-server pam pam-member-attribute attrstring
```
```
sonic-cli(config)# ldap-server pam nss-base-passwd dnsearchstr
```
## ldap-server source-interface 
### Description 
```
Configure source-interface to be used for LDAP packets
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
ldap-server source-interface { Ethernet | Loopback | Management | PortChannel | Vlan }
no ldap-server source-interface
```
### Examples 
```
sonic(config)# ldap-server source-interface Loopback 3
sonic(config)#
```
## ldap-server sudo 
### Description 
```
Configure time limit for LDAP sudo server
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
ldap-server sudo { { timelimit <timelimit_val> } | { bind-timelimit <bind_timelimit_val> } | { retry <retry_val> } | { port <port_val> } | { version <ldap_version_val> } | { base <ldap_base_val> } | { ssl <ssl_val> } | { binddn <binddn_val> } | { bindpw <bindpw_val> } | { sudoers-base <sudoers_val> } }
no ldap-server sudo { timelimit | bind-timelimit | retry | port | version | base | ssl | binddn | bindpw | sudoers-base }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| timelimit_val |   | Integer  |
| bind_timelimit_val |   | Integer  |
| retry_val |   | Integer  |
| port_val |   | Integer  |
| ldap_version_val |   | Integer  |
| ldap_base_val | String Base distinguished name  | String  |
| ssl_val |   | Select [on(on) off(off) start_tls(start_tls) ]  |
| binddn_val | String Distinguished name to bind  | String  |
| bindpw_val | String Credentials to bind (Valid Chars: ASCII printable except SPACE)  | String  |
| sudoers_val | String Sudo base distinguished name  | String  |
### Examples 
```
sonic-cli(config)# ldap-server sudo timelimit 13
```
```
sonic-cli(config)# ldap-server sudo bind-timelimit 10
```
```
sonic-cli(config)# ldap-server sudo retry 8
```
```
sonic-cli(config)# ldap-server sudo port 81
```
```
sonic-cli(config)# ldap-server sudo scope sub
```
```
sonic-cli(config)# ldap-server sudo version 2
```
```
sonic-cli(config)# ldap-server sudo base basetest
```
```
sonic-cli(config)# ldap-server sudo ssl on
```
```
sonic-cli(config)# ldap-server sudo binddn dnname
```
```
sonic-cli(config)# ldap-server sudo bindpw testpasswd
```
```
sonic-cli(config)# ldap-server sudo sudoers-base dnqrystr
```
## ldap-server vrf 
### Description 
```
Configure VRF tp be used for LDAP server connection
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
ldap-server vrf <vrf_name>
no ldap-server vrf
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrf_name | WORD  | String  |
### Examples 
```
sonic(config)# ldap-server vrf Vrf1
sonic(config)#
```
## line vty 

### Description 

```
Terminal type 


```
### Parent Commands (Modes) 

```
configure terminal

```
### Syntax 

```
line vty

```
## link state track 
### Description 
```
Create a link state tracking group.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
link state track <grp-name>
no link state track <grp-name>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| grp-name | name  | String  |
### Usage Guidelines 
```
Link state tracking group name can be of maximum 63 characters. The name must begin with A-Z, a-z or 0-9. Underscore and hypens can be used except as the first character.
```
### Examples 
#### Create a link state tracking group with name as 'FooBar' 
```
sonic(config)# link state track FooBar
```
### Alternate command 
#### click 
```
admin@sonic:~$ sudo config linktrack add <name>
```
## link state track 
### Description 
```
Configure upstream or downstream interfaces.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
link state track <grp-name> { upstream | downstream }
no link state track { <grp-name> | upstream | downstream } { upstream | downstream }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| grp-name | name  | String  |
### Examples 
#### Configure upstream or downstream interfaces 
```
sonic(conf-if-Vlan100)# link state track FooBar upstream
sonic(conf-if-Ethernet4)# link state track FooBar downstream
```
### Alternate command 
#### click 
```
admin@sonic:~$ sudo config linktrack update <name> --upstream <interfaces> --downstream <interfaces>
```
## link state track 
### Description 
```
Configure upstream or downstream interfaces.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
link state track <grp-name> { upstream | downstream }
no link state track { <grp-name> | upstream | downstream } { upstream | downstream }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| grp-name | name  | String  |
### Examples 
#### Configure upstream or downstream interfaces 
```
sonic(conf-if-Vlan100)# link state track FooBar upstream
sonic(conf-if-Ethernet4)# link state track FooBar downstream
```
### Alternate command 
#### click 
```
admin@sonic:~$ sudo config linktrack update <name> --upstream <interfaces> --downstream <interfaces>
```
## link state track 
### Description 
```
Configure upstream or downstream interfaces.
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
link state track <grp-name> { upstream | downstream }
no link state track { <grp-name> | upstream | downstream } { upstream | downstream }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| grp-name | name  | String  |
### Examples 
#### Configure upstream or downstream interfaces 
```
sonic(conf-if-Vlan100)# link state track FooBar upstream
sonic(conf-if-Ethernet4)# link state track FooBar downstream
```
### Alternate command 
#### click 
```
admin@sonic:~$ sudo config linktrack update <name> --upstream <interfaces> --downstream <interfaces>
```
## listen limit 

### Description 

```
This command sets listen limit for BGP for dynamic BGP neighbors.


```
### Parent Commands (Modes) 

```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }

```
### Syntax 

```
listen limit <lmt-val>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| lmt-val |   | Integer  |


### Usage Guidelines 

```
Use this command to configure Maximum number of BGP Dynamic Neighbors that can be created.


```
### Examples 
#### Below command creates a listen limit 

```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# listen limit 123


```
## listen range 

### Description 

```
This command create a listen range for BGP for dynamic BGP neighbors.
BGP will accept connections from any peers in the specified prefix. Configuration
from the specified peer-group is used to configure these peers.


```
### Parent Commands (Modes) 

```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }

```
### Syntax 

```
listen range <addr> { peer-group <pgname> }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| addr | A.B.C.D/mask or A::B/mask  | String  |
| pgname | WORD  | String  |


### Usage Guidelines 

```
Use this command to accept peering connection from neighbors and create
dynamic neighbors


```
### Examples 
#### Below command creates a listen range 192.168.0.0/16 

```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# listen range 192.168.0.0/16 peer-group PG_Ext


```
## lldp 
### Description 
```
Configure LLDP frame Receiption and Transmission mode.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
lldp <mode>
no lldp <mode>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| mode | Mode type  | Select [receive transmit ]  |
### Usage Guidelines 
```
Use this command to configure LLDP frame Receiption and Transmission mode.
```
### Examples 
#### Configure LLDP mode 
```
sonic-cli(config)# lldp receive
        or
sonic-cli(config)# lldp transmit
```
### Features this CLI belongs to 
*  LLDP
## lldp 
### Description 
```
Configure LLDP frame Receiption and Transmission mode at interface level.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
lldp <mode>
no lldp <mode>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| mode | Mode type  | Select [receive transmit ]  |
### Usage Guidelines 
```
Use this command to configure LLDP frame Receiption and Transmission mode at interface level.
```
### Examples 
#### Configure LLDP mode 
```
sonic-cli(config)# interface Ethernet 0
sonic-cli(conf-if-Ethernet0)# lldp receive
```
### Features this CLI belongs to 
*  LLDP
## lldp enable 
### Description 
```
Enable LLDP at global level
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
lldp enable
no lldp enable
```
### Usage Guidelines 
```
Use this command to enable LLDP globally
```
### Examples 
#### Enable LLDP at global level 
```
sonic-cli(config)# lldp enable
```
### Features this CLI belongs to 
*  LLDP
## lldp enable 
### Description 
```
Enable LLDP at interface level
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
lldp enable
no lldp enable
```
### Usage Guidelines 
```
Use this command to enable LLDP at interface level
```
### Examples 
#### Enable LLDP at interface level 
```
sonic-cli(config)# interface Ethernet 0
sonic-cli(conf-if-Ethernet0)# lldp enable
```
### Features this CLI belongs to 
*  LLDP
## lldp multiplier 
### Description 
```
Configure LLDP multiplier value that is used to determine the
timeout interval (i.e. hello-time x multiplier value) after
which LLDP neighbor entry is deleted
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
lldp multiplier <multiplier>
no lldp multiplier
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| multiplier |   | Integer  |
### Usage Guidelines 
```
Use this command to set LLDP multiplier value. Default value is 4.
```
### Examples 
#### Configure LLDP multiplier 
```
sonic-cli(config)# lldp multiplier 6
```
### Features this CLI belongs to 
*  LLDP
## lldp system-description 
### Description 
```
Configure LLDP System description.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
lldp system-description <system_description>
no lldp system-description
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| system_description | Line  | String  |
### Usage Guidelines 
```
Use this command to configure LLDP system description.
```
### Examples 
#### Configure LLDP system description 
```
sonic-cli(config)# lldp system-description "Broadcom Sonic"
```
### Features this CLI belongs to 
*  LLDP
## lldp system-name 
### Description 
```
Configure LLDP System name.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
lldp system-name <system_name>
no lldp system-name
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| system_name | String  | String  |
### Usage Guidelines 
```
Use this command to configure LLDP system name.
```
### Examples 
#### Configure LLDP system name 
```
sonic-cli(config)# lldp system-name "BroadcomSonic"
```
### Features this CLI belongs to 
*  LLDP
## lldp timer 
### Description 
```
Configure LLDP hello time. It is the time interval at which periodic hellos are exchanged.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
lldp timer <hello-time>
no lldp timer
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| hello-time |   | Integer  |
### Usage Guidelines 
```
Use this command to set LLDP hello time. Default hello time is 30 seconds.
```
### Examples 
#### Configure LLDP hello time 
```
sonic-cli(config)# lldp timer 10
```
### Features this CLI belongs to 
*  LLDP
## lldp tlv-select 
### Description 
```
Enable sending of TLVs in LLDP frames.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
lldp tlv-select <tlv>
no lldp tlv-select <tlv>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| tlv | TLV type  | Select [management-address system-capabilities ]  |
### Usage Guidelines 
```
Use this command to configure LLDP TLVs to be advertised
```
### Examples 
#### Configure LLDP TLV 
```
sonic-cli(config)# lldp tlv-select system-capabilities
sonic-cli(config)# lldp tlv-select management-address
```
### Features this CLI belongs to 
*  LLDP
## local-as 
### Description 
```
This command specifies an alternate AS for this BGP process when
interacting with the specified peer. With no modifiers, the specified
local-as is prepended to the received AS_PATH when receiving routing
updates from the peer, and prepended to the outgoing AS_PATH (after the
process local AS) when transmitting local routes to the peer.
If the no-prepend CLI option is specified, then the supplied local-as is
not prepended to the received AS_PATH.
If the replace-as CLI option is specified, then only the supplied
local-as is prepended to the AS_PATH when transmitting local-route
updates to this peer.
Note that replace-as can only be specified if no-prepend is.
This command is only allowed for eBGP peers.
```
### Parent Commands (Modes) 
```
neighbor { <ip> | { interface { Ethernet | PortChannel | Vlan } } }
```
### Syntax 
```
local-as <asnum> { [ no-prepend [ replace-as ] ] }
no local-as
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| asnum | 1-4294967295  | Integer  |
### Usage Guidelines 
```
Use this command to configure local AS number for a BGP neighbor and
control how the local AS number is prepended to the AS_PATH of incoming
and outgoing routes.
```
### Examples 
#### Following command configures local as and enables          no-prepend for BGP neighbor 30.30.30.3 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 30.30.30.3
sonic(config-router-bgp-neighbor)# local-as 65200 no-prepend
```
## local-as 
### Description 
```
This command specifies an alternate AS for this BGP process when
interacting with the specified peers in a peer-group. With no modifiers, the specified
local-as is prepended to the received AS_PATH when receiving routing
updates from the peer, and prepended to the outgoing AS_PATH (after the
process local AS) when transmitting local routes to the peer.
If the no-prepend CLI option is specified, then the supplied local-as is
not prepended to the received AS_PATH.
If the replace-as CLI option is specified, then only the supplied
local-as is prepended to the AS_PATH when transmitting local-route
updates to this peer.
Note that replace-as can only be specified if no-prepend is.
This command is only allowed for eBGP peers.
```
### Parent Commands (Modes) 
```
peer-group <template-str>
```
### Syntax 
```
local-as <asnum> { [ no-prepend [ replace-as ] ] }
no local-as
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| asnum | 1-4294967295  | Integer  |
### Usage Guidelines 
```
Use this command to configure local AS number for BGP neighbors in a
peer-group and control how the local AS number is prepended to the
AS_PATH of incoming and outgoing routes.
```
### Examples 
#### Following command configures local AS and enables          no-prepend for BGP peer-group PG_Ext 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# local-as 65200 non-prepend
```
## log-adjacency-changes 
### Description 
```
Enables OSPFv2 adjacency state logs.
```
### Parent Commands (Modes) 
```
router ospf [ vrf <vrf-name> ]
```
### Syntax 
```
log-adjacency-changes [ detail ]
no log-adjacency-changes [ detail ]
```
### Usage Guidelines 
```
Use this command to enable OSPFv2 adjacency state logs.
```
### Examples 
#### Enables OSPFv2 adjacency state logs. 
```
sonic-cli(config-router-ospf)# log-adjacency-changes
sonic-cli(config-router-ospf)# log-adjacency-changes detail
```
### Features this CLI belongs to 
*  OSPFv2
## log-neighbor-changes 
### Description 
```
This command enables logging of neighbor's state transition events
```
### Parent Commands (Modes) 
```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }
```
### Syntax 
```
log-neighbor-changes
no log-neighbor-changes
```
### Usage Guidelines 
```
Use this command to enable logging of neighbor UP/Down events along with
reason code for down event.
```
### Examples 
#### Below command enables logging of BGP neighbor's up/down          events 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# log-neighbor-changes
```
## logger 

### Description 

```
Enter messages into the system log 


```
### Syntax 

```
logger <message>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| message | String  | String  |


## logging server 
### Description 
```
Configure remote syslog server to forward syslog messages
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
logging server <host> [ remote-port <vrport> ] [ source-interface { Ethernet | Loopback | Management | PortChannel | Vlan } ] [ vrf { mgmt | <vrf-name> } ]
no logging server <host> { [ remote-port ] | [ source-interface ] | [ vrf ] } ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| host | WORD  | String  |
| vrport | port  | Integer  |
| vrf-name | VRF name (prefixed by Vrf, Max: 15 characters)  | String  |
### Examples 
```
sonic(config)# logging server 20.1.1.1 source-iinterface Ethernet 2 vrf Vrf1
sonic(config)#
```
## mac access-group 
### Description 
```
Configure MAC ACL 
```
### Parent Commands (Modes) 
```
line vty
```
### Syntax 
```
mac access-group <access-list-name> in
no mac access-group <access-list-name> in
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| access-list-name | WORD  | String  |
## mac access-group 
### Description 
```
Apply MAC ACL to an interface.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
mac access-group <access-list-name> { in | out }
no mac access-group <access-list-name> { in | out }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| access-list-name | WORD  | String  |
### Usage Guidelines 
```
ACL must be created first and must be of type MAC to be applied. Only 1 ACL of a given type can be applied per interface and per direction.
```
### Examples 
#### Apply MAC ACL to an interface 
```
sonic(conf-if-Vlan100)# mac access-group macacl-example in
```
## mac access-group 
### Description 
```
Apply MAC ACL to an interface.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
mac access-group <access-list-name> { in | out }
no mac access-group <access-list-name> { in | out }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| access-list-name | WORD  | String  |
### Usage Guidelines 
```
ACL must be created first and must be of type MAC to be applied. Only 1 ACL of a given type can be applied per interface and per direction.
```
### Examples 
#### Apply MAC ACL to an interface 
```
sonic(conf-if-Vlan100)# mac access-group macacl-example in
```
## mac access-group 
### Description 
```
Apply MAC ACL to an interface.
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
mac access-group <access-list-name> { in | out }
no mac access-group <access-list-name> { in | out }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| access-list-name | WORD  | String  |
### Usage Guidelines 
```
ACL must be created first and must be of type MAC to be applied. Only 1 ACL of a given type can be applied per interface and per direction.
```
### Examples 
#### Apply MAC ACL to an interface 
```
sonic(conf-if-Vlan100)# mac access-group macacl-example in
```
## mac access-group 
### Description 
```
Apply MAC ACL globally.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
mac access-group <access-list-name> { in | out }
no mac access-group <access-list-name> { in | out }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| access-list-name | WORD  | String  |
### Usage Guidelines 
```
ACL must be created first and must be of type MAC to be applied. Only 1 ACL of a given type can be applied globally per direction.
```
### Examples 
#### Apply MAC ACL globally 
```
sonic(config)# mac access-group macacl-example in
```
## mac access-list 
### Description 
```
Create MAC ACL
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
mac access-list <access-list-name>
no mac access-list <access-list-name>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| access-list-name | WORD  | String  |
### Usage Guidelines 
```
ACL name can be of maximum 63 characters. The name must begin with A-Z, a-z or 0-9. Underscore and hypens can be used except as the first character. ACL name must be unique across all ACL types.
```
### Examples 
#### Create MAC ACL 
```
sonic(config)# mac access-list macacl-example
```
## mac address-table 
### Description 
```
MAC address-table configure commands 
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
mac address-table <mac-address> { vlan { <phy-if-name> | PortChannel } }
no mac address-table <mac-address> vlan
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| mac-address | nn:nn:nn:nn:nn:nn  | String  |
| phy-if-name | EthernetNUM  |   |
## mac address-table aging-time 
### Description 
```
MAC aging time 
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
mac address-table aging-time <mac-time>
no mac address-table aging-time
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| mac-time |   | Integer  |
## mac address-table dampening-interval 
### Description 
```
MAC move dampening threshold interval 
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
mac address-table dampening-interval <interval-value>
no mac address-table dampening-interval
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| interval-value |   | Integer  |
## mac address-table dampening-threshold 
### Description 
```
MAC move dampening threshold 
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
mac address-table dampening-threshold <threshold-value>
no mac address-table dampening-threshold
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| threshold-value |   | Integer  |
## map 
### Description 
```
Command to configure VNI-VLAN mappings and VNI-VRF mappings
```
### Parent Commands (Modes) 
```
interface vxlan <vxlan-if-name>
```
### Syntax 
```
map vni { <vnid> { { vlan { <vid> { [ count [ <numvid> ] ] } } } | { vrf <vrf-name> } } }
no map vni { <vnid> { { vlan <vid> { [ count [ <numvid> ] ] } } | { vrf <vrf-name> } } }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| vnid |   | Integer  |
| vid |   | Integer  |
| numvid |   | Integer  |
| vrf-name | WORD  | String  |
### Usage Guidelines 
```
(conf-if-vxlan-vtep)# map vni VNID vlan VLANID count COUNT
(conf-if-vxlan-vtep)# map vni VNID vrf VRFNAME
VNID - VNI value between 1 to 16777215
VLANID - VLAN value between 1 to 4094
COUNT - number of mappings (optional parameter)
VRFNAME - string
```
### Examples 
#### configure vni-vlan and vni-vrf mappings 
```
sonic(config)# interface vxlan vtep1
sonic(conf-if-vxlan-vtep1)# map vni 100 vlan 100 count 2
sonic(conf-if-vxlan-vtep1)# map vni 100 vrf vrf1
```
## match access-group 
### Description 
```
Configures class-map match ACL
```
### Parent Commands (Modes) 
```
class-map <fbs-class-name> match-type { acl | { fields match-all } | copp }
```
### Syntax 
```
match access-group { mac | ip | ipv6 } <access-list-name>
no match access-group
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| access-list-name | WORD  | String  |
### Examples 
#### Configures class-map match acess-group attributes 
```
sonic(config)# class-map class_ip_acl match-type acl
sonic(config-class-map)# match access-group ip ip_acl1
```
## match as-path 
### Description 
```
Set routing policy match criteria as-path 
```
### Parent Commands (Modes) 
```
route-map <route-map-name> { permit | deny } <seq-nu>
```
### Syntax 
```
match as-path <as-path-name>
no match as-path
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| as-path-name | WORD  | String  |
## match community 
### Description 
```
Set routing policy match criteria to BGP community 
```
### Parent Commands (Modes) 
```
route-map <route-map-name> { permit | deny } <seq-nu>
```
### Syntax 
```
match community <community-name>
no match community
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| community-name | WORD  | String  |
## match dei 
### Description 
```
Configures class-map match packet criteria DEI
```
### Parent Commands (Modes) 
```
class-map <fbs-class-name> match-type { acl | { fields match-all } | copp }
```
### Syntax 
```
match dei <dei-val>
no match dei
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| dei-val | 0-1  | Integer  |
### Examples 
#### Configures class-map match packet PCP  
```
sonic(config)# class-map class1_fields match-type fields match-all
sonic(config-class-map)# match dei 0
```
## match destination-address 
### Description 
```
Configures class-map match packet destination-address
```
### Parent Commands (Modes) 
```
class-map <fbs-class-name> match-type { acl | { fields match-all } | copp }
```
### Syntax 
```
match destination-address { { mac { { <destination-mac-addr> <destination-mac-mask> } | { destination-mac-host <destination-mac-addr> } } } | { ip { <destination-ip-prefix> | { destination-ip-host <destination-ip> } } } | { ipv6 { <destination-ip-prefix> | { destination-ip-host <destination-ip> } } } }
no match destination-address { mac | ip | ipv6 }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| destination-mac-addr | MACADDRESS  | String  |
| destination-mac-mask | MACADDRESS  | String  |
| destination-ip-prefix | A.B.C.D/mask  | String  |
| destination-ip | A.B.C.D  | String  |
### Examples 
#### Configures class-map match packet source-address 
```
sonic(config)# class-map class1_fields match-type fields match-all
sonic(config-class-map)# match destination-address ip 2.1.1.1/32
```
## match dscp 
### Description 
```
Configures class-map match dscp
```
### Parent Commands (Modes) 
```
class-map <fbs-class-name> match-type { acl | { fields match-all } | copp }
```
### Syntax 
```
match dscp { default | cs1 | cs2 | cs3 | cs4 | cs5 | cs6 | cs7 | af11 | af12 | af13 | af21 | af22 | af23 | af31 | af32 | af33 | af41 | af42 | af43 | ef | voice-admit | <dscp-val> }
no match dscp
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| dscp-val |   | Integer  |
### Examples 
#### Configures class-map match dscp 
```
sonic(config)# class-map class1_fields match-type fields match-all
sonic(config-class-map)# match dscp cs1
```
## match ethertype 
### Description 
```
Configures class-map match packet criteria ethertype
```
### Parent Commands (Modes) 
```
class-map <fbs-class-name> match-type { acl | { fields match-all } | copp }
```
### Syntax 
```
match ethertype { ethertype-ip | ethertype-ipv6 | ethertype-arp | <ETHERTYPE> }
no match ethertype
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| ETHERTYPE | 0x600-0xffff  | String  |
### Examples 
#### Configures class-map match packet ethertype 
```
sonic(config)# class-map class1_fields match-type fields match-all
sonic(config-class-map)# match ethertype ip
```
## match evpn 
### Description 
```
Set routing policy match criteria to BGP Ethernet Virtual Private Network 
```
### Parent Commands (Modes) 
```
route-map <route-map-name> { permit | deny } <seq-nu>
```
### Syntax 
```
match evpn { default-route | { route-type { macip | multicast | prefix } } | { vni <vni-number> } }
no match evpn { default-route | { route-type { macip | multicast | prefix } } | { vni <vni-number> } }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| vni-number |   | Integer  |
## match ext-community 
### Description 
```
Set routing policy match criteria to BGP extended community 
```
### Parent Commands (Modes) 
```
route-map <route-map-name> { permit | deny } <seq-nu>
```
### Syntax 
```
match ext-community <community-name>
no match ext-community
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| community-name | WORD  | String  |
## match interface 
### Description 
```
Set routing policy match criteria to interface 
```
### Parent Commands (Modes) 
```
route-map <route-map-name> { permit | deny } <seq-nu>
```
### Syntax 
```
match interface { <phy-if-name> | { PortChannel <lag-id> } | { Vlan <vlan-id> } }
no match interface
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| phy-if-name | EthernetNUM  |   |
| lag-id |   | Integer  |
| vlan-id |   | Integer  |
## match ip address prefix-list 
### Description 
```
Set routing policy match criteria to IPv4 prefix-list 
```
### Parent Commands (Modes) 
```
route-map <route-map-name> { permit | deny } <seq-nu>
```
### Syntax 
```
match ip address prefix-list <prefix-list-name>
no match ip address prefix-list
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| prefix-list-name | WORD  | String  |
## match ip next-hop prefix-list 
### Description 
```
Set routing policy match criteria to next-hop prefix-list 
```
### Parent Commands (Modes) 
```
route-map <route-map-name> { permit | deny } <seq-nu>
```
### Syntax 
```
match ip next-hop prefix-list <match-hop>
no match ip next-hop prefix-list
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| match-hop | WORD  | String  |
## match ip protocol 
### Description 
```
Updates class-map match ip attributes
```
### Parent Commands (Modes) 
```
class-map <fbs-class-name> match-type { acl | { fields match-all } | copp }
```
### Syntax 
```
match ip protocol { <ip-protocol-val> | icmp | icmpv6 | tcp | udp }
no match ip protocol
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| ip-protocol-val |   | Integer  |
### Examples 
#### Configures class-map match ip attributes 
```
sonic(config)# class-map class1_fields match-type fields match-all
sonic(config-class-map)# match ip protocol tcp
```
## match ipv6 address prefix-list 
### Description 
```
Set routing policy match criteria to IPv6 prefix-list 
```
### Parent Commands (Modes) 
```
route-map <route-map-name> { permit | deny } <seq-nu>
```
### Syntax 
```
match ipv6 address prefix-list <prefix-list-name>
no match ipv6 address prefix-list
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| prefix-list-name | WORD  | String  |
## match l4-port 
### Description 
```
Configures class-map match l4 source port
```
### Parent Commands (Modes) 
```
class-map <fbs-class-name> match-type { acl | { fields match-all } | copp }
```
### Syntax 
```
match l4-port { source | destination } { { eq <eq-port-val> } | { range <begin-port-val> <end-port-val> } }
no match l4-port { source | destination }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| eq-port-val |   | Integer  |
| begin-port-val |   | Integer  |
| end-port-val |   | Integer  |
### Usage Guidelines 
```
Match on source port is allowed only when IP protocol is set to TCP or UDP
```
### Examples 
#### Configures class-map match l4 source port  
```
sonic(config)# class-map class1_fields match-type fields match-all
sonic(config-class-map)# match l4-port source eq 10
```
## match local-preference 
### Description 
```
Set routing policy match criteria to local-preference 
```
### Parent Commands (Modes) 
```
route-map <route-map-name> { permit | deny } <seq-nu>
```
### Syntax 
```
match local-preference <match-loc>
no match local-preference
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| match-loc |   | Integer  |
## match metric 
### Description 
```
Set routing policy match criteria to metric 
```
### Parent Commands (Modes) 
```
route-map <route-map-name> { permit | deny } <seq-nu>
```
### Syntax 
```
match metric <match-met>
no match metric
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| match-met |   | Integer  |
## match origin 
### Description 
```
Specify BGP origin 
```
### Parent Commands (Modes) 
```
route-map <route-map-name> { permit | deny } <seq-nu>
```
### Syntax 
```
match origin { egp | igp | incomplete }
no match origin
```
## match pcp 
### Description 
```
Configures class-map match packet criteria pcp
```
### Parent Commands (Modes) 
```
class-map <fbs-class-name> match-type { acl | { fields match-all } | copp }
```
### Syntax 
```
match pcp { pcp-be | pcp-bk | pcp-ee | pcp-ca | pcp-vi | pcp-vo | pcp-ic | pcp-nc | { <pcp-val> { [ pcp-mask <pcp-val-mask> ] } } }
no match pcp
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| pcp-val | 0-7  | Integer  |
| pcp-val-mask | 0-7  | Integer  |
### Examples 
#### Configures class-map match packet PCP  
```
sonic(config)# class-map class1_fields match-type fields match-all
sonic(config-class-map)# match pcp vi
```
## match peer 
### Description 
```
Set routing policy match criteria to peer IP 
```
### Parent Commands (Modes) 
```
route-map <route-map-name> { permit | deny } <seq-nu>
```
### Syntax 
```
match peer { <match-peer> | <phy-if-name> | { PortChannel <lag-id> } | { Vlan <vlan-id> } }
no match peer
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| match-peer | A.B.C.D/A::B  | String  |
| phy-if-name | EthernetNUM  |   |
| lag-id |   | Integer  |
| vlan-id |   | Integer  |
## match protocol 
### Description 
```
Matches protocol trap to class-map 
```
### Parent Commands (Modes) 
```
class-map <fbs-class-name> match-type { acl | { fields match-all } | copp }
```
### Syntax 
```
match protocol <trap-id>
no match protocol <trap-id>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| trap-id | WORD  | String  |
## match source-address 
### Description 
```
Configures class-map match packet source-address
```
### Parent Commands (Modes) 
```
class-map <fbs-class-name> match-type { acl | { fields match-all } | copp }
```
### Syntax 
```
match source-address { { mac { { <source-mac-addr> <source-mac-mask> } | { source-mac-host <source-mac-addr> } } } | { ip { <source-ip-prefix> | { source-ip-host <source-ip> } } } | { ipv6 { <source-ip-prefix> | { source-ip-host <source-ip> } } } }
no match source-address { mac | ip | ipv6 }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| source-mac-addr | MACADDRESS  | String  |
| source-mac-mask | MACADDRESS  | String  |
| source-ip-prefix | A.B.C.D/mask  | String  |
| source-ip | A.B.C.D  | String  |
### Examples 
#### Configures class-map match packet source-address 
```
sonic(config)# class-map class1_fields match-type fields match-all
sonic(config-class-map)# match source-address ip 1.1.1.1/32
```
## match source-protocol 
### Description 
```
Specify source protocol 
```
### Parent Commands (Modes) 
```
route-map <route-map-name> { permit | deny } <seq-nu>
```
### Syntax 
```
match source-protocol { bgp | ospf | ospf3 | static | connected }
no match source-protocol
```
## match source-vrf 
### Description 
```
Source VRF 
```
### Parent Commands (Modes) 
```
route-map <route-map-name> { permit | deny } <seq-nu>
```
### Syntax 
```
match source-vrf <src-vrf>
no match source-vrf
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| src-vrf | WORD  | String  |
## match tag 
### Description 
```
Redistributes routes in the routing table that match the specified tags. 
```
### Parent Commands (Modes) 
```
route-map <route-map-name> { permit | deny } <seq-nu>
```
### Syntax 
```
match tag <match-tag>
no match tag
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| match-tag | 1-4294967295  | Integer  |
## match tcp-flags 
### Description 
```
Configures class-map match TCP Flags
```
### Parent Commands (Modes) 
```
class-map <fbs-class-name> match-type { acl | { fields match-all } | copp }
```
### Syntax 
```
match tcp-flags { [ fin ] | [ not-fin ] } ] { [ syn ] | [ not-syn ] } ] { [ rst ] | [ not-rst ] } ] { [ psh ] | [ not-psh ] } ] { [ ack ] | [ not-ack ] } ] { [ urg ] | [ not-urg ] } ]
no match tcp-flags { [ fin ] | [ not-fin ] } ] { [ syn ] | [ not-syn ] } ] { [ rst ] | [ not-rst ] } ] { [ psh ] | [ not-psh ] } ] { [ ack ] | [ not-ack ] } ] { [ urg ] | [ not-urg ] } ]
```
### Usage Guidelines 
```
Match on TCP flags is allowed only when IP protocol is set to TCP. not-xxx keyword can be used to match the corresponding flag set to 0
```
### Examples 
#### Configures class-map match tcp flags 
```
sonic(config)# class-map class1_fields match-type fields match-all
sonic(config-class-map)# match tcp-flags urg
```
## match vlan 
### Description 
```
Configures class-map match packet criteria vlan ID
```
### Parent Commands (Modes) 
```
class-map <fbs-class-name> match-type { acl | { fields match-all } | copp }
```
### Syntax 
```
match vlan <vlan-val>
no match vlan
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| vlan-val |   | String  |
### Examples 
#### Configures class-map match packet vlan id  
```
sonic(config)# class-map class1_fields match-type fields match-all
sonic(config-class-map)# match vlan 200
```
## max-med 
### Description 
```
This command instructs BGP to advertise routes with max MED value under
a given condition
```
### Parent Commands (Modes) 
```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }
```
### Syntax 
```
max-med { { on-startup { <stime> [ <maxmedval> ] } } | { administrative [ <maxmedval> ] } }
no max-med { { on-startup <stime> } | administrative } [ <maxmedval> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| stime |   | Integer  |
| maxmedval |   | Integer  |
### Usage Guidelines 
```
Use this command to instruct BGP to advertise routes with max MED value.
The command allows user to set the condition under which routes with max
MED value will be sent. One is during the startup for a prespecified
number of seconds. The other is permanently. User can also specify the
value for max MED.
```
### Examples 
#### Below command instructs BGP to send Max MED after          startup for 300seconds. Max MED value is set to 2000 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# max-med on-startup 300 2000
```
## max-metric 
### Description 
```
Enables infinite metric advertising in OSPFv2 LSAs.
```
### Parent Commands (Modes) 
```
router ospf [ vrf <vrf-name> ]
```
### Syntax 
```
max-metric router-lsa { [ administrative ] | { [ on-startup <interdistance> ] } }
no max-metric router-lsa { [ administrative ] | [ on-startup ] }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| interdistance |   | Integer  |
### Usage Guidelines 
```
Use this command to enable infinite metric advertising in OSPFv2 LSAs. Max-metric can
be enabled administratively or during OSPFv2 startup time. When enabled administratively,
max-metric advertizing will be effective till it is unconfigures explicitely. When
enabled for restrt time, max-metric will be advertised for a period of time specified
in the config.
```
### Examples 
#### Enables infinite metric advertising in OSPFv2 LSAs. 
```
sonic-cli(config-router-ospf)# max-metric router-lsa administrative
sonic-cli(config-router-ospf)# max-metric router-lsa on-startup 90
```
### Features this CLI belongs to 
*  OSPFv2
## maximum-paths 
### Description 
```
Forward packets over multiple paths 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
maximum-paths <paths>
no maximum-paths
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| paths |   | Integer  |
## maximum-paths 
### Description 
```
Sets the maximum number of equal cost multipath for eBGP.
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
maximum-paths <paths>
no maximum-paths
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| paths |   | Integer  |
### Usage Guidelines 
```
Use this command to configure BGP to control the maximum number of equal cost
multipath routes to eBGP destinations. This command is per
address-family
```
### Examples 
#### Configures maximum equal cost multipath to 32 for eBGP 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# address-family ipv4 unicast
sonic(config-router-bgp-af)# maximum-paths 32
```
## maximum-paths ibgp 
### Description 
```
IBGP-multipath 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
maximum-paths ibgp <ipaths> [ equal-cluster-length ]
no maximum-paths ibgp
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| ipaths |   | Integer  |
## maximum-paths ibgp 
### Description 
```
Sets the maximum number of equal cost multipath for iBGP.
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
maximum-paths ibgp <ipaths> [ equal-cluster-length ]
no maximum-paths ibgp
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| ipaths |   | Integer  |
### Usage Guidelines 
```
Use this command to configure BGP to control the maximum number of equal cost
multipath routes to iBGP destinations. This command is per
address-family
```
### Examples 
#### Configures maximum equal cost multipath to 32 for iBGP 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# address-family ipv4 unicast
sonic(config-router-bgp-af)# maximum-paths ibgp 32
```
## maximum-prefix 
### Description 
```
Maximum number of prefixes to accept from this peer 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
maximum-prefix <max-prefix-val> { [ <threshold-val> ] { [ warning-only ] | { [ restart <interval> ] } ] } }
no maximum-prefix [ <max-prefix-val> { [ threshold-val ] | [ warning-only ] | { [ restart <val> ] } ] } ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| max-prefix-val | 1-4294967295  | Integer  |
| threshold-val |   | Integer  |
| interval |   | Integer  |
## maximum-prefix 
### Description 
```
Maximum number of prefixes to accept from this peer 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
maximum-prefix <max-prefix-val> { [ <threshold-val> ] { [ warning-only ] | { [ restart <interval> ] } ] } }
no maximum-prefix [ <max-prefix-val> { [ threshold-val ] | [ warning-only ] | { [ restart <val> ] } ] } ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| max-prefix-val | 1-4294967295  | Integer  |
| threshold-val |   | Integer  |
| interval |   | Integer  |
## maximum-prefix 
### Description 
```
This command configures the maximum number of prefix to accept from this
BGP neighbor.
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
maximum-prefix <max-prefix-val> { [ <threshold-val> ] { [ warning-only ] | { [ restart <interval> ] } ] } }
no maximum-prefix [ <max-prefix-val> { [ threshold-val ] | [ warning-only ] | { [ restart <val> ] } ] } ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| max-prefix-val | 1-4294967295  | Integer  |
| threshold-val |   | Integer  |
| interval |   | Integer  |
### Usage Guidelines 
```
Use this command to set the upper limit on number of BGP prefixes to
accept from this neighbor. This command has optional parameters for
warning user when a threshold is reached and rsetarting BGP neighborship
when maximum prefix limit has exceeded.
```
### Examples 
#### Following command configures maximum prefix of 2000 from          neighbor 20.20.20.2. The command also configures a threhold of 80% and          the action to take is warning when the number of prefxies received          exceeds the max limit of 2000 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 20.20.20.2
sonic(config-router-bgp-neighbor)# remote-as 300
sonic(config-router-bgp-neighbor)# address-family ipv4 unicast
sonic(config-router-bgp-neighbor-af)# maximum-prefix 2000 80 warning-only
```
## maximum-prefix 
### Description 
```
This command configures the maximum number of prefix to accept from
BGP neighbors in a peer-group.
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
maximum-prefix <max-prefix-val> { [ <threshold-val> ] { [ warning-only ] | { [ restart <interval> ] } ] } }
no maximum-prefix [ <max-prefix-val> { [ threshold-val ] | [ warning-only ] | { [ restart <val> ] } ] } ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| max-prefix-val | 1-4294967295  | Integer  |
| threshold-val |   | Integer  |
| interval |   | Integer  |
### Usage Guidelines 
```
Use this command to set the upper limit on number of BGP prefixes to
accept from neighbors in a peer-group. This command has optional parameters for
warning user when a threshold is reached and rsetarting BGP neighborship
when maximum prefix limit has exceeded.
```
### Examples 
#### Following command configures maximum prefix of 2000 from          neighbors in peer-group PG_Int. The command also configures a threhold of 80% and          the action to take is warning when the number of prefxies received          exceeds the max limit of 2000 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Int
sonic(config-router-bgp-pg)# address-family ipv4 unicast
sonic(config-router-bgp-pg-af)# maximum-prefix 2000 80 warning-only
```
## mclag 
### Description 
```
Configure MCLAG interface 
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
mclag <domain_id>
no mclag <domain_id>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| domain_id |   | Integer  |
## mclag domain 
### Description 
```
Enters MCLAG domain configuration mode 
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
mclag domain <mclag-domain-id>
no mclag domain <mclag-domain-id>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| mclag-domain-id |   | Integer  |
## mclag gateway-mac 
### Description 
```
Delete Gateway Mac for MCLAG.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
mclag gateway-mac <gw_mac>
no mclag gateway-mac <gw_mac>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| gw_mac | nn:nn:nn:nn:nn:nn  | String  |
## mclag-separate-ip 
### Description 
```
Configure separate IP on Vlan interface for L3 protocol support over MCLAG
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
mclag-separate-ip
no mclag-separate-ip
```
### Usage Guidelines 
```
sonic-cli(conf-if-Vlan10)# mclag-separate-ip
```
### Examples 
#### mclag-separate-ip 
```
sonic-cli(config)# interface Vlan 10
sonic-cli(conf-if-Vlan10)# mclag-separate-ip
```
## mclag-system-mac 
### Description 
```
Configures MCLAG system MAC address
```
### Parent Commands (Modes) 
```
mclag domain <mclag-domain-id>
```
### Syntax 
```
mclag-system-mac <MSM>
no mclag-system-mac
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| MSM | nn:nn:nn:nn:nn:nn  | String  |
### Usage Guidelines 
```
Use this command to set MCLAG system mac which will be used for MCLAG interface mac
```
### Examples 
#### Configures MCLAG system mac address for MCLAG domain 100 
```
sonic-cli(config-mclag-domain-100)#mclag-system-mac 00:bb:bb:bb:cc:cc
```
## meter-type 
### Description 
```
scheduler meter type (packets/bytes) 
```
### Parent Commands (Modes) 
```
queue <sequence>
```
### Syntax 
```
meter-type <meter_type>
no meter-type
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| meter_type | Scheduler meter-type Options  | Select [packets(PACKETS) bytes(BYTES) ]  |
## mirror-session 
### Description 
```
Mirror session configuration 
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
mirror-session <session-name>
no mirror-session <session-name>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| session-name | String(Max: 20 characters)  | String  |
## mtu 
### Description 
```
Configure MTU 
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
mtu <mtu>
no mtu
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| mtu |   | Integer  |
## mtu 
### Description 
```
Configure MTU 
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
mtu <mtu>
no mtu
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| mtu |   | Integer  |
## mtu 
### Description 
```
Configure MTU 
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
mtu <mtu>
no mtu
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| mtu |   | Integer  |
## mtu 
### Description 
```
Configure MTU 
```
### Parent Commands (Modes) 
```
interface Management <mgmt-if-id>
```
### Syntax 
```
mtu <mtu>
no mtu
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| mtu |   | Integer  |
## mtu 
### Description 
```
Configure interface link MTU 
```
### Parent Commands (Modes) 
```
interface range iface_range_num
```
### Syntax 
```
mtu <mtu>
no mtu
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| mtu |   | Integer  |
## mtu 
### Description 
```
Configure interface link MTU 
```
### Parent Commands (Modes) 
```
interface range create vlan_range_num
interface range vlan_range_num
```
### Syntax 
```
mtu <mtu>
no mtu
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| mtu |   | Integer  |
## mtu 
### Description 
```
Configure interface link MTU 
```
### Parent Commands (Modes) 
```
interface range create po_range_num { [ mode <PoMode> ] } { [ min-links <min-links-value> ] } [ fallback ] [ fast_rate ]
interface range po_range_num
```
### Syntax 
```
mtu <mtu>
no mtu
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| mtu |   | Integer  |
## nat 

### Description 

```
Enter NAT configuration 


```
### Parent Commands (Modes) 

```
configure terminal

```
### Syntax 

```
nat

```
## nat-zone 
### Description 
```
NAT Zone 
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
nat-zone <zone>
no nat-zone
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| zone |   | Integer  |
## nat-zone 
### Description 
```
NAT Zone 
```
### Parent Commands (Modes) 
```
interface Loopback <lo-id>
```
### Syntax 
```
nat-zone <zone>
no nat-zone
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| zone |   | Integer  |
## nat-zone 
### Description 
```
NAT Zone 
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
nat-zone <zone>
no nat-zone
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| zone |   | Integer  |
## nat-zone 
### Description 
```
NAT Zone 
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
nat-zone <zone>
no nat-zone
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| zone |   | Integer  |
## neigh-suppress 
### Description 
```
Enable ARP and ND Suppression 
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
neigh-suppress
no neigh-suppress
```
## neighbor 
### Description 
```
This command creates a BGP neighbor
```
### Parent Commands (Modes) 
```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }
```
### Syntax 
```
neighbor { <ip> | { interface { Ethernet | PortChannel | Vlan } } }
no neighbor { <ip> | { interface { Ethernet | PortChannel | Vlan } } }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| ip | A.B.C.D/A::B  | String  |
### Usage Guidelines 
```
Use this command to create a BGP neighbor. User can input neighbor's
IPv4/IPv6 address directly or can input an interface name for unnumbered
BGP neighbor.
```
### Examples 
#### Below command creates a BGP neighbor 30.30.30.3 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# neighbor 30.30.30.3
sonic(config-router-bgp-neighbor)#
```
## network 
### Description 
```
Configures networks in an area.
```
### Parent Commands (Modes) 
```
router ospf [ vrf <vrf-name> ]
```
### Syntax 
```
network <ipaddrmask> { area <areaid> }
no network <ipaddrmask> { area <areaid> }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| ipaddrmask | A.B.C.D/mask  | String  |
| areaid | A.B.C.D or 0..4294967295  | String  |
### Usage Guidelines 
```
Use this command to configure or associate network addresses into specific areas.
Interfaces belonging to these network addresses will be condidere as part of the area
specified. This config command is mutually exclusive with interface mode area command
within a VRF. When network command is used in a VRF, area command cannot be used at
interface mode configuration.
```
### Examples 
#### Configures networks in an area. 
```
sonic-cli(config-router-ospf)# network 10.1.1.0/24 area 0
sonic-cli(config-router-ospf)# network 19.1.1.0/16 area 19
```
### Features this CLI belongs to 
*  OSPFv2
## network 
### Description 
```
Enable routing on an IP network 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
network <prefix> { [ backdoor ] { [ route-map <route-map-name> ] } }
no network <prefix> { [ backdoor ] { [ route-map <route-map-name> ] } }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| prefix | A::B/mask  | String  |
| route-map-name | WORD  | String  |
## network 
### Description 
```
This command enables user to add a network to announce via BGP
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
network <prefix> { [ backdoor ] { [ route-map <route-map-name> ] } }
no network <prefix> { [ backdoor ] { [ route-map <route-map-name> ] } }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| prefix | A.B.C.D/mask  | String  |
| route-map-name | WORD  | String  |
### Usage Guidelines 
```
This command can be used by users and network administrators to
statically inject routes into BGP. User can use route-map optional
parameter to modify/set the various attributes of the route.
```
### Examples 
#### Creates a network 10.10.0.0/16 which will be announced            to all BGP neighbors 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# address-family ipv4 unicast
sonic(config-router-bgp-af)# network 10.10.0.0/16
```
## network import-check 
### Description 
```
This command instructs BGP to check if BGP network route exists in local
route table before advertising the network
```
### Parent Commands (Modes) 
```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }
```
### Syntax 
```
network import-check
no network import-check
```
### Usage Guidelines 
```
By default, BGP networks are adverised to neighbors irrespective of
whether the same route exists in local route table or not. This behavior
may lead to data traffic blackholing. User can use this command to put a
restriction on BGP networks to get advertised only if a corresponding
route from IGP exists in local route table.
```
### Examples 
#### Below command enables checking of presence of network in          local routing table before adverizing the network to neighbors 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# network import-check
```
## next-hop-self 
### Description 
```
This command sets next-hop attribute as it's own address in the outbound
route updates to this BGP neighbor
```
### Parent Commands (Modes) 
```
address-family l2vpn evpn
```
### Syntax 
```
next-hop-self [ force ]
no next-hop-self [ force ]
```
### Usage Guidelines 
```
Use this command to disable BGP next-hop attribute computation and
override the next-hop by sender's own address.
```
### Examples 
#### Following command configures next-hop-self for BGP          updates sent to neighbor 20.20.20.2 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 20.20.20.2
sonic(config-router-bgp-neighbor)# remote-as 300
sonic(config-router-bgp-neighbor)# address-family l2vpn evpn
sonic(config-router-bgp-neighbor-af)# next-hop-self
```
## next-hop-self 
### Description 
```
This command sets next-hop attribute as it's own address in the outbound
route updates to BGP neighbors in a peer-group
```
### Parent Commands (Modes) 
```
address-family l2vpn evpn
```
### Syntax 
```
next-hop-self [ force ]
no next-hop-self [ force ]
```
### Usage Guidelines 
```
Use this command to disable BGP next-hop attribute computation and
override the next-hop by sender's own address.
```
### Examples 
#### Following command configures next-hop-self for BGP          updates sent to neighbors in peer-group PG_Ext 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# address-family l2vpn evpn
sonic(config-router-bgp-pg-af)# next-hop-self
```
## next-hop-self 
### Description 
```
Disable the next hop calculation for this neighbor 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
next-hop-self [ force ]
no next-hop-self [ force ]
```
## next-hop-self 
### Description 
```
Disable the next hop calculation for this neighbor 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
next-hop-self [ force ]
no next-hop-self [ force ]
```
## next-hop-self 
### Description 
```
This command sets next-hop attribute as it's own address in the outbound
route updates to this BGP neighbor
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
next-hop-self [ force ]
no next-hop-self [ force ]
```
### Usage Guidelines 
```
Use this command to disable BGP next-hop attribute computation and
override the next-hop by sender's own address.
```
### Examples 
#### Following command configures next-hop-self for BGP          updates sent to neighbor 20.20.20.2 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 20.20.20.2
sonic(config-router-bgp-neighbor)# remote-as 300
sonic(config-router-bgp-neighbor)# address-family ipv4 unicast
sonic(config-router-bgp-neighbor-af)# next-hop-self
```
## next-hop-self 
### Description 
```
This command sets next-hop attribute as it's own address in the outbound
route updates to BGP neighbors in a peer-group
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
next-hop-self [ force ]
no next-hop-self [ force ]
```
### Usage Guidelines 
```
Use this command to disable BGP next-hop attribute computation and
override the next-hop by sender's own address.
```
### Examples 
#### Following command configures next-hop-self for BGP          updates sent to neighbors in peer-group PG_Ext 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# address-family ipv4 unicast
sonic(config-router-bgp-pg-af)# next-hop-self
```
## no 

### Description 

```
This command to unconfigure WRED minimum, maximum thresholds
and drop probability for color green.


```
### Parent Commands (Modes) 

```
qos wred-policy <name>

```
### Syntax 

```
no green

```
### Usage Guidelines 

```
Use this command to unconfigure WRED minimum, maximum and drop probability for green color packets.


```
### Examples 
#### The following command to unconfigure WRED thresholds for green color           packets 

```
sonic(conf-wred-wred-green)# no random-detect color green


```
## ntp authenticate 
### Description 
```
Enable NTP authentication in configuration mode. By default, NTP authentication is disabled
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
ntp authenticate
no ntp authenticate
```
### Usage Guidelines 
```
sonic(config)# ntp authenticate
```
### Examples 
#### Enable NTP authentication 
```
sonic# configure
sonic(config)# ntp authenticate
```
## ntp authentication-key 
### Description 
```
Configure NTP authentication key.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
ntp authentication-key <key-number> { <auth-type> { <key> [ encrypted ] } }
no ntp authentication-key <key-number>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| key-number |   | Integer  |
| auth-type |   | Select [md5(md5) sha1(sha1) sha2-256(sha2-256) ]  |
| key | WORD  | String  |
### Usage Guidelines 
```
sonic(config)# ntp authentication-key key-number auth-type key encrypted
"encrypted" is optional
```
### Examples 
#### Configure NTP authentication-key 
```
sonic# configure
sonic(config)# ntp authentication-key 128 md5 DellEmc2020
sonic(config)# ntp authentication-key 128 md5 dryjsgjd123 encrypted
```
## ntp server 
### Description 
```
Configure a NTP server in configuration mode. NTP sever can be identified by
IPv4, IPv6 address or host name.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
ntp server { <ip> | <server-name> } [ key <key-number> ]
no ntp server { <ip> | <server-name> }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| ip | A.B.C.D/A::B  | String  |
| server-name | WORD  | String  |
| key-number |   | Integer  |
### Usage Guidelines 
```
sonic(config)# ntp server NTP-SERVER-IP
sonic(config)# ntp server NTP-SERVER-HOST-NAME
NTP-SERVER-IP:        A.B.C.D/A::B                         IPv4/IPv6 address of NTP server
NTP-SERVER-HOST-NAME: WORD (Max: 253 characters)           Hostname of NTP server
```
### Examples 
#### Configure a NTP server 
```
sonic# configure
sonic(config)# ntp server 10.11.0.1 key 128
sonic(config)# ntp server pool.ntp.org
```
## ntp source-interface 
### Description 
```
Configure NTP source interface in configuration mode. NTP source interface can be
an ethernet, loopback, management, port channel or vlan interface.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
ntp source-interface { Ethernet | Loopback | Management | PortChannel | Vlan }
no ntp source-interface { Ethernet | Loopback | Management | PortChannel | Vlan }
```
### Usage Guidelines 
```
sonic(config)# ntp source-interface INTERFACE
INTERFACE: Ethernet     Ethernet interface
           Loopback     Loopback interface
           Management   Management Interface
           PortChannel  PortChannel interface
           Vlan         Vlan interface
```
### Examples 
#### Configure NTP source interface 
```
sonic# configure
sonic(config)# ntp source-interface Ethernet 8
sonic(config)# ntp source-interface Management 0
sonic(config)# ntp source-interface Loopback 88
sonic(config)# ntp source-interface PortChannel 18
sonic(config)# ntp source-interface Vlan 888
```
## ntp trusted-key 
### Description 
```
Configure NTP authentication key number for trusted time source.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
ntp trusted-key <key-number>
no ntp trusted-key <key-number>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| key-number |   | Integer  |
### Usage Guidelines 
```
sonic(config)# ntp trusted-key
     key-number  Trusted key number
```
### Examples 
#### Configure NTP trusted-key number 
```
sonic# configure
sonic(config)# ntp trusted-key 128
```
## ntp vrf 
### Description 
```
Enable NTP on VRF in configuration mode. NTP can be enabled on management VRF or default VRF.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
ntp vrf { mgmt | default }
no ntp vrf
```
### Usage Guidelines 
```
sonic(config)# ntp vrf VRF
VRF: mgmt     Enable NTP on management VRF
     default  Enable NTP on default VRF
```
### Examples 
#### Enable NTP on VRF 
```
sonic# configure
sonic(config)# ntp vrf mgmt
sonic(config)# ntp vrf default
```
## ospf 
### Description 
```
Configures OSPFv2 router parameters.
```
### Parent Commands (Modes) 
```
router ospf [ vrf <vrf-name> ]
```
### Syntax 
```
ospf
no ospf
```
### Usage Guidelines 
```
Use this command to configure OSPFv2 router parameters like router-id and ABR type.
```
### Examples 
#### Configures OSPFv2 router parameters. 
```
```
### Features this CLI belongs to 
*  OSPFv2
## ospf abr-type 
### Description 
```
Configures OSPFv2 router ABR type.
```
### Parent Commands (Modes) 
```
router ospf [ vrf <vrf-name> ]
```
### Syntax 
```
ospf abr-type { cisco | ibm | shortcut | standard }
no ospf abr-type
```
### Usage Guidelines 
```
Use this command to configure OSPFv2 router ABR type.
```
### Examples 
#### Configures router ABR type. 
```
sonic-cli(config-router-ospf)# ospf abr-type shortcut
sonic-cli(config-router-ospf)# ospf abr-type cisco
```
### Features this CLI belongs to 
*  OSPFv2
## ospf router-id 
### Description 
```
Configures OSPFv2 router ID.
```
### Parent Commands (Modes) 
```
router ospf [ vrf <vrf-name> ]
```
### Syntax 
```
ospf router-id <routerid>
no ospf router-id
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| routerid | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to configure OSPFv2 router ID. At the time of OSPFv2 router startup or
new configuration, if the router-id configuration is not present then the highest IPv4
address among the available IPv4 interfaces will be considered as router-id. Upon
configuring a specific router-id, newly configured router-id will be used. Upon
unconfiguring a router-id, same router-id will continue to be used till next router-id
config or system restart or OSPFv2 router unconfigure.
```
### Examples 
#### Configures OSPFv2 router ID. 
```
sonic-cli(config-router-ospf)# ospf router-id 19.1.1.1
```
### Features this CLI belongs to 
*  OSPFv2
## override-capability 
### Description 
```
This command instructs BGP to override the result of Capability
Negotiation with local configuration. Ignore remote peer's capability
value.
```
### Parent Commands (Modes) 
```
neighbor { <ip> | { interface { Ethernet | PortChannel | Vlan } } }
```
### Syntax 
```
override-capability
no override-capability
```
### Usage Guidelines 
```
Use this command to ignored the negotiated capability parameters with
neighbor and instead use the locally configured parameters.
```
### Examples 
#### Following command instructs BGP to ignore the negotiated          parameters and use the locally configured one for BGP neighbor 30.30.30.3 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 30.30.30.3
sonic(config-router-bgp-neighbor)# override-capability
```
## override-capability 
### Description 
```
This command instructs BGP to override the result of Capability
Negotiation with local configuration. Ignore remote peer's capability
value.
```
### Parent Commands (Modes) 
```
peer-group <template-str>
```
### Syntax 
```
override-capability
no override-capability
```
### Usage Guidelines 
```
Use this command to ignored the negotiated capability parameters with
neighbors in a peer-group and instead use the locally configured parameters.
```
### Examples 
#### Following command instructs BGP to ignore the negotiated          parameters and use the locally configured one for neighbors in          peer-group PG_Ext 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# override-capability
```
## passive 
### Description 
```
This command makes BGP neighbor passive. That is, this BGP neighbor will
not initiate a session. However, it will listen to any incoming BGP
session.
```
### Parent Commands (Modes) 
```
neighbor { <ip> | { interface { Ethernet | PortChannel | Vlan } } }
```
### Syntax 
```
passive
no passive
```
### Usage Guidelines 
```
Use this command to set a BGP neighbor as passive.
```
### Examples 
#### Following command makes BGP neighbor 30.30.30.3 as          passive one 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 30.30.30.3
sonic(config-router-bgp-neighbor)# passive
```
## passive 
### Description 
```
This command makes BGP neighbors in a peer-group passive. That is, BGP
neighbors will not initiate a session. However, it will listen to any incoming BGP
session.
```
### Parent Commands (Modes) 
```
peer-group <template-str>
```
### Syntax 
```
passive
no passive
```
### Usage Guidelines 
```
Use this command to set BGP neighbors in a peer-group as passive.
```
### Examples 
#### Following command makes BGP neighbors in peer-group          PG_Ext passive 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# passive
```
## passive-interface 
### Description 
```
Configures OSPFv2 passive interfaces.
```
### Parent Commands (Modes) 
```
router ospf [ vrf <vrf-name> ]
```
### Syntax 
```
passive-interface { default | { <interfacename> { { [ <ipaddr> [ non-passive ] ] } | [ non-passive ] ] } } }
no passive-interface { default | { <interfacename> { [ <ipaddr> ] ] } } }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| interfacename | Interface Type - Ranges  |   |
| ipaddr | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to configure OSPFv2 passive interfaces. Default mode makes all
interfaces as passive interface. When default mode is enabled, user will be able
to activate chosen interaces by enabling non-passive mode of passive interface
configuration.
```
### Examples 
#### Configures OSPFv2 passive interfaces. 
```
sonic-cli(config-router-ospf)# passive-interface Ethernet0
   or
sonic-cli(config-router-ospf)# passive-interface default
sonic-cli(config-router-ospf)# passive-interface Ethernet0 non-passive
```
### Features this CLI belongs to 
*  OSPFv2
## password 
### Description 
```
This command sets a MD5 password to be used with the tcp socket that is
being used to connect to the remote peer.
```
### Parent Commands (Modes) 
```
neighbor { <ip> | { interface { Ethernet | PortChannel | Vlan } } }
```
### Syntax 
```
password <String> [ encrypted ]
no password
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| String | String  | String  |
### Usage Guidelines 
```
This command is for security purposes. When Password is configures for a
BGP neighbor, sender will include a 16-bytes MD5 digest in TCP header of
BGP message and the receiver should be able to validate the digest then
only accept the BGP message.
```
### Examples 
#### Following command configures password for BGP neighbor          30.30.30.3 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 30.30.30.3
sonic(config-router-bgp-neighbor)# password jackandjillwentupthehill
```
## password 
### Description 
```
This command sets a MD5 password to be used with the tcp socket that is
being used to connect to the remote peers in a peer-group.
```
### Parent Commands (Modes) 
```
peer-group <template-str>
```
### Syntax 
```
password <String> [ encrypted ]
no password
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| String | String  | String  |
### Usage Guidelines 
```
This command is for security purposes. When Password is configures for
BGP neighbors in a peer-group, sender will include a 16-bytes MD5 digest in TCP header of
BGP message and the receiver should be able to validate the digest then
only accept the BGP message.
```
### Examples 
#### Following command configures password for BGP neighbors          in a peer-group PG_Ext 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# password ilovebeansbecausetheyaremean
```
## pbs 
### Description 
```
Peak Burst Size 
```
### Parent Commands (Modes) 
```
queue <sequence>
```
### Syntax 
```
pbs <be>
no pbs
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| be |   | Integer  |
## pbs 
### Description 
```
Peak Burst Size in bytes 
```
### Parent Commands (Modes) 
```
port
```
### Syntax 
```
pbs <pbs>
no pbs
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| pbs |   | Integer  |
## peer 
### Description 
```
Configure single-hop and multi-hop Bidirectional Forwarding detection(BFD) peer.
```
### Parent Commands (Modes) 
```
bfd
```
### Syntax 
```
peer { <peer_ipv4> | <peer_ipv6> } [ vrf <vrfname> ] [ multihop ] [ local-address { <local_ipv4> | <local_ipv6> } ] [ interface <interfacename> ]
no peer { <peer_ipv4> | <peer_ipv6> } [ vrf <vrfname> ] [ multihop ] [ local-address { <local_ipv4> | <local_ipv6> } ] [ interface <interfacename> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| peer_ipv4 | A.B.C.D  | String  |
| peer_ipv6 | A::B  | String  |
| vrfname | String  | String  |
| local_ipv4 | A.B.C.D  | String  |
| local_ipv6 | A::B  | String  |
| interfacename | String  | String  |
### Usage Guidelines 
```
For single-hop BFD peer interace must be configured and for multi-hop BFD peer local address must be configured.
For link-local BFD Peer address, it is mandatory to configure link-local local address.
```
### Examples 
#### Configure single-hop BFD peer with default vrf 
```
device()#configure terminal
device(config)#bfd
device(conf-bfd)# peer 192.168.0.5 interface Ethernet0
```
#### Configure single-hop BFD peer with user vrf 
```
device()#configure terminal
device(config)#bfd
device(conf-bfd)# peer 192.168.0.5 interface Ethernet0 vrf Vrf1
```
#### Configure multi-hop BFD peer with default vrf 
```
device()#configure terminal
device(config)#bfd
device(conf-bfd)# peer 192.168.0.2 multihop local-address 192.168.0.3
```
#### Configure multi-hop BFD peer with user vrf 
```
device()#configure terminal
device(config)#bfd
device(conf-bfd)# peer 192.168.0.2 multihop local-address 192.168.0.3 vrf Vrf1
```
## peer-group 
### Description 
```
This command creates a BGP peer-group
```
### Parent Commands (Modes) 
```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }
```
### Syntax 
```
peer-group <template-str>
no peer-group <template>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| template-str | WORD  | String  |
### Usage Guidelines 
```
Use this command to create a BGP peer-group.
```
### Examples 
#### Below command creates a BGP peer-group named PG_Ext 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)#
```
## peer-group 
### Description 
```
This command assigns a BGP neighbor to a peer-group
```
### Parent Commands (Modes) 
```
neighbor { <ip> | { interface { Ethernet | PortChannel | Vlan } } }
```
### Syntax 
```
peer-group <template-name>
no peer-group [ <template-name> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| template-name | WORD  | String  |
### Usage Guidelines 
```
Assigning a BGP neighbor to a peer-group will inherit parameters from
peer-group.
```
### Examples 
#### Following command assigns neighbor 30.30.30.3 to          peer-group PG_Ext 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 30.30.30.3
sonic(config-router-bgp-neighbor)# peer-group PG_Ext
```
## peer-ip 
### Description 
```
Configures MCLAG session's peer IP address
```
### Parent Commands (Modes) 
```
mclag domain <mclag-domain-id>
```
### Syntax 
```
peer-ip <PIP>
no peer-ip
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| PIP | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to configure/change MCLAG session's peer ip address
```
### Examples 
#### Configures MCLAG peer ip address for MCLAG domain 100 
```
sonic-cli(config-mclag-domain-100)#peer-ip 10.1.1.2
```
## peer-link 
### Description 
```
Configures MCLAG peer interface
```
### Parent Commands (Modes) 
```
mclag domain <mclag-domain-id>
```
### Syntax 
```
peer-link { <eth-if> | <po-if> }
no peer-link
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| eth-if | EthernetNUM  |   |
| po-if | PortChannelNUM  |   |
### Usage Guidelines 
```
Use this command to configure/change MCLAG session's peer link
```
### Examples 
#### Configures MCLAG peer link for MCLAG domain 100 
```
sonic-cli(config-mclag-domain-100)#peer-link Eth 1/12/1
```
## pfc-priority 
### Description 
```
This command to add PFC Priority to queue entry in map.
```
### Parent Commands (Modes) 
```
qos map pfc-priority-queue <name>
```
### Syntax 
```
pfc-priority <dot1p_list> { queue <qid> }
no pfc-priority <dot1p_list>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| dot1p_list |   | String  |
| qid |   | Integer  |
### Usage Guidelines 
```
Use this command to add entry to map PFC Priority to queue.
```
### Examples 
#### The following command add entry to map PFC Priority to queue 
```
sonic# configure terminal
sonic(config)# dot1p 1 queue 0
sonic(config)# dot1p 2 queue 0
sonic(config)# dot1p 3 queue 1
```
## ping 

### Description 

```
Send ICMP ECHO_REQUEST to network hosts 


```
### Syntax 

```
ping

```
## ping vrf 

### Description 

```
Select VRF instance 


```
### Syntax 

```
ping vrf <vrf-name>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrf-name | VRF name (prefixed by Vrf, Max: 15 characters)  | String  |


## ping vrf mgmt 

### Description 

```
Ping using management VRF 


```
### Syntax 

```
ping vrf mgmt

```
## ping6 

### Description 

```
Send ICMPv6 ECHO_REQUEST to network hosts 


```
### Syntax 

```
ping6

```
## ping6 vrf 

### Description 

```
Select VRF instance 


```
### Syntax 

```
ping6 vrf <vrf-name>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrf-name | VRF name (prefixed by Vrf, Max: 15 characters)  | String  |


## ping6 vrf mgmt 

### Description 

```
Ping6 using management VRF 


```
### Syntax 

```
ping6 vrf mgmt

```
## pir 
### Description 
```
Peak Information Rate 
```
### Parent Commands (Modes) 
```
queue <sequence>
```
### Syntax 
```
pir <pir>
no pir
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| pir |   | Integer  |
## pir 
### Description 
```
Peak Information Rate in Kbps 
```
### Parent Commands (Modes) 
```
port
```
### Syntax 
```
pir <pir>
no pir
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| pir |   | Integer  |
## police 
### Description 
```
Configures policer action for Qos Flow
```
### Parent Commands (Modes) 
```
class <fbs-class-name> [ priority <fbs-flow-priority> ]
```
### Syntax 
```
police { { cir <cir-value> { [ cbs <cbs-value> ] } { [ pir <pir-value> ] } { [ pbs <pbs-value> ] } } | { cbs <cbs-value> { [ pir <pir-value> ] } { [ pbs <pbs-value> ] } } | { pir <pir-value> { [ pbs <pbs-value> ] } } | { pbs <pbs-value> } }
no police [ cir ] [ cbs ] [ pir ] [ pbs ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| cir-value |   | String  |
| cbs-value |   | String  |
| pir-value |   | String  |
| pbs-value |   | String  |
### Usage Guidelines 
```
CIR: Committed information rate in bits per second. CIR is mandatory. The value can be optionally suffixed with kbps(1000), mbps(1000000), gbps (1000000000) or tbps (1000000000000)cir 300000000 cbs 300000000 pir 300000000 pbs 300000000.
CBS: Committed burst size in bytes. The value can be suffixed with KB(1000), MB(1000000), GB(1000000000) or TB(1000000000000). The default value is 20% of the CIR in bytes. If configured by the user, it must be greater than or equal to CIR in bytes.
PIR: Peak information rate in bits per second. The value can be optionally suffixed with kbps(1000), mbps(1000000), gbps (1000000000) or tbps (1000000000000). If configured by the user, it must be greater than CIR
PBS: Peak burst size. The value can be suffixed with KB(1000), MB(1000000), GB(1000000000) or TB(1000000000000). The default value is 20% of the PIR value in bytes. If configured by the user, it must be greater than PIR value in bytes and also CBS value
If only CIR is configured, then its 1 rate, 2 color policer.  Any traffic exceeding CIR value will be marked as red and will be dropped.
If both CIR and PIR is configured, then is 2 rate 3 color policer. Any traffic that exceeds CIR but less than PIR will be marked as yellow. Any traffic that is more than PIR will be marked as red and will be dropped
```
### Examples 
#### Configures traffic class action for Qos Flow 
```
sonic(config)# policy-map policy_qos type qos
sonic(config-policy-map)# class class_permit_ip priority 10
sonic(config-policy-map-flow)#cir 300000000 cbs 300000000 pir 300000000 pbs 300000000
```
## police 
### Description 
```
Set rate limiting parameters
```
### Parent Commands (Modes) 
```
copp-action <copp-action-name>
```
### Syntax 
```
police { { cir <cir-value> { [ cbs <cbs-value> ] } } | { cbs <cbs-value> } }
no police [ cir ] [ cbs ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| cir-value |   | String  |
| cbs-value |   | String  |
### Examples 
#### Set rate parameters 
```
sonic(config-action)# police cir 6000 cbs 6000
sonic(config-action)#
```
#### Set mode parameters 
```
sonic(config-action)# police mode sr_tcm red drop
sonic(config-action)#
```
## policy-map 
### Description 
```
Configures policy-map
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
policy-map <fbs-policy-name> type { qos | monitoring | forwarding | copp }
no policy-map <fbs-policy-name>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| fbs-policy-name | WORD  | String  |
### Usage Guidelines 
```
Policy-map name can be of maximum 63 characters. The name must begin with A-Z, a-z or 0-9. Underscore and hypens can be used except as the first character
```
### Examples 
#### Creates policy-map of type Forwarding 
```
sonic(config)# policy-map policy_vrf type forwarding
```
## pool 
### Description 
```
Create NAT pool 
```
### Parent Commands (Modes) 
```
nat
```
### Syntax 
```
pool <pool-name> <global-ip-range> [ <global-port-range> ]
no pool <pool-name>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| pool-name | String  | String  |
| global-ip-range | String  | String  |
| global-port-range | [1-65535]-[1-65535]  | String  |
## port 
### Description 
```
This command sets TCP port for a BGP neighbor.
```
### Parent Commands (Modes) 
```
neighbor { <ip> | { interface { Ethernet | PortChannel | Vlan } } }
```
### Syntax 
```
port <tcpport>
no port
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| tcpport |   | Integer  |
### Usage Guidelines 
```
Use this command to set a specific port for BGP neighbor. This command
is optional.
```
### Examples 
#### Following command configures TCP port for BGP neighbor          30.30.30.3 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 30.30.30.3
sonic(config-router-bgp-neighbor)# port 61356
```
## port 
### Description 
```
This command sets TCP port for BGP neighbors in a peer-group.
```
### Parent Commands (Modes) 
```
peer-group <template-str>
```
### Syntax 
```
port <tcpport>
no port
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| tcpport |   | Integer  |
### Usage Guidelines 
```
Use this command to set a specific port for BGP neighbors in a
peer-group. This command is optional.
```
### Examples 
#### Following command configures TCP port for BGP peer-group          PG_Ext 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# port 65001
```
## port 
### Description 
```
Scheduler for interface 
```
### Parent Commands (Modes) 
```
qos scheduler-policy <name>
```
### Syntax 
```
port
no port
```
## port-group 
### Description 
```
This command configure port speed for the member ports of a port-group.
The port-group is not supported in all the hardware plaforms.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
port-group <pg> { speed <port_speed> }
no port-group <pg> speed
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| pg | Port-group (id)  |   |
| port_speed | Port speed  | Select [10(10MBPS) 100(100MBPS) 1000(1GIGE) 10000(10GIGE) 25000(25GIGE) 40000(40GIGE) 100000(100GIGE) ]  |
### Usage Guidelines 
```
Use this command to change the speed of the ports for the platform which supports port-group.
 port-group id speed speed-in-Mbps
```
### Examples 
```
sonic# configure terminal
sonic(config)# port-group 1 speed 10000
```
## portchannel graceful-shutdown 
### Description 
```
Enable portchannel graceful shutdown
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
portchannel graceful-shutdown
no portchannel graceful-shutdown
```
### Usage Guidelines 
```
Use this command to enable portchannel graceful shutdown
```
### Examples 
#### Enable portchannel graceful shutdown 
```
sonic-cli(config)# portchannel graceful-shutdown
```
## preempt 
### Description 
```
Configure preempt for IPv4 VRRP instance
```
### Parent Commands (Modes) 
```
vrrp ipv4
```
### Syntax 
```
preempt
no preempt
```
### Examples 
#### Configure preempt for IPv4 VRRP instance 
```
sonic(config)# interface Ethernet4
sonic(conf-if-Ethernet4)#
sonic(conf-if-Ethernet4)# vrrp 1 address-family ipv4
sonic(conf-if-Ethernet4-vrrp-ipv4-1)#preempt
```
## preempt 
### Description 
```
Configure preempt for IPv6 VRRP instance
```
### Parent Commands (Modes) 
```
vrrp ipv6
```
### Syntax 
```
preempt
no preempt
```
### Examples 
#### Configure preempt for IPv6 VRRP instance 
```
sonic(config)# interface Ethernet4
sonic(conf-if-Ethernet4)#
sonic(conf-if-Ethernet4)# vrrp 1 address-family ipv6
sonic(conf-if-Ethernet4-vrrp-ipv6-1)#preempt
```
## prefix-list 
### Description 
```
Filter updates to/from this neighbor 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
prefix-list <pname> { in | out }
no prefix-list <pname> { in | out }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| pname | WORD  | String  |
## prefix-list 
### Description 
```
Filter updates to/from this neighbor 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
prefix-list <pname> { in | out }
no prefix-list <pname> { in | out }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| pname | WORD  | String  |
## prefix-list 
### Description 
```
This command configures prefix list for a BGP neighbor
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
prefix-list <pname> { in | out }
no prefix-list <pname> { in | out }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| pname | WORD  | String  |
### Usage Guidelines 
```
Use this command to define policy (route filtering) for a BGP neighbor
in outbound or/and inbound direction.
```
### Examples 
#### Following command configures a prefix-list          pl_allow_remote in inbound direction for neighbor 20.20.20.2 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 20.20.20.2
sonic(config-router-bgp-neighbor)# remote-as 300
sonic(config-router-bgp-neighbor)# address-family ipv4 unicast
sonic(config-router-bgp-neighbor-af)# prefix-list pl_allow_remote in
```
## prefix-list 
### Description 
```
This command configures prefix list for a BGP peer-group
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
prefix-list <pname> { in | out }
no prefix-list <pname> { in | out }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| pname | WORD  | String  |
### Usage Guidelines 
```
Use this command to define policy (route filtering) for a BGP peer-group
in outbound or/and inbound direction.
```
### Examples 
#### Following command configures a prefix-list          pl_allow_remote in inbound direction for peer-group PG_Ext 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# address-family ipv4 unicast
sonic(config-router-bgp-pg-af)# prefix-list pl_allow_remote in
```
## priority 
### Description 
```
Configure priority for IPv4 VRRP instance
```
### Parent Commands (Modes) 
```
vrrp ipv4
```
### Syntax 
```
priority <priority-value>
no priority
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| priority-value |   | Integer  |
### Examples 
#### Configure priority for IPv4 VRRP instance 
```
sonic(config)# interface Ethernet4
sonic(conf-if-Ethernet4)#
sonic(conf-if-Ethernet4)# vrrp 1 address-family ipv4
sonic(conf-if-Ethernet4-vrrp-ipv4-1)#priority 120
```
## priority 
### Description 
```
Configure priority for IPv6 VRRP instance
```
### Parent Commands (Modes) 
```
vrrp ipv6
```
### Syntax 
```
priority <priority-value>
no priority
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| priority-value |   | Integer  |
### Examples 
#### Configure priority for IPv6 VRRP instance 
```
sonic(config)# interface Ethernet4
sonic(conf-if-Ethernet4)#
sonic(conf-if-Ethernet4)# vrrp 1 address-family ipv6
sonic(conf-if-Ethernet4-vrrp-ipv6-1)#priority 120
```
## priority-flow-control 
### Description 
```
PFC Configuration 
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
priority-flow-control { { priority <dot1p> } | asymmetric }
no priority-flow-control { { priority [ <dot1p> ] } | asymmetric }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| dot1p |   | Integer  |
## priority-flow-control watchdog action 
### Description 
```
PFC watchdog storm action 
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
priority-flow-control watchdog action { alert | drop | forward }
no priority-flow-control watchdog action
```
## priority-flow-control watchdog counter-poll 
### Description 
```
Enable PFC watchdog FLEX counters 
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
priority-flow-control watchdog counter-poll
no priority-flow-control watchdog counter-poll
```
## priority-flow-control watchdog detect-time 
### Description 
```
PFC watchdog detection time 
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
priority-flow-control watchdog detect-time <detection-time>
no priority-flow-control watchdog detect-time
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| detection-time |   | Integer  |
## priority-flow-control watchdog off 

### Description 

```
PFC watchdog disable 


```
### Parent Commands (Modes) 

```
interface <phy-if-name>

```
### Syntax 

```
priority-flow-control watchdog off

```
## priority-flow-control watchdog on 

### Description 

```
PFC watchdog enable 


```
### Parent Commands (Modes) 

```
interface <phy-if-name>

```
### Syntax 

```
priority-flow-control watchdog on

```
## priority-flow-control watchdog polling-interval 
### Description 
```
Configure watchdog 
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
priority-flow-control watchdog polling-interval <interval>
no priority-flow-control watchdog polling-interval
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| interval |   | Integer  |
## priority-flow-control watchdog restore-time 
### Description 
```
PFC watchdog restoration time 
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
priority-flow-control watchdog restore-time <restore-time>
no priority-flow-control watchdog restore-time
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| restore-time |   | Integer  |
## ptp announce-timeout 

### Description 

```
Configure PTP announce receipt timeout value. The default value is 3.


```
### Parent Commands (Modes) 

```
configure terminal

```
### Syntax 

```
ptp announce-timeout <ptp_announce_timeout>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| ptp_announce_timeout |   | Integer  |


### Examples 

```
sonic(config)# ptp announce-timeout 2
Success


```
## ptp domain 

### Description 

```
Configure PTP domain


```
### Parent Commands (Modes) 

```
configure terminal

```
### Syntax 

```
ptp domain <ptp_domain>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| ptp_domain |   | Integer  |


### Examples 

```
sonic(config)# ptp domain 1
Success


```
## ptp domain-profile 

### Description 

```
Configure PTP domain profile


```
### Parent Commands (Modes) 

```
configure terminal

```
### Syntax 

```
ptp domain-profile <ptp_domain_profile>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| ptp_domain_profile |   | Select [default(ieee1588) g8275.1(G.8275.1) g8275.2(G.8275.2) ]  |


### Examples 

```
sonic(config)# ptp domain-profile default
Success


```
## ptp ipv6-scope 

### Description 

```
Configure PTP IPv6 multicast address scope


```
### Parent Commands (Modes) 

```
configure terminal

```
### Syntax 

```
ptp ipv6-scope <ptp_ipv6_scope>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| ptp_ipv6_scope | Hexadecimal range (0x0 to 0xf)  | String  |


### Examples 

```
sonic(config)# ptp ipv6-scope 0xe
Success


```
## ptp log-announce-interval 

### Description 

```
Configure PTP log announce interval value. The interval should be the sam in the whole domain. It's specified as a power of two in seconds. The default is 1 (2 seconds).


```
### Parent Commands (Modes) 

```
configure terminal

```
### Syntax 

```
ptp log-announce-interval <ptp_announce_interval>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| ptp_announce_interval |   | Integer  |


### Examples 

```
sonic(config)# ptp log-announce-interval 0
Success


```
## ptp log-min-delay-req-interval 

### Description 

```
Configure PTP log min delay req interval value. It is specified as a power of two in seconds. The default is 0 (1 second).


```
### Parent Commands (Modes) 

```
configure terminal

```
### Syntax 

```
ptp log-min-delay-req-interval <ptp_delay_request_interval>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| ptp_delay_request_interval |   | Integer  |


### Examples 

```
sonic(config)# ptp log-min-delay-req-interval 0
Success


```
## ptp log-sync-interval 

### Description 

```
Configure PTP log sync interval value. It is specified as a power of two in seconds. The default is 0 (1 second).


```
### Parent Commands (Modes) 

```
configure terminal

```
### Syntax 

```
ptp log-sync-interval <ptp_sync_interval>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| ptp_sync_interval |   | Integer  |


### Examples 

```
sonic(config)# ptp log-sync-interval 0
Success


```
## ptp mode 

### Description 

```
Configure PTP clock type


```
### Parent Commands (Modes) 

```
configure terminal

```
### Syntax 

```
ptp mode <mode_type>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| mode_type | PTP mode  | Select [boundary-clock(BC) peer-to-peer-transparent-clock(P2P_TC) end-to-end-transparent-clock(E2E_TC) disable(disable) ]  |


### Examples 

```
sonic(config)# ptp mode boundary-clock
Success


```
## ptp network-transport 

### Description 

```
Configure PTP network-transport


```
### Parent Commands (Modes) 

```
configure terminal

```
### Syntax 

```
ptp network-transport <ptp_network_transport_type> <ptp_master_slave>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| ptp_network_transport_type |   | Select [l2(L2) ipv4(UDPv4) ipv6(UDPv6) ]  |
| ptp_master_slave | unicast/multicast  | Select [unicast(unicast) multicast(multicast) ]  |


### Examples 

```
sonic(config)# ptp network-transport ipv4 unicast
Success


```
## ptp port add 

### Description 

```
Add a PTP port


```
### Parent Commands (Modes) 

```
configure terminal

```
### Syntax 

```
ptp port add <Ethernet>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| Ethernet | EthernetNUM  |   |


### Examples 

```
sonic(config)# ptp port add Ethernet 64
Success


```
## ptp port del 

### Description 

```
Delete a PTP port


```
### Parent Commands (Modes) 

```
configure terminal

```
### Syntax 

```
ptp port del <Ethernet>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| Ethernet | EthernetNUM  |   |


### Examples 

```
sonic(config)# ptp port del Ethernet 64
Success


```
## ptp port master-table 

### Description 

```
Add/Delete a master IP/MAC from the master table for the designated slave port


```
### Parent Commands (Modes) 

```
configure terminal

```
### Syntax 

```
ptp port master-table { <Ethernet> { { add { <l3_ip> | <mac> } } | { del { <l3_ip> | <mac> } } } }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| Ethernet | EthernetNUM  |   |
| l3_ip | A.B.C.D/A::B  | String  |
| mac | nn:nn:nn:nn:nn:nn  | String  |


### Examples 

```
sonic(config)# ptp port master-table Ethernet 64 add 10.1.1.1
Success
sonic(config)# ptp port master-table Ethernet 64 del 10.1.1.1
Success


```
## ptp priority1 

### Description 

```
Configure PTP priority1 value


```
### Parent Commands (Modes) 

```
configure terminal

```
### Syntax 

```
ptp priority1 <ptp_priority1>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| ptp_priority1 |   | Integer  |


### Examples 

```
sonic(config)# ptp priority1 128
Success


```
## ptp priority2 

### Description 

```
Configure PTP priority2 value


```
### Parent Commands (Modes) 

```
configure terminal

```
### Syntax 

```
ptp priority2 <ptp_priority2>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| ptp_priority2 |   | Integer  |


### Examples 

```
sonic(config)# ptp priority2 128
Success


```
## ptp two-step 

### Description 

```
Configure PTP two-step mode


```
### Parent Commands (Modes) 

```
configure terminal

```
### Syntax 

```
ptp two-step <ptp_two_step>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| ptp_two_step | enable or disable  | Select [enable(enable) disable(disable) ]  |


### Examples 

```
sonic(config)# ptp two-step enable
Success


```
## qos map dot1p-tc 
### Description 
```
This command creates map to associates set of DOT1P to Traffic classes.
This map used to assign a traffic class to data packets on the
basis of the received packets DOT1P field.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
qos map dot1p-tc <name>
no qos map dot1p-tc <name>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| name | WORD  | String  |
### Usage Guidelines 
```
Use this command to create DOT1P to Traffic class entires map.
```
### Examples 
#### The following command creates DOT1P to Traffic class entrie map. 
```
sonic# configure terminal
sonic(config)#qos map dot1p-tc dot1p-map
```
## qos map dscp-tc 
### Description 
```
This command creates map to associates set of DSCP(Differentiated Services Code Point)
to Traffic classes. This map used to assign a traffic class to data packets on the
basis of the received packets DSCP field.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
qos map dscp-tc <name>
no qos map dscp-tc <name>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| name | WORD  | String  |
### Usage Guidelines 
```
Use this command to create DSCP to Traffic class entires map.
```
### Examples 
#### The following command creates DSCP to Traffic class entrie map. 
```
sonic# configure terminal
sonic(config)# qos map dscp-tc dscp-map
```
## qos map pfc-priority-queue 
### Description 
```
This command creates map to associates set of PFC Priorities to Queues.
This map is used to classify a queue for data packets on the
basis of the received packets DOT1P field.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
qos map pfc-priority-queue <name>
no qos map pfc-priority-queue <name>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| name | WORD  | String  |
### Usage Guidelines 
```
Use this command to create PFC Priority to Queue entires map.
```
### Examples 
#### The following command creates DOT1P to queue entrie map. 
```
sonic# configure terminal
sonic(config)#qos map pfc-priority-queue pfc-priority-queue-map
```
## qos map tc-dot1p 
### Description 
```
This command creates map to associates set of TC(Traffic Class) to
DOT1P(Vlan PCP value).
This map is used to Remark the DOT1P field in egress packets
on the basis of the internal Traffic Class.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
qos map tc-dot1p <name>
no qos map tc-dot1p <name>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| name | WORD  | String  |
### Usage Guidelines 
```
Use this command to create Traffic class to DOT1P entries map.
```
### Examples 
#### The following command creates Traffic class to DOT1P entries map. 
```
sonic# configure terminal
sonic(config)# qos map tc-dot1p tc_dot1p
```
## qos map tc-dscp 
### Description 
```
This command creates map to associates set of TC(Traffic Class) to
DSCP(Differentiated Services Code Point).
This map is used to Remark the DSCP field in egress packets
on the basis of the internal Traffic Class.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
qos map tc-dscp <name>
no qos map tc-dscp <name>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| name | WORD  | String  |
### Usage Guidelines 
```
Use this command to create Traffic class to DSCP entries map.
```
### Examples 
#### The following command creates Traffic class to DSCP entries map. 
```
sonic# configure terminal
sonic(config)# qos map tc-dscp tc_dscp
```
## qos map tc-pg 
### Description 
```
This command creates map to associates set of TC(Traffic Class)
to queue. This map is used to assign a priority group to data packets on the
basis of the traffic class.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
qos map tc-pg <name>
no qos map tc-pg <name>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| name | WORD  | String  |
### Usage Guidelines 
```
Use this command to create Traffic class to Priority-Group entires map.
```
### Examples 
#### The following command creates Traffic class to Priority-Group entrie map. 
```
sonic# configure terminal
sonic(config)#qos map tc-pg tc-pg-map
```
## qos map tc-queue 
### Description 
```
This command creates map to associates set of TC(Traffic Class)
to queue. This map is used to assign an egress queue to data packets on the
basis of the traffic class.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
qos map tc-queue <name>
no qos map tc-queue <name>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| name | WORD  | String  |
### Usage Guidelines 
```
Use this command to create Traffic class to Queue entires map.
```
### Examples 
#### The following command creates Traffic class to Queue entrie map. 
```
sonic# configure terminal
sonic(config)# qos map tc-queue tc-queue-map
```
## qos scheduler-policy 
### Description 
```
Scheduler Policy Configuration 
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
qos scheduler-policy <name>
no qos scheduler-policy <name>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| name | WORD  | String  |
## qos wred-policy 
### Description 
```
This command creates WRED policy
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
qos wred-policy <name>
no qos wred-policy <name>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| name | WORD  | String  |
### Usage Guidelines 
```
Use this command to create WRED policy.
```
### Examples 
#### The following command creates WRED policy named wred-green 
```
sonic# configure terminal
sonic(config)# qos wred-policy wred-green
```
## qos-map dot1p-tc 
### Description 
```
DOT1P to TC map configuration 
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
qos-map dot1p-tc <dot1p_tc_map_name>
no qos-map dot1p-tc
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| dot1p_tc_map_name | WORD  | String  |
## qos-map dot1p-tc 
### Description 
```
DOT1P to TC map configuration 
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
qos-map dot1p-tc <dot1p_tc_map_name>
no qos-map dot1p-tc
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| dot1p_tc_map_name | WORD  | String  |
## qos-map dscp-tc 
### Description 
```
DSCP to TC map configuration 
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
qos-map dscp-tc <dscp_tc_map_name>
no qos-map dscp-tc
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| dscp_tc_map_name | WORD  | String  |
## qos-map dscp-tc 
### Description 
```
DSCP to TC map configuration 
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
qos-map dscp-tc <dscp_tc_map_name>
no qos-map dscp-tc
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| dscp_tc_map_name | WORD  | String  |
## qos-map dscp-tc 
### Description 
```
DSCP to TC map configuration 
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
qos-map dscp-tc <dscp_tc_map_name>
no qos-map dscp-tc
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| dscp_tc_map_name | WORD  | String  |
## qos-map pfc-priority-queue 
### Description 
```
PFC Priority to Queue map configuration 
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
qos-map pfc-priority-queue <pfc_priority_queue_map_name>
no qos-map pfc-priority-queue
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| pfc_priority_queue_map_name | WORD  | String  |
## qos-map tc-dot1p 
### Description 
```
TC to DOT1P map configuration 
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
qos-map tc-dot1p <tc_dot1p_map_name>
no qos-map tc-dot1p
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| tc_dot1p_map_name | WORD  | String  |
## qos-map tc-dot1p 
### Description 
```
TC to DOT1P map configuration 
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
qos-map tc-dot1p <tc_dot1p_map_name>
no qos-map tc-dot1p
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| tc_dot1p_map_name | WORD  | String  |
## qos-map tc-dscp 
### Description 
```
TC to DSCP map configuration 
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
qos-map tc-dscp <tc_dscp_map_name>
no qos-map tc-dscp
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| tc_dscp_map_name | WORD  | String  |
## qos-map tc-dscp 
### Description 
```
TC to DSCP map configuration 
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
qos-map tc-dscp <tc_dscp_map_name>
no qos-map tc-dscp
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| tc_dscp_map_name | WORD  | String  |
## qos-map tc-dscp 
### Description 
```
TC to DSCP map configuration 
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
qos-map tc-dscp <tc_dscp_map_name>
no qos-map tc-dscp
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| tc_dscp_map_name | WORD  | String  |
## qos-map tc-pg 
### Description 
```
TC to priority group map configuration 
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
qos-map tc-pg <tc_pg_map_name>
no qos-map tc-pg
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| tc_pg_map_name | WORD  | String  |
## qos-map tc-queue 
### Description 
```
TC to Queue map configuration 
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
qos-map tc-queue <tc_queue_map_name>
no qos-map tc-queue
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| tc_queue_map_name | WORD  | String  |
## qos-mode 

### Description 

```
Configure QoS Mode 


```
### Parent Commands (Modes) 

```
interface vxlan <vxlan-if-name>

```
### Syntax 

```
qos-mode { uniform | { pipe { dscp <dscp-value> } } }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| dscp-value |   | Integer  |


## queue 
### Description 
```
Scheduler for queues 
```
### Parent Commands (Modes) 
```
qos scheduler-policy <name>
```
### Syntax 
```
queue <sequence>
no queue <sequence>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| sequence |   | Integer  |
## queue 
### Description 
```
Queue configuration 
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
queue <qid> { wred-policy <wred_prof_name> }
no queue <qid> wred-policy
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| qid |   | Integer  |
| wred_prof_name | WORD  | String  |
## radius-server auth-type 
### Description 
```
Configures global auth-type for RADIUS.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
radius-server auth-type <auth_type>
no radius-server auth-type
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| auth_type | authentication type  | Select [pap(pap) chap(chap) mschapv2(mschapv2) ]  |
### Examples 
```
sonic(config)# radius-server auth-type chap
```
## radius-server host 
### Description 
```
Configures a server for RADIUS.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
radius-server host <host> [ auth-port <vauth_port> ] [ auth-type <vauth_type> ] [ key <vkey> ] [ priority <vpriority> ] [ retransmit <vretransmit> ] [ source-interface { Ethernet | Loopback | Management | PortChannel | Vlan } ] [ timeout <vtimeout> ] [ vrf { mgmt | <vrf-name> } ]
no radius-server host <host> { [ auth-port ] | [ auth-type ] | [ key ] | [ priority ] | [ retransmit ] | [ source-interface ] | [ timeout ] | [ vrf ] } ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| host | WORD  | String  |
| vauth_port | port  | Integer  |
| vauth_type | authentication type  | Select [pap(pap) chap(chap) mschapv2(mschapv2) ]  |
| vkey | WORD  | String  |
| vpriority | (1..64)  | Integer  |
| vretransmit | attempts  | Integer  |
| vtimeout | seconds  | Integer  |
| vrf-name | WORD  | String  |
### Examples 
```
sonic(config)# radius-server host 10.59.100.2 key testing123
```
## radius-server key 
### Description 
```
Configures global shared secret for RADIUS.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
radius-server key <key>
no radius-server key
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| key | WORD  | String  |
### Examples 
```
sonic(config)# radius-server key testing123
```
## radius-server nas-ip 
### Description 
```
Configures global NAS-IP|IPV6-Address (Type 4|95) attribute for RADIUS PDU.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
radius-server nas-ip <nas_ip>
no radius-server nas-ip
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| nas_ip | A.B.C.D/A::B  | String  |
### Examples 
```
sonic(config)# radius-server nas-ip 10.59.100.2
```
## radius-server retransmit 
### Description 
```
Configures global timeout for RADIUS.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
radius-server retransmit <retransmit>
no radius-server retransmit
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| retransmit | attempts  | Integer  |
### Examples 
```
sonic(config)# radius-server timeout 3
```
## radius-server source-ip 
### Description 
```
Configure global source ip for RADIUS 
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
radius-server source-ip <source_ip>
no radius-server source-ip
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| source_ip | A.B.C.D/A::B  | String  |
## radius-server statistics 

### Description 

```
Configures global statistics collection for RADIUS.

```
### Parent Commands (Modes) 

```
configure terminal

```
### Syntax 

```
radius-server statistics <enable>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| enable | enable or disable  | Select [enable(enable) disable(disable) ]  |


### Examples 

```
sonic(config)# radius-server statistics enable


```
## radius-server timeout 
### Description 
```
Configures global timeout for RADIUS.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
radius-server timeout <timeout>
no radius-server timeout
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| timeout | seconds  | Integer  |
### Examples 
```
sonic(config)# radius-server timeout 3
```
## rd 
### Description 
```
This command specifies the route-distinguisher to be attached to routes exported from current VRF into EVPN
```
### Parent Commands (Modes) 
```
address-family l2vpn evpn
```
### Syntax 
```
rd <rdvalue>
no rd <rdvalue>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| rdvalue | A.B.C.D:NN or ASN:NN  | String  |
### Usage Guidelines 
```
[no] rd {route-distinguisher}
```
### Examples 
#### Following command attaches route-distinguisher 11:11 to routes exported fron vrf Vrf1 
```
sonic# configure terminal
sonic(config)# router bgp 100 vrf Vrf1
sonic(config-router-bgp)# address-family l2vpn evpn
sonic(config-router-bgp-af)# rd 11:11
```
## rd 
### Description 
```
This command specifies the route-distinguisher to be attached to routes exported from current VRF into EVPN
```
### Parent Commands (Modes) 
```
vni <vninum>
```
### Syntax 
```
rd <rdvalue>
no rd <rdvalue>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| rdvalue | A.B.C.D:NN or ASN:NN  | String  |
### Usage Guidelines 
```
[no] rd {route-distinguisher}
```
### Examples 
#### Following command attaches route-distinguisher 11:11 to routes exported fron vrf Vrf1 
```
sonic# configure terminal
sonic(config)# router bgp 100 vrf Vrf1
sonic(config-router-bgp)# address-family l2vpn evpn
sonic(config-router-bgp-af)# vni 100
sonic(config-router-bgp-af-vni)# rd 11:11
```
## read-quanta 
### Description 
```
This command configures the maximum number of BGP packets to read from
peer socker in one cycle of I/O
```
### Parent Commands (Modes) 
```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }
```
### Syntax 
```
read-quanta <rdval>
no read-quanta
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| rdval |   | Integer  |
### Usage Guidelines 
```
BGP packets are read off the wire one at a time in a loop. This setting
controls how many iterations the loop runs for. It is best to leave this
setting on the default.
```
### Examples 
#### Below command configures the read-quanta to          6 packets 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# read-quanta 6
```
## reboot 

### Description 

```
reboot [options] (-h shows help) 


```
### Syntax 

```
reboot [ <options> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| options | options  | String  |


## receive-interval 

### Description 

```
Configure packet receive interval for Bidirectional Forwarding detection(BFD) peer.


```
### Parent Commands (Modes) 

```
peer <peer_ipv4>
peer <peer_ipv6>
peer [ interface ] <interfacename>
peer [ local-address ] <local_ipv4>
peer [ local-address ] <local_ipv6>
peer [ multihop ]
peer [ vrf ] <vrfname>

```
### Syntax 

```
receive-interval <receive_interval>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| receive_interval |   | Integer  |


### Usage Guidelines 

```
This command can be used to configure desired packet receive interval from BFD peer, default value is 300 milliseconds.


```
### Examples 
#### Configure packet receive interval 

```
device()#configure terminal
device(config)#bfd
device(conf-bfd)# peer 192.168.0.5 interface Ethernet0
device(conf-bfd-peer)# receive-interval 200


```
## redistribute 
### Description 
```
Configures route redistribution into OSPFv2 router.
```
### Parent Commands (Modes) 
```
router ospf [ vrf <vrf-name> ]
```
### Syntax 
```
redistribute { { bgp { [ metric <bgpmetricval> ] } { [ metric-type <bgpmertrictype> ] } { [ route-map <bgproutemapname> ] } } | { connected { [ metric <connmetricval> ] } { [ metric-type <connmertrictype> ] } { [ route-map <connroutemapname> ] } } | { static { [ metric <staticmetricval> ] } { [ metric-type <staticmertrictype> ] } { [ route-map <staticroutemapname> ] } } | { kernel { [ metric <kernelmetricval> ] } { [ metric-type <kernelmertrictype> ] } { [ route-map <kernelroutemapname> ] } } }
no redistribute { { bgp [ metric ] [ metric-type ] [ route-map ] } | { connected [ metric ] [ metric-type ] [ route-map ] } | { static [ metric ] [ metric-type ] [ route-map ] } | { kernel [ metric ] [ metric-type ] [ route-map ] } }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| bgpmetricval |   | Integer  |
| bgpmertrictype |   | Integer  |
| bgproutemapname | WORD  | String  |
| connmetricval |   | Integer  |
| connmertrictype |   | Integer  |
| connroutemapname | WORD  | String  |
| staticmetricval |   | Integer  |
| staticmertrictype |   | Integer  |
| staticroutemapname | WORD  | String  |
| kernelmetricval |   | Integer  |
| kernelmertrictype |   | Integer  |
| kernelroutemapname | WORD  | String  |
### Usage Guidelines 
```
Use this command to configure route redistribution into OSPFv2 router. Redistributed
route's metric and metric-type can be modified using this commands. Route map can
also be aslo be applied to redistributed route. Unconfiguring any of the redistribute
attribute for a protocol will unconfigure all attributes.
```
### Examples 
#### Configures route redistribution into OSPFv2 router. 
```
sonic-cli(config-router-ospf)# redistribute bgp
sonic-cli(config-router-ospf)# redistribute bgp route-map bgpospfrmap
sonic-cli(config-router-ospf)# redistribute static metric 10 metrict-type 1 routemap redist_rmap
sonic-cli(config-router-ospf)# redistribute connected metric 10 metrict-type 1
```
### Features this CLI belongs to 
*  OSPFv2
## redistribute 
### Description 
```
Redistribute information from another routing protocol 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
redistribute { connected | static | ospfv3 } [ route-map <route-map-name> ] [ metric <metvalue> ]
no redistribute { connected | static | ospfv3 } [ route-map <route-map-name> ] [ metric <metvalue> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| route-map-name | WORD  | String  |
| metvalue |   | Integer  |
## redistribute 
### Description 
```
Redistribute information from another routing protocol to BGP. User will
have an option to apply a route-map to control the routes that can be
redistributed into BGP.
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
redistribute { connected | static | ospf } [ route-map <route-map-name> ] [ metric <metvalue> ]
no redistribute { connected | static | ospf } [ route-map <route-map-name> ] [ metric <metvalue> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| route-map-name | WORD  | String  |
| metvalue |   | Integer  |
### Usage Guidelines 
```
User can provide a route-map while enabling redistribution of routes to
control the routes that goes into BGP. User can also use metric option
to set the default metric for the redistributed routes.
```
### Examples 
#### Enable redistribution of connected routes into BGP 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# address-family ipv4 unicast
sonic(config-router-bgp-af)# redistribute connected
```
## refresh 
### Description 
```
Configures OSPFv2 LSA refresh interval.
```
### Parent Commands (Modes) 
```
router ospf [ vrf <vrf-name> ]
```
### Syntax 
```
refresh timer <refreshtimer>
no refresh timer
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| refreshtimer |   | Integer  |
### Usage Guidelines 
```
Use this command to configure OSPFv2 LSA refresh interval.
```
### Examples 
#### Configures OSPFv2 LSA refresh interval 
```
sonic-cli(config-router-ospf)# refresh timer 20
```
### Features this CLI belongs to 
*  OSPFv2
## remark 
### Description 
```
Set an ACL remark or description.
```
### Parent Commands (Modes) 
```
mac access-list <access-list-name>
```
### Syntax 
```
remark <remark-val>
no remark
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| remark-val | String (Max: 256 characters)  | String  |
### Usage Guidelines 
```
Remark with spaces should be mentioned in double quotes.
```
### Examples 
#### Set ACL remark 
```
sonic(config-mac-acl)# remark"Example ACL remark"
```
## remark 
### Description 
```
Set an ACL remark or description.
```
### Parent Commands (Modes) 
```
ip access-list <access-list-name>
```
### Syntax 
```
remark <remark-val>
no remark
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| remark-val | String (Max: 256 characters)  | String  |
### Usage Guidelines 
```
Remark with spaces should be mentioned in double quotes.
```
### Examples 
#### Set ACL remark 
```
sonic(config-mac-acl)# remark"Example ACL remark"
```
## remark 
### Description 
```
Set an ACL remark or description.
```
### Parent Commands (Modes) 
```
ipv6 access-list <access-list-name>
```
### Syntax 
```
remark <remark-val>
no remark
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| remark-val | String (Max: 256 characters)  | String  |
### Usage Guidelines 
```
Remark with spaces should be mentioned in double quotes.
```
### Examples 
#### Set ACL remark 
```
sonic(config-mac-acl)# remark"Example ACL remark"
```
## remote-as 
### Description 
```
This command configure the remote-as number for a BGP neighbor. This
command also can tag a neighbor (dynmaic) as internal (iBGP) or external (eBGP)
```
### Parent Commands (Modes) 
```
neighbor { <ip> | { interface { Ethernet | PortChannel | Vlan } } }
```
### Syntax 
```
remote-as { internal | external | <as-num-dot> }
no remote-as { [ internal ] | [ external ] | [ <as-num-dot> ] } ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| as-num-dot | 1-4294967295  | Integer  |
### Usage Guidelines 
```
remote-as configuration for a BGP neighbor is mandatory. User must
configure remote-as right after creating the BGP neighbor. User can
either specify the remote AS number or can specify whether a neighbor is
internal or external.
```
### Examples 
#### Following command configures remote AS number for neighbor          30.30.30.3 to 65100 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 30.30.30.3
sonic(config-router-bgp-neighbor)# remote-as 65100
```
## remote-as 
### Description 
```
This command configure the remote-as number for a BGP peer-group. This
command also can tag a peer-group as internal (iBGP) or external (eBGP)
```
### Parent Commands (Modes) 
```
peer-group <template-str>
```
### Syntax 
```
remote-as { internal | external | <as-num-dot> }
no remote-as { [ internal ] | [ external ] | [ <as-num-dot> ] } ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| as-num-dot | 1-4294967295  | Integer  |
### Usage Guidelines 
```
This command configures remote-as number for a BGP peer-group.
User can either specify the remote AS number or can specify whether
a peer-group is internal or external.
```
### Examples 
#### Following command configures remote AS number for          peer-group PG_Ext 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# remote-as 65200
```
## remove-private-as 
### Description 
```
Remove private ASNs in outbound updates 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
remove-private-as [ all ] [ replace-as ]
no remove-private-as [ all ] [ replace-as ]
```
## remove-private-as 
### Description 
```
Remove private ASNs in outbound updates 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
remove-private-as [ all ] [ replace-as ]
no remove-private-as [ all ] [ replace-as ]
```
## remove-private-as 
### Description 
```
This command configures BGP to remove private AS numbers from as-path in
outbound BGP updates to neighbor
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
remove-private-as [ all ] [ replace-as ]
no remove-private-as [ all ] [ replace-as ]
```
### Usage Guidelines 
```
Use this command at the boundary of your BGP network to remove
the internal/private AS numbers from outbound route updates. User can
optionally choose to replace private AS number by local AS number.
```
### Examples 
#### Following command enables BGP to remove all private AS          numbers from outbound updates to neighbor 20.20.20.2 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 20.20.20.2
sonic(config-router-bgp-neighbor)# remote-as 300
sonic(config-router-bgp-neighbor)# address-family ipv4 unicast
sonic(config-router-bgp-neighbor-af)# remove-private-as all
```
## remove-private-as 
### Description 
```
This command configures BGP to remove private AS numbers from as-path in
outbound BGP updates to neighbors in a peer-group
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
remove-private-as [ all ] [ replace-as ]
no remove-private-as [ all ] [ replace-as ]
```
### Usage Guidelines 
```
Use this command at the boundary of your BGP network to remove
the internal/private AS numbers from outbound route updates. User can
optionally choose to replace private AS number by local AS number.
```
### Examples 
#### Following command enables BGP to remove all private AS          numbers from outbound updates to peer-group PG_Ext 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# address-family ipv4 unicast
sonic(config-router-bgp-pg-af)# remove-private-as all
```
## renew dhcp-lease 

### Description 

```
Renew DHCP lease 


```
### Syntax 

```
renew dhcp-lease interface { Management <mgmt-if-id> }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| mgmt-if-id |   | Integer  |


## request-data-size 
### Description 
```
Configure ICMP request data size
```
### Parent Commands (Modes) 
```
icmp-echo <addr>
```
### Syntax 
```
request-data-size <size>
no request-data-size
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| size |   | Integer  |
### Examples 
#### Configure request-data-size for an IP SLA ICMP instance 
```
sonic(config)# ip sla 10
sonic(conf-ipsla-10)# icmp-echo 30.30.1.2
sonic(conf-ipsla-10-icmp)# request-data-size 128
```
## route-map 
### Description 
```
This command configures policy for BGP neighbor using a route-map. The
policy can be applied in INBOUND or OUTBOUND direction
```
### Parent Commands (Modes) 
```
address-family l2vpn evpn
```
### Syntax 
```
route-map <route-name-str> { in | out }
no route-map <route-name-str> { [ in ] | [ out ] ] }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| route-name-str | WORD  | String  |
### Usage Guidelines 
```
Use this command to configure policy for BGP neighbor. The policy can be
applied in inbound or outbound direction. The policy will dicatate if a
subset of routes needs to be filtered out or/and if attributes of some
routes needs to be modified
```
### Examples 
#### Following command configures a route-map for BGP          neighbor in inbound direction 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 20.20.20.2
sonic(config-router-bgp-neighbor)# remote-as 300
sonic(config-router-bgp-neighbor)# address-family l2vpn evpn
sonic(config-router-bgp-neighbor-af)# route-map rmap_filter_intra_routes in
```
## route-map 
### Description 
```
This command configures policy for BGP neighbors in peer-group using a route-map. The
policy can be applied in INBOUND or OUTBOUND direction
```
### Parent Commands (Modes) 
```
address-family l2vpn evpn
```
### Syntax 
```
route-map <route-name-str> { in | out }
no route-map <route-name-str> { [ in ] | [ out ] ] }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| route-name-str | WORD  | String  |
### Usage Guidelines 
```
Use this command to configure policy for BGP peer-group. The policy can be
applied in inbound or outbound direction. The policy will dicatate if a
subset of routes needs to be filtered out or/and if attributes of some
routes needs to be modified
```
### Examples 
#### Following command configures a route-map for BGP          peer-group in inbound direction 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# address-family l2vpn evpn
sonic(config-router-bgp-pg-af)# route-map RM_Blk_192 in
```
## route-map 
### Description 
```
Name of the route map 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
route-map <route-name-str> { in | out }
no route-map <route-name-str> { [ in ] | [ out ] ] }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| route-name-str | WORD  | String  |
## route-map 
### Description 
```
Name of the route map 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
route-map <route-name-str> { in | out }
no route-map <route-name-str> { [ in ] | [ out ] ] }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| route-name-str | WORD  | String  |
## route-map 
### Description 
```
Configure routing policies 
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
route-map <route-map-name> { permit | deny } <seq-nu>
no route-map <route-map-name> { { [ permit <seq-nu> ] } | { [ deny <seq-nu> ] } } ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| route-map-name | WORD  | String  |
| seq-nu |   | Integer  |
## route-map 
### Description 
```
This command configures policy for BGP neighbor using a route-map. The
policy can be applied in INBOUND or OUTBOUND direction
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
route-map <route-name-str> { in | out }
no route-map <route-name-str> { [ in ] | [ out ] ] }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| route-name-str | WORD  | String  |
### Usage Guidelines 
```
Use this command to configure policy for BGP neighbor. The policy can be
applied in inbound or outbound direction. The policy will dicatate if a
subset of routes needs to be filtered out or/and if attributes of some
routes needs to be modified
```
### Examples 
#### Following command configures a route-map for BGP          neighbor in inbound direction 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 20.20.20.2
sonic(config-router-bgp-neighbor)# remote-as 300
sonic(config-router-bgp-neighbor)# address-family ipv4 unicast
sonic(config-router-bgp-neighbor-af)# route-map rmap_filter_intra_routes in
```
## route-map 
### Description 
```
This command configures policy for BGP neighbors in peer-group using a route-map. The
policy can be applied in INBOUND or OUTBOUND direction
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
route-map <route-name-str> { in | out }
no route-map <route-name-str> { [ in ] | [ out ] ] }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| route-name-str | WORD  | String  |
### Usage Guidelines 
```
Use this command to configure policy for BGP peer-group. The policy can be
applied in inbound or outbound direction. The policy will dicatate if a
subset of routes needs to be filtered out or/and if attributes of some
routes needs to be modified
```
### Examples 
#### Following command configures a route-map for BGP          peer-group in inbound direction 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# address-family ipv4 unicast
sonic(config-router-bgp-pg-af)# route-map RM_Blk_192 in
```
## route-map delay-timer 
### Description 
```
This command sets the route-map change processing delay interval in
seconds.
```
### Parent Commands (Modes) 
```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }
```
### Syntax 
```
route-map delay-timer <delaytm>
no route-map delay-timer
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| delaytm |   | Integer  |
### Usage Guidelines 
```
Change in route-map may require BGP RIB to get re-processed to reflect
the change in policy. This command set the interval in seconds to wait before processing
route-map change.
```
### Examples 
#### Below command set the route-map change delay to          60seconds 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# route-map delay-timer 60
```
## route-reflector allow-outbound-policy 
### Description 
```
This command allows to set the outbound policy for route reflector
neighbors.
```
### Parent Commands (Modes) 
```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }
```
### Syntax 
```
route-reflector allow-outbound-policy
no route-reflector allow-outbound-policy
```
### Usage Guidelines 
```
Use this command to allow users to set outbound policy for route
reflector neighbors.
```
### Examples 
#### Below command enables route-policy for route reflector          BGP neighbors 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# route-reflector allow-outbound-policy
```
## route-reflector-client 
### Description 
```
This command configures a BGP neighbor as route reflector client.
```
### Parent Commands (Modes) 
```
address-family l2vpn evpn
```
### Syntax 
```
route-reflector-client
no route-reflector-client
```
### Usage Guidelines 
```
Use this command to configure an IBGP neighbor a route reflector client.
This command will implicitly make the local router a route reflector
server.
```
### Examples 
#### Following command configures neighbor 20.20.20.2 as          route reflector client 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 20.20.20.2
sonic(config-router-bgp-neighbor)# remote-as 300
sonic(config-router-bgp-neighbor)# address-family l2vpn evpn
sonic(config-router-bgp-neighbor-af)# route-reflector-client
```
## route-reflector-client 
### Description 
```
This command configures BGP neighbors in a peer-group as route reflector client.
```
### Parent Commands (Modes) 
```
address-family l2vpn evpn
```
### Syntax 
```
route-reflector-client
no route-reflector-client
```
### Usage Guidelines 
```
Use this command to configure an IBGP peer-group route reflector client.
```
### Examples 
#### Following command configures neighbors in a peer-group as          route reflector client 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Int
sonic(config-router-bgp-pg)# address-family l2vpn evpn
sonic(config-router-bgp-pg-af)# route-reflector-client
```
## route-reflector-client 
### Description 
```
Configure a neighbor as Route Reflector client 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
route-reflector-client
no route-reflector-client
```
## route-reflector-client 
### Description 
```
Configure a neighbor as Route Reflector client 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
route-reflector-client
no route-reflector-client
```
## route-reflector-client 
### Description 
```
This command configures a BGP neighbor as route reflector client.
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
route-reflector-client
no route-reflector-client
```
### Usage Guidelines 
```
Use this command to configure an IBGP neighbor a route reflector client.
This command will implicitly make the local router a route reflector
server.
```
### Examples 
#### Following command configures neighbor 20.20.20.2 as          route reflector client 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 20.20.20.2
sonic(config-router-bgp-neighbor)# remote-as 300
sonic(config-router-bgp-neighbor)# address-family ipv4 unicast
sonic(config-router-bgp-neighbor-af)# route-reflector-client
```
## route-reflector-client 
### Description 
```
This command configures BGP neighbors in a peer-group as route reflector client.
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
route-reflector-client
no route-reflector-client
```
### Usage Guidelines 
```
Use this command to configure an IBGP peer-group route reflector client.
```
### Examples 
#### Following command configures neighbors in a peer-group as          route reflector client 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Int
sonic(config-router-bgp-pg)# address-family ipv4 unicast
sonic(config-router-bgp-pg-af)# route-reflector-client
```
## route-server-client 
### Description 
```
This command configures a BGP neighbor a route server client.
```
### Parent Commands (Modes) 
```
address-family l2vpn evpn
```
### Syntax 
```
route-server-client
no route-server-client
```
### Usage Guidelines 
```
Use this command to configure an IBGP neighbor a route server client.
```
### Examples 
#### Following command configures neighbor 20.20.20.2 as          route server client 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 20.20.20.2
sonic(config-router-bgp-neighbor)# remote-as 300
sonic(config-router-bgp-neighbor)# address-family l2vpn evpn
sonic(config-router-bgp-neighbor-af)# route-server-client
```
## route-server-client 
### Description 
```
This command configures BGP neighbors in a peer-group route server client.
```
### Parent Commands (Modes) 
```
address-family l2vpn evpn
```
### Syntax 
```
route-server-client
no route-server-client
```
### Usage Guidelines 
```
Use this command to configure an IBGP peer-group route server client.
```
### Examples 
#### Following command configures neighbors in a peer-group as          route server client 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Int
sonic(config-router-bgp-pg)# address-family l2vpn evpn
sonic(config-router-bgp-pg-af)# route-server-client
```
## route-server-client 
### Description 
```
Configure a neighbor as Route Server client 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
route-server-client
no route-server-client
```
## route-server-client 
### Description 
```
Configure a neighbor as Route Server client 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
route-server-client
no route-server-client
```
## route-server-client 
### Description 
```
This command configures a BGP neighbor a route server client.
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
route-server-client
no route-server-client
```
### Usage Guidelines 
```
Use this command to configure an IBGP neighbor a route server client.
```
### Examples 
#### Following command configures neighbor 20.20.20.2 as          route server client 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 20.20.20.2
sonic(config-router-bgp-neighbor)# remote-as 300
sonic(config-router-bgp-neighbor)# address-family ipv4 unicast
sonic(config-router-bgp-neighbor-af)# route-server-client
```
## route-server-client 
### Description 
```
This command configures BGP neighbors in a peer-group route server client.
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
route-server-client
no route-server-client
```
### Usage Guidelines 
```
Use this command to configure an IBGP peer-group route server client.
```
### Examples 
#### Following command configures neighbors in a peer-group as          route server client 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Int
sonic(config-router-bgp-pg)# address-family ipv4 unicast
sonic(config-router-bgp-pg-af)# route-server-client
```
## route-target 
### Description 
```
This command specifies the route-target or a community to be attached while exporting routes from current vrf.
This command also allows to specific route-target to be matched when importing routes into current vrf
```
### Parent Commands (Modes) 
```
address-family l2vpn evpn
```
### Syntax 
```
route-target <rttype> <rt>
no route-target <rttype> <rt>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| rttype | Advertise options both or import or export  | Select [both(both) import(import) export(export) ]  |
| rt | A.B.C.D:NN or NN:NN or MMMM:NN or NN:MMMM  | String  |
### Usage Guidelines 
```
[no] route-target import|export|both {route-target-value}
```
### Examples 
#### Following command specifies matching route-target 11:11 while importing route,           attaching route-target 22:22 while exporting routes and          specifying route-target 33:33 to be attached/matched while exporting/importing routes respectively          in vrf Vrf1 
```
sonic# configure terminal
sonic(config)# router bgp 100 vrf Vrf1
sonic(config-router-bgp)# address-family l2vpn evpn
sonic(config-router-bgp-af)# route-target import 11:11
sonic(config-router-bgp-af)# route-target export 22:22
sonic(config-router-bgp-af)# route-target both 33:33
```
## route-target 
### Description 
```
This command specifies the route-target or a community to be attached while exporting routes from current vrf for a specific VNI.
This command also allows to specific route-target to be matched when importing routes into current vrf for a specific VNI
```
### Parent Commands (Modes) 
```
vni <vninum>
```
### Syntax 
```
route-target <rttype> <rt>
no route-target <rttype> <rt>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| rttype | Advertise options both or import or export  | Select [both(both) import(import) export(export) ]  |
| rt | A.B.C.D:NN or NN:NN or MMMM:NN or NN:MMMM  | String  |
### Usage Guidelines 
```
[no] route-target import|export|both {route-target-value}
```
### Examples 
#### Following command specifies matching route-target 11:11 while importing route,               attaching route-target 22:22 while exporting routes and              specifying route-target 33:33 to be attached/matched while exporting/importing routes respectively              in vrf Vrf1 for VNI 100 
```
sonic# configure terminal
sonic(config)# router bgp 100 vrf Vrf1
sonic(config-router-bgp)# address-family l2vpn evpn
sonic(config-router-bgp-af)# vni 100
sonic(config-router-bgp-af-vni)# route-target import 11:11
sonic(config-router-bgp-af-vni)# route-target export 22:22
sonic(config-router-bgp-af-vni)# route-target both 33:33
```
## router bgp 
### Description 
```
This command creates an instnace of BGP routing protocol in a VRF.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }
no router bgp [ vrf <vrf-name> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| as-num-dot | 1-4294967295  | Integer  |
| vrf-name | WORD  | String  |
### Usage Guidelines 
```
Use this config to create BGP routing instance in a VRF. If vrf key is
not supplied by user, default-vrf is assumed. Only one instance of BGP
protocol can be created in a VRF. Attempt to create more than one
instance will result in command execution failure.
If a BGP instance already exists, executing this command with same AS
number will simply enter into the "router bgp ..." conifguration mode of
the CLI.
```
### Examples 
#### Below command creates a new BGP instance in default VRF          with local AS number as 65300 
```
sonic# configure terminal
sonic(config)# router bgp 65300
```
## router ospf 
### Description 
```
Configures OSPFv2 router within a VRF
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
router ospf [ vrf <vrf-name> ]
no router ospf [ vrf <vrf-name> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrf-name | WORD  | String  |
### Usage Guidelines 
```
Use this command configure an OSPFv2 router. User can optionally specify VRF
on which the router have to be configured. If VRF name is not specified then
the command is considered for VRF default. Upon successful configuration CLI
mode will be changed to config-router-ospf.
Technical details on OSPFv2 support is also available at
http://docs.frrouting.org/en/latest/
```
### Examples 
####  
```
sonic-cli(config)# router ospf
sonic-cli(config-router-ospf)#
           or
sonic-cli(config)# router ospf vrf Vrf-blue
sonic-cli(config-router-ospf)#
```
### Features this CLI belongs to 
*  OSPFv2
## router-id 
### Description 
```
This command configures router ID for an instance of BGP protocol
```
### Parent Commands (Modes) 
```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }
```
### Syntax 
```
router-id <ip-addr>
no router-id
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| ip-addr | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to configure router ID for an instance of BGP protocol.
Router ID configuration is optional for user. BGP automatically picks up
one of the interface IP address as router ID if not configured
explicitly by user.
```
### Examples 
#### Below command configures router ID for BGP instance on          default-VRF 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# router-id 163.134.6.97
```
## router-id 

### Description 

```
This command configures router ID for an instance of BGP protocol


```
### Syntax 

```
router-id <ip-addr>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| ip-addr | A.B.C.D  | String  |


### Usage Guidelines 

```
Use this command to configure router ID for an instance of BGP protocol.
Router ID configuration is optional for user. BGP automatically picks up
one of the interface IP address as router ID if not configured
explicitly by user.


```
### Examples 
#### Below command configures router ID for BGP instance on          default-VRF 

```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# router-id 163.134.6.97


```
## sampler 
### Description 
```
This command configures any configured sampler information.
```
### Parent Commands (Modes) 
```
tam
```
### Syntax 
```
sampler <name> rate <sampler_rate>
no sampler <name>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| name | WORD  | String  |
| sampler_rate | 1-4294967295  | Integer  |
### Usage Guidelines 
```
TAM infrastructure supports setting up a sampling configuration and refer to this sampler configuration from the application which support sampling for the traffic. This command configures any configured sampler information.
```
### Examples 
#### sampler <name>  rate <sample-rate> 
```
sonic(config-tam)# sampler s34 rate 1000
sonic# show tam samplers
Name         Sample Rate
-----------  -----------
s1           1
s34          1000
sonic#
```
## scheduler-policy 
### Description 
```
This command is used to configure scheduler policy of CPU interface.
```
### Parent Commands (Modes) 
```
interface CPU
```
### Syntax 
```
scheduler-policy <sp_name>
no scheduler-policy
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| sp_name | WORD  | String  |
### Usage Guidelines 
```
Use this command to configure scheduler policy of CPU interface.
```
### Examples 
#### Configure scheduler policy of CPU interface. 
```
sonic(conf-if-CPU)# scheduler-policy scheduler.cpu
```
## scheduler-policy 
### Description 
```
Scheduler Policy configuration 
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
scheduler-policy <sp_name>
no scheduler-policy
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| sp_name | WORD  | String  |
## send-community 
### Description 
```
Send Community attribute to this neighbor 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
send-community { standard | extended | both | large | all | none }
no send-community
```
## send-community 
### Description 
```
Send Community attribute to this neighbor 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
send-community { standard | extended | both | large | all | none }
no send-community
```
## send-community 
### Description 
```
This command configures BGP to send community to a neighbor
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
send-community { standard | extended | both | large | all | none }
no send-community
```
### Usage Guidelines 
```
Use this command to enable sending of community attribute to a BGP
neighbor. The command option provides the flexibility to enable sending
of standard, extended, large communities.
```
### Examples 
#### Following command enables BGP to send community          attribute to neighbor 20.20.20.2 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 20.20.20.2
sonic(config-router-bgp-neighbor)# remote-as 300
sonic(config-router-bgp-neighbor)# address-family ipv4 unicast
sonic(config-router-bgp-neighbor-af)# send-community
```
## send-community 
### Description 
```
This command configures BGP to send community to neighbors in a
peer-group
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
send-community { standard | extended | both | large | all | none }
no send-community
```
### Usage Guidelines 
```
Use this command to enable sending of community attribute to BGP
neighbors in a peer-group. The command option provides the flexibility to enable sending
of standard, extended, large communities.
```
### Examples 
#### Following command enables BGP to send community          attribute to neighbors in a peer-group PG_Int 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Int
sonic(config-router-bgp-pg)# address-family ipv4 unicast
sonic(config-router-bgp-pg-af)# send-community
```
## seq 
### Description 
```
Create a MAC ACL Rule.
```
### Parent Commands (Modes) 
```
mac access-list <access-list-name>
```
### Syntax 
```
seq <seq-no> { { deny | discard | permit | transit | do-not-nat | { remark <remark-val> } } { { <src-mac-addr> <src-mac-mac> } | src-mac-any | { src-mac-host <src-mac-addr> } } { { <dst-mac-addr> <dst-mac-mask> } | dst-mac-any | { dst-mac-host <dst-mac-addr> } } { [ ethertype-ip ] | [ ethertype-ipv6 ] | [ ethertype-arp ] | [ <ETHERTYPE> ] ] } { [ pcp { pcp-be | pcp-bk | pcp-ee | pcp-ca | pcp-vi | pcp-vo | pcp-ic | pcp-nc | { <pcp-val> { [ pcp-mask <pcp-val-mask> ] } } } ] } { [ dei <dei-val> ] } { [ vlan <vlan-val> ] } { [ remark <remark-val> ] } }
no seq <seq-no> [ remark ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| seq-no |   | Integer  |
| remark-val | String (Max: 256 characters)  | String  |
| src-mac-addr | MACADDRESS  | String  |
| src-mac-mac | MACADDRESS  | String  |
| dst-mac-addr | MACADDRESS  | String  |
| dst-mac-mask | MACADDRESS  | String  |
| ETHERTYPE | 0x600-0xffff  | String  |
| pcp-val | 0-7  | Integer  |
| pcp-val-mask | 0-7  | Integer  |
| dei-val | 0-1  | Integer  |
| vlan-val |   | String  |
### Usage Guidelines 
```
The rule will be created if there is no existing rule with the same sequence number. ACL Rule cant be updated. To update, the rule must be deleted and added with updated parameters.
```
### Examples 
#### Add MAC ACL Rule 
```
sonic(config-mac-acl)# seq 10 permit host 00:00:10:00:00:01 host 00:00:20:00:00:01
```
## seq 
### Description 
```
Create a IPv4 ACL Rule.
```
### Parent Commands (Modes) 
```
ip access-list <access-list-name>
```
### Syntax 
```
seq <seq-no> { { deny | discard | permit | transit | do-not-nat | { remark <remark-val> } } { <ip-protocol-val> | icmp | ip | tcp | udp } { <src-ip-prefix> | src-ip-any | { src-ip-host <src-ip> } } { { [ src-eq <src-port1> ] } | { [ src-gt <src-port1> ] } | { [ src-lt <src-port1> ] } | { [ src-range <src-port1> <src-port2> ] } ] } { <dst-ip-prefix> | dst-ip-any | { dst-ip-host <dst-ip> } } { { [ dst-eq <dst-port1> ] } | { [ dst-gt <dst-port1> ] } | { [ dst-lt <dst-port1> ] } | { [ dst-range <dst-port1> <dst-port2> ] } ] } { [ dscp { default | cs1 | cs2 | cs3 | cs4 | cs5 | cs6 | cs7 | af11 | af12 | af13 | af21 | af22 | af23 | af31 | af32 | af33 | af41 | af42 | af43 | ef | voice-admit | <dscp-val> } ] } [ tcp-established ] { [ fin ] | [ not-fin ] ] } { [ syn ] | [ not-syn ] ] } { [ rst ] | [ not-rst ] ] } { [ psh ] | [ not-psh ] ] } { [ ack ] | [ not-ack ] ] } { [ urg ] | [ not-urg ] ] } { [ type <TYPE> ] } { [ code <CODE> ] } { [ vlan <vlan-val> ] } { [ remark <remark-val> ] } }
no seq <seq-no> [ remark ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| seq-no |   | Integer  |
| remark-val | String (Max: 256 characters)  | String  |
| ip-protocol-val |   | Integer  |
| src-ip-prefix | A.B.C.D/mask  | String  |
| src-ip | A.B.C.D  | String  |
| src-port1 |   | Integer  |
| src-port2 |   | Integer  |
| dst-ip-prefix | A.B.C.D/mask  | String  |
| dst-ip | A.B.C.D  | String  |
| dst-port1 |   | Integer  |
| dst-port2 |   | Integer  |
| dscp-val |   | Integer  |
| TYPE |   | Integer  |
| CODE |   | Integer  |
| vlan-val |   | String  |
### Usage Guidelines 
```
The rule will be created if there is no existing rule with the same sequence number. ACL Rule cant be updated. To update, the rule must be deleted and added with updated parameters.
```
### Examples 
#### Add IP ACL Rule 
```
sonic(config-ip-acl)# seq 10 permit ip host 10.1.1.1 host 20.1.1.1
```
## seq 
### Description 
```
Create a IPv6 ACL Rule.
```
### Parent Commands (Modes) 
```
ipv6 access-list <access-list-name>
```
### Syntax 
```
seq <seq-no> { { deny | discard | permit | transit | do-not-nat | { remark <remark-val> } } { <ip-protocol-val> | icmpv6 | ipv6 | tcp | udp } { <src-ip-prefix> | src-ip-any | { src-ip-host <src-ip> } } { { [ src-eq <src-port1> ] } | { [ src-gt <src-port1> ] } | { [ src-lt <src-port1> ] } | { [ src-range <src-port1> <src-port2> ] } ] } { <dst-ip-prefix> | dst-ip-any | { dst-ip-host <dst-ip> } } { { [ dst-eq <dst-port1> ] } | { [ dst-gt <dst-port1> ] } | { [ dst-lt <dst-port1> ] } | { [ dst-range <dst-port1> <dst-port2> ] } ] } { [ dscp { default | cs1 | cs2 | cs3 | cs4 | cs5 | cs6 | cs7 | af11 | af12 | af13 | af21 | af22 | af23 | af31 | af32 | af33 | af41 | af42 | af43 | ef | voice-admit | <dscp-val> } ] } [ tcp-established ] { [ fin ] | [ not-fin ] ] } { [ syn ] | [ not-syn ] ] } { [ rst ] | [ not-rst ] ] } { [ psh ] | [ not-psh ] ] } { [ ack ] | [ not-ack ] ] } { [ urg ] | [ not-urg ] ] } { [ type <TYPE> ] } { [ code <CODE> ] } { [ vlan <vlan-val> ] } { [ remark <remark-val> ] } }
no seq <seq-no> [ remark ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| seq-no |   | Integer  |
| remark-val | String (Max: 256 characters)  | String  |
| ip-protocol-val |   | Integer  |
| src-ip-prefix | A::B/mask  | String  |
| src-ip | A::B  | String  |
| src-port1 |   | Integer  |
| src-port2 |   | Integer  |
| dst-ip-prefix | A::B/mask  | String  |
| dst-ip | A::B  | String  |
| dst-port1 |   | Integer  |
| dst-port2 |   | Integer  |
| dscp-val |   | Integer  |
| TYPE |   | Integer  |
| CODE |   | Integer  |
| vlan-val |   | String  |
### Usage Guidelines 
```
The rule will be created if there is no existing rule with the same sequence number. ACL Rule cant be updated. To update, the rule must be deleted and added with updated parameters.
```
### Examples 
#### Add IPv6 ACL Rule 
```
sonic(config-ip-acl)# seq 100 permit ipv6 host abcd::1 host bcde::1
```
## service-policy 
### Description 
```
Applies ingress/egress service policy on given interface
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
service-policy type { qos | monitoring | forwarding | copp } { in | out } <fbs-policy-name>
no service-policy type { qos | monitoring | forwarding | copp } { in | out }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| fbs-policy-name | WORD  | String  |
### Examples 
#### Applies ingress/egress service policy 
```
sonic(conf-if-Vlan100)# service-policy type forwarding in policy_vrf
```
## service-policy 
### Description 
```
Applies ingress/egress service policy on given interface
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
service-policy type { qos | monitoring | forwarding | copp } { in | out } <fbs-policy-name>
no service-policy type { qos | monitoring | forwarding | copp } { in | out }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| fbs-policy-name | WORD  | String  |
### Examples 
#### Applies ingress/egress service policy 
```
sonic(conf-if-Vlan100)# service-policy type forwarding in policy_vrf
```
## service-policy 
### Description 
```
Applies ingress/egress service policy on given interface
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
service-policy type { qos | monitoring | forwarding | copp } { in | out } <fbs-policy-name>
no service-policy type { qos | monitoring | forwarding | copp } { in | out }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| fbs-policy-name | WORD  | String  |
### Examples 
#### Applies ingress/egress service policy 
```
sonic(conf-if-Vlan100)# service-policy type forwarding in policy_vrf
```
## service-policy 
### Description 
```
Applies ingress/egress service policy on given interface
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
service-policy type { qos | monitoring | forwarding | copp } { in | out } <fbs-policy-name>
no service-policy type { qos | monitoring | forwarding | copp } { in | out }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| fbs-policy-name | WORD  | String  |
### Examples 
#### Applies ingress/egress service policy 
```
sonic(conf-if-Vlan100)# service-policy type forwarding in policy_vrf
```
## service-policy 
### Description 
```
Applies ingress/egress service policy on given interface
```
### Parent Commands (Modes) 
```
line vty
```
### Syntax 
```
service-policy type { qos | monitoring | forwarding | copp } { in | out } <fbs-policy-name>
no service-policy type { qos | monitoring | forwarding | copp } { in | out }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| fbs-policy-name | WORD  | String  |
### Examples 
#### Applies ingress/egress service policy 
```
sonic(conf-if-Vlan100)# service-policy type forwarding in policy_vrf
```
## session 
### Description 
```
This command creates IFA monitoring session associated a previously defined flow-group.
```
### Parent Commands (Modes) 
```
ifa
```
### Syntax 
```
session <session_name> flowgroup <flowgroup_name> [ collector <collector_name> ] [ sampler <sampler_name> ] node-type <node_type>
no session <session_name>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| session_name | WORD  | String  |
| flowgroup_name | WORD  | String  |
| collector_name | WORD  | String  |
| sampler_name | WORD  | String  |
| node_type | IFA Node Role  | Select [ingress(INGRESS) egress(EGRESS) ]  |
### Usage Guidelines 
```
This command creates IFA monitoring session associated a previously defined flow-group.
```
### Examples 
#### session <name> flowgroup <fg-name> [collector <col-name>] [sample-rate <sampler-name>] node-type {ingress | egress } 
```
sonic(config-tam-ifa)# session ss1 flowgroup f9 sampler s1 node-type ingress
sonic(config-tam-ifa)#
sonic# show tam ifa sessions
Name           Flow Group          Collector          Sampler           Node Type
-----------    ----------------    --------------     -------------     ----------
ss1            f9                                     s1                Ingress Node
sonic#
```
## session 
### Description 
```
This command creates drop monitoring session associated a previously defined flow-group.
```
### Parent Commands (Modes) 
```
drop-monitor
```
### Syntax 
```
session <session_name> flowgroup <flowgroup_name> collector <collector_name> sampler <sampler_name>
no session <session_name>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| session_name | WORD  | String  |
| flowgroup_name | WORD  | String  |
| collector_name | WORD  | String  |
| sampler_name | WORD  | String  |
### Usage Guidelines 
```
This command creates drop monitoring session associated a previously defined flow-group.
```
### Examples 
#### session <name> flowgroup <fg-name> collector <col-name> [sample-rate <sampler-name>] 
```
sonic# configure terminal
sonic(config)# tam
sonic(config-tam)# drop-monitor
sonic(config-tam-dm)# session ss91 flowgroup f9 collector c2 sampler s1
sonic(config-tam-dm)# end
sonic# show tam drop-monitor sessions
Name           Flow Group          Collector          Sampler
-----------    ----------------    --------------     -------------
ss1            f1                  c1                 s1
ss2            DEMO                c1                 s2
ss91           f9                  c2                 s1
sonic#
```
## session 
### Description 
```
This command creates tail stamping session associated a previously defined flow-group.
```
### Parent Commands (Modes) 
```
tail-stamping
```
### Syntax 
```
session <session_name> flowgroup <flowgroup_name> [ node-type <node_type> ]
no session <session_name>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| session_name | WORD  | String  |
| flowgroup_name | WORD  | String  |
| node_type | Tail Stamping Node Role  | Select [normal(NORMAL) ifa(IFA) ]  |
### Usage Guidelines 
```
This command creates tail stamping session associated a previously defined flow-group.
```
### Examples 
#### session <name> flowgroup <fg-name> {node-type <normal|ifa>} 
```
sonic# configure terminal
sonic(config)# tam
sonic(config-tam)# tail-stamping
sonic(config-tam-ts)# session ss66 flowgroup f9 node-type ifa
sonic(config-tam-ts)# end
sonic# show tam tail-stamping sessions
Name           Flow Group     Node Type
-----------    ------------   ------------
ss66           f9             IFA
ss99           f10            Normal
sonic#
```
## session-timeout 
### Description 
```
Configures MCLAG session timeout value in seconds
```
### Parent Commands (Modes) 
```
mclag domain <mclag-domain-id>
```
### Syntax 
```
session-timeout <ST>
no session-timeout
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| ST |   | Integer  |
### Usage Guidelines 
```
Use this command to change the default MCLAG session timeout
```
### Examples 
#### Configures MCLAG session timeout value to 100 seconds for MCLAG domain 100 
```
sonic-cli(config-mclag-domain-100)#session-timeout 100
```
## set as-path 
### Description 
```
Transform BGP AS-path attribute 
```
### Parent Commands (Modes) 
```
route-map <route-map-name> { permit | deny } <seq-nu>
```
### Syntax 
```
set as-path { prepend { <as-number_list> } }
no set as-path prepend
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| as-number_list | asn list  | String  |
## set community 
### Description 
```
BGP community attribute 
```
### Parent Commands (Modes) 
```
route-map <route-map-name> { permit | deny } <seq-nu>
```
### Syntax 
```
set community { { [ <comm-num1> { [ <comm-num2> { [ <comm-num3> { [ <comm-num4> { [ <comm-num5> [ local-as ] [ no-advertise ] [ no-export ] [ no-peer ] [ additive ] ] } [ local-as ] [ no-advertise ] [ no-export ] [ no-peer ] [ additive ] ] } [ local-as ] [ no-advertise ] [ no-export ] [ no-peer ] [ additive ] ] } [ local-as ] [ no-advertise ] [ no-export ] [ no-peer ] [ additive ] ] } [ local-as ] [ no-advertise ] [ no-export ] [ no-peer ] [ additive ] ] } | { [ local-as [ no-advertise ] [ no-export ] [ no-peer ] [ additive ] ] } | { [ no-advertise [ local-as ] [ no-export ] [ no-peer ] [ additive ] ] } | { [ no-export [ local-as ] [ no-advertise ] [ no-peer ] [ additive ] ] } | { [ no-peer [ local-as ] [ no-advertise ] [ no-export ] [ additive ] ] } }
no set community { { [ <comm-num1> { [ <comm-num2> { [ <comm-num3> { [ <comm-num4> { [ <comm-num5> [ local-as ] [ no-advertise ] [ no-export ] [ no-peer ] [ additive ] ] } [ local-as ] [ no-advertise ] [ no-export ] [ no-peer ] [ additive ] ] } [ local-as ] [ no-advertise ] [ no-export ] [ no-peer ] [ additive ] ] } [ local-as ] [ no-advertise ] [ no-export ] [ no-peer ] [ additive ] ] } [ local-as ] [ no-advertise ] [ no-export ] [ no-peer ] [ additive ] ] } | { [ local-as [ no-advertise ] [ no-export ] [ no-peer ] [ additive ] ] } | { [ no-advertise [ local-as ] [ no-export ] [ no-peer ] [ additive ] ] } | { [ no-export [ local-as ] [ no-advertise ] [ no-peer ] [ additive ] ] } | { [ no-peer [ local-as ] [ no-advertise ] [ no-export ] [ additive ] ] } } ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| comm-num1 | AA:NN  | String  |
| comm-num2 | AA:NN  | String  |
| comm-num3 | AA:NN  | String  |
| comm-num4 | AA:NN  | String  |
| comm-num5 | AA:NN  | String  |
## set copp-action 

### Description 

```
Updates class-map match ACL attributes


```
### Parent Commands (Modes) 

```
class <fbs-class-name> [ priority <fbs-flow-priority> ]

```
### Syntax 

```
set copp-action <copp-action-name>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| copp-action-name | WORD  | String  |


### Examples 
#### Configures class-map match acess-group attributes 

```
sonic(config)# class-map class_ip_acl match-type acl
sonic(config-class-map)# match access-group ip ip_acl1


```
## set dscp 
### Description 
```
Configures DSCP Remarking action for Qos Flow
```
### Parent Commands (Modes) 
```
class <fbs-class-name> [ priority <fbs-flow-priority> ]
```
### Syntax 
```
set dscp <dscp-value>
no set dscp
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| dscp-value |   | Integer  |
### Examples 
#### Configures DSCP remarking action for Qos flow 
```
sonic(config)# policy-map policy_qos type qos
sonic(config-policy-map)# class class_permit_ip priority 10
sonic(config-policy-map-flow)#set dscp 10
```
## set extcommunity 
### Description 
```
BGP extended community attribute 
```
### Parent Commands (Modes) 
```
route-map <route-map-name> { permit | deny } <seq-nu>
```
### Syntax 
```
set extcommunity { { rt <value> } | { soo <value> } }
no set extcommunity { { [ rt <value> ] } | { [ soo <value> ] } } ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| value | ASN:NN_OR_IP-ADDRESS:NN  | String  |
## set interface 
### Description 
```
Configures ip interface for forwarding flow
```
### Parent Commands (Modes) 
```
class <fbs-class-name> [ priority <fbs-flow-priority> ]
```
### Syntax 
```
set interface { null | <port-id> | <portchannel-id> } [ priority <priority-value> ]
no set interface { null | <port-id> | <portchannel-id> } [ priority <priority-value> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| port-id | EthernetNUM  |   |
| portchannel-id | PortChannelNUM  |   |
| priority-value |   | Integer  |
### Usage Guidelines 
```
Egress interfaces configuration is valid only if the classifier uses MAC/L2 ACL for match. Only L2 switched traffic will be forwarded to the configured egress interface.  Combining egress interface with IPv4 or IPv6 next-hops is not permitted.
Drop action(set interface to null) if configured will be of the lowest priority and will be chosen if none of the configured next-hops or egress interfaces can be used for forwarding
```
### Examples 
#### Configures ip next hop for forwarding Flow 
```
sonic(config)# policy-map policy_vrf type forwarding
sonic(config-policy-map)# class class10 priority 10
sonic(config-policy-map-flow)# set interface Eth 1/9
sonic(config-policy-map-flow)# set interface null
```
## set ip 
### Description 
```
Configures ip next hop for forwarding flow
```
### Parent Commands (Modes) 
```
class <fbs-class-name> [ priority <fbs-flow-priority> ]
```
### Syntax 
```
set ip next-hop <ip-address> [ vrf { <vrf-name> | default } ] [ priority <priority-value> ]
no set ip next-hop <ip-address> [ vrf { <vrf-name> | default } ] [ priority <priority-value> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| ip-address | A.B.C.D  | String  |
| vrf-name | VRF name (prefixed by Vrf, Max: 15 characters)  | String  |
| priority-value |   | Integer  |
### Usage Guidelines 
```
If the VRF name is not specified then it will be derived from the VRF of the interface on which the policy is applied or default will be used for global application.
Priority of the next-hop. Range is 1-65535. Default is 0 ie lowest priority if not configured by the user. The next-hop with the higher priority will be picked up for forwarding first. If more than 1 next-hops have the same priority then the next-hop which is configured first will be used
```
### Examples 
#### Configures ip next hop for forwarding Flow 
```
sonic(config)# policy-map policy_vrf type forwarding
sonic(config-policy-map)# class class_permit_ip priority 10
set ip next-hop 12.12.2.2 vrf Vrf-BLUE priority 20
```
## set ip 
### Description 
```
IPv4 information 
```
### Parent Commands (Modes) 
```
route-map <route-map-name> { permit | deny } <seq-nu>
```
### Syntax 
```
set ip next-hop <ip-addr>
no set ip next-hop <ip-addr>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| ip-addr | A.B.C.D  | String  |
## set ipv6 
### Description 
```
Configures ipv6 next hop for forwarding flow
```
### Parent Commands (Modes) 
```
class <fbs-class-name> [ priority <fbs-flow-priority> ]
```
### Syntax 
```
set ipv6 next-hop <ip-address> [ vrf { <vrf-name> | default } ] [ priority <priority-value> ]
no set ipv6 next-hop <ip-address> [ vrf { <vrf-name> | default } ] [ priority <priority-value> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| ip-address | A::B  | String  |
| vrf-name | VRF name (prefixed by Vrf, Max: 15 characters)  | String  |
| priority-value |   | Integer  |
### Usage Guidelines 
```
If the VRF name is not specified then it will be derived from the VRF of the interface on which the policy is applied or default will be used for global application.
Priority of the next-hop. Range is 1-65535. Default is 0 ie lowest priority if not configured by the user. The next-hop with the higher priority will be picked up for forwarding first. If more than 1 next-hops have the same priority then the next-hop which is configured first will be used
```
### Examples 
#### Configures ipv6 next hop for forwarding Flow 
```
sonic(config)# policy-map policy_vrf type forwarding
sonic(config-policy-map)# class class_permit_ipv6 priority 10
sonic(config-policy-map)# set ipv6 next-hop 1211::2  priority 20
```
## set ipv6 next-hop 
### Description 
```
IPv6 next-hop address 
```
### Parent Commands (Modes) 
```
route-map <route-map-name> { permit | deny } <seq-nu>
```
### Syntax 
```
set ipv6 next-hop { prefer-global | { global <ip-addr> } }
no set ipv6 next-hop { prefer-global | { global <ip-addr> } }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| ip-addr | A::B  | String  |
## set local-preference 
### Description 
```
BGP local preference path attribute 
```
### Parent Commands (Modes) 
```
route-map <route-map-name> { permit | deny } <seq-nu>
```
### Syntax 
```
set local-preference <pvalue>
no set local-preference [ <pvalue> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| pvalue |   | Integer  |
## set metric 
### Description 
```
Set metric value action for the routing policy 
```
### Parent Commands (Modes) 
```
route-map <route-map-name> { permit | deny } <seq-nu>
```
### Syntax 
```
set metric { <metric> | rtt | +rtt | -rtt }
no set metric { [ <metric> ] | [ rtt ] | [ +rtt ] | [ -rtt ] } ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| metric | (0-4294967295) +(0-4294967295) -(0-4294967295)  | String  |
## set mirror-session 
### Description 
```
Configures mirror session name for monitoring flow
```
### Parent Commands (Modes) 
```
class <fbs-class-name> [ priority <fbs-flow-priority> ]
```
### Syntax 
```
set mirror-session <session-name>
no set mirror-session
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| session-name | String  | String  |
### Examples 
#### Configures mirror session name for monitoring Flow 
```
sonic(config)# policy-map policy_mirror type monitoring
sonic(config-policy-map)# class class1 priority 10
sonic(config-policy-map-flow)# set mirror-session mirror1
```
## set origin 
### Description 
```
BGP origin code 
```
### Parent Commands (Modes) 
```
route-map <route-map-name> { permit | deny } <seq-nu>
```
### Syntax 
```
set origin { egp | igp | incomplete }
no set origin { [ egp ] | [ igp ] | [ incomplete ] } ]
```
## set pcp 
### Description 
```
Configures PCP Remarking action for Qos Flow
```
### Parent Commands (Modes) 
```
class <fbs-class-name> [ priority <fbs-flow-priority> ]
```
### Syntax 
```
set pcp <pcp-value>
no set pcp
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| pcp-value | 0-7  | Integer  |
### Examples 
#### Configures PCP remarking action for Qos flow 
```
sonic(config)# policy-map policy_qos type qos
sonic(config-policy-map)# class class_permit_ip priority 10
sonic(config-policy-map-flow)#set pcp 1
```
## set traffic-class 
### Description 
```
Configures set traffic class action for Qos Flow
```
### Parent Commands (Modes) 
```
class <fbs-class-name> [ priority <fbs-flow-priority> ]
```
### Syntax 
```
set traffic-class <tc-value>
no set traffic-class
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| tc-value | 0-7  | Integer  |
### Examples 
#### Configures traffic class action for Qos Flow 
```
sonic(config)# policy-map policy_qos type qos
sonic(config-policy-map)# class class_permit_ip priority 10
sonic(config-policy-map-flow)#set traffic-class 1
```
## set trap-action 

### Description 

```
Set a CoPP trap action

```
### Parent Commands (Modes) 

```
copp-action <copp-action-name>

```
### Syntax 

```
set trap-action <trap-action-value>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| trap-action-value |   | Select [drop(DROP) forward(FORWARD) copy(COPY) copy_cancel(COPY_CANCEL) trap(TRAP) log(LOG) deny(DENY) transit(TRANSIT) ]  |


### Examples 

```
sonic(config-action)# set trap-action copy
sonic(config-action)#


```
## set trap-priority 
### Description 
```
Set a CoPP trap priority
```
### Parent Commands (Modes) 
```
copp-action <copp-action-name>
```
### Syntax 
```
set trap-priority <trap-priority-value>
no set trap-priority
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| trap-priority-value |   | Integer  |
### Examples 
```
sonic(config-action)# set trap-priority 3
sonic(config-action)#
```
## set trap-queue 
### Description 
```
Set a CoPP queue id
```
### Parent Commands (Modes) 
```
copp-action <copp-action-name>
```
### Syntax 
```
set trap-queue <queue-id-value>
no set trap-queue
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| queue-id-value |   | Integer  |
### Examples 
```
sonic(config-action)# set trap-queue 3
sonic(config-action)#
```
## sflow agent-id 
### Description 
```
Configure sFlow agent interface
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
sflow agent-id { <phy-if-name> | <vlan-if-name> | <loop-if-name> }
no sflow agent-id
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| phy-if-name | EthernetNUM  |   |
| vlan-if-name | VlanNUM  |   |
| loop-if-name |   |   |
### Examples 
```
sonic(config)# sflow agent-id Ethernet0
sonic(config)#
```
## sflow collector 
### Description 
```
Add an sFLow Collector
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
sflow collector <ip> [ <port> ] [ vrf <vrf_name> ]
no sflow collector <ip> [ <port> ] [ vrf <vrf_name> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| ip | A.B.C.D or A:B:C:D:E:F:G:H  | String  |
| port |   | Integer  |
| vrf_name | String(Max: 16 characters)  | String  |
### Examples 
#### Add an sFlow collector using default port 6343 
```
sonic(config)# sflow collector 1.1.1.1
sonic(config)#
```
#### Add an sFlow collector with port number 
```
sonic(config)# sflow collector 1.1.1.2 port 4451
sonic(config)#
```
#### Add an sFlow collector with port number and mgmt VRF 
```
sonic(config)# sflow collector 1.1.1.2 port 4451 vrf mgmt
sonic(config)#
```
## sflow enable 
### Description 
```
Enable sFlow
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
sflow enable
no sflow enable
```
### Examples 
```
sonic(config)# sflow enable
sonic(config)#
```
## sflow enable 
### Description 
```
Enable sflow 
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
sflow enable
no sflow enable
```
## sflow polling-interval 
### Description 
```
Configure sFlow polling interval
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
sflow polling-interval <interval>
no sflow polling-interval
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| interval | 0 to disable  | Integer  |
### Examples 
```
sonic(config)# sflow polling-interval 44
sonic(config)#
```
## sflow sampling-rate 
### Description 
```
Set sampling-rate 
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
sflow sampling-rate <rate>
no sflow sampling-rate
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| rate |   | Integer  |
## show PortChannel summary 

### Description 

```
LAG status and configurationn 


```
### Syntax 

```
show PortChannel summary

```
## show Vlan 

### Description 

```
Show VLAN commands 


```
### Syntax 

```
show Vlan [ <id> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| id |   | Integer  |


## show aaa 

### Description 

```
Show aaa info 


```
### Syntax 

```
show aaa

```
## show access-group 

### Description 

```
Display ACL binding summary


```
### Syntax 

```
show access-group

```
### Examples 
#### show access-group 

```
Ingress IPV6 access-list ipv6acl-example on Ethernet0
Ingress IP access-list ipacl-example on PortChannel1
Ingress MAC access-list macacl-example on Vlan100


```
### Alternate command 
#### click 

```
admin@sonic:~$ show acl table [name]


```
## show audit-log 

### Description 

```
Display audit log 


```
### Syntax 

```
show audit-log [ <stype> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| stype |   | Select [all(all) ]  |


## show authentication 

### Description 

```
Show authentication modes 


```
### Syntax 

```
show authentication

```
## show authentication rest 

### Description 

```
Show rest authentication modes 


```
### Syntax 

```
show authentication rest

```
## show authentication telemetry 

### Description 

```
Show telemetry authentication modes 


```
### Syntax 

```
show authentication telemetry

```
## show bfd peer 

### Description 

```
Display specific Bidirectional Forwarding detection(BFD) peer with the specificed filters..


```
### Syntax 

```
show bfd peer { <peer_ipv4> | <peer_ipv6> } [ vrf <vrfname> ] [ multihop ] [ local-address { <local_ipv4> | <local_ipv6> } ] [ interface <interfacename> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| peer_ipv4 | A.B.C.D  | String  |
| peer_ipv6 | A::B  | String  |
| vrfname | String  | String  |
| local_ipv4 | A.B.C.D  | String  |
| local_ipv6 | A::B  | String  |
| interfacename | String  | String  |


### Examples 
#### Display singlehop peer 

```
device# show bfd peer 192.168.2.1 interface Ethernet0
            BFD Peers:


                peer 192.168.2.1 vrf default interface Ethernet0
                    ID: 106764218
                    Remote ID: 3876686491
                    Status: up
                    Uptime: 0 day(s), 0 hour(s), 51 min(s), 49 sec(s)
                    Diagnostics: ok
                    Remote diagnostics: ok
                    Peer Type: configured
                    Local timers:
                        Detect-multiplier: 3
                        Receive interval: 300ms
                        Transmission interval: 300ms
                        Echo transmission interval: 0ms
                    Remote timers:
                        Detect-multiplier: 3
                        Receive interval: 300ms
                        Transmission interval: 300ms
                        Echo transmission interval: 50ms


```
#### Display multihop peer 

```
device# show bfd peer 192.168.2.1 multihop local-address 192.168.2.2
            BFD Peers:


                peer 192.168.2.1 multihop local-address 192.168.2.2 vrf default
                    ID: 2900535060
                    Remote ID: 0
                    Status: down
                    Downtime: 0 day(s), 0 hour(s), 0 min(s), 30 sec(s)
                    Diagnostics: ok
                    Remote diagnostics: ok
                    Peer Type: configured
                    Local timers:
                        Detect-multiplier: 3
                        Receive interval: 300ms
                        Transmission interval: 300ms
                        Echo transmission interval: 60ms
                    Remote timers:
                        Detect-multiplier: 3
                        Receive interval: 1000ms
                        Transmission interval: 1000ms
                        Echo transmission interval: 0ms


```
## show bfd peer counters 

### Description 

```
Displays counters for the specified Bidirectional Forwarding detection(BFD) peer with the input filters.


```
### Syntax 

```
show bfd peer counters { <peer_ipv4> | <peer_ipv6> } [ vrf <vrfname> ] [ multihop ] [ local-address { <local_ipv4> | <local_ipv6> } ] [ interface <interfacename> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| peer_ipv4 | A.B.C.D  | String  |
| peer_ipv6 | A::B  | String  |
| vrfname | String  | String  |
| local_ipv4 | A.B.C.D  | String  |
| local_ipv6 | A::B  | String  |
| interfacename | String  | String  |


### Examples 
#### Display singlehop peer counters 

```
device# show bfd peer counters 192.168.2.1 interface Ethernet0
peer 192.168.2.1 vrf default interface Ethernet0
    Control packet input: 25 packets
    Control packet output: 25 packets
    Echo packet input: 0 packets
    Echo packet output: 0 packets
    Session up events: 1
    Session down events: 0
    Zebra notifications: 0


```
#### Display multihop peer 

```
device# show bfd peer counters 192.168.2.1 multihop local-address 192.168.2.2
peer 192.168.2.1 multihop local-address 192.168.2.2 vrf default
    Control packet input: 25 packets
    Control packet output: 25 packets
    Echo packet input: 0 packets
    Echo packet output: 0 packets
    Session up events: 1
    Session down events: 0
    Zebra notifications: 0


```
## show bfd peers 

### Description 

```
Displays all Bidirectional Forwarding detection(BFD) peers or counters.


```
### Syntax 

```
show bfd peers [ vrf <vrfname> ] { [ brief ] | [ counters ] } ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrfname | String  | String  |


### Examples 
#### Display BFD peers 

```
device# show bfd peers
BFD Peers:


    peer 192.168.2.2 vrf default interface Ethernet0
        ID: 1861362724
        Remote ID: 3644437776
        Status: up
        Uptime: 0 day(s), 0 hour(s), 0 min(s), 6 sec(s)
        Diagnostics: ok
        Remote diagnostics: ok
        Peer Type: configured
        Local timers:
            Detect-multiplier: 3
            Receive interval: 300ms
            Transmission interval: 300ms
            Echo transmission interval: 0ms
        Remote timers:
            Detect-multiplier: 3
            Receive interval: 300ms
            Transmission interval: 300ms
            Echo transmission interval: 50ms


```
#### Display BFD peers in user vrf 

```
device# show bfd peers vrf Vrf7
BFD Peers:


    peer 192.168.2.2 vrf Vrf7 interface Ethernet0
        ID: 1861362724
        Remote ID: 3644437776
        Status: up
        Uptime: 0 day(s), 0 hour(s), 0 min(s), 6 sec(s)
        Diagnostics: ok
        Remote diagnostics: ok
        Peer Type: configured
        Local timers:
            Detect-multiplier: 3
            Receive interval: 300ms
            Transmission interval: 300ms
            Echo transmission interval: 0ms
        Remote timers:
            Detect-multiplier: 3
            Receive interval: 300ms
            Transmission interval: 300ms
            Echo transmission interval: 50ms


```
#### Display BFD peers counters 

```
device# show bfd peers counters
BFD Peers:

    peer 192.168.2.2 vrf default interface Ethernet0
        Control packet input: 239 packets
        Control packet output: 292 packets
        Echo packet input: 0 packets
        Echo packet output: 0 packets
        Session up events: 1
        Session down events: 0
        Zebra notifications: 0


```
#### Display BFD peers counters in user vrf 

```
device# show bfd peers vrf Vrf7 counters
BFD Peers:

    peer 192.168.2.2 vrf Vrf7 interface Ethernet0
        Control packet input: 239 packets
        Control packet output: 292 packets
        Echo packet input: 0 packets
        Echo packet output: 0 packets
        Session up events: 1
        Session down events: 0
        Zebra notifications: 0


```
## show bgp all 

### Description 

```
Display BGP information for all address families 


```
### Syntax 

```
show bgp all [ vrf <vrf-name> ] { { peer-group [ <peer-group-name> ] } | { neighbors { [ <neighbor-ip> ] | { [ interface { Ethernet | PortChannel | Vlan } ] } ] } } }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrf-name | WORD  | String  |
| peer-group-name | WORD  | String  |
| neighbor-ip | A.B.C.D/A::B  | String  |


## show bgp as-path-access-list 

### Description 

```
This command displays BGP AS Path lists configured on the device.


```
### Syntax 

```
show bgp as-path-access-list [ <list-name> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| list-name | WORD  | String  |


### Usage Guidelines 

```
User configures AS Path lists to use it in route-maps and with
neighbors to design BGP routing policies. This command enables users
to display the AS Path lists configured on this device. User can
provide AS path list name optional CLI key to display only that AS
Path list. If AS Path list name is not specified, all AS Path lists will
be displayed by this command.


```
### Examples 
#### The following command displays all AS Path lists            configured in BGP 

```
sonic# show bgp as-path-access-list
AS path list asp_private:
   members: ^65000.*6510565109$,65107.*65200
AS path list asp_public:
   members: ^107.*2301.*709$,97.*201


```
## show bgp community-list 

### Description 

```
This command displays BGP community lists configured on the device.


```
### Syntax 

```
show bgp community-list [ <list-name> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| list-name | WORD  | String  |


### Usage Guidelines 

```
User configures community-list to use it in route-maps to design BGP
routing policies. This command enables users to display the community
lists configured on this device. User can provide community list name
optional CLI key to display only that community list. If community list
name is not specified, all community lists will be displayed by this command.


```
### Examples 
#### The following command displays all communities            configured in BGP 

```
sonic# show bgp community-list
Expanded community list CommList_Exp:   match: ANY
     300:500
     800:900
     no-export
Standard community list CommList_RT:  match: ANY
     100:200
     no-export
     no-peer
     65100:3456


```
## show bgp ext-community-list 

### Description 

```
This command displays BGP extended community lists configured on the device.


```
### Syntax 

```
show bgp ext-community-list [ <list-name> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| list-name | WORD  | String  |


### Usage Guidelines 

```
User configures extended community-list to use it in route-maps to design BGP
routing policies. This command enables users to display the extended community
lists configured on this device. User can provide extended community list name
optional CLI key to display only that extended community list. If
extended community list name is not specified, all extended community lists will
be displayed by this command.


```
### Examples 
#### The following command displays all extended communities            configured in BGP 

```
sonic# show bgp ext-community-list
Standard extended community list ExtComm_AllowInt:  match: ALL
     rt:19.32.56.167:65011,rt:31.67.182.214:3001,soo:01:65010,soo:.13.175.21:65101
Standard extended community list ExtComm_BlockExt:  match: ANY
     rt:4020:65104
     soo:9.54.32.165:65200


```
## show bgp ipv4 

### Description 

```
This command displays BGP information including routes, neighbors,
peer-group etc.


```
### Syntax 

```
show bgp ipv4 unicast [ vrf { <vrf-name> | all } ] { { [ <ip-addr> { [ bestpath ] | [ multipath ] ] } ] } | { [ <ip-prefix> { [ bestpath ] | [ multipath ] ] } ] } | { [ community { <aann> | local-as | no-advertise | no-export | no-peer } [ exact-match ] ] } | { [ route-map <route-map-name> ] } | [ statistics ] | { [ neighbors { { [ <neighbor-ip> { [ routes ] | [ received-routes ] | [ advertised-routes ] ] } ] } | { [ interface { { Ethernet { [ routes ] | [ received-routes ] | [ advertised-routes ] ] } } | { PortChannel { [ routes ] | [ received-routes ] | [ advertised-routes ] ] } } | { Vlan { [ routes ] | [ received-routes ] | [ advertised-routes ] ] } } } ] } ] } ] } | [ summary ] | { [ dampening { dampened-paths | flap-statistics | parameters } ] } } ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrf-name | WORD  | String  |
| ip-addr | A.B.C.D  | String  |
| ip-prefix | A.B.C.D/mask  | String  |
| aann | AA:NN  | String  |
| route-map-name | WORD  | String  |
| neighbor-ip | A.B.C.D/A::B  | String  |


### Usage Guidelines 

```
Use this command to display BGP neighbors, routes, peer-group etc. There
are various CLI options available to display various informations from
BGP. User can use "vrf" option to display information from a particular
VRF instance of BGP. User can also choose ipv4 or ipv6 to display
information from either of the address family.
- show bgp ipv4 unicast summary
This command will display BGP global parameters as well as brief
information about BGP neighbors
- show bgp ipv4 unicast
This command will show BGP local RIB routes. User can use filtering
options on CLI to zoom into a subset of routes that user is interested
in
- show bgp ipv4 unicast neighbors
This will display one or all BGP neighbors information in detail
- show bgp all peer-group
This will display one or all BGP peer-group information in detail


```
### Examples 
#### Below are some of the sample outputs of show ip bgp          command 

```
leaf4# show bgp ipv4 unicast summary
BGP router identifier 200.9.0.5, local AS number 100
Neighbor        V   AS    MsgRcvd   MsgSent   InQ     OutQ    Up/Down  State/PfxRcd
14.14.14.1      4   400   8         2         0       0       00:00:43 0

leaf4# show bgp ipv4 unicast
BGP routing table information for VRF default
Router identifier 200.9.0.5, local AS number 100
Route status codes: * - valid, > - best
Origin codes: i - IGP, e - EGP, ? - incomplete
     Network             Next Hop            Metric         LocPref Path
*>   4.4.4.44/32         14.14.14.1          0                      400 ?
*>   10.59.128.0/20      14.14.14.1          0                      400 ?
*>   13.1.1.0/24         14.14.14.1          0                      400 ?
*>   14.14.14.0/24       14.14.14.1          0                      400 ?
*>   29.2.2.2/32         14.14.14.1          0                      400 ?
*>   192.168.1.0/24      14.14.14.1          0                      400 ?
*>   200.0.0.0/24        14.14.14.1          0                      400 ?

leaf4# show bgp ipv4 unicast neighbors

BGP neighbor is 14.14.14.1, remote AS 400, local AS 100, external link
  Administratively shut down
  BGP version 4, remote router ID  , local router ID
  BGP state = ESTABLISHED, up for 00:01:03
  Hold time is  seconds, keepalive interval is 60 seconds, negotiated hold time is 180 seconds
  Minimum time between advertisement runs is  seconds
  Neighbor capabilities:
    4 Byte AS: advertised and received
  Message statistics:
    InQ depth is 0
    OutQ depth is 0
                          Sent        Rcvd
    Opens:                1           1
    Notifications:        0           0
    Updates:              2           8
    Keepalive:            2           2
    Route Refresh:        0           0
    Capability:           0           0
    Total:                5           11

  Local host: 14.14.14.4, Local port: 46782
  Foreign host: 14.14.14.1, Foreign port: 179


```
## show bgp ipv6 

### Description 

```
IPv6 information 


```
### Syntax 

```
show bgp ipv6 unicast [ vrf { <vrf-name> | all } ] { { [ <ip-addr> { [ bestpath ] | [ multipath ] ] } ] } | { [ <ip-prefix> { [ bestpath ] | [ multipath ] ] } ] } | { [ community { <aann> | local-as | no-advertise | no-export | no-peer } [ exact-match ] ] } | { [ route-map <route-map-name> ] } | [ statistics ] | { [ neighbors { { [ <neighbor-ip> { [ routes ] | [ received-routes ] | [ advertised-routes ] ] } ] } | { [ interface { { Ethernet { [ routes ] | [ received-routes ] | [ advertised-routes ] ] } } | { PortChannel { [ routes ] | [ received-routes ] | [ advertised-routes ] ] } } | { Vlan { [ routes ] | [ received-routes ] | [ advertised-routes ] ] } } } ] } ] } ] } | [ summary ] | { [ dampening { dampened-paths | flap-statistics | parameters } ] } } ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrf-name | WORD  | String  |
| ip-addr | A::B  | String  |
| ip-prefix | A::B/mask  | String  |
| aann | AA:NN  | String  |
| route-map-name | WORD  | String  |
| neighbor-ip | A.B.C.D/A::B  | String  |


## show bgp l2vpn evpn route 

### Description 

```
This command displays BGP EVPN routes in tabular format.


```
### Syntax 

```
show bgp l2vpn evpn route

```
### Usage Guidelines 

```
show bgp l2vpn evpn route {filters}


```
### Examples 
#### Below is a sample output for show bgp l2vpn evpn route 

```
sonic# show bgp l2vpn evpn route
BGP table version is 2, local router ID is 10.59.142.127
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal
Origin codes: i - IGP, e - EGP, ? - incomplete
EVPN type-1 prefix: [1]:[ESI]:[EthTag]
EVPN type-2 prefix: [2]:[EthTag]:[MAClen]:[MAC]:[IPlen]:[IP]
EVPN type-3 prefix: [3]:[EthTag]:[IPlen]:[OrigIP]
EVPN type-4 prefix: [4]:[ESI]:[IPlen]:[OrigIP]
EVPN type-5 prefix: [5]:[EthTag]:[IPlen]:[IP]

   Network          Next Hop            Metric LocPrf Weight Path
                    Extended Community
Route Distinguisher: 11:11
*>  [5]:[0]:[0]:[0.0.0.0]
                    0.0.0.0                                    32768 i
                    ET:8
*>  [5]:[0]:[0]:[::] 0.0.0.0                                    32768 i
                    ET:8
Route Distinguisher: 22:22
*>  [2]:[0]:[48]:[52:54:00:76:be:f7]:[32]:[2.1.1.1]
                    1.1.1.1                                    32768 i
                    ET:8 RT:100:268435556 Default Gateway
*>  [2]:[0]:[48]:[52:54:00:cb:f0:e3]
                    1.1.1.1                                    32768 i
                    ET:8 RT:100:268435556
*>  [2]:[0]:[48]:[52:54:00:cb:f0:e3]:[32]:[2.1.1.2]
                    1.1.1.1                                    32768 i
                    ET:8 RT:100:268435556
*>  [3]:[0]:[32]:[1.1.1.1]
                    1.1.1.1                                    32768 i
                    ET:8 RT:100:268435556
Route Distinguisher: 3.1.1.1:5096
*>  [5]:[0]:[24]:[3.1.1.0]
                    1.1.1.1                  0                 32768 ?
                    ET:8 RT:100:200 Rmac:52:54:00:76:be:f7
Route Distinguisher: 4.1.1.2:5096
*>  [5]:[0]:[24]:[4.1.1.0]
                    2.2.2.2                  0                     0 200 ?
                    RT:200:200 ET:8 Rmac:52:54:00:cb:f0:e3
Route Distinguisher: 10.59.143.68:100
*>  [2]:[0]:[48]:[52:54:00:cb:f0:e3]:[32]:[2.1.1.2]
                    2.2.2.2                                        0 200 i
                    RT:200:100 ET:8 Default Gateway
*>  [3]:[0]:[32]:[2.2.2.2]
                    2.2.2.2                                        0 200 i
                    RT:200:100 ET:8

Displayed 10 prefixes (10 paths)
sonic#



```
## show bgp l2vpn evpn route detail 

### Description 

```
This command displays BGP EVPN routes in detail format.


```
### Syntax 

```
show bgp l2vpn evpn route detail

```
### Usage Guidelines 

```
show bgp l2vpn evpn route detail {filters}


```
### Examples 
#### Below is a sample output for show bgp l2vpn evpn route         in detailed format with rd and type filter 

```
sonic# show bgp l2vpn evpn route rd 11:11 type prefix
EVPN type-2 prefix: [2]:[EthTag]:[MAClen]:[MAC]
EVPN type-3 prefix: [3]:[EthTag]:[IPlen]:[OrigIP]
EVPN type-5 prefix: [5]:[EthTag]:[IPlen]:[IP]

BGP routing table entry for 11:11:[5]:[0]:[0]:[0.0.0.0]
Paths: (1 available, best #1)
  Zebra Add: 6d21h36m
  Advertised to non peer-group peers:
  10.1.1.2
  Route [5]:[0]:[0]:[0.0.0.0] VNI 0
  Local
    0.0.0.0 from 0.0.0.0 (10.59.142.127)
      Origin IGP, weight 32768, valid, sourced, local, best (First path received)
      Extended Community: ET:8
      Last update: Wed Feb 12 17:06:15 2020

BGP routing table entry for 11:11:[5]:[0]:[0]:[::]
Paths: (1 available, best #1)
  Zebra Add: 6d21h36m
  Advertised to non peer-group peers:
  10.1.1.2
  Route [5]:[0]:[0]:[::] VNI 0
  Local
    0.0.0.0 from 0.0.0.0 (10.59.142.127)
      Origin IGP, weight 32768, valid, sourced, local, best (First path received)
      Extended Community: ET:8
      Last update: Wed Feb 12 17:06:15 2020


Displayed 2 prefixes (2 paths) with this RD (of requested type)
sonic#


```
## show bgp l2vpn evpn route detail type 

### Description 

```
This command displays BGP EVPN routes of a specified type in detailed format


```
### Syntax 

```
show bgp l2vpn evpn route detail type { ead | es | macip | multicast | prefix }

```
### Usage Guidelines 

```
show bgp l2vpn evpn route detail type ead|es|macip|multicast|prefix


```
### Examples 
#### User can filter route based on type 

```
Refer example from show bgp l2vpn evpn route detail


```
## show bgp l2vpn evpn route rd 

### Description 

```
This command displays BGP EVPN routes for a specific RD in detailed format.


```
### Syntax 

```
show bgp l2vpn evpn route rd <rdvalue> { { [ mac { <macvalue> { ip <ipvalue> } } ] } | { [ type { ead | es | macip | multicast | prefix } ] } ] }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| rdvalue | A.B.C.D:NN or ASN:NN  | String  |
| macvalue | nn:nn:nn:nn:nn:nn  | String  |
| ipvalue | A.B.C.D/A::B  | String  |


### Usage Guidelines 

```
show bgp l2vpn evpn route rd {rdvalue} {filters}


```
### Examples 
#### User can filter route based on type 

```
Refer example from show bgp l2vpn evpn route detail


```
## show bgp l2vpn evpn route type 

### Description 

```
This command displays BGP EVPN routes of a specified type


```
### Syntax 

```
show bgp l2vpn evpn route type { ead | es | macip | multicast | prefix }

```
### Usage Guidelines 

```
show bgp l2vpn evpn route type ead|es|macip|multicast|prefix


```
### Examples 
#### User can filter route based on type 

```
Refer example from show bgp l2vpn evpn route


```
## show bgp l2vpn evpn route vni 

### Description 

```
This command displays BGP EVPN routes for a specified VNI


```
### Syntax 

```
show bgp l2vpn evpn route vni <vninum>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vninum | VNI  | Integer  |


### Usage Guidelines 

```
show bgp l2vpn evpn route vni <vni-num>


```
### Examples 
#### Route display 

```
Refer example from show bgp l2vpn evpn route


```
## show bgp l2vpn evpn summary 

### Description 

```
This command displays BGP summarized information for BGP L2VPN EVPN address family including neghbors
with evpn address family activated


```
### Syntax 

```
show bgp l2vpn evpn summary

```
### Usage Guidelines 

```
show bgp l2vpn evpn summary


```
### Examples 
#### Below is sample output for show bgp l2vpn evpn summary command 

```
sonic# show bgp l2vpn evpn summary
BGP router identifier 10.59.142.127, local AS number 100 vrf-id 0
BGP table version 0

Neighbor        V         AS MsgRcvd MsgSent   TblVer  InQ OutQ  Up/Down State/PfxRcd
10.1.1.2        4        200   11338   11337        0    0    0 6d21h29m            3

Total number of neighbors 1
Total number of neighbors established 1
sonic#


```
## show bgp l2vpn evpn vni 

### Description 

```
This command displays VNI information including RD, Route-targets etc.


```
### Syntax 

```
show bgp l2vpn evpn vni <vninum>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vninum | VNI  | Integer  |


### Usage Guidelines 

```
show bgp l2vpn evpn vni {vni-number}


```
### Examples 
#### Below are some of the sample outputs of show bgp l2vpn evpn vni               command for VNI 100 

```
sonic# show bgp l2vpn evpn vni 100
VNI: 100 (known to the kernel)
  Type: L2
  Tenant-Vrf: default
  RD: 22:22
  Originator IP: 1.1.1.1
  Mcast group: 0.0.0.0
  Advertise-gw-macip : Yes
  Advertise-svi-macip : No
  Import Route Target:
    22:22
    22:23
  Export Route Target:
    100:268435556


```
## show buffer_pool 

### Description 

```
This command is used to show user/persistant watermark counters recorded by the system.


```
### Syntax 

```
show buffer_pool { watermark | persistent-watermark } [ percentage ]

```
### Usage Guidelines 

```
Use this command to display user/persistant watermark counters recorded by the system.


```
### Examples 
#### Show buffer_pool watermark 

```
show buffer_pool watermark {percentage}
show buffer_pool persistent-watermark (percentage)


```
## show class-map 

### Description 

```
Shows flow based services class-map related information


```
### Syntax 

```
show class-map { [ <show-fbs-class-name> ] | [ match-type ] } ] { acl | fields | copp }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| show-fbs-class-name | WORD  | String  |


### Usage Guidelines 

```
Class-map match type and class-map name arguments are optional. If match type argument or class-map name not provided command will show all cclass-map information. Else it show corresponding class-map information of given type or given name


```
### Examples 
#### Displays class-map information 

```
sonic# show class-map class_permit_ip
Class-map class_permit_ip match-type fields
  Description:
  Match:
  Referenced in flows:
    policy policy_qos at priority 10
    policy policy_vrf at priority 10


```
## show clock 

### Description 

```
Show system date and time 


```
### Syntax 

```
show clock

```
## show configuration 

### Description 

```
show bgp configuration 


```
### Parent Commands (Modes) 

```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }

```
### Syntax 

```
show configuration

```
## show configuration 

### Description 

```
show bgp nbr configuration 


```
### Parent Commands (Modes) 

```
neighbor { <ip> | { interface { Ethernet | PortChannel | Vlan } } }

```
### Syntax 

```
show configuration

```
## show configuration 

### Description 

```
show bgp peer group configuration 


```
### Parent Commands (Modes) 

```
peer-group <template-str>

```
### Syntax 

```
show configuration

```
## show configuration 

### Description 

```
show vxlan configuration 


```
### Parent Commands (Modes) 

```
interface vxlan <vxlan-if-name>

```
### Syntax 

```
show configuration

```
## show configuration 

### Description 

```
Displays Bidirectional Forwarding detection(BFD) configurations.


```
### Parent Commands (Modes) 

```
bfd

```
### Syntax 

```
show configuration

```
### Examples 
#### Display BFD configurations 

```
device# configure terminal
device(config)# bfd
device(conf-bfd)# show configuration
!
bfd
 peer 192.168.2.1 interface Ethernet0
  detect-multiplier 5
  echo-interval 200
  echo-mode
  receive-interval 200
  transmit-interval 200
 !
 peer 192.168.2.1 multihop local-address 192.168.2.2
  detect-multiplier 4
  receive-interval 150
  transmit-interval 150
 !


```
## show configuration 

### Description 

```
show dscp-tc-map configuration 


```
### Parent Commands (Modes) 

```
qos map dscp-tc <name>

```
### Syntax 

```
show configuration

```
## show configuration 

### Description 

```
show bgp configuration 


```
### Parent Commands (Modes) 

```
address-family l2vpn evpn

```
### Syntax 

```
show configuration

```
## show configuration 

### Description 

```
show bgp neighbor l2vpn evpn configuration 


```
### Parent Commands (Modes) 

```
address-family l2vpn evpn

```
### Syntax 

```
show configuration

```
## show configuration 

### Description 

```
show bgp peer group l2vpn evpn configuration 


```
### Parent Commands (Modes) 

```
address-family l2vpn evpn

```
### Syntax 

```
show configuration

```
## show configuration 

### Description 

```
show pfc-priority-queue-map configuration 


```
### Parent Commands (Modes) 

```
qos map pfc-priority-queue <name>

```
### Syntax 

```
show configuration

```
## show configuration 

### Description 

```
Displays current OSPFv2 router configuration


```
### Parent Commands (Modes) 

```
router ospf [ vrf <vrf-name> ]

```
### Syntax 

```
show configuration

```
### Usage Guidelines 

```
Use this command to display running configurations within current OSPFv2 router.


```
### Examples 
####  

```
sonic-cli(config-router-ospf)# show configuration
!
router ospf
 ospf router-id 10.1.1.1
 network 10.10.3.0/24 area 0.0.0.1
 network 10.10.4.0/24 area 0.0.0.1


```
### Features this CLI belongs to 
*  OSPFv2


## show configuration 

### Description 

```
show scheduler-policy configuration 


```
### Parent Commands (Modes) 

```
qos scheduler-policy <name>

```
### Syntax 

```
show configuration

```
## show configuration 

### Description 

```
show scheduler_policy queue configuration 


```
### Parent Commands (Modes) 

```
queue <sequence>

```
### Syntax 

```
show configuration

```
## show configuration 

### Description 

```
show scheduler_policy port configuration 


```
### Parent Commands (Modes) 

```
port

```
### Syntax 

```
show configuration

```
## show configuration 

### Description 

```
show configuration 


```
### Parent Commands (Modes) 

```
mclag domain <mclag-domain-id>

```
### Syntax 

```
show configuration

```
## show configuration 

### Description 

```
show wred-policy configuration 


```
### Parent Commands (Modes) 

```
qos wred-policy <name>

```
### Syntax 

```
show configuration

```
## show configuration 

### Description 

```
show configuration 


```
### Parent Commands (Modes) 

```
mirror-session <session-name>

```
### Syntax 

```
show configuration

```
## show configuration 

### Description 

```
show dot1p-tc-map configuration 


```
### Parent Commands (Modes) 

```
qos map dot1p-tc <name>

```
### Syntax 

```
show configuration

```
## show configuration 

### Description 

```
show bgp configuration 


```
### Parent Commands (Modes) 

```
vni <vninum>

```
### Syntax 

```
show configuration

```
## show configuration 

### Description 

```
show tc-dot1p-map configuration 


```
### Parent Commands (Modes) 

```
qos map tc-dot1p <name>

```
### Syntax 

```
show configuration

```
## show configuration 

### Description 

```
show tc-pg-map configuration 


```
### Parent Commands (Modes) 

```
qos map tc-pg <name>

```
### Syntax 

```
show configuration

```
## show configuration 

### Description 

```
show bgp configuration 


```
### Parent Commands (Modes) 

```
address-family ipv6 unicast

```
### Syntax 

```
show configuration

```
## show configuration 

### Description 

```
show bgp neighbor IPv6 configuration 


```
### Parent Commands (Modes) 

```
address-family ipv6 unicast

```
### Syntax 

```
show configuration

```
## show configuration 

### Description 

```
show bgp peer group IPv6 configuration 


```
### Parent Commands (Modes) 

```
address-family ipv6 unicast

```
### Syntax 

```
show configuration

```
## show configuration 

### Description 

```
show route-map configuration 


```
### Parent Commands (Modes) 

```
route-map <route-map-name> { permit | deny } <seq-nu>

```
### Syntax 

```
show configuration

```
## show configuration 

### Description 

```
show tc-queue-map configuration 


```
### Parent Commands (Modes) 

```
qos map tc-queue <name>

```
### Syntax 

```
show configuration

```
## show configuration 

### Description 

```
show configuration 


```
### Parent Commands (Modes) 

```
interface <phy-if-name>

```
### Syntax 

```
show configuration

```
## show configuration 

### Description 

```
show configuration 


```
### Parent Commands (Modes) 

```
interface Loopback <lo-id>

```
### Syntax 

```
show configuration

```
## show configuration 

### Description 

```
show configuration 


```
### Parent Commands (Modes) 

```
interface <vlan-if-name>

```
### Syntax 

```
show configuration

```
## show configuration 

### Description 

```
show configuration 


```
### Parent Commands (Modes) 

```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]

```
### Syntax 

```
show configuration

```
## show configuration 

### Description 

```
show configuration 


```
### Parent Commands (Modes) 

```
interface Management <mgmt-if-id>

```
### Syntax 

```
show configuration

```
## show configuration 

### Description 

```
show tc-dscp-map configuration 


```
### Parent Commands (Modes) 

```
qos map tc-dscp <name>

```
### Syntax 

```
show configuration

```
## show configuration 

### Description 

```
show bgp configuration 


```
### Parent Commands (Modes) 

```
address-family ipv4 unicast

```
### Syntax 

```
show configuration

```
## show configuration 

### Description 

```
show bgp neighbor IPv4 configuration 


```
### Parent Commands (Modes) 

```
address-family ipv4 unicast

```
### Syntax 

```
show configuration

```
## show configuration 

### Description 

```
show bgp peer group IPv4 configuration 


```
### Parent Commands (Modes) 

```
address-family ipv4 unicast

```
### Syntax 

```
show configuration

```
## show configuration 

### Description 

```
Displays current NAT configuration


```
### Parent Commands (Modes) 

```
nat

```
### Syntax 

```
show configuration

```
### Usage Guidelines 

```
Use this command to display running configurations.


```
### Features this CLI belongs to 
*  NAT


## show copp 

### Description 

```
Display CoPP information


```
### Syntax 

```
show copp { classifiers | protocols | actions | policy }

```
### Examples 
#### Show CoPP classifier information 

```
sonic-cli# show copp classifiers
Classifier  match-type copp
  protocol arp_req
  protocol arp_resp
  protocol neigh_discovery
Classifier  match-type copp
  protocol bgp


```
#### Show CoPP protocols information 

```
sonic-cli# show copp protocols
Classifier match-type copp protocols
protocol stp
protocol lacp
protocol eapol
protocol lldp
protocol pvrst
protocol igmp_query
protocol igmp_leave
protocol igmp_v1_report
protocol igmp_v2_report
...


```
#### Show CoPP actions information 

```
sonic-cli# show copp actions
CoPP action group copp-system-arp
    trap-action copy
    trap-priority 3
    trap-queue 3
    police cir 6000 cbs 6000
      meter-type packets
      mode sr_tcm
      red-action drop
CoPP action group copp-system-bgp
    police cir 7000 cbs 7000


```
#### Show CoPP policy information 

```
sonic-cli# show copp policy
Policy copp-system-policy Type copp
  Flow copp-system-arp
      trap-action copy
      trap-priority 3
      trap-queue 3
      police cir 6000 cbs 6000
        meter-type packets
        mode sr_tcm
        red-action drop


```
## show core config 

### Description 

```
Display the coredump configuration. Use this command to display if the coredump feature is administratively enabled or disabled.


```
### Syntax 

```
show core config

```
### Usage Guidelines 

```
sonic# show core config


```
### Examples 

```
sonic# show core config
Coredump : Enabled


```
### Features this CLI belongs to 
*  COREDUMP


### Alternate command 
#### click 

```
show cores config


```
## show core info 

### Description 

```
Use this command to display detailed information about a crash that has occured in the system. This command
takes processid or executable name as input to search and display the corresponding crash information. If multiple
core files are found which satisfy the match condition, information of all core files is displayed.

The following information about matching core files is displayed:
-  Time: The time of the crash, as reported by the kernel in UTC
-  Executable: The full path to the application executable that has crashed"
-  Core File: The file name of the application core dump of the executable that has crashed
-  PID: The identifier of the process that crashed
-  User ID: The user identifier of the process that crashed
-  Group ID: The group identifier of the process that crashed
-  Signal: The signal that caused the process to crash, when applicable
-  Command Line: The command line arguments of the process that crashed
-  Boot ID: The unique identifier of the local system that is generated and set on each system boot up event
-  Machine ID: The unique machine identifier of the local system that is set during installation
-  Core File Found: Indicates whether the captured core file exists on local disk or has been removed
-  Crash Message: A copy of the application stack trace information of the process crashed


```
### Syntax 

```
show core info <key>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| key | String  | String  |


### Usage Guidelines 

```
sonic# show core info clish


```
### Examples 

```
sonic# show core info clish
Time            : 2020-05-16 11:54:33
Executable      : /usr/sbin/cli/clish
Core File       : /var/lib/systemd/coredump/core.clish.1000.8f1cad11c59840318a6df3aa6ed3633e.26480.1589630073000000000000.lz4
PID             : 26480
User ID         : 1000
Group ID        : 1000
Signal          : 11
Command Line    : /usr/sbin/cli/clish
Boot ID         : 8f1cad11c59840318a6df3aa6ed3633e
Machine ID      : fc0a437952314ee5a585a94ceaa480af
Core File Found : present
Crash Message   :
Process 26480 (clish) of user 1000 dumped core.
Stack trace of thread 152:
#0  0x00007f30f516357a PyEval_EvalFrameEx (libpython2.7.so.1.0)
#1  0x00007f30f52cc29c PyEval_EvalCodeEx (libpython2.7.so.1.0)
#2  0x00007f30f5220670 n/a (libpython2.7.so.1.0)
#3  0x00007f30f51b85c3 PyObject_Call (libpython2.7.so.1.0)
#4  0x00007f30f52cb6c7 PyEval_CallObjectWithKeywords (libpython2.7.so.1.0)
#5  0x00007f30ebb2f43a n/a (/usr/sbin/cli/.libs/clish_plugin_clish.so)


```
### Features this CLI belongs to 
*  COREDUMP


### Alternate command 
#### click 

```
show cores info


```
## show core list 

### Description 

```
Use this command to list a summary of the core files generated by the kernel. The following information
about each core file is also displayed.
 - TIME The time of the crash, as reported by the kernel in UTC
 - PID: The identifier of the process that crashed
 - SIG: The signal that caused the process to crash, when applicable
 - COREFILE: Indicates whether the captured core file exists on local disk or has been removed
 - EXE: The application executable that has crashed


```
### Syntax 

```
show core list

```
### Usage Guidelines 

```
sonic# show core list


```
### Examples 

```
sonic# show core list
        TIME               PID SIG COREFILE EXE
2020-05-16 11:54:33      26480  11 present  clish
2020-05-15 01:25:16       6195  11 present  crashme
2020-05-15 00:45:28      13604  11 present  crashme
2020-05-14 02:11:11       3197  11 present  crashme
2020-05-13 01:10:56      17844  11 missing  crashme
2020-05-13 01:10:55      17728  11 present  crashme


```
### Features this CLI belongs to 
*  COREDUMP


### Alternate command 
#### click 

```
show cores list


```
## show crm 

### Description 

```
Show CRM information 


```
### Syntax 

```
show crm { summary | { resources { { acl { group | table } } | all | dnat | fdb | ipmc | { ipv4 { neighbor | nexthop | route } } | { ipv6 { neighbor | nexthop | route } } | { nexthop { { group { member | object } } } } | snat } } | { thresholds { { acl { group | table } } | all | dnat | fdb | ipmc | { ipv4 { neighbor | nexthop | route } } | { ipv6 { neighbor | nexthop | route } } | { nexthop { { group { member | object } } } } | snat } } }

```
## show database map 

### Description 

```
Use this command to display a summary of the databases currently in use in SONiC. The following information
about each database is also displayed:
 - ID: Numeric database identifier.
 - Name: Database name string used to refer to the database in the
         sonic-db-cli command and Database connector APIs.
 - Instance: Redis instance this database is part of
 - TCP Port: The TCP Port used to connect to the Redis instance which this
             database is part of.
 - Unix Socket Path: The unix socket path used to connect to the Redis instance
                     which this database is part of.


```
### Syntax 

```
show database map

```
### Usage Guidelines 

```
sonic# show database map


```
### Examples 

```
sonic# show database map
----------------------------------------------------------------------------
 ID             Name         Instance  TCP Port  Unix Socket Path
----------------------------------------------------------------------------
  0          APPL_DB           redis2     26379  /var/run/redis/redis2.sock
  1          ASIC_DB           redis3     36379  /var/run/redis/redis3.sock
  2      COUNTERS_DB            redis      6379  /var/run/redis/redis.sock
  3      LOGLEVEL_DB            redis      6379  /var/run/redis/redis.sock
  4        CONFIG_DB            redis      6379  /var/run/redis/redis.sock
  5        PFC_WD_DB            redis      6379  /var/run/redis/redis.sock
  6         STATE_DB            redis      6379  /var/run/redis/redis.sock
  7  SNMP_OVERLAY_DB            redis      6379  /var/run/redis/redis.sock
  8         ERROR_DB            redis      6379  /var/run/redis/redis.sock


```
## show errdisable recovery 

### Description 

```
Shows error disable recovery information


```
### Syntax 

```
show errdisable recovery

```
### Usage Guidelines 

```
Use this command to check status of error disable recovery for all supported feature


```
### Examples 
#### Shows status of error disable recovery for all supported feature 

```
show errdisable recovery
Errdisable Cause    Status
------------------  --------
udld                enabled
bpduguard           disabled
Timeout for Auto-recovery:  300 seconds



```
### Features this CLI belongs to 
*  ERRDISABLE


## show evpn 

### Description 

```
Displays EVPN information summary 


```
### Syntax 

```
show evpn

```
## show evpn arp-cache vni 

### Description 

```
VxLAN Network Identifier 


```
### Syntax 

```
show evpn arp-cache vni <vninum> { { [ ip <ipvalue> ] } | [ duplicate ] | { [ vtep <vtepvalue> ] } ] }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vninum | VNI  | Integer  |
| ipvalue | A.B.C.D/A::B  | String  |
| vtepvalue | A.B.C.D  | String  |


## show evpn arp-cache vni all 

### Description 

```
All VNIs 


```
### Syntax 

```
show evpn arp-cache vni all { [ detail ] | [ duplicate ] } ]

```
## show evpn mac vni 

### Description 

```
VxLAN Network Identifier 


```
### Syntax 

```
show evpn mac vni <vninum> { { [ mac <macvalue> ] } | [ duplicate ] | { [ vtep <vtepvalue> ] } ] }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vninum | VNI  | Integer  |
| macvalue | nn:nn:nn:nn:nn:nn  | String  |
| vtepvalue | A.B.C.D  | String  |


## show evpn mac vni all 

### Description 

```
All VNIs 


```
### Syntax 

```
show evpn mac vni all { [ detail ] | [ duplicate ] | { [ vtep <vtepvalue> ] } } ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vtepvalue | A.B.C.D  | String  |


## show evpn next-hops vni 

### Description 

```
VxLAN Network Identifier 


```
### Syntax 

```
show evpn next-hops vni <vninum> { [ ip <ipvalue> ] }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vninum | VNI  | Integer  |
| ipvalue | A.B.C.D/A::B  | String  |


## show evpn next-hops vni all 

### Description 

```
All VNIs 


```
### Syntax 

```
show evpn next-hops vni all

```
## show evpn rmac vni 

### Description 

```
VxLAN Network Identifier 


```
### Syntax 

```
show evpn rmac vni <vninum> { [ mac <macvalue> ] }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vninum | VNI  | Integer  |
| macvalue | nn:nn:nn:nn:nn:nn  | String  |


## show evpn rmac vni all 

### Description 

```
All VNIs 


```
### Syntax 

```
show evpn rmac vni all

```
## show evpn vni 

### Description 

```
show VxLAN Network Identifier 


```
### Syntax 

```
show evpn vni <vninum>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vninum | VNI  | Integer  |


## show evpn vni detail 

### Description 

```
Detailed information on each VNI 


```
### Syntax 

```
show evpn vni detail

```
## show hosts 

### Description 

```
IP name servers 


```
### Syntax 

```
show hosts

```
## show image 

### Description 

```
Show image information 


```
### Syntax 

```
show image list

```
## show interface 

### Description 

```
Show interface info 


```
### Syntax 

```
show interface { { counters { [ rate ] | [ <phy-if-name> ] | [ <po-name> ] ] } } | { Eth [ <iface_num> ] } | { Ethernet [ <iface_num> ] } | <iface_range_num> | { PortChannel [ <po-id> ] } | { Management [ <mgmt-if-id> ] } | <vlan_range_num> | { Loopback <lo-id> } | status | { transceiver [ <phy-if-name> ] [ summary ] } }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| phy-if-name | EthernetNUM  |   |
| po-name | Interface PortChannel Range  |   |
| iface_num | EthernetSLOTPORT  |   |
| iface_range_num | Interface Ethernet Range  |   |
| po-id | PortChannel Range  |   |
| mgmt-if-id |   | Integer  |
| vlan_range_num | Interface Vlan Range  |   |
| lo-id |   | Integer  |


## show interface breakout 

### Description 

```
Show information related to dynamic port breakout.


```
### Syntax 

```
show interface breakout { { [ dependencies { port <slotport> } ] } | [ modes ] | { [ port <slotport> ] } } ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| slotport | Front-panel port (slot/port)  |   |


### Usage Guidelines 

```
Use this command to show,
    1. the dependent configurations on a port.
      show interface breakout dependencies port front-panel-port
    2. breakout modes supported by the device.
      show interface breakout modes
    3. the current breakout configuration, member ports and status.
      show interface breakout port front-panel-port
      show interface breakout port


```
### Examples 

```

               sonic# show interface breakout modes
               -------------------------------------------------------------------------
               Port  Interface   Supported Modes                           Default Mode
               -------------------------------------------------------------------------
               1/1   Ethernet0   1x100G, 1x40G, 4x25G, 4x10G               1x100G
               1/3   Ethernet8   1x100G, 1x40G, 4x25G, 4x10G               1x100G
               1/5   Ethernet16  1x100G, 1x40G, 4x25G, 4x10G               1x100G
               1/7   Ethernet24  1x100G, 1x40G, 4x25G, 4x10G               1x100G
               1/9   Ethernet32  1x100G, 1x40G, 4x25G, 4x10G               1x100G
               1/11  Ethernet40  1x100G, 1x40G, 4x25G, 4x10G               1x100G
               1/13  Ethernet48  1x100G, 1x40G, 4x25G, 4x10G               1x100G
               1/15  Ethernet56  1x100G, 1x40G, 4x25G, 4x10G               1x100G
               1/17  Ethernet64  1x100G, 1x40G, 4x25G, 4x10G               1x100G
               1/19  Ethernet72  1x100G, 1x40G, 4x25G, 4x10G               1x100G
               1/21  Ethernet80  1x100G, 1x40G, 4x25G, 4x10G               1x100G
               1/23  Ethernet88  1x100G, 1x40G, 4x25G, 4x10G               1x100G
               1/25  Ethernet96  1x100G, 1x40G, 4x25G, 4x10G               1x100G
               1/27  Ethernet104 1x100G, 1x40G, 4x25G, 4x10G               1x100G
               1/29  Ethernet112 1x100G, 1x40G, 4x25G, 4x10G               1x100G
               1/31  Ethernet120 1x100G, 1x40G, 4x25G, 4x10G               1x100G
               sonic#
               sonic# show interface breakout dependencies port 1/1
               ----------------------------------
               Dependent Configurations
               ----------------------------------
               VLAN|Vlan100
               VLAN_MEMBER|Vlan100|Ethernet2
               sonic#
               sonic# show interface breakout port 1/1
               -----------------------------------------------
               Port  Breakout Mode  Status        Interfaces
               -----------------------------------------------
               1/1   4x25G          Completed     Ethernet0
                                                  Ethernet1
                                                  Ethernet2
                                                  Ethernet3
               sonic#
               sonic# show interface breakout
               -----------------------------------------------
               Port  Breakout Mode  Status        Interfaces
               -----------------------------------------------
               1/1   4x25G          Completed     Ethernet0
                                                  Ethernet1
                                                  Ethernet2
                                                  Ethernet3
               1/3   4x10G          Completed     Ethernet8
                                                  Ethernet9
                                                  Ethernet10
                                                  Ethernet11
               sonic#


```
## show interface-naming 

### Description 

```
Show interface naming mode 


```
### Syntax 

```
show interface-naming

```
## show ip access-group 

### Description 

```
Display IPv4 ACL binding summary


```
### Syntax 

```
show ip access-group

```
### Examples 
#### show ip access-group 

```
Ingress IP access-list ipacl-example on PortChannel1


```
### Alternate command 
#### click 

```
admin@sonic:~$ show acl table [name]


```
## show ip access-lists 

### Description 

```
Show IPv4 ACL rules and statistics


```
### Syntax 

```
show ip access-lists [ <access-list-name> { { [ interface { Ethernet | <PortChannel> | <Vlan> } ] } | [ Switch ] ] } ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| access-list-name | WORD  | String  |
| PortChannel | PortChannelNUM  |   |
| Vlan | VlanNUM  |   |


### Usage Guidelines 

```
ACL name and interface names are optional. If ACL name is not specified then all IPv4 ACLs will be displayed. ACL statistics will be shown only if the ACL is applied globally or to any interface.


```
### Examples 
#### show ip access-lists 

```
ip access-list ipacl-example
    seq 10 permit ip host 10.1.1.1 host 20.1.1.1 (0 packets) [0 bytes]
    seq 20 permit ip host 10.1.1.2 host 20.1.1.2 (0 packets) [0 bytes]
    seq 30 permit ip host 10.1.1.3 host 20.1.1.3 (0 packets) [0 bytes]
    seq 40 permit ip host 10.1.1.4 host 20.1.1.4 (0 packets) [0 bytes]


```
### Alternate command 
#### click 

```
admin@sonic:~$ show acl rule [name] [rule_name]


```
## show ip arp 

### Description 

```
This command displays ARP table entries. To filter the
output, specify an interface, port channel, or VLAN, an IP address, a MAC
address, or a combination of more than one value to match.  You can also
display total number of ARP entries using 'summary' option.


```
### Syntax 

```
show ip arp [ vrf { <vrfname> | mgmt | all } ] { [ <ip-addr> ] | { [ mac-address <mac-addr> ] } | [ summary ] } ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrfname | VRF name (prefixed by Vrf, Max: 15 characters)  | String  |
| ip-addr | A.B.C.D  | String  |
| mac-addr | nn:nn:nn:nn:nn:nn  | String  |


### Usage Guidelines 

```
sonic# show ip arp [interface { Ethernet < port > [summary] | PortChannel < id > [summary] | Vlan < id > [summary] }] [< ipv4-address >] [mac-address < mac >] [summary]


```
### Examples 
#### show ip arp information 

```
sonic# show ip arp
---------------------------------------------------------------------
Address         Hardware address    Interface        Egress Interface
---------------------------------------------------------------------
192.168.1.4     00:01:02:03:44:55   Ethernet8         -
192.168.2.4     00:01:02:03:ab:cd   PortChannel200    -
192.168.3.6     00:01:02:03:04:05   Vlan100           Ethernet4
10.11.48.254    00:01:e8:8b:44:71   eth0              -
10.14.8.102     00:01:e8:8b:44:71   eth0              -


```
#### show ip arp interface information 

```
sonic# show ip arp interface Vlan 100
---------------------------------------------------------------------
Address         Hardware address    Interface        Egress Interface
---------------------------------------------------------------------
192.168.3.6     00:01:02:03:04:05   Vlan100          Ethernet4

sonic# show ip arp interface Management 0
---------------------------------------------------------------------
Address         Hardware address    Interface        Egress Interface
---------------------------------------------------------------------
10.11.48.254    00:01:e8:8b:44:71   eth0              -
10.14.8.102     00:01:e8:8b:44:71   eth0              -


```
#### show ip arp ip information 

```
sonic# show ip arp 192.168.1.4
---------------------------------------------------------------------
Address         Hardware address    Interface        Egress Interface
---------------------------------------------------------------------
192.168.1.4     00:01:02:03:44:55   Ethernet8         -


```
#### show ip arp mac-address information 

```
sonic# show ip arp mac-address 00:01:02:03:ab:cd
---------------------------------------------------------------------
Address         Hardware address    Interface        Egress Interface
---------------------------------------------------------------------
192.168.2.4     00:01:02:03:ab:cd   PortChannel200    -


```
## show ip arp interface 

### Description 

```
ARP entries for this interface 


```
### Syntax 

```
show ip arp interface { { <phy-if-name> [ summary ] } | { Loopback { <lo-id> [ summary ] } } | { Management { <mgmt-if-id> [ summary ] } } | { PortChannel { <lag-id> [ summary ] } } | { Vlan { <vlan-id> [ summary ] } } | { Vxlan { <vxlan-if-name> [ summary ] } } }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| phy-if-name | EthernetNUM  |   |
| lo-id |   | Integer  |
| mgmt-if-id |   | Integer  |
| lag-id |   | Integer  |
| vlan-id |   | Integer  |
| vxlan-if-name | WORD  | String  |


## show ip dhcp-relay 

### Description 

```
Show IP DHCP relay information 


```
### Syntax 

```
show ip dhcp-relay { [ brief ] | { [ detailed [ <ifName1> ] ] } | { [ statistics <ifName> ] } }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| ifName1 | String  | String  |
| ifName | String  | String  |


## show ip forward-protocol 

### Description 

```
Displays IP helper global information.


```
### Syntax 

```
show ip forward-protocol

```
### Usage Guidelines 

```
show ip forward-protocol


```
### Examples 
#### Sample output 

```
sonic# show ip forward-protocol
UDP forwarding  : Enabled
UDP rate limit  : 2000 pps
UDP forwarding enabled on the ports: TFTP, DNS, NTP, TACACS, 12200, 12202
UDP forwarding disabled on the ports: NetBios-Name-Server, NetBios-Datagram-Server
sonic#


```
### Features this CLI belongs to 
*  IP Helper


### Alternate command 
#### click 

```
show ip forward_protocol config


```
## show ip helper-address 

### Description 

```
Displays IP helper server addresses configured on interface.


```
### Syntax 

```
show ip helper-address [ <iface> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| iface | Interface Type - Ranges  |   |


### Usage Guidelines 

```
show ip helper-address [ <interface-name> ]


```
### Examples 
#### Sample output displaying all helper address configuration 

```
sonic# show ip helper-address
Interface    Vrf    Relay Address
-----------  -----  ---------------
  Vlan200             4.4.4.4
  Ethernet0           1.1.1.1
               Vrf1   2.2.2.2
  Vlan100             3.3.3.3
  Ethernet4           5.5.5.5
                      7.7.7.7
sonic#


```
#### Sample output displaying helper address configuration on specific interface 

```
sonic# show ip helper-address Ethernet0
Interface    Vrf    Relay Address
-----------  -----  ---------------
  Ethernet0    Vrf1   2.2.2.2
                      1.1.1.1
sonic#


```
### Features this CLI belongs to 
*  IP Helper


### Alternate command 
#### click 

```
show ip helper_address config


```
## show ip helper-address statistics 

### Description 

```
Displays IP helper packet counters and statistics on interface.


```
### Syntax 

```
show ip helper-address statistics [ <iface> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| iface | Interface Type - Ranges  |   |


### Usage Guidelines 

```
show ip helper-address statistics [ <interface-name> ]


```
### Examples 
#### Sample output displaying all helper statistics 

```
sonic# show ip helper-address statistics
Ethernet0
-----------
  Packets received                         : 0
  Packets relayed                          : 24
  Packets dropped                          : 0
  Invalid TTL packets                      : 0
  All ones broadcast packets received      : 0
  Net directed broadcast packets received  : 0
Vlan100
-----------
  Packets received                         : 45678
  Packets relayed                          : 0
  Packets dropped                          : 0
  Invalid TTL packets                      : 0
  All ones broadcast packets received      : 0
  Net directed broadcast packets received  : 0
Ethernet4
-----------
  Packets received                         : 0
  Packets relayed                          : 0
  Packets dropped                          : 24444
  Invalid TTL packets                      : 0
  All ones broadcast packets received      : 0
  Net directed broadcast packets received  : 0
sonic#


```
#### Sample output displaying helper statistics on specific interface 

```
sonic# show ip helper-address statistics Vlan100
  Packets received                         : 45678
  Packets relayed                          : 0
  Packets dropped                          : 0
  Invalid TTL packets                      : 0
  All ones broadcast packets received      : 0
  Net directed broadcast packets received  : 0
sonic#


```
### Features this CLI belongs to 
*  IP Helper


### Alternate command 
#### click 

```
show ip helper_address statistics


```
## show ip igmp 

### Description 

```
Show IGMP snooping configuration across all the VLANs or specified VLAN Or
Show IGMP snooping groups learnt across all VLANs or specified VLAN


```
### Syntax 

```
show ip igmp snooping { { [ vlan <vlan-id> ] } { [ groups { [ vlan <vlan-id> ] } ] } }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vlan-id |   | Integer  |


### Usage Guidelines 

```
sonic# show ip igmp snooping
sonic# show ip igmp snooping groups


```
### Examples 
#### show IGMP Snooping information  

```
sonic# show ip igmp snooping
Vlan ID: 100
Querier: Disabled
IGMP Operation mode: IGMPv1
Is Fast-Leave Enabled: Disabled
Query interval: 125
Last Member Query Interval: 1000
Max Response time: 10

Vlan ID: 200
Querier: Enabled
IGMP Operation mode: IGMPv2
Is Fast-Leave Enabled: Disabled
Query interval: 125
Last Member Query Interval: 1000
Max Response time: 10

Vlan ID: 300
Querier: Enabled
IGMP Operation mode: IGMPv3
Is Fast-Leave Enabled: Disabled
Query interval: 20
Last Member Query Interval: 1000
Max Response time: 10


sonic# show ip igmp snooping vlan 200
Vlan ID: 200
Querier: Enabled
IGMP Operation mode: IGMPv2
Is Fast-Leave Enabled: Disabled
Query interval: 125
Last Member Query Interval: 1000
Max Response time: 10



```
#### show IGMP Snooping group information  

```
sonic# show ip igmp snooping groups
Vlan ID: 100
-----------
1 (*, 225.1.1.1)
  Outgoing Ports: Ethernet4,PortChannel3
2 (*, 225.1.1.2)
  Outgoing Ports: Ethernet8
Total number of entries: 2

Vlan ID : 300
-------------
1 (100.10.2.3, 226.0.0.1 )
  Outgoing Ports: Ethernet8,Portchannel2
Total number of entries: 1


sonic# show ip igmp snooping groups vlan 100
Vlan ID: 100
-----------
1 (*, 225.1.1.1)
  Outgoing Ports: Ethernet4, PortChannel3
2 (*, 225.1.1.2)
  Outgoing Ports: Ethernet8
Total number of entries: 2



```
## show ip interfaces 

### Description 

```
IP information of interfaces 


```
### Syntax 

```
show ip interfaces

```
## show ip load-share 

### Description 

```
Displays load share information


```
### Syntax 

```
show ip load-share

```
### Usage Guidelines 

```
Use this command to display load share information of ECMP paths on a system


```
### Examples 
#### Display hash param for load balancing 

```
sonic-cli# show ip load-share



```
### Features this CLI belongs to 
*  HASH


## show ip mroute 

### Description 

```
This command displays multicast route information.


```
### Syntax 

```
show ip mroute [ vrf { <vrf-name> | all } ] { { [ <grp-addr> [ <src-addr> ] ] } | [ summary ] } ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrf-name | WORD  | String  |
| grp-addr | A.B.C.D  | String  |
| src-addr | A.B.C.D  | String  |


### Usage Guidelines 

```
* show ip mroute [vrf {<vrf-name> | all}]
* show ip mroute [vrf {<vrf-name> | all}] summary


```
### Examples 
#### show ip mroute [vrf {<vrf-name> | all}] 

```
sonic# show ip mroute

IP multicast routing table for VRF: default
* -> indicates installed route

  Source          Group           Input         Output        Uptime
* 71.0.0.11       233.0.0.1       Vlan100       Vlan200       00:41:59
* 71.0.0.22       233.0.0.1       Vlan100       Vlan200       00:41:54
                                                Vlan201       00:41:59
* 71.0.0.11       234.0.0.1       Vlan100       Vlan200       00:41:34
* 71.0.0.33       234.0.0.1       Vlan100       Vlan200       00:41:31
                                                Vlan201       00:41:44
* 71.0.0.22       235.0.0.1       Vlan100       Vlan200       00:41:16
* 71.0.0.33       235.0.0.1       Vlan100       Vlan200       00:41:14


```
#### show ip mroute [vrf {<vrf-name> | all}] summary 

```
sonic# show ip mroute summary

IP multicast routing table summary for VRF: default
Mroute Type      Installed/Total
(S, G)           7/7


```
## show ip ospf 

### Description 

```
Display show related ospf information for a specific vrf


```
### Syntax 

```
show ip ospf [ vrf { <vrf-name> | all } ] { [ border-routers ] | [ route ] | { [ database { { [ asbr-summary [ <lsid> ] { { [ adv-router <advrouter> ] } | [ self-originate ] ] } ] } | { [ external [ <lsid> ] { { [ adv-router <advrouter> ] } | [ self-originate ] ] } ] } | [ max-age ] | { [ network [ <lsid> ] { { [ adv-router <advrouter> ] } | [ self-originate ] ] } ] } | { [ nssa-external [ <lsid> ] { { [ adv-router <advrouter> ] } | [ self-originate ] ] } ] } | { [ opaque-area [ <lsid> ] { { [ adv-router <advrouter> ] } | [ self-originate ] ] } ] } | { [ opaque-as [ <lsid> ] { { [ adv-router <advrouter> ] } | [ self-originate ] ] } ] } | { [ opaque-link [ <lsid> ] { { [ adv-router <advrouter> ] } | [ self-originate ] ] } ] } | { [ router [ <lsid> ] { { [ adv-router <advrouter> ] } | [ self-originate ] ] } ] } | [ self-originate ] | { [ summary [ <lsid> ] { { [ adv-router <advrouter> ] } | [ self-originate ] ] } ] } ] } ] } | { [ interface [ traffic ] [ <interfacename> ] ] } | { [ neighbor { [ <neighip> ] | [ all ] | [ <interfacename> ] ] } [ detail ] ] } } ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrf-name | WORD  | String  |
| lsid | A.B.C.D  | String  |
| advrouter | A.B.C.D  | String  |
| interfacename | Interface Type - Ranges  |   |
| neighip | A.B.C.D  | String  |


### Usage Guidelines 

```
Use this command to display global, neighbors, interfaces etc information
related to an OSPFv2 router. User can optionally specify VRF
on which the router have to be configured. If VRF name is not specified then
the command is considered for VRF default.
Technical details on OSPFv2 support is also available at
http://docs.frrouting.org/en/latest/


```
### Examples 
#### Display global ospf information for default vrf 

```
device# show ip ospf
 OSPF Routing Process, Router ID: 1.1.1.1
 Supports only single TOS (TOS0) routes
 This implementation conforms to RFC2328
 RFC1583Compatibility flag is enabled
 OpaqueCapability flag is disabled
 Initial SPF scheduling delay 0 millisec(s)
 Minimum hold time between consecutive SPFs 50 millisec(s)
 Maximum hold time between consecutive SPFs 5000 millisec(s)
 Hold time multiplier is currently 1
 time is 92031756
 SPF algorithm last executed 1065d4h22m ago
 Last SPF duration 0.0s
 SPF timer is inactive
 LSA minimum interval 5000 msecs
 LSA minimum arrival 1000 msecs
 Write Multiplier set to 20
 Refresh timer 10 secs
 Number of external LSA 0. Checksum Sum 0x0
 Number of opaque AS LSA 0. Checksum Sum 0x0
 Number of areas attached to this router: 2
 Area ID: 0.0.0.0 (Backbone)
   Number of interfaces in this area: Total: 1 , Active: 1
   Number of fully adjacent neighbors in this area: 1
   Area has no authentication
   SPF algorithm executed 8 times
   Number of LSA 3
   Number of router LSA 2. Checksum Sum 0x40f64b4000000000
   Number of network LSA 1. Checksum Sum 0x40d5adc000000000
   Number of summary LSA 0. Checksum Sum 0x0
   Number of ASBR summary LSA 0. Checksum Sum 0x0
   Number of NSSA LSA 0. Checksum Sum 0x0
   Number of opaque link LSA . Checksum Sum 0x
   Number of opaque area LSA 0. Checksum Sum 0x0
 Area ID: 0.0.0.1
   Number of interfaces in this area: Total: 1 , Active: 1
   Number of fully adjacent neighbors in this area: 0
   Area has no authentication
   SPF algorithm executed 1 times
   Number of LSA 2
   Number of router LSA 0. Checksum Sum 0x0
   Number of network LSA 0. Checksum Sum 0x0
   Number of summary LSA 2. Checksum Sum 0x40f1f61000000000
   Number of ASBR summary LSA 0. Checksum Sum 0x0
   Number of NSSA LSA 0. Checksum Sum 0x0
   Number of opaque link LSA . Checksum Sum 0x
   Number of opaque area LSA 0. Checksum Sum 0x0


```
#### Display neighbor information for ospf on default vrf 

```
sonic# show ip ospf neighbor | no-more

Neighbor ID     Pri State           Dead Time Address         Interface            RXmtL RqstL DBsmL
10.59.142.247     1 Full/Backup       37.343s 64.1.1.2        Ethernet64:64.1.1.1      0     0     0

Leaf1# show ip ospf neighbor Ethernet66 | no-more

Neighbor ID     Pri State           Dead Time Address         Interface            RXmtL RqstL DBsmL
2.2.2.2           1 Full/Backup       38.245s 64.1.1.2        Ethernet66:64.1.1.1      0     0     0



```
#### Display neighbor detailed information for ospf on default vrf 

```
sonic# show ip ospf neighbor detail | no-more
Neighbor 10.59.142.247, interface address 64.1.1.2
    In the area 0.0.0.0 via interface Ethernet64
    Neighbor priority is 1, State is Full, 6 state changes
    Most recent state change statistics:
      Progressive change 7h3m25s ago
    DR is 64.1.1.1, BDR is 64.1.1.2
    Options 2 *|-|-|-|-|-|E|-
    Dead timer due in 30.687s
    Database Summary List 0
    Link State Request List 0
    Link State Retransmission List 0
    Thread Inactivity Timer on
    Thread Database Description Retransmision off
    Thread Link State Request Retransmission on
    Thread Link State Update Retransmission on

Leaf1# show ip ospf neighbor 2.2.2.2 | no-more
Neighbor 2.2.2.2, interface address 64.1.1.2
    In the area 0.0.0.0 via interface Ethernet66
    Neighbor priority is 1, State is Full, 5 state changes
    Most recent state change statistics:
      Progressive change 0h1m11s ago
    DR is 64.1.1.1, BDR is 64.1.1.2
    Options 2 *|-|-|-|-|-|E|-
    Dead timer due in 33.203s
    Database Summary List 0
    Link State Request List 0
    Link State Retransmission List 0
    Thread Inactivity Timer on
    Thread Database Description Retransmision off
    Thread Link State Request Retransmission on
    Thread Link State Update Retransmission on

Neighbor 2.2.2.2, interface address 65.1.1.2
    In the area 0.0.0.1 via interface Ethernet67
    Neighbor priority is 1, State is Full, 5 state changes
    Most recent state change statistics:
      Progressive change 0h1m10s ago
    DR is 65.1.1.1, BDR is 65.1.1.2
    Options 2 *|-|-|-|-|-|E|-
    Dead timer due in 34.590s
    Database Summary List 0
    Link State Request List 0
    Link State Retransmission List 0
    Thread Inactivity Timer on
    Thread Database Description Retransmision off
    Thread Link State Request Retransmission on
    Thread Link State Update Retransmission on



```
#### Display interface information for ospf on default vrf 

```
sonic# show ip ospf interface | no-more
VRF Name: default
Ethernet64 is up
  ifindex 128, MTU 9100 bytes, BW 25000 Mbit UP,BROADCAST,RUNNING,MULTICAST
  Internet Address 64.1.1.1/24, Broadcast 64.1.1.255, Area 0.0.0.0
  MTU mismatch detection: enabled
  Router ID 10.59.143.131, Network Type BROADCAST, Cost: 4
  Transmit Delay is 1 sec, State DR, Priority 1
  Backup Designated Router (ID) 10.59.142.247, Interface Address 64.1.1.2
  Saved Network-LSA sequence number 0x8000000f
  Multicast group memberships: OSPFAllRouters OSPFDesignatedRouters
  Timer intervals configured, Hello 10s, Dead 40s, Wait 40s, Retransmit 5
    Hello due in 9.023s
  Neighbor Count is 1, Adjacent neighbor count is 1

Leaf1# show ip ospf interface Ethernet67 | no-more
VRF Name: default
Ethernet67 is up
  ifindex 926, MTU 9100 bytes, BW 25000 Mbit UP,BROADCAST,RUNNING,MULTICAST
  Internet Address 65.1.1.1/24, Broadcast 65.1.1.255, Area 0.0.0.1
  MTU mismatch detection: enabled
  Router ID 1.1.1.1, Network Type BROADCAST, Cost: 4
  Transmit Delay is 1 sec, State DR, Priority 1
  Backup Designated Router (ID) 2.2.2.2, Interface Address 65.1.1.2
  Multicast group memberships: OSPFAllRouters OSPFDesignatedRouters
  Timer intervals configured, Hello 10s, Dead 40s, Wait 40s, Retransmit 5
    Hello due in 7.957s
  Neighbor Count is 1, Adjacent neighbor count is 1



```
#### Display interface traffic information for ospf on default vrf 

```
sonic# show ip ospf interface traffic | no-more

Interface       HELLO            DB-Desc         LS-Req           LS-Update        LS-Ack
                Rx/Tx            Rx/Tx            Rx/Tx            Rx/Tx            Rx/Tx
--------------------------------------------------------------------------------------------
Ethernet64     2563/2563           3/3             1/1            17/30           29/16

Leaf1# show ip ospf interface traffic Ethernet67 | no-more

Interface       HELLO            DB-Desc         LS-Req           LS-Update        LS-Ack
                Rx/Tx            Rx/Tx            Rx/Tx            Rx/Tx            Rx/Tx
--------------------------------------------------------------------------------------------
Ethernet67       19/22             2/3             1/1             3/3             2/2



```
#### Display database information for ospf on default vrf 

```
sonic# show ip ospf database | no-more
VRF Name: default

       OSPF Router with ID (10.59.143.131)

                Router Link States (Area 0.0.0.0)

Link ID         ADV Router      Age  Seq#       CkSum  Link count
10.59.142.247   10.59.142       1682 0x80000011 0xb56b 1
10.59.143.131   10.59.143       1498 0x80000011 0xdc2c 1

                Net Link States (Area 0.0.0.0)

Link ID         ADV Router      Age  Seq#       CkSum
64.1.1.1        10.59.143.131   1538 0x8000000f 0x1c70


```
#### Display database router information for ospf on default vrf 

```
sonic# show ip ospf database router | no-more
VRF Name: default

       OSPF Router with ID (10.59.143.131)


                Router Link States (Area 0.0.0.0)

  LS age: 1709
  Options: 0x2  : *|-|-|-|-|-|E|-
  LS Flags: 0x6
  Flags: 0x0  :
  LS Type: router-LSA
  Link State ID: 10.59.142.247
  Advertising Router: 10.59.142
  LS Seq Number: 80000011
  Checksum: 0xb56b
  Length: 36

   Number of Links: 1

    Link connected to: a Transit Network
     (Link ID) Designated Router address: 64.1.1.1
     (Link Data) Router Interface address: 64.1.1.2
      Number of TOS metrics: 0
       TOS 0 Metric: 4


  LS age: 1525
  Options: 0x2  : *|-|-|-|-|-|E|-
  LS Flags: 0x3
  Flags: 0x0  :
  LS Type: router-LSA
  Link State ID: 10.59.143.131
  Advertising Router: 10.59.143
  LS Seq Number: 80000011
  Checksum: 0xdc2c
  Length: 36

   Number of Links: 1

    Link connected to: a Transit Network
     (Link ID) Designated Router address: 64.1.1.1
     (Link Data) Router Interface address: 64.1.1.1
      Number of TOS metrics: 0
       TOS 0 Metric: 4


```
#### Display database network information for ospf on default vrf 

```
sonic# show ip ospf database network | no-more
VRF Name: default

       OSPF Router with ID (10.59.143.131)


                Net Link States (Area 0.0.0.0)

  LS age: 1602
  Options: 0x2  : *|-|-|-|-|-|E|-
  LS Flags: 0x3
  LS Type: network-LSA
  Link State ID: 64.1.1.1 (address of Designated Router)
  Advertising Router: 10.59.143.131
  LS Seq Number: 8000000f
  Checksum: 0x1c70
  Length: 32

  Network Mask: /24
        Attached Router: 10.59.142.247

        Attached Router: 10.59.143.131


```
#### Display database summary information for ospf on default vrf 

```
Leaf1# show ip ospf database summary | no-more
VRF Name: default

       OSPF Router with ID (1.1.1.1)


                Summary Link States (Area 0.0.0.0)

  LS age: 468
  Options: 0x2  : *|-|-|-|-|-|E|-
  LS Flags: 0x11
  LS Type: summary-LSA
  Link State ID: 65.1.1.0 (summary Network Number)
  Advertising Router: 1.1.1.1
  LS Seq Number: 80000001
  Checksum: 0x0e04
  Length: 28

  Network Mask: /24
        TOS: 0  Metric: 4


  LS age: 429
  Options: 0x2  : *|-|-|-|-|-|E|-
  LS Flags: 0x6
  LS Type: summary-LSA
  Link State ID: 65.1.1.0 (summary Network Number)
  Advertising Router: 2.2.2.2
  LS Seq Number: 80000002
  Checksum: 0xed1f
  Length: 28

  Network Mask: /24
        TOS: 0  Metric: 4


                Summary Link States (Area 0.0.0.1)

  LS age: 468
  Options: 0x2  : *|-|-|-|-|-|E|-
  LS Flags: 0x11
  LS Type: summary-LSA
  Link State ID: 64.1.1.0 (summary Network Number)
  Advertising Router: 1.1.1.1
  LS Seq Number: 80000001
  Checksum: 0x1bf7
  Length: 28

  Network Mask: /24
        TOS: 0  Metric: 4


  LS age: 429
  Options: 0x2  : *|-|-|-|-|-|E|-
  LS Flags: 0x6
  LS Type: summary-LSA
  Link State ID: 64.1.1.0 (summary Network Number)
  Advertising Router: 2.2.2.2
  LS Seq Number: 80000002
  Checksum: 0xfa13
  Length: 28

  Network Mask: /24
        TOS: 0  Metric: 4


```
#### Display database asbr-summary information for ospf on default vrf 

```
Leaf1# show ip ospf database asbr-summary | no-more
VRF Name: default

       OSPF Router with ID (1.1.1.1)


                ASBR-Summary Link States (Area 0.0.0.0)

  LS age: 38
  Options: 0x2  : *|-|-|-|-|-|E|-
  LS Type: summary-LSA
  Link State ID: 2.2.2.2 (AS Boundary Router address)
  Advertising Router: 1.1.1.1
  LS Seq Number: 80000001
  Checksum: 0x0b41
  Length: 28

  Network Mask: /0
        TOS: 0  Metric: 4


```
#### Display database external information for ospf on default vrf 

```
Leaf1# show ip ospf database external | no-more
VRF Name: default

       OSPF Router with ID (1.1.1.1)

                AS External Link States

  LS age: 52
  Options: 0x2  : *|-|-|-|-|-|E|-
  LS Flags: 0x6
  LS Type: AS-external-LSA
  Link State ID: 25.1.1.1 (External Network Number)
  Advertising Router: 2.2.2.2
  LS Seq Number: 80000001
  Checksum: 0x0892
  Length: 36

  Network Mask: /32
        Metric Type: 2 (Larger than any link state path)
        TOS: 0
        Metric: 20
        Forward Address: 0.0.0.0
        External Route Tag: 0


```
#### Display database max-age LSA information for ospf on default vrf 

```
Leaf1# show ip ospf database max-age
       OSPF Router with ID (1.1.1.1)
                MaxAge Link States:


```
#### Display self originated database information for ospf on default vrf 

```
Leaf1# show ip ospf database self-originate | no-more
VRF Name: default

       OSPF Router with ID (1.1.1.1)

                Router Link States (Area 0.0.0.0)

Link ID         ADV Router      Age  Seq#       CkSum  Link count
1.1.1.1         1.1.1.1          777 0x80000004 0x7b42 1

                Net Link States (Area 0.0.0.0)

Link ID         ADV Router      Age  Seq#       CkSum
64.1.1.1        1.1.1.1          777 0x80000001 0x8581

                Summary Link States (Area 0.0.0.0)

Link ID         ADV Router      Age  Seq#       CkSum  Route
65.1.1.0        1.1.1.1          816 0x80000001 0x0e04 65.1.1.0/24

                ASBR-Summary Link States (Area 0.0.0.0)

Link ID         ADV Router      Age  Seq#       CkSum
2.2.2.2         1.1.1.1          360 0x80000001 0x0b41

                Router Link States (Area 0.0.0.1)

Link ID         ADV Router      Age  Seq#       CkSum  Link count
1.1.1.1         1.1.1.1          776 0x80000004 0x8d2e 1

                Net Link States (Area 0.0.0.1)

Link ID         ADV Router      Age  Seq#       CkSum
65.1.1.1        1.1.1.1          776 0x80000001 0x788d

                Summary Link States (Area 0.0.0.1)

Link ID         ADV Router      Age  Seq#       CkSum  Route
64.1.1.0        1.1.1.1          816 0x80000001 0x1bf7 64.1.1.0/24


```
#### Display database network information with advertising-router option for ospf on default vrf 

```
Leaf1# show ip ospf database network adv-router 1.1.1.1 | no-more
VRF Name: default

       OSPF Router with ID (1.1.1.1)


                Net Link States (Area 0.0.0.0)

  LS age: 886
  Options: 0x2  : *|-|-|-|-|-|E|-
  LS Flags: 0x3
  LS Type: network-LSA
  Link State ID: 64.1.1.1 (address of Designated Router)
  Advertising Router: 1.1.1.1
  LS Seq Number: 80000001
  Checksum: 0x8581
  Length: 32

  Network Mask: /24
        Attached Router: 1.1.1.1

        Attached Router: 2.2.2.2


                Net Link States (Area 0.0.0.1)

  LS age: 886
  Options: 0x2  : *|-|-|-|-|-|E|-
  LS Flags: 0x3
  LS Type: network-LSA
  Link State ID: 65.1.1.1 (address of Designated Router)
  Advertising Router: 1.1.1.1
  LS Seq Number: 80000001
  Checksum: 0x788d
  Length: 32

  Network Mask: /24
        Attached Router: 1.1.1.1

        Attached Router: 2.2.2.2



```
### Features this CLI belongs to 
*  OSPFv2


## show ip ospf route 

### Description 

```
Display ospf route information for a user-vrf


```
### Syntax 

```
show ip ospf route

```
### Usage Guidelines 

```
Use this command to display ospf routes in an OSPFv2 router. User can
optionally specify VRF on which the router have to be configured.
If VRF name is not specified then the command is considered for VRF
default. Technical details on OSPFv2 support is also available at
http://docs.frrouting.org/en/latest/


```
### Examples 
#### Display ospf route information for user vrf 

```
sonic# show ip ospf vrf Vrf1 route | no-more
VRF Name: Vrf1
============ OSPF network routing table ============
N    101.1.1.0/24          [10] area: 0.0.0.0
                           directly attached to Vlan101

============ OSPF router routing table =============

============ OSPF external routing table ===========


```
### Features this CLI belongs to 
*  OSPFv2


## show ip pim 

### Description 

```
This command displays PIM table entries. Available options are:
interface, neighbor, ssm, topology and rpf. Results can also be filtered based on VRF name.


```
### Syntax 

```
show ip pim [ vrf { <vrf-name> | all } ] { { [ interface { [ <port-id> ] | { [ PortChannel <port-id> ] } | { [ Vlan <port-id> ] } ] } ] } | { [ nbr [ <nbr-addr> ] ] } | [ rpf ] | [ ssm ] | { [ topology { [ <grp-addr> [ <src-addr> ] ] } ] } }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrf-name | WORD  | String  |
| port-id | EthernetNUM  |   |
| nbr-addr | A.B.C.D  | String  |
| grp-addr | A.B.C.D  | String  |
| src-addr | A.B.C.D  | String  |


### Usage Guidelines 

```
* show ip pim [vrf <vrf-name>] interface [<intf-name>]
* show ip pim [vrf <vrf-name>] neighbor [[<nbr-ipv4-address>]
* show ip pim [vrf <vrf-name>] ssm
* show ip pim [vrf <vrf-name>] topology [Group-addr | {Group-addr   Source-addr}]
* show ip pim [vrf <vrf-name>] rpf


```
### Examples 
#### show ip pim [vrf {<vrf-name> | all}] interface [<intf-name>] 

```
sonic # show ip pim interface

PIM interface information for VRF: default
Interface       State       Address         PIM Nbrs       PIM DR          Hello-interval       PIM DR-Priority
Vlan100         up          100.0.0.2       1              100.0.0.2       30                   1
Vlan200         up          200.0.0.2       1              200.0.0.3       30                   1

----------------------------------------------------------------------------------------------------------------

sonic # show ip pim interface vlan 100

PIM interface information for VRF: default
Interface       State       Address         PIM Nbrs       PIM DR          Hello-interval       PIM DR-Priority
Vlan100         up          100.0.0.2       1              100.0.0.2       30                   1


```
#### show ip pim [vrf {<vrf-name> | all}] neighbor [<nbr-ipv4-address>] 

```
sonic# show ip pim neighbor

PIM neighbor information for VRF: default
Interface       Neighbor        Uptime         Expirytime       DR-Priority
Vlan100         100.0.0.1       01:38:52       00:01:22         1
Vlan200         200.0.0.3       01:22:33       00:01:13         1

--------------------------------------------------------------------------------

sonic# show ip pim neighbor 100.0.0.1

PIM neighbor information for VRF: default
Interface       Neighbor        Uptime         Expirytime       DR-Priority
Vlan100         100.0.0.1       01:38:52       00:01:22         1


```
#### show ip pim [vrf {<vrf-name> | all}] ssm 

```
If IP-prefix list is associated:
===============================
sonic# show ip pim ssm

PIM SSM information for VRF: default
SSM group range : PIM_PLIST1

-----------------------------------------

If IP-prefix list is not associated:
===================================
sonic# show ip pim ssm

PIM SSM information for VRF: default
SSM group range : 232.0.0.0/8


```
#### show ip pim [vrf {<vrf-name> | all}] topology [Group-addr | {Group-addr Source-addr}] 

```
sonic# show ip pim topology

PIM multicast routing table for VRF: default
(71.0.0.11, 233.0.0.1), uptime 13:08:24, expires 00:00:12
  Incoming interface: vlan100, RPF neighbor 100.0.0.1
  Outgoing interface list:
    vlan200   uptime/expiry-time: 13:07:50/00:01:39
    vlan122   uptime/expiry-time: 12:33:21/Never

(71.0.0.22, 233.0.0.1), uptime 13:08:45, expires 00:00:18
  Incoming interface: vlan100, RPF neighbor 100.0.0.1
  Outgoing interface list:
    vlan200   uptime/expiry-time: 13:22:52/00:01:45
    vlan124   uptime/expiry-time: 12:42:28/Never

(101.0.0.22, 225.1.1.1), uptime 13:07:51, expires 00:06:09
  Incoming interface: vlan105, RPF neighbor 105.0.0.1
  Outgoing interface list:
    vlan200   uptime/expiry-time: 13:03:50/00:01:39
    vlan123   uptime/expiry-time: 13:02:40/Never

--------------------------------------------------------------------------------

sonic# show ip pim topology 233.0.0.1

PIM multicast routing table for VRF: default
(71.0.0.11, 233.0.0.1), uptime 13:08:24, expires 00:00:12
  Incoming interface: vlan100, RPF neighbor 100.0.0.1
  Outgoing interface list:
    vlan200   uptime/expiry-time: 13:07:50/00:01:39
    vlan122   uptime/expiry-time: 12:33:21/Never

(71.0.0.22, 233.0.0.1), uptime 13:08:45, expires 00:00:18
  Incoming interface: vlan100, RPF neighbor 100.0.0.1
  Outgoing interface list:
    vlan200   uptime/expiry-time: 13:22:52/00:01:45
    vlan124   uptime/expiry-time: 12:42:28/Never

--------------------------------------------------------------------------------

sonic# show ip pim topology 225.1.1.1 101.0.0.22

PIM multicast routing table for VRF: default
(101.0.0.22, 225.1.1.1), uptime 13:07:51, expires 00:06:09
  Incoming interface: vlan105, RPF neighbor 105.0.0.1
  Outgoing interface list:
    vlan200   uptime/expiry-time: 13:03:50/00:01:39
    vlan123   uptime/expiry-time: 13:02:40/Never


```
#### show ip pim [vrf {<vrf-name> | all}] rpf 

```
sonic# show ip pim rpf

PIM RPF information for VRF: default
Source          Group           RpfIface       RpfAddress       RibNextHop       Metric       Pref
71.0.0.11       233.0.0.1       Vlan100        100.0.0.1        100.0.0.1        0            1
71.0.0.22       235.0.0.1       Vlan100        100.0.0.1        100.0.0.1        0            1


```
## show ip prefix-list 

### Description 

```
IPv4 prefix list 


```
### Syntax 

```
show ip prefix-list [ <list-name> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| list-name | WORD  | String  |


## show ip route 

### Description 

```
IP route information 


```
### Syntax 

```
show ip route [ vrf <vrfname> ] { [ <address> ] | [ <prefix> ] | [ summary ] | [ bgp ] | [ connected ] | [ static ] | [ ospf ] } ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrfname | WORD  | String  |
| address | A.B.C.D  | String  |
| prefix | A.B.C.D/mask  | String  |


## show ip sla 

### Description 

```
Displays IP SLA information


```
### Syntax 

```
show ip sla [ <id> [ history ] ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| id |   | Integer  |


### Usage Guidelines 

```
Use this command to display IP SLA summary information of all instances on a system,
to display IP SLA detailed information of a particular instance or history information
of a particular instance


```
### Examples 
#### Display IP SLA(Internet protocol service level agreement) information 

```
sonic-cli(config)# show ip sla
           or
sonic-cli(config)# show ip sla 10
           or
sonic-cli(config)# show ip sla 10 history



```
#### Display IP SLA summary information 

```
sonic# show ip sla
SLA#    Type         State Target                    VRF          Transitions  Last change
----    ----         ----- ------------------------  -----------  -----------  -----------
10      ICMP-echo    Up    30.30.1.2                 default      1            00:06:41 ago
20      TCP-connect  Up    40.40.1.2(100)            default      1            00:05:40 ago


```
#### Display IP SLA instance with operation type ICMP detailed information 

```
sonic# show ip sla 10
IP SLA Operation Number: 10
Type of Operation: ICMP-echo
ICMP destination IP address: 30.30.1.2
ICMP source IP address: 30.30.1.1
ICMP source interface:
ICMP request data size: 32
ICMP Time-To-Live(TTL): 0
ICMP Type-of-Service(ToS): 0
Source VRF: default
Operation frequency (sec): 30
Operation timeout (sec): 5
Operation threshold: 3
Operation state: Up
Operation state transitions: 1
Operation last state change: 00:08:39 ago
ICMP Echo Request counter: 107
ICMP Echo Reply counter: 107
ICMP Error counter: 0
ICMP Invalid responses: 0


```
#### Display IP SLA instance with operation type TCP detailed information 

```
sonic# show ip sla 20
Type of Operation: TCP-connect
TCP destination IP address: 40.40.1.2
TCP destination port: 100
TCP source IP address: 40.40.1.1
TCP source port: 200
TCP source interface:
TCP Time-To-Live(TTL): 0
TCP Type-of-Service(ToS): 0
Source VRF: default
Operation frequency (sec): 30
Operation timeout (sec): 3
Operation threshold: 3
Operation state: Up
Operation state transitions: 1
Operation last state change: 00:28:59 ago
TCP connect request counter: 58
TCP connect success counter: 58
TCP connect error counter: 0


```
#### Display IP SLA summary information 

```
sonic# show ip sla 10 history
Timestamp                 Event
---------                 -----
Fri Sep 25 22:54:36 2020  State changed to: Up
Fri Sep 25 22:54:21 2020  Started


```
### Features this CLI belongs to 
*  IPSLA


## show ip static-anycast-gateway 

### Description 

```
Displays IPv4 static-anycast-gateway information.


```
### Syntax 

```
show ip static-anycast-gateway

```
### Examples 
#### Display IPv4 SAG information 

```
sonic# show ip static-anycast-gateway
Configured Anycast Gateway MAC address: 00:22:33:44:55:66
IPv4 Anycast Gateway MAC address: enable
Total number of gateway: 3
Total number of gateway admin UP: 3
Total number of gateway oper UP: 3
Interfaces Gateway Address      Vrf        Admin/Oper
---------- ---------------      ------     ----------
Vlan3      30.30.1.1/24                       up/up
Vlan5      50.0.0.1/24                        up/up
Vlan54     54.1.1.1/24                        up/up


```
## show ip vrf 

### Description 

```
Display configuration information for a specified VRF and its associated interfaces or
all VRF and their associated interfaces.


```
### Syntax 

```
show ip vrf [ <vrf-name> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrf-name | WORD  | String  |


### Usage Guidelines 

```
sonic# show ip vrf


```
### Examples 
#### Display all VRF with their associated interfaces 

```
sonic# show ip vrf
VRF-NAME            INTERFACES
----------------------------------------------------------------
mgmt                eth0
Vrf_red             Ethernet16
                    Ethernet8


```
#### Display a specified VRF with its associated interface 

```
sonic# show ip vrf Vrf_red
VRF-NAME            INTERFACES
----------------------------------------------------------------
Vrf_red             Ethernet16
                    Ethernet8


```
## show ip vrf mgmt 

### Description 

```
Display configuration information for management VRF.


```
### Syntax 

```
show ip vrf mgmt

```
### Usage Guidelines 

```
sonic# show ip vrf mgmt


```
### Examples 
#### Display management VRF 

```
sonic# show ip vrf mgmt
VRF-NAME            INTERFACES
----------------------------------------------------------------
mgmt                eth0


```
## show ipv6 access-group 

### Description 

```
Display IPv6 ACL binding summary


```
### Syntax 

```
show ipv6 access-group

```
### Examples 
#### show ipv6 access-group 

```
Ingress IPV6 access-list ipv6acl-example on Ethernet0


```
### Alternate command 
#### click 

```
admin@sonic:~$ show acl table [name]


```
## show ipv6 access-lists 

### Description 

```
Show IPv6 ACL rules and statistics


```
### Syntax 

```
show ipv6 access-lists [ <access-list-name> { { [ interface { Ethernet | <PortChannel> | <Vlan> } ] } | [ Switch ] ] } ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| access-list-name | WORD  | String  |
| PortChannel | PortChannelNUM  |   |
| Vlan | VlanNUM  |   |


### Usage Guidelines 

```
ACL name and interface names are optional. If ACL name is not specified then all IPv6 ACLs will be displayed. ACL statistics will be shown only if the ACL is applied globally or to any interface.


```
### Examples 
#### show ipv6 access-lists 

```
ipv6 access-list ipv6acl-example
    seq 100 permit ipv6 host abcd::1 host bcde::1 (0 packets) [0 bytes]
    seq 200 permit tcp host abcd::2 host bcde::2 (0 packets) [0 bytes]
    seq 300 permit udp host abcd::3 host bcde::3 (0 packets) [0 bytes]


```
### Alternate command 
#### click 

```
admin@sonic:~$ show acl rule [name] [rule_name]


```
## show ipv6 dhcp-relay 

### Description 

```
Show IPV6 DHCP relay information 


```
### Syntax 

```
show ipv6 dhcp-relay { [ brief ] | { [ detailed [ <ifName1> ] ] } | { [ statistics <ifName> ] } }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| ifName1 | String  | String  |
| ifName | String  | String  |


## show ipv6 interfaces 

### Description 

```
IPv6 Info of Interfaces 


```
### Syntax 

```
show ipv6 interfaces

```
## show ipv6 neighbors 

### Description 

```
This command displays NDP table entries. To filter the
output, specify an interface, port channel, or VLAN, an IPv6 address, a MAC
address, or a combination of more than one value to match.  You can also
display total number of ARP entries using 'summary' option.


```
### Syntax 

```
show ipv6 neighbors [ vrf { <vrfname> | mgmt | all } ] { [ <ip-addr> ] | { [ mac-address <mac-addr> ] } | [ summary ] } ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrfname | VRF name (prefixed by Vrf, Max: 15 characters)  | String  |
| ip-addr | A::B  | String  |
| mac-addr | nn:nn:nn:nn:nn:nn  | String  |


### Usage Guidelines 

```
sonic# show ipv6 neighbors [interface { Ethernet < port > [summary] | PortChannel < id > [summary] | Vlan < id > [summary] }] [< ipv6-address >] [mac-address < mac >] [summary]


```
### Examples 
#### show ipv6 neighbors information 

```
sonic# show ipv6 neighbors
---------------------------------------------------------------------------------
Address                     Hardware address    Interface        Egress Interface
---------------------------------------------------------------------------------
20::1                       00:01:02:03:44:55   Ethernet8         -
20::2                       00:01:02:03:ab:cd   PortChannel200    -
20::3                       00:01:02:03:04:05   Vlan100           Ethernet4
fe80::e6f0:4ff:fe79:34c7    00:01:e8:8b:44:71   eth0              -


```
#### show ipv6 neighbors interface information 

```
sonic# show ipv6 neighbors Vlan 100
---------------------------------------------------------------------------------
Address                     Hardware address    Interface        Egress Interface
---------------------------------------------------------------------------------
20::3                       00:01:02:03:04:05   Vlan100           Ethernet4

sonic# show ipv6 neighbors interface Management 0
---------------------------------------------------------------------------------
Address                     Hardware address    Interface        Egress Interface
---------------------------------------------------------------------------------
fe80::e6f0:4ff:fe79:34c7    00:01:e8:8b:44:71   eth0              -


```
#### show ipv6 neighbors ip information 

```
sonic# show ipv6 neighbors 20::2
---------------------------------------------------------------------------------
Address                     Hardware address    Interface        Egress Interface
---------------------------------------------------------------------------------
20::2                       00:01:02:03:ab:cd   PortChannel200    -


```
#### show ipv6 neighbors mac-address information 

```
sonic# show ipv6 neighbors mac-address 00:01:02:03:04:05
---------------------------------------------------------------------------------
Address                     Hardware address    Interface        Egress Interface
---------------------------------------------------------------------------------
20::3                       00:01:02:03:04:05   Vlan100           Ethernet4


```
## show ipv6 neighbors interface 

### Description 

```
NDP entries for this interface 


```
### Syntax 

```
show ipv6 neighbors interface { { [ <phy-if-name> [ summary ] ] } | { Loopback { <lo-id> [ summary ] } } | { Management { <mgmt-if-id> [ summary ] } } | { PortChannel { <lag-id> [ summary ] } } | { Vlan { <vlan-id> [ summary ] } } | { Vxlan { <vxlan-if-name> [ summary ] } } }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| phy-if-name | EthernetNUM  |   |
| lo-id |   | Integer  |
| mgmt-if-id |   | Integer  |
| lag-id |   | Integer  |
| vlan-id |   | Integer  |
| vxlan-if-name | WORD  | String  |


## show ipv6 prefix-list 

### Description 

```
IPv6 prefix list 


```
### Syntax 

```
show ipv6 prefix-list [ <list-name> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| list-name | WORD  | String  |


## show ipv6 route 

### Description 

```
IP route information 


```
### Syntax 

```
show ipv6 route [ vrf <vrfname> ] { [ <address> ] | [ <prefix> ] | [ summary ] | [ bgp ] | [ connected ] | [ static ] } ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrfname | WORD  | String  |
| address | A::B  | String  |
| prefix | A::B/mask  | String  |


## show ipv6 static-anycast-gateway 

### Description 

```
Displays IPv6 static-anycast-gateway information.


```
### Syntax 

```
show ipv6 static-anycast-gateway

```
### Examples 
#### Display IPv6 SAG information 

```
sonic# show ipv6 static-anycast-gateway
Configured Anycast Gateway MAC address: 00:22:33:44:55:66
IPv6 Anycast Gateway MAC address: enable
Total number of gateway: 2
Total number of gateway admin UP: 2
Total number of gateway oper UP: 2
Interfaces Gateway Address      Vrf        Admin/Oper
---------- ---------------      ------     ----------
Vlan3      30::1/64                           up/up
Vlan5      50::1/64                           up/up


```
## show kdump files 

### Description 

```
Show the kdump kernel core dump files which are stored locally.


```
### Syntax 

```
show kdump files

```
### Usage Guidelines 

```
Use this command to show the kdump kernel core dump files which are stored
locally.


```
### Examples 

```
sonic# show kdump files
Record Key           Filename
-------------------------------------------------------------
     1 202002101809 /var/crash/202002101809/dmesg.202002101809
                    /var/crash/202002101809/kdump.202002101809


```
### Features this CLI belongs to 
*  KDUMP


### Alternate command 
#### click 

```
show kdump files


```
## show kdump log 

### Description 

```
Show a kdump kernel core dump file kernel log from a file stored locally.


```
### Syntax 

```
show kdump log <record> [ <lines> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| record | kdump dump file record number  | Integer  |
| lines |   | Integer  |


### Usage Guidelines 

```
Use this command to show a kdump kernel core dump file kernel log from a file
stored locally. The mandatory parameter is the number of the kernel core dump
files which are stored locally. The optional parameter is the number of lines
displayed (20 is the default number of lines displayed).


```
### Examples 

```
sonic# show kdump log 1 5
File: /var/crash/202002101809/dmesg.202002101809
[326785.222049]  [<ffffffffa0c0484e>] ? entry_SYSCALL_64_after_swapgs+0x58/0xc6
[326785.229926] Code: 41 5c 41 5d 41 5e 41 5f e9 6c 2f cf ff 66 2e 0f 1f 84 00 00 00 00 00 66 90 0f 1f 44 00 00 c7 05 29 28 a8 00 01 00 00 00 0f ae f8 <c6> 04 25 00 00 00 00 01 c3 0f 1f 44 00 00 0f 1f 44 00 00 53 8d
[326785.251451] RIP  [<ffffffffa0a2a562>] sysrq_handle_crash+0x12/0x20
[326785.258463]  RSP <ffffafd2c6523e78>
[326785.262453] CR2: 0000000000000000

In this example, we show the kernel log for the first kernel core dump file
stored locally. We display only the first 5 lines of the log.


```
### Features this CLI belongs to 
*  KDUMP


### Alternate command 
#### click 

```
show kdump log


```
## show kdump memory 

### Description 

```
Show the amount of memory reserved for kdump operation.


```
### Syntax 

```
show kdump memory

```
### Usage Guidelines 

```
Use this command to show the amount of memory reserved for kdump operation.


```
### Examples 

```
sonic# show kdump memory
Memory Reserved: 0M-2G:256M,2G-4G:320M,4G-8G:384M,8G-:448M


```
### Features this CLI belongs to 
*  KDUMP


### Alternate command 
#### click 

```
show kdump memory


```
## show kdump num-dumps 

### Description 

```
Show the maximum number of kernel core dump files which can be stored locally.


```
### Syntax 

```
show kdump num-dumps

```
### Usage Guidelines 

```
Use this command to show the maximum number of kernel core dump files which
can be stored locally.


```
### Examples 

```
sonic# show kdump num-dumps
Maximum number of Kernel Core files Stored: 3


```
### Features this CLI belongs to 
*  KDUMP


### Alternate command 
#### click 

```
show kdump num_dumps


```
## show kdump status 

### Description 

```
Show the status of kdump operation.


```
### Syntax 

```
show kdump status

```
### Usage Guidelines 

```
Use this command to show the status of kdump operation.


```
### Examples 

```
sonic# show kdump status
Kdump Administrative Mode:  Enabled
Kdump Operational State:    Ready
Memory Reserved: 512M
Maximum number of Kernel Core files Stored: 3
Record Key           Filename
-------------------------------------------------------------
     1 202002101809 /var/crash/202002101809/dmesg.202002101809
                    /var/crash/202002101809/kdump.202002101809


```
### Features this CLI belongs to 
*  KDUMP


### Alternate command 
#### click 

```
show kdump status


```
## show ldap-server 

### Description 

```
Show LDAP information

```
### Syntax 

```
show ldap-server

```
### Examples 

```
sonic-cli# show ldap-server
---------------------------------------------------------
LDAP Global Configuration
---------------------------------------------------------
binddn               : dc=sji,dc=example,dc=com
---------------------------------------------------------
LDAP NSS Configuration
---------------------------------------------------------
ssl                  : start_tls
--------------------------------------------------------------------------------
HOST            USE-TYPE  PORT      PRIORITY SSL       RETRY
--------------------------------------------------------------------------------
1.1.1.1         NSS       -         1        START_TLS 2
sonic-cli#


```
## show link state tracking 

### Description 

```
Show the link state tracking group operational state information.


```
### Syntax 

```
show link state tracking [ <grp-name> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| grp-name | name  | String  |


### Usage Guidelines 

```
Link state tracking group name can be of maximum 63 characters. The name must begin with A-Z, a-z or 0-9. Underscore and hypens can be used except as the first character.
If the group name is not specified then a summary of all configured groups will be displayed.


```
### Examples 
#### Display link state tracking group summary/details 

```
sonic# show link state tracking FooBar
Name: FooBar
Description: Example description
Timeout: 120 seconds
Upstream Interfaces:
    Ethernet0 (Up)
    Ethernet4 (Up)
    Vlan100 (Up)
Downstream Interfaces:
    PortChannel1 (Up)
    PortChannel2 (Up)
    Ethernet4 (Up)


```
### Alternate command 
#### click 

```
admin@sonic:~$ show linktrack group <name>


```
## show lldp neighbor 

### Description 

```
Shows LLPD neigbhor information in detail


```
### Syntax 

```
show lldp neighbor [ <ifname> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| ifname | EthernetNUM  |   |


### Usage Guidelines 

```
This command is useful to view the LLDP neighbor information in detail


```
### Examples 
#### Shows LLDP neigbhor information in detail 

```
sonic-cli# show lldp neighbor
-----------------------------------------------------------
LLDP Neighbors
-----------------------------------------------------------
Interface:   Ethernet64,via: LLDP
Chassis:
    ChassisID:    80:a2:35:26:48:5e
    SysName:      Leaf9
    SysDescr:     Debian GNU/Linux 9 (stretch) Linux 4.9.0-11-2-amd64 #1 SMP Debian 4.9.189-3+deb9u2 (2019-11-11) x86_64
    MgmtIP:       10.59.132.165
    MgmtIP:       10.59.132.165
    Capability:   MAC_BRIDGE, ON
    Capability:   ROUTER, ON
Port
    PortID:       hundredGigE53
    PortDescr:    Ethernet64
-----------------------------------------------------------


```
## show lldp table 

### Description 

```
Shows LLPD neigbhor information in brief


```
### Syntax 

```
show lldp table

```
### Usage Guidelines 

```
This command is useful to view the LLDP neighbor information in brief


```
### Examples 
#### Shows LLDP neigbhor information in brief 

```
sonic-cli# show lldp table
------------------------------------------------------------------------------------------------------
LocalPort           RemoteDevice        RemotePortID        Capability           RemotePortDescr
-------------------------------------------------------------------------------------------------------
Ethernet64          Leaf9               hundredGigE53       BR                  Ethernet64


```
## show logging 

### Description 

```
Displays logging information 


```
### Syntax 

```
show logging

```
## show logging count 

### Description 

```
total number of logging 


```
### Syntax 

```
show logging count

```
## show logging lines 

### Description 

```
Output of last NUM lines 


```
### Syntax 

```
show logging lines [ <lines> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| lines | port  | Integer  |


## show logging servers 

### Description 

```
Shows list of remote syslog servers are configured

```
### Syntax 

```
show logging servers

```
### Examples 

```
sonic# show logging servers
--------------------------------------------------------------------------------
HOST            PORT      SOURCE-INTERFACE       VRF
--------------------------------------------------------------------------------
30.1.1.1        514       -                      Vrf2
40.1.1.1        514       -                      -
sonic#



```
## show mac access-group 

### Description 

```
Display MAC ACL binding summary


```
### Syntax 

```
show mac access-group

```
### Examples 
#### show mac access-group 

```
Ingress MAC access-list macacl-example on Vlan100


```
### Alternate command 
#### click 

```
admin@sonic:~$ show acl table [name]


```
## show mac access-lists 

### Description 

```
Show MAC ACL rules and statistics


```
### Syntax 

```
show mac access-lists [ <access-list-name> { { [ interface { Ethernet | <PortChannel> | <Vlan> } ] } | [ Switch ] ] } ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| access-list-name | WORD  | String  |
| PortChannel | PortChannelNUM  |   |
| Vlan | VlanNUM  |   |


### Usage Guidelines 

```
ACL name and interface names are optional. If ACL name is not specified then all MAC ACLs will be displayed. ACL statistics will be shown only if the ACL is applied globally or to any interface.


```
### Examples 
#### show mac access-lists 

```
mac access-list macacl-example
    seq 10 permit host 00:00:10:00:00:01 host 00:00:20:00:00:01 (10 packets) [1000 bytes]
    seq 20 permit host 00:00:10:00:00:02 host 00:00:20:00:00:02 (20 packets) [2000 bytes]
    seq 30 permit host 00:00:10:00:00:03 host 00:00:20:00:00:03 (30 packets) [3000 bytes]
    seq 40 permit host 00:00:10:00:00:04 host 00:00:20:00:00:04 (40 packets) [4000 bytes]


```
### Alternate command 
#### click 

```
admin@sonic:~$ show acl rule [name] [rule_name]


```
## show mac address-table 

### Description 

```
MAC address-table 


```
### Syntax 

```
show mac address-table

```
## show mac address-table Vlan 

### Description 

```
Show MAC address-table for VLAN 


```
### Syntax 

```
show mac address-table Vlan <vlan-id>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vlan-id |   | Integer  |


## show mac address-table address 

### Description 

```
Show MAC address-table address for MAC address 


```
### Syntax 

```
show mac address-table address <mac-addr>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| mac-addr | nn:nn:nn:nn:nn:nn  | String  |


## show mac address-table aging-time 

### Description 

```
MAC aging-time 


```
### Syntax 

```
show mac address-table aging-time

```
## show mac address-table count 

### Description 

```
Count keyword 


```
### Syntax 

```
show mac address-table count

```
## show mac address-table dynamic 

### Description 

```
MAC address-table for dynamic commands 


```
### Syntax 

```
show mac address-table dynamic { { [ address <mac-addr> ] } | [ Vlan ] | { [ interface { <phy-if-name> | <PortChannel> } ] } } ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| mac-addr | nn:nn:nn:nn:nn:nn  | String  |
| phy-if-name | EthernetNUM  |   |
| PortChannel | PortChannelNUM  |   |


## show mac address-table interface 

### Description 

```
Show MAC address-table for interfaces 


```
### Syntax 

```
show mac address-table interface { <phy-if-name> | <PortChannel> }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| phy-if-name | EthernetNUM  |   |
| PortChannel | PortChannelNUM  |   |


## show mac address-table static 

### Description 

```
MAC address-table for static commands 


```
### Syntax 

```
show mac address-table static { { [ address <mac-addr> ] } | [ Vlan ] | { [ interface { <phy-if-name> | <PortChannel> } ] } } ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| mac-addr | nn:nn:nn:nn:nn:nn  | String  |
| phy-if-name | EthernetNUM  |   |
| PortChannel | PortChannelNUM  |   |


## show mac dampening 

### Description 

```
Show MAC dampening configuration 


```
### Syntax 

```
show mac dampening

```
## show mac dampening-disabled-ports 

### Description 

```
Show MAC dampening-disabled-ports 


```
### Syntax 

```
show mac dampening-disabled-ports

```
## show mclag brief 

### Description 

```
Show command to display MCLAG domain summary


```
### Syntax 

```
show mclag brief

```
### Usage Guidelines 

```
sonic-cli# show mclag brief


```
### Examples 
#### show mclag brief 

```
sonic-cli# show mclag brief

Domain ID            : 10
Role                 :
Session Status       :
Peer Link Status     :
Source Address       : 1.1.1.1
Peer Address         :
Peer Link            : Eth1/12/1
Keepalive Interval   : 1 secs
Session Timeout      : 30 secs
Delay Restore        : 300 secs
System Mac           :
Mclag System Mac     : 00:bb:bb:bb:cc:cc

Number of MLAG Interfaces:1
-----------------------------------------------------------
MLAG Interface       Local/Remote Status
-----------------------------------------------------------
PortChannel10            down/down


```
## show mclag interface 

### Description 

```
Show command to display MCLAG interface inforamation


```
### Syntax 

```
show mclag interface <ifid> <domain_id>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| ifid |   | Integer  |
| domain_id |   | Integer  |


### Usage Guidelines 

```
sonic-cli# show mclag interface PortchannelId DomainId


```
### Examples 
#### show mclag 10 100 

```
sonic-cli# show mclag 10 100
Local/Remote Status  : down/down
TrafficDisable       : No
IsolateWithPeerLink  : No


```
## show mclag separate-ip-interfaces 

### Description 

```
Show command to display Vlan interfaces on which MCLAG separate IP is configured.


```
### Syntax 

```
show mclag separate-ip-interfaces

```
### Usage Guidelines 

```
sonic-cli# show mclag separate-ip-interfaces


```
### Examples 
#### show mclag separate-ip-interfaces 

```
sonic-cli# show mclag separate-ip-interfaces

Interface Name
===================
Vlan10
===================
Total count : 1
===================


```
## show mirror-session 

### Description 

```
Display configured mirror sessions


```
### Syntax 

```
show mirror-session [ <session-name> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| session-name | String(Max: 20 characters)  | String  |


### Examples 

```
sonic# show mirror-session
ERSPAN Sessions
---------------------------------------------------------------------------------------------------------------
Name      Status    SRC-IP          DST-IP          GRE    DSCP  TTL   Queue   Policer   SRC-Port        Direction
---------------------------------------------------------------------------------------------------------------
Mirror2   active    11.1.1.1        10.1.1.1        0x88ee 10    10    10                Ethernet4       rx
SPAN Sessions
---------------------------------------------------------------
Name      Status    DST-Port        SRC-Port        Direction
---------------------------------------------------------------
Mirror1   active    Ethernet0       Ethernet4       rx


```
## show nat 

### Description 

```
Show NAT info 


```
### Syntax 

```
show nat { { translations [ count ] } | statistics | { config { [ static ] | [ pool ] | [ bindings ] | [ globalvalues ] | [ zones ] ] } } }

```
## show neighbor-suppress-status 

### Description 

```
Show arp and nd suppression status 


```
### Syntax 

```
show neighbor-suppress-status [ <id> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| id |   | Integer  |


## show ntp associations 

### Description 

```
Display associated NTP servers and their properties.


```
### Syntax 

```
show ntp associations

```
### Usage Guidelines 

```
sonic# show ntp associations


```
### Examples 
#### Display associated NTP servers and their properties 

```
sonic# show ntp associations
remote                       refid            st   t  when   poll   reach  delay offset       jitter
------------------------------------------------------------------------------------------------------
*10.11.0.1                   10.14.1.1        4    u  44     64     255    0.258 17915.100    151.732
+10.11.0.2                   10.11.0.1        5    u  48     64     255    0.306 17774.800    86.586
------------------------------------------------------------------------------------------------------
* master (synced), # master (unsynced), + selected, - candidate, ~ configured


```
## show ntp global 

### Description 

```
Display global configuration for NTP, which includes VRF configuration and
source interface configuration.


```
### Syntax 

```
show ntp global

```
### Usage Guidelines 

```
sonic# show ntp global


```
### Examples 
#### Display NTP global configuraion 

```
sonic# show ntp global
----------------------------------------------
NTP Global Configuration
----------------------------------------------
NTP source-interface:   Ethernet8
NTP vrf:                default


```
## show ntp server 

### Description 

```
Display configured NTP servers.


```
### Syntax 

```
show ntp server

```
### Usage Guidelines 

```
sonic# show ntp server


```
### Examples 
#### Display configured NTP servers 

```
sonic# show ntp server
--------------------------------
NTP Servers
--------------------------------
10.11.0.1
10.11.0.2


```
## show platform environment 

### Description 

```
Show platform Environment 


```
### Syntax 

```
show platform environment

```
## show platform fanstatus 

### Description 

```
Show platform fan status 


```
### Syntax 

```
show platform fanstatus

```
## show platform psustatus 

### Description 

```
Show platform PSU status 


```
### Syntax 

```
show platform psustatus

```
## show platform psusummary 

### Description 

```
Show platform PSU summary 


```
### Syntax 

```
show platform psusummary

```
## show platform syseeprom 

### Description 

```
Show platform EEPROM information 


```
### Syntax 

```
show platform syseeprom

```
## show platform temperature 

### Description 

```
Show platform temperature sensors 


```
### Syntax 

```
show platform temperature

```
## show platform temperature detail 

### Description 

```
Show detailed temperature sensors information 


```
### Syntax 

```
show platform temperature detail

```
## show policy-map 

### Description 

```
Shows flow based services policy-map related information


```
### Syntax 

```
show policy-map { [ <show-fbs-policy-name> ] | [ type ] } ] { qos | monitoring | forwarding | copp }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| show-fbs-policy-name | WORD  | String  |


### Usage Guidelines 

```
Policy-map type and policy-map name arguments are optional. If type argument or policy-map name not provided command will show all policy-map information. Else it show corresponding policy-map information of given type or given name


```
### Examples 
#### Displays policy-map information 

```
Policy policy_mirror Type monitoring
  Description:
  Flow class1 at priority 10
    Description:
    set mirror-session mirror1
  Applied to:
    Vlan100 at Ingress

Policy policy_qos Type qos
  Description:
  Flow class_permit_ipv6 at priority 10
    Description:
    police cir 300000000 cbs 300000000 pir 300000000 pbs 300000000
  Flow class_permit_ip at priority 10
    Description:
    police cir 300000000 cbs 300000000 pir 300000000 pbs 300000000
  Applied to:
    Vlan100 at Egress

Policy policy_vrf Type forwarding
  Description:
  Flow class_permit_ipv6 at priority 10
    Description:
    set ipv6 nexthop 1211::2 priority 20
    set ipv6 nexthop 1212::2 vrf Vrf-BLUE priority 30
  Flow class_permit_ip at priority 10
    Description:
    set ip nexthop 12.12.1.2 vrf default priority 30
    set ip nexthop 12.12.2.2 vrf Vrf-BLUE priority 20
    set ip nexthop 12.12.1.2 priority 10
  Applied to:
    Vlan100 at Ingress
    Switch at Ingress


```
## show port-group 

### Description 

```
Display the list of port groups, it's member ports and valid speeds.
The port-group is not supported in all the hardware plaforms.


```
### Syntax 

```
show port-group

```
### Usage Guidelines 

```
Use this command to know port group members, valid speeds and default speed.
  show port-group


```
### Examples 

```
show sonic# show port-group
-----------------------------------------------------------------------
Port-group  Interface range            Valid speeds      Default Speed
-----------------------------------------------------------------------
1           Ethernet0 - Ethernet11     10G, 25G          25G
2           Ethernet12 - Ethernet23    10G, 25G          25G
3           Ethernet24 - Ethernet35    10G, 25G          25G
4           Ethernet36 - Ethernet47    10G, 25G          25G
sonic#



```
## show priority-flow-control 

### Description 

```
Show PFC summary 


```
### Syntax 

```
show priority-flow-control watchdog

```
## show priority-group 

### Description 

```
This command displays priroity group shared/headroom watermarks and persistent-watermarks.


```
### Syntax 

```
show priority-group { { watermark { { headroom { [ interface { <phy-intf-name> } ] } } | { shared { [ interface { <phy-intf-name> } ] } } | { percentage { { headroom { [ interface { <phy-intf-name> } ] } } | { shared { [ interface { <phy-intf-name> } ] } } } } } } | { persistent-watermark { { headroom { [ interface { <phy-intf-name> } ] } } | { shared { [ interface { <phy-intf-name> } ] } } | { percentage { { headroom { [ interface { <phy-intf-name> } ] } } | { shared { [ interface { <phy-intf-name> } ] } } } } } } }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| phy-intf-name | EthernetNUM  |   |


### Usage Guidelines 

```
Use this command to display priority group watermarks, persistent-watermarks etc.
There are various CLI options available to display infomration for shared, headroom and interface watermarks.
- show priority-group (watermark|persitent-watermark) (shared | headroom ) (interface Ethernet [ifname]))
  This command will display priority groups shared/headroom watermars.


```
### Examples 
#### Below are some of the sample outputs of show priority-group command 

```
sonic# show priority-group watermark shared
Ingress shared pool watermark per PG:
-------------------------------------------------------------------
Port        PG0  PG1  PG2  PG3  PG4  PG5  PG6  PG7
-------------------------------------------------------------------
Ethernet0   0    0    0    0    0    0    0    0
Ethernet4   0    0    0    0    0    0    0    0
Ethernet8   0    0    0    0    0    0    0    0
Ethernet12  0    0    0    0    0    0    0    0
Ethernet16  0    0    0    0    0    0    0    0

sonic# show priority-group persistent-watermark headroom
Ingress headroom persistent watermark per PG:
-------------------------------------------------------------------
Port        PG0  PG1  PG2  PG3  PG4  PG5  PG6  PG7
-------------------------------------------------------------------
Ethernet0   0    0    0    0    0    0    0    0
Ethernet4   0    0    0    0    0    0    0    0
Ethernet8   0    0    0    0    0    0    0    0
Ethernet12  0    0    0    0    0    0    0    0
Ethernet16  0    0    0    0    0    0    0    0
Ethernet20  0    0    0    0    0    0    0    0
Ethernet24  0    0    0    0    0    0    0    0


```
## show ptp 

### Description 

```
Display PTP status/configuration


```
### Syntax 

```
show ptp

```
### Examples 

```
sonic# show ptp
-----------------------------------------------------------
Interface            State
-----------------------------------------------------------
Ethernet56           master
Ethernet64           slave


```
## show ptp clock 

### Description 

```
Display PTP clock configuration/status


```
### Syntax 

```
show ptp clock

```
### Examples 

```
sonic# show ptp clock
Mode                  BC
Domain Profile        ieee1588
Network Transport     UDPv4 unicast
Domain Number         1
Clock Identity        3c2c99.fffe.2d7c35
Priority1             128
Priority2             128
Two Step              Enabled
Slave Only            False
Number Ports          2
Clock Quality:
Clock Class         248
Clock Accuracy      254
Ofst Scaled Log Var 65535
Mean Path Delay       0
Steps Removed         0
Offset from master    0


```
## show ptp parent 

### Description 

```
Display PTP parent status


```
### Syntax 

```
show ptp parent

```
### Examples 

```
sonic# show ptp parent
Parent Clock Identity          3c2c99.fffe.2d7c35
Port Number                    0
Grandmaster Clock Class        248
Grandmaster Off Scaled Log Var 65535
Grandmaster Clock Accuracy     254
Grandmaster Identity           3c2c99.fffe.2d7c35
Grandmaster Priority1          128
Grandmaster Priority2          128
Stats Valid                    False
Observed Off Scaled Log Var    65535
Observed Clock Phase Chg Rate  2147483647


```
## show ptp port 

### Description 

```
Display PTP port status/configuration


```
### Syntax 

```
show ptp port <Ethernet>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| Ethernet | EthernetNUM  |   |


### Examples 

```
sonic# show ptp port Ethernet 64
Port Number                    64
Port State                     master
Log Min delay Req Intvl        0
Peer Mean Path Delay           0
Log Announce Interval          1
Log Sync Interval              0
Log Min PDelay Req Interval    0
Version Number                 2
Unicast Master Table:
                               10.1.1.1


```
## show ptp time-property 

### Description 

```
Display PTP time-property status


```
### Syntax 

```
show ptp time-property

```
### Examples 

```
sonic# show ptp time-property
Curr UTC Offset Vld  False
Curr UTC Offset      37
Leap59               False
Leap61               False
Time Traceable       False
Freq Traceable       False
PTP Timescale        True


```
## show qos 

### Description 

```
Show QoS information 


```
### Syntax 

```
show qos interface { CPU | { <phy-intf-name> { [ queue <queue-id> ] } { [ priority-flow-control { statistics [ queue ] } ] } } | <po-intf-name> | <vlan-intf-name> }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| phy-intf-name | EthernetNUM  |   |
| queue-id |   | Integer  |
| po-intf-name | PortChannelNUM  |   |
| vlan-intf-name | VlanNUM  |   |


## show qos map dot1p-tc 

### Description 

```
Show configured dot1p-tc-map 


```
### Syntax 

```
show qos map dot1p-tc [ <name> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| name | WORD  | String  |


## show qos map dscp-tc 

### Description 

```
Show configured dscp-tc-map 


```
### Syntax 

```
show qos map dscp-tc [ <name> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| name | WORD  | String  |


## show qos map pfc-priority-queue 

### Description 

```
Show configured pfc-priority-queue-map 


```
### Syntax 

```
show qos map pfc-priority-queue [ <name> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| name | WORD  | String  |


## show qos map tc-dot1p 

### Description 

```
Show configured tc-dot1p-map 


```
### Syntax 

```
show qos map tc-dot1p [ <name> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| name | WORD  | String  |


## show qos map tc-dscp 

### Description 

```
Show configured tc-dscp-map 


```
### Syntax 

```
show qos map tc-dscp [ <name> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| name | WORD  | String  |


## show qos map tc-pg 

### Description 

```
Show configured traffic-class-priority-group-map 


```
### Syntax 

```
show qos map tc-pg [ <name> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| name | WORD  | String  |


## show qos map tc-queue 

### Description 

```
Show configured traffic-class-queue-map 


```
### Syntax 

```
show qos map tc-queue [ <name> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| name | WORD  | String  |


## show qos scheduler-policy 

### Description 

```
Show scheduler policy information 


```
### Syntax 

```
show qos scheduler-policy [ <name> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| name | WORD  | String  |


## show qos wred-policy 

### Description 

```
This command displays WRED policies configured on the device.


```
### Syntax 

```
show qos wred-policy [ <name> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| name | WORD  | String  |


### Usage Guidelines 

```
User configures WRED policies to use it in interface queues.
This command enables users to display the WRED policies configured on this device.
User can provide WRED profile name optional CLI key to display only that profile.
If profile name is not specified, all policies will be displayed by this command.


```
### Examples 
#### The following command displays all WRED policies            configured in device 

```
sonic# show qos wred-policy
---------------------------------------------------
Profile                : test
---------------------------------------------------
Profile                : wred-green
ecn                    : ecn_all
green-min-threshold    : 100  KBytes
green-max-threshold    : 200  KBytes
greep-drop-rate        : 50


```
## show queue 

### Description 

```
This command displays queue counters, watermarks, persistent-watermarks and breaches etc.


```
### Syntax 

```
show queue { { counters { [ interface { { <phy-intf-name> { [ queue <queue-id> ] } } | { CPU { [ queue <queue-id> ] } } } ] } } | { watermark { { unicast { [ interface { <phy-intf-name> } ] } } | { multicast { [ interface { <phy-intf-name> } ] } } | CPU | { percentage { { unicast { [ interface { <phy-intf-name> } ] } } | { multicast { [ interface { <phy-intf-name> } ] } } | CPU } } } } | { persistent-watermark { { unicast { [ interface { <phy-intf-name> } ] } } | { multicast { [ interface { <phy-intf-name> } ] } } | CPU | { percentage { { unicast { [ interface { <phy-intf-name> } ] } } | { multicast { [ interface { <phy-intf-name> } ] } } | CPU } } } } }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| phy-intf-name | EthernetNUM  |   |
| queue-id |   | Integer  |


### Usage Guidelines 

```
Use this command to display queue counters, watermarks, persistent-watermarks and breches etc.
There are various CLI options available to display infomration for queue, interface and all interfaces.
- show queue counters (interface Ethernet|CPU [ifname] (queue [id]))
  This command will display all queue counters
- show queue (watermark|persitent-watermark) (unicast| multicast|CPU) (interface Ethernet [ifname]))
  This command will display all queue watermarks


```
### Examples 
#### Below are some of the sample outputs of show queue command 

```
sonic# show queue counters
-------------------------------------------------------------------
Port        TxQ  Counter/pkts   Counter/bytes  Drop/pkts   Drop/bytes
-------------------------------------------------------------------
Ethernet0   UC0  0              0              0           0
Ethernet0   UC1  0              0              0           0
Ethernet0   UC2  0              0              0           0
Ethernet0   UC3  0              0              0           0
Ethernet0   UC4  0              0              0           0
Ethernet0   UC5  0              0              0           0
Ethernet0   UC6  0              0              0           0
Ethernet0   UC7  0              0              0           0
Ethernet0   MC8  0              0              0           0
Ethernet0   MC9  0              0              0           0
Ethernet0   MC10 0              0              0           0
Ethernet0   MC11 0              0              0           0
Ethernet0   MC12 0              0              0           0
Ethernet0   MC13 0              0              0           0
Ethernet0   MC14 0              0              0           0
Ethernet0   MC15 0              0              0           0

sonic# show queue watermark unicast
Egress queue watermark per unicast queue:
-------------------------------------------------------------------
Port        UC0  UC1  UC2  UC3  UC4  UC5  UC6  UC7
-------------------------------------------------------------------
Ethernet0   0    0    0    0    0    0    0    0
Ethernet4   0    0    0    0    0    0    0    0
Ethernet8   0    0    0    0    0    0    0    0
Ethernet12  0    0    0    0    0    0    0    0
Ethernet16  0    0    0    0    0    0    0    0
Ethernet20  0    0    0    0    0    0    0    0
Ethernet24  0    0    0    0    0    0    0    0

sonic# show queue persistent-watermark multicast
Egress queue persistent watermark per multicast queue:
-------------------------------------------------------------------
Port        MC8  MC9  MC10 MC11 MC12 MC13 MC14 MC15
-------------------------------------------------------------------
Ethernet0   0    0    0    0    0    0    0    0
Ethernet4   0    0    0    0    0    0    0    0
Ethernet8   0    0    0    0    0    0    0    0
Ethernet12  0    0    0    0    0    0    0    0
Ethernet16  0    0    0    0    0    0    0    0
Ethernet20  0    0    0    0    0    0    0    0
Ethernet24  0    0    0    0    0    0    0    0
Ethernet28  0    0    0    0    0    0    0    0



```
## show radius-server 

### Description 

```
Show RADIUS information 


```
### Syntax 

```
show radius-server

```
## show reboot-cause 

### Description 

```
Show cause of most recent reboot 


```
### Syntax 

```
show reboot-cause

```
## show route-map 

### Description 

```
Display route map information 


```
### Syntax 

```
show route-map [ <rt-map-name> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| rt-map-name | WORD  | String  |


## show running-configuration 

### Description 

```
Current operating configuration 


```
### Syntax 

```
show running-configuration

```
## show running-configuration bfd 

### Description 

```
Displays Bidirectional Forwarding detection(BFD) configurations.


```
### Syntax 

```
show running-configuration bfd

```
### Examples 
#### Display BFD configurations 

```
device# show running-configuration bfd
!
bfd
 peer 192.168.2.1 interface Ethernet0
  detect-multiplier 5
  echo-interval 200
  echo-mode
  receive-interval 200
  transmit-interval 200
 !
 peer 192.168.2.1 multihop local-address 192.168.2.2
  detect-multiplier 4
  receive-interval 150
  transmit-interval 150
 !


```
## show running-configuration bgp 

### Description 

```
Display current BGP configurations 


```
### Syntax 

```
show running-configuration bgp [ vrf <vrf-name-opt> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrf-name-opt | WORD  | String  |


## show running-configuration bgp as-path-acess-list 

### Description 

```
Show current BGP AS path acess list configuration 


```
### Syntax 

```
show running-configuration bgp as-path-acess-list [ <aspath-list-name> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| aspath-list-name | WORD  | String  |


## show running-configuration bgp community-list 

### Description 

```
Show current BGP community-list configuration 


```
### Syntax 

```
show running-configuration bgp community-list [ <community-list-name> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| community-list-name | WORD  | String  |


## show running-configuration bgp extcommunity-list 

### Description 

```
Show current BGP extended community-list configuration 


```
### Syntax 

```
show running-configuration bgp extcommunity-list [ <extcommunity-list-name> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| extcommunity-list-name | WORD  | String  |


## show running-configuration bgp neighbor 

### Description 

```
Display current BGP neighbor configurations 


```
### Syntax 

```
show running-configuration bgp neighbor vrf { <vrf-name> { [ <ip> ] | { [ interface { Ethernet | PortChannel | Vlan } ] } ] } }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrf-name | WORD  | String  |
| ip | A.B.C.D/A::B  | String  |


## show running-configuration bgp peer-group 

### Description 

```
Display current BGP peer group configurations 


```
### Syntax 

```
show running-configuration bgp peer-group vrf { <vrf-name> [ <peer-group-name> ] }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrf-name | WORD  | String  |
| peer-group-name | WORD  | String  |


## show running-configuration class-map 

### Description 

```
Shows running configuration of class-map(s)


```
### Syntax 

```
show running-configuration class-map [ <show-fbs-class-name> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| show-fbs-class-name | WORD  | String  |


### Usage Guidelines 

```
Class-map name is optional. If Class-map name provided it will show all running configuration of all class-maps configured


```
### Examples 
#### show running-configuration class-map 

```
sonic# show running-configuration class-map class_permit_ipv6
class-map class_permit_ipv6 match-type fields match-all


```
## show running-configuration hardware 

### Description 

```
Show current hardware configuration 


```
### Syntax 

```
show running-configuration hardware

```
## show running-configuration hardware access-list 

### Description 

```
Show current hardware ACL configuration 


```
### Syntax 

```
show running-configuration hardware access-list

```
## show running-configuration interface 

### Description 

```
Show interface info 


```
### Syntax 

```
show running-configuration interface { [ <port> ] | { [ Eth [ <iface_num> ] ] } | { [ Ethernet [ <iface_num> ] ] } } ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| port | EthernetNUM  |   |
| iface_num | EthernetSLOTPORT  |   |


## show running-configuration interface Loopback 

### Description 

```
Show interface loopback info 


```
### Syntax 

```
show running-configuration interface Loopback [ <lo-id> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| lo-id |   | Integer  |


## show running-configuration interface Management 

### Description 

```
Show interface management info 


```
### Syntax 

```
show running-configuration interface Management [ <mgmt-if-id> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| mgmt-if-id |   | Integer  |


## show running-configuration interface PortChannel 

### Description 

```
Show interface PortChannel info 


```
### Syntax 

```
show running-configuration interface PortChannel [ <po-id> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| po-id |   | Integer  |


## show running-configuration interface Vlan 

### Description 

```
Show interface Vlan info 


```
### Syntax 

```
show running-configuration interface Vlan [ <vlan-id> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vlan-id |   | Integer  |


## show running-configuration interface vxlan 

### Description 

```
show VXLAN configuration 


```
### Syntax 

```
show running-configuration interface vxlan

```
## show running-configuration ip access-list 

### Description 

```
Show current IPv4 ACL configuration.


```
### Syntax 

```
show running-configuration ip access-list [ <access-list-name> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| access-list-name | WORD  | String  |


### Usage Guidelines 

```
ACL Name is optional. If no ACL name is specified then configuration for all IPv4 ACLs will be displayed.


```
### Examples 
#### show running-configuration ip access-list 

```
ip access-list ipacl-example
 seq 10 permit ip host 10.1.1.1 host 20.1.1.1
 seq 20 permit ip host 10.1.1.2 host 20.1.1.2
 seq 30 permit ip host 10.1.1.3 host 20.1.1.3
 seq 40 permit ip host 10.1.1.4 host 20.1.1.4


```
## show running-configuration ip prefix-list 

### Description 

```
Show current IPv4 prefix-list configuration 


```
### Syntax 

```
show running-configuration ip prefix-list [ <prefix-list-name> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| prefix-list-name | WORD  | String  |


## show running-configuration ipv6 access-list 

### Description 

```
Show current IPv6 ACL configuration.


```
### Syntax 

```
show running-configuration ipv6 access-list [ <access-list-name> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| access-list-name | WORD  | String  |


### Usage Guidelines 

```
ACL Name is optional. If no ACL name is specified then configuration for all IPv6 ACLs will be displayed.


```
### Examples 
#### show running-configuration ipv6 access-list 

```
ipv6 access-list ipv6acl-example
  seq 100 permit ipv6 host abcd::1 host bcde::1
  seq 200 permit tcp host abcd::2 host bcde::2
  seq 300 permit udp host abcd::3 host bcde::3


```
## show running-configuration ipv6 prefix-list 

### Description 

```
Show current IPv6 prefix-list configuration 


```
### Syntax 

```
show running-configuration ipv6 prefix-list [ <prefix-list-name> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| prefix-list-name | WORD  | String  |


## show running-configuration line vty 

### Description 

```
Show current session configuration 


```
### Syntax 

```
show running-configuration line vty

```
## show running-configuration link state tracking 

### Description 

```
Show current link state tracking configuration 


```
### Syntax 

```
show running-configuration link state tracking [ <show-runn-grp-name> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| show-runn-grp-name | name  | String  |


## show running-configuration mac access-list 

### Description 

```
Show current MAC ACL configuration.


```
### Syntax 

```
show running-configuration mac access-list [ <access-list-name> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| access-list-name | WORD  | String  |


### Usage Guidelines 

```
ACL Name is optional. If no ACL name is specified then configuration for all MAC ACLs will be displayed.


```
### Examples 
#### show running-configuration mac access-list 

```
mac access-list macacl-example
 seq 10 permit host 00:00:10:00:00:01 host 00:00:20:00:00:01
 seq 20 permit host 00:00:10:00:00:02 host 00:00:20:00:00:02
 seq 30 permit host 00:00:10:00:00:03 host 00:00:20:00:00:03
 seq 40 permit host 00:00:10:00:00:04 host 00:00:20:00:00:04


```
## show running-configuration mirror-session 

### Description 

```
Show mirror session info 


```
### Syntax 

```
show running-configuration mirror-session

```
## show running-configuration nat 

### Description 

```
Displays all NAT configurations 


```
### Syntax 

```
show running-configuration nat

```
## show running-configuration ospf 

### Description 

```
Displays all OSPFv2 router configurations 


```
### Syntax 

```
show running-configuration ospf

```
## show running-configuration policy-map 

### Description 

```
Shows running configuration of policy-map(s)


```
### Syntax 

```
show running-configuration policy-map [ <show-fbs-policy-name> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| show-fbs-policy-name | WORD  | String  |


### Usage Guidelines 

```
Policy-map name is optional. If policy-map name provided it will show all running configuration of all policy-maps configured


```
### Examples 
#### show running-configuration policy-map 

```
sonic# show running-configuration policy-map policy_vrf
policy-map policy_vrf type forwarding
 class class_permit_ipv6 priority 10
  set ipv6 next-hop 1211::2  priority 20
  set ipv6 next-hop 1212::2 vrf Vrf-BLUE priority 30
 class class_permit_ip priority 10
  set ip next-hop 12.12.1.2 vrf default priority 30
  set ip next-hop 12.12.2.2 vrf Vrf-BLUE priority 20
  set ip next-hop 12.12.1.2  priority 10


```
## show running-configuration route-map 

### Description 

```
Display current route map configuration 


```
### Syntax 

```
show running-configuration route-map [ <rt-map-name> [ <seq-nu> ] ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| rt-map-name | WORD  | String  |
| seq-nu |   | Integer  |


## show running-configuration spanning-tree 

### Description 

```
Shows running-config for spanning-tree


```
### Syntax 

```
show running-configuration spanning-tree

```
### Usage Guidelines 

```
This command is useful in viewing a summary of running-config for spanning-tree


```
### Examples 
#### show running-config spanning-tree 

```
sonic-cli# show running-configuration spanning-tree
spanning-tree mode pvst
spanning-tree edge-port bpdufilter default
spanning-tree forward-time 4
spanning-tree guard root timeout 5
spanning-tree hello-time 1
spanning-tree max-age 40
spanning-tree priority 61440
!
no spanning-tree vlan 101
!
spanning-tree vlan 100 forward-time 5
spanning-tree vlan 100 hello-time 3
!
spanning-tree vlan 101 forward-time 15
!
interface Ethernet0
 spanning-tree bpdufilter enable
 spanning-tree vlan 100 cost 1
!
interface PortChannel10
 no spanning-tree enable
 no spanning-tree portfast
 spanning-tree cost 2
 spanning-tree vlan 100 cost 1


```
## show running-configuration tam 

### Description 

```
This command is used to show TAM running configuration.


```
### Syntax 

```
show running-configuration tam

```
### Usage Guidelines 

```
Use this command to view TAM running configuration.


```
### Examples 
#### show running-configuration tam 

```
sonic-cli# show running-configuration tam
!
!
tam
 switch-id 3232
 enterprise-id 434
 collector c1 ip 1.1.1.1 port 1111 protocol UDP
 sampler s1 rate 1
 sampler s2 rate 655
 sampler s4 rate 65550
 sampler s5 rate 999999999
 flow-group f1 src-ip 10.1.1.10/24 dst-ip 30.1.1.10/24 protocol TCP l4-src-port 8080 priority 100
 flow-group f2 src-ip 10.1.1.10/32 dst-ip 30.1.1.10/32 protocol UDP priority 100
!
 drop-monitor
  aging-interval 23
!
 ifa
  session ifa1 flowgroup f1 collector c1 node-type EGRESS


```
## show service-policy 

### Description 

```
Shows Global/Switch level flow based services applied policies information


```
### Syntax 

```
show service-policy { Switch | CtrlPlane } [ type { qos | monitoring | forwarding | copp } ]

```
### Usage Guidelines 

```

```
### Examples 
#### Displays service policies applied at the Switch level 

```
Sonic# show service-policy Switch
Switch
  Policy policy_vrf type forwarding at ingress
  Description:
    Flow class_permit_ipv6 at priority 10 (Inactive)
      Description:
      set ipv6 nexthop 1211::2 priority 20
      set ipv6 nexthop 1212::2 vrf Vrf-BLUE priority 30
      Packet matches: 0 frames 0 bytes
    Flow class_permit_ip at priority 10 (Inactive)
      Description:
      set ip nexthop 12.12.1.2 vrf default priority 30
      set ip nexthop 12.12.2.2 vrf Vrf-BLUE priority 20
      set ip nexthop 12.12.1.2 priority 10
      Packet matches: 0 frames 0 bytes


```
## show service-policy interface 

### Description 

```
Shows flow based services applied policies information by interface name


```
### Syntax 

```
show service-policy interface { <eth-if-id> | <po-if-id> | <vlan-if-id> } [ type { qos | monitoring | forwarding | copp } ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| eth-if-id | EthernetNUM  |   |
| po-if-id | PortChannelNUM  |   |
| vlan-if-id | VlanNUM  |   |


### Usage Guidelines 

```
policy-map type is optional. If type is not specified then all policies applied to this given interface will be shown. If type is also provided then only corresponding type policies applied on the given interface will be shown


```
### Examples 
#### Display fbs service applied on given interface vlan 100 

```
show service-policy interface Vlan 100
Vlan100
  Policy policy_mirror type monitoring at ingress
  Description:
    Flow class1 at priority 10 (Active)
      Description:
      Packet matches: 0 frames 0 bytes
  Policy policy_vrf type forwarding at ingress
  Description:
    Flow class_permit_ipv6 at priority 10 (Inactive)
      Description:
      set ipv6 nexthop 1211::2 priority 20
      set ipv6 nexthop 1212::2 vrf Vrf-BLUE priority 30
      Packet matches: 0 frames 0 bytes
    Flow class_permit_ip at priority 10 (Active)
      Description:
      set ip nexthop 12.12.1.2 vrf default priority 30
      set ip nexthop 12.12.2.2 vrf Vrf-BLUE priority 20
      set ip nexthop 12.12.1.2 priority 10
      Packet matches: 0 frames 0 bytes
  Policy policy_qos type qos at egress
  Description:
    Flow class_permit_ipv6 at priority 10 (Inactive)
      Description:
      police: cir 300000000 cbs 300000000 pir 300000000 pbs 300000000 (Active)
        type bytes mode color-blind
        operational cir 0 cbs 0 pir 0 pbs 0
        green 0 packets 0 bytes action forward
        yellow 0 packets 0 bytes action forward
        red 0 packets 0 bytes action drop
      Packet matches: 0 frames 0 bytes
    Flow class_permit_ip at priority 10 (Inactive)
      Description:
      police: cir 300000000 cbs 300000000 pir 300000000 pbs 300000000 (Active)
        type bytes mode color-blind
        operational cir 0 cbs 0 pir 0 pbs 0
        green 0 packets 0 bytes action forward
        yellow 0 packets 0 bytes action forward
        red 0 packets 0 bytes action drop
      Packet matches: 0 frames 0 bytes


```
## show service-policy policy-map 

### Description 

```
Shows flow based services applied policies information by policy name


```
### Syntax 

```
show service-policy policy-map <fbs-policy-name> { { [ interface { <eth-if-id> | <po-if-id> | <vlan-if-id> } ] } | [ Switch ] | [ CtrlPlane ] } ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| fbs-policy-name | WORD  | String  |
| eth-if-id | EthernetNUM  |   |
| po-if-id | PortChannelNUM  |   |
| vlan-if-id | VlanNUM  |   |


### Usage Guidelines 

```
policy-map interface argument is optional. If interface is not specified then it shows all services applied interfaces information for given policy. If interface is also specified then it gives service applied information for given policy name and given interface


```
### Examples 
#### Displays fbs service applied for given policy policy_vrf 

```
show service-policy policy-map policy_vrf
Vlan100
  Policy policy_vrf type forwarding at ingress
  Description:
    Flow class_permit_ipv6 at priority 10 (Inactive)
      Description:
      set ipv6 nexthop 1211::2 priority 20
      set ipv6 nexthop 1212::2 vrf Vrf-BLUE priority 30
      Packet matches: 0 frames 0 bytes
    Flow class_permit_ip at priority 10 (Inactive)
      Description:
      set ip nexthop 12.12.1.2 vrf default priority 30
      set ip nexthop 12.12.2.2 vrf Vrf-BLUE priority 20
      set ip nexthop 12.12.1.2 priority 10
      Packet matches: 0 frames 0 bytes
Switch
  Policy policy_vrf type forwarding at ingress
  Description:
    Flow class_permit_ipv6 at priority 10 (Inactive)
      Description:
      set ipv6 nexthop 1211::2 priority 20
      set ipv6 nexthop 1212::2 vrf Vrf-BLUE priority 30
      Packet matches: 0 frames 0 bytes
    Flow class_permit_ip at priority 10 (Inactive)
      Description:
      set ip nexthop 12.12.1.2 vrf default priority 30
      set ip nexthop 12.12.2.2 vrf Vrf-BLUE priority 20
      set ip nexthop 12.12.1.2 priority 10
      Packet matches: 0 frames 0 bytes


```
## show service-policy summary 

### Description 

```
Shows summary of applied flow based services policies


```
### Syntax 

```
show service-policy summary { { [ interface { <eth-if-id> | <po-if-id> | <vlan-if-id> } ] } | [ Switch ] | [ CtrlPlane ] } ] [ type { qos | monitoring | forwarding | copp } ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| eth-if-id | EthernetNUM  |   |
| po-if-id | PortChannelNUM  |   |
| vlan-if-id | VlanNUM  |   |


### Usage Guidelines 

```
Interface argument is optional. If interface is not specified it shows all service applied interfaces and their policy information. If interface is specified it shows service applied policy information for given interface.If interface is Switch it shows global/Switch level service policies.


```
### Examples 
#### Displays summary of applied flow based service policies 

```
sonic# show service-policy summary
Vlan100
    monitoring policy policy_mirror at ingress
    forwarding policy policy_vrf at ingress
    qos policy policy_qos at egress
Switch
    forwarding policy policy_vrf at ingress
CtrlPlane
    qos policy oob-qos-policy at ingress


```
## show sflow 

### Description 

```
Show global sFlow configuration

```
### Syntax 

```
show sflow

```
### Examples 

```
sonic# show sflow
---------------------------------------------------------
Global sFlow Information
---------------------------------------------------------
admin state:       up
polling-interval:  20
agent-id:          default
sonic#


```
## show sflow interface 

### Description 

```
Show sFlow interface configuration

```
### Syntax 

```
show sflow interface

```
### Examples 

```
sonic# show sflow interface
-----------------------------------------------------------
sFlow interface configurations
Interface            Admin State             Sampling Rate
Ethernet0            up                      4000
Ethernet1            up                      4000
Ethernet2            up                      4000
Ethernet3            up                      4000
Ethernet4            up                      4000
Ethernet5            up                      4000
Ethernet6            up                      4000
Ethernet7            up                      4000
Ethernet8            up                      4000
Ethernet9            up                      4000
Ethernet10           up                      4000
Ethernet11           up                      4000
Ethernet12           up                      4000
Ethernet13           up                      4000
Ethernet14           up                      4000
Ethernet15           up                      4000
Ethernet16           up                      4000
Ethernet17           up                      4000
Ethernet18           up                      4000
Ethernet19           up                      4000
Ethernet20           up                      4000
Ethernet21           up                      4000
--more--
Ethernet22           up                      4000
Ethernet23           up                      4000
Ethernet24           up                      4000
Ethernet25           up                      4000
Ethernet26           up                      4000
Ethernet27           up                      4000
Ethernet28           up                      4000
Ethernet29           up                      4000
sonic#


```
## show snmp counters 

### Description 

```
Displays the Simple Network Management Protocol (SNMP) counters statistics
as per RFC1213 MIB SNMP group


```
### Syntax 

```
show snmp counters

```
### Usage Guidelines 

```
Use this command to view global SNMP counters statistics.


```
### Examples 

```

         sonic# show snmp counters
         37 SNMP packets input
         0 Bad SNMP version errors
         4 Unknown community name
         0 Illegal operation for community name supplied
         0 Encoding errors
         24 Number of requested variables
         0 Number of altered variables
         0 Get-request PDUs
         28 Get-next PDUs
         0 Set-request PDUs
         78 SNMP packets output
         0 Too big errors (Maximum packet size 1500)
         0 No such name errors
         0 Bad values errors
         0 General errors
         24 Response PDUs
         13 Trap PDUs


```
## show snmp-server 

### Description 

```
Displays the Simple Network Management Protocol (SNMP) server information including
the physical location of the switch, the organization responsible for the network,
SNMP engine identification, trap status and the agent addresses, if configured.
SNMP engine identification is derived from the device MAC address on an initial boot.


```
### Syntax 

```
show snmp-server

```
### Usage Guidelines 

```
Use this command to view global SNMP server information.


```
### Examples 

```
sonic# show snmp-server

Location   : Lab1, Rack-10
Contact    : Broadcom Support
EngineID   : 8000013703525400f6817e
Traps      : enable

Agent Addresses:

               IP Address               UDP Port            Interface
--------------------------------------- -------- --------------------------------
1.2.3.4                                 161
1.2.3.4                                 1024
1.2.3.5                                 1024     Ethernet10


```
## show snmp-server community 

### Description 

```
Displays the SNMP communities configured on the switch and the community group
association, if configured. Communities are used by SNMPv2 protocol to access the switch.


```
### Syntax 

```
show snmp-server community

```
### Usage Guidelines 

```
Use this command to view the configured SNMP communities.


```
### Examples 

```
sonic# show snmp-server community

         Community Name                     Group Name
-------------------------------- --------------------------------
comm1                            group-lab
comm2                            None


```
## show snmp-server group 

### Description 

```
Displays the SNMP groups configured on the switch. The model and security information
indicate the SNMP protocol and security level used to access the system via the group.
View names indicate the view that a group provides read, write or trap access to.


```
### Syntax 

```
show snmp-server group

```
### Usage Guidelines 

```
Use this command to view the configured SNMP groups.


```
### Examples 

```
sonic# show snmp-server group

   Group Name      Model: Security       Read View        Write View      Notify View
---------------- -------------------- ---------------- ---------------- ----------------
group-floor1     v2c: no-auth-no-priv ro_view          wr_view          None
group-floor2     v3 : auth-priv       r_view           None             None
group-lab        v2c: no-auth-no-priv None             None             None


```
## show snmp-server host 

### Description 

```
Displays the SNMP hosts to which the trap or inform messages are sent by the SNMP agent.
Timeout indicates the number of seconds before the traps/informs time out when sending to a host.
Retries indicate the number of times the traps/informs are sent after timing out.


```
### Syntax 

```
show snmp-server host

```
### Usage Guidelines 

```
Use this command to view the configured SNMP hosts.


```
### Examples 

```
sonic# show snmp-server host

             Target Address              Type    Community    Ver T-Out Retries
--------------------------------------- ------ -------------- --- ----- -------
1.2.3.4                                 trap   comm1          v2c 15    3


             Target Address              Type    User Name        Security    T-Out Retries
--------------------------------------- ------ -------------- --------------- ----- -------
1001::1                                 inform user1          auth-priv       200   10


```
## show snmp-server user 

### Description 

```
Displays the SNMPv3 users configured on the switch including any authentication
and/or encryption algorithm for the user. The group name indicates a group
that defines the SNMPv3 access parameters.


```
### Syntax 

```
show snmp-server user

```
### Usage Guidelines 

```
Use this command to view the configured SNMPv3 users.


```
### Examples 

```
sonic# show snmp-server user

           User Name                        Group Name            Auth  Privacy
-------------------------------- -------------------------------- ----- -------
user1                            group-lab                        md5   aes-128
user2                            group-floor2                     None  None


```
## show snmp-server view 

### Description 

```
Displays the SNMP views configured on the switch including the OID tree that
the view includes or excludes.


```
### Syntax 

```
show snmp-server view

```
### Usage Guidelines 

```
Use this command to view the configured SNMP views.


```
### Examples 

```
sonic# show snmp-server view

           View Name                         OID Tree               Type
-------------------------------- -------------------------------- --------
view1                            1.2.3.4.5.6.7.8.9.1              included
view2                            1.2.3.4.5.6.7.8.9.5.1            excluded


```
## show spanning-tree 

### Description 

```
Shows spanning-tree information


```
### Syntax 

```
show spanning-tree [ vlan { <vlan-id> { [ interface <name> ] } } ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vlan-id |   | String  |
| name | String  | String  |


### Usage Guidelines 

```
This command is useful in viewing the spanning-tree information


```
### Examples 
#### show spanning-tree 

```
sonic(config)# do show spanning-tree
Spanning-tree Mode: PVST

VLAN 100 - STP instance 0
------------------------------------------------------------------------------------------------------------
STP Bridge Parameters:

Bridge            Bridge    Bridge    Bridge    Hold  LastTopology  Topology
Identifier        MaxAge    Hello     FwdDly    Time  Change        Change
hex               sec       sec       sec       sec   sec           cnt
80643c2c99a704a0  20        2         15        1     515           4

RootBridge        RootPath  DesignatedBridge  Root        Max  Hel  Fwd
Identifier        Cost      Identifier        Port        Age  lo   Dly
hex                         hex                           sec  sec  sec
00646cb9c51613ca  1600      10643c2c992d8235  PortChannel120   2    15

STP Port Parameters:
Port            Prio Path      Port Uplink BPDU   Guard State      Designated Designated       Designated
Num             rity Cost      Fast Fast   Filter Type             Cost       Root             bridge
PortChannel1    128  800       N    N      N      -        FORWARDING 800        00646cb9c51613ca 10643c2c992d8235


```
## show spanning-tree bpdu-guard 

### Description 

```
Shows spanning-tree BPDU guard information


```
### Syntax 

```
show spanning-tree bpdu-guard

```
### Usage Guidelines 

```
This command is useful in viewing the spanning-tree BPDU guard information for the ports


```
### Examples 
#### show spanning-tree bpdu-guard 

```
sonic#show spanning-tree bpdu-guard
PortNum         Shutdown    Port shut
                Configured  due to BPDU guard
Ethernet64       Y           N
PortChannel2     Y           N


```
## show spanning-tree counters 

### Description 

```
Shows spanning-tree counters information


```
### Syntax 

```
show spanning-tree counters [ vlan <vlan-id> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vlan-id |   | String  |


### Usage Guidelines 

```
This command is useful in viewing the spanning-tree counters information


```
### Examples 
#### show spanning-tree counters 

```
sonic(config)# do show spanning-tree counters

VLAN 100 - STP instance 0
----------------------------------------------------------------------
PortNum         BPDU Tx   BPDU Rx   TCN Tx    TCN Rx

----------------------------------------------------------------------
Ethernet0       0         0         0         0
Ethernet48      53        7         1         0
Ethernet62      0         0         0         0
Ethernet64      0         0         0         0
PortChannel1    0         76        1         0
PortChannel2    0         0         0         0
PortChannel3    0         0         0         0


```
## show spanning-tree inconsistentports 

### Description 

```
Shows spanning-tree root or loop inconsistent information


```
### Syntax 

```
show spanning-tree inconsistentports [ vlan <vlan-id> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vlan-id |   | String  |


### Usage Guidelines 

```
This command is useful in viewing the spanning-tree root or loop inconsistent ports information


```
### Examples 
#### show spanning-tree inconsistentports 

```
sonic# show spanning-tree inconsistentports

Root guard timeout: 30  secs
Loop guard default: Enabled

----------------------------------------------------------------------
PortNum          VLAN  Inconsistency State
----------------------------------------------------------------------
Ethernet48       100   Root Inconsistent (29 seconds left on timer)
Ethernet49       10    Loop Inconsistent


```
## show ssh-server vrf 

### Description 

```
Shows list of VRFs on which ssh server is enabled

```
### Syntax 

```
show ssh-server vrf { all | <vrf-name> }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrf-name | WORD  | String  |


### Examples 

```
sonic# show ssh-server vrf all
            VRF Name                          Status
 -------------------------------- --------------------------------
            Vrf1                             enable
            Vrf34                            enable
            Vrf5                             enable

sonic# show ssh-server vrf Vrf5
            VRF Name                          Status
 -------------------------------- --------------------------------
            Vrf5                             enable


```
## show ssh-server vrfs 

### Description 

```
Displays SSH server status for all VRFs 


```
### Syntax 

```
show ssh-server vrfs

```
## show storm-control 

### Description 

```
show command to display all BUM storm-control configurations.


```
### Syntax 

```
show storm-control

```
### Usage Guidelines 

```
sonic-cli# show storm-control


```
### Examples 
#### show storm-control 

```
sonic-cli# show storm-control
-----------------------------------------------------
Interface Name      Storm-type          Rate (kbps)
-----------------------------------------------------
Ethernet0           broadcast           10000
Ethernet0           unknown-unicast     20000
Ethernet0           unknown-multicast   30000
Ethernet4           unknown-unicast     20000
Ethernet4           unknown-multicast   30000


```
## show storm-control interface 

### Description 

```
show command to display BUM storm-control configurations on the given interface.


```
### Syntax 

```
show storm-control interface <Ethernet>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| Ethernet | EthernetNUM  |   |


### Usage Guidelines 

```
sonic-cli# show storm-control interface Ethernet 4


```
### Examples 
#### show storm-control 

```
sonic-cli# show storm-control interface Ethernet 4
-----------------------------------------------------
Interface Name      Storm-type          Rate (kbps)
-----------------------------------------------------
Ethernet4           unknown-unicast     20000
Ethernet4           unknown-multicast   30000


```
## show switch-profiles 

### Description 

```
Use this command to list all supported factory default configuration profiles.
It also displays the current active default configuration profile. The current
active profile name is used to create a startup configuration file when the write
erase command is used. The following information about each default configuration
profile is displayed.

 - Name: Profile name used to refer to a default configuration profile
 - Description: A descriptive string


```
### Syntax 

```
show switch-profiles

```
### Usage Guidelines 

```
show factory default profiles


```
### Examples 

```
sonic# show switch-profiles
Factory Default: l3

Profile Name     Description
-------------------------------------------------------------
l2               Layer 2 Switch Configuration
l3               Layer 3 Router Configuration


```
## show switch-resource drop-monitor 

### Description 

```
Show warm restart

```
### Syntax 

```
show switch-resource drop-monitor

```
### Examples 
#### Shows the warm restart state information 

```

```
## show swsslog-configuration 

### Description 

```
Display components registered in DB for loglevel severity 


```
### Syntax 

```
show swsslog-configuration

```
## show system 

### Description 

```
Show system information 


```
### Syntax 

```
show system

```
## show system cpu 

### Description 

```
Show system cpu information 


```
### Syntax 

```
show system cpu

```
## show system memory 

### Description 

```
Show system memory information 


```
### Syntax 

```
show system memory

```
## show system processes 

### Description 

```
Show system processes 


```
### Syntax 

```
show system processes

```
## show system processes cpu 

### Description 

```
Show system processes sorted by CPU usage 


```
### Syntax 

```
show system processes cpu

```
## show system processes mem-usage 

### Description 

```
Show system processes sorted by memory usage 


```
### Syntax 

```
show system processes mem-usage

```
## show system processes mem-util 

### Description 

```
Show system processes sorted by memory utilization 


```
### Syntax 

```
show system processes mem-util

```
## show system processes pid 

### Description 

```
Show system process information of a particular PID 


```
### Syntax 

```
show system processes pid <pid-no>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| pid-no | Process ID  | Integer  |


## show tacacs-server 

### Description 

```
Show TACACS information 


```
### Syntax 

```
show tacacs-server

```
## show tacacs-server global 

### Description 

```
Show TACACS global configuration 


```
### Syntax 

```
show tacacs-server global

```
## show tacacs-server host 

### Description 

```
Show TACACS server configuration 


```
### Syntax 

```
show tacacs-server host [ <address> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| address | WORD  | String  |


## show tam collectors 

### Description 

```
This command lists the details for all collectors or for a specific collector.


```
### Syntax 

```
show tam collectors [ <name> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| name | WORD  | String  |


### Usage Guidelines 

```
This command lists the details for all collectors or for a specific collector.


```
### Examples 
#### show tam collectors 

```
Name               IP Address       Port    Protocol
----------------   --------------   ------  --------
IFA_Col_i19        192.168.78.121   7071    UDP
MOD_Col_m16        192.168.78.123   7076    UDP

# show tam collectors IFA_Col_i19
Name          : IFA_Col_i19
IP Address    : 192.168.78.121
Port          : 7071
Protocol      : UDP


```
## show tam drop-monitor 

### Description 

```
This command lists the switch-wide attributes that are in use.


```
### Syntax 

```
show tam drop-monitor

```
### Usage Guidelines 

```
This command lists the switch-wide attributes that are in use.


```
### Examples 
#### show tam drop-monitor 

```
Status             : Active
Switch ID          : 2020
Aging Interval     : 20


```
## show tam drop-monitor sessions 

### Description 

```
This command lists the details for all drop-monitor sessions or for a specific session.


```
### Syntax 

```
show tam drop-monitor sessions [ <name> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| name | WORD  | String  |


### Usage Guidelines 

```
This command lists the details for all drop-monitor sessions or for a specific session. Note that only explicitly configured tuples in the associated flow-group are displayed.


```
### Examples 
#### show tam drop-monitor sessions 

```
Name           Flow Group          Collector          Sampler
-----------    ----------------    --------------     -------------
http_236       tcp_port_236        Col_i19            aggresive
http_239       tcp_port_239        Col_i19            aggresive
http_241       tcp_port_241        Col_i19            aggresive

sonic # show tam drop-monitor sessions http_236

Session            : http_236
Flow Group Name    : tcp_port_236
   Id              : 4025
   Priority        : 100
   SRC IP          : 13.92.96.32
   DST IP          : 7.72.235.82
   DST L4 Port     : 236
   Ingress Intf    : Ethernet20
Collector          : Col_i19
Sampler            : aggresive
Packet Count       : 7656


```
## show tam features 

### Description 

```
This command lists the current status for all TAM features or for a specific feature.


```
### Syntax 

```
show tam features { [ ifa ] | [ drop-monitor ] | [ tail-stamping ] } ]

```
### Usage Guidelines 

```
This command lists the current status for all TAM features or for a specific feature.


```
### Examples 
#### show tam features 

```
Name         Status
-----------  ---------
ifa          Active
drop-monitor Active

sonic-cli# show tam features ifa

Name          : ifa
Status        : Active


```
## show tam flowgroups 

### Description 

```
This command lists the details for all flow-groups or for a specific flow-group.


```
### Syntax 

```
show tam flowgroups [ <name> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| name | WORD  | String  |


### Usage Guidelines 

```
This command lists the details for all flow-groups or for a specific flow-group. Note that only explicitly configured tuples are displayed.


```
### Examples 
#### show tam flow-groups 

```
Flow Group Name    : udp_port_239
   Id              : 4025
   Priority        : 100
   SRC IP          : 10.72.195.23
   DST L4 Port     : 239
   Ingress Intf    : Ethernet20
Packet Count       : 10584

Flow Group Name    : udp_port_241
   Id              : 4022
   Priority        : 99
   SRC Port        : 1906
   DST L4 Port     : 241
Packet Count       : 8654367

sonic # show tam flow-groups udp_port_239

Flow Group Name    : udp_port_239
   Id              : 4025
   Priority        : 100
   SRC IP          : 10.72.195.23
   DST L4 Port     : 239
Packet Count       : 10584


```
## show tam ifa 

### Description 

```
This command lists the switch-wide attributes that are in use.


```
### Syntax 

```
show tam ifa

```
### Usage Guidelines 

```
This command lists the switch-wide attributes that are in use.


```
### Examples 
#### show tam ifa 

```
Status             : Active
Version            : 2.0
Switch ID          : 2020
Enterprise ID      : 2345


```
## show tam ifa sessions 

### Description 

```
This command lists the details for all ifa sessions or for a specific session.


```
### Syntax 

```
show tam ifa sessions [ <name> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| name | WORD  | String  |


### Usage Guidelines 

```
This command lists the details for all ifa sessions or for a specific session. Note that only explicitly configured tuples in the associated flow-group are displayed.


```
### Examples 
#### show tam ifa sessions 

```
Name           Flow Group          Collector          Sampler           Node Type
-----------    ----------------    --------------     -------------     ----------
http_236       tcp_port_236        -                  aggresive         Ingress
http_239       tcp_port_239        -                  -                 Intermediate
http_241       tcp_port_241        IFA_Col_i19        -                 Egress

sonic # show tam ifa sessions http_236

Session            : http_236 (Ingress)
Flow Group Name    : tcp_port_236
   Id              : 4025
   Priority        : 100
   SRC IP          : 13.92.96.32
   DST IP          : 7.72.235.82
   DST L4 Port     : 236
   Ingress Intf    : Ethernet20
Collector          : None
Sampler            : aggresive
Packet Count       : 7656


```
## show tam samplers 

### Description 

```
This command lists the details for all samplers or for a specific sampler.


```
### Syntax 

```
show tam samplers [ <name> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| name | WORD  | String  |


### Usage Guidelines 

```
This command lists the details for all samplers or for a specific sampler.


```
### Examples 
#### show tam samplers 

```
Name         Sample Rate
-----------  -----------
sflow_low    1
aggresive    2000

# show tam samplers aggresive
Name          : aggresive
Sample Rate   : 2000


```
## show tam switch 

### Description 

```
This command is used to show TAM device information.


```
### Syntax 

```
show tam switch

```
### Usage Guidelines 

```
This command displays the configured TAM Device Identifier.


```
### Examples 
#### show tam switch 

```
TAM Device information
----------------------
Switch ID      : 23456
Enterprise ID  : 1234


```
## show tam tail-stamping 

### Description 

```
This command lists the switch-wide attributes that are in use.


```
### Syntax 

```
show tam tail-stamping

```
### Usage Guidelines 

```
This command lists the switch-wide attributes that are in use.


```
### Examples 
#### show tam tail-stamping 

```
Status             : Active
Switch ID          : 2020


```
## show tam tail-stamping sessions 

### Description 

```
This command lists the details for all tail-stamping sessions or for a specific session.


```
### Syntax 

```
show tam tail-stamping sessions [ <name> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| name | WORD  | String  |


### Usage Guidelines 

```
This command lists the details for all tail-stamping sessions or for a specific session. Note that only explicitly configured tuples in the associated flow-group are displayed.


```
### Examples 
#### show tam tail-stamping sessions 

```
Name           Flow Group
-----------    ----------------
http_236       tcp_port_236
http_239       tcp_port_239
http_241       tcp_port_241

sonic # show tam tail-stamping sessions http_236

Session            : http_236
Flow Group Name    : tcp_port_236
   Id              : 4025
   Priority        : 100
   SRC IP          : 13.92.96.32
   DST IP          : 7.72.235.82
   DST L4 Port     : 236
Packet Count       : 7656


```
## show tech-support 

### Description 

```
Collect technical support information 


```
### Syntax 

```
show tech-support [ since <date> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| date | String  | String  |


## show threshold breaches 

### Description 

```
This command is used to show information about threshold breach events recorded by the system.


```
### Syntax 

```
show threshold breaches

```
### Usage Guidelines 

```
Use this command to display the threshold breach events recorded by the system.


```
### Examples 
#### Show threshold breaches 

```
------------------------------------------------------------------------------------------------------------------
Event-id    Buffer            Type        Port           Index     Breach Value(%)       Time-stamp
------------------------------------------------------------------------------------------------------------------
2           priority-group    shared      Ethernet0      7         77                    2020-04-14-11:35:20
3           queue             unicast     Ethernet0      5         45                    2020-04-17-11:30:20


```
## show threshold buffer-pool 

### Description 

```
Show buffer-pool configuration 


```
### Syntax 

```
show threshold buffer-pool

```
## show threshold priority-group 

### Description 

```
Show priority-group threshold configuration 


```
### Syntax 

```
show threshold priority-group { headroom | shared }

```
## show threshold queue 

### Description 

```
Show queue threshold configuration 


```
### Syntax 

```
show threshold queue { CPU | multicast | unicast }

```
## show tpcm 

### Description 

```
Show TPC image information 


```
### Syntax 

```
show tpcm list

```
## show udld global 

### Description 

```
Shows global level UDLD information


```
### Syntax 

```
show udld global

```
### Usage Guidelines 

```
After enabling UDLD at global level or modifying global level UDLD attributes, use this command to check global level UDLD information


```
### Examples 
#### Shows UDLD global level details 

```
sonic-cli(config)# udld enable
sonic-cli# show udld global

UDLD Global Information
  Admin State        : UDLD Enabled
  Mode               : Normal
  UDLD Message Time  : 1 seconds
  UDLD Multiplier    : 3


```
## show udld interface 

### Description 

```
Shows interface level UDLD information and neighbors details on this interface


```
### Syntax 

```
show udld interface <interface-name>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| interface-name | EthernetNUM  |   |


### Usage Guidelines 

```
After enabling UDLD at interface level, use this command to get interface level UDLD information and neighbors attached to this interface.


```
### Examples 
#### Shows interface level UDLD information and neighbors details on this interface 

```
sonic-cli(config)# udld enable
sonic-cli(config)# interface Ethernet 0
sonic-cli(conf-if-Ethernet0)# udld enable
sonic-cli# show udld interface Ethernet0

UDLD information for Ethernet0
  UDLD Admin State:                  Enabled
  Mode:                              Normal
  Status:                            Bidirectional
  Local device id:                   3c2c.992d.8201
  Local port id :                    Ethernet0
  Local device name:                 Sonic
  Message time:                      1
  Timeout interval:                  3
    Neighbor Entry 1
    ----------------------------------------------------------------------------------------
    Neighbor device id:         3c2c.992d.8235
    Neighbor port id:           Ethernet0
    Neighbor device name:       Sonic
    Neighbor message time:      1
    Neighbor timeout interval:  3


```
## show udld neighbors 

### Description 

```
Shows UDLD neighbors information


```
### Syntax 

```
show udld neighbors

```
### Usage Guidelines 

```
After enabling UDLD at global and interface level, use this command to get all UDLD neighbors information.


```
### Examples 
#### Shows UDLD neighbors details 

```
sonic-cli(config)# udld enable
sonic-cli(config)# interface Ethernet 0
sonic-cli(conf-if-Ethernet0)# udld enable
sonic-cli# show udld neighbors

Port           Device Name     Device ID         Port ID         Neighbor State
---------------------------------------------------------------------------------
Ethernet1      Sonic           3c2c.992d.8201    Ethernet0       Bidirectional
Ethernet3      Sonic           3c2c.992d.8201    Ethernet3       Bidirectional


```
## show udld statistics 

### Description 

```
Shows UDLD statistics for all interfaces


```
### Syntax 

```
show udld statistics

```
### Usage Guidelines 

```
Use this command to get UDLD statistics for all interfaces


```
### Examples 
#### Shows UDLD statistics for all interfaces 

```
sonic-cli# show udld statistics

UDLD Interface statistics for Ethernet0
Frames transmitted:         10
Frames received:            9
Frames with error:          0

UDLD Interface statistics for Ethernet1
Frames transmitted:         5
Frames received:            8
Frames with error:          0


```
## show udld statistics interface 

### Description 

```
Shows UDLD statistics for a specific interface


```
### Syntax 

```
show udld statistics interface <interface-name>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| interface-name | EthernetNUM  |   |


### Usage Guidelines 

```
Use this command to get UDLD statistics for a specific interface


```
### Examples 
#### Shows UDLD statistics for a specific interface 

```
sonic-cli# show udld statistics interface Ethernet0

UDLD Interface statistics for Ethernet0
Frames transmitted:         10
Frames received:            9
Frames with error:          0


```
## show uptime 

### Description 

```
Show system uptime 


```
### Syntax 

```
show uptime

```
## show users 

### Description 

```
Show users 


```
### Syntax 

```
show users

```
## show version 

### Description 

```
Show version information 


```
### Syntax 

```
show version

```
## show vrrp 

### Description 

```
Displays IPv4 VRRP information


```
### Syntax 

```
show vrrp [ interface { <ifname> { vrid <id> } } ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| ifname | String  | String  |
| id |   | Integer  |


### Usage Guidelines 

```
Use this command to display VRRP summary information of all instances on a system or
to display VRRP detailed information of a particular instance.


```
### Examples 
#### Display IPv4 VRRP information 

```
sonic-cli(config)# show vrrp
           or
sonic-cli(config)# show vrrp interface Ethernet4 vrid 1


```
#### Display IPv4 VRRP summary information 

```
sonic# show vrrp
    Interface_Name  VRID   State        VIP            Cfg_Prio   Curr_Prio
    Ethernet4       1      Master       40.0.0.5       120        120
    Ethernet8       2      Backup       80.0.0.5       100        100


```
#### Display IPv4 VRRP detailed information 

```
sonic# show vrrp interface Ethernet4 vrid 1
    Ethernet4, VRID 1
    Version is 2
    State is Master
    Virtual IP address:
        40.0.0.5
    Virtual MAC address is 0000.5e00.0101
    Track interface:
        None
    Configured Priority is 100, Current Priority is 100
    Advertisement interval is 1 sec
    Preemption is enabled


```
### Features this CLI belongs to 
*  VRRP


## show vrrp6 

### Description 

```
Displays IPv6 VRRP information,


```
### Syntax 

```
show vrrp6 [ interface { <ifname> { vrid <id> } } ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| ifname | String  | String  |
| id |   | Integer  |


### Usage Guidelines 

```
Use this command to display VRRP summary information of all instances on a system or
to display VRRP detailed information of a particular instance.


```
### Examples 
#### Display IPv6 VRRP information 

```
sonic-cli(config)# show vrrp6
           or
sonic-cli(config)# show vrrp6 interface Ethernet4 vrid 1


```
#### Display IPv6 VRRP summary information 

```
sonic# show vrrp
    Interface_Name  VRID   State        VIP            Cfg_Prio   Curr_Prio
    Ethernet4       1      Master       40::5          120        120
    Ethernet8       2      Backup       80::5          100        100


```
#### Display IPv6 VRRP detailed information 

```
sonic# show vrrp interface Ethernet4 vrid 1
    Ethernet4, VRID 1
    Version is 3
    State is Master
    Virtual IP address:
      40::5
    Virtual MAC address is 0000.5e00.0201
    Track interface:
      None
    Configured Priority is 100, Current Priority is 100
    Advertisement interval is 1 sec
    Preemption is enabled


```
### Features this CLI belongs to 
*  VRRP


## show vxlan interface 

### Description 

```
show command to display the VXLAN global parameters.


```
### Syntax 

```
show vxlan interface

```
### Usage Guidelines 

```
sonic# show vxlan interface


```
### Examples 
#### show vxlan interface 

```
_
                     sonic# show vxlan interface

                     VTEP Name        :  vtep1
                     VTEP Source IP   :  1.1.1.1
                     QoS Mode         :  pipe (dscp:0)
                     Source Interface :  Loopback0



```
## show vxlan remote mac 

### Description 

```
Show command to display all the MACs learnt from the specified remote IP or all the remotes for the specified/all VLANs.
VLAN, MAC, RemoteVTEP, VNI, Type are the columns.


```
### Syntax 

```
show vxlan remote mac [ <remote_ip_addr> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| remote_ip_addr | A.B.C.D  | String  |


### Usage Guidelines 

```
sonic# show vxlan remote mac


```
### Examples 
#### show vxlan remote mac information 

```
sonic# show vxlan remote mac
+---------+-------------------+--------------+-------+--------+
| VLAN    | MAC               | RemoteVTEP   |   VNI | Type   |
+=========+===================+==============+=======+========+
| Vlan101 | 00:00:00:00:00:01 | 4.4.4.4      |  1001 | static |
+---------+-------------------+--------------+-------+--------+
| Vlan101 | 00:00:00:00:00:02 | 3.3.3.3      |  1001 | static |
+---------+-------------------+--------------+-------+--------+
| Vlan101 | 00:00:00:00:00:03 | 4.4.4.4      |  1001 | static |
+---------+-------------------+--------------+-------+--------+
| Vlan101 | 00:00:00:00:00:04 | 4.4.4.4      |  1001 | static |
+---------+-------------------+--------------+-------+--------+
| Vlan101 | 00:00:00:00:00:05 | 4.4.4.4      |  1001 | static |
+---------+-------------------+--------------+-------+--------+
| Vlan101 | 00:00:00:00:00:99 | 3.3.3.3      |  1001 | static |
+---------+-------------------+--------------+-------+--------+
Total count : 6

sonic# show vxlan remote mac 3.3.3.3
+---------+-------------------+--------------+-------+--------+
| VLAN    | MAC               | RemoteVTEP   |   VNI | Type   |
+=========+===================+==============+=======+========+
| Vlan101 | 00:00:00:00:00:02 | 3.3.3.3      |  1001 | static |
+---------+-------------------+--------------+-------+--------+
| Vlan101 | 00:00:00:00:00:99 | 3.3.3.3      |  1001 | static |
+---------+-------------------+--------------+-------+--------+
Total count : 2


```
## show vxlan remote mac count 

### Description 

```
shows number of remote MACs 


```
### Syntax 

```
show vxlan remote mac count [ <remote_ip_addr> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| remote_ip_addr | A.B.C.D  | String  |


## show vxlan remote vni 

### Description 

```
Show command to display all the VLANs learnt from the specified remote IP or all the remotes.
VLAN, RemoteVTEP, VNI are the columns


```
### Syntax 

```
show vxlan remote vni [ <remote_ip_addr> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| remote_ip_addr | A.B.C.D  | String  |


### Usage Guidelines 

```
sonic# show vxlan remote vni


```
### Examples 
#### show vxlan remote vni information 

```
sonic# show vxlan remote vni

+---------+--------------+-------+
| VLAN    | RemoteVTEP   |   VNI |
+=========+==============+=======+
| Vlan101 | 3.3.3.3      |  1001 |
+---------+--------------+-------+
| Vlan101 | 4.4.4.4      |  1001 |
+---------+--------------+-------+
Total count : 2

sonic# show vxlan remote vni 3.3.3.3

+---------+--------------+-------+
| VLAN    | RemoteVTEP   |   VNI |
+=========+==============+=======+
| Vlan101 | 3.3.3.3      |  1001 |
+---------+--------------+-------+
Total count : 1


```
## show vxlan remote vni count 

### Description 

```
shows the number of VLANs extended to remote VTEPs. 


```
### Syntax 

```
show vxlan remote vni count [ <remote_ip_addr> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| remote_ip_addr | A.B.C.D  | String  |


## show vxlan tunnel 

### Description 

```
show command to display all the discovered tunnels.
SIP, DIP, creation source, operstatus are the columns.


```
### Syntax 

```
show vxlan tunnel

```
### Usage Guidelines 

```
sonic# show vxlan tunnel


```
### Examples 
#### show vxlan tunnel 

```
sonic# show vxlan tunnel

+---------+---------+-------------------+--------------+
| SIP     | DIP     | creation source   | operstatus   |
+=========+=========+===================+==============+
| 2.2.2.2 | 4.4.4.4 | EVPN              | oper_up      |
+---------+---------+-------------------+--------------+
| 2.2.2.2 | 3.3.3.3 | EVPN              | oper_up      |
+---------+---------+-------------------+--------------+
Total count : 2


```
## show vxlan tunnel count 

### Description 

```
shows number of remote VTEPs 


```
### Syntax 

```
show vxlan tunnel count

```
## show vxlan vlanvnimap 

### Description 

```
show command to display all the VLAN VNI mappings


```
### Syntax 

```
show vxlan vlanvnimap

```
### Usage Guidelines 

```
sonic# show vxlan vlanvnimap


```
### Examples 
#### show vxlan vlanvnimap 

```
sonic# show vxlan vlanvnimap

+---------+-------+
| VLAN    |   VNI |
+=========+=======+
| Vlan100 |   100 |
+---------+-------+
| Vlan101 |   101 |
+---------+-------+
Total count : 2


```
## show vxlan vlanvnimap count 

### Description 

```
shows number of VLAN VNI mappings  


```
### Syntax 

```
show vxlan vlanvnimap count

```
## show vxlan vrfvnimap 

### Description 

```
show command to display all the VRF VNI mappings


```
### Syntax 

```
show vxlan vrfvnimap

```
### Usage Guidelines 

```
sonic# show vxlan vrfvnimap


```
### Examples 
#### show vxlan vrfvnimap 

```
sonic# show vxlan vrfvnimap

+-------+-------+
| VRF   |   VNI |
+=======+=======+
| Vrf1  |   600 |
+-------+-------+
Total count : 1


```
## show vxlan vrfvnimap count 

### Description 

```
shows number of VRF VNI mappings  


```
### Syntax 

```
show vxlan vrfvnimap count

```
## show warm-restart 

### Description 

```
Show warm restart

```
### Syntax 

```
show warm-restart

```
### Examples 
#### Shows the warm restart state information 

```
sonic# show warm-restart
------------------------------------------------------
Module              Restore_count   State
------------------------------------------------------
aclsvcd             0
bgp                 0               disabled
dropmgrd            0
fdbsyncd            0               disabled
gearsyncd           0
ifamgrd             0
intfmgrd            0               disabled
iphelpermgr         0
l2mcmgrd            0
natsyncd            0
nbrmgrd             0
neighsyncd          0
orchagent           0               disabled
portmgrd            0



```
## show watermark interval 

### Description 

```
This command is used to display watermark snapshot interval configured in the system.


```
### Syntax 

```
show watermark interval

```
### Usage Guidelines 

```
Use this command to display watermark snapshot interval configured in the system.


```
### Examples 
#### Show watermark interval 

```
sonic-cli# show watermark interval
           Snapshot interval : 220 seconds


```
## show watermark telemetry 

### Description 

```
This command is used to display watermark telemetry interval configured in the system.


```
### Syntax 

```
show watermark telemetry interval

```
### Usage Guidelines 

```
Use this command to display watermark telemetry interval configured in the system.


```
### Examples 
#### Show watermark telemetry interval 

```
sonic-cli# show watermark telemetry interval
 interval : 220 seconds


```
## show ztp-status 

### Description 

```
Show the status of ZTP operation.


```
### Syntax 

```
show ztp-status

```
### Usage Guidelines 

```
Use this command to show the status of ZTP operation.

These are the possible current states or result of ZTP session:
 - IN-PROGRESS: ZTP session is currently in progress. ZTP service is processing switch provisioning information.
 - SUCCESS: ZTP service has successfully processed the switch provisioning information.
 - FAILED: ZTP service has failed to process the switch provisioning information.
 - Not Started: ZTP service has not started processing the discovered switch provisioning information.

These are the state and result of a configuration section:
 - IN-PROGRESS: Corresponding configuration section is currently being processed.
 - SUCCESS: Corresponding configuration section was processed successfully.
 - FAILED: Corresponding configuration section failed to execute successfully.
 - Not Started: ZTP service has not started processing the corresponding configuration section.
 - DISABLED: Corresponding configuration section has been marked as disabled and will not be processed.



```
### Examples 

```
sonic# show ztp-status
========================================
ZTP
========================================
ZTP Admin Mode      : True
ZTP Service         : Inactive
ZTP Status          : Not Started


```
### Features this CLI belongs to 
*  ZTP


## shutdown 
### Description 
```
This command administratively shutsdown a BGP neighbor. The CLI allows
user to specify a shutdown message that can be communicated to the
neighbor
```
### Parent Commands (Modes) 
```
neighbor { <ip> | { interface { Ethernet | PortChannel | Vlan } } }
```
### Syntax 
```
shutdown [ message <MSG> ]
no shutdown [ message <MSG> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| MSG | String  | String  |
### Usage Guidelines 
```
Use this command to administratively shutdown a BGP neighbor
sessions
```
### Examples 
#### Following command shutdown a neighbor 30.30.30.3 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 30.30.30.3
sonic(config-router-bgp-neighbor)# shutdown
```
## shutdown 
### Description 
```
This command administratively shutsdown a BGP peer-group. The CLI allows
user to specify a shutdown message that can be communicated to the
neighbors in peer-group
```
### Parent Commands (Modes) 
```
peer-group <template-str>
```
### Syntax 
```
shutdown [ message <MSG> ]
no shutdown [ message <MSG> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| MSG | String  | String  |
### Usage Guidelines 
```
Use this command to administratively shutdown a BGP peer-group
sessions
```
### Examples 
#### Following command shutdown a peer-group PG_Ext 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# shutdown
```
## shutdown 
### Description 
```
Shut Bidirectional Forwarding detection(BFD) peer administratively down.
```
### Parent Commands (Modes) 
```
peer <peer_ipv4>
peer <peer_ipv6>
peer [ interface ] <interfacename>
peer [ local-address ] <local_ipv4>
peer [ local-address ] <local_ipv6>
peer [ multihop ]
peer [ vrf ] <vrfname>
```
### Syntax 
```
shutdown
no shutdown
```
### Usage Guidelines 
```
This command will change the BFD session state to DOWN.
```
### Examples 
#### Shut BFD peer down 
```
device()#configure terminal
device(config)#bfd
device(conf-bfd)# peer 192.168.0.5 interface Ethernet0
device(conf-bfd-peer)# shutdown
```
## shutdown 
### Description 
```
Disable the interface 
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
shutdown
no shutdown
```
## shutdown 
### Description 
```
Disable the interface 
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
shutdown
no shutdown
```
## shutdown 
### Description 
```
Disable the interface 
```
### Parent Commands (Modes) 
```
interface Management <mgmt-if-id>
```
### Syntax 
```
shutdown
no shutdown
```
## shutdown 
### Description 
```
Disable the interface 
```
### Parent Commands (Modes) 
```
interface range iface_range_num
```
### Syntax 
```
shutdown
no shutdown
```
## shutdown 
### Description 
```
Disable the interface 
```
### Parent Commands (Modes) 
```
interface range create po_range_num { [ mode <PoMode> ] } { [ min-links <min-links-value> ] } [ fallback ] [ fast_rate ]
interface range po_range_num
```
### Syntax 
```
shutdown
no shutdown
```
## snmp-server agentaddress 
### Description 
```
Configure one or more SNMP agent addresses and optionally set the UDP port number on
which the SNMP server listens for requests, and the Virtual Routing and Forwarding (VRF)
interface used by the management station to access SNMP. The default UDP port is 161.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
snmp-server agentaddress <host-addr> { { [ port <udp-port> ] } { [ interface <ifname> ] } }
no snmp-server agentaddress <host-addr> { { [ port <udp-port> ] } { [ interface <ifname> ] } }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| host-addr | A.B.C.D or A:B:C:D:E:F:G:H  | String  |
| udp-port |   | Integer  |
| ifname | String  | String  |
### Usage Guidelines 
```
Use this command to configure an SNMP agent address, UDP port number, and VRF interface.
```
### Examples 
#### Add an SNMP agent address using the default UDP port number. 
```
sonic# configure terminal
sonic(config)# snmp-server agentaddress 1.2.3.4
```
#### Add an SNMP agent address specifying a non-default UDP port number. 
```
sonic# configure terminal
sonic(config)# snmp-server agentaddress 1.2.3.4 port 1024
```
#### Add an SNMP agent address specifying a non-default UDP port number and a VRF interface. 
```
sonic# configure terminal
sonic(config)# snmp-server agentaddress 1.2.3.5 port 1024 interface Vrf1
```
## snmp-server community 
### Description 
```
Configure one or more SNMP communities and optionally associate them with a group.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
snmp-server community <community-name> { [ group <group-name> ] }
no snmp-server community <community-name>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| community-name | String(Max: 32 characters)  | String  |
| group-name | String(Max: 32 characters)  | String  |
### Usage Guidelines 
```
Use this command to configure an SNMP community and group.
```
### Examples 
#### Add an SNMP community without specifying a group. 
```
sonic# configure terminal
sonic(config)# snmp-server community comm1
```
#### Add an SNMP community and associate it with a group. 
```
sonic# configure terminal
sonic(config)# snmp-server community comm1 group group-lab
```
## snmp-server contact 
### Description 
```
Configure the contact information about the organization responsible for the network.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
snmp-server contact <contact-name>
no snmp-server contact
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| contact-name | String  | String  |
### Usage Guidelines 
```
Use this command to configure the SNMP server contact information.
```
### Examples 
```
sonic# configure terminal
sonic(config)# snmp-server contact "Broadcom Support"
```
## snmp-server enable 
### Description 
```
Enable the SNMP authentication flag on the switch. The flag is disabled by default.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
snmp-server enable trap
no snmp-server enable trap
```
### Usage Guidelines 
```
Use this command to configure the SNMP authentication flag.
```
### Examples 
```
sonic# configure terminal
sonic(config)# snmp-server enable trap
```
## snmp-server engine 
### Description 
```
Configure the SNMP engine identification on the local device. It is a hexadecimal
string used for localizing configuration. The default engine ID is derived from
the device MAC address.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
snmp-server engine <engineID>
no snmp-server engine
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| engineID | Octet (hex) string, 5-32 octets  | String  |
### Usage Guidelines 
```
Use this command to configure the SNMP engine ID.
```
### Examples 
```
sonic# configure terminal
sonic(config)# snmp-server engine 8100013703525400abcd
```
## snmp-server group 
### Description 
```
Configure one or more SNMPv2c and SNMPv3 access groups and optionally set the views which
the group uses for the GET/SET requests and to send traps. Authentication and privacy can be
set for SNMPv3 groups only. Groups are used when configuring SNMP communities and users.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
snmp-server group <group-name> { { any | v2c | { v3 { noauth | auth | priv } } } { [ read <read-view> ] } { [ write <write-view> ] } { [ notify <notify-view> ] } }
no snmp-server group <group-name> { any | v2c | { v3 { noauth | auth | priv } } }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| group-name | String(Max: 32 characters)  | String  |
| read-view | String(Max: 32 characters)  | String  |
| write-view | String(Max: 32 characters)  | String  |
| notify-view | String(Max: 32 characters)  | String  |
### Usage Guidelines 
```
Use this command to configure an SNMP access group.
```
### Examples 
#### Add an SNMP group that can only access via SNMPv2c without associating it with a view. 
```
sonic# configure terminal
sonic(config)# snmp-server group group1 v2c
```
#### Add an SNMP group that can only access via SNMPv2c associating it with a view for only sending out traps. 
```
sonic# configure terminal
sonic(config)# snmp-server group group1 v2c notify no_view
```
#### Add an SNMP group that can only access via SNMPv3 specifying the security level and without associating it with a view. 
```
sonic# configure terminal
sonic(config)# snmp-server group group-floor2 v3 priv
```
#### Add an SNMP group that can only access via SNMPv3 associating it with the views for the GET/SET requests and sending out traps. 
```
sonic# configure terminal
sonic(config)# snmp-server group group-floor2 v3 priv read r_view write w_view notify n_view
```
## snmp-server host 
### Description 
```
Configure one or more SNMP IPv4 or IPv6 hosts to which the trap or inform messages are
sent to by the SNMP agent. Optionally set the timeout and number of retries for the inform
messages sent to a host. Timeout indicates the number of seconds before the informs time out
when sending to a host. Retries indicate the number of times the informs are sent after timing out.
The default UDP destination port traps and inform messages are sent to is 162. This may be
modified using the port parameter. The outging interface may also be specifed using the interface
parameter. A valid system interface must be specified.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
snmp-server host <host-addr> { { community { <community-name> { { [ traps v2c ] } | { [ informs { [ timeout <time-out> ] } { [ retries <retry> ] } ] } ] } { [ interface <ifname> ] } { [ port <udpPort> ] } } } | { user { <username> { { [ traps { noauth | auth | priv } ] } | { [ informs { noauth | auth | priv } { [ timeout <time-out> ] } { [ retries <retry> ] } ] } ] } } { [ interface <ifname> ] } { [ port <udpPort> ] } } }
no snmp-server host <host-addr>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| host-addr | A.B.C.D or A:B:C:D:E:F:G:H  | String  |
| community-name | String(Max: 32 characters)  | String  |
| time-out |   | Integer  |
| retry |   | Integer  |
| ifname | String  | String  |
| udpPort |   | Integer  |
| username | String(Max: 32 characters)  | String  |
### Usage Guidelines 
```
Use this command to configure an SNMP host.
```
### Examples 
#### Add an SNMP IPv4 host community to receive SNMPv2c traps. 
```
sonic# configure terminal
sonic(config)# snmp-server host 1.2.3.4 community comm1 traps v2c
```
#### Add an SNMP IPv4 host user to receive SNMPv3 informs without authentication specifying the timeout and number of retries. 
```
sonic# configure terminal
sonic(config)# snmp-server host 1.2.3.5 user user1 informs noauth timeout 200 retries 10
```
#### Add an SNMP IPv6 host community to receive SNMPv2c informs without authentication specifying the timeout and number of retries. 
```
sonic# configure terminal
sonic(config)# snmp-server host 2001::1 community comm2 informs timeout 150 retries 5
```
#### Add an SNMP IPv6 host user to receive SNMPv3 traps with authentication and privacy. 
```
sonic# configure terminal
sonic(config)# snmp-server host 3001::1 user u1 traps priv
```
#### Add an SNMP IPv4 host community to receive SNMPv2c traps on UDP port 1492 sent out a VRF interface. 
```
sonic# configure terminal
sonic(config)# snmp-server host 2.3.4.5 community public traps v2c port 1492 interface Vrf1
```
## snmp-server location 
### Description 
```
Configure the physical location of the switch.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
snmp-server location <location-name>
no snmp-server location
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| location-name | String  | String  |
### Usage Guidelines 
```
Use this command to configure the SNMP server location information.
```
### Examples 
```
sonic# configure terminal
sonic(config)# snmp-server location "Lab1, Rack-10"
```
## snmp-server user 
### Description 
```
Configure one or more SNMPv3 users and optionally set the authentication and group
association. Authentication passwords can be encrypted. If password encryption is desired,
it must be specified prior to setting the authentication type.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
snmp-server user <username> { { [ group <group-name> ] } { { [ encrypted { auth { { md5 { auth-password { <authpassword> { [ priv { { des { priv-password <privpassword> } } | { aes-128 { priv-password <privpassword> } } } ] } } } } | { sha { auth-password { <authpassword> { [ priv { { des { priv-password <privpassword> } } | { aes-128 { priv-password <privpassword> } } } ] } } } } } } ] } | { [ auth { noauth | { md5 { auth-password { <authpassword> { [ priv { { des { priv-password <privpassword> } } | { aes-128 { priv-password <privpassword> } } } ] } } } } | { sha { auth-password { <authpassword> { [ priv { { des { priv-password <privpassword> } } | { aes-128 { priv-password <privpassword> } } } ] } } } } } ] } ] } }
no snmp-server user <username>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| username | String(Max: 32 characters)  | String  |
| group-name | String(Max: 32 characters)  | String  |
| authpassword | 16-byte hex string  | String  |
| privpassword | 16-byte hex string  | String  |
### Usage Guidelines 
```
Use this command to configure an SNMPv3 user.
```
### Examples 
#### Add an SNMPv3 user without authentication and group association. 
```
sonic# configure terminal
sonic(config)# snmp-server user user1
```
#### Add an SNMPv3 user without authentication associating it with a group. 
```
sonic# configure terminal
sonic(config)# snmp-server user user1 group group-lab
```
#### Add an SNMPv3 user specifying authentication and group association. 
```
sonic# configure terminal
sonic(config)# snmp-server user user1 group group-lab auth md5 auth-password pwd priv aes-128 priv-password pwd
```
#### Add an SNMPv3 user specifying encrypted authentication and group association. 
```
sonic# configure terminal
sonic(config)# snmp-server user user2 group group-floor2 encrypted auth sha auth-password abcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcd priv des priv-password abcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcdabcd
```
## snmp-server view 
### Description 
```
Configure one or more SNMP views and set the OID tree to include or exclude from the view.
SNMP views are used by the groups for the GET/SET requests and to send traps.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
snmp-server view <view-name> { <oid-tree> { included | excluded } }
no snmp-server view <view-name> <oid-tree>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| view-name | String(Max: 32 characters)  | String  |
| oid-tree | OID(Max: 255 characters)  | String  |
### Usage Guidelines 
```
Use this command to configure a SNMP view.
```
### Examples 
#### Add an SNMP view that excludes the OID tree. 
```
sonic# configure terminal
sonic(config)# snmp-server view view2 1.2.3.4.5.6.7.8.9.2 excluded
```
## soft-reconfiguration 
### Description 
```
This command enables soft-reconfiguration for a BGP neighbor
```
### Parent Commands (Modes) 
```
address-family l2vpn evpn
```
### Syntax 
```
soft-reconfiguration inbound
no soft-reconfiguration inbound
```
### Usage Guidelines 
```
Use this command to store routes received (RIB-In) from a BGP neighbor.
These stored routes could be used to refresh the Loc-RIB in future as
needed. If inbound policy changes, these stored routes will be used to
generate LocRIB after applying the modified inbound policy.
```
### Examples 
#### Following command enables soft reconfiguration for neighbor 20.20.20.2 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 20.20.20.2
sonic(config-router-bgp-neighbor)# remote-as 300
sonic(config-router-bgp-neighbor)# address-family l2vpn evpn
sonic(config-router-bgp-neighbor-af)# soft-reconfiguration
```
## soft-reconfiguration 
### Description 
```
This command enables soft-reconfiguration for BGP neighbors in a
peer-group
```
### Parent Commands (Modes) 
```
address-family l2vpn evpn
```
### Syntax 
```
soft-reconfiguration inbound
no soft-reconfiguration inbound
```
### Usage Guidelines 
```
Use this command to store routes received (RIB-In) from BGP neighbors in
a peer-group. These stored routes could be used to refresh the Loc-RIB in
future as needed. If inbound policy changes, these stored routes will be used to
generate LocRIB after applying the modified inbound policy.
```
### Examples 
#### Following command enables soft reconfiguration for          neighbors in a peer-group 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Int
sonic(config-router-bgp-pg)# address-family l2vpn evpn
sonic(config-router-bgp-pg-af)# soft-reconfiguration
```
## soft-reconfiguration 
### Description 
```
Per neighbor soft reconfiguration 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
soft-reconfiguration inbound
no soft-reconfiguration inbound
```
## soft-reconfiguration 
### Description 
```
Per neighbor soft reconfiguration 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
soft-reconfiguration inbound
no soft-reconfiguration inbound
```
## soft-reconfiguration 
### Description 
```
This command enables soft-reconfiguration for a BGP neighbor
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
soft-reconfiguration inbound
no soft-reconfiguration inbound
```
### Usage Guidelines 
```
Use this command to store routes received (RIB-In) from a BGP neighbor.
These stored routes could be used to refresh the Loc-RIB in future as
needed. If inbound policy changes, these stored routes will be used to
generate LocRIB after applying the modified inbound policy.
```
### Examples 
#### Following command enables soft reconfiguration for neighbor 20.20.20.2 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 20.20.20.2
sonic(config-router-bgp-neighbor)# remote-as 300
sonic(config-router-bgp-neighbor)# address-family ipv4 unicast
sonic(config-router-bgp-neighbor-af)# soft-reconfiguration
```
## soft-reconfiguration 
### Description 
```
This command enables soft-reconfiguration for BGP neighbors in a
peer-group
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
soft-reconfiguration inbound
no soft-reconfiguration inbound
```
### Usage Guidelines 
```
Use this command to store routes received (RIB-In) from BGP neighbors in
a peer-group. These stored routes could be used to refresh the Loc-RIB in
future as needed. If inbound policy changes, these stored routes will be used to
generate LocRIB after applying the modified inbound policy.
```
### Examples 
#### Following command enables soft reconfiguration for          neighbors in a peer-group 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Int
sonic(config-router-bgp-pg)# address-family ipv4 unicast
sonic(config-router-bgp-pg-af)# soft-reconfiguration
```
## solo 
### Description 
```
This command is used to indicate that routes advertised by the peer
should not be reflected back to the peer. This command is only
meaningful when there is a single peer defined in the peer-group.
```
### Parent Commands (Modes) 
```
neighbor { <ip> | { interface { Ethernet | PortChannel | Vlan } } }
```
### Syntax 
```
solo
no solo
```
### Usage Guidelines 
```
Use this command to set a neighbor solo
```
### Examples 
#### Following command configures BGP neighbor          30.30.30.3 as solo 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 30.30.30.3
sonic(config-router-bgp-neighbor)# solo
```
## solo 
### Description 
```
This command is used to indicate that routes advertised by the peer
should not be reflected back to the peer. This command is only
meaningful when there is a single peer defined in the peer-group.
```
### Parent Commands (Modes) 
```
peer-group <template-str>
```
### Syntax 
```
solo
no solo
```
### Usage Guidelines 
```
Use this command to set a peer-group solo
```
### Examples 
#### Following command configures BGP peer-group          PG_Ext as solo 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# solo
```
## source-address 
### Description 
```
Configure source IP address for an IP SLA ICMP instance
```
### Parent Commands (Modes) 
```
icmp-echo <addr>
```
### Syntax 
```
source-address <addr>
no source-address
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| addr | String  | String  |
### Examples 
#### Configure source IP address for an IP SLA ICMP instance 
```
sonic(config)# ip sla 10
sonic(conf-ipsla-10)# icmp-echo 30.30.1.2
sonic(conf-ipsla-10-icmp)# source-address 30.30.1.1
```
## source-address 
### Description 
```
Configure source IP address for an IP SLA TCP instance
```
### Parent Commands (Modes) 
```
tcp-connect <addr> port <portno>
```
### Syntax 
```
source-address <addr>
no source-address
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| addr | String  | String  |
### Examples 
#### Configure source IP address for an IP SLA TCP instance 
```
sonic(config)# ip sla 10
sonic(conf-ipsla-10)# tcp-connect 40.40.1.2
sonic(conf-ipsla-10-tcp)# source-address 40.40.1.1
```
## source-interface 
### Description 
```
Sets source address 
```
### Parent Commands (Modes) 
```
icmp-echo <addr>
```
### Syntax 
```
source-interface <intf>
no source-interface
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| intf | String  | String  |
## source-interface 
### Description 
```
Sets source address 
```
### Parent Commands (Modes) 
```
tcp-connect <addr> port <portno>
```
### Syntax 
```
source-interface <intf>
no source-interface
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| intf | String  | String  |
## source-interface Ethernet 

### Description 

```
Configure source interface as Ethernet for an IP SLA ICMP instance


```
### Parent Commands (Modes) 

```
icmp-echo <addr>

```
### Syntax 

```
source-interface Ethernet <iface_num>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| iface_num | <0..65535>  | Integer  |


### Examples 
#### Configure source interface as Ethernet for an IP SLA ICMP instance 

```
sonic(config)# ip sla 10
sonic(conf-ipsla-10)# icmp-echo 30.30.1.2
sonic(conf-ipsla-10-icmp)# source-interface Ethernet10


```
## source-interface Ethernet 

### Description 

```
Configure source interface as Ethernet for an IP SLA TCP instance


```
### Parent Commands (Modes) 

```
tcp-connect <addr> port <portno>

```
### Syntax 

```
source-interface Ethernet <iface_num>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| iface_num | <0..65535>  | Integer  |


### Examples 
#### Configure source interface as Vlan for an IP SLA TCP instance 

```
sonic(config)# ip sla 10
sonic(conf-ipsla-20)# tcp-connect 40.40.1.2
sonic(conf-ipsla-20-tcp)# source-interface Ethernet50


```
## source-interface PortChannel 

### Description 

```
Configure source interface as PortChannel for an IP SLA ICMP instance


```
### Parent Commands (Modes) 

```
icmp-echo <addr>

```
### Syntax 

```
source-interface PortChannel <po-id>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| po-id |   | Integer  |


### Examples 
#### Configure source interface as PortChannel for an IP SLA ICMP instance 

```
sonic(config)# ip sla 10
sonic(conf-ipsla-10)# icmp-echo 30.30.1.2
sonic(conf-ipsla-10-icmp)# source-interface PortChannel50


```
## source-interface PortChannel 

### Description 

```
Configure source interface as PortChannel for an IP SLA TCP instance


```
### Parent Commands (Modes) 

```
tcp-connect <addr> port <portno>

```
### Syntax 

```
source-interface PortChannel <po-id>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| po-id |   | Integer  |


### Examples 
#### Configure source interface as PortChannel for an IP SLA TCP instance 

```
sonic(config)# ip sla 10
sonic(conf-ipsla-20)# tcp-connect 40.40.1.2
sonic(conf-ipsla-20-tcp)# source-interface PortChannel50


```
## source-interface Vlan 

### Description 

```
Configure source interface as Ethernet for an IP SLA ICMP instance


```
### Parent Commands (Modes) 

```
icmp-echo <addr>

```
### Syntax 

```
source-interface Vlan <vlan-id>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vlan-id |   | Integer  |


### Examples 
#### Configure source interface as Vlan for an IP SLA ICMP instance 

```
sonic(config)# ip sla 10
sonic(conf-ipsla-10)# icmp-echo 30.30.1.2
sonic(conf-ipsla-10-icmp)# source-interface Vlan50


```
## source-interface Vlan 

### Description 

```
Configure source interface as Ethernet for an IP SLA TCP instance


```
### Parent Commands (Modes) 

```
tcp-connect <addr> port <portno>

```
### Syntax 

```
source-interface Vlan <vlan-id>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vlan-id |   | Integer  |


### Examples 
#### Configure source interface as Vlan for an IP SLA TCP instance 

```
sonic(config)# ip sla 10
sonic(conf-ipsla-20)# tcp-connect 40.40.1.2
sonic(conf-ipsla-20-tcp)# source-interface Vlan50


```
## source-ip 
### Description 
```
Command to set the source IPv4 address
```
### Parent Commands (Modes) 
```
interface vxlan <vxlan-if-name>
```
### Syntax 
```
source-ip <SIP>
no source-ip
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| SIP | A.B.C.D  | String  |
### Usage Guidelines 
```
(conf-if-vxlan-vtep)# source-ip SOURCEIP
SOURCEIP - source IPv4 address
```
### Examples 
#### configure the source IPv4 address 
```
sonic(config)# interface vxlan vtep1
sonic(conf-if-vxlan-vtep1)# source-ip 1.1.1.1
```
## source-ip 
### Description 
```
Configures MCLAG session's source ip address
```
### Parent Commands (Modes) 
```
mclag domain <mclag-domain-id>
```
### Syntax 
```
source-ip <SIP>
no source-ip
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| SIP | A.B.C.D  | String  |
### Usage Guidelines 
```
Use this command to configure/change MCLAG session's source ip address
```
### Examples 
#### Configures MCLAG session source ip address for MCLAG domain 100 
```
sonic-cli(config-mclag-domain-100)#source-ip 10.1.1.1
```
## source-port 
### Description 
```
Configure source port for an IP SLA TCP instance
```
### Parent Commands (Modes) 
```
tcp-connect <addr> port <portno>
```
### Syntax 
```
source-port <port>
no source-port
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| port |   | Integer  |
### Examples 
#### Configure source port for an IP SLA TCP instance 
```
sonic(config)# ip sla 10
sonic(conf-ipsla-20)# tcp-connect 40.40.1.2
sonic(conf-ipsla-20-tcp)# source-port 200
```
## source-vrf 
### Description 
```
Configure ICMP source VRF
```
### Parent Commands (Modes) 
```
icmp-echo <addr>
```
### Syntax 
```
source-vrf <vrf-name>
no source-vrf
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrf-name | String  | String  |
### Examples 
#### Configure source VRF for an IP SLA ICMP instance 
```
sonic(config)# ip sla 10
sonic(conf-ipsla-10)# icmp-echo 30.30.1.2
sonic(conf-ipsla-10-icmp)# source-vrf VrfRed
```
## source-vrf 
### Description 
```
Configure TCP VRF
```
### Parent Commands (Modes) 
```
tcp-connect <addr> port <portno>
```
### Syntax 
```
source-vrf <vrf-name>
no source-vrf
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrf-name | String  | String  |
### Examples 
#### Configure source VRF for an IP SLA TCP instance 
```
sonic(config)# ip sla 10
sonic(conf-ipsla-20)# tcp-connect 40.40.1.2
sonic(conf-ipsla-20-tcp)# source-vrf VrfBlue
```
## spanning-tree bpdufilter 
### Description 
```
Configure interface specific bpdu-filter parameters.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
spanning-tree bpdufilter { enable | disable }
no spanning-tree bpdufilter
```
### Usage Guidelines 
```
These commands allow configuring interface specific bpdu-filter parameters
```
### Examples 
```
sonic(config)# interface PortChannel 1
sonic(conf-if-po1)# spanning-tree bpdufilter enable
sonic(conf-if-po1)# spanning-tree bpdufilter disable
```
## spanning-tree bpdufilter 
### Description 
```
Configure interface specific bpdu-filter parameters.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
spanning-tree bpdufilter { enable | disable }
no spanning-tree bpdufilter
```
### Usage Guidelines 
```
These commands allow configuring interface specific bpdu-filter parameters
```
### Examples 
```
sonic(config)# interface PortChannel 1
sonic(conf-if-po1)# spanning-tree bpdufilter enable
sonic(conf-if-po1)# spanning-tree bpdufilter disable
```
## spanning-tree bpduguard 
### Description 
```
Configure interface specific bpdu-guard parameters.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
spanning-tree bpduguard [ port-shutdown ]
no spanning-tree bpduguard
```
### Usage Guidelines 
```
This commands allows enabling bpdu-guard on an interface
```
### Examples 
```
sonic(config)# interface PortChannel 1
sonic(conf-if-po1)# spanning-tree bpduguard port-shutdown
```
## spanning-tree bpduguard 
### Description 
```
Configure interface specific bpdu-guard parameters.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
spanning-tree bpduguard [ port-shutdown ]
no spanning-tree bpduguard
```
### Usage Guidelines 
```
This commands allows enabling bpdu-guard on an interface
```
### Examples 
```
sonic(config)# interface PortChannel 1
sonic(conf-if-po1)# spanning-tree bpduguard port-shutdown
```
## spanning-tree cost 
### Description 
```
Configure spanning-tree cost on an interface.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
spanning-tree cost <value>
no spanning-tree cost
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| value |   | Integer  |
### Usage Guidelines 
```
This command allows to configure the port level cost value, range 1 - 200000000
```
### Examples 
```
sonic(config)# interface PortChannel 1
sonic(conf-if-po1)# spanning-tree cost 1000
```
## spanning-tree cost 
### Description 
```
Configure spanning-tree cost on an interface.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
spanning-tree cost <value>
no spanning-tree cost
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| value |   | Integer  |
### Usage Guidelines 
```
This command allows to configure the port level cost value, range 1 - 200000000
```
### Examples 
```
sonic(config)# interface PortChannel 1
sonic(conf-if-po1)# spanning-tree cost 1000
```
## spanning-tree edge-port 
### Description 
```
Configure spanning-tree global level BPDU filter
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
spanning-tree edge-port bpdufilter default
no spanning-tree edge-port bpdufilter default
```
### Usage Guidelines 
```
This command allows configuring global level BPDU filter
```
### Examples 
```
sonic(config)# spanning-tree edge-port bpdufilter default
```
## spanning-tree enable 
### Description 
```
Configure spanning-tree on an interface.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
spanning-tree enable
no spanning-tree enable
```
### Usage Guidelines 
```
This command allows enabling of spanning-tree on an interface
```
### Examples 
```
sonic(config)# interface PortChannel 1
sonic(conf-if-po1)# spanning-tree enable
```
## spanning-tree enable 
### Description 
```
Configure spanning-tree on an interface.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
spanning-tree enable
no spanning-tree enable
```
### Usage Guidelines 
```
This command allows enabling of spanning-tree on an interface
```
### Examples 
```
sonic(config)# interface PortChannel 1
sonic(conf-if-po1)# spanning-tree enable
```
## spanning-tree forward-time 
### Description 
```
Configure the spanning-tree forward delay time.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
spanning-tree forward-time <seconds>
no spanning-tree forward-time
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| seconds |   | Integer  |
### Usage Guidelines 
```
This command allows configuring the forward delay time in seconds (default = 15), range 4-30.
```
### Examples 
```
sonic(config)# spanning-tree forward-time 20
```
## spanning-tree guard 
### Description 
```
Configure the spanning-tree root guard timeout.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
spanning-tree guard root { timeout <value> }
no spanning-tree guard root timeout
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| value |   | Integer  |
### Usage Guidelines 
```
This command allows configuring a root guard timeout value. Once superior BPDUs stop coming on the port, device will wait for a period until root guard timeout before moving the port to forwarding state(default = 30 secs), range 5-600.
```
### Examples 
```
sonic(config)# spanning-tree guard root timeout 10
```
## spanning-tree guard 
### Description 
```
Configure interface specific guard parameters.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
spanning-tree guard <guard>
no spanning-tree guard
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| guard | Guard type  | Select [root loop none ]  |
### Usage Guidelines 
```
This commands allows enabling guard on a port-channel interface
```
### Examples 
```
sonic(config)# interface PortChannel 1
sonic(conf-if-po1)# spanning-tree guard root
sonic(conf-if-po1)# spanning-tree guard loop
```
## spanning-tree guard 
### Description 
```
Configure interface specific guard parameters.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
spanning-tree guard <guard>
no spanning-tree guard
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| guard | Guard type  | Select [root loop none ]  |
### Usage Guidelines 
```
This commands allows enabling guard on a port-channel interface
```
### Examples 
```
sonic(config)# interface PortChannel 1
sonic(conf-if-po1)# spanning-tree guard root
sonic(conf-if-po1)# spanning-tree guard loop
```
## spanning-tree hello-time 
### Description 
```
Configure the spanning-tree hello time value.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
spanning-tree hello-time <seconds>
no spanning-tree hello-time
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| seconds |   | Integer  |
### Usage Guidelines 
```
This command allow configuring the hello interval in secs for transmission of bpdus (default = 2), range 1-10.
```
### Examples 
```
sonic(config)# spanning-tree hello-time 3
```
## spanning-tree link-type 
### Description 
```
Configure spanning-tree link-type on an interface.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
spanning-tree link-type { point-to-point | shared }
no spanning-tree link-type
```
### Usage Guidelines 
```
This command allows setting the link-type of spanning-tree on an interface, link-type can be set to either point-point or shared.
```
### Examples 
```
sonic(config)# interface PortChannel 1
sonic(conf-if-po1)# spanning-tree link-type point-to-point
```
## spanning-tree link-type 
### Description 
```
Configure spanning-tree link-type on an interface.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
spanning-tree link-type { point-to-point | shared }
no spanning-tree link-type
```
### Usage Guidelines 
```
This command allows setting the link-type of spanning-tree on an interface, link-type can be set to either point-point or shared.
```
### Examples 
```
sonic(config)# interface PortChannel 1
sonic(conf-if-po1)# spanning-tree link-type point-to-point
```
## spanning-tree loopguard 
### Description 
```
Configure the spanning-tree loop guard.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
spanning-tree loopguard default
no spanning-tree loopguard default
```
### Usage Guidelines 
```
This command allows to enable loop guard globally on all ports. When loop guard is enabled and if BPDUs are not received on a non-designated port, that port is moved into the STP loop-inconsistent blocking state, instead of the listening / learning / forwarding state.
```
### Examples 
```
sonic(config)# spanning-tree loopguard default
```
## spanning-tree max-age 
### Description 
```
Configure the spanning-tree max-age time value.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
spanning-tree max-age <seconds>
no spanning-tree max-age
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| seconds |   | Integer  |
### Usage Guidelines 
```
This command allows configuring the maximum time to listen for root bridge in seconds (default = 20), range 6-40.
```
### Examples 
```
sonic(config)# spanning-tree max-age 22
```
## spanning-tree mode 
### Description 
```
Configure the global spanning-tree mode for the device
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
spanning-tree mode { [ pvst ] | [ rapid-pvst ] }
no spanning-tree mode
```
### Usage Guidelines 
```
This command allows configuring the spanning-tree mode for the device. There are 2 modes supported - pvst and rapid-pvst.
```
### Examples 
```
sonic(config)#spanning-tree mode pvst
```
## spanning-tree port 
### Description 
```
Configure spanning-tree port type on an interface.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
spanning-tree port type edge
no spanning-tree port [ type ]
```
### Usage Guidelines 
```
This command allows setting edge-port type to an interface
```
### Examples 
```
sonic(config)# interface PortChannel 1
sonic(conf-if-po1)# spanning-tree port type edge
```
## spanning-tree port 
### Description 
```
Configure spanning-tree port type on an interface.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
spanning-tree port type edge
no spanning-tree port [ type ]
```
### Usage Guidelines 
```
This command allows setting edge-port type to an interface
```
### Examples 
```
sonic(config)# interface PortChannel 1
sonic(conf-if-po1)# spanning-tree port type edge
```
## spanning-tree port-priority 
### Description 
```
Configure spanning-tree port priority on an interface.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
spanning-tree port-priority <value>
no spanning-tree port-priority
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| value |   | Integer  |
### Usage Guidelines 
```
This command allows setting port priority value on an interface, range 0 - 240 (default 128)
```
### Examples 
```
sonic(config)# interface PortChannel 1
sonic(conf-if-po1)# spanning-tree port-priority 16
```
## spanning-tree port-priority 
### Description 
```
Configure spanning-tree port priority on an interface.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
spanning-tree port-priority <value>
no spanning-tree port-priority
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| value |   | Integer  |
### Usage Guidelines 
```
This command allows setting port priority value on an interface, range 0 - 240 (default 128)
```
### Examples 
```
sonic(config)# interface PortChannel 1
sonic(conf-if-po1)# spanning-tree port-priority 16
```
## spanning-tree portfast 
### Description 
```
Configure the spanning-tree portfast.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
spanning-tree portfast default
no spanning-tree portfast default
```
### Usage Guidelines 
```
This command allows to enable portfast globally on all access ports. When portfast is enabled, port transitions from blocking to forwarding state immediately, bypassing the usual 802.1D STP transition states.
```
### Examples 
```
sonic(config)# spanning-tree portfast default
```
## spanning-tree portfast 
### Description 
```
Configure spanning-tree portfast on an interface.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
spanning-tree portfast
no spanning-tree portfast
```
### Usage Guidelines 
```
This command allows enabling portfast on an interface, portfast allows edge ports to move to forwarding state quickly when the connected device is not participating in spanning-tree.
```
### Examples 
```
sonic(config)# interface PortChannel 1
sonic(conf-if-po1)# spanning-tree portfast
```
## spanning-tree portfast 
### Description 
```
Configure spanning-tree portfast on an interface.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
spanning-tree portfast
no spanning-tree portfast
```
### Usage Guidelines 
```
This command allows enabling portfast on an interface, portfast allows edge ports to move to forwarding state quickly when the connected device is not participating in spanning-tree.
```
### Examples 
```
sonic(config)# interface PortChannel 1
sonic(conf-if-po1)# spanning-tree portfast
```
## spanning-tree priority 
### Description 
```
Configure the global level spanning-tree bridge priority value.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
spanning-tree priority <value>
no spanning-tree priority
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| value |   | Integer  |
### Usage Guidelines 
```
This command allows configuring the bridge priority in increments of 4096, range 0-61440
```
### Examples 
```
sonic(config)# spanning-tree priority 4096
```
## spanning-tree uplinkfast 
### Description 
```
Configure spanning-tree uplink fast on an interface.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
spanning-tree uplinkfast
no spanning-tree uplinkfast
```
### Usage Guidelines 
```
Uplink fast feature enhances STP performance for switches with redundant uplinks. Using the default value for the standard STP forward delay,
convergence following a transition from an active link to a redundant link can take 30 seconds (15 seconds for listening and an
additional 15 seconds for learning).
When uplink fast is configured on the redundant uplinks, it reduces the convergence time to just one second by moving to forwarding
state (bypassing listening and learning modes) in just once second when the active link goes down.
```
### Examples 
```
sonic(config)# interface PortChannel 1
sonic(conf-if-po1)# spanning-tree uplinkfast
```
## spanning-tree uplinkfast 
### Description 
```
Configure spanning-tree uplink fast on an interface.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
spanning-tree uplinkfast
no spanning-tree uplinkfast
```
### Usage Guidelines 
```
Uplink fast feature enhances STP performance for switches with redundant uplinks. Using the default value for the standard STP forward delay,
convergence following a transition from an active link to a redundant link can take 30 seconds (15 seconds for listening and an
additional 15 seconds for learning).
When uplink fast is configured on the redundant uplinks, it reduces the convergence time to just one second by moving to forwarding
state (bypassing listening and learning modes) in just once second when the active link goes down.
```
### Examples 
```
sonic(config)# interface PortChannel 1
sonic(conf-if-po1)# spanning-tree uplinkfast
```
## spanning-tree vlan 
### Description 
```
Configure spanning-tree parameters on per VLAN basis
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
spanning-tree vlan <vlan-range> { { [ forward-time <seconds> ] } | { [ hello-time <seconds> ] } | { [ max-age <seconds> ] } | { [ priority <value> ] } } ]
no spanning-tree vlan <vlan-range> { [ forward-time ] | [ hello-time ] | [ max-age ] | [ priority ] } ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| vlan-range |   | String  |
| seconds |   | Integer  |
| value |   | Integer  |
### Usage Guidelines 
```
These commands are similar to the global level commands but allow configuring the spanning-tree parameters on per VLAN basis.
```
### Examples 
```
sonic(config)#spanning-tree vlan 100
sonic(config)#spanning-tree vlan 100 forward-time 11
sonic(config)#spanning-tree vlan 100 hello-time 3
sonic(config)#spanning-tree vlan 100 max-age 22
sonic(config)#spanning-tree vlan 100 priority 4096
```
## spanning-tree vlan 
### Description 
```
Configure interface and vlan specific spanning-tree parameters.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
spanning-tree vlan <vlan-range> { { cost <value> } | { port-priority <value> } }
no spanning-tree vlan <vlan-range> { cost | port-priority }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| vlan-range |   | String  |
| value |   | Integer  |
### Usage Guidelines 
```
This commands allows configuring interface and vlan specific spanning-tree parameters.
```
### Examples 
```
sonic(config)# interface PortChannel 1
sonic(conf-if-po1)# spanning-tree vlan 100 cost 1000
sonic(conf-if-po1)# spanning-tree vlan 100 port-priority 16
```
## spanning-tree vlan 
### Description 
```
Configure interface and vlan specific spanning-tree parameters.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
spanning-tree vlan <vlan-range> { { cost <value> } | { port-priority <value> } }
no spanning-tree vlan <vlan-range> { cost | port-priority }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| vlan-range |   | String  |
| value |   | Integer  |
### Usage Guidelines 
```
This commands allows configuring interface and vlan specific spanning-tree parameters.
```
### Examples 
```
sonic(config)# interface PortChannel 1
sonic(conf-if-po1)# spanning-tree vlan 100 cost 1000
sonic(conf-if-po1)# spanning-tree vlan 100 port-priority 16
```
## speed 
### Description 
```
Configure speed 
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
speed <speed>
no speed
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| speed | Port speed  | Select [10(10MBPS) 100(100MBPS) 1000(1GIGE) 10000(10GIGE) 25000(25GIGE) 40000(40GIGE) 100000(100GIGE) ]  |
## speed 
### Description 
```
Configure the speed of a port.
```
### Parent Commands (Modes) 
```
interface Management <mgmt-if-id>
```
### Syntax 
```
speed <speed>
no speed
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| speed | Port speed  | Select [10(10MBPS) 100(100MBPS) 1000(1GIGE) auto(1GIGE) ]  |
### Usage Guidelines 
```
speed speed-in-Mbps
```
### Examples 
#### speed 
```
sonic(conf-if-Ethernet0)# speed 10000
sonic(conf-if-Ethernet0)#
```
## speed 
### Description 
```
Configure speed 
```
### Parent Commands (Modes) 
```
interface range iface_range_num
```
### Syntax 
```
speed <speed>
no speed
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| speed | Port speed  | Select [10(10MBPS) 100(100MBPS) 1000(1GIGE) 10000(10GIGE) 25000(25GIGE) 40000(40GIGE) 100000(100GIGE) ]  |
## ssh-server vrf 
### Description 
```
Enable ssh server on give VRF
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
ssh-server vrf <vrf-name>
no ssh-server vrf <vrf-name>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrf-name | WORD  | String  |
### Examples 
```
sonic(config)# ssh-server vrf Vrf2
sonic(config)#
```
## static 
### Description 
```
Configures static NAT entry 
```
### Parent Commands (Modes) 
```
nat
```
### Syntax 
```
static { { basic <global-ip> <local-ip> [ <natType> ] { [ twice-nat-id <twice-nat-id-value> ] } } | { <natPortType> <global-ip> <global-port> <local-ip> <local-port> [ <natType> ] { [ twice-nat-id <twice-nat-id-value> ] } } }
no static { all | { basic <global-ip> } | { <natPortType> <global-ip> <global-port> } }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| global-ip | A.B.C.D  | String  |
| local-ip | A.B.C.D  | String  |
| natType | NAT type  | Select [snat dnat ]  |
| twice-nat-id-value |   | Integer  |
| natPortType | NAT port type  | Select [tcp udp ]  |
| global-port |   | Integer  |
| local-port |   | Integer  |
## storm-control broadcast 
### Description 
```
Command to enable broadcast storm-control on the given interface.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
storm-control broadcast <kbps>
no storm-control broadcast
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| kbps | kbps  | Integer  |
### Usage Guidelines 
```
sonic-cli(conf-if-Ethernet0)# storm-control broadcast 10000
```
### Examples 
#### storm-control broadcast 10000 
```
sonic-cli# configure terminal
sonic-cli(config)# interface Ethernet 0
sonic-cli(conf-if-Ethernet0)# storm-control broadcast 10000
```
## storm-control unknown-multicast 
### Description 
```
Command to enable unknown-multicast storm-control on the given interface.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
storm-control unknown-multicast <kbps>
no storm-control unknown-multicast
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| kbps | kbps  | Integer  |
### Usage Guidelines 
```
sonic-cli(conf-if-Ethernet0)# storm-control unknown-multicast 30000
```
### Examples 
#### storm-control unknown-multicast 30000 
```
sonic-cli# configure terminal
sonic-cli(config)# interface Ethernet 0
sonic-cli(conf-if-Ethernet0)# storm-control unknown-multicast 30000
```
## storm-control unknown-unicast 
### Description 
```
Command to enable unknown-unicast storm-control on the given interface.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
storm-control unknown-unicast <kbps>
no storm-control unknown-unicast
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| kbps | kbps  | Integer  |
### Usage Guidelines 
```
sonic-cli(conf-if-Ethernet0)# storm-control unknown-unicast 20000
```
### Examples 
#### storm-control unknown-unicast 20000 
```
sonic-cli# configure terminal
sonic-cli(config)# interface Ethernet 0
sonic-cli(conf-if-Ethernet0)# storm-control unknown-unicast 20000
```
## strict-capability-match 
### Description 
```
This command instructs BGP to strictly compare remote capabilities and
local capabilities. If capabilities are different, send Unsupported
Capability error then reset connection.
```
### Parent Commands (Modes) 
```
neighbor { <ip> | { interface { Ethernet | PortChannel | Vlan } } }
```
### Syntax 
```
strict-capability-match
no strict-capability-match
```
### Usage Guidelines 
```
Use this command for a BGP neighbor to enforce exact matching of sent
and received capabilities
```
### Examples 
#### Following command enables strict capability match          for BGP neighbor 30.30.30.3 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 30.30.30.3
sonic(config-router-bgp-neighbor)# strict-capability-match
```
## strict-capability-match 
### Description 
```
This command instructs BGP to strictly compare remote capabilities and
local capabilities. If capabilities are different, send Unsupported
Capability error then reset connection.
```
### Parent Commands (Modes) 
```
peer-group <template-str>
```
### Syntax 
```
strict-capability-match
no strict-capability-match
```
### Usage Guidelines 
```
Use this command for a BGP peer-group to enforce exact matching of sent
and received capabilities
```
### Examples 
#### Following command enables strict capability match          for BGP peer-group PG_Ext 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# strict-capability-match
```
## switch-id 
### Description 
```
This command configures a 32-bit identifier that uniquely identifies the switch, and is used in the telemetry reports.
```
### Parent Commands (Modes) 
```
tam
```
### Syntax 
```
switch-id <id>
no switch-id
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| id | 1-4294967295  | Integer  |
### Usage Guidelines 
```
This command configures a 32-bit identifier that uniquely identifies the switch, and is used in the telemetry reports. When not configured, the last 16-bits from the system mac address are used.
```
### Examples 
#### switch-id <number> 
```
sonic# configure terminal
sonic(config)# tam
sonic(config-tam)# switch-id 1234
sonic(config-tam)# exit
sonic(config)# exit
sonic# show tam switch
TAM Device information
----------------------
Switch ID      : 1234
Enterprise ID  : 4312
sonic#
```
## switch-resource 

### Description 

```
Switch Resource Configuration 


```
### Parent Commands (Modes) 

```
configure terminal

```
### Syntax 

```
switch-resource

```
## switchport access 
### Description 
```
Set access mode characteristics of the interface 
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
switchport access Vlan <vlan-id>
no switchport access Vlan
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| vlan-id |   | Integer  |
## switchport access 
### Description 
```
Set access mode characteristics of the interface 
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
switchport access Vlan <vlan-id>
no switchport access Vlan
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| vlan-id |   | Integer  |
## switchport access 
### Description 
```
Set access mode characteristics of the interface 
```
### Parent Commands (Modes) 
```
interface range iface_range_num
```
### Syntax 
```
switchport access Vlan <vlan-id>
no switchport access Vlan
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| vlan-id |   | Integer  |
## switchport access 
### Description 
```
Set access mode characteristics of the interface 
```
### Parent Commands (Modes) 
```
interface range create po_range_num { [ mode <PoMode> ] } { [ min-links <min-links-value> ] } [ fallback ] [ fast_rate ]
interface range po_range_num
```
### Syntax 
```
switchport access Vlan <vlan-id>
no switchport access Vlan
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| vlan-id |   | Integer  |
## switchport trunk 
### Description 
```
Configure trunking parameters on an interface.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
switchport trunk allowed { Vlan { { add <vlan_id_list> } | { remove <vlan_id_list> } } }
no switchport trunk allowed { Vlan <vlan_id_list> }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| vlan_id_list |   | String  |
### Usage Guidelines 
```
Use this command to add or remove trunking parameters on an interface.
```
### Examples 
#### Add trunking parameter on an interface 
```
sonic(conf-if-Ethernet4)# switchport trunk allowed Vlan add 10
sonic(conf-if-Ethernet4)#
```
#### Remove trunking parameter on an interface 
```
sonic(conf-if-Ethernet4)# switchport trunk allowed Vlan remove 10
sonic(conf-if-Ethernet4)#
```
## switchport trunk 
### Description 
```
Configure trunking parameters on an interface.
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
switchport trunk allowed { Vlan { { add <vlan_id_list> } | { remove <vlan_id_list> } } }
no switchport trunk allowed { Vlan <vlan_id_list> }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| vlan_id_list |   | String  |
### Usage Guidelines 
```
Use this command to add or remove trunking parameters on an interface.
```
### Examples 
#### Add trunking parameter on an interface 
```
sonic(conf-if-po1)# switchport trunk allowed Vlan add 10
sonic(conf-if-po1)#
```
#### Remove trunking parameter on an interface 
```
sonic(conf-if-po1)# switchport trunk allowed Vlan remove 10
sonic(conf-if-po1)#
```
## switchport trunk 
### Description 
```
Configure trunking parameters on an interface 
```
### Parent Commands (Modes) 
```
interface range iface_range_num
```
### Syntax 
```
switchport trunk allowed { Vlan { { add <vlan_id_list> } | { remove <vlan_id_list> } } }
no switchport trunk allowed { Vlan <vlan_id_list> }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| vlan_id_list |   | String  |
## switchport trunk 
### Description 
```
Configure trunking parameters on an interface 
```
### Parent Commands (Modes) 
```
interface range create po_range_num { [ mode <PoMode> ] } { [ min-links <min-links-value> ] } [ fallback ] [ fast_rate ]
interface range po_range_num
```
### Syntax 
```
switchport trunk allowed { Vlan { { add <vlan_id_list> } | { remove <vlan_id_list> } } }
no switchport trunk allowed { Vlan <vlan_id_list> }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| vlan_id_list |   | String  |
## swsslog 

### Description 

```
SONiC logging severity level setting 


```
### Parent Commands (Modes) 

```
configure terminal

```
### Syntax 

```
swsslog loglevel { <loglevel_value> { component <component_name> { [ sai_component <is_sai> ] } } }

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| loglevel_value | String  | String  |
| component_name | String  | String  |
| is_sai |   | Select [yes(yes) no(no) ]  |


## table-map 
### Description 
```
BGP table to RIB route download filter 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
table-map <rtmap>
no table-map [ <rtmap> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| rtmap | WORD  | String  |
## table-map 
### Description 
```
This command enables user to apply a route-map on route updates from BGP
to Zebra
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
table-map <rtmap>
no table-map [ <rtmap> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| rtmap | WORD  | String  |
### Usage Guidelines 
```
This command enables user to apply a route-map on route updates from BGP to
Zebra (RIB manager). All the applicable match operations are allowed, such as match on
prefix, next-hop, communities, etc. Set operations for this attach-point
are limited to metric and next-hop only. Any operation of this feature
does not affect BGPs internal RIB.
```
### Examples 
#### Following example configures a table-map rmap_block_private 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# address-family ipv4 unicast
sonic(config-router-bgp-af)# table-map rmap_block_private
```
## tacacs-server auth-type 
### Description 
```
Configure global authentication type for TACACS 
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
tacacs-server auth-type <auth-type>
no tacacs-server auth-type
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| auth-type |   | Select [pap(pap) chap(chap) mschap(mschap) login(login) ]  |
## tacacs-server host 
### Description 
```
Configure a server for TACACS 
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
tacacs-server host <host> [ port <port-val> ] [ timeout <timeout-val> ] [ key <key-val> ] [ type <type-val> ] [ priority <priority-val> ] [ vrf { mgmt } ]
no tacacs-server host <address>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| host | WORD  | String  |
| port-val | port  | Integer  |
| timeout-val | seconds  | Integer  |
| key-val | (Valid Chars: ASCII printable except SPACE, #, and COMMA, Max Len: 65) shared secret  | String  |
| type-val |   | Select [pap(pap) chap(chap) mschap(mschap) login(login) ]  |
| priority-val | (1..64)  | Integer  |
## tacacs-server key 
### Description 
```
Configure global shared secret for TACACS 
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
tacacs-server key <secret-key>
no tacacs-server key
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| secret-key | (Valid Chars: ASCII printable except SPACE, #, and COMMA, Max Len: 65) shared secret  | String  |
## tacacs-server source-interface 
### Description 
```
Configure source interface to pick the source IP, used for the TACACS+ packets 
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
tacacs-server source-interface { Ethernet | Loopback | Management | PortChannel | Vlan }
no tacacs-server source-interface
```
## tacacs-server timeout 
### Description 
```
Configure global timeout for TACACS 
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
tacacs-server timeout <timeout>
no tacacs-server timeout
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| timeout | seconds  | Integer  |
## tail-stamping 

### Description 

```
Configures tail-stamping feature 


```
### Parent Commands (Modes) 

```
tam

```
### Syntax 

```
tail-stamping

```
## tam 

### Description 

```
Telemetry and monitoring configuration 


```
### Parent Commands (Modes) 

```
configure terminal

```
### Syntax 

```
tam

```
## tcp-connect 
### Description 
```
Configure operation type as TCP, destination IP and destination port for an IP SLA instance
```
### Parent Commands (Modes) 
```
ip sla <sla-id>
```
### Syntax 
```
tcp-connect <addr> port <portno>
no tcp-connect
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| addr | String  | String  |
| portno |   | Integer  |
### Examples 
#### Configure operation type as TCP, destination IP and destination port for an IP SLA instance 
```
sonic(config)# ip sla 10
sonic(conf-ipsla-10)# tcp-connect 40.40.1.2 port 100
```
## tcp-timeout 
### Description 
```
Configures TCP NAT entry aging timeout in seconds 
```
### Parent Commands (Modes) 
```
nat
```
### Syntax 
```
tcp-timeout <tcp-timeout-value>
no tcp-timeout
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| tcp-timeout-value |   | Integer  |
## terminal length 

### Description 

```
Set terminal length 


```
### Syntax 

```
terminal length <length>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| length |   | Integer  |


## threshold 
### Description 
```
Configure threshold count for an IP SLA instance
```
### Parent Commands (Modes) 
```
ip sla <sla-id>
```
### Syntax 
```
threshold <threshold-value>
no threshold
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| threshold-value |   | Integer  |
### Examples 
#### Configure threshold count for an IP SLA instance 
```
sonic(config)# ip sla 10
sonic(conf-ipsla-10)# threshold 5
```
## threshold 
### Description 
```
Configure the threshold parameters which can be used to startup or shutdown downstream interfaces.
```
### Parent Commands (Modes) 
```
link state track <grp-name>
```
### Syntax 
```
threshold { { type percentage { [ up <threshold-up> ] } { [ down <threshold-down> ] } } | { up <threshold-up> { [ down <threshold-down> ] } } | { down <threshold-down> } }
no threshold { [ up ] | [ down ] } ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| threshold-up |   | Integer  |
| threshold-down |   | Integer  |
### Usage Guidelines 
```
The threshold up value must be higher than threshold down value. If threshold up is not configured then its assumed as all upstream interface must be online to bringup downstream interfaces. If threshold down is not configured then its assumed as all upstream interfaces must be offline to shutdown downstream interfaces.
```
### Examples 
#### Configure threshold parameters 
```
sonic(config-link-track)# threshold type percentage up 70 down 40
sonic(config-link-track)# threshold up 80
sonic(config-link-track)# threshold down 20
```
## threshold buffer-pool 
### Description 
```
this command is to configure buffer pool threshold on ingress/egress buffers pools-lossy/lossless.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
threshold buffer-pool <buffer_pool_name> <threshold_value>
no threshold buffer-pool
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| buffer_pool_name | WORD  | String  |
| threshold_value |   | Integer  |
### Usage Guidelines 
```
use this command to configure buffer pool threshold on ingress/egress buffer pools-lossy/lossless.
```
### Examples 
#### buffer pool threshold configuration commands. 
```
sonic(config)# threshold buffer-pool ingress_lossless_pool 1
sonic(config)# threshold buffer-pool egress_lossy_pool 1
sonic(config)# threshold buffer-pool egress_lossless_pool 1
```
## threshold priority-group 
### Description 
```
This command is used to configure a threshold for a specific priority-group shared/headroom buffer of an interface. The threshold value is provided in %. Valid values are 1-100.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
threshold priority-group <PG-Index> { headroom | shared } <threshold_value>
no threshold priority-group <PG-Index> { headroom | shared }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| PG-Index |   | Integer  |
| threshold_value |   | Integer  |
### Usage Guidelines 
```
Use this command to configure a threshold for a  specific priority-group shared/headroom buffer of an interface.
```
### Examples 
#### Configure priority-group buffer threshold configuration. 
```
sonic(conf-if-Ethernet0)# threshold priority-group 5 headroom 57
sonic(conf-if-Ethernet0)# threshold priority-group 7 shared 78
```
## threshold queue 
### Description 
```
This command is used to configure a threshold for a specific unicast/multicast queue of an interface. The threshold value is provided in %. Valid values are 1-100..
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
threshold queue <queue_index> { unicast | multicast } <threshold_value>
no threshold queue <queue_index> { unicast | multicast }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| queue_index |   | Integer  |
| threshold_value |   | Integer  |
### Usage Guidelines 
```
Use this command to configure queue threshold for a specific unicast/multicast queue of an interface.
```
### Examples 
#### Configure queue buffer threshold configuration. 
```
sonic(conf-if-Ethernet0)# threshold queue 4 unicast 47
sonic(conf-if-Ethernet0)# threshold queue 2 multicast 67
```
## threshold queue 
### Description 
```
This command is used to configure multicact queue buffer threshold of CPU interface. The threshold value is provided in %. Valid values are 1-100..
```
### Parent Commands (Modes) 
```
interface CPU
```
### Syntax 
```
threshold queue <cpu_queue_index> multicast <threshold_value>
no threshold queue <cpu_queue_index> multicast
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| cpu_queue_index |   | Integer  |
| threshold_value |   | Integer  |
### Usage Guidelines 
```
Use this command to configure multicast queue buffer threshold of CPU interface.
```
### Examples 
#### Configure queue buffer threshold of CPU interface. 
```
sonic(conf-if-CPU)# threshold queue 3 multicast 37
```
## timeout 
### Description 
```
Configure timeout interval for an IP SLA instance
```
### Parent Commands (Modes) 
```
ip sla <sla-id>
```
### Syntax 
```
timeout <timeout-value>
no timeout
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| timeout-value |   | Integer  |
### Examples 
#### Configure timeout interval for an IP SLA instance 
```
sonic(config)# ip sla 10
sonic(conf-ipsla-10)# timeout 30
```
## timeout 
### Description 
```
Set timeout to bring up downstream interfaces when atleast one upstream interface comes online.
```
### Parent Commands (Modes) 
```
link state track <grp-name>
```
### Syntax 
```
timeout <grp-tmout>
no timeout
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| grp-tmout |   | Integer  |
### Examples 
#### Set timeout configuration 
```
sonic(config-link-track)# timeout 120
```
### Alternate command 
#### click 
```
admin@sonic:~$ sudo config linktrack update <name> --timeout <value>
```
## timeout 
### Description 
```
Configures NAT entry aging timeout in seconds 
```
### Parent Commands (Modes) 
```
nat
```
### Syntax 
```
timeout <timeout-value>
no timeout
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| timeout-value |   | Integer  |
## timers 
### Description 
```
This command configures keelapive and hold timer interval (in seconds)
at a global level.
```
### Parent Commands (Modes) 
```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }
```
### Syntax 
```
timers <keepalive-intvl> <hold-time>
no timers <keepalive-intvl> <hold-time>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| keepalive-intvl |   | Integer  |
| hold-time |   | Integer  |
### Usage Guidelines 
```
Use this command to configure keepalive and hold timer interval for an
instnace of BGP. Note that keepalive timer interval must be smaller than
hold timer interval.
```
### Examples 
#### Below command configures Keepalive interval to 10sec and          hold timer interval to 30sec 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# timers 10 30
```
## timers 
### Description 
```
This command enables user to configure keepalive, hold and connect timer
intervals for a BGP neighbor.
```
### Parent Commands (Modes) 
```
neighbor { <ip> | { interface { Ethernet | PortChannel | Vlan } } }
```
### Syntax 
```
timers { { <keepalive-intvl> <hold-time> } | { connect <connect-time> } }
no timers { { <keepalive-intvl> <hold-time> } | { connect <connect-time> } }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| keepalive-intvl |   | Integer  |
| hold-time |   | Integer  |
| connect-time |   | Integer  |
### Usage Guidelines 
```
Use this command to configure keeplaive, hold and connect retry timer
intervals for a BGP neighbor.
```
### Examples 
#### Following command configures keepalive and hold timer          interval for neighbor 30.30.30.3 to 3 and 9 seconds respectively 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 30.30.30.3
sonic(config-router-bgp-neighbor)# timers 3 9
```
## timers 
### Description 
```
This command enables user to configure keepalive, hold and connect timer
intervals for a BGP peer-group.
```
### Parent Commands (Modes) 
```
peer-group <template-str>
```
### Syntax 
```
timers { { <keepalive-intvl> <hold-time> } | { connect <connect-time> } }
no timers { { <keepalive-intvl> <hold-time> } | { connect <connect-time> } }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| keepalive-intvl |   | Integer  |
| hold-time |   | Integer  |
| connect-time |   | Integer  |
### Usage Guidelines 
```
Use this command to configure keeplaive, hold and connect retry timer
intervals for a BGP peer-group.
```
### Examples 
#### Following command configures keepalive and hold timer          interval for peer-group PG_Ext to 3 and 9 seconds respectively 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# timers 3 9
```
## timers 
### Description 
```
Configures OSPFv2 LSA and SPF intervals.
```
### Parent Commands (Modes) 
```
router ospf [ vrf <vrf-name> ]
```
### Syntax 
```
timers { { lsa { min-arrival <minarrivaltimer> } } | { throttle { { lsa { all <lsadelaytimer> } } | { spf <spfdelaytime> <spfinitialholdtime> <spfmaxholdtime> } } } }
no timers { { lsa min-arrival } | { throttle { { lsa all } | spf } } }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| minarrivaltimer |   | Integer  |
| lsadelaytimer |   | Integer  |
| spfdelaytime |   | Integer  |
| spfinitialholdtime |   | Integer  |
| spfmaxholdtime |   | Integer  |
### Usage Guidelines 
```
Use this command to configure OSPFv2 LSA and SPF intervals.
```
### Examples 
#### Configures OSPFv2 LSA and SPF intervals 
```
sonic-cli(config-router-ospf)# timers lsa min-arrival
sonic-cli(config-router-ospf)# timers throttle lsa all
sonic-cli(config-router-ospf)# timers throttle spf 10 20 30
```
### Features this CLI belongs to 
*  OSPFv2
## tos 
### Description 
```
Configure ICMP TOS
```
### Parent Commands (Modes) 
```
icmp-echo <addr>
```
### Syntax 
```
tos <size>
no tos
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| size |   | Integer  |
### Examples 
#### Configure Type-Of-Service for an IP SLA ICMP instance 
```
sonic(config)# ip sla 10
sonic(conf-ipsla-10)# icmp-echo 30.30.1.2
sonic(conf-ipsla-10-icmp)# tos 4
```
## tos 
### Description 
```
Configure TCP TOS
```
### Parent Commands (Modes) 
```
tcp-connect <addr> port <portno>
```
### Syntax 
```
tos <size>
no tos
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| size |   | Integer  |
### Examples 
#### Configure Type-Of-Service for an IP SLA TCP instance 
```
sonic(config)# ip sla 10
sonic(conf-ipsla-20)# tcp-connect 40.40.1.2
sonic(conf-ipsla-20-tcp)# tos 4
```
## tpcm install 

### Description 

```
Install docker image from local file, dcoker... 


```
### Syntax 

```
tpcm install name <container_name> { { [ url <url_path> ] } | { [ image <image_path> ] } | { [ pull <pull_path> ] } | { [ file <file_path> ] } | { [ scp <scp_host> { username <scp_username> } { password <scp_password> } { filename <scp_filename> } ] } | { [ sftp <sftp_host> { username <sftp_username> } { password <sftp_password> } { filename <sftp_filename> } ] } } [ args <_args> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| container_name | String  | String  |
| url_path | String  | String  |
| image_path | String  | String  |
| pull_path | String  | String  |
| file_path | String  | String  |
| scp_host | String  | String  |
| scp_username | String  | String  |
| scp_password | String  | String  |
| scp_filename | String  | String  |
| sftp_host | String  | String  |
| sftp_username | String  | String  |
| sftp_password | String  | String  |
| sftp_filename | String  | String  |
| _args | String  | String  |


## tpcm uninstall 

### Description 

```
Uninstall the TPC docker image 


```
### Syntax 

```
tpcm uninstall name <container_name> [ clean_data <skip> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| container_name | String  | String  |
| skip |   | Select [yes(yes) no(no) ]  |


## tpcm upgrade 

### Description 

```
Upgrade docker image from local, docker hub,... 


```
### Syntax 

```
tpcm upgrade name <container_name> { { [ url <url_path> ] } | { [ image <image_path> ] } | { [ pull <pull_path> ] } | { [ file <file_path> ] } | { [ scp <scp_host> { username <scp_username> } { password <scp_password> } { filename <scp_filename> } ] } | { [ sftp <sftp_host> { username <sftp_username> } { password <sftp_password> } { filename <sftp_filename> } ] } } [ skip_data_migration <skip> ] [ args <_args> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| container_name | String  | String  |
| url_path | String  | String  |
| image_path | String  | String  |
| pull_path | String  | String  |
| file_path | String  | String  |
| scp_host | String  | String  |
| scp_username | String  | String  |
| scp_password | String  | String  |
| scp_filename | String  | String  |
| sftp_host | String  | String  |
| sftp_username | String  | String  |
| sftp_password | String  | String  |
| sftp_filename | String  | String  |
| skip |   | Select [yes(yes) no(no) ]  |
| _args | String  | String  |


## traceroute 

### Description 

```
Print the route packets take to the host 


```
### Syntax 

```
traceroute

```
## traceroute vrf 

### Description 

```
Select VRF instance 


```
### Syntax 

```
traceroute vrf <vrf-name>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrf-name | VRF name (prefixed by Vrf, Max: 15 characters)  | String  |


## traceroute vrf mgmt 

### Description 

```
Traceroute using management VRF 


```
### Syntax 

```
traceroute vrf mgmt

```
## traceroute6 

### Description 

```
Print the route packets take to the IPv6 host 


```
### Syntax 

```
traceroute6

```
## traceroute6 vrf 

### Description 

```
Select VRF instance 


```
### Syntax 

```
traceroute6 vrf <vrf-name>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrf-name | VRF name (prefixed by Vrf, Max: 15 characters)  | String  |


## traceroute6 vrf mgmt 

### Description 

```
Traceroute6 using management VRF 


```
### Syntax 

```
traceroute6 vrf mgmt

```
## track-interface 
### Description 
```
Configure track interface for changing priority of IPv4 VRRP instance
```
### Parent Commands (Modes) 
```
vrrp ipv4
```
### Syntax 
```
track-interface <interface-name> { weight <wt_value> }
no track-interface <interface-name>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| interface-name | String  | String  |
| wt_value |   | Integer  |
### Usage Guidelines 
```
Increases the effective priority by weight value if track interface is up
```
### Examples 
#### Configure track interface for IPv4 VRRP instance 
```
sonic(config)# interface Ethernet4
sonic(conf-if-Ethernet4)#
sonic(conf-if-Ethernet4)# vrrp 1 address-family ipv4
sonic(conf-if-Ethernet4-vrrp-ipv4-1)#track-interface Ethernet12 weight 10
sonic(conf-if-Ethernet4-vrrp-ipv4-1)#track-interface Ethernet13 weight 10
```
## track-interface 
### Description 
```
Configure track interface for changing priority of IPv6 VRRP instance
```
### Parent Commands (Modes) 
```
vrrp ipv6
```
### Syntax 
```
track-interface <interface-name> { weight <wt_value> }
no track-interface <interface-name>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| interface-name | String  | String  |
| wt_value |   | Integer  |
### Usage Guidelines 
```
Increases the effective priority by weight value if track interface is up
```
### Examples 
#### Configure track interface for IPv6 VRRP instance 
```
sonic(config)# interface Ethernet4
sonic(conf-if-Ethernet4)#
sonic(conf-if-Ethernet4)# vrrp 1 address-family ipv6
sonic(conf-if-Ethernet4-vrrp-ipv6-1)#track-interface Ethernet24 weight 10
sonic(conf-if-Ethernet4-vrrp-ipv6-1)#track-interface Ethernet25 weight 20
```
## traffic-class 
### Description 
```
This command to add Traffic class to DOT1P entry in map.
```
### Parent Commands (Modes) 
```
qos map tc-dot1p <name>
```
### Syntax 
```
traffic-class <tc_list> { dot1p <dot1p-val> }
no traffic-class <tc_list>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| tc_list |   | String  |
| dot1p-val |   | Integer  |
### Usage Guidelines 
```
Use this command to add entry to Traffic class to DOT1P map.
```
### Examples 
#### The following command add entry to Traffic class to DOT1P map 
```
sonic# configure terminal
sonic(config)# qos map tc-dot1p tc_dot1p
sonic(conf-tc-dot1p-map-tc_dot1p)# traffic-class 0 dot1p 0
sonic(conf-tc-dot1p-map-tc_dot1p)# traffic-class 1 dot1p 1
sonic(conf-tc-dot1p-map-tc_dot1p)# traffic-class 3 dot1p 2
```
## traffic-class 
### Description 
```
This command to add Traffic class to Priority-Group entry in map.
```
### Parent Commands (Modes) 
```
qos map tc-pg <name>
```
### Syntax 
```
traffic-class <tc_list> { priority-group <pg> }
no traffic-class <tc_list>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| tc_list |   | String  |
| pg |   | Integer  |
### Usage Guidelines 
```
Use this command to add entry to map Traffic class to Priority-Group.
```
### Examples 
#### The following command add entry to map Traffic class to Priority-Group 
```
sonic# configure terminal
sonic(config)# traffic-class 0 priority-group 0
sonic(config)# traffic-class 2 priority-group 0
sonic(config)# traffic-class 1 priority-group 2
```
## traffic-class 
### Description 
```
This command to add Traffic class to Queue entry in map.
```
### Parent Commands (Modes) 
```
qos map tc-queue <name>
```
### Syntax 
```
traffic-class <tc_list> { queue <qid> }
no traffic-class <tc_list>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| tc_list |   | String  |
| qid |   | Integer  |
### Usage Guidelines 
```
Use this command to add entry to map Traffic class to Queue.
```
### Examples 
#### The following command add entry to map Traffic class to Queue 
```
sonic# configure terminal
sonic(config)# traffic-class 0 queue 0
sonic(config)# traffic-class 2 queue 0
sonic(config)# traffic-class 1 queue 2
```
## traffic-class 
### Description 
```
This command to add Traffic class to DSCP entry in map.
```
### Parent Commands (Modes) 
```
qos map tc-dscp <name>
```
### Syntax 
```
traffic-class <tc_list> { dscp <dscp-val> }
no traffic-class <tc_list>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| tc_list |   | String  |
| dscp-val |   | Integer  |
### Usage Guidelines 
```
Use this command to add entry to Traffic class to DSCP map.
```
### Examples 
#### The following command add entry to Traffic class to DSCP map 
```
sonic# configure terminal
sonic(config)# qos map tc-dscp tc_dscp
sonic(conf-tc-dscp-map-tc_dscp)# traffic-class 0 dscp 0
sonic(conf-tc-dscp-map-tc_dscp)# traffic-class 1 dscp 10
sonic(conf-tc-dscp-map-tc_dscp)# traffic-class 3 dscp 29
```
## transmit-interval 

### Description 

```
Configure desired packet transmit interval for Bidirectional Forwarding detection(BFD) peer.


```
### Parent Commands (Modes) 

```
peer <peer_ipv4>
peer <peer_ipv6>
peer [ interface ] <interfacename>
peer [ local-address ] <local_ipv4>
peer [ local-address ] <local_ipv6>
peer [ multihop ]
peer [ vrf ] <vrfname>

```
### Syntax 

```
transmit-interval <transmit_interval>

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| transmit_interval |   | Integer  |


### Usage Guidelines 

```
Default value is 300 milliseconds.


```
### Examples 
#### Configure packet transmit interval 

```
device()#configure terminal
device(config)#bfd
device(conf-bfd)# peer 192.168.0.5 interface Ethernet0
device(conf-bfd-peer)# transmit-interval 200


```
## ttl 
### Description 
```
Configure ICMP TTL
```
### Parent Commands (Modes) 
```
icmp-echo <addr>
```
### Syntax 
```
ttl <size>
no ttl
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| size |   | Integer  |
### Examples 
#### Configure Time-To-Live for an IP SLA ICMP instance 
```
sonic(config)# ip sla 10
sonic(conf-ipsla-10)# icmp-echo 30.30.1.2
sonic(conf-ipsla-10-icmp)# ttl 16
```
## ttl 
### Description 
```
Configure TCP TTL
```
### Parent Commands (Modes) 
```
tcp-connect <addr> port <portno>
```
### Syntax 
```
ttl <size>
no ttl
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| size |   | Integer  |
### Examples 
#### Configure Time-To-Live for an IP SLA TCP instance 
```
sonic(config)# ip sla 10
sonic(conf-ipsla-20)# tcp-connect 40.40.1.2
sonic(conf-ipsla-20-tcp)# ttl 16
```
## ttl-security hops 
### Description 
```
This command enforces Generalized TTL Security Mechanism (GTSM), as
specified in RFC 5082. With this command, only neighbors that are the
specified number of hops away will be allowed to become neighbors. This
command is mutually exclusive with ebgp-multihop.
```
### Parent Commands (Modes) 
```
neighbor { <ip> | { interface { Ethernet | PortChannel | Vlan } } }
```
### Syntax 
```
ttl-security hops <nhops>
no ttl-security hops
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| nhops |   | Integer  |
### Usage Guidelines 
```
Use this command for a BGP neighbor to enable Generalized TTL Security
Mechanism (GTSM). This is for security purposes.
```
### Examples 
#### Following command enables GTSM for BGP neighbor 30.30.30.3 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 30.30.30.3
sonic(config-router-bgp-neighbor)# ttl-security hops 6
```
## ttl-security hops 
### Description 
```
This command enforces Generalized TTL Security Mechanism (GTSM), as
specified in RFC 5082. With this command, only neighbors that are the
specified number of hops away will be allowed to become neighbors. This
command is mutually exclusive with ebgp-multihop.
```
### Parent Commands (Modes) 
```
peer-group <template-str>
```
### Syntax 
```
ttl-security hops <nhops>
no ttl-security hops
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| nhops |   | Integer  |
### Usage Guidelines 
```
Use this command for a BGP peer-group to enable Generalized TTL Security
Mechanism (GTSM). This is for security purposes.
```
### Examples 
#### Following command enables GTSM for BGP peer-group PG_Ext 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# ttl-security hops 8
```
## type 
### Description 
```
scheduler type (strict/dwrr/wrr) 
```
### Parent Commands (Modes) 
```
queue <sequence>
```
### Syntax 
```
type <type>
no type
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| type | Scheduler type Options  | Select [dwrr(DWRR) wrr(WRR) strict(STRICT) ]  |
## udld aggressive 
### Description 
```
Configure UDLD mode to aggressive.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
udld aggressive
no udld aggressive
```
### Usage Guidelines 
```
Use this command to change UDLD mode to aggressive. Default UDLD mode is normal.
```
### Examples 
#### Configure UDLD mode to aggressive 
```
sonic-cli(config)# udld aggressive
```
## udld aggressive 
### Description 
```
Enable aggressive mode of UDLD at interface level.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
udld aggressive
no udld aggressive
```
### Usage Guidelines 
```
Use this command to change UDLD mode to aggressive at interface level. Default UDLD mode is normal.
```
### Examples 
#### Enable aggressive mode of UDLD at interface level 
```
sonic-cli(config)# interface Ethernet 0
sonic-cli(conf-if-Ethernet0)# udld aggressive
```
## udld enable 
### Description 
```
Enable UDLD at global level
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
udld enable
no udld enable
```
### Usage Guidelines 
```
Use this command to enable UDLD globally
```
### Examples 
#### Enable UDLD at global level 
```
sonic-cli(config)# udld enable
```
## udld enable 
### Description 
```
Enable UDLD at interface level.
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
udld enable
no udld enable
```
### Usage Guidelines 
```
Use this command to enable UDLD at interface level
```
### Examples 
#### Enable UDLD at interface level 
```
sonic-cli(config)# interface Ethernet 0
sonic-cli(conf-if-Ethernet0)# udld enable
```
## udld message-time 
### Description 
```
Configure UDLD message time. It is the time interval at which periodic hellos are exchanged.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
udld message-time <msg-time>
no udld message-time
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| msg-time |   | Integer  |
### Usage Guidelines 
```
Use this command to set UDLD message time. Default message time is 1 second.
```
### Examples 
#### Configure UDLD message time 
```
sonic-cli(config)# udld message-time 3
```
## udld multiplier 
### Description 
```
Configure UDLD multiplier value. This multiplier value is used to determine the timeout interval (i.e. message-time x multiplier value) after which UDLD declares the link as Unidirectonal.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
udld multiplier <multiplier>
no udld multiplier
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| multiplier |   | Integer  |
### Usage Guidelines 
```
Use this command to set UDLD multiplier value. Default multiplier value is 3.
```
### Examples 
#### Configure UDLD multiplier value 
```
sonic-cli(config)# udld multiplier 8
```
## udp-timeout 
### Description 
```
Configures UDP NAT entry aging timeout in seconds 
```
### Parent Commands (Modes) 
```
nat
```
### Syntax 
```
udp-timeout <udp-timeout-value>
no udp-timeout
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| udp-timeout-value |   | Integer  |
## unsuppress-map 
### Description 
```
Route-map to selectively unsuppress suppressed routes 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
unsuppress-map <map>
no unsuppress-map <map>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| map | WORD  | String  |
## unsuppress-map 
### Description 
```
Route-map to selectively unsuppress suppressed routes 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
unsuppress-map <map>
no unsuppress-map <map>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| map | WORD  | String  |
## unsuppress-map 
### Description 
```
This command configures a Route-map to selectively unsuppress
suppressed routes
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
unsuppress-map <map>
no unsuppress-map <map>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| map | WORD  | String  |
### Usage Guidelines 
```
Use this command to define a route policy via route-map to unsuppress
suppressed routes.
```
### Examples 
#### Following command configures an unsuppress map for neighbor 20.20.20.2 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 20.20.20.2
sonic(config-router-bgp-neighbor)# remote-as 300
sonic(config-router-bgp-neighbor)# address-family ipv4 unicast
sonic(config-router-bgp-neighbor-af)# unsuppress-map rm_unsup_ext_rt
```
## unsuppress-map 
### Description 
```
This command configures a Route-map to selectively unsuppress
suppressed routes
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
unsuppress-map <map>
no unsuppress-map <map>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| map | WORD  | String  |
### Usage Guidelines 
```
Use this command to define a route policy via route-map to unsuppress
suppressed routes.
```
### Examples 
#### Following command configures an unsuppress map for a          peer-group 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Int
sonic(config-router-bgp-pg)# address-family ipv4 unicast
sonic(config-router-bgp-pg-af)# unsuppress-map rm_unsup_ext_rt
```
## update-delay 
### Description 
```
This command allows user to set update delay. This parameter control how
long to wait before running best-path selection after Graceful Restart.
```
### Parent Commands (Modes) 
```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }
```
### Syntax 
```
update-delay <time> [ <maxmedval> ]
no update-delay
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| time |   | Integer  |
| maxmedval |   | Integer  |
### Usage Guidelines 
```
This feature is used to enable read-only mode on BGP process restart or
when BGP process is cleared using clear ip bgp *. When applicable,
read-only mode would begin as soon as the first peer reaches Established
status and a timer for max-delay seconds is started.
During this mode BGP doesn't run any best-path or generate any updates
to its peers. This mode continues until:
All the configured peers, except the shutdown peers, have sent explicit
EOR (End-Of-RIB) or an implicit-EOR. The first keep-alive after BGP has
reached Established is considered an implicit-EOR. If the establish-wait
optional value is given, then BGP will wait for peers to reach
established from the beginning of the update-delay till the
establish-wait period is over, i.e. the minimum set of established peers
for which EOR is expected would be peers established during the
establish-wait window, not necessarily all the configured neighbors.
max-delay period is over.
On hitting any of the above two conditions, BGP resumes the decision
process and generates updates to its peers.
Default max-delay is 0, i.e. the feature is off by default.
```
### Examples 
#### Below command configures update-delay to 120 seconds 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# update-delay 120
```
## update-source 
### Description 
```
This command specifies the IPv4 or IPv6 source address to use for the
BGP session to this neighbor. Source address may be specified as either
an IPv4/IPv6 address directly or as an interface name. The interface
name could be router port or Port Channel or Vlan interface with
IPv4/IPv6 address configured on it.
```
### Parent Commands (Modes) 
```
neighbor { <ip> | { interface { Ethernet | PortChannel | Vlan } } }
```
### Syntax 
```
update-source { <ip> | { interface { Ethernet | PortChannel | Vlan | Loopback } } }
no update-source { [ <ip> ] | { interface { Ethernet | PortChannel | Vlan | Loopback } } }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| ip | A.B.C.D/A::B  | String  |
### Usage Guidelines 
```
Use this command to configure source interface for a BGP neighbor
sessions
```
### Examples 
#### Following command configures source interface          for neighbor 30.30.30.3 to 12.56.36.74 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 30.30.30.3
sonic(config-router-bgp-neighbor)# update-source 12.56.36.74
```
## update-source 
### Description 
```
This command specifies the IPv4 or IPv6 source address to use for the
BGP session to neighbors in a peer-group. Source address may be specified
as either an IPv4/IPv6 address directly or as an interface name. The
interface name could be router port or Port Channel or Vlan interface with
IPv4/IPv6 address configured on it.
```
### Parent Commands (Modes) 
```
peer-group <template-str>
```
### Syntax 
```
update-source { <ip> | { interface { Ethernet | PortChannel | Vlan | Loopback } } }
no update-source { [ <ip> ] | { interface { Ethernet | PortChannel | Vlan | Loopback } } }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| ip | A.B.C.D/A::B  | String  |
### Usage Guidelines 
```
Use this command to configure source interface for a BGP peer-group
```
### Examples 
#### Following command configures source interface          for peer-group PG_Ext 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Ext
sonic(config-router-bgp-pg)# update-source Ethernet16
```
## use-v2-checksum 
### Description 
```
Configure checksum compatiblity for VRRP instance
```
### Parent Commands (Modes) 
```
vrrp ipv4
```
### Syntax 
```
use-v2-checksum
no use-v2-checksum
```
### Examples 
#### Configure preempt for IPv4 VRRP instance 
```
sonic(config)# interface Ethernet4
sonic(conf-if-Ethernet4)#
sonic(conf-if-Ethernet4)# vrrp 1 address-family ipv4
sonic(conf-if-Ethernet4-vrrp-ipv4-1)#use-v2-checksum
```
## username 
### Description 
```
Add new user 
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
username <user-name> password { <passwd> { role <rl> } }
no username <user-name>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| user-name | WORD  | String  |
| passwd | WORD  | String  |
| rl | String  | String  |
## version 
### Description 
```
Configure version for IPv4 VRRP instance
```
### Parent Commands (Modes) 
```
vrrp ipv4
```
### Syntax 
```
version <ver>
no version
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| ver |   | Integer  |
### Usage Guidelines 
```
Default value is version 2
```
### Examples 
#### Configure version for IPv4 VRRP instance 
```
sonic(config)# interface Ethernet4
sonic(conf-if-Ethernet4)#
sonic(conf-if-Ethernet4)# vrrp 1 address-family ipv4
sonic(conf-if-Ethernet4-vrrp-ipv4-1)#version 3
```
## vip 
### Description 
```
Configure virtual IP address for IPv4 VRRP instance
```
### Parent Commands (Modes) 
```
vrrp ipv4
```
### Syntax 
```
vip <vip_addr>
no vip <vip_addr>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| vip_addr | String  | String  |
### Examples 
#### Configure virtual IP address for IPv4 VRRP instance 
```
sonic(config)# interface Ethernet4
sonic(conf-if-Ethernet4)#
sonic(conf-if-Ethernet4)# vrrp 1 address-family ipv4
sonic(conf-if-Ethernet4-vrrp-ipv4-1)#vip 40.0.0.5
```
## vip 
### Description 
```
Configure virtual IP address for IPv6 VRRP instance
```
### Parent Commands (Modes) 
```
vrrp ipv6
```
### Syntax 
```
vip <vip_addr>
no vip <vip_addr>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| vip_addr | String  | String  |
### Examples 
#### Configure virtual IP address for IPv6 VRRP instance 
```
sonic(config)# interface Ethernet4
sonic(conf-if-Ethernet4)#
sonic(conf-if-Ethernet4)# vrrp 1 address-family ipv6
sonic(conf-if-Ethernet4-vrrp-ipv6-1)#vip 40::5
```
## vni 
### Description 
```
This command enables user to configure per-VNI EVPN parameters
```
### Parent Commands (Modes) 
```
address-family l2vpn evpn
```
### Syntax 
```
vni <vninum>
no vni <vninum>
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| vninum | VNI  | Integer  |
### Usage Guidelines 
```
[no] vni {vni-number}
```
### Examples 
#### Following command enters into config mode for VNI 100 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# address-family l2vpn evpn
sonic(config-router-bgp-af)# vni 100
sonic(config-router-bgp-af-vni)#
```
## vrrp 
### Description 
```
Configure Virtual Router Redundancy Protocol(VRRP) on Ethernet interface
```
### Parent Commands (Modes) 
```
interface <phy-if-name>
```
### Syntax 
```
vrrp <vrrp-id> address-family { ipv4 | ipv6 }
no vrrp <vrrp-id> address-family { ipv4 | ipv6 }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrrp-id |   | Integer  |
### Examples 
#### Configure VRRP for IPv4 on Ethernet interface 
```
sonic(config)# interface Ethernet4
sonic(conf-if-Ethernet4)#
sonic(conf-if-Ethernet4)# vrrp 1 address-family ipv4
sonic(conf-if-Ethernet4-vrrp-ipv4-1)#
```
#### Configure VRRP for IPv6 on Ethernet interface 
```
sonic(config)# interface Ethernet4
sonic(conf-if-Ethernet4)#
sonic(conf-if-Ethernet4)# vrrp 1 address-family ipv6
```
## vrrp 
### Description 
```
Configure Virtual Router Redundancy Protocol(VRRP) on PortChannel interface
```
### Parent Commands (Modes) 
```
interface PortChannel <lag-id> [ mode <PoMode> ] [ min-links <min-links-value> ] [ fallback ] [ fast_rate ]
```
### Syntax 
```
vrrp <vrrp-id> address-family { ipv4 | ipv6 }
no vrrp <vrrp-id> address-family { ipv4 | ipv6 }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrrp-id |   | Integer  |
### Examples 
#### Configure VRRP for IPv4 on PortChannel interface 
```
sonic(config)# interface PortChannel 10
sonic(conf-if-po10)#
sonic(conf-if-po10)# vrrp 1 address-family ipv4
sonic(conf-if-po10-vrrp-ipv4-1)#
```
#### Configure VRRP for IPv6 on PortChannel interface 
```
sonic(config)# interface PortChannel 10
sonic(conf-if-po10)#
sonic(conf-if-po10)# vrrp 1 address-family ipv6
```
## vrrp 
### Description 
```
Configure Virtual Router Redundancy Protocol(VRRP) on Vlan interface
```
### Parent Commands (Modes) 
```
interface <vlan-if-name>
```
### Syntax 
```
vrrp <vrrp-id> address-family { ipv4 | ipv6 }
no vrrp <vrrp-id> address-family { ipv4 | ipv6 }
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| vrrp-id |   | Integer  |
### Examples 
#### Configure VRRP for IPv4 on Vlan interface 
```
sonic(config)# interface Vlan 10
sonic(conf-if-Vlan10)#
sonic(conf-if-Vlan10)# vrrp 1 address-family ipv4
sonic(conf-if-Vlan10-vrrp-ipv4-1)#
```
#### Configure VRRP for IPv6 on Vlan interface 
```
sonic(config)# interface Vlan 10
sonic(conf-if-Vlan10)#
sonic(conf-if-Vlan10)# vrrp 1 address-family ipv6
```
## warm-reboot 

### Description 

```
warm-reboot [options] (-h shows help) 


```
### Syntax 

```
warm-reboot [ <options> ]

```
### Parameters 

| Name | Description | Type |
|:---:|:-----:|:-----:|
| options | options  | String  |


## warm-restart bgp 
### Description 
```
Timer value for warm restart of BGP service
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
warm-restart bgp timer <value>
no warm-restart bgp timer
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| value | timeout in seconds  | Integer  |
### Examples 
#### Warm restart timer for BGP service in seconds. Range 1-3599 
```
sonic(config)# warm-restart bgp timer 60
sonic(config)#
```
## warm-restart bgp enable 
### Description 
```
Enable warm restart feature
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
warm-restart bgp enable
no warm-restart bgp enable
```
### Examples 
#### Enable warm restart for BGP service 
```
sonic(config)# warm-restart bgp enable
sonic(config)#
```
## warm-restart bgp eoiu 
### Description 
```
Enable BGP EOIU flag
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
warm-restart bgp eoiu
no warm-restart bgp eoiu
```
### Examples 
#### Enable BGP EOIU flag status 
```
sonic(config)# warm-restart bgp eoiu
sonic(config)#
```
## warm-restart swss 
### Description 
```
Timer value for warm restart of SWSS service
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
warm-restart swss timer <value>
no warm-restart swss timer
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| value | timeout in seconds  | Integer  |
### Examples 
#### Warm restart timer for SWSS service in seconds. Range 1-9998  
```
sonic(config)# warm-restart swss timer 3600
sonic(config)#
```
## warm-restart swss enable 
### Description 
```
Enable warm restart feature
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
warm-restart swss enable
no warm-restart swss enable
```
### Examples 
#### Enable warm restart for SWSS service 
```
sonic(config)# warm-restart swss enable
sonic(config)#
```
## warm-restart system 
### Description 
```
Enable warm restart feature
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
warm-restart system [ enable ]
no warm-restart system [ enable ]
```
### Examples 
#### Enable warm restart for system service 
```
sonic(config)# warm-restart system
sonic(config)#
```
## warm-restart teamd 
### Description 
```
Timer value for warm restart of teamd service
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
warm-restart teamd timer <value>
no warm-restart teamd timer
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| value | timeout in seconds  | Integer  |
### Examples 
#### Warm restart timer for teamd service in seconds. Range 1-3599 
```
sonic(config)# warm-restart teamd timer 600
sonic(config)#
```
## warm-restart teamd enable 
### Description 
```
Enable warm restart feature
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
warm-restart teamd enable
no warm-restart teamd enable
```
### Examples 
#### Enable warm restart for teamd service 
```
sonic(config)# warm-restart teamd enable
sonic(config)#
```
## watermark interval 
### Description 
```
This command is used to configure snapshot watermark interval.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
watermark interval <interval_value>
no watermark interval
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| interval_value |   | Integer  |
### Usage Guidelines 
```
Use this command to configure sanpshot watermark interval.
```
### Examples 
#### Config watermark interval 
```
sonic(config)# watermark interval 77
```
## watermark telemetry 
### Description 
```
This command is used to configure watermark telemetry interval.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
watermark telemetry interval <interval_value>
no watermark telemetry interval
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| interval_value |   | Integer  |
### Usage Guidelines 
```
Use this command to configure watermark telemetry interval.
```
### Examples 
#### Config watermark telemetry interval 
```
sonic(config)# watermark telemetry interval 88
```
## weight 
### Description 
```
scheduler weight 
```
### Parent Commands (Modes) 
```
queue <sequence>
```
### Syntax 
```
weight <weight>
no weight
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| weight |   | Integer  |
## weight 
### Description 
```
Set default weight for routes from this neighbor 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
weight <val>
no weight [ <val> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| val |   | Integer  |
## weight 
### Description 
```
Set default weight for routes from this neighbor 
```
### Parent Commands (Modes) 
```
address-family ipv6 unicast
```
### Syntax 
```
weight <val>
no weight [ <val> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| val |   | Integer  |
## weight 
### Description 
```
This command configures a default weight for all routes received from
this BGP neighbor
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
weight <val>
no weight [ <val> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| val |   | Integer  |
### Usage Guidelines 
```
Use this command to assign a default weight to BGP routes received from
this neighbor. Weight parameter is used in BGP route selection process.
So, configuring weight may influence the outcome of the route selection
process
```
### Examples 
#### Following command configures a default weight of 45 for          routes received from neighbor 20.20.20.2 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# neighbor 20.20.20.2
sonic(config-router-bgp-neighbor)# remote-as 300
sonic(config-router-bgp-neighbor)# address-family ipv4 unicast
sonic(config-router-bgp-neighbor-af)# weight 45
```
## weight 
### Description 
```
This command configures a default weight for all routes received from
neighbors in this peer-group
```
### Parent Commands (Modes) 
```
address-family ipv4 unicast
```
### Syntax 
```
weight <val>
no weight [ <val> ]
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| val |   | Integer  |
### Usage Guidelines 
```
Use this command to assign a default weight to BGP routes received from
neighbors in this peer-group. Weight parameter is used in BGP route
selection process.  So, configuring weight may influence the outcome of
the route selection process
```
### Examples 
#### Following command configures a default weight of 25 for          routes received from neighbors in peer-group PG_Ext 
```
sonic# configure terminal
sonic(config)# router bgp 100
sonic(config-router-bgp)# peer-group PG_Int
sonic(config-router-bgp-pg)# address-family ipv4 unicast
sonic(config-router-bgp-pg-af)# weight 25
```
## write 

### Description 

```
Save config 


```
### Syntax 

```
write memory

```
## write erase 
### Description 
```
Erase the existing switch configuration files except the management interface configuration,
or cancel the configuration erase operation.
The "write erase" command deletes the startup configuration JSON file and all application
configuration files in the /etc/sonic directory. The management interface configuration
in the startup configuration file is retained so that the user can access the switch using
the same management address after the switch reboots.
For the write erase command operation to take effect, the user has to reboot the switch
after issuing the write erase command. If the user wishes to not proceed with the configuration
removal operation, the write erase cancel command can be used to undo the previously issued
write erase command.
```
### Syntax 
```
write erase
no write erase
```
### Usage Guidelines 
```
Use the command "write erase" to erase the existing switch configuration files except for the management interface configuration.
Use the command "no write erase" to cancel configuration erase operation.
```
### Examples 
```
sonic# configure terminal
sonic(config)# write erase
Existing switch configuration files except management interface configuration will be removed, continue? [y/N]:
sonic(config)# no write erase
Switch configuration erase operation will be cancelled, continue? [y/N]:
```
## write erase boot 

### Description 

```
Erase the configuration files including management interface configuration.

The "write erase boot" command deletes the startup configuration JSON file and all
application configuration files in the /etc/sonic directory. The management
interface configuration in the startup configuration JSON file is also removed.
The SONiC switch boots with a factory default configuration file.


```
### Syntax 

```
write erase boot

```
### Usage Guidelines 

```
Use this command to erase the configuration files including the management
interface configuration.


```
### Examples 

```
sonic# configure terminal
sonic(config)# write erase boot
Existing switch configuration files will be removed, continue? [y/N]:


```
## write erase install 

### Description 

```
Restore SONiC switch content to default values.

The "write erase install" command removes all changes made by the user.
All user installed packages and file changes are removed. It also deletes
the startup configuration JSON file and the files in /etc/sonic directory.
The SONiC switch is reverted to the same state as a newly installed image.
After the SONiC switch is rebooted, if the Zero Touch Provisioning (ZTP)
feature is enabled, the SONIC switch will start performing ZTP to discover
and download the switch configuration.


```
### Syntax 

```
write erase install

```
### Usage Guidelines 

```
Use this command to restore SONiC switch content to default values.


```
### Examples 

```
sonic# configure terminal
sonic(config)# write erase install
All SONiC switch content will be restored to default values, continue? [y/N]:


```
## write-multiplier 
### Description 
```
Configures OSPFv2 write multiplier.
```
### Parent Commands (Modes) 
```
router ospf [ vrf <vrf-name> ]
```
### Syntax 
```
write-multiplier <maxinterfacewrite>
no write-multiplier
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| maxinterfacewrite |   | Integer  |
### Usage Guidelines 
```
Use this command to configure OSPFv2 write multiplier.
```
### Examples 
#### Configures OSPFv2 write multiplier 
```
sonic-cli(config-router-ospf)# write-multiplier 20
```
### Features this CLI belongs to 
*  OSPFv2
## write-quanta 
### Description 
```
This command configures the maximum number of BGP packets to write to
peer socker in one cycle of I/O
```
### Parent Commands (Modes) 
```
router bgp <as-num-dot> { [ vrf <vrf-name> ] }
```
### Syntax 
```
write-quanta <wrval>
no write-quanta
```
### Parameters 
| Name | Description | Type |
|:---:|:-----:|:-----:|
| wrval |   | Integer  |
### Usage Guidelines 
```
BGP message Tx I/O is vectored. This means that multiple packets are
written to the peer socket at the same time each I/O cycle, in order to
minimize system call overhead. This value controls how many are written
at a time. Under certain load conditions, reducing this value could make
peer traffic less bursty. In practice, leave this settings on the
default (64).
```
### Examples 
#### Below command configures the write-quanta to          50 packets 
```
sonic# configure terminal
sonic(config)# router bgp 65300
sonic(config-router-bgp)# write-quanta 50
```
## ztp 
### Description 
```
Administratively enable or disable ZTP.
```
### Parent Commands (Modes) 
```
configure terminal
```
### Syntax 
```
ztp enable
no ztp enable
```
### Usage Guidelines 
```
Use these commands to administratively enable or disable ZTP.
```
### Examples 
```
sonic# configure terminal
sonic(config)# ztp enable
sonic(config)# no ztp enable
```
### Features this CLI belongs to 
*  ZTP
