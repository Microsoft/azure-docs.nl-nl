---
title: Doorlopend exporteren van telemetrie uit Application Insights | Microsoft Docs
description: Exporteer diagnostische en gebruiks gegevens naar opslag in Microsoft Azure en down load deze vanaf daar.
ms.topic: conceptual
ms.date: 02/19/2021
ms.custom: references_regions
ms.openlocfilehash: e7831123834df9186310453106c50261373160ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101737032"
---
# <a name="export-telemetry-from-application-insights"></a>Telemetrie exporteren vanuit Application Insights
Wilt u de telemetrie langer houden dan de standaard retentie periode? Of verwerk het op een specifieke manier? Continue export is ideaal voor dit. De gebeurtenissen die u in de Application Insights Portal ziet, kunnen worden geëxporteerd naar de opslag in Microsoft Azure in JSON-indeling. Van daaruit kunt u uw gegevens downloaden en de code schrijven die u nodig hebt om deze te verwerken.  

> [!IMPORTANT]
> Continue export is afgeschaft. [Migreer naar een Application Insights bron op basis van een werk ruimte](convert-classic-resource.md) voor het gebruik van [Diagnostische instellingen](#diagnostic-settings-based-export) voor het exporteren van telemetrie.

> [!NOTE]
> Continue export wordt alleen ondersteund voor klassieke Application Insights-resources. [Pp werkruimte gebaseerde Application Insights-resources](./create-workspace-resource.md) moeten [diagnostische instellingen](./create-workspace-resource.md#export-telemetry) gebruiken.
>

Voordat u doorlopend exporteren instelt, zijn er enkele alternatieven die u wellicht wilt overwegen:

* Met de knop Exporteren boven aan een metriek of zoek tabblad kunt u tabellen en grafieken overdragen naar een Excel-spread sheet.

* [Analytics](../logs/log-query-overview.md) biedt een krachtige query taal voor telemetrie. Er kunnen ook resultaten worden geëxporteerd.
* Als u [uw gegevens in Power bi wilt verkennen](./export-power-bi.md), kunt u dat doen zonder doorlopend exporteren te gebruiken.
* Met de [rest API voor gegevens toegang](https://dev.applicationinsights.io/) kunt u via een programma toegang krijgen tot uw telemetrie.
* U kunt ook instellen dat [doorlopende export via Power shell wordt uitgevoerd](/powershell/module/az.applicationinsights/new-azapplicationinsightscontinuousexport).

Nadat u uw gegevens hebt gekopieerd naar de opslag ruimte (waar u deze zo lang mogelijk kunt blijven), is deze nog steeds beschikbaar in Application Insights voor de gebruikelijke [Bewaar periode](./data-retention-privacy.md).

## <a name="supported-regions"></a>Ondersteunde regio's

Continue export wordt ondersteund in de volgende regio's:

* Azië - zuidoost
* Canada - midden
* India - centraal
* Europa - noord
* Verenigd Koninkrijk Zuid
* Australië - oost
* Japan - oost
* Korea - centraal
* Frankrijk - centraal
* Azië - oost
* VS - west
* US - centraal
* US - oost 2
* VS - zuid-centraal
* VS - west 2
* Zuid-Afrika - noord
* VS - noord-centraal
* Brazilië - zuid
* Zwitserland - noord
* Australië - zuidoost
* Verenigd Koninkrijk West
* Duitsland - west-centraal
* Zwitserland - west
* Australië - centraal 2
* UAE - centraal
* Brazilië-Zuidoost
* Australië - centraal
* VAE - noord
* Noorwegen - oost
* Japan - west

> [!NOTE]
> Toepassingen die al zijn geconfigureerd in **Europa-West** en **VS-Oost** worden ondersteund, maar het onboarden van nieuwe toepassingen in deze regio's wordt niet ondersteund.

## <a name="continuous-export-advanced-storage-configuration"></a>Geavanceerde opslag configuratie voor continue export

Continue export **biedt geen ondersteuning** voor de volgende functies/configuraties van Azure Storage:

* Gebruik van [VNET/Azure Storage-firewalls](../../storage/common/storage-network-security.md) in combi natie met Azure Blob Storage.

* [Azure data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md).

## <a name="create-a-continuous-export"></a><a name="setup"></a> Een continue export maken

> [!NOTE]
> Een toepassing kan niet meer dan 3 TB aan gegevens per dag exporteren. Als er meer dan 3 TB per dag wordt geëxporteerd, wordt de export uitgeschakeld. Als u wilt exporteren zonder limiet, gebruikt u de functie voor het [exporteren van diagnostische instellingen](#diagnostic-settings-based-export).

1. Open in de resource Application Insights voor uw app onder configureren aan de linkerkant de optie doorlopend exporteren en kies **toevoegen**:

2. Kies de gegevens typen voor telemetrie die u wilt exporteren.

3. Maak of selecteer een [Azure Storage-account](../../storage/common/storage-introduction.md) waar u de gegevens wilt opslaan. Ga naar de [pagina met officiële prijzen](https://azure.microsoft.com/pricing/details/storage/)voor meer informatie over de opties voor opslag prijzen.

     Klik op toevoegen, bestemming exporteren, opslag account en vervolgens een nieuwe winkel maken of een bestaande Store kiezen.

    > [!Warning]
    > De opslag locatie wordt standaard ingesteld op dezelfde geografische regio als uw Application Insights-resource. Als u in een andere regio opslaat, kunnen er kosten in rekening worden gebracht.

4. Maak of selecteer een container in de opslag.

> [!NOTE]
> Zodra u uw export hebt gemaakt, gaan nieuwe opgenomen gegevens naar Azure Blob-opslag stromen. Continue export verzendt alleen nieuwe telemetrie die is gemaakt/opgenomen nadat continue export is ingeschakeld. Alle gegevens die bestonden vóór het inschakelen van continue export worden niet geëxporteerd en er wordt geen ondersteunde manier geboden om eerder gemaakte gegevens te exporteren met continue export.

Er kan een vertraging van ongeveer een uur optreden voordat de gegevens in de opslag worden weer gegeven.

Zodra de eerste export is voltooid, ziet u een structuur die vergelijkbaar is met het volgende in de Azure Blob Storage-container: (dit is afhankelijk van de gegevens die u verzamelt.)

|Naam | Beschrijving |
|:----|:------|
| [Beschikbaarheid](export-data-model.md#availability) | Rapporten over [Beschik baarheid van webtests](./monitor-web-app-availability.md).  |
| [Gebeurtenis](export-data-model.md#events) | Aangepaste gebeurtenissen gegenereerd door [track Event ()](./api-custom-events-metrics.md#trackevent). 
| [Uitzonderingen](export-data-model.md#exceptions) |Rapporteert [uitzonde ringen](./asp-net-exceptions.md) op de server en in de browser.
| [Berichten](export-data-model.md#trace-messages) | Verzonden door [TrackTrace](./api-custom-events-metrics.md#tracktrace)en door de [logboek registratie-adapters](./asp-net-trace-logs.md).
| [Metrische gegevens](export-data-model.md#metrics) | Gegenereerd door metrieke API-aanroepen.
| [Performance Counters](export-data-model.md) | Prestatie meter items die worden verzameld door Application Insights.
| [Aanvragen](export-data-model.md#requests)| Verzonden door [TrackRequest](./api-custom-events-metrics.md#trackrequest). De standaard modules gebruiken dit om de reactie tijd van de server te rapporteren, gemeten op de server.| 

### <a name="to-edit-continuous-export"></a>Continue export bewerken

Klik op doorlopend exporteren en selecteer het opslag account dat u wilt bewerken.

### <a name="to-stop-continuous-export"></a>Continue export stoppen

Als u het exporteren wilt stoppen, klikt u op uitschakelen. Wanneer u nogmaals op inschakelen klikt, wordt het exporteren opnieuw gestart met nieuwe gegevens. De gegevens die in de portal zijn ontvangen, worden niet weer gegeven terwijl het exporteren is uitgeschakeld.

Als u de export permanent wilt stoppen, verwijdert u deze. Hierdoor worden uw gegevens niet verwijderd uit de opslag.

### <a name="cant-add-or-change-an-export"></a>Kunt u een export niet toevoegen of wijzigen?
* Als u exports wilt toevoegen of wijzigen, hebt u de machtiging voor Eigenaar, Inzender of Application Insights-Inzender nodig. [Meer informatie over rollen][roles].

## <a name="what-events-do-you-get"></a><a name="analyze"></a> Welke gebeurtenissen krijgt u?
De geëxporteerde gegevens zijn de ruwe telemetriegegevens die we van uw toepassing ontvangen, behalve dat er locatie gegevens worden toegevoegd die worden berekend op basis van het IP-adres van de client.

Gegevens die zijn verwijderd door middel van [steek proeven](./sampling.md) , worden niet opgenomen in de geëxporteerde gegevens.

Andere berekende metrische gegevens worden niet opgenomen. We exporteren bijvoorbeeld geen gemiddeld CPU-gebruik, maar we exporteren de ruwe telemetrie waarvan het gemiddelde wordt berekend.

De gegevens bevatten ook de resultaten van alle [beschik bare-webtesten](./monitor-web-app-availability.md) die u hebt ingesteld.

> [!NOTE]
> **Proef.** Als uw toepassing veel gegevens verzendt, kan de steekproeffunctie worden gebruikt en kan slechts een fractie van de gegenereerde telemetrie worden verzonden. [Meer informatie over steekproeven.](./sampling.md)
>
>

## <a name="inspect-the-data"></a><a name="get"></a> De gegevens controleren
U kunt de opslag direct in de portal inspecteren. Klik op Start in het meest linkse menu, waar u ' Azure-Services ' selecteert, selecteer **opslag accounts**, selecteer de naam van het opslag account op de pagina overzicht Selecteer **blobs** onder Services en selecteer ten slotte de container naam.

Als u Azure Storage wilt controleren in Visual Studio, opent u **weer gave**, **Cloud Explorer**. (Als u deze menu opdracht niet hebt, moet u de Azure SDK installeren: Open het dialoog venster **New Project** , vouw Visual C#/Cloud uit en kies **Get Microsoft Azure SDK voor .net**.)

Wanneer u uw BLOB-archief opent, ziet u een container met een set BLOB-bestanden. De URI van elk bestand dat is afgeleid van uw Application Insights resource naam, de bijbehorende instrumentatie sleutel, telemetrie-type/datum/tijd. (De resource naam is allemaal kleine letters en de instrumentatie sleutel laat streepjes.)

![Inspecteer de BLOB Store met een geschikt hulp programma](./media/export-telemetry/04-data.png)

De datum en tijd zijn UTC en wanneer de telemetrie in de Store is gestort, niet de tijd dat deze is gegenereerd. Dus als u code schrijft voor het downloaden van de gegevens, kan deze lineair door de gegevens worden verplaatst.

Dit is de vorm van het pad:

```console
$"{applicationName}_{instrumentationKey}/{type}/{blobDeliveryTimeUtc:yyyy-MM-dd}/{ blobDeliveryTimeUtc:HH}/{blobId}_{blobCreationTimeUtc:yyyyMMdd_HHmmss}.blob"
```

Waar

* `blobCreationTimeUtc` is de tijd waarop de blob is gemaakt in de interne staging-opslag
* `blobDeliveryTimeUtc` is de tijd waarop BLOB wordt gekopieerd naar de export doel opslag

## <a name="data-format"></a><a name="format"></a> Gegevens indeling
* Elke blob is een tekst bestand dat meerdere \n-gescheiden rijen bevat. Het bevat de telemetrie die gedurende een periode van ongeveer een halve minuut is verwerkt.
* Elke rij vertegenwoordigt een telemetrie-gegevens punt, zoals een aanvraag of pagina weergave.
* Elke rij is een niet-opgemaakt JSON-document. Als u de rijen wilt weer geven, opent u de BLOB in Visual Studio en kiest u  >  **Geavanceerde**  >  **indelings bestand** bewerken:

   ![De telemetrie weer geven met een geschikt hulp programma](./media/export-telemetry/06-json.png)

Tijds duursen zijn in tikken, waarbij 10 000 Ticks = 1 MS. Deze waarden tonen bijvoorbeeld een tijd van 1 MS om een aanvraag te verzenden vanuit de browser, 3 MS om deze te ontvangen en 1,8 s om de pagina in de browser te verwerken:

```json
"sendRequest": {"value": 10000.0},
"receiveRequest": {"value": 30000.0},
"clientProcess": {"value": 17970000.0}
```

[Gedetailleerde gegevens model verwijzing voor de eigenschaps typen en-waarden.](export-data-model.md)

## <a name="processing-the-data"></a>De gegevens verwerken
Op een kleine schaal kunt u code schrijven om uw gegevens uit elkaar te halen, deze in een werk blad te lezen, enzovoort. Bijvoorbeeld:

```csharp
private IEnumerable<T> DeserializeMany<T>(string folderName)
{
   var files = Directory.EnumerateFiles(folderName, "*.blob", SearchOption.AllDirectories);
   foreach (var file in files)
   {
      using (var fileReader = File.OpenText(file))
      {
         string fileContent = fileReader.ReadToEnd();
         IEnumerable<string> entities = fileContent.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));
         foreach (var entity in entities)
         {
            yield return JsonConvert.DeserializeObject<T>(entity);
         }
      }
   }
}
```

Zie [een werk rollen gebruiken][exportasa]voor een groter code voorbeeld.

## <a name="delete-your-old-data"></a><a name="delete"></a>Oude gegevens verwijderen
U bent verantwoordelijk voor het beheren van uw opslag capaciteit en het verwijderen van de oude gegevens, indien nodig.

## <a name="if-you-regenerate-your-storage-key"></a>Als u uw opslag sleutel opnieuw genereert...
Als u de sleutel naar uw opslag wijzigt, werkt continue export niet meer. U ziet een melding in uw Azure-account.

Open het tabblad Continue export en bewerk de export. Bewerk de Exportdoelvoorziening, maar behoudt dezelfde geselecteerde opslag. Klik op OK om te bevestigen.

De continue export wordt opnieuw gestart.

## <a name="export-samples"></a>Voor beelden exporteren

* [Exporteren naar SQL met Stream Analytics][exportasa]
* [Stream Analytics voor beeld 2](export-stream-analytics.md)

Overweeg op grotere schaal [HDInsight](https://azure.microsoft.com/services/hdinsight/) -Hadoop-clusters in de Cloud. HDInsight biedt diverse technologieën voor het beheren en analyseren van big data, en u kunt deze gebruiken voor het verwerken van gegevens die zijn geëxporteerd uit Application Insights.

## <a name="q--a"></a>Vragenronde
* *Maar het enige wat ik wil, is een eenmalige down load van een grafiek.*  

    Ja, dat kunt u doen. Klik boven aan het tabblad op **gegevens exporteren**.
* *Ik heb een export ingesteld, maar er zijn geen gegevens in mijn Store.*

    Heeft Application Insights telemetrie van uw app ontvangen sinds u de export hebt ingesteld? U ontvangt alleen nieuwe gegevens.
* *Ik heb geprobeerd een export in te stellen, maar de toegang is geweigerd*

    Als het account eigendom is van uw organisatie, moet u lid zijn van de groepen eigen aren of Inzender.
* *Kan ik rechtstreeks naar mijn eigen on-premises Store exporteren?*

    Nee, helaas. Onze export engine werkt momenteel alleen met Azure Storage.  
* *Geldt er een limiet voor de hoeveelheid gegevens die u in mijn Store plaatst?*

    Nee. Het pushen van gegevens wordt bewaard totdat u de export verwijdert. We stoppen als we de buitenste limieten voor Blob-opslag hebben bereikt, maar dat is tamelijk groot. U kunt bepalen hoeveel opslag ruimte u gebruikt.  
* *Hoeveel blobs moet er worden weer geven in de opslag?*

  * Voor elk gegevens type dat u hebt geselecteerd om te exporteren, wordt elke minuut een nieuwe BLOB gemaakt (als er gegevens beschikbaar zijn).
  * Daarnaast worden er voor toepassingen met veel verkeer extra partitie-eenheden toegewezen. In dit geval maakt elke eenheid elke minuut een blob.
* *Ik heb de sleutel opnieuw gegenereerd voor mijn opslag of de naam van de container gewijzigd en nu werkt de export niet.*

    Bewerk de export en open het tabblad Export bestemming. Wijzig dezelfde opslag als voorheen, en klik op OK om te bevestigen. Het exporteren wordt opnieuw gestart. Als de wijziging in de afgelopen paar dagen valt, gaan er geen gegevens verloren.
* *Kan ik het exporteren onderbreken?*

    Ja. Klik op Uitschakelen.

## <a name="code-samples"></a>Codevoorbeelden

* [Stream Analytics-voor beeld](export-stream-analytics.md)
* [Exporteren naar SQL met Stream Analytics][exportasa]
* [Gedetailleerde gegevens model verwijzing voor de eigenschaps typen en-waarden.](export-data-model.md)

## <a name="diagnostic-settings-based-export"></a>Op basis van de diagnostische instellingen exporteren

Voor de diagnostische instellingen gebaseerd exporteren wordt een ander schema gebruikt dan doorlopende export. Het biedt ook ondersteuning voor functies die continue export niet bevalt:

* Azure Storage-accounts met vnet, firewalls en persoonlijke koppelingen.
* Exporteren naar Event Hub.

Migreren naar Diagnostische instellingen op basis van de export:

1. Huidige continue export uitschakelen.
2. [Toepassing migreren naar een werk ruimte](convert-classic-resource.md).
3. [Schakel de diagnostische instellingen exporteren in](create-workspace-resource.md#export-telemetry). Selecteer **Diagnostische instellingen > diagnostische instelling toevoegen** vanuit uw Application Insights resource.

<!--Link references-->

[exportasa]: ./code-sample-export-sql-stream-analytics.md
[roles]: ./resources-roles-access-control.md

