---
title: Flexibele hybride authenticatie in Azure Active Directory maken
description: Een hand leiding voor architecten en IT-beheerders bij het bouwen van een robuuste hybride infra structuur.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c45b362bc37df71346fc3b635c8ae4a51f62cdc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919585"
---
# <a name="build-resilience-in-your-hybrid-architecture"></a>Maak flexibiliteit in uw hybride architectuur

Met hybride verificatie kunnen gebruikers toegang krijgen tot Cloud bronnen met hun identiteiten die on-premises zijn opgeslagen. Een hybride infra structuur omvat zowel Cloud-als on-premises onderdelen.

* Cloud onderdelen zijn onder andere Azure AD, Azure-resources en-services, de Cloud-apps van uw organisatie en SaaS-toepassingen.

* On-premises onderdelen zijn on-premises toepassingen, resources zoals SQL-data bases en een id-provider zoals Windows Server Active Directory. 

> [!IMPORTANT]
> Bij het plannen van flexibiliteit in uw hybride infra structuur is het een belang rijke oplossing om afhankelijkheden en storingen met één punt te minimaliseren. 

Micro soft biedt drie mechanismen voor hybride verificatie. De opties worden weer gegeven in de volg orde van tolerantie. U wordt aangeraden wachtwoord hash-synchronisatie te implementeren, indien mogelijk.

* Bij het synchroniseren van [wacht woord-hashes](../hybrid/whatis-phs.md) (PHS) wordt gebruikgemaakt van Azure AD Connect om de identiteit en een hash van de hash van het wacht woord te synchroniseren met Azure AD, zodat gebruikers zich kunnen aanmelden bij cloud resources met hun wacht woord op locatie. PHS heeft alleen on-premises afhankelijkheden voor synchronisatie, niet voor verificatie.

* [Pass-Through-verificatie](../hybrid/how-to-connect-pta.md) (PTA) omleidt gebruikers naar Azure AD om zich aan te melden. De gebruikers naam en het wacht woord worden vervolgens gevalideerd op basis van Active Directory on-premises, via een agent die in het bedrijfs netwerk is geïmplementeerd. PTA heeft een on-premises footprint van de Azure AD PTA-agents die on-premises zich op servers bevinden.

* [Federation](../hybrid/whatis-fed.md) -klanten implementeren een Federation-service, zoals AD FS, en vervolgens valideert Azure AD de door de Federation service GEPRODUCEERDe SAML-bevestigingen. Federatie heeft de hoogste afhankelijkheid op de on-premises infra structuur en daarom meer fout punten. 

   
Het is mogelijk dat u een of meer van deze methoden in uw organisatie gebruikt. Zie [de juiste verificatie methode kiezen voor uw Azure AD hybride identiteits oplossing](../hybrid/choose-ad-authn.md)voor meer informatie. Dit artikel bevat een beslissings structuur die u kan helpen bij het bepalen van uw methodologie.

## <a name="password-hash-synchronization"></a>Wachtwoord-hashsynchronisatie

De eenvoudigste en meest flexibele hybride verificatie optie voor Azure AD is een [wachtwoord hash-synchronisatie](../hybrid/whatis-phs.md) zonder een on-premises identiteits infrastructuur afhankelijkheid bij het verwerken van verificatie aanvragen. Wanneer de identiteiten met wacht woord-hashes zijn gesynchroniseerd met Azure AD, kunnen gebruikers zich verifiëren bij cloud resources zonder afhankelijkheid van de on-premises identiteits onderdelen. 

![Architectuur diagram van PHS](./media/resilience-in-hybrid/admin-resilience-password-hash-sync.png)

Als u deze verificatie optie kiest, ondervindt u geen onderbrekingen wanneer on-premises identiteits onderdelen niet meer beschikbaar zijn. On-premises onderbreking kan om verschillende redenen optreden, zoals hardwarestoringen, stroom storingen, natuur rampen en aanvallen op malware. 

### <a name="how-do-i-implement-phs"></a>Hoe kan ik PHS implementeren?

Voor het implementeren van PHS raadpleegt u de volgende bronnen:

* [Wachtwoord hash synchronisatie met Azure AD Connect implementeren](../hybrid/how-to-connect-password-hash-synchronization.md)

* [Wachtwoord-hash-synchronisatie inschakelen](../hybrid/how-to-connect-password-hash-synchronization.md)

Als uw vereisten zodanig zijn dat u PHS niet kunt gebruiken, moet u Pass-Through-verificatie gebruiken.

## <a name="pass-through-authentication"></a>Pass-through-verificatie

Pass-Through-verificatie is afhankelijk van verificatie agenten die zich on-premises op servers bevinden. Er is een permanente verbinding of service bus aanwezig tussen Azure AD en de on-premises PTA-agents. De firewall, servers die als host fungeren voor de verificatie agenten en de on-premises Windows Server-Active Directory (of een andere ID-provider) zijn alle mogelijke fout punten. 

![Architectuur diagram van PTA](./media/resilience-in-hybrid/admin-resilience-pass-through-authentication.png)

### <a name="how-do-i-implement-pta"></a>Hoe kan ik PTA implementeren?

Zie de volgende bronnen voor het implementeren van Pass-Through-verificatie.

* [Werking van Pass-Through-verificatie](../hybrid/how-to-connect-pta-how-it-works.md)

* [Gedetailleerde informatie over beveiliging met Pass Through-verificatie](../hybrid/how-to-connect-pta-security-deep-dive.md)

* [Azure AD Pass-Through-verificatie installeren](../hybrid/how-to-connect-pta-quick-start.md)

* Als u PTA gebruikt, definieert u een [Maxi maal beschik bare topologie](../hybrid/how-to-connect-pta-quick-start.md).

 ## <a name="federation"></a>Federatie

Federatie omvat het maken van een vertrouwens relatie tussen Azure AD en de Federation-service, waaronder de uitwisseling van eind punten, certificaten voor token-ondertekening en andere meta gegevens. Wanneer een aanvraag bij Azure AD komt, wordt de configuratie gelezen en wordt de gebruiker omgeleid naar de geconfigureerde eind punten. Op dat moment communiceert de gebruiker met de Federation service, die een SAML-verklaring uitgeeft die door Azure AD is gevalideerd. 

In het volgende diagram ziet u een topologie van een Enter prise Active Directory Federation Services (AD FS), implementatie met redundante Federatie-en Web Application proxy-servers voor meerdere on-premises data centers. Deze configuratie is afhankelijk van de infra structuur van bedrijfs netwerken, zoals DNS, netwerk taakverdeling met geo-affiniteits mogelijkheden, firewalls enzovoort. Alle on-premises onderdelen en verbindingen zijn gevoelig voor fouten. Ga naar de [documentatie over capaciteits planning voor AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/design/planning-for-ad-fs-server-capacity) voor meer informatie.

> [!NOTE]
>  Federatie heeft het hoogste aantal on-premises afhankelijkheden en daarom de meest potentiële fout punten. Hoewel in dit diagram AD FS worden weer gegeven, zijn andere on-premises id-providers onderhevig aan vergelijk bare ontwerp overwegingen voor hoge Beschik baarheid, schaal baarheid en failover.

![Architectuur diagram van Federatie](./media/resilience-in-hybrid/admin-resilience-federation.png)

 ### <a name="how-do-i-implement-federation"></a>Federatie Hoe kan ik implementeren?

Raadpleeg de volgende bronnen als u een Federated Authentication strategie implementeert of als u deze beter wilt maken.

* [Wat is Federated Authentication](../hybrid/whatis-fed.md)

* [Hoe federatie werkt](../hybrid/how-to-connect-fed-whatis.md)

* [Azure AD-federation compatibiliteitslijst](../hybrid/how-to-connect-fed-compatibility.md)

* Volg de [documentatie over de AD FS capaciteits planning](https://docs.microsoft.com/windows-server/identity/ad-fs/design/planning-for-ad-fs-server-capacity)

* [AD FS implementeren in azure IaaS](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs)

* [PHS](../hybrid/tutorial-phs-backup.md) samen met uw Federatie inschakelen

## <a name="next-steps"></a>Volgende stappen
Flexibiliteits bronnen voor beheerders en architecten
 
* [Tolerantie bouwen met referentie beheer](resilience-in-credentials.md)

* [Een tolerantie bouwen met de status van het apparaat](resilience-with-device-states.md)

* [Een tolerantie bouwen met behulp van voortdurende toegang (CAE)](resilience-with-continuous-access-evaluation.md)

* [Flexibiliteit in externe gebruikers verificatie maken](resilience-b2b-authentication.md)

* [Tolerantie in toepassings toegang bouwen met toepassings proxy](resilience-on-premises-access.md)

Flexibiliteits bronnen voor ontwikkel aars

* [IAM-tolerantie bouwen in uw toepassingen](resilience-app-development-overview.md)

* [Maak flexibiliteit in uw CIAM-systemen](resilience-b2c.md)
