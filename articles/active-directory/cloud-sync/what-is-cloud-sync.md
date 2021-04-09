---
title: Wat is Azure AD Connect-cloudsynchronisatie? | Microsoft Docs
description: Hierin wordt Azure AD Connect-cloudsynchronisatie beschreven.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/11/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3b880d70baf7c160f8235bd01a5000f83825c36
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98614489"
---
# <a name="what-is-azure-ad-connect-cloud-sync"></a>Wat is Azure AD Connect-cloudsynchronisatie?
Azure AD Connect-cloudsynchronisatie is een nieuwe aanbieding van Microsoft, ontworpen om te voldoen aan uw hybride identiteitsdoelen voor synchronisatie van gebruikers, groepen en contactpersonen met Azure AD.  Dit wordt bereikt door gebruik te maken van de Azure AD-inrichtingsagent in plaats van de Azure AD Connect-toepassing.  De agent kan echter naast Azure AD Connect-synchronisatie worden gebruikt en biedt de volgende voordelen:
    
- Ondersteuning voor het synchroniseren naar een Azure AD-tenant vanuit een niet-verbonden Active Directory-forestomgeving met meerdere forests: De algemene scenario's omvatten samenvoeging en overname, waarbij de AD-forests van het overgenomen bedrijf zijn geïsoleerd van de AD-forests van het moederbedrijf en bedrijven die historisch meerdere AD-forests hadden.
- Vereenvoudigde installatie met lichtgewicht inrichtingsagents: De agents fungeren als een brug van AD naar Azure AD, waarbij alle synchronisatieconfiguratie in de cloud wordt beheerd. 
- Er kunnen meerdere inrichtingsagents worden gebruikt om implementaties met een hoge beschikbaarheid te vereenvoudigen. Dit is met name belangrijk voor organisaties die wachtwoord-hashsynchronisatie van AD naar Azure AD gebruiken.
- Ondersteuning voor grote groepen (maximaal 50.000 leden). Het is raadzaam om alleen het OE-bereikfilter te gebruiken bij het synchroniseren van grote groepen.


![Wat is Azure AD Connect?](media/what-is-cloud-sync/architecture-1.png)

## <a name="how-is-azure-ad-connect-cloud-sync-different-from-azure-ad-connect-sync"></a>Hoe verschilt Azure AD Connect-cloudsynchronisatie van Azure AD Connect-synchronisatie?
Met Azure AD Connect-cloudsynchronisatie wordt het inrichten van AD naar Azure AD ingedeeld in Microsoft Online Services. Een organisatie hoeft alleen in hun on-premises en IaaS-omgeving een lichtgewicht agent te implementeren die als een brug tussen Azure AD en AD fungeert. De inrichtingsconfiguratie wordt opgeslagen in Azure AD en wordt beheerd als onderdeel van de service.

## <a name="azure-ad-connect-cloud-sync-video"></a>Video: Azure AD Connect-cloudsynchronisatie
De volgende korte video biedt een uitstekend overzicht van Azure AD Connect-cloudsynchronisatie:

> [!VIDEO https://youtube.com/embed/mOT3ID02_YQ]


## <a name="comparison-between-azure-ad-connect-and-cloud-sync"></a>Vergelijking tussen Azure AD Connect en cloudsynchronisatie

In de volgende tabel worden Azure AD Connect en Azure AD Connect-cloudsynchronisatie met elkaar vergeleken:

| Functie | Azure Active Directory Connect-synchronisatie| Azure Active Directory Connect-cloudsynchronisatie |
|:--- |:---:|:---:|
|Verbinding maken met één on-premises AD-forest|● |● |
| Verbinding maken met meerdere on-premises AD-forests |● |● |
| Verbinding maken met meerdere niet-verbonden on-premises AD-forests | |● |
| Model met installatie van lichtgewicht agent | |● |
| Meerdere actieve agents voor hoge beschikbaarheid | |● |
| Verbinding maken met LDAP-directory's|●| | 
| Ondersteuning voor gebruikersobjecten |● |● |
| Ondersteuning voor groepsobjecten |● |● |
| Ondersteuning voor verbindingsobjecten |● |● |
| Ondersteuning voor apparaatobjecten |● | |
| Basisaanpassing toestaan voor kenmerkstromen |● |● |
| Exchange Online-kenmerken synchroniseren |● |● |
| Extensiekenmerken 1-15 synchroniseren |● |● |
| Door de klant gedefinieerde AD-kenmerken synchroniseren (directory-extensies) |● | |
| Ondersteuning voor wachtwoord-hashsynchronisatie |●|●|
| Ondersteuning voor passthrough-verificatie |●||
| Ondersteuning voor federatie |●|●|
| Naadloze eenmalige aanmelding|● |●|
| Biedt ondersteuning voor installatie op een domeincontroller |● |● |
| Ondersteuning voor Windows Server 2012 en Windows Server 2012 R2 |● |● |
| Filteren op domeinen/OE's/groepen |● |● |
| Filteren op kenmerkwaarden van objecten |● | |
| Toestaan dat minimale set kenmerken worden gesynchroniseerd (MinSync) |● |● |
| Toestaan dat het verwijderen van kenmerken van AD naar Azure AD stroomt |● |● |
| Geavanceerd aanpassen voor kenmerkstromen toestaan |● | |
| Ondersteuning voor write-back (wachtwoorden, apparaten, groepen) |● | |
| Azure Active Directory Domain Services-ondersteuning|● | |
| [Hybride Exchange-write-back](../hybrid/reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) |● | |
| Ondersteuning voor maximaal 150.000 objecten per AD-domein |● |● |
| Ondersteuning voor grote groepen, groepen met maximaal 50.000 leden |● |● |
| Verwijzingen naar meerdere domeinen|● | |
| Inrichting on-demand| |● |

## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichting?](what-is-provisioning.md)
- [Cloudsynchronisatie installeren](how-to-install.md)
