---
title: Codevoorbeelden voor de API-connector voor gebruikersstromen - Azure AD
description: Codevoorbeelden voor API-connectors in stromen voor aanmelding via zelfservice voor Azure Active Directory External Identities.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: sample
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3995395cfd6256689bedc7a4a3c83effc65c0b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87906862"
---
# <a name="samples-for-external-identities-self-service-sign-up"></a>Voorbeelden van aanmelding via zelfservice voor External Identities

De volgende tabellen bieden koppelingen naar codevoorbeelden voor het gebruik van web-API’s in uw gebruikersstroom voor aanmelding via zelfservice met behulp van [API-connectors](api-connectors-overview.md).

## <a name="api-connector-azure-function-quickstarts"></a>Quickstarts voor API-connectors in Azure Function

| Voorbeeld                                                                                                                          | Beschrijving                                                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connector-azure-function-validate) | Dit .NET Core Azure Function-voorbeeld laat zien hoe u aanmeldingen kunt beperken tot specifieke tenantdomeinen en door de gebruiker verstrekte informatie kunt valideren. |
| [Node.js](https://github.com/Azure-Samples/active-directory-nodejs-external-identities-api-connector-azure-function-validate)   | Dit Node.js Azure Function-voorbeeld laat zien hoe u aanmeldingen kunt beperken tot specifieke tenantdomeinen en door de gebruiker verstrekte informatie kunt valideren.  |
| [Python](https://github.com/Azure-Samples/active-directory-python-external-identities-api-connector-azure-function-validate)    | Dit Python Azure Function-voorbeeld laat zien hoe u aanmeldingen kunt beperken tot specifieke tenantdomeinen en door de gebruiker verstrekte informatie kunt valideren.    |

<!-- \| [Java](../../azure-docs-pr/articles/active-directory/b2b/invite-internal-users.md#use-the-invitation-api-to-send-a-b2b-invitation) |  The sample below illustrates how to call the invitation API to invite an internal user as a B2B user. | -->

## <a name="custom-approval-workflows"></a>Aangepaste goedkeuringswerkstromen

| Voorbeeld | Beschrijving |
|--------| ----------- |
| [Werkstroom voor handmatig goedkeuring](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connectors-approvals) | In dit voorbeeld ziet u een end-to-end-goedkeuringswerkstroom voor het beheren van het maken van gastgebruikersaccounts in aanmelding via zelfservice |

## <a name="identity-verification"></a>Identiteitverificatie

| Voorbeeld                                                                                                            | Beschrijving                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [IDology](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-idology-identity-verification) | In dit voorbeeld ziet u hoe u een gebruikersidentiteit kunt verifiëren als onderdeel van uw aanmelding via zelfservice, door een API-connector te gebruiken om te integreren met IDology. |
| [Experian](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-experian-identity-verification) | In dit voorbeeld ziet u hoe u een gebruikersidentiteit kunt verifiëren als onderdeel van uw aanmelding via zelfservice, door een API-connector te gebruiken om te integreren met Experian. |
