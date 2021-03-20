---
title: 'Azure AD Connect synchronisatie: het wacht woord voor het AD DS account wijzigen | Microsoft Docs'
description: In dit document wordt beschreven hoe u Azure AD Connect bijwerkt nadat het wacht woord van het AD DS account is gewijzigd.
services: active-directory
keywords: AD DS account, Active Directory account, wacht woord
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4077146292db1266d5dbc51cc577f952b2bff191
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85357508"
---
# <a name="changing-the-ad-ds-account-password"></a>Het wachtwoord voor het AD DS-account wijzigen
Het AD DS-account verwijst naar het gebruikers account dat door Azure AD Connect wordt gebruikt om te communiceren met on-premises Active Directory. Als u het wacht woord van het AD DS account wijzigt, moet u Azure AD Connect synchronisatie service bijwerken met het nieuwe wacht woord. Als dat niet het geval is, kan de synchronisatie niet meer correct worden gesynchroniseerd met de on-premises Active Directory en worden de volgende fouten optreden:

* In de Synchronization Service Manager wordt een import-of export bewerking met on-premises AD mislukt zonder **Start-referenties-** fout.

* Onder Windows Logboeken bevat het gebeurtenis logboek van de toepassing een fout met **gebeurtenis-ID 6000** en bericht **' de beheer agent ' contoso.com ' kan niet worden uitgevoerd omdat de referenties ongeldig zijn '**.


## <a name="how-to-update-the-synchronization-service-with-new-password-for-ad-ds-account"></a>De synchronisatie service bijwerken met het nieuwe wacht woord voor AD DS account
De synchronisatie service bijwerken met het nieuwe wacht woord:

1. Start de Synchronization Service Manager (START → synchronisatie service).
</br>![Service Manager synchroniseren](./media/how-to-connect-sync-change-addsacct-pass/startmenu.png)  

2. Ga naar het tabblad **connectors** .

3. Selecteer de **ad-connector** die overeenkomt met het AD DS account waarvoor het wacht woord is gewijzigd.

4. Onder **acties**, selecteert u **Eigenschappen**.

5. Selecteer in het pop-updialoogvenster **verbinding maken met Active Directory forest**:

6. Voer het nieuwe wacht woord van het AD DS account in het tekstvak **wacht woord** in.

7. Klik op **OK** om het nieuwe wacht woord op te slaan en het pop-updialoogvenster te sluiten.

8. Start de Azure AD Connect-synchronisatie service opnieuw onder Windows-service besturings beheer. Zo zorgt u ervoor dat de verwijzingen naar het oude wacht woord worden verwijderd uit de geheugen cache.

## <a name="next-steps"></a>Volgende stappen
**Overzichts onderwerpen**

* [Azure AD Connect synchroniseren: Synchronisatie begrijpen en aanpassen](how-to-connect-sync-whatis.md)

* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)
