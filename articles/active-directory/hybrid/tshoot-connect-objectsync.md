---
title: 'Azure AD Connect: problemen met object synchronisatie oplossen | Microsoft Docs'
description: Dit onderwerp bevat stappen voor het oplossen van problemen met object synchronisatie met de taak voor probleem oplossing.
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
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73d4239dd34f2a64aa7b3edbf88bad4348e01291
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85356199"
---
# <a name="troubleshoot-object-synchronization-with-azure-ad-connect-sync"></a>Problemen met objectsynchronisatie oplossen met Azure AD Connect-synchronisatie
Dit artikel bevat stappen voor het oplossen van problemen met object synchronisatie met behulp van de taak voor het oplossen van problemen. Bekijk [deze korte video](https://aka.ms/AADCTSVideo)voor informatie over het oplossen van problemen met de werking van Azure Active Directory (Azure AD).

## <a name="troubleshooting-task"></a>Probleemoplossings taak
Voor implementatie van Azure AD Connect met versie 1.1.749.0 of hoger, gebruikt u de probleemoplossingstaak in de wizard  om problemen met objectsynchronisatie op te lossen. Voor eerdere versies kunt u het probleem hand matig oplossen zoals [hier](tshoot-connect-object-not-syncing.md)wordt beschreven.

### <a name="run-the-troubleshooting-task-in-the-wizard"></a>Taak voor probleemoplossing uitvoeren in de wizard
Voer de volgende stappen uit om de taak voor het oplossen van problemen in de wizard uit te voeren:

1.  Open een nieuwe Windows Power shell-sessie op uw Azure AD Connect-server met de optie als administrator uitvoeren.
2.  Uitvoeren `Set-ExecutionPolicy RemoteSigned` of `Set-ExecutionPolicy Unrestricted` .
3.  Start de wizard Azure AD Connect.
4.  Ga naar de pagina extra taken, selecteer problemen oplossen en klik op volgende.
5.  Klik op de pagina probleem oplossing op starten om het menu probleem oplossing in Power shell te starten.
6.  Selecteer in het hoofd menu de optie problemen met object synchronisatie oplossen.
![Problemen met object synchronisatie oplossen](media/tshoot-connect-objectsync/objsynch11.png)

### <a name="troubleshooting-input-parameters"></a>Problemen met invoer parameters oplossen
De volgende invoer parameters zijn nodig voor de taak voor het oplossen van problemen:
1.  **DN-naam van object** : dit is de DN-naam van het object waarvoor problemen moeten worden oplossen
2.  **Naam** van de AD-connector: dit is de naam van het AD-forest waarin het bovenstaande object zich bevindt.
3.  Globale beheerders referenties van de Azure AD-Tenant globale ![ beheerder referenties](media/tshoot-connect-objectsync/objsynch1.png)

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Inzicht in de resultaten van de taak voor het oplossen van problemen
De taak voor het oplossen van problemen voert de volgende controles uit:

1.  Niet-overeenkomende UPN detecteren als het object is gesynchroniseerd met Azure Active Directory
2.  Controleren of het object is gefilterd als gevolg van domein filtering
3.  Controleren of het object is gefilterd vanwege OE-filtering
4.  Controleren of object synchronisatie is geblokkeerd vanwege een gekoppeld Postvak
5. Controleren of object een dynamische distributie groep is die niet zou moeten worden gesynchroniseerd

In de rest van deze sectie worden de specifieke resultaten beschreven die door de taak worden geretourneerd. In elk geval bevat de taak een analyse, gevolgd door aanbevolen acties om het probleem op te lossen.

## <a name="detect-upn-mismatch-if-object-is-synced-to-azure-active-directory"></a>Niet-overeenkomende UPN detecteren als object is gesynchroniseerd met Azure Active Directory
### <a name="upn-suffix-is-not-verified-with-azure-ad-tenant"></a>UPN-achtervoegsel wordt niet geverifieerd met Azure AD-Tenant
Wanneer het achtervoegsel voor de aanmeldings-ID van UserPrincipalName (UPN)/Alternate niet wordt geverifieerd met de Azure AD-Tenant, worden de UPN-achtervoegsels door Azure Active Directory vervangen door de standaard domein naam ' onmicrosoft.com '.

![De UPN wordt vervangen door Azure AD](media/tshoot-connect-objectsync/objsynch2.png)

### <a name="azure-ad-tenant-dirsync-feature-synchronizeupnformanagedusers-is-disabled"></a>De Azure AD-Tenant DirSync-functie SynchronizeUpnForManagedUsers is uitgeschakeld
Wanneer de functie SynchronizeUpnForManagedUsers van Azure AD-Tenant is uitgeschakeld, staat Azure Active Directory geen synchronisatie-updates toe op UserPrincipalName/alternatieve aanmeldings-ID voor gelicentieerde gebruikers accounts met beheerde authenticatie.

![SynchronizeUpnForManagedUsers](media/tshoot-connect-objectsync/objsynch4.png)

## <a name="object-is-filtered-due-to-domain-filtering"></a>Het object is gefilterd vanwege een domein filtering
### <a name="domain-is-not-configured-to-sync"></a>Het domein is niet geconfigureerd om te synchroniseren
Het object valt buiten het bereik omdat het domein niet is geconfigureerd. In het onderstaande voor beeld ligt het object niet binnen het synchronisatie bereik als het domein waarvan het deel uitmaakt, wordt gefilterd op basis van de synchronisatie.

![Het domein is niet geconfigureerd om te synchroniseren](media/tshoot-connect-objectsync/objsynch5.png)

### <a name="domain-is-configured-to-sync-but-is-missing-run-profilesrun-steps"></a>Het domein is geconfigureerd om te synchroniseren, maar er ontbreken profielen voor uitvoeren/uitvoeren
Object valt buiten het bereik omdat voor het domein geen uitvoerings profielen worden uitgevoerd/stappen moeten worden uitgevoerd. In het onderstaande voor beeld bevindt het object zich niet in het synchronisatie bereik als in het domein waar het deel van uitmaakt, de stappen voor het volledige import profiel worden uitgevoerd.
![ontbrekende uitvoerings profielen](media/tshoot-connect-objectsync/objsynch6.png)

## <a name="object-is-filtered-due-to-ou-filtering"></a>Het object is gefilterd vanwege een organisatie-eenheids filter
Het object bevindt zich buiten het synchronisatie bereik vanwege de configuratie van de OE-filtering. In het onderstaande voor beeld behoort het object bij OE = NoSync, DC = bvtadwbackdc, DC = com.  Deze organisatie-eenheid is niet opgenomen in het synchronisatie bereik.</br>

![ORGANISATIE-EENHEID](./media/tshoot-connect-objectsync/objsynch7.png)

## <a name="linked-mailbox-issue"></a>Probleem met gekoppeld Postvak
Een gekoppeld postvak moet worden gekoppeld aan een extern hoofd account dat zich in een ander vertrouwd-account forest bevindt. Als er geen dergelijk extern hoofd account is, wordt het gebruikers account niet door Azure AD Connect gesynchroniseerd met het gekoppelde postvak in het Exchange-forest aan de Azure AD-Tenant.</br>
![Gekoppeld Postvak](./media/tshoot-connect-objectsync/objsynch12.png)

## <a name="dynamic-distribution-group-issue"></a>Probleem met dynamische distributie groep
Als gevolg van verschillende verschillen tussen on-premises Active Directory en Azure Active Directory, worden dynamische distributie groepen door Azure AD Connect niet gesynchroniseerd met de Azure AD-Tenant.

![Dynamische distributie groep](./media/tshoot-connect-objectsync/objsynch13.png)

## <a name="html-report"></a>HTML-rapport
Naast het analyseren van het-object genereert de taak voor het oplossen van problemen ook een HTML-rapport met alles wat bekend is over het object. Dit HTML-rapport kan worden gedeeld met het ondersteunings team, indien nodig om verdere problemen op te lossen.

![HTML-rapport](media/tshoot-connect-objectsync/objsynch8.png)

## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
