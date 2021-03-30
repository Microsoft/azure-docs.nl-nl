---
title: De wizard Azure AD Connect installeren opnieuw uitvoeren | Microsoft Docs
description: Hierin wordt uitgelegd hoe de installatie wizard de tweede keer wordt uitgevoerd.
keywords: Met de wizard Azure AD Connect-installatie kunt u de onderhouds instellingen configureren voor de tweede keer dat u deze uitvoert
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: d800214e-e591-4297-b9b5-d0b1581cc36a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/17/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d81836b47acb19f624075480aafef74c9c0934c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91306120"
---
# <a name="azure-ad-connect-sync-running-the-installation-wizard-a-second-time"></a>Azure AD Connect synchronisatie: een tweede keer uitvoeren van de installatie wizard
De eerste keer dat u de Azure AD Connect-installatie wizard uitvoert, wordt u begeleid bij het configureren van de installatie. Als u de installatie wizard opnieuw uitvoert, biedt het opties voor onderhoud.

>[!IMPORTANT]
>Houd er rekening mee dat u de installatie wizard niet kunt uitvoeren terwijl er een synchronisatie wordt uitgevoerd.  Controleer of er geen synchronisatie wordt uitgevoerd voordat de wizard wordt gestart.

U kunt de installatie wizard vinden in het menu Start met de naam **Azure AD Connect**.

![Startmenu](./media/how-to-connect-installation-wizard/startmenu.png)

Wanneer u de installatie wizard start, ziet u een pagina met de volgende opties:

![Pagina met een lijst met aanvullende taken](./media/how-to-connect-installation-wizard/additionaltasks.png)

Als ADFS met Azure AD Connect is geïnstalleerd, hebt u nog meer opties. De extra opties die u voor ADFS hebt, worden beschreven in [ADFS-beheer](how-to-connect-fed-management.md#manage-ad-fs).

Selecteer een van de taken en klik op **volgende** om door te gaan.

> [!IMPORTANT]
> Terwijl u de installatie wizard hebt geopend, worden alle bewerkingen in de synchronisatie-engine onderbroken. Zorg ervoor dat u de installatie wizard sluit zodra u de configuratie wijzigingen hebt voltooid.
>
>

## <a name="view-current-configuration"></a>Huidige configuratie weer geven
Met deze optie krijgt u een snel overzicht van de momenteel geconfigureerde opties.

![Pagina met een lijst met alle opties en hun status](./media/how-to-connect-installation-wizard/viewconfig.png)

Klik op **vorige** om terug te gaan. Als u **Afsluiten** selecteert, sluit u de installatie wizard.

## <a name="customize-synchronization-options"></a>Synchronisatie opties aanpassen
Deze optie wordt gebruikt om wijzigingen aan te brengen in de synchronisatie configuratie. U ziet een subset met opties vanuit het installatiepad van de aangepaste configuratie. U ziet deze optie ook als u in eerste instantie snelle installatie hebt gebruikt.

* [Voeg meer Directory's toe](how-to-connect-install-custom.md#connect-your-directories). Zie [een connector verwijderen](how-to-connect-sync-service-manager-ui-connectors.md#delete)voor meer informatie over het verwijderen van een map.
* [Filteren van domein en OE wijzigen](how-to-connect-install-custom.md#domain-and-ou-filtering).
* Groeps filtering verwijderen.
* [Wijzig optionele functies](how-to-connect-install-custom.md#optional-features).

De andere opties van de eerste installatie kunnen niet worden gewijzigd en zijn niet beschikbaar. Deze opties zijn:

* Wijzig het kenmerk dat moet worden gebruikt voor userPrincipalName en source Anchor.
* Wijzig de methode voor samen voeging voor objecten uit een ander forest.
* Filteren op basis van een groep inschakelen.

## <a name="refresh-directory-schema"></a>Adreslijst schema vernieuwen
Deze optie wordt gebruikt als u het schema hebt gewijzigd in een van uw on-premises AD DS-forests. U hebt bijvoorbeeld Exchange of een upgrade naar een Windows Server 2012-schema met Device-objecten geïnstalleerd. In dit geval moet u Azure AD Connect het schema opnieuw te lezen vanaf AD DS en de cache ervan bij te werken. Met deze actie worden ook de synchronisatie regels opnieuw gegenereerd. Als u het Exchange-schema toevoegt, worden de synchronisatie regels voor Exchange toegevoegd aan de configuratie.

Wanneer u deze optie selecteert, worden alle mappen in uw configuratie weer gegeven. U kunt de standaard instelling blijven gebruiken en alle forests vernieuwen of de selectie ervan opheffen.

![Pagina met een lijst met alle directory's in de omgeving](./media/how-to-connect-installation-wizard/refreshschema.png)

## <a name="configure-staging-mode"></a>Faserings modus configureren
Met deze optie kunt u de faserings modus op de server inschakelen en uitschakelen. Meer informatie over de faserings modus en hoe deze wordt gebruikt, vindt u in [bewerkingen](how-to-connect-sync-staging-server.md).

De optie geeft aan of fase ring op dit moment is ingeschakeld of uitgeschakeld:  
![Scherm opname waarin de faserings modus wordt weer gegeven.](./media/how-to-connect-installation-wizard/stagingmodecurrentstate.png)

Als u de status wilt wijzigen, selecteert u deze optie en schakelt u het selectie vakje in of uit.  
![Optie die ook de huidige status van de faserings modus weergeeft](./media/how-to-connect-installation-wizard/stagingmodeenable.png)

## <a name="change-user-sign-in"></a>Gebruikers aanmelding wijzigen
Met deze optie kunt u de aanmeldings methode voor gebruikers wijzigen in en op basis van wacht woord-hash-synchronisatie, Pass Through-verificatie of Federatie. U kunt niet wijzigen in **niet configureren**.

Zie [gebruikers aanmelding](plan-connect-user-signin.md#changing-the-user-sign-in-method)voor meer informatie over deze optie.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het configuratie model dat wordt gebruikt door Azure AD Connect Sync in [inzicht in declaratieve inrichting](concept-azure-ad-connect-sync-declarative-provisioning.md).

**Overzichts onderwerpen**

* [Azure AD Connect synchroniseren: Synchronisatie begrijpen en aanpassen](how-to-connect-sync-whatis.md)
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)
