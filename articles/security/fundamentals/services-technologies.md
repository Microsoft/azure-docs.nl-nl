---
title: Azure-beveiligings Services en-technologieën | Microsoft Docs
description: In dit artikel vindt u een overzicht van Azure-beveiligings Services en-technologieën.
services: security
documentationcenter: na
author: terrylanfear
manager: barbkess
editor: TomSh
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/29/2019
ms.author: terrylan
ms.openlocfilehash: 5914d5a54606f49f6fbd809a17e2b47375b58dcd
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105641977"
---
# <a name="security-services-and-technologies-available-on-azure"></a>Beveiligings Services en-technologieën die beschikbaar zijn op Azure

In onze besprekingen met huidige en toekomstige Azure-klanten wordt u vaak gevraagd "hebt u een lijst met alle aan beveiliging gerelateerde services en technologieën die Azure te bieden heeft?"

Wanneer u opties voor Cloud serviceproviders evalueert, is het handig om deze informatie te bevatten. We hebben deze lijst nu voorzien van de vraag om aan de slag te gaan.

In de loop van de tijd wordt deze lijst gewijzigd en groeit, net zoals in Azure. Controleer deze pagina regel matig om op de hoogte te blijven van de beveiligings-gerelateerde services en technologieën.

## <a name="general-azure-security"></a>Algemene Azure-beveiliging
|Service|Beschrijving|
|--------|--------|
|[Azure &nbsp; Security &nbsp; Center](../../security-center/security-center-introduction.md)| Een werk belasting voor de beveiliging van de cloud die beveiligings beheer en geavanceerde bedreigings beveiliging biedt in hybride Cloud werkbelastingen.|
|[Azure Key Vault](../../key-vault/general/overview.md)| Een beveiligde geheimen opslag voor de wacht woorden, verbindings reeksen en andere informatie die u nodig hebt om uw apps te laten werken. |
|[Azure Monitor-logboeken](../../azure-monitor/logs/log-query-overview.md)|Een bewakings service waarmee telemetrie en andere gegevens worden verzameld, en biedt een query taal en analyse-engine voor het leveren van operationele inzichten voor uw apps en resources. Kan alleen worden gebruikt of met andere services, zoals Security Center. |
|[Azure dev/test Labs](../../devtest-labs/devtest-lab-overview.md)|Een service waarmee ontwikkel aars en testers snel omgevingen in azure kunnen maken, terwijl verspilling en beheer kosten worden geminimaliseerd.  |

<!---|[Azure&nbsp;Disk&nbsp;Encryption](/azure/azure-security-disk-encryption-overview)| THIS WILL GO TO THE NEW OVERVIEW TOPIC MEGHAN STEWART IS WRITING|--->

## <a name="storage-security"></a>Opslagbeveiliging
|Service|Beschrijving|
|------|--------|
| [Versleuteling van Azure &nbsp; Storage- &nbsp; service &nbsp;](../../storage/common/storage-service-encryption.md)|Een beveiligings functie waarmee uw gegevens automatisch worden versleuteld in azure Storage.   |
|[StorSimple versleutelde hybride opslag](../../storsimple/storsimple-ova-overview.md)| Een geïntegreerde opslag oplossing voor het beheer van opslag taken tussen on-premises apparaten en Azure-Cloud opslag.|
|[Versleuteling van Azure Client-Side](../../storage/common/storage-client-side-encryption.md)| Een oplossing voor versleuteling aan client zijde waarmee gegevens binnen client toepassingen worden versleuteld voordat ze naar Azure Storage worden geüpload. tijdens het downloaden worden de gegevens ook ontsleuteld. |
| [Azure Storage hand tekeningen voor gedeelde toegang](../../storage/common/storage-sas-overview.md)|Een SAS (een handtekening voor gedeelde toegang) biedt gedelegeerde toegang tot resources in uw opslagaccount.  |
|[Azure Storage account sleutels](../../storage/common/storage-account-create.md)| Een toegangs beheer methode voor Azure Storage die wordt gebruikt voor verificatie wanneer het opslag account wordt geopend. |
|[Azure-bestands shares met SMB 3,0-versleuteling](../../storage/files/storage-files-introduction.md)|Een netwerk beveiligings technologie die automatische netwerk versleuteling mogelijk maakt voor het SMB-protocol (Server Message Block). |
|[Azure Opslaganalyse](/rest/api/storageservices/Storage-Analytics)| Een logboek registratie en metrische gegevens voor het genereren van uw opslag account. |

<!------>

## <a name="database-security"></a>Databasebeveiliging
|Service|Beschrijving|
|------|--------|
| [Azure &nbsp; SQL- &nbsp; firewall](../../azure-sql/database/firewall-configure.md)|Een functie voor netwerk toegangs beheer die bescherming biedt tegen aanvallen via het netwerk tot data base. |
|[Versleuteling van het niveau van Azure &nbsp; SQL- &nbsp; cellen &nbsp;](/archive/blogs/sqlsecurity/recommendations-for-using-cell-level-encryption-in-azure-sql-database)| Een Data Base-beveiligings technologie die versleuteling op een gedetailleerd niveau biedt.  |
| [Versleuteling van Azure &nbsp; SQL- &nbsp; verbinding](../../azure-sql/database/logins-create-manage.md)|Voor de beveiliging regelt SQL Database toegang met firewallregels die de connectiviteit beperken op basis van IP-adres. Met behulp van verificatiemechanismen moeten gebruikers hun identiteit kunnen aantonen en met autorisatiemechanismen worden gebruikers beperkt tot bepaalde gegevens en acties. |
| [Versleuteling van Azure SQL altijd](/sql/relational-databases/security/encryption/always-encrypted-database-engine)|Beveiligt gevoelige gegevens, zoals creditcard nummers of nationale identificatie nummers (bijvoorbeeld Amerikaans-sofi-nummers), die zijn opgeslagen in Azure SQL Database-of SQL Server-data bases.  |
| [Azure &nbsp; SQL &nbsp; transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)| Een functie voor database beveiliging waarmee de opslag van een volledige data base wordt versleuteld. |
| [Azure SQL Database controle](../../azure-sql/database/auditing-overview.md)|Een functie voor het controleren van de data base waarmee database gebeurtenissen worden bijgehouden en naar een audit logboek in uw Azure Storage-account wordt geschreven.  |


## <a name="identity-and-access-management"></a>Identiteits- en toegangsbeheer
|Service|Beschrijving|
|------|--------|
| [&nbsp;Toegangs beheer op basis van rollen in azure &nbsp;](../../role-based-access-control/role-assignments-portal.md)|Een functie voor toegangs beheer waarmee gebruikers alleen toegang hebben tot de resources die ze nodig hebben om toegang te krijgen op basis van hun rollen binnen de organisatie.  |
| [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)|Een op de cloud gebaseerde verificatie opslag die ondersteuning biedt voor een multi tenant, een Cloud Directory en meerdere identiteits beheer Services in Azure.  |
| [Azure Active Directory B2C](../../active-directory-b2c/overview.md)|Een service voor identiteits beheer waarmee u kunt bepalen hoe klanten zich kunnen aanmelden, aanmelden en hun profielen beheren bij het gebruik van Azure-toepassingen.   |
| [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md)| Een op de cloud gebaseerde en beheerde versie van Active Directory Domain Services. |
| [Azure AD Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md)| Een beveiligings voorziening die gebruikmaakt van verschillende verificatie-en verificatie methoden voordat toegang tot beveiligde gegevens wordt toegestaan. |

## <a name="backup-and-disaster-recovery"></a>Back-up en herstel na noodgevallen
|Service|Beschrijving|
|------|--------|
| [Azure &nbsp; Backup](../../backup/backup-overview.md)| Een Azure-service die wordt gebruikt om back-ups te maken van gegevens in de Azure-Cloud en deze te herstellen. |
| [Azure &nbsp; site &nbsp; Recovery](../../site-recovery/site-recovery-overview.md)|Een online service die de werk belastingen die worden uitgevoerd op fysieke en virtuele machines (Vm's) van een primaire site naar een secundaire locatie repliceert om het herstel van Services na een storing mogelijk te maken. |

## <a name="networking"></a>Netwerken
|Service|Beschrijving|
|------|--------|
| [Netwerk &nbsp; beveiligings &nbsp; groepen](../../virtual-network/virtual-network-vnet-plan-design-arm.md)| Een functie voor toegangs beheer op basis van het netwerk met behulp van een 5-tuple om beslissingen toe te staan of te weigeren.  |
| [Azure VPN-gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)| Een netwerk apparaat dat als een VPN-eind punt wordt gebruikt om cross-premises toegang tot virtuele Azure-netwerken toe te staan.  |
| [Azure Application Gateway](../../application-gateway/overview.md)|Een geavanceerde webtoepassing load balancer die op basis van URL kan omleiden en SSL-offloading kan uitvoeren. |
|[Web Application firewall](../../web-application-firewall/afds/afds-overview.md) (WAF)|Een functie van Application Gateway waarmee uw webtoepassingen gecentraliseerd worden beschermd tegen veelvoorkomende aanvallen en beveiligings problemen|
| [Azure Load Balancer](../../load-balancer/load-balancer-overview.md)|Een TCP/UDP-toepassings netwerk load balancer. |
| [Azure ExpressRoute](../../expressroute/expressroute-introduction.md)| Een specifieke WAN-verbinding tussen on-premises netwerken en virtuele netwerken van Azure. |
| [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md)| Een globale DNS-load balancer.|
| [Azure-toepassing proxy](../../active-directory/manage-apps/application-proxy.md)| Een verificatie-front-end die wordt gebruikt voor het beveiligen van externe toegang voor webtoepassingen die on-premises worden gehost. |
|[Azure Firewall](../../firewall/overview.md)|Een beheerde, Cloud service voor netwerk beveiliging die uw Azure Virtual Network-Resources beveiligt.|
|[Azure DDoS-beveiliging](../../ddos-protection/ddos-protection-overview.md)|Gecombineerd met aanbevolen procedures voor het ontwerpen van toepassingen, biedt beveiliging tegen DDoS-aanvallen.|
|[Service-eindpunten voor virtueel netwerk](../../virtual-network/virtual-network-service-endpoints-overview.md)|Breidt de privé-adres ruimte van uw virtuele netwerk en de identiteit van uw VNet uit met de Azure-Services, via een directe verbinding.|
