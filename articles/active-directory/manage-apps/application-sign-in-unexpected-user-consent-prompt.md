---
title: Onverwachte toestemmingsprompt bij het aanmelden bij een toepassing | Microsoft Docs
description: Problemen oplossen wanneer een gebruiker een toestemmingsprompt ziet voor een toepassing die u hebt geïntegreerd met Azure AD die u niet had verwacht
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: iangithinji
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37e4384b5a1b400f11b7b7d6ab15beec4d2f8de9
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378054"
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>Onverwachte toestemmingsprompt bij het aanmelden bij een toepassing

Veel toepassingen die zijn geïntegreerd met Azure Active Directory hebben machtigingen voor verschillende resources nodig om te kunnen worden uitgevoerd. Wanneer deze resources ook zijn geïntegreerd met Azure Active Directory, worden machtigingen voor toegang aangevraagd met behulp van het Azure AD-toestemmingsraamwerk. 

Dit leidt ertoe dat de eerste keer dat een toepassing wordt gebruikt, een toestemmingsprompt wordt weergegeven. Dit is vaak een een-time bewerking. 

> [!VIDEO https://www.youtube.com/embed/a1AjdvNDda4]

## <a name="scenarios-in-which-users-see-consent-prompts"></a>Scenario's waarin gebruikers toestemmingsprompts zien

Aanvullende prompts kunnen in verschillende scenario's worden verwacht:

* De set machtigingen die vereist is voor de toepassing is gewijzigd.

* De gebruiker die oorspronkelijk toestemming heeft gegeven voor de toepassing, was geen beheerder en nu gebruikt een andere (niet-beheerder) gebruiker de toepassing voor het eerst.

* De gebruiker die oorspronkelijk toestemming heeft gegeven voor de toepassing, was een beheerder, maar niet namens de hele organisatie.

* De toepassing gebruikt [incrementele en dynamische toestemming om](../azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) aanvullende machtigingen aan te vragen nadat de toestemming voor het eerst is verleend. Dit wordt vaak gebruikt wanneer voor optionele functies van een toepassing extra machtigingen nodig zijn die niet vereist zijn voor basislijnfunctionaliteit.

* De toestemming is ingetrokken nadat deze in eerste instantie is verleend.

* De ontwikkelaar heeft de toepassing geconfigureerd om telkens wanneer deze wordt gebruikt een toestemmingsprompt te vereisen (opmerking: dit is niet best practice).

## <a name="next-steps"></a>Volgende stappen

-   [Apps, machtigingen en toestemming in Azure Active Directory (v1.0-eindpunt)](../develop/quickstart-register-app.md)

-   [Scopes, machtigingen en toestemming in het Azure Active Directory (v2.0-eindpunt)](../develop/v2-permissions-and-consent.md)
