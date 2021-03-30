---
title: Persoonlijke gegevens verwijderen en exporteren uit Azure DevTest Labs
description: Meer informatie over het verwijderen en exporteren van persoonlijke gegevens uit de Azure DevLast Labs-service ter ondersteuning van uw verplichtingen onder de algemene verordening gegevensbescherming (AVG).
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2c44b2f3aa6f2dfad18ed53804842a5dad8bd94a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85483513"
---
# <a name="export-or-delete-personal-data-from-azure-devtest-labs"></a>Persoonlijke gegevens exporteren of verwijderen uit Azure DevTest Labs
In dit artikel worden de stappen beschreven voor het verwijderen en exporteren van persoonlijke gegevens uit de Azure DevTest Labs-service. 

## <a name="what-personal-data-does-devtest-labs-collect"></a>Welke persoonlijke gegevens worden DevTest Labs verzameld?
DevTest Labs verzamelt twee belang rijke persoonlijke gegevens van de gebruiker. Ze zijn: e-mail adres van gebruiker en gebruikers object-ID. Deze informatie is essentieel voor de service om in service functies te bieden aan Lab-beheerders en Lab-gebruikers.

### <a name="user-email-address"></a>E-mail adres van gebruiker
DevTest Labs gebruikt het e-mail adres van de gebruiker voor het verzenden van e-mail meldingen voor automatisch afsluiten aan Lab-gebruikers. Het e-mail bericht meldt gebruikers van hun computer af te sluiten. De gebruikers kunnen de afsluiting uitstellen of overs Laan als ze dat willen. U configureert het e-mail adres op het niveau van het lab of op het niveau van de virtuele machine.

**E-mail instellen op het Lab:**

![E-mail instellen op het niveau van het lab](./media/personal-data-delete-export/lab-user-email.png)

**E-mail instellen op de virtuele machine:**

![E-mail instellen op VM-niveau](./media/personal-data-delete-export/vm-user-email.png)

### <a name="user-object-id"></a>Gebruikers object-ID
DevTest Labs maakt gebruik van de gebruikers object-ID voor het weer geven van trends in de maand en kosten per resource-informatie voor Lab-beheerders. Hiermee kunnen ze kosten bijhouden en drempels voor hun Lab beheren. 

**Trend van de geschatte kosten voor de huidige kalender maand:** 
 ![ Trend van de geschatte kosten voor de huidige kalender maand](./media/personal-data-delete-export/estimated-cost-trend-per-month.png)

**Geschatte kosten van de maand tot heden per resource:** 
 ![ Geschatte kosten van maand tot heden per resource](./media/personal-data-delete-export/estimated-month-to-date-cost-by-resource.png)


## <a name="why-do-we-need-this-personal-data"></a>Waarom hebben we deze persoonlijke gegevens nodig?
De DevTest Labs-service gebruikt de persoonlijke gegevens voor operationele doel einden. Deze gegevens zijn essentieel voor de service om belang rijke functies te leveren. Als u een Bewaar beleid instelt voor het e-mail adres van de gebruiker, ontvangen Lab-gebruikers niet tijd om e-mail meldingen automatisch op te lossen nadat het e-mail adres van ons systeem is verwijderd. Op dezelfde manier kan de test beheerder de trends van de maand en de kosten per resource voor machines in hun Labs niet weer geven als de gebruikers object-Id's worden verwijderd op basis van een Bewaar beleid. Daarom moeten deze gegevens worden bewaard zolang de resource van de gebruiker in het lab actief is.

## <a name="how-can-i-have-the-system-to-forget-my-personal-data"></a>Hoe kan ik mijn persoonlijke gegevens verg eten met het systeem?
Als test gebruiker als u deze persoons gegevens wilt verwijderen, kunt u dit doen door de bijbehorende resource in het lab te verwijderen. De DevTest Labs-service anoniem gemaakt de verwijderde persoons gegevens 30 dagen nadat deze is verwijderd door de gebruiker.

Als u bijvoorbeeld uw virtuele machine verwijdert of als u uw e-mail adres hebt verwijderd, wordt deze gegevens 30 dagen nadat de resource is verwijderd door de DevTest Labs-service anoniem gemaakt. Het beleid voor de Bewaar periode van 30 dagen na het verwijderen is om ervoor te zorgen dat we een nauw keurige schatting van de maandelijkse kosten bieden aan de test beheerder.

## <a name="how-can-i-request-an-export-on-my-personal-data"></a>Hoe kan ik een export aanvraag indienen voor mijn persoonlijke gegevens?
U kunt persoonlijke en Lab-gebruiks gegevens exporteren met behulp van de Azure Portal of Power shell. De gegevens worden geëxporteerd als twee verschillende CSV-bestanden:

- **disks.csv** : bevat informatie over de schijven die worden gebruikt door de verschillende vm's
- **virtualmachines.csv** : bevat informatie over de virtuele machines in het lab.

### <a name="azure-portal"></a>Azure Portal
Als test gebruiker kunt u een export aanvragen voor de persoons gegevens die door de DevTest Labs-service worden opgeslagen. Als u een aanvraag voor een export wilt aanvragen, gaat u naar de optie **persoonlijke gegevens** op de pagina **overzicht** van uw test omgeving. Selecteer de knop voor het **exporteren van aanvragen** start het maken van een downloadbaar Excel-bestand in het opslag account van uw test omgeving. Vervolgens kunt u contact opnemen met de test beheerder om deze gegevens te bekijken.

1. Selecteer **persoonlijke gegevens** in het menu links. 

    ![Pagina met persoonlijke gegevens](./media/personal-data-delete-export/personal-data-page.png)
2. Selecteer de **resource groep** die het Lab bevat.

    ![Resourcegroep selecteren](./media/personal-data-delete-export/select-resource-group.png)
3. Selecteer het **opslag account** in de resource groep.
4. Selecteer op de pagina **opslag account** de optie **blobs**.

    ![Tegel blobs selecteren](./media/personal-data-delete-export/select-blobs-tile.png)
5. Selecteer de container met de naam **labresourceusage** in de lijst met containers.

    ![BLOB-container selecteren](./media/personal-data-delete-export/select-blob-container.png)
6. Selecteer de **map** met de naam na uw Lab. U vindt **CSV** -bestanden voor **schijven** en **virtuele machines** in uw Lab in deze map. U kunt deze CSV-bestanden downloaden, de inhoud filteren voor de test gebruiker die toegang vraagt, en deze delen met hen.

    ![CSV-bestand downloaden](./media/personal-data-delete-export/download-csv-file.png)

### <a name="azure-powershell"></a>Azure PowerShell

```powershell
Param (
    [Parameter (Mandatory=$true, HelpMessage="The storage account name where to store usage data")]
    [string] $storageAccountName,

    [Parameter (Mandatory=$true, HelpMessage="The storage account key")]
    [string] $storageKey,

    [Parameter (Mandatory=$true, HelpMessage="The DevTest Lab name to get usage data from")]
    [string] $labName,

    [Parameter (Mandatory=$true, HelpMessage="The DevTest Lab subscription")]
    [string] $labSubscription
    )

#Login
Login-AzureRmAccount

# Set the subscription for the lab
Get-AzureRmSubscription -SubscriptionId $labSubscription  | Select-AzureRmSubscription

# DTL will create this container in the storage when invoking the action, cannot be changed currently
$containerName = "labresourceusage"

# Get the storage context
$Ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageKey 
$SasToken = New-AzureStorageAccountSASToken -Service Blob, File -ResourceType Container, Service, Object -Permission rwdlacup -Protocol HttpsOnly -Context $Ctx

# Generate the storage blob uri
$blobUri = $Ctx.BlobEndPoint + $SasToken

# blobStorageAbsoluteSasUri and usageStartDate are required
$actionParameters = @{
    'blobStorageAbsoluteSasUri' = $blobUri    
}

$startdate = (Get-Date).AddDays(-7)

$actionParameters.Add('usageStartDate', $startdate.Date.ToString())

# Get the lab resource group
$resourceGroupName = (Find-AzureRmResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $labName}).ResourceGroupName
    
# Create the lab resource id
$resourceId = "/subscriptions/" + $labSubscription + "/resourceGroups/" + $resourceGroupName + "/providers/Microsoft.DevTestLab/labs/" + $labName + "/"

# !!!!!!! this is the new resource action to get the usage data.
$result = Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
 
# Finish up cleanly
if ($result.Status -eq "Succeeded") {
    Write-Output "Telemetry successfully downloaded for " $labName
    return 0
}
else
{
    Write-Output "Failed to download lab: " + $labName
    Write-Error $result.toString()
    return -1
}
```

De belangrijkste onderdelen in het bovenstaande voor beeld zijn:

- De Invoke-AzureRmResourceAction opdracht.
   
    ```
    Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
    ```
- Twee actie parameters
    - **blobStorageAbsoluteSasUri** : de URI van het opslag account met het Shared Access Signature SAS-token. In het Power shell-script kan deze waarde worden door gegeven in plaats van de opslag sleutel.
    - **usageStartDate** : de begin datum voor het ophalen van gegevens, waarbij de eind datum de huidige datum is waarop de actie wordt uitgevoerd. De granulatie is op het niveau van de dag, dus zelfs als u tijd gegevens toevoegt, wordt deze genegeerd.

### <a name="exported-data---a-closer-look"></a>Geëxporteerde gegevens-een dichter blik
Laten we nu eens kijken naar de geëxporteerde gegevens. Zoals eerder is vermeld, worden er twee CSV-bestanden weer gegeven wanneer de gegevens zijn geëxporteerd. 

De **virtualmachines.csv** bevat de volgende gegevens kolommen:

| Kolomnaam | Beschrijving |
| ----------- | ----------- | 
| SubscriptionId | De abonnements-id waarin het lab zich bevindt. |
| LabUId | De unieke GUID-id voor het lab. |
| LabName | De naam van het lab. |
| LabResourceId | Volledig gekwalificeerde Resource-ID voor Lab. |
| ResourceGroupName | De naam van de resource groep die de virtuele machine bevat | 
| ResourceId | De volledig gekwalificeerde Resource-ID voor de virtuele machine. |
| ResourceUId | GUID voor de virtuele machine |
| Name | Naam van de virtuele machine. |
| CreatedTime | De datum/tijd waarop de virtuele machine is gemaakt. |
| DeletedDate | De datum/tijd waarop de virtuele machine is verwijderd. Als het leeg is, is het verwijderen nog niet uitgevoerd. |
| ResourceOwner | Eigenaar van de virtuele machine. Als de waarde leeg is, is het een claim bare VM of is deze gemaakt door een service-principal. |
| PricingTier | Prijs categorie van de virtuele machine |
| ResourceStatus | Beschikbaarheids status van de virtuele machine. Actief, als deze nog bestaat of niet actief is, als de virtuele machine is verwijderd. |
| ComputeResourceId | Fully Qualified Compute resource-id van de virtuele machine. |
| Claim bare | Stel deze waarde in op True als de virtuele machine een claim bare VM is | 
| EnvironmentId | De resource-id van de omgeving waarin de virtuele machine is gemaakt. Het is leeg wanneer de virtuele machine niet is gemaakt als onderdeel van een omgevings resource. |
| ExpirationDate | De verval datum voor de virtuele machine. De instelling is leeg als er geen verval datum is ingesteld.
| GalleryImageReferenceVersion |  Versie van de basis installatie kopie van de virtuele machine. |
| GalleryImageReferenceOffer | Aanbieding van de basis installatie kopie van de virtuele machine. |
| GalleryImageReferencePublisher | Uitgever van de basis installatie kopie van de virtuele machine. |
| GalleryImageReferenceSku | SKU van de basis installatie kopie van de virtuele machine |
| GalleryImageReferenceOsType | Type besturings systeem van de basis installatie kopie van de virtuele machine |
| CustomImageId | Volledig gekwalificeerde ID van de aangepaste VM-basis afbeelding. |

De gegevens kolommen in **disks.csv** worden hieronder weer gegeven:

| Kolomnaam | Beschrijving | 
| ----------- | ----------- | 
| SubscriptionId | ID van het abonnement dat het Lab bevat |
| LabUId | GUID voor het lab |
| LabName | Naam van het lab | 
| LabResourceId | Volledig gekwalificeerde Resource-ID voor het lab | 
| ResourceGroupName | Naam van de resource groep die het Lab bevat | 
| ResourceId | De volledig gekwalificeerde Resource-ID voor de virtuele machine. |
| ResourceUId | GUID voor de virtuele machine |
 |Name | De naam van de gekoppelde schijf |
| CreatedTime |De datum en tijd waarop de gegevens schijf is gemaakt. |
| DeletedDate | De datum en tijd waarop de gegevens schijf is verwijderd. |
| ResourceStatus | De status van de resource. Actief, als de resource bestaat. Inactief na verwijdering. |
| DiskBlobName | De naam van de BLOB voor de gegevens schijf. |
| DiskSizeGB | De grootte van de gegevens schijf. |
| DiskType | Type van de gegevens schijf. 0 voor Standard, 1 voor Premium. |
| LeasedByVmId | De resource-ID van de virtuele machine waaraan de gegevens schijf is gekoppeld. |


> [!NOTE]
> Als u met meerdere Labs werkt en algemene informatie wilt krijgen, zijn de twee belang rijke kolommen de **LabUID** en de **ResourceUId**. Dit zijn de unieke id's voor abonnementen.

De geëxporteerde gegevens kunnen worden gemanipuleerd en gevisualiseerd met behulp van hulpprogram ma's, zoals SQL Server, Power BI, enzovoort. Deze functie is vooral nuttig als u het gebruik van uw Lab wilt rapporteren aan uw beheer team dat niet hetzelfde Azure-abonnement gebruikt als u.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen: 

- [Beleids regels instellen voor een Lab](devtest-lab-set-lab-policy.md)
- [Veelgestelde vragen](devtest-lab-faq.md)
