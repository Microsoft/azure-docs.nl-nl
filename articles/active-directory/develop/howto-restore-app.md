---
title: 'Procedure: een onlangs verwijderde toepassing herstellen of verwijderen met het micro soft-identiteits platform | Azure'
titleSuffix: Microsoft identity platform
description: In deze procedure leert u hoe u een onlangs verwijderde toepassing die is geregistreerd bij het micro soft-identiteits platform kunt herstellen of permanent verwijderen.
services: active-directory
author: arcrowe
manager: dastrock
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 3/22/2021
ms.author: arcrowe
ms.custom: aaddev
ms.openlocfilehash: dbe3e16bf56c5480fbe4aff8dad78bbbb5591f67
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080211"
---
# <a name="restore-or-remove-a-recently-deleted-application-with-the-microsoft-identity-platform"></a>Een onlangs verwijderde toepassing herstellen of verwijderen met het micro soft Identity-platform
Nadat u een app-registratie hebt verwijderd, blijft de app gedurende 30 dagen in een onderbroken status. Tijdens deze periode van 30 dagen kan de registratie van de app worden hersteld, samen met alle eigenschappen. Nadat deze periode van 30 dagen is verstreken, kunnen app-registraties niet worden hersteld en kan het permanente verwijderings proces automatisch worden gestart.  Deze functionaliteit is alleen van toepassing op toepassingen die zijn gekoppeld aan een map.  Het is niet beschikbaar voor toepassingen van een persoonlijke Microsoft-account. deze kan niet worden hersteld.

U kunt uw verwijderde toepassingen weer geven, een verwijderde toepassing herstellen of een toepassing definitief verwijderen met de App-registraties-ervaring onder Azure Active Directory (Azure AD) in de Azure Portal.

Houd er rekening mee dat noch micro soft Customer Support een permanent verwijderde toepassing of een toepassing die meer dan 30 dagen geleden is verwijderd, kan herstellen.

> [!IMPORTANT]
> De UI-functie Verwijderde toepassingen Portal [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

## <a name="required-permissions"></a>Vereiste machtigingen
U moet een van de volgende rollen hebben om toepassingen permanent te kunnen verwijderen.

- Globale beheerder
- Toepassings beheerder
- Beheerder van de Cloud toepassing
- Hybride identiteits beheerder
- Eigenaar van de toepassing

U moet een van de volgende rollen hebben om toepassingen te herstellen.

- Globale beheerder
- Eigenaar van de toepassing

### <a name="view-your-deleted-applications"></a>Uw verwijderde toepassingen weer geven
U kunt alle toepassingen met een voorlopig verwijderde status zien.  Alleen toepassingen die minder dan 30 dagen geleden zijn verwijderd, kunnen worden hersteld.

#### <a name="to-view-your-restorable-applications"></a>Uw herstorable toepassingen weer geven
1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Zoek en selecteer **Azure Active Directory**, selecteer **app-registraties** en selecteer vervolgens het tabblad **Verwijderde toepassingen (preview)** .

Bekijk de lijst met toepassingen. Alleen toepassingen die in de afgelopen 30 dagen zijn verwijderd, zijn beschikbaar om te herstellen. Als u de App-registraties Zoek voorbeeld gebruikt, kunt u filteren op de kolom verwijderde datum om alleen deze toepassingen weer te geven.

## <a name="restore-a-recently-deleted-application"></a>Een onlangs verwijderde toepassing herstellen

Wanneer een app-registratie uit de organisatie wordt verwijderd, bevindt de app zich in een onderbroken status en blijven de configuraties behouden. Wanneer u een app-registratie herstelt, worden de bijbehorende configuraties ook hersteld.  Als er echter organisatie-specifieke instellingen zijn in **bedrijfs toepassingen** voor de thuis Tenant van de toepassing, zullen deze niet worden hersteld.  

Dit komt doordat organisatie-specifieke instellingen worden opgeslagen in een afzonderlijk object, de Service-Principal genoemd.  Instellingen voor de Service-Principal bevatten machtigingen voor toestemmingen en gebruikers-en groeps toewijzingen voor een bepaalde organisatie; Deze configuraties worden niet hersteld wanneer de app wordt hersteld. Zie [Application and Service Principal Objects](app-objects-and-service-principals.md)(Engelstalig) voor meer informatie. 


### <a name="to-restore-an-application"></a>Een toepassing herstellen
1. Op het tabblad **Verwijderde toepassingen (preview)** zoekt en selecteert u een van de toepassingen die minder dan 30 dagen geleden zijn verwijderd.
2. Selecteer **app-registratie herstellen**.

## <a name="permanently-delete-an-application"></a>Een toepassing definitief verwijderen
U kunt een toepassing hand matig permanent verwijderen uit uw organisatie. Een permanent verwijderde toepassing kan niet worden hersteld door u, een andere beheerder of door de klant ondersteuning van micro soft.

### <a name="to-permanently-delete-an-application"></a>Een toepassing permanent verwijderen

1. Zoek en selecteer een van de beschik bare toepassingen op het tabblad **Verwijderde toepassingen (preview)** .
2. Selecteer **permanent verwijderen**.
3. Lees de tekst van de waarschuwing en selecteer **Ja**.

## <a name="next-steps"></a>Volgende stappen
Nadat u uw app hebt hersteld of definitief hebt verwijderd, kunt u het volgende doen:

- [Een toepassing toevoegen](quickstart-register-app.md).
- Meer informatie over [toepassings- en service-principalobjecten](app-objects-and-service-principals.md) in Microsoft Identity Platform.
