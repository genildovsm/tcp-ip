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
## Como é calculado o Header Checksum
O checksum é calculado de 2 em 2 bytes. Por exemplo: acima temos `Version` e `IHL`, ambos com 4 bits cada, totalizando 1 byte. Então quando circular o Version e IHL na rede, é 1 byte contendo os dois.  
O `Type of Service` é 1 byte e o `Total Length` são 2 bytes.  
Pega os dois primeiros bytes compostos pelo `Version`, `IHL` e `Type of Service`, soma-se aos próximos 2 bytes que é o `Total Length`, soma-se com os próximos 2 bytes que é o `Identification`, soma-se com os próximos dois bytes que é o `Flags` e o `Fragment Offset`, soma-se aos próximos 2 bytes que é o `Time to Live` e o `Protocol`, ignora-se o próximo que é o Header Checksum, soma-se a primeira metade do `Source Address` que corresponde a 2 bytes, depois soma-se a outra metade do `Source Address`, o mesmo para `Destination Address`, até o final do cabeçalho.
