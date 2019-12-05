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

Agora, o host azul pode abrir um navegador, acessar http://localhost:8080e ser apresentado à página da Web hospedada em 192.168.0.3.



