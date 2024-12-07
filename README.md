# GoCase - Desafio DevOps
## Sobre os repositórios
Este repositório contém uma ligação para dois outros repositórios do desafio, um com a aplicação Front-end em React/Vite e outro com o Back-end Ruby on Rails com a API que é utilizada pelo front. No repositório atual você irá encontrar o arquivo docker-compose responsável por executar a aplicação por completo por meio da orquestração dos três serviços em containeres (front, back, banco de dados).

Nos ademais repositórios encontram-se os arquivos *dockerfile* que conteinerizam cada uma das aplicações individualmente para gerar suas imagens. No caso do front também encontra-se a pipeline para seu deploy na Vercel na pasta *.github/workflows*. <br>
Vale lembrar que a aplicação do **front não irá funcionar como um todo** sem o back-end, que é o caso do deploy na Vercel. <br>
**Atenção: Todas as alterações feitas a partir dos repositórios originais estão na Branch Main!**

---
A aplicação deployada na Vercel se encontra no domínio:
[https://devopscasefront-two.vercel.app](https://devopscasefront-two.vercel.app)

## Como executar a aplicação em container localmente.
Aqui temos o arquivo **docker-compose** que pode ser usado para executar a aplicação de forma ***[conteinreizada](https://aws.amazon.com/pt/what-is/containerization/)*** localmente. Para isso siga os passos abaixo:

**PRÉ-REQUISITOS**: <br>
É necessário ter o ***[git](https://git-scm.com/book/pt-br/v2/Começando-Instalando-o-Git)***, o ***[docker](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-20-04-pt)*** e o ***[docker compose](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-compose-on-ubuntu-20-04-pt)*** instalados na máquina.

1. Para rodar a aplicação faça a clonagem deste repositório com o comando:
```
git clone https://github.com/YohansN/gocase-devops.git
```

2. Entre no diretório raiz do projeto e execute o comando:
```
docker-compose up -d
```

3. **Pronto!** Agora você já pode entrar na página da aplicação acessando: **[localhost:80](localhost:80)** no seu navegador.
   
4. Para parar a aplicação utilize ``` docker-compose down ``` na raiz do projeto.

---
### ----- ATENÇÃO -----
As aplicações utilizam as seguintes portas: <br>
| Serviço  | Porta |
| ------------- | ------------- |
| Front-end | `80`  |
| Back-end  | `3000`  |
| Banco de dados  | `5432`  |

Caso alguma já esteja em uso na sua máquina isso pode **acarretar em problemas na hora da execução dos containeres**. Certifique-se de que não tenha outra aplicação rodando nessas portas ou modifique o arquivo docker-compose para que ele utilize portas distintas.
Os logs do container podem ser verificados com ```docker-compose logs``` para identificar possíveis erros.

## Integração entre os Serviços
Os três serviços são orquestrados por meio do Docker Compose que faz as conexões entre eles. Ele faz isso criando um conteiner de cada serviço. No caso do front-end e do back-end da aplicação ele cria um conteiner a partir da imagem do arquivo dockerfile de cada aplicação. Para o banco de dados eu escolhi o PostgreSQL que é um sistema de gerenciamento de banco de dados open source e amplamente utilizado. Seu container é gerado a partir de sua imagem oficial no [DockerHub](https://hub.docker.com/_/postgres). O Front-End se conecta com o Back-End por meio de configurações internas do projeto onde o front faz requisições para o endereço localhost:3000 que é o domínio padrão da API do back-end.<br>
Dentro do arquivo compose também são definidos as variáveis de ambiente que são essenciais para fazer a conexão do back-end ruby no banco de dados postgres e algumas configurações que variam a depender do serviço. Também é lá que é definido as portas lógicas onde os serviços irão ser executados e que estarão disponíveis para o acesso na nossa máquina. Por último foram definidos volumes, responsaveis por fazer a persistência dos dados dos serviços.

## Desenvolvimento da Pipeline CI/CD
Por meio do GitHub Actions foi desenvolvido uma Pipeline para deploy do **Front-End** da aplicação na plataforma **[Vercel](https://vercel.com/home)** de forma totalmente gratuita e automátizada. Na pipeline são definidas regras de execução que dizem em que momento o código da pipeline deverá ser executado. Em projetos maiores é comum existirem diferentes pipelines para diferentes ambientes como testes, homologação e produção. Nesse caso apenas uma pipeline de produção foi criada e ela é executada quando um push é feito na branch principal main como requisitado. Foram configuradas chaves/tokens secretos que fazem a conexão entre o repositório do GitHub e o projeto na Vercel. <br>
A Pipeline é executada em uma máquina virtual Ubuntu. Lá ela faz o clone do repositório e executa os comandos para instalação do CLI da Vercel, build da aplicação e envio dos arquivos Artifacts para o deploy na plataforma. <br>
Algumas etapas de verificação de código podem ser adicionadas para um maior controle do que deve ser deployado ou não.

## Escolhas técnicas
No caso da aplicação Back-End em Ruby foi necessário gerar uma nova chave master.key durante a criação do container no docker-compose. **Isso não é uma boa prática visto que a chave secreta fica exposta**. No entanto para fins de testes acredito ser uma solução valida. No caso de uma aplicação real que seria deploiada/hospedada eu colocaria o valor dessa chave em uma variavel de ambiente do servidor e passaria durante a criação do container de forma dinâmica.

<br>

---
Esté projeto foi desenvolvido por Yohans Nascimento para o desafio técnico da vaga **DevOps Estagiário** na ***GoCase***. 🐋📱🌐