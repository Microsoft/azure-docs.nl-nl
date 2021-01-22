---
title: Aan de slag met PowerShell
description: Een korte inleiding in de Azure PowerShell-cmdlets die u kunt gebruiken voor het beheren van Batch-resources.
ms.topic: how-to
ms.date: 01/21/2021
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 2b51a2a7852df82625fb342bbbbc4a3a1cbf72a3
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685507"
---
# <a name="manage-batch-resources-with-powershell-cmdlets"></a>Batch-resources beheren met PowerShell-cmdlets

Met de Azure Batch Power shell-cmdlets kunt u veel veelvoorkomende batch taken uitvoeren en scripteren. Dit is een korte inleiding in de cmdlets die u kunt gebruiken om uw Batch-accounts te beheren en te werken met uw Batch-resources, zoals pools en taken.

Zie [Naslaginformatie over Azure Batch-cmdlets](/powershell/module/az.batch) voor een volledige lijst met Batch-cmdlets en gedetailleerde cmdlet-syntaxis.

Het wordt aangeraden uw Azure PowerShell-modules regelmatig bij te werken om te profiteren van service-updates en -verbeteringen.

## <a name="prerequisites"></a>Vereisten

- [De Azure PowerShell-module installeren en configureren](/powershell/azure/). Zie de [PowerShell Gallery](https://www.powershellgallery.com/packages/Az.Batch/) voor het installeren van een specifieke Azure Batch-module, zoals een prerelease-module.

- Voer de cmdlet **Connect-AzAccount** uit om verbinding te maken met uw abonnement (de Azure Batch-cmdlets zijn meegeleverd in de Azure Resource Manager-module):

  ```powershell
  Connect-AzAccount
  ```

- **Registreer bij de naamruimte van de Batch-provider**. U hoeft deze bewerking slechts **één keer per abonnement** uit te voeren.
  
  ```powershell
  Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
  ```

## <a name="manage-batch-accounts-and-keys"></a>Batch-accounts en -sleutels beheren

### <a name="create-a-batch-account"></a>Batch-account maken

Met **New-AzBatchAccount** wordt een Batch-account in een opgegeven resourcegroep gemaakt. Als u nog geen resourcegroep hebt, maakt u er een door de cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) uit te voeren. Geef een van de Azure-gebieden op in de parameter **locatie**, bijvoorbeeld 'US - centraal'. Bijvoorbeeld:

```powershell
New-AzResourceGroup –Name MyBatchResourceGroup –Location "Central US"
```

Maak vervolgens een Batch-account in de resourcegroep. Geef in <*accountnaam*> een naam op voor het account en de locatie en naam van uw resourcegroep. Het kan even duren voordat het Batch-account is gemaakt. Bijvoorbeeld:

```powershell
New-AzBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName <res_group_name>
```

> [!NOTE]
> De Batch-accountnaam moet uniek zijn voor de Azure-regio voor de resourcegroep, minimaal 3 en maximaal 24 tekens bevatten en alleen bestaan uit kleine letters en cijfers.

### <a name="get-account-access-keys"></a>Toegangssleutels van account ophalen

Met **Get-AzBatchAccountKeys** worden de toegangssleutels weergegeven die aan een Azure Batch-account zijn gekoppeld. Voer bijvoorbeeld de volgende opdracht uit om de primaire en secundaire sleutels op te halen van het account dat u hebt gemaakt.

 ```powershell
$Account = Get-AzBatchAccountKeys –AccountName <account_name>

$Account.PrimaryAccountKey

$Account.SecondaryAccountKey
```

### <a name="generate-a-new-access-key"></a>Een nieuwe toegangssleutel genereren

Met **New-AzBatchAccountKey** wordt een nieuwe primaire of secundaire accountsleutel voor een Azure Batch-account gegenereerd. Typ bijvoorbeeld de volgende opdracht voor het genereren van een nieuwe primaire sleutel voor het Batch-account:

```powershell
New-AzBatchAccountKey -AccountName <account_name> -KeyType Primary
```

> [!NOTE]
> Als u een nieuwe secundaire sleutel wilt genereren, geeft u 'Secundair' op voor de parameter **KeyType**. U moet de primaire en secundaire sleutels afzonderlijk opnieuw genereren.

### <a name="delete-a-batch-account"></a>Batch-account verwijderen

Met **Remove-AzBatchAccount** wordt een Batch-account verwijderd. Bijvoorbeeld:

```powershell
Remove-AzBatchAccount -AccountName <account_name>
```

Als hierom wordt gevraagd, bevestigt u dat u het account wilt verwijderen. Het kan even duren voordat het account is verwijderd.

## <a name="create-a-batchaccountcontext-object"></a>Een BatchAccountContext-object maken

Voor het beheren van Batch-resources kunt u verificatie op basis van gedeelde sleutels of Azure Active Directory-verificatie gebruiken. Voor verificatie met behulp van de Batch PowerShell-cmdlets maakt u eerst een BatchAccountContext-object om uw accountreferenties of identiteit in op te slaan. U geeft het BatchAccountContext-object door aan cmdlets waarvoor de parameter **BatchContext** wordt gebruikt.

### <a name="shared-key-authentication"></a>Gedeelde sleutelverificatie

```powershell
$context = Get-AzBatchAccountKeys -AccountName <account_name>
```

> [!NOTE]
> Standaard wordt de primaire sleutel van het account gebruikt voor verificatie, maar u kunt expliciet de te gebruiken sleutel selecteren door de eigenschap **KeyInUse** van het object BatchAccountContext te wijzigen: `$context.KeyInUse = "Secondary"`.

### <a name="azure-active-directory-authentication"></a>Verificatie via Azure Active Directory

```powershell
$context = Get-AzBatchAccount -AccountName <account_name>
```

## <a name="create-and-modify-batch-resources"></a>Batch-resources maken en wijzigen

Gebruik cmdlets zoals **New-AzBatchPool**, **New-AzBatchJob** en **New-AzBatchTask** om resources onder een Batch-account te maken. Er zijn overeenkomende **Get-**- en **Set-**-cmdlets voor het bijwerken van de eigenschappen van bestaande resources en **Remove-**-cmdlets om resources onder een Batch-account te verwijderen.

Wanneer u veel van deze cmdlets gebruikt, moet u niet alleen een BatchContext-object doorgeven, maar ook objecten maken of doorgeven die gedetailleerde resource-instellingen bevatten, zoals weergegeven in het volgende voorbeeld. Raadpleeg de gedetailleerde Help-informatie voor elke cmdlet voor meer voorbeelden.

### <a name="create-a-batch-pool"></a>Batch-pool maken

Wanneer u een batch-pool maakt of bijwerkt, geeft u een [configuratie](nodes-and-pools.md#configurations)op. Pools moeten in het algemeen worden geconfigureerd met virtuele-machine configuratie, waarmee u een van de ondersteunde Linux-of Windows VM-installatie kopieën kunt opgeven die worden vermeld op de [Azure virtual machines Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images&page=1), of een aangepaste installatie kopie moeten bieden die u hebt voor bereid. Cloud Services-configuratie Pools bieden alleen Windows Compute-knoop punten en bieden geen ondersteuning voor alle batch-functies.

Wanneer u **New-AzBatchPool** uitvoert, geeft u de instellingen van het besturings systeem door in een PSVirtualMachineConfiguration-of PSCloudServiceConfiguration-object. Met het volgende codefragment wordt bijvoorbeeld een Batch-groep gemaakt met rekenknooppunten met de grootte Standard_a1 in de configuratie van de virtuele machine, gerepliceerd met Ubuntu Server 18.04-LTS. Hier geeft de parameter **VirtualMachineConfiguration** de variabele *$configuration* op als het PSVirtualMachineConfiguration-object. Met de parameter **BatchContext** wordt een eerder gedefinieerde variabele *$context* opgegeven als het BatchAccountContext-object.

```powershell
$imageRef = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSImageReference" -ArgumentList @("UbuntuServer","Canonical","18.04-LTS")

$configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSVirtualMachineConfiguration" -ArgumentList @($imageRef, "batch.node.ubuntu 18.04")

New-AzBatchPool -Id "mypspool" -VirtualMachineSize "Standard_a1" -VirtualMachineConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context
```

Het doelaantal rekenknooppunten in de nieuwe pool wordt berekend met een formule voor automatisch schalen. In dit geval is de formule gewoon **$TargetDedicated=4**, waarmee wordt aangegeven dat het aantal rekenknooppunten in de pool maximaal 4 is.

## <a name="query-for-pools-jobs-tasks-and-other-details"></a>Query voor pools, jobs, taken en andere details

Gebruik cmdlets zoals **Get-AzBatchPool**, **Get-AzBatchJob** en **Get-AzBatchTask** om een query uit te voeren voor entiteiten die zijn gemaakt onder een Batch-account.

### <a name="query-for-data"></a>Query voor gegevens

Gebruik bijvoorbeeld **Get-AzBatchPools** om uw pools te vinden. Standaard wordt deze query voor alle pools onder uw account uitgevoerd, in de veronderstelling dat u het object BatchAccountContext al hebt opgeslagen in *$context*:

```powershell
Get-AzBatchPool -BatchContext $context
```

### <a name="use-an-odata-filter"></a>Een OData-filter gebruiken

U kunt een OData-filter opgeven met behulp van de parameter **Filter** om alleen de objecten te zoeken waarin u bent geïnteresseerd. U kunt bijvoorbeeld alle pools zoeken met id's die beginnen met 'myPool':

```powershell
$filter = "startswith(id,'myPool')"

Get-AzBatchPool -Filter $filter -BatchContext $context
```

Deze methode is niet zo flexibel als het gebruik van een 'Where-Object' in een lokale pijplijn. De query wordt rechtstreeks naar de Batch-service verzonden zodat al het filteren aan de serverzijde plaatsvindt, waardoor er internetbandbreedte wordt bespaard.

### <a name="use-the-id-parameter"></a>De parameter Id gebruiken

Een alternatief voor een OData-filter is het gebruik van de parameter **Id**. Als u een query uit wilt voeren voor een specifieke pool met id 'myPool':

```powershell
Get-AzBatchPool -Id "myPool" -BatchContext $context
```

Met de parameter **Id** wordt alleen een zoekopdracht voor een volledige id ondersteund, geen jokertekens of filters van het OData-type.

### <a name="use-the-maxcount-parameter"></a>De parameter MaxCount gebruiken

Standaard worden met elke cmdlet maximaal 1000 objecten geretourneerd. Als deze limiet is bereikt, moet u uw filter verfijnen zodat er minder objecten worden opgehaald, of moet u expliciet een maximum instellen met de parameter **MaxCount**. Bijvoorbeeld:

```powershell
Get-AzBatchTask -MaxCount 2500 -BatchContext $context
```

Als u de bovengrens wilt verwijderen, stelt u **MaxCount** in op 0 of minder.

### <a name="use-the-powershell-pipeline"></a>De PowerShell-pijplijn gebruiken

Batch-cmdlets gebruiken de PowerShell-pijplijn om gegevens tussen cmdlets te verzenden. Dit heeft hetzelfde effect als het opgeven van een parameter, maar het vergemakkelijkt het werken met meerdere entiteiten.

Zo kunt u bijvoorbeeld alle taken onder uw account zoeken en weergeven:

```powershell
Get-AzBatchJob -BatchContext $context | Get-AzBatchTask -BatchContext $context
```

Start elk computerknooppunt in een groep opnieuw op:

```powershell
Get-AzBatchComputeNode -PoolId "myPool" -BatchContext $context | Restart-AzBatchComputeNode -BatchContext $context
```

## <a name="application-package-management"></a>Beheer van toepassingspakketten

[Toepassings pakketten](batch-application-packages.md) bieden een vereenvoudigde manier om toepassingen te implementeren op de reken knooppunten in uw Pools. Met de Batch PowerShell-cmdlets kunt u toepassingspakketten in uw Batch-account uploaden en beheren, en pakketversies implementeren op rekenknooppunten.

> [!IMPORTANT]
> U moet een Azure Storage-account koppelen aan het Batch-account om toepassingspakketten te kunnen gebruiken.

Een toepassing **maken** :

```powershell
New-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"
```

Een toepassings pakket **toevoegen** :

```powershell
New-AzBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0" -Format zip -FilePath package001.zip
```

Stel de **standaardversie** voor de toepassing in:

```powershell
Set-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -DefaultVersion "1.0"
```

**Geeft een overzicht van** de pakketten van een toepassing

```powershell
$application = Get-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

$application.ApplicationPackages
```

Een toepassingspakket **verwijderen**

```powershell
Remove-AzBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0"
```

Een toepassing **verwijderen**

```powershell
Remove-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"
```

> [!NOTE]
> Voordat u de toepassing verwijdert, moet u alle versies van de toepassingspakketten van een toepassing verwijderen. U ontvangt een 'conflictfout' als u een toepassing probeert te verwijderen die nog toepassingspakketten heeft.

### <a name="deploy-an-application-package"></a>Een toepassingspakket implementeren

U kunt een of meer toepassingspakketten voor implementatie opgeven wanneer u een groep maakt. Wanneer u een pakket opgeeft tijdens het maken van een groep, wordt dit pakket geïmplementeerd op elk knooppunt dat wordt gekoppeld aan de groep. Pakketten worden ook geïmplementeerd als een knooppunt opnieuw wordt gestart of teruggezet.

Geef de optie voor `-ApplicationPackageReference` op als u een groep maakt, zodat een toepassingspakket wordt geïmplementeerd naar de knooppunten van de groep wanneer deze lid worden van de groep. Maak eerst een **PSApplicationPackageReference**-object en configureer dit met de toepassings-id en pakketversie die u wilt implementeren op de rekenknooppunten van de groep:

```powershell
$appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

$appPackageReference.ApplicationId = "MyBatchApplication"

$appPackageReference.Version = "1.0"
```

Nu maakt u de adresgroep en geeft u het pakketverwijzingsobject op als het argument voor de optie `ApplicationPackageReferences`:

```powershell
New-AzBatchPool -Id "PoolWithAppPackage" -VirtualMachineSize "Small" -VirtualMachineConfiguration $configuration -BatchContext $context -ApplicationPackageReferences $appPackageReference
```

Zie [Deploy applications to compute nodes with Batch application packages](batch-application-packages.md) (Toepassingen implementeren naar rekenknooppunten met Batch-toepassingspakketten) voor meer informatie over toepassingspakketten.

### <a name="update-a-pools-application-packages"></a>De toepassingspakketten van een groep bijwerken

Als u de toepassingen wilt bijwerken die zijn toegewezen aan een bestaande groep, maakt u eerst een PSApplicationPackageReference-object met de gewenste eigenschappen (toepassings-id en pakketversie):

```powershell
$appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

$appPackageReference.ApplicationId = "MyBatchApplication"

$appPackageReference.Version = "2.0"

```

Vervolgens haalt u de groep op uit batch, wist u eventuele bestaande pakketten, voegt u de nieuwe pakket verwijzing toe en werkt u de batch-service bij met de nieuwe groeps instellingen:

```powershell
$pool = Get-AzBatchPool -BatchContext $context -Id "PoolWithAppPackage"

$pool.ApplicationPackageReferences.Clear()

$pool.ApplicationPackageReferences.Add($appPackageReference)

Set-AzBatchPool -BatchContext $context -Pool $pool
```

Nu hebt u de eigenschappen van de adresgroep bijgewerkt in de Batch-service. Voor de werkelijke implementatie van het nieuwe toepassingspakket op rekenknooppunten in de adresgroep moet u deze knooppunten echter opnieuw starten of de installatiekopie ervan terugzetten. U kunt elk knooppunt in een adresgroep met de volgende opdracht opnieuw starten:

```powershell
Get-AzBatchComputeNode -PoolId "PoolWithAppPackage" -BatchContext $context | Restart-AzBatchComputeNode -BatchContext $context
```

> [!TIP]
> U kunt meerdere toepassingspakketten op de rekenknooppunten in de adresgroep implementeren. Als u een toepassingspakket wilt toevoegen in plaats van de huidige geïmplementeerde pakketten te vervangen, laat dan de `$pool.ApplicationPackageReferences.Clear()`-regel hierboven weg.

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg de [Azure batch cmdlet-Naslag informatie](/powershell/module/az.batch) voor gedetailleerde cmdlet-syntaxis en voor beelden.
- Meer informatie over het [implementeren van toepassingen voor reken knooppunten met batch-toepassings pakketten](batch-application-packages.md).
