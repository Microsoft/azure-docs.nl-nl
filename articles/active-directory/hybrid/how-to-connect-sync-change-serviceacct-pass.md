---
title: 'Azure AD Connect synchronisatie: het ADSync-service account wijzigen | Microsoft Docs'
description: In dit onderwerp document wordt de versleutelings sleutel beschreven en wordt aangegeven hoe u deze kunt afbreken nadat het wacht woord is gewijzigd.
services: active-directory
keywords: Azure AD Sync-Service account, wacht woord
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
ms.date: 03/17/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e8778e50dcb881647696c6e901bf1058b9d6ac43
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "104720335"
---
# <a name="changing-the-adsync-service-account-password"></a>Het wacht woord voor het ADSync-service account wijzigen
Als u het wacht woord voor het ADSync-service account wijzigt, kan de synchronisatie service niet goed worden gestart totdat u de versleutelings sleutel hebt verlaten en het wacht woord voor het ADSync-service account opnieuw hebt geïnitialiseerd. 

>[!IMPORTANT]
> Als u verbinding maken met een build van 2017 maart of eerder gebruikt, moet u het wacht woord niet opnieuw instellen voor het service account, omdat Windows de versleutelings sleutels om veiligheids redenen heeft vernietigd. U kunt het account niet wijzigen in een ander account zonder Azure AD Connect opnieuw te installeren. Als u een upgrade uitvoert naar een build van 2017 april of hoger, wordt het wacht woord voor het service account gewijzigd, maar u kunt het gebruikte account niet wijzigen. 

Azure AD Connect, als onderdeel van de synchronisatie Services, gebruikt een versleutelings sleutel voor het opslaan van de wacht woorden van het AD DS Connector-account en het ADSync-service account.  Deze accounts worden versleuteld voordat ze worden opgeslagen in de-data base. 

De versleutelings sleutel die wordt gebruikt, is beveiligd met [Windows Data Protection (DPAPI)](/previous-versions/ms995355(v=msdn.10)). DPAPI beveiligt de versleutelings sleutel met behulp van het **ADSync-service account**. 

Als u het wacht woord van het service account wilt wijzigen, kunt u de procedures in [de versleutelings sleutel voor het ADSync-service account afbreken](#abandoning-the-adsync-service-account-encryption-key) om dit te bereiken.  Deze procedures moeten ook worden gebruikt als u de versleutelings sleutel om de een of andere reden moet afbreken.

## <a name="issues-that-arise-from-changing-the-password"></a>Problemen die zich voordoen bij het wijzigen van het wacht woord
Er zijn twee dingen die moeten worden uitgevoerd wanneer u het wacht woord van het service account wijzigt.

Eerst moet u het wacht woord wijzigen onder Windows-service besturings beheer.  Totdat dit probleem is opgelost, ziet u de volgende fouten:


- Als u de synchronisatie service in Windows Service Control Manager probeert te starten, wordt het volgende fout bericht weer gegeven: '**Windows kan de Microsoft Azure AD synchronisatie service op de lokale computer niet starten**'. **Fout 1069: de service is niet gestart vanwege een fout bij het aanmelden.**
- Onder Windows Logboeken bevat het systeem gebeurtenis logboek een fout met **gebeurtenis-ID 7038** en bericht '**de ADSync-service kan niet worden aangemeld met het momenteel geconfigureerde wacht woord vanwege de volgende fout: de gebruikers naam of het wacht woord is onjuist.**'

Ten tweede, onder specifieke omstandigheden, als het wacht woord is bijgewerkt, kan de synchronisatie service de versleutelings sleutel niet meer ophalen via DPAPI. Zonder de versleutelings sleutel kan de synchronisatie service de wacht woorden die vereist zijn om te synchroniseren met of van on-premises AD en Azure AD niet ontsleutelen.
Er worden fouten weer geven zoals:

- Als u in Windows Service Control Manager de synchronisatie service probeert te starten en de versleutelings sleutel niet kan ophalen, mislukt de fout '<strong>Windows kan de Microsoft Azure AD synchronisatie op de lokale computer niet starten. Raadpleeg het systeem gebeurtenis logboek voor meer informatie. Als dit een niet-micro soft-service is, neemt u contact op met de leverancier van de service en gaat u naar servicespecifieke fout code-21451857952</strong>.
- Onder Windows Logboeken bevat het gebeurtenis logboek van de toepassing een fout met **gebeurtenis-ID 6028** en fout bericht *' de server versleutelings sleutel is niet toegankelijk '.*

Om ervoor te zorgen dat deze fouten niet worden weer gegeven, volgt u de procedures in [de versleutelings sleutel voor het ADSync-service account afbreken](#abandoning-the-adsync-service-account-encryption-key) wanneer u het wacht woord wijzigt.
 
## <a name="abandoning-the-adsync-service-account-encryption-key"></a>De versleutelings sleutel voor het ADSync-service account wordt afgebroken
>[!IMPORTANT]
>De volgende procedures zijn alleen van toepassing op Azure AD Connect build 1.1.443.0 of ouder. Dit kan niet worden gebruikt voor nieuwere versies van Azure AD Connect omdat het afbreken van de versleutelings sleutel door Azure AD Connect zelf wordt afgehandeld wanneer u het wacht woord van de AD Sync-Service account wijzigt. de volgende stappen zijn niet vereist in de nieuwere versies.   

Gebruik de volgende procedures om de versleutelings sleutel te verlaten.

### <a name="what-to-do-if-you-need-to-abandon-the-encryption-key"></a>Wat u moet doen als u de versleutelings sleutel wilt verlaten

Als u de versleutelings sleutel wilt verlaten, gebruikt u de volgende procedures om dit te bereiken.

1. [De synchronisatie service stoppen](#stop-the-synchronization-service)

1. [De bestaande versleutelings sleutel afbreken](#abandon-the-existing-encryption-key)

2. [Geef het wacht woord van het AD DS Connector-account op](#provide-the-password-of-the-ad-ds-connector-account)

3. [Het wacht woord van het ADSync-service account opnieuw initialiseren](#reinitialize-the-password-of-the-adsync-service-account)

4. [De synchronisatie service starten](#start-the-synchronization-service)

#### <a name="stop-the-synchronization-service"></a>De synchronisatie service stoppen
Eerst kunt u de service stoppen in het Windows-service besturings beheer.  Zorg ervoor dat de service niet wordt uitgevoerd wanneer u deze probeert te stoppen.  Als dat het geval is, wacht u totdat de bewerking is voltooid en stopt u deze.


1. Ga naar Windows Service Control Manager (START → Services).
2. Selecteer **Microsoft Azure AD synchronisatie** en klik op stoppen.

#### <a name="abandon-the-existing-encryption-key"></a>De bestaande versleutelings sleutel afbreken
De bestaande versleutelings sleutel afbreken zodat de nieuwe versleutelings sleutel kan worden gemaakt:

1. Meld u als beheerder aan bij uw Azure AD Connect-server.

2. Een nieuwe Power shell-sessie starten.

3. Navigeer naar map: `'$env:ProgramFiles\Microsoft Azure AD Sync\bin\'`

4. Voer de volgende opdracht uit: `./miiskmu.exe /a`

![Scherm opname van Power shell na het uitvoeren van de opdracht.](./media/how-to-connect-sync-change-serviceacct-pass/key5.png)

#### <a name="provide-the-password-of-the-ad-ds-connector-account"></a>Geef het wacht woord van het AD DS Connector-account op
Als de bestaande wacht woorden in de data base niet langer kunnen worden ontsleuteld, moet u de synchronisatie service opgeven met het wacht woord van het AD DS Connector-account. Met de synchronisatie service worden de wacht woorden versleuteld met de nieuwe versleutelings sleutel:

1. Start de Synchronization Service Manager (START → synchronisatie service).
</br>![Service Manager synchroniseren](./media/how-to-connect-sync-change-serviceacct-pass/startmenu.png)  
2. Ga naar het tabblad **connectors** .
3. Selecteer de **ad-connector** die overeenkomt met uw on-premises AD. Als u meer dan één AD-connector hebt, herhaalt u de volgende stappen voor elk van deze.
4. Onder **acties**, selecteert u **Eigenschappen**.
5. Selecteer in het pop-updialoogvenster **verbinding maken met Active Directory forest**:
6. Voer het wacht woord van het AD DS account in het tekstvak **wacht woord** in. Als u het wacht woord niet kent, moet u dit instellen op een bekende waarde voordat u deze stap uitvoert.
7. Klik op **OK** om het nieuwe wacht woord op te slaan en het pop-updialoogvenster te sluiten.
![Scherm afbeelding met de pagina verbinding maken met Active Directory forest in het venster Eigenschappen.](./media/how-to-connect-sync-change-serviceacct-pass/key6.png)

#### <a name="reinitialize-the-password-of-the-adsync-service-account"></a>Het wacht woord van het ADSync-service account opnieuw initialiseren
U kunt het wacht woord van het Azure AD-service account niet rechtstreeks opgeven bij de synchronisatie service. In plaats daarvan moet u de cmdlet **add-ADSyncAADServiceAccount** gebruiken om het Azure AD-service account opnieuw te initialiseren. De cmdlet stelt het account wachtwoord opnieuw in en maakt het beschikbaar voor de synchronisatie service:

1. Start een nieuwe Power shell-sessie op de Azure AD Connect-server.
2. Voer de cmdlet uit `Add-ADSyncAADServiceAccount` .
3. Geef in het pop-upvenster de globale beheerders referenties voor Azure AD op voor uw Azure AD-Tenant.
![Hulp programma voor synchronisatie van versleutelings sleutel Azure AD Connect](./media/how-to-connect-sync-change-serviceacct-pass/key7.png)
4. Als dat lukt, wordt de Power shell-opdracht prompt weer gegeven.

#### <a name="start-the-synchronization-service"></a>De synchronisatie service starten
Nu de synchronisatie service toegang heeft tot de versleutelings sleutel en alle wacht woorden die ze nodig hebben, kunt u de service opnieuw starten in Windows Service Control Manager:


1. Ga naar Windows Service Control Manager (START → Services).
2. Selecteer **Microsoft Azure AD synchronisatie** en klik op opnieuw starten.

## <a name="next-steps"></a>Volgende stappen
**Overzichts onderwerpen**

* [Azure AD Connect synchroniseren: Synchronisatie begrijpen en aanpassen](how-to-connect-sync-whatis.md)

* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)
