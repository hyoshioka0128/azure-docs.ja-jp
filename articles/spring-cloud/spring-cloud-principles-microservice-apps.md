---
title: Azure Spring Cloud マイクロサービス アプリ用の Java と基本 OS
description: Azure Spring Cloud マイクロサービス アプリ用の Java および基本オペレーティング システムを正常に維持するための原則
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 05/27/2020
ms.custom: devx-track-java
ms.openlocfilehash: 1c403398f7320cefa16a4a570645a6d7d750acc7
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89297503"
---
# <a name="java-and-base-os-for-spring-microservice-apps"></a>Spring マイクロサービス アプリ用の Java とベ基本 OS
以下に示すのは、Azure Spring Cloud マイクロサービス アプリ用の Java および基本オペレーティング システムを正常に維持するための原則です。
## <a name="principles-for-healthy-java-and-base-os"></a>正常な Java および基本 OS の原則
* すべてのサービス レベル (Basic、Standard、Premium) で同じ基本オペレーティング システムを使用する必要があります。
    * 現在、Azure Spring Cloud 上のアプリでは、Debian 10 と Ubuntu 18.04 の組み合わせが使用されています。
    * VMware ビルド サービスでは、Ubuntu 18.04 が使用されています。
* デプロイの開始点 (ソース、JAR) に関係なく、同じ基本オペレーティング システムを使用する必要があります
    * 現在、Azure Spring Cloud 上のアプリでは、Debian 10 と Ubuntu 18.04 の組み合わせが使用されています。
* 基本オペレーティング システムからは、セキュリティ上の脆弱性が排除されている必要があります。
    * Debian 10 基本オペレーティング システムでは、147 の CVE が開かれています。
    * Ubuntu 18.04 基本オペレーティング システムでは、132 の CVE が開かれています。
* ヘッドレス JRE を使用する必要があります。
    * 現在、Azure Spring Cloud 上のアプリでは JDK を使用しています。 ヘッドレス JRE は、より小さいイメージです。
* Java の最新ビルドを使用する必要があります。
    * 現在、Azure Spring Cloud 上のアプリでは、Java 8 ビルド 242 を使用しています。 これは古いビルドです。
 
Azul Systems では、基本オペレーティング システムに対する変更を継続的に精査し、最後にビルドされたイメージを最新の状態に維持します。 Azure Spring Cloud はイメージの変更を検索し、デプロイ全体で継続的に更新を行います。
 
## <a name="faq-for-azure-spring-cloud"></a>Azure Spring Cloud に関する FAQ

* サポートされている Java のバージョンを教えてください (メジャー バージョンとビルド番号)。
    * サポートされる LTS バージョン - Java 8 および11。
    * 最新のビルドを使用してください (たとえば、現在であれば、Java 8 ビルド 252、Java 11 ビルド 7)。
* これらの Java ランタイムはだれがビルドしたのですか。
    * Azul Systems です。
* イメージの基本オペレーティング システムは何ですか。
    * Ubuntu 20.04 LTS (Focal Fossa) です。 アプリは Ubuntu の最新 LTS バージョン上で継続的に使用できます。
    * 「[Ubuntu 20.04 LTS (Focal Fossa)](http://releases.ubuntu.com/focal/)」を参照してください。
* サポートされている Java ランタイムをローカル開発用にダウンロードするにはどうすればよいですか。 
    * 「[Azure 用の JDK および Azure Stack をインストールする](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install)」を参照してください
* Java ランタイム レベルの問題についてサポートを受けるにはどうすればよいですか。
    * Azure サポートでサポート チケットを開いてください。
 
## <a name="default-deployment-on-azure-spring-cloud"></a>Azure Spring Cloud での既定のデプロイ

> ![既定のデプロイ](media/spring-cloud-principles/spring-cloud-default-deployment.png)
 
## <a name="next-steps"></a>次のステップ
* [クイック スタート: Azure Portal を使用して既存の Azure Spring Cloud アプリケーションを起動する](spring-cloud-quickstart.md)」をご覧ください。
* [Azure および Azure Stack の Java 長期サポート](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-long-term-support)
