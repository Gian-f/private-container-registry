# Docker Registry Privado com Interface Gráfica e Nginx

Este projeto configura um registro Docker privado local usando o Docker Registry oficial, uma interface gráfica para gerenciamento via navegador, e um proxy reverso Nginx para controle de tráfego. Ele permite armazenar, visualizar e deletar imagens Docker localmente.

## Estrutura dos Serviços

### 1. **Registry Server**
O serviço `registry-server` utiliza a imagem oficial `registry:2.8.3`, que é o registro Docker em si. Ele armazena todas as imagens e está configurado para persistir os dados localmente em `./registry/data`.

- **Imagem**: `registry:2.8.3`
- **Porta**: `5000`
- **Volume**: `./registry/data:/var/lib/registry`

### 2. **UI (Interface Gráfica)**
O serviço `registry-ui` fornece uma interface gráfica para navegar, visualizar e gerenciar as imagens do registro Docker. Ele é configurado para conectar-se ao servidor de registro e oferece funcionalidades como exclusão de imagens, exibição de digest e paginação de tags.

- **Imagem**: `joxit/docker-registry-ui:latest`
- **Porta**: `120`
- **Variáveis de ambiente**:
  - `SINGLE_REGISTRY=true`: Configura a UI para um único registro.
  - `REGISTRY_TITLE=Docker Registry UI`: Define o título da interface.
  - `NGINX_PROXY_PASS_URL=http://registry-server:5000`: URL para o servidor do registro.
  - `DELETE_IMAGES=true`: Habilita a exclusão de imagens.
  - `SHOW_CONTENT_DIGEST=true`: Mostra o digest de conteúdo das imagens.
  - `TAGLIST_PAGE_SIZE=100`: Define a paginação para até 100 tags por página.

### 3. **Nginx (Proxy Reverso)**
O serviço `nginx` atua como proxy reverso para o servidor de registro e a interface gráfica, tornando o acesso ao sistema mais seguro e simplificado. Ele escuta na porta `80`.

- **Imagem**: `nginx:latest`
- **Porta**: `80`
- **Volume**: `./nginx.conf:/etc/nginx/nginx.conf:ro`

## Requisitos

- Docker instalado
- Docker Compose instalado

## Como Usar

### Passo 1: Clonar o Repositório

Clone este repositório para sua máquina local:

```bash
git clone https://seu-repositorio-url
cd seu-repositorio
```

### Passo 2: Estrutura de Diretórios

Certifique-se de que os diretórios e arquivos necessários existam:

- `./registry/data`: Armazenará as imagens do Docker.
- `./nginx.conf`: Arquivo de configuração do Nginx.

### Passo 3: Iniciar os Contêineres

Execute o comando abaixo para iniciar todos os serviços:

```bash
docker-compose up -d
```

### Passo 4: Acessar os Serviços

- **Interface gráfica (UI)**: Acesse a UI do registro no navegador em `http://localhost:120`.
- **Registro Docker**: O servidor de registro estará disponível em `http://localhost:5000`.

### Passo 5: Interagir com o Registro

Você pode realizar push e pull de imagens Docker utilizando o seguinte endereço:

```bash
docker tag <sua-imagem> localhost:5000/<nome-da-imagem>
docker push localhost:5000/<nome-da-imagem>
docker pull localhost:5000/<nome-da-imagem>
```

## Personalização

- **Configurações do Nginx**: O arquivo `nginx.conf` pode ser personalizado conforme suas necessidades.
- **Volume de Armazenamento**: O volume `./registry/data` pode ser ajustado para outro diretório se necessário.

## Referências

- [Documentação Oficial do Docker Registry](https://docs.docker.com/registry/)
- [Docker Registry UI](https://github.com/Joxit/docker-registry-ui)
- [NGINX Docker Image](https://hub.docker.com/_/nginx)
