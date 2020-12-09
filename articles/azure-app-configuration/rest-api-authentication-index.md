---
title: Azure-app configuratie REST API-verificatie
description: Referentie pagina's voor verificatie met behulp van de Azure-app configuratie REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 58fba309f4cf7e4fc4364d075500c02e0ed45259
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932690"
---
# <a name="authentication"></a>Verificatie

Alle HTTP-aanvragen moeten worden geverifieerd. De volgende verificatie schema's worden ondersteund.

## <a name="hmac"></a>HMAC

[HMAC-verificatie](./rest-api-authentication-hmac.md) maakt gebruik van een wille keurig gegenereerd geheim voor het ondertekenen van nettoladingen van aanvragen. Details over hoe aanvragen die gebruikmaken van deze verificatie methode worden geautoriseerd, vindt u in de sectie [HMAC-autorisatie](./rest-api-authorization-hmac.md) .

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory-verificatie (Azure AD)](../active-directory/authentication/overview-authentication.md) maakt gebruik van een Bearer-token dat is verkregen van Azure Active Directory om aanvragen te verifiëren. Meer informatie over hoe aanvragen die gebruikmaken van deze verificatie methode, kunt u vinden in de sectie over [Azure AD-autorisatie](./rest-api-authorization-azure-ad.md) .