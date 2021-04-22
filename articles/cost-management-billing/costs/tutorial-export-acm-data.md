---
title: 'Zelfstudie: Gegevensexports uit Azure Cost Management instellen en beheren'
description: In dit artikel leest u hoe u gegevensexports uit Azure Cost Management instelt en beheert, zodat u deze kunt gebruiken in externe systemen.
author: bandersmsft
ms.author: banders
ms.date: 03/24/2020
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: seodec18, devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 08bf165254c8759a978b406b3e9a990a41ab7a30
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873198"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Zelfstudie: Geëxporteerde gegevens maken en beheren

Als u de zelfstudie over kostenanalyse hebt doorgenomen, bent u al bekend met het handmatig downloaden van gegevens uit Cost Management. U kunt echter ook een terugkerende taak instellen om uw Cost Management-gegevens automatisch dagelijks, wekelijks of maandelijks naar uw Azure-opslag te exporteren. De gegevens worden in CSV-indeling geëxporteerd en bevatten alle informatie die door Cost Management is verzameld. U kunt de geëxporteerde gegevens in uw Azure-opslag vervolgens in externe systemen laden en combineren met uw eigen gegevens. Externe systemen waarin de geëxporteerde gegevens kunnen worden gebruikt, zijn bijvoorbeeld dashboards of andere financiële systemen.

Bekijk de video [How to schedule exports to storage with Azure Cost Management](https://www.youtube.com/watch?v=rWa_xI1aRzo) (Exports naar opslag plannen met Azure Cost Management) over het maken van een geplande export van uw Azure-kostengegevens naar Azure Storage. Als u andere video’s wilt bekijken, gaat u naar het [YouTube-kanaal voor Cost Management](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/rWa_xI1aRzo]

De voorbeelden in deze zelfstudie laten u stapsgewijs zien hoe u uw Cost Management-gegevens exporteert en vervolgens controleert of de export is gelukt.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een dagelijkse export uitvoeren
> * Controleren of de gegevens zijn geëxporteerd

## <a name="prerequisites"></a>Vereisten
Exporteren van gegevens is beschikbaar voor verschillende typen Azure-accounts, waaronder klanten met een [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) en een [Microsoft-klantovereenkomst](get-started-partners.md). Zie voor de volledige lijst met ondersteunde accounttypen [Gegevens van Azure Cost Management begrijpen](understand-cost-mgt-data.md). In abonnementen worden de volgende Azure-machtigingen, of bereiken, ondersteund voor het exporteren van gegevens door gebruikers en groepen. Zie [Bereiken begrijpen en gebruiken](understand-work-scopes.md) voor meer informatie over bereiken

- Eigenaar â€" Kan geplande exports voor een abonnement maken, wijzigen of verwijderen.
- Inzender â€" Kan hun eigen geplande exports maken, wijzigen of verwijderen. Zij kunnen bovendien de naam wijzigen van geplande exports die door anderen zijn ingesteld.
- Lezer â€" Kan exports plannen die ze machtigingen hebben.

Voor Azure-opslagaccounts:
- Voor het wijzigen van een geconfigureerd opslagaccount zijn schrijfmachtigingen vereist, ongeacht de machtiging voor het exporteren.
- Uw Azure-opslagaccount moet zijn geconfigureerd voor blob- of bestandsopslag.

Als u een nieuw abonnement hebt, kunt u de Cost Management-functies niet meteen gebruiken. Het kan tot 48 uur duren voordat u alle Cost Management-functies kunt gebruiken.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure
Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com/).

## <a name="create-a-daily-export"></a>Een dagelijkse export uitvoeren

### <a name="portal"></a>[Portal](#tab/azure-portal)

Als u een gegevensexport wilt maken of weergeven, of een export wilt plannen, opent u het gewenste bereik in de Azure-portal en selecteert u **Kostenanalyse** in het menu. Ga bijvoorbeeld naar **Abonnementen**, selecteer een abonnement in de lijst en selecteer vervolgens **Kostenanalyse** in het menu. Selecteer bovenaan de pagina Kostenanalyse de optie **Instellingen**, en vervolgens **Exports**.

> [!NOTE]
> - Naast abonnementen kunt u exports maken voor resourcegroepen, beheergroepen, afdelingen en registraties. Zie [Understand and work with scopes](understand-work-scopes.md) (Engelstalig) voor meer informatie over bereiken.
>- Wanneer u als partner bent aangemeld bij het bereik van het factureringsaccount of bij de tenant van een klant, kunt u gegevens exporteren naar een Azure Storage-account dat is gekoppeld aan uw partneropslagaccount. U moet hiervoor wel een actief abonnement hebben in uw CSP-tenant.

1. Selecteer **Toevoegen** en typ een naam voor de export.
1. Maak een selectie voor de **Metrische gegevens**:
    - **Werkelijke kosten (gebruik en aankopen)** : selecteer dit om standaardgebruik en -aankopen te exporteren
    - **Afgeschreven kosten (gebruik en aankopen)** : selecteer dit om de afgeschreven kosten te exporteren voor aankopen zoals Azure-reserveringen
1. Maak een selectie voor **Exporttype**:
    - **Dagelijkse export van maand-tot-heden-kosten** â€" Biedt dagelijks een nieuw exportbestand voor uw kosten van maand tot heden. De meest recente gegevens worden uit de vorige dagelijkse exports samengevoegd.
    - **Wekelijkse export van kosten voor de afgelopen** zeven dagen" Maakt een wekelijkse export van uw kosten voor de afgelopen zeven dagen vanaf de geselecteerde begindatum van uw export.
    - **Maandelijkse export van** de kosten van de afgelopen maand â€" Biedt u een export van de kosten van uw vorige maand in vergelijking met de huidige maand die u de export maakt. Vanaf dit moment voert de planning een export uit op de vijfde dag van elke nieuwe maand, met hierin uw kosten voor de vorige maand.
    - **One-time export** â€" Hiermee kunt u een datumbereik kiezen voor historische gegevens die u wilt exporteren naar Azure Blob Storage. U kunt de historische kosten voor maximaal 90 dagen exporteren, vanaf de door u gekozen dag. Deze export wordt onmiddellijk uitgevoerd en is binnen twee uur beschikbaar in uw opslagaccount.
        Afhankelijk van uw exporttype kiest u een begindatum of een datum **Van** - **Tot en met**.
1. Geef het abonnement voor uw Azure opslagaccount op, en selecteer vervolgens een resourcegroep of maak een nieuwe.
1. Selecteer de naam van het opslagaccount of maak een nieuwe.
1. Selecteer de locatie (Azure-regio).
1. Geef de opslagcontainer en het pad naar de map op waar u het exportbestand wilt opslaan.
    :::image type="content" source="./media/tutorial-export-acm-data/basics_exports.png" alt-text="Voorbeeld van nieuwe export" lightbox="./media/tutorial-export-acm-data/basics_exports.png":::
1. Controleer de details van uw export en selecteer **Maken**.

Uw nieuwe export wordt weergegeven in de lijst met exports. Nieuwe exports zijn standaard ingeschakeld. Als u een geplande export wilt uitschakelen of verwijderen, selecteert u een item in de lijst en selecteert u vervolgens **Uitschakelen** of **Verwijderen**.

Het kan 12 tot 24 uur duren voordat de export voor het eerst wordt uitgevoerd. Het kan echter langer duren voordat er in het exportbestand gegevens worden weergegeven.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Wanneer u een exportprogramma maakt, moet u de resourceprovider handmatig registreren bij het abonnement `Microsoft.CostManagementExports` waarin het opslagaccount zich bevindt. Registratie vindt automatisch plaats wanneer u de export maakt met behulp van Azure Portal. Zie Resourceprovider registreren voor meer informatie over het registreren [van resourceproviders.](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)

Begin door de omgeving voor te bereiden op de Azure CLI:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

1. Nadat u zich hebt aangemeld, gebruikt u de [az costmanagement export list](/cli/azure/costmanagement/export#az_costmanagement_export_list) opdracht om uw huidige exports te bekijken:

   ```azurecli
   az costmanagement export list --scope "subscriptions/00000000-0000-0000-0000-000000000000"
   ```

   >[!NOTE]
   >
   >* Naast abonnementen kunt u exports maken voor resourcegroepen en beheergroepen. Zie [Understand and work with scopes](understand-work-scopes.md) (Engelstalig) voor meer informatie over bereiken.
   >* Wanneer u als partner bent aangemeld bij het bereik van het factureringsaccount of bij de tenant van een klant, kunt u gegevens exporteren naar een Azure Storage-account dat is gekoppeld aan uw partneropslagaccount. U moet hiervoor wel een actief abonnement hebben in uw CSP-tenant.

1. Maak een resourcegroep of gebruik een bestaande resourcegroep. U kunt een resourcegroep maken met de opdracht [az group create](/cli/azure/group#az_group_create):

   ```azurecli
   az group create --name TreyNetwork --location "East US"
   ```

1. Maak een opslagaccount om de exports te ontvangen of gebruik een bestaand opslagaccount. Om een opslagaccount te maken, gebruikt u de opdracht [az storage account create](/cli/azure/storage/account#az_storage_account_create):

   ```azurecli
   az storage account create --resource-group TreyNetwork --name cmdemo
   ```

1. Voer de opdracht [az costmanagement export create](/cli/azure/costmanagement/export#az_costmanagement_export_create) uit om de export te maken:

   ```azurecli
   az costmanagement export create --name DemoExport --type ActualCost \
   --scope "subscriptions/00000000-0000-0000-0000-000000000000" --storage-account-id cmdemo \
   --storage-container democontainer --timeframe MonthToDate --recurrence Daily \
   --recurrence-period from="2020-06-01T00:00:00Z" to="2020-10-31T00:00:00Z" \
   --schedule-status Active --storage-directory demodirectory
   ```

   Voor de parameter **--type** kunt u `ActualCost`, `AmortizedCost`of `Usage`kiezen.

   In dit voorbeeld wordt `MonthToDate` gebruikt. De export maakt dagelijks een exportbestand voor de kosten van de maand tot heden. De meest recente gegevens worden uit de vorige dagelijkse exports samengevoegd van deze maand.

1. Als u de details van de exportbewerking wilt bekijken, gebruikt u de opdracht [az costmanagement export show](/cli/azure/costmanagement/export#az_costmanagement_export_show):

   ```azurecli
   az costmanagement export show --name DemoExport \
      --scope "subscriptions/00000000-0000-0000-0000-000000000000"
   ```

1. Een export bijwerken met behulp van de opdracht [az costmanagement export update](/cli/azure/costmanagement/export#az_costmanagement_export_update):

   ```azurecli
   az costmanagement export update --name DemoExport
      --scope "subscriptions/00000000-0000-0000-0000-000000000000" --storage-directory demodirectory02
   ```

   In dit voorbeeld wordt de uitvoermap gewijzigd.

>[!NOTE]
>Het kan 12 tot 24 uur duren voordat de export voor het eerst wordt uitgevoerd. Het kan echter langer duren voordat er in het exportbestand gegevens worden weergegeven.

U kunt een export verwijderen met de opdracht [az costmanagement export delete](/cli/azure/costmanagement/export#az_costmanagement_export_delete):

```azurecli
az costmanagement export delete --name DemoExport --scope "subscriptions/00000000-0000-0000-0000-000000000000"
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Wanneer u programmatisch een export maakt, moet u de resourceprovider handmatig registreren bij het `Microsoft.CostManagementExports` abonnement waarin het opslagaccount zich bevindt. Registratie vindt automatisch plaats wanneer u de export maakt met behulp van Azure Portal. Zie Resourceprovider registreren voor meer informatie over het registreren [van resourceproviders.](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)

Begin door de omgeving voor te bereiden op Azure PowerShell:

[!INCLUDE [azure-powershell-requirements-no-header.md](../../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > Zo lang de PowerShell-module **Az.CostManagement** in de preview-fase is, moet u deze afzonderlijk installeren met behulp van de cmdlet `Install-Module`. Nadat de PowerShell-module algemeen beschikbaar is geworden, wordt deze onderdeel van toekomstige releases van de Az PowerShell-module en is deze standaard beschikbaar vanuit Azure Cloud Shell.

  ```azurepowershell-interactive
  Install-Module -Name Az.CostManagement
  ```

1. Nadat u zich hebt aangemeld, gebruikt u de opdracht [Get-AzCostManagementExport](/powershell/module/Az.CostManagement/get-azcostmanagementexport) om uw huidige exports te bekijken:

   ```azurepowershell-interactive
   Get-AzCostManagementExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000'
   ```

   >[!NOTE]
   >
   >* Naast abonnementen kunt u exports maken voor resourcegroepen en beheergroepen. Zie [Understand and work with scopes](understand-work-scopes.md) (Engelstalig) voor meer informatie over bereiken.
   >* Wanneer u als partner bent aangemeld bij het bereik van het factureringsaccount of bij de tenant van een klant, kunt u gegevens exporteren naar een Azure Storage-account dat is gekoppeld aan uw partneropslagaccount. U moet hiervoor wel een actief abonnement hebben in uw CSP-tenant.

1. Maak een resourcegroep of gebruik een bestaande resourcegroep. Maak een resourcegroep met de cdmlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

   ```azurepowershell-interactive
   New-AzResourceGroup -Name TreyNetwork -Location eastus
   ```

1. Maak een opslagaccount om de exports te ontvangen of gebruik een bestaand opslagaccount. Maak een opslagaccount met de cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount):

   ```azurepowershell-interactive
   New-AzStorageAccount -ResourceGroupName TreyNetwork -AccountName cmdemo -SkuName Standard_RAGRS -Location eastus
   ```

1. Voer de cmdlet [New-AzCostManagementExport](/powershell/module/Az.CostManagement/new-azcostmanagementexport) uit om de export te maken:

   ```azurepowershell-interactive
   $Params = @{
     Name = 'DemoExport'
     DefinitionType = 'ActualCost'
     Scope = 'subscriptions/00000000-0000-0000-0000-000000000000'
     DestinationResourceId = '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/treynetwork/providers/Microsoft.Storage/storageAccounts/cmdemo'
     DestinationContainer = 'democontainer'
     DefinitionTimeframe = 'MonthToDate'
     ScheduleRecurrence = 'Daily'
     RecurrencePeriodFrom = '2020-06-01T00:00:00Z'
     RecurrencePeriodTo = '2020-10-31T00:00:00Z'
     ScheduleStatus = 'Active'
     DestinationRootFolderPath = 'demodirectory'
     Format = 'Csv'
   }
   New-AzCostManagementExport @Params
   ```

   Voor de parameter **DefinitionType** kunt u `ActualCost`, `AmortizedCost` of `Usage` kiezen.

   In dit voorbeeld wordt `MonthToDate` gebruikt. De export maakt dagelijks een exportbestand voor de kosten van de maand tot heden. De meest recente gegevens worden uit de vorige dagelijkse exports samengevoegd van deze maand.

1. Gebruik de cmdlet `Get-AzCostManagementExport` om de details van uw exportbewerking te bekijken:

   ```azurepowershell-interactive
   Get-AzCostManagementExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000'
   ```

1. Gebruik de cmdlet [Update-AzCostManagementExport](/powershell/module/Az.CostManagement/update-azcostmanagementexport) op een export bij te werken:

   ```azurepowershell-interactive
   Update-AzCostManagementExport -Name DemoExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000' -DestinationRootFolderPath demodirectory02
   ```

   In dit voorbeeld wordt de uitvoermap gewijzigd.

>[!NOTE]
>Het kan 12 tot 24 uur duren voordat de export voor het eerst wordt uitgevoerd. Het kan echter langer duren voordat er in het exportbestand gegevens worden weergegeven.

Gebruik de cmdlet [Remove-AzCostManagementExport](/powershell/module/Az.CostManagement/remove-azcostmanagementexport) op een export te verwijderen:

```azurepowershell-interactive
Remove-AzCostManagementExport -Name DemoExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000'
```

---

### <a name="export-schedule"></a>Exportplanning

Geplande exports worden beïnvloed door het tijdstip en de dag van de week waarop u de export in eerste instantie maakt. Wanneer u een geplande export maakt, wordt de export voor elk volgend exemplaar van de export met dezelfde frequentie uitgevoerd. Als voor een dagelijkse export van een exportset van de kosten van maand tot heden bijvoorbeeld een dagelijkse frequentie is ingesteld, wordt de export dagelijks uitgevoerd. Bij een wekelijkse export wordt de export elke week op dezelfde dag uitgevoerd zoals dit is gepland. De exacte levertijd van de export kan niet worden gegarandeerd en de geëxporteerde gegevens zijn binnen vier uur na de uitvoering beschikbaar.

Voor elke export wordt een nieuw bestand gemaakt. Oudere exports worden dus niet overschreven.

#### <a name="create-an-export-for-multiple-subscriptions"></a>Een export maken voor meerdere abonnementen

Als u een Enterprise Agreement hebt, kunt u een beheergroep gebruiken om gegevens over abonnementskosten te verzamelen in één container. Vervolgens kunt u gegevens over kostenbeheer exporteren voor de beheergroep.

Exports voor beheergroepen van andere typen abonnementen worden niet ondersteund.

1. Als u nog geen beheergroep hebt gemaakt, maakt u een groep en wijst u er abonnementen aan toe.
1. Stel in kostenanalyse het bereik in op uw beheergroep, en selecteer **Deze beheergroep selecteren**.
    :::image type="content" source="./media/tutorial-export-acm-data/management-group-scope.png" alt-text="Voorbeeld van de optie Deze beheergroep selecteren" lightbox="./media/tutorial-export-acm-data/management-group-scope.png":::
1. Maak een export in het bereik om gegevens over kostenbeheer op te halen voor de abonnementen in de beheergroep.
    :::image type="content" source="./media/tutorial-export-acm-data/new-export-management-group-scope.png" alt-text="Voorbeeld van de optie Nieuwe export maken, met het bereik van een beheergroep":::

## <a name="verify-that-data-is-collected"></a>Controleren of de gegevens zijn geëxporteerd

U kunt eenvoudig controleren of uw Cost Management-gegevens daadwerkelijk zijn geëxporteerd door het CSV-exportbestand in Azure Storage Explorer te bekijken.

Selecteer de naam van het opslagaccount in de lijst met exports. Selecteer Openen in Explorer op de pagina van het opslagaccount. Als u een bevestigingsvenster ziet, selecteert u **Ja** om het bestand te openen in Azure Storage Explorer.

![Opslagaccountpagina, waarbij voorbeeldinformatie en een koppeling naar Openen in Explorer worden weergegeven](./media/tutorial-export-acm-data/storage-account-page.png)

Ga in Storage Explorer naar de container die u wilt openen en selecteer de map voor de huidige maand. U ziet nu een lijst met CSV-bestanden. Selecteer het gewenste bestand en selecteer **Openen**.

![Voorbeeldinformatie weergegeven in Storage Explorer](./media/tutorial-export-acm-data/storage-explorer.png)

Het bestand wordt geopend met het programma of de toepassing waarmee bestanden met de extensie CSV standaard worden geopend. Dit is een voorbeeld met Excel.

![Voorbeeld van geëxporteerde CSV-gegevens, weergegeven in Excel](./media/tutorial-export-acm-data/example-export-data.png)

### <a name="download-an-exported-csv-data-file"></a>Een geëxporteerd CSV-gegevensbestand downloaden

U kunt het geëxporteerde CSV-bestand ook downloaden in de Azure-portal. In de volgende stappen wordt uitgelegd hoe u het bestand kunt vinden in Kostenanalyse.

1. Selecteer in Kostenanalyse **Instellingen**, en selecteer vervolgens **Exports**.
1. Selecteer in de lijst met exports het opslagaccount voor een export.
1. Selecteer in uw opslagaccount op **Containers**.
1. Selecteer de container in de lijst met containers.
1. Navigeer door de mappen en opslagblobs naar de gewenste datum.
1. Selecteer het CSV-bestand en selecteer vervolgens **Downloaden**.

[![Voorbeeld van download met exportgegevens](./media/tutorial-export-acm-data/download-export.png)](./media/tutorial-export-acm-data/download-export.png#lightbox)

## <a name="view-export-run-history"></a>Bekijk export uitvoeringsgeschiedenis

U kunt de uitvoeringsgeschiedenis van uw geplande export bekijken door een individuele export te selecteren op de exportlijstpagina. De exportlijstpagina biedt u ook snelle toegang om de looptijd van uw vorige exporten te bekijken en de volgende keer dat de export wordt uitgevoerd. Hier is een voorbeeld dat de uitvoeringsgeschiedenis laat zien.

:::image type="content" source="./media/tutorial-export-acm-data/run-history.png" alt-text="Schermopname van het deelvenster Exports.":::

Selecteer een export om de uitvoeringsgeschiedenis te bekijken.

:::image type="content" source="./media/tutorial-export-acm-data/single-export-run-history.png" alt-text="Schermopname van de uitvoeringsgeschiedenis van een export.":::

## <a name="access-exported-data-from-other-systems"></a>Geëxporteerde gegevens gebruiken in externe systemen

Het exporteren van Cost Management-gegevens heeft vaak als doel om deze gegevens in een extern systeem te gebruiken. Denk bijvoorbeeld aan een dashboard of een ander financieel systeem. Omdat al deze systemen sterk van elkaar verschillen, heeft het weinig zin om hier een voorbeeld van te laten zien.  In [Introduction to Azure Storage](../../storage/common/storage-introduction.md) (Inleiding tot Azure Storage) kunt u echter aan de slag met het openen van uw gegevens in de door u gebruikte toepassingen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een dagelijkse export uitvoeren
> * Controleren of de gegevens zijn geëxporteerd

Ga naar de volgende zelfstudie, waarin u leert om de efficiëntie te optimaliseren en te verbeteren door inactieve en onderbenutte resources te identificeren.

> [!div class="nextstepaction"]
> [Aanbevelingen bekijken en hier actie op ondernemen](tutorial-acm-opt-recommendations.md)
