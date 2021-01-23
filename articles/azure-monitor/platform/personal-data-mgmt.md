---
title: Richt lijnen voor persoons gegevens die zijn opgeslagen in azure Log Analytics | Microsoft Docs
description: In dit artikel wordt beschreven hoe u persoons gegevens beheert die zijn opgeslagen in azure Log Analytics en hoe u deze kunt herkennen en verwijderen.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/18/2018
ms.openlocfilehash: 40ea367c1298a916df541d362b85e8d485d193f1
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98734929"
---
# <a name="guidance-for-personal-data-stored-in-log-analytics-and-application-insights"></a>Richtlijnen voor persoonsgegevens die zijn opgeslagen in Logboekanalyse en Application Insights

Log Analytics is een gegevens archief waar persoons gegevens waarschijnlijk worden gevonden. Application Insights worden de gegevens opgeslagen in een Log Analytics partitie. In dit artikel wordt beschreven waar in Log Analytics en Application Insights dergelijke gegevens doorgaans worden gevonden, evenals de mogelijkheden die beschikbaar zijn voor het afhandelen van dergelijke gegevens.

> [!NOTE]
> Voor de doel einden van dit artikel verwijzen _gegevens_ naar gegevens die naar een log Analytics werkruimte worden verzonden, terwijl _toepassings gegevens_ verwijzen naar gegevens die worden verzameld door Application Insights.

[!INCLUDE [gdpr-dsr-and-stp-note](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>Strategie voor het afhandelen van persoonlijke gegevens

Hoewel het aan u is en uw bedrijf uiteindelijk de strategie wil bepalen waarmee u uw privé gegevens gaat verwerken (in alle), zijn de volgende mogelijke benaderingen van toepassing. Deze worden weer gegeven in volg orde van voor keur van een technisch oogpunt van het grootste voor deel:

* Indien mogelijk, stopt u het verzamelen van, het afschermen, anoniem maken of anderszins aanpassen van de gegevens die worden verzameld om deze uit te sluiten van als ' privé '. Dit is de aanbevolen benadering en bespaart _u de nood_ zaak om een zeer kost bare strategie voor gegevens verwerking te maken.
* Als dat niet mogelijk is, probeert u de gegevens te normaliseren om de impact op het gegevens platform en de prestaties te verminderen. In plaats van een expliciete gebruikers-ID te registreren, maakt u bijvoorbeeld een opzoek gegevens waarmee de gebruikers naam en de details ervan worden gecorreleerd aan een interne ID die vervolgens ergens anders kan worden geregistreerd. Op die manier is het mogelijk dat als een van uw gebruikers u vraagt om zijn of haar persoonlijke gegevens te verwijderen, de rij in de opzoek tabel die overeenkomt met de gebruiker, voldoende wordt verwijderd. 
* Ten slotte, als er persoonlijke gegevens moeten worden verzameld, moet u een proces maken rond het pad van de API voor opschonen en het bestaande query-API-pad om te voldoen aan eventuele verplichtingen die u mogelijk hebt voor het exporteren en verwijderen van persoonlijke gegevens die aan een gebruiker zijn gekoppeld. 

## <a name="where-to-look-for-private-data-in-log-analytics"></a>Waar moet u zoeken naar privé gegevens in Log Analytics?

Log Analytics is een flexibele Store, waarmee u een schema voor uw gegevens voorschrijft, kunt u elk veld overschrijven met aangepaste waarden. Daarnaast kan elk aangepast schema worden opgenomen. Het is dus niet mogelijk om precies te zeggen waar privé gegevens worden gevonden in uw specifieke werk ruimte. De volgende locaties zijn echter goede start punten in uw inventaris:

### <a name="log-data"></a>Logboekgegevens

* *IP-adressen*: log Analytics verzamelt diverse IP-gegevens over diverse tabellen. Met de volgende query worden bijvoorbeeld alle tabellen weer gegeven waarin IPv4-adressen zijn verzameld in de afgelopen 24 uur:
    ```
    search * 
    | where * matches regex @'\b((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)(\.|$)){4}\b' //RegEx originally provided on https://stackoverflow.com/questions/5284147/validating-ipv4-addresses-with-regexp
    | summarize count() by $table
    ```
* *Gebruikers-id's*: gebruikers-id's zijn te vinden in een groot aantal verschillende oplossingen en tabellen. U kunt met behulp van de zoek opdracht zoeken naar een bepaalde gebruikers naam in uw hele gegevensset:
    ```
    search "[username goes here]"
    ```
  Zoek niet alleen naar gebruikers namen met een lees bare naam, maar ook GUID'S die rechtstreeks naar een bepaalde gebruiker kunnen worden getraceerd.
* *Apparaat-id's*: zoals gebruikers-id's, worden apparaat-id's soms als ' privé ' beschouwd. Gebruik dezelfde methode als hierboven hierboven voor gebruikers-Id's om tabellen te identificeren waarin dit probleem kan optreden. 
* *Aangepaste gegevens*: met log Analytics kunt u de verzameling op diverse manieren toestaan: aangepaste logboeken en aangepaste velden, de [http-gegevens verzamelaar-API](./data-collector-api.md) en aangepaste gegevens die zijn verzameld als onderdeel van systeem gebeurtenis Logboeken. Al deze zijn gevoelig voor persoonlijke gegevens en moeten worden onderzocht om te controleren of dergelijke gegevens bestaan.
* Door oplossingen *vastgelegde gegevens*: omdat het oplossings mechanisme een open-end is, raden we u aan alle tabellen te controleren die zijn gegenereerd door een oplossing om te voldoen aan de vereisten.

### <a name="application-data"></a>Toepassingsgegevens

* *IP-adressen*: Hoewel Application Insights alle IP-adres velden standaard van elkaar maakt op ' 0.0.0.0 ', is dit een redelijk gebruikelijk patroon om deze waarde te overschrijven met de werkelijke gebruikers-IP om sessie gegevens te onderhouden. De onderstaande Analytics-query kan worden gebruikt om een tabel te vinden die waarden bevat in de kolom IP-adres in plaats van ' 0.0.0.0 ' in de afgelopen 24 uur:
    ```
    search client_IP != "0.0.0.0"
    | where timestamp > ago(1d)
    | summarize numNonObfuscatedIPs_24h = count() by $table
    ```
* *Gebruikers-id's*: standaard worden door Application Insights wille keurig gegenereerde id's gebruikt voor het bijhouden van gebruikers en sessies. Het is echter gebruikelijk dat deze velden worden overschreven voor het opslaan van een ID die relevant is voor de toepassing. Bijvoorbeeld: gebruikers namen, AAD-GUID'S, enzovoort. Deze Id's worden vaak beschouwd als persoonlijke gegevens en moeten daarom op de juiste wijze worden afgehandeld. Onze aanbeveling is altijd om deze Id's te veranoniem makenen. Velden waarin deze waarden vaak worden gevonden, zijn onder andere session_Id, user_Id, user_AuthenticatedId, user_AccountId, en customDimensions.
* *Aangepaste gegevens*: met Application Insights kunt u een set aangepaste dimensies toevoegen aan elk gegevens type. Deze dimensies kunnen *alle* gegevens zijn. Gebruik de volgende query om alle aangepaste dimensies te identificeren die in de afgelopen 24 uur zijn verzameld:
    ```
    search * 
    | where isnotempty(customDimensions)
    | where timestamp > ago(1d)
    | project $table, timestamp, name, customDimensions 
    ```
* *In-Memory en in-transit gegevens*: Application Insights worden uitzonde ringen, aanvragen, afhankelijkheids aanroepen en traceringen bijgehouden. Persoonlijke gegevens kunnen vaak worden verzameld op het niveau van code en HTTP-aanroepen. Bekijk de tabellen met uitzonde ringen, aanvragen, afhankelijkheden en traceringen om dergelijke gegevens te identificeren. Gebruik de [initialisatie functies voor telemetrie](../app/api-filtering-sampling.md) waar mogelijk deze gegevens kunnen worden verborgen.
* *Snapshot debugger Capture*: met de functie [Snapshot debugger](../app/snapshot-debugger.md) in Application Insights kunt u moment opnamen van fout opsporing verzamelen wanneer er een uitzonde ring wordt gedetecteerd op het productie-exemplaar van uw toepassing. Met moment opnamen wordt de volledige Stack tracering voor de uitzonde ringen en de waarden voor lokale variabelen bij elke stap in de stack beschikbaar gemaakt. Helaas is deze functie niet toegestaan voor selectieve verwijdering van magnetische punten of programmatische toegang tot gegevens in de moment opname. Als de standaard retentie tijd van de moment opname niet voldoet aan uw nalevings vereisten, is de aanbeveling daarom de functie uit te scha kelen.

## <a name="how-to-export-and-delete-private-data"></a>Privé gegevens exporteren en verwijderen

Zoals vermeld in de sectie [strategie voor het afhandelen van persoonlijke gegevens](#strategy-for-personal-data-handling) , wordt het __ten zeerste__ aangeraden om het beleid voor gegevens verzameling te herstructureren om het verzamelen van persoonlijke gegevens uit te scha kelen, te maken of op te Anoniemen, of anderszins te wijzigen om het te verwijderen als ' privé '. Het afhandelen van de gegevens leidt ertoe dat de kosten voor u en uw team een strategie definiëren en automatiseren, een interface bouwen voor uw klanten om te communiceren met hun gegevens via en continue onderhouds kosten. Verder is het kostenbesparend voor Log Analytics en Application Insights en is een groot volume van gelijktijdige query's of API-aanroepen voor het opschonen van api's de mogelijkheid om alle andere interactie met Log Analytics functionaliteit negatief te beïnvloeden. Dit heeft te maken met inderdaad enkele geldige scenario's waarin privé gegevens moeten worden verzameld. In dergelijke gevallen moeten gegevens worden verwerkt, zoals beschreven in deze sectie.

[!INCLUDE [gdpr-intro-sentence](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>Weer geven en exporteren

Voor beide gegevens aanvragen weer geven en exporteren moet de [log Analytics query-API](https://dev.loganalytics.io/) of de  [Application INSIGHTS query-API](https://dev.applicationinsights.io/quickstart) worden gebruikt. Logica voor het converteren van de vorm van de gegevens naar een geschikte shape om aan uw gebruikers te leveren, is aan u toe te passen. [Azure functions](https://azure.microsoft.com/services/functions/) is een goede plaats om deze logica te hosten.

> [!IMPORTANT]
>  Hoewel het meren deel van de opschoon bewerkingen veel sneller kan worden uitgevoerd dan de SLA, **wordt de formele sla voor het volt ooien van de opschoon bewerking 30 dagen ingesteld** als gevolg van de zware impact van het gebruikte gegevens platform. Deze SLA voldoet aan de vereisten voor AVG. Het is een geautomatiseerd proces dat er geen manier is om aan te vragen dat een bewerking sneller wordt verwerkt. 

### <a name="delete"></a>Verwijderen

> [!WARNING]
> Verwijderingen in Log Analytics zijn destructief en niet-omkeerbaar. Wees uiterst voorzichtig bij de uitvoering ervan.

We zijn beschikbaar gesteld als onderdeel van een privacy-verwerkings *-API-* pad. Dit pad moet spaarzaam worden gebruikt vanwege het risico dat eraan is gekoppeld, de potentiële invloed op de prestaties en de kans om alle aggregaties, metingen en andere aspecten van uw Log Analytics gegevens te scheef trekken. Zie de sectie [strategie voor het verwerken van persoonlijke gegevens](#strategy-for-personal-data-handling) voor alternatieve benaderingen voor het afhandelen van persoonlijke gegevens.

> [!NOTE]
> Zodra de bewerking leegmaken is uitgevoerd, zijn de gegevens niet toegankelijk wanneer de status van de [opschoon bewerking](/rest/api/loganalytics/workspacepurge/getpurgestatus) *in behandeling* is. 

Opschonen is een zeer beschermde bewerking die geen enkele app of gebruiker in azure (inclusief zelfs de resource-eigenaar) toestemming heeft om uit te voeren zonder expliciet een rol in Azure Resource Manager te krijgen. Deze rol is _gegevens verzamelaar_ en moet voorzichtig worden gedelegeerd vanwege het mogelijke verlies van gegevens. 

> [!IMPORTANT]
> Voor het beheren van systeem bronnen worden opschoon aanvragen beperkt tot 50 aanvragen per uur. U moet een batch uitvoeren voor het opschonen van aanvragen door één opdracht te verzenden waarvan het predicaat alle gebruikers-id's bevat die moeten worden opgeschoond. Gebruik de [operator in](/azure/kusto/query/inoperator) om meerdere identiteiten op te geven. U moet de query uitvoeren voordat u de opschoon aanvraag uitvoert om te controleren of de resultaten worden verwacht. 



Zodra de Azure Resource Manager rol is toegewezen, zijn er twee nieuwe API-paden beschikbaar: 

#### <a name="log-data"></a>Logboekgegevens

* [Bericht opschonen](/rest/api/loganalytics/workspacepurge/purge) : Hiermee wordt een object opgegeven met de para meters voor het verwijderen van de gegevens en wordt een verwijzings-GUID geretourneerd 
* Status van leegmaken ophalen: de aanroep ' x-MS-status-location ' wordt door het aanroepen van de POST geretourneerd en bevat een URL die u kunt aanroepen om de status van uw opschoon API te bepalen. Bijvoorbeeld:

    ```
    x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/Microsoft.OperationalInsights/workspaces/[WorkspaceName]/operations/purge-[PurgeOperationId]?api-version=2015-03-20
    ```

> [!IMPORTANT]
>  Hoewel we verwachten dat het overgrote deel van de opschoon bewerking veel sneller is dan onze SLA, vanwege hun zware impact op het gegevens platform dat wordt gebruikt door Log Analytics, **wordt de formele sla voor het volt ooien van de opschoon bewerking ingesteld op 30 dagen**. 

#### <a name="application-data"></a>Toepassingsgegevens

* [Bericht opschonen](/rest/api/application-insights/components/purge) : Hiermee wordt een object opgegeven met de para meters voor het verwijderen van de gegevens en wordt een verwijzings-GUID geretourneerd
* Status van leegmaken ophalen: de aanroep ' x-MS-status-location ' wordt door het aanroepen van de POST geretourneerd en bevat een URL die u kunt aanroepen om de status van uw opschoon API te bepalen. Bijvoorbeeld:

   ```
   x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/microsoft.insights/components/[ComponentName]/operations/purge-[PurgeOperationId]?api-version=2015-05-01
   ```

> [!IMPORTANT]
>  Hoewel het meren deel van de opschoon bewerkingen veel sneller kan worden uitgevoerd dan de SLA Application Insights, **wordt de formele sla voor het volt ooien van de opschoon bewerking op 30 dagen ingesteld**.

## <a name="next-steps"></a>Volgende stappen
- Zie [log Analytics Data Security](./data-security.md)(Engelstalig) voor meer informatie over hoe log Analytics gegevens worden verzameld, verwerkt en beveiligd.
- Zie [Application Insights Data Security](../app/data-retention-privacy.md)(Engelstalig) voor meer informatie over hoe Application Insights gegevens worden verzameld, verwerkt en beveiligd.