---
title: Een app markeren als uitgever geverifieerd-micro soft Identity-platform | Azure
description: Hierin wordt beschreven hoe u een app markeert als geverifieerd door een uitgever. Wanneer een toepassing is gemarkeerd als uitgever, betekent dit dat de uitgever zijn of haar identiteit heeft gecontroleerd met behulp van een Microsoft Partner Network account dat het verificatie proces heeft voltooid en dat dit MPN-account is gekoppeld aan de registratie van de toepassing.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: 2b658cdc58777274bb14f9e8069cef2facdb0479
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92363450"
---
# <a name="mark-your-app-as-publisher-verified"></a>Uw app markeren als Uitgever geverifieerd

Wanneer een app-registratie een geverifieerde uitgever heeft, betekent dit dat de uitgever van de app de identiteit heeft [gecontroleerd](/partner-center/verification-responses) met behulp van hun Microsoft Partner Network-account (MPN) en dat deze MPN-account is gekoppeld aan de app-registratie. In dit artikel wordt beschreven hoe u het verificatie proces van de [Uitgever](publisher-verification-overview.md) kunt volt ooien.

## <a name="quickstart"></a>Snelstart
Als u al bent Inge schreven in de Microsoft Partner Network (MPN) en u aan de [vereisten](publisher-verification-overview.md#requirements)hebt voldaan, kunt u meteen aan de slag gaan: 

1. Meld u aan bij de [Portal voor app-registratie](https://aka.ms/PublisherVerificationPreview) met [multi-factor Authentication](../fundamentals/concept-fundamentals-mfa-get-started.md)

1. Kies een app en klik op **huis stijl**. 

1. Klik op **MPN-id toevoegen om de uitgever te controleren** en de vermelde vereisten te bekijken.

1. Voer uw MPN-ID in en klik op **verifiëren en opslaan**.

Zie het [overzicht](publisher-verification-overview.md)voor meer informatie over specifieke voor delen, vereisten en veelgestelde vragen.


## <a name="mark-your-app-as-publisher-verified"></a>Uw app markeren als Uitgever geverifieerd
Zorg ervoor dat u aan de [vereisten](publisher-verification-overview.md#requirements)voldoet en voer vervolgens de volgende stappen uit om uw app (s) te markeren als Uitgever: gecontroleerd.  

1. Zorg ervoor dat u bent aangemeld met [multi-factor Authentication](../fundamentals/concept-fundamentals-mfa-get-started.md) voor een organisatie-of Azure ad-account dat is gemachtigd om wijzigingen aan te brengen in de app (s) die u wilt markeren als uitgever en op het MPN-account in partner centrum.

    - In azure AD moet deze gebruiker lid zijn van een van de volgende [rollen](../roles/permissions-reference.md): toepassings beheerder, Cloud toepassings beheerder, globale beheerder. 

    - In het partner centrum moet deze gebruiker over de volgende [rollen](/partner-center/permissions-overview)beschikken: MPN admin, accounts Administrator of een globale beheerder (dit is een gedeelde rol in azure AD). 

1. Ga naar de app registratie-portal:  

1. Klik op een app die u wilt markeren als uitgever en open de Blade huis stijl. 

1. Zorg ervoor dat het [Uitgever domein](howto-configure-publisher-domain.md) van de app is ingesteld. 

1. Zorg ervoor dat het domein van de uitgever of een aangepast DNS- [domein](../fundamentals/add-custom-domain.md) op de Tenant overeenkomt met het domein van het e-mail adres dat wordt gebruikt tijdens het verificatie proces voor uw MPN-account.

1. Klik op **MPN-id toevoegen om de uitgever** aan de onderkant van de pagina te controleren. 

1. Voer uw **MPN-id** in. Deze MPN-ID moet gelden voor: 

    - Een geldig Microsoft Partner Network account dat het verificatie proces heeft voltooid.  

    - Het wereld wijde partner account (PGA) voor uw organisatie. 

1. Klik op **controleren en opslaan**. 

1. Wacht totdat de aanvraag is verwerkt. Dit kan enkele minuten duren. 

1. Als de verificatie is geslaagd, wordt het verificatie venster van de uitgever gesloten, waarna u terugkeert naar de Blade huis stijl. Er wordt een blauwe geverifieerde badge weer gegeven naast de **weergave naam** van uw geverifieerde Uitgever. 

1. Gebruikers die wordt gevraagd om toestemming te geven voor uw app, zien het badge bericht snel nadat u het proces hebt door lopen, hoewel het enige tijd kan duren voordat dit wordt gerepliceerd in het hele systeem. 

1. Test deze functionaliteit door u aan te melden bij uw toepassing en ervoor te zorgen dat de gecontroleerde badge wordt weer gegeven op het scherm voor toestemming. Als u bent aangemeld als een gebruiker die al toestemming heeft gegeven voor de app, kunt u de vraag = para meter voor *toestemming* query's gebruiken om een toestemming prompt af te dwingen. Deze para meter moet worden gebruikt voor het testen van alleen en nooit vastgelegd in de aanvragen van uw app.

1. Herhaal dit proces zo nodig voor eventuele extra apps waarvoor de badge moet worden weer gegeven. U kunt Microsoft Graph gebruiken om dit sneller te doen, en Power shell-cmdlets zijn binnenkort beschikbaar. Zie [micro soft API Graph-aanroepen](troubleshoot-publisher-verification.md#making-microsoft-graph-api-calls) voor meer informatie. 

Dat is alles. Laat het ons weten als u feedback hebt over het proces, de resultaten of de functie in het algemeen. 

## <a name="next-steps"></a>Volgende stappen
Lees de [informatie over probleem oplossing](troubleshoot-publisher-verification.md)als u problemen ondervindt.
