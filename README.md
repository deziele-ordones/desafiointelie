# Desafio Intelie

# Problema Proposto
![alt text](https://github.com/intelie/challenge-remote-access/raw/master/example%20network%20infrastructure.png "Example network infrastructure")
The only allowed connection between Client A and Server A is via SSH. The only allowed connection between Server A and Server B is via SSH.

We need to access, from Client A and using HTTP, a service running on port 8000 of Server B.

Notes and restrictions:

- There is no direct route from Client A to Server B and there is no practical way to create one. Imagine the following scenario as an example: Server B belongs to a client's internal datacenter and we receive a VPN that allows access only to server A's SSH port.
- There is another service running on port 8000 of Server A, we should not impact this.


# Solução Proposta

The suggestion would be to create SSH tunnels, the ssh protocol can encapsulate other traffic between two hosts. The ssh tunnel reaches inaccessible networks and systems.
SSH tunneling is a method of transporting arbitrary network data over an encrypted SSH connection. Can be used to add encryption to legacy applications. It can also be used to deploy virtual private networks (VPNs) and access intranet services through firewalls.

Below is an example of an SSH tunnel implementation:

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

## Encaminhamento de porta SSH remota

Nesse cenário, estamos criando um túnel ssh reverso. Aqui, podemos iniciar um túnel ssh em uma direção e usá-lo para criar um túnel ssh de volta para o outro lado. Isso pode ser útil para quando você solta um computador drone dentro de uma rede e deseja que ele “ligue para casa”. Então, quando ligar para casa, você poderá conectar-se a ele através do túnel ssh estabelecido.

![alt text](https://github.com/deziele-ordones/desafiointelie/blob/master/ssh-remote.png "infra")

Estamos no host verde e queremos ssh no host azul. No entanto, o firewall bloqueia essa conexão diretamente. Como o host azul pode ssh para o host verde, podemos conectar usando isso e, quando o host verde deseja voltar ao host azul, ele pode percorrer esse túnel previamente estabelecido.

O host azul inicia o túnel ssh assim:

- ssh -R 2222:localhost:22 greenuser@192.168.0.2

Isso abre a porta 2222 no host verde, que é o encaminhamento de porta para a porta 22 no host azul. Portanto, se o host verde fosse ssh para si mesmo na porta 2222, chegaria ao host azul.

Agora, o host verde pode ssh para host azul como este:

- ssh -p 2222 blueuser@localhost

## Usando a opção -N

Ao usar o ssh, você pode especificar o -Nsinalizador que informa ao ssh que você não precisa enviar nenhum comando pela conexão ssh quando ela estiver estabelecida. Essa opção costuma ser usada ao criar túneis, pois muitas vezes não precisamos receber um prompt.

## Autossh
O comando autossh é usado para adicionar persistência aos seus túneis. O trabalho que ele tem é verificar se sua conexão ssh está ativa e, se não estiver, crie-a.

Aqui está um comando autossh que você pode reconhecer.
autossh -N -i /home/blueuser/.ssh/id_rsa -R 2222:localhost:22 greenuser@192.168.0.3

Agora, quando seu túnel desce, ele automaticamente tentará se reconectar e continuará tentando até obter êxito. Para torná-lo persistente durante uma reinicialização, adicione o comando ssh como um trabalho cron.



