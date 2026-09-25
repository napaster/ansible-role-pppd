# ansible-pppd

Deploy [Paul's PPP Package](//ppp.samba.org/) configuration.

## Requirements

* Ansible 2.8+;

## Example configuration

```yaml
pppd:
# Enable ppp after deploy or not
- enable: 'true'
# Restart ppp after deploy or not
  restart: 'true'
# Install/upgrade ppp package or not
  install_package: 'true'
# 'present' (do nothing if package is already installed) or 'latest' (always
# upgrade to last version)
  package_state: 'latest'
  settings:
# Sets the logical name of the link to name. Pppd will create a file named
# ppp-name.pid in /var/run containing its process ID. This can be useful in
# determining which instance of pppd is responsible for the link to a given
# peer system.
  - linkname: 'ppp0' # mandatory for role
# This feature allow to override shipped with package service unit. Two things
# it can do, both optional and independent:
# 1. If 'ttyname' is set, bind the unit to that interface device (BindsTo /
#    After). Reason: some times ppp was started before vlan bridge is created
#    and then ppp service will be failed.
# 2. Restart policy via 'systemd_restart', 'systemd_restart_sec',
#    'systemd_timeout_start_sec', 'systemd_start_limit_interval_sec' and
#    'systemd_start_limit_burst'. Shipped unit is 'Type=notify' with no
#    'Restart=': if the peer is unreachable at boot, start times out and the
#    link stays dead until started by hand. 'persist' in pppd does not help
#    here, it only works once pppd is running.
    systemd_override: 'true'
# Restart policy for the unit ('no', 'on-failure', 'always', ...). Empty or
# absent keeps the shipped default.
    systemd_restart: 'on-failure'
# Delay before restart, seconds.
    systemd_restart_sec: '60'
# Start timeout, seconds or 'infinity'. Mind 'Before=network.target' in the
# shipped unit: an endless start would hold network.target during boot.
    systemd_timeout_start_sec: ''
# Start rate limiting ('0' disables it). With a long 'systemd_restart_sec'
# the default limit (5 in 10 s) is never hit, but keeps retries unbounded.
    systemd_start_limit_interval_sec: '0'
    systemd_start_limit_burst: ''
# Load the shared library object file filename as a plugin. If filename does
# not contain a slash (/), pppd will look in the '/usr/lib/pppd'.
    plugin: 'rp-pppoe.so'
# Use the serial port called ttyname to communicate with the peer. If ttyname
# does not begin with a slash (/), the string '/dev/' is prepended to ttyname
# to form the name of the device to open. If no device name is given, or if the
# name of the terminal connected to the standard input is given, pppd will use
# that terminal, and will not fork to put itself in the background. A value for
# this option from a privileged source cannot be overridden by a non-privileged
# user.
    ttyname: 'vlan3158'
# This option sets the Async-Control-Character-Map (ACCM) for this end of the
# link. The ACCM is a set of 32 bits, one for each of the ASCII control
# characters with values from 0 to 31, where a 1 bit indicates that the
# corresponding control character should not be used in PPP packets sent to
# this system. The map is encoded as a hexadecimal number (without a leading
# 0x) where the least significant bit (00000001) represents character 0 and the
# most significant bit (80000000) represents character 31. Pppd will ask the
# peer to send these characters as a 2-byte escape sequence. If multiple
# asyncmap options are given, the values are ORed together. If no asyncmap
# option is given, the default is zero, so pppd will ask the peer not to escape
# any control characters. To escape transmitted characters, use the 'escape'
# option.
    asyncmap: ''
# Require the peer to authenticate itself before allowing network packets to be
# sent or received. This option is the default if the system has a default
# route. If neither this option nor the 'noauth' option is specified, pppd will
# only allow the peer to use IP addresses to which the system does not already
# have a route.
    auth: ''
# Read additional options from the file /etc/ppp/peers/name. This file may
# contain privileged options, such as noauth, even if pppd is not being run by
# root. The name string may not begin with '/' or include '..' as a pathname
# component.
    call: ''
# Usually there is something which needs to be done to prepare the link before
# the PPP protocol can be started for instance, with a dial-up modem, commands
# need to be sent to the modem to dial the appropriate phone number. This
# option specifies an command for pppd to execute (by passing it to a shell)
# before attempting to start PPP negotiation. The 'chat' program is often useful
# here, as it provides a way to send arbitrary strings to a modem and respond
# to received characters. A value for this option from a privileged source
# cannot be overridden by a non-privileged user.
    connect: ''
# Specifies that pppd should set the serial port to use hardware flow control
# using the RTS and CTS signals in the RS-232 interface. If neither the crtscts,
# the nocrtscts, the cdtrcts nor the nocdtrcts option is given, the hardware
# flow control setting for the serial port is left unchanged. Some serial ports
# (such as Macintosh serial ports) lack a true RTS output. Such serial ports
# use this mode to implement unidirectional flow control. The serial port will
# suspend transmission when requested by the modem (via CTS) but will be unable
# to request the modem to stop sending to the computer. This mode retains the
# ability to use DTR as a modem control line.
    crtscts: ''
# Add a default route to the system routing tables, using the peer as the
# gateway, when IPCP negotiation is successfully completed. This entry is
# removed when the PPP connection is broken. This option is privileged if the
# nodefaultroute option has been specified.
    defaultroute: ''
# Execute the command specified by script, by passing it to a shell, after pppd
# has terminated the link. This command could, for example, issue commands to
# the modem to cause it to hang up if hardware modem control signals were not
# available. The disconnect script is not run if the modem has already hung up.
# A value for this option from a privileged source cannot be overridden by a
# non-privileged user.
    disconnect: ''
# Specifies that certain characters should be escaped on transmission
# (regardless of whether the peer requests them to be escaped with its async
# control character map). The characters to be escaped are specified as a list
# of hex numbers separated by commas. Note that almost any character can be
# specified for the escape option, unlike the asyncmap option which only allows
# control characters to be specified. The characters which may not be escaped
# are those with hex values '0x20 - 0x3f' or '0x5e'.
    escape: ''
# Read options from file name. The file must be readable by the user who has
# invoked pppd.
    file: ''
# Execute the command specified by script, by passing it to a shell, to
# initialize the serial line. This script would typically use the 'chat'
# program to configure the modem to enable auto answer. A value for this option
# from a privileged source cannot be overridden by a non-privileged user.
    init: ''
# Specifies that pppd should create a UUCP-style lock file for the serial
# device to ensure exclusive access to the device. By default, pppd will not
# create a lock file.
    lock: ''
# Set the MRU (aximum Receive Unit) value to n. Pppd will ask the peer to send
# packets of no more than n bytes. The value of n must be between 128 and 16384
# (default is 1500). A value of 296 works well on very slow links (40 bytes for
# TCP/IP header + 256 bytes of data). Note that for the IPv6 protocol, the MRU
# must be at least 1280.
    mru: ''
# Set the MTU (Maximum Transmit Unit) value to n. Unless the peer requests a
# smaller value via MRU negotiation, pppd will request that the kernel
# networking code send data packets of no more than n bytes through the PPP
# network interface. Note that for the IPv6 protocol, the MTU must be at least
# 1280.
    mtu: ''
# Enables the "passive" option in the LCP. With this option, pppd will attempt
# to initiate a connection, if no reply is received from the peer, pppd will
# then just wait passively for a valid LCP packet from the peer, instead of
# exiting, as it would without this option.
    passive: ''
# Specifies a packet filter to be applied to data packets to determine which
# packets are to be regarded as link activity, and therefore reset the idle
# timer, or cause the link to be brought up in demand-dialling mode. This
# option is useful in conjunction with the idle option if there are packets
# being sent or received regularly over the link (for example, routing
# information packets) which would otherwise prevent the link from ever
# appearing to be idle. The filter-expression syntax is as described for
# 'tcpdump', except that qualifiers which are inappropriate for a PPP link, such
# as ether and arp, are not permitted. This option is currently only available
# under Linux, and requires that the kernel was configured to include PPP
# filtering support (CONFIG_PPP_FILTER). Note that it is possible to apply
# different constraints to incoming and outgoing packets using the inbound and
# outbound qualifiers.
    active_filter: ''
# Allow peers to use the given IP address or subnet without authenticating
# themselves. The parameter is parsed as for each element of the list of
# allowed IP addresses in the secrets files.
    allow_ip: ''
# Allow peers to connect from the given telephone number. A trailing '*'
# character will match all numbers beginning with the leading part.
    allow_number: ''
# Request that the peer compress packets that it sends, using the BSD-Compress
# scheme, with a maximum code size of nr bits, and agree to compress packets
# sent to the peer with a maximum code size of 'nt' bits. If nt is not
# specified, it defaults to the value given for nr. Values in the range 9 to 15
# may be used for 'nr' and 'nt'; larger values give better compression but
# consume more kernel memory for compression dictionaries. Alternatively, a
# value of 0 for 'nr' or 'nt' disables compression in the corresponding
# direction. Use 'nobsdcomp' to disable BSD-Compress compression entirely.
    bsdcomp: ''
# Use a non-standard hardware flow control (i.e. DTR/CTS) to control the flow
# of data on the serial port. If neither the crtscts, the nocrtscts, the
# cdtrcts nor the nocdtrcts option is given, the hardware flow control setting
# for the serial port is left unchanged. Some serial ports (such as Macintosh
# serial ports) lack a true RTS output. Such serial ports use this mode to
# implement true bi-directional flow control. The sacrifice is that this flow
# control mode does not permit using DTR as a modem control line.
    cdtrcts: ''
# If this option is given, pppd will rechallenge the peer every n seconds.
    chap_interval: ''
# Set the maximum number of CHAP challenge transmissions to n (default 10).
    chap_max_challenge: ''
# Set the CHAP restart interval (retransmission timeout for challenges) to n
# seconds (default 3).
    chap_restart: ''
# When exiting, wait for up to n seconds for any child processes (such as the
# command specified with the pty command) to exit before exiting. At the end of
# the timeout, pppd will send a SIGTERM signal to any remaining child processes
# and exit. A value of 0 means no timeout, that is, pppd will wait until all
# child processes have exited.
    child_timeout: ''
# Wait for up to n milliseconds after the connect script finishes for a valid
# PPP packet from the peer. At the end of this time, or when a valid PPP packet
# is received from the peer, pppd will commence negotiation by sending its first
# LCP packet. The default value is 1000 (1 second). This wait period only
# applies if the connect or pty option is used.
    connect_delay: ''
# Enables connection debugging facilities. If this option is given, pppd will
# log the contents of all control packets sent or received in a readable form.
# The packets are logged through syslog with facility 'daemon' and level
# 'debug'.
    debug: ''
# Disable asyncmap negotiation, forcing all control characters to be escaped
# for both the transmit and the receive direction.
    default_asyncmap: ''
# Disable MRU (Maximum Receive Unit) negotiation. With this option, pppd will
# use the default MRU value of 1500 bytes for both the transmit and receive
# direction.
    default_mru: ''
# Request that the peer compress packets that it sends, using the Deflate
# scheme, with a maximum window size of 2**nr bytes, and agree to compress
# packets sent to the peer with a maximum window size of 2**nt bytes. If 'nt' is
# not specified, it defaults to the value given for 'nr'. Values in the range
# 9 to 15 may be used for 'nr' and 'nt', larger values give better compression
# but consume more kernel memory for compression dictionaries. Alternatively, a
# value of 0 for 'nr' or 'nt' disables compression in the corresponding
# direction. Use 'nodeflate' Deflate compression entirely. Note: pppd requests
# Deflate compression in preference to BSD-Compress if the peer can do either.
    deflate: ''
# Initiate the link only on demand, i.e. when data traffic is present. With
# this option, the remote IP address may be specified by the user on the
# command line or in an options file, or if not, pppd will use an arbitrary
# address in the 10.x.x.x range. Pppd will initially configure the interface
# and enable it for IP traffic without connecting to the peer. When traffic is
# available, pppd will connect to the peer and perform negotiation,
# authentication, etc. When this is completed, pppd will commence passing data
# packets (i.e., IP packets) across the link. The demand option implies the
# 'persist' option. If this behaviour is not desired, use the 'nopersist'
# option after the demand option. The 'idle' and 'holdoff' options are also
# useful in conjunction with the demand option.
    demand: ''
# Append the domain name to the local host name for authentication purposes.
# For example, if gethostname() returns the name "porsche", but the fully
# qualified domain name is "porsche.Quotron.COM",  you could specify domain
# "Quotron.COM". Pppd would then use the name "porsche.Quotron.COM" for looking
# up secrets in the secrets file, and as the default name to send to the peer
# when authenticating itself to the peer. This option is privileged.
    domain: ''
# With the dump option, pppd will print out all the option values which have
# been set and proceeds normal operaions.
    dump: ''
# Enables session accounting via PAM or wtwp/wtmpx, as appropriate. When PAM is
# enabled, the PAM "account" and "session" module stacks determine behavior,
# and are enabled for all PPP authentication protocols. When PAM is disabled,
# wtmp/wtmpx entries are recorded regardless of whether the peer name
# identifies a valid user on the local system, making peers visible in the
# 'last' log. This feature is automatically enabled when the pppd login option
# is used. Session accounting is disabled by default.
    enable_session: ''
# Sets the endpoint discriminator sent by the local machine to the peer during
# multilink negotiation to <epdisc>. The default is to use the MAC address of
# the first ethernet interface on the system, if any, otherwise the IPv4
# address corresponding to the hostname, if any, provided it is not in the
# multicast or locally-assigned IP address ranges, or the localhost address.
# The endpoint discriminator can be the string null or of the form type:value,
# where type is a decimal number or one of the strings local, IP, MAC, magic,
# or phone. The value is an IP address in dotted-decimal notation for the IP
# type, or a string of bytes in hexadecimal, separated by periods or colons for
# the other types. For the MAC type, the value may also be the name of an
# ethernet or similar network interface. This option is currently only
# available under Linux.
    endpoint: ''
# If this option is given and pppd authenticates the peer with EAP (i.e., is
# the server), pppd will restart EAP authentication every n seconds. For EAP
# SRP-SHA1, see also the srp-interval option, which enables lightweight
# rechallenge.
    eap_interval: ''
# Set the maximum number of EAP Requests to which pppd will respond
# (as a client) without hearing EAP Success or Failure (default is 20).
    eap_max_rreq: ''
# Set the maximum number of EAP Requests that pppd will issue (as a server)
# while attempting authentication (default is 10).
    eap_max_sreq: ''
# Set the retransmit timeout for EAP Requests when acting as a server
# (authenticator) (default is 3 seconds).
    eap_restart: ''
# Set the maximum time to wait for the peer to send an EAP Request when acting
# as a client (authenticatee) (default is 20 seconds).
    eap_timeout: ''
# When logging the contents of PAP packets, this option causes pppd to exclude
# the password string from the log. Default is 'true'.
    hide_password: ''
# Specifies how many seconds to wait before re-initiating the link after it
# terminates. This option only has any effect if the persist or demand option
# is used. The holdoff period is not applied if the link was terminated because
# it was idle.
    holdoff: ''
# Specifies that pppd should disconnect if the link is idle for n seconds. The
# link is idle when no data packets (i.e. IP packets) are being sent or
# received. Note: it is not advisable to use this option with the persist
# option without the demand option. If the 'active_filter' option is given,
# data packets which are rejected by the specified activity filter also count
# as the link being idle.
    idle: ''
# With this option, pppd will accept the peer's idea of our local IP address,
# even if the local IP address was specified in an option.
    ipcp_accept_local: ''
# With this option, pppd will accept the peer's idea of its (remote) IP address,
# even if the remote IP address was specified in an option.
    ipcp_accept_remote: ''
# Set the maximum number of IPCP configure-request transmissions to n
# (default 10).
    ipcp_max_configure: ''
# Set the maximum number of IPCP configure-NAKs returned before starting to
# send configure-Rejects instead to n (default 10).
    ipcp_max_failure: ''
# Set the maximum number of IPCP terminate-request transmissions to n
# (default 3).
    ipcp_max_terminate: ''
# Set the IPCP restart interval (retransmission timeout) to n seconds
# (default 3).
    ipcp_restart: ''
# Provides an extra parameter to the ip-up, ip-pre-up and ip-down scripts. If
# this option is given, the string supplied is given as the 6th parameter to
# those scripts.
    ipparam: ''
# With this option, pppd will accept the peer's idea of our local IPv6
# interface identifier, even if the local IPv6 interface identifier was
# specified in an option.
    ipv6cp_accept_local: ''
# Set the maximum number of IPv6CP configure-request transmissions to n
# (default 10).
    ipv6cp_max_configure: ''
# Set the maximum number of IPv6CP configure-NAKs returned before starting to
# send configure-Rejects instead to n (default 10).
    ipv6cp_max_failure: ''
# Set the maximum number of IPv6CP terminate-request transmissions to n
# (default 3).
    ipv6cp_max_terminate: ''
# Set the IPv6CP restart interval (retransmission timeout) to n seconds
# (default 3).
    ipv6cp_restart: ''
# Enable the IPXCP and IPX protocols. This option is presently only supported
# under Linux, and only if your kernel has been configured to include IPX
# support.
    ipx: ''
# Set the IPX network number in the IPXCP configure request frame to n, a
# hexadecimal number (without a leading 0x). There is no valid default. If this
# option is not specified, the network number is obtained from the peer. If the
# peer does not have the network number, the IPX protocol will not be started.
    ipx_network: ''
# Set the IPX node numbers. The two node numbers are separated from each other
# with a colon character. The first number n is the local node number. The
# second number m is the peer's node number. Each node number is a hexadecimal
# number, at most 10 digits long. The node numbers on the ipx-network must be
# unique. There is no valid default. If this option is not specified then the
# node numbers are obtained from the peer.
    ipx_node: ''
# Set the name of the router. This is a string and is sent to the peer as
# information data.
    ipx_router_name: ''
# Set the routing protocol to be received by this option. More than one
# instance of ipx-routing may be specified. The 'none' option (0) may be
# specified as the only instance of ipx-routing. The values may be 0 for NONE,
# 2 for RIP/SAP, and 4 for NLSP.
    ipx_routing: ''
# Accept the peer's NAK for the node number specified in the ipx-node option.
# If a node number was specified, and non-zero, the default is to insist that
# the value be used. If you include this option then you will permit the peer
# to override the entry of the node number.
    ipxcp_accept_local: ''
# Accept the peer's NAK for the network number specified in the ipx-network
# option. If a network number was specified, and non-zero, the default is to
# insist that the value be used. If you include this option then you will permit
# the peer to override the entry of the node number.
    ipxcp_accept_network: ''
# Use the peer's network number specified in the configure request frame. If a
# node number was specified for the peer and this option was not specified, the
# peer will be forced to use the value which you have specified.
    ipxcp_accept_remote: ''
# Set the maximum number of IPXCP configure request frames which the system will
# send to n. The default is 10.
    ipxcp_max_configure: ''
# Set the maximum number of IPXCP NAK frames which the local system will send
# before it rejects the options. The default value is 3.
    ipxcp_max_failure: ''
# Set the maximum number of IPXCP terminate request frames before the local
# system considers that the peer is not listening to them. Default value is 3.
    ipxcp_max_terminate: ''
# Enable debugging code in the kernel-level PPP driver. The argument values
# depend on the specific kernel driver, but in general a value of 1 will enable
# general kernel debug messages. Note that these messages are usually only
# useful for debugging the kernel driver itself. For the Linux 2.2.x kernel
# driver, the value is a sum of bits: 1 to enable general debug messages, 2 to
# request that the contents of received packets be printed, and 4 to request
# that the contents of transmitted packets be printed.
    kdebug: ''
# Enables pppd to alter kernel settings as appropriate. Under Linux, pppd will
# enable IP forwarding (i.e. set '/proc/sys/net/ipv4/ip_forward' to '1') if the
# proxyarp option is used, and will enable the dynamic IP address option
# (i.e. set '/proc/sys/net/ipv4/ip_dynaddr' to '1') in demand mode if the local
# address changes.
    ktune: ''
# If this option is given, pppd will presume the peer to be dead if n LCP
# echo-requests are sent without receiving a valid LCP echo-reply. If this
# happens, pppd will terminate the connection. Use of this option requires a
# non-zero value for the lcp-echo-interval parameter. This option can be used
# to enable pppd to terminate after the physical connection has been broken
# (e.g., the modem has hung up) in situations where no hardware modem control
# lines are available.
    lcp_echo_failure: ''
# If this option is given, pppd will send an LCP echo-request frame to the peer
# every n seconds. Normally the peer should respond to the echo-request by
# sending an echo-reply. This option can be used with the 'lcp_echo_failure'
# option to detect that the peer is no longer connected.
    lcp_echo_interval: ''
# Set the maximum number of LCP configure-request transmissions (default 10).
    lcp_max_configure: ''
# Set the maximum number of LCP configure-NAKs returned before starting to send
# configure-Rejects instead (default 10).
    lcp_max_failure: ''
# Set the maximum number of LCP terminate-request transmissions (default 3).
    lcp_max_terminate: ''
# Set the LCP restart interval (retransmission timeout). Default is 3 seconds.
    lcp_restart: ''
# Don't use the modem control lines. With this option, pppd will ignore the
# state of the CD (Carrier Detect) signal from the modem and will not change
# the state of the DTR (Data Terminal Ready) signal. This is the opposite of
# the modem option.
    local: ''
# Send log messages to file descriptor. Pppd will send log messages to at most
# one file or file descriptor (as well as sending the log messages to syslog),
# so this option and the 'logfile' option are mutually exclusive. The default
# is for pppd to send log messages to stdout (file descriptor 1), unless the
# serial port is already open on stdout.
    logfd: ''
# Append log messages to the file filename (as well as sending the log messages
# to syslog). The file is opened with the privileges of the user who invoked
# pppd, in append mode.
    logfile: ''
# Use the system password database for authenticating the peer using PAP, and
# record the user in the system wtmp file. Note that the peer must have an
# entry in the '/etc/ppp/pap-secrets' file as well as the system password
# database to be allowed access. See also the 'enable_session' option.
    login: ''
# If multilink is enabled and this pppd process is the multilink bundle master,
# and the link controlled by this pppd process terminates, this pppd process
# continues to run in order to maintain the bundle. If the master_detach option
# has been given, pppd will detach from its controlling terminal in this
# situation, even if the nodetach option has been given.
    master_detach: ''
# Terminate the connection when it has been available for network traffic for n
# seconds (i.e. n seconds after the first network control protocol comes up).
    maxconnect: ''
# Terminate after consecutive failed connection attempts. A value of 0 means no
# limit. The default value is 10.
    maxfail: '0'
# Use the modem control lines. This option is the default. With this option,
# pppd will wait for the CD (Carrier Detect) signal from the modem to be
# asserted when opening the serial device (unless a connect script is
# specified), and it will drop the DTR (Data Terminal Ready) signal briefly
# when the connection is terminated and before executing the connect script. On
# Ultrix, this option implies hardware flow control, as for the crtscts option.
# This is the opposite of the local option.
    modem: ''
# Enables the use of PPP multilink - this is an alias for the `multilink'
# option. This option is currently only available under Linux.
    mp: ''
# Allow MPPE to use stateful mode. Stateless mode is still attempted first. The
# default is to disallow stateful mode.
    mppe_stateful: ''
# Enables the use of short (12-bit) sequence numbers in multilink headers, as
# opposed to 24-bit sequence numbers. This option is only available under Linux,
# and only has any effect if multilink is enabled (see the multilink option).
    mpshortseq: ''
# The MRRU is the maximum size for a received packet on a multilink bundle, and
# is analogous to the MRU for the individual links. This option is currently
# only available under Linux, and only has any effect if multilink is enabled
# (see the multilink option).
    mrru: ''
# If pppd is acting as a server for Microsoft Windows clients, this option
# allows pppd to supply one or two DNS (Domain Name Server) addresses to the
# clients. The first instance of this option specifies the primary DNS address
# the second instance (if given) specifies the secondary DNS address. This
# option was present in some older versions of pppd under the name 'dns_addr'.
    ms_dns: ''
# If pppd is acting as a server for Microsoft Windows or "Samba" clients, this
# option allows pppd to supply one or two WINS (Windows Internet Name Services)
# server addresses to the clients. The first instance of this option specifies
# the primary WINS address, the second instance (if given) specifies the
# secondary WINS address.
    ms_wins: ''
# Enables the use of the PPP multilink protocol. If the peer also supports
# multilink, then this link can become part of a bundle between the local
# system and the peer. If there is an existing bundle to the peer, pppd will
# join this link to that bundle, otherwise pppd will create a new bundle. See
# the MULTILINK section below. This option is currently only available under
# Linux.
    multilink: ''
# Set the name of the local system for authentication purposes to name. With
# this option, pppd will use lines in the secrets files which have name as the
# second field when looking for a secret to use in authenticating the peer. In
# addition, unless overridden with the user option, name will be used as the
# name to send to the peer when authenticating the local system to the peer.
# Note that pppd does not append the domain name to name.
    name: ''
# Disable Address/Control compression in both directions (send and receive).
    noaccomp: ''
# Do not require the peer to authenticate itself.
    noauth: 'true'
# Disables BSD-Compress compression; pppd will not request or agree to compress
# packets using the BSD-Compress scheme.
    nobsdcomp: ''
# Disable CCP (Compression Control Protocol) negotiation. This option should
# only be required if the peer is buggy and gets confused by requests from pppd
# for CCP negotiation.
    noccp: ''
# Disable hardware flow control (i.e. RTS/CTS) on the serial port. If neither
# the 'crtscts' nor the 'nocrtscts' nor the 'cdtrcts' nor the 'nocdtrcts'
# option is given, the hardware flow control setting for the serial port is
# left unchanged.
    nocrtscts: ''
# Disable the defaultroute option.
    nodefaultroute: 'true'
# Disables Deflate compression. Pppd will not request or agree to compress
# packets using the Deflate scheme.
    nodeflate: ''
# Don't detach from the controlling terminal. Without this option, if a serial
# device other than the terminal on the standard input is specified, pppd will
# fork to become a background process.
    nodetach: ''
# Disables pppd from sending an endpoint discriminator to the peer or accepting
# one from the peer. This option should only be required if the peer is buggy.
    noendpoint: ''
# Disable IPCP negotiation and IP communication. This option should only be
# required if the peer is buggy and gets confused by requests from pppd for
# IPCP negotiation.
    noip: ''
# Disable IPv6CP negotiation and IPv6 communication. This option should only be
# required if the peer is buggy and gets confused by requests from pppd for
# IPv6CP negotiation.
    noipv6: ''
# Disables the default behaviour when no local IP address is specified, which
# is to determine (if possible) the local IP address from the hostname. With
# this option, the peer will have to supply the local IP address during IPCP
# negotiation (unless it specified explicitly on the command line or in an
# options file).
    noipdefault: ''
# Disable the IPXCP and IPX protocols. This option should only be required if
# the peer is buggy and gets confused by requests from pppd for IPXCP
# negotiation.
    noipx: ''
# Opposite of the 'ktune' option, disables pppd from changing system settings.
    noktune: ''
# Opposite of the lock option, specifies that pppd should not create a
# UUCP-style lock file for the serial device.
    nolock: ''
# Do not send log messages to a file or file descriptor. This option cancels
# the 'logfd' and 'logfile' options.
    nolog: ''
# Disable magic number negotiation. With this option, pppd cannot detect a
# looped-back line. This option should only be needed if the peer is buggy.
    nomagic: ''
# Disables the use of PPP multilink. This option is currently only available
# under Linux.
    nomp: ''
# Disables MPPE (Microsoft Point to Point Encryption). This is the default.
    nomppe: ''
# Disable 40-bit encryption with MPPE.
    nomppe_40: ''
# Disable 128-bit encryption with MPPE.
    nomppe_128: ''
# Disable MPPE stateful mode. This is the default.
    nomppe_stateful: ''
# Disables the use of short (12-bit) sequence numbers in the PPP multilink
# protocol, forcing the use of 24-bit sequence numbers. This option is
# currently only available under Linux, and only has any effect if multilink is
# enabled.
    nompshortseq: ''
# Disables the use of PPP multilink. This option is currently only available
# under Linux.
    nomultilink: ''
# Disable protocol field compression negotiation in both the receive and the
# transmit direction.
    nopcomp: ''
# Exit once a connection has been made and terminated. This is the default
# unless the 'persist' or 'demand' option has been specified.
    nopersist: ''
# Do not accept or agree to Predictor-1 compression.
    nopredictor1: ''
# Disable the proxyarp option.
    noproxyarp: ''
# Allow pppd to operate without having an IP address for the peer. This option
# is only available under Linux. Normally, pppd will request the peer's IP
# address, and if the peer does not supply it, pppd will use an arbitrary
# address in the 10.x.x.x subnet. With this option, if the peer does not supply
# its IP address, pppd will not ask the peer for it, and will not set the
# destination address of the ppp interface. In this situation, the ppp
# interface can be used for routing by creating device routes, but the peer
# itself cannot be addressed directly for IP traffic.
    noremoteip: ''
# Normally, pppd  requires a terminal device. With this option, pppd will
# allocate itself a pseudo-tty master/slave pair and use the slave as its
# terminal device. Pppd will create a child process to act as a
# 'character shunt' to transfer characters between the pseudo-tty master and
# its standard input and output. Thus pppd will transmit characters on its
# standard output and receive characters on its standard input even if they are
# not terminal devices. This option increases the latency and CPU overhead of
# transferring data over the ppp interface as all of the characters sent and
# received must flow through the character shunt process. An explicit device
# name may not be given if this option is used.
    notty: ''
# Disable Van Jacobson style TCP/IP header compression in both the transmit and
# the receive direction.
    novj: ''
# Disable the connection-ID compression option in Van Jacobson style TCP/IP
# header compression. With this option, pppd will not omit the connection-ID
# byte from Van Jacobson compressed TCP/IP headers, nor ask the peer to do so.
    novjccomp: ''
# Indicates that all secrets in the '/etc/ppp/pap-secrets' file which are used
# for checking the identity of the peer are encrypted, and thus pppd should not
# accept a password which, before encryption, is identical to the secret from
# the '/etc/ppp/pap-secrets' file.
    papcrypt: ''
# Set the maximum number of PAP authenticate-request transmissions (default 10).
    pap_max_authreq: ''
# Set the PAP restart interval (retransmission timeout). Default is 3.
    pap_restart: ''
# Set the maximum time that pppd will wait for the peer to authenticate itself
# with PAP (0 means no limit).
    pap_timeout: ''
# Specifies a packet filter to applied to data packets being sent or received
# to determine which packets should be allowed to pass. Packets which are
# rejected by the filter are silently discarded. This option can be used to
# prevent specific network daemons (such as routed) using up link bandwidth, or
# to provide a very basic firewall capability. The filter-expression syntax is
# as described for 'tcpdump', except that qualifiers which are inappropriate
# for a PPP link, such as ether and arp, are not permitted. Note that it is
# possible to apply different constraints to incoming and outgoing packets
# using the inbound and outbound qualifiers. This option is currently only
# available under Linux, and requires that the kernel was configured to include
# PPP filtering support (CONFIG_PPP_FILTER).
    pass_filter: ''
# Specifies the password to use for authenticating to the peer. Use of this
# option is discouraged, as the password is likely to be visible to other users
# on the system.
    password: 'pppoe_12312'
# Do not exit after a connection is terminated, instead try to reopen the
# connection. The maxfail option still has an effect on persistent connections.
    persist: 'true'
# Request that the peer compress frames that it sends using Predictor-1
# compression, and agree to compress transmitted frames with Predictor-1 if
# requested. This option has no effect unless the kernel driver supports
# Predictor-1 compression.
    predictor1: ''
# Allows members of group to use privileged options. Use of this option
# requires care as there is no guarantee that members of group cannot use pppd
# to become root themselves. Consider it equivalent to putting the members of
# group-name in the kmem or disk group.
    privgroup: ''
# Add an entry to this system's ARP table with the IP address of the peer and
# the Ethernet address of this system. This will have the effect of making the
# peer appear to other systems to be on the local ethernet.
    proxyarp: ''
# Specifies that the command script is to be used to communicate rather than a
# specific terminal device. Pppd will allocate itself a pseudo-tty master/slave
# pair and use the slave as its terminal device. The script will be run in a
# child process with the pseudo-tty master as its standard input and output. An
# explicit device name may not be given if this option is used. Note, if the
# record option is used in conjunction with the pty option, the child process
# will have pipes on its standard input and output.
    pty: ''
# With this option, pppd will accept all control characters from the peer,
# including those marked in the receive asyncmap. Without this option, pppd
# will discard those characters as specified in RFC1662. This option should
# only be needed if the peer is buggy.
    receive_all: ''
# Specifies that pppd should record all characters sent and received to a file.
# This file is opened in append mode, using the user's user-ID and permissions.
# This option is implemented using a pseudo-tty and a process to transfer
# characters between the pseudo-tty and the real serial device, so it will
# increase the latency and CPU overhead of transferring data over the ppp
# interface. The characters are stored in a tagged format with timestamps,
# which can be displayed in readable form using the pppdump program.
    record: ''
# Set the assumed name of the remote system for authentication purposes to name.
    remotename: ''
# Set the assumed telephone number of the remote system for authentication
# purposes to number.
    remotenumber: ''
# With this option, pppd will not agree to authenticate itself to the peer
# using CHAP.
    refuse_chap: ''
# With this option, pppd will not agree to authenticate itself to the peer
# using MS-CHAP.
    refuse_mschap: ''
# With this option, pppd will not agree to authenticate itself to the peer
# using MS-CHAPv2.
    refuse_mschap_v2: ''
# With this option, pppd will not agree to authenticate itself to the peer
# using EAP.
    refuse_eap: ''
# With this option, pppd will not agree to authenticate itself to the peer
# using PAP.
    refuse_pap: ''
# Require the peer to authenticate itself using CHAP authentication.
    require_chap: ''
# Require the use of MPPE. This option disables all other compression types.
# This option enables both 40-bit and 128-bit encryption. In order for MPPE to
# successfully come up, you must have authenticated with either MS-CHAP or
# MS-CHAPv2. This option is presently only supported under Linux, and only if
# your kernel has been configured to include MPPE support.
    require_mppe: ''
# Require the use of MPPE, with 40-bit encryption.
    require_mppe_40: ''
# Require the use of MPPE, with 128-bit encryption.
    require_mppe_128: ''
# Require the peer to authenticate itself using MS-CHAP authentication.
    require_mschap: ''
# Require the peer to authenticate itself using MS-CHAPv2 authentication.
    require_mschap_v2: ''
# Require the peer to authenticate itself using EAP authentication.
    require_eap: ''
# Require the peer to authenticate itself using PAP authentication.
    require_pap: ''
# Set an environment variable for scripts that are invoked by pppd. When set by
# a privileged source, the variable specified by name cannot be changed by
# options contained in an unprivileged source. See also the unset option and
# the environment described in SCRIPTS.
    set: ''
# When logging the contents of PAP packets, this option causes pppd to show the
# password string in the log message.
    show_password: ''
# With this option, pppd will not transmit LCP packets to initiate a connection
# until a valid LCP packet is received from the peer (as for the `passive'
# option with ancient versions of pppd).
    silent: ''
# If this parameter is given and pppd uses EAP SRP-SHA1 to authenticate the
# peer (i.e., is the server), then pppd will use the optional lightweight SRP
# rechallenge mechanism at intervals. This option is faster than 'eap_interval'
# reauthentication because it uses a hash-based mechanism and does not derive a
# new session key.
    srp_interval: ''
# Set the long-term pseudonym-generating secret for the server. This value is
# optional and if set, needs to be known at the server (authenticator) side
# only, and should be different for each server (or poll of identical servers).
# It is used along with the current date to generate a key to encrypt and
# decrypt the client's identity contained in the pseudonym.
    srp_pn_secret: ''
# When operating as an EAP SRP-SHA1 client, attempt to use the pseudonym stored
# in ~/.ppp_pseudonym first as the identity, and save in this file any
# pseudonym offered by the peer during authentication.
    srp_use_pseudonym: ''
# Use synchronous HDLC serial encoding instead of asynchronous. The device used
# by pppd with this option must have sync support. Currently supports Microgate
# SyncLink adapters under Linux and FreeBSD 2.2.8 and later.
    sync: ''
# Sets the ppp unit number (for a ppp0 or ppp1 interface name) for outbound
# connections.
    unit: '0' # ppp0
# Remove a variable from the environment variable for scripts that are invoked
# by pppd. When specified by a privileged source, the variable name cannot be
# set by options contained in an unprivileged source.
    unset: ''
# With this option, pppd will detach from its controlling terminal once it has
# successfully established the ppp connection (to the point where the first
# network control protocol, usually the IP control protocol, has come up).
    updetach: 'true'
# Enforce the use of the hostname (with domain name appended, if given) as the
# name of the local system for authentication purposes (overrides the name
# option). This option is not normally needed since the name option is
# privileged.
    usehostname: ''
# Ask the peer for up to 2 DNS server addresses. The addresses supplied by the
# peer (if any) are passed to the '/etc/ppp/ip-up' script in the environment
# variables DNS1 and DNS2, and the environment variable USEPEERDNS will be set
# to 1. In addition, pppd will create an '/etc/ppp/resolv.conf' file containing
# one or two nameserver lines with the address(es) supplied by the peer.
    usepeerdns: 'false'
# Sets the name used for authenticating the local system to the peer to name.
    user: 'ppp_user1354'
# Sets the number of connection slots to be used by the Van Jacobson TCP/IP
# header compression and decompression, which must be between 2 and
# 16 (inclusive).
    vj_max_slots: ''
# Run the executable or shell command specified by script before initiating PPP
# negotiation, after the connect script (if any) has completed.
    welcome: ''
# Use software flow control (i.e. XON/XOFF) to control the flow of data on the
# serial port.
    xonxoff: ''
    ifup_local:
    - 'ip route add 0.0.0.0/0 dev ppp0 table red'
    - 'ip rule add from 5.120.220.1 lookup red'
    ifdown_local:
    - 'ip rule delete from 5.120.220.1 lookup red'
```
