---
title: Beheer concepten voor Azure AD Domain Services | Microsoft Docs
description: Meer informatie over het beheren van een Azure Active Directory Domain Services beheerd domein en het gedrag van gebruikers accounts en wacht woorden
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: justinha
ms.openlocfilehash: 76af477af8290634d2658c58574b8f96cff7b1fb
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620338"
---
# <a name="management-concepts-for-user-accounts-passwords-and-administration-in-azure-active-directory-domain-services"></a>Beheer concepten voor gebruikers accounts, wacht woorden en beheer in Azure Active Directory Domain Services

Wanneer u een door Azure Active Directory Domain Services (AD DS) beheerd domein maakt en uitvoert, zijn er enkele verschillen in werking ten opzichte van een traditionele on-premises AD DS omgeving. U gebruikt dezelfde beheer Programma's in azure AD DS als een zelf beheerd domein, maar u hebt geen rechtstreekse toegang tot de domein controllers (DC). Er zijn ook enkele verschillen in gedrag voor wachtwoord beleid en wacht woord-hashes, afhankelijk van de bron van het maken van het gebruikers account.

Dit conceptuele artikel bevat informatie over het beheren van een beheerd domein en het andere gedrag van gebruikers accounts, afhankelijk van de manier waarop ze zijn gemaakt.

## <a name="domain-management"></a>Domein beheer

Een beheerd domein is een DNS-naamruimte en bijbehorende directory. In een beheerd domein maken de domein controllers (Dc's) die alle resources zoals gebruikers en groepen, referenties en beleids regels bevatten onderdeel van de beheerde service. Voor redundantie worden twee Dc's gemaakt als onderdeel van een beheerd domein. U kunt zich niet aanmelden bij deze Dc's om beheer taken uit te voeren. In plaats daarvan maakt u een beheer-VM die is gekoppeld aan het beheerde domein en installeert u vervolgens uw normale AD DS-beheer hulpprogramma's. U kunt bijvoorbeeld de Active Directory-beheercentrum-of MMC-modules (micro soft Management Console), zoals DNS of groepsbeleid objecten, gebruiken.

## <a name="user-account-creation"></a>Gebruikers account maken

Gebruikers accounts kunnen op verschillende manieren in een beheerd domein worden gemaakt. De meeste gebruikers accounts worden gesynchroniseerd vanuit Azure AD, waarmee ook gebruikers accounts kunnen worden gesynchroniseerd vanuit een on-premises AD DS omgeving. U kunt ook hand matig rechtstreeks in het beheerde domein accounts maken. Sommige functies, zoals initiële wachtwoord synchronisatie of wachtwoord beleid, gedragen zich anders afhankelijk van hoe en waar gebruikers accounts worden gemaakt.

* Het gebruikers account kan worden gesynchroniseerd vanuit Azure AD. Dit omvat alleen Cloud gebruikers accounts die rechtstreeks zijn gemaakt in azure AD, en hybride gebruikers accounts die zijn gesynchroniseerd vanuit een on-premises AD DS omgeving met behulp van Azure AD Connect.
    * Het meren deel van gebruikers accounts in een beheerd domein wordt gemaakt via het synchronisatie proces van Azure AD.
* Het gebruikers account kan hand matig worden gemaakt in een beheerd domein en bestaat niet in azure AD.
    * Als u service accounts moet maken voor toepassingen die alleen in het beheerde domein worden uitgevoerd, kunt u ze hand matig maken in het beheerde domein. Omdat synchronisatie een van Azure AD is, worden gebruikers accounts die zijn gemaakt in het beheerde domein, niet gesynchroniseerd met Azure AD.

## <a name="password-policy"></a>Wachtwoord beleid

Azure AD DS bevat een standaard wachtwoord beleid dat instellingen definieert voor zaken als account vergrendeling, maximale wachtwoord duur en wachtwoord complexiteit. Instellingen als account vergrendelings beleid gelden voor alle gebruikers in een beheerd domein, ongeacht de manier waarop de gebruiker is gemaakt, zoals beschreven in de vorige sectie. Enkele instellingen, zoals minimale wachtwoord lengte en wachtwoord complexiteit, zijn alleen van toepassing op gebruikers die rechtstreeks zijn gemaakt in een beheerd domein.

U kunt uw eigen aangepaste wachtwoord beleidsregels maken om het standaard beleid in een beheerd domein te onderdrukken. Deze aangepaste beleids regels kunnen vervolgens worden toegepast op specifieke groepen gebruikers als dat nodig is.

Zie [wacht woord-en account vergrendelings beleid in beheerde domeinen][password-policy]voor meer informatie over de verschillen in de manier waarop wachtwoord beleid wordt toegepast, afhankelijk van de bron van het maken van een gebruiker.

## <a name="password-hashes"></a>Wacht woord-hashes

Voor de verificatie van gebruikers in het beheerde domein heeft Azure AD DS wachtwoordhashes nodig in een indeling die geschikt is voor NTLM- (NT LAN Manager) en Kerberos-verificatie. Totdat u Azure AD DS voor uw tenant inschakelt, maakt of bewaart Azure AD geen wachtwoordhashes in de vereiste indeling voor NTLM- of Kerberos-verificatie. Om veiligheidsredenen slaat Azure AD ook geen wachtwoorden op in niet-gecodeerde vorm. Azure AD kan deze wachtwoordhashes voor NTLM of Kerberos niet automatisch genereren op basis van bestaande referenties van gebruikers.

Voor Cloud gebruikers accounts moeten gebruikers hun wacht woord wijzigen voordat ze het beheerde domein kunnen gebruiken. Door deze wachtwoordwijziging worden de wachtwoordhashes voor Kerberos- en NTLM-verificatie gegenereerd en opgeslagen in Azure AD. Het account wordt niet gesynchroniseerd vanuit Azure AD naar Azure AD DS totdat het wachtwoord wordt gewijzigd.

[Schakel synchronisatie van wacht woord-hashes][hybrid-phs]in voor gebruikers die zijn gesynchroniseerd vanuit een on-premises AD DS omgeving met Azure AD Connect.

> [!IMPORTANT]
> Azure AD Connect synchroniseert alleen verouderde wachtwoord-hashes wanneer u Azure AD DS voor uw Azure AD-Tenant inschakelt. Verouderde wachtwoord hashes worden niet gebruikt als u alleen Azure AD Connect gebruikt om een on-premises AD DS omgeving te synchroniseren met Azure AD.
>
> Als uw verouderde toepassingen geen gebruikmaken van NTLM-verificatie of LDAP-eenvoudige bindingen, raden we u aan NTLM-wachtwoord hash-synchronisatie voor Azure AD DS uit te scha kelen. Zie voor meer informatie [zwak coderings suites en hash-synchronisatie van NTLM-referenties uitschakelen][secure-domain].

Zodra de configuratie is geslaagd, worden de bruikbare wachtwoordhashes opgeslagen in het beheerde domein. Als u het beheerde domein verwijdert, worden alle wachtwoordhashes die op dat punt zijn opgeslagen ook verwijderd. Gesynchroniseerde referentie gegevens in azure AD kunnen niet opnieuw worden gebruikt als u later een ander beheerd domein maakt, moet u de wachtwoord-hash-synchronisatie opnieuw configureren om de wacht woord-hashes opnieuw op te slaan. Eerder aan het domein toegevoegde virtuele machines of gebruikers kunnen niet direct een verificatie uitvoeren; Azure AD moet eerst de wachtwoordhashes in het nieuwe beheerd domein genereren en opslaan. Zie [Synchronisatieproces voor wachtwoordhashes voor Azure AD DS en Azure AD Connect][azure-ad-password-sync] voor meer informatie.

> [!IMPORTANT]
> Azure AD Connect moet alleen worden geïnstalleerd en geconfigureerd voor synchronisatie met on-premises AD DS-omgevingen. Het installeren van Azure AD Connect in een beheerd domein om objecten weer naar Azure AD te synchroniseren, wordt niet ondersteund.

## <a name="forests-and-trusts"></a>Forests en vertrouwensrelaties

Een *forest* is een logische constructie die door Active Directory Domain Services (AD DS) wordt gebruikt om een of meer *domeinen* te groeperen. De domeinen slaan objecten vervolgens op voor gebruikers of groepen en bieden verificatie services.

In azure AD DS bevat het forest slechts één domein. On-premises AD DS bossen bevatten vaak veel domeinen. In grote organisaties, met name na fusies en acquisities, kunt u meerdere on-premises forests gebruiken die elk meerdere domeinen bevatten.

Standaard wordt een beheerd domein gemaakt als een *gebruikers* forest. Met dit type forest worden alle objecten van Azure AD gesynchroniseerd, waaronder alle gebruikersaccounts die zijn gemaakt in een on-premises AD DS-omgeving. Gebruikers accounts kunnen rechtstreeks worden geverifieerd aan de hand van het beheerde domein, bijvoorbeeld om zich aan te melden bij een virtuele machine die lid is van een domein. Een gebruikers forest werkt wanneer de wacht woord-hashes kunnen worden gesynchroniseerd en gebruikers gebruiken geen exclusieve aanmeldings methoden zoals smartcard verificatie.

In een Azure AD DS- *bron* -forest verifiëren *gebruikers via een eenrichtings forestvertrouwensrelatie van hun* on-premises AD DS. Met deze benadering worden de gebruikers objecten en wacht woord-hashes niet gesynchroniseerd met Azure AD DS. De gebruikers objecten en referenties bestaan alleen in het on-premises AD DS. Met deze aanpak kunnen bedrijven hosten van resources en toepassings platforms in azure die afhankelijk zijn van klassieke verificatie, zoals LDAPS, Kerberos of NTLM, maar eventuele verificatie problemen of problemen worden verwijderd.

Zie [Wat zijn resource][concepts-forest] -forests? en [Hoe worden forest-vertrouwens relaties in azure AD DS?][concepts-trust] voor meer informatie over forest-typen in azure AD DS?

## <a name="azure-ad-ds-skus"></a>Azure AD DS Sku's

In azure AD DS zijn de beschik bare prestaties en functies gebaseerd op de SKU. U selecteert een SKU wanneer u het beheerde domein maakt en u kunt switches als uw bedrijfs vereisten veranderen nadat het beheerde domein is geïmplementeerd. De volgende tabel geeft een overzicht van de beschik bare Sku's en de verschillen ertussen:

| SKU-naam   | Maximum aantal objecten | Back-upfrequentie | Maximum aantal uitgaande forest-vertrouwens relaties |
|------------|----------------------|------------------|----|
| Standard   | Onbeperkt            | Elke 7 dagen     | 0  |
| Enterprise | Onbeperkt            | Elke 3 dagen     | 5  |
| Premium    | Onbeperkt            | Dagelijks            | 10 |

Vóór deze Azure AD DS Sku's werd een facturerings model gebruikt dat is gebaseerd op het aantal objecten (gebruikers-en computer accounts) in het beheerde domein. Er zijn geen variabele prijzen meer op basis van het aantal objecten in het beheerde domein.

Zie de pagina met prijzen voor [Azure AD DS][pricing]voor meer informatie.

### <a name="managed-domain-performance"></a>Prestaties van beheerd domein

De prestaties van het domein variëren op basis van de manier waarop verificatie voor een toepassing wordt geïmplementeerd. Aanvullende reken bronnen kunnen helpen de reactie tijd van query's te verbeteren en de tijd te beperken die wordt besteed aan synchronisatie bewerkingen. Naarmate het SKU-niveau toeneemt, worden de reken bronnen die beschikbaar zijn voor het beheerde domein verhoogd. Bewaak de prestaties van uw toepassingen en plan de vereiste resources.

Als uw bedrijfs-of toepassings vereisten veranderen en u extra reken kracht voor uw beheerde domein nodig hebt, kunt u overschakelen naar een andere SKU.

### <a name="backup-frequency"></a>Back-upfrequentie

De back-upfrequentie bepaalt hoe vaak een moment opname van het beheerde domein wordt gemaakt. Back-ups zijn een geautomatiseerd proces dat wordt beheerd door het Azure-platform. In het geval van een probleem met uw beheerde domein kan ondersteuning voor Azure u helpen bij het terugzetten van een back-up. Omdat synchronisatie slechts één manier plaatsvindt *vanuit* Azure AD, zijn de problemen in een beheerd domein niet van invloed op Azure AD of on-premises AD DS omgevingen en-functionaliteit.

Naarmate het SKU-niveau toeneemt, neemt de frequentie van de back-upmomentopnamen toe. Controleer uw bedrijfs vereisten en Recovery Point Objective (RPO) om de vereiste back-upfrequentie voor uw beheerde domein te bepalen. Als uw zakelijke of toepassings vereisten veranderen en u meer frequente back-ups nodig hebt, kunt u overschakelen naar een andere SKU.

### <a name="outbound-forest-trusts"></a>Uitgaande forest-vertrouwens relaties

In de vorige sectie worden uitgebreide uitgaande forest-vertrouwens relaties van een beheerd domein naar een on-premises AD DS omgeving beschreven. De SKU bepaalt het maximum aantal forest-vertrouwens relaties dat u kunt maken voor een beheerd domein. Bekijk de vereisten van uw bedrijf en toepassing om te bepalen hoeveel vertrouwens relaties u werkelijk nodig hebt en kies de juiste Azure AD DS-SKU. Als uw bedrijfs vereisten veranderen en u extra forest-vertrouwens relaties wilt maken, kunt u overschakelen naar een andere SKU.

## <a name="next-steps"></a>Volgende stappen

Als u aan de slag wilt gaan, [maakt u een door Azure AD DS beheerd domein][create-instance].

<!-- INTERNAL LINKS -->
[password-policy]: password-policy.md
[hybrid-phs]: tutorial-configure-password-hash-sync.md#enable-synchronization-of-password-hashes
[secure-domain]: secure-your-domain.md
[azure-ad-password-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[create-instance]: tutorial-create-instance.md
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
