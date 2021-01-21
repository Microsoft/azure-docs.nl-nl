---
title: Overzicht van Azure Active Directory Domain Services | Microsoft Docs
description: In dit overzicht leert u wat Azure Active Directory Domain Services biedt en hoe u dit in uw organisatie kunt gebruiken om identiteitsservices te bieden voor toepassingen en services in de cloud.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 01/20/2021
ms.author: justinha
ms.custom: contperf-fy21q1
ms.openlocfilehash: 194a5420f38e99d45f74241f67ae8cfbbaaee8f4
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98660993"
---
# <a name="what-is-azure-active-directory-domain-services"></a>Wat is Azure Active Directory Domain Services?

Azure Active Directory Domain Services (AD DS) biedt beheerde domein Services, zoals domein deelname, groeps beleid, LDAP (Lightweight Directory Access Protocol) en Kerberos/NTLM-verificatie. U gebruikt deze domeinservices zonder domeincontrollers (DC’s) in de cloud te hoeven implementeren, beheren en patchen.

Met een door Azure AD DS beheerd domein kunt u in de cloud oudere toepassingen uitvoeren die geen gebruik kunnen maken van moderne verificatiemethoden, of waarbij u niet wilt dat zoekopdrachten in de adreslijst altijd teruggaan naar een on-premises AD DS-omgeving. U kunt deze verouderde toepassingen uit uw on-premises omgeving verplaatsen naar een beheerd domein, zonder dat u de AD DS-omgeving in de cloud hoeft te beheren.

Azure AD DS kan met uw bestaande Azure AD-tenant worden geïntegreerd. Met deze integratie kunnen gebruikers zich aanmelden bij Services en toepassingen die zijn verbonden met het beheerde domein met hun bestaande referenties. U kunt ook bestaande groepen en gebruikersaccounts gebruiken om toegang tot resources te beveiligen. Deze functies bieden een soepeler lift-and-shift van on-premises resources naar Azure.

> [!div class="nextstepaction"]
> [Maak eerst een beheerd Azure AD DS-domein met behulp van Azure Portal][tutorial-create]

Bekijk onze korte video voor meer informatie over Azure AD DS.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LblD]

## <a name="how-does-azure-ad-ds-work"></a>Hoe werkt Azure AD DS?

Wanneer u een beheerd Azure AD DS-domein maakt, definieert u een unieke naamruimte. Deze naamruimte is de domeinnaam, bijvoorbeeld *aaddscontoso.com*. Er worden vervolgens twee Windows Server-domeincontrollers (DC's) geïmplementeerd in uw geselecteerde Azure-regio. Deze implementatie van DC's wordt een replicaset genoemd.

U hoeft deze DC’s niet te beheren, te configureren of bij te werken. Op het Azure-platform worden de DC's verwerkt als onderdeel van het beheerde domein, inclusief back-ups en versleuteling van data-at-rest, met behulp van Azure Disk Encryption.

Een beheerd domein wordt geconfigureerd voor het uitvoeren van synchronisatie van Azure AD in één richting om toegang te bieden tot een centrale set gebruikers, groepen en referenties. U kunt resources rechtstreeks in het beheerde domein maken, maar ze worden niet terug gesynchroniseerd naar Azure AD. Toepassingen, services en virtuele machines in azure die verbinding maken met het beheerde domein kunnen vervolgens gebruikmaken van algemene AD DS functies, zoals domein deelname, groeps beleid, LDAP en Kerberos/NTLM-verificatie.

In een hybride omgeving met een on-premises AD DS-omgeving synchroniseert [Azure AD Connect][azure-ad-connect] identiteitsgegevens met Azure AD, die vervolgens worden gesynchroniseerd naar het beheerde domein.

![Synchronisatie in Azure AD-domeinservices met Azure AD en on-premises AD DS met behulp van AD Connect](./media/active-directory-domain-services-design-guide/sync-topology.png)

In Azure AD DS worden identiteitsgegevens van Azure AD gerepliceerd, zodat het werkt met Azure AD-tenants die alleen in de cloud bestaan of die zijn gesynchroniseerd met een on-premises AD DS-omgeving. Dezelfde set Azure AD DS-functies bestaat voor beide omgevingen.

* Als u over een bestaande on-premises AD DS-omgeving beschikt, kunt u gebruikersaccountgegevens synchroniseren om gebruikers een consistente identiteit te bieden. Zie [Synchronisatie van objecten en referenties in een beheerd domein][synchronization] voor meer informatie.
* Voor omgevingen die alleen in de cloud bestaan, hebt u geen traditionele on-premises AD DS-omgeving nodig om de gecentraliseerde identiteitsservices van Azure AD DS te gebruiken.

U kunt een beheerd domein uitbreiden als u meer dan één replicaset per Azure AD-tenant wilt hebben. Replicasets kunnen worden toegevoegd aan elk gekoppeld virtueel netwerk in een Azure-regio die Azure AD DS ondersteunt. Extra replica sets in verschillende Azure-regio's bieden geografisch herstel na noodgeval voor oudere toepassingen als een Azure-regio offline gaat. Replicasets zijn momenteel beschikbaar in preview. Zie [Concepten en functies van replicasets voor beheerde domeinen][concepts-replica-sets].

In de volgende video ziet u een overzicht van de manier waarop Azure AD DS kan worden geïntegreerd met uw toepassingen en workloads om identiteitsservices in de cloud te bieden:

<br />

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

U kunt de volgende voorbeelden verkennen om implementatiescenario’s voor Azure AD DS in actie te zien:

* [Azure AD DS voor hybride organisaties](scenarios.md#azure-ad-ds-for-hybrid-organizations)
* [Azure AD DS voor organisaties die alleen de cloud gebruiken](scenarios.md#azure-ad-ds-for-cloud-only-organizations)

## <a name="azure-ad-ds-features-and-benefits"></a>Azure AD DS-functies en -voordelen

Om identiteitsservices te bieden voor toepassingen en virtuele machines in de cloud is Azure AD DS volledig compatibel met een traditionele AD DS-omgeving voor bewerkingen zoals domeindeelname, beveiligde LDAP (LDAPS), groepsbeleid, DNS-beheer en ondersteuning voor LDAP-bindingen en -leesbewerkingen. LDAP-schrijfondersteuning is beschikbaar voor objecten die in het beheerde domein zijn gemaakt, maar niet voor resources die zijn gesynchroniseerd vanuit Azure AD.

[Vergelijk Azure AD DS met Azure AD, AD DS op virtuele Azure-machines en on-premises AD DS][compare] voor meer informatie over uw identiteitsopties.

De volgende functies van Azure AD DS zorgen voor eenvoudigere implementatie- en beheerbewerkingen:

* **Vereenvoudigde implementatie-ervaring:** Azure AD DS wordt ingeschakeld voor uw Azure AD-tenant met behulp van één wizard in Azure Portal.
* **Geïntegreerd met Azure AD:** Gebruikersaccounts, groepslidmaatschappen en referenties worden automatisch beschikbaar via uw Azure AD-tenant. Nieuwe gebruikers en groepen, of wijzigingen in kenmerken van uw Azure AD-tenant of uw on-premises AD DS-omgeving, worden automatisch gesynchroniseerd naar Azure AD DS.
    * Accounts in externe mappen die aan uw Azure AD zijn gekoppeld, zijn niet beschikbaar in Azure AD DS. Referenties zijn niet beschikbaar voor die externe mappen, dus deze kunnen niet worden gesynchroniseerd naar een beheerd domein.
* **Gebruik uw zakelijke referenties/wachtwoorden:** Wachtwoorden voor gebruikers in Azure AD DS zijn dezelfde als in uw Azure AD-tenant. Gebruikers kunnen hun zakelijke referenties gebruiken om domeindeelname op apparaten te gebruiken, zich interactief of via een extern bureaublad aan te melden en zich te verifiëren bij het beheerde domein.
* **NTLM- en Kerberos-verificatie:** Met ondersteuning voor NTLM- en Kerberos-verificatie kunt u toepassingen implementeren die afhankelijk zijn van met Windows geïntegreerde verificatie.
* **Hoge beschikbaarheid:** Azure AD DS bevat meerdere domeincontrollers, die hoge beschikbaarheid voor uw beheerde domein bieden. Door deze hoge beschikbaarheid is uptime van de service en weerstand tegen fouten gegarandeerd.
    * In regio's die ondersteuning bieden voor [Azure-beschikbaarheidszones][availability-zones] worden deze domeincontrollers ook gedistribueerd over meerdere zones voor extra flexibiliteit.
    * Er kunnen ook [replicasets][concepts-replica-sets] worden gebruikt om geografisch herstel na noodgevallen te bieden voor oudere toepassingen als een Azure-regio offline gaat.

Belangrijke aspecten van een beheerd domein zijn onder andere:

* Het beheerde domein is een zelfstandig domein. Het domein is geen extensie van een on-premises domein.
    * Waar nodig kunt u uitgaande forestvertrouwensrelaties in één richting maken van Azure AD DS naar een on-premises AD DS-omgeving. Zie [Resourceforestconcepten en -functies voor Azure AD DS][ forest-trusts] voor meer informatie.
* Uw IT-team hoeft geen domeincontrollers voor dit beheerde domein te beheren, patchen of bewaken.

Voor hybride omgevingen waarin AD DS on-premises wordt uitgevoerd, hoeft u geen AD-replicatie naar het beheerde domein te beheren. Gebruikersaccounts, groepslidmaatschappen en referenties van uw on-premises map worden gesynchroniseerd naar Azure AD via [Azure AD Connect][azure-ad-connect]. Deze gebruikersaccounts, groepslidmaatschappen en referenties worden automatisch beschikbaar in het beheerde domein.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over het verschil tussen Azure AD DS en andere identiteitsoplossingen en de werking van synchronisatie:

* [Azure AD DS vergelijken met Azure AD, Active Directory Domain Services op virtuele Azure-machines en on-premises Active Directory Domain Services][compare]
* [Meer informatie over de manier waarop Azure AD-domeinservices met uw Azure AD-map worden gesynchroniseerd][synchronization]
* Zie [Beheerconcepten voor gebruikersaccounts, wachtwoorden en beheer in Azure AD DS][administration-concepts] voor meer informatie over het beheer van een beheerd domein.

[Maak eerst een beheerd domein met behulp van de Azure-portal][tutorial-create].

<!-- INTERNAL LINKS -->
[compare]: compare-identity-solutions.md
[synchronization]: synchronization.md
[tutorial-create]: tutorial-create-instance.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-azure-ad-connect.md
[password-hash-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md
[availability-zones]: ../availability-zones/az-overview.md
[forest-trusts]: concepts-resource-forest.md
[administration-concepts]: administration-concepts.md
[synchronization]: synchronization.md
[concepts-replica-sets]: concepts-replica-sets.md
