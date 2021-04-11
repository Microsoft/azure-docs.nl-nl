---
title: Overzicht van Azure Internet peering voor communicatie Services
titleSuffix: Azure
description: Overzicht van Azure Internet peering voor communicatie Services
services: internet-peering
author: gthareja
ms.service: internet-peering
ms.topic: how-to
ms.date: 03/30/2021
ms.author: gatharej
ms.openlocfilehash: ff6750883a904ff5ddbddd3ddfd1ed82e72aebbc
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106498960"
---
# <a name="azure-internet-peering-for-communications-services-walkthrough"></a>Overzicht van Azure Internet peering voor communicatie Services

In deze sectie wordt uitgelegd welke stappen een communicatie Services-provider moet volgen om een rechtstreekse verbinding met micro soft tot stand te brengen.

**Communicatie services-providers:**  Communicatie services-providers zijn de organisaties die communicatie services aanbieden (communicatie, berichten, vergaderingen enz.) en de infra structuur van de communicatie Services (SBC/SIP-gateway enz.) willen integreren.  met Azure Communication Services en micro soft teams. 

Azure Internet peering ondersteunt communicatie services-providers om direct verbinding met micro soft tot stand te brengen met behulp van hun Edge-sites (pop-locaties). De lijst met alle sites met een open bare rand is beschikbaar in [PeeringDB](https://www.peeringdb.com/net/694).

Azure Internet peering voorziet in zeer betrouw bare en QoS (Quality of Service) ingeschakelde Interconnect voor communicatie Services om hoogwaardige en hoogwaardige services te garanderen.

## <a name="technical-requirements"></a>Technische vereisten
De technische vereisten voor het tot stand brengen van direct Interconnect voor communicatie Services zijn als volgt:
-   De peer moet een eigen autonoom systeem nummer (ASN) opgeven. dit moet openbaar zijn.
-   De peer moet een redundante Interconnect (PNI) hebben op elke Interconnect-locatie om lokale redundantie te garanderen.
-   De peer moet geo-redundantie hebben om ervoor te zorgen dat failover plaatsvindt in het geval van site storingen in de regio/Metro lijn.
-   De peer moet de BGP-sessies als actief-actief hebben om hoge Beschik baarheid en snellere convergentie te garanderen en niet als primaire en back-up moeten worden ingericht.
-   De peer moet een verhouding van 1:1 voor peer-peering-routers onderhouden voor peering-circuits en er geen snelheids beperking wordt toegepast.
-   De peer moet hun eigen, door de communicatie service-eind punten (bijvoorbeeld SBC) gebruikte, open bare IPv4-adres ruimte leveren en adverteren. 
-   De peer moet Details opgeven over welke klasse van verkeer en eind punten in elk geadverteerd subnet zijn gehuisvest. 
-   De peer moet BGP via bidirectionele doorstuur detectie (BFD) uitvoeren om de convergentie van sub Second-routes te vergemakkelijken.
-   Alle voor voegsels voor communicatie-infra structuur worden in Azure Portal geregistreerd en geadverteerd met de Community-teken reeks 8075:8007.
-   Peering mag niet worden beëindigd op een apparaat met een stateful firewall. 
-   Micro soft configureert alle Interconnect-koppelingen standaard als vertraging (koppelings bundels), dus moet peer LACP (Link Aggregation Control Protocol) ondersteunen op de Interconnect-koppelingen.

## <a name="establishing-direct-interconnect-with-microsoft-for-communications-services"></a>Verbinding maken met micro soft voor communicatie Services.

Voer de onderstaande stappen uit om een rechtstreekse verbinding tot stand te brengen met Azure Internet peering:

**1. Open peer openbaar ASN aan het Azure-abonnement:**

Als de peer al een openbaar ASN voor het Azure-abonnement heeft gekoppeld, moet u deze stap overs Laan.

[Peer-ASN koppelen aan een Azure-abonnement met behulp van de portal-Azure | Microsoft Docs](https://docs.microsoft.com/azure/internet-peering/howto-subscription-association-portal)

De volgende stap is het maken van een rechtstreekse peering verbinding voor de peering-service.

> [!NOTE]
> Zodra de ASN-koppeling is goedgekeurd, peeringservices@microsoft.com kunt u een e-mail sturen naar uw ASN-en abonnements-id om uw abonnement te koppelen aan communicatie Services. 

**2. directe peering-verbinding maken voor de peering-service:**

Volg de instructies voor [het maken of wijzigen van een directe peering met behulp van de portal](https://docs.microsoft.com/azure/internet-peering/howto-direct-portal)

Zorg ervoor dat het voldoet aan de vereiste voor hoge Beschik baarheid.

Zorg ervoor dat u de volgende opties selecteert op de pagina ' een peering maken ':

Peering-type:   **direct**

Micro soft-netwerk:  **8075**

SKU:        **Premium gratis**


Selecteer onder ' directe peering-verbindings pagina ' de volgende opties:

Sessie adres provider:   **micro soft**

Gebruiken voor peering Services:   **ingeschakeld**

> [!NOTE] 
> Het volgende bericht negeren bij het selecteren voor peering Services.
> *Schakel deze optie niet in, tenzij u contact hebt opgenomen met peering@microsoft.com een Maps-provider.*


  **2a. gebruik een bestaande directe peering verbinding voor peering Services**

Als u een bestaande directe peering hebt die u wilt gebruiken ter ondersteuning van de peering-service, kunt u activeren op Azure Portal.
1.  Volg de instructies voor [het converteren van een verouderde directe peering naar Azure-resource met behulp van de portal](https://docs.microsoft.com/azure/internet-peering/howto-legacy-direct-portal).
Bestel zo nodig extra circuits om te voldoen aan de vereiste voor hoge Beschik baarheid.

2.  Volg de stappen om [peering service in te scha kelen](https://docs.microsoft.com/azure/internet-peering/howto-peering-service-portal) op een directe peering met behulp van de portal.




**3. uw voor voegsels voor geoptimaliseerde route ring registreren**

Voor geoptimaliseerde route ring voor de voor voegsels van uw communicatie Services-infra structuur moet u al uw voor voegsels met uw peering-interconnects registreren.
[Azure peering-service registreren-Azure Portal | Microsoft Docs](https://docs.microsoft.com/azure/peering-service/azure-portal)

De prefix sleutel wordt automatisch ingevuld voor communicatie service partners, dus de partner hoeft geen voor voegsel te gebruiken om te registreren. 

Zorg ervoor dat de geregistreerde voor voegsels worden aangekondigd over de directe interconnecties die zijn ingesteld voor de regio.


## <a name="faqs"></a>Veelgestelde vragen:

**Nils.**  Ik heb kleinere subnetten (</24) voor mijn communicatie Services. Kan ik de kleinere subnetten ook routeren?

**Één.**  Ja, Microsoft Azure peering-service ondersteunt ook de route ring van kleinere voor voegsels. Zorg ervoor dat u de kleinere voor voegsels voor route ring registreert en dat hetzelfde wordt aangekondigd via de onderlinge verbindingen.

**Nils.**  Welke micro soft-routes worden ontvangen via deze interconnects?

**Één.** Micro soft introduceert alle open bare voor voegsels van micro soft voor deze onderlinge verbindingen. Dit zorgt ervoor dat er niet alleen communicaties, maar andere Cloud Services toegankelijk zijn vanuit dezelfde Interconnect.

**Nils.**  Ik wil de limiet voor het voor voegsel instellen, het aantal routes dat micro soft zou aankondigen?

**Één.** Micro soft kondigt ongeveer 280 voor voegsels toe op internet en kan in de toekomst met 10-15% worden verhoogd. Een veilige limiet van 400-500 kan dus goed worden ingesteld als "maximum aantal voor voegsels"

**Nils.** Adverteert micro soft de peer-voor voegsels opnieuw aan het Internet?

**Één.** Nee.

**Nils.** Zijn er kosten verbonden aan deze service?

**Één.** Nee, maar er wordt naar verwachting de Cross Connect-kosten van de site worden overgedragen.

**Nils.** Wat is de minimale verbindings snelheid voor een Interconnect?

**Één.** 10 Gbps.

**Nils.** Is de peer gebonden aan een SLA?

**Één.** Ja, wanneer het gebruik is bereikt 40%, moet een 60day VERTRAGINGs proces van 45% worden gestart.

**Nils.** Wat is het voor deel van deze service via de huidige directe peering of Express route?

**Één.** Het gratis en volledige pad van de vereffening is geoptimaliseerd voor spraak verkeer via micro soft WAN en convergentie is afgestemd op BFD.

**Nils.** Hoe voltooit u het voorbereidings proces?

**Één.** De tijd wordt variabele, afhankelijk van het aantal en de locatie van de sites, en als de peer bestaande particuliere peerings migreert of nieuwe bekabeling tot stand brengt. De transporteur moet 3 en weken plannen.

**Nils.** Kunnen we Api's gebruiken voor onboarding?

**Één.** Er is momenteel geen API-ondersteuning en de configuratie moet worden uitgevoerd via de webportal. 
