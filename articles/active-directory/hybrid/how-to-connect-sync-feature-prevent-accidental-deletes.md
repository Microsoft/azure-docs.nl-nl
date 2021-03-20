---
title: 'Azure AD Connect synchronisatie: onopzettelijke verwijderingen voor komen | Microsoft Docs'
description: In dit onderwerp wordt de functie onopzettelijke verwijderingen voor komen (onbedoeld verwijderen voor komen) in Azure AD Connect beschreven.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b852cb4-2850-40a1-8280-8724081601f7
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16d48cda87b8226ebc3bbab179c1034abf0a486f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90084606"
---
# <a name="azure-ad-connect-sync-prevent-accidental-deletes"></a>Azure AD Connect-synchronisatie: onopzettelijke verwijderingen voorkomen
In dit onderwerp wordt de functie onopzettelijke verwijderingen voor komen (onbedoeld verwijderen voor komen) in Azure AD Connect beschreven.

Wanneer u Azure AD Connect installeert, is onbedoeld verwijderen standaard ingeschakeld en zo geconfigureerd dat een export met meer dan 500 verwijderingen niet is toegestaan. Deze functie is ontworpen om u te beschermen tegen onbedoelde configuratiewijzigingen en wijzigingen aan uw on-premises directory die invloed zouden hebben op veel gebruikers en andere objecten.

## <a name="what-is-prevent-accidental-deletes"></a>Wat is onopzettelijke verwijderingen voor komen
Veelvoorkomende scenario's zijn onder meer:

* Wijzigingen in [filters](how-to-connect-sync-configure-filtering.md) waarbij een volledige [organisatie-eenheid](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) of [domein](how-to-connect-sync-configure-filtering.md#domain-based-filtering) niet is geselecteerd.
* alle objecten in een organisatie-eenheid worden verwijderd;
* de naam van een organisatie-eenheid is gewijzigd, zodat alle objecten in die eenheid als buiten het synchronisatiebereik worden beschouwd.

De standaard waarde van 500-objecten kan worden gewijzigd met Power shell met `Enable-ADSyncExportDeletionThreshold` , die deel uitmaakt van de AD Sync module die met Azure Active Directory Connect is geïnstalleerd. U moet deze waarde zodanig configureren dat deze overeenkomt met de grootte van uw organisatie. Omdat de synchronisatie planner elke 30 minuten wordt uitgevoerd, is de waarde het aantal verwijderingen dat binnen 30 minuten wordt weer gegeven.

Als er te veel verwijderde verwijderingen naar Azure AD worden uitgevoerd, wordt de export gestopt en ontvangt u een e-mail bericht als volgt:

![E-mail voor onopzettelijke verwijderingen voor komen](./media/how-to-connect-sync-feature-prevent-accidental-deletes/email.png)

> *Hallo (technische contact). De Identiteitssynchronisatie-service heeft gedetecteerd dat het aantal verwijderingen de geconfigureerde drempel waarde voor verwijdering voor (organisatie naam) heeft overschreden. Er is een totaal van (aantal) objecten verzonden om te verwijderen in deze Identiteitssynchronisatie worden uitgevoerd. Er is voldaan aan de geconfigureerde drempel waarde voor verwijdering van (aantal) objecten of deze is overschreden. U moet bevestigen dat deze verwijderingen moeten worden verwerkt voordat we verdergaan. Zie het voor komen van onopzettelijke verwijderingen voor meer informatie over de fout die wordt weer gegeven in dit e-mail bericht.*
>
> 

U kunt ook de status zien `stopped-deletion-threshold-exceeded` Wanneer u de **Synchronization Service Manager** gebruikers interface voor het export profiel bekijkt.
![Onopzettelijke verwijderingen van synchronisatie Service Manager gebruikers interface voor komen](./media/how-to-connect-sync-feature-prevent-accidental-deletes/syncservicemanager.png)

Als dit niet werd verwacht, onderzoekt en corrigeert u corrigerende maat regelen. Ga als volgt te werk om te zien welke objecten er worden verwijderd:

1. Start de **synchronisatie service** vanuit het menu Start.
2. Naar **connectors**.
3. Selecteer de connector met het type **Azure Active Directory**.
4. Selecteer onder **acties** aan de rechter kant de **ruimte Zoek connector**.
5. Selecteer in het pop-upvenster onder **Scope** de optie **verbinding verbroken sinds** en kies een tijd in het verleden. Klik op **Zoeken**. Deze pagina geeft een overzicht van alle objecten die moeten worden verwijderd. Als u op elk item klikt, kunt u aanvullende informatie over het object ophalen. U kunt ook op **kolom instelling** klikken om aanvullende kenmerken toe te voegen die zichtbaar zijn in het raster.

![Zoek connector ruimte](./media/how-to-connect-sync-feature-prevent-accidental-deletes/searchcs.png)

[!NOTE] Als u niet zeker weet of alle verwijderingen gewenst zijn, en u een veiliger route wilt door lopen. U kunt de Power shell-cmdlet gebruiken: `Enable-ADSyncExportDeletionThreshold` Als u een nieuwe drempel waarde wilt instellen in plaats van de drempel waarde uit te scha kelen, waardoor ongewenste verwijderingen kunnen worden toegestaan. 

## <a name="if-all-deletes-are-desired"></a>Als alle verwijderingen gewenst zijn
Als alle verwijderingen gewenst zijn, gaat u als volgt te werk:

1. Voer de Power shell-cmdlet uit om de huidige drempel waarde voor verwijderen op te halen `Get-ADSyncExportDeletionThreshold` . Geef een globaal beheerders account en wacht woord voor Azure AD op. De standaardwaarde is 500.
2. Als u deze beveiliging tijdelijk wilt uitschakelen en deze wilt verwijderen, voert u de Power shell-cmdlet uit: `Disable-ADSyncExportDeletionThreshold` . Geef een globaal beheerders account en wacht woord voor Azure AD op.
   ![Scherm afbeelding toont een dialoog venster voor het invoeren van de gebruikers naam en het wacht woord van de globale beheerder van Azure AD.](./media/how-to-connect-sync-feature-prevent-accidental-deletes/credentials.png)
3. Selecteer, terwijl de Azure Active Directory-Connector nog steeds is geselecteerd, de actie **uitvoeren** en selecteer **exporteren**.
4. Voer de Power shell-cmdlet uit om de beveiliging opnieuw in te scha kelen: `Enable-ADSyncExportDeletionThreshold -DeletionThreshold 500` . Vervang 500 door de waarde die u hebt opgevallen bij het ophalen van de huidige verwijderings drempel. Geef een globaal beheerders account en wacht woord voor Azure AD op.

## <a name="next-steps"></a>Volgende stappen
**Overzichts onderwerpen**

* [Azure AD Connect synchroniseren: Synchronisatie begrijpen en aanpassen](how-to-connect-sync-whatis.md)
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)
