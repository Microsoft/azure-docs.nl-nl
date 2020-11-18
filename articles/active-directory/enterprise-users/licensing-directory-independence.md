---
title: Kenmerken van de interactie tussen meerdere tenants - Azure AD | Microsoft Docs
description: Meer informatie over de gegevensonafhankelijkheid van uw Azure Active Directory-organisaties
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: overview
ms.workload: identity
ms.subservice: enterprise-users
ms.date: 11/15/2020
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: b35c8630f47910c61e169d405c2117e53d0d536e
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94650524"
---
# <a name="understand-how-multiple-azure-active-directory-organizations-interact"></a>Meer informatie over de interactie tussen meerdere Azure Active Directory-organisaties

In Azure Active Directory (Azure AD) is elke Azure AD-organisatie volledig onafhankelijk: een peer die logisch onafhankelijk is van de andere Azure AD-organisaties die u beheert. Onder deze onafhankelijkheid van uw organisaties vallen ook resourceonafhankelijkheid, beheeronafhankelijkheid en synchronisatieonafhankelijkheid. Er is geen hiërarchische relatie tussen organisaties.

## <a name="resource-independence"></a>Resourceonafhankelijkheid

* Wanneer u een Azure AD-resource maakt in of verwijdert uit een organisatie, heeft dat geen gevolgen voor de resources in andere organisaties. Externe gebruikers vormen hier een gedeeltelijke uitzondering op.
* Als u een van uw domeinnamen registreert voor één organisatie, kan deze niet worden gebruikt door een andere organisatie.

## <a name="administrative-independence"></a>Beheeronafhankelijkheid

Als een gebruiker (geen beheerder) van de organisatie Contoso de testorganisatie 'Test' maakt:

* De gebruiker die een organisatie maakt, wordt standaard toegevoegd als externe gebruiker in die nieuwe organisatie, en krijgt de rol 'Globale beheerder' in die organisatie.
* De beheerders van de organisatie Contoso hebben geen directe beheerdersrechten voor de organisatie Test, tenzij een beheerder van Test hun deze rechten verleent. Beheerders van Contoso kunnen de toegang tot de organisatie Test echter beheren als ze het beheer hebben over het gebruikersaccount waarmee Test is gemaakt.
* Als u een Azure AD-rol toevoegt of verwijdert voor een gebruiker in de ene organisatie, heeft de wijziging geen invloed op de rollen die de gebruiker in een andere Azure AD-organisatie zijn toegewezen.

## <a name="synchronization-independence"></a>Synchronisatieonafhankelijkheid

U kunt elke Azure AD-organisatie onafhankelijk zodanig configureren dat de gegevens worden gesynchroniseerd vanuit één exemplaar van:

* Het hulpprogramma Azure AD Connect, om de gegevens met één AD-forest te synchroniseren.
* De Azure Active Directory-connector voor Forefront Identity Manager, om gegevens met één of meer on-premises forests en/of niet-Azure AD-gegevensbronnen te synchroniseren.

## <a name="add-an-azure-ad-organization"></a>Een Azure AD-organisatie toevoegen

Als u een Azure AD-organisatie wilt toevoegen in de Azure-portal, meldt u zich aan bij [de Azure-portal](https://portal.azure.com) met een account dat een globale beheerder van Azure AD is en selecteert u **Nieuw**.

> [!NOTE]
> In tegenstelling tot andere Azure-resources, zijn uw Azure AD-organisaties geen onderliggende resources van een Azure-abonnement. Als uw Azure-abonnement is geannuleerd of verlopen, hebt u nog wel toegang tot de gegevens van uw Azure AD-organisatie met behulp van Azure PowerShell, de Microsoft Graph API of het Microsoft 365-beheercentrum. U kunt ook [een ander abonnement koppelen aan de organisatie](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
>

## <a name="next-steps"></a>Volgende stappen

Zie [Wat is Azure Active Directory licentieverlening?](../fundamentals/active-directory-licensing-whatis-azure-portal.md) voor overwegingen en best practices bij het nemen van een Azure AD-licentie.
