---
title: Azure AD-wachtwoord beveiliging-Azure Active Directory
description: Zwakke wacht woorden in on-premises Active Directory Domain Services omgevingen verbieden door gebruik te maken van Azure AD-wachtwoord beveiliging
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f5df1cb158821fb0cd85d90f9ba3b79d80adf45
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96743918"
---
# <a name="enforce-on-premises-azure-ad-password-protection-for-active-directory-domain-services"></a>On-premises Azure AD-wachtwoord beveiliging afdwingen voor Active Directory Domain Services

Azure AD-wachtwoord beveiliging detecteert en blokkeert bekende zwakke wacht woorden en hun varianten, en kan ook extra zwakke termen die specifiek zijn voor uw organisatie blok keren. De on-premises implementatie van Azure AD-wachtwoord beveiliging maakt gebruik van dezelfde algemene en aangepaste verboden wachtwoord lijsten die zijn opgeslagen in azure AD en voert dezelfde controles uit voor on-premises wachtwoord wijzigingen als Azure AD voor wijzigingen in de Cloud. Deze controles worden uitgevoerd tijdens het wijzigen van het wacht woord en het opnieuw instellen van wacht woorden op domein controllers op locatie Active Directory Domain Services (AD DS).

## <a name="design-principles"></a>Ontwerpprincipes

Azure AD-wachtwoord beveiliging is ontworpen met de volgende principes:

* Domein controllers (Dc's) hoeven nooit rechtstreeks met Internet te communiceren.
* Er zijn geen nieuwe netwerk poorten geopend op Dc's.
* Er zijn geen wijzigingen in het AD DS schema vereist. De software maakt gebruik van de bestaande AD DS- *container* en de schema objecten van *serviceConnectionPoint* .
* Er is geen minimum AD DS domein of forest-functionaliteits niveau (DFL/FFL) vereist.
* De software maakt of vereist geen accounts in de AD DS domeinen die deze beveiligt.
* Wacht woorden voor ongecodeerde tekst van de gebruiker houden nooit de domein controller, hetzij tijdens wachtwoord validatie bewerkingen of op een ander tijdstip.
* De software is niet afhankelijk van andere Azure AD-functies. Bijvoorbeeld: Azure AD Password Hash Sync (PHS) is niet gerelateerd aan of vereist voor Azure AD-wachtwoord beveiliging.
* Incrementele implementatie wordt ondersteund, maar het wachtwoord beleid wordt alleen afgedwongen wanneer de domein controller agent (DC-agent) is geïnstalleerd.

## <a name="incremental-deployment"></a>Incrementele implementatie

Azure AD-wachtwoord beveiliging ondersteunt incrementele implementatie over Dc's in een AD DS domein. Het is belang rijk om te begrijpen wat dit echt betekent en wat de voor afwegingen zijn.

Met de Azure AD-agent software voor wachtwoord beveiliging kunnen alleen wacht woorden worden gevalideerd wanneer deze zijn geïnstalleerd op een domein controller en alleen voor wachtwoord wijzigingen die naar die domein controller worden verzonden. Het is niet mogelijk om te bepalen welke Dc's door Windows-client machines worden gekozen voor het verwerken van wijzigingen in het gebruikers wachtwoord. Om consistent gedrag en universele afdwinging van Azure AD-wachtwoord beveiliging te garanderen, moet de DC-agent software worden geïnstalleerd op alle domein controllers in een domein.

Veel organisaties willen de Azure AD-wachtwoord beveiliging zorgvuldig testen op een subset van hun Dc's vóór een volledige implementatie. Ter ondersteuning van dit scenario ondersteunt Azure AD-wachtwoord beveiliging gedeeltelijke implementatie. De DC-agent software op een bepaalde domein controller valideert wacht woorden zelfs wanneer andere domein controllers in het domein de DC-agent software niet hebben geïnstalleerd. Gedeeltelijke implementaties van dit type zijn niet veilig en worden niet aanbevolen voor test doeleinden.

## <a name="architectural-diagram"></a>Architectuur diagram

Het is belang rijk om inzicht te krijgen in het onderliggende ontwerp en functionele concepten voordat u Azure AD-wachtwoord beveiliging in een on-premises AD DS omgeving implementeert. In het volgende diagram ziet u hoe de onderdelen van Azure AD-wachtwoord beveiliging samen werken:

![Hoe de Azure AD-onderdelen voor wachtwoord beveiliging samen werken](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* De Azure AD-proxy service voor wachtwoord beveiliging wordt uitgevoerd op een computer die lid is van een domein in het huidige AD DS-forest. Het primaire doel van de service is het door sturen van aanvragen voor wachtwoord beleid van Dc's naar Azure AD en het retour neren van de antwoorden van Azure AD naar de domein controller.
* De wachtwoord filter-DLL van de DC-agent ontvangt aanvragen voor validatie van gebruikers wachtwoorden van het besturings systeem. Het filter stuurt deze door naar de DC-Agent service die lokaal op de domein controller wordt uitgevoerd.
* De DC-Agent service van Azure AD-wachtwoord beveiliging ontvangt aanvragen voor wachtwoord validatie van de wachtwoord filter-DLL van de DC-agent. De DC-Agent service verwerkt deze met behulp van het huidige (lokaal beschik bare) wachtwoord beleid en retourneert het resultaat van *slagen* of *mislukken*.

## <a name="how-azure-ad-password-protection-works"></a>Hoe werkt Azure AD-wachtwoord beveiliging?

De on-premises Azure AD-onderdelen voor wachtwoord beveiliging werken als volgt:

1. Elk Azure AD-service-exemplaar van het wacht woord voor wachtwoord beveiliging adverteert zichzelf aan de Dc's in het forest door een *serviceConnectionPoint* -object te maken in Active Directory.

    Elke DC-Agent service voor Azure AD-wachtwoord beveiliging maakt ook een *serviceConnectionPoint* -object in Active Directory. Dit object wordt hoofd zakelijk gebruikt voor rapportage en diagnose.

1. De DC-Agent service is verantwoordelijk voor het initiëren van het downloaden van een nieuw wachtwoord beleid vanuit Azure AD. De eerste stap is het vinden van een Azure AD-proxy service voor wachtwoord beveiliging door het uitvoeren van een query op het forest voor proxy *serviceConnectionPoint* -objecten.

1. Wanneer een beschik bare proxy service wordt gevonden, verzendt de DC-agent een aanvraag voor wachtwoord beleid downloaden naar de proxy service. De proxy service op zijn beurt verzendt de aanvraag naar Azure AD en retourneert vervolgens de reactie van de DC-Agent service.

1. Nadat de DC-Agent service een nieuw wachtwoord beleid van Azure AD heeft ontvangen, slaat de service het beleid op in een speciale map in de hoofdmap van de map *SYSVOL* van het domein. De DC-Agent service bewaakt deze map ook voor het geval nieuwere beleids regels worden gerepliceerd vanuit andere services van de DC-agent in het domein.

1. De DC-Agent service vraagt altijd een nieuw beleid op bij het starten van de service. Nadat de service DC-agent is gestart, wordt de leeftijd van het huidige lokaal beschik bare beleid per uur gecontroleerd. Als het beleid ouder is dan één uur, vraagt de DC-agent een nieuw beleid aan bij Azure AD via de proxy service, zoals eerder is beschreven. Als het huidige beleid niet ouder is dan één uur, blijft de DC-agent dit beleid gebruiken.

1. Wanneer wachtwoord wijzigings gebeurtenissen worden ontvangen door een domein controller, wordt het beleid in de cache gebruikt om te bepalen of het nieuwe wacht woord wordt geaccepteerd of geweigerd.

### <a name="key-considerations-and-features"></a>Belang rijke overwegingen en functies

* Wanneer een Azure AD-wachtwoord beleid voor wachtwoord beveiliging wordt gedownload, is dat beleid specifiek voor een Tenant. Met andere woorden, wachtwoord beleid is altijd een combi natie van de aangepaste lijst met verboden wacht woorden van micro soft en de lijst met geblokkeerde wacht woorden per Tenant.
* De DC-Agent communiceert met de proxy service via RPC via TCP. De proxy service luistert naar deze aanroepen op een dynamische of statische RPC-poort, afhankelijk van de configuratie.
* De DC-agent luistert nooit naar een poort die beschikbaar is via het netwerk.
* De proxy service roept de DC-Agent service nooit aan.
* De proxy service is stateless. Het beleid of een andere status die is gedownload van Azure, wordt nooit in de cache opgeslagen.
* De DC Agent-service maakt altijd gebruik van het meest recente, lokaal beschik bare wachtwoord beleid om het wacht woord van een gebruiker te evalueren. Als er geen wachtwoord beleid beschikbaar is op de lokale domein controller, wordt het wacht woord automatisch geaccepteerd. Als dat gebeurt, wordt een gebeurtenis bericht in het logboek geregistreerd om de beheerder te waarschuwen.
* Azure AD-wachtwoord beveiliging is geen real-time beleids toepassings engine. Er kan een vertraging optreden tussen het moment dat een wachtwoord beleid configuratie wijziging wordt aangebracht in azure AD en wanneer deze wijziging wordt doorgevoerd en op alle Dc's wordt afgedwongen.
* Azure AD-wachtwoord beveiliging fungeert als aanvulling op de bestaande beleids regels voor AD DS wachtwoord, niet als vervanging. Dit omvat alle andere dll-bestanden voor wachtwoord filter van derden die kunnen worden geïnstalleerd. AD DS is altijd vereist dat alle onderdelen van wachtwoord validatie overeenkomen voordat een wacht woord wordt geaccepteerd.

## <a name="forest--tenant-binding-for-azure-ad-password-protection"></a>Forest/Tenant-binding voor Azure AD-wachtwoord beveiliging

Voor de implementatie van Azure AD-wachtwoord beveiliging in een AD DS-forest is registratie van dat forest met Azure AD vereist. Elke proxy service die is geïmplementeerd, moet ook worden geregistreerd bij Azure AD. Deze forest-en proxy registraties zijn gekoppeld aan een specifieke Azure AD-Tenant, die impliciet wordt geïdentificeerd door de referenties die worden gebruikt tijdens de registratie.

Het AD DS-forest en alle geïmplementeerde proxy services binnen een forest moeten zijn geregistreerd bij dezelfde Tenant. Het is niet mogelijk om een AD DS-forest of proxy services in dat forest te registreren bij verschillende Azure AD-tenants. Symptomen van een dergelijke configuratie met een onjuiste configuratie zijn het niet mogelijk om wachtwoord beleid te downloaden.

> [!NOTE]
> Klanten die meerdere Azure AD-tenants hebben, moeten daarom één DN-Tenant kiezen voor het registreren van elk forest voor Azure AD-wachtwoord beveiliging.

## <a name="download"></a>Downloaden

De twee vereiste agent installatie Programma's voor Azure AD-wachtwoord beveiliging zijn beschikbaar via het [micro soft Download centrum](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="next-steps"></a>Volgende stappen

Voer de volgende instructies uit om aan de slag te gaan met het gebruik van on-premises Azure AD-wachtwoord beveiliging:

> [!div class="nextstepaction"]
> [On-premises Azure AD-wachtwoord beveiliging implementeren](howto-password-ban-bad-on-premises-deploy.md)
