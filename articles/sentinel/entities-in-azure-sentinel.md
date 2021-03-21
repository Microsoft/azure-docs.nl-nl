---
title: Entiteiten gebruiken voor het classificeren en analyseren van gegevens in de Azure-Sentinel | Microsoft Docs
description: Wijs entiteits classificaties (gebruikers, hostnamen, IP-adressen) toe aan gegevens items in azure Sentinel en gebruik ze om gegevens uit meerdere bronnen te vergelijken, analyseren en correleren.
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
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: 43da1af7a3001d7f8e000a878948428a3d63aa4e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102456249"
---
# <a name="classify-and-analyze-data-using-entities-in-azure-sentinel"></a>Gegevens classificeren en analyseren met behulp van entiteiten in azure Sentinel

## <a name="what-are-entities"></a>Wat zijn entiteiten?

Wanneer waarschuwingen worden verzonden naar of gegenereerd door Azure Sentinel, bevatten ze gegevens items die door Sentinel als **entiteiten** kunnen worden herkend en ingedeeld. Wanneer Azure Sentinel begrijpt wat voor soort entiteit een bepaald gegevens item is, kent het de juiste vragen om het te vragen en kan vervolgens inzichten over dat item worden vergeleken in het volledige bereik van gegevens bronnen, en kan het eenvoudig worden gevolgd en in de gehele Sentinel-ervaring worden getraceerd-analyses, onderzoeken, herbemiddeling, jacht, enzovoort. Enkele algemene voor beelden van entiteiten zijn gebruikers, hosts, bestanden, processen, IP-adressen en Url's.

### <a name="entity-identifiers"></a>Entiteit-id's

Azure Sentinel ondersteunt een breed scala aan entiteits typen. Elk type heeft zijn eigen unieke kenmerken, inclusief een aantal dat kan worden gebruikt om een bepaalde entiteit te identificeren. Deze kenmerken worden weer gegeven als velden in de entiteit en worden **id's** genoemd. Bekijk de volledige lijst met ondersteunde entiteiten en hun bijbehorende id's.

#### <a name="strong-and-weak-identifiers"></a>Sterke en zwakke id's

Zoals eerder vermeld, is er voor elk type entiteit velden, of sets velden, waarmee het kan worden geïdentificeerd. Deze velden of sets velden kunnen worden aangeduid als **sterke id's** als ze een entiteit kunnen identificeren zonder dubbel zinnigheid of als **zwakke id's** als ze een entiteit onder bepaalde omstandigheden kunnen identificeren, maar niet gegarandeerd zijn om een entiteit in alle gevallen uniek te identificeren. In veel gevallen kan echter een selectie van zwakke id's worden gecombineerd om een sterke id te produceren.

Zo kunnen gebruikers accounts in meer dan één manier als **account** entiteiten worden aangeduid: met een enkele **sterke id** , zoals de numerieke id van een Azure ad-account (het veld **GUID** ) of de **UPN-waarde (User Principal name** ), of een combi natie van **zwakke id's** , zoals de velden **naam** en **NTDomain** . Verschillende gegevens bronnen kunnen dezelfde gebruiker op verschillende manieren identificeren. Wanneer Azure Sentinel twee entiteiten tegen komt die deze als dezelfde entiteit kan herkennen op basis van hun id's, worden de twee entiteiten samengevoegd tot één entiteit, zodat deze op de juiste en consistente wijze kan worden verwerkt.

Als echter een van de resource providers een waarschuwing maakt waarbij een entiteit niet voldoende is geïdentificeerd, bijvoorbeeld door slechts één **zwakke id** te gebruiken, zoals een gebruikers naam zonder de domein naam context, kan de gebruikers entiteit niet worden samengevoegd met andere exemplaren van hetzelfde gebruikers account. Deze andere instanties worden aangeduid als een afzonderlijke entiteit, en deze twee entiteiten blijven gescheiden in plaats van Unified.

Als u het risico van dit probleem zo klein mogelijk wilt maken, moet u controleren of al uw waarschuwings providers de entiteiten op de juiste wijze identificeren in de door hen geproduceerde waarschuwingen. Daarnaast kan het synchroniseren van gebruikers account entiteiten met Azure Active Directory een andere map maken, waarmee gebruikers account entiteiten kunnen worden samengevoegd.

#### <a name="supported-entities"></a>Ondersteunde entiteiten

De volgende typen entiteiten worden momenteel geïdentificeerd in azure Sentinel:

- Gebruikersaccount
- Host
- IP-adres
- Malware
- File
- Proces
- Cloud toepassing
- Domeinnaam
- Azure-resource
- Bestandshash
- Registersleutel
- Registerwaarde
- Beveiligings groep
- URL
- IoT-apparaat
- Postvak
- E-mail cluster
- E-mail bericht
- E-mail verzenden

U kunt de id's van deze entiteiten en andere relevante informatie in de [entiteiten verwijzing](entities-reference.md)weer geven.

## <a name="entity-mapping"></a>Entiteitstoewijzing

Hoe herkent Azure Sentinel een deel van de gegevens in een waarschuwing als een entiteit identificeren?

Laten we eens kijken hoe gegevens verwerking wordt uitgevoerd in azure Sentinel. Gegevens worden uit verschillende bronnen opgenomen via [connectors](connect-data-sources.md), of service-to-service, op basis van een agent of met behulp van een syslog-service en een logboek doorstuur server. De gegevens worden opgeslagen in tabellen in uw Log Analytics-werk ruimte. Deze tabellen worden vervolgens op regel matige tijdstippen met de analyse regels die u hebt gedefinieerd en ingeschakeld, opgevraagd. Een van de vele acties die door deze analyse regels worden uitgevoerd, is het toewijzen van gegevens velden in de tabellen aan door Sentinel herkende entiteiten van Azure. Volgens de toewijzingen die u in uw analyse regels definieert, neemt Azure Sentinel velden op uit de resultaten die door de query zijn geretourneerd, herkent u deze aan de id's die u voor elk entiteits type hebt opgegeven en past u deze toe op het entiteits type dat door die id's is geïdentificeerd.

Wat is het punt van dit?

Wanneer Azure Sentinel entiteiten kan identificeren in waarschuwingen van verschillende soorten gegevens bronnen, en met name als dit kan doen met behulp van sterke id's die gebruikelijk zijn voor elke gegevens bron of een derde schema, kan het vervolgens eenvoudig samen werken tussen deze waarschuwingen en gegevens bronnen. Deze correlaties helpen u bij het bouwen van een uitgebreide opslag van informatie en inzichten op de entiteiten, waardoor u een solide basis hebt voor uw beveiligings activiteiten.

Meer informatie over het [toewijzen van gegevens velden aan entiteiten](map-data-fields-to-entities.md).

Meer informatie over [welke id's een entiteit sterk identificeren](entities-reference.md).

## <a name="entity-pages"></a>Entiteits pagina's

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

Entiteits inzichten zijn query's die door micro soft-beveiligings onderzoekers worden gedefinieerd om uw analisten efficiënter en effectief te onderzoeken. De inzichten worden weer gegeven als onderdeel van de entiteits pagina en bieden waardevolle beveiligings informatie over hosts en gebruikers, in de vorm van tabellaire gegevens en diagrammen. Als u hier informatie over hebt, hoeft u niet door te geven aan Log Analytics. De inzichten bevatten gegevens over aanmeldingen, groeps toevoegingen, afwijkende gebeurtenissen en meer, en bevatten geavanceerde ML-algoritmen voor het detecteren van afwijkend gedrag.

De inzichten zijn gebaseerd op de volgende gegevens bronnen:

- Syslog (Linux)
- SecurityEvent (Windows)
- Audit logs bevat (Azure AD)
- SigninLogs (Azure AD)
- OfficeActivity (Office 365)
- BehaviorAnalytics (Azure Sentinel UEBA)
- Heartbeat (Azure Monitor-agent)
- CommonSecurityLog (Azure Sentinel)

### <a name="how-to-use-entity-pages"></a>Entiteits pagina's gebruiken

Entiteits pagina's zijn ontworpen om deel uit te maken van meerdere gebruiks scenario's, en zijn toegankelijk via incident beheer, de onderzoek grafiek, blad wijzers of rechtstreeks vanaf de pagina entiteit zoeken onder gedrag van entiteits **analyses** in het hoofd menu van Azure Sentinel.

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-use-cases.png" alt-text="Use cases van entiteits pagina":::

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd over het werken met entiteiten in azure Sentinel. Raadpleeg de volgende artikelen voor praktische richt lijnen voor de implementatie en het gebruik van de inzichten die u hebt gewonnen:

- [Schakel de analyse van entiteits gedrag](./enable-entity-behavior-analytics.md) in azure Sentinel in.
- [Zoeken naar beveiligings Risico's](./hunting.md).
