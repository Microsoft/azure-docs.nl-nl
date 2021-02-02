---
title: Beheerde identiteiten
description: Media Services kan worden gebruikt met door Azure beheerde identiteiten.
keywords: ''
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: inhenkel
ms.openlocfilehash: 71a2b8f0734de80f71dbb2372f8600b464d6c606
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258436"
---
# <a name="managed-identities"></a>Beheerde identiteiten

Een veelvoorkomende uitdaging voor ontwikkelaars is het beheer van geheimen en referenties om communicatie tussen verschillende services te beveiligen. In Azure elimineren beheerde identiteiten de noodzaak voor ontwikkelaars om referenties te beheren door een identiteit voor de Azure-resource in Azure AD op te geven en deze te gebruiken voor het verkrijgen van Azure Active Directory-tokens (Azure AD).

Er zijn momenteel twee scenario's waarin beheerde identiteiten kunnen worden gebruikt met Media Services:

- Gebruik de beheerde identiteit van het Media Services-account voor toegang tot opslag accounts.

- Gebruik de beheerde identiteit van het Media Services account om toegang te krijgen tot Key Vault voor toegang tot klant sleutels.

In de volgende twee secties worden de stappen van de twee scenario's beschreven.

## <a name="use-the-managed-identity-of-the-media-services-account-to-access-storage-accounts"></a>De beheerde identiteit van het Media Services account gebruiken voor toegang tot opslag accounts

1. Maak een Media Services-account met een beheerde identiteit.
1. Verleen de beheerde identiteits-Principal toegang tot een opslag account dat u bezit.
1. Media Services kunt vervolgens met behulp van de beheerde identiteit toegang krijgen tot het opslag account.

## <a name="use-the-managed-identity-of-the-media-services-account-to-access-key-vault-to-access-customer-keys"></a>De beheerde identiteit van het Media Services account gebruiken om toegang te krijgen tot Key Vault voor toegang tot klant sleutels

1. Maak een Media Services-account met een beheerde identiteit.
1. Verleen de beheerde identiteits-Principal toegang tot een Key Vault waarvan u de eigenaar bent.
1. Configureer het Media Services-account om de account versleuteling op basis van de klant te gebruiken.
1. Media Services met behulp van de beheerde identiteit Key Vault namens u toegang hebt.

Zie [uw eigen sleutel (door de klant beheerde sleutels) meenemen met Media Services](concept-use-customer-managed-keys-byok.md) voor meer informatie over door de klant beheerde sleutels en Key Vault.

## <a name="tutorials"></a>Zelfstudies

Deze zelf studies bevatten beide hierboven vermelde scenario's.

- [De Azure Portal gebruiken om door klant beheerde sleutels of BYOK met Media Services](tutorial-byok-portal.md)
- [Gebruik door de klant beheerde sleutels of BYOK met Media Services rest API](tutorial-byok-postman.md).

## <a name="next-steps"></a>Volgende stappen

Zie [Azure AD Managed Identities](../../active-directory/managed-identities-azure-resources/overview.md)(Engelstalig) voor meer informatie over welke beheerde identiteiten voor u en uw Azure-toepassingen kunnen worden uitgevoerd.
