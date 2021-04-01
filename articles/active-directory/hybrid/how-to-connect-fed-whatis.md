---
title: Azure AD Connect en Federatie | Microsoft Docs
description: Deze pagina is de centrale locatie voor alle documentatie over AD FS bewerkingen die gebruikmaken van Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: f9107cf5-0131-499a-9edf-616bf3afef4d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/09/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a97142e0c512f4f95235ad08c94c852906d3efd8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92095853"
---
# <a name="azure-ad-connect-and-federation"></a>Azure AD Connect en federatie
Met Azure Active Directory (Azure AD) Connect kunt u Federatie configureren met on-premises Active Directory Federation Services (AD FS) en Azure AD. Met aanmelden via de Federatie kunt u gebruikers in staat stellen om zich aan te melden bij Azure AD-Services met hun on-premises wacht woorden, en, in het bedrijfs netwerk, zonder dat ze hun wacht woord opnieuw hoeven in te voeren. U kunt met behulp van de optie Federatie met AD FS een nieuwe installatie van AD FS implementeren of u kunt een bestaande installatie opgeven in een Windows Server 2012 R2-farm.

In dit onderwerp vindt u informatie over de federatieve functionele aspecten van Azure AD Connect. Het bevat koppelingen naar alle verwante onderwerpen. Zie [uw on-premises identiteiten integreren met Azure Active Directory](whatis-hybrid-identity.md)voor koppelingen naar Azure AD Connect.

## <a name="azure-ad-connect-federation-topics"></a>Azure AD Connect: Federatie onderwerpen
| Onderwerp | Wat er gebeurt en wanneer het wordt gelezen |
|:--- |:--- |
| **Opties voor Azure AD Connect gebruikers aanmelding** | |
| [Informatie over aanmeldings opties voor gebruikers](plan-connect-user-signin.md) |Meer informatie over verschillende aanmeldings opties voor gebruikers en hoe deze van invloed zijn op de gebruikers ervaring van Azure-aanmelding. |
| **AD FS installeren met behulp van Azure AD Connect** | |
| [Vereisten](how-to-connect-install-custom.md#ad-fs-configuration-prerequisites) |Zie de vereisten voor een geslaagde AD FS installatie via Azure AD Connect. |
| [Een AD FS-farm configureren](how-to-connect-install-custom.md#configuring-federation-with-ad-fs) |Installeer een nieuwe AD FS-farm met behulp van Azure AD Connect. |
| [Communiceren met Azure AD met een alternatieve aanmeldings-ID](how-to-connect-fed-management.md#alternateid) | Federatie configureren met alternatieve aanmeldings-ID  |
| **De configuratie van de AD FS wijzigen** | |
| [De vertrouwens relatie herstellen](how-to-connect-fed-management.md#repairthetrust) |Herstel de huidige vertrouwens relatie tussen on-premises AD FS en Microsoft 365/Azure. |
| [Een nieuwe AD FS-server toevoegen](how-to-connect-fed-management.md#addadfsserver) |Vouw een AD FS-Farm uit met een extra AD FS-server na de eerste installatie. |
| [Een nieuwe AD FS WAP-server toevoegen](how-to-connect-fed-management.md#addwapserver) |Vouw een AD FS-Farm uit met een extra WAP-server (Web Application proxy) na de eerste installatie. |
| [Een nieuw federatief domein toevoegen](how-to-connect-fed-management.md#addfeddomain) |Voeg een ander domein toe voor Federatie met Azure AD. |
| [Het TLS/SSL-certificaat bijwerken](how-to-connect-fed-ssl-update.md)| Het TLS/SSL-certificaat voor een AD FS-farm bijwerken. |
| [Federatie certificaten voor Microsoft 365 en Azure AD vernieuwen](how-to-connect-fed-o365-certs.md)|Vernieuw uw O365-certificaat met Azure AD.|
| **Andere Federatie configuratie** | |
| [Meerdere exemplaren van Azure AD federeren met één exemplaar van AD FS](how-to-connect-fed-single-adfs-multitenant-federation.md) | Meerdere Azure AD-servers met één AD FS Farm| 
| [Een aangepast bedrijfs logo/afbeelding toevoegen](how-to-connect-fed-management.md#customlogo) |Wijzig de aanmeldings ervaring door het aangepaste logo op te geven dat wordt weer gegeven op de aanmeldings pagina van AD FS. |
| [Een beschrijving van een aanmelding toevoegen](how-to-connect-fed-management.md#addsignindescription) |Wijzig de aanmeldings beschrijving op de aanmeldings pagina van AD FS. |
| [AD FS claim regels wijzigen](how-to-connect-fed-management.md#modclaims) |Wijzig of Voeg claim regels toe aan AD FS die overeenkomen met de configuratie van Azure AD Connect Sync. |


## <a name="additional-resources"></a>Aanvullende bronnen
* [Federeren twee Azure AD met één AD FS](how-to-connect-fed-single-adfs-multitenant-federation.md)
* [AD FS-implementatie in Azure](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs)
* [Multi-geografische AD FS implementatie met hoge Beschik baarheid in azure met Azure Traffic Manager](/windows-server/identity/ad-fs/deployment/active-directory-adfs-in-azure-with-azure-traffic-manager)