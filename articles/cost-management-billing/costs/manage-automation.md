---
title: Azure-kosten beheren met automatisering
description: In dit artikel wordt uitgelegd hoe u Azure-kosten kunt beheren met automatisering.
author: bandersmsft
ms.author: banders
ms.date: 01/06/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 02215bace693ac5ac36f9fc29758215d45b23eb1
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98051782"
---
# <a name="manage-costs-with-automation"></a>Kosten beheren met automatisering

U kunt Cost Management-automatisering gebruiken om een aangepaste set oplossingen te maken waarmee kostengegevens kunnen worden opgehaald en beheerd. In dit artikel worden algemene scenario's voor Cost Management-automatisering en beschikbare opties op basis van uw situatie behandeld. Als u wilt ontwikkelen met behulp van API's, worden voorbeelden van veelvoorkomende API-aanvragen weergegeven om uw ontwikkelproces te versnellen.

## <a name="automate-cost-data-retrieval-for-offline-analysis"></a>Ophalen van kostengegevens automatiseren voor offline analyse

Mogelijk moet u uw Azure-kostengegevens downloaden om deze samen te voegen met andere gegevenssets. Of u moet kostengegevens integreren in uw eigen systemen. Er zijn verschillende opties beschikbaar, afhankelijk van de hoeveelheid betrokken gegevens. U moet beschikken over Cost Management-machtigingen op het juiste bereik om API's en hulpprogramma's te kunnen gebruiken. Zie [Toegang tot gegevens toewijzen](./assign-access-acm-data.md) voor meer informatie.

## <a name="suggestions-for-handling-large-datasets"></a>Suggesties voor het verwerken van grote gegevenssets

Als uw organisatie over veel Azure-resources of -abonnementen beschikt, hebt u een grote hoeveelheid gebruiksgegevens. Excel kan vaak niet zulke grote bestanden laden. In deze situatie worden de volgende opties aanbevolen:

**Power BI**

Power BI wordt gebruikt om grote hoeveelheden gegevens op te nemen en af te handelen. Als u een Enterprise Agreement-klant bent, kunt u de Power BI-sjabloon-app gebruiken om de kosten voor uw factureringsaccount te analyseren. Het rapport bevat belangrijke weergaven die door klanten worden gebruikt. Zie [Azure-kosten analyseren met de app Power BI-sjabloon-app](./analyze-cost-data-azure-cost-management-power-bi-template-app.md) voor meer informatie.

**Power BI-gegevensconnector**

Als u uw gegevens dagelijks wilt analyseren, kunt u het beste de [Power BI-gegevensconnector](/power-bi/connect-data/desktop-connect-azure-cost-management) gebruiken om gegevens op te halen voor gedetailleerde analyse. Alle rapporten die u maakt, worden bijgewerkt door de connector naarmate er meer kosten worden gegenereerd.

**Cost Management-exports**

Mogelijk hoeft u de gegevens niet dagelijks te analyseren. Als dit wel het geval is, kunt u de functie [Exports](./tutorial-export-acm-data.md) van Cost Management gebruiken om gegevensexports naar een Azure Storage-account te plannen. Vervolgens kunt u de gegevens naar Power BI laden of in Excel analyseren als het bestand klein genoeg is. Exports zijn beschikbaar in Azure Portal of u kunt exports configureren met de [Exports-API](/rest/api/cost-management/exports).

**API voor gedetailleerde gebruiksgegevens**

U kunt de [API voor gedetailleerde gebruiksgegevens](/rest/api/consumption/usageDetails) gebruiken als u een kleine kostengegevensset hebt. Als u een grote hoeveelheid kostengegevens hebt, moet u de kleinste hoeveelheid gebruiksgegevens voor een periode aanvragen. Dit kunt u doen door een klein tijdsbereik op te geven of een filter in uw aanvraag te gebruiken. In een scenario waarin u bijvoorbeeld drie jaar aan kostengegevens nodig hebt, werkt de API beter wanneer u meerdere aanroepen voor verschillende tijdsbereiken maakt in plaats van één aanroep. Van daaruit kunt u de gegevens in Excel laden voor verdere analyse.

## <a name="automate-retrieval-with-usage-details-api"></a>Automatisch ophalen met API voor gedetailleerde gebruiksgegevens

Met de [API voor gedetailleerde gebruiksgegevens](/rest/api/consumption/usageDetails) kunt u eenvoudig ruwe, niet-samengevoegde kostengegevens ophalen die overeenkomen met uw Azure-factuur. De API is handig wanneer uw organisatie een programmatische oplossing voor het ophalen van gegevens nodig heeft. U kunt de API gebruiken als u kleinere gegevenssets wilt analyseren. Voor grotere gegevenssets moet u echter andere oplossingen gebruiken die eerder zijn geïdentificeerd. De gegevens in Gebruiksgegevens worden per meter per dag verstrekt. Dit wordt gebruikt bij het berekenen van uw maandelijkse factuur. De GA-versie (algemene beschikbaarheid) van de API's is `2019-10-01`. Gebruik `2019-04-01-preview` voor toegang tot de preview-versie voor reservering en Azure Marketplace-aankopen met de API's.

### <a name="usage-details-api-suggestions"></a>Suggesties van de API voor gedetailleerde gebruiksgegevens

**Aanvraagschema**

U wordt aangeraden _niet meer dan één aanvraag_ te maken met de API voor gedetailleerde gebruiksgegevens. Zie [Inzicht in gegevens van Cost Management](./understand-cost-mgt-data.md) voor meer informatie over hoe vaak kostengegevens worden vernieuwd en hoe afronding wordt verwerkt.

**Bereiken op het hoogste niveau bepalen zonder te filteren**

Gebruik de API om alle gegevens op te halen die u nodig hebt voor het bereik op het hoogste niveau. Wacht totdat alle benodigde gegevens zijn opgenomen voordat u een filter, groepering of samengevoegde analyse uitvoert. De API is specifiek geoptimaliseerd om grote hoeveelheden niet-samengevoegde ruwe kostengegevens te leveren. Zie [Bereiken begrijpen en gebruiken](./understand-work-scopes.md) in Cost Management voor meer informatie over het werken met bereiken. Wanneer u de benodigde gegevens voor een bereik hebt gedownload, gebruikt u Excel om de gegevens verder te analyseren met filters en draaitabellen.

### <a name="notes-about-pricing"></a>Opmerkingen over prijzen

Als u het gebruik en de kosten wilt afstemmen met het prijsoverzicht of de factuur, moet u rekening houden met de volgende informatie.

Prijswijzigingen op de prijslijst: de prijzen die worden weergegeven op de prijslijst, zijn de prijzen die u van Azure ontvangt. Ze worden geschaald naar een specifieke maateenheid. Helaas komt de maateenheid niet altijd overeen de maateenheid waarin het werkelijke resourcegebruik en de kosten worden uitgegeven.

Prijswijzigingen met betrekking tot gebruiksgegevens: in de gebruiksbestanden wordt geschaalde informatie weergegeven die mogelijk niet exact overeenkomt met het prijsoverzicht. Met name:

- Eenheidsprijs: de prijs wordt geschaald, zodat deze overeenkomt met de maateenheid waarmee waarin de kosten daadwerkelijk door Azure-resources worden uitgegeven. Als er wordt geschaald, komt de prijs niet overeen met de prijs die in het prijsoverzicht wordt weergegeven.
- Maateenheid: vertegenwoordigt de maateenheid waarmee de kosten daadwerkelijk door Azure-resources worden uitgegeven.
- Effectieve prijs/resourcetarief: de prijs staat voor het werkelijke tarief dat u per eenheid betaalt, nadat rekening is gehouden met kortingen. Dit is de prijs die moet worden gebruikt voor Hoeveelheid om berekeningen (Prijs * Hoeveelheid) te kunnen uitvoeren om de kosten af te stemmen. In de prijs wordt rekening gehouden met de volgende scenario's en de geschaalde eenheidsprijs, die ook in de bestanden voor komt. Als gevolg hiervan kan deze afwijken van de geschaalde eenheidsprijs.
  - Gestaffelde prijzen: bijvoorbeeld € 10 voor de eerste honderd eenheden, € 8 voor de volgende honderd eenheden.
  - Inbegrepen hoeveelheid, bijvoorbeeld: de eerste honderd eenheden zijn gratis en vervolgens € 10 per eenheid.
  - Reservations
  - Afronding die tijdens de berekening plaatsvindt: bij het afronden wordt rekening gehouden met het verbruikte aantal, gestaffelde/opgenomen prijzen per hoeveelheid en de geschaalde eenheidsprijs.

## <a name="example-usage-details-api-requests"></a>Voorbeeld van API voor gedetailleerde gebruiksgegevens

De volgende voorbeeldaanvragen worden door Microsoft-klanten gebruikt voor het oplossen van veelvoorkomende scenario's.

### <a name="get-usage-details-for-a-scope-during-specific-date-range"></a>Gebruiksgegevens voor een bereik tijdens een bepaald datumbereik ophalen

De gegevens die door de aanvraag worden geretourneerd, komen overeen met de datum waarop het gebruik is ontvangen door het factureringssysteem. Dit kan kosten van meerdere facturen bevatten. De aanroep die moet worden gebruikt, hang af van het type abonnement.

Gebruik de volgende aanroep voor verouderde klanten met een Enterprise Agreement (EA) of een betalen per gebruik-abonnement:

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?$filter=properties%2FusageStart%20ge%20'2020-02-01'%20and%20properties%2FusageEnd%20le%20'2020-02-29'&$top=1000&api-version=2019-10-01
```

Voor moderne klanten met een Microsoft-klantovereenkomst gebruikt u de volgende aanroep:

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?startDate=2020-08-01&endDate=&2020-08-05$top=1000&api-version=2019-10-01
```

### <a name="get-amortized-cost-details"></a>Gegevens van afgeschreven kosten ophalen

Als u de werkelijke kosten wilt zien omdat u de aankopen wilt zien op het moment dat ze worden samengevoegd, wijzigt u de *metrische waarde*  in `ActualCost` in de volgende aanvraag. Als u afgeschreven en werkelijke kosten wilt gebruiken, moet u de `2019-04-01-preview`-versie gebruiken. De huidige API-versie werkt op dezelfde wijze als de `2019-10-01`-versie, met uitzondering van het nieuwe type/metrisch-kenmerk en de gewijzigde eigenschapsnamen. Als u een Microsoft-klantovereenkomst hebt, zijn uw filters `startDate` en `endDate` in het volgende voorbeeld.

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?metric=AmortizedCost&$filter=properties/usageStart+ge+'2019-04-01'+AND+properties/usageEnd+le+'2019-04-30'&api-version=2019-04-01-preview
```

## <a name="retrieve-large-cost-datasets-recurringly-with-exports"></a>Grote kosten gegevenssets herhaaldelijk ophalen met Exports

U kunt regelmatig grote hoeveelheden gegevens exporteren met exports uit Cost Management. Exporteren is de aanbevolen manier om niet-samengevoegde kostengegevens op te halen, vooral wanneer gebruiksbestanden te groot zijn om ze betrouwbaar te kunnen aanroepen en downloaden met de API voor gedetailleerde gebruiksgegevens. Geëxporteerde gegevens worden in het Azure Storage-account van uw keuze geplaatst. Van daaruit kunt u ze in uw eigen systemen laden en naar behoefte analyseren. Zie [Gegevens exporteren](tutorial-export-acm-data.md) om exports in Azure Portal te configureren.

Als u exports met verschillende bereiken wilt automatiseren, is het voorbeeld van een API-aanvraag in de volgende sectie een goed uitgangspunt. U kunt de Exports-API gebruiken om automatische exports te maken als onderdeel van uw algemene omgevingsconfiguratie. Automatische exports helpen te verzekeren dat u over de gegevens beschikt die u nodig hebt. U kunt ze gebruiken in de systemen van uw eigen organisatie naarmate u uw Azure-gebruik uitbreidt.

### <a name="common-export-configurations"></a>Algemene exportconfiguraties

Voordat u uw eerste export maakt, overweegt u uw scenario en de configuratieopties die u nodig hebt om het mogelijk te maken. Overweeg de volgende exportopties:

- **Terugkeerpatroon**: Bepaalt hoe vaak de exporttaak wordt uitgevoerd en wanneer een bestand in uw Azure Storage-account wordt geplaatst. Kies tussen Dagelijks, Wekelijks en Maandelijks. Probeer uw terugkeerpatroon zodanig te configureren dat het overeenkomt met de gegevensimporttaken die worden gebruikt door het interne systeem van uw organisatie.
- **Terugkeerperiode**: Bepaalt hoelang de export geldig blijft. Bestanden worden alleen geëxporteerd tijdens de terugkeerperiode.
- **Tijdsbestek**: Bepaalt hoeveel gegevens er door de export worden gegenereerd bij een bepaalde uitvoering. Algemene opties zijn MonthToDate en WeekToDate.
- **StartDate**: Hiermee configureert u wanneer u wilt dat het exportschema begint. Een export wordt gemaakt op de StartDate en wordt daarna gebaseerd op uw Terugkeerpatroon.
- **Type**: Er zijn drie exporttypen:
  - ActualCost: Toont het totale gebruik en de totale kosten voor de opgegeven periode naarmate ze worden gemaakt en weergegeven op uw factuur.
  - AmortizedCost: Toont het totale gebruik en de totale kosten voor de opgegeven periode, waarbij afschrijving wordt toegepast op de toepasselijke aankoopkosten van reserveringen.
  - Gebruik: Alle exports die vóór 20 juli 2020 zijn gemaakt, zijn van het type Gebruik. Werk al uw geplande exports bij als ActualCost of AmortizedCost.
- **Kolommen**: Definieert de gegevensvelden die u in uw exportbestand wilt opnemen. Ze komen overeen met de velden die beschikbaar zijn in de API voor gedetailleerde gebruiksgegevens. Zie [API voor gedetailleerde gebruiksgegevens](/rest/api/consumption/usagedetails/list) voor meer informatie.

### <a name="create-a-daily-month-to-date-export-for-a-subscription"></a>Een dagelijkse export van maand tot heden maken voor een abonnement

Aanvraag-URL: `PUT https://management.azure.com/{scope}/providers/Microsoft.CostManagement/exports/{exportName}?api-version=2020-06-01`

```json
{
  "properties": {
    "schedule": {
      "status": "Active",
      "recurrence": "Daily",
      "recurrencePeriod": {
        "from": "2020-06-01T00:00:00Z",
        "to": "2020-10-31T00:00:00Z"
      }
    },
    "format": "Csv",
    "deliveryInfo": {
      "destination": {
        "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MYDEVTESTRG/providers/Microsoft.Storage/storageAccounts/{yourStorageAccount} ",
        "container": "{yourContainer}",
        "rootFolderPath": "{yourDirectory}"
      }
    },
    "definition": {
      "type": "ActualCost",
      "timeframe": "MonthToDate",
      "dataSet": {
        "granularity": "Daily",
        "configuration": {
          "columns": [
            "Date",
            "MeterId",
            "ResourceId",
            "ResourceLocation",
            "Quantity"
          ]
        }
      }
    }
}
```

### <a name="automate-alerts-and-actions-with-budgets"></a>Waarschuwingen en acties automatiseren met Budgetten

Er zijn twee essentiële onderdelen om de waarde van uw investering in de cloud te maximaliseren. Eén daarvan is het automatisch maken van een budget. Het andere is het configureren van op kosten gebaseerde indeling in reactie op budgetwaarschuwingen. Er zijn verschillende manieren om het maken van een budget in Azure te automatiseren. Er vinden verscheidene waarschuwingsreacties plaats wanneer uw geconfigureerde waarschuwingsdrempels worden overschreden.

In de volgende secties worden beschikbare opties behandeld en voorbeelden van API-aanvragen gegeven om u aan de slag te laten gaan met budgetautomatisering.

#### <a name="how-costs-are-evaluated-against-your-budget-threshold"></a>Hoe kosten worden geëvalueerd ten opzichte van uw budgetdrempel

Uw kosten worden eenmaal per dag geëvalueerd ten opzichte van uw budgetdrempel. Wanneer u een nieuw budget maakt of op de dag dat uw budget opnieuw wordt ingesteld, zullen de kosten vergeleken met de drempel nul zijn, omdat de evaluatie mogelijk nog niet heeft plaatsgevonden.

Wanneer Azure detecteert dat uw kosten de drempel hebben overschreden, wordt er binnen een uur na de detectieperiode een melding geactiveerd.

#### <a name="view-your-current-cost"></a>Uw huidige kosten bekijken

Als u uw huidige kosten wilt bekijken, moet u een GET-aanroep doen met behulp van de [Query-API](/rest/api/cost-management/query).

Een GET-aanroep bij de API voor budgetten zal niet de huidige kosten retourneren die in Kostenanalyse worden weergegeven. Deze aanroep retourneert in plaats daarvan uw laatste geëvalueerde kosten.

### <a name="automate-budget-creation"></a>Het maken van een budget automatiseren

U kunt het maken van een budget automatiseren met behulp van de [API voor budgetten](/rest/api/consumption/budgets). U kunt ook een budget maken met een [budgetsjabloon](quick-create-budget-template.md). Sjablonen zijn een gemakkelijke manier voor u om Azure-implementaties te standaardiseren terwijl u verzekert dat kostenbeheer goed wordt geconfigureerd en afgedwongen.

#### <a name="supported-locales-for-budget-alert-emails"></a>Ondersteunde talen voor e-mails voor budgetwaarschuwingen

Met budgetten wordt u gewaarschuwd als u een bepaalde drempelwaarde overschrijdt. U kunt maximaal vijf e-mailontvangers per budget instellen. Ontvangers ontvangen de e-mailwaarschuwingen binnen 24 uur nadat de budgetdrempelwaarde is overschreden. Het kan echter zijn dat uw ontvanger een e-mail in een andere taal moet ontvangen. U kunt de volgende taalcultuurcodes gebruiken met de Budget-API. Stel de cultuurcode in met de parameter `locale`, zoals in het volgende voorbeeld.

```json
{
  "eTag": "\"1d681a8fc67f77a\"",
  "properties": {
    "timePeriod": {
      "startDate": "2020-07-24T00:00:00Z",
      "endDate": "2022-07-23T00:00:00Z"
    },
    "timeGrain": "BillingMonth",
    "amount": 1,
    "currentSpend": {
      "amount": 0,
      "unit": "USD"
    },
    "category": "Cost",
    "notifications": {
      "actual_GreaterThan_10_Percent": {
        "enabled": true,
        "operator": "GreaterThan",
        "threshold": 20,
        "locale": "en-us",
        "contactEmails": [
          "user@contoso.com"
        ],
        "contactRoles": [],
        "contactGroups": [],
        "thresholdType": "Actual"
      }
    }
  }
}

```

Talen die worden ondersteund door een cultuurcode:

| Cultuurcode| Taal |
| --- | --- |
| nl-nl | Engels (Verenigde Staten) |
| ja-jp | Japans (Japan) |
| zh-cn | Chinees (Vereenvoudigd, China) |
| de-de | Duits (Duitsland) |
| es-es | Spaans (Spanje, internationaal) |
| fr-fr | Frans (Frankrijk) |
| it-it | Italiaans (Italië) |
| ko-kr | Koreaans (Korea) |
| pt-br | Portugees (Brazilië) |
| ru-ru | Russisch (Rusland) |
| zh-tw | Chinees (Traditioneel, Taiwan) |
| cs-cz | Tsjechisch (Tsjechische Republiek) |
| pl-pl | Pools (Polen) |
| tr-tr | Turks (Turkije) |
| da-dk | Deens (Denemarken) |
| dn-gb | Engels (Verenigd Koninkrijk) |
| hu-hu | Hongaars (Hongarije) |
| nb-bo | Noors (Bokmål) (Noorwegen) |
| nl-nl | Nederlands (Nederland) |
| pt-pt | Portugees (Portugal) |
| sv-se | Zweeds (Zweden) |

#### <a name="common-budgets-api-configurations"></a>Algemene configuraties van de API voor budgetten

Er zijn vele manieren om een budget te configureren in uw Azure-omgeving. Overweeg eerst uw scenario en identificeer vervolgens de configuratieopties die het mogelijk maken. Controleer de volgende opties:

- **Tijdseenheid**: Vertegenwoordigt de terugkeerperiode die uw budget gebruikt om kosten te maken en evalueren. De meest algemene opties zijn Maandelijks, Driemaandelijks en Jaarlijks.
- **Tijdsperiode**: Vertegenwoordigt hoelang uw budget geldig is. Het budget bewaakt actief en waarschuwt u alleen terwijl het geldig is.
- **Meldingen**
  - Contact-e-mails: De e-mailadressen ontvangen waarschuwingen wanneer een budget kosten maakt en gedefinieerde drempels overschrijdt.
  - Contactrollen: Alle gebruikers die een overeenkomende Azure-rol in het opgegeven bereik hebben, ontvangen e-mailwaarschuwingen met deze optie. Abonnementseigenaren kunnen bijvoorbeeld een waarschuwing ontvangen voor een budget dat in het abonnementsbereik is gemaakt.
  - Contactgroepen: Het budget roept de geconfigureerde actiegroepen aan wanneer een waarschuwingsdrempel wordt overschreden.
- **Kostendimensiefilters**: Voor uw budget kunt u dezelfde filters gebruiken als die u in Kostenanalyse of de Query-API gebruikt. Gebruik dit filter om het bereik te verkleinen van de kosten die u met het budget bewaakt.

Nadat u de opties voor het maken van een budget hebt geïdentificeerd die aan uw behoeften voldoen, maakt u het budget met behulp van de API. Het onderstaande voorbeeld helpt u aan de slag te gaan met een algemene budgetconfiguratie.

**Een budget maken dat is gefilterd op meerdere resources en tags**

Aanvraag-URL: `PUT https://management.azure.com/subscriptions/{SubscriptionId} /providers/Microsoft.Consumption/budgets/{BudgetName}/?api-version=2019-10-01`

```json
{
  "eTag": "\"1d34d016a593709\"",
  "properties": {
    "category": "Cost",
    "amount": 100.65,
    "timeGrain": "Monthly",
    "timePeriod": {
      "startDate": "2017-10-01T00:00:00Z",
      "endDate": "2018-10-31T00:00:00Z"
    },
    "filter": {
      "and": [
        {
          "dimensions": {
            "name": "ResourceId",
            "operator": "In",
            "values": [
              "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{meterName}",
              "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{meterName}"
            ]
          }
        },
        {
          "tags": {
            "name": "category",
            "operator": "In",
            "values": [
              "Dev",
              "Prod"
            ]
          }
        },
        {
          "tags": {
            "name": "department",
            "operator": "In",
            "values": [
              "engineering",
              "sales"
            ]
          }
        }
      ]
    },
    "notifications": {
      "Actual_GreaterThan_80_Percent": {
        "enabled": true,
        "operator": "GreaterThan",
        "threshold": 80,
        "contactEmails": [
          "user1@contoso.com",
          "user2@contoso.com"
        ],
        "contactRoles": [
          "Contributor",
          "Reader"
        ],
        "contactGroups": [
          "/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/microsoft.insights/actionGroups/{actionGroupName}
        ],
        "thresholdType": "Actual"
      }
    }
  }
}
```

### <a name="configure-cost-based-orchestration-for-budget-alerts"></a>Op kosten gebaseerde indeling configureren voor budgetwaarschuwingen

U kunt budgetten configureren om geautomatiseerde acties te starten met behulp van Azure-actiegroepen. Zie [Automatisering met Azure-budgetten](../manage/cost-management-budget-scenario.md) voor meer informatie over het automatiseren van acties met behulp van budgetten.

## <a name="data-latency-and-rate-limits"></a>Gegevenslatentie en frequentielimieten

Het is raadzaam de API's niet vaker dan één keer per dag aan te roepen. Cost Management-gegevens worden elke vier uur vernieuwd, wanneer nieuwe gebruiksgegevens van Azure-resourceproviders worden ontvangen. Er komen niet meer gegevens vrij als de API vaker wordt aangeroepen. In plaats daarvan wordt de belasting groter. Zie [Inzicht in gegevens van Cost Management](understand-cost-mgt-data.md) voor meer informatie over hoe vaak gegevens worden gewijzigd en hoe gegevenslatentie wordt verwerkt.

### <a name="error-code-429---call-count-has-exceeded-rate-limits"></a>Foutcode 429: het aantal aanroepen heeft de frequentielimieten overschreden

Om een consistente ervaring voor alle Cost Management-abonnees mogelijk te maken, zijn frequentielimieten ingesteld voor Cost Management API's. Wanneer u de limiet bereikt, ontvangt u de HTTP-statuscode `429: Too many requests`. De huidige doorvoerlimieten voor onze API's zijn als volgt:

- 30 aanroepen per minuut: dit gebeurt per bereik, gebruiker of toepassing.
- 200 aanroepen per minuut: dit gebeurt per tenant, gebruiker of toepassing.

## <a name="next-steps"></a>Volgende stappen

- [Azure-kosten analyseren met de Power BI-sjabloon-app](./analyze-cost-data-azure-cost-management-power-bi-template-app.md).
- [Geëxporteerde gegevens maken en beheren](./tutorial-export-acm-data.md) met Exports.
- Meer informatie over de [API voor gedetailleerde gebruiksgegevens](/rest/api/consumption/usageDetails).