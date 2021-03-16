---
title: Snelstart - Toegangstoken maken en beheren
titleSuffix: An Azure Communication Services quickstart
description: Meer informatie over het beheren van identiteits-en toegangs tokens met behulp van de Azure Communication Services Identity client-bibliotheek.
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 921934e581d9b3d32cba644d85987ebb9802f73b
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103495298"
---
# <a name="quickstart-create-and-manage-access-tokens"></a>Quickstart: Toegangstokens maken en beheren

Ga aan de slag met Azure Communication Services met behulp van de client bibliotheek van de communicatie Services-identiteit. Hiermee kunt u identiteiten maken en uw toegangstokens beheren. De identiteit geeft de entiteit weer van uw toepassing in de Azure Communication Service (bijvoorbeeld gebruiker of apparaat). Met toegangstokens kunt u uw chat- en aanroep-clientbibliotheken rechtstreeks verifiëren tegen Azure Communication Services. Het wordt aanbevolen om toegangstokens te genereren voor een service aan de serverzijde. Toegangstokens worden vervolgens gebruikt voor het initialiseren van de Communication Services-clientbibliotheken op clientapparaten.

Prijzen die in de afbeeldingen in deze zelfstudie worden getoond, zijn alleen bedoeld voor demonstratiedoeleinden.

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/user-access-token-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/user-access-token-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/user-access-token-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/user-access-token-java.md)]
::: zone-end

De uitvoer van de app beschrijft elke actie die is voltooid:
<!---cSpell:disable --->
```console
Azure Communication Services - Access Tokens Quickstart

Created an identity: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502

Issued an access token with 'voip' scope that expires at Fri Nov 27 2020 16:47:05 GMT-0800 (Pacific Standard Time):
<token signature here>

Successfully revoked all access tokens for identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502

Deleted the identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502
```
<!---cSpell:enable --->

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Communication Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd. Meer informatie over het [opschonen van resources](./create-communication-resource.md#clean-up-resources).


## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u de volgende zaken geleerd:

> [!div class="checklist"]
> * Identiteiten beheren
> * Toegangstokens uitgeven
> * De client bibliotheek voor identiteit van communicatie Services gebruiken


> [!div class="nextstepaction"]
> [Spraakoproep aan uw app toevoegen](./voice-video-calling/getting-started-with-calling.md)

U kunt ook het volgende doen:

 - [Meer informatie over verificatie](../concepts/authentication.md)
 - [Chat aan uw app toevoegen](./chat/get-started.md)
 - [Meer informatie over de client -en serverarchitectuur](../concepts/client-and-server-architecture.md)
