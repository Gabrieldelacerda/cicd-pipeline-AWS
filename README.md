# CI/CD Pipeline — GitHub Actions + Docker + AWS EC2

(versão em português abaixo)

You can test it yourself: http://54.233.211.29:8080

Before getting into the details, here's a quick overview of how everything works.

A push to main triggers a pipeline on GitHub Actions. That pipeline builds a Docker image from the app, pushes it to Docker Hub, SSHs into the EC2 instance and restarts the container with the new version. From the moment you push to the moment the new version is live, nothing is done manually.

In practice, the layers are separated like this: GitHub Actions is responsible for running the pipeline, Docker packages the application and guarantees it runs the same way everywhere, Docker Hub stores the image between the build and the deploy steps, and EC2 is where the container actually runs. Nginx sits in front of the app as a reverse proxy, handling incoming requests on port 8080 and forwarding them to Flask.

If you wanted to reproduce this from scratch, the flow is straightforward. Clone the repo, set up the GitHub Secrets with your own credentials, and push something. The pipeline takes care of the rest.

```bash
docker build -t cicd-pipeline-aws .
docker run -p 5000:5000 cicd-pipeline-aws
```

Project 1 was focused on infrastructure — provisioning, configuration, containerization. This project takes that same environment and adds the next natural layer: automated deploys. The idea was to close the loop so that a code change goes from a push to production without any manual steps in between.

The application itself is simple on purpose. A Flask API with a single endpoint. The focus here was never the app — it was the pipeline around it. Getting GitHub Actions to build the image correctly, push it to Docker Hub, and then connect to a remote server and deploy it without any manual intervention.

One of the parts that required the most attention was the SSH authentication between the Actions runner and the EC2 instance. The private key needs to be stored as a GitHub Secret, passed into the runner at runtime and written to disk with the right permissions before the SSH connection is made. Getting the key format right and making sure nothing was lost in the process took a few iterations, but understanding exactly why each step was needed made it worth it.

Once everything was stable, the result was exactly what I was after. Push code, watch the pipeline run, see the new version live on the server. No SSH, no manual pulls, no restarts by hand.

There are things I would still improve. Adding health checks before swapping containers and eventually moving toward a proper blue-green deployment to avoid any downtime during updates. But this version already covers the core of what CI/CD is about in practice.

---

Você pode testar por conta própria: http://54.233.211.29:8080

Antes de entrar nos detalhes, aqui vai uma visão geral rápida de como tudo funciona.

Um push para a branch main dispara um pipeline no GitHub Actions. Esse pipeline faz o build de uma imagem Docker a partir da aplicação, envia para o Docker Hub, acessa a instância EC2 via SSH e reinicia o container com a nova versão. Do momento do push até a nova versão estar no ar, nada é feito manualmente.

Na prática, as camadas estão separadas assim: o GitHub Actions é responsável por executar o pipeline, o Docker empacota a aplicação e garante que ela rode da mesma forma em qualquer lugar, o Docker Hub armazena a imagem entre o build e o deploy, e a EC2 é onde o container de fato roda. O Nginx fica na frente da aplicação como reverse proxy, recebendo as requisições na porta 8080 e encaminhando para o Flask.

Se você quiser reproduzir isso do zero, o fluxo é direto. Clone o repositório, configure os GitHub Secrets com suas próprias credenciais e faça um push. O pipeline cuida do resto.

```bash
docker build -t cicd-pipeline-aws .
docker run -p 5000:5000 cicd-pipeline-aws
```

O Projeto 1 foi focado em infraestrutura — provisionamento, configuração, containerização. Este projeto pega esse mesmo ambiente e adiciona a próxima camada natural: deploys automatizados. A ideia foi fechar o ciclo para que uma mudança no código vá de um push até a produção sem nenhuma etapa manual no meio.

A aplicação em si é simples de propósito. Uma API em Flask com um único endpoint. O foco aqui nunca foi a aplicação — foi o pipeline ao redor dela. Fazer o GitHub Actions buildar a imagem corretamente, enviar para o Docker Hub e depois conectar em um servidor remoto e fazer o deploy sem nenhuma intervenção manual.

Uma das partes que exigiu mais atenção foi a autenticação SSH entre o runner do Actions e a instância EC2. A chave privada precisa ser armazenada como um GitHub Secret, injetada no runner em tempo de execução e gravada em disco com as permissões certas antes de a conexão SSH ser estabelecida. Acertar o formato da chave e garantir que nada fosse perdido no processo exigiu algumas iterações, mas entender exatamente por que cada etapa era necessária valeu o esforço.

Quando tudo ficou estável, o resultado foi exatamente o que eu queria. Push no código, pipeline rodando, nova versão no ar. Sem SSH, sem pull manual, sem restart na mão.

Ainda há coisas que eu melhoraria. Adicionar health checks antes de trocar os containers e eventualmente caminhar para um deploy blue-green para evitar downtime durante as atualizações. Mas essa versão já cobre o núcleo do que CI/CD significa na prática.
