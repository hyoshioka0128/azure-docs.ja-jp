---
title: Hive テーブルを作成して Azure Blob Storage からデータを読み込む | Microsoft Docs
description: Hive テーブルを作成し、BLOB 内のデータを Hive テーブルに読み込む
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: cff9280d-18ce-4b66-a54f-19f358d1ad90
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: c1cbd523b8c74bf1221dc47b832c35e9119493a9
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2018
ms.locfileid: "51346288"
---
# <a name="create-hive-tables-and-load-data-from-azure-blob-storage"></a>Hive テーブルを作成して Azure Blob Storage からデータを読み込む

この記事では Hive テーブルを作成して Azure Blob Storage からデータを読み込む汎用の Hive クエリについて説明します。 Hive テーブルをパーティション分割する方法や、Optimized Row Columnar (ORC) 形式を使用してクエリのパフォーマンスを向上させる方法についてのガイダンスも提供されます。

## <a name="prerequisites"></a>前提条件
この記事では、以下のことを前提としています。

* Azure のストレージ アカウントが作成されている。 手順については、「[Azure ストレージ アカウントについて](../../storage/common/storage-create-storage-account.md)」をご覧ください。
* HDInsight サービスでカスタマイズされた Hadoop クラスターがプロビジョニングされている。  手順については、「 [Advanced Analytics Process and Technology 向けに HDInsight Hadoop クラスターをカスタマイズする](customize-hadoop-cluster.md)」をご覧ください。
* クラスターへのリモート アクセスを有効にし、ログインして Hadoop コマンド ライン コンソールを開いている。 手順については、「 [Hadoop クラスターのヘッド ノードへのアクセス](customize-hadoop-cluster.md)」をご覧ください。

## <a name="upload-data-to-azure-blob-storage"></a>Azure BLOB ストレージにデータをアップロードする
[高度な分析のための Azure 仮想マシンのセットアップに関するページ](../data-science-virtual-machine/setup-virtual-machine.md)の指示に従って Azure 仮想マシンを作成した場合、このスクリプト ファイルは仮想マシンの *C:\\Users\\\<ユーザー名\>\\Documents\\Data Science Scripts* ディレクトリにダウンロードされています。 これらの Hive クエリに必要なことは、独自のデータ スキーマと Azure BLOB ストレージの構成を適切なフィールドに接続し、送信できるようにすることだけです。

ここでは、Hive テーブルのデータが **圧縮されていない** 表形式であることと、Hadoop クラスターが使用するストレージ アカウントの既定の (または追加の) コンテナーにデータがアップロードされていることを想定しています。

**NYC タクシー乗車データ**の実習を行う場合は、次の準備が必要です。

* **NYC タクシー乗車データ** ファイル (12 個の Trip ファイルと 12 個の Fare ファイル) を [NYC タクシー乗車データ](http://www.andresmh.com/nyctaxitrips) します。
* **解凍** します。
* **Advanced Analytics Process and Technology 向けに Azure HDInsight Hadoop クラスターをカスタマイズする方法** に関する記事に記載されている手順で作成された Azure ストレージ アカウントの既定のコンテナー (または適切なコンテナー) にそれらのファイルを [アップロード](customize-hadoop-cluster.md) します。 ストレージ アカウントの既定のコンテナーに .csv ファイルをアップロードするプロセスについては、この [ページ](hive-walkthrough.md#upload)をご覧ください。

## <a name="submit"></a>Hive クエリを送信する方法
Hive クエリは、以下のものを使用して送信できます。

1. [Hadoop クラスターのヘッド ノードで Hadoop コマンド ラインを使用して Hive クエリを送信する](#headnode)
2. [Hive エディターで Hive クエリを送信する](#hive-editor)
3. [Azure PowerShell コマンドで Hive クエリを送信する](#ps)

Hive クエリは SQL に似ています。 SQL を使い慣れている場合は、 [Hive for SQL Users チート シート](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf) が役立つことがあります。

Hive クエリの送信時、Hive クエリの出力先を、画面上、ヘッド ノード上のローカル ファイル、または Azure BLOB のどれにするか制御できます。

### <a name="headnode"></a> 1.Hadoop クラスターのヘッドノードで Hadoop コマンド ラインを使用して Hive クエリを送信する
Hive クエリが複雑な場合、Hadoop クラスターのヘッド ノードに直接クエリを送信することにより、通常、Hive エディターまたは Azure PowerShell スクリプトを使用して送信するよりも速度が上がります。

Hadoop クラスターのヘッド ノードにログインし、ヘッド ノードのデスクトップで Hadoop コマンド ラインを開き、コマンド `cd %hive_home%\bin`を入力します。

Hadoop コマンド ラインで Hive クエリを送信する場合、次の 3 つの方法があります。

* 直接
* .hql ファイルの使用
* Hive コマンド コンソールで

#### <a name="submit-hive-queries-directly-in-hadoop-command-line"></a>Hadoop コマンド ラインでハイブ クエリを直接送信する
`hive -e "<your hive query>;` のようなコマンドを実行することで、Hadoop コマンド ラインで単純な Hive クエリを直接送信できます。 次に例を示します。ここで赤いボックスは Hive クエリを送信するコマンドを囲んでいます。緑色のボックスは Hive クエリの出力を囲んでいます。

![ワークスペースの作成](./media/move-hive-tables/run-hive-queries-1.png)

#### <a name="submit-hive-queries-in-hql-files"></a>.hql ファイルで Hive クエリを送信する
ハイブ クエリがより複雑で、複数の行が存在する場合、コマンド ラインやハイブ コマンド コンソールでクエリを編集することは実際的ではありません。 別の方法として、Hadoop クラスターのヘッド ノードでテキスト エディターを使用して、ヘッド ノードのローカル ディレクトリの中の .hql ファイルにハイブ クエリを保存します。 次のように `-f` 引数を使用すると、.hql ファイルの Hive クエリを送信できます。

    hive -f "<path to the .hql file>"

![ワークスペースの作成](./media/move-hive-tables/run-hive-queries-3.png)

**Hive クエリの進行状況ステータス画面の出力を抑制する**

既定では、Hadoop コマンド ラインで Hive クエリを送信した後に、マップ/縮小ジョブの進行状況が画面に出力されます。 マップ/縮小ジョブの進捗の画面出力を抑制するには、次のように、コマンド ラインで引数 `-S` ("S" は大文字) を使用します。

    hive -S -f "<path to the .hql file>"
    hive -S -e "<Hive queries>"

#### <a name="submit-hive-queries-in-hive-command-console"></a>Hive コマンド コンソールで Hive クエリを送信する。
Hadoop コマンド ラインで `hive` コマンドを実行すると、まず Hive コマンド コンソールに入力できるようになります。その後、Hive コマンド コンソールで Hive クエリを送信します。 たとえば次のようになります。 この例では、2 つの赤いボックスは、それぞれ Hive コマンド コンソールに入るために使用するコマンドと、Hive コマンド コンソールで送信された Hive クエリを強調表示しています。 緑色のボックスは、Hive クエリからの出力を強調表示しています。

![ワークスペースの作成](./media/move-hive-tables/run-hive-queries-2.png)

前の例では、画面上に直接 Hive クエリの結果を出力しています。 出力は、ヘッド ノードにあるローカル ファイルまたは Azure BLOB に書き込むこともできます。 その後、他のツールを使用して、Hive クエリの出力をさらに分析できます。

**Hive クエリの結果をローカル ファイルに出力する。**
Hive クエリの結果をヘッド ノード上のローカル ディレクトリに出力するには、次のように Hadoop コマンド ラインで Hive クエリを送信する必要があります。

    hive -e "<hive query>" > <local path in the head node>

次の例では、Hive クエリの出力が `C:\apps\temp` ディレクトリ内の `hivequeryoutput.txt` ファイルに書き込まれます。

![ワークスペースの作成](./media/move-hive-tables/output-hive-results-1.png)

**Hive クエリの結果を Azure BLOB に出力する**

Hive クエリの結果は、Hadoop クラスターの既定のコンテナー内にある Azure BLOB に出力することもできます。 そのための Hive クエリは次のとおりです。

    insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

次の例では、Hive クエリの出力が、Hadoop クラスターの既定のコンテナー内にある BLOB ディレクトリ `queryoutputdir` に書き込まれます。 ここでは、ディレクトリ名のみを指定する必要があります (BLOB 名は必要ありません)。 `wasb:///queryoutputdir/queryoutput.txt`のようにディレクトリ名と BLOB 名の両方を指定すると、エラーがスローされます。

![ワークスペースの作成](./media/move-hive-tables/output-hive-results-2.png)

Azure ストレージ エクスプローラーを使用して Hadoop クラスターの既定のコンテナーを開くと、Hive クエリの出力が次の図のように表示されます。 フィルター (赤色の四角形によって示されています) を適用して、名前に指定された文字を持つ BLOB のみを取得できます。

![ワークスペースの作成](./media/move-hive-tables/output-hive-results-3.png)

### <a name="hive-editor"></a> 2.Hive エディターで Hive クエリを送信する
*https://<Hadoop cluster name>.azurehdinsight.net/Home/HiveEditor* の形式の URL を Web ブラウザーに入力することで、クエリ コンソール (Hive エディター) を使用することもできます。 このコンソールにログインする必要があるので、Hadoop クラスターの資格情報が必要になります。

### <a name="ps"></a> 3.Azure PowerShell コマンドで Hive クエリを送信する
PowerShell を使用して Hive クエリを送信することもできます。 手順については、「 [PowerShell を使用して Hive ジョブを送信する](../../hdinsight/hadoop/apache-hadoop-use-hive-powershell.md)」を参照してください。

## <a name="create-tables"></a>Hive データベースとテーブルの作成。
Hive クエリは、[GitHub リポジトリ](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) 内で共有されており、そこからダウンロードできます。

Hive テーブルを作成する Hive クエリを次に示します。

    create database if not exists <database name>;
    CREATE EXTERNAL TABLE if not exists <database name>.<table name>
    (
        field1 string,
        field2 int,
        field3 float,
        field4 double,
        ...,
        fieldN string
    )
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' lines terminated by '<line separator>'
    STORED AS TEXTFILE LOCATION '<storage location>' TBLPROPERTIES("skip.header.line.count"="1");

接続する必要があるフィールドと他の構成の説明を次に示します。

* **<database name>**: 作成するデータベースの名前。 既定のデータベースを使用する場合、 *create database...* クエリは省略してかまいません。
* **<table name>**: 指定したデータベース内に作成するテーブルの名前。 既定のデータベースを使用する場合、テーブルは *<table name>* で直接参照でき、<database name> は不要です。
* **<field separator>**: Hive テーブルにアップロードするデータ ファイルのフィールドを区切る区切り記号。
* **<line separator>**: データ ファイル内の行を区切る区切り記号。
* **<storage location>**: Hive テーブルのデータを保存する Azure Sorage の場所。 *LOCATION <storage location>* を指定しなかった場合、既定では、データベースとテーブルは、Hive クラスターの既定のコンテナー内の *hive/warehouse/* ディレクトリに格納されます。 ストレージの場所を指定する場合、ストレージの場所は、データベースとテーブルの既定のコンテナー内でなければなりません。 この場所は、クラスターの既定のコンテナーを基準として、"*'wasb:///<ディレクトリ 1>/'*" や "*'wasb:///<ディレクトリ 1>/<ディレクトリ 2>/'*" などの形式で参照する必要があります。クエリが実行されると、既定のコンテナー内に相対ディレクトリが作成されます。
* **TBLPROPERTIES("skip.header.line.count"="1")**: データ ファイルにヘッダー行が含まれる場合は、このプロパティを *create table* クエリの**最後に**追加する必要があります。 それ以外の場合、ヘッダー行はレコードとしてテーブルに読み込まれます。 データ ファイルにヘッダー行が含まれない場合は、クエリでこの構成を省略することができます。

## <a name="load-data"></a>Hive テーブルへのデータの読み込み
Hive テーブルにデータを読み込む Hive クエリを次に示します。

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

* **<path to blob data>**: Hive テーブルにアップロードする BLOB ファイルが HDInsight Hadoop クラスターの既定のコンテナーに存在する場合、*<path to blob data>* は *'wasb:///<directory in this container>/<blob file name>'* の形式にする必要があります。 BLOB ファイルは、HDInsight Hadoop クラスターの追加コンテナーに配置することもできます。 この場合、*<path to blob data>* は、*'wasb://<container name><storage account name>.blob.core.windows.net/<blob file name>'* の形式である必要があります。

  > [!NOTE]
  > Hive テーブルにアップロードする BLOB データは、Hadoop クラスターのストレージ アカウントの既定のコンテナーまたは追加のコンテナーに配置されている必要があります。 それ以外の場合、 *LOAD DATA* クエリはデータにアクセスできないために失敗します。
  >
  >

## <a name="partition-orc"></a>拡張トピック: パーティション分割テーブルと ORC 形式での Hive データの格納
データのサイズが大きい場合、テーブルのいくつかのパーティションのみをスキャンすればよいクエリでは、テーブルをパーティション分割すると効果的です。 たとえば、Web サイトのログ データを日付別にパーティション分割することができます。

Hive テーブルをパーティション分割することに加え、Optimized Row Columnar (ORC) 形式で Hive データを格納することも効果的です。 詳細については、<a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">Hive によるデータの読み取り、書き込み、および処理時における ORC ファイルを使用したパフォーマンスの向上</a>に関するページをご覧ください。

### <a name="partitioned-table"></a>パーティション テーブル
パーティション テーブルを作成し、そこにデータを読み込む Hive クエリを次に示します。

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
    (field1 string,
    ...
    fieldN string
    )
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
         lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
    LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name>
        PARTITION (<partitionfieldname>=<partitionfieldvalue>);

パーティション テーブルの照会時には、検索の有効性が大幅に向上するように、`where` 句の**先頭**にパーティション条件を追加することをお勧めします。

    select
        field1, field2, ..., fieldN
    from <database name>.<partitioned table name>
    where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>ORC 形式での Hive データの格納
ORC 形式で格納されているデータを BLOB ストレージから Hive テーブルに直接読み込むことはできません。 ORC 形式で格納されているデータを Azure BLOB から Hive テーブルに読み込むために実行する必要がある手順を以下に示します。

外部テーブル **STORED AS TEXTFILE** を作成し、データを BLOB ストレージからテーブルに読み込みます。

        CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<external textfile table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
            lines terminated by '<line separator>' STORED AS TEXTFILE
            LOCATION 'wasb:///<directory in Azure blob>' TBLPROPERTIES("skip.header.line.count"="1");

        LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<table name>;

手順 1 の外部テーブルと同じスキーマを持つ内部テーブルを、同じフィールド区切り記号を使用して作成し、ORC 形式で Hive データを格納します。

        CREATE TABLE IF NOT EXISTS <database name>.<ORC table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

手順 1 の外部テーブルからデータを選択し、ORC テーブルに挿入します。

        INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

> [!NOTE]
> TEXTFILE テーブル *<database name>.<external textfile table name>*  にパーティションが含まれている場合、手順 3. で、`SELECT * FROM <database name>.<external textfile table name>` コマンドは、返されたデータ セット内のフィールドとしてパーティション変数を選択します。 これを *<database name>.<ORC table name>* に挿入すると 失敗します。その理由は、*<database name>.<ORC table name>*  にはテーブル スキーマのフィールドとしてパーティション変数が含まれないためです。 この場合、ユーザーは *<database name><ORC table name>.* に挿入するフィールドを、以下のように具体的に選択する必要があります 。
>
>

        INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
           SELECT field1, field2, ..., fieldN
           FROM <database name>.<external textfile table name>
           WHERE <partition variable>=<partition value>;

すべてのデータが *<database name>.<ORC table name>* に挿入された後に、次のクエリを使用して *<external textfile table name>* を削除するのが安全です。

        DROP TABLE IF EXISTS <database name>.<external textfile table name>;

この手順が終了すれば、すぐに使用できる ORC 形式のデータを含むテーブルが手に入ったことになります。  
