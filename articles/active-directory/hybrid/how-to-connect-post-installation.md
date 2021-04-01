---
title: 'Azure AD Connect: volgende stappen en How to manage Azure AD Connect | Microsoft Docs'
description: Meer informatie over het uitbreiden van de standaard configuratie en operationele taken voor Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: c18bee36-aebf-4281-b8fc-3fe14116f1a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d6689b4b67b8462e983ae9b111e0fbc60c422b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89657111"
---
# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>Volgende stappen en het beheren van Azure AD Connect
Gebruik de operationele procedures in dit artikel om de verbinding van Azure Active Directory (Azure AD) aan te passen aan de behoeften en vereisten van uw organisatie.  

## <a name="add-additional-sync-admins"></a>Aanvullende synchronisatie-Administrators toevoegen
Standaard kan alleen de gebruiker die de installatie heeft uitgevoerd en lokale beheerders de geïnstalleerde synchronisatie-engine beheren. Ga naar de groep met de naam ADSyncAdmins op de lokale server en voeg deze toe aan deze groep voor meer personen die de synchronisatie-engine kunnen openen en beheren.

## <a name="assign-licenses-to-azure-ad-premium-and-enterprise-mobility-suite-users"></a>Licenties toewijzen aan gebruikers van Azure AD Premium en Enter prise Mobility Suite
Nu uw gebruikers zijn gesynchroniseerd met de Cloud, moet u ze een licentie toewijzen zodat ze kunnen gaan werken met Cloud-apps, zoals Microsoft 365.

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>Een licentie voor een Azure AD Premium of ENTER prise Mobility Suite toewijzen

1. Meld u aan bij de Azure Portal als beheerder.
2. Selecteer aan de linkerkant **Active Directory**.
3. Dubbel klik op de pagina **Active Directory** op de map met de gebruikers die u wilt instellen.
4. Selecteer boven aan de pagina met de adreslijst **Licenties**.
5. Selecteer op de pagina **licenties** **Active Directory Premium** -of **Enter prise Mobility Suite** en klik vervolgens op **toewijzen**.
6. Selecteer in het dialoogvenster de gebruikers aan wie u een licentie wilt toewijzen en klik op het vinkje om de wijzigingen op te slaan.

## <a name="verify-the-scheduled-synchronization-task"></a>De geplande synchronisatie taak controleren
Gebruik de Azure Portal om de status van een synchronisatie te controleren.

### <a name="to-verify-the-scheduled-synchronization-task"></a>De geplande synchronisatie taak controleren
1. Meld u aan bij de Azure Portal als beheerder.
2. Selecteer aan de linkerkant **Active Directory**.
3. Selecteer aan de linkerkant **Azure AD Connect**
4. Noteer de laatste synchronisatie aan de bovenkant van de pagina.

![Tijd van Directory synchronisatie](./media/how-to-connect-post-installation/verify2.png)

## <a name="start-a-scheduled-synchronization-task"></a>Een geplande synchronisatie taak starten
Als u een synchronisatie taak moet uitvoeren, kunt u dit als volgt doen:

1. Dubbel klik op de snelkoppeling Azure AD Connect bureau blad om de wizard te starten.
2. Klik op **Configureren**
3. Selecteer op het scherm taken de opties voor het aanpassen van de **synchronisatie** en klik op **volgende**
4. Voer uw Azure AD-referenties in
5. Klik op **Volgende**. Klik op **Volgende**.  Klik op **Volgende**.
5.  Controleer op het scherm **gereed voor configureren** of het selectie vakje **het synchronisatie proces starten wanneer de configuratie is voltooid** is geselecteerd.
6.  Klik op **Configureren**

Zie [Azure AD Connect scheduler](how-to-connect-sync-feature-scheduler.md)voor meer informatie over de Azure AD Connect synchronisatie planner.

## <a name="additional-tasks-available-in-azure-ad-connect"></a>Aanvullende taken die beschikbaar zijn in Azure AD Connect
Na de eerste installatie van Azure AD Connect kunt u de wizard altijd opnieuw starten vanaf de Azure AD Connect start pagina of snelkoppeling op het bureau blad.  U ziet dat de wizard opnieuw een aantal nieuwe opties bevat in de vorm van aanvullende taken.  

De volgende tabel bevat een samen vatting van deze taken en een korte beschrijving van elke taak.

![Lijst met aanvullende taken](./media/how-to-connect-post-installation/addtasks2.png)

| Aanvullende taak | Description |
| --- | --- |
|**Privacyinstellingen**|Weer geven welke telemetriegegevens worden gedeeld met micro soft.|
|**Huidige configuratie weer geven**|Bekijk uw huidige Azure AD Connect-oplossing.  Dit omvat algemene instellingen, gesynchroniseerde directory's en synchronisatie-instellingen. |
| **Synchronisatie opties aanpassen** |Wijzig de huidige configuratie, zoals het toevoegen van extra Active Directory forests aan de configuratie, of het inschakelen van synchronisatie opties zoals gebruiker, groep, apparaat of wacht woord terugschrijven. |
|**Opties voor apparaten configureren**|Beschik bare apparaatinstellingen voor synchronisatie|
|**Adreslijst schema vernieuwen**|Hiermee kunt u nieuwe on-premises Directory-objecten voor synchronisatie toevoegen|
|**Faserings modus configureren** |Fase-informatie die niet onmiddellijk wordt gesynchroniseerd en niet wordt geëxporteerd naar Azure AD of on-premises Active Directory.  Met deze functie kunt u een voor beeld van de synchronisaties bekijken voordat deze zich voordoen. |
|**Gebruikers aanmelding wijzigen**|De verificatie methode wijzigen die gebruikers gebruiken om zich aan te melden|
|**Federatie beheren**|Uw AD FS-infra structuur beheren, certificaten vernieuwen en AD FS servers toevoegen|
|**Problemen oplossen**|Hulp bij het oplossen van problemen met Azure AD Connect|

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [het integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
