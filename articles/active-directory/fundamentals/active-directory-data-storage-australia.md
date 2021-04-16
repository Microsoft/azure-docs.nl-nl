---
title: Opslag van identiteitsgegevens voor Australische en Nieuw-Zeelandse klanten - Azure AD
description: Meer informatie over waar Azure Active Directory identiteitsgerelateerde gegevens voor klanten uit Australië en Nieuw-Zeeland op slaat.
services: active-directory
author: ajburnle
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4bb095e93a3728835e26cbe283f79569c91b7487
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479063"
---
# <a name="identity-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Opslag van identiteitsgegevens voor Australische en Nieuw-Zeelandse klanten in Azure Active Directory

Identiteitsgegevens worden door Azure AD opgeslagen op een geografische locatie op basis van het adres dat is opgegeven door uw organisatie wanneer u zich abonneert op een Microsoft Online-service zoals Microsoft 365 en Azure. Voor informatie over waar uw identiteitsgegevens van klanten worden opgeslagen, kunt u de sectie Waar bevinden uw gegevens [zich?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) van het Microsoft Trust Center gebruiken.

> [!NOTE]
> Services en toepassingen die zijn geïntegreerd met Azure AD hebben toegang tot identiteitsgegevens van klanten. Evalueer elke service en toepassing die u gebruikt om te bepalen hoe identiteitsgegevens van de klant worden verwerkt door die specifieke service en toepassing en of ze voldoen aan de vereisten voor gegevensopslag van uw bedrijf. Zie de sectie Waar bevinden uw gegevens zich? van het Microsoft Trust Center voor meer informatie over de gegevenslocatie van Microsoft-services.

Voor klanten die een adres in Australië of Nieuw-Zeeland hebben opgegeven, bewaart Azure AD identiteitsgegevens voor deze services in Australische datacenters: 
- Azure AD Directory Management 
- Verificatie

Alle andere Azure AD-services slaan klantgegevens op in wereldwijde datacenters. Als u het datacenter voor een service wilt vinden, [Azure Active Directory: waar bevinden uw gegevens zich?](https://aka.ms/AADDataMap)

## <a name="microsoft-azure-ad-multi-factor-authentication-mfa"></a>Microsoft Azure AD Multi-Factor Authentication (MFA)

Met MFA worden identiteitsgegevens van klanten opgeslagen in wereldwijde datacenters. Zie Azure [Multi-Factor](../authentication/concept-mfa-data-residency.md)Authentication-gebruikersgegevensverzameling voor meer informatie over de gebruikersgegevens die zijn verzameld en opgeslagen door Azure AD MFA en Azure MFA-server in de cloud.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen voor meer informatie over een van de functies en functionaliteit die hierboven worden beschreven:
- [Wat is Multi-Factor Authentication?](../authentication/concept-mfa-howitworks.md)
