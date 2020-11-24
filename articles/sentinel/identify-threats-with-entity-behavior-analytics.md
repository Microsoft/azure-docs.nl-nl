---
title: Geavanceerde bedreigingen met UEBA (User and entity Behavior Analytics) identificeren in azure Sentinel | Microsoft Docs
description: Maak gedrags basislijnen voor entiteiten (gebruikers, hostnamen, IP-adressen) en gebruik deze om afwijkend gedrag te detecteren en te identificeren dat APT (Zero-Day Advanced persistent Threats) zijn.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/19/2020
ms.author: yelevin
ms.openlocfilehash: a0a677342534cd92d5530199076e3d2e7084134d
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95494774"
---
# <a name="identify-advanced-threats-with-user-and-entity-behavior-analytics-ueba-in-azure-sentinel"></a>Geavanceerde bedreigingen met UEBA (User and entity Behavior Analytics) identificeren in azure Sentinel

> [!IMPORTANT]
>
> - De functies UEBA en Entity pages zijn nu **algemeen beschikbaar** in de volgende Azure Sentinel-geografische gebieden en regio's:
>    - Verenigde Staten Geografie
>    - Regio Oost-West
>    - Geografie van Australië
>
> - In alle andere geografische gebieden en regio's blijven deze functies gedurende de **Preview**-periode. Zie de [**aanvullende gebruiks voorwaarden voor Microsoft Azure previews**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.

## <a name="what-is-user-and-entity-behavior-analytics-ueba"></a>Wat is UEBA (User and entity Behavior Analytics)?

### <a name="the-concept"></a>Het concept

Het identificeren van bedreigingen in uw organisatie en hun mogelijke gevolgen: of een gemanipuleerde entiteit of een schadelijke Insider altijd een tijdrovend en arbeids intensief proces is. Door waarschuwingen te maken, de punten te verbinden en actief te zijn, kunt u alle tijd en inspanningen met minimale rendementen opdoen, en de mogelijkheid van geavanceerde dreigingen evading detectie. Met name onvindbare Threats zoals Zero-Day, targeted en Advanced persistente Threats kunnen het meest gevaarlijk zijn voor uw organisatie, waardoor de detectie van de kritieke.

De UEBA-functie in azure Sentinel elimineert de drudgery van de workloads van uw analisten en de onzekerheid van hun inspanningen, en levert hoogwaardige, beschik bare intelligentie, zodat ze zich kunnen concentreren op onderzoeken en herstel.

Omdat Azure Sentinel logboeken en waarschuwingen van alle verbonden gegevens bronnen verzamelt, worden deze geanalyseerd en worden er basis profielen voor basislijn gedrag van de entiteiten van uw organisatie (gebruikers, hosts, IP-adressen, toepassingen, enzovoort) weer gegeven in de periode tussen tijd en peer groep. Met behulp van verschillende technieken en machine learning mogelijkheden kan Sentinel afwijkende activiteiten identificeren en kunt u bepalen of een activum is aangetast. Dat is niet alleen dat, maar kan ook de relatieve gevoeligheid van bepaalde activa bepalen, peer groepen met activa identificeren en de mogelijke gevolgen van een gegeven aangetaste activa evalueren (de ' versterking van de ' straal '). Met deze informatie kunt u effectief bepalen hoe uw onderzoek en incidenten worden verwerkt. 

### <a name="architecture-overview"></a>Overzicht van de architectuur

:::image type="content" source="media/identify-threats-with-entity-behavior-analytics/entity-behavior-analytics-architecture.png" alt-text="Architectuur van entiteit gedrag analyse":::

### <a name="security-driven-analytics"></a>Security-gestuurde analyses

Azure Sentinel is geïnspireerd op het paradigma van Gartner voor UEBA-oplossingen en biedt een "buiten-in" benadering, op basis van drie referentie frames:

- **Use cases:** Door prioriteit te geven aan relevante aanvals vectoren en-scenario's op basis van beveiligings onderzoek dat is afgestemd op Security Research, uitgelijnd met het MITRE ATT&verzonken Framework van tactieken, technieken en subtechnieken die verschillende entiteiten als slacht offers, Perpetrators of draaiings punten in de Kill-keten plaatsen; Azure Sentinel richt zich specifiek op de meest waardevolle logboeken die elke gegevens bron kan bieden.

- **Gegevens bronnen:** Hoewel Azure-gegevens bronnen voor het eerst worden ondersteund, worden de gegevens bronnen van derden op basis van de eerste en beste ondersteuning geselecteerd om gegevens te leveren die overeenkomen met onze bedreigings scenario's.

- **Analytics:** Met behulp van verschillende machine learning (ML) algoritmen identificeert Azure Sentinel afwijkende activiteiten en presenteert deze duidelijk en beknopt in de vorm van contextuele verrijkingen, met enkele voor beelden die hieronder worden weer gegeven.

    :::image type="content" source="media/identify-threats-with-entity-behavior-analytics/behavior-analytics-top-down.png" alt-text="Gedrags analyse buiten de benadering":::

Azure Sentinel presenteert artefacten die uw beveiligings analisten helpen een duidelijk inzicht te krijgen in afwijkende activiteiten in de context en in vergelijking met het basislijn Profiel van de gebruiker. Acties die worden uitgevoerd door een gebruiker (of een host of een adres) worden contextuele geëvalueerd, waarbij een ' waar ' resultaat duidt op een geïdentificeerde anomalie:
- over geografische locaties, apparaten en omgevingen.
- over tijd en frequentie horizon (vergeleken met de eigen geschiedenis van de gebruiker).
- vergeleken met gedrag van de peers.
- in vergelijking met het gedrag van de organisatie.

    :::image type="content" source="media/identify-threats-with-entity-behavior-analytics/context.png" alt-text="Context van entiteit":::


### <a name="scoring"></a>Scoren

Voor elke activiteit wordt gescoord met ' onderzoek prioriteits Score ', waarmee de kans wordt bepaald dat een specifieke gebruiker een specifieke activiteit uitvoert, op basis van het gedrag van de gebruiker en de bijbehorende peers. Activiteiten die zijn geïdentificeerd als de meest abnormale ontvangst van de hoogste scores (op een schaal van 0-10).

Bekijk hoe gedrags analyses in [Microsoft Cloud app Security](https://techcommunity.microsoft.com/t5/microsoft-security-and/prioritize-user-investigations-in-cloud-app-security/ba-p/700136) wordt gebruikt voor een voor beeld van hoe dit werkt.

## <a name="entities-in-azure-sentinel"></a>Entiteiten in azure Sentinel

### <a name="entity-identifiers"></a>Entiteit-id's

Wanneer waarschuwingen naar Azure Sentinel worden verzonden, bevatten ze gegevens elementen die door Azure Sentinel worden geïdentificeerd en geclassificeerd als entiteiten, zoals gebruikers accounts, hosts, IP-adressen en andere. Als de waarschuwing niet voldoende informatie over de entiteit bevat, kan dit een uitdaging zijn.

Gebruikers accounts kunnen bijvoorbeeld op meer dan één manier worden geïdentificeerd: met behulp van de numerieke id (GUID) van een Azure AD-account of de UPN-waarde (User Principal Name), of een combi natie van de gebruikers naam en het NT-domein. Verschillende gegevens bronnen kunnen dezelfde gebruiker op verschillende manieren identificeren. Als dat mogelijk is, voegt Azure Sentinel deze id's samen tot één entiteit, zodat deze naar behoren kan worden geïdentificeerd.

Het kan gebeuren dat een van de resource providers een waarschuwing maakt waarin een entiteit niet voldoende is geïdentificeerd, bijvoorbeeld een gebruikers naam zonder de domein naam context. In dat geval kan de gebruikers entiteit niet worden samengevoegd met andere exemplaren van hetzelfde gebruikers account, die als een afzonderlijke entiteit zouden worden geïdentificeerd en die twee entiteiten in plaats van Unified blijven.

Als u het risico van dit probleem zo klein mogelijk wilt maken, moet u controleren of al uw waarschuwings providers de entiteiten op de juiste wijze identificeren in de door hen geproduceerde waarschuwingen. Daarnaast kan het synchroniseren van gebruikers account entiteiten met Azure Active Directory een andere map maken, waarmee gebruikers account entiteiten kunnen worden samengevoegd.

De volgende typen entiteiten worden momenteel geïdentificeerd in azure Sentinel:

- Gebruikers account (account)
- Host
- IP-adres (IP)
- Malware
- Bestand
- Proces
- Cloud toepassing (CloudApplication)
- Domein naam (DNS)
- Azure-resource
- Bestand (FileHash)
- Registersleutel
- Registerwaarde
- Beveiligings groep
- URL
- IoT-apparaat
- Postvak
- E-mail cluster
- E-mail bericht
- E-mail verzenden

### <a name="entity-pages"></a>Entiteits pagina's

Wanneer u een entiteit (momenteel beperkt tot gebruikers en hosts) tegen komt in een zoek opdracht, een waarschuwing of een onderzoek, kunt u de entiteit selecteren en naar een **entiteits pagina** gaan, een gegevens blad vol met nuttige informatie over die entiteit. De typen informatie die u op deze pagina vindt, bevatten elementaire feiten over de entiteit, een tijd lijn met belang rijke gebeurtenissen die betrekking hebben op deze entiteit en inzichten over het gedrag van de entiteit.
 
Entiteit pagina's bestaan uit drie delen:
- Het deel venster aan de linkerkant bevat de identiteits gegevens van de entiteit, verzameld uit gegevens bronnen zoals Azure Active Directory, Azure Monitor, Azure Security Center en micro soft Defender.

- In het middelste deel venster ziet u een grafische en tekstuele tijd lijn met belang rijke gebeurtenissen met betrekking tot de entiteit, zoals waarschuwingen, blad wijzers en activiteiten. Activiteiten zijn aggregaties van belang rijke gebeurtenissen van Log Analytics. De query's die deze activiteiten detecteren, worden ontwikkeld door micro soft security-onderzoeksteams.

- Het deel venster aan de rechter kant bevat gedrags inzichten voor de entiteit. Deze inzichten helpen u om snel afwijkingen en beveiligings Risico's te identificeren. De inzichten worden ontwikkeld door micro soft Security Research teams en zijn gebaseerd op anomalie detectie modellen.

### <a name="the-timeline"></a>De tijd lijn

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-timeline.png" alt-text="Tijd lijn van entiteits pagina's":::

De tijd lijn is een belang rijk onderdeel van de bijdrage van de entiteits pagina voor gedrags analyses in azure Sentinel. Er wordt een verhaal gepresenteerd over entiteits gebeurtenissen, zodat u inzicht krijgt in de activiteit van de entiteit binnen een specifiek tijds bestek.

U kunt het **tijds bereik** kiezen tussen verschillende vooraf ingestelde opties (zoals de *afgelopen 24 uur*) of instellen op een wille keurig aangepast tijds bestek. Daarnaast kunt u filters instellen die de informatie in de tijd lijn beperken tot specifieke typen gebeurtenissen of waarschuwingen.

De volgende typen items zijn opgenomen in de tijd lijn:

- Waarschuwingen: alle waarschuwingen waarin de entiteit is gedefinieerd als een **toegewezen entiteit**. Als uw organisatie aangepaste waarschuwingen heeft gemaakt [met behulp van analyse regels](./tutorial-detect-threats-custom.md), moet u ervoor zorgen dat de entiteits toewijzing van de regels goed wordt uitgevoerd.

- Blad wijzers: blad wijzers die de specifieke entiteit bevatten die op de pagina wordt weer gegeven.

- Activiteiten-aggregatie van belang rijke gebeurtenissen met betrekking tot de entiteit. 
 
### <a name="entity-insights"></a>Entiteits inzichten
 
Entiteits inzichten zijn query's die door micro soft-beveiligings onderzoekers worden gedefinieerd om uw analisten efficiënter en effectief te onderzoeken. De inzichten worden weer gegeven als onderdeel van de entiteits pagina en bieden waardevolle beveiligings informatie over hosts en gebruikers, in de vorm van tabellaire gegevens en diagrammen. Als u hier informatie over hebt, hoeft u niet door te geven aan Log Analytics. De inzichten bevatten gegevens over aanmeldingen, groeps toevoegingen, afwijkende gebeurtenissen en meer, en bevatten geavanceerde ML-algoritmen voor het detecteren van afwijkend gedrag. De inzichten zijn gebaseerd op de volgende gegevens typen:
- Syslog
- SecurityEvent
- Auditlogboeken
- Aanmeld logboeken
- Office-activiteit
- BehaviorAnalytics (UEBA) 
 
### <a name="how-to-use-entity-pages"></a>Entiteits pagina's gebruiken

Entiteits pagina's zijn ontworpen om deel uit te maken van meerdere gebruiks scenario's, en zijn toegankelijk via incident beheer, de onderzoek grafiek, blad wijzers of rechtstreeks vanaf de pagina entiteit zoeken onder gedrag van entiteits **analyses** in het hoofd menu van Azure Sentinel.

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-use-cases.png" alt-text="Use cases van entiteits pagina":::


## <a name="data-schema"></a>Gegevens schema

### <a name="behavior-analytics-table"></a>Tabel met gedrags analyse

| Veld                     | Beschrijving                                                         |
|---------------------------|---------------------------------------------------------------------|
| TenantId                  | uniek ID-nummer van de Tenant                                      |
| SourceRecordId            | het unieke ID-nummer van de EBA-gebeurtenis                                   |
| TimeGenerated             | tijds tempel van het exemplaar van de activiteit                              |
| TimeProcessed             | tijds tempel van de verwerking van de activiteit door de EBA-engine            |
| ActivityType              | Categorie op hoog niveau van de activiteit                                 |
| ActionType                | de genormaliseerde naam van de activiteit                                     |
| UserName                  | gebruikers naam van de gebruiker die de activiteit heeft gestart                    |
| UserPrincipalName         | volledige gebruikers naam van de gebruiker die de activiteit heeft gestart               |
| Source               | gegevens bron die de oorspronkelijke gebeurtenis heeft gegeven                        |
| SourceIPAddress           | Het IP-adres waarop de activiteit is geïnitieerd                        |
| SourceIPLocation          | land van waaruit de activiteit is geïnitieerd, verrijkt van het IP-adres |
| SourceDevice              | de hostnaam van het apparaat dat de activiteit heeft gestart                  |
| DestinationIPAddress      | IP-adres van het doel van de activiteit                            |
| DestinationIPLocation     | land van het doel van de activiteit, verrijkt van IP-adres     |
| DestinationDevice         | naam van het doel apparaat                                           |
| **UsersInsights**         | contextuele verrijkingen van betrokken gebruikers                            |
| **DevicesInsights**       | contextuele verrijkingen van betrokken apparaten                          |
| **ActivityInsights**      | contextuele analyse van de activiteit op basis van onze profilering              |
| **InvestigationPriority** | afwijkings Score, tussen 0-10 (0 = goed aardig, 10 = sterk afwijkend)         |
|

### <a name="querying-behavior-analytics-data"></a>Query's uitvoeren op de analyse gegevens van gedrag

Met behulp van [KQL](/azure/data-explorer/kusto/query/)kunnen we de gedrags analyse tabel opvragen.

Als we bijvoorbeeld alle gevallen willen vinden van een gebruiker die zich niet heeft kunnen aanmelden bij een Azure-resource, waarbij de gebruiker de eerste keer probeert verbinding te maken vanuit een bepaald land en verbindingen van dat land ongebruikelijk zijn, zelfs voor de peers van de gebruiker, kunnen we de volgende query gebruiken:

```Kusto
BehaviorAnalytics
| where ActivityType == "FailedLogOn"
| where FirstTimeUserConnectedFromCountry == True
| where CountryUncommonlyConnectedFromAmongPeers == True
```

### <a name="user-peers-metadata---table-and-notebook"></a>Meta gegevens van gebruikers peers-tabel en notitie blok

Meta gegevens van gebruikers zijn belang rijk voor het detecteren van bedreigingen, het onderzoeken van een incident en het zoeken naar een mogelijke bedreiging. Beveiligings analisten kunnen de normale activiteiten van de peers van een gebruiker observeren om te bepalen of de activiteiten van de gebruiker ongebruikelijk zijn in vergelijking met die van zijn of haar peers.

Azure Sentinel berekent en rangschikt de peers van een gebruiker op basis van het lidmaatschap van de Azure AD-beveiligings groep van de gebruiker, mailing lijst, et enzovoort en slaat de peers op die 1-20 in de tabel **UserPeerAnalytics** zijn gerangschikt. In de onderstaande scherm afbeelding ziet u het schema van de tabel UserPeerAnalytics en worden de bovenste acht gerangschikte peers van de gebruiker Kendall Collins weer gegeven. Azure Sentinel maakt gebruik van het algoritme voor de *frequentie-inverse document frequentie* (TF-IDF) om de weeging voor het berekenen van de positie te normaliseren: de kleinere groep, hoe hoger het gewicht. 

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/user-peers-metadata.png" alt-text="Scherm opname van de meta gegevens tabel van gebruikers peers":::

U kunt de [Jupyter-notebook](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/BehaviorAnalytics/UserSecurityMetadata) in de Azure Sentinel github-opslag plaats gebruiken om de meta gegevens van de gebruikers peers te visualiseren. Zie voor gedetailleerde instructies voor het gebruik van het notitie blok de [begeleide analyse-gebruikers beveiliging meta gegevens](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/BehaviorAnalytics/UserSecurityMetadata/Guided%20Analysis%20-%20User%20Security%20Metadata.ipynb) notebook.

### <a name="permission-analytics---table-and-notebook"></a>Machtigings analyse-tabel en notitie blok

Met de machtigings analyse kunt u de mogelijke gevolgen bepalen van het compromissen van een organisatie-Asset door een aanvaller. Deze invloed wordt ook wel de ' versterking van de activa ' genoemd. Beveiligings analisten kunnen deze informatie gebruiken om de prioriteit van onderzoek en incidenten te bepalen.

Azure Sentinel bepaalt de directe en transitieve toegangs rechten van een bepaalde gebruiker tot Azure-resources door de Azure-abonnementen te evalueren die de gebruiker rechtstreeks of via groepen of service-principals kan benaderen. Deze informatie, evenals de volledige lijst van het lidmaatschap van de Azure AD-beveiligings groep van de gebruiker, wordt vervolgens opgeslagen in de tabel **UserAccessAnalytics** . In de onderstaande scherm afbeelding ziet u een voor beeld van een rij in de tabel UserAccessAnalytics voor de gebruiker Alex Johnson. **Bron entiteit** is het account van de gebruiker of Service-Principal en de **doel entiteit** is de resource waartoe de bron entiteit toegang heeft. De waarden van **toegangs niveau** en **toegangs type** zijn afhankelijk van het toegangs beheer model van de doel entiteit. U kunt zien dat Alex toegang heeft tot het Azure-abonnement *Contoso Hotels-Tenant*. Het model voor het toegangs beheer van het abonnement is Azure RBAC.   

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/user-access-analytics.png" alt-text="Scherm opname van de analyse tabel voor gebruikers toegang":::

U kunt de [Jupyter-notebook](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/BehaviorAnalytics/UserSecurityMetadata) (hetzelfde notitie blok dat hierboven wordt vermeld) gebruiken uit de Azure Sentinel github-opslag plaats om de gegevens over de machtigings analyse te visualiseren. Zie voor gedetailleerde instructies voor het gebruik van het notitie blok de [begeleide analyse-gebruikers beveiliging meta gegevens](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/BehaviorAnalytics/UserSecurityMetadata/Guided%20Analysis%20-%20User%20Security%20Metadata.ipynb) notebook.

### <a name="hunting-queries-and-exploration-queries"></a>Zoek query's en onderzoek query's

Azure Sentinel voorziet in out-of-the-box een set van jacht-query's, verkennen en een werkmap op basis van de tabel BehaviorAnalytics. Deze hulpprogram ma's pres teren verrijkte gegevens, gericht op specifieke gebruiks voorbeelden, die wijzen op afwijkend gedrag. 

Meer informatie over [jacht en de onderzoek grafiek](./hunting.md) in azure Sentinel.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd over de analyse mogelijkheden van Azure Sentinel voor het entiteit gedrag. Raadpleeg de volgende artikelen voor praktische richt lijnen voor de implementatie en het gebruik van de inzichten die u hebt gewonnen:

- [Schakel de analyse van entiteits gedrag](./enable-entity-behavior-analytics.md) in azure Sentinel in.
- [Zoeken naar beveiligings Risico's](./hunting.md).