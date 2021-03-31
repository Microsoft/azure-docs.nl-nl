---
title: Koppeling van gebruikers claims voor B2B-samen werking-Azure Active Directory
description: Pas de gebruikers claims aan die zijn uitgegeven in het SAML-token voor Azure Active Directory (Azure AD) B2B-gebruikers.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/06/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3508865d9f89501cf70e09087c6a609beb6662b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87908747"
---
# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>Toewijzing van gebruikers claims voor B2B-samen werking in Azure Active Directory

Azure Active Directory (Azure AD) biedt ondersteuning voor het aanpassen van de claims die in het SAML-token voor B2B-samenwerkings gebruikers worden uitgegeven. Wanneer een gebruiker zich bij de toepassing bevindt, geeft Azure AD een SAML-token door aan de app die informatie (of claims) bevat over de gebruiker die deze unieke identificeert. Dit omvat standaard de gebruikers naam, het e-mail adres, de voor naam en de achternaam van de gebruiker.

In de [Azure Portal](https://portal.azure.com)kunt u de claims weer geven of bewerken die in het SAML-token naar de toepassing worden verzonden. Als u toegang wilt krijgen tot de instellingen, selecteert u **Azure Active Directory**  >  **bedrijfs toepassingen** > de toepassing die is geconfigureerd voor eenmalige aanmelding > **eenmalige aanmelding**. Zie de SAML-token instellingen in de sectie **gebruikers kenmerken** .

![Hiermee worden de kenmerken van SAML-tokens in de gebruikers interface weer gegeven](media/claims-mapping/view-claims-in-saml-token.png)

Er zijn twee mogelijke redenen waarom u de claims die in het SAML-token zijn uitgegeven, mogelijk moet bewerken:

1. Voor de toepassing is een andere set claim-Uri's of claim waarden vereist.

2. Voor de toepassing moet de claim van de NameIdentifier iets anders zijn dan de user principal name (UPN) die is opgeslagen in azure AD.

Zie [claims aanpassen die zijn uitgegeven in het SAML-token voor zakelijke toepassingen in azure Active Directory](../develop/active-directory-saml-claims-customization.md)voor informatie over het toevoegen en bewerken van claims.

Voor B2B-samenwerkings gebruikers worden NameID en UPN-cross-Tenant om veiligheids redenen voor komen.

## <a name="next-steps"></a>Volgende stappen

- Zie [Eigenschappen van een Azure Active Directory B2B-samenwerkings gebruiker](user-properties.md)voor meer informatie over gebruikers eigenschappen van B2B-samen werking.
- Zie voor meer informatie over gebruikers tokens voor B2B-samenwerkings gebruikers [inzicht in gebruikers tokens in azure AD B2B-samen werking](user-token.md).

