---
title: Privé-eindpunten
description: Meer informatie over het proces van het maken van privé-eind punten voor Azure Backup en de scenario's waarbij persoonlijke eind punten worden gebruikt om de beveiliging van uw resources te hand haven.
ms.topic: conceptual
ms.date: 05/07/2020
ms.openlocfilehash: 0d9d77c139896f9067f73943dbb213fc655f00f6
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054869"
---
# <a name="private-endpoints-for-azure-backup"></a>Privé-eind punten voor Azure Backup

Met Azure Backup kunt u veilig back-ups van uw gegevens maken en uw Recovery Services kluizen herstellen met behulp van [privé-eind punten](../private-link/private-endpoint-overview.md). Privé-eind punten gebruiken een of meer privé-IP-adressen van uw VNet, waardoor de service effectief in uw VNet wordt gezet.

In dit artikel vindt u informatie over het proces van het maken van privé-eind punten voor Azure Backup en de scenario's waarbij persoonlijke eind punten worden gebruikt om de beveiliging van uw resources te hand haven.

## <a name="before-you-start"></a>Voordat u begint

- Privé-eind punten kunnen alleen worden gemaakt voor nieuwe Recovery Services kluizen (waarvoor geen items zijn geregistreerd bij de kluis). Daarom moeten persoonlijke eind punten worden gemaakt voordat u items op de kluis probeert te beveiligen.
- Eén virtueel netwerk kan persoonlijke eind punten voor meerdere Recovery Services kluizen bevatten. Daarnaast kan een Recovery Services kluis persoonlijke eind punten in meerdere virtuele netwerken hebben. Het maximum aantal privé-eind punten dat voor een kluis kan worden gemaakt, is echter 12.
- Zodra een persoonlijk eind punt is gemaakt voor een kluis, wordt de kluis vergrendeld. Het is niet toegankelijk (voor back-ups en herstel bewerkingen) van netwerken van een locatie die een persoonlijk eind punt voor de kluis bevatten. Als alle persoonlijke eind punten voor de kluis worden verwijderd, is de kluis toegankelijk vanuit alle netwerken.
- Een particuliere endpoint-verbinding voor back-up gebruikt een totaal van 11 privé Ip's in uw subnet, inclusief de IP-adressen die worden gebruikt door Azure Backup voor opslag. Dit aantal kan hoger zijn (Maxi maal 25) voor bepaalde Azure-regio's. Daarom raden we aan dat u voldoende privé Ip's hebt die beschikbaar zijn wanneer u persoonlijke eind punten voor back-ups probeert te maken.
- Hoewel een Recovery Services kluis wordt gebruikt door (beide) Azure Backup en Azure Site Recovery, wordt in dit artikel alleen het gebruik van privé-eind punten voor Azure Backup besproken.
- Azure Active Directory biedt momenteel geen ondersteuning voor persoonlijke eind punten. IP-adressen en FQDN-namen die vereist zijn voor de Azure Active Directory om in een regio te werken, moeten dus uitgaande toegang hebben tot het beveiligde netwerk wanneer ze back-ups maken van data bases in azure-Vm's en-back-ups met behulp van de MARS-agent. U kunt ook NSG Tags en Azure Firewall Tags gebruiken om toegang te verlenen tot Azure AD, zoals van toepassing.
- Virtuele netwerken met netwerk beleidsregels worden niet ondersteund voor privé-eind punten. U moet netwerk beleid uitschakelen voordat u doorgaat.
- U moet de Recovery Services resource provider bij het abonnement opnieuw registreren als u deze vóór 1 2020 mei hebt geregistreerd. Als u de provider opnieuw wilt registreren, gaat u naar uw abonnement in het Azure Portal, navigeert u naar **resource provider** in de linkernavigatiebalk en selecteert u vervolgens **micro soft. Recovery Services** en selecteert u **opnieuw registreren**.
- Het [terugzetten van meerdere regio's](backup-create-rs-vault.md#set-cross-region-restore) voor SQL-en SAP Hana database back-ups wordt niet ondersteund als de kluis een persoonlijk eind punt heeft ingeschakeld.

## <a name="recommended-and-supported-scenarios"></a>Aanbevolen en ondersteunde scenario's

Hoewel persoonlijke eind punten zijn ingeschakeld voor de kluis, worden ze gebruikt voor het maken van back-ups en het herstellen van SQL-en SAP HANA-workloads in een Azure-VM en alleen back-ups van de MARS-agent. U kunt ook de kluis gebruiken voor het maken van back-ups van andere werk belastingen (er zijn echter geen persoonlijke eind punten nodig). Naast het maken van een back-up van SQL-en SAP HANA-workloads en-back-ups met behulp van de MARS-agent worden persoonlijke eind punten ook gebruikt voor het herstellen van bestanden voor Azure VM-back-ups. Zie de volgende tabel voor meer informatie:

| Back-ups van werk belastingen in azure VM (SQL, SAP HANA), back-up maken met MARS agent | Het gebruik van privé-eind punten wordt aanbevolen voor het toestaan van back-ups en herstel, zonder dat er Ip's/FQDN-namen voor Azure Backup of Azure Storage van uw virtuele netwerken hoeven te worden allowlist. Zorg er in dat scenario voor dat Vm's die SQL-data bases hosten, Azure AD Ip's of FQDN-adressen kunnen bereiken. |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Back-up van Azure VM**                                         | Voor VM-back-ups is geen toegang tot Ip's of FQDN-adressen toegestaan. Daarom zijn geen persoonlijke eind punten vereist voor het maken van back-ups en het herstellen van schijven.  <br><br>   Bestands herstel vanuit een kluis met persoonlijke eind punten zou echter worden beperkt tot virtuele netwerken die een persoonlijk eind punt voor de kluis bevatten. <br><br>    Wanneer u ACL'ed unmanaged disks gebruikt, moet u ervoor zorgen dat het opslag account met de schijven toegang krijgt tot **vertrouwde micro soft-Services** als het ACL'ed is. |
| **Azure Files back-up**                                      | Azure Files back-ups worden opgeslagen in het lokale opslag account. Daarom zijn geen persoonlijke eind punten vereist voor back-up en herstel. |

## <a name="creating-and-using-private-endpoints-for-backup"></a>Privé-eind punten maken en gebruiken voor back-up

In deze sectie vindt u informatie over de stappen voor het maken en gebruiken van privé-eind punten voor Azure Backup binnen uw virtuele netwerken.

>[!IMPORTANT]
> Het wordt ten zeerste aangeraden om de stappen uit te voeren in dezelfde volg orde als vermeld in dit document. Als u dit niet doet, kan dit ertoe leiden dat de kluis niet compatibel is met het gebruik van privé-eind punten en dat u het proces opnieuw moet starten met een nieuwe kluis.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

Zie [deze sectie](#create-a-recovery-services-vault-using-the-azure-resource-manager-client) voor meer informatie over het maken van een kluis met behulp van de Azure Resource Manager-client. Hiermee maakt u een kluis met de beheerde identiteit al ingeschakeld. Meer informatie over Recovery Services kluizen [vindt u hier](./backup-azure-recovery-services-vault-overview.md).

## <a name="enable-managed-identity-for-your-vault"></a>Beheerde identiteit voor uw kluis inschakelen

Met beheerde identiteiten kan de kluis privé-eind punten maken en gebruiken. In deze sectie vindt u informatie over het inschakelen van de beheerde identiteit voor uw kluis.

1. Ga naar uw Recovery Services kluis-> **identiteit**.

    ![Identiteits status wijzigen in aan](./media/private-endpoints/identity-status-on.png)

1. Wijzig de **status** in **op aan** en selecteer **Opslaan**.

1. Er wordt een **object-id** gegenereerd. Dit is de beheerde identiteit van de kluis.

    >[!NOTE]
    >Wanneer de beheerde identiteit is ingeschakeld, mag deze **niet** worden uitgeschakeld (zelfs tijdelijk). Het uitschakelen van de beheerde identiteit kan leiden tot inconsistent gedrag.

## <a name="grant-permissions-to-the-vault-to-create-required-private-endpoints"></a>Machtigingen verlenen aan de kluis om vereiste persoonlijke eind punten te maken

Als u de vereiste privé-eind punten voor Azure Backup wilt maken, moet de kluis (de beheerde identiteit van de kluis) machtigingen hebben voor de volgende resource groepen:

- De resource groep die het doel-VNet bevat
- De resource groep waar de persoonlijke eind punten moeten worden gemaakt
- De resource groep die de Privé-DNS zones bevat, zoals [hier](#creating-private-endpoints-for-backup) wordt beschreven

We raden u aan de rol **Inzender** voor deze drie resource groepen toe te kennen aan de kluis (beheerde identiteit). In de volgende stappen wordt beschreven hoe u dit doet voor een bepaalde resource groep (dit moet worden gedaan voor elk van de drie resource groepen):

1. Ga naar de resource groep en navigeer naar **Access Control (IAM)** op de balk aan de linkerkant.
1. Ga in **Access Control** naar **een roltoewijzing toevoegen**.

    ![Een roltoewijzing toevoegen](./media/private-endpoints/add-role-assignment.png)

1. Kies in het deel venster **roltoewijzing toevoegen** de optie **Inzender** als **rol** en gebruik de **naam** van de kluis als de **Principal**. Selecteer uw kluis en selecteer **Opslaan** wanneer u klaar bent.

    ![Rol en Principal kiezen](./media/private-endpoints/choose-role-and-principal.png)

Zie voor het beheren van machtigingen op een meer gedetailleerd niveau [rollen en machtigingen hand matig maken](#create-roles-and-permissions-manually).

## <a name="creating-and-approving-private-endpoints-for-azure-backup"></a>Privé-eind punten voor Azure Backup maken en goed keuren

### <a name="creating-private-endpoints-for-backup"></a>Privé-eind punten voor back-up maken

In deze sectie wordt het proces voor het maken van een persoonlijk eind punt voor uw kluis beschreven.

1. Zoek in de zoek balk naar en selecteer **privé-koppeling**. Hiermee gaat u naar het **persoonlijke koppelings centrum**.

    ![Zoeken naar persoonlijke koppeling](./media/private-endpoints/search-for-private-link.png)

1. Selecteer **privé-eind punten** op de navigatie balk aan de linkerkant. Selecteer in het deel venster **privé-eind punten** **+ toevoegen** om te beginnen met het maken van een persoonlijk eind punt voor uw kluis.

    ![Persoonlijk eind punt toevoegen in persoonlijk koppelings centrum](./media/private-endpoints/add-private-endpoint.png)

1. Eenmaal in het proces voor het maken van een **persoonlijk eind punt** , moet u details opgeven voor het maken van uw verbinding met een privé-eind punt.

    1. **Basis beginselen**: Vul de basis gegevens in voor uw privé-eind punten. De regio moet hetzelfde zijn als de kluis en de resource.

        ![Basis details invullen](./media/private-endpoints/basic-details.png)

    1. **Resource**: op dit tabblad moet u de Paas-resource vermelden waarvoor u de verbinding wilt maken. Selecteer **micro soft. Recovery Services/kluizen** van het resource type voor uw gewenste abonnement. Als u klaar bent, kiest u de naam van uw Recovery Services kluis als de **resource** en **AzureBackup** als de **subresource** van het doel.

        ![Het tabblad resource invullen](./media/private-endpoints/resource-tab.png)

    1. **Configuratie**: Geef in configuratie het virtuele netwerk en het subnet op waar u het persoonlijke eind punt wilt maken. Dit is het Vnet waar de virtuele machine zich bevindt. U kunt ervoor kiezen om **uw persoonlijke eind punt te integreren** met een privé-DNS-zone. U kunt ook uw aangepaste DNS-server gebruiken of een privé-DNS-zone maken.

        ![Tabblad Configuratie invullen](./media/private-endpoints/configuration-tab.png)

        Raadpleeg [deze sectie](#dns-changes-for-custom-dns-servers) als u uw aangepaste DNS-servers wilt gebruiken in plaats van de integratie met Azure privé-DNS-zones.  

    1. U kunt eventueel **labels** voor uw persoonlijke eind punt toevoegen.

    1. Ga door met het invoeren van gegevens en het eenmaal **maken** is voltooid. Wanneer de validatie is voltooid, selecteert u **maken** om het persoonlijke eind punt te maken.

## <a name="approving-private-endpoints"></a>Persoonlijke eind punten goed keuren

Als de gebruiker die het persoonlijke eind punt maakt ook de eigenaar is van de Recovery Services kluis, wordt het persoonlijke eind punt dat hierboven is gemaakt, automatisch goedgekeurd. Anders moet de eigenaar van de kluis het persoonlijke eind punt goed keuren voordat het kan worden gebruikt. In deze sectie wordt de hand matige goed keuring van privé-eind punten in de Azure Portal beschreven.

Zie [hand matige goed keuring van privé-eind punten met behulp van de Azure Resource Manager-client](#manual-approval-of-private-endpoints-using-the-azure-resource-manager-client) om de Azure Resource Manager-client te gebruiken voor het goed keuren van persoonlijke eind punten.

1. Navigeer in uw Recovery Services-kluis naar **privé-eindpunt verbindingen** op de balk aan de linkerkant.
1. Selecteer de verbinding van het particuliere eind punt dat u wilt goed keuren.
1. Selecteer **goed keuren** in de bovenste balk. U kunt ook **weigeren** of **verwijderen** selecteren als u de eindpunt verbinding wilt afwijzen of verwijderen.

    ![Privé-eind punten goed keuren](./media/private-endpoints/approve-private-endpoints.png)

## <a name="using-private-endpoints-for-backup"></a>Privé-eind punten gebruiken voor back-up

Zodra de privé-eind punten die zijn gemaakt voor de kluis in uw VNet zijn goedgekeurd, kunt u deze gebruiken om uw back-ups te maken en op te slaan.

>[!IMPORTANT]
>Zorg ervoor dat u alle hierboven vermelde stappen in het document hebt voltooid voordat u verdergaat. Voor samen vatting moet u de stappen in de volgende controle lijst hebben voltooid:
>
>1. Er is een (nieuwe) Recovery Services kluis gemaakt
>1. De kluis is ingeschakeld om een door het systeem toegewezen beheerde identiteit te gebruiken
>1. Relevante machtigingen toegewezen aan de beheerde identiteit van de kluis
>1. Een persoonlijk eind punt voor uw kluis gemaakt
>1. Het persoonlijke eind punt is goedgekeurd (indien niet automatisch goedgekeurd)

### <a name="backup-and-restore-of-workloads-in-azure-vm-sql-sap-hana"></a>Back-ups maken en herstellen van werk belastingen in azure VM (SQL, SAP HANA)

Zodra het persoonlijke eind punt is gemaakt en goedgekeurd, zijn er geen aanvullende wijzigingen vereist aan de client zijde om het persoonlijke eind punt te gebruiken. Alle communicatie-en gegevens overdracht van uw beveiligde netwerk naar de kluis wordt uitgevoerd via het persoonlijke eind punt.
Als u echter persoonlijke eind punten voor de kluis verwijdert nadat er een server (SQL/SAP HANA) is geregistreerd, moet u de container opnieuw registreren bij de kluis. U hoeft de beveiliging niet te stoppen.

### <a name="backup-and-restore-through-mars-agent"></a>Back-ups maken en herstellen via MARS-agent

Wanneer u de MARS-agent gebruikt om een back-up van uw on-premises resources te maken, moet u ervoor zorgen dat uw on-premises netwerk (met uw resources waarvan u een back-up wilt maken) is gekoppeld aan het Azure-VNet dat een persoonlijk eind punt voor de kluis bevat, zodat u het kunt gebruiken. U kunt vervolgens door gaan met de installatie van de MARS-agent en de back-up configureren, zoals hier wordt beschreven. U moet er echter voor zorgen dat alle communicatie voor back-ups alleen via het peered netwerk plaatsvindt.

Als u echter persoonlijke eind punten voor de kluis verwijdert nadat er een MARS-agent is geregistreerd, moet u de container opnieuw registreren bij de kluis. U hoeft de beveiliging niet te stoppen.

## <a name="additional-topics"></a>Extra onderwerpen

### <a name="create-a-recovery-services-vault-using-the-azure-resource-manager-client"></a>Een Recovery Services kluis maken met behulp van de Azure Resource Manager-client

U kunt de Recovery Services-kluis maken en de beheerde identiteit inschakelen (waardoor de beheerde identiteit is vereist, zoals we later zien) met behulp van de Azure Resource Manager-client. Hieronder vindt u een voor beeld van het volgende:

```rest
armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>?api-version=2017-07-01-preview @C:\<filepath>\MSIVault.json
```

Het bovenstaande JSON-bestand moet de volgende inhoud hebben:

JSON aanvragen:

```json
{
  "location": "eastus2",
  "name": "<vaultname>",
  "etag": "W/\"datetime'2019-05-24T12%3A54%3A42.1757237Z'\"",
  "tags": {
    "PutKey": "PutValue"
  },
  "properties": {},
  "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
  "type": "Microsoft.RecoveryServices/Vaults",
  "sku": {
    "name": "RS0",
    "tier": "Standard"
  },
  "identity": {
    "type": "systemassigned"
  }
}
```

Reactie JSON:

```json
{
   "location": "eastus2",
   "name": "<vaultname>",
   "etag": "W/\"datetime'2020-02-25T05%3A26%3A58.5181122Z'\"",
   "tags": {
     "PutKey": "PutValue"
   },
   "identity": {
     "tenantId": "<tenantid>",
     "principalId": "<principalid>",
     "type": "SystemAssigned"
   },
   "properties": {
     "provisioningState": "Succeeded",
     "privateEndpointStateForBackup": "None",
     "privateEndpointStateForSiteRecovery": "None"
   },
   "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
   "type": "Microsoft.RecoveryServices/Vaults",
   "sku": {
     "name": "RS0",
     "tier": "Standard"
   }
 }
```

>[!NOTE]
>De kluis die in dit voor beeld wordt gemaakt via de Azure Resource Manager-client, is al gemaakt met een door het systeem toegewezen beheerde identiteit.

### <a name="managing-permissions-on-resource-groups"></a>Machtigingen voor resource groepen beheren

De beheerde identiteit voor de kluis moet de volgende machtigingen hebben in de resource groep en het virtuele netwerk waar de persoonlijke eind punten worden gemaakt:

- `Microsoft.Network/privateEndpoints/*` Dit is vereist voor het uitvoeren van ruw op privé-eind punten in de resource groep. Deze moet worden toegewezen aan de resource groep.
- `Microsoft.Network/virtualNetworks/subnets/join/action` Dit is vereist in het virtuele netwerk waar het privé-IP-adres aan het persoonlijke eind punt wordt gekoppeld.
- `Microsoft.Network/networkInterfaces/read` Dit is vereist voor de resource groep om de netwerk interface op te halen die voor het persoonlijke eind punt is gemaakt.
- Privé-DNS rol zone bijdrager deze rol bestaat al en kan worden gebruikt om machtigingen op te geven `Microsoft.Network/privateDnsZones/A/*` `Microsoft.Network/privateDnsZones/virtualNetworkLinks/read` .

U kunt een van de volgende methoden gebruiken om rollen met de vereiste machtigingen te maken:

#### <a name="create-roles-and-permissions-manually"></a>Rollen en machtigingen hand matig maken

Maak de volgende JSON-bestanden en gebruik de Power shell-opdracht aan het einde van de sectie om rollen te maken:

PrivateEndpointContributorRoleDef.jsop

```json
{
  "Name": "PrivateEndpointContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows management of Private Endpoint",
  "Actions": [
    "Microsoft.Network/privateEndpoints/*",
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

NetworkInterfaceReaderRoleDef.jsop

```json
{
  "Name": "NetworkInterfaceReader",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows read on networkInterfaces",
  "Actions": [
    "Microsoft.Network/networkInterfaces/read"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

PrivateEndpointSubnetContributorRoleDef.jsop

```json
{
  "Name": "PrivateEndpointSubnetContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows adding of Private Endpoint connection to Virtual Networks",
  "Actions": [
    "Microsoft.Network/virtualNetworks/subnets/join/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

```azurepowershell
 New-AzRoleDefinition -InputFile "PrivateEndpointContributorRoleDef.json"
 New-AzRoleDefinition -InputFile "NetworkInterfaceReaderRoleDef.json"
 New-AzRoleDefinition -InputFile "PrivateEndpointSubnetContributorRoleDef.json"
```

#### <a name="use-a-script"></a>Een script gebruiken

1. Start de **Cloud shell** in het Azure Portal en selecteer **bestand uploaden** in het Power shell-venster.

    ![Bestand uploaden selecteren in Power shell-venster](./media/private-endpoints/upload-file-in-powershell.png)

1. Upload het volgende script: [VaultMsiPrereqScript](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/VaultMsiPrereqScript.ps1)

1. Ga naar uw basismap (bijvoorbeeld: `cd /home/user` )

1. Voer het volgende script uit:

    ```azurepowershell
    ./VaultMsiPrereqScript.ps1 -subscription <subscription-Id> -vaultPEResourceGroup <vaultPERG> -vaultPESubnetResourceGroup <subnetRG> -vaultMsiName <msiName>
    ```

    Dit zijn de para meters:

    - **abonnement**: * * SubscriptionId met de resource groep waar het persoonlijke eind punt voor de kluis moet worden gemaakt en het subnet waar het persoonlijke eind punt van de kluis wordt gekoppeld

    - **vaultPEResourceGroup**: resource groep waar het persoonlijke eind punt voor de kluis wordt gemaakt

    - **vaultPESubnetResourceGroup**: de resource groep van het subnet waaraan het persoonlijke eind punt wordt gekoppeld

    - **vaultMsiName**: naam van het MSI-bestand van de kluis. Dit is hetzelfde als de **kluisnaam**

1. Voltooi de verificatie en het script zal de context van het opgegeven abonnement nemen. De juiste rollen worden gemaakt als deze ontbreken in de Tenant en rollen worden toegewezen aan het MSI-bestand van de kluis.

### <a name="creating-private-endpoints-using-azure-powershell"></a>Privé-eind punten maken met behulp van Azure PowerShell

#### <a name="auto-approved-private-endpoints"></a>Automatisch goedgekeurde privé-eind punten

```azurepowershell
$vault = Get-AzRecoveryServicesVault `
        -ResourceGroupName $vaultResourceGroupName `
        -Name $vaultName
  
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
        -Name $privateEndpointConnectionName `
        -PrivateLinkServiceId $vault.ID `
        -GroupId "AzureBackup"  
  
$privateEndpoint = New-AzPrivateEndpoint `
        -ResourceGroupName $vmResourceGroupName `
        -Name $privateEndpointName `
        -Location $location `
        -Subnet $subnet `
        -PrivateLinkServiceConnection $privateEndpointConnection `
        -Force
```

### <a name="manual-approval-of-private-endpoints-using-the-azure-resource-manager-client"></a>Hand matige goed keuring van privé-eind punten met behulp van de Azure Resource Manager-client

1. Gebruik **GetVault** om de particuliere endpoint-verbindings-id voor uw privé-eind punt op te halen.

    ```rest
    armclient GET /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/vaults/<vaultname>?api-version=2017-07-01-preview
    ```

    Hiermee wordt de verbindings-ID van het particuliere eind punt geretourneerd. De naam van de verbinding kan als volgt worden opgehaald met het eerste deel van de verbindings-ID:

    `privateendpointconnectionid = {peName}.{vaultId}.backup.{guid}`

1. Haal de **verbindings-id van het particuliere eind punt** (en de naam van het **persoonlijke eind** punt, waar nodig) van het antwoord op en vervang deze in de volgende JSON en Azure Resource Manager URI en probeer de status te wijzigen in goedgekeurd/afgewezen/losgekoppeld, zoals wordt weer gegeven in het voor beeld hieronder:

    ```rest
    armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>?api-version=2020-02-02-preview @C:\<filepath>\BackupAdminApproval.json
    ```

    JSON

    ```json
    {
    "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>",
    "properties": {
        "privateEndpoint": {
        "id": "/subscriptions/<subscriptionid>/resourceGroups/<pergname>/providers/Microsoft.Network/privateEndpoints/pename"
        },
        "privateLinkServiceConnectionState": {
        "status": "Disconnected",  //choose state from Approved/Rejected/Disconnected
        "description": "Disconnected by <userid>"
        }
    }
    }
    ```

### <a name="dns-changes-for-custom-dns-servers"></a>DNS-wijzigingen voor aangepaste DNS-servers

#### <a name="create-dns-zones-for-custom-dns-servers"></a>DNS-zones maken voor aangepaste DNS-servers

U moet drie particuliere DNS-zones maken en deze koppelen aan uw virtuele netwerk. Als de open bare Url's van de back-upservice, in tegens telling tot Blob en Queue, worden niet geregistreerd in open bare Azure DNS voor de omleiding naar de DNS-zones van de persoonlijke koppeling. 

| **Zone**                                                     | **Service** |
| ------------------------------------------------------------ | ----------- |
| `privatelink.<geo>.backup.windowsazure.com`      | Backup      |
| `privatelink.blob.core.windows.net`                            | Blob        |
| `privatelink.queue.core.windows.net`                           | Wachtrij       |

>[!NOTE]
>In de bovenstaande tekst verwijst *geo* naar de regio code. Bijvoorbeeld *wcus* en *ne* voor West-Centraal VS en Europa-Noord respectievelijk.

Raadpleeg [deze lijst](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx) voor regio codes. Raadpleeg de volgende koppelingen voor URL-naamgevings conventies in nationale regio's:

- [China](/azure/china/resources-developer-guide#check-endpoints-in-azure)
- [Duitsland](../germany/germany-developer-guide.md#endpoint-mapping)
- [US Gov](../azure-government/documentation-government-developer-guide.md)

#### <a name="adding-dns-records-for-custom-dns-servers"></a>DNS-records toevoegen voor aangepaste DNS-servers

Hiervoor moet u vermeldingen voor elke FQDN in uw privé-eind punt in uw Privé-DNS zone maken.

U moet erop gewezen dat de persoonlijke eind punten die zijn gemaakt voor back-up, Blob en Queue-service, worden gebruikt.

- Het persoonlijke eind punt voor de kluis gebruikt de naam die is opgegeven tijdens het maken van het persoonlijke eind punt
- De persoonlijke eind punten voor Blob-en Queue-services worden voorafgegaan door de naam van hetzelfde voor de kluis.

In de volgende afbeelding ziet u bijvoorbeeld de drie persoonlijke eind punten die zijn gemaakt voor een privé-eind punt verbinding met de naam *pee2epe*:

![Drie privé-eind punten voor een verbinding met een privé-eind punt](./media/private-endpoints/three-private-endpoints.png)

DNS-zone voor de back-upservice ( `privatelink.<geo>.backup.windowsazure.com` ):

1. Navigeer naar uw persoonlijke eind punt voor back-up in het **persoonlijke koppelings centrum**. Op de overzichts pagina vindt u een lijst met de FQDN-en privé Ip's voor uw persoonlijke eind punt.

1. Voeg één vermelding voor elke FQDN en een persoonlijk IP-adres toe als een type record.

    ![Vermelding voor elke FQDN en privé-IP toevoegen](./media/private-endpoints/add-entry-for-each-fqdn-and-ip.png)

DNS-zone voor de Blob service ( `privatelink.blob.core.windows.net` ):

1. Navigeer naar uw persoonlijke eind punt voor Blob in het **persoonlijke koppelings centrum**. Op de overzichts pagina vindt u een lijst met de FQDN-en privé Ip's voor uw persoonlijke eind punt.

1. Voeg een vermelding voor de FQDN-naam en het persoonlijke IP-adres toe als een type record.

    ![Voeg een vermelding voor de FQDN-naam en het persoonlijke IP-adres toe als een type record voor de Blob service](./media/private-endpoints/add-type-a-record-for-blob.png)

DNS-zone voor de Queue-service ( `privatelink.queue.core.windows.net` ):

1. Navigeer naar het persoonlijke eind punt voor de wachtrij in het **persoonlijke koppelings centrum**. Op de overzichts pagina vindt u een lijst met de FQDN-en privé Ip's voor uw persoonlijke eind punt.

1. Voeg een vermelding voor de FQDN-naam en het persoonlijke IP-adres toe als een type record.

    ![Voeg een vermelding voor de FQDN-naam en het persoonlijke IP-adres toe als een type record voor de Queue-service](./media/private-endpoints/add-type-a-record-for-queue.png)

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

V. Kan ik een persoonlijk eind punt maken voor een bestaande back-upkluis?<br>
A. Nee, privé-eind punten kunnen alleen worden gemaakt voor nieuwe back-upkluizen. De kluis mag dus nooit items bevatten die erop zijn beveiligd. Het is in feite geen pogingen om items te beveiligen voor de kluis voordat persoonlijke eind punten worden gemaakt.

V. Ik heb geprobeerd een item te beveiligen voor mijn kluis, maar dit is mislukt en de kluis bevat nog steeds geen items die ermee zijn beveiligd. Kan ik privé-eind punten voor deze kluis maken?<br>
A. Nee, de kluis mag geen pogingen hebben gehad om in het verleden items te beveiligen.

V. Ik heb een kluis die gebruikmaakt van privé-eind punten voor back-up en herstel. Kan ik later privé-eind punten toevoegen of verwijderen voor deze kluis, zelfs als er back-upitems worden beveiligd?<br>
A. Ja. Als u al persoonlijke eind punten voor een kluis en beveiligde back-upitems hebt gemaakt, kunt u later persoonlijke eind punten toevoegen of verwijderen als dat nodig is.

V. Kan het privé-eind punt voor Azure Backup ook worden gebruikt voor Azure Site Recovery?<br>
A. Nee, het privé-eind punt voor back-up kan alleen worden gebruikt voor Azure Backup. U moet een nieuw privé-eind punt voor Azure Site Recovery maken als dit wordt ondersteund door de service.

V. Ik heb een van de stappen in dit artikel gemist en is ingeschakeld om mijn gegevens bron te beveiligen. Kan ik nog steeds persoonlijke eind punten gebruiken?<br>
A. Het is niet mogelijk om de stappen in het artikel te volgen en door te gaan met het beveiligen van items kan ertoe leiden dat de kluis geen privé-eind punten kan gebruiken. Daarom wordt u aangeraden deze controle lijst te raadplegen voordat u doorgaat met het beveiligen van items.

V. Kan ik mijn eigen DNS-server gebruiken in plaats van de privé-DNS-zone van Azure of een geïntegreerde privé-DNS-zone?<br>
A. Ja, u kunt uw eigen DNS-servers gebruiken. Zorg er echter voor dat alle vereiste DNS-records worden toegevoegd, zoals wordt voorgesteld in deze sectie.

V. Moet ik aanvullende stappen uitvoeren op mijn server nadat ik het proces heb gevolgd in dit artikel?<br>
A. Na het uitvoeren van het proces dat in dit artikel wordt beschreven, hoeft u geen extra werk te doen om persoonlijke eind punten te gebruiken voor back-up en herstel.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over alle [beveiligings functies in azure backup](security-overview.md)
