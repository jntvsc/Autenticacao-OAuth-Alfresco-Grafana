# Autenticacao-OAuth-Alfresco-Grafana
Para integrar a autenticação do **Alfresco** com o **Grafana** usando o protocolo **OAuth**, é necessário configurar o Alfresco como um **provedor de OAuth** e o Grafana como um **cliente OAuth**, de modo que os usuários possam autenticar-se no Grafana utilizando suas credenciais do Alfresco.

Segue o processo para configurar essa integração:

### 1. Configurando o Alfresco como Provedor OAuth

O **Alfresco** pode ser configurado para atuar como um servidor **OAuth2**, permitindo que outras aplicações, como o Grafana, autentiquem usuários.

#### Passo 1: Ativando OAuth no Alfresco

Para ativar o OAuth no Alfresco, edite o arquivo `alfresco-global.properties` e adicione as seguintes configurações:

```properties
# Habilitar OAuth no Alfresco
oauth.enabled=true
oauth.client.id=grafana
oauth.client.secret=your-client-secret
oauth.redirect.uri=http://localhost:3000/login/generic_oauth
```

Aqui:
- `oauth.client.id`: É o ID do cliente OAuth que o Grafana usará para se autenticar.
- `oauth.client.secret`: É a chave secreta compartilhada entre o Alfresco e o Grafana.
- `oauth.redirect.uri`: É a URI para onde o usuário será redirecionado após a autenticação no Alfresco, neste caso, o Grafana.

#### Passo 2: Configuração de escopos e permissões

O Alfresco precisará definir os escopos (scopes) e permissões que serão fornecidos aos aplicativos clientes (neste caso, o Grafana). Essa configuração pode variar de acordo com os requisitos de segurança da sua organização.

Por exemplo:

```properties
oauth.scope=openid,email,profile
oauth.token.endpoint.url=https://alfresco.example.com/oauth/token
oauth.authorization.endpoint.url=https://alfresco.example.com/oauth/authorize
oauth.userinfo.endpoint.url=https://alfresco.example.com/oauth/userinfo
```

### 2. Configurando o Grafana como Cliente OAuth

Agora, você precisa configurar o **Grafana** para autenticar os usuários utilizando o OAuth configurado no Alfresco.

#### Passo 1: Habilitar OAuth no Grafana

Abra o arquivo de configuração do Grafana (`grafana.ini`) e configure o Grafana para usar OAuth:

```ini
[auth.generic_oauth]
enabled = true
name = Alfresco
allow_sign_up = true
client_id = grafana
client_secret = your-client-secret
scopes = openid email profile
auth_url = https://alfresco.example.com/oauth/authorize
token_url = https://alfresco.example.com/oauth/token
api_url = https://alfresco.example.com/oauth/userinfo
allowed_domains = example.com
```

Explicação dos parâmetros principais:
- **client_id**: Deve ser o mesmo valor definido no Alfresco (`grafana`).
- **client_secret**: A mesma chave secreta compartilhada configurada no Alfresco.
- **auth_url**: O endpoint de autorização do Alfresco.
- **token_url**: O endpoint para troca de código de autorização por token de acesso no Alfresco.
- **api_url**: O endpoint para obter informações do usuário autenticado.
- **allowed_domains**: Domínios permitidos para login (opcional).

#### Passo 2: Configuração das permissões de usuário no Grafana

No Grafana, você pode configurar como os usuários autenticados via OAuth terão suas permissões atribuídas. No mesmo arquivo `grafana.ini`, você pode configurar as funções padrão para os usuários:

```ini
[users]
default_role = Viewer
```

Se quiser diferenciar permissões, pode fazer isso com base em grupos ou perfis do OAuth, se configurado no Alfresco.

### 3. Testando a Integração

Após configurar o OAuth no Alfresco e no Grafana, reinicie ambos os serviços:

```bash
# No Alfresco
sudo service alfresco restart

# No Grafana
sudo service grafana-server restart
```

Agora, ao tentar acessar o **Grafana**, você verá uma opção para login via **OAuth** (neste caso, o provedor Alfresco). O usuário será redirecionado para o Alfresco para inserir suas credenciais, e após a autenticação, será redirecionado de volta ao Grafana com uma sessão autenticada.

### Desfecho

Essa abordagem permite que o **Grafana** utilize o **Alfresco** como um provedor de autenticação OAuth, simplificando o gerenciamento de usuários ao utilizar um único sistema de login.
