---
title: Gegevens fabrieken bewaken met behulp van Azure Monitor
description: Meer informatie over het gebruik van Azure Monitor om/Azure Data Factory-pijp lijnen te bewaken door Diagnostische logboeken in te scha kelen met informatie van Data Factory.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: 55b2fd911b32eae4f04ad9690de091e9dbd46ea4
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643966"
---
# <a name="monitor-and-alert-data-factory-by-using-azure-monitor"></a>Data Factory bewaken en waarschuwen door gebruik te maken van Azure Monitor

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Cloud toepassingen zijn complex en hebben veel bewegende onderdelen. Monitors bieden gegevens waarmee u ervoor kunt zorgen dat uw toepassingen in een goede staat blijven werken en worden uitgevoerd. Monitors helpen u om potentiële problemen te voor komen en eerdere problemen op te lossen. U kunt bewakings gegevens gebruiken om uitgebreid inzicht te krijgen in uw toepassingen. Deze kennis helpt u bij het verbeteren van de prestaties en het onderhoud van toepassingen. Daarnaast kunt u hiermee acties automatiseren die anders hand matig moeten worden uitgevoerd.

Azure Monitor biedt metrische gegevens op basis van de infra structuur en logboeken voor de meeste Azure-Services. Diagnostische logboeken van Azure worden verzonden door een resource en bieden uitgebreide, frequente gegevens over de werking van die resource. Azure Data Factory (ADF) kan Diagnostische logboeken in Azure Monitor schrijven. Bekijk de volgende video voor een inleiding en demonstratie van zeven minuten voor deze functie:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

Zie [Overzicht van Azure Monitor](../azure-monitor/overview.md) voor meer informatie.

## <a name="keeping-azure-data-factory-metrics-and-pipeline-run-data"></a>Azure Data Factory metrische gegevens en pijp lijn bewaren

Data Factory Stores-pijp lijn-gegevens slechts 45 dagen worden uitgevoerd. Gebruik Azure Monitor als u deze gegevens gedurende een langere periode wilt blijven gebruiken. Met monitor kunt u Diagnostische logboeken voor analyse naar meerdere verschillende doelen routeren.

* **Opslag account**: Sla uw Diagnostische logboeken op in een opslag account voor controle of hand matige inspectie. U kunt de diagnostische instellingen gebruiken om de Bewaar tijd in dagen op te geven.
* **Event hub**: de logboeken naar Azure Event hubs streamen. De logboeken worden ingevoerd in een partner service/aangepaste analyse oplossing, zoals Power BI.
* **Log Analytics**: Analyseer de logboeken met log Analytics. De integratie van Data Factory met Azure Monitor is handig in de volgende scenario's:
  * U wilt complexe query's schrijven naar een uitgebreide set metrische gegevens die worden gepubliceerd door Data Factory om te bewaken. U kunt met behulp van monitor aangepaste waarschuwingen maken voor deze query's.
  * U wilt controleren op gegevens fabrieken. U kunt gegevens van meerdere gegevens fabrieken naar een enkele werk ruimte van de monitor routeren.

U kunt ook een opslag account of een event hub-naam ruimte gebruiken die niet voor komt in het abonnement van de resource die logboeken meenemen. De gebruiker die de instelling configureert, moet beschikken over het juiste toegangs beheer (Azure RBAC) voor op rollen gebaseerde toegang tot beide abonnementen.

## <a name="configure-diagnostic-settings-and-workspace"></a>Diagnostische instellingen en werk ruimte configureren

Diagnostische instellingen voor uw data factory maken of toevoegen.

1. Ga in de portal naar monitor. Instellingen **voor**  >  **Diagnostische** instellingen selecteren.

1. Selecteer de data factory waarvoor u een diagnostische instelling wilt instellen.

1. Als er geen instellingen zijn op de geselecteerde data factory, wordt u gevraagd een instelling te maken. Selecteer **Diagnostische gegevens inschakelen**.

   ![Een diagnostische instelling maken als er geen instellingen zijn](media/data-factory-monitor-oms/monitor-oms-image1.png)

   Als er bestaande instellingen zijn op de data factory, ziet u een lijst met instellingen die al zijn geconfigureerd op de data factory. Selecteer **Diagnostische instellingen toevoegen**.

   ![Een diagnostische instelling toevoegen als er instellingen bestaan](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. Geef een naam op voor de instelling, selecteer **verzenden naar log Analytics** en selecteer een werk ruimte in **log Analytics werk ruimte**.

    * In de _Diagnostische_ modus van Azure stroomt Diagnostische logboeken in de tabel _AzureDiagnostics_ .

    * In de _resource-specifieke_ modus worden Diagnostische logboeken van Azure Data Factory in de volgende tabellen geplaatst:
      - _ADFActivityRun_
      - _ADFPipelineRun_
      - _ADFTriggerRun_
      - _ADFSSISIntegrationRuntimeLogs_
      - _ADFSSISPackageEventMessageContext_
      - _ADFSSISPackageEventMessages_
      - _ADFSSISPackageExecutableStatistics_
      - _ADFSSISPackageExecutionComponentPhases_
      - _ADFSSISPackageExecutionDataStatistics_

      U kunt verschillende logboeken selecteren die relevant zijn voor uw workloads om te verzenden naar Log Analytics tabellen. Als u bijvoorbeeld geen gebruikmaakt van SQL Server Integration Services (SSIS), hoeft u geen SSIS-logboeken te selecteren. Als u de bewerkingen voor starten/stoppen en onderhoud van SSIS Integration Runtime (IR) wilt registreren, kunt u SSIS IR-logboeken selecteren. Als u SSIS-pakket uitvoeringen aanroept via T-SQL op SQL Server Management Studio (SSMS), SQL Server Agent of andere aangewezen hulpprogram ma's, kunt u SSIS-pakket logboeken selecteren. Als u SSIS-pakket uitvoeringen aanroept via uitvoering van SSIS-pakket activiteiten in ADF-pijp lijnen, kunt u alle logboeken selecteren.

    * Als u _AllMetrics_ selecteert, worden er diverse ADF-metrische gegevens beschikbaar gesteld waarmee u waarschuwingen kunt bewaken of genereren, inclusief de metrische gegevens voor ADF-activiteiten, pijp lijnen en triggers en voor SSIS IR-bewerkingen en SSIS-pakket uitvoeringen.

   ![Geef uw instellingen een naam en selecteer een werk ruimte voor logboek analyse](media/data-factory-monitor-oms/monitor-oms-image2.png)

    > [!NOTE]
    > Omdat een Azure-logboek tabel niet meer dan 500 kolommen kan bevatten, raden we u **ten zeerste** aan de _resource-specifieke modus_ te selecteren. Zie [AzureDiagnostics-logboeken](/azure/azure-monitor/reference/tables/azurediagnostics)voor meer informatie.

1. Selecteer **Opslaan**.

Na enkele ogen blikken wordt de nieuwe instelling weer gegeven in de lijst met instellingen voor deze data factory. Diagnostische logboeken worden naar die werk ruimte gestreamd zodra er nieuwe gebeurtenis gegevens worden gegenereerd. Maxi maal 15 minuten kan verstrijken tussen het moment dat een gebeurtenis wordt verzonden en wanneer deze wordt weer gegeven in Log Analytics.

## <a name="install-azure-data-factory-analytics-solution-from-azure-marketplace"></a>Azure Data Factory Analytics-oplossing installeren vanuit Azure Marketplace

Deze oplossing biedt een overzicht van de algemene status van uw Data Factory, met opties voor het inzoomen op Details en het oplossen van problemen met onverwachte gedrags patronen. Met uitgebreide, uit de venster weergaven kunt u inzicht krijgen in de belangrijkste verwerking, waaronder:

* In één oogopslag een overzicht van de uitvoeringen van data factory pijp lijn, activiteit en triggers
* De mogelijkheid om in te zoomen op data factory-activiteit uitvoeringen per type
* Samen vatting van data factory bovenste pijp lijn, activiteiten fouten

1. Ga naar **Azure Marketplace**, kies **Analytics** filter en zoek naar **Azure Data Factory Analytics (preview)**

   ![Ga naar ' Azure Marketplace ', voer ' Analytics filter ' in en selecteer ' Azure Data Factory Analytics (preview).](media/data-factory-monitor-oms/monitor-oms-image3.png)

1. Details over **Azure Data Factory Analytics (preview-versie)**

   ![Meer informatie over ' Azure Data Factory Analytics (preview) '](media/data-factory-monitor-oms/monitor-oms-image4.png)

1. Selecteer **maken** en maak of selecteer de **log Analytics werk ruimte**.

   ![Een nieuwe oplossing maken](media/data-factory-monitor-oms/monitor-log-analytics-image-5.png)

### <a name="monitor-data-factory-metrics"></a>Data Factory metrische gegevens bewaken

Als u deze oplossing installeert, wordt er een standaardset weer gaven gemaakt in de sectie werkmappen van de gekozen Log Analytics werk ruimte. Als gevolg hiervan worden de volgende metrische gegevens ingeschakeld:

* ADF-uitvoeringen-1) pijplijn uitvoeringen door Data Factory
* ADF-uitvoeringen-2) uitvoering van activiteit door Data Factory
* ADF-uitvoeringen-3) activering wordt uitgevoerd door Data Factory
* ADF-fouten-1) Top 10 van pijplijn fouten per Data Factory
* ADF-fouten-2) de tien uitvoering van de activiteit wordt uitgevoerd door Data Factory
* ADF-fouten-3) de tien belangrijkste trigger fouten door Data Factory
* ADF-statistieken-1) uitvoering van activiteit per type
* ADF-statistieken-2) trigger uitvoeringen per type
* ADF-statistieken-3) maximale duur van pijplijn uitvoeringen

![Venster met ' werkmappen (preview) ' en ' AzureDataFactoryAnalytics ' gemarkeerd](media/data-factory-monitor-oms/monitor-oms-image6.png)

U kunt de voor gaande metrische gegevens visualiseren, de query's achter deze metrische gegevens bekijken, de query's bewerken, waarschuwingen maken en andere acties uitvoeren.

![Grafische weer gave van de pijp lijn wordt uitgevoerd door data factory "](media/data-factory-monitor-oms/monitor-oms-image8.png)

> [!NOTE]
> Met Azure Data Factory Analytics (preview) worden Diagnostische logboeken naar _resource-specifieke_ doel tabellen verzonden. U kunt query's schrijven op basis van de volgende tabellen: _ADFPipelineRun_, _ADFTriggerRun_ en _ADFActivityRun_.

## <a name="data-factory-metrics"></a>Data Factory metrische gegevens

Met monitor kunt u inzicht krijgen in de prestaties en status van uw Azure-workloads. Het belangrijkste type monitor gegevens is de metriek. dit wordt ook wel het prestatie meter item genoemd. Metrische gegevens worden verzonden door de meeste Azure-resources. Monitor biedt verschillende manieren om deze metrische gegevens te configureren en te gebruiken voor bewaking en probleem oplossing.

Hier volgen enkele van de metrische gegevens die worden verzonden door Azure Data Factory versie 2:

| **Meting**                           | **Weergave naam voor metrische gegevens**                  | **Eenheid** | **Type aggregatie** | **Beschrijving**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| ActivityCancelledRuns                 | Metrische gegevens voor geannuleerde activiteit uitgevoerd           | Aantal    | Totaal                | Het totale aantal uitgevoerde activiteiten dat in een minuut venster is geannuleerd. |
| ActivityFailedRuns                   | Metrische gegevens mislukte uitvoering van activiteit             | Aantal    | Totaal                | Het totale aantal uitgevoerde activiteiten in een minuut venster. |
| ActivitySucceededRuns                | Metrische gegevens uitvoeringen uitgevoerde activiteit          | Aantal    | Totaal                | Het totale aantal uitgevoerde activiteiten in een minuut venster. |
| PipelineCancelledRuns                 | Metrische gegevens van de pijplijn uitvoeringen geannuleerd           | Aantal    | Totaal                | Het totale aantal uitvoeringen van de pijp lijn dat in een minuut venster is geannuleerd. |
| PipelineFailedRuns                   | Metrische gegevens van mislukte pijplijn uitvoeringen             | Aantal    | Totaal                | Het totale aantal uitvoeringen van de pijp lijn dat is mislukt binnen een minuut venster. |
| PipelineSucceededRuns                | Metrische uitvoerings metingen geslaagde pijp lijnen          | Aantal    | Totaal                | Het totale aantal uitvoeringen van de pijp lijn dat is geslaagd binnen een minuut venster. |
| TriggerCancelledRuns                  | Metrische gegevens over de trigger is geannuleerd            | Aantal    | Totaal                | Het totale aantal uitgevoerde triggers dat binnen een minuut venster is geannuleerd. |
| TriggerFailedRuns                    | Meet waarden voor uitvoering van mislukte triggers              | Aantal    | Totaal                | Het totale aantal uitvoeringen van triggers dat is mislukt binnen een minuut venster. |
| TriggerSucceededRuns                 | Meet waarden voor uitvoering van geslaagde triggers           | Aantal    | Totaal                | Het totale aantal uitvoeringen van triggers dat is geslaagd binnen een minuut venster. |
| SSISIntegrationRuntimeStartCancelled  | Geannuleerde metrische gegevens voor SSIS Integration runtime starten           | Aantal    | Totaal                | Het totale aantal SSIS-integratie runtime begint met het annuleren binnen een minuut venster. |
| SSISIntegrationRuntimeStartFailed    | Fout bij starten van metrische gegevens voor SSIS Integration runtime             | Aantal    | Totaal                | Het totale aantal SSIS-integratie runtime begint met een fout in een minuut venster. |
| SSISIntegrationRuntimeStartSucceeded | Start metrische gegevens van SSIS Integration runtime starten          | Aantal    | Totaal                | Het totale aantal SSIS-integratie runtime begint in een minuut venster. |
| SSISIntegrationRuntimeStopStuck      | Meet waarden voor stoppen van vastgelopen SSIS Integration runtime               | Aantal    | Totaal                | Het totale aantal SSIS Integration runtime stopt dat binnen een minuut venster is vastgelopen. |
| SSISIntegrationRuntimeStopSucceeded  | Meet waarden voor stoppen van SSIS Integration runtime           | Aantal    | Totaal                | Het totale aantal SSIS Integration runtime stopt in een minuut venster. |
| SSISPackageExecutionCancelled         | Metrische uitvoerings gegevens geannuleerd SSIS-pakket  | Aantal    | Totaal                | Het totale aantal uitvoeringen van SSIS-pakketten dat binnen een minuut venster is geannuleerd. |
| SSISPackageExecutionFailed           | Metrische gegevens voor uitvoering van SSIS-pakket mislukt    | Aantal    | Totaal                | Het totale aantal uitvoeringen SSIS-pakketten dat is mislukt binnen een minuut venster. |
| SSISPackageExecutionSucceeded        | Metrische uitvoerings gegevens geslaagd SSIS-pakket | Aantal    | Totaal                | Het totale aantal uitvoeringen van SSIS-pakketten dat is voltooid binnen een minuut venster. |

Volg de instructies in [Azure monitor data platform](../azure-monitor/data-platform.md)om toegang te krijgen tot de metrische gegevens.

> [!NOTE]
> Alleen gebeurtenissen uit voltooide, geactiveerde activiteiten en pijplijn uitvoeringen worden verzonden. Uitvoeringen en fout opsporing worden **niet** verzonden. Aan de andere kant worden gebeurtenissen van **alle** SSIS-pakket uitvoeringen verzonden, met inbegrip van de procedures die zijn voltooid en worden uitgevoerd, ongeacht hun aanroep methoden. U kunt bijvoorbeeld pakket uitvoeringen aanroepen op met Azure ingeschakelde SQL Server Data Tools (SSDT), via T-SQL op SSMS, SQL Server Agent of andere aangewezen hulpprogram ma's, en als geactiveerd of fout opsporing uitvoeren van SSIS-pakket activiteiten in ADF-pijp lijnen.

## <a name="data-factory-alerts"></a>Data Factory waarschuwingen

Meld u aan bij de Azure Portal en selecteer **monitor**  >  **waarschuwingen** om waarschuwingen te maken.

![Waarschuwingen in het menu van de portal](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Waarschuwingen maken

1. Selecteer **+ nieuwe waarschuwings regel** om een nieuwe waarschuwing te maken.

    ![Nieuwe waarschuwingsregel](media/monitor-using-azure-monitor/alerts_image4.png)

1. Definieer de waarschuwings voorwaarde.

    > [!NOTE]
    > Zorg ervoor dat u **alle** selecteert in de vervolg keuzelijst **filteren op resource type** .

    !["Waarschuwings voorwaarde definiëren" > "doel selecteren", waarmee het deel venster "een resource selecteren" wordt geopend ](media/monitor-using-azure-monitor/alerts_image5.png)

    ![' Geef een waarschuwings voorwaarde op ' > ' criteria toevoegen ', waarmee het deel venster ' signalerings logica configureren ' wordt geopend](media/monitor-using-azure-monitor/alerts_image6.png)

    ![Het deel venster signaal type configureren](media/monitor-using-azure-monitor/alerts_image7.png)

1. Definieer de details van de waarschuwing.

    ![Meldingsdetails](media/monitor-using-azure-monitor/alerts_image8.png)

1. Definieer de actie groep.

    ![Een regel maken, waarbij ' nieuwe actie groep ' is gemarkeerd](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Een nieuwe actie groep maken](media/monitor-using-azure-monitor/alerts_image10.png)

    ![E-mail, SMS, push en Voice configureren](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Een actiegroep definiëren](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="set-up-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Diagnostische logboeken instellen via de Azure Monitor REST API

### <a name="diagnostic-settings"></a>Diagnostische instellingen

Diagnostische instellingen gebruiken voor het configureren van Diagnostische logboeken voor niet-reken resources. De instellingen voor een resource-besturings element hebben de volgende kenmerken:

* Ze geven aan waar Diagnostische logboeken worden verzonden. Voor beelden zijn onder andere een Azure-opslag account, een Azure Event Hub of controle Logboeken.
* Ze geven aan welke logboek categorieën worden verzonden.
* Ze geven op hoe lang elke logboek categorie moet worden bewaard in een opslag account.
* Een waarde van nul voor de optie Retentie houdt in dat logboeken permanent worden bewaard. Anders kan de waarde een wille keurig aantal dagen van 1 tot en met 2.147.483.647 zijn.
* Als het Bewaar beleid is ingesteld, maar logboeken opslaat in een opslag account is uitgeschakeld, heeft het Bewaar beleid geen effect. Dit probleem kan bijvoorbeeld optreden wanneer alleen de opties Event Hubs of controle logboeken zijn geselecteerd.
* Bewaar beleid wordt per dag toegepast. De grens tussen dagen vindt plaats om middernacht Coordinated Universal Time (UTC). Aan het einde van de dag worden logboeken van dagen die het Bewaar beleid overschrijden, verwijderd. Als u bijvoorbeeld een Bewaar beleid van één dag hebt, wordt aan het begin van de huidige datum de logboeken van vóór gisteren verwijderd.

### <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Diagnostische logboeken inschakelen via de Azure Monitor REST API

#### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>Een diagnostische instelling in de monitor REST API maken of bijwerken

##### <a name="request"></a>Aanvraag

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Kopteksten

* Vervang `{api-version}` door `2016-09-01`.
* Vervang door `{resource-id}` de id van de resource waarvoor u de diagnostische instellingen wilt bewerken. Zie [resource groepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/manage-resource-groups-portal.md)voor meer informatie.
* Stel de `Content-Type` koptekst in op `application/json` .
* Stel de autorisatie-header in op het JSON-webtoken dat u hebt ontvangen van Azure Active Directory (Azure AD). Zie [verificatie aanvragen](../active-directory/develop/authentication-vs-authorization.md)voor meer informatie.

##### <a name="body"></a>Hoofdtekst

```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
}
```

| Eigenschap | Type | Description |
| --- | --- | --- |
| **storageAccountId** |Tekenreeks | De resource-ID van het opslag account waarnaar u Diagnostische logboeken wilt verzenden. |
| **Servicebusruleid kunnen** |Tekenreeks | De service bus-regel-ID van de service bus-naam ruimte waarin u Event Hubs wilt maken voor het streamen van Diagnostische logboeken. De regel-ID heeft de indeling `{service bus resource ID}/authorizationrules/{key name}` .|
| **workspaceId** | Complex type | Een matrix met metrische tijd korrels en het Bewaar beleid. De waarde van deze eigenschap is leeg. |
|**metrische gegevens**| Parameter waarden van de pijplijn uitvoering worden door gegeven aan de aangeroepen pijp lijn| Een JSON-object waarmee parameter namen worden toegewezen aan argument waarden. |
| **hout**| Complex type| De naam van een diagnostische logboek categorie voor een resource type. Als u de lijst met diagnostische logboek categorieën voor een resource wilt ophalen, voert u de bewerking Diagnostische instellingen ophalen uit. |
| **category**| Tekenreeks| Een matrix met logboek categorieën en het Bewaar beleid. |
| **timeGrain** | Tekenreeks | De granulatie van metrische gegevens, die worden vastgelegd in de ISO 8601-duur notatie. De waarde van de eigenschap moet `PT1M` een minuut zijn. |
| **ingeschakeld**| Booleaans | Hiermee wordt aangegeven of de verzameling van de categorie metrisch of logboek is ingeschakeld voor deze resource. |
| **retentionPolicy**| Complex type| Hierin wordt het Bewaar beleid voor een metrische of logboek categorie beschreven. Deze eigenschap wordt alleen gebruikt voor opslag accounts. |
|**resterende**| Int| Het aantal dagen dat de metrische gegevens of logboeken moeten worden bewaard. Als de waarde van de eigenschap 0 is, worden de logboeken permanent bewaard. Deze eigenschap wordt alleen gebruikt voor opslag accounts. |

##### <a name="response"></a>Antwoord

200 OK.

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

#### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>Informatie over diagnostische instellingen in de monitor REST API weer geven

##### <a name="request"></a>Aanvraag

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Kopteksten

* Vervang `{api-version}` door `2016-09-01`.
* Vervang door `{resource-id}` de id van de resource waarvoor u de diagnostische instellingen wilt bewerken. Zie [resource groepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/manage-resource-groups-portal.md)voor meer informatie.
* Stel de `Content-Type` koptekst in op `application/json` .
* Stel de autorisatie-header in op een JSON-webtoken dat u hebt ontvangen van Azure AD. Zie [verificatie aanvragen](../active-directory/develop/authentication-vs-authorization.md)voor meer informatie.

##### <a name="response"></a>Antwoord

200 OK.

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```
Zie [Diagnostische instellingen](/rest/api/monitor/diagnosticsettings)voor meer informatie.

## <a name="schema-of-logs-and-events"></a>Schema van Logboeken en gebeurtenissen

### <a name="monitor-schema"></a>Schema controleren

#### <a name="activity-run-log-attributes"></a>Activiteiten-logboek kenmerken uitvoeren

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties":
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| Eigenschap | Type | Beschrijving | Voorbeeld |
| --- | --- | --- | --- |
| **Afvlakking** |Tekenreeks | Het niveau van de diagnostische Logboeken. Stel de waarde van de eigenschap in op 4 voor de logboeken voor de uitvoering van de activiteit. | `4` |
| **correlationId** |Tekenreeks | De unieke ID voor het bijhouden van een bepaalde aanvraag. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | Tekenreeks | De tijd van de gebeurtenis in de UTC-notatie time span `YYYY-MM-DDTHH:MM:SS.00000Z` . | `2017-06-28T21:00:27.3534352Z` |
|**activityRunId**| Tekenreeks| De ID van de uitvoering van de activiteit. | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| Tekenreeks| De ID van de pijplijn uitvoering. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| Tekenreeks | De ID die is gekoppeld aan de Data Factory-resource. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| Tekenreeks | De categorie van de diagnostische Logboeken. Stel de waarde van de eigenschap in op `ActivityRuns` . | `ActivityRuns` |
|**niveau**| Tekenreeks | Het niveau van de diagnostische Logboeken. Stel de waarde van de eigenschap in op `Informational` . | `Informational` |
|**operationName**| Tekenreeks | De naam van de activiteit met de status. Als de activiteit de heartbeat start is, is de waarde van de eigenschap `MyActivity -` . Als de activiteit de end heartbeat is, is de waarde van de eigenschap `MyActivity - Succeeded` . | `MyActivity - Succeeded` |
|**pipelineName**| Tekenreeks | De naam van de pijp lijn. | `MyPipeline` |
|**activityName**| Tekenreeks | De naam van de activiteit. | `MyActivity` |
|**starten**| Tekenreeks | De begin tijd van de activiteit wordt uitgevoerd met de UTC-notatie time span. | `2017-06-26T20:55:29.5007959Z`|
|**endsystemen**| Tekenreeks | De eind tijd van de activiteit wordt uitgevoerd met de UTC-notatie time span. Als in het diagnostische logboek wordt aangegeven dat een activiteit is gestart, maar nog niet is beëindigd, is de waarde van de eigenschap `1601-01-01T00:00:00Z` . | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-log-attributes"></a>Pijp lijn-kenmerken van Logboeken uitvoeren

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties":
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| Eigenschap | Type | Beschrijving | Voorbeeld |
| --- | --- | --- | --- |
| **Afvlakking** |Tekenreeks | Het niveau van de diagnostische Logboeken. Stel de waarde van de eigenschap in op 4 voor de logboeken voor de uitvoering van de activiteit. | `4` |
| **correlationId** |Tekenreeks | De unieke ID voor het bijhouden van een bepaalde aanvraag. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | Tekenreeks | De tijd van de gebeurtenis in de UTC-notatie time span `YYYY-MM-DDTHH:MM:SS.00000Z` . | `2017-06-28T21:00:27.3534352Z` |
|**runId**| Tekenreeks| De ID van de pijplijn uitvoering. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| Tekenreeks | De ID die is gekoppeld aan de Data Factory-resource. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| Tekenreeks | De categorie van de diagnostische Logboeken. Stel de waarde van de eigenschap in op `PipelineRuns` . | `PipelineRuns` |
|**niveau**| Tekenreeks | Het niveau van de diagnostische Logboeken. Stel de waarde van de eigenschap in op `Informational` . | `Informational` |
|**operationName**| Tekenreeks | De naam van de pijp lijn samen met de status. Nadat de pijplijn uitvoering is voltooid, is de waarde van de eigenschap `Pipeline - Succeeded` . | `MyPipeline - Succeeded`. |
|**pipelineName**| Tekenreeks | De naam van de pijp lijn. | `MyPipeline` |
|**starten**| Tekenreeks | De begin tijd van de activiteit wordt uitgevoerd met de UTC-notatie time span. | `2017-06-26T20:55:29.5007959Z`. |
|**endsystemen**| Tekenreeks | De eind tijd van de activiteit wordt uitgevoerd met de UTC-notatie time span. Als in het diagnostische logboek wordt aangegeven dat een activiteit is gestart, maar nog niet is beëindigd, is de waarde van de eigenschap `1601-01-01T00:00:00Z` .  | `2017-06-26T20:55:29.5007959Z` |
|**status**| Tekenreeks | De laatste status van de pijplijn uitvoering. Mogelijke eigenschaps waarden zijn `Succeeded` en `Failed` . | `Succeeded`|

#### <a name="trigger-run-log-attributes"></a>Logboek kenmerken van de trigger-uitvoer

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
}
```

| Eigenschap | Type | Beschrijving | Voorbeeld |
| --- | --- | --- | --- |
| **Afvlakking** |Tekenreeks | Het niveau van de diagnostische Logboeken. Stel de waarde van de eigenschap in op 4 voor de logboeken voor de uitvoering van de activiteit. | `4` |
| **correlationId** |Tekenreeks | De unieke ID voor het bijhouden van een bepaalde aanvraag. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | Tekenreeks | De tijd van de gebeurtenis in de UTC-notatie time span `YYYY-MM-DDTHH:MM:SS.00000Z` . | `2017-06-28T21:00:27.3534352Z` |
|**triggerId**| Tekenreeks| De ID van de trigger uitvoering. | `08587023010602533858661257311` |
|**resourceId**| Tekenreeks | De ID die is gekoppeld aan de Data Factory-resource. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| Tekenreeks | De categorie van de diagnostische Logboeken. Stel de waarde van de eigenschap in op `PipelineRuns` . | `PipelineRuns` |
|**niveau**| Tekenreeks | Het niveau van de diagnostische Logboeken. Stel de waarde van de eigenschap in op `Informational` . | `Informational` |
|**operationName**| Tekenreeks | De naam van de trigger met de eind status, die aangeeft of de trigger is geactiveerd. Als de heartbeat is geslaagd, is de waarde van de eigenschap `MyTrigger - Succeeded` . | `MyTrigger - Succeeded` |
|**triggerName**| Tekenreeks | De naam van de trigger. | `MyTrigger` |
|**Trigger type**| Tekenreeks | Het type van de trigger. Mogelijke eigenschaps waarden zijn `Manual Trigger` en `Schedule Trigger` . | `ScheduleTrigger` |
|**triggerEvent**| Tekenreeks | Het gebeurtenis van de trigger. | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**starten**| Tekenreeks | De begin tijd van de trigger die wordt geactiveerd in timeas UTC-notatie. | `2017-06-26T20:55:29.5007959Z`|
|**status**| Tekenreeks | De uiteindelijke status die aangeeft of de trigger is geactiveerd. Mogelijke eigenschaps waarden zijn `Succeeded` en `Failed` . | `Succeeded`|

#### <a name="ssis-integration-runtime-log-attributes"></a>Kenmerken van SSIS Integration runtime-logboeken

Hier vindt u de logboek kenmerken van SSIS IR start/stop/onderhouds bewerkingen.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "resultType": "",
   "properties": {
      "message": ""
   },
   "resourceId": ""
}
```

| Eigenschap                   | Type   | Beschrijving                                                   | Voorbeeld                        |
| -------------------------- | ------ | ------------------------------------------------------------- | ------------------------------ |
| **time**                   | Tekenreeks | De tijd van de gebeurtenis in UTC-indeling: `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | Tekenreeks | De naam van uw SSIS-IR-bewerking                            | `Start/Stop/Maintenance` |
| **category**               | Tekenreeks | De categorie Diagnostische logboeken                               | `SSISIntegrationRuntimeLogs` |
| **correlationId**          | Tekenreeks | De unieke ID voor het bijhouden van een bepaalde bewerking             | `f13b159b-515f-4885-9dfa-a664e949f785Deprovision0059035558` |
| **dataFactoryName**        | Tekenreeks | De naam van de ADF                                          | `MyADFv2` |
| **integrationRuntimeName** | Tekenreeks | De naam van uw SSIS IR                                      | `MySSISIR` |
| **niveau**                  | Tekenreeks | Het niveau van Diagnostische logboeken                                  | `Informational` |
| **resultType**             | Tekenreeks | Het resultaat van uw SSIS IR-bewerking                          | `Started/InProgress/Succeeded/Failed` |
| **message**                | Tekenreeks | Het uitvoer bericht van uw SSIS IR-bewerking                  | `The stopping of your SSIS integration runtime has succeeded.` |
| **resourceId**             | Tekenreeks | De unieke ID van de ADF-resource                            | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-event-message-context-log-attributes"></a>Kenmerken van context logboek van SSIS-gebeurtenis berichten

Hier vindt u de logboek kenmerken van voor waarden met betrekking tot gebeurtenis berichten die door SSIS-pakket uitvoeringen op uw SSIS-IR worden gegenereerd. Ze geven soort gelijke informatie als de [context tabel of weer gave van de gebeurtenis bericht van de SSIS-catalogus (SSISDB)](/sql/integration-services/system-views/catalog-event-message-context) waarin runtime waarden van veel SSIS-pakket eigenschappen worden weer gegeven. Ze worden gegenereerd wanneer u `Basic/Verbose` logboek registratie niveau selecteert en nuttig is voor het controleren op fouten.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "contextDepth": "",
      "packagePath": "",
      "contextType": "",
      "contextSourceName": "",
      "contextSourceId": "",
      "propertyName": "",
      "propertyValue": ""
   },
   "resourceId": ""
}
```

| Eigenschap                   | Type   | Beschrijving                                                          | Voorbeeld                        |
| -------------------------- | ------ | -------------------------------------------------------------------- | ------------------------------ |
| **time**                   | Tekenreeks | De tijd van de gebeurtenis in UTC-indeling: `YYYY-MM-DDTHH:MM:SS.00000Z`        | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | Tekenreeks | Dit is ingesteld op `YourSSISIRName-SSISPackageEventMessageContext`       | `mysqlmissisir-SSISPackageEventMessageContext` |
| **category**               | Tekenreeks | De categorie Diagnostische logboeken                                      | `SSISPackageEventMessageContext` |
| **correlationId**          | Tekenreeks | De unieke ID voor het bijhouden van een bepaalde bewerking                    | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | Tekenreeks | De naam van de ADF                                                 | `MyADFv2` |
| **integrationRuntimeName** | Tekenreeks | De naam van uw SSIS IR                                             | `MySSISIR` |
| **niveau**                  | Tekenreeks | Het niveau van Diagnostische logboeken                                         | `Informational` |
| **operationId**            | Tekenreeks | De unieke ID voor het bijhouden van een bepaalde bewerking in SSISDB          | `1` (1 betekent bewerkingen met betrekking tot pakketten die **niet** zijn opgeslagen in SSISDB/aangeroepen via T-SQL) |
| **contextDepth**           | Tekenreeks | De diepte van de context van uw gebeurtenis bericht                              | `0` (0 geeft de context aan voordat de uitvoering van het pakket wordt gestart, 1 geeft de context aan als er een fout optreedt, en neemt toe naarmate de context verder van de fout is) |
| **packagePath**            | Tekenreeks | Het pad van het pakket object als de context bron van uw gebeurtenis bericht      | `\Package` |
| **contextType**            | Tekenreeks | Het type pakket object als de context bron van het gebeurtenis bericht      | `60`(Zie [meer context typen](/sql/integration-services/system-views/catalog-event-message-context#remarks)) |
| **contextSourceName**      | Tekenreeks | De naam van het pakket object als de context bron van het gebeurtenis bericht      | `MyPackage` |
| **contextSourceId**        | Tekenreeks | De unieke ID van het pakket object als de context bron van uw gebeurtenis bericht | `{E2CF27FB-EA48-41E9-AF6F-3FE938B4ADE1}` |
| **propertyName**           | Tekenreeks | De naam van de pakket eigenschap voor de context bron van uw gebeurtenis bericht   | `DelayValidation` |
| **Tegelijkertijd**          | Tekenreeks | De waarde van de eigenschap package voor de context bron van uw gebeurtenis bericht  | `False` |
| **resourceId**             | Tekenreeks | De unieke ID van de ADF-resource                                   | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-event-messages-log-attributes"></a>Logboek kenmerken van SSIS-gebeurtenis berichten

Hier vindt u de logboek kenmerken van gebeurtenis berichten die door SSIS-pakket uitvoeringen op uw SSIS-IR worden gegenereerd. Ze geven soort gelijke informatie als [SSISDB gebeurtenis berichten tabel of weer gave](/sql/integration-services/system-views/catalog-event-messages) waarin de gedetailleerde tekst/meta gegevens van gebeurtenis berichten worden weer gegeven. Ze worden gegenereerd op basis van elk registratie niveau, behalve `None` .

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "messageTime": "",
      "messageType": "",
      "messageSourceType": "",
      "message": "",
      "packageName": "",
      "eventName": "",
      "messageSourceName": "",
      "messageSourceId": "",
      "subcomponentName": "",
      "packagePath": "",
      "executionPath": "",
      "threadId": ""
   }
}
```

| Eigenschap                   | Type   | Beschrijving                                                        | Voorbeeld                        |
| -------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **time**                   | Tekenreeks | De tijd van de gebeurtenis in UTC-indeling: `YYYY-MM-DDTHH:MM:SS.00000Z`      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | Tekenreeks | Dit is ingesteld op `YourSSISIRName-SSISPackageEventMessages`           | `mysqlmissisir-SSISPackageEventMessages` |
| **category**               | Tekenreeks | De categorie Diagnostische logboeken                                    | `SSISPackageEventMessages` |
| **correlationId**          | Tekenreeks | De unieke ID voor het bijhouden van een bepaalde bewerking                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | Tekenreeks | De naam van de ADF                                               | `MyADFv2` |
| **integrationRuntimeName** | Tekenreeks | De naam van uw SSIS IR                                           | `MySSISIR` |
| **niveau**                  | Tekenreeks | Het niveau van Diagnostische logboeken                                       | `Informational` |
| **operationId**            | Tekenreeks | De unieke ID voor het bijhouden van een bepaalde bewerking in SSISDB        | `1` (1 betekent bewerkingen met betrekking tot pakketten die **niet** zijn opgeslagen in SSISDB/aangeroepen via T-SQL) |
| **messageTime**            | Tekenreeks | Het tijdstip waarop uw gebeurtenis bericht is gemaakt in UTC-indeling          | `2017-06-28T21:00:27.3534352Z` |
| **Message type**            | Tekenreeks | Het type gebeurtenis bericht                                     | `70`( [meer bericht typen](/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database#remarks)weer geven) |
| **messageSourceType**      | Tekenreeks | Het type van de bron van het gebeurtenis bericht                              | `20`(Zie [meer bron typen van berichten](/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database#remarks)) |
| **message**                | Tekenreeks | De tekst van uw gebeurtenis bericht                                     | `MyPackage:Validation has started.` |
| **packageName**            | Tekenreeks | De naam van het uitgevoerde pakket bestand                             | `MyPackage.dtsx` |
| **eventName**              | Tekenreeks | De naam van de gerelateerde runtime gebeurtenis                                 | `OnPreValidate` |
| **messageSourceName**      | Tekenreeks | De naam van het pakket onderdeel als bron van het gebeurtenis bericht         | `Data Flow Task` |
| **messageSourceId**        | Tekenreeks | De unieke ID van het pakket onderdeel als bron van het gebeurtenis bericht    | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |
| **subonderdeelnaam**       | Tekenreeks | De naam van de gegevens stroom component als bron van het gebeurtenis bericht       | `SSIS.Pipeline` |
| **packagePath**            | Tekenreeks | Het pad van het pakket object als bron van het gebeurtenis bericht            | `\Package\Data Flow Task` |
| **executionPath**          | Tekenreeks | Het volledige pad van het bovenliggende pakket naar het uitgevoerde onderdeel            | `\Transformation\Data Flow Task` (In dit pad worden ook onderdeel herhalingen vastgelegd) |
| **Thread**               | Tekenreeks | De unieke ID van de thread die wordt uitgevoerd wanneer uw gebeurtenis bericht wordt geregistreerd | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |

#### <a name="ssis-executable-statistics-log-attributes"></a>Logboek kenmerken van de SSIS-uitvoer bare statistieken

Dit zijn de logboek kenmerken van uitvoer bare gegevens die worden gegenereerd door SSIS-pakket uitvoeringen op uw SSIS-IR, waarbij uitvoer bare containers of taken in de controle stroom van pakketten zijn. Ze geven soort gelijke informatie als [SSISDB uitvoer bare statistieken tabel of weer gave](/sql/integration-services/system-views/catalog-executable-statistics) waarin een rij wordt weer gegeven voor elk uitgevoerd uitvoer bare bestand, met inbegrip van de herhalingen. Ze worden gegenereerd met elk logboek registratie niveau, behalve `None` en nuttig voor het identificeren van knel punten op taak niveau of fouten.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "executionPath": "",
      "startTime": "",
      "endTime": "",
      "executionDuration": "",
      "executionResult": "",
      "executionValue": ""
   },
   "resourceId": ""
}
```

| Eigenschap                   | Type   | Beschrijving                                                      | Voorbeeld                        |
| -------------------------- | ------ | ---------------------------------------------------------------- | ------------------------------ |
| **time**                   | Tekenreeks | De tijd van de gebeurtenis in UTC-indeling: `YYYY-MM-DDTHH:MM:SS.00000Z`    | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | Tekenreeks | Dit is ingesteld op `YourSSISIRName-SSISPackageExecutableStatistics`  | `mysqlmissisir-SSISPackageExecutableStatistics` |
| **category**               | Tekenreeks | De categorie Diagnostische logboeken                                  | `SSISPackageExecutableStatistics` |
| **correlationId**          | Tekenreeks | De unieke ID voor het bijhouden van een bepaalde bewerking                | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | Tekenreeks | De naam van de ADF                                             | `MyADFv2` |
| **integrationRuntimeName** | Tekenreeks | De naam van uw SSIS IR                                         | `MySSISIR` |
| **niveau**                  | Tekenreeks | Het niveau van Diagnostische logboeken                                     | `Informational` |
| **Executionid is vereist**            | Tekenreeks | De unieke ID voor het bijhouden van een bepaalde uitvoering in SSISDB      | `1` (1 betekent uitvoeringen gerelateerd aan pakketten die **niet** zijn opgeslagen in SSISDB/aangeroepen via T-SQL) |
| **executionPath**          | Tekenreeks | Het volledige pad van het bovenliggende pakket naar het uitgevoerde onderdeel          | `\Transformation\Data Flow Task` (In dit pad worden ook onderdeel herhalingen vastgelegd) |
| **startTime**              | Tekenreeks | Het tijdstip waarop het uitvoer bare fase vooraf wordt uitgevoerd in de UTC-indeling  | `2017-06-28T21:00:27.3534352Z` |
| **Tijd**                | Tekenreeks | Het tijdstip waarop het uitvoer bare post-Execute-fase in UTC-indeling wordt ingevoerd | `2017-06-28T21:00:27.3534352Z` |
| **executionDuration**      | Tekenreeks | De uitvoerings tijd van het uitvoer bare bestand in milliseconden                   | `1,125` |
| **executionResult**        | Tekenreeks | Het resultaat van het uitvoeren van een uitvoerbaar bestand                                 | `0` (0 betekent geslaagd, 1 geeft aan dat er een fout is opgetreden, 2 geeft aan dat de waarde is voltooid en 3 betekent dat de annulering wordt geannuleerd) |
| **executionValue**         | Tekenreeks | De door de gebruiker gedefinieerde waarde die door het uitvoeren van het uitvoer bare bestand is geretourneerd            | `1` |
| **resourceId**             | Tekenreeks | De unieke ID van de ADF-resource                               | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-execution-component-phases-log-attributes"></a>Logboek kenmerken van fasen van SSIS-uitvoerings onderdelen

Hier vindt u de logboek kenmerken van runtime-statistieken voor gegevens stroom onderdelen die worden gegenereerd door SSIS-pakket uitvoeringen op uw SSIS-IR. Ze geven soort gelijke informatie als de [tabel of weer gave met SSISDB-uitvoerings onderdelen](/sql/integration-services/system-views/catalog-execution-component-phases) waarin de tijd wordt weer gegeven die door de onderdelen van de gegevens stroom in al hun uitvoerings fasen wordt besteed. Ze worden gegenereerd wanneer u `Performance/Verbose` logboek registratie niveau selecteert en nuttig is voor het vastleggen van uitvoerings statistieken voor de gegevens stroom.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "subcomponentName": "",
      "phase": "",
      "startTime": "",
      "endTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| Eigenschap                   | Type   | Beschrijving                                                         | Voorbeeld                        |
| -------------------------- | ------ | ------------------------------------------------------------------- | ------------------------------ |
| **time**                   | Tekenreeks | De tijd van de gebeurtenis in UTC-indeling: `YYYY-MM-DDTHH:MM:SS.00000Z`       | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | Tekenreeks | Dit is ingesteld op `YourSSISIRName-SSISPackageExecutionComponentPhases` | `mysqlmissisir-SSISPackageExecutionComponentPhases` |
| **category**               | Tekenreeks | De categorie Diagnostische logboeken                                     | `SSISPackageExecutionComponentPhases` |
| **correlationId**          | Tekenreeks | De unieke ID voor het bijhouden van een bepaalde bewerking                   | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | Tekenreeks | De naam van de ADF                                                | `MyADFv2` |
| **integrationRuntimeName** | Tekenreeks | De naam van uw SSIS IR                                            | `MySSISIR` |
| **niveau**                  | Tekenreeks | Het niveau van Diagnostische logboeken                                        | `Informational` |
| **Executionid is vereist**            | Tekenreeks | De unieke ID voor het bijhouden van een bepaalde uitvoering in SSISDB         | `1` (1 betekent uitvoeringen gerelateerd aan pakketten die **niet** zijn opgeslagen in SSISDB/aangeroepen via T-SQL) |
| **packageName**            | Tekenreeks | De naam van het uitgevoerde pakket bestand                              | `MyPackage.dtsx` |
| **taskName**               | Tekenreeks | De naam van de uitgevoerde gegevens stroom taak                                 | `Data Flow Task` |
| **subonderdeelnaam**       | Tekenreeks | De naam van het gegevens stroom onderdeel                                     | `Derived Column` |
| **Partition**                  | Tekenreeks | De naam van de uitvoerings fase                                         | `AcquireConnections` |
| **startTime**              | Tekenreeks | Het tijdstip waarop uitvoerings fase wordt gestart in UTC-indeling                  | `2017-06-28T21:00:27.3534352Z` |
| **Tijd**                | Tekenreeks | Het tijdstip waarop de uitvoerings fase eindigt op de UTC-indeling                    | `2017-06-28T21:00:27.3534352Z` |
| **executionPath**          | Tekenreeks | Het pad van de uitvoering van de gegevens stroom taak                            | `\Transformation\Data Flow Task` |
| **resourceId**             | Tekenreeks | De unieke ID van de ADF-resource                                  | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-execution-data-statistics-log-attributes"></a>Logboek kenmerken statistieken uitvoerings gegevens SSIS

Dit zijn de logboek kenmerken van gegevens verplaatsing via elke leg van gegevens stroom pijplijnen, van upstream naar downstream-onderdelen, die worden gegenereerd door SSIS-pakket uitvoeringen op uw SSIS-IR. Ze geven soort gelijke informatie als de [Statistieken tabel van de SSISDB-uitvoerings gegevens of weer gave](/sql/integration-services/system-views/catalog-execution-data-statistics) waarin het aantal rijen van de gegevens die worden verplaatst via gegevens stroom taken worden weer gegeven. Ze worden gegenereerd wanneer u `Verbose` logboek registratie niveau selecteert en nuttig is voor het berekenen van de door Voer van gegevens stromen.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "dataflowPathIdString": "",
      "dataflowPathName": "",
      "sourceComponentName": "",
      "destinationComponentName": "",
      "rowsSent": "",
      "createdTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| Eigenschap                     | Type   | Beschrijving                                                        | Voorbeeld                        |
| ---------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **time**                     | Tekenreeks | De tijd van de gebeurtenis in UTC-indeling: `YYYY-MM-DDTHH:MM:SS.00000Z`      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**            | Tekenreeks | Dit is ingesteld op `YourSSISIRName-SSISPackageExecutionDataStatistics` | `mysqlmissisir-SSISPackageExecutionDataStatistics` |
| **category**                 | Tekenreeks | De categorie Diagnostische logboeken                                    | `SSISPackageExecutionDataStatistics` |
| **correlationId**            | Tekenreeks | De unieke ID voor het bijhouden van een bepaalde bewerking                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**          | Tekenreeks | De naam van de ADF                                               | `MyADFv2` |
| **integrationRuntimeName**   | Tekenreeks | De naam van uw SSIS IR                                           | `MySSISIR` |
| **niveau**                    | Tekenreeks | Het niveau van Diagnostische logboeken                                       | `Informational` |
| **Executionid is vereist**              | Tekenreeks | De unieke ID voor het bijhouden van een bepaalde uitvoering in SSISDB        | `1` (1 betekent uitvoeringen gerelateerd aan pakketten die **niet** zijn opgeslagen in SSISDB/aangeroepen via T-SQL) |
| **packageName**              | Tekenreeks | De naam van het uitgevoerde pakket bestand                             | `MyPackage.dtsx` |
| **taskName**                 | Tekenreeks | De naam van de uitgevoerde gegevens stroom taak                                | `Data Flow Task` |
| **dataflowPathIdString**     | Tekenreeks | De unieke ID voor het pad naar de tracerings gegevensstroom                          | `Paths[SQLDB Table3.ADO NET Source Output]` |
| **dataflowPathName**         | Tekenreeks | De naam van het pad naar de gegevens stroom                                         | `ADO NET Source Output` |
| **sourceComponentName**      | Tekenreeks | De naam van het gegevens stroom onderdeel dat gegevens verzendt                    | `SQLDB Table3` |
| **destinationComponentName** | Tekenreeks | De naam van het gegevens stroom onderdeel waarmee gegevens worden ontvangen                 | `Derived Column` |
| **rowsSent**                 | Tekenreeks | Het aantal rijen dat is verzonden door het bron onderdeel                        | `500` |
| **createdTime**              | Tekenreeks | De tijd waarop rijwaarden worden opgehaald in UTC-indeling                | `2017-06-28T21:00:27.3534352Z` |
| **executionPath**            | Tekenreeks | Het pad van de uitvoering van de gegevens stroom taak                           | `\Transformation\Data Flow Task` |
| **resourceId**               | Tekenreeks | De unieke ID van de ADF-resource                                 | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

### <a name="log-analytics-schema"></a>Log Analytics schema

Log Analytics neemt het schema over van monitor met de volgende uitzonde ringen:

* De eerste letter in elke kolom naam wordt gekapitaliseerd. De kolom naam ' correlationId ' in de monitor is bijvoorbeeld ' CorrelationId ' in Log Analytics.
* Er is geen kolom van het niveau.
* De dynamische kolom ' Eigenschappen ' wordt bewaard als het volgende dynamische JSON-BLOB-type.

    | Azure Monitor kolom | Log Analytics kolom | Type |
    | --- | --- | --- |
    | $. Properties. UserProperties | UserProperties | Dynamisch |
    | $. Properties. Aantekeningen | Aantekeningen | Dynamisch |
    | $. Properties. Ingevoerd | Invoer | Dynamisch |
    | $. Properties. Uitvoer | Uitvoer | Dynamisch |
    | $. Properties. Fout. error code | ErrorCode | int |
    | $. Properties. Fout. Message | ErrorMessage | tekenreeks |
    | $. Properties. Optreedt | Fout | Dynamisch |
    | $. Properties. Voorgangers dikwijls werden | Voorgangers dikwijls werden | Dynamisch |
    | $. Properties. Instellen | Parameters | Dynamisch |
    | $.properties.SystemParameters | SystemParameters | Dynamisch |
    | $. Properties. Koptags | Tags | Dynamisch |

## <a name="monitor-ssis-operations-with-azure-monitor"></a>SSIS-bewerkingen bewaken met Azure Monitor

Als u uw SSIS-werk belastingen wilt & verplaatsen, kunt u [SSIS IR inrichten in ADF](./tutorial-deploy-ssis-packages-azure.md) die ondersteuning biedt voor:

- Pakketten die zijn geïmplementeerd in SSIS Catalog (SSISDB) die worden gehost door een server of beheerd exemplaar van Azure SQL Database (projectimplementatiemodel)
- Pakketten die zijn geïmplementeerd in het bestandssysteem, Azure Files of SQL Server-database (MSDB) die worden gehost door Azure SQL Managed Instance (pakketimplementatiemodel)

Wanneer u eenmaal hebt ingericht, kunt u de [operationele status van SSIS IR controleren met behulp van Azure PowerShell of op de hub **bewaken** van de ADF-Portal](./monitor-integration-runtime.md#azure-ssis-integration-runtime). Met het project implementatie model worden SSIS-pakket uitvoerings Logboeken opgeslagen in SSISDB interne tabellen of weer gaven, zodat u ze kunt opvragen, analyseren en visueel weer geven met behulp van aangewezen hulpprogram ma's als SSMS. Met het pakket implementatie model kunnen SSIS-uitvoer logboeken worden opgeslagen in bestands systeem of Azure Files als CSV-bestanden die u nog steeds moet parseren en verwerken met andere aangewezen hulpprogram ma's voordat u ze kunt opvragen, analyseren en visueel weer geven.

Nu met [Azure monitor](../azure-monitor/data-platform.md) -integratie kunt u alle metrische gegevens en logboeken die zijn gegenereerd door SSIS IR-bewerkingen en SSIS-pakket uitvoeringen, opvragen, analyseren en visueel weer geven op Azure Portal. Daarnaast kunt u ook waarschuwingen genereren.

### <a name="configure-diagnostic-settings-and-workspace-for-ssis-operations"></a>Diagnostische instellingen en werk ruimte voor SSIS-bewerkingen configureren

Als u alle metrische gegevens en logboeken die zijn gegenereerd door SSIS IR-bewerkingen en SSIS-pakket uitvoeringen wilt verzenden naar Azure Monitor, moet u [Diagnostische instellingen en werk ruimte voor uw ADF configureren](#configure-diagnostic-settings-and-workspace).

### <a name="ssis-operational-metrics"></a>Operationele metrische gegevens voor SSIS

SSIS operationele [metrische gegevens](../azure-monitor/essentials/data-platform-metrics.md) zijn prestatie meter items of numerieke waarden die de status van de start-en stop bewerkingen van SSIS IR beschrijven, evenals de uitvoeringen van SSIS-pakketten op een bepaald moment. Ze maken deel uit van [ADF-metrische gegevens in azure monitor](#data-factory-metrics).

Wanneer u Diagnostische instellingen en werk ruimte voor uw ADF op Azure Monitor configureert en u het selectie vakje _AllMetrics_ inschakelt, krijgen SSIS operationele metrische gegevens beschikbaar voor [interactieve analyse met behulp van Azure Metrics Explorer](../azure-monitor/essentials/metrics-getting-started.md), [presentatie op Azure-dash board](../azure-monitor/app/tutorial-app-dashboards.md)en [bijna real time-waarschuwingen](../azure-monitor/alerts/alerts-metric.md).

![Geef uw instellingen een naam en selecteer een werk ruimte voor logboek analyse](media/data-factory-monitor-oms/monitor-oms-image2.png)

### <a name="ssis-operational-alerts"></a>Operationele waarschuwingen voor SSIS

Als u waarschuwingen wilt genereren over de operationele metrische gegevens van SSIS vanuit de ADF-Portal, [selecteert u de pagina **waarschuwingen & metrische gegevens** van ADF **monitor** hub en volgt u de stapsgewijze instructies](./monitor-visually.md#alerts).

![Operationele waarschuwingen van SSIS verhogen vanuit de ADF-Portal](media/data-factory-monitor-oms/data-factory-monitor-alerts-ssis.png)

Als u waarschuwingen wilt genereren op basis van de SSIS-metrische gegevens van Azure Portal, [selecteert u de pagina **waarschuwingen** van de hub van Azure **monitor** en volgt u de stapsgewijze instructies](#data-factory-alerts).

![SSIS-operationele waarschuwingen van Azure Portal verhogen](media/data-factory-monitor-oms/azure-monitor-alerts-ssis.png)

### <a name="ssis-operational-logs"></a>SSIS-operationele logboeken

SSIS-operationele [Logboeken](../azure-monitor/logs/data-platform-logs.md) zijn gebeurtenissen die worden gegenereerd door SSIS IR-bewerkingen en SSIS-pakket uitvoeringen die voldoende context bieden voor geïdentificeerde problemen en die nuttig zijn voor analyse van hoofd oorzaken. 

Wanneer u Diagnostische instellingen en werk ruimte voor uw ADF op Azure Monitor configureert, kunt u de relevante SSIS-operationele logboeken selecteren en deze naar Log Analytics op basis van Azure Data Explorer verzenden. In dat geval worden ze beschikbaar gesteld voor [analyse met behulp van uitgebreide query taal](../azure-monitor/logs/log-query-overview.md), [presentatie op Azure-dash board](../azure-monitor/app/tutorial-app-dashboards.md)en [nabije real-time waarschuwingen](../azure-monitor/alerts/alerts-log.md).

![Geef uw instellingen een naam en selecteer een werk ruimte voor logboek analyse](media/data-factory-monitor-oms/monitor-oms-image2.png)

De schema's en inhoud van de SSIS-logboeken voor het uitvoeren van een pakket in Azure Monitor en Log Analytics lijken op de schema's van interne tabellen of weer gaven van SSISDB.

| Azure Monitor-logboek Categorieën          | Log Analytics tabellen                     | SSISDB interne tabellen/weer gaven              |
| ------------------------------------- | ---------------------------------------- | ----------------------------------------- |
| `SSISIntegrationRuntimeLogs`          | `ADFSSISIntegrationRuntimeLogs`          |                                           |
| `SSISPackageEventMessageContext`      | `ADFSSISPackageEventMessageContext`      | `[internal].[event_message_context]`      |
| `SSISPackageEventMessages`            | `ADFSSISPackageEventMessages`            | `[internal].[event_messages]`             |
| `SSISPackageExecutableStatistics`     | `ADFSSISPackageExecutableStatistics`     | `[internal].[executable_statistics]`      |
| `SSISPackageExecutionComponentPhases` | `ADFSSISPackageExecutionComponentPhases` | `[internal].[execution_component_phases]` |
| `SSISPackageExecutionDataStatistics`  | `ADFSSISPackageExecutionDataStatistics`  | `[internal].[execution_data_statistics]`  |

Zie [Azure monitor en log Analytics schema's voor ADF](#schema-of-logs-and-events)voor meer informatie over de kenmerken/eigenschappen van het SSIS-operationele logboek.

De geselecteerde SSIS-pakket uitvoerings logboeken worden altijd naar Log Analytics verzonden, ongeacht hun aanroep methoden. U kunt bijvoorbeeld pakket uitvoeringen aanroepen op Azure-SSDT, via T-SQL op SSMS, SQL Server Agent of andere aangewezen hulpprogram ma's, en als triggered of debug uitvoering van SSIS-pakket activiteiten uitvoeren in ADF-pijp lijnen.

Bij het uitvoeren van query's op SSIS IR-bewerkings logboeken op Logboeken kunt u de **bewerkings** -en **ResultType** -eigenschappen gebruiken die zijn ingesteld op `Start/Stop/Maintenance` en `Started/InProgress/Succeeded/Failed` respectievelijk. 

![Query's uitvoeren op SSIS IR-bewerkings logboeken op Log Analytics](media/data-factory-monitor-oms/log-analytics-query.png)

Bij het uitvoeren van query's op SSIS-pakket uitvoer Logboeken in Logboeken, kunt u deze koppelen met behulp van de **OperationId** / **executionid is vereist** / **CorrelationId** -eigenschappen. **OperationId** / **Executionid is vereist** zijn altijd ingesteld op `1` voor alle bewerkingen/uitvoeringen die zijn gerelateerd aan pakketten die **niet** zijn opgeslagen in SSISDB/aangeroepen via T-SQL.

![Uitvoerings logboeken van SSIS-pakketten uitvoeren op Log Analytics](media/data-factory-monitor-oms/log-analytics-query2.png)

## <a name="next-steps"></a>Volgende stappen
[Pijp lijnen programmatisch controleren en beheren](monitor-programmatically.md)
