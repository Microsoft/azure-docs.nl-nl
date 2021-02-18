---
title: Een Azure Monitor Application Insights klassieke resource migreren naar een op werk ruimte gebaseerde resource | Microsoft Docs
description: Meer informatie over de stappen die nodig zijn om uw Azure Monitor Application Insights klassieke resource bij te werken naar het nieuwe model op basis van werk ruimte.
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 5791abe33dee2e62aadb00ae1024338e1e44a900
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100584247"
---
# <a name="migrate-to-workspace-based-application-insights-resources"></a>Migreren naar op werk ruimte gebaseerde Application Insights resources

In deze hand leiding wordt stapsgewijs uitgelegd hoe u een klassieke Application Insights resource migreert naar een resource op basis van een werk ruimte. Resources op basis van een werk ruimte ondersteunen volledige integratie tussen Application Insights en Log Analytics. Resources op basis van een werk ruimte verzenden Application Insights telemetrie naar een algemene Log Analytics-werk ruimte, waarmee u toegang krijgt tot [de nieuwste functies van Azure monitor](#new-capabilities) terwijl logboeken voor de toepassings-, infra structuur en het platform op één geconsolideerde locatie worden bewaard.

Met resources op basis van een werk ruimte kunnen veelvoorkomende Azure-functies voor op rollen gebaseerd toegangs beheer (Azure RBAC) worden gebruikt in uw resources en hoeft u niet meer te vragen over cross-app-en werkruimte query's.

**Op werk ruimte gebaseerde resources zijn momenteel beschikbaar in alle commerciële regio's en de Amerikaanse overheid van Azure**

## <a name="new-capabilities"></a>Nieuwe functionaliteit

Met Application Insights op basis van een werk ruimte kunt u profiteren van de nieuwste mogelijkheden van Azure Monitor en Log Analytics met inbegrip van:

* Door de [klant beheerde sleutels (CMK)](../logs/customer-managed-keys.md) bieden versleuteling op rest voor uw gegevens met versleutelings sleutels waartoe alleen u toegang hebt.
* Met de [persoonlijke Azure-koppeling](../logs/private-link-security.md) kunt u Azure PaaS-services veilig koppelen aan uw virtuele netwerk met behulp van privé-eind punten.
* [Met uw eigen opslag (BYOS) voor Profiler en snapshot debugger](./profiler-bring-your-own-storage.md) hebt u volledige controle over het beleid voor versleuteling op rest, het beheer beleid voor levens duur en netwerk toegang tot alle gegevens die zijn gekoppeld aan Application Insights Profiler en snapshot debugger. 
* Met [capaciteits reserverings lagen](../logs/manage-cost-storage.md#pricing-model) kunt u Maxi maal 25% besparen op basis van de betalen naar gebruik-prijs. 
* Snellere gegevens opname via Log Analytics streaming-opname.

## <a name="migration-process"></a>Migratieproces

Wanneer u migreert naar een resource op basis van een werk ruimte, worden er geen gegevens vanuit de opslag van uw klassieke resource overgedragen naar de nieuwe opslag op basis van de werk ruimte. Als u wilt migreren, wijzigt u in plaats daarvan de locatie waar nieuwe gegevens naar een Log Analytics werkruimte worden geschreven, terwijl u de toegang tot uw klassieke resource gegevens behoudt. 

Uw klassieke resource gegevens blijven behouden en zijn onderhevig aan de Bewaar instellingen op uw klassieke Application Insights-resource. Voor alle nieuwe gegevensgestuurde post migratie zijn de [Bewaar instellingen](../logs/manage-cost-storage.md#change-the-data-retention-period) van de gekoppelde log Analytics werk ruimte van toepassing, die ook [verschillende Bewaar instellingen per gegevens type](../logs/manage-cost-storage.md#retention-by-data-type)ondersteunt.
Het migratie proces is **permanent en kan niet worden omgekeerd**. Wanneer u een resource hebt gemigreerd naar een werk ruimte op basis van Application Insights, is het altijd een resource op basis van een werk ruimte. Zodra u de migratie hebt uitgevoerd, kunt u echter zo vaak als nodig is de doel werkruimte wijzigen. 

> [!NOTE]
> Gegevens opname en retentie voor op werk ruimte gebaseerde Application Insights resources worden [gefactureerd via de log Analytics werk ruimte](../logs/manage-cost-storage.md) waarin de gegevens zich bevinden. Als u hebt gekozen voor het bewaren van gegevens van meer dan 90 dagen voor gegevens die zijn opgenomen in de klassieke Application Insights resource voordat de migratie wordt uitgevoerd, blijft de gegevens retentie in rekening worden gebracht via die Application Insights resource. Meer [informatie]( ./pricing.md#workspace-based-application-insights) over facturering voor op werk ruimte gebaseerde Application Insights resources.

Als u geen bestaande resource hoeft te migreren en u in plaats daarvan een nieuwe op werk ruimte gebaseerde Application Insights resource wilt maken, gebruikt u de [hand leiding voor het maken van resources op basis van de werk ruimte](create-workspace-resource.md).

## <a name="pre-requisites"></a>Vereisten 

- Een Log Analytics-werk ruimte waarvoor de toegangs beheer modus is ingesteld op de **`use resource or workspace permissions`** instelling. 

    - Application Insights resources op basis van een werk ruimte zijn niet compatibel met werk ruimten die zijn ingesteld op de toegewezen **`workspace based permissions`** instelling. Raadpleeg de [richt lijnen voor het log Analytics configureren van toegangs beheer modus](../logs/manage-access.md#configure-access-control-mode) voor meer informatie log Analytics over het toegangs beheer voor werk ruimten.

    - Als u nog geen bestaande Log Analytics-werk ruimte hebt, [raadpleegt u de documentatie voor het maken van log Analytics werk ruimte](../logs/quick-create-workspace.md).
    
- Continue export wordt niet ondersteund voor op werk ruimte gebaseerde resources en moet worden uitgeschakeld.
Zodra de migratie is voltooid, kunt u [Diagnostische instellingen](../essentials/diagnostic-settings.md) gebruiken om gegevens archivering te configureren voor een opslag account of streaming naar Azure Event hub.  

- Controleer de huidige Bewaar instellingen onder **Algemeen**  >  **gebruik en geschatte kosten**  >  voor het **bewaren van gegevens** voor uw log Analytics-werk ruimte. Deze instelling is van invloed op hoe lang nieuwe opgenomen gegevens worden opgeslagen nadat u uw Application Insights-resource hebt gemigreerd. Als u momenteel Application Insights gegevens langer dan de standaard 90 dagen opslaat en deze grotere Bewaar periode wilt behouden, moet u mogelijk de Bewaar instellingen voor uw werk ruimte aanpassen.

## <a name="migrate-your-resource"></a>Uw resource migreren

In deze sectie wordt u begeleid bij het migreren van een klassieke Application Insights resource naar het nieuwe resource type op basis van een werk ruimte.

1. Selecteer in uw Application Insights-resource **Eigenschappen** onder de kop **configureren** in de linker menu balk.

    ![Eigenschappen die zijn gemarkeerd in het rood vak](./media/convert-classic-resource/properties.png)

2. Selecteer **`Migrate to Workspace-based`**.
    
     ![Knop Resource migreren](./media/convert-classic-resource/migrate.png)

3. Kies de Log Analytics werk ruimte waar u alle toekomstige opgenomen Application Insights telemetrie wilt opslaan.

     ![Gebruikers interface van de wizard Migratie met optie om de targe-werk ruimte te selecteren](./media/convert-classic-resource/migration.png)
    

Zodra de resource is gemigreerd, ziet u de bijbehorende werkruimte gegevens in het deel venster **overzicht** :

![Werkruimtenaam](./media/create-workspace-resource/workspace-name.png)

Als u op de tekst van de blauwe koppeling klikt, gaat u naar de gekoppelde Log Analytics-werk ruimte waar u kunt profiteren van de nieuwe geïntegreerde werk ruimte query omgeving.

## <a name="understanding-log-queries"></a>Informatie over logboek query's

We bieden nog steeds volledige compatibiliteit voor uw Application Insights klassieke resource query's, werkmappen en waarschuwingen op basis van Logboeken in de Application Insights ervaring. 

Als u query's wilt schrijven op [basis van de nieuwe, op werk ruimte gebaseerde tabel structuur/schema](apm-tables.md), moet u eerst naar uw log Analytics-werk ruimte navigeren. 

Wanneer u rechtstreeks vanuit de Log Analytics gebruikers interface in uw werk ruimte een query uitvoert, worden alleen de gegevens weer gegeven die zijn opgenomen na migratie. Als u uw klassieke Application Insights gegevens en nieuwe gegevens die zijn opgenomen na de migratie in een geïntegreerde query-ervaring wilt zien, gebruikt u de query weergave logboeken (analyse) vanuit uw gemigreerde Application Insights resource.

## <a name="programmatic-resource-migration"></a>Migratie van programma bronnen

### <a name="azure-cli"></a>Azure CLI

Als u toegang wilt krijgen tot de preview-Application Insights Azure CLI-opdrachten, moet u eerst het volgende uitvoeren:

```azurecli
 az extension add -n application-insights
```

Als u de opdracht niet uitvoert `az extension add` , wordt een fout bericht weer gegeven waarin staat: `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

U kunt nu het volgende uitvoeren om uw Application Insights-resource te maken:

```azurecli
az monitor app-insights component update --app
                                         --resource-group
                                         [--ingestion-access {Disabled, Enabled}]
                                         [--kind]
                                         [--query-access {Disabled, Enabled}]
                                         [--retention-time]
                                         [--workspace]
```

#### <a name="example"></a>Voorbeeld

```azurecli
az monitor app-insights component update --app your-app-insights-resource-name -g your_resource_group --workspace "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/test1234/providers/microsoft.operationalinsights/workspaces/test1234555"
```

Raadpleeg de [documentatie van Azure cli](/cli/azure/ext/application-insights/monitor/app-insights/component#ext-application-insights-az-monitor-app-insights-component-update)voor de volledige Azure cli-documentatie voor deze opdracht.

### <a name="azure-powershell"></a>Azure PowerShell

De `Update-AzApplicationInsights` Power shell-opdracht biedt momenteel geen ondersteuning voor het migreren van een klassieke Application Insights resource naar een werk ruimte. Als u een resource op basis van een werk ruimte wilt maken met Power shell, kunt u de onderstaande Azure Resource Manager sjablonen gebruiken en implementeren met Power shell.

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sjablonen

#### <a name="template-file"></a>Sjabloonbestand

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string"
        },
        "type": {
            "type": "string"
        },
        "regionId": {
            "type": "string"
        },
        "tagsArray": {
            "type": "object"
        },
        "requestSource": {
            "type": "string"
        },
        "workspaceResourceId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "microsoft.insights/components",
            "location": "[parameters('regionId')]",
            "tags": "[parameters('tagsArray')]",
            "apiVersion": "2020-02-02-preview",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Application_Type": "[parameters('type')]",
                "Flow_Type": "Redfield",
                "Request_Source": "[parameters('requestSource')]",
                "WorkspaceResourceId": "[parameters('workspaceResourceId')]"
            }
        }
    ]
}
```

#### <a name="parameters-file"></a>Parameterbestand

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "type": {
            "value": "web"
        },
        "name": {
            "value": "customresourcename"
        },
        "regionId": {
            "value": "eastus"
        },
        "tagsArray": {
            "value": {}
        },
        "requestSource": {
            "value": "Custom"
        },
        "workspaceResourceId": {
            "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my_resource_group/providers/microsoft.operationalinsights/workspaces/myworkspacename"
        }
    }
}

```

## <a name="modifying-the-associated-workspace"></a>De gekoppelde werk ruimte wijzigen

Zodra een Application Insights resource op basis van een werk ruimte is gemaakt, kunt u de gekoppelde Log Analytics werk ruimte wijzigen.

Selecteer in het deel venster Resource Application Insights **Eigenschappen**  >  **werk ruimte wijzigen**  >  **log Analytics werk ruimten**.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="access-mode"></a>Toegangsmodus

**Fout bericht:** *de geselecteerde werk ruimte is geconfigureerd met toegangs modus op basis van een werk ruimte. Sommige APM-functies kunnen worden beïnvloed. Selecteer een andere werk ruimte of sta toegang op basis van resources in de werk ruimte-instellingen toe. U kunt deze fout negeren door CLI te gebruiken.* 

Als u de Application Insights resource op basis van een werk ruimte goed wilt laten werken, moet u de toegangs beheer modus van uw doel Log Analytics werk ruimte wijzigen in de instelling **machtigingen voor resource of werk ruimte** . Deze instelling bevindt zich in de gebruikers interface van log Analytics werk ruimte onder **Eigenschappen**  >  **toegangs beheer modus**. Raadpleeg de richt lijnen voor het [log Analytics configureren van de toegangs beheer modus](../logs/manage-access.md#configure-access-control-mode)voor gedetailleerde instructies. Als uw toegangs beheer modus is ingesteld op de instelling exclusieve **machtigingen voor werk ruimte vereist** , blijft migratie via de portal migratie-ervaring geblokkeerd.

Als u de toegangs beheer modus uit veiligheids overwegingen voor uw huidige doel werkruimte niet kunt wijzigen, kunt u het beste een nieuwe Log Analytics-werk ruimte maken die u voor de migratie kunt gebruiken. 

### <a name="continuous-export"></a>Continue export

**Fout bericht:** *continue export moet worden uitgeschakeld voordat u kunt door gaan. Na de migratie gebruikt u de diagnostische instellingen voor het exporteren.* 

De verouderde functionaliteit voor continue export wordt niet ondersteund voor op werk ruimte gebaseerde resources. Voordat u de migratie uitvoert, moet u doorlopend exporteren uitschakelen.

1. Selecteer in de resource weergave van uw Application Insights **doorlopend exporteren** onder de kop **configureren** .

    ![Menu-item doorlopend exporteren](./media/convert-classic-resource/continuous-export.png)

2. Selecteer **Uitschakelen**.

    ![Knop uitschakelen voor doorlopend exporteren](./media/convert-classic-resource/disable.png)

- Zodra u uitschakelen hebt geselecteerd, kunt u teruggaan naar de migratie GEBRUIKERSINTERFACE. Als de pagina doorlopend exporteren wordt gevraagd of uw instellingen niet worden opgeslagen, kunt u OK selecteren voor deze prompt, omdat deze niet van toepassing is op het uit-en inschakelen van continue export.

- Zodra u uw Application Insights resource naar een werk ruimte hebt gemigreerd, kunt u Diagnostische instellingen gebruiken om de functionaliteit te vervangen die doorlopende export wordt gebruikt om te bieden. Selecteer **Diagnostische instellingen**  >  **Diagnostische instelling toevoegen** vanuit uw Application Insights-resource. U kunt alle tabellen selecteren of een subset van tabellen die u wilt archiveren naar een opslag account of naar een Azure Event hub streamen. Raadpleeg de [richt lijnen voor Azure monitor Diagnostische instellingen](../essentials/diagnostic-settings.md)voor gedetailleerde richt lijnen voor Diagnostische instellingen.

### <a name="retention-settings"></a>Bewaar instellingen

**Waarschuwings bericht:** *uw aangepaste Application Insights Bewaar instellingen zijn niet van toepassing op gegevens die naar de werk ruimte worden verzonden. U moet dit afzonderlijk opnieuw configureren.*

U hoeft geen wijzigingen aan te brengen voordat u de migratie uitvoert, maar dit bericht meldt u dat uw huidige Application Insights Bewaar instellingen niet zijn ingesteld op de standaard retentie periode van 90 dagen. Dit bericht betekent dat u de Bewaar instellingen voor uw Log Analytics-werk ruimte kunt wijzigen voordat u de migratie uitvoert en nieuwe gegevens opneemt. 

U kunt de huidige Bewaar instellingen voor log Analytics controleren onder **Algemeen**  >  **gebruik en geschatte kosten** voor  >  het **bewaren van gegevens** vanuit de log Analytics-gebruikers interface. Deze instelling is van invloed op hoe lang nieuwe opgenomen gegevens worden opgeslagen nadat u uw Application Insights-resource hebt gemigreerd.

## <a name="next-steps"></a>Volgende stappen

* [Metrische gegevens verkennen](../essentials/metrics-charts.md)
* [Analytics-query's schrijven](../logs/log-query-overview.md)
