Para entender o fluxo de uma requisição em um cluster Kubernetes e como cada componente se relaciona, podemos dividir o processo em várias camadas:
1. Camada do Usuário (Usuário e Internet)

    Usuário: O ponto de partida, onde o usuário faz uma requisição (ex: um acesso HTTP a um site) via Internet.

2. Camada de Entrada no Cluster (Ingress e Service)

    Ingress: O Ingress é uma porta de entrada para o cluster que gerencia o tráfego HTTP/HTTPS externo. Ele recebe a requisição do usuário e aplica regras de roteamento, como direcionar para o serviço correto.
    Service: O Service é um objeto Kubernetes que expõe um conjunto de Pods e define como o tráfego para esses Pods deve ser balanceado. O Ingress encaminha a requisição para o Service correspondente ao aplicativo.

3. Camada do Control Plane (API Server, etcd, Scheduler, Controller Manager)

    API Server: Recebe todas as requisições administrativas, como o envio de definições de recursos (Deployment, Pods) para serem aplicadas. No caso de uma requisição de criação de um Deployment, o usuário se comunica com o API Server via kubectl ou outra interface.
    etcd: O etcd é o banco de dados que armazena o estado atual e o desejado do cluster. O estado desejado dos recursos, como a definição de Pods, é armazenado aqui.
    Scheduler: O Scheduler analisa as necessidades do Pod e seleciona o melhor Node (máquina física ou virtual) no cluster para executá-lo.
    Controller Manager: Garante que o estado real do cluster corresponda ao estado desejado. Ele observa objetos como Deployments e ReplicaSets e, se necessário, cria novos Pods para alcançar o número desejado de réplicas.

4. Camada do Worker (Node, Kubelet, Container Runtime, Kube-proxy)

    Node: Cada máquina no cluster que executa as cargas de trabalho. Pode ser uma VM ou um servidor físico.
    Kubelet: O agente que roda em cada Node. Ele é responsável por garantir que os contêineres estejam rodando conforme especificado pelo API Server.
    Container Runtime: O software responsável por rodar os contêineres. O Kubernetes pode usar diferentes runtimes, como Docker, containerd ou CRI-O.
    Kube-proxy: Este componente no Node é responsável pelo roteamento de tráfego de rede para os Pods, garantindo que as comunicações entre serviços e pods aconteçam corretamente.

5. Camada de Execução (Pod e Container)

    Pod: A menor unidade executável no Kubernetes, que contém um ou mais contêineres.
    Container: Cada contêiner dentro do Pod executa uma instância de uma aplicação. O container engine (ex: Docker) no Node é responsável pela execução do contêiner.

Fluxo do Processo em Kubernetes
Etapa 1: Solicitação do Usuário

    O Usuário faz uma requisição via Internet para acessar o aplicativo.
    O tráfego externo chega ao Ingress, que lida com roteamento HTTP e HTTPS.
    O Ingress direciona o tráfego para o Service apropriado, que mapeia para um conjunto de Pods.

Etapa 2: Roteamento e Execução no Cluster

    O Service decide para qual Pod a requisição deve ser enviada, utilizando o kube-proxy para fazer o balanceamento de carga entre os Pods.
    A requisição chega ao Pod, onde o contêiner rodando no Container Runtime processa a solicitação.

Etapa 3: Processamento de Requisições de Criação/Atualização

    Um administrador cria ou modifica um recurso (ex: um Deployment) usando o kubectl, que envia uma requisição ao API Server.
    O API Server valida a requisição e atualiza o etcd com o estado desejado (ex: criação de novos Pods).
    O Scheduler escolhe o Node onde os Pods serão executados.
    O Controller Manager monitora o estado real e o estado desejado, gerenciando réplicas de Pods e outros recursos.
    O Kubelet no Node alocado gerencia a execução do Pod, interagindo com o Container Runtime para criar ou destruir contêineres conforme necessário.
