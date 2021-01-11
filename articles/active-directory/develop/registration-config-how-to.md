---
title: De eind punten voor een Azure AD-App-registratie ophalen
titleSuffix: Microsoft identity platform
description: De verificatie-eind punten zoeken voor een aangepaste toepassing die u ontwikkelt of registreert met Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: ryanwi
ROBOTS: NOINDEX
ms.openlocfilehash: 3afaf654228511a357461a9e762be0b04acc65c6
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2021
ms.locfileid: "98064161"
---
# <a name="how-to-discover-endpoints"></a>Eind punten detecteren

U kunt de verificatie-eind punten voor uw toepassing vinden in de [Azure Portal](https://portal.azure.com).

1. Meld u aan bij <a href="https://portal.azure.com/" target="_blank">Azure Portal<span class="docon docon-navigate-external x-hidden-focus"></span></a>.
1. Selecteer **Azure Active Directory**.
1. Selecteer onder **beheren** de optie **app-registraties** en selecteer **eind punten** in het bovenste menu.

    De pagina **eind punten** wordt weer gegeven, met de verificatie-eind punten voor uw Tenant.
    
    Gebruik het eind punt dat overeenkomt met het verificatie protocol dat u gebruikt in combi natie met de **client-id** van de toepassing om de verificatie aanvraag te maken die specifiek is voor uw toepassing.

**Nationale Clouds** (bijvoorbeeld Azure AD China, Duitsland en de Amerikaanse overheid) hebben hun eigen app-registratie Portal en Azure AD-verificatie-eind punten. Meer informatie vindt u in het [overzicht van nationale Clouds](authentication-national-cloud.md).

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over eind punten in de verschillende Azure-omgevingen raadpleegt u het [overzicht van nationale Clouds](authentication-national-cloud.md).
