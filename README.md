# Desafio Intelie

# Problema Proposto
![alt text](https://github.com/intelie/challenge-remote-access/raw/master/example%20network%20infrastructure.png "Example network infrastructure")
A única conexão permitida entre o Cliente A e o Servidor A é via SSH. A única conexão permitida entre o Servidor A e o Servidor B é via SSH.

Precisamos acessar, do Cliente A e usando HTTP, um serviço em execução na porta 8000 do Servidor B.

Notas e restrições:

- Não há rota direta do Cliente A para o Servidor B e não há maneira prática de criar uma. Imagine o seguinte cenário como exemplo: O servidor B pertence a um datacenter interno do cliente e recebemos uma VPN que permite o acesso apenas à porta SSH do servidor A.
- Há outro serviço em execução na porta 8000 do Servidor A, não devemos causar impactos neste.



# Solução Proposta

A sugestão seria a criação de túneis SSH, o protocolo ssh pode encapsular outro tráfego entre dois hosts. O túnel ssh alcança redes e sistemas inacessíveis.
O encapsulamento SSH é um método de transporte de dados de rede arbitrários através de uma conexão SSH criptografada. Pode ser usado para adicionar criptografia a aplicativos herdados. Também pode ser usado para implementar VPNs (redes privadas virtuais) e acessar serviços da intranet através de firewalls.

Abaixo segue um exemplo de uma implementação de túnel SSH:

![alt text](https://github.com/deziele-ordones/desafiointelie/blob/master/ssh-local2.png "Exemple network infrastructure")



Na imagem acima, o host azul não pode alcançar, http://192.168.0.3 mas pode conectar via ssh para 192.168.0.2. O seguinte comando ssh executado no host azul permitirá que o host azul alcance o host vermelho.


- ssh -L 8080:192.168.0.3:80 reduser@192.168.0.2

Agora, o host azul pode abrir um navegador, acessar http://localhost:8080 e ser apresentado à página da Web hospedada em 192.168.0.3.

![alt text](https://github.com/deziele-ordones/desafiointelie/blob/master/ssh-local1.png "Exemple Network Infra")

Na imagem acima, o host azul deseja conectar-se ao host vermelho na porta 80, mas há um firewall no meio que nega isso. Como o host azul pode conectar via ssh para o host vermelho, podemos criar um túnel ssh de encaminhamento de porta local para acessar essa porta.

O comando no host azul será:

- ssh -L 8080:192.168.0.2:80 reduser@192.168.0.2

Agora, quando o host azul abrir um navegador e acessar, http://localhost:8080 eles poderão ver o que o servidor vermelho tiver na porta 80.

Sintaxe de encaminhamento de porta local
Esta sintaxe para criar um túnel de encaminhamento de porta ssh local é esta:

- ssh -L LPORT:RHOST:RPORT GATEWAY 

Encaminhamento de porta SSH remota

Nesse cenário, estamos criando um túnel ssh reverso. Aqui, podemos iniciar um túnel ssh em uma direção e usá-lo para criar um túnel ssh de volta para o outro lado. Isso pode ser útil para quando você solta um computador drone dentro de uma rede e deseja que ele “ligue para casa”. Então, quando ligar para casa, você poderá conectar-se a ele através do túnel ssh estabelecido.

![alt text](https://github.com/deziele-ordones/desafiointelie/blob/master/ssh-remote.png "infra")


