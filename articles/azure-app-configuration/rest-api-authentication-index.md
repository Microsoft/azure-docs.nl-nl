---
title: Azure-app configuratie REST API-verificatie
description: Referentie pagina's voor verificatie met behulp van de Azure-app configuratie REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 56416009395ebf8270ad0fa8d141277424dd6d9a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183461"
---
# <a name="authentication"></a>Verificatie

Alle HTTP-aanvragen moeten worden geverifieerd. De volgende verificatie schema's worden ondersteund.

## <a name="hmac"></a>HMAC

[HMAC-verificatie](./rest-api-authentication-hmac.md) maakt gebruik van een wille keurig gegenereerd geheim voor het ondertekenen van nettoladingen van aanvragen. Details over hoe aanvragen die gebruikmaken van deze verificatie methode worden geautoriseerd, vindt u in de sectie [HMAC-autorisatie](./rest-api-authorization-hmac.md) .

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory-verificatie (Azure AD)](../active-directory/authentication/overview-authentication.md) maakt gebruik van een Bearer-token dat is verkregen van Azure Active Directory om aanvragen te verifiëren. Meer informatie over hoe aanvragen die gebruikmaken van deze verificatie methode, kunt u vinden in de sectie over [Azure AD-autorisatie](./rest-api-authorization-azure-ad.md) .