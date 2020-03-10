---
page_type: sample
products:
- office-outlook
- office-365
languages:
- javascript
extensions:
  contentType: samples
  technologies:
  - Add-ins
  createdDate: 6/26/2015 3:02:27 PM
---
# Art Curator para Angular 

Esse exemplo demonstra como usar a API do Outlook Mail para obter e-mails e anexos do Office 365. Ele foi criado para [iOS](https://github.com/OfficeDev/O365-iOS-ArtCurator), [Android](https://github.com/OfficeDev/O365-Android-ArtCurator), Web (aplicativo Web Angular) e [Windows Phone](https://github.com/OfficeDev/O365-WinPhone-ArtCurator). Confira nosso [artigo em Medium](https://medium.com/office-app-development) e este [vídeo passo a passo no YouTube](https://www.youtube.com/watch?v=M88A6VB9IIw&feature=youtu.be).

O Art Curator oferece uma maneira diferente de exibir sua caixa de entrada. Imagine que você possui uma empresa que vende camisetas artísticas. Como proprietário da empresa, você recebe muitos emails de artistas com designs que eles querem que você compre. Em vez de usar o Outlook e abrir cada e-mail individualmente, baixando a imagem anexada e abrindo-a em seguida, você pode usar o Art Curator para ter uma visualização prévia do anexo (limitado aos arquivos: .jpg e .png) da sua caixa de entrada para escolher designs que você gosta de uma forma mais eficiente.

[![Captura de tela do Art Curator\](/READMEAssets/AC_Angular.png)](https://youtu.be/4LOvkweDfhY "Clique para visualizar o exemplo em ação.")

Este exemplo demonstra as seguintes operações da **API do Outlook Mail**:
* [Obter pastas](https://msdn.microsoft.com/office/office365/APi/mail-rest-operations#GetFolders)
* [Obter mensagens](https://msdn.microsoft.com/office/office365/APi/mail-rest-operations#Getmessages) (incluindo a filtragem e o uso da seleção)
* [Obter anexos](https://msdn.microsoft.com/office/office365/APi/mail-rest-operations#GetAttachments)
* [Atualizar mensagens](https://msdn.microsoft.com/office/office365/APi/mail-rest-operations#Updatemessages)
* [(com ou sem anexo)](https://msdn.microsoft.com/office/office365/APi/mail-rest-operations#Sendmessages) 

Esse exemplo também demonstra a autenticação com o Azure Active Directory usando [Biblioteca do Azure Active Directory (ADAL) para JavaScript](https://github.com/AzureAD/azure-activedirectory-library-for-js).

<a name="prerequisites"></a>
## Pré-requisitos

Esse exemplo requer o seguinte:
* [Node.js](https://nodejs.org/). O Nó é necessário para executar o exemplo em um servidor de desenvolvimento e para instalar as dependências.
* Uma conta do Office 365. Inscreva-se para [uma assinatura de Desenvolvedor do Office 365](http://aka.ms/ro9c62), que inclui os recursos necessários para começar a criar aplicativos do Office 365.

<a name="configure"></a>
## Registrar o aplicativo

1. Para colocar este exemplo em funcionamento rapidamente, acesse a [Ferramenta de Registro do Aplicativo do Portal de Desenvolvedor do Outlook](https://dev.outlook.com/appregistration).
2. Na **Etapa 1**, entre com sua conta do Office 365 ou clique no botão para obter uma avaliação gratuita. Depois de entrar, prossiga para a próxima etapa.
3. Na**Etapa 2**, preencha o formulário com os seguintes valores.
	* *Nome do aplicativo:* Art Curator
	* *Tipo do aplicativo:* Aplicativo de página única (SPA)
	* *URI de redirecionamento* http://127.0.0.1:8080/
	* *URL da Home Page:* http://artcurator.{your_subdomain}.com (o subdomínio do .onmicrosoft do seu locatário do Office 365)
4. Na **Etapa 3**, escolha as seguintes permissões em **API do Mail**.
	* *Ler e escrever e-mails*
	* *Enviar e-mail*
5. Na **Etapa 4**, clique **Registrar Aplicativo** para registrar seu aplicativo no Azure Active Directory.
6. Por fim, copie a **ID do cliente** do formulário para usar na próxima seção.

<a name="run"></a>
## Executar o aplicativo

Abra *app/scripts/app.js* e substitua *{your_tenant}* com o subdomínio do .onmicrosoft especificado ao seu locatário do Office 365 e a ID do cliente do aplicativo do Azure registrado que você recebeu da Ferramenta de registro do aplicativo do portal do desenvolvedor do Outlook na última etapa nas linhas 46 e 47 , respectivamente. 

Em seguida, instale as dependências necessárias e execute o projeto através da linha de comando. Comece abrindo um prompt de comando e navegando para a pasta raiz. Chegando lá, siga as etapas abaixo.

1. Instalar dependências do projeto executando ```instalar npm```.
2. Agora que todas as dependências do projeto estão instaladas, inicie o servidor de desenvolvimento executando ```server.js do nó``` na pasta fonte.
3. Acesse ```http://127.0.0.1:8080/``` no navegador da Web.

<a name="understand"></a>
## Compreender o código

### Conectar-se ao Office 365

Este projeto utiliza [Azure Active Directory usando a Biblioteca do Azure Active Directory (ADAL) para JavaScript](https://github.com/AzureAD/azure-activedirectory-library-for-js) na autenticação com o Azure Active Directory para solicitar e receber tokens para usar as APIs do Office 365.

O serviço está configurado no *app/app.js* na função de configuração do módulo. Em *app/controllers/navBarController.js*, há duas funções que manipulam o logon e o logoff do Azure Active Directory que também trata da adquisição dos tokens. 

### API do Mail

Esse projeto usa chamadas REST padrão para interagir com a API do Mail. Confira a [Referência da API do Outlook Mail REST](https://msdn.microsoft.com/en-us/office/office365/api/mail-rest-operations)para obter detalhes sobre os terminais disponíveis.

A funcionalidade completa da API do Mail reside em *app/controllers/mainController.js*. Primeiro, ela obtém todas as pastas disponíveis no locatário do usuário e utiliza o valor armazenado em *localStorage* para localizar a pasta de destino. Depois disso, ela obtém os 50 e-mails mais recentes que não foram lidos e possuem anexos. Em seguida, chamadas são feitas para acessar o conteúdo de cada um desses anexos. Neste ponto, ela possui todos os e-mails e conteúdo dos anexos, e tudo isso é armazenado em uma matriz que fica disponível no modo de visualização a ser apresentado.

Outras funcionalidades disponíveis em *mainController.js* incluem a marcação de e-mails como lidos e a criação e envio de respostas. 

### Limitações

Os recursos a seguir não são incluídos na versão atual.

* Suporte a arquivos além de .png e .jpg
* Lidar com uma única mensagem de email com vários anexos
* Paginação (recebendo mais de 50 emails)
* Lidar com exclusividade de nome de pasta
* A pasta de envio deve ser uma pasta de nível superior

## Observação de segurança
O [ADAL JS](https://github.com/AzureAD/azure-activedirectory-library-for-js) não valida o token recebido do Azure AD. Ele se baseia no terminal do aplicativo para fazê-lo, e até que o terminal seja chamado, você não saberá se o usuário obteve um token aceitável. Os aplicativos de negócios devem ter um componente no servidor para autenticar usuários criados no aplicativo Web por motivos de segurança. Sem essa validação do token do terminal, seu aplicativo está sujeito às violações de segurança, como [problema substituo confuso](https://en.wikipedia.org/wiki/Confused_deputy_problem). Confira essa [postagem de blog](http://www.cloudidentity.com/blog/2015/02/19/introducing-adal-js-v1/) para obter mais informações.

<a name="questions-and-comments"></a>
## Perguntas e comentários

- Se você tiver problemas para executar este exemplo, [relate um problema](https://github.com/OfficeDev/O365-Angular-ArtCurator/issues).
- Para perguntas gerais sobre as APIs do Office 365, poste no [Stack Overflow](http://stackoverflow.com/). Não deixe de marcar as perguntas ou comentários com [office365].
  
<a name="additional-resources"></a>
## Recursos adicionais

* [Criar um aplicativo Angular com APIs do Office 365](http://aka.ms/get-started-with-js)
* [Visão geral da plataforma de APIs do Office 365](http://msdn.microsoft.com/office/office365/howto/platform-development-overview)
* [Centro de Desenvolvimento do Office](http://dev.office.com/)
* [Art Curator para iOS](https://github.com/OfficeDev/O365-iOS-ArtCurator)
* [Art Curator para Android](https://github.com/OfficeDev/O365-Android-ArtCurator)
* [Art Curator para Windows Phone](https://github.com/OfficeDev/O365-WinPhone-ArtCurator)

## Direitos autorais
Copyright © 2015 Microsoft. Todos os direitos reservados.


Este projeto adotou o [Código de Conduta do Código Aberto da Microsoft](https://opensource.microsoft.com/codeofconduct/). Para saber mais, confira [Perguntas frequentes sobre o Código de Conduta](https://opensource.microsoft.com/codeofconduct/faq/) ou contate [opencode@microsoft.com](mailto:opencode@microsoft.com) se tiver outras dúvidas ou comentários.
