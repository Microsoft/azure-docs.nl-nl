---
title: Onverwachte toestemming vragen bij het aanmelden bij een toepassing | Microsoft Docs
description: Problemen oplossen wanneer een gebruiker een toestemming prompt krijgt voor een toepassing die u hebt geïntegreerd met Azure AD en die u niet verwacht
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6253458bb31388e5f7e6f84b7720cce0dc22886c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99259957"
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>Onverwachte toestemming vragen bij het aanmelden bij een toepassing

Veel toepassingen die met Azure Active Directory worden geïntegreerd, hebben machtigingen voor verschillende resources nodig om te kunnen worden uitgevoerd. Wanneer deze resources ook zijn geïntegreerd met Azure Active Directory, wordt er met behulp van het Azure AD-instemming raamwerk toestemming gevraagd om ze te openen. 

Dit resulteert in een toestemming prompt die de eerste keer wordt gebruikt om een toepassing te gebruiken. Dit is vaak een eenmalige bewerking. 

## <a name="scenarios-in-which-users-see-consent-prompts"></a>Scenario's waarin gebruikers toestemming geven voor het goed keuren

Er kunnen in verschillende scenario's extra prompts worden verwacht:

* De set met machtigingen die vereist zijn voor de toepassing is gewijzigd.

* De gebruiker die oorspronkelijk heeft ingestemd met de toepassing, is geen beheerder en nu gebruikt een andere gebruiker (niet-beheerder) de toepassing voor de eerste keer.

* De gebruiker die oorspronkelijk heeft ingestemd met de toepassing, was een beheerder, maar heeft geen toestemming verleend namens de hele organisatie.

* De toepassing gebruikt [incrementele en dynamische toestemming](../azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) om aanvullende machtigingen aan te vragen nadat de toestemming voor het eerst is toegekend. Dit wordt vaak gebruikt wanneer optionele functies van een toepassing extra machtigingen vereisen dan vereisten die vereist zijn voor de basislijn functionaliteit.

* De toestemming is ingetrokken nadat deze in eerste instantie is toegekend.

* De ontwikkelaar heeft de toepassing zo geconfigureerd dat elke keer dat deze wordt gebruikt een toestemming wordt gevraagd (Let op: dit is niet best practice).

## <a name="next-steps"></a>Volgende stappen

-   [Apps, machtigingen en toestemming in Azure Active Directory (v 1.0-eind punt)](../develop/quickstart-register-app.md)

-   [Bereiken, machtigingen en toestemming in de Azure Active Directory (v 2.0-eind punt)](../develop/v2-permissions-and-consent.md)