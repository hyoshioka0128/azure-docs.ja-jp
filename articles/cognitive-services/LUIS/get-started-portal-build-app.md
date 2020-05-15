---
title: クイック スタート:LUIS ポータルで新しいアプリを作成する
description: このクイックスタートでは、アプリの基本パーツ、意図、エンティティを作成すると共に、LUIS ポータルからサンプル発話を使ってテストを行います。
ms.topic: quickstart
ms.date: 04/14/2020
ms.openlocfilehash: 2d601646c43c0f0d99dc6934cf1f1c960e0b0f79
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382583"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>クイック スタート:LUIS ポータルで新しいアプリを作成する

このクイック スタートでは、LUIS ポータルで新しいアプリを構築します。 まず、アプリ、**意図**、および**エンティティ**の基本パーツを作成します。 次いで、インタラクティブ テスト パネルにユーザーの発話例を入力し、予測される意図を取得して、アプリをテストします。

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-an-app"></a>アプリを作成する

1. コンテキスト ツールバーの **[+ New app for conversation]** \(+ 会話用の新しいアプリ\) を選択し、 **[New app for conversation]** \(会話用の新しいアプリ\) を選択します。

    > [!div class="mx-imgBorder"]
    > [![LUIS ポータルで新しいアプリを作成する](./media/create-app-in-portal.png)](./media/create-app-in-portal.png#lightbox)

1. ポップアップ ウィンドウで、以下の設定を使用してアプリを構成した後、 **[Done]\(完了\)** を選択します。

   |設定名| 値 | 目的|
   |--|--|--|
   |Name|`myEnglishApp`|LUIS アプリの一意の名前<br>required|
   |Culture|**English**|ユーザーの発話の言語、**en-us**<br>required|
   |Description (省略可能)|`App made with LUIS Portal`|アプリの説明<br>省略可能|
   |Prediction resource (省略可能) |-  |選択しないでください。 作成と 1,000 件の予測エンドポイント要求に無料で使用できるスターター キーが LUIS から提供されます。 |

   ![新しいアプリ設定を入力する](./media/get-started-portal-build-app/create-new-app-settings.png)

## <a name="create-intents"></a>意図の作成

LUIS アプリを作成したら、意図を作成する必要があります。 意図は、ユーザーから発せられたテキストを分類する手段です。 たとえば、人事管理アプリに 2 つの機能を含めるとします。 ユーザーが次のことをできるようにします。

 1. 仕事を探し、応募する
 1. 仕事に応募するためのフォームを探す

アプリの 2 つの _目的_ は次の意図に対応しています。

|Intent|ユーザーから発せられたテキストの例<br>"_発話_" と呼ばれる|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

意図を作成するには、次の手順を実行します。

1. アプリの作成が完了すると、 **[Build]\(ビルド\)** セクションに **[Intents]\(意図\)** ページが表示されます。 **[Create]\(作成\)** を選択します

   [![[作成] を選択して新しい意図を作成する](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. 意図の名前として `FindForm` を入力し、 **[Done]\(完了\)** を選択します。

## <a name="add-an-example-utterance"></a>発話の例を追加する

意図を作成したら、発話の例を追加します。 発話の例は、ユーザーがチャット ボットや他のクライアント アプリケーションに入力するテキストです。 ユーザーのテキストの意図を、LUIS 意図にマッピングします。

このアプリケーション例の `FindForm` 意図では、発話の例にフォーム番号を含めます。 ユーザーの要求を実行するためにクライアント アプリケーションはフォーム番号が必要なので、発話にそれを含めることは重要です。

> [!div class="mx-imgBorder"]
> [![FindForm 意図に発話の例を入力する](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

`FindForm` 意図に次の 15 の発話の例を追加します。

|#|発話の例|
|--|--|
|1|hrf-123456 を探しています|
|2|人事フォーム hrf-234591 はどこにありますか?|
|3|hrf-345623 はどこにありますか|
|4|hrf-345794 を私に送付していただくことは可能ですか|
|5|内部のジョブに申し込むには hrf-234695 が必要ですか?|
|6|hrf-234091 を使用して私がジョブに申し込みをしていることを、マネージャーは知る必要はありますか|
|7|hrf-234918 はどこに送信しますか? メールを受信したという応答は届きますか?|
|8|hrf-234555|
|9|hrf-234987 はいつ更新されましたか?|
|10|エンジニア リング職に申し込む際は、フォーム hrf-876345 を使用しますか|
|11|私のオープン要求に対して、新しいバージョンの hrf-765234 が送信されましたか?|
|12|国際的なジョブには、hrf-234234 を使用しますか?|
|13|hrf-234598 のスペル ミス|
|14|hrf-234567 は、新しい要件に応じて編集されますか|
|15|hrf-123456、hrf-123123、hrf-234567|

これらの発話の例は、次の点を意図的に変化させています。

* 発話の長さ
* [句読点](luis-reference-application-settings.md#punctuation-normalization)
* 単語の選択
* 動詞の時制 (現在、過去、未来)
* 単語の順序


## <a name="create-a-regular-expression-entity"></a>正規表現エンティティを作成する

実行時の予測応答としてフォーム番号を返すには、フォームをエンティティとしてマークする必要があります。 フォーム番号のテキストは、高度に構造化されているため、正規表現エンティティを使用してマークできます。 エンティティを作成するには、以下の手順に従います。

1. 左側のメニューから **[Entities]\(エンティティ\)** を選択します。

1. **[Entities]\(エンティティ\)** ページの **[Create]\(作成\)** を選択します。

1. 「`Human Resources Form Number`」という名前を入力し、 **[Regex]\(正規表現\)** エンティティ型を選択して、 **[Next]\(次へ\)** を選択します。

   ![正規表現エンティティを作成する](./media/get-started-portal-build-app/create-regular-expression-entity.png)

1. 正規表現 (**RegEx**) 式として「`hrf-[0-9]{6}`」を入力します。 これは、リテラル文字 `hrf-` と同じで、6 桁に対応します。続けて **[Create]\(作成\)** を選択します。

   ![エンティティの正規表現を入力する](./media/get-started-portal-build-app/create-regular-expression-entity-with-expression.png)


## <a name="add-example-utterances-to-the-none-intent"></a>発話の例を None 意図に追加する

**None** 意図は、fallback 意図であるため、空のままにしないでください。 この意図には、アプリの他の意図に追加した 10 の発話の例それぞれに対して 1 つの発話を含める必要があります。

**None** 意図の発話の例は、クライアント アプリケーションのドメイン外にある必要があります。

1. 左側のメニューから **[Intents]\(意図\)** を選択した後、意図の一覧から **[None]** を選択します。

1. 以下の発話の例を意図に追加します。

   |None 意図の発話の例|
   |--|
   |吠える犬はうっとうしい|
   |ピザを注文して|
   |海の中のペンギン|

   このアプリの場合、これらの発話の例は、ドメイン外です。 ドメインに動物、食品、または海洋が含まれる場合、**None** 意図には別の発話の例を使用する必要があります。

## <a name="train-the-app"></a>アプリをトレーニングする

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>発話の例内の正規表現エンティティを見る

1. 左側のメニューから **[Intents]\(意図\)** を選択して、エンティティが **FindForm** 意図内にあることを確認します。 その後、**FindForm** 意図を選択します。

   エンティティは、発話の例の中の出現箇所でマークされます。 エンティティ名ではなく、元のテキストを表示する場合は、ツールバーから **[Entities View]\(エンティティ ビュー\)** を切り替えます。

   > [!div class="mx-imgBorder"]
   > [![エンティティによってマークが付けられたすべての発話の例](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>インタラクティブな [テスト] ウィンドウを使用して新しいアプリをテストする

LUIS ポータル内のインタラクティブな **[Test]\(テスト\)** ウィンドウを使用して、アプリでまだ確認されていない新しい発話からエンティティが抽出されることを確認します。

1. 右上のメニューから **[Test]\(テスト\)** を選択します。

1. 新しい発話を追加して、Enter キーを押します。

   ```Is there a form named hrf-234098```

    **[Inspect]\(検査\)** を選択してエンティティの予測を表示します。

   > [!div class="mx-imgBorder"]
   > ![テスト ウィンドウで新しい発話をテストする](./media/get-started-portal-build-app/test-new-utterance.png)

   予測意図の上位は正しく **FindForm** になっており、信頼度は 90% を超えています (0.977)。 抽出された **Human Resources Form Number** エンティティの値は hrf-234098 です。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイック スタートが完了し、次のクイック スタートに進まない場合は、上部のナビゲーション メニューから **[My apps]\(マイ アプリ\)** を選択します。 次に、一覧からアプリの左側にあるチェック ボックスをオンにし、一覧の上にあるコンテキスト ツール バーから **[Delete]\(削除\)** を選択します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [2.アプリのデプロイ](get-started-portal-deploy-app.md)
