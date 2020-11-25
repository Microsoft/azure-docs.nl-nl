---
title: Privacy van gebruikers en Azure Active Directory Pass Through-verificatie | Microsoft Docs
description: In dit artikel worden de Pass-Through-verificatie en AVG-naleving van Azure Active Directory (Azure AD) behandeld.
services: active-directory
keywords: Azure AD Connect Pass-Through-verificatie, AVG, vereiste onderdelen voor Azure AD, SSO, eenmalige aanmelding
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/23/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 396344ba90aa3850d7d23dc40d6df95f6d1f6c3f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996573"
---
# <a name="user-privacy-and-azure-active-directory-pass-through-authentication"></a>Gebruikersprivacy en Pass-through-verificatie van Azure Active Directory


[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Overzicht

Met Azure AD Pass-Through-verificatie maakt u het volgende logboek type, dat persoonlijke gegevens kan bevatten:

- Traceer logboek bestanden Azure AD Connect.
- Traceer logboek bestanden van de verificatie agent.
- Windows-gebeurtenis logboek bestanden.

Verbeter de privacy van gebruikers voor Pass-Through-verificatie op twee manieren:

1.  Extraheer op aanvraag gegevens voor een persoon en verwijder gegevens van die persoon uit de installaties.
2.  Zorg ervoor dat er geen gegevens meer dan 48 uur worden bewaard.

We raden u ten zeerste aan de tweede optie te implementeren, omdat deze eenvoudiger kan worden geïmplementeerd en onderhouden. Hieronder vindt u de instructies voor elk logboek type:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Azure AD Connect traceer logboek bestanden verwijderen

Controleer de inhoud van de map **%ProgramData%\AADConnect** en verwijder de inhoud van het tracerings logboek (**Trace- \* . log-** bestanden) van deze map binnen 48 uur na het installeren of upgraden van Azure AD Connect of het wijzigen van de Pass-Through-verificatie configuratie, omdat deze actie gegevens kan maken die onder AVG vallen.

>[!IMPORTANT]
>Verwijder niet het **PersistedState.xml** bestand in deze map, omdat dit bestand wordt gebruikt om de status van de vorige installatie van Azure AD Connect te behouden en wordt gebruikt wanneer de installatie van een upgrade is voltooid. Dit bestand bevat nooit gegevens over een persoon en mag nooit worden verwijderd.

U kunt deze traceer logboek bestanden met behulp van Windows Verkenner controleren en verwijderen of u kunt het volgende Power shell-script gebruiken om de vereiste acties uit te voeren:

```
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Sla het script op in een bestand met de ". PS1-extensie. Voer dit script uit als dat nodig is.

Zie [dit artikel](reference-connect-user-privacy.md)voor meer informatie over gerelateerde Azure AD Connect AVG-vereisten.

### <a name="delete-authentication-agent-event-logs"></a>Gebeurtenis logboeken voor de verificatie agent verwijderen

Dit product kan ook **Windows-gebeurtenis logboeken** maken. Lees [dit artikel](/windows/win32/wes/windows-event-log)voor meer informatie.

Als u logboeken wilt weer geven die betrekking hebben op de Pass-Through-verificatie agent, opent u de **Logboeken** -toepassing op de server en controleert u onder **Application and service Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin**.

### <a name="delete-authentication-agent-trace-log-files"></a>Traceer logboek bestanden voor de verificatie agent verwijderen

Controleer regel matig de inhoud van de **%PROGRAMDATA%\MICROSOFT\AZURE AD Connect-verificatie Agent\Trace** en verwijder de inhoud van deze map elke 48 uur. 

>[!IMPORTANT]
>Als de verificatie Agent-service wordt uitgevoerd, kunt u het huidige logboek bestand niet verwijderen uit de map. Stop de service voordat u het opnieuw probeert. Als u wilt voor komen dat gebruikers zich aanmelden, moet u de Pass-Through-verificatie voor [hoge Beschik baarheid](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)al hebben geconfigureerd.

U kunt deze bestanden controleren en verwijderen met Windows Verkenner of u kunt het volgende script gebruiken om de vereiste acties uit te voeren:

```
$Files = ((Get-childitem -Path "$env:programdata\microsoft\azure ad connect authentication agent\trace" -Recurse).VersionInfo).FileName 
 
Foreach ($file in $files) { 
    {Remove-Item -Path $File -Force} 
}
```

Voer de volgende stappen uit om dit script te plannen om om de 48 uur uit te voeren:

1.  Sla het script op in een bestand met de ". PS1-extensie.
2.  Open **configuratie scherm** en klik op **systeem en beveiliging**.
3.  Klik onder de kop **systeem beheer** op **taken plannen**.
4.  Klik in **taak planner** met de rechter muisknop op de **bibliotheek taak planning** en klik op **basis taak maken...**.
5.  Voer de naam voor de nieuwe taak in en klik op **volgende**.
6.  Selecteer **dagelijks** voor de **taak trigger** en klik op **volgende**.
7.  Stel het terugkeer patroon in op twee dagen en klik op **volgende**.
8.  Selecteer **een programma starten** als de actie en klik op **volgende**.
9.  Typ '**Power shell**' in het vak voor het programma/script en in het vak '**Voeg argumenten toe (optioneel)**' het volledige pad naar het script dat u eerder hebt gemaakt en klik vervolgens op **volgende**.
10. In het volgende scherm ziet u een samen vatting van de taak die u gaat maken. Controleer de waarden en klik op **volt ooien** om de taak te maken:
 
### <a name="note-about-domain-controller-logs"></a>Opmerking over de logboeken van de domein controller

Als controle logboek registratie is ingeschakeld, kan dit product beveiligings logboeken genereren voor uw domein controllers. Lees dit [artikel](/previous-versions/tn-archive/dd277403(v=technet.10))voor meer informatie over het configureren van controle beleid.

## <a name="next-steps"></a>Volgende stappen
* [Het privacybeleid van micro soft op vertrouwens centrum bekijken](https://www.microsoft.com/trustcenter)
* [**Problemen oplossen**](tshoot-connect-pass-through-authentication.md) : informatie over het oplossen van veelvoorkomende problemen met de functie.