---
title: Bedreigings reacties automatiseren met playbooks in azure Sentinel | Microsoft Docs
description: In dit artikel wordt beschreven hoe Automation in azure Sentinel is en hoe u playbooks kunt gebruiken om het voor komen van dreigingen en reacties te automatiseren.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: yelevin
ms.openlocfilehash: 0158c9f5b9debf0c47978e816951e25634621645
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104609361"
---
# <a name="automate-threat-response-with-playbooks-in-azure-sentinel"></a>Bedreigings reacties automatiseren met playbooks in azure Sentinel

In dit artikel wordt uitgelegd wat Azure Sentinel playbooks zijn en hoe u deze kunt gebruiken voor het implementeren van uw Security Orchestration-, Automation-en Response-bewerkingen (via), waardoor u betere resultaten krijgt tijdens het besparen van tijd en bronnen.

## <a name="what-is-a-playbook"></a>Wat is een Playbook?

SIEM/SOC-teams zijn doorgaans regel matig overspoeld met beveiligings waarschuwingen en incidenten, zodat de volumes zo groot zijn dat beschik bare mede werkers worden overspoeld. Dit resulteert al te vaak in situaties waarin veel waarschuwingen worden genegeerd en veel incidenten niet worden onderzocht, waardoor de organisatie kwetsbaar is voor aanvallen die niet worden opgemerkt.

Veel, in het bijzonder, van deze waarschuwingen en incidenten voldoen aan terugkerende patronen die kunnen worden aangepakt door specifieke en gedefinieerde sets herstel acties.

Een Playbook is een verzameling van deze herstel acties die kunnen worden uitgevoerd vanuit Azure Sentinel als een routine. Een Playbook kan helpen bij het automatiseren en organiseren van uw bedreigings antwoord. het kan hand matig worden uitgevoerd of worden ingesteld om automatisch te worden uitgevoerd als reactie op specifieke waarschuwingen of incidenten, wanneer deze worden geactiveerd door een analyse regel of een automatiserings regel.

Playbooks worden gemaakt en toegepast op het abonnements niveau, maar op het tabblad **Playbooks** (in de Blade nieuwe **automatisering** ) worden alle beschik bare Playbooks voor alle geselecteerde abonnementen weer gegeven.

### <a name="azure-logic-apps-basic-concepts"></a>Basis concepten Azure Logic Apps

Playbooks in azure Sentinel zijn gebaseerd op werk stromen die zijn ingebouwd in [Azure Logic apps](../logic-apps/logic-apps-overview.md), een Cloud service die u helpt bij het plannen, automatiseren en organiseren van taken en werk stromen tussen systemen binnen de hele onderneming. Dit betekent dat playbooks kan profiteren van alle kracht en aanpassings mogelijkheden van ingebouwde sjablonen van Logic Apps.

> [!NOTE]
> Omdat Azure Logic Apps een afzonderlijke resource zijn, kunnen er extra kosten in rekening worden gebracht. Ga naar de pagina met [Azure Logic apps](https://azure.microsoft.com/pricing/details/logic-apps/) prijzen voor meer informatie.

Azure Logic Apps communiceert met andere systemen en services met behulp van connectors. Hier volgt een korte uitleg van connectors en enkele van de belangrijkste kenmerken:

- **Beheerde connector:** Een reeks acties en triggers die omleiden om API-aanroepen naar een bepaald product of service. Azure Logic Apps biedt honderden connectors om te communiceren met micro soft-en niet-micro soft-Services.
  - [Lijst met alle Logic Apps-connectors en de bijbehorende documentatie](/connectors/connector-reference/)

- **Aangepaste connector:** U kunt communiceren met services die niet beschikbaar zijn als vooraf gedefinieerde connectors. Aangepaste connectors geven deze behoefte aan door u toe te staan om een connector te maken (en zelfs te delen) en de eigen triggers en acties te definiëren.
  - [Uw eigen aangepaste Logic Apps-connectors maken](/connectors/custom-connectors/create-logic-apps-connector)

- **Azure-Sentinel-connector:** Als u playbooks wilt maken die communiceren met Azure Sentinel, gebruikt u de Azure Sentinel connector.
  - [Documentatie voor Azure Sentinel connector](/connectors/azuresentinel/)

- **Trigger:** Een connector onderdeel dat een Playbook start. Hiermee wordt het schema gedefinieerd dat de Playbook verwacht te ontvangen wanneer het wordt geactiveerd. De Azure Sentinel connector heeft momenteel twee triggers:
  - [Waarschuwings trigger](/connectors/azuresentinel/#triggers): de Playbook ontvangt de waarschuwing als invoer.
  - [Incident trigger](/connectors/azuresentinel/#triggers): de Playbook ontvangt het incident als invoer, samen met alle opgenomen waarschuwingen en entiteiten.

    > [!IMPORTANT]
    >
    > - De functie voor **incident activering** voor playbooks is momenteel beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.

- **Acties:** Acties zijn alle stappen die na de trigger optreden. Ze kunnen sequentieel, parallel of in een matrix met complexe voor waarden worden gerangschikt.

- **Dynamische velden:** Tijdelijke velden, bepaald door het uitvoer schema van triggers en acties en ingevuld door de werkelijke uitvoer, die kunnen worden gebruikt in de volgende acties.

### <a name="permissions-required"></a>Machtigingen vereist

 Als u uw SecOps-team de mogelijkheid wilt geven om Logic Apps te gebruiken voor de beveiliging van integratie-, automatiserings-en antwoord-(via)-bewerkingen (dat wil zeggen, het maken en uitvoeren van playbooks-in azure Sentinel, kunt u Azure-rollen toewijzen aan specifieke leden van uw beveiligings team of aan het hele team. Hieronder vindt u een beschrijving van de verschillende beschik bare rollen en de taken waarvoor ze moeten worden toegewezen:

#### <a name="azure-roles-for-logic-apps"></a>Azure-rollen voor Logic Apps

- Met de rol van **Logic** apps kunt u logische apps beheren en playbooks uitvoeren, maar u kunt de toegang niet wijzigen (hiervoor hebt u de functie **eigenaar** nodig).
- Met de **logische app-operator** kunt u logische apps lezen, inschakelen en uitschakelen, maar u kunt ze niet bewerken of bijwerken.

#### <a name="azure-roles-for-sentinel"></a>Azure-rollen voor Sentinel

- Met de rol **Azure Sentinel contributor** kunt u een Playbook koppelen aan een analyse regel.
- Met de rol **Azure Sentinel responder** kunt u hand matig een Playbook uitvoeren.
- Met de **rol Inzender van Azure Sentinel Automation** kunt u playbooks uitvoeren. De puntkomma wordt niet gebruikt voor andere doeleinden.

#### <a name="learn-more"></a>Lees meer

- Meer [informatie over Azure-rollen in azure Logic apps](../logic-apps/logic-apps-securing-a-logic-app.md#access-to-logic-app-operations).
- Meer [informatie over Azure-rollen in azure Sentinel](roles.md).

## <a name="steps-for-creating-a-playbook"></a>Stappen voor het maken van een Playbook

- [Definieer het automatiserings scenario](#use-cases-for-playbooks).

- [Bouw de Azure Logic-app](tutorial-respond-threats-playbook.md).

- [Test uw logische app](#run-a-playbook-manually-on-an-alert).

- Koppel de Playbook aan een [Automation-regel](#incident-creation-automated-response) of een [analyse regel](#alert-creation-automated-response), of [Voer de bewerking hand matig uit wanneer dat nodig](#run-a-playbook-manually-on-an-alert)is.

### <a name="use-cases-for-playbooks"></a>Gebruiks voorbeelden voor playbooks

Het Azure Logic Apps-platform biedt honderden acties en triggers, zodat bijna elk automatiserings scenario kan worden gemaakt. Azure-Sentinel wordt aanbevolen om te beginnen met de volgende SOC-scenario's:

#### <a name="enrichment"></a>Verrijking

**Verzamel gegevens en koppel deze aan het incident om slimmere beslissingen te nemen.**

Bijvoorbeeld:

Er is een Azure Sentinel-incident gemaakt op basis van een waarschuwing door een analyse regel waarmee IP-adres entiteiten worden gegenereerd.

Het incident activeert een Automation-regel waarbij een Playbook wordt uitgevoerd met de volgende stappen:

- Starten wanneer er een [Nieuw Azure-verklikker incident wordt gemaakt](/connectors/azuresentinel/#triggers). De entiteiten die in het incident worden weer gegeven, worden opgeslagen in de dynamische velden van de incident trigger.

- Voor elk IP-adres een query uitvoeren op een externe Threat Intelligence-provider, zoals [virus totaal](https://www.virustotal.com/), om meer gegevens op te halen.

- Voeg de geretourneerde gegevens en inzichten toe als opmerkingen van het incident.

#### <a name="bi-directional-sync"></a>Bidirectionele synchronisatie

**Playbooks kan worden gebruikt om uw Azure-Sentinel-incidenten te synchroniseren met andere ticket systemen.**

Bijvoorbeeld:

Maak een Automation-regel voor het maken van een incident en koppel een Playbook waarmee een ticket wordt geopend in ServiceNow:

- Starten wanneer er een [Nieuw Azure-verklikker incident wordt gemaakt](/connectors/azuresentinel/#triggers).

- Maak een nieuw ticket in [ServiceNow](/connectors/service-now/).

- Neem in het ticket de naam van het incident, de belang rijke velden en een URL naar het verklikker incident van Azure op om eenvoudig te draaien.

#### <a name="orchestration"></a>Orchestration

**Gebruik het SOC chat-platform om de wachtrij met incidenten beter te beheren.**

Bijvoorbeeld:

Er is een Azure Sentinel-incident gemaakt op basis van een waarschuwing door een analyse regel waarmee gebruikers namen en IP-adres entiteiten worden gegenereerd.

Het incident activeert een Automation-regel waarbij een Playbook wordt uitgevoerd met de volgende stappen:

- Starten wanneer er een [Nieuw Azure-verklikker incident wordt gemaakt](/connectors/azuresentinel/#triggers).

- Stuur een bericht naar uw beveiligings kanaal in [micro soft teams](/connectors/teams/) of een [toegestane vertraging](/connectors/slack/) om te controleren of uw beveiligings analisten op de hoogte zijn van het incident.

- Stuur alle informatie in de waarschuwing per e-mail naar uw senior netwerk beheerder en beveiligings beheerder. Het e-mail bericht bevat de keuze rondjes **blok keren** en gebruikers opties **negeren** .

- Wacht totdat er een antwoord is ontvangen van de beheerders en ga vervolgens verder met uitvoeren.

- Als de beheerders **blok keren** hebben gekozen, verzendt u een opdracht naar de firewall om het IP-adres in de waarschuwing te blok keren en een ander naar Azure AD om de gebruiker uit te scha kelen.

#### <a name="response"></a>Antwoord

**Reageer onmiddellijk op bedreigingen, met minimale menselijke afhankelijkheden.**

Twee voor beelden:

**Voor beeld 1:** Reageer op een Analytics-regel die aangeeft dat er een aangetast gebruiker is, zoals gedetecteerd door [Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md):

   - Starten wanneer er een [Nieuw Azure-verklikker incident wordt gemaakt](/connectors/azuresentinel/#triggers).

   - Voor elke gebruikers entiteit in het incident dat is vermoed als aangetast:

     - Een teams bericht verzenden naar de gebruiker en vragen om bevestiging dat de gebruiker de verdachte actie heeft ondernomen.

     - Neem contact op met Azure AD Identity Protection om te controleren [of de status van de gebruiker is aangetast](/connectors/azureadip/#confirm-a-risky-user-as-compromised). Azure AD Identity Protection krijgt de gebruiker een label als **riskant** en er is al een afdwingings beleid toegepast, bijvoorbeeld om te vereisen dat de gebruiker MFA gebruikt wanneer u zich vervolgens aanmeldt.

        > [!NOTE]
        > De Playbook initieert geen handhavings actie voor de gebruiker en initieert geen configuratie van het afdwingings beleid. Er wordt alleen Azure AD Identity Protection dat er al gedefinieerde beleids regels worden toegepast. Elke afdwinging is volledig afhankelijk van de juiste beleids regels die worden gedefinieerd in Azure AD Identity Protection.

**Voor beeld 2:** Reageer op een Analytics-regel die aangeeft dat er een beschadigde computer is, zoals gedetecteerd door [micro soft Defender voor eind punt](/windows/security/threat-protection/):

   - Starten wanneer er een [Nieuw Azure-verklikker incident wordt gemaakt](/connectors/azuresentinel/#triggers).

   - Gebruik de actie voor het **ophalen van hosts** in azure Sentinel voor het parseren van de verdachte computers die zijn opgenomen in de incident entiteiten.

   - Geef een opdracht op bij micro soft Defender voor eind punt om de computers in de waarschuwing te [isoleren](/connectors/wdatp/#actions---isolate-machine) .

## <a name="how-to-run-a-playbook"></a>Een Playbook uitvoeren

Playbooks kan **hand matig** of **automatisch** worden uitgevoerd.

Als u ze handmatig uitvoert, kunt u ervoor kiezen om een playbook op aanvraag uit te voeren als reactie op de geselecteerde waarschuwing. Deze functie wordt momenteel alleen ondersteund voor waarschuwingen, niet voor incidenten.

Als u deze uitvoert, worden ze automatisch ingesteld als een geautomatiseerd antwoord in een analyse regel (voor waarschuwingen) of als een actie in een Automation-regel (voor incidenten). [Meer informatie over Automation-regels](automate-incident-handling-with-automation-rules.md).

### <a name="set-an-automated-response"></a>Een geautomatiseerd antwoord instellen

Beveiligings bewerkings teams kunnen hun werk belasting aanzienlijk verminderen door de routine reacties op terugkerende incidenten en waarschuwingen volledig te automatiseren, waardoor u meer kunt concentreren op unieke incidenten en waarschuwingen, het analyseren van patronen, het ontdekken van dreigingen en meer.

Het instellen van een geautomatiseerd antwoord betekent dat als er een analyse regel wordt geactiveerd, naast het maken van een waarschuwing, er een Playbook wordt uitgevoerd, dat wordt ontvangen als een invoer van de waarschuwing die door de regel is gemaakt.

Als er door de waarschuwing een incident wordt gemaakt, wordt er een Automation-regel geactiveerd waarbij een Playbook kan worden uitgevoerd, die wordt ontvangen als invoer van het incident dat door de waarschuwing is gemaakt.

#### <a name="alert-creation-automated-response"></a>Automatische respons voor het maken van waarschuwingen

Voor playbooks die worden geactiveerd door het maken van een waarschuwing en het ontvangen van waarschuwingen als invoer (de eerste stap is ' wanneer een Azure Sentinel-waarschuwing wordt geactiveerd '), koppelt u de Playbook aan een analyse regel:

1. Bewerk de [analyse regel](tutorial-detect-threats-custom.md) die de waarschuwing genereert waarvoor u een geautomatiseerd antwoord wilt definiëren.

1. Selecteer onder **waarschuwings automatisering** op het tabblad **geautomatiseerd antwoord** de Playbook of playbooks dat deze analyse regel wordt geactiveerd wanneer er een waarschuwing wordt gemaakt.

#### <a name="incident-creation-automated-response"></a>Automatische respons voor het maken van incidenten

Voor playbooks die worden geactiveerd door incidenten te maken en incidenten te ontvangen als invoer (de eerste stap is "wanneer een Azure Sentinel-incident wordt geactiveerd"), maakt u een Automation-regel en definieert u een actie **Run Playbook** . U kunt dit op twee manieren doen:

- Bewerk de analyse regel waarmee het incident wordt gegenereerd waarvoor u een geautomatiseerd antwoord wilt definiëren. Maak onder **incident automatisering** op het tabblad **automatisch antwoord** een Automation-regel. Hiermee wordt alleen een geautomatiseerd antwoord gemaakt voor deze analyse regel.

- Op het tabblad **Automation-regels** op de Blade **Automation** maakt u een nieuwe Automation-regel en geeft u de juiste voor waarden en gewenste acties op. Deze Automation-regel wordt toegepast op elke analyse regel die voldoet aan de opgegeven voor waarden.

    > [!NOTE]
    > Als u een Playbook wilt uitvoeren vanuit een Automation-regel, gebruikt Azure Sentinel een service account dat speciaal daartoe is gemachtigd. Het gebruik van dit account (in plaats van uw gebruikers account) verhoogt het beveiligings niveau van de service en schakelt de Automation Rules-API in voor de ondersteuning van CI/CD-use cases.
    >
    > Aan dit account moet expliciete machtigingen worden verleend voor de resource groep waar de Playbook zich bevindt. Op dat moment kan elke automatiserings regel elk Playbook in die resource groep uitvoeren.
    >
    > Wanneer u de actie **Playbook uitvoeren** toevoegt aan een Automation-regel, wordt een vervolg keuzelijst met playbooks weer gegeven. Playbooks waaraan Azure Sentinel geen machtigingen heeft, worden weer gegeven als niet-beschikbaar (' lichter gekleurd '). U kunt op de spot machtigingen verlenen voor Azure Sentinel door de koppeling **machtigingen voor Playbook beheren** te selecteren.
    >
    > In een scenario met meerdere tenants ([Lighthouse](extend-sentinel-across-workspaces-tenants.md#managing-workspaces-across-tenants-using-azure-lighthouse)) moet u de machtigingen definiëren voor de Tenant waar de Playbook woont, zelfs als de automatiserings regel die de Playbook aanroept, zich in een andere Tenant bevindt. Hiervoor moet u **eigenaars** machtigingen hebben voor de resource groep van de Playbook.

Zie de [volledige instructies voor het maken van Automation-regels](tutorial-respond-threats-playbook.md#respond-to-incidents).

### <a name="run-a-playbook-manually-on-an-alert"></a>Een Playbook hand matig uitvoeren voor een waarschuwing

Hand matige activering is beschikbaar via de Azure Sentinel-Portal op de volgende blades:

- Kies in **incidenten** weer gave een specifiek incident, open het tabblad **waarschuwingen** en kies een waarschuwing.

- Kies bij **onderzoek** een specifieke waarschuwing.

1. Klik op **Playbooks weer geven** voor de gekozen waarschuwing. U krijgt een lijst van alle playbooks die beginnen met een **Wanneer een Azure-verklikker waarschuwing wordt geactiveerd** en waartoe u toegang hebt.

1. Klik op **uitvoeren** op de regel van een specifieke Playbook om deze te activeren.

1. Selecteer het tabblad **uitvoeringen** om een lijst weer te geven van alle keren dat een Playbook is uitgevoerd op deze waarschuwing. Het kan een paar seconden duren voordat de uitgevoerde uitvoering in deze lijst wordt weer gegeven.

1. Als u op een specifieke uitvoering klikt, wordt de Logic Apps voor het volledige uitvoerings logboek geopend.

### <a name="run-a-playbook-manually-on-an-incident"></a>Een Playbook hand matig uitvoeren op een incident

Nog niet ondersteund. <!--make this a note instead? -->

## <a name="manage-your-playbooks"></a>Uw playbooks beheren

Op het tabblad **Playbooks** wordt een lijst weer gegeven met alle Playbooks waartoe u toegang hebt, gefilterd op de abonnementen die momenteel worden weer gegeven in Azure. Het filter abonnementen is beschikbaar via het menu **map + abonnement** in de kop van de algemene pagina.

Als u op een Playbook-naam klikt, wordt u omgeleid naar de hoofd pagina van de Playbook in Logic Apps. De kolom **status** geeft aan of deze is ingeschakeld of uitgeschakeld.

**Trigger soort** vertegenwoordigt de Logic apps trigger waarmee deze Playbook wordt gestart.

| Type trigger | Hiermee worden onderdeel typen in Playbook aangeduid |
|-|-|
| **Onderverklikker incident/waarschuwing van Azure** | De Playbook wordt gestart met een van de Sentinel-triggers (waarschuwing, incident) |
| **De actie Azure Sentinel gebruiken** | De Playbook wordt gestart met een niet-Sentinel-trigger, maar gebruikt een Azure Sentinel-actie |
| **Overige** | De Playbook bevat geen Sentinel-onderdelen |
| **Niet geïnitialiseerd** | De Playbook is gemaakt, maar bevat geen onderdelen (Triggers of acties). |
|

Op de pagina logische app van Playbook kunt u meer informatie over de Playbook zien, inclusief een logboek van alle keren dat het is uitgevoerd, en het resultaat (geslaagd of mislukt) en andere informatie. U kunt ook de Logic Apps Designer opgeven en de Playbook rechtstreeks bewerken als u de juiste machtigingen hebt.

### <a name="api-connections"></a>API-verbindingen

API-verbindingen worden gebruikt om Logic Apps te verbinden met andere services. Telkens wanneer er een nieuwe verificatie naar een Logic Apps-Connector wordt gemaakt, wordt een nieuwe bron van het type **API-verbinding** gemaakt en bevat de informatie die is opgegeven bij het configureren van de toegang tot de service.

Als u alle API-verbindingen wilt weer geven, voert u *API-verbindingen* in het zoekvak voor koptekst van het Azure Portal. Let op de kolommen met interesse:

- Weergave naam: de beschrijvende naam die u aan de verbinding geeft, elke keer dat u een maakt.
- Status: geeft de verbindings status aan: fout, verbonden.
- Resource groep-API-verbindingen worden gemaakt in de resource groep van de Playbook-resource (Logic Apps).

Een andere manier om API-verbindingen weer te geven, gaat u naar de Blade **alle resources** en filtert u op de type- *API-verbinding*. Op deze manier kan de selectie, het labelen en het verwijderen van meerdere verbindingen tegelijk worden toegestaan.

Als u de autorisatie van een bestaande verbinding wilt wijzigen, voert u de verbindings bron in en selecteert u **API-verbinding bewerken**.

## <a name="next-steps"></a>Volgende stappen

- [Zelf studie: playbooks gebruiken om bedreigings reacties in azure Sentinel te automatiseren](tutorial-respond-threats-playbook.md)
