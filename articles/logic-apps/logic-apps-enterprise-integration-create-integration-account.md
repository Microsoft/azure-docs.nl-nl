---
title: B2B-integratieaccounts maken of beheren
description: Integratieaccounts maken, koppelen en beheren voor bedrijfsintegratie met Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 11/04/2020
ms.openlocfilehash: ae5ca6ac822dabd32b6463c3a742901f32b34323
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862251"
---
# <a name="create-and-manage-integration-accounts-for-b2b-enterprise-integrations-in-azure-logic-apps"></a>Integratieaccounts voor B2B-ondernemingsintegraties maken en beheren met behulp van Azure Logic Apps

Voordat u [bedrijfsintegratie en B2B-oplossingen](../logic-apps/logic-apps-enterprise-integration-overview.md) kunt bouwen met behulp van [Azure Logic Apps](../logic-apps/logic-apps-overview.md), moet u een integratieaccount maken. Dit is een afzonderlijke Azure-resource die een veilige, schaalbare en beheerbare container biedt voor de integratieartefacten die u definieert en gebruikt met de werkstromen in uw logische app.

U kunt bijvoorbeeld B2B-artefacten maken, opslaan en beheren, zoals handelspartners, overeenkomsten, kaarten, schema's, certificaten en batchconfiguraties. Voordat uw logische app met deze artefacten kan werken en de Logische apps B2B-connectors kan gebruiken, moet u uw integratieaccount koppelen [aan](#link-account) uw logische app. Zowel uw integratieaccount als de logische app moeten zich op *dezelfde locatie* of regio bevinden.

> [!IMPORTANT]
> Op basis van het type integratieaccount dat u selecteert, worden er kosten in rekening genomen bij het maken van een integratieaccount. Zie prijzen en [factureringsmodellen Logic Apps en](logic-apps-pricing.md#integration-accounts) Logic Apps voor meer [informatie.](https://azure.microsoft.com/pricing/details/logic-apps/)

In dit onderwerp ziet u hoe u deze taken uitvoert:

* Maak uw integratieaccount.

  > [!TIP]
  > Zie Integratieaccounts maken in [een](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)ISE voor het maken van een integratieaccount in een [integratieserviceomgeving.](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment)

* Koppel uw integratieaccount aan een logische app.

* Wijzig de prijscategorie voor uw integratieaccount.

* Ontkoppel uw integratieaccount van een logische app.

* Verplaats uw integratieaccount naar een andere Azure-resourcegroep of -abonnement.

* Verwijder uw integratieaccount.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account en -abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

## <a name="create-integration-account"></a>Integratieaccount maken

### <a name="portal"></a>[Portal](#tab/azure-portal)

Voor deze taak kunt u de Azure Portal door de stappen in deze sectie te volgen, [Azure PowerShell](/powershell/module/Az.LogicApp/New-AzIntegrationAccount)of [Azure CLI.](/cli/azure/resource#az_resource_create)

1. Gebruik de referenties van uw Azure-account om u aan melden bij het [Azure Portal](https://portal.azure.com).

1. Selecteer **Een resource maken** in het hoofdmenu van Azure. Voer in het zoekvak 'integratieaccount' in als filter en selecteer **Integratieaccount.**

   ![Nieuw integratieaccount maken](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

1. Selecteer **onder Integratieaccount** de optie **Maken.**

   ![Kies Toevoegen om een integratieaccount te maken](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

1. Geef de volgende informatie op over uw integratieaccount:

   ![Details van integratieaccounts verstrekken](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Naam** | Ja | <*integration-account-name*> | De naam van uw integratieaccount, die alleen letters, cijfers, afbreekstreepingstekens ( `-` ), onderstrepingstekens ( ), haakjes ( , ) en punten () mag `_` `(` `)` `.` bevatten. In dit voorbeeld wordt Fabrikam-Integration gebruikt. |
   | **Abonnement** | Ja | <*Azure-subscription-name*> | De naam van uw Azure-abonnement |
   | **Resourcegroep** | Ja | <*Naam-van-Azure-resourcegroep*> | De naam voor de [Azure-resourcegroep die moet](../azure-resource-manager/management/overview.md) worden gebruikt voor het ordenen van gerelateerde resources. Voor dit voorbeeld maakt u een nieuwe resourcegroep met de naam FabrikamIntegration-RG. |
   | **Prijscategorie** | Yes | <*prijsniveau*> | De prijscategorie voor het integratieaccount, die u later kunt wijzigen. Voor dit voorbeeld selecteert u **Gratis.** Raadpleeg de volgende onderwerpen voor meer informatie: <p>- [Logic Apps prijsmodel](../logic-apps/logic-apps-pricing.md#integration-accounts) <p>- [Logic Apps en configuratie configureren](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) <p>- [Logic Apps prijzen](https://azure.microsoft.com/pricing/details/logic-apps/) |
   | **Locatie** | Ja | <*Azure-regio*> | De regio waar de metagegevens van uw integratieaccount moeten worden opgeslagen. Selecteer dezelfde locatie als uw logische app of maak uw logische apps op dezelfde locatie als uw integratieaccount. Gebruik voor dit voorbeeld 'VS - west'. <p>**Opmerking:** als u een integratieaccount in een [ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)wilt maken, selecteert u die ISE als de locatie. Zie Integratieaccounts maken in een [ISE voor meer informatie.](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment) |
   | **Log Analytics** | No | Uit, Aan | Houd de **instelling Uit** voor dit voorbeeld. |
   |||||

1. Wanneer u klaar bent, selecteert u **Maken**.

   Nadat de implementatie is voltooid, opent Azure uw integratieaccount.

   ![Azure opent integratieaccount](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

1. Voordat uw logische app uw integratieaccount kan gebruiken, volgt u de volgende stappen om uw integratieaccount en logische app aan elkaar te koppelen.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

U kunt een integratieaccount maken met behulp van de Azure CLI-opdrachten in deze sectie.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-an-integration-account"></a>Een integratieaccount maken

Gebruik deze opdrachten om een integratieaccount te maken.

1. Gebruik de opdracht az extension add om [de extensie az logic integration-account](/cli/azure/logic/integration-account) [toe te](/cli/azure/extension#az_extension_add) voegen:

   ```azurecli
   az extension add –-name logic
   ```

1. Voer de opdracht [az group create](/cli/azure/group#az_group_create) uit om een resourcegroep te maken of een bestaande resourcegroep te gebruiken:

   ```azurecli
   az group create --name myresourcegroup --location westus
   ```

   Gebruik de opdracht [az logic integration-account list](/cli/azure/logic/integration-account#az_logic_integration_account_list) om de integratieaccounts voor een resourcegroep weer te geven:

   ```azurecli
   az logic integration-account list --resource-group myresourcegroup
   ```

1. Voer de opdracht az [logic integration-account create](/cli/azure/logic/integration-account#az_logic_integration_account_create) uit om een integratieaccount te maken:

   ```azurecli
   az logic integration-account create --resource-group myresourcegroup \
       --name integration_account_01 --location westus --sku name=Standard
   ```

   De naam van uw integratieaccount mag alleen letters, cijfers, afbreekstreepingstekens (-), onderstrepingstekens (_), haakjes ((, )) en punten (.) bevatten.

   > [!TIP]
   > Als u een integratieaccount in een [ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)wilt maken, selecteert u die ISE als de locatie. Zie Integratieaccounts maken [in een ISE voor meer informatie.](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment)

   Als u een specifiek integratieaccount wilt weergeven, gebruikt u [de opdracht az logic integration-account show:](/cli/azure/logic/integration-account#az_logic_integration_account_show)

   ```azurecli
   az logic integration-account show --name integration_account_01 --resource-group myresourcegroup
   ```

   U kunt uw SKU of prijscategorie wijzigen met behulp van de [opdracht az logic integration-account update:](/cli/azure/logic/integration-account#az_logic_integration_account_update)

   ```azurecli
   az logic integration-account update --sku name=Basic --name integration_account_01 \
       --resource-group myresourcegroup
   ```

   Zie de volgende resources voor meer informatie over prijzen:

   * [Prijsmodel voor logische apps](../logic-apps/logic-apps-pricing.md#integration-accounts)
   * [Logic Apps en configuratie instellen](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)
   * [Logic Apps prijzen](https://azure.microsoft.com/pricing/details/logic-apps/)

Als u een integratieaccount wilt importeren met behulp van een JSON-bestand, gebruikt [u de opdracht az logic integration-account import:](/cli/azure/logic/integration-account#az_logic_integration_account_import)

```azurecli
az logic integration-account import --name integration_account_01 \
    --resource-group myresourcegroup --input-path integration.json
```

U kunt een integratieaccount verwijderen met behulp van [de opdracht az logic integration-account delete:](/cli/azure/logic/integration-account#az_logic_integration_account_delete)

```azurecli
az logic integration-account delete --name integration_account_01 --resource-group myresourcegroup
```

Voordat uw logische app uw integratieaccount kan gebruiken, koppelt u uw integratieaccount en logische app aan elkaar. In de volgende sectie wordt koppeling beschreven.

---
<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Koppeling maken naar logische app

Als u uw logische apps toegang wilt geven tot een integratieaccount dat uw B2B-artefacten bevat, moet u eerst uw integratieaccount koppelen aan uw logische app. Zowel de logische app als het integratieaccount moeten in dezelfde regio bestaan. Als u deze taak wilt uitvoeren, kunt u de Azure Portal. Als u Visual Studio logische app in een [Azure-resourcegroepproject](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)gebruikt, kunt u uw logische app koppelen aan een integratieaccount met behulp [van Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md#link-integration-account).

1. Zoek en open Azure Portal logische app in de Azure Portal.

1. Open in [Azure Portal](https://portal.azure.com)een bestaande logische app of maak een nieuwe logische app.

1. Selecteer in het menu van uw logische app onder **Instellingen** de optie **Werkstroominstellingen.** Open **onder Integratieaccount** de lijst **Een integratieaccount** selecteren. Selecteer het integratieaccount dat u aan uw logische app wilt koppelen.

   ![Uw integratieaccount selecteren](./media/logic-apps-enterprise-integration-create-integration-account/select-integration-account.png)

1. Selecteer Opslaan om de koppeling te **voltooien.**

   ![Schermopname die laat zien waar u Opslaan selecteert om uw integratieaccount te kiezen.](./media/logic-apps-enterprise-integration-create-integration-account/save-link.png)

   Nadat uw integratieaccount is gekoppeld, wordt in Azure een bevestigingsbericht weergegeven.

   ![Azure bevestigt een geslaagde koppeling](./media/logic-apps-enterprise-integration-create-integration-account/link-confirmation.png)

Uw logische app kan nu gebruikmaken van de artefacten in uw integratieaccount plus de B2B-connectors, zoals XML-validatie en het coderen of decoderen van platte bestanden.  

<a name="change-pricing-tier"></a>

## <a name="change-pricing-tier"></a>Prijscategorie wijzigen

Als u de [limieten voor](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) een integratieaccount wilt verhogen, kunt u upgraden naar een hogere [prijscategorie](#upgrade-pricing-tier), indien beschikbaar. U kunt bijvoorbeeld een upgrade uitvoeren van de gratis laag naar de basic-laag of de Standard-laag. U kunt ook [downgraden naar een lagere laag,](#downgrade-pricing-tier)indien beschikbaar. Zie de volgende onderwerpen voor meer informatie over de prijzen:

* [Logic Apps prijzen](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Prijsmodel voor logische apps](../logic-apps/logic-apps-pricing.md#integration-accounts)

<a name="upgrade-pricing-tier"></a>

### <a name="upgrade-pricing-tier"></a>Prijscategorie upgraden

Als u deze wijziging wilt maken, kunt u de Azure Portal of de Azure CLI gebruiken.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Gebruik de referenties van uw Azure-account om u aan melden bij het [Azure Portal](https://portal.azure.com).

1. Voer in het hoofdzoekvak van Azure 'integratieaccounts' in als filter en selecteer **Integratieaccounts.**

   ![Integratieaccount zoeken](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure toont alle integratieaccounts in uw Azure-abonnementen.

1. Selecteer **onder Integratieaccounts** het integratieaccount dat u wilt verplaatsen. Selecteer Overzicht in het menu van uw **integratieaccount.**

   ![Selecteer 'Overzicht' in het menu van het integratieaccount](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Selecteer in het deelvenster Overzicht de optie **Prijscategorie upgraden,** waarbij alle beschikbare hogere lagen worden vermeld. Wanneer u een laag selecteert, wordt de wijziging onmiddellijk van kracht.

<a name="upgrade-tier-azure-cli"></a>

#### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Als u dit nog niet hebt gedaan, [installeert u de Azure CLI-vereisten.](/cli/azure/get-started-with-azure-cli)

1. Open in Azure Portal de [Azure Cloud Shell](../cloud-shell/overview.md) omgeving.

   ![Azure Cloud Shell openen](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. Voer bij de opdrachtprompt de [ **opdracht az resource** in](/cli/azure/resource#az_resource_update)en stel in op de hogere laag die u `skuName` wilt.

   ```azurecli
   az resource update --resource-group {ResourceGroupName} --resource-type Microsoft.Logic/integrationAccounts --name {IntegrationAccountName} --subscription {AzureSubscriptionID} --set sku.name={SkuName}
   ```
  
   Als u bijvoorbeeld de basic-laag hebt, kunt u instellen `skuName` op `Standard` :

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Standard
   ```

---

<a name="downgrade-pricing-tier"></a>

### <a name="downgrade-pricing-tier"></a>Prijscategorie downgraden

Gebruik de Azure CLI om deze wijziging [aan te brengen.](/cli/azure/get-started-with-azure-cli)

1. Als u dit nog niet hebt gedaan, [installeert u de Azure CLI-vereisten.](/cli/azure/get-started-with-azure-cli)

1. Open in Azure Portal de [Azure Cloud Shell](../cloud-shell/overview.md) omgeving.

   ![Azure Cloud Shell openen](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. Voer bij de opdrachtprompt de [ **opdracht az resource** in](/cli/azure/resource#az_resource_update) en stel in op de laag lager die u `skuName` wilt.

   ```azurecli
   az resource update --resource-group <resourceGroupName> --resource-type Microsoft.Logic/integrationAccounts --name <integrationAccountName> --subscription <AzureSubscriptionID> --set sku.name=<skuName>
   ```
  
   Als u bijvoorbeeld de standard-laag hebt, kunt u instellen `skuName` op `Basic` :

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Basic
   ```

## <a name="unlink-from-logic-app"></a>Loskoppelen van logische app

Als u uw logische app wilt koppelen aan een ander integratieaccount of geen integratieaccount meer wilt gebruiken met uw logische app, verwijdert u de koppeling met behulp van Azure Resource Explorer.

1. Open het browservenster en ga naar [Azure Resource Explorer ( https://resources.azure.com) ](https://resources.azure.com). Meld u aan met dezelfde azure-accountreferenties.

   ![Azure Resource Explorer](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

1. Voer in het zoekvak de naam van uw logische app in, zodat u uw logische app kunt vinden en selecteren.

   ![Logische app zoeken en selecteren](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

1. Selecteer lezen/schrijven op de titelbalk **van de verkenner.**

   ![De modus Lezen/schrijven inschakelen](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-read-write.png)

1. Selecteer bewerken **op het** tabblad **Gegevens.**

   ![Selecteer op het tabblad Gegevens de optie Bewerken](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-edit.png)

1. Zoek in de editor het `integrationAccount` -object en verwijder die eigenschap, die deze indeling heeft:

   ```json
   {
      // <other-attributes>
      "integrationAccount": {
         "name": "<integration-account-name>",
         "id": "<integration-account-resource-ID>",
         "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   Bijvoorbeeld:

   ![Zoek het object integrationAccount](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

1. Selecteer op **het tabblad** Gegevens de optie **Put om** uw wijzigingen op te slaan.

   ![Als u wijzigingen wilt opslaan, selecteert u 'Put'](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

1. Zoek en Azure Portal logische app in het dialoogvenster. Controleer onder de werkstroominstellingen **van uw** app of de eigenschap **Integratieaccount** nu leeg lijkt.

   ![Controleer of het integratieaccount niet is gekoppeld](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Integratieaccount verplaatsen

U kunt uw integratieaccount verplaatsen naar een andere Azure-resourcegroep of een ander Azure-abonnement. Wanneer u resources verplaatst, maakt Azure nieuwe resource-ID's. Zorg er dus voor dat u in plaats daarvan de nieuwe ID's gebruikt en werk alle scripts of hulpprogramma's bij die zijn gekoppeld aan de verplaatste resources. Als u het abonnement wilt wijzigen, moet u ook een bestaande of nieuwe resourcegroep opgeven.

Voor deze taak kunt u de Azure Portal door de stappen in deze sectie of de [Azure CLI te volgen.](/cli/azure/resource#az_resource_move)

1. Gebruik de referenties van uw Azure-account om u aan melden bij het [Azure Portal](https://portal.azure.com).

1. Voer in het hoofdzoekvak van Azure 'integratieaccounts' in als filter en selecteer **Integratieaccounts.**

   ![Integratieaccount zoeken](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure toont alle integratieaccounts in uw Azure-abonnementen.

1. Selecteer **onder Integratieaccounts** het integratieaccount dat u wilt verplaatsen. Selecteer Overzicht in het menu van uw **integratieaccount.**

   ![Selecteer 'Overzicht' in het menu van het integratieaccount](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Selecteer wijzigen **naast Resourcegroep** of **Abonnementsnaam.** 

   ![Resourcegroep of abonnement wijzigen](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

1. Selecteer alle gerelateerde resources die u ook wilt verplaatsen.

1. Op basis van uw selectie volgt u deze stappen om de resourcegroep of het abonnement te wijzigen:

   * Resourcegroep: selecteer in de **lijst Resourcegroep** de doelresourcegroep. Als u een andere resourcegroep wilt maken, selecteert **u Een nieuwe resourcegroep maken.**

   * Abonnement: selecteer in **de lijst** Abonnement het doelabonnement. Selecteer in **de lijst Resourcegroep** de doelresourcegroep. Als u een andere resourcegroep wilt maken, selecteert **u Een nieuwe resourcegroep maken.**

1. Als u wilt bevestigen dat alle scripts of hulpprogramma's die zijn gekoppeld aan de verplaatste resources pas werken als u ze bijwerkt met de nieuwe resource-ID's, selecteert u het bevestigingsvak en selecteert u **OK.**

1. Nadat u klaar bent, moet u alle scripts bijwerken met de nieuwe resource-ID's voor uw verplaatste resources.  

## <a name="delete-integration-account"></a>Integratieaccount verwijderen

Voor deze taak kunt u de Azure Portal door de stappen in deze sectie, [Azure CLI](/cli/azure/resource#az_resource_delete)of [Azure PowerShell.](/powershell/module/az.logicapp/remove-azintegrationaccount)

1. Gebruik de referenties van uw Azure-account om u aan melden bij het [Azure Portal](https://portal.azure.com).

1. Voer in het hoofdzoekvak van Azure 'integratieaccounts' in als filter en selecteer **Integratieaccounts.**

   ![Integratieaccount zoeken](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure toont alle integratieaccounts in uw Azure-abonnementen.

1. Selecteer **onder Integratieaccounts** het integratieaccount dat u wilt verwijderen. Selecteer Overzicht in het menu van uw **integratieaccount.**

   ![Selecteer 'Overzicht' in het menu van het integratieaccount](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Selecteer verwijderen in het deelvenster **Overzicht.**

   ![Selecteer in het deelvenster Overzicht de optie Verwijderen](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

1. Selecteer Ja om te bevestigen dat u uw integratieaccount wilt **verwijderen.**

   ![Selecteer Ja om het verwijderen te bevestigen](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>Volgende stappen

* [Handelspartners maken in uw integratieaccount](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Overeenkomsten maken tussen partners in uw integratieaccount](../logic-apps/logic-apps-enterprise-integration-agreements.md)
