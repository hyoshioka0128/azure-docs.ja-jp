---
title: クイック スタート:複数コンテナー アプリを作成する
description: 初めての複数コンテナー アプリをデプロイして、Azure App Service での複数コンテナー アプリの使用を開始します。
keywords: azure app service, Web アプリ, linux, docker, compose, マルチコンテナー, マルチ コンテナー, コンテナー用の Web アプリ, 複数のコンテナー, コンテナー, wordpress, azure db for mysql, コンテナーを使用した運用データベース
author: msangapu-msft
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: ab8e416e460546dcf758c83aa8b357139c6dbefb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "82085877"
---
# <a name="create-a-multi-container-preview-app-using-a-docker-compose-configuration"></a>Docker Compose 構成を使用してマルチコンテナー (プレビュー) アプリを作成する

> [!NOTE]
> 複数コンテナーは現在プレビュー段階です。

[Web App for Containers](app-service-linux-intro.md) には、Docker イメージを柔軟に使用できる機能があります。 このクイックスタートでは、[Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) で Docker Compose 構成を使用して Web App for Containers にマルチコンテナー アプリ (プレビュー) をデプロイする方法を示します。

このクイック スタートは Cloud Shell で行いますが、これらのコマンドは [Azure CLI](/cli/azure/install-azure-cli) (2.0.32 以降) を使用してローカルで実行することもできます。 

![Web App for Containers のサンプル マルチコンテナー アプリ][1]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>サンプルのダウンロード

このクイック スタートでは、[Docker](https://docs.docker.com/compose/wordpress/#define-the-project) にある Compose ファイルを使用します。 構成ファイルは [Azure サンプル](https://github.com/Azure-Samples/multicontainerwordpress)にあります。

[!code-yml[Main](../../../azure-app-service-multi-container/docker-compose-wordpress.yml)]

Cloud Shell で、クイックスタートのディレクトリを作成し、それに変更します。

```bash
mkdir quickstart

cd $HOME/quickstart
```

次に、以下のコマンドを実行して、サンプル アプリのリポジトリをクイックスタートのディレクトリに複製します。 その後、`multicontainerwordpress` ディレクトリに移動します。

```bash
git clone https://github.com/Azure-Samples/multicontainerwordpress

cd multicontainerwordpress
```

## <a name="create-a-resource-group"></a>リソース グループを作成する

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

Cloud Shell で [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) コマンドを使用して、リソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを場所*米国中南部*に作成します。 **Standard** レベルの Linux 上の App Service がサポートされているすべての場所を表示するには、[`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az-appservice-list-locations) コマンドを実行します。

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

通常は、現在地付近の地域にリソース グループおよびリソースを作成します。

コマンドが完了すると、リソース グループのプロパティが JSON 出力に表示されます。

## <a name="create-an-azure-app-service-plan"></a>Azure App Service プランの作成

Cloud Shell で [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) コマンドを使用して、リソース グループに App Service プランを作成します。

次の例では、**Standard** 価格レベル (`--sku S1`) を使用して、Linux コンテナー (`--is-linux`) に `myAppServicePlan` という名前の App Service プランを作成します。

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

App Service プランが作成されると、Azure CLI によって、次の例のような情報が表示されます。

<pre>
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "South Central US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "South Central US",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
</pre>

## <a name="create-a-docker-compose-app"></a>Docker Compose アプリを作成する

> [!NOTE]
> 現在、Azure App Service の Docker Compose には 4,000 文字の制限があります。

Cloud Shell ターミナルで [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) コマンドを使って、`myAppServicePlan` App Service プランにマルチコンテナーの [Web アプリ](app-service-linux-intro.md)を作成します。 _\<app_name>_ は忘れずに固有のアプリ名に置き換えてください (有効な文字は `a-z`、`0-9`、`-` です)。

```azurecli
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --multicontainer-config-type compose --multicontainer-config-file compose-wordpress.yml
```

Web アプリが作成されると、Azure CLI によって次の例のような出力が表示されます。

<pre>
{
  "additionalProperties": {},
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app_name&gt;.azurewebsites.net",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

### <a name="browse-to-the-app"></a>アプリの参照

デプロイされたアプリ (`http://<app_name>.azurewebsites.net`) を参照します。 アプリの読み込みには数分かかることがあります。 エラーが発生した場合は、数分待ってからブラウザーを更新してください。

![Web App for Containers のサンプル マルチコンテナー アプリ][1]

**おめでとうございます**。Web App for Containers にマルチコンテナー アプリを作成しました。

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル:マルチコンテナーの WordPress アプリ](tutorial-multi-container-app.md)

> [!div class="nextstepaction"]
> [カスタム コンテナーの構成](configure-custom-container.md)

<!--Image references-->
[1]: ./media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png
