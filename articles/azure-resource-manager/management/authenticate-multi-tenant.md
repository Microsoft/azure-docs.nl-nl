---
title: Verifiëren in meerdere tenants
description: Hierin wordt beschreven hoe Azure Resource Manager verificatie aanvragen afhandelt tussen tenants.
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 7a13ba6f6cbfc10c52484c45e4011da7a0d8ee4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "75478823"
---
# <a name="authenticate-requests-across-tenants"></a>Aanvragen verifiëren via tenants

Wanneer u een multi tenant toepassing maakt, moet u mogelijk verificatie aanvragen afhandelen voor bronnen die zich in verschillende tenants bevinden. Een veelvoorkomend scenario is wanneer een virtuele machine in een Tenant moet worden toegevoegd aan een virtueel netwerk in een andere Tenant. Azure Resource Manager biedt een header waarde voor het opslaan van de hulp tokens voor het verifiëren van de aanvragen voor verschillende tenants.

## <a name="header-values-for-authentication"></a>Header waarden voor verificatie

De aanvraag heeft de volgende waarden voor de verificatie-header:

| Headernaam | Beschrijving | Voorbeeldwaarde |
| ----------- | ----------- | ------------ |
| Autorisatie | Primair token | Primaire token van Bearer &lt;&gt; |
| x-MS-autorisatie-hulp | Hulp tokens | Bearer &lt; -token1 &gt; , EncryptedBearer &lt; hulp token2 &gt; , Bearer &lt; -token3&gt; |

De hulp header kan Maxi maal drie hulp tokens bevatten. 

In de code van uw app met meerdere tenants haalt u het verificatie token voor andere tenants op en slaat u deze op in de hulp headers. Alle tokens moeten afkomstig zijn uit dezelfde gebruiker of toepassing. De gebruiker of toepassing moet als gast aan de andere tenants worden uitgenodigd.

## <a name="processing-the-request"></a>De aanvraag wordt verwerkt

Wanneer uw app een aanvraag naar Resource Manager verzendt, wordt de aanvraag uitgevoerd onder de identiteit van het primaire token. Het primaire token moet geldig zijn en niet verlopen. Dit token moet afkomstig zijn van een Tenant die het abonnement kan beheren.

Wanneer de aanvraag verwijst naar een bron van een andere Tenant, controleert Resource Manager de hulp tokens om te bepalen of de aanvraag kan worden verwerkt. Alle hulp tokens in de header moeten geldig en niet-verlopen zijn. Als een token is verlopen, retourneert Resource Manager een 401-respons code. Het antwoord bevat de client-ID en Tenant-ID van het token dat niet geldig is. Als de hulp header een geldig token voor de Tenant bevat, wordt de aanvraag voor cross tenants verwerkt.

## <a name="next-steps"></a>Volgende stappen

* Zie [verificatie stromen en toepassings scenario's](../../active-directory/develop/authentication-flows-app-scenarios.md)voor meer informatie over verificatie aanvragen.
* Zie [Azure Active Directory toegangs tokens](../../active-directory/develop/access-tokens.md)voor meer informatie over tokens.
