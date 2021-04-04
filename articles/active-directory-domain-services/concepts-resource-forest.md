---
title: Concepten van resource-forests voor Azure AD Domain Services | Microsoft Docs
description: Meer informatie over wat een resource-forest is in Azure Active Directory Domain Services en hoe ze uw organisatie in hybride omgevingen voor delen met beperkte gebruikers verificatie opties of beveiligings problemen.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 79cf408bcb9060c247b97e6a81204c5a5517d384
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619998"
---
# <a name="resource-forest-concepts-and-features-for-azure-active-directory-domain-services"></a>Concepten en functies van het resource-forest voor Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure AD DS) biedt een aanmeldings ervaring voor oudere, on-premises en line-of-business-toepassingen. Gebruikers, groepen en wacht woord-hashes van on-premises en Cloud gebruikers worden gesynchroniseerd met het door Azure AD DS beheerde domein. Deze gesynchroniseerde wacht woord-hashes zijn wat gebruikers één set referenties kunnen gebruiken voor de on-premises AD DS, Microsoft 365 en Azure Active Directory.

Hoewel beveiligde en extra beveiligings voordelen bieden, kunnen sommige organisaties die gebruikers wachtwoorden hashes niet synchroniseren met Azure AD of Azure AD DS. Gebruikers in een organisatie hebben het wacht woord mogelijk niet kennen, omdat ze alleen smartcard verificatie gebruiken. Deze beperkingen verhinderen dat sommige organisaties Azure AD DS gebruiken om on-premises klassieke toepassingen naar Azure te tilleren en te verplaatsen.

U kunt deze behoeften en beperkingen oplossen door een beheerd domein te maken dat gebruikmaakt van een resource-forest. In dit conceptuele artikel wordt uitgelegd wat forests zijn en hoe ze andere bronnen vertrouwen om een veilige verificatie methode te bieden.

## <a name="what-are-forests"></a>Wat zijn forests?

Een *forest* is een logische constructie die door Active Directory Domain Services (AD DS) wordt gebruikt om een of meer *domeinen* te groeperen. De domeinen slaan objecten vervolgens op voor gebruikers of groepen en bieden verificatie services.

In een door Azure AD DS beheerd domein bevat het forest slechts één domein. On-premises AD DS bossen bevatten vaak veel domeinen. In grote organisaties, met name na fusies en acquisities, kunt u meerdere on-premises forests gebruiken die elk meerdere domeinen bevatten.

Standaard wordt een beheerd domein gemaakt als een *gebruikers* forest. Met dit type forest worden alle objecten van Azure AD gesynchroniseerd, waaronder alle gebruikersaccounts die zijn gemaakt in een on-premises AD DS-omgeving. Gebruikers accounts kunnen rechtstreeks worden geverifieerd aan de hand van het beheerde domein, bijvoorbeeld om zich aan te melden bij een virtuele machine die lid is van een domein. Een gebruikers forest werkt wanneer de wacht woord-hashes kunnen worden gesynchroniseerd en gebruikers gebruiken geen exclusieve aanmeldings methoden zoals smartcard verificatie.

In een beheerd domein *resource* -forest verifiëren *gebruikers via een eenrichtings forestvertrouwensrelatie van hun* on-premises AD DS. Met deze benadering worden de gebruikers objecten en wacht woord-hashes niet gesynchroniseerd met het beheerde domein. De gebruikers objecten en referenties bestaan alleen in het on-premises AD DS. Met deze aanpak kunnen bedrijven hosten van resources en toepassings platforms in azure die afhankelijk zijn van klassieke verificatie, zoals LDAPS, Kerberos of NTLM, maar eventuele verificatie problemen of problemen worden verwijderd.

Resource forests bieden ook de mogelijkheid om uw toepassingen één onderdeel tegelijk uit te voeren. Veel oudere on-premises toepassingen zijn meerdere lagen, vaak met een webserver of front-end en veel data base-gerelateerde onderdelen. Deze lagen maken het moeilijk om in één stap de volledige toepassing naar de cloud te tillen en te verplaatsen. Met resource-forests kunt u uw toepassing in gefaseerde benadering optillen, waardoor het eenvoudiger wordt om uw toepassing naar Azure te verplaatsen.

## <a name="what-are-trusts"></a>Wat zijn vertrouwens relaties?

Organisaties met meer dan één domein hebben vaak gebruikers nodig om toegang te krijgen tot gedeelde bronnen in een ander domein. Voor toegang tot deze gedeelde bronnen moeten gebruikers in het ene domein worden geverifieerd bij een ander domein. Als u deze verificatie-en autorisatie mogelijkheden wilt bieden tussen clients en servers in verschillende domeinen, moet er een *vertrouwens relatie* tussen de twee domeinen zijn.

Met vertrouwens relaties tussen domeinen, vertrouwen de verificatie mechanismen voor elk domein de verificaties die afkomstig zijn uit het andere domein. Vertrouwens relaties helpen beheerde toegang tot gedeelde bronnen in een bron domein (het *vertrouwende* domein) te bieden door te controleren of binnenkomende verificatie aanvragen afkomstig zijn van een vertrouwde instantie (het *vertrouwde* domein). Vertrouwens relaties fungeren als bruggen waarmee alleen gevalideerde verificatie aanvragen kunnen worden getransporteerd tussen domeinen.

Hoe een vertrouwens relatie verificatie aanvragen doorgeeft, is afhankelijk van de configuratie. Vertrouwens relaties kunnen op een van de volgende manieren worden geconfigureerd:

* **Eén richting** : biedt toegang vanuit het vertrouwde domein aan bronnen in het vertrouwende domein.
* **Twee richtingen** : Hiermee geeft u toegang tot resources in het andere domein.

Vertrouwens relaties worden ook zo geconfigureerd dat er op een van de volgende manieren aanvullende vertrouwens relatie wordt verwerkt:

* Niet- **transitief** : de vertrouwens relatie bestaat alleen tussen de twee vertrouwens partner domeinen.
* **Transitieve** vertrouwens relaties worden automatisch uitgebreid naar andere domeinen die een van de partners vertrouwt.

In sommige gevallen worden vertrouwens relaties automatisch ingesteld wanneer er domeinen worden gemaakt. Andere keren, moet u een vertrouwens type kiezen en expliciet de juiste relaties instellen. De specifieke typen vertrouwens relaties die worden gebruikt en de structuur van deze vertrouwens relatie, zijn afhankelijk van de manier waarop de AD DS Directory is ingedeeld en of er verschillende versies van Windows naast elkaar in het netwerk aanwezig zijn.

## <a name="trusts-between-two-forests"></a>Vertrouwens relaties tussen twee forests

U kunt domein vertrouwen in één forest uitbreiden naar een ander forest door hand matig een eenrichtings-of twee richtings vertrouwensrelatie voor het forest te maken. Een forest-vertrouwens relatie is een transitieve vertrouwens relatie die alleen bestaat tussen een foresthoofddomein en een tweede forest-hoofd domein.

* Met een eenrichtings vertrouwensrelatie voor forests kunnen alle gebruikers in het ene forest alle domeinen in het andere forest vertrouwen.
* Een twee richtings relatie tussen forests vormt een transitieve vertrouwens relatie tussen elk domein in beide forests.

De transitiviteit van forestvertrouwensrelaties is beperkt tot de twee forest-partners. De vertrouwens relatie tussen forests en andere forests die door een van de partners worden vertrouwd, wordt niet uitgebreid.

![Diagram van forestvertrouwensrelatie van Azure AD DS naar on-premises AD DS](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

U kunt verschillende configuraties voor domein-en forest-vertrouwens relaties maken, afhankelijk van de AD DS structuur van de organisatie. Azure AD DS ondersteunt alleen een eenrichtings vertrouwensrelatie voor forests. In deze configuratie kunnen resources in het beheerde domein alle domeinen in een on-premises forest vertrouwen.

## <a name="supporting-technology-for-trusts"></a>Ondersteunende technologie voor vertrouwens relaties

Vertrouwens relaties gebruiken verschillende services en functies, zoals DNS, om domein controllers te vinden in partner forests. Vertrouwens relaties zijn ook afhankelijk van NTLM-en Kerberos-verificatie protocollen en op Windows gebaseerde autorisatie-en toegangs beheer mechanismen om een beveiligde communicatie-infra structuur te bieden tussen AD DS domeinen en forests. De volgende services en functies helpen u bij het ondersteunen van succes volle vertrouwens relaties.

### <a name="dns"></a>DNS

AD DS moet DNS zijn voor de locatie en naam van de domein controller (DC). De volgende ondersteuning van DNS wordt geboden om AD DS te kunnen werken:

* Een service voor naam omzetting die netwerk-hosts en-services toestaat om Dc's te vinden.
* Een naamgevings structuur waarmee een onderneming zijn organisatie structuur kan weer spie gelen in de namen van de domein Directory-service domeinen.

Een naam ruimte van een DNS-domein wordt meestal geïmplementeerd die overeenkomt met de naam ruimte AD DS domein. Als er vóór de implementatie van de AD DS een bestaande DNS-naam ruimte is, wordt de DNS-naam ruimte doorgaans gepartitioneerd voor AD DS en wordt een DNS-subdomein en delegering voor de hoofdmap van AD DS forest gemaakt. Extra DNS-domein namen worden vervolgens toegevoegd voor elk AD DS onderliggende domein.

DNS wordt ook gebruikt om de locatie van AD DS Dc's te ondersteunen. De DNS-zones worden gevuld met DNS-bron records waarmee netwerk-hosts en-services AD DS Dc's kunnen vinden.

### <a name="applications-and-net-logon"></a>Toepassingen en Net Logon

Zowel toepassingen als de Net Logon-service zijn onderdelen van het Windows-model voor gedistribueerde beveiliging. Toepassingen die zijn geïntegreerd met Windows Server en AD DS authenticatie protocollen gebruiken om te communiceren met de Net Logon-service zodat een beveiligd pad kan worden ingesteld waarover verificatie kan optreden.

### <a name="authentication-protocols"></a>Verificatieprotocollen

AD DS Dc's gebruikers en toepassingen verifiëren met behulp van een van de volgende protocollen:

* **Kerberos versie 5-verificatie Protocol**
    * Het protocol Kerberos versie 5 is het standaard verificatie protocol dat wordt gebruikt door on-premises computers waarop Windows wordt uitgevoerd en die besturings systemen van derden ondersteunen. Dit protocol is opgegeven in RFC 1510 en is volledig geïntegreerd met AD DS, SMB (Server Message Block), HTTP en Remote Procedure Call (RPC), evenals de client-en server toepassingen die gebruikmaken van deze protocollen.
    * Wanneer het Kerberos-protocol wordt gebruikt, hoeft de server geen contact te maken met de domein controller. In plaats daarvan krijgt de client een ticket voor een server door een aanvraag te doen van een domein controller in het Server account domein. De server valideert vervolgens het ticket zonder dat hiervoor een andere instantie wordt geraadpleegd.
    * Als een computer die deel uitmaakt van een trans actie, niet het Kerberos versie 5-protocol ondersteunt, wordt het NTLM-protocol gebruikt.

* **NTLM-verificatie Protocol**
    * Het NTLM-protocol is een klassiek netwerk verificatie protocol dat wordt gebruikt door oudere besturings systemen. Om compatibiliteits redenen wordt het door AD DS domeinen gebruikt voor het verwerken van netwerk verificatie aanvragen die afkomstig zijn van toepassingen die zijn ontworpen voor eerdere Windows-clients en-servers en besturings systemen van derden.
    * Wanneer het NTLM-protocol tussen een client en een server wordt gebruikt, moet de server contact opnemen met een domein authenticatie service op een DC om de client referenties te controleren. De server verifieert de client door de client referenties door te sturen naar een domein controller in het account domein van de client.
    * Wanneer twee AD DS domeinen of forests door een vertrouwens relatie zijn verbonden, kunnen verificatie aanvragen die gebruikmaken van deze protocollen, worden gerouteerd om toegang te bieden tot bronnen in beide forests.

## <a name="authorization-and-access-control"></a>Autorisatie en toegangs beheer

Verificatie-en vertrouwens technologieën werken samen om een beveiligde communicatie-infra structuur in AD DS domeinen of forests te bieden. Autorisatie bepaalt welk niveau van toegang een gebruiker heeft tot resources in een domein. Vertrouwens relaties vergemakkelijken interdomein-autorisatie van gebruikers door een pad op te geven voor de verificatie van gebruikers in andere domeinen, zodat hun aanvragen voor gedeelde bronnen in die domeinen kunnen worden geautoriseerd.

Wanneer een verificatie aanvraag in een vertrouwende domein is gevalideerd door het vertrouwde domein, wordt deze door gegeven aan de doel bron. De doel resource bepaalt vervolgens of de specifieke aanvraag door de gebruiker, de service of de computer in het vertrouwde domein moet worden geautoriseerd op basis van de toegangs beheer configuratie.

Vertrouwens relaties bieden dit mechanisme voor het valideren van verificatie aanvragen die worden door gegeven aan een vertrouwend domein. Toegangs beheer mechanismen op de bron computer bepalen het uiteindelijke niveau van toegang dat is verleend aan de aanvrager in het vertrouwde domein.

## <a name="next-steps"></a>Volgende stappen

Zie [Hoe kunnen forest-vertrouwens relaties werken in Azure AD DS?][concepts-trust] voor meer informatie over vertrouwens relaties.

Zie [een door Azure AD DS beheerd domein maken en configureren][tutorial-create-advanced]om aan de slag te gaan met het maken van een beheerd domein met een bron-forest. U kunt vervolgens [een uitgaande forest-vertrouwens relatie maken met een on-premises domein][create-forest-trust].

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
