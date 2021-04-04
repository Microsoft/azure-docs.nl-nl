---
title: Azure Analysis Services verplaatsen naar een andere regio | Microsoft Docs
description: Hierin wordt beschreven hoe u een Azure Analysis Services resource verplaatst naar een andere regio.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: how-to
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: 049ff6d14c3967481eb73037814082fa261154e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96497925"
---
# <a name="move-analysis-services-to-a-different-region"></a>Analysis Services verplaatsen naar een andere regio

In dit artikel wordt beschreven hoe u een Analysis Services server-resource naar een andere Azure-regio kunt verplaatsen. U kunt uw server om een aantal redenen verplaatsen naar een andere regio, bijvoorbeeld om te profiteren van een Azure-regio dichter bij gebruikers, om alleen service plannen te gebruiken die worden ondersteund in specifieke regio's, of om te voldoen aan de interne beleids-en beheer vereisten. 

In deze en gekoppelde gekoppelde artikelen leert u het volgende:

> [!div class="checklist"]
> 
> * Back-up maken van een bron server model-Data Base naar [Blob Storage](../storage/blobs/storage-blobs-introduction.md).
> * Een [resource sjabloon](../azure-resource-manager/templates/overview.md)voor een bron server exporteren.
> * Een opslag- [Shared Access Signature (SAS)](../storage/common/storage-sas-overview.md)ophalen.
> * Wijzig de resource sjabloon.
> * Implementeer de sjabloon om een nieuwe doel server te maken.
> * Herstel een model database op de nieuwe doel server.
> * Controleer de nieuwe doel server en data base.
> * Verwijder de bron server.

In dit artikel wordt beschreven hoe u een resource sjabloon gebruikt voor het migreren van een enkele Analysis Services server met een **basis configuratie** naar een andere regio *en* resource groep in hetzelfde abonnement. Als u een sjabloon gebruikt, blijven geconfigureerde server eigenschappen behouden, zodat de doel server is geconfigureerd met dezelfde eigenschappen, behalve regio en resource groep als de bron server. In dit artikel wordt het verplaatsen van gekoppelde resources die deel uitmaken van dezelfde resource groep, zoals gegevens bron-, opslag-en gateway resources, niet beschreven. 

Voordat u een server naar een andere regio verplaatst, is het raadzaam om een gedetailleerd plan te maken. Overweeg aanvullende bronnen zoals gateways en opslag die ook moeten worden verplaatst. Bij elk abonnement is het belang rijk dat u een of meer test bewerkingen uitvoert met test servers voordat u een productie server verplaatst.

> [!IMPORTANT]
> Client toepassingen en verbindings reeksen maken verbinding met Analysis Services met behulp van de volledige server naam, een URI die de regio bevat waarin de server zich bevindt. Bijvoorbeeld `asazure://westcentralus.asazure.windows.net/advworks01`. Wanneer u een server naar een andere regio verplaatst, maakt u effectief een nieuwe server bron in een andere regio. deze heeft een andere regio dan de server naam-URI. Client toepassingen en verbindings reeksen die in scripts worden gebruikt, moeten verbinding maken met de nieuwe server met behulp van de nieuwe server naam-URI. Het gebruik van een [Server naam alias](analysis-services-server-alias.md) kan het aantal locaties beperken dat de server naam-URI moet worden gewijzigd, maar moet worden geïmplementeerd voordat een regio wordt verplaatst.

> [!IMPORTANT]
> Azure-regio's gebruiken verschillende IP-adresbereiken. Als er Firewall uitzonderingen zijn geconfigureerd voor de regio waarin uw server en/of opslag account zich bevindt, kan het nodig zijn om een ander IP-adres bereik te configureren. Zie [Veelgestelde vragen over Analysis Services-netwerk verbinding](analysis-services-network-faq.md)voor meer informatie.

> [!NOTE]
> In dit artikel wordt beschreven hoe u een back-up van een Data Base naar een doel server terugzet vanuit een opslag container in de regio van de bron server. In sommige gevallen kan het herstellen van back-ups vanuit een andere regio slechte prestaties hebben, met name voor grote data bases. Voor de beste prestaties tijdens het herstellen van de Data Base kunt u een nieuwe opslag container in de doel server regio migreren of maken. Kopieer de ABF-back-upbestanden van de bron regio opslag container naar de doel regio opslag container voordat u de Data Base naar de doel server herstelt. In het kader van dit artikel, in sommige gevallen, met name bij zeer grote data bases, wordt het uitvoeren van een Data Base op de doel server, het opnieuw maken en vervolgens verwerken van database gegevens, mogelijk rendabeler dan met back-up/herstel.

> [!NOTE]
> Als u een on-premises gegevens gateway gebruikt om verbinding te maken met gegevens bronnen, moet u de gateway bron ook verplaatsen naar de doel server regio. Zie [een on-premises gegevens gateway installeren en configureren](analysis-services-gateway-install.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

- **Azure-opslag account**: vereist voor het opslaan van een. ABF-back-upbestand.
- **SQL Server Management Studio (SSMS)**: vereist voor het maken van back-ups en het herstellen van model databases.
- **Azure PowerShell**. Alleen vereist als u ervoor kiest om deze taak te volt ooien met behulp van Power shell.

## <a name="prepare"></a>Voorbereiden

### <a name="backup-model-databases"></a>Back-upmodel databases

Als er nog geen **opslag instellingen** voor de bron server zijn geconfigureerd, volgt u de stappen in [opslag instellingen configureren](analysis-services-backup.md#configure-storage-settings).

Wanneer opslag instellingen zijn geconfigureerd, volgt u de stappen in [back-up](analysis-services-backup.md#backup) om een model database te maken. ABF-back-up in uw opslag container. U kunt later de ABF-back-up naar uw nieuwe doel server herstellen.


### <a name="export-template"></a>Sjabloon exporteren

De sjabloon bevat configuratie-eigenschappen van de bron server.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Een sjabloon exporteren via de Azure-portal:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Selecteer **alle resources** en selecteer vervolgens uw Analysis Services-server.

3. Selecteer > **instellingen**  >  **sjabloon exporteren**.

4. Kies **downloaden** in de Blade **sjabloon exporteren** .

5. Zoek het zip-bestand dat u hebt gedownload van de portal en pak dat bestand vervolgens uit naar een map.

   Het zip-bestand bevat de. json-bestanden waaruit de sjabloon en de vereiste para meters bestaan voor het implementeren van een nieuwe server.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Een sjabloon exporteren met behulp van Power shell:

1. Meld u aan bij uw Azure-abonnement met de opdracht [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) en volg de instructies op het scherm:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Als uw identiteit is gekoppeld aan meer dan één abonnement, stelt u uw actieve abonnement in op het abonnement van de server bron die u wilt verplaatsen.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. Exporteer de sjabloon van de bron server. Met deze opdrachten wordt een JSON-sjabloon met de ResourceGroupName als bestands naam opgeslagen in de huidige map.

   ```azurepowershell-interactive
   $resource = Get-AzResource `
     -ResourceGroupName <resource-group-name> `
     -ResourceName <server-account-name> `
     -ResourceType Microsoft.AnalysisServices/servers
   Export-AzResourceGroup `
     -ResourceGroupName <resource-group-name> `
     -Resource $resource.ResourceId
   ```
---

### <a name="get-storage-shared-access-signature-sas"></a>Shared Access Signature (SAS) voor opslag ophalen

Bij het implementeren van een doel server op basis van een sjabloon is een SAS-token voor gebruikers overdracht (als URI) vereist om de opslag container op te geven die de database back-up bevat.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Een Shared Access Signature ophalen met behulp van de portal:

1. Selecteer in de portal het opslag account dat wordt gebruikt om een back-up te maken van uw server database.

2. Selecteer **Storage Explorer** en vouw vervolgens **BLOB-containers** uit. 

3. Klik met de rechter muisknop op uw opslag container en selecteer vervolgens **Shared Access Signature ophalen**.

    :::image type="content" source="media/move-between-regions/get-sas.png" alt-text="SAS ophalen":::

4. Selecteer in **Shared Access Signature** **maken**. Standaard verloopt de SAS over 24 uur.

5. Kopieer de **URI** en sla deze op. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u een gedeelde toegangs handtekening wilt ophalen met behulp van Power shell, volgt u de stappen in [een gebruikers delegering Sa's maken voor een container of BLOB met Power shell](../storage/blobs/storage-blob-user-delegation-sas-create-powershell.md#create-a-user-delegation-sas-for-a-blob).

---

### <a name="modify-the-template"></a>De sjabloon aanpassen

Gebruik een tekst editor om de template.jste wijzigen van het bestand dat u hebt geëxporteerd, het wijzigen van de eigenschappen van de regio en de BLOB-container. 

De sjabloon wijzigen:

1. In een tekst editor, in de eigenschap **Location** , geeft u de nieuwe doel regio op. Plak in de eigenschap **backupBlobContainerUri** de opslag container-URI met SAS-sleutel. 

    In het volgende voor beeld wordt de doel regio voor Server advworks1 ingesteld op `South Central US` en wordt de opslag container-URI met de hand tekening voor gedeelde toegang opgegeven: 

    ```json
    "resources": [
        {
            "type": "Microsoft.AnalysisServices/servers",
            "apiVersion": "2017-08-01",
            "name": "[parameters('servers_advworks1_name')]",
            "location": "South Central US",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "capacity": 1
            },
            "properties": {
                "asAdministrators": {
                    "members": [
                        "asadmins@adventure-works.com"
                    ]
                },
                "backupBlobContainerUri": "https://storagenorthcentralus.blob.core.windows.net/backup?sp=rl&st=2020-06-01T19:30:42Z&se=2020-06-02T19:30:42Z&sv=2019-10-10&sr=c&sig=PCQ4s9RujJkxu89gO4tiDTbE3%2BFECx6zAdcv8x0cVUQ%3D",
                "querypoolConnectionMode": "All"
            }
        }
    ]         
    ```
2. Sla de sjabloon op.

#### <a name="regions"></a>Regio's

Zie [Azure-locaties](https://azure.microsoft.com/global-infrastructure/locations/)voor meer informatie over Azure-regio's. Als u regio's wilt ophalen met behulp van Power shell, voert u de opdracht [Get-AzLocation](/powershell/module/az.resources/get-azlocation) uit.

```azurepowershell-interactive
   Get-AzLocation | format-table 
```

## <a name="move"></a>Verplaatsen

Als u een nieuwe server bron in een andere regio wilt implementeren, gebruikt u de **template.jsvoor** het bestand dat u in de vorige secties hebt geëxporteerd en gewijzigd.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecteer in de portal **een resource maken**.

2. Typ in **de Marketplace zoeken de** **sjabloon implementatie** en druk vervolgens op **Enter**.

3. Selecteer **Sjabloonimlementatie**.

4. Selecteer **Maken**.

5. Selecteer **Bouw uw eigen sjabloon in de editor**.

6. Selecteer **bestand laden** en volg de instructies voor het laden van de **template.jsvoor** het bestand dat u hebt geëxporteerd en gewijzigd.

7. Controleer of in de sjabloon editor de juiste eigenschappen voor de nieuwe doel server worden weer gegeven.

8. Selecteer **Opslaan**.

9. Voer de eigenschaps waarden in of Selecteer deze:

    - **Abonnement**: selecteer het Azure-abonnement.
    
    - **Resource groep**: Selecteer **nieuwe maken** en voer vervolgens de naam van een resource groep in. U kunt een bestaande resource groep selecteren, die er nog geen Analysis Services server met dezelfde naam bevat.
    
    - **Locatie**: Selecteer de regio die u in de sjabloon hebt opgegeven.

10. Selecteer **controleren en maken**.

11. Bekijk de voor waarden en basis informatie en selecteer vervolgens **maken**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Gebruik de volgende opdrachten om uw sjabloon te implementeren:

```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. South Central US)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<local template file>"  
   ```
---

### <a name="get-target-server-uri"></a>URI van doel server ophalen

Als u verbinding wilt maken met de nieuwe doel server van SSMS om de model database te herstellen, moet u de nieuwe URI voor de doel server ophalen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

De URI van de server in de portal ophalen:

1. Ga in de portal naar de nieuwe doel server bron.

2. Kopieer op de pagina **overzicht** de URL van de **Server naam** .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u de URI van de server wilt ophalen met behulp van Power shell, gebruikt u de volgende opdrachten:

```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $serverName = Read-Host -Prompt "Enter the server name (i.e. advworks2)"

   Get-AzAnalysisServicesServer -ResourceGroupName $resourceGroupName -Name "$serverName"
```
Kopieer **ServerFullName** uit de uitvoer.

---

### <a name="restore-model-database"></a>Model database herstellen

Volg de stappen die worden beschreven in [herstellen](analysis-services-backup.md#restore) om de model database te herstellen. ABF-back-up naar de nieuwe doel server.

Optioneel: nadat u de model database hebt hersteld, verwerkt u het model en de tabellen om gegevens uit gegevens bronnen te vernieuwen. Het model en de tabel verwerken met behulp van SSMS:

1. Klik in SSMS met de rechter muisknop op de model database > **process-data base**.

2. Vouw **tabellen** uit, klik met de rechter muisknop op een tabel. Selecteer in **proces tabel (len)** alle tabellen en selecteer vervolgens **OK**.

## <a name="verify"></a>Verifiëren

1. Ga in de portal naar de nieuwe doel server.

2. Controleer op de pagina overzicht, in **modellen op Analysis Services server**, de herstelde modellen worden weer gegeven.

3. Gebruik een client toepassing als Power BI of Excel om verbinding te maken met het model op de nieuwe server. Controleer of model objecten zoals tabellen, metingen, hiërarchieën worden weer gegeven. 

4. Voer alle automatiserings scripts uit. Controleer of deze zijn uitgevoerd.

Optioneel: [ALM Toolkit](http://alm-toolkit.com/) is een *open source* -hulp programma voor het vergelijken en beheren van Power BI gegevens sets *en* Analysis Services tabellaire model databases. Gebruik de Toolkit om verbinding te maken met zowel de bron-als de doel server database en vergelijk. Als uw database migratie is voltooid, worden model objecten dezelfde definitie. 

:::image type="content" source="media/move-between-regions/alm-toolkit.png" alt-text="ALM Toolkit":::

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u hebt gecontroleerd of de client toepassingen verbinding kunnen maken met de nieuwe server en alle automatiserings scripts correct worden uitgevoerd, verwijdert u de bron server. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

De bron server verwijderen uit de portal:

Selecteer **verwijderen** op de pagina **overzicht** van de bron server.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u de bron server wilt verwijderen met behulp van Power shell, gebruikt u de opdracht Remove-AzAnalysisServicesServer.

```azurepowershell-interactive
Remove-AzAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

---

> [!NOTE]
> Na het volt ooien van een regio, wordt het aanbevolen om de nieuwe doel server een opslag container in dezelfde regio voor back-ups te gebruiken in plaats van de opslag container in de regio van de bron server.