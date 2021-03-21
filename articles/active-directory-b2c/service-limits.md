---
title: Limieten en beperkingen voor Azure AD B2C-service
titleSuffix: Azure AD B2C
description: Verwijzing voor service limieten en-beperkingen voor Azure Active Directory B2C service.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/02/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 190d88e62069a34b61017a0079f75696d67f6c82
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99979909"
---
# <a name="azure-active-directory-b2c-service-limits-and-restrictions"></a>Limieten en beperkingen voor Azure Active Directory B2C-service

Dit artikel bevat de gebruiks beperkingen en andere service limieten voor de Azure Active Directory B2C (Azure AD B2C)-service.

## <a name="end-userconsumption-related-limits"></a>Limieten voor eind gebruikers/verbruik

De volgende service limieten voor eind gebruikers zijn van toepassing op alle verificatie-en autorisatie protocollen die worden ondersteund door Azure AD B2C, waaronder SAML, Open-ID Connect, OAuth2 en ROPC.

|Categorie |Limiet    |
|---------|---------|
|Aantal aanvragen per IP-adres per Azure AD B2C Tenant       |6000/5min          |
|Totaal aantal aanvragen per Azure AD B2C Tenant     |12000/min          |

Het aantal aanvragen kan variëren, afhankelijk van het aantal lees-en schrijf bewerkingen van de map die zich voordoen tijdens de Azure AD B2C gebruikers traject. Zo kan een eenvoudige aanmeld traject zijn dat lees bewerkingen van de map bestaat uit één aanvraag. Als de aanmeld traject ook de Directory moet bijwerken, wordt deze bewerking als een extra aanvraag beschouwd.

## <a name="azure-ad-b2c-configuration-limits"></a>Configuratie limieten Azure AD B2C

De volgende tabel bevat de beheer configuratie limieten in de Azure AD B2C-service.

|Categorie  |Limiet  |
|---------|---------|
|Aantal bereiken per toepassing        |1000          |
|Aantal [aangepaste kenmerken](user-profile-attributes.md#extension-attributes)   per gebruiker <sup>1</sup>       |100         |
|Aantal omleidings-Url's per toepassing       |100         |
|Aantal afmeldings-Url's per toepassing        |1          |
|Teken reeks limiet per kenmerk      |250 tekens          |
|Aantal B2C-tenants per abonnement      |20         |
|Niveaus van [overname](custom-policy-overview.md#inheritance-model) in aangepast beleid     |10         |
|Aantal beleids regels per Azure AD B2C Tenant      |200          |
|Maximale grootte van beleids bestand      |400 KB          |

<sup>1</sup> Zie ook [Azure AD-service limieten en-beperkingen](../active-directory/enterprise-users/directory-service-limits-restrictions.md).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [de richt lijnen voor beperking van Microsoft Graph](/graph/throttling) 
- Meer informatie over de [validatie verschillen voor Azure AD B2C toepassingen](../active-directory/develop/supported-accounts-validation.md)













