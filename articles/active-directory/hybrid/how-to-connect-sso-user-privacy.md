---
title: Privacy van gebruikers en Azure AD naadloze single Sign-On | Microsoft Docs
description: In dit artikel worden Azure Active Directory (Azure AD) naadloze SSO-en AVG-naleving besproken.
services: active-directory
keywords: Wat is Azure AD Connect, AVG, vereiste onderdelen voor Azure AD, SSO, eenmalige aanmelding
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2aa1a30c548ef60cd9b596031f4115297dd20844
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89278595"
---
# <a name="user-privacy-and-azure-ad-seamless-single-sign-on"></a>Gebruikersprivacy en naadloze eenmalige aanmelding van Azure AD

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Overzicht


Azure AD naadloze SSO maakt het volgende logboek type, dat persoonlijke gegevens kan bevatten: 

- Traceer logboek bestanden Azure AD Connect.

Verbeter de privacy van gebruikers voor naadloze SSO op twee manieren:

1.  Extraheer op aanvraag gegevens voor een persoon en verwijder gegevens van die persoon uit de installaties.
2.  Zorg ervoor dat er geen gegevens meer dan 48 uur worden bewaard.

We raden u ten zeerste aan de tweede optie te implementeren, omdat deze eenvoudiger kan worden geïmplementeerd en onderhouden. Zie de volgende instructies voor elk logboek type:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Azure AD Connect traceer logboek bestanden verwijderen

Controleer de inhoud van de map **%ProgramData%\AADConnect** en verwijder de inhoud van het tracerings logboek (**Trace- \* . log-** bestanden) van deze map binnen 48 uur na het installeren of upgraden van Azure AD Connect of het wijzigen van de naadloze SSO-configuratie, omdat deze actie gegevens kan maken die onder AVG vallen.

>[!IMPORTANT]
>Verwijder niet het **PersistedState.xml** bestand in deze map, omdat dit bestand wordt gebruikt om de status van de vorige installatie van Azure AD Connect te behouden en wordt gebruikt wanneer de installatie van een upgrade is voltooid. Dit bestand bevat nooit gegevens over een persoon en mag nooit worden verwijderd.

U kunt deze traceer logboek bestanden met behulp van Windows Verkenner controleren en verwijderen of u kunt het volgende Power shell-script gebruiken om de vereiste acties uit te voeren:

```powershell
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Sla het script op in een bestand met de ". PS1-extensie. Voer dit script uit als dat nodig is.

Zie [dit artikel](reference-connect-user-privacy.md)voor meer informatie over gerelateerde Azure AD Connect AVG-vereisten.

### <a name="note-about-domain-controller-logs"></a>Opmerking over de logboeken van de domein controller

Als controle logboek registratie is ingeschakeld, kan dit product beveiligings logboeken genereren voor uw domein controllers. Lees dit [artikel](/previous-versions/tn-archive/dd277403(v=technet.10))voor meer informatie over het configureren van controle beleid.

## <a name="next-steps"></a>Volgende stappen

* [Het privacybeleid van micro soft op vertrouwens centrum bekijken](https://www.microsoft.com/trustcenter)
  - [**Problemen oplossen**](tshoot-connect-sso.md) : informatie over het oplossen van veelvoorkomende problemen met de functie.
  - [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) -voor het indienen van nieuwe functie aanvragen.