---
title: Hybride identiteits ontwerp-vereisten voor adreslijst synchronisatie Azure | Microsoft Docs
description: Bepaal welke vereisten nodig zijn om alle gebruikers te synchroniseren tussen on = premises en de Cloud voor de onderneming.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 593eaa71-17eb-4c16-8c98-43cc62987e65
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 500d226fcb60646becc49144f206dcb0dee49bd8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89278391"
---
# <a name="determine-directory-synchronization-requirements"></a>Vereisten voor adreslijst synchronisatie bepalen
Synchronisatie is alles over het bieden van gebruikers een identiteit in de Cloud op basis van hun on-premises identiteit. Ongeacht of ze een gesynchroniseerd account voor verificatie of Federated Authentication gebruiken, moeten de gebruikers nog steeds een identiteit in de Cloud hebben.  Deze identiteit moet regel matig worden onderhouden en bijgewerkt.  De updates kunnen veel formulieren aannemen, van wijzigingen in de naam van het wacht woord.  

Begin met het evalueren van de on-premises identiteits oplossing en gebruikers vereisten van de organisatie. Deze evaluatie is belang rijk voor het definiëren van de technische vereisten voor het maken en onderhouden van gebruikers identiteiten in de Cloud.  Voor een meerderheid van organisaties is Active Directory on-premises en de on-premises Directory waarmee gebruikers worden gesynchroniseerd, maar in sommige gevallen is dit niet het geval.  

Zorg ervoor dat u de volgende vragen beantwoordt:

* Hebt u één AD-forest, meerdere of geen?
  
  * Hoeveel Azure AD-directory's wilt u synchroniseren?
    
    1. Gebruikt u filteren?
    2. Hebt u meerdere Azure AD Connect-servers gepland?
* Hebt u momenteel een hulp programma voor synchronisatie op locatie?
  
  * Zo ja, worden uw gebruikers als ze een virtuele map/integratie van identiteiten hebben?
* Hebt u een andere directory on-premises die u wilt synchroniseren (bijvoorbeeld LDAP-adres lijst, HR-data base, enzovoort)?
  * Gaat u een GALSync uitvoeren?
  * Wat is de huidige status van Upn's in uw organisatie? 
  * Hebt u een andere Directory waarmee gebruikers worden geverifieerd?
  * Maakt uw bedrijf gebruik van micro soft Exchange?
    * Bent u van plan een hybride Exchange-implementatie uit te voeren?

Nu u een idee hebt over uw synchronisatie vereisten, moet u bepalen welk hulp programma het juist is om aan deze vereisten te voldoen.  Micro soft biedt verschillende hulp middelen voor het uitvoeren van adreslijst integratie en synchronisatie.  Zie de [vergelijkings tabel integratie Hulpprogramma's hybride identiteit Directory](plan-hybrid-identity-design-considerations-tools-comparison.md) voor meer informatie. 

Nu u de synchronisatie vereisten hebt en het hulp programma dat dit voor uw bedrijf gaat uitvoeren, moet u de toepassingen evalueren die gebruikmaken van deze adreslijst Services. Deze evaluatie is belang rijk voor het definiëren van de technische vereisten voor het integreren van deze toepassingen in de Cloud. Zorg ervoor dat u de volgende vragen beantwoordt:

* Worden deze toepassingen naar de Cloud verplaatst en wordt de map gebruikt?
* Zijn er speciale kenmerken die moeten worden gesynchroniseerd met de Cloud, zodat deze toepassingen ze kunnen gebruiken?
* Moeten deze toepassingen opnieuw worden geschreven om gebruik te kunnen maken van Cloud authenticatie?
* Blijven deze toepassingen on-premises actief terwijl gebruikers er toegang tot hebben met de Cloud identiteit?

U moet ook de Directory synchronisatie van de beveiligings vereisten en beperkingen bepalen. Deze evaluatie is belang rijk voor het verkrijgen van een lijst met de vereisten die nodig zijn om de gebruikers identiteiten in de cloud te maken en te onderhouden. Zorg ervoor dat u de volgende vragen beantwoordt:

* Waar wordt de synchronisatie server gevonden?
* Is het lid van een domein?
* Bevindt de server zich op een beperkt netwerk achter een firewall, zoals een DMZ?
  * Kunt u de vereiste firewall poorten openen ter ondersteuning van synchronisatie?
* Hebt u een plan voor nood herstel voor de synchronisatie server?
* Hebt u een account met de juiste machtigingen voor alle forests waarmee u wilt synchroniseren?
  * Als uw bedrijf niet het antwoord op deze vraag kent, raadpleegt u de sectie Machtigingen voor wachtwoord synchronisatie in het artikel [Installeer de Azure Active Directory Sync-Service](/previous-versions/azure/azure-services/dn757602(v=azure.100)#BKMK_CreateAnADAccountForTheSyncService) en bepaalt u of u al een account met deze machtigingen hebt of dat u er een hebt gemaakt.
* Als u Multi-forest Sync is, kan de synchronisatie server elk forest ophalen?

> [!NOTE]
> Zorg ervoor dat u elk antwoord noteert de logica achter het antwoord begrijpt. De [vereisten voor de reactie op incidenten bepalen](plan-hybrid-identity-design-considerations-incident-response-requirements.md) de beschik bare opties. Als u deze vragen hebt beantwoord, selecteert u welke optie het beste past bij uw bedrijfs behoeften.
> 
> 

## <a name="next-steps"></a>Volgende stappen
[Vereisten voor multi-factor Authentication bepalen](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="see-also"></a>Zie ook
[Overzicht van ontwerp overwegingen](plan-hybrid-identity-design-considerations-overview.md)