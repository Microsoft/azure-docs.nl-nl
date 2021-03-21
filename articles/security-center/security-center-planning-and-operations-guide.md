---
title: Plannings- en bedieningsgids voor Security Center
description: Dit document helpt u met uw planning voordat u overstapt op het Azure Beveiligingscentrum en bevat aandachtspunten voor het dagelijks gebruik.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: tutorial
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: a8b285ca0b8d0dae88bfe8dd19ac7e1bc8372e32
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102099196"
---
# <a name="planning-and-operations-guide"></a>Handleiding voor planning en bewerking
Deze gids is bedoeld voor IT-specialisten, IT-architecten, gegevensbeveiligingsanalisten en cloudbeheerders die willen gaan werken met Azure Security Center.


## <a name="planning-guide"></a>Planningshandleiding
Deze gids bevat taken die u kunt uitvoeren om uw gebruik van Azure Security Center te optimaliseren op basis van de beveiligingsvereisten en het cloudbeheermodel van uw organisatie. Als u optimaal wilt profiteren van Security Center, is het belangrijk te begrijpen hoe verschillende personen of teams binnen uw organisatie de service gaan gebruiken om te voldoen aan alle vereisten voor veilige ontwikkeling en gebruik, bewaking, bestuur en reactie op incidenten. De belangrijkste gebieden waarmee u rekening moet houden bij het plannen van het gebruik van Security Center zijn:

* Beveiligingsrollen en toegangsbeheer
* Beveiligingsbeleid en aanbevelingen
* Gegevensverzameling en -opslag
* Onboarding van niet-Azure-resources
* Continue beveiligingsbewaking
* Reageren op incidenten

In de volgende sectie leert u hoe u voor elk van deze gebieden een planning maakt en hoe u deze aanbevelingen toepast op grond van uw vereisten.


> [!NOTE]
> Raadpleeg de [Azure Security Center frequently asked questions (Veelgestelde vragen over het Azure Beveiligingscentrum)](faq-general.md) voor een lijst met veelgestelde vragen die ook nuttig kunnen zijn tijdens de ontwerp- en planningsfase.

## <a name="security-roles-and-access-controls"></a>Beveiligingsrollen en toegangsbeheer
Afhankelijk van de grootte en de structuur van uw organisatie kunnen meerdere individuen en teams het Beveiligingscentrum gebruiken om verschillende beveiligingstaken uit te voeren. In het volgende diagram vindt u een voorbeeld met fictieve personen en hun respectieve rollen en beveiligingsverantwoordelijkheden:

![Rollen](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig01-new.png)

Met Security Center kunnen deze personen voldoen aan deze verschillende verantwoordelijkheden. Bijvoorbeeld:

**Jeff (eigenaar van workload)**

* Een cloud-workload en de bijbehorende resources beheren
* Verantwoordelijk voor het implementeren en onderhouden van beveiliging volgens het beveiligingsbeleid van het bedrijf

**Ellen (CISO/CIO)**

* Verantwoordelijk voor alle beveiligingsaspecten van het bedrijf
* Wil de beveiliging van het bedrijf in alle cloud-workloads begrijpen
* Moet worden geïnformeerd over belangrijke aanvallen en risico 's

**David (IT-beveiliging)**

* Stelt beveiligingsbeleid in om er zeker van te zijn dat de juiste beveiliging aanwezig is
* Bewaakt naleving van beleid
* Genereert rapporten voor leidinggevenden of auditors

**Judy (beveiligingsbewerkingen)**

* Bewaakt en reageert 24/7 op beveiligingswaarschuwingen
* Escaleert naar eigenaar cloud-workload of IT-beveiligingsanalist

**Sam (beveiligingsanalist)**

* Onderzoekt aanvallen
* Werkt samen met de eigenaar van de workload in de cloud om herstelstappen toe te passen

Security Center maakt gebruik van [op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md), dat [ingebouwde rollen](../role-based-access-control/built-in-roles.md) biedt die kunnen worden toegewezen aan gebruikers, groepen en services in Azure. Wanneer gebruikers Security Center openen, zien ze alleen informatie met betrekking tot resources waartoe ze toegang hebben. Dit betekent dat aan de gebruiker de rol van de eigenaar, bijdrager of lezer is toegewezen voor het abonnement of de resourcegroep waartoe een resource behoort. Naast deze rollen zijn er twee specifieke Security Center-rollen:

- **Beveiligingslezer**: een gebruiker die deel uitmaakt van deze rol kan alleen configuraties van Security Center bekijken, en kan dus onder andere aanbevelingen, waarschuwingen, beleid en statusinformatie bekijken, maar kan geen wijzigingen aanbrengen.
- **Beveiligingsbeheerder**: dezelfde machtigingen als de rol Beveiligingslezer, maar kan ook het beveiligingsbeleid bijwerken, en aanbevelingen en waarschuwingen verwijderen.

De hierboven beschreven Security Center-rollen hebben geen toegang tot andere servicegebieden van Azure, zoals Storage, Web & Mobile of Internet of Things.

Wanneer wordt gebruikgemaakt van de personen in het vorige diagram, zou de volgende Azure-RBAC nodig zijn:

**Jeff (eigenaar van workload)**

* Resourcegroepeigenaar/inzender

**Ellen (CISO/CIO)**

* Abonnementseigenaar/inzender of beveiligingsbeheerder

**David (IT-beveiliging)**

* Abonnementseigenaar/inzender of beveiligingsbeheerder

**Judy (beveiligingsbewerkingen)**

* Abonnementslezer of beveiligingslezer voor het bekijken van waarschuwingen
* Abonnementseigenaar/inzender of beveiligingsbeheerder voor het verwijderen van waarschuwingen

**Sam (beveiligingsanalist)**

* Abonnementslezer voor het bekijken van waarschuwingen
* Abonnementseigenaar/inzender voor het verwijderen van waarschuwingen
* Toegang tot de werkruimte is mogelijk vereist

Andere belangrijke informatie om rekening mee te houden:

* Alleen abonnementseigenaren/medewerkers en beveiligingsbeheerders kunnen een beveiligingsbeleid bewerken.
* Alleen abonnements- en resourcegroepeigenaren en bijdragers kunnen beveiligingsaanbevelingen voor een resource toepassen.

Bij het plannen van toegangsbeheer met Azure-RBAC voor Security Center moet u zeker weten wie in uw organisatie Security Center gaan gebruiken. Ook moet u weten welke typen taken zij gaan uitvoeren en Azure-RBAC vervolgens dienovereenkomstig configureren.

> [!NOTE]
> We raden u aan de rol toe te wijzen die gebruikers minimaal nodig hebben om hun taken uit te voeren. Wijs bijvoorbeeld gebruikers die alleen informatie hoeven te bekijken over de beveiligingstoestand van resources, maar geen maatregelen hoeven te nemen zoals het toepassen van aanbevelingen of het bewerken van beleid, de rol Lezer toe.
>
>

## <a name="security-policies-and-recommendations"></a>Beveiligingsbeleid en aanbevelingen
Een beveiligingsbeleid definieert de gewenste configuratie van uw workloads en helpt ervoor te zorgen dat aan de beveiligingsvereisten van het bedrijf of aan regelgeving wordt voldaan. In Security Center kunt u beleidsregels definiëren voor uw Azure-abonnementen, die kunnen worden afgestemd op het type workload of de vertrouwelijkheid van gegevens.

Beleidsregels van Security Center bevatten de volgende onderdelen:
- [Gegevensverzameling](security-center-enable-data-collection.md): instellingen voor configuratie van agent en verzamelen van gegevens.
- [Beveiligingsbeleid](tutorial-security-policy.md): een [Azure-beleid](../governance/policy/overview.md) dat bepaalt welke items worden bewaakt en aanbevolen door Security Center, of welke items Azure-beleid gebruiken voor het maken van nieuwe definities, het definiëren van extra beleidsregels en het toewijzen van beleidsregels binnen beheergroepen.
- [E-mailmeldingen](security-center-provide-security-contact-details.md): instellingen voor contactpersonen en meldingen voor beveiliging.
- [Prijscategorie](security-center-pricing.md): met of zonder Azure Defender, die bepaalt welke functies van Security Center beschikbaar zijn voor resources binnen het bereik (kan worden opgegeven voor abonnementen en werkruimten of resourcegroepen die de API gebruiken).

> [!NOTE]
> Het opgeven van een contactpersoon voor beveiliging zorgt ervoor dat Azure de juiste persoon in uw organisatie kan bereiken als er zich een beveiligingsincident voordoet. Lees [Contactgegevens voor beveiliging verstrekken in Azure Security Center](security-center-provide-security-contact-details.md) voor meer informatie over het inschakelen van deze mogelijkheid.

### <a name="security-policies-definitions-and-recommendations"></a>Definities en aanbevelingen van beveiligingsbeleid
In Security Center wordt voor elk van uw Azure-abonnementen automatisch een standaardbeveiligingsbeleid gemaakt. U kunt het beleid bewerken in Security Center of Azure Policy gebruiken voor het maken van nieuwe definities, het definiëren van extra beleidsregels en het toewijzen van beleidsregels binnen beheergroepen (die de hele organisatie, een bedrijfseenheid, enzovoort kan vertegenwoordigen), en naleving van deze beleidsregels bewaken binnen deze bereiken.

Voordat u beleidsregels voor veiligheid configureert, moet u elk van de [aanbevelingen voor beveiliging](security-center-recommendations.md) controleren en bepalen of deze beleidsregels geschikt zijn voor uw verschillende abonnementen en resourcegroepen. Het is ook belangrijk om te begrijpen welke actie moet worden ondernomen om aan de slag te gaan met beveiligingsaanbevelingen en wie in uw organisatie verantwoordelijk is voor het controleren op nieuwe aanbevelingen en het nemen van de benodigde stappen.

## <a name="data-collection-and-storage"></a>Gegevensverzameling en -opslag
Azure Security Center maakt gebruik van de Log Analytics-agent. Dit is dezelfde agent die ook wordt gebruikt door de Azure Monitor-service om beveiligingsgegevens van uw virtuele machines te verzamelen. [Gegevens die worden verzameld](security-center-enable-data-collection.md) van deze agent worden opgeslagen in uw Log Analytics-werkruimte(n).

### <a name="agent"></a>Agent

Als automatisch inrichten is ingeschakeld in het beveiligingsbeleid, wordt de Log Analytics-agent (voor [Windows](../azure-monitor/agents/agent-windows.md) of [Linux](../azure-monitor/vm/quick-collect-linux-computer.md)) geïnstalleerd op alle ondersteunde virtuele machines van Azure en op nieuwe virtuele machines die worden gemaakt. Als de Log Analytics-agent al op de virtuele machine of computer is geïnstalleerd, maakt Azure Security Center gebruik van de huidige geïnstalleerde agent. Het proces van de agent is ontworpen om niet-invasief te zijn en minimale invloed te hebben op de prestaties van de virtuele machine.

De Log Analytics-agent voor Windows vereist TCP-poort 443. Zie de [Handleiding voor het oplossen van problemen met Azure Security Center](security-center-troubleshooting-guide.md) voor meer informatie.

Als u gegevensverzameling op een bepaald moment wilt uitschakelen, kunt u dat doen in het beveiligingsbeleid. Omdat de Log Analytics-agent kan worden gebruikt door andere Azure-services voor beheer en controle, wordt de agent niet automatisch verwijderd wanneer u het verzamelen van gegevens uitschakelt in Security Center. U kunt de agent indien nodig echter handmatig verwijderen.

> [!NOTE]
> Lees de [veelgestelde vragen over Azure Security Center](faq-vms.md) (Engelstalig) als u een lijst met ondersteunde virtuele machines nodig hebt.

### <a name="workspace"></a>Werkruimte

Een werkruimte is een Azure-resource die als een container voor gegevens fungeert. U of andere leden van uw organisatie kunnen meerdere werkruimten gebruiken om verschillende gegevenssets te beheren die worden verzameld uit de gehele of delen van uw IT-infrastructuur.

Gegevens die (namens Azure Security Center) via de Log Analytics-agent worden verzameld, worden opgeslagen in een bestaande Log Analytics-werkruimte die is gekoppeld aan uw Azure-abonnement, of in een nieuwe werkruimte, rekening houdend met de geolocatie van de virtuele machine.

In Azure Portal kunt u bladeren om een overzicht te zien van uw Log Analytics-werkruimten, inclusief alle werkruimten die door Azure Security Center zijn gemaakt. Er wordt een gerelateerde resourcegroep gemaakt voor nieuwe werkruimten. Beide volgen deze naamconventie:

* Werkruimte: *DefaultWorkspace-[abonnements-id]-[geolocatie]*
* Resourcegroep: *DefaultResourceGroup-[geo]*

Voor werkruimten die zijn gemaakt door Azure Security Center worden gegevens 30 dagen bewaard. Voor bestaande werkruimten is de bewaarperiode gebaseerd op de prijscategorie van de werkruimte. Als u wilt, kunt u ook een bestaande werkruimte gebruiken.

> [!NOTE]
> Microsoft doet er alles aan om de privacy van gegevens te beschermen en deze gegevens te beveiligen. Microsoft voldoet aan strikte nalevings- en beveiligingsrichtlijnen - van het schrijven van code tot de uitvoering van een service. Lees [Gegevensbeveiliging van Azure Security Center](security-center-data-security.md) voor meer informatie over de verwerking van gegevens en privacy.
>

## <a name="onboard-non-azure-resources"></a>Niet-Azure-resources onboarden

Security Center kan de beveiligingsstatus van uw niet-Azure-computers controleren, maar u moet deze resources dan eerst onboarden. Lees [Onboarding van niet-Azure-computers](quickstart-onboard-machines.md) voor meer informatie over het onboarden van niet-Azure-resources.

## <a name="ongoing-security-monitoring"></a>Continue beveiligingsbewaking
Na de eerste configuratie en toepassing van aanbevelingen voor Security Center, komen in de volgende stap de operationele processen voor Security Center ter sprake.

In het Security Center-overzicht ziet u beknopte informatie over de beveiliging van alle Azure-resources en van eventuele niet-Azure-resources die u hebt gekoppeld. In het onderstaande voorbeeld ziet u een omgeving waarin heel wat problemen moeten worden opgelost:

![Dashboard](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig11.png)

> [!NOTE]
> Security Center heeft geen invloed op uw normale operationele procedures. Het bewaakt uw implementaties passief en doet aanbevelingen op grond van het beveiligingsbeleid dat u hebt ingeschakeld.

Wanneer u Security Center voor het eerst inschakelt voor uw huidige Azure-omgeving, moet u alle aanbevelingen doornemen. Dit kunt u doen op de pagina **Aanbevelingen**.

Het is een goed idee om de functie Bedreigingsinformatie iedere dag even te bekijken. U daar bedreigingen van de omgeving identificeren, bijvoorbeeld dat een bepaalde computer deel uitmaakt van een botnet.

### <a name="monitoring-for-new-or-changed-resources"></a>Bewaking voor nieuwe of gewijzigde resources

De meeste Azure-omgevingen zijn dynamisch, waarbij resources met vaste regelmaat worden gemaakt, omhoog of omlaag verplaatst, opnieuw worden geconfigureerd en gewijzigd. Security Center zorgt ervoor dat de beveiligingsstatus van deze nieuwe resources voor u goed zichtbaar is.

Wanneer u nieuwe resources (virtuele machines, SQL Databases) toevoegt aan uw Azure-omgeving, detecteert Security Center deze resources automatisch en begint het de beveiliging ervan te bewaken. Dit omvat ook PaaS-webrollen en -werkrollen. Als gegevensverzameling wordt ingeschakeld in het [beveiligingsbeleid](tutorial-security-policy.md), worden er automatisch extra bewakingsmogelijkheden ingeschakeld voor uw virtuele machines.

U moet bestaande resources ook regelmatig controleren om configuratiewijzigingen te identificeren die zouden kunnen leiden tot beveiligingsrisico's, afwijking van aanbevolen basislijnen en beveiligingswaarschuwingen. 

### <a name="hardening-access-and-applications"></a>Toegang en toepassingen beperken

Als onderdeel van uw beveiligingsbeleid moet u preventieve maatregelen nemen om de toegang tot virtuele machines te beperken en te bepalen welke toepassingen mogen worden uitgevoerd op virtuele machines. Door beperkingen in te stellen voor het inkomende verkeer naar uw VM's in Azure, vermindert u de blootstelling aan aanvallen en kunnen gebruikers tegelijkertijd eenvoudig verbinding maken met virtuele machines wanneer dit nodig is. Gebruik de functie [Just-In-Time-VM-toegang](security-center-just-in-time.md) om de toegang tot uw virtuele machines te beperken.

U kunt [besturingselementen voor adaptieve toepassingen](security-center-adaptive-application.md) gebruiken om te bepalen welke toepassingen op uw VM's kunnen worden uitgevoerd. Een van de voordelen daarvan is dat u uw virtuele machines zo kunt beschermen tegen malware. Met behulp van machine learning analyseert Security Center processen die worden uitgevoerd in de VM om u te helpen bij het opstellen van regels voor het toestaan van vermeldingen.


## <a name="incident-response"></a>Reageren op incidenten
Security Center detecteert en waarschuwt voor bedreigingen wanneer deze optreden. Organisaties moeten controleren op nieuwe beveiligingswaarschuwingen en zo nodig maatregelen nemen om het probleem verder te onderzoeken of de aanval af te weren. Lees [Hoe Azure Security bedreigingen detecteert en daarop reageert](security-center-alerts-overview.md#detect-threats) voor meer informatie over de werking van detectie van bedreigingen in Azure Security Center.

Hoewel dit artikel niet de bedoeling heeft om u te helpen uw eigen plan voor het reageren op incidenten te maken, maken we gebruik van Microsoft Azure Security Response in de cloud-levenscyclus als de basis voor fasen voor het reageren op incidenten. In het volgende diagram ziet u de fasen:

![Fasen van de reactie op incidenten in de cloudlevenscyclus](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-1.png)

> [!NOTE]
> U kunt de [Computer Security Incident Handling Guide](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) van het National Institute of Standards and Technology (NIST) gebruiken als richtlijn om uw eigen plan te ontwikkelen.
>

U kunt waarschuwingen van Security Center gebruiken tijdens de volgende fasen:

* **Detecteren**: een verdachte activiteit in een of meer resources identificeren.
* **Beoordelen**: de eerste beoordeling uitvoeren voor meer informatie over de verdachte activiteiten.
* **Diagnose uitvoeren**: de herstelstappen gebruiken voor het uitvoeren van de technische procedure om het probleem op te lossen.

Elke beveiligingswaarschuwing bevat informatie die kan worden gebruikt om beter te begrijpen wat de aard van de aanval is en om mogelijke oplossingen voor te stellen. Sommige waarschuwingen bevatten ook koppelingen naar meer informatie of naar andere informatiebronnen binnen Azure. U kunt de weergegeven informatie gebruiken voor verder onderzoek en om te beginnen met risicobeperking. Bovendien kunt u zoeken in beveiligingsgegevens die zijn opgeslagen in uw werkruimte.

Het volgende voorbeeld betreft een verdachte RDP-activiteit die op dat moment plaatsvindt:

![Verdachte activiteit](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-ga.png)

Deze pagina bevat informatie over het tijdstip waarop de aanval plaatsvond, de hostnaam van de bron, de VM die het doelwit was en aanbevolen maatregelen. Onder bepaalde omstandigheden kunnen de brongegevens van de aanval leeg zijn. Lees [Missing Source Information in Azure Security Center Alerts](/archive/blogs/azuresecurity/missing-source-information-in-azure-security-center-alerts) voor meer informatie over dit type gedrag.

Wanneer u het geïnfecteerde systeem hebt geïdentificeerd, kunt u een [workflowautomatisering ](workflow-automation.md) uitvoeren die eerder is gemaakt. Dit is een verzameling procedures die na activering door een waarschuwing vanuit Security Center kunnen worden uitgevoerd.

In de video [Reageren op incidenten met Azure Security Center en Microsoft Operations Management Suite](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703) ziet u enkele demonstraties die laten zien hoe Security Center kan worden gebruikt in elk van deze fasen.

> [!NOTE]
> Lees [Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center](security-center-managing-and-responding-alerts.md) voor meer informatie over hoe u Security Center kunt gebruiken wanneer u reageert op incidenten.
>
>

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u kunnen lezen hoe u een planning kunt maken voor het overstappen op Security Center. Zie de volgende onderwerpen voor meer informatie over Security Center:

* [Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Partneroplossingen bewaken met Azure Security Center](./security-center-partner-integration.md): leer hoe u de integriteitsstatus van uw partneroplossingen kunt bewaken.
* [Azure Security Center FAQ](faq-general.md): raadpleeg veelgestelde vragen over het gebruik van de service.
* [Azure-beveiligingsblog](/archive/blogs/azuresecurity/): lees blogberichten over de beveiliging en naleving van Azure.