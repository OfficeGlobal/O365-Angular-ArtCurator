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
# Angular 用 Art Curator 

このサンプルでは、Outlook メール API を使用して Office 365 からメールと添付ファイルを取得する方法を例示します。この API は、[iOS](https://github.com/OfficeDev/O365-iOS-ArtCurator)、[Android](https://github.com/OfficeDev/O365-Android-ArtCurator)、Web (Angular Web アプリ)、および [Windows Phone](https://github.com/OfficeDev/O365-WinPhone-ArtCurator) 用にビルドされています。[Medium の記事](https://medium.com/office-app-development)および [YouTube 上のチュートリアル ビデオ](https://www.youtube.com/watch?v=M88A6VB9IIw&feature=youtu.be)をご覧ください。

Art Curator を使用すると、通常とは異なる方法で受信トレイを表示できます。芸術的な T シャツを販売する会社を経営していると想像してみてください。会社のオーナーであるあなたのもとには、買ってほしいと思うデザインを示したたくさんのメールがアーティストから届きます。Outlook を使用して個々のメールを開き、添付の画像をダウンロードしてから開いて表示する代わりに、Art Curator を使用すると、受信トレイの添付ファイル優先ビュー (.jpg と .png ファイルに限定) が最初に表示され、より効率的に気に入ったデザインを選べるようになります。

[![Art Curator Screenshot\](./README Assets/AC_Angular.png)](https://youtu.be/4LOvkweDfhY " こちらをクリックしてサンプルの動作をご覧ください。")

このサンプルでは、**Outlook Mail API** からの次の操作を例示します。
* [フォルダーの取得](https://msdn.microsoft.com/office/office365/APi/mail-rest-operations#GetFolders)
* [メッセージの取得](https://msdn.microsoft.com/office/office365/APi/mail-rest-operations#Getmessages) (フイルター処理と選択機能の使用も含む)
* [添付ファイルの取得](https://msdn.microsoft.com/office/office365/APi/mail-rest-operations#GetAttachments)
* [メッセージの更新](https://msdn.microsoft.com/office/office365/APi/mail-rest-operations#Updatemessages)
* [メッセージの作成と送信](https://msdn.microsoft.com/office/office365/APi/mail-rest-operations#Sendmessages) (添付ファイルあり/なし) 

また、このサンプルでは [JavaScript 用 Active Directory 認証ライブラリ (ADAL)](https://github.com/AzureAD/azure-activedirectory-library-for-js) を使用した Azure Active Directory での認証も例示します。

<a name="prerequisites"></a>
## 前提条件

このサンプルを実行するには次のものが必要です。
* [Node.js](https://nodejs.org/)。開発サーバーでサンプルを実行して依存関係をインストールするには、Node が必要です。
* Office 365 アカウント。Office 365 アプリのビルドを開始するために必要なリソースが含まれている [Office 365 Developer サブスクリプション](http://aka.ms/ro9c62)にサインアップすることができます。

<a name="configure"></a>
## アプリを登録する

1. このサンプルをすばやく実行するには、[Outlook 開発者ポータルのアプリ登録ツール](https://dev.outlook.com/appregistration)に移動します。
2. **手順 1** で、既存の Office 365 アカウントでサインインするか、ボタンをクリックして無料試用版を入手します。サインインしたら、次の手順に進みます。
3. **手順 2** で、次の値をフォームに入力します。
	* *アプリ名:*Art Curator
	* *アプリの種類:*シングル ページ アプリ (SPA)
	* *リダイレクト URI:* http://127.0.0.1:8080/
	* *ホーム ページ URL:* http://artcurator.{your_subdomain}.com (お客様の Office 365 テナントの .onmicrosoft のサブドメイン)
4. **手順 3** で、**メール API** の下で次のアクセス許可を選択します。
	* *メールの読み取りと書き込み*
	* *メールの送信*
5. **手順 4** で、[**アプリの登録**] をクリックして、アプリケーションを Azure Active Directory に登録します。
6. 最後に、次のセクションで使用するために、フォームから**クライアント ID** をコピーします。

<a name="run"></a>
## アプリを実行する

*app/scripts/app.js* を開き、行 46 および行 47 にある *{your_tenant}* をそれぞれ、Office 365 テナント用に指定した .onmicrosoft のサブドメインと、直前の手順で Outlook 開発者ポータル アプリ登録ツールから受け取った登録済みの Azure アプリケーションのクライアント ID に置き換えます。 

次に、必要な依存関係をインストールし、コマンドラインからプロジェクトを実行します。まず、コマンド プロンプトを開き、ルート フォルダーに移動します。移動したら、以下の手順を行います。

1. ```npm install``` を実行して、プロジェクトの依存関係をインストールします。
2. これですべてのプロジェクトの依存関係がインストールされたので、ルート フォルダーで ```node server.js``` を実行して開発サーバーを起動します。
3. Web ブラウザーで ```http://127.0.0.1:8080/``` に移動します。

<a name="understand"></a>
## コードを理解する

### Office 365 への接続

このプロジェクトでは、Office 365 API を使用するためのトークンを要求して受け取るために、[JavaScript 用 Azure Active Directory Library (ADAL) を使用した Azure Active Directory](https://github.com/AzureAD/azure-activedirectory-library-for-js) を使用して Azure Active Directory で認証します。

サービスは、*app/app.js* のモジュールの config 関数内で構成されています。*app/controllers/navBarController.js* には、Azure Active Directory へのログインとログアウトを処理する 2 つの関数があり、この関数はトークンの取得も処理します。 

### メール API

このプロジェクトでは、メール API を操作する標準的な REST 呼び出しを使用します。使用可能なエンドポイントの詳細については、「[Outlook Mail REST API リファレンス](https://msdn.microsoft.com/en-us/office/office365/api/mail-rest-operations)」を参照してください。

すべてのメール API 機能は、*app/controllers/mainController.js* に格納されています。まず、ユーザーのテナントで利用可能なすべてのフォルダーが取得され、*localStorage* に格納されている値を使用して対象のフォルダーが検索されます。その後、ファイルが添付されている未読の直近のメール 50 通が取得されます。次に、これらの添付ファイルのそれぞれのコンテンツを取得するために呼び出しが行われます。この時点で、すべてのメールと添付ファイルの内容の取得が完了し、これらのコンテンツは表示用のビューで使用できる配列に格納されます。

*mainController.js* に含まれるその他の機能には、メールに開封済みマークを付ける機能と返信を作成して送信する機能があります。 

### 制限事項

現在のバージョンには、次の機能は含まれていません。

* .png と .jpg 以外のファイルのサポート
* 1 通に複数の添付ファイルが含まれているメールの処理
* ページング (50 通を超えるメールの受け取り)
* フォルダー名の一意性の処理
* 送信フォルダーは最上位のフォルダーでなければならない

## セキュリティに関するお知らせ
[ADAL JS](https://github.com/AzureAD/azure-activedirectory-library-for-js) では、Azure AD から受信したトークンは検証されません。検証はアプリのバックエンドを使用して行われるため、ユーザーが取得したトークンが許容可能なものであったかどうかは、バックエンドを呼び出すまで分かりません。セキュリティ上の理由から、ビジネス アプリケーションには、ユーザー認証用のサーバー側コンポーネントが Web アプリケーションに組み込まれている必要があります。このバックエンドでのトークン検証がない場合、アプリは[混乱した使節の問題](https://en.wikipedia.org/wiki/Confused_deputy_problem)などのセキュリティ攻撃を受けやすくなります。詳細については、こちらの[ブログ投稿](http://www.cloudidentity.com/blog/2015/02/19/introducing-adal-js-v1/)をご覧ください。

<a name="questions-and-comments"></a>
## 質問とコメント

- このサンプルの実行で問題が発生した場合は、[問題をログに記録](https://github.com/OfficeDev/O365-Angular-ArtCurator/issues)してください。
- Office 365 API に関する全般的な質問は、[Stack Overflow](http://stackoverflow.com/) に投稿してください。質問やコメントには、必ず [Office365] とタグを付けてください。
  
<a name="additional-resources"></a>
## その他のリソース

* [Office 365 API で Angular アプリを作成する](http://aka.ms/get-started-with-js)
* [Office 365 API プラットフォームの概要](http://msdn.microsoft.com/office/office365/howto/platform-development-overview)
* [Office デベロッパー センター](http://dev.office.com/)
* [IOS 用 Art Curator](https://github.com/OfficeDev/O365-iOS-ArtCurator)
* [Android 用 Art Curator](https://github.com/OfficeDev/O365-Android-ArtCurator)
* [Windows Phone 用 Art Curator](https://github.com/OfficeDev/O365-WinPhone-ArtCurator)

## 著作権
Copyright (c) 2015 Microsoft.All rights reserved.


このプロジェクトでは、[Microsoft オープン ソース倫理規定](https://opensource.microsoft.com/codeofconduct/)が採用されています。詳細については、「[倫理規定の FAQ](https://opensource.microsoft.com/codeofconduct/faq/)」を参照してください。また、その他の質問やコメントがあれば、[opencode@microsoft.com](mailto:opencode@microsoft.com) までお問い合わせください。
