---
title: Azure AD Connect en privacy van de gebruiker | Microsoft Docs
description: In dit document wordt beschreven hoe u AVG nalevings kunt verkrijgen met Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f1d1bf35d03f0f7c4d11e4cc7e9b6f98f38d8ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95997695"
---
# <a name="user-privacy-and-azure-ad-connect"></a>Privacy en Azure AD Connect van gebruiker 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>In dit artikel worden Azure AD Connect en privacy van gebruikers besproken.  Zie het artikel [hier](reference-connect-health-user-privacy.md)voor meer informatie over Azure AD Connect Health en privacy van de gebruiker.

Verbeter de privacy van gebruikers voor Azure AD Connect installaties op twee manieren:

1.  Op aanvraag gegevens voor een persoon extra heren en gegevens van die persoon verwijderen uit de installaties
2.  Zorg ervoor dat er geen gegevens meer dan 48 uur worden bewaard.

Het Azure AD Connect team raadt de tweede optie aan omdat het veel gemakkelijker is om te implementeren en onderhouden.

Een Azure AD Connect-synchronisatie server slaat de volgende privacygegevens van de gebruiker op:
1.  Gegevens over een persoon in de **Azure AD Connect-Data Base**
2.  Gegevens in de **Windows-gebeurtenis logboek** bestanden die mogelijk informatie over een persoon bevatten
3.  Gegevens in de **logboek bestanden** van de Azure AD Connect-installatie die een persoon kunnen bevatten

Azure AD Connect klanten moeten de volgende richt lijnen gebruiken bij het verwijderen van gebruikers gegevens:
1.  Verwijder de inhoud van de map die de Azure AD Connect installatie logboek bestanden regel matig bevat, ten minste elke 48 uur
2.  Dit product kan ook gebeurtenis logboeken maken.  Raadpleeg de [documentatie](/windows/win32/wes/windows-event-log)voor meer informatie over Logboeken voor gebeurtenis Logboeken.

Gegevens over een persoon worden automatisch uit de Azure AD Connect-Data Base verwijderd wanneer de gegevens van die persoon worden verwijderd uit het bron systeem waarvan ze afkomstig zijn. Een specifieke actie van beheerders moet AVG compatibel zijn.  Het is echter wel vereist dat de Azure AD Connect gegevens ten minste elke twee dagen worden gesynchroniseerd met uw gegevens bron.

## <a name="delete-the-azure-ad-connect-installation-log-file-folder-contents"></a>De inhoud van de map Azure AD Connect-installatie logboek bestand verwijderen
De inhoud van de map **c:\programdata\aadconnect** regel matig controleren en verwijderen, met uitzonde ring van het **PersistedState.Xml** -bestand. Dit bestand houdt de status van de vorige installatie van Azure A Connect bij en wordt gebruikt wanneer een upgrade-installatie wordt uitgevoerd. Dit bestand bevat geen gegevens over een persoon en mag niet worden verwijderd.

>[!IMPORTANT]
>Verwijder het PersistedState.xml bestand niet.  Dit bestand bevat geen gebruikers gegevens en onderhoudt de status van de vorige installatie.

U kunt deze bestanden controleren en verwijderen met Windows Verkenner of u kunt een script gebruiken zoals de volgende om de vereiste acties uit te voeren:


```
$Files = ((Get-childitem -Path "$env:programdata\aadconnect" -Recurse).VersionInfo).FileName
Foreach ($file in $files) {
If ($File.ToUpper() -ne "$env:programdata\aadconnect\PERSISTEDSTATE.XML".toupper()) # Do not delete this file
    {Remove-Item -Path $File -Force}
    } 
```

### <a name="schedule-this-script-to-run-every-48-hours"></a>Plannen dat dit script elke 48 uur wordt uitgevoerd
Gebruik de volgende stappen om het script te plannen dat elke 48 uur wordt uitgevoerd.

1.  Sla het script op in een bestand met de extensie **&#46;PS1**, open het configuratie scherm en klik op **systemen en beveiliging**.
    ![Systeem](./media/reference-connect-user-privacy/gdpr2.png)

2.  Klik onder de kop systeem beheer op **taken plannen**.
    ![Taak](./media/reference-connect-user-privacy/gdpr3.png)
3.  Klik in taak planner met de rechter muisknop op de **bibliotheek voor taak schema's** en klik op **basis taak maken...**
4.  Voer de naam voor de nieuwe taak in en klik op **volgende**.
5.  Selecteer **dagelijks** voor de taak trigger en klik op **volgende**.
6.  Stel het terugkeer patroon in op **2 dagen** en klik op **volgende**.
7.  Selecteer **een programma starten** als de actie en klik op **volgende**.
8.  Typ **Power shell** in het vak voor het programma/script, en typ in het vak **argumenten toevoegen (optioneel)** het volledige pad naar het script dat u eerder hebt gemaakt en klik vervolgens op **volgende**.
9.  In het volgende scherm ziet u een samen vatting van de taak die u gaat maken. Controleer de waarden en klik op **volt ooien** om de taak te maken.



## <a name="next-steps"></a>Volgende stappen
* [Het privacybeleid van micro soft op vertrouwens centrum bekijken](https://www.microsoft.com/trustcenter)
* [Azure AD Connect Health en privacy van gebruikers](reference-connect-health-user-privacy.md)