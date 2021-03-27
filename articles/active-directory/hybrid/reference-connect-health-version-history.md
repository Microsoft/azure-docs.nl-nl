---
title: Versiegeschiedenis van Azure AD Connect Health
description: In dit document worden de releases van Azure AD Connect Health beschreven en wat in deze releases is opgenomen.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
editor: curtand
ms.assetid: 8dd4e998-747b-4c52-b8d3-3900fe77d88f
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/10/2020
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 408e972eb5950baa75fa1bdf33fb2cd507154bcb
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612837"
---
# <a name="azure-ad-connect-health-version-release-history"></a>Versiegeschiedenis van status Azure AD Connect
Het Azure Active Directory team werkt Azure AD Connect Health regel matig bij met nieuwe functies en functionaliteit. In dit artikel vindt u een overzicht van de versies en functies die zijn uitgebracht.  

> [!NOTE]
> Connect Health-Agents worden automatisch bijgewerkt wanneer er een nieuwe versie wordt uitgebracht. Zorg ervoor dat de instellingen voor automatisch bijwerken zijn ingeschakeld in Azure Portal.
>

Azure AD Connect Health voor synchronisatie is geïntegreerd met Azure AD Connect-installatie. Meer informatie over het Azure AD Connect van de [release geschiedenis](./reference-connect-version-history.md) voor functie feedback, stemmen bij [verbindings status van gebruikers Voice Channel](https://feedback.azure.com/forums/169401-azure-active-directory/filters/new?category_id=165591)

## <a name="march-2021"></a>2021 maart
**Agent update**

- Azure AD Connect Health Agent voor AD FS (versie 3.1.95.0)

  - Oplossing voor het omzetten van een gebruikers naam met NT4-indeling naar een UPN tijdens aanmeldings gebeurtenissen.
  - Oplossing om onjuiste toepassings-id-scenario's met een toegewezen fout code te identificeren.
  - Wijzigingen om een nieuwe eigenschap voor OAuth-client-id toe te voegen.
  - Oplossing voor het weer geven van de juiste waarden in de velden **protocol** en **verificatie type** in azure AD Sign-In rapport voor bepaalde aanmeldings scenario's.
  - Oplossing voor het weer geven van IP-adressen in azure AD Sign-In het veld IP-keten in volg orde van de aanvraag.
  - Wijzigingen voor het introduceren van een nieuw veld om onderscheid te maken als secundaire verificatie is aangevraagd tijdens een aanmelding.
  - Oplossing voor de AD FS toepassings-id-eigenschap die moet worden weer gegeven in het rapport van Azure AD-Sign-In.

## <a name="april-2020"></a>April 2020
**Agent update**

- Azure AD Connect Health Agent voor AD FS (versie 3.1.77.0)

   - Er is een fout opgelost voor de waarschuwing ' ongeldige SPN (Service Principal Name) voor de AD FS-service ', waarvoor de waarschuwing onjuist is gerapporteerd.


## <a name="july-2019"></a>Juli 2019
**Agent update**
* Azure AD Connect Health Agent voor AD FS (versie 3.1.59.0) 
   1. Tekst wijziging in TestWindowsTransport
   2. Wijzigingen voor het uploaden van AD FS RP
   
* Azure AD Connect Health Agent voor AD FS (versie 3.1.56.0) 
   1. TestWindowsTransport-test toevoegen en WsTrust-eind punten in CheckOffice365Endpoints-test verwijderen
   2. OS-en .NET-gegevens vastleggen in logboek
   3. Verhoog de upload grootte voor het RP-configuratie bericht naar 1MB.
   4. Opgeloste fouten
   
* Azure AD Connect Health Agent voor AD DS (versie 3.1.56.0) 
   1. OS-en .NET-gegevens vastleggen in logboek 
   2. Opgeloste fouten

## <a name="may-2019"></a>Mei 2019
**Agent update:** 
* Azure AD Connect Health Agent voor AD FS (versie 3.1.51.0) 
   1. Fout oplossing om onderscheid te maken tussen meerdere aanmeldingen die dezelfde client-aanvraag-id delen.
   2. Fout oplossing voor het parseren van fouten met ongeldige gebruikers namen en wacht woorden op gelokaliseerde servers.   

## <a name="april-2019"></a>April 2019
**Agent update:** 
* Azure AD Connect Health Agent voor AD FS (versie 3.1.46.0) 
   1. Het controle proces voor dubbele SPN-waarschuwingen voor ADFS oplossen

## <a name="march-2019"></a>Maart 2019
**Agent update:** 
* Azure AD Connect Health Agent voor AD DS (versie 3.1.41.0)  
   1. .NET-versie verzameling
   2. Verbetering van de verzameling prestatie meter items bij ontbrekende bepaalde categorieën
   3. Fout oplossing voor het voor komen van het starten van meerdere exemplaren van bewakings agent

* Azure AD Connect Health Agent voor AD FS (versie 3.1.41.0) 
   1. Integratie en upgrade van AD FS test scripts met behulp van ADFSToolBox
   2. .NET-versie verzameling implementeren
   3. Verbetering van de verzameling prestatie meter items bij ontbrekende bepaalde categorieën
   4. Fout oplossing voor het voor komen van het starten van meerdere exemplaren van bewakings agent


## <a name="november-2018"></a>November 2018
**Nieuwe GA-functies:** 
* Azure AD Connect Health voor Sync: problemen vaststellen en dubbele kenmerken synchronisatie fouten van de portal oplossen

**Agent update:** 
* Azure AD Connect Health Agent voor AD DS (versie 3.1.24.0) 
   1. Compatibiliteit en afdwinging van Protocol versie 1,2 van Transport Layer Security (TLS)
   2. Waarschuwings ruis van globale catalogus verminderen
   3. Fout oplossingen voor registratie van Health Agent

* Azure AD Connect Health Agent voor AD FS (versie 3.1.24.0)  
   1. Compatibiliteit en afdwinging van Protocol versie 1,2 van Transport Layer Security (TLS)
   2. Ondersteuning van Test-ADFSRequestToken voor gelokaliseerd besturings systeem
   3. Probleem met het oplossen van problemen met de diagnostische agent is opgelost
   4. Fout oplossingen voor registratie van Health Agent

## <a name="august-2018"></a>Augustus 2018 
*  Azure AD Connect Health-Agent voor synchronisatie (versie 3.1.7.0) uitgebracht met Azure AD Connect versie 1.1.880.0    
   1. Hotfix voor een [hoge CPU-probleem van de bewakings agent met .NET Framework KB-releases](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)

## <a name="june-2018"></a>Juni 2018 
**Nieuwe preview-functies:** 
* Azure AD Connect Health voor Sync: problemen vaststellen en dubbele kenmerken synchronisatie fouten van de portal oplossen 

**Agent update:** 
* Azure AD Connect Health Agent voor AD DS (versie 3.1.7.0)    
  1. Hotfix voor een [hoge CPU-probleem van de bewakings agent met .NET Framework KB-releases](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
   
* Azure AD Connect Health Agent voor AD FS (versie 3.1.7.0)  
  1. Hotfix voor een [hoge CPU-probleem van de bewakings agent met .NET Framework KB-releases](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
  2. Resultaten van tests op AD FS-server 2016 secundaire server herstellen
   
* Azure AD Connect Health Agent voor AD FS (versie 3.1.2.0)  
  1. Hotfix voor agent geheugen beheer en gerelateerde waarschuwingen specifiek voor versie 3.0.244.0


## <a name="may-2018"></a>Mei 2018
**Agent update:**
* Azure AD Connect Health Agent voor AD DS (versie 3.0.244.0)
  1. Verbetering van de beveiligings agent  
  2. Oplossingen voor fouten en algemene verbeteringen

* Azure AD Connect Health Agent voor AD FS (versie 3.0.244.0)
  1. Verbeteringen van de Agent-service en gerelateerde Power shell-module
  2. Verbetering van de beveiligings agent  
  3. Oplossingen voor fouten en algemene verbeteringen

* Azure AD Connect Health-Agent voor synchronisatie (versie 3.0.164.0) uitgebracht met Azure AD Connect versie 1.1.819.0 
  1. Verbetering van de beveiligings agent  
  2. Oplossingen voor fouten en algemene verbeteringen


## <a name="march-2018"></a>Maart 2018
**Nieuwe preview-functies:**
* Azure AD Connect Health voor AD FS-Riskeel IP-rapport en waarschuwing.

**Agent update:**

* Azure AD Connect Health Agent voor AD DS (versie 3.0.176.0)
  1. Verbeteringen van agent beschikbaarheid 
  2. Oplossingen voor fouten en algemene verbeteringen
* Azure AD Connect Health Agent voor AD FS (versie 3.0.176.0)
  1. Verbeteringen van agent beschikbaarheid 
  2. Oplossingen voor fouten en algemene verbeteringen
* Azure AD Connect Health-Agent voor synchronisatie (versie 3.0.129.0) uitgebracht met Azure AD Connect versie 1.1.750.0  
  1. Verbeteringen van agent beschikbaarheid 
  2. Oplossingen voor fouten en algemene verbeteringen

## <a name="december-2017"></a>December 2017
**Agent update:**

* Azure AD Connect Health Agent voor AD DS (versie 3.0.145.0)
  1. Verbeteringen van agent beschikbaarheid 
  2. Nieuwe probleemoplossings opdrachten voor agents toegevoegd
  3. Oplossingen voor fouten en algemene verbeteringen
* Azure AD Connect Health Agent voor AD FS (versie 3.0.145.0)
  1. Nieuwe probleemoplossings opdrachten voor agents toegevoegd
  2. Verbeteringen van agent beschikbaarheid 
  3. Oplossingen voor fouten en algemene verbeteringen
  
## <a name="october-2017"></a>Oktober 2017
**Agent update:**

 * Azure AD Connect Health-Agent voor synchronisatie (versie 3.0.129.0) uitgebracht met Azure AD Connect versie 1.1.649.0
<br></br> Er is een probleem opgelost met versie compatibiliteit tussen Azure AD Connect en Azure AD Connect Health Agent voor synchronisatie. Dit probleem is van invloed op klanten die Azure AD Connect in-place upgrade uitvoeren op versie 1.1.647.0, maar die momenteel versie 3.0.127.0 van Health Agent hebben. Na de upgrade kan de Health-Agent geen status gegevens meer verzenden over Azure AD Connect synchronisatie service naar Azure AD Health Service. Met deze oplossing wordt de versie 3.0.129.0 van de status agent geïnstalleerd tijdens Azure AD Connect in-place upgrade. Versie 3.0.129.0 van Health Agent heeft geen compatibiliteits probleem met Azure AD Connect versie 1.1.649.0.

## <a name="july-2017"></a>Juli 2017
**Agent update:**

* Azure AD Connect Health Agent voor AD DS (versie 3.0.68.0)
  1. Oplossingen voor fouten en algemene verbeteringen
  2. Ondersteuning van soevereine Cloud
* Azure AD Connect Health Agent voor AD FS (versie 3.0.68.0)
  1. Oplossingen voor fouten en algemene verbeteringen
  2. Ondersteuning van soevereine Cloud
* Azure AD Connect Health-Agent voor synchronisatie (versie 3.0.68.0) uitgebracht met Azure AD Connect versie 1.1.614.0
  1. Ondersteuning voor Microsoft Azure Government Cloud en Microsoft Cloud Duitsland

## <a name="april-2017"></a>April 2017      
**Agent update:**

* Azure AD Connect Health Agent voor AD FS (versie 3.0.12.0)
  1. Oplossingen voor fouten en algemene verbeteringen
* Azure AD Connect Health Agent voor AD DS (versie 3.0.12.0)
  1. Prestatie meter items Upload verbeteringen
  2. Oplossingen voor fouten en algemene verbeteringen

## <a name="october-2016"></a>Oktober 2016
**Agent update:**

* Azure AD Connect Health Agent voor AD FS (versie 2.6.408.0)
* Verbeteringen in het detecteren van IP-adressen van clients in verificatie aanvragen
* Oplossingen met betrekking tot waarschuwingen
* Azure AD Connect Health Agent voor AD DS (versie 2.6.408.0)
* Oplossingen met betrekking tot waarschuwingen.
* Azure AD Connect Health-Agent voor synchronisatie (versie 2.6.353.0) uitgebracht met Azure AD Connect versie 1.1.281.0
* Geef de vereiste gegevens op voor de synchronisatie fouten rapporten
* Oplossingen met betrekking tot waarschuwingen

**Nieuwe preview-functies:**

* Rapporten over synchronisatie fouten voor Azure AD Connect

**Nieuwe functies:**

* Azure AD Connect Health voor AD FS-IP-adres veld is beschikbaar in het rapport over de belangrijkste 50 gebruikers met een onjuiste gebruikers naam/wacht woord.

## <a name="july-2016"></a>Juli 2016
**Nieuwe preview-functies:**

* [Azure AD Connect Health voor AD DS](how-to-connect-health-adds.md).

## <a name="january-2016"></a>Januari 2016
**Agent update:**

* Azure AD Connect Health Agent voor AD FS (versie 2.6.91.1512)

**Nieuwe functies:**

* [Het hulp programma connectiviteit testen voor Azure AD Connect Health Agents](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)

## <a name="november-2015"></a>November 2015
**Nieuwe functies:**

* Ondersteuning voor [op rollen gebaseerd toegangs beheer van Azure (Azure RBAC)](how-to-connect-health-operations.md#manage-access-with-azure-rbac)

**Nieuwe preview-functies:**

* [Azure AD Connect Health voor synchronisatie](how-to-connect-health-sync.md).

**Opgeloste problemen:**

* Fout oplossingen voor fouten die zijn opgetreden tijdens agent registraties.

## <a name="september-2015"></a>September 2015
**Nieuwe functies:**

* Onjuist wacht woord voor gebruikers naam voor AD FS
* Ondersteuning voor het configureren van niet-geverifieerde HTTP-proxy
* Ondersteuning voor het configureren van agents op Server Core
* Verbeteringen in waarschuwingen voor AD FS
* Verbeteringen in Azure AD Connect Health-Agent voor AD FS voor connectiviteit en het uploaden van gegevens.

**Opgeloste problemen:**

* Fouten worden opgelost in gebruiks inzichten voor AD FS fout typen.

## <a name="june-2015"></a>Juni 2015
**De eerste versie van Azure AD Connect Health voor AD FS en AD FS proxy.**

**Nieuwe functies:**

* Waarschuwingen voor het bewaken van AD FS en AD FS proxy servers met e-mail meldingen.
* Eenvoudige toegang tot AD FS topologie en patronen in AD FS prestatie meter items.
* Trend in geslaagde token aanvragen op AD FS servers gegroepeerd op toepassingen, verificatie methoden, netwerk locatie aanvragen enz.
* Trends in mislukte aanvragen op AD FS servers, gegroepeerd op toepassingen, fout typen enz.
* Een eenvoudigere agent implementatie met behulp van de referenties van de globale beheerder van Azure AD.  

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [het bewaken van uw on-premises infra structuur voor identiteiten en synchronisatie Services in de Cloud](./whatis-azure-ad-connect.md).
