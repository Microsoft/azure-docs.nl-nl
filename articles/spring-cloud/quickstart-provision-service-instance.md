---
title: 'Quickstart: Azure Spring Cloud-service inrichten'
description: Beschrijft het maken van een Azure Spring Cloud service-exemplaar voor app-implementatie.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 6f25c4172b384abd487d2084f31981d16e73ee93
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104879285"
---
# <a name="quickstart-provision-azure-spring-cloud-service"></a>Quickstart: Azure Spring Cloud-service inrichten

::: zone pivot="programming-language-csharp"
In deze quickstart gebruikt u de Azure CLI om een exemplaar van de Azure Spring Cloud-service in te richten.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1). De Azure Spring Cloud-service ondersteunt .NET Core 3.1 en latere versies.
* [Azure CLI versie 2.0.67 of nieuwer](/cli/azure/install-azure-cli).
* [Git](https://git-scm.com/).

## <a name="install-azure-cli-extension"></a>Azure CLI-extensie installeren

Controleer of de Azure CLI-versie 2.0.67 of hoger is:

```azurecli
az --version
```

Voer de volgende opdracht uit om de Azure Spring Cloud-extensie voor Azure CLI te installeren:

```azurecli
az extension add --name spring-cloud
```

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

1. Meld u aan bij de Azure CLI.

    ```azurecli
    az login
    ```

1. Als u meerdere abonnementen hebt, kiest u het abonnement dat u voor deze quickstart wilt gebruiken.

   ```azurecli
   az account list -o table
   ```

   ```azurecli
   az account set --subscription <Name or ID of a subscription from the last step>
   ```

## <a name="provision-an-instance-of-azure-spring-cloud"></a>Een exemplaar van Azure Spring Cloud inrichten

1. Maak een [resourcegroep](../azure-resource-manager/management/overview.md) die uw Azure Spring Cloud-service moet bevatten. De naam van de resourcegroep kan alfanumerieke tekens, onderstrepingstekens, haakjes, afbreekstreepjes, punten (behalve aan het einde) en Unicode-tekens bevatten.

   ```azurecli
   az group create --location eastus --name <resource group name>
   ```

1. Richt een exemplaar van Azure Spring Cloud in. De naam van het server-exemplaar moet uniek zijn, tussen de 4 en 32 tekens lang en mag alleen kleine letters, cijfers en afbreekstreepjes bevatten. Het eerste teken van de servicenaam moet een letter zijn en het laatste teken moet een letter of een cijfer zijn.

    ```azurecli
    az spring-cloud create -n <service instance name> -g <resource group name>
    ```

    Het uitvoeren van deze opdracht kan enkele minuten in beslag nemen.

1. Stel de naam van de standaardresourcegroep en de naam van het service-exemplaar in, zodat u deze waarden niet herhaaldelijk hoeft op te geven in navolgende opdrachten.

   ```azurecli
   az configure --defaults group=<resource group name>
   ```

   ```azurecli
   az configure --defaults spring-cloud=<service instance name>
   ```
::: zone-end

::: zone pivot="programming-language-java"
U kunt Azure Spring Cloud instantiëren met behulp van de Azure-portal of de Azure CLI.  Beide methoden worden beschreven in de volgende procedures.
## <a name="prerequisites"></a>Vereisten

* [JDK 8 installeren](/java/azure/jdk/)
* [Aanmelden voor een Azure-abonnement](https://azure.microsoft.com/free/)
* (Optioneel) [De Azure CLI versie 2.0.67 of hoger installeren](/cli/azure/install-azure-cli) en de Azure Spring Cloud-extensie installeren met de opdracht: `az extension add --name spring-cloud`
* (Optioneel) [De Azure-toolkit voor IntelliJ installeren](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) en [aanmelden](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="provision-an-instance-of-azure-spring-cloud"></a>Een exemplaar van Azure Spring Cloud inrichten

#### <a name="portal"></a>[Portal](#tab/Azure-portal)

Met de volgende procedure maakt u een exemplaar van Azure Spring Cloud met behulp van de Azure Portal.

1. Open [Azure Portal](https://ms.portal.azure.com/) op een nieuw tabblad. 

2. Zoek in het bovenste zoekvak naar **Azure Spring Cloud**.

3. Selecteer **Azure Spring Cloud** in de resultaten.

    ![ASC-pictogram: starten](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. Klik op **+ Toevoegen** op de Azure Spring Cloud-pagina.

    ![ASC-pictogram: toevoegen](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Vul het formulier in op de Azure Spring Cloud-pagina **Maken**.  Houd rekening met de volgende richtlijnen:
    - **Abonnement**: Selecteer het abonnement waarvoor u voor deze resource gefactureerd wilt worden.
    - **Resourcegroep**: Het is een best practice om nieuwe resourcegroepen te maken voor nieuwe resources. Dit wordt in latere stappen gebruikt als **\<resource group name\>** .
    - **Servicedetails/naam**: Geef de **\<service instance name\>** op.  De naam moet tussen de 4 en 32 tekens lang zijn en mag alleen kleine letters, cijfers en afbreekstreepjes bevatten.  Het eerste teken van de servicenaam moet een letter zijn en het laatste teken moet een letter of een cijfer zijn.
    - **Locatie**: Selecteer de locatie voor uw service-exemplaar.

    ![ASC-portal starten](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Klik op **Controleren en maken**.

> [!div class="nextstepaction"]
> [Er is een fout opgetreden](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=public-endpoint)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

De volgende procedure maakt gebruik van de Azure CLI-extensie om een exemplaar van Azure Spring Cloud in te richten.

1. Meld u aan bij de Azure CLI en kies uw actieve abonnement.

    ```azurecli
    az login
    az account list -o table
    az account set --subscription <Name or ID of subscription, skip if you only have 1 subscription>
    ```

1. Bedenk een naam voor uw Azure Spring Cloud-service.  De naam moet tussen de 4 en 32 tekens lang zijn en mag alleen kleine letters, cijfers en afbreekstreepjes bevatten.  Het eerste teken van de servicenaam moet een letter zijn en het laatste teken moet een letter of een cijfer zijn.

1. Maak een resourcegroep die uw Azure Spring Cloud-service bevat.

    ```azurecli
    az group create --location eastus --name <resource group name>
    ```

    Meer informatie over [Azure-resourcegroepen](../azure-resource-manager/management/overview.md).

1. Open een Azure CLI-venster en voer de volgende opdrachten uit om een exemplaar van Azure Spring Cloud in te richten.

    ```azurecli
    az spring-cloud create -n <service instance name> -g <resource group name>
    ```

    De implementatie van de service-instantie duurt ongeveer vijf minuten.
---
::: zone-end

## <a name="next-steps"></a>Volgende stappen

In deze quickstarts hebt u Azure-resources gemaakt waarmee de kosten blijven toenemen als de resources in uw abonnement blijven. Zie [Resources opschonen](spring-cloud-quickstart-logs-metrics-tracing.md#clean-up-resources) als u niet wilt doorgaan met de volgende quickstart. Ga anders verder met de volgende quickstart:

> [!div class="nextstepaction"]
> [Configuratieserver instellen](spring-cloud-quickstart-setup-config-server.md)
