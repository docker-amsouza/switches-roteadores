# Cisco
Configuração Básica do Roteador Cisco 1941/K9

Este arquivo README fornece um guia básico para configurar e operar o roteador Cisco 1941/K9, incluindo configurações iniciais, conectividade, e comandos essenciais.
Especificações Principais do Cisco 1941/K9

    Interfaces:
        2 interfaces Gigabit Ethernet.
        Slots para módulos de expansão (HWIC, EHWIC).
        Suporte a interfaces WAN como PPPoE.
    Segurança: Suporte a IPSec, VPN e firewall integrado.
    Gerenciamento: Console serial, SSH, Telnet, e interfaces web (opcional).
    Sistema Operacional: Cisco IOS.

Configuração Inicial

    Acesso ao roteador:
        Conecte-se via porta Console usando cabo serial e software como PuTTY ou Tera Term.
        Acesse usando o comando:

    Router> enable

Definição de senhas:

    Configure uma senha segura para o modo privilegiado:

    Router# configure terminal
    Router(config)# enable secret <sua_senha>

Configuração do console:

    Defina a senha de acesso local:

    Router(config)# line con 0
    Router(config-line)# password <sua_senha>
    Router(config-line)# login
    Router(config-line)# exit

Configuração para acesso remoto (Telnet/SSH):

    Configure as linhas virtuais para conexões remotas:

        Router(config)# line vty 0 4
        Router(config-line)# password <sua_senha>
        Router(config-line)# login
        Router(config-line)# exit

Configuração de Rede

    Configuração da LAN:
        Atribua um endereço IP à interface LAN:

    Router(config)# interface GigabitEthernet0/0
    Router(config-if)# ip address 192.168.0.1 255.255.255.0
    Router(config-if)# no shutdown
    Router(config-if)# exit

Configuração de NAT:

    Defina NAT para compartilhar a conexão WAN com a LAN:

    Router(config)# ip nat inside source list 1 interface dialer 1 overload
    Router(config)# access-list 1 permit 192.168.0.0 0.0.0.255

Configuração da WAN (PPPoE):

    Configure a interface PPPoE para conexão com o ISP:

    Router(config)# interface GigabitEthernet0/1
    Router(config-if)# pppoe-client dial-pool-number 1
    Router(config-if)# no shutdown
    Router(config-if)# exit

    Router(config)# interface dialer 1
    Router(config-if)# mtu 1480
    Router(config-if)# encapsulation ppp
    Router(config-if)# ip address negotiated
    Router(config-if)# ppp authentication chap
    Router(config-if)# ppp chap hostname <seu_usuario>
    Router(config-if)# ppp chap password <sua_senha>
    Router(config-if)# dialer pool 1
    Router(config-if)# ip nat outside
    Router(config-if)# exit

Configuração da Rota Padrão:

    Instrua o roteador a encaminhar todo tráfego desconhecido para a internet:

        Router(config)# ip route 0.0.0.0 0.0.0.0 dialer 1

Salvando Configurações

    Para salvar as configurações permanentemente:

    Router# copy running-config startup-config

Solução de Problemas

    Verificar interfaces:
        Veja o status das interfaces:

    Router# show ip interface brief

Testar conectividade:

    Teste a conectividade usando o comando ping:

    Router# ping <endereço_ip>

Examinar tabela de roteamento:

    Verifique as rotas configuradas:

        Router# show ip route

Recuperação de Senhas

    Se o roteador estiver bloqueado por uma senha perdida:
        Reinicie o roteador em ROMmon.
        Execute os comandos:

    rommon 1 > confreg 0x2142
    rommon 2 > reset

Após o reset:

    Entre no roteador, restaure as configurações salvas:

        Router> enable
        Router# copy startup-config running-config
        Router(config)# config-register 0x2102

Notas Finais

    Segurança:
        Sempre use senhas fortes e criptografadas.
        Ative o SSH para conexões seguras (em vez de Telnet).
    Backup:
        Mantenha backups das configurações usando TFTP ou FTP.
