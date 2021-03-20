---
title: Identiteits vereisten voor hybride Cloud identiteits ontwerp Azure | Microsoft Docs
description: Identificeer de bedrijfs behoeften van het bedrijf waarmee u de vereisten voor het ontwerp van de hybride identiteit kunt definiëren.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: de690978-84ef-41ad-9dfe-785722d343a1
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 074c203a0a5688855d4f7607a877b25ce6ee6ad7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89660559"
---
# <a name="determine-identity-requirements-for-your-hybrid-identity-solution"></a>Bepaal de identiteits vereisten voor uw hybride identiteits oplossing
De eerste stap bij het ontwerpen van een hybride identiteits oplossing is het bepalen van de vereisten voor de zakelijke organisatie die deze oplossing gebruiken.  Hybride identiteit wordt gestart als een ondersteunende rol (alle andere cloud oplossingen worden ondersteund door verificatie) en gaat in om nieuwe en interessante mogelijkheden te bieden voor het ontgrendelen van nieuwe werk belastingen voor gebruikers.  Deze werk belastingen of services die u wilt aannemen voor uw gebruikers, bepalen de vereisten voor het ontwerp van de hybride identiteit.  Deze services en workloads moeten zowel on-premises als in de Cloud gebruikmaken van hybride identiteiten.  

U moet de belang rijke aspecten van het bedrijf door lopen om te begrijpen wat het is wat er nu nodig is en wat het bedrijf voor de toekomst plant. Als u niet de zicht baarheid van de strategie voor de lange termijn voor het ontwerpen van hybride identiteiten hebt, is de kans groot dat uw oplossing niet schaalbaar is wanneer de bedrijfs behoeften groeien en veranderen. In het onderstaande diagram ziet u een voor beeld van een hybride identiteits architectuur en de workloads die worden ontgrendeld voor gebruikers. Dit is slechts een voor beeld van alle nieuwe mogelijkheden die kunnen worden ontgrendeld en geleverd met een solide hybride identiteits strategie. 

Sommige onderdelen die deel uitmaken van de hybride identiteits architectuur hybride identiteits architectuur ![](./media/plan-hybrid-identity-design-considerations/hybrid-identity-architechture.png)

## <a name="determine-business-needs"></a>Bedrijfs behoeften bepalen
Elk bedrijf heeft verschillende vereisten, zelfs als deze bedrijven deel uitmaken van dezelfde bedrijfstak, kunnen de echte zakelijke behoeften verschillen. U kunt nog steeds gebruikmaken van best practices uit de branche, maar het is uiteindelijk de bedrijfs behoeften van het bedrijf waarmee u de vereisten voor het ontwerp van de hybride identiteit definieert. 

Zorg ervoor dat u de volgende vragen beantwoordt om uw bedrijfs behoeften te identificeren:

* Is uw bedrijf bezig met het knippen van IT-bedrijfs kosten?
* Is uw bedrijf bezig met het beveiligen van Cloud assets (SaaS-apps, infra structuur)?
* Wilt u uw bedrijf moderniseren?
  * Zijn uw gebruikers mobiel en veeleisend hij het om uitzonde ringen in uw DMZ te maken zodat verschillende soorten verkeer toegang hebben tot verschillende bronnen?
  * Heeft uw bedrijf verouderde apps die moeten worden gepubliceerd naar deze moderne gebruikers, maar zijn ze niet eenvoudig te herschrijven?
  * Moet uw bedrijf al deze taken uitvoeren en het op hetzelfde moment onder controle brengen?
* Beveiligt uw bedrijf de identiteiten van gebruikers en vermindert het risico door nieuwe hulp middelen te gebruiken die gebruikmaken van de expertise van Azure-beveiligings expertise van micro soft on-premises?
* Probeert uw bedrijf de dreaded "externe" accounts on-premises te verwijderen en deze naar de cloud te verplaatsen, waar ze niet langer een bedreiging voor inactieve bedreigingen in uw on-premises omgeving zijn?

## <a name="analyze-on-premises-identity-infrastructure"></a>On-premises identiteits infrastructuur analyseren
Nu u een idee hebt over de bedrijfs vereisten van uw bedrijf, moet u uw on-premises identiteits infrastructuur evalueren. Deze evaluatie is belang rijk voor het definiëren van de technische vereisten voor het integreren van uw huidige identiteits oplossing in het Cloud Identity Management-systeem. Zorg ervoor dat u de volgende vragen beantwoordt:

* Welke verificatie-en autorisatie oplossing gebruikt uw bedrijf on-premises? 
* Heeft uw bedrijf momenteel een on-premises synchronisatie service?
* Maakt uw bedrijf gebruik van id-providers van derden (IdP)?

U moet ook rekening houden met de Cloud Services die uw bedrijf kan hebben. Het is zeer belang rijk een evaluatie uit te voeren om inzicht te krijgen in de huidige integratie met SaaS-, IaaS-of PaaS-modellen in uw omgeving. Zorg ervoor dat u de volgende vragen beantwoordt tijdens deze beoordeling:

* Heeft uw bedrijf een integratie met een Cloud serviceprovider?
* Zo ja, welke services worden er gebruikt?
* Is deze integratie momenteel in productie of is deze een pilot?

> [!NOTE]
> Cloud Discovery analyseert uw verkeers logboeken tegen Microsoft Cloud App Security van de Cloud-app-catalogus van meer dan 16.000 cloud-apps die zijn geclassificeerd en gescoord op basis van meer dan 70 risico factoren, om u te laten zien hoe u gebruik kunt maken van de Cloud, schaduw IT en de risico schaduw in uw organisatie. Zie [set up Cloud Discovery](/cloud-app-security/set-up-cloud-discovery)om aan de slag te gaan.
> 
> 

## <a name="evaluate-identity-integration-requirements"></a>Vereisten voor identiteits integratie evalueren
Vervolgens moet u de vereisten voor identiteits integratie evalueren. Deze evaluatie is belang rijk voor het definiëren van de technische vereisten voor de manier waarop gebruikers zich verifiëren, hoe de aanwezigheid van de organisatie in de Cloud zal zien, hoe de organisatie autorisatie toestaat en wat de gebruikers ervaring zal zijn. Zorg ervoor dat u de volgende vragen beantwoordt:

* Maakt uw organisatie gebruik van Federatie, standaard verificatie of beide?
* Is een vereiste voor Federatie?  Vanwege de volgende:
  * SSO op basis van Kerberos
  * Uw bedrijf heeft een on-premises toepassing (ingebouwde interne of externe partij) die gebruikmaakt van SAML-of vergelijk bare Federatie mogelijkheden.
  * MFA via Smart Cards. RSA SecurID, etc.
  * Regels voor client toegang die de onderstaande vragen beantwoorden:
    1. Kan ik alle externe toegang tot Microsoft 365 blok keren op basis van het IP-adres van de client?
    2. Kan ik alle externe toegang tot Microsoft 365 blok keren, met uitzonde ring van Exchange ActiveSync?
    3. Kan ik alle externe toegang tot Microsoft 365 blok keren, met uitzonde ring van op browser gebaseerde apps (OWA, SPO)
    4. Kan ik alle externe toegang tot Microsoft 365 voor leden van aangewezen AD-groepen blok keren
* Aandachtspunten voor beveiliging/controles
* Al bestaande investeringen in Federated Authentication
* Welke naam zal onze organisatie gebruiken voor ons domein in de Cloud?
* Heeft de organisatie een aangepast domein?
  1. Is dat domein openbaar en eenvoudig te controleren via DNS?
  2. Als dat niet het geval is, hebt u dan een openbaar domein dat kan worden gebruikt voor het registreren van een alternatieve UPN in AD?
* Zijn de gebruikers-id's consistent voor de Cloud weergave? 
* Heeft de organisatie apps waarvoor integratie met Cloud Services is vereist?
* Heeft de organisatie meerdere domeinen en worden deze allemaal standaard of Federated Authentication gebruikt?

## <a name="evaluate-applications-that-run-in-your-environment"></a>Toepassingen evalueren die in uw omgeving worden uitgevoerd
Nu u een idee hebt met betrekking tot uw on-premises en Cloud infrastructuur, moet u de toepassingen evalueren die in deze omgevingen worden uitgevoerd. Deze evaluatie is belang rijk voor het definiëren van de technische vereisten om deze toepassingen te integreren in het Cloud Identity Management-systeem. Zorg ervoor dat u de volgende vragen beantwoordt:

* Waar worden onze toepassingen Live?
* Hebben gebruikers toegang tot on-premises toepassingen?  In de Cloud? Of beide?
* Zijn er plannen om de bestaande workloads van de toepassing uit te voeren en deze naar de cloud te verplaatsen?
* Zijn er plannen voor het ontwikkelen van nieuwe toepassingen die on-premises of in de cloud worden geplaatst en die gebruikmaken van Cloud verificatie?

## <a name="evaluate-user-requirements"></a>Gebruikers vereisten evalueren
U moet ook de gebruikers vereisten evalueren. Deze evaluatie is belang rijk voor het definiëren van de stappen die nodig zijn voor de on-boarding en het ondersteunen van gebruikers wanneer deze naar de cloud worden overgezet. Zorg ervoor dat u de volgende vragen beantwoordt:

* Hebben gebruikers on-premises toegang tot toepassingen?
* Hebben gebruikers toegang tot toepassingen in de Cloud?
* Hoe melden gebruikers zich meestal aan bij hun on-premises omgeving?
* Hoe kunnen gebruikers zich aanmelden bij de Cloud?

> [!NOTE]
> Zorg ervoor dat u elk antwoord noteert de logica achter het antwoord begrijpt. [Bepaal de vereisten voor de reactie op incidenten](plan-hybrid-identity-design-considerations-incident-response-requirements.md) over de beschik bare opties en voor-en nadelen van elke optie.  Als u deze vragen hebt beantwoord, selecteert u welke optie het beste past bij uw bedrijfs behoeften.
> 
> 

## <a name="next-steps"></a>Volgende stappen
[Vereisten voor adreslijst synchronisatie bepalen](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)

## <a name="see-also"></a>Zie ook
[Overzicht van ontwerp overwegingen](plan-hybrid-identity-design-considerations-overview.md)

