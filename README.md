# cdc-kafka-demo

Change Data Capture with Kafka

Clone the repository:

```
git clone git@github.com:vitorpavani/cdc-kafka-demo.git

cd cdc-kafka-demo
```

1. Create Kubernetes Cluster with Kind:

```
kind create cluster --config kind/cluster.yaml
```

Validated the cluster with Lens. Install Lens from https://k8slens.dev/

2. Install Postgres Operator:

You can install the latest operator manifest for this minor release as follows:

```
kubectl apply -f \
  https://raw.githubusercontent.com/cloudnative-pg/cloudnative-pg/release-1.22/releases/cnpg-1.22.1.yaml
```

You can verify that with:

```
kubectl get deployment -n cnpg-system cnpg-controller-manager
```

Create a Postgres instance:

```
kubectl apply -f postgres/initdb.yaml
```

3. Install MariaDB Operator:

You can install the latest operator manifest for this minor release as follows:

```
# Create a MariaDB instance:
kubectl create namespace mariadb
kubectl -n mariadb apply -f mariadb/mariadb.yaml
```

4. Install Kafka Operator:

You can install the operator manifest for this minor release as follows:

```
# Install the Kafka CRDs
kubectl create namespace kafka
kubectl apply -f kafka/
```

---

PARTE 1

Passo a passo de “Como transformar alterações nos bancos de dados em um stream de eventos utilizando KAFKA e publicar em outros bancos de dados ou e outro serviço”.

    1. Mostrar bancos de dados de tecnologias diferentes se conectando de alguma maneira

Por que isso é interessante?

Porque numa empresa já estabelecida já existem bancos de dados instalados, e muitas vezes ficar buscando a informação para poder saber o que precisa ser feito, é um processo lento e pouco eficiente. Quando a gente troca pra um stream de eventos, as alterações serão publicadas nos serviços que tem interesses nesses. eventos.

Exemplo básico: sistema de pagamento gera um invoice de uma operação. Ao criar um novo registro de invoice no banco de dados o sistema gera este evento no KAFKA e notifica o sistema de cobrança pra notificar o cliente do novo invoice.

Tutorial

    1 Instalar um Cluster Kubernetes usando Kind;
        1.1 Instalar o Kind;
        1.2 Criar o Cluster;
        1.3 Conectar no Cluster LENS.

-- DONE

    2 Instalar PostgresPD Operator e iniciar um banco de dados;
        2.1 Instalar PostgresPD Operator;
        2.2 Instalar banco de dados PostgresPD usando Operator;
        2.3 Conectar no banco de dados.

-- DONE

    3 Instalar MariaDB Operator e iniciar um banco de dados MYSQL;
        3.1 Instalar MariaDB Operator;
        3.2 Instalar banco de dandos MariaDB usando Operator;
        3.3 Conectar no banco de dados.

    4 Instalar KAFKA utilizando o Strimzi Operator;
        4.1 Instalar Strimzi Operator;
        4.2 Instalar KAFKA;
        4.3 Criar um usuário e um tópico;
        4.4 Usar esse usuário para mandar uma mensagem e visualizá-la.

    5 Instalar um CDC Source Conector e conectá-lo no KAFKA;
        5.1 Montar uma imagem do CDC Conector com o Postgres Plugin;
        5.2 Publicar no Docker Hub;
        5.3 Instalar o CDC Conector no Cluster;
        5.4 Conectar o Plugin do CDC no Postgres;
        5.5 Visualizar uma alteração feita no Postgres utilizando um tópico do KAFKA.

PARTE 2

    6 Utilizando o CDC Conector começar a capturar as alterações em ambos os bancos de dados;
        6.1 Montar uma imagem do CDC Conector com MYSQL Plugin;
        6.2 Publicar no Docker Hub;
        6.3 Instalar o CDC Conector no Cluster;
        6.4 Conectar este CDC no Maria DB usando o MYSQL Plugin;
        6.5 Visualizar uma alteração feita no MariaDB utilizando um tópico do KAFKA.

    7 Capturar esses eventos e publicar num Rediscache;
        7.1 Instalar um Rediscache;
        7.2 Fazer um microserviço usando NODEJS para receber as mensagens dos tópicos do KAFKA e salvá-las no Rediscache.
            7.2.1 Iniciar um serviço no NODEJS;
            7.2.2 Instalar o pacote KAFKAJS e Redis;
            7.2.3 Criar um pipe de eventos para receber as mensagens do KAFKA e salvá-las no Redis
            7.2.4 Publicar a imagem no Docker Hub;
            7.2.5 Instalar a imagem no Cluster.

    8 Testando todo o ambiente montado;
        8.1 Criando novos itens nos bancos de dados
            8.1.1 Criar 10 novos itens no Postgres;
            8.1.2 Criar 10 novos itens no MariaDB;
            8.1.3 Visualizar os itens criados no Rediscache.
        8.2 Editar itens nos bancos de dados;
            8.2.1 Editar 2 itens no Postgres;
            8.2.2 Editar 2 itens no MariaDB;
            8.2.3 Visualizar atualizados no Rediscache.
        8.3 Deletar itens nos bancos de dados;
            8.3.1 Deletar 2 itens no Postgres;
            8.3.2 Deletar 2 itens no MariaDB;
            8.3.3 Visualizar no Rediscache.

    9 Para finalizar, destruir o Kubernetes.

Essa arquitetura é capaz de capturar em tempo real as alterações dos bancos de dados e disponibilizá-las para outros sistemas sem colocar pressão nos bancos de dados.

Por que isso é interessante?

Esse modelo oferece flexibilidade e escalabilidade. Por quê? Porque ao invés de multiplus serviços conectarem diretamente nos bancos de dados e ficarem realizando várias buscas tentando capturar as alterações nos bancos de dados, e assim, causando uma pressão no banco de dados e porventura, provocando uma baixa performance.

Neste modelo apresentado, as alterações são enviadas ao vivo para todos os stakeholders e cada um realiza a sua operação. Ou seja, ao acrescentar um novo microserviço no ambiente, não causará um pressão extra no banco de dados. Por exemplo, dois microserviços ou 20 microserviços terão o mesmo impacto no banco de dados.

Sem falar que o mircroserviço está recebendo informações de bancos de dados de tecnologias diferentes sem conectar diretamente neles. Trazendo mais segurança e flexibilidade, pois não precisa mais instalar os pacotes do Postgres e do Maria DB.

Vale ressaltar que a pressão sai dos bancos de dados e vai para KAFKA. Por isso ele precisa ser muito bem dimensionado.
