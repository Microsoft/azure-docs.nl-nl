---
title: 'Azure AD Connect: service-exemplaren synchroniseren | Microsoft Docs'
description: Op deze pagina worden speciale aandachtspunten voor Azure AD-exemplaren beschreven.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: f340ea11-8ff5-4ae6-b09d-e939c76355a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c4d8b0a33763a967550453d8a205258f7583084
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90015259"
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect: speciale overwegingen voor exemplaren
Azure AD Connect wordt meestal gebruikt in combi natie met de wereld wijde instantie van Azure AD en Microsoft 365. Maar er zijn ook andere instanties en deze hebben verschillende vereisten voor Url's en andere speciale overwegingen.

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud Duitsland
De [Microsoft Cloud Duitsland](https://www.microsoft.de/cloud-deutschland) is een soevereine Cloud die wordt beheerd door een Duitse gegevens beheerder.

| Url's om te openen in proxy server |
| --- |
| \*. microsoftonline.de |
| \*.windows.net |
| + Certificaat intrekkings lijsten |

Wanneer u zich aanmeldt bij uw Azure AD-Tenant, moet u een account gebruiken in het domein onmicrosoft.de.

Functies die momenteel niet aanwezig zijn in de Microsoft Cloud Duitsland:

* **Wacht woord terugschrijven** is beschikbaar voor preview met Azure AD Connect versie 1.1.570.0 en later.
* Andere Azure AD Premium Services zijn niet beschikbaar.

## <a name="microsoft-azure-government"></a>Microsoft Azure Government
De [Microsoft Azure Government Cloud](https://azure.microsoft.com/features/gov/) is een Cloud voor de Amerikaanse overheid.

Deze Cloud wordt ondersteund door eerdere versies van DirSync. Van build 1.1.180 van Azure AD Connect wordt de volgende generatie van de Cloud ondersteund. Deze generatie maakt gebruik van alleen Amerikaanse eind punten en heeft een andere lijst met Url's om te openen in de proxy server.

| Url's om te openen in proxy server |
| --- |
| \*.microsoftonline.com |
| \*.microsoftonline.us |
| \*. windows.net (vereist voor automatische Azure Government Tenant detectie) |
| \*. gov.us.microsoftonline.com |
| + Certificaat intrekkings lijsten |

> [!NOTE]
> Vanaf Azure AD Connect versie 1.1.647.0 is het instellen van de AzureInstance-waarde in het REGI ster niet langer vereist, op voor waarde dat *. windows.net is geopend op uw proxy server (s). Voor klanten die geen verbinding met internet met hun Azure AD Connect server (s) toestaan, kan de volgende hand matige configuratie echter worden gebruikt.

### <a name="manual-configuration"></a>Hand matige configuratie

De volgende hand matige configuratie stappen worden gebruikt om ervoor te zorgen dat Azure AD Connect Azure Government synchronisatie-eind punten gebruikt.

1. Start de Azure AD Connect-installatie.
2. Wanneer u de eerste pagina ziet waar u de gebruiksrecht overeenkomst moet accepteren, moet u de installatie wizard niet voortzetten.
3. Start regedit en wijzig de register sleutel `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` in de waarde `4` .
4. Ga terug naar de installatie wizard van Azure AD Connect, accepteer de gebruiksrecht overeenkomst en ga door. Zorg er tijdens de installatie voor dat u het **aangepaste configuratie** -installatiepad (en niet de snelle installatie) gebruikt en de installatie op de gebruikelijke manier door te voeren.

## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
