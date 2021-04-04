---
title: Naslag informatie voor Azure CDN-regels-engine | Microsoft Docs
description: Referentie documentatie voor voor waarden en functies voor de Azure CDN regels-engine.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: 295bc0a20a547bf944f48af6711b18af34571b02
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91362577"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>Referentie voor Azure CDN van Verizon Premium-regels engine

In dit artikel vindt u gedetailleerde beschrijvingen van de beschik bare matching voorwaarden en-functies voor de [regels engine](cdn-verizon-premium-rules-engine.md)van Azure Content Delivery Network (CDN).

De regel engine is ontworpen als de definitieve autoriteit voor het verwerken van specifieke typen aanvragen door het CDN.

**Veelvoorkomende toepassingen**:

- Een aangepast cache beleid overschrijft of definieert.
- Aanvragen voor gevoelige inhoud beveiligen of weigeren.
- Aanvragen omleiden.
- Aangepaste logboek gegevens opslaan.
## <a name="key-concepts"></a>Belangrijkste concepten
De belangrijkste concepten voor het instellen van de regel engine worden hieronder beschreven.
### <a name="draft"></a>Concept
Een concept van een beleid bestaat uit een of meer regels die bedoeld zijn voor het identificeren van aanvragen en de set acties die hierop worden toegepast. Een concept is een onderhanden werk dat veelvuldige configuratie-updates toestaat zonder dat dit van invloed is op het verkeer van de site. Zodra een concept gereed is om te worden voltooid, moet het worden omgezet in een alleen-lezen-beleid.

### <a name="rule"></a>Regel
Een regel identificeert een of meer typen aanvragen en de set acties die hierop worden toegepast.

Het bestaat uit: 

- Een set voorwaardelijke expressies waarmee de logica wordt gedefinieerd waarmee aanvragen worden geïdentificeerd.
- Een set matching voorwaarden waarmee de criteria worden gedefinieerd die worden gebruikt voor het identificeren van aanvragen.
- Een set functies waarmee wordt gedefinieerd hoe het CDN de bovenstaande aanvragen afhandelt.
Deze elementen worden aangegeven in de volgende afbeelding.

![Scherm afbeelding met labels toont de voorwaardelijke expressie, de overeenkomst en de functies van een regel.](./media/cdn-verizon-premium-rules-engine-reference/verizon-rules-engine-reference.png)

### <a name="policy"></a>Beleid
Een beleid dat bestaat uit een set met alleen-lezen regels, biedt de mogelijkheid om het volgende te doen:

- Meerdere varianten van uw regels maken, opslaan en beheren.
- Terugkeren naar een eerder geïmplementeerde versie.
- Gebeurtenis-specifieke regels vooraf voorbereiden (bijvoorbeeld een regel die verkeer omleidt als gevolg van het onderhoud van de klant.)

> [!NOTE]
> Hoewel er slechts één beleid per omgeving is toegestaan, kan het beleid zo nodig worden geïmplementeerd.

### <a name="deploy-request"></a>Aanvraag implementeren
Een implementatie aanvraag biedt een eenvoudige en gestroomlijnde procedure waarmee een beleid snel kan worden toegepast op de Faserings-of productie omgeving. Er wordt een overzicht van de implementatie aanvragen gegeven om het bijhouden van wijzigingen die zijn toegepast op die omgevingen te vergemakkelijken.

> [!NOTE]
> Alleen aanvragen waarvoor het systeem voor automatische validatie en fout detectie niet wordt door gegeven, vereisen hand matige controle en goed keuring.

### <a name="rule-precedence"></a>Prioriteit van regel
De regels in een beleid worden doorgaans verwerkt in de volg orde waarin ze worden weer gegeven (bijvoorbeeld van boven naar beneden). Als de aanvraag overeenkomt met conflicterende regels, heeft de laatste regel die moet worden verwerkt prioriteit.

### <a name="policy-deployment-workflow"></a>Werk stroom voor beleids implementatie
De werk stroom waarmee een beleid kan worden toegepast op de productie-of Faserings omgeving, wordt hieronder weer gegeven.

![Werk stroom voor beleids implementatie](./media/cdn-verizon-premium-rules-engine-reference/policy-deployment-workflow.png)

|Stap |Beschrijving |
|---------|---------|
|[Concept maken](https://docs.vdms.com/cdn/index.html#HRE/AdministeringDraftsandRules.htm#Create)    |    Een concept bestaat uit een set regels die definiëren hoe aanvragen voor uw inhoud moeten worden verwerkt door het CDN.     |
|Concept vergren delen   |     Zodra een concept is voltooid, moet het worden vergrendeld en omgezet in een alleen-lezen-beleid.    |
|[Implementatie aanvraag verzenden](https://docs.vdms.com/cdn/index.html#HRE/DeployRequest.htm)   |   <br> Met een implementatie aanvraag kan een beleid worden toegepast op test-of productie verkeer.</br> <br>Dien een implementatie aanvraag in voor de staging-of productie omgeving.</br>     |
|Aanvraag beoordeling implementeren   |    <br>Een implementatie aanvraag ondergaat automatische validatie en fout detectie.</br><br>Hoewel het meren deel van de implementatie aanvragen automatisch wordt goedgekeurd, is hand matige controle vereist voor complexere beleids regels.</br>   |
|Beleids implementatie ([fase ring](https://docs.vdms.com/cdn/index.html#HRE/Environment.htm#Staging))   |  <br> Na goed keuring van een implementatie aanvraag voor de Faserings omgeving wordt een beleid toegepast op de Faserings omgeving. Met deze omgeving kan een beleid worden getest op basis waarvan site verkeer wordt gesimuleerd.</br><br>Zodra het beleid gereed is om te worden toegepast op live site verkeer, moet een nieuwe implementatie aanvraag voor de productie omgeving worden ingediend.</br>      |
|Beleids implementatie ([productie](https://docs.vdms.com/cdn/index.html#HRE/Environment.htm#Producti))   |  Na goed keuring van een implementatie aanvraag voor de productie omgeving wordt een beleid toegepast op de productie omgeving. Met deze omgeving kan een beleid fungeren als de laatste instantie om te bepalen hoe het CDN live verkeer moet verwerken.     |
## <a name="syntax"></a>Syntax

De manier waarop speciale tekens worden behandeld, is afhankelijk van hoe een overeenkomst voorwaarde of-functie tekst waarden verwerkt. Een match-voor waarde of functie kan tekst op een van de volgende manieren interpreteren:

- [**Letterlijke waarden**](#literal-values)
- [**Joker teken waarden**](#wildcard-values)
- [**Reguliere expressies**](#regular-expressions)

### <a name="literal-values"></a>Letterlijke waarden

Tekst die wordt geïnterpreteerd als een letterlijke waarde, behandelt alle speciale tekens, met uitzonde ring van het symbool%, als onderdeel van de waarde die moet worden aangepast. Met andere woorden, een letterlijke match-voor waarde `\'*'\` die is ingesteld op is alleen vervuld wanneer die exacte waarde (dat wil zeggen `\'*'\` ) wordt gevonden.

Een percentage symbool wordt gebruikt om URL-code ring aan te geven (bijvoorbeeld `%20` ).

### <a name="wildcard-values"></a>Joker teken waarden

Tekst die wordt geïnterpreteerd als een Joker teken, wijst extra betekenissen toe aan speciale tekens. In de volgende tabel wordt beschreven hoe de volgende reeks tekens wordt geïnterpreteerd:

Teken | Beschrijving
----------|------------
\ | Een back slash wordt gebruikt om een van de tekens die in deze tabel zijn opgegeven, te escapepen. Een back slash moet direct voor het speciale teken worden opgegeven.<br/>De volgende syntaxis verescapet bijvoorbeeld een asterisk: `\*`
% | Een percentage symbool wordt gebruikt om URL-code ring aan te geven (bijvoorbeeld `%20` ).
\* | Een asterisk is een Joker teken dat bestaat uit een of meer tekens.
Space | Een spatie geeft aan dat aan een voor waarde voor een overeenkomst kan worden voldaan door een van de opgegeven waarden of patronen.
Value | Een enkele aanhaling heeft geen speciale betekenis. Er wordt echter een set met enkele aanhalings tekens gebruikt om aan te geven dat een waarde moet worden behandeld als een letterlijke waarde. Dit kan op de volgende manieren worden gebruikt:<br><br/>-Hiermee kan een match-voor waarde worden vervuld wanneer de opgegeven waarde overeenkomt met een deel van de vergelijkings waarde.  Bijvoorbeeld, `'ma'` komt overeen met een van de volgende teken reeksen: <br/><br/>/Business/**ma** rathon/asset.htm<br/>**ma** p.gif<br/>/business/template. **ma** p<br /><br />-Hiermee kan een speciaal teken worden opgegeven als een letterlijke teken. U kunt bijvoorbeeld een letterlijke spatie opgeven door een spatie te plaatsen binnen een set met enkele aanhalings tekens ( `' '` of `'sample value'` ).<br/>-Hiermee kan een lege waarde worden opgegeven. Geef een lege waarde op door een set met enkele aanhalings tekens (,) op te geven.<br /><br/>**Belangrijk:**<br/>-Als de opgegeven waarde geen joker teken bevat, wordt deze automatisch beschouwd als een letterlijke waarde, wat betekent dat het niet nodig is om een set met enkele aanhalings tekens op te geven.<br/>-Als een back slash geen ander teken in deze tabel weglaat, wordt deze genegeerd wanneer deze is opgegeven binnen een set met enkele aanhalings tekens.<br/>-Een andere manier om een speciaal teken op te geven als een letterlijke teken, is het escapeel te maken met behulp van een back slash (dat wil zeggen `\` ).

### <a name="regular-expressions"></a>Reguliere expressies

Reguliere expressies definiëren een patroon dat wordt doorzocht in een tekst waarde. De notatie reguliere expressie definieert specifieke betekenissen voor diverse symbolen. In de volgende tabel wordt aangegeven hoe speciale tekens worden behandeld door matching voorwaarden en functies die reguliere expressies ondersteunen.

Speciaal teken | Description
------------------|------------
\ | Met een back slash wordt het teken dat het volgt, als een letterlijke waarde gezien in plaats van de normale expressie betekenis. De volgende syntaxis verescapet bijvoorbeeld een asterisk: `\*`
% | De betekenis van een percentage symbool is afhankelijk van het gebruik.<br/><br/> `%{HTTPVariable}`: Met deze syntaxis wordt een HTTP-variabele aangeduid.<br/>`%{HTTPVariable%Pattern}`: In deze syntaxis wordt een percentage symbool gebruikt om een HTTP-variabele en als scheidings teken te identificeren.<br />`\%`: Als u een percentage tekent, kan dit worden gebruikt als letterlijke waarde of om URL-code ring aan te geven (bijvoorbeeld `\%20` ).
\* | Met een sterretje kan het voorafgaande teken nul of meer keer worden gevonden.
Space | Een spatie wordt meestal beschouwd als een letterlijke teken.
Value | Enkele aanhalings tekens worden beschouwd als letterlijke letters. Een set met enkele aanhalings tekens heeft geen speciale betekenis.

Voor waarden en functies die reguliere expressies ondersteunen, accepteren patronen die zijn gedefinieerd door perl compatibele reguliere expressies (PCRE).



## <a name="next-steps"></a>Volgende stappen

- [Criteria voor overeenkomst in de regelengine](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Voorwaardelijke expressies in de regelengine](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Functies van de regelengine](cdn-verizon-premium-rules-engine-reference-features.md)
- [HTTP-gedrag negeren met de regel engine](cdn-verizon-premium-rules-engine.md)
- [Overzicht van Azure CDN](cdn-overview.md)
