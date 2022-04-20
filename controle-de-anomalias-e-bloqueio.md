# Controle de anomalias e bloqueio

Fonte: [Análise de tráfego de redes TCP/IP](https://youtu.be/8KyrUA1nACY?t=3430)
Autor: Eriberto Mota

### Visão geral do processo
- Alguns exemplos de ações e alertas, em tempo real, que poderão ocorrer:
   - Bloqueio de máquinas MS Windows na rede.
   - Bloqueio de máquinas realizando escaneamentos de portas (nmap e outros).
   - Bloqueio de máquinas executando escaneamento de vulnerabilidades (OpenVAS, nikto etc).
   - Bloqueio de máquinas com múltiplos erros de autenticação.
   - Avisos sobre ocorrências de port security em switches Cisco.
   
### Conceitos básicos e ferramentas
- **Syslog:** esse é o principal sistema de logs no GNU/Linux.
- O Debian utiliza o servidor **rsyslog** para prover o syslog.
- O rsyslog é amplamente configurável e permite a coleta de dados locais e remotos para gerar os logs.
- É possível criar regras, inclusive com expressões regulares, para tratar os logs gerados pelo rsyslog.
- **inotify:** monitor de eventos de filesystem, baseado em informações de inodes.
- Os inodes controlam os arquivos e diretórios existentes em um filesystem.
- Filesystems são estabelecidos por formatação de disco.
- O inotify é parte integrante do kernel linux e o seu trabalho é obrigatório para que o SO funcione.
- Em resumo, o **inotify monitora mudanças nos arquivos e diretórios**. Então, poderemos acompanhar atividades em logs.

| **Software** | **Descrição** |  
|--- |---| 
| **rsyslog:** | servidor de logs padrão no Debian e outros. |
| **logger:** | envia logs personalizados para rsyslogs locais ou remotos. |
| **iptables:** | ferramenta do Netfilter que gera logs de rede e faz bloqueios. |
| **snort:** | pode analisar payloads de rede e ferar logs. |
| **suricata:** | faz o mesmo trabalho do snort. |
| **portsentry:** | gera logs sobre port scans e os bloqueia. |
| **curl:** | acessa servidores web, gerando logs, podendo estes serem personalizados. |

### Ferramentas baseadas em inotify
- fail2ban: analisa logs em tempo real e faz bloqueios por tempos pré determinados.
- iwatch: reage, em tempo real, a alterações em filesystems. Pode ser utilizado para verificar logs.

### Ferramentas para comunicação em linha de comando
- sendxmpp: envia mensagens XMPP (Jabber).
- sendmail: envia mensagens de email.
- yowsup-cli: envia mensagens WhatsApp.
- tg (by vysheng): envia mensagens Telegram.

### Ferramentas e sistemas XMPP (Jabber)
- prosody: servidor XMPP com diversos recursos, permitindo criptografia e salas de conferência.
- pidgin: cliente para desktops. Extremamente versátil. Disponível para Linux, Windows e Mac.
- Yaxim: cliente leve para android. Mantém-se, por default, em segundo plano, da mesma forma como faz o WhatsApp. Permite criptografia.
- Frreelab: cliente para android, com segundo palno e criptografia configuráveis (não é o default).

# Geração de logs
- O rsyslog pode ser configurado para receber logs remotos. A porta default é a 514 UDP.
- Para isso, no Debian, basta descomentar as seguintes linhas em `/etc/rsyslog.conf`:
~~~
# provides UDP syslog reception
module(load="imudp")
input(type="imudp" port="514")
~~~
- É uma boa prática utilizar o iptables para dizer quem poderá acessar a porta 514 UDP do servidor.
  
### Gerar log local com o logger (pode ser via usuário comum do sistema);
~~~
:~$ logger "Teste de Log"
:~# tail -n1 /var/log/syslog
Oct 17 14:40:01 coiote genildo: Teste de log
~~~

### Gerar log com ID do processo e TAG:
~~~
:~$ logger -i -t TESTE "Teste de log"
:~# tail -n1 /var/log/syslog
Oct 17 14:41:21 coiote TESTE[32146]: Teste de log
~~~

### Gerar log em servidor remoto (-d = UDP)
~~~
:~$ logger -n logserver.com.ex -d -P 514 -i -t TESTE "teste de log"
Oct 17 14:52:11 192.168.0.100 TESTE[32329]: teste de log
~~~

Se mais de um nome for colocado na TAG, o primeiro será interpretado como sendo o nome do host. Dessa forma é possível registrar o nome da máquina ao invés do endereço IP.
~~~
:~$ logger -n logserver.com.ex -d -P 514 -i -t 'coiote TESTE' "teste de log"
Oct 17 14:54:13 coiote TESTE[32329]: teste de log
~~~
  
- O rsyslog permite filtragens, fazendo com que cada tipo de log caia em um arquivo de log diferente do **/var/log/syslog**, que é o default.
- Os arquivos de configuração de filtros deverão ter a extensão `.conf` e ser colocados em **/etc/rsyslog.d/**. É possível ter todas as regras em um único arquivo.
- Como exemplo, a linha a seguir enviará todas as mensagens que contém a **TAG ALERTA** para `/var/log/01_SERVERS/ALERTA.log`.
~~~
:syslogtag, contains, "ALERTA" /var/log/01_SERVERS/ALERTA.log
~~~

### Composição básica da linha de log
~~~
Oct 17 14:54:13 coiote TESTE[32329]: teste de log

TIMESTAMP    HOSTNAME    programname    syslogtag    msg
~~~
- Dependendo do log haverá outros campos possíveis. Para mais detalhes: `man rsyslog.conf`.
- Dentro de um arquivo, as regras serão processadas na ordem em que aparecem.

### Outros exemplos de filtro

Mensagens oriundas de 10.0.0.1 irão para `/var/log/01_SERVERS/01-CISCO.log`.  
~~~
:fromhost-ip, contains, "10.0.0.1" /var/log/01_SERVERS/01-CISCO.log
& stop
~~~

Endereços privados (RFC1918) saindo para a internet. mensagem contendo `RFC1918` irão para `/var/log/01_SERVERS/ALERTA.log`.
~~~
:msg, contains, "RFC1918" /var/log/01_SERVERS/ALERTA.log
& stop
~~~

Regra com expressão regular
~~~
:msg, regex, "fatal .* error" /var/log/01_SERVERS/ALERTA.log
& stop
~~~

Separar logs por nome de servidor usando template
~~~
$template Servers, "/var/log/01_SERVERS/%HOSTNAME%/%PROGRAMNAME%.log"
*.* ?Servers
& stop
~~~

> [Referência](http://www.rsyslog.com/doc/v8-stable/configuration/filters.html)

### Direcionar os logs para o servidor remoto de logs

