---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: 2ff600429cefbfe0c9a4f0522ee49274000029ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98760668"
---
Dit artikel bevat een overzicht van de verschillende soorten fouten en aanbevelingen voor het verwerken van veelvoorkomende aanmeldings fouten.

## <a name="msal-error-handling-basics"></a>Basis beginselen van MSAL-fout afhandeling

Uitzonde ringen in micro soft Authentication Library (MSAL) zijn bedoeld voor app-ontwikkel aars om problemen op te lossen, niet voor het weer geven van eind gebruikers. Uitzonderings berichten zijn niet gelokaliseerd.

Bij het verwerken van uitzonde ringen en fouten kunt u het uitzonderings type zelf en de fout code gebruiken om onderscheid te maken tussen uitzonde ringen.  Zie [Azure AD-verificatie en fout codes voor autorisatie](../articles/active-directory/develop/reference-aadsts-error-codes.md)voor een lijst met fout codes.

Tijdens de aanmeldings procedure kunnen er fouten optreden met betrekking tot toestemmingen, voorwaardelijke toegang (MFA, Apparaatbeheer, op locatie gebaseerde beperkingen), het uitgeven en inwisselen van tokens en gebruikers eigenschappen.

In de volgende sectie vindt u meer informatie over het afhandelen van fouten voor uw app.