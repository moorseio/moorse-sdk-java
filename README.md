# Moorse Java SDK

O moorse sdk surge como solução ao problema que muitos desenvolvedores enfrentam ao necessitar criar suas próprias requisições http aos serviços web da Moorse, com ele, torna-se possível enviar requests à nossa API de forma simplificada, sem a necessidade de configurar todos os requests do início ao fim.

Todos os métodos e rotas aqui citados podem ser encontrados com maiores detalhes na [documentação da Moorse](https://moorse.readme.io/reference/princ%C3%ADpios-b%C3%A1sicos), bem como maiores detalhes sobre nossos produtos.

## Sumário

1. [Início rápido](#1-início-rápido)
2. [Objeto Moorse](#2-objeto-moorse)
3. [Autorização](#3-autorização)
    1. [login](#31-login)
    2. [setToken](#32-settoken)
    3. [getToken](#33-gettoken)
    4. [setEmail](#34-setemail)
    5. [getEmail](#35-getemail)
    6. [setPassword](#36-setpassword)
    7. [getPassword](#37-getpassword)
4. [Integrações](#4-integrações)
    1. [setId](#41-setid)
    2. [getId](#42-getid)
    3. [sendText](#43-sendtext)
    4. [sendFile](#44-sendfile)
    5. [sendTemplate](#45-sendtemplate)
    6. [sendMenu](#46-sendmenu)
    7. [sendButtons](#47-sendbuttons)
    8. [getById](#48-getbyid)
    9. [getAll](#49-getall)
    10. [delete](#410-delete)
    11. [getStatusById](#411-getstatusbyid)
5. [Webhooks](#5-webhooks)
    1. [create](#51-create)
    2. [update](#52-update)
    3. [delete](#53-delete)
    4. [getOne](#54-getone)
    5. [getAll](#55-getall)
6. [Templates](#6-templates)
    1. [create](#61-create)
    2. [delete](#62-delete)
    3. [getOne](#63-getone)
    4. [getAll](#64-getall)
7. [Relatórios](#7-relatórios)
    1. [getMessages](#71-getmessages)
    2. [getMessagesByChannel](#72-getmessagesbychannel)
    3. [getMessagesByTimeline](#73-getmessagesbytimeline)
8. [Faturamento](8-faturamento)
    1. [getCredits](81-getcredits)

## 1. Início Rápido

Antes de tudo, inicie um projeto maven em Java e instale o sdk da Moorse adicionando o seguinte trecho de código no seu arquivo *pom.xml*:

```xml
<dependency>
    <groupId>io.moorse</groupId>
    <artifactId>sdk</artifactId>
    <!--Escolha uma versão-->
    <version>x.x.x</version>
</dependency>
```

Para ver todas as versões disponíveis do sdk visite a [página do maven central repository do sdk](https://central.sonatype.com/artifact/io.moorse/sdk/).

Após instalada a dependência, crie um objeto Moorse e faça login para estar apto a enviar mensagens utilizando suas integrações.

```java
import java.io.IOException;

import io.moorse.Moorse;
import io.moorse.dto.CommunicationChannel;

public class Main {
    public static void main(String[] args) throws IOException, InterruptedException {
        
        Moorse moorse = new Moorse(
            "seu-email",
            "sua-senha",
            CommunicationChannel.WHATSAPP
        );
        
        moorse.auth.login();
        
        moorse.integration.sendText(
            "integração-que-enviará-a-mensagem",
            "5583995765327", // número do destinatário
            "Olá, bem-vindo à Moorse!"
        );
        
    }
}
```

## 2. Objeto Moorse

A classe **Moorse** é o ponto chave de todo o SDK, criando uma instância dela é possível gerenciar integrações, criar templates, configurar webhooks, enviar mensagens e muito mais!

```java
import io.moorse.Moorse;
import io.moorse.dto.CommunicationChannel;

public class Main {
	public static void main(String[] args) {
        Moorse moorse = new Moorse(
        	"seu-email",
        	"sua-senha",
        	CommunicationChannel.WHATSAPP
        );
    }
}
```

Acima é criada uma instância **Moorse** que configura e-mail e senha de uma conta bem como o canal de comunicação desejado para enviar mensagens, essas informações serão úteis para que posteriormente seja possível gerar um token de usuário e consigamos realizar chamadas à API, entretanto, também é possível instanciar uma classe Moorse apenas com a informação do canal de comunicação desejado.

```java
Moorse moorse = new Moorse(CommunicationChannel.INSTAGRAM);
```

Desse modo, o e-mail e a senha da conta necessários para autorização não terão valores definidos e será necessário defini-los antes de tentar obter o token de acesso.

## 3. Autorização

Para que seja possível acessar todos os recursos da sua conta é necessário obter seu token de acesso. Desse modo, o objeto Moorse provê um atributo público e constante nomeado *auth*, com ele é possível chamar a rota de login da Moorse que possibilita receber o token em troca do e-mail e senha da conta.

### 3.1. Login

Este método é implementado de duas formas, em uma é necessário informar o e-mail e a senha da conta na qual deseja-se logar, no outro não é necessário, o SDK se encarregará de utilizar as informações de e-mail e senha já salvos.

#### Definição: (primeira forma)

```java
public LoginDto login(String email, String password)
```

#### Retorno: (primeira forma)

```java
public class LoginDto {
	private String data;
	private String[] errors;
	private String[] links;
}
```

#### Exemplo: (primeira forma)

```java
LoginDto data = moorse.auth.login();
```

Em ambas as implementações o token é salvo, então não é necessário se preocupar em adquirir o token da resposta e usá-lo nas requisições posteriores, o SDK se encarrega disso.

#### Definição: (segunda forma)

```java
public LoginDto login()
```

#### Retorno: (segunda forma)

```java
public class LoginDto {
	private String data;
	private String[] errors;
	private String[] links;
}
```

Exemplo: (segunda forma)

```java
LoginDto data = moorse.auth.login("email", "senha");
```

### 3.2 setToken

Método setter padrão, serve para definir o token que se deseja usar diretamente e não precisar fazer chamadas à API da Moorse.

#### Definição:

```java
public void setToken(String token)
```

#### Exemplo:

```java
moorse.auth.setToken("seu-token-secreto");
```

### 3.3 getToken

Método getter padrão, serve para resgatar o token salvo no SDK.

#### Definição:

```java
public String getToken()
```

#### Exemplo:

```java
String meuToken = moorse.auth.getToken();
System.out.println(meuToken);
```

### 3.4 setEmail

Método setter padrão, serve para definir o email que se deseja configurar no SDK para realizar o login.

#### Definição:

```java
public void setEmail(String email)
```

#### Exemplo:

```java
moorse.auth.setEmail("seu-email-moorse");
```

### 3.5 getEmail

Método getter padrão, serve para resgatar o email salvo no SDK.

#### Definição:

```java
public String getEmail()
```

#### Exemplo:

```java
String meuEmail = moorse.auth.getEmail();
System.out.println(meuEmail);
```

### 3.6. setPassword

Método setter padrão, serve para definir a senha que se deseja configurar no SDK para realizar o login.

#### Definição:

```java
public void setPassword(String pass)
```

#### Exemplo:

```java
moorse.auth.setPassword("sua-senha-moorse");
```

### 3.7. getPassword

Método getter padrão, serve para resgatar a senha salva no SDK.

#### Definição:

```java
public String getPassword()
```

#### Exemplo:

```java
String minhaSenha = moorse.auth.getPassword();
System.out.println(minhaSenha);
```

## 4. Integrações

Após ter passado da etapa de autorização usando o método login para obtenção do token você está apto a acessar os recursos da sua conta. O que primeiro veremos são as rotas que envolvem integrações Moorse. Aqui você poderá enviar mensagens de vários tipos e gerenciar suas integrações.

### 4.1. setId

Método setter padrão, serve para definir o id de qual integração deseja-se usar no SDK.

#### Definição:

```java
public void setId(String id)
```

#### Exemplo:

```java
moorse.integration.setId("id-da-minha-integração");
```

### 4.2. getId

Método getter padrão, serve para obter o id da integração configurada no SDK, caso exista alguma.

#### Definição:

```java
public String getId()
```

#### Exemplo:

```java
moorse.integration.setId("123");
moorse.integration.getId(); // retorna "123"
```

### 4.3. sendText

Método que serve para enviar uma mensagem de texto utilizando o canal de comunicação(WHATSAPP, INSTAGRAM OU SMS) selecionado na instanciação do objeto Moorse. Existem duas implementações possíveis. A primeira funciona apenas caso o id da integração tenha sido previamente configurado.

#### Definição:

```java
// Implementação 1
public SendMessageDto sendText(
    String to, 
    String body
)
// Implementação 2
public SendMessageDto sendText(
    String integrationId, 
    String to, 
    String body
)
```

#### Exemplo:

```java
// exemplo 1
moorse.integration.setId("id-da-minha-integração");
moorse.integration.sendText(
    "5583996834356",
    "Olá, bem-vindo à Moorse!"
);

// exemplo 2
moorse.integration.sendText(
    "id-da-minha-integração",
    "5583996834356",
    "Olá, bem-vindo à Moorse!"
);
```

#### Retorno:

```java
public class SendMessageDto{
    
    private DataDto data;
    private String[] errors;
    private String[] links;
    
    public class DataDto{
    	private String control;
    	private String creationDate;
    	private String message;
    }

}
```

### 4.4. sendFile

Método que serve para enviar um arquivo utilizando o canal de comunicação WHATSAPP. Existem duas implementações possíveis. A primeira funciona apenas caso o id da integração tenha sido previamente configurado.

*Esse método não tem implementação para INSTAGRAM nem SMS.*

#### Definição:

```java
// Implementação 1
public SendMessageDto sendFile(
    String to, 
    String body, 
    String filename, 
    String caption
)
// Implementação 2
public SendMessageDto sendFile(
    String integrationId, 
    String to, 
    String body, 
    String filename, 
    String caption
)
```

#### Exemplo:

```java
// exemplo 1
moorse.integration.setId("id-da-minha-integração");
moorse.integration.sendFile(
    "5583996834356",
    "arquivo-codificado-em-base64",
    "nome-do-arquivo",
    "legenda da mensagem"
);

// exemplo 2
moorse.integration.sendFile(
    "id-da-minha-integração",
    "5583996834356",
    "arquivo-codificado-em-base64",
    "nome-do-arquivo",
    "legenda da mensagem"
);
```

#### Retorno:

```java
public class SendMessageDto{
    
    private DataDto data;
    private String[] errors;
    private String[] links;
    
    public class DataDto{
    	private String control;
    	private String creationDate;
    	private String message;
    }

}
```

### 4.5. sendTemplate

Método que serve para enviar um template utilizando o canal de comunicação WHATSAPP. Existem duas implementações possíveis. A primeira funciona apenas caso o id da integração tenha sido previamente configurado.

*Esse método não tem implementação para INSTAGRAM nem SMS.*

#### Definição:

```java
// Implementação 1
public SendMessageDto sendTemplate(TemplateMessageRequest message)
// Implementação 2
public SendMessageDto sendTemplate(
    String integrationId, 
    TemplateMessageRequest message
)
```

Para este exemplo é importante que seu objeto Moorse já esteja devidamente configurado com token e id da integração. Atente-se à montagem do objeto que especifica como o template deve ser enviado.

#### Exemplo:

```java
TemplateMessageRequest message = new TemplateMessageRequest(
    "5583996769802",
    "nome-do-template",
    Arrays.asList(
        new Component(
            "header",
            Arrays.asList(
                new ImageParameter("moorse.io/assets/img/moorse/sobre-moorse.png"),
                new VideoParameter("https://youtu.be/dQw4w9WgXcQ")
            )
        ),
        new Component(
            "body", 
            Arrays.asList(
                new ImageParameter("moorse.io/assets/img/moorse.jpg"),
                new TextParameter("Olá {1}, bem-vindo à Moorse!")
            )
        )
    )
);

//enviando o template
moorse.integration.sendTemplate(message);
```

#### Retorno:

```java
public class SendMessageDto{
    
    private DataDto data;
    private String[] errors;
    private String[] links;
    
    public class DataDto{
    	private String control;
    	private String creationDate;
    	private String message;
    }

}
```

### 4.6. sendMenu

Método que serve para enviar um menu utilizando o canal de comunicação WHATSAPP. Existem duas implementações possíveis. A primeira funciona apenas caso o id da integração tenha sido previamente configurado.

*Esse método não tem implementação para INSTAGRAM nem SMS.*

#### Definição:

```java
// Implementação 1
public SendMessageDto sendMenu(MenuMessageRequest message)
// Implementação 2
public SendMessageDto sendMenu(
    String integrationId, 
    MenuMessageRequest message
)
```

#### Exemplo:

```java
List <Section> secoes = new ArrayList<Section>();

secoes.add(new Section(
    "OFERTA",
    Arrays.asList(
        new Row("MEUS_CUPONS", "Meus cupons"),
        new Row("PRODUTOS_ALTA", "Produtos em alta"),
        new Row("PRODUTOS_MAIS_VENDIDOS", "Produtos mais vendidos"),
        new Row("OFERTAS_DIA", "Ofertas do dia")
    )
));

secoes.add(new Section(
    "OUTROS",
    Arrays.asList(
        new Row("SUA_OPINIAO", "Dê sua opinião"),
        new Row("FALAR_PESSOA", "Falar com uma pessoa")
    )
));

MenuMessageRequest message = new MenuMessageRequest(
    "5583993657482", 
    "Olá 🙂\nQue bom que você chegou!",
    secoes
);

moorse.integration.sendMenu(message);
```

#### Retorno:

```java
public class SendMessageDto{
    
    private DataDto data;
    private String[] errors;
    private String[] links;
    
    public class DataDto{
    	private String control;
    	private String creationDate;
    	private String message;
    }

}
```

### 4.7. sendButtons

Método que serve para enviar botões utilizando o canal de comunicação WHATSAPP. Existem duas implementações possíveis. A primeira funciona apenas caso o id da integração tenha sido previamente configurado.

*Esse método não tem implementação para INSTAGRAM nem SMS.*

### Definição:

```java
// Implementação 1
public SendMessageDto sendButtons(ButtonsMessageRequest message)
// Implementação 2
public SendMessageDto sendButtons(
    String integrationId, 
    ButtonsMessageRequest message
)
```

#### Exemplo:

```java
ButtonsMessageRequest message = new ButtonsMessageRequest(
    "5583996873456",
    "Avaliação de atendimento",
    Arrays.asList(
        new Button("AGRADAVEL", "Agradável 🙂"),
        new Button("MEDIANO", "Mediano 😐"),
        new Button("RUIM", "Ruim 🙁")
    )
);

moorse.integration.sendButtons(message);
```

#### Retorno:

```java
public class SendMessageDto{
    
    private DataDto data;
    private String[] errors;
    private String[] links;
    
    public class DataDto{
    	private String control;
    	private String creationDate;
    	private String message;
    }

}
```

### 4.8. getById

Método responsável por resgatar as informações de uma integração Moorse dado seu id. Assim como nos métodos acima, existe uma implementação sem o id da integração sendo passado no parâmetro e outra com o id.

#### Definição:

```java
// implementação 1
public IntegrationDto getById(String integrationId)
// implementação 2
public IntegrationDto getById()
```

#### Exemplo:

```java
IntegrationDto data = null;
// exemplo 1
data = moorse.integration.getById("id-da-integração");
// exemplo 2
data = moorse.integration.getById();
```

#### Retorno:

```java
public class IntegrationDto{

    private Data data;
    private List<MoorseError> errors;

    public class Data{
        private String id;
        private String name;
        private String description;
        private String accountName;
        private String accountDescription;
        private String type;
        private String externalId;
        private Boolean moorseManagement;
        private String billingType;
        private String device;
        private String model;
        private Integer battery;
        private String state;
        private String avatar;
        private String phoneDescription;
        private Boolean trial;
        private Boolean official;
        private String externalClientId;
        private String externalChannelId;
        private List<WebhookResponse> webhooks;
    }

    public class MoorseError{
        private String message;
        private String code;
    }

}
```

### 4.9. getAll

Pega a informação de todas as integrações pertencentes à conta, para isso é apenas necessário a configuração do token da conta.

#### Definição:

```java
public IntegrationListDto getAll()
```

#### Exemplo:

```java
Moorse moorse = new Moorse(
    "email", "senha", 
    CommunicationChannel.WHATSAPP
);
moorse.auth.login();
IntegrationListDto data = moorse.integration.getAll();
```

#### Retorno:

```java
public class IntegrationListDto{

    private Data data;
    private List<MoorseError> errors;

    public class Data{
        
        private List<Integration> content;
        
        public class Integration{
            private String id;
            private String name;
            private String description;
            private String accountName;
            private String accountDescription;
            private String type;
            private String externalId;
            private Boolean trial;
            private Boolean official;
        }
        
    }

    public class MoorseError{
        private String message;
        private String code;
    }

}
```

### 4.10. delete

Método responsável por deletar um integração dado seu id. Assim como nos métodos acima, existe uma implementação sem o id da integração sendo passado no parâmetro e outra com o id.

#### Definição:

```java
// implementação 1
public DeleteIntegrationDto delete(String integrationId)
// implementação 2
public DeleteIntegrationDto delete()
```

#### Exemplo:

```java
DeleteIntegrationDto deleteData = null;
// exemplo 1
deleteData = moorse.integration.delete("id-da-integração");
// exemplo 2
deleteData = moorse.integration.delete();
```

#### Retorno:

```java
public class DeleteIntegrationDto {

    private Data data;
    private List<MoorseError> errors;

    public class Data{
        private String message;
    }

    public class MoorseError{
        private String message;
        private String code;
    }

}
```

### 4.11. getStatusById

Método responsável por resgatar o status de uma integração dado seu id. Assim como nos métodos acima, existe uma implementação sem o id da integração sendo passado no parâmetro e outra com o id.

#### Definição:

```java
// implementação 1
public IntegrationStatusDto getStatusById(String integrationId)
// implementação 2
public IntegrationStatusDto getStatusById()
```

#### Exemplo:

```java
IntegrationStatusDto statusData = null;
// exemplo 1
statusData = moorse.integration.getStatusById("id-da-integração");
// exemplo 2
statusData = moorse.integration.getStatusById();
```

#### Retorno:

```java
public class IntegrationStatusDto {

    private Data data;
    private List<MoorseError> errors;

    public class Data{
        private String status;
    }

    public class MoorseError{
        private String message;
        private String code;
    }
    
}
```

## 5. Webhooks

Assumindo que o leitor já tenha realizado o login e obtido o seu token de acesso, torna-se possível manipular os webhooks da sua conta para receber requests em uma URL específica quando houverem eventos com as suas integrações. A seguir estão listados os métodos capazes de interagir com os webhooks.



### 5.1. create

Método capaz de criar um webhook em uma conta Moorse, dadas as informações necessárias à sua criação. O login já deve ter sido realizado antes da criação.

#### Definição:

```java
public WebhookDto create(WebhookRequest webhook)
```

#### Exemplo:

```java
Moorse moorse = new Moorse(
    "sample@email.com", 
    "senha-secreta", 
    CommunicationChannel.WHATSAPP
);

moorse.auth.login();

WebhookRequest webhook = new WebhookRequest(
    "nome-do-webhook",
    "url-desejada",
    WebhookMethod.POST,
    true, // <-- se o webhook está ativo ou não
    Arrays.asList(
        "id-da-integração-1", /* integrações */
        "id-da-integração-2", /* associadas  */
        "id-da-integração-3"  /* ao webhook  */
    ),
    Arrays.asList(
        new Pair<String,String>("sender", "moorse.io"),       /*                        */
        new Pair<String,String>("url", "https://moorse.io/"), /* headers personalizados */
        new Pair<String,String>("integrations", "3")          /*                        */
    ),
    true, // <-- se o webhook deve notificar mensagens respondidas
    true, // <-- se o webhook deve notificar mensagens recebidas
    true, // <-- se o webhook deve notificar mensagens enviadas
    5,    // <-- número de retentativas
    1     // <-- tempo para o timeout do webhook
);

moorse.webhook.create(webhook);
```

#### Retorno:

```java
public class WebhookDto{

    private WebhookResponse data;
    private List<MoorseError> errors;

    public class WebhookResponse{

        private String id;
        private String clientId;
        private String creationDate;
        private String lastUpdateDate;
        private String name;
        private String url;
        private WebhookMethod method;
        private Boolean active;
        private List<String> integrations;
        private List<Pair<String,String>> headers;
        private Boolean answered;
        private Boolean received;
        private Boolean sent;
        private Integer retries;
        private Integer timeout;

        public enum WebhookMethod{ POST, GET }

        public class Pair<T1,T2>{
            private T1 key;
            private T2 value;
        }

    }

    public class MoorseError{
        private String message;
        private String code;
    }

}
```

### 5.2. update

Método capaz de atualizar um webhook dado seu id, de modo semelhante ao método de cima aqui também é necessário que o login já tenha sido feito e o token de acesso esteja configurado. 

#### Definição:

```java
public WebhookDto update(String webhookId, WebhookRequest webhook)
```

#### Exemplo:

```java
Moorse moorse = new Moorse(
    "sample@email.com", 
    "senha-secreta", 
    CommunicationChannel.WHATSAPP
);

moorse.auth.login();

WebhookRequest webhook = new WebhookRequest(
    "nome-do-webhook-atualizado",
    "nova-url",
    WebhookMethod.GET,
    false, // <-- se o webhook está ativo ou não
    Arrays.asList(
        "id-da-integração-1", /* integrações */
        "id-da-integração-2", /* associadas  */
        "id-da-integração-3"  /* ao webhook  */
    ),
    Arrays.asList(
        new Pair<String,String>("sender", "moorse.io"),       /*                        */
        new Pair<String,String>("url", "https://moorse.io/"), /* headers personalizados */
        new Pair<String,String>("integrations", "3")          /*                        */
    ),
    false, // <-- se o webhook deve notificar mensagens respondidas
    true, // <-- se o webhook deve notificar mensagens recebidas
    true, // <-- se o webhook deve notificar mensagens enviadas
    2,    // <-- número de retentativas
    5     // <-- tempo para o timeout do webhook
);

moorse.webhook.update(
    "id-do-webhook"
    webhook
);
```

#### Retorno:

```java
public class WebhookDto{

    private WebhookResponse data;
    private List<MoorseError> errors;

    public class WebhookResponse{

        private String id;
        private String clientId;
        private String creationDate;
        private String lastUpdateDate;
        private String name;
        private String url;
        private WebhookMethod method;
        private Boolean active;
        private List<String> integrations;
        private List<Pair<String,String>> headers;
        private Boolean answered;
        private Boolean received;
        private Boolean sent;
        private Integer retries;
        private Integer timeout;

        public enum WebhookMethod{ POST, GET }

        public class Pair<T1,T2>{
            private T1 key;
            private T2 value;
        }

    }

    public class MoorseError{
        private String message;
        private String code;
    }

}
```

### 5.3. delete

Método capaz de deletar um webhook dado seu id, assim como nos outros métodos de webhooks, é necessário autorizar-se com o método login antes de tentar acessar essas rotas.

#### Definição:

```java
public WebhookDto delete(String webhookId)
```

#### Exemplo:

```java
Moorse moorse = new Moorse(
    "sample@email.com", 
    "senha-secreta", 
    CommunicationChannel.WHATSAPP
);

moorse.auth.login();
moorse.webhook.delete("id-do-webhook");
```

#### Retorno:

```java
public class WebhookDto{

    private WebhookResponse data;
    private List<MoorseError> errors;

    public class WebhookResponse{

        private String id;
        private String clientId;
        private String creationDate;
        private String lastUpdateDate;
        private String name;
        private String url;
        private WebhookMethod method;
        private Boolean active;
        private List<String> integrations;
        private List<Pair<String,String>> headers;
        private Boolean answered;
        private Boolean received;
        private Boolean sent;
        private Integer retries;
        private Integer timeout;

        public enum WebhookMethod{ POST, GET }

        public class Pair<T1,T2>{
            private T1 key;
            private T2 value;
        }

    }

    public class MoorseError{
        private String message;
        private String code;
    }

}
```

### 5.4. getOne

Método capaz de obter a informação de um webhook dado seu id, aqui também é preciso utilizar o método login de modo a autorizar-se antes de tentar obter informação de um webhook.

#### Definição:

```java
public WebhookDto getOne(String webhookId)
```

#### Exemplo:

```java
Moorse moorse = new Moorse(
    "sample@email.com", 
    "senha-secreta", 
    CommunicationChannel.WHATSAPP
);

moorse.auth.login();

// informação salva em data
WebhookDto data = moorse.webhook.getOne("id-do-webhook");
```

#### Retorno:

```java
public class WebhookDto{

    private WebhookResponse data;
    private List<MoorseError> errors;

    public class WebhookResponse{

        private String id;
        private String clientId;
        private String creationDate;
        private String lastUpdateDate;
        private String name;
        private String url;
        private WebhookMethod method;
        private Boolean active;
        private List<String> integrations;
        private List<Pair<String,String>> headers;
        private Boolean answered;
        private Boolean received;
        private Boolean sent;
        private Integer retries;
        private Integer timeout;

        public enum WebhookMethod{ POST, GET }

        public class Pair<T1,T2>{
            private T1 key;
            private T2 value;
        }

    }

    public class MoorseError{
        private String message;
        private String code;
    }

}
```

### 5.5. getAll

Método capaz de obter a informação de todos os webhooks de uma conta, este método não recebe informação como parâmetro, já que apenas necessita do token de acesso do dono da conta com os webhooks, e este é configurado com a utilização do método login.

#### Definição:

```java
public WebhookList getAll()
```

#### Exemplo:

```java
Moorse moorse = new Moorse(
    "sample@email.com", 
    "senha-secreta", 
    CommunicationChannel.WHATSAPP
);

moorse.auth.login();

// informação salva em data
WebhookDto data = moorse.webhook.getAll();
```

#### Retorno:

```java
public class WebhookDto{

    private Content data;
    private List<MoorseError> errors;

    public class Content{

        private List<WebhookResponse> content;

        public class WebhookResponse{

            private String id;
            private String clientId;
            private String creationDate;
            private String lastUpdateDate;
            private String name;
            private String url;
            private WebhookMethod method;
            private Boolean active;
            private List<String> integrations;
            private List<Pair<String,String>> headers;
            private Boolean answered;
            private Boolean received;
            private Boolean sent;
            private Integer retries;
            private Integer timeout;

            public enum WebhookMethod{ POST, GET }

            public class Pair<T1,T2>{
                private T1 key;
                private T2 value;
            }

        }
    
    }

    public class MoorseError{
        private String message;
        private String code;
    }

}
```

## 6. Templates

Nesta seção são apresentados os métodos que permitem a um usuário Moorse manipular seus templates e realizar ações como criação, remoção e obtenção das informações de um template.

### 6.1. create

Método responsável por criar um template na sua conta.

#### Definição:

```java
public TemplateDto create(TemplateRequest template)
```

#### Exemplo:

```java
Moorse moorse = new Moorse(
    "sample@email.com", 
    "senha-secreta", 
    CommunicationChannel.WHATSAPP
);

moorse.auth.login();

TemplateRequest template = new TemplateRequest(
    "nome-do-template",
    "descrição-do-template",
    TemplateType.STANDARD, // <-- tipo do template
    "id-da-integração-dona-do-template",
    TemplateCategory.AUTO_REPLY, // <-- categoria na qual o template se encaixa
    TemplateLanguage.PORTUGUESE_BR, // <-- linguagem do template
    Arrays.asList( // componentes do template
        new TemplateImageComponent(
            TemplateComponentType.HEADER, 
            null // <-- possível exemplo de componente
        ),
        new TemplateTextComponent(
            TemplateComponentType.BODY, 
            "olá {1}, percebemos que você mora em {2}, bem vindo!", 
            new TemplateComponentExample(
                null, // <-- exemplos para header
                Arrays.asList(
                    Arrays.asList("Pedro", "São Paulo"),     /* exemplos de uso */
                    Arrays.asList("João", "Belo Horizonte"), /* dos parâmetros  */
                    Arrays.asList("Gabriel", "Goiás")        /* do template     */
                ),
                null  // <-- exemplos para header
            )
        ),
        new TemplateButtonComponent(
            Arrays.asList(
                new ButtonQuickReply("resposta-rápida", null), /* Valores nulo são   */
                new ButtonUrl("youtu.be/dQw4w9WgXcQ", null),   /* possíveis exemplos */
                new ButtonPhoneNumber("5583996546372", null)   /* de envio de botão  */
            ),
            null // <-- possível exemplo de componente
        )
    )
);

moorse.template.create(template);
```

#### Retorno:

```java
public class TemplateDto{

    private TemplateResponse data;
    private List<MoorseError> errors;

    public class TemplateResponse{
        
        private String id;
        private String creationDate;
        private String lastUpdateDate;
        private String clientId;
        private String status;
        private String rejectedReason;
        private String templateFb;
        private String name;
        private String description;
        private TemplateType type;
        private String integrationId;
        private TemplateCategory category;
        private TemplateLanguage language;
        private List<TemplateComponent> components;

        public class TemplateComponent{

            private TemplateComponentType type;
            private String text;
            private TemplateComponentFormat format;
            private TemplateComponentExample example;
            private List<Button> buttons;
            
            public enum TemplateComponentType{ BODY, HEADER, FOOTER, BUTTONS }
            public enum TemplateComponentFormat{ TEXT, IMAGE, DOCUMENT, VIDEO }
            
            public class Button{
                private ButtonType type;
                private String text;
                private String url;
                private String phone_number;
                private List<String> example;
                private enum ButtonType{ QUICK_REPLY, URL, PHONE_NUMBER }
            }

        }

    }

    public class MoorseError{
        private String message;
        private String code;
    }

}
```

### 6.2. delete

Método responsável por deletar um template da sua conta dado o nome do template.

#### Definição:

```java
public TemplateDto delete(String templateName)
```

#### Exemplo:

```java
Moorse moorse = new Moorse(
    "sample@email.com", 
    "senha-secreta", 
    CommunicationChannel.WHATSAPP
);
moorse.auth.login();
moorse.template.delete("nome-do-template");
```

#### Retorno:

```java
public class TemplateDto{

    private TemplateResponse data;
    private List<MoorseError> errors;

    public class TemplateResponse{
        
        private String id;
        private String creationDate;
        private String lastUpdateDate;
        private String clientId;
        private String status;
        private String rejectedReason;
        private String templateFb;
        private String name;
        private String description;
        private TemplateType type;
        private String integrationId;
        private TemplateCategory category;
        private TemplateLanguage language;
        private List<TemplateComponent> components;

        public class TemplateComponent{

            private TemplateComponentType type;
            private String text;
            private TemplateComponentFormat format;
            private TemplateComponentExample example;
            private List<Button> buttons;
            
            public enum TemplateComponentType{ BODY, HEADER, FOOTER, BUTTONS }
            public enum TemplateComponentFormat{ TEXT, IMAGE, DOCUMENT, VIDEO }
            
            public class Button{
                private ButtonType type;
                private String text;
                private String url;
                private String phone_number;
                private List<String> example;
                private enum ButtonType{ QUICK_REPLY, URL, PHONE_NUMBER }
            }

        }

    }

    public class MoorseError{
        private String message;
        private String code;
    }

}
```

### 6.3. getOne

Método responsável por obter informação de um template específico de uma conta Moorse dado o nome do template buscado.

#### Definição:

```java
public TemplateDto getOne(String templateName)
```

#### Exemplo:

```java
Moorse moorse = new Moorse(
    "sample@email.com", 
    "senha-secreta", 
    CommunicationChannel.WHATSAPP
);
moorse.auth.login();
TemplateDto template = moorse.template.getOne("nome-do-template");
```

#### Retorno:

```java
public class TemplateDto{

    private TemplateResponse data;
    private List<MoorseError> errors;

    public class TemplateResponse{
        
        private String id;
        private String creationDate;
        private String lastUpdateDate;
        private String clientId;
        private String status;
        private String rejectedReason;
        private String templateFb;
        private String name;
        private String description;
        private TemplateType type;
        private String integrationId;
        private TemplateCategory category;
        private TemplateLanguage language;
        private List<TemplateComponent> components;

        public class TemplateComponent{

            private TemplateComponentType type;
            private String text;
            private TemplateComponentFormat format;
            private TemplateComponentExample example;
            private List<Button> buttons;
            
            public enum TemplateComponentType{ BODY, HEADER, FOOTER, BUTTONS }
            public enum TemplateComponentFormat{ TEXT, IMAGE, DOCUMENT, VIDEO }
            
            public class Button{
                private ButtonType type;
                private String text;
                private String url;
                private String phone_number;
                private List<String> example;
                public enum ButtonType{ QUICK_REPLY, URL, PHONE_NUMBER }
            }

        }

    }

    public class MoorseError{
        private String message;
        private String code;
    }

}
```

### 6.4. getAll

Método responsável por obter informação de todos os templates de uma conta Moorse.

#### Definição:

```java
public TemplateList getAll()
```

#### Exemplo:

```java
Moorse moorse = new Moorse(
    "sample@email.com", 
    "senha-secreta", 
    CommunicationChannel.WHATSAPP
);
moorse.auth.login();
TemplateList templates = moorse.template.getAll();
```

#### Retorno:

```java
public class TemplateList{

    private Content data;
    private MoorseError errors;

    public class Content{
        
        private List<TemplateResponse> content;
    
        public class TemplateResponse{
        
            private String id;
            private String creationDate;
            private String lastUpdateDate;
            private String clientId;
            private String status;
            private String rejectedReason;
            private String templateFb;
            private String name;
            private String description;
            private TemplateType type;
            private String integrationId;
            private TemplateCategory category;
            private TemplateLanguage language;
            private List<TemplateComponent> components;

            public class TemplateComponent{

                private TemplateComponentType type;
                private String text;
                private TemplateComponentFormat format;
                private TemplateComponentExample example;
                private List<Button> buttons;
                
                public enum TemplateComponentType{ BODY, HEADER, FOOTER, BUTTONS }
                public enum TemplateComponentFormat{ TEXT, IMAGE, DOCUMENT, VIDEO }
                
                public class Button{
                    private ButtonType type;
                    private String text;
                    private String url;
                    private String phone_number;
                    private List<String> example;
                    public enum ButtonType{ QUICK_REPLY, URL, PHONE_NUMBER }
                }

            }

        }

    }

    public class MoorseError{
        private String message;
        private String code;
    }

}
```

## 7. Relatórios

Com os métodos aqui citados é possível receber informações sobre quantidade, canais e dias que as mensagens da conta foram enviadas. Para que seja possível acessar estes métodos é necessário apenas estar logado na sua conta Moorse e ter obtido o token de acesso.

### 7.1. getMessages

Método que serve para obter informação da quantidade de mensagens enviadas e recebidas e do número de contatos com os quais se manteve contato com alguma integração em um dado período de tempo.

#### Definição:

```java
public MessagesReportDto getMessages(LocalDate begin, LocalDate end)
```

#### Exemplo:

```java
Moorse moorse = new Moorse(
    "email@sample.com",
    "sua-senha-forte",
    CommunicationChannel.WHATSAPP
);

moorse.auth.login();

// pegando mensagens do último mês
MessagesReportDto messagesData = moorse.report.getMessages(
    LocalDate.now().minusDays(30),
    LocalDate.now(),
);
```

#### Retorno:

```java
public class MessagesReportDto {

    private Data data;
    private List<MoorseError> errors;

    public class Data{
        private Integer totalMessageSent;
        private Integer totalMessagesReceived;
        private Integer totalContacts;
    }

    public class MoorseError{
        private String message;
        private String code;
    }

}
```

### 7.2. getMessagesByChannel

Método que serve para obter informação da quantidade de mensagens por canal (WHATSAPP, INSTAGRAM ou SMS) com alguma integração em um dado período de tempo.

#### Definição:

```java
public MessagesByChannelReportDto getMessagesByChannel(LocalDate begin, LocalDate end)
```

#### Exemplo:

```java
Moorse moorse = new Moorse(
    "email@sample.com",
    "sua-senha-forte",
    CommunicationChannel.WHATSAPP
);
    
moorse.auth.login();

// pegando mensagens (por canal) do último mês
MessagesByChannelReportDto data = moorse.report.getMessagesByChannel(
    LocalDate.now().minusDays(30),
    LocalDate.now(),
);
```

#### Retorno:

```java
public class MessagesByChannelReportDto {

    private Data data;
    private List<MoorseError> errors;

    public class Data{
        private Integer whatsapp;
        private Integer messenger;
        private Integer instagram;
        private Integer telegram;
        private Integer sms;
        private Integer email;
    }

    public class MoorseError{
        private String message;
        private String code;
    }

}
```

### 7.3. getMessagesByTimeline

Método que serve para obter informação da quantidade de mensagens mês a mês com alguma integração em um dado período de tempo.

#### Definição:

```java
public MessagesByTimelineReportDto getMessagesByTimeline(LocalDate begin, LocalDate end)
```

#### Exemplo:

```java
Moorse moorse = new Moorse(
    "email@sample.com",
    "sua-senha-forte",
    CommunicationChannel.WHATSAPP
);
    
moorse.auth.login();

// pegando mensagens mês a mês dos últimos 3 meses
MessagesByTimelineReportDto data = moorse.report.getMessagesByTimeline(
    LocalDate.now().minusDays(90),
    LocalDate.now(),
);
```

## 8. Faturamento

Serviço que conta apenas com um método que serve para pegar todos os créditos de uma integração. É necessário que o usuário esteja logado para acessá-lo. O método possui duas implementações, em uma é necessário informar o id da integração no parâmetro e na outra não. Pode-se usar a segunda implementação unicamente caso o id da integração já tenha sido previamente configurado.

### 8.1. getCredits

Método que serve para resgatar o número de créditos de uma dada integração, possui implementação com e sem id da integração no parâmetro.

#### Definição:

```java
public BillingDto getCredits(String integrationId)
public BillingDto getCredits()
```

#### Exemplo:

```java
Moorse moorse = new Moorse(
    "email@sample.com",
    "sua-senha-forte",
    CommunicationChannel.WHATSAPP
);

moorse.auth.login();

// implementação 1
BillingDto data = moorse.billing.getCredits("id-da-sua-integração");

// implementação 2
moorse.integration.setId("id-da-sua-integração");
BillingDto data = moorse.billing.getCredits();
```

#### Retorno:

```java
public class BillingDto {

    private Data data;
    private List<MoorseError> errors;

    public class Data{
        private String integrationId;
        private Integer balance;
        private Integer credit;
        private String channel;
        private String billingType;
    }

    public class MoorseError{
        private String message;
        private String code;
    }

}
```
