---
title: Verplaatsen Azure Analysis Services naar een andere regio | Microsoft Docs
description: Beschrijft hoe u een resource Azure Analysis Services verplaatsen naar een andere regio.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: how-to
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions , devx-track-azurepowershell
ms.openlocfilehash: 2b698ffaddb4bc818eaabda34022ab58ff05fe5f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786347"
---
# <a name="move-analysis-services-to-a-different-region"></a>Verplaatsen Analysis Services naar een andere regio

In dit artikel wordt beschreven hoe u een Analysis Services serverresource naar een andere Azure-regio verplaatst. U kunt uw server om verschillende redenen verplaatsen naar een andere regio, bijvoorbeeld om te profiteren van een Azure-regio dichter bij gebruikers, om serviceplannen te gebruiken die alleen in specifieke regio's worden ondersteund, of om te voldoen aan de vereisten voor intern beleid en governance. 

In deze en gekoppelde artikelen leert u het volgende:

> [!div class="checklist"]
> 
> * Een back-up maken van een bronservermodeldatabase naar [Blob Storage](../storage/blobs/storage-blobs-introduction.md).
> * Een bronserverresourcesjabloon [exporteren.](../azure-resource-manager/templates/overview.md)
> * Haal een Shared [Access Signature (SAS) voor opslag op.](../storage/common/storage-sas-overview.md)
> * Wijzig de resourcesjabloon.
> * Implementeer de sjabloon om een nieuwe doelserver te maken.
> * Een modeldatabase herstellen naar de nieuwe doelserver.
> * Controleer de nieuwe doelserver en database.
> * Verwijder de bronserver.

In dit artikel wordt beschreven hoe u een resourcesjabloon gebruikt om één Analysis Services-server met een basisconfiguratie te migreren naar een andere regio en *resourcegroep* in hetzelfde abonnement.  Het gebruik van een sjabloon behoudt geconfigureerde servereigenschappen om ervoor te zorgen dat de doelserver is geconfigureerd met dezelfde eigenschappen, met uitzondering van regio en resourcegroep, als de bronserver. In dit artikel wordt niet beschreven hoe u gekoppelde resources verplaatst die deel kunnen uitmaken van dezelfde resourcegroep, zoals gegevensbron-, opslag- en gatewayresources. 

Voordat u een server naar een andere regio verplaatst, is het raadzaam om een gedetailleerd plan te maken. Overweeg aanvullende resources, zoals gateways en opslag, die mogelijk ook moeten worden verplaatst. Bij elk plan is het belangrijk om een of meer proefbewerkingen met testservers te voltooien voordat u een productieserver verplaatst.

> [!IMPORTANT]
> Clienttoepassingen en verbindingsreeksen maken verbinding met Analysis Services met behulp van de volledige servernaam. Dit is een URI die de regio bevat waarin de server zich in zich. Bijvoorbeeld `asazure://westcentralus.asazure.windows.net/advworks01`. Wanneer u een server naar een andere regio verplaatst, maakt u effectief een nieuwe serverresource in een andere regio, die een andere regio heeft in de servernaam URI. Clienttoepassingen en verbindingsreeksen die in scripts worden gebruikt, moeten verbinding maken met de nieuwe server met behulp van de nieuwe servernaam URI. Het gebruik [van een servernaamalias](analysis-services-server-alias.md) kan het aantal plaatsen beperken waar de servernaam URI moet worden gewijzigd, maar moet worden geïmplementeerd voordat een regio wordt verplaatst.

> [!IMPORTANT]
> Azure-regio's gebruiken verschillende IP-adresbereiken. Als er firewall-uitzonderingen zijn geconfigureerd voor de regio waarin uw server en/of opslagaccount zich bevinden, kan het nodig zijn om een ander IP-adresbereik te configureren. Zie Veelgestelde vragen over het gebruik van Analysis Services [netwerkconnectiviteit voor meer informatie.](analysis-services-network-faq.md)

> [!NOTE]
> In dit artikel wordt beschreven hoe u een databaseback-up herstelt naar een doelserver vanuit een opslagcontainer in de regio van de bronserver. In sommige gevallen kunnen het herstellen van back-ups vanuit een andere regio slechte prestaties hebben, met name voor grote databases. Migreert of maakt een nieuwe opslagcontainer in de doelserverregio voor de beste prestaties tijdens het herstellen van de database. Kopieer de .abf-back-upbestanden van de opslagcontainer voor de bronregio naar de opslagcontainer in de doelregio voordat u de database naar de doelserver herstelt. Hoewel dit niet binnen het bereik van dit artikel valt, kan het in sommige gevallen, met name bij zeer grote databases, rendabeler zijn om een database uit uw bronserver te scripten, opnieuw te maken en vervolgens te verwerken op de doelserver om databasegegevens te laden dan back-up/herstel te gebruiken.

> [!NOTE]
> Als u een on-premises gegevensgateway gebruikt om verbinding te maken met gegevensbronnen, moet u de gatewayresource ook verplaatsen naar de doelserverregio. Zie Een on-premises gegevensgateway installeren en [configureren voor meer informatie.](analysis-services-gateway-install.md)

## <a name="prerequisites"></a>Vereisten

- **Azure-opslagaccount:** vereist voor het opslaan van een .abf-back-upbestand.
- **SQL Server Management Studio (SSMS)**: vereist voor het maken van back-ups en het herstellen van modeldatabases.
- **Azure PowerShell**. Alleen vereist als u ervoor kiest om deze taak te voltooien met behulp van PowerShell.

## <a name="prepare"></a>Voorbereiden

### <a name="backup-model-databases"></a>Back-upmodeldatabases

Als **opslaginstellingen nog** niet zijn geconfigureerd voor de bronserver, volgt u de stappen in [Opslaginstellingen configureren.](analysis-services-backup.md#configure-storage-settings)

Wanneer de opslaginstellingen zijn geconfigureerd, volgt u de stappen in [Back-up om](analysis-services-backup.md#backup) een ABF-back-up van een modeldatabase te maken in uw opslagcontainer. Later herstelt u de .abf-back-up naar de nieuwe doelserver.


### <a name="export-template"></a>Sjabloon exporteren

De sjabloon bevat configuratie-eigenschappen van de bronserver.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Een sjabloon exporteren via de Azure-portal:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Selecteer **Alle resources** en selecteer vervolgens uw Analysis Services server.

3. Selecteer > **Instellingen Sjabloon**  >  **exporteren.**

4. Kies **Downloaden** op de blade **Sjabloon** exporteren.

5. Zoek het ZIP-bestand dat u hebt gedownload uit de portal en open het bestand in een map.

   Het zip-bestand bevat de JSON-bestanden die de sjabloon en parameters bevatten die nodig zijn om een nieuwe server te implementeren.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Een sjabloon exporteren met behulp van PowerShell:

1. Meld u aan bij uw Azure-abonnement met de [opdracht Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) en volg de instructies op het scherm:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Als uw identiteit is gekoppeld aan meer dan één abonnement, stelt u uw actieve abonnement in op het abonnement van de serverresource die u wilt verplaatsen.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. Exporteert u de sjabloon van de bronserver. Met deze opdrachten wordt een json-sjabloon met de ResourceGroupName als bestandsnaam opgeslagen in uw huidige map.

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

### <a name="get-storage-shared-access-signature-sas"></a>Shared Access Signature (SAS) voor opslag op halen

Bij het implementeren van een doelserver vanuit een sjabloon is een SAS-token voor gebruikersdelegatie (als een URI) vereist om de opslagcontainer op te geven die de back-up van de database bevat.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Een shared access signature op te halen via de portal:

1. Selecteer in de portal het opslagaccount dat wordt gebruikt om een back-up te maken van uw serverdatabase.

2. Selecteer **Storage Explorer** en vouw **blobcontainers uit.** 

3. Klik met de rechtermuisknop op uw opslagcontainer en selecteer **vervolgens Shared Access Signature**.

    :::image type="content" source="media/move-between-regions/get-sas.png" alt-text="SAS ophalen":::

4. Selecteer **Shared Access Signature** in **het dialoogvenster Maken.** Standaard verloopt de SAS binnen 24 uur.

5. Kopieer de **URI** en sla deze op. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Volg de stappen in Create a user delegation SAS for a container or blob with PowerShell (Een SAS voor gebruikersdelegatie maken voor een container of blob met PowerShell) om een handtekening voor gedeelde toegang op te [halen met behulp van PowerShell.](../storage/blobs/storage-blob-user-delegation-sas-create-powershell.md#create-a-user-delegation-sas-for-a-blob)

---

### <a name="modify-the-template"></a>De sjabloon aanpassen

Gebruik een teksteditor om de gegevens te template.jsbestand dat u hebt geëxporteerd, en wijzig de eigenschappen van de regio- en blobcontainer. 

De sjabloon wijzigen:

1. Geef in een teksteditor in **de eigenschap location** de nieuwe doelregio op. Plak in **de eigenschap backupBlobContainerUri** de opslagcontainer-URI met sas-sleutel. 

    In het volgende voorbeeld wordt de doelregio voor server advworks1 ingesteld op en wordt de `South Central US` opslagcontainer-URI met shared access signature opgegeven: 

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

Zie Azure-locaties om [Azure-regio's op te halen.](https://azure.microsoft.com/global-infrastructure/locations/) Voer de opdracht [Get-AzLocation](/powershell/module/az.resources/get-azlocation) uit om regio's op te halen met behulp van PowerShell.

```azurepowershell-interactive
   Get-AzLocation | format-table 
```

## <a name="move"></a>Verplaatsen

Als u een nieuwe serverresource in een  andere regio wilt implementeren, gebruikt u detemplate.jsin het bestand dat u in de vorige secties hebt geëxporteerd en gewijzigd.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecteer een resource maken **in de portal.**

2. In **Marketplace doorzoeken** typt u **sjabloonimplementatie** en drukt u op **ENTER.**

3. Selecteer **Sjabloonimlementatie**.

4. Selecteer **Maken**.

5. Selecteer **Bouw uw eigen sjabloon in de editor**.

6. Selecteer **Bestand laden** en volg de instructies voor het laden van detemplate.js **in** het bestand dat u hebt geëxporteerd en gewijzigd.

7. Controleer of in de sjablooneditor de juiste eigenschappen voor de nieuwe doelserver worden weergeven.

8. Selecteer **Opslaan**.

9. Voer de eigenschapswaarden in of selecteer deze:

    - **Abonnement**: selecteer het Azure-abonnement.
    
    - **Resourcegroep:** selecteer **Nieuwe maken** en voer vervolgens de naam van een resourcegroep in. U kunt een bestaande resourcegroep selecteren op voorwaarde dat deze nog geen Analysis Services server met dezelfde naam bevat.
    
    - **Locatie:** selecteer dezelfde regio die u hebt opgegeven in de sjabloon.

10. Selecteer **Controleren en maken.**

11. Lees de voorwaarden en Basisbeginselen en selecteer vervolgens **Maken.**

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Gebruik deze opdrachten om uw sjabloon te implementeren:

```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. South Central US)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<local template file>"  
   ```
---

### <a name="get-target-server-uri"></a>Doelserver-URI op halen

Als u vanuit SSMS verbinding wilt maken met de nieuwe doelserver om de modeldatabase te herstellen, moet u de nieuwe doelserver-URI krijgen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Ga als volgende te werk om de server-URI op te halen in de portal:

1. Ga in de portal naar de nieuwe doelserverresource.

2. Kopieer op **de** pagina Overzicht de **servernaam** URI.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Gebruik de volgende opdrachten om de server-URI op te halen met behulp van PowerShell:

```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $serverName = Read-Host -Prompt "Enter the server name (i.e. advworks2)"

   Get-AzAnalysisServicesServer -ResourceGroupName $resourceGroupName -Name "$serverName"
```
Kopieer **ServerFullName** uit de uitvoer.

---

### <a name="restore-model-database"></a>Modeldatabase herstellen

Volg de stappen die worden beschreven in [Herstellen](analysis-services-backup.md#restore) om de .abf-back-up van de modeldatabase te herstellen naar de nieuwe doelserver.

Optioneel: Na het herstellen van de modeldatabase verwerkt u het model en de tabellen om gegevens uit gegevensbronnen te vernieuwen. Het model en de tabel verwerken met behulp van SSMS:

1. Klik in SSMS met de rechtermuisknop op de modeldatabase > **Procesdatabase**.

2. Vouw **Tabellen uit** en klik met de rechtermuisknop op een tabel. Selecteer **in Procestabel(s)** alle tabellen en selecteer vervolgens **OK.**

## <a name="verify"></a>Verifiëren

1. Ga in de portal naar de nieuwe doelserver.

2. Controleer op de pagina Overzicht in **Modellen op Analysis Services-server** of herstelde modellen worden weergegeven.

3. Gebruik een clienttoepassing zoals Power BI of Excel om verbinding te maken met het model op de nieuwe server. Controleer of modelobjecten zoals tabellen, metingen en hiërarchieën worden weergegeven. 

4. Voer automatiseringsscripts uit. Controleer of ze zijn uitgevoerd.

Optioneel: [ALM Toolkit](http://alm-toolkit.com/) is een *open source* voor het vergelijken en beheren Power BI gegevenssets  en databases Analysis Services tabellair model. Gebruik de toolkit om verbinding te maken met de bron- en doelserverdatabases en om deze te vergelijken. Als de databasemigratie is geslaagd, krijgen modelobjecten dezelfde definitie. 

:::image type="content" source="media/move-between-regions/alm-toolkit.png" alt-text="ALM Toolkit":::

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u hebt gecontroleerd of clienttoepassingen verbinding kunnen maken met de nieuwe server en alle automatiseringsscripts correct worden uitgevoerd, verwijdert u de bronserver. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

De bronserver verwijderen uit de portal:

Selecteer verwijderen op de pagina **Overzicht van** de **bronserver.**

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u de bronserver wilt verwijderen met behulp van PowerShell, gebruikt u de Remove-AzAnalysisServicesServer opdracht.

```azurepowershell-interactive
Remove-AzAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

---

> [!NOTE]
> Nadat een regio is verplaatst, is het raadzaam dat uw nieuwe doelserver een opslagcontainer in dezelfde regio gebruikt voor back-ups, in plaats van de opslagcontainer in de bronserverregio.
