---
title: Persoonlijke gegevens verwijderen-Azure Active Directory-toepassingsproxy
description: Persoonlijke gegevens verwijderen van connectors die op apparaten zijn geïnstalleerd voor Azure Active Directory-toepassingsproxy.
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/21/2018
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90913ba8f7fbe8158a5cfea01e49a175180677b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258945"
---
# <a name="remove-personal-data-for-azure-active-directory-application-proxy"></a>Persoonlijke gegevens voor Azure Active Directory-toepassingsproxy verwijderen

Voor Azure Active Directory-toepassingsproxy moet u connectors op uw apparaten installeren. Dit betekent dat er persoons gegevens op uw apparaten kunnen zijn. In dit artikel worden de stappen beschreven voor het verwijderen van die persoons gegevens ter verbetering van de privacy.

## <a name="where-is-the-personal-data"></a>Waar bevindt zich de persoonlijke gegevens?

Het is mogelijk om met een toepassings proxy persoonlijke gegevens te schrijven naar de volgende logboek typen:

- Gebeurtenis logboeken voor connector
- Windows-gebeurtenislogboeken

## <a name="remove-personal-data-from-windows-event-logs"></a>Persoonlijke gegevens uit de Windows-gebeurtenis logboeken verwijderen

Zie [instellingen voor gebeurtenis logboeken](https://technet.microsoft.com/library/cc952132.aspx)voor meer informatie over het configureren van gegevens retentie voor de Windows-gebeurtenis Logboeken. Zie [Windows-gebeurtenis logboek gebruiken](/windows/win32/wes/using-windows-event-log)voor meer informatie over Windows-gebeurtenis Logboeken.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="remove-personal-data-from-connector-event-logs"></a>Persoonlijke gegevens uit connector gebeurtenis logboeken verwijderen

Om ervoor te zorgen dat de toepassings proxy logboeken geen persoonlijke gegevens hebben, kunt u het volgende doen:

- Gegevens verwijderen of weer geven wanneer dit nodig is, of
- Logboek registratie uitschakelen

Gebruik de volgende secties om persoonlijke gegevens uit de gebeurtenis logboeken van connectors te verwijderen. U moet het verwijderings proces volt ooien voor alle apparaten waarop de connector is geïnstalleerd.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-or-export-specific-data"></a>Specifieke gegevens weer geven of exporteren

Als u specifieke gegevens wilt bekijken of exporteren, zoekt u naar verwante items in elk van de gebeurtenis logboeken van de connector. De logboeken bevinden zich op `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace` .

Aangezien de logboeken tekst bestanden zijn, kunt u [findstr](/windows-server/administration/windows-commands/findstr) gebruiken om te zoeken naar tekst items die betrekking hebben op een gebruiker.  

Zoek logboek bestanden op gebruikers-id om persoons gegevens te vinden.

Als u wilt zoeken naar persoons gegevens die zijn geregistreerd door een toepassing die gebruikmaakt van Kerberos-beperkte overdracht, zoekt u naar deze onderdelen van het type gebruikers naam:

- On-premises user principal name
- Het deel van de gebruikers naam van user principal name
- Het deel van de gebruikers naam van de on-premises user principal name
- On-premises SAM-account naam (Security Accounts Manager)

### <a name="delete-specific-data"></a>Specifieke gegevens verwijderen

Specifieke gegevens verwijderen:

1. Start de Microsoft Azure AD Application proxy connector-service opnieuw om een nieuw logboek bestand te genereren. Met het nieuwe logboek bestand kunt u de oude logboek bestanden verwijderen of wijzigen. 
1. Volg de eerder beschreven procedure voor het [weer geven of exporteren van specifieke gegevens](#view-or-export-specific-data) om informatie te vinden die moet worden verwijderd. Zoek alle connector Logboeken.
1. Verwijder de relevante logboek bestanden of verwijder selectief de velden die persoonlijke gegevens bevatten. U kunt ook alle oude logboek bestanden verwijderen als u deze niet meer nodig hebt.

### <a name="turn-off-connector-logs"></a>Connector Logboeken uitschakelen

Een optie om ervoor te zorgen dat de connector logboeken geen persoonlijke gegevens bevatten, is het genereren van het logboek uit te scha kelen. Als u wilt stoppen met het genereren van connector logboeken, verwijdert u de volgende gemarkeerde regel uit `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config` .

![Hiermee wordt een code fragment weer gegeven met de gemarkeerde code die moet worden verwijderd](./media/application-proxy-remove-personal-data/01.png)

## <a name="next-steps"></a>Volgende stappen

Zie [veilige externe toegang bieden voor on-premises toepassingen](application-proxy.md)voor een overzicht van de toepassings proxy.