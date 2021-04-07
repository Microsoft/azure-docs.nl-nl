---
title: Azure-app configuratie REST API-autorisatie
description: Referentie pagina's voor autorisatie met behulp van de Azure-app configuratie REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 70f05799ce2856ad490937a17b456e78789088f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932639"
---
# <a name="authorization"></a>Autorisatie

Autorisatie verwijst naar de procedure die wordt gebruikt om te bepalen welke machtigingen een beller heeft bij het maken van een aanvraag. Er zijn meerdere autorisatie modellen. Het autorisatie model dat voor een aanvraag wordt gebruikt, is afhankelijk van de gebruikte [verificatie](./rest-api-authentication-index.md) methode. De autorisatie modellen worden hieronder weer gegeven.

## <a name="hmac"></a>HMAC

Het [autorisatie model](./rest-api-authorization-hmac.md) model dat is gekoppeld aan HMAC-verificatie, splitst machtigingen op alleen-lezen of lezen-schrijven. Zie de pagina [HMAC-autorisatie](./rest-api-authorization-hmac.md) voor meer informatie.

## <a name="azure-active-directory"></a>Azure Active Directory

Het [autorisatie model](./rest-api-authorization-azure-ad.md) dat is gekoppeld aan Azure Active Directory-verificatie (Azure AD) maakt gebruik van Azure RBAC om machtigingen te beheren. Raadpleeg de [Azure AD-autorisatie](./rest-api-authorization-azure-ad.md) pagina voor meer informatie.
