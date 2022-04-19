# Controle de anomalias e bloqueio

Fonte: [Análise de tráfego de redes TCP/IP](https://youtu.be/8KyrUA1nACY?t=1888)
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
- Os inodes controlan os arquivos e diretórios existentes em um filesystem.
- Filesystems são estabelecidos por formatação de disco.
- O inotify é parte integrante do kernel linux e o seu trabalho é obrigatório para que o SO funcione.
- Em resumo, o **inotify monitora mudanças nos arquivos e diretórios**. Então, poderemos acompanhar atividades em logs.

| Software | Descrição |  
|--- |---| 
| **rsyslog:** | servidor de logs padrão no Debian e outros. |
| **logger:** | envia logs personalizados para rsyslogs locais ou remotos. |
| **iptables:** | ferramenta do Netfilter que gera logs de rede e faz bloqueios. |
| **snort:** | pode analisar payloads de rede e ferar logs. |
| **suricata:** | faz o mesmo trabalho do snort. |
| **portsentry:** | gera logs sobre port scans e os bloqueia. |
| **curl:** | acessa servidores web, gerando logs, podendo estes serem personalizados. |
