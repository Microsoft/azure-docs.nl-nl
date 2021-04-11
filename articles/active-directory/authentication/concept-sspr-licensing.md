---
title: Self-service voor wachtwoord herstel van licentie-Azure Active Directory
description: Meer informatie over het verschil Azure Active Directory selfservice voor wachtwoord herstel
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d332c831cc764c61a4672ea5ad1db231b68e106
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952368"
---
# <a name="licensing-requirements-for-azure-active-directory-self-service-password-reset"></a>Licentie vereisten voor Azure Active Directory self-service voor wachtwoord herstel

Gebruikers accounts in Azure Active Directory (Azure AD) kunnen worden ingeschakeld voor de self-service voor wachtwoord herstel (SSPR) om de helpdesk oproepen en productiviteits verlies te verminderen wanneer een gebruiker zich niet kan aanmelden bij hun apparaat of een toepassing. Functies die deel uitmaken van SSPR bevatten wachtwoord wijziging, opnieuw instellen, ontgrendelen en terugschrijven naar een on-premises Directory. De basis functies van SSPR zijn beschikbaar in Microsoft 365 Business Standard of hoger en alle Azure AD Premium Sku's gratis.

In dit artikel vindt u meer informatie over de verschillende manieren waarop self-service voor wachtwoord herstel kan worden gelicentieerd en gebruikt. Zie de [pagina met prijzen voor Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)voor specifieke informatie over prijzen en facturering.

## <a name="compare-editions-and-features"></a>Edities en functies vergelijken

SSPR vereist alleen een licentie voor de Tenant. 

De volgende tabel bevat een overzicht van de verschillende SSPR-scenario's voor het wijzigen van wacht woorden, resetten of on-premises terugschrijven, en welke Sku's de functie bieden.

| Functie | Azure AD Free | Microsoft 365 Business standaard | Microsoft 365 Business Premium | Azure AD Premium P1 of P2 |
| --- |:---:|:---:|:---:|:---:|
| **Gebruikers wachtwoord wijziging in de Cloud**<br />Wanneer een gebruiker in azure AD het wacht woord kent en wilt wijzigen in een nieuwe waarde. | ● | ● | ● | ● |
| **Alleen Cloud gebruikers wachtwoord opnieuw instellen**<br />Wanneer een gebruiker in azure AD het wacht woord verg eten is en opnieuw moet worden ingesteld. | | ● | ● | ● |
| **Gebruikers wachtwoord wijzigen of opnieuw instellen met on-premises terugschrijven**<br />Wanneer een gebruiker in azure AD die is gesynchroniseerd vanuit een on-premises map met Azure AD Connect het wacht woord wil wijzigen of opnieuw wilt instellen en het nieuwe wacht woord moet worden teruggeschreven naar on-premises. | | | ● | ● |

> [!WARNING]
> Standalone Microsoft 365 Basic-en Standard Licensing-abonnementen bieden geen ondersteuning voor SSPR met on-premises terugschrijven. De on-premises write-functie vereist Azure AD Premium P1, Premium P2 of Microsoft 365 Business Premium.

Zie de volgende pagina's voor aanvullende informatie over licenties, inclusief kosten:

* [Azure Active Directory prijzen](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory functies en mogelijkheden](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Zakelijk](https://www.microsoft.com/microsoft-365/enterprise)
* [Microsoft 365 Business](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="next-steps"></a>Volgende stappen

Voltooi de volgende zelf studie om aan de slag te gaan met SSPR:

> [!div class="nextstepaction"]
> [Zelf studie: Self-service voor wacht woord opnieuw instellen inschakelen (SSPR)](tutorial-enable-sspr.md)