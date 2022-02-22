## Análise de tráfego em redes TCP/IP com tcpdump.
- IP, RFC 791. O protocolo mais importante da família TCP/IP.
~~~
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|Version|  IHL  |Type of Service|          Total Length         |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|         Identification        |Flags|      Fragments Offset   |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|  Time to Live |    Protocol   |         Header Checksum       |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                       Source Address                          |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                       Options                 |    Padding    |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~
- Usando o programa `mtr` para descobrir a rota até um determinado destino. A opção `-n` serve para não resolver nomes.
~~~
:~$ mtr -n yahoo.co.jp
~~~

- O IPV6 não necessita calcular o checksum do cabeçalho, por isso é mais rápido.
- Protocolos IP: são protocolos que são encapsulados pelo IP. São listados pela [IANA](http://www.iana.org/assignments/protocol-numbers) e um resumo poderá ser encontrado em `/etc/protocols`.
~~~
:~$ cat /etc/protocols
# Internet (IP) protocols
#
# Updated from http://www.iana.org/assignments/protocol-numbers and other
# sources.
# New protocols will be added on request if they have been officially
# assigned by IANA and are not historical.
# If you need a huge list of used numbers please install the nmap package.

ip      0       IP              # internet protocol, pseudo protocol number
hopopt  0       HOPOPT          # IPv6 Hop-by-Hop Option [RFC1883]
icmp    1       ICMP            # internet control message protocol
igmp    2       IGMP            # Internet Group Management
ggp     3       GGP             # gateway-gateway protocol
ipencap 4       IP-ENCAP        # IP encapsulated in IP (officially ``IP'')
st      5       ST              # ST datagram mode
tcp     6       TCP             # transmission control protocol
egp     8       EGP             # exterior gateway protocol
igp     9       IGP             # any private interior gateway (Cisco)
pup     12      PUP             # PARC universal packet protocol
udp     17      UDP             # user datagram protocol
hmp     20      HMP             # host monitoring protocol
xns-idp 22      XNS-IDP         # Xerox NS IDP
rdp     27      RDP             # "reliable datagram" protocol
iso-tp4 29      ISO-TP4         # ISO Transport Protocol class 4 [RFC905]
dccp    33      DCCP            # Datagram Congestion Control Prot. [RFC4340]
xtp     36      XTP             # Xpress Transfer Protocol
ddp     37      DDP             # Datagram Delivery Protocol
idpr-cmtp 38    IDPR-CMTP       # IDPR Control Message Transport
ipv6    41      IPv6            # Internet Protocol, version 6
ipv6-route 43   IPv6-Route      # Routing Header for IPv6
ipv6-frag 44    IPv6-Frag       # Fragment Header for IPv6
idrp    45      IDRP            # Inter-Domain Routing Protocol
rsvp    46      RSVP            # Reservation Protocol
gre     47      GRE             # General Routing Encapsulation
esp     50      IPSEC-ESP       # Encap Security Payload [RFC2406]
ah      51      IPSEC-AH        # Authentication Header [RFC2402]
skip    57      SKIP            # SKIP
ipv6-icmp 58    IPv6-ICMP       # ICMP for IPv6
ipv6-nonxt 59   IPv6-NoNxt      # No Next Header for IPv6
ipv6-opts 60    IPv6-Opts       # Destination Options for IPv6
rspf    73      RSPF CPHB       # Radio Shortest Path First (officially CPHB)
vmtp    81      VMTP            # Versatile Message Transport
eigrp   88      EIGRP           # Enhanced Interior Routing Protocol (Cisco)
ospf    89      OSPFIGP         # Open Shortest Path First IGP
ax.25   93      AX.25           # AX.25 frames
ipip    94      IPIP            # IP-within-IP Encapsulation Protocol
etherip 97      ETHERIP         # Ethernet-within-IP Encapsulation [RFC3378]
encap   98      ENCAP           # Yet Another IP encapsulation [RFC1241]
#       99                      # any private encryption scheme
pim     103     PIM             # Protocol Independent Multicast
ipcomp  108     IPCOMP          # IP Payload Compression Protocol
vrrp    112     VRRP            # Virtual Router Redundancy Protocol [RFC5798]
l2tp    115     L2TP            # Layer Two Tunneling Protocol [RFC2661]
isis    124     ISIS            # IS-IS over IPv4
sctp    132     SCTP            # Stream Control Transmission Protocol
fc      133     FC              # Fibre Channel
mobility-header 135 Mobility-Header # Mobility Support for IPv6 [RFC3775]
udplite 136     UDPLite         # UDP-Lite [RFC3828]
mpls-in-ip 137  MPLS-in-IP      # MPLS-in-IP [RFC4023]
manet   138                     # MANET Protocols [RFC5498]
hip     139     HIP             # Host Identity Protocol
shim6   140     Shim6           # Shim6 Protocol [RFC5533]
wesp    141     WESP            # Wrapped Encapsulating Security Payload
rohc    142     ROHC            # Robust Header Compression
~~~
 ## Flags TCP
 - Syn (synchronize): inicia conexões.
 - Fin (finish): finaliza conexões.
 - Psh (push): envia dados.
 - Ack (acknowledgment): confirmação de que é conhecido o número de sequência do próximo segmento a ser enviado pelo lado oposto.
 - Rst (reset): "não entendi".
   
Importante: as flags TCP são disparadas contra portas e somente a flag push possui payload.
 
## Protocolo UDP
- UDP, RFC 768. O protocolo de transporte masi rápido da família TCP/IP
~~~
 0      7 8     15 16    23 24    31 
+--------+--------+--------+--------+
|     Source      |   Destination   |
|      Port       |      Port       |
+--------+--------+--------+--------+
|     Length      |    Checksum     |
+--------+--------+--------+--------+
~~~
- Somente os protocolos TCP e UDP possuem portas.
- Sempre que houver uma nova conexão TCP ou UDP, a porta do cliente mudará.
- O protocolo UDP é mais rápido mas exige que todo o controle de fluxo seja feito pela aplicação, ao contrário do TCP.

## Protocolo ICMP
- ICMP, RFC 792. O protocolo de controle do IP.
~~~
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|     Type      |     Code      |          Checksum             |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                              ICMP                             |
~~~
**Exemplos:**
- Tipo 8: echo request.
- Tipo 0: echo reply.
- Tipo 3, código 3: porta de destino inacessível.
- Tipo 11, código 0: TTL expirado em trânsito.

## O Modelo OSI
- Modelo criado pela ISO para que fabricantes de hardware de rede possam desenvolver equipamentos compatíveis entre si.
~~~
Dados          Aplicação        Usuario, http, ftp, smtp, pop3, chat etc
Dados          Apresentação     SSL, conversão de padrões, (des)compressão
Dados          Sessão           Sessão de aplicações
Segmentos      Transporte       TCP, UDP
Pacotes[1]     Rede             IP e protocolos IP (exceto TCP e UDP) / roteador
Quadros[2]     Enlace           Ethernet, ATM, PPP, frame relay / switch, bridge
Bits           Física           Hub, cabos, placa de rede, ondas wireless etc
~~~
[1] pacotes ou datagramas  
[2] quadros ou frames  
  
## Protocolo Ethernet
~~~
|-------------------------------------------------------------------------------------------------------| 
|     80 00 20 7A 3F 3E   |   80 00 20 20 3A AE     |        P, ARP, etc.     |      00 20 20 3A        |
| Destination MAC Address |   Source MAC Address    |          Payload        |      CRC Checksum       |
|-------------------------------------------------------------------------------------------------------|
|               MAC Header (14 bytes)               |  Data (46 - 1400 bytes) |        (4 bytes)        |
|-------------------------------------------------------------------------------------------------------|
|                              ETHERNET TYPE II FRAME (64 to 1518 bytes)                                |
|-------------------------------------------------------------------------------------------------------|
~~~
**MTU :** Máxima quantidade de dados que o payload da camada 2 consegue transmitir.  


