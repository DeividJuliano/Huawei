# Huawei
Tutoriais visto do you tube
Huawei

Configurar OLT Huawei

A olt será acessado via cabo serial usando um MKT, No MKT na linha de comando digite: port set serial0 baud-rate=9600 #velocidade de  comunicação serial
system serial-terminal serial0

A OLT pedirá usuário:root e senha:admin123, senha padrão.

comandos para criar vlans e adicionar na interface uplink 0/3
enable 
config  #modo de configuração

board confirm 0      #confirma os slots
display board 0    #mostra as configurações do slot 0

vlan 100 smart    #criar vlan
port vlan 100 0/3 0   #a vlan terá o acesso pela porta 0/3
display board 0/3 # mostra as configurações da interface 0/3 

Criar DBA-PROFILE;
dba-profile add profiled-id 500 profile-name “GPON” type max 1024000

Criar o SRV-PROFILE
ont-srvprofile gpon profile-id 500 profile-name “GPON”

Vincular as interfaces
ont-port pots adaptative 32 eth adaptative 8
ont vlan eth 1 translation 100 user-vlan 100  #traduz o serviço vlan
commit   #aplicar as configurações
quit    #para sair

Criar o line profile
ont-lineprofile gpon profile-id 500 profile-name “GPON”
tcont 4 dba-profile-id 500  # adiciona o DBA profile
gem add 37 eth tcont 4     # adiciona as interfaces gem tcont
gem mapping 37 0 vlan 100  #
commit
quit
Ativar as portas PON
interface gpon 0/1  #entra na interface
port 0 ont-auto-find enable #habilita a interface para ser encontrada de forma automática
port 1 ont-auto-find enable
port 2 ont-auto-find enable 
quit
quit

# após esse comando será possível visualizar as onus conectadas na olt
display ont autofind all   #comando para visualizar as onus

Cadastrar onu
config
interface gpon 0/1
ont add 0 sn-auth (cole o número que em ont sn mostrado no display ont autofind all) omci ont-line profile-id 500 ont-srvprofile-id 500 desc “(cole a letra e número mostrado no ont sn que está dentro do parêntese)”
display ont info summary 0  #mostrar o status da ont provisionada , distância, tipo, serial, o ideal de sinal na onu é -13 a -20. conta inversa -7 muito sinal e -25 pouco sinal

Para excluir 
ont delete 1 0 

Adicionar vlan nativa

ont port native-vlan 0 0 eth 1 vlan 100 priority 0  #se tiver outras vlans é só adicionar usando esse comando 
quit 

Adicionar serviço
service-port 1 vlan 100 gpon 0/1/0 ont 0 gemport 37 multi-service user-vlan 100 tag-transform translate

display ont info summary 0
save   #para salvar toda a configuração

display current-configuration   #configurações atuais rodando

Para adicionar vlan de gerência( vlan existente em uma interface)
interface vlanif 103
ip address 100.127.255.2 255.255.255.252
save
