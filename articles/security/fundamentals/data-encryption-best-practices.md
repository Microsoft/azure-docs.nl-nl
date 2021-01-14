---
title: Best practices voor gegevens beveiliging en-versleuteling-Microsoft Azure
description: Dit artikel bevat een aantal aanbevolen procedures voor gegevens beveiliging en-versleuteling met ingebouwde Azure-functies.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: 17ba67ad-e5cd-4a8f-b435-5218df753ca4
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/09/2020
ms.author: terrylan
ms.openlocfilehash: 3808da24c1baf6dd6e3332d4bd26fd176b761c65
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98196075"
---
# <a name="azure-data-security-and-encryption-best-practices"></a>Best practices voor gegevensbeveiliging en -versleuteling in Azure
In dit artikel worden aanbevolen procedures beschreven voor gegevens beveiliging en-versleuteling.

De aanbevolen procedures zijn gebaseerd op een advies van de mening en ze werken met de huidige mogelijkheden en functie sets van het Azure-platform. Meningen en technologieën veranderen in de loop van de tijd en dit artikel wordt regel matig bijgewerkt om deze wijzigingen weer te geven.

## <a name="protect-data"></a>Gegevens beveiligen
Als u gegevens in de Cloud wilt beveiligen, moet u rekening houden met de mogelijke statussen waarin uw gegevens zich kunnen voordoen en welke besturings elementen beschikbaar zijn voor die status. Aanbevolen procedures voor Azure-gegevens beveiliging en-versleuteling hebben betrekking op de volgende gegevens statussen:

- In rust: Dit omvat alle opslag objecten, containers en typen van informatie die statisch aanwezig zijn op fysieke media, hetzij magnetische als optische schijf.
- Onderweg: wanneer gegevens worden overgedragen tussen onderdelen, locaties of Program ma's, is het onderweg. Voor beelden zijn overdracht via het netwerk, over een service bus (van on-premises naar de Cloud en vice versa, waaronder hybride verbindingen zoals ExpressRoute) of tijdens een invoer-en uitvoer proces.

## <a name="choose-a-key-management-solution"></a>Een oplossing voor sleutel beheer kiezen

Het beveiligen van uw sleutels is essentieel voor het beveiligen van uw gegevens in de Cloud.

Met [Azure Key Vault](../../key-vault/general/overview.md) kunt u de cryptografische sleutels en geheimen beveiligen die cloudtoepassingen en -services gebruiken. Key Vault stroomlijnt het beheerproces voor sleutels en zorgt dat u de controle houdt over de sleutels waarmee uw gegevens toegankelijk zijn en worden versleuteld. Ontwikkelaars kunnen binnen enkele minuten sleutels voor ontwikkel- en testdoeleinden maken en deze vervolgens migreren naar productiesleutels. Beveiligingsadministrator kunnen wanneer dit nodig is machtigingen aan sleutels verlenen (en intrekken).

U kunt Key Vault gebruiken om meerdere beveiligde containers te maken, ook wel kluizen genoemd. Deze kluizen worden ondersteund door Hsm's. Kluizen verminderen de kans op onbedoeld verlies van beveiligingsinformatie door de opslag van toepassingsgeheimen te centraliseren. Sleutelkluizen regelen en registreren ook de toegang tot alles wat erin is opgeslagen. Azure Key Vault kunt aanvragen en het vernieuwen van Transport Layer Security (TLS)-certificaten verwerken. Het bevat functies voor een robuuste oplossing voor het beheer van de levens cyclus van certificaten.

Azure Key Vault is ontworpen ter ondersteuning van toepassings sleutels en geheimen. Key Vault is niet bedoeld als een archief voor gebruikers wachtwoorden.

Hieronder vindt u aanbevolen beveiligings procedures voor het gebruik van Key Vault.

**Best Practice**: Verleen gebruikers, groepen en toepassingen toegang tot een specifiek bereik.   
**Details**: gebruik Azure RBAC vooraf gedefinieerde rollen. Als u bijvoorbeeld toegang wilt verlenen aan een gebruiker voor het beheren van sleutel kluizen, wijst u de vooraf gedefinieerde rol [Key Vault bijdrager](../../role-based-access-control/built-in-roles.md) aan deze gebruiker toe aan een bepaald bereik. Het bereik in dit geval is een abonnement, een resource groep of alleen een specifieke sleutel kluis. Als de vooraf gedefinieerde rollen niet aan uw behoeften voldoen, kunt u [uw eigen rollen definiëren](../../role-based-access-control/custom-roles.md).

**Best Practice**: bepalen wat gebruikers toegang hebben tot.   
**Details**: toegang tot een sleutel kluis wordt geregeld via twee afzonderlijke interfaces: beheer vlak en gegevensvlak. Het toegangsbeheer van de beheerlaag en het toegangsbeheer van de gegevenslaag werken onafhankelijk van elkaar.

Gebruik Azure RBAC om te bepalen wat gebruikers toegang hebben tot. Als u bijvoorbeeld een toepassing toegang wilt verlenen voor het gebruik van sleutels in een sleutel kluis, hoeft u alleen toegangs machtigingen voor het data-vlak te verlenen met behulp van het sleutel kluis toegangs beleid en is er geen beheer vlak toegang nodig voor deze toepassing. Als u daarentegen wilt dat een gebruiker de eigenschappen van de kluis en tags kan lezen, maar geen toegang heeft tot sleutels, geheimen of certificaten, kunt u deze gebruiker lees toegang verlenen met behulp van Azure RBAC en is er geen toegang nodig tot het gegevens vlak.

**Best Practice**: Sla certificaten op in uw sleutel kluis. Uw certificaten zijn van hoge waarde. In de verkeerde handen kan de beveiliging van uw toepassing of de beveiliging van uw gegevens worden aangetast.   
**Details**: Azure Resource Manager kunt certificaten die zijn opgeslagen in azure Key Vault, veilig implementeren op virtuele Azure-machines wanneer de virtuele machines worden geïmplementeerd. Door het juiste toegangsbeleid voor de sleutelkluis in te stellen, bepaalt u ook wie toegang tot uw certificaat krijgt. Een ander voordeel is dat u al uw certificaten op één plek beheert in Azure Key Vault. Zie [certificaten implementeren op vm's van door de klant beheerde Key Vault](/archive/blogs/kv/updated-deploy-certificates-to-vms-from-customer-managed-key-vault) voor meer informatie.

**Aanbevolen procedure**: Zorg ervoor dat u een verwijdering van sleutel kluizen of sleutel kluis objecten kunt herstellen.   
**Details**: het verwijderen van sleutel kluizen of sleutel kluis objecten kan onbedoeld of schadelijk zijn. Schakel de beveiliging voor voorlopig verwijderen en opschonen van Key Vault in, met name voor sleutels die worden gebruikt voor het versleutelen van data-at-rest. Het verwijderen van deze sleutels is gelijk aan gegevensverlies, dus u kunt zo nodig verwijderde kluizen en kluisobjecten herstellen. Oefen Key Vault herstel bewerkingen regel matig uit.

> [!NOTE]
> Als een gebruiker Inzender machtigingen (Azure RBAC) heeft voor een sleutel kluis beheer vlak, kunnen ze zichzelf toegang verlenen tot het gegevens vlak door een beleid voor sleutel kluis toegang in te stellen. We raden u aan om nauw keurig te bepalen wie inzenders toegang heeft tot uw sleutel kluizen, om ervoor te zorgen dat alleen geautoriseerde personen uw sleutel kluizen, sleutels, geheimen en certificaten kunnen openen en beheren.
>
>

## <a name="manage-with-secure-workstations"></a>Beheren met beveiligde werk stations

> [!NOTE]
> De abonnements beheerder of de eigenaar moet een beveiligd werk station of een privileged Access-werk station gebruiken.
>
>

Omdat de meeste aanvallen de eind gebruiker bedoelt, wordt het eind punt een van de primaire aanvallen. Een aanvaller die het eind punt verkrijgt, kan de referenties van de gebruiker gebruiken om toegang te krijgen tot de gegevens van de organisatie. De meeste endpoint-aanvallen profiteren van het feit dat gebruikers beheerders zijn op hun lokale werk station.

**Best Practice**: gebruik een veilig beheer werkstation om gevoelige accounts, taken en gegevens te beveiligen.   
**Details**: gebruik een [privileged Access-werk station](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) om de kwets baarheid op werk stations te verminderen. Deze beveiligde beheer werkstations kunnen u helpen een aantal van deze aanvallen te beperken en ervoor te zorgen dat uw gegevens veiliger zijn.

**Aanbevolen procedure**: zorg voor Endpoint Protection.   
**Details**: beveiligings beleid afdwingen op alle apparaten die worden gebruikt voor het verbruiken van gegevens, ongeacht de gegevens locatie (Cloud of on-premises).

## <a name="protect-data-at-rest"></a>Data-at-rest beschermen

[Gegevens versleuteling in rust](https://cloudblogs.microsoft.com/microsoftsecure/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) is een verplichte stap voor gegevens privacy, naleving en data soevereiniteit.

**Best Practice**: schijf versleuteling Toep assen om uw gegevens te beveiligen.   
**Details**: gebruik [Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md). IT-beheerders kunnen de VM-schijven van Windows en Linux IaaS versleutelen. Schijf versleuteling combineert de standaard Windows BitLocker-functie en de Linux DM-cryptografie functie om volume versleuteling voor het besturings systeem en de gegevens schijven te bieden.

Azure Storage en Azure SQL Database gegevens op rest standaard versleutelen, en veel services bieden versleuteling als optie. U kunt Azure Key Vault gebruiken om controle te houden over de codes die toegang tot uw gegevens hebben en deze versleutelen. Zie [Azure resource providers Encryption model support voor meer informatie](encryption-atrest.md#azure-resource-providers-encryption-model-support).

**Best practices**: versleuteling gebruiken om Risico's te verhelpen die betrekking hebben op onbevoegde toegang tot gegevens.   
**Details**: Versleutel uw stations voordat u er gevoelige gegevens naar schrijft.

Organisaties die geen gegevens versleuteling afdwingen, worden meer blootgesteld aan problemen met gegevens vertrouwelijkheid. Niet-geautoriseerde of Rogue gebruikers kunnen bijvoorbeeld gegevens stelen in gemanipuleerde accounts of ongeoorloofde toegang krijgen tot gegevens die zijn gecodeerd met een lees bare indeling. Bedrijven moeten er ook voor zorgen dat ze regel maat zijn en de juiste beveiligings maatregelen gebruiken om hun gegevens beveiliging te verbeteren teneinde te voldoen aan de industriële voor Schriften.

## <a name="protect-data-in-transit"></a>Gegevens tijdens de overdracht beveiligen

Het beschermen van gegevens tijdens de overdracht moet een essentieel onderdeel zijn van uw strategie om gegevens te beschermen. Omdat gegevens tussen veel locaties worden verplaatst, adviseren we over het algemeen om SSL-/TLS-protocollen te gebruiken om gegevens tussen verschillende locaties uit te wisselen. In sommige omstandigheden wilt u mogelijk het gehele communicatiekanaal tussen uw lokale infrastructuur en de cloud isoleren met behulp van een VPN.

U kunt gepaste beschermingsmaatregelen gebruiken, zoals HTTPS of VPN, voor gegevens die worden verplaatst tussen uw lokale infrastructuur en Azure. Gebruik [Azure VPN gateway](../../vpn-gateway/index.yml)bij het verzenden van versleuteld verkeer tussen een virtueel Azure-netwerk en een on-premises locatie via het open bare Internet.

Hieronder vindt u aanbevolen procedures voor het gebruik van Azure VPN Gateway, SSL/TLS en HTTPS.

**Best Practice**: veilige toegang vanaf meerdere werk stations op locatie naar een virtueel Azure-netwerk.   
**Details**: gebruik [site-naar-site-VPN](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

**Best Practice**: veilige toegang vanaf een individueel werk station op locatie in een virtueel Azure-netwerk.   
**Details**: gebruik [punt-naar-site-VPN](../../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md).

**Aanbevolen procedure**: Verplaats grotere gegevens sets over een speciale WAN-verbinding met hoge snelheid.   
**Details**: gebruik [ExpressRoute](../../expressroute/expressroute-introduction.md). Als u ervoor kiest om ExpressRoute te gebruiken, kunt u de gegevens ook in de toepassing zelf versleutelen door SSL/TLS of andere protocollen te gebruiken voor extra bescherming.

**Best Practice**: interactie met Azure Storage via de Azure Portal.   
**Details**: alle trans acties worden uitgevoerd via https. U kunt ook [opslag rest API](/rest/api/storageservices/) over https gebruiken om te communiceren met [Azure Storage](https://azure.microsoft.com/services/storage/).

Organisaties die gegevens in de overdracht niet kunnen [beveiligen, zijn](/previous-versions/office/skype-server-2010/gg195641(v=ocs.14))gevoeliger voor [man-in-the-middle-aanvallen en het overnemen van](/previous-versions/office/skype-server-2010/gg195821(v=ocs.14))sessies. Deze aanvallen kunnen de eerste stap zijn om toegang te krijgen tot vertrouwelijke gegevens.

## <a name="secure-email-documents-and-sensitive-data"></a>Beveiligde e-mail, documenten en gevoelige gegevens

U wilt e-mail berichten, documenten en gevoelige gegevens die u deelt buiten uw bedrijf beheren en beveiligen. [Azure Information Protection](/azure/information-protection/) is een Cloud oplossing waarmee een organisatie documenten en e-mail berichten kan classificeren, labelen en beveiligen. Dit kan automatisch worden gedaan door beheerders die regels en voor waarden definiëren, hand matig door gebruikers of een combi natie van waar gebruikers aanbevelingen krijgen.

Classificatie kan te allen tijde worden geïdentificeerd, ongeacht waar de gegevens worden opgeslagen of met wie ze worden gedeeld. De labels bevatten visuele markeringen, zoals een koptekst, voettekst of watermerk. Metagegevens worden als ongecodeerde tekst aan bestanden en koppen van e-mails toegevoegd. De Lees bare tekst zorgt ervoor dat andere services, zoals oplossingen om gegevens verlies te voor komen, de classificatie kunnen identificeren en passende maat regelen te nemen.

De beveiligings technologie maakt gebruik van Azure Rights Management (Azure RMS). Deze technologie is geïntegreerd met andere micro soft-Cloud Services en-toepassingen, zoals Microsoft 365 en Azure Active Directory. Deze beveiligingstechnologie maakt gebruik van beleidsregels voor versleuteling, identiteit en verificatie. Beveiliging die wordt toegepast via Azure RMS blijft met de documenten en e-mail berichten, onafhankelijk van de locatie, binnen of buiten uw organisatie, netwerken, bestands servers en toepassingen.

Met deze oplossing voor gegevens beveiliging blijft u de controle over uw gegevens, zelfs wanneer deze met andere personen worden gedeeld. U kunt Azure RMS ook gebruiken met uw eigen line-of-business-toepassingen en oplossingen voor gegevens beveiliging van software leveranciers, ongeacht of deze toepassingen en oplossingen on-premises of in de Cloud zijn.

U wordt aangeraden dat u:

- [Implementeer Azure Information Protection](/azure/information-protection/deployment-roadmap) voor uw organisatie.
- Pas labels toe die overeenkomen met uw bedrijfs vereisten. Bijvoorbeeld: een label met de naam ' uiterst vertrouwelijk ' toep assen op alle documenten en e-mail berichten die de gegevens van het hoogste geheim bevatten, om deze gegevens te classificeren en te beveiligen. Vervolgens hebben alleen gemachtigde gebruikers toegang tot deze gegevens, met de beperkingen die u opgeeft.
- Configureer [gebruiks logboek registratie voor Azure RMS](/azure/information-protection/log-analyze-usage) zodat u kunt controleren hoe uw organisatie de beveiligings service gebruikt.

Organisaties die weinig [gegevens classificatie](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) en bestands beveiliging hebben, zijn mogelijk gevoeliger voor gegevens lekkage of misbruik van gegevens. Met de juiste bestands beveiliging kunt u gegevens stromen analyseren om inzicht te krijgen in uw bedrijf, Risk ante gedrag te detecteren en corrigerende maat regelen te nemen, de toegang tot documenten te volgen, enzovoort.

## <a name="next-steps"></a>Volgende stappen

Zie [Aanbevolen procedures en patronen voor Azure-beveiliging](best-practices-and-patterns.md) voor meer aanbevolen procedures voor beveiliging bij het ontwerpen, implementeren en beheren van uw cloud oplossingen met behulp van Azure.

De volgende resources zijn beschikbaar om meer algemene informatie te geven over Azure-beveiliging en gerelateerde micro soft-Services:
* [Blog van het Azure-beveiligings team](/archive/blogs/azuresecurity/) : voor actuele informatie over de nieuwste Azure-beveiliging
* [Micro soft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) : waar micro soft-beveiligings problemen, met inbegrip van problemen met Azure, kunnen worden gerapporteerd of via e-mail worden verzonden naar secure@microsoft.com