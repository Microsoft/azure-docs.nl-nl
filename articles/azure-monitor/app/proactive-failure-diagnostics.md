---
title: Slimme detectie-fout afwijkingen, in Application Insights | Microsoft Docs
description: Hiermee wordt u gewaarschuwd voor ongebruikelijke wijzigingen in de frequentie van mislukte aanvragen voor uw web-app en diagnostische analyse. Er is geen configuratie nodig.
ms.topic: conceptual
ms.date: 12/18/2018
ms.reviewer: yalavi
ms.openlocfilehash: 0f4de3aaba4acf86df37048134089326196e87ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100587542"
---
# <a name="smart-detection---failure-anomalies"></a>Slimme detectie-fout afwijkingen
[Application Insights](./app-insights-overview.md) waarschuwt u automatisch in bijna realtime als uw web-app een abnormale toename in de frequentie van mislukte aanvragen ondervindt. Er wordt een ongebruikelijke toename gedetecteerd met het aantal HTTP-aanvragen of afhankelijkheids aanroepen dat als mislukt wordt gerapporteerd. Voor aanvragen bevat mislukte aanvragen meestal antwoord codes van 400 of hoger. Om u te helpen het probleem te sorteren en te diagnosticeren, wordt een analyse van de kenmerken van de fouten en gerelateerde toepassings gegevens weer gegeven in de waarschuwings Details. Er zijn ook koppelingen naar de Application Insights portal voor verdere diagnose. De functie heeft geen instellingen of configuratie nodig, omdat deze machine learning algoritmen gebruikt om het normale fout aantal te voors pellen.

Deze functie werkt voor elke web-app, die wordt gehost in de Cloud of op uw eigen servers, waarmee toepassings aanvragen of afhankelijkheids gegevens worden gegenereerd. Als u bijvoorbeeld een werknemersrol hebt die [TrackRequest ()](./api-custom-events-metrics.md#trackrequest) of [TrackDependency ()](./api-custom-events-metrics.md#trackdependency)aanroept.

Na het instellen van [Application Insights voor uw project](./app-insights-overview.md), en als uw app een bepaalde minimale hoeveelheid gegevens genereert, neemt Slimme detectie van fout afwijkingen 24 uur in beslag om het normale gedrag van uw app te achterhalen, voordat deze wordt ingeschakeld en waarschuwingen kan verzenden.

Hier volgt een voor beeld van een waarschuwing:

:::image type="content" source="./media/proactive-failure-diagnostics/013.png" alt-text="Voor beeld van een Smart detection-waarschuwing met analyse van het cluster." lightbox="./media/proactive-failure-diagnostics/013.png":::

In de details van de waarschuwing ziet u het volgende:

* De fout frequentie vergeleken met het gedrag van de normale app.
* Het aantal gebruikers waarop dit van toepassing is, zodat u weet hoeveel u zich kunt voordoen.
* Een karakteristiek patroon dat is gekoppeld aan de fouten. In dit voor beeld is er een bepaalde antwoord code, aanvraag naam (Operation) en toepassings versie. Zo weet u meteen waar u moet beginnen met het zoeken naar uw code. Andere mogelijkheden zijn mogelijk een specifieke browser of client besturingssysteem.
* De uitzonde ring, logboek traceringen en afhankelijkheids fout (data bases of andere externe onderdelen) die moeten worden gekoppeld aan de getekende fouten.
* Hiermee worden rechtstreeks koppelingen naar relevante Zoek opdrachten op de gegevens in Application Insights.

## <a name="benefits-of-smart-detection"></a>Voor delen van Slimme detectie
Normale [metrische waarschuwingen](../alerts/alerts-log.md) geven aan dat er mogelijk een probleem is. Met Slimme detectie wordt het diagnostische werk voor u gestart, waarbij veel van de analyse wordt uitgevoerd die u anders zou moeten uitvoeren. U krijgt de resultaten die u netjes kunt bundelen, zodat u snel aan de slag kunt met het probleem.

## <a name="how-it-works"></a>Uitleg
Slimme detectie bewaakt de gegevens die van uw app zijn ontvangen, met name de uitval snelheid. Deze regel telt het aantal aanvragen waarvoor de `Successful request` eigenschap False is en het aantal afhankelijkheids aanroepen waarvoor de `Successful call` eigenschap False is. Voor aanvragen standaard `Successful request == (resultCode < 400)` (tenzij u aangepaste code hebt geschreven om uw eigen [TrackRequest](./api-custom-events-metrics.md#trackrequest) -aanroepen te [filteren](./api-filtering-sampling.md#filtering) of te genereren). 

De prestaties van uw app hebben een typisch patroon van het gedrag. Sommige aanvragen of afhankelijkheids aanroepen zijn gevoelig voor fouten dan andere. en het totale aantal fouten kan optreden naarmate de belasting toeneemt. Slimme detectie maakt gebruik van machine learning om deze afwijkingen te vinden.

Wanneer gegevens worden geleverd in Application Insights van uw web-app, vergelijkt Slimme detectie het huidige gedrag met de patronen die in de afgelopen paar dagen zijn waargenomen. Als een abnormale toename van het aantal fouten wordt waargenomen door vergelijking met de vorige prestaties, wordt een analyse geactiveerd.

Wanneer een analyse wordt geactiveerd, voert de service een cluster analyse uit op de mislukte aanvraag, om te proberen een patroon van waarden te identificeren dat de fouten kenmerkt. 

In het bovenstaande voor beeld heeft de analyse ontdekt dat de meeste fouten betrekking hebben op een specifieke resultaat code, aanvraag naam, server-URL-host en rolinstantie. 

Als uw service wordt gebruikt met deze aanroepen, zoekt de Analyzer naar een uitzonde ring en een afhankelijkheids fout die is gekoppeld aan aanvragen in het cluster dat is geïdentificeerd, samen met een voor beeld van traceer logboeken die zijn gekoppeld aan deze aanvragen.

De resulterende analyse wordt naar u verzonden als waarschuwing, tenzij u deze hebt geconfigureerd.

Net als de [waarschuwingen die u hand matig hebt ingesteld](../alerts/alerts-log.md), kunt u de status van de geactiveerde waarschuwing inspecteren, die kan worden opgelost als het probleem is opgelost. Configureer de waarschuwings regels op de pagina waarschuwingen van uw Application Insights-resource. Maar in tegens telling tot andere waarschuwingen hoeft u geen slimme detectie in te stellen of te configureren. Als u wilt, kunt u deze uitschakelen of de e-mail adressen van het doel wijzigen.

### <a name="alert-logic-details"></a>Details van waarschuwings logica

De waarschuwingen worden geactiveerd door onze eigen machine learning-algoritme, zodat de details van de exacte implementatie niet kunnen worden gedeeld. We begrijpen dat u soms meer moet weten over de werking van de onderliggende logica. De primaire factoren die worden geëvalueerd om te bepalen of een waarschuwing moet worden geactiveerd zijn: 

* Analyse van het percentage mislukte aanvragen/afhankelijkheden in een rollend venster van 20 minuten.
* Een vergelijking van het uitval percentage van de laatste 20 minuten tot het percentage in de afgelopen 40 minuten en de afgelopen zeven dagen en op zoek naar aanzienlijke afwijkingen die de X-tijden van de standaard deviatie overschrijden.
* Het gebruik van een adaptieve limiet voor het minimale uitval percentage, dat varieert op basis van het volume van de aanvragen/afhankelijkheden van de app.
* Er is een logica waarmee de geactiveerde waarschuwing voor waarschuwings monitor automatisch kan worden opgelost als het probleem gedurende 8-24 uur niet meer wordt gedetecteerd.
  Opmerking: in het huidige ontwerp. Er wordt geen melding of actie verzonden wanneer een detectie waarschuwing wordt opgelost. U kunt controleren of een waarschuwing voor een slimme detectie is omgezet in de Azure Portal.

## <a name="configure-alerts"></a>Waarschuwingen configureren

U kunt de waarschuwings regel voor slimme detectie uitschakelen vanuit de portal of met behulp van Azure Resource Manager ([Zie voor beeld van sjabloon](./proactive-arm-config.md)).

Deze waarschuwings regel wordt gemaakt met een bijbehorende [actie groep](../alerts/action-groups.md) met de naam ' Application Insights Slimme detectie ' die e-mail en webhook-acties bevat, en kan worden uitgebreid om aanvullende acties te activeren wanneer de waarschuwing wordt geactiveerd.

> [!NOTE]
> E-mail meldingen die vanuit deze waarschuwings regel worden verzonden, worden nu standaard verzonden naar gebruikers die zijn gekoppeld aan de bewakings-en bewakings functies van het abonnement. Hier vindt u meer informatie over dit [onderwerp](./proactive-email-notification.md).
> Meldingen die vanuit deze waarschuwings regel worden verzonden, volgen het [algemene waarschuwings schema](../alerts/alerts-common-schema.md).
>

Open de pagina waarschuwingen. Waarschuwings regels voor fout afwijkingen zijn opgenomen in alle waarschuwingen die u hand matig hebt ingesteld en u kunt zien of deze zich momenteel in de status van de waarschuwing bevindt.

:::image type="content" source="./media/proactive-failure-diagnostics/021.png" alt-text="Klik op de pagina Application Insights resource op waarschuwingen tegel en vervolgens op regels voor waarschuwingen beheren." lightbox="./media/proactive-failure-diagnostics/021.png":::

Klik op de waarschuwing om deze te configureren.

:::image type="content" source="./media/proactive-failure-diagnostics/032.png" alt-text="Scherm regel configuratie." lightbox="./media/proactive-failure-diagnostics/032.png":::

U kunt een waarschuwings regel voor fout afwijkingen uitschakelen of verwijderen, maar niet op dezelfde Application Insights resource maken.

## <a name="example-of-failure-anomalies-alert-webhook-payload"></a>Voor beeld van fout afwijkingen waarschuwing webhook Payload

```json
{
    "properties": {
        "essentials": {
            "severity": "Sev3",
            "signalType": "Log",
            "alertState": "New",
            "monitorCondition": "Resolved",
            "monitorService": "Smart Detector",
            "targetResource": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.insights/components/test-rule",
            "targetResourceName": "test-rule",
            "targetResourceGroup": "test-group",
            "targetResourceType": "microsoft.insights/components",
            "sourceCreatedId": "1a0a5b6436a9b2a13377f5c89a3477855276f8208982e0f167697a2b45fcbb3e",
            "alertRule": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.alertsmanagement/smartdetectoralertrules/failure anomalies - test-rule",
            "startDateTime": "2019-10-30T17:52:32.5802978Z",
            "lastModifiedDateTime": "2019-10-30T18:25:23.1072443Z",
            "monitorConditionResolvedDateTime": "2019-10-30T18:25:26.4440603Z",
            "lastModifiedUserName": "System",
            "actionStatus": {
                "isSuppressed": false
            },
            "description": "Failure Anomalies notifies you of an unusual rise in the rate of failed HTTP requests or dependency calls."
        },
        "context": {
            "DetectionSummary": "An abnormal rise in failed request rate",
            "FormattedOccurenceTime": "2019-10-30T17:50:00Z",
            "DetectedFailureRate": "50.0% (200/400 requests)",
            "NormalFailureRate": "0.0% (over the last 30 minutes)",
            "FailureRateChart": [
                [
                    "2019-10-30T05:20:00Z",
                    0
                ],
                [
                    "2019-10-30T05:40:00Z",
                    100
                ],
                [
                    "2019-10-30T06:00:00Z",
                    0
                ],
                [
                    "2019-10-30T06:20:00Z",
                    0
                ],
                [
                    "2019-10-30T06:40:00Z",
                    100
                ],
                [
                    "2019-10-30T07:00:00Z",
                    0
                ],
                [
                    "2019-10-30T07:20:00Z",
                    0
                ],
                [
                    "2019-10-30T07:40:00Z",
                    100
                ],
                [
                    "2019-10-30T08:00:00Z",
                    0
                ],
                [
                    "2019-10-30T08:20:00Z",
                    0
                ],
                [
                    "2019-10-30T08:40:00Z",
                    100
                ],
                [
                    "2019-10-30T17:00:00Z",
                    0
                ],
                [
                    "2019-10-30T17:20:00Z",
                    0
                ],
                [
                    "2019-10-30T09:00:00Z",
                    0
                ],
                [
                    "2019-10-30T09:20:00Z",
                    0
                ],
                [
                    "2019-10-30T09:40:00Z",
                    100
                ],
                [
                    "2019-10-30T10:00:00Z",
                    0
                ],
                [
                    "2019-10-30T10:20:00Z",
                    0
                ],
                [
                    "2019-10-30T10:40:00Z",
                    100
                ],
                [
                    "2019-10-30T11:00:00Z",
                    0
                ],
                [
                    "2019-10-30T11:20:00Z",
                    0
                ],
                [
                    "2019-10-30T11:40:00Z",
                    100
                ],
                [
                    "2019-10-30T12:00:00Z",
                    0
                ],
                [
                    "2019-10-30T12:20:00Z",
                    0
                ],
                [
                    "2019-10-30T12:40:00Z",
                    100
                ],
                [
                    "2019-10-30T13:00:00Z",
                    0
                ],
                [
                    "2019-10-30T13:20:00Z",
                    0
                ],
                [
                    "2019-10-30T13:40:00Z",
                    100
                ],
                [
                    "2019-10-30T14:00:00Z",
                    0
                ],
                [
                    "2019-10-30T14:20:00Z",
                    0
                ],
                [
                    "2019-10-30T14:40:00Z",
                    100
                ],
                [
                    "2019-10-30T15:00:00Z",
                    0
                ],
                [
                    "2019-10-30T15:20:00Z",
                    0
                ],
                [
                    "2019-10-30T15:40:00Z",
                    100
                ],
                [
                    "2019-10-30T16:00:00Z",
                    0
                ],
                [
                    "2019-10-30T16:20:00Z",
                    0
                ],
                [
                    "2019-10-30T16:40:00Z",
                    100
                ],
                [
                    "2019-10-30T17:30:00Z",
                    50
                ]
            ],
            "ArmSystemEventsRequest": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourceGroups/test-group/providers/microsoft.insights/components/test-rule/query?query=%0d%0a++++++++++++++++systemEvents%0d%0a++++++++++++++++%7c+where+timestamp+%3e%3d+datetime(%272019-10-30T17%3a20%3a00.0000000Z%27)+%0d%0a++++++++++++++++%7c+where+itemType+%3d%3d+%27systemEvent%27+and+name+%3d%3d+%27ProactiveDetectionInsight%27+%0d%0a++++++++++++++++%7c+where+dimensions.InsightType+in+(%275%27%2c+%277%27)+%0d%0a++++++++++++++++%7c+where+dimensions.InsightDocumentId+%3d%3d+%27718fb0c3-425b-4185-be33-4311dfb4deeb%27+%0d%0a++++++++++++++++%7c+project+dimensions.InsightOneClassTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightExceptionCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightDependencyCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightRequestCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightTraceCorrelationTable%0d%0a++++++++++++&api-version=2018-04-20",
            "LinksTable": [
                {
                    "Link": "<a href=\"https://portal.azure.com/#blade/AppInsightsExtension/ProactiveDetectionFeedBlade/ComponentId/{\"SubscriptionId\":\"4f9b81be-fa32-4f96-aeb3-fc5c3f678df9\",\"ResourceGroup\":\"test-group\",\"Name\":\"test-rule\"}/SelectedItemGroup/718fb0c3-425b-4185-be33-4311dfb4deeb/SelectedItemTime/2019-10-30T17:50:00Z/InsightType/5\" target=\"_blank\">View full details in Application Insights</a>"
                }
            ],
            "SmartDetectorId": "FailureAnomaliesDetector",
            "SmartDetectorName": "Failure Anomalies",
            "AnalysisTimestamp": "2019-10-30T17:52:32.5802978Z"
        },
        "egressConfig": {
            "displayConfig": [
                {
                    "rootJsonNode": null,
                    "sectionName": null,
                    "displayControls": [
                        {
                            "property": "DetectionSummary",
                            "displayName": "What was detected?",
                            "type": "Text",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "property": "FormattedOccurenceTime",
                            "displayName": "When did this occur?",
                            "type": "Text",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "property": "DetectedFailureRate",
                            "displayName": "Detected failure rate",
                            "type": "Text",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "property": "NormalFailureRate",
                            "displayName": "Normal failure rate",
                            "type": "Text",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "chartType": "Line",
                            "xAxisType": "Date",
                            "yAxisType": "Percentage",
                            "xAxisName": "",
                            "yAxisName": "",
                            "property": "FailureRateChart",
                            "displayName": "Failure rate over last 12 hours",
                            "type": "Chart",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "defaultLoad": true,
                            "displayConfig": [
                                {
                                    "rootJsonNode": null,
                                    "sectionName": null,
                                    "displayControls": [
                                        {
                                            "showHeader": false,
                                            "columns": [
                                                {
                                                    "property": "Name",
                                                    "displayName": "Name"
                                                },
                                                {
                                                    "property": "Value",
                                                    "displayName": "Value"
                                                }
                                            ],
                                            "property": "tables[0].rows[0][0]",
                                            "displayName": "All of the failed requests had these characteristics:",
                                            "type": "Table",
                                            "isOptional": false,
                                            "isPropertySerialized": true
                                        }
                                    ]
                                }
                            ],
                            "property": "ArmSystemEventsRequest",
                            "displayName": "",
                            "type": "ARMRequest",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "showHeader": false,
                            "columns": [
                                {
                                    "property": "Link",
                                    "displayName": "Link"
                                }
                            ],
                            "property": "LinksTable",
                            "displayName": "Links",
                            "type": "Table",
                            "isOptional": false,
                            "isPropertySerialized": false
                        }
                    ]
                }
            ]
        }
    },
    "id": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.insights/components/test-rule/providers/Microsoft.AlertsManagement/alerts/7daf8739-ca8a-4562-b69a-ff28db4ba0a5",
    "type": "Microsoft.AlertsManagement/alerts",
    "name": "Failure Anomalies - test-rule"
}
```

## <a name="triage-and-diagnose-an-alert"></a>Een waarschuwing sorteren en vaststellen

Een waarschuwing geeft aan dat een abnormale toename van het aantal mislukte aanvragen is gedetecteerd. Waarschijnlijk is er sprake van een probleem met uw app of van de omgeving.

Als u verder wilt onderzoeken, klikt u op ' volledige details weer geven in Application Insights ' op de koppelingen op deze pagina gaat u rechtstreeks naar een [Zoek pagina](./diagnostic-search.md) die wordt gefilterd op de relevante aanvragen, uitzonde ringen, afhankelijkheid of traceringen. 

U kunt ook de [Azure Portal](https://portal.azure.com)openen, naar de Application Insights resource voor uw app gaan en de pagina fouten openen.

Als u op fouten opsporen klikt, krijgt u meer informatie en kunt u het probleem oplossen.

:::image type="content" source="./media/proactive-failure-diagnostics/051.png" alt-text="Diagnostische gegevens zoeken." lightbox="./media/proactive-failure-diagnostics/051.png#lightbox":::

Op basis van het percentage aanvragen en het aantal betrokken gebruikers kunt u bepalen hoe urgent het probleem is. In het bovenstaande voor beeld wordt het uitval percentage van 78,5% vergeleken met een normaal percentage van 2,2%, wat erop wijst dat er iets mis gaat. Aan de andere kant werden slechts 46 gebruikers beïnvloed. Als dit uw app is, kunt u beoordelen hoe ernstig het is.

In veel gevallen kunt u het probleem snel vaststellen op basis van de gegevens van de aanvraag naam, uitzonde ring, fout bij het openen van een afhankelijkheid en traceer gegeven.

In dit voor beeld is er een uitzonde ring van SQL Database omdat de aanvraag limiet is bereikt.

:::image type="content" source="./media/proactive-failure-diagnostics/052.png" alt-text="Details van mislukte aanvragen." lightbox="./media/proactive-failure-diagnostics/052.png":::

## <a name="review-recent-alerts"></a>Recente waarschuwingen controleren

Klik op **waarschuwingen** op de pagina Application Insights resource om de meest recente geactiveerde waarschuwingen weer te geven:

:::image type="content" source="./media/proactive-failure-diagnostics/070.png" alt-text="Overzicht van waarschuwingen." lightbox="./media/proactive-failure-diagnostics/070.png":::

## <a name="whats-the-difference-"></a>Wat is het verschil...
Slimme detectie van fout afwijkingen is een aanvulling op andere vergelijk bare functies van Application Insights.

* [metrische waarschuwingen](../alerts/alerts-log.md) worden door u ingesteld en kunnen een breed scala aan metrische gegevens bewaken, zoals CPU-bezetting, aanvraag tarieven, pagina laad tijden, enzovoort. U kunt deze gebruiken om u te waarschuwen als u bijvoorbeeld meer resources wilt toevoegen. Slimme detectie van fout afwijkingen is daarentegen van toepassing op een klein aantal kritische metrische gegevens (momenteel alleen mislukte aanvragen), ontworpen om u vrijwel realtime te informeren wanneer de frequentie van de mislukte aanvragen van de web-app toeneemt in vergelijking met het normale gedrag van de web-app. In tegens telling tot metrische waarschuwingen worden door slimme detectie automatisch drempel waarden ingesteld en bijgewerkt als reactie wijzigingen in het gedrag optreden. Slimme detectie start ook het diagnostische werk voor u en bespaart u tijd bij het oplossen van problemen.

* [Slimme detectie van prestatie afwijkingen](proactive-performance-diagnostics.md) maakt ook gebruik van machine Intelligence om ongebruikelijke patronen in uw metrische gegevens te ontdekken. u hoeft hiervoor geen configuratie op te nemen. Maar in tegens telling tot Slimme detectie van fout afwijkingen, is het doel van de detectie van de prestatie afwijkingen te bepalen van de segmenten van uw gebruiks fragmenten die mogelijk niet goed worden aangeboden, bijvoorbeeld door specifieke pagina's van een specifiek type browser. De analyse wordt dagelijks uitgevoerd en als er een resultaat wordt gevonden, is het waarschijnlijk veel minder urgent dan een waarschuwing. Daarentegen wordt de analyse voor fout afwijkingen doorlopend uitgevoerd op binnenkomende toepassings gegevens en wordt u binnen enkele minuten gewaarschuwd als de snelheid van de server fouten groter is dan verwacht.

## <a name="if-you-receive-a-smart-detection-alert"></a>Als u een Smart detection-waarschuwing ontvangt
*Waarom heb ik deze waarschuwing ontvangen?*

* Er is een abnormale toename in de frequentie van mislukte aanvragen gedetecteerd vergeleken met de normale basis lijn van de voor gaande periode. Na de analyse van de fouten en de bijbehorende toepassings gegevens, denken we dat er een probleem is waarin u moet letten.

*Is er een probleem met de melding?*

* We proberen een waarschuwing te ontvangen over het onderbreken of degraderen van apps, maar alleen u kunt de semantiek en de impact van de app of gebruikers volledig begrijpen.

*En nu gaat u naar mijn toepassings gegevens?*

* Nee. De service is volledig automatisch. Alleen de meldingen worden weer geven. Uw gegevens zijn [privé](./data-retention-privacy.md).

*Moet ik zich abonneren op deze waarschuwing?*

* Nee. Elke toepassing die aanvraag gegevens verzendt, heeft de regel waarschuwing voor slimme detectie.

*Kan ik uw abonnement opzeggen of meldingen ontvangen die naar mijn collega's worden verzonden?*

* Ja, klik in waarschuwings regels op de regel voor slimme detectie om deze te configureren. U kunt de waarschuwing uitschakelen of ontvangers wijzigen voor de waarschuwing.

*Ik ben de e-mail kwijt geraakt. Waar vind ik de meldingen in de portal?*

* In de activiteiten Logboeken. Open in azure de Application Insights resource voor uw app en selecteer vervolgens activiteiten Logboeken.

*Sommige waarschuwingen zijn te vinden op bekende problemen en ik wil ze niet ontvangen.*

* U kunt de functie voor het onderdrukken van [waarschuwings regels](../alerts/alerts-action-rules.md) gebruiken.

## <a name="next-steps"></a>Volgende stappen
Deze diagnostische hulpprogram ma's helpen u bij het controleren van de gegevens in uw app:

* [Metrische Explorer](../essentials/metrics-charts.md)
* [Zoek Verkenner](./diagnostic-search.md)
* [Analyse-krachtige query taal](../logs/log-analytics-tutorial.md)

Slimme detecties zijn automatisch. Maar misschien wilt u nog meer waarschuwingen instellen?

* [Hand matig geconfigureerde metrische waarschuwingen](../alerts/alerts-log.md)
* [Webtests voor beschikbaarheid](./monitor-web-app-availability.md)