---
title: Azure-activiteitenlogboek
description: Bekijk het Azure-activiteiten logboek en verzend het naar Azure Monitor-logboeken, Azure Event Hubs en Azure Storage.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 06/12/2020
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 38f5743e8a80af1ec824b07833f66ad50d67b91f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101723296"
---
# <a name="azure-activity-log"></a>Azure-activiteitenlogboek
Het activiteitenlogboek is een [platformlogboek](./platform-logs-overview.md) in Azure dat inzicht biedt in gebeurtenissen op abonnementsniveau. Dit geldt ook voor gegevens zoals wanneer een resource wordt gewijzigd of wanneer een virtuele machine wordt gestart. U kunt het activiteitenlogboek weergeven in de Azure-portal, of items ophalen met PowerShell en CLI. Voor aanvullende functionaliteit moet u een diagnostische instelling maken om het activiteiten logboek naar [Azure monitor logboeken](../logs/data-platform-logs.md)te verzenden naar Azure Event hubs om buiten Azure door te sturen, of om Azure Storage te archiveren. In dit artikel vindt u informatie over het weer geven van het activiteiten logboek en het verzenden ervan naar verschillende bestemmingen.

Zie [Diagnostische instellingen maken om platform logboeken en metrische gegevens naar verschillende bestemmingen te verzenden](./diagnostic-settings.md) voor meer informatie over het maken van een diagnostische instelling.

> [!NOTE]
> Vermeldingen in het activiteitenlogboek worden door het systeem gegenereerd en kunnen niet worden gewijzigd of verwijderd.

## <a name="view-the-activity-log"></a>Het activiteitenlogboek weergeven
U kunt het activiteitenlogboek openen vanuit de meeste menu's in de Azure Portal. Het menu waarmee u het opent, bepaalt het aanvankelijke filter. Als u het opent vanuit het menu **monitor** , wordt het enige filter voor het abonnement. Als u het opent vanuit het menu van een resource, wordt het filter ingesteld op die resource. U kunt het filter altijd wijzigen om alle andere vermeldingen weer te geven. Klik op **filter toevoegen** om extra eigenschappen toe te voegen aan het filter.

![Activiteiten logboek weer geven](./media/activity-log/view-activity-log.png)

Zie [Azure activiteiten logboek gebeurtenis schema](activity-log-schema.md#categories)voor een beschrijving van de categorieën activiteiten Logboeken.

### <a name="view-change-history"></a>Wijzigings overzicht weer geven

Voor sommige gebeurtenissen kunt u de wijzigings geschiedenis bekijken, waarin wordt weer gegeven welke wijzigingen er zijn aangebracht tijdens die gebeurtenis tijd. Selecteer een gebeurtenis in het activiteiten logboek waarin u wilt zoeken. Selecteer het tabblad **wijzigings overzicht (preview)** om de bijbehorende wijzigingen met die gebeurtenis weer te geven.

![Lijst met wijzigings geschiedenis voor een gebeurtenis](media/activity-log/change-history-event.png)

Als er wijzigingen zijn aangebracht aan de gebeurtenis, ziet u een lijst met wijzigingen die u kunt selecteren. Hiermee opent u de pagina **wijzigings overzicht (preview)** . Op deze pagina ziet u de wijzigingen van de resource. In het volgende voor beeld ziet u niet alleen dat de VM de grootte heeft gewijzigd, maar wat de vorige VM-grootte was vóór de wijziging en waar deze is gewijzigd in. Zie [resource wijzigingen ophalen](../../governance/resource-graph/how-to/get-resource-changes.md)voor meer informatie over wijzigings geschiedenis.

![Wijzigings geschiedenis pagina met verschillen](media/activity-log/change-history-event-details.png)


### <a name="other-methods-to-retrieve-activity-log-events"></a>Andere methoden om gebeurtenissen in het activiteiten logboek op te halen
U kunt activiteiten logboek gebeurtenissen ook openen met behulp van de volgende methoden.

- Gebruik de cmdlet [Get-AzLog](/powershell/module/az.monitor/get-azlog) om het activiteiten logboek van Power shell op te halen. Zie [Azure monitor Power shell](../powershell-samples.md#retrieve-activity-log)-voor beelden.
- Gebruik [AZ monitor Activity-Log](/cli/azure/monitor/activity-log) om het activiteiten logboek op te halen uit de cli.  Zie [Azure monitor cli](../cli-samples.md#view-activity-log)-voor beelden.
- Gebruik de [Azure Monitor rest API](/rest/api/monitor/) om het activiteiten logboek van een rest-client op te halen. 


## <a name="send-to-log-analytics-workspace"></a>Verzenden naar Log Analytics-werkruimte
 Verzend het activiteiten logboek naar een Log Analytics-werk ruimte om de functies van [Azure monitor logboeken](../logs/data-platform-logs.md) in te scha kelen. Dit omvat het volgende:

- Correleer activiteiten logboek gegevens met andere bewakings gegevens die zijn verzameld door Azure Monitor.
- Consolideer logboek vermeldingen van meerdere Azure-abonnementen en-tenants naar één locatie voor analyse tegelijk.
- Gebruik logboek query's om complexe analyses uit te voeren en uitgebreide inzichten te verkrijgen over activiteiten logboek vermeldingen.
- Gebruik logboek waarschuwingen met activiteiten vermeldingen die meer complexe logica voor waarschuwingen toestaan.
- Vermeldingen in het activiteiten logboek worden langer dan 90 dagen bewaard.
- Geen kosten voor gegevens opname voor activiteiten logboek gegevens die zijn opgeslagen in een Log Analytics-werk ruimte.
- Er worden geen kosten in rekening gebracht tot 90 dagen voor activiteiten logboek gegevens die zijn opgeslagen in een Log Analytics-werk ruimte.

[Een diagnostische instelling maken](./diagnostic-settings.md) om het activiteiten logboek naar een log Analytics-werk ruimte te verzenden. U kunt het activiteiten logboek vanuit elk abonnement verzenden naar Maxi maal vijf werk ruimten. Voor het verzamelen van logboeken tussen tenants is [Azure Lighthouse](../../lighthouse/index.yml) vereist.

Gegevens van activiteiten logboek in een Log Analytics werkruimte worden opgeslagen in een tabel met de naam *AzureActivity* die u kunt ophalen met een [logboek query](../logs/log-query-overview.md) in [log Analytics](../logs/log-analytics-tutorial.md). De structuur van deze tabel is afhankelijk van de [categorie van de logboek vermelding](activity-log-schema.md). Zie de [Azure monitor gegevens referentie](/azure/azure-monitor/reference/tables/azureactivity)voor een beschrijving van de tabel eigenschappen.

Als u bijvoorbeeld een aantal records in het activiteiten logboek voor elke categorie wilt weer geven, gebruikt u de volgende query.

```kusto
AzureActivity
| summarize count() by CategoryValue
```

Gebruik de volgende query om alle records in de beheer categorie op te halen.

```kusto
AzureActivity
| where CategoryValue == "Administrative"
```


## <a name="send-to-azure-event-hubs"></a>Verzenden naar Azure Event Hubs
Verzend het activiteiten logboek naar Azure Event Hubs om vermeldingen buiten Azure te verzenden, bijvoorbeeld naar een SIEM van derden of andere log Analytics-oplossingen. Activiteiten logboek gebeurtenissen van Event hubs worden gebruikt in JSON-indeling met een- `records` element dat de records in elke Payload bevat. Het schema is afhankelijk van de categorie en wordt beschreven in [schema van opslag account en Event hubs](activity-log-schema.md).

Hieronder ziet u voor beelden van uitvoer gegevens van Event Hubs voor een activiteiten logboek:

``` JSON
{
    "records": [
        {
            "time": "2019-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "00000000-0000-0000-0000-000000000000",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```


## <a name="send-to--azure-storage"></a>Verzenden naar Azure Storage
Stuur het activiteiten logboek naar een Azure Storage account als u uw logboek gegevens langer dan 90 dagen wilt bewaren voor controle, statische analyse of back-up. Als u uw gebeurtenissen gedurende 90 dagen wilt behouden of minder hoeft u geen archief in te stellen op een opslag account, omdat activiteiten in het Azure-platform gedurende 90 dagen worden bewaard.

Wanneer u het activiteiten logboek naar Azure verzendt, wordt er een opslag container gemaakt in het opslag account zodra er een gebeurtenis optreedt. De blobs in de container gebruiken de volgende naam Conventie:

```
insights-activity-logs/resourceId=/SUBSCRIPTIONS/{subscription ID}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Een bepaalde Blob kan bijvoorbeeld een naam hebben die er ongeveer als volgt uitziet:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/y=2020/m=06/d=08/h=18/m=00/PT1H.json
```

Elke PT1H.json-blob bevat een JSON-blob van gebeurtenissen die hebben plaatsgevonden binnen het uur dat is opgegeven in de blob-URL (bijvoorbeeld, h=12). Tijdens het huidige uur worden gebeurtenissen toegevoegd aan het bestand PT1H.json wanneer deze zich voordoen. De minuut waarde (m = 00) is altijd 00, omdat de bron logboek gebeurtenissen worden opgesplitst in afzonderlijke blobs per uur.

Elke gebeurtenis wordt opgeslagen in het PT1H.jsbestand met de volgende indeling die gebruikmaakt van een algemeen schema op het hoogste niveau, maar is anders uniek voor elke categorie, zoals beschreven in het schema van het  [activiteiten logboek](activity-log-schema.md).

``` JSON
{ "time": "2020-06-12T13:07:46.766Z", "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MV-VM-01", "correlationId": "0f0cb6b4-804b-4129-b893-70aeeb63997e", "operationName": "Microsoft.Resourcehealth/healthevent/Updated/action", "level": "Information", "resultType": "Updated", "category": "ResourceHealth", "properties": {"eventCategory":"ResourceHealth","eventProperties":{"title":"This virtual machine is starting as requested by an authorized user or process. It will be online shortly.","details":"VirtualMachineStartInitiatedByControlPlane","currentHealthStatus":"Unknown","previousHealthStatus":"Unknown","type":"Downtime","cause":"UserInitiated"}}}
```


## <a name="legacy-collection-methods"></a>Verouderde verzamelings methoden
In deze sectie worden de verouderde methoden beschreven voor het verzamelen van het activiteiten logboek dat eerder is gebruikt voor Diagnostische instellingen. Als u deze methoden gebruikt, kunt u overwegen om over te stappen op Diagnostische instellingen die betere functionaliteit en consistentie met resource logboeken bieden.

### <a name="log-profiles"></a>Logboekprofielen
Logboek profielen zijn de verouderde methode voor het verzenden van het activiteiten logboek naar Azure Storage of event hubs. Gebruik de volgende procedure om met een logboek profiel te blijven werken of om het uit te scha kelen als voor bereiding op het migreren naar een diagnostische instelling.

1. Selecteer in het menu **Azure monitor** van de Azure Portal het **activiteiten logboek**.
3. Klik op **Diagnostische instellingen**.

   ![Diagnostische instellingen](media/activity-log/diagnostic-settings.png)

4. Klik op de banner paars voor de oude ervaring.

    ![Verouderde ervaring](media/activity-log/legacy-experience.png)



### <a name="configure-log-profile-using-powershell"></a>Logboek profiel configureren met Power shell

Als er al een logboek profiel bestaat, moet u eerst het bestaande logboek profiel verwijderen en vervolgens een nieuwe maken.

1. Gebruiken `Get-AzLogProfile` om te bepalen of er een logboek profiel bestaat.  Als er een logboek profiel bestaat, noteert u de eigenschap *name* .

1. Gebruik `Remove-AzLogProfile` om het logboek profiel te verwijderen met de waarde van de eigenschap *name* .

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. Gebruiken `Add-AzLogProfile` om een nieuw logboek profiel te maken:

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | Eigenschap | Vereist | Beschrijving |
    | --- | --- | --- |
    | Name |Ja |De naam van het logboek profiel. |
    | StorageAccountId |Nee |De resource-ID van het opslag account waarin het activiteiten logboek moet worden opgeslagen. |
    | Servicebusruleid kunnen |Nee |Service Bus regel-ID voor de Service Bus naam ruimte waarin u Event hubs wilt maken. Dit is een teken reeks met de volgende indeling: `{service bus resource ID}/authorizationrules/{key name}` . |
    | Locatie |Ja |Een door komma's gescheiden lijst met regio's waarvoor u activiteiten logboek gebeurtenissen wilt verzamelen. |
    | RetentionInDays |Ja |Aantal dagen dat gebeurtenissen moeten worden bewaard in het opslag account, tussen 1 en 365. Met de waarde nul worden de logboeken voor onbepaalde tijd opgeslagen. |
    | Categorie |Nee |Een door komma's gescheiden lijst met gebeurtenis categorieën die moeten worden verzameld. Mogelijke waarden zijn _schrijven_, _verwijderen_ en _actie_. |

### <a name="example-script"></a>Voorbeeldscript
Hier volgt een voor beeld van een Power shell-script voor het maken van een logboek profiel waarmee het activiteiten logboek naar zowel een opslag account als Event Hub wordt geschreven.

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzLogProfile -Name $logProfileName -Location $locations -StorageAccountId  $storageAccountId -ServiceBusRuleId $serviceBusRuleId
   ```


### <a name="configure-log-profile-using-azure-cli"></a>Logboek profiel configureren met behulp van Azure CLI

Als er al een logboek profiel bestaat, moet u eerst het bestaande logboek profiel verwijderen en vervolgens een nieuw logboek profiel maken.

1. Gebruiken `az monitor log-profiles list` om te bepalen of er een logboek profiel bestaat.
2. Gebruik `az monitor log-profiles delete --name "<log profile name>` om het logboek profiel te verwijderen met de waarde van de eigenschap *name* .
3. Gebruiken `az monitor log-profiles create` om een nieuw logboek profiel te maken:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | Eigenschap | Vereist | Beschrijving |
    | --- | --- | --- |
    | naam |Ja |De naam van het logboek profiel. |
    | Storage-account-id |Ja |De resource-ID van het opslag account waarnaar de activiteiten logboeken moeten worden opgeslagen. |
    | locaties |Ja |Een door spaties gescheiden lijst met regio's waarvoor u activiteiten logboek gebeurtenissen wilt verzamelen. U kunt een lijst weer geven met alle regio's voor uw abonnement met behulp van `az account list-locations --query [].name` . |
    | resterende |Ja |Aantal dagen dat gebeurtenissen moeten worden bewaard, tussen 1 en 365. Met de waarde nul worden de logboeken voor onbepaalde tijd opgeslagen (permanent).  Als de waarde nul is, moet de para meter ingeschakeld worden ingesteld op ONWAAR. |
    |enabled | Ja |Waar of Niet waar.  Wordt gebruikt om het Bewaar beleid in of uit te scha kelen.  Indien waar, moet de para meter Days een waarde zijn die groter is dan 0.
    | categorieën |Ja |Een door spaties gescheiden lijst met gebeurtenis categorieën die moeten worden verzameld. Mogelijke waarden zijn schrijven, verwijderen en actie. |


### <a name="log-analytics-workspace"></a>Log Analytics-werkruimte
De verouderde methode voor het verzenden van het activiteiten logboek naar een Log Analytics-werk ruimte is het koppelen van het logboek in de werkruimte configuratie. 

1. Selecteer in het menu **log Analytics werk ruimten** in de Azure Portal de werk ruimte om het activiteiten logboek te verzamelen.
1. Selecteer in de sectie **werkruimte gegevens bronnen** van het menu van de werk ruimte **Azure-activiteiten logboek**.
1. Klik op het abonnement dat u wilt verbinden.

    ![Scherm opname toont Log Analytics-werk ruimte waarvoor een Azure-activiteiten logboek is geselecteerd.](media/activity-log/workspaces.png)

2. Klik op **verbinden** om het activiteiten logboek in het abonnement te verbinden met de geselecteerde werk ruimte. Als het abonnement al is verbonden met een andere werk ruimte, klikt u eerst op **verbinding verbreken** om de verbinding te verbreken.

    ![Werk ruimten verbinden](media/activity-log/connect-workspace.png)


Als u de instelling wilt uitschakelen, voert u dezelfde procedure uit en klikt u op **verbinding verbreken** om het abonnement uit de werk ruimte te verwijderen.

### <a name="data-structure-changes"></a>Wijzigingen in de gegevens structuur
Diagnostische instellingen verzenden dezelfde gegevens als de verouderde methode die wordt gebruikt om het activiteiten logboek te verzenden met enkele wijzigingen in de structuur van de tabel *AzureActivity* .

De kolommen in de volgende tabel zijn afgeschaft in het bijgewerkte schema. Ze zijn nog steeds aanwezig in *AzureActivity* , maar ze hebben geen gegevens. De vervanging voor deze kolommen is niet nieuw, maar bevat dezelfde gegevens als de afgeschafte kolom. Ze hebben een andere indeling, dus u moet mogelijk logboek query's wijzigen die deze gebruiken. 

| Afgeschafte kolom | Vervangende kolom |
|:---|:---|
| Activity status    | ActivityStatusValue    |
| ActivitySubstatus | ActivitySubstatusValue |
| Categorie          | CategoryValue          |
| OperationName     | OperationNameValue     |
| ResourceProvider  | ResourceProviderValue  |

> [!IMPORTANT]
> In sommige gevallen kunnen de waarden in deze kolommen in hoofdletters zijn getypt. Als u een query hebt die deze kolommen bevat, moet u de operator [=~](/azure/kusto/query/datatypes-string-operators) gebruiken om een niet-hoofdletter gevoelige vergelijking te maken.

De volgende kolom zijn toegevoegd aan *AzureActivity* in het bijgewerkte schema:

- Authorization_d
- Claims_d
- Properties_d

## <a name="activity-log-analytics-monitoring-solution"></a>Bewakings oplossing Analyse van activiteitenlogboek
De bewakings oplossing voor Azure Log Analytics wordt binnenkort afgeschaft en vervangen door een werkmap met het bijgewerkte schema in de Log Analytics-werk ruimte. U kunt de oplossing blijven gebruiken als u deze al hebt ingeschakeld, maar deze kan alleen worden gebruikt als u het activiteiten logboek verzamelt met behulp van verouderde instellingen. 



### <a name="use-the-solution"></a>De oplossing gebruiken
Bewakings oplossingen zijn toegankelijk via het menu **monitor** in de Azure Portal. Selecteer **meer** in het gedeelte **inzichten** om de **overzichts** pagina te openen met de oplossings tegels. De tegel **activiteiten logboeken van Azure** bevat een telling van het aantal **AzureActivity** -records in uw werk ruimte.

![Tegel Azure-activiteiten logboeken](media/activity-log/azure-activity-logs-tile.png)


Klik op de tegel **Azure-activiteiten logboeken** om de weer gave **Azure-activiteiten logboeken** te openen. De weer gave bevat de visualisatie onderdelen in de volgende tabel. Elk onderdeel bevat Maxi maal 10 items die overeenkomen met de criteria van die onderdelen voor het opgegeven tijds bereik. U kunt een logboek query uitvoeren waarmee alle overeenkomende records worden geretourneerd door te klikken op **Alles bekijken** onder aan het onderdeel.

![Dash board Azure-activiteiten logboeken](media/activity-log/activity-log-dash.png)


### <a name="enable-the-solution-for-new-subscriptions"></a>De oplossing voor nieuwe abonnementen inschakelen
U kunt de activiteiten logboek analyse niet meer aan uw abonnement toevoegen met behulp van de Azure Portal. U kunt deze toevoegen aan de hand van de volgende procedure met een resource manager-sjabloon. 

1. Kopieer de volgende JSON naar een bestand met de naam *ActivityLogTemplate*. json.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "defaultValue": "my-workspace",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "east us",
              "west us",
              "australia central",
              "west europe"
            ],
            "defaultValue": "australia central",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        }
      },
        "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "features": {
                    "searchVersion": 2
                }
            }
        },
        {
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "2015-11-01-preview",
            "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
            ],
            "plan": {
                "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
                "promotionCode": "",
                "product": "OMSGallery/AzureActivity",
                "publisher": "Microsoft"
            },
            "properties": {
                "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]",
                "containedResources": [
                    "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName')), '/views/AzureActivity(',parameters('workspaceName'))]"
                ]
            }
        },
        {
          "type": "Microsoft.OperationalInsights/workspaces/datasources",
          "kind": "AzureActivityLog",
          "name": "[concat(parameters('workspaceName'), '/', subscription().subscriptionId)]",
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[parameters('WorkspaceName')]"
          ],
          "properties": {
              "linkedResourceId": "[concat(subscription().Id, '/providers/microsoft.insights/eventTypes/management')]"
          }
        }
      ]
    }    
    ```

2. Implementeer de sjabloon met behulp van de volgende Power shell-opdrachten:

    ```PowerShell
    Connect-AzAccount
    Select-AzSubscription <SubscriptionName>
    New-AzResourceGroupDeployment -Name activitysolution -ResourceGroupName <ResourceGroup> -TemplateFile <Path to template file>
    ```



## <a name="next-steps"></a>Volgende stappen

* [Een overzicht van de platform logboeken lezen](./platform-logs-overview.md)
* [Gebeurtenis schema voor activiteiten logboek controleren](activity-log-schema.md)
* [Diagnostische instelling maken om activiteiten logboeken te verzenden naar andere bestemmingen](./diagnostic-settings.md)