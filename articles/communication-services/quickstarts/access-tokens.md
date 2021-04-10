---
title: Snelstart - Toegangstoken maken en beheren
titleSuffix: An Azure Communication Services quickstart
description: Meer informatie over het beheren van identiteits-en toegangs tokens met behulp van de Azure Communication Services Identity SDK.
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: e356219d22ee558ce3de5a96d58f24b9e7902d8a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105726614"
---
# <a name="quickstart-create-and-manage-access-tokens"></a>Quickstart: Toegangstokens maken en beheren

Ga aan de slag met Azure Communication Services met behulp van de communicatie Services Identity SDK. Hiermee kunt u identiteiten maken en uw toegangstokens beheren. De identiteit geeft de entiteit weer van uw toepassing in de Azure Communication Service (bijvoorbeeld gebruiker of apparaat). Met toegangs tokens kunt u uw chat-en aanroepen van Sdk's rechtstreeks verifiëren voor Azure Communication Services. Het wordt aanbevolen om toegangstokens te genereren voor een service aan de serverzijde. Toegangs tokens worden vervolgens gebruikt voor het initialiseren van de communicatie Services-Sdk's op client apparaten.

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

Created an identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502

Issued an access token with 'voip' scope that expires at 30/03/21 08:09 09 AM:
<token signature here>

Created an identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-1ce9-31b4-54b7-a43a0d006a52

Issued an access token with 'voip' scope that expires at 30/03/21 08:09 09 AM:
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
> * De Communication Services-identiteits-SDK gebruiken


> [!div class="nextstepaction"]
> [Spraakoproep aan uw app toevoegen](./voice-video-calling/getting-started-with-calling.md)

U kunt ook het volgende doen:

 - [Meer informatie over verificatie](../concepts/authentication.md)
 - [Chat aan uw app toevoegen](./chat/get-started.md)
 - [Meer informatie over de client -en serverarchitectuur](../concepts/client-and-server-architecture.md)
