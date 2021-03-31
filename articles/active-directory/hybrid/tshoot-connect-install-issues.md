---
title: Problemen met Azure AD Connect installatie oplossen | Microsoft Docs '
description: Dit onderwerp bevat stappen voor het oplossen van problemen met het installeren van Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25314b4a306678dc877a95194907b3d73979e4f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89275858"
---
# <a name="troubleshoot-azure-ad-connect-install-issues"></a>Problemen oplossen: problemen met Azure AD Connect installeren

## <a name="recommended-steps"></a>**Aanbevolen stappen**
Controleer welk [Azure AD Connect installatie type](./how-to-connect-install-select-installation.md) geschikt is voor u. Als u voldoet aan de criteria van snelle installatie, raden we u ten zeerste aan om met de snelle installatie te gaan. De snelle installatie biedt minimale opties die nodig zijn om de installatie te volt ooien, waardoor er minder kans is op problemen. 

Als u echter niet voldoet aan de criteria voor de snelle installatie en u de aangepaste installatie moet uitvoeren, zijn hier enkele aanbevolen procedures die u kunt volgen om veelvoorkomende problemen te voor komen. In het geval van eenvoud worden hier alleen selectieve opties vermeld:

* Zorg ervoor dat u een beheerder bent van de computer waarop u AAD Connect installeert. Meld u aan bij de computer met dezelfde beheerders referenties.

* Laat alle opties standaard op de volgende pagina staan, met uitzonde ring van ' een bestaande SQL Server gebruiken ', als u bestaande SQL Server wilt gebruiken. Hier vindt u [meer informatie](./how-to-connect-install-custom.md) over het gebruik van aangepaste installatie opties. 

    ![Bestaande SQL Server gebruiken](media/tshoot-connect-install-issues/tshoot-connect-install-issues/useexistingsqlserver.png)

* Kies op de volgende pagina de optie nieuwe AD-account maken om te voor komen dat er machtigings problemen met een bestaand account worden gemaakt.

    ![AD-forest-account](media/tshoot-connect-install-issues/tshoot-connect-install-issues/createnewaccount.png)

### <a name="common-issues"></a>**Algemene problemen**

* [Connectiviteits problemen met on-premises Active Directory](./reference-connect-adconnectivitytools.md).

* [Connectiviteits problemen met online Azure Active Directory](./tshoot-connect-connectivity.md).

* [Machtigings problemen met on-premises Active Directory](./how-to-connect-configure-ad-ds-connector-account.md).

## <a name="recommended-documents"></a>**Aanbevolen documenten**
* [Vereisten voor Azure AD Connect](./how-to-connect-install-prerequisites.md)
* [Het installatietype voor Azure AD Connect selecteren](./how-to-connect-install-select-installation.md)
* [Aan de slag met Azure AD Connect met expresinstellingen](./how-to-connect-install-express.md)
* [Aangepaste installatie van Azure AD Connect](./how-to-connect-install-custom.md)
* [Azure AD Connect: upgraden van een vorige naar de meest recente versie](./how-to-upgrade-previous-version.md)
* [Azure AD Connect: wat is een staging-server?](./plan-connect-topologies.md#staging-server)
* [Wat is de PowerShell-module ADConnectivityTool?](./how-to-connect-adconnectivitytools.md)

## <a name="next-steps"></a>Volgende stappen
- [Azure AD Connect synchronisatie](how-to-connect-sync-whatis.md).
- [Wat is hybride identiteit?](whatis-hybrid-identity.md)