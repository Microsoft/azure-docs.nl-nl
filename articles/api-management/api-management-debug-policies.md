---
title: Fouten opsporen in azure API Management-beleid in Visual Studio code | Microsoft Docs
description: Meer informatie over het opsporen van fouten in azure API Management-beleid met behulp van de Azure API Management Visual Studio code extension
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/22/2020
ms.author: apimpm
ms.openlocfilehash: 2e45d1274cf7332dbca70eaa8fc51f0ac98e5359
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101648013"
---
# <a name="debug-azure-api-management-policies-in-visual-studio-code"></a>Fouten opsporen in azure API Management-beleid in Visual Studio code

[Beleids regels](api-management-policies.md) in azure API management bieden krachtige mogelijkheden die API-uitgevers helpen bij het oplossen van problemen, zoals verificatie, autorisatie, beperking, caching en trans formatie. Beleidsregels zijn een verzameling instructies die sequentieel worden uitgevoerd op de aanvraag of het antwoord van een API. 

In dit artikel wordt beschreven hoe u fouten opspoort in API Management-beleid met behulp [van de Azure API Management-extensie voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement). 

## <a name="prerequisites"></a>Vereisten

* Maak eerst een API Management ontwikkelaars-tier door deze [Quick](get-started-create-service-instance.md) start te volgen.

* Installeer [Visual Studio code](https://code.visualstudio.com/) en de nieuwste versie van de [Azure API Management-extensie voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement). 

* Importeer een API naar uw API Management-exemplaar. Zie voor beelden van [de stappen zelf studie: de API Management extensie voor Visual Studio code gebruiken om api's te importeren en te beheren](visual-studio-code-tutorial.md).

## <a name="restrictions-and-limitations"></a>Beperkingen en limieten

Deze functie is alleen beschikbaar in de Developer-laag van API Management. Elk API Management-exemplaar ondersteunt slechts één gelijktijdige foutopsporingssessie.

## <a name="initiate-a-debugging-session"></a>Een foutopsporingssessie starten

1. Visual Studio Code starten
2. Ga naar de uitbrei ding API Management onder Azure Extensions
3. Het API Management-exemplaar voor fout opsporing zoeken
4. De API en bewerking voor fout opsporing zoeken
5. Klik met de rechter muisknop op de bewerking en selecteer **fout opsporing in het opstart beleid**

Op dit moment probeert de uitbrei ding een sessie voor fout opsporing te initiëren en tot stand te brengen met de API Management Gateway.

![fout opsporing initiëren](media/api-management-debug-policies/initiate-debugging-session.png)

## <a name="send-a-test-request"></a>Een test aanvraag verzenden
Wanneer de foutopsporingssessie is ingesteld, wordt door de uitbrei ding een nieuwe editor geopend waarmee de HTTP-aanvraag voor de [rest-client extensie](https://marketplace.visualstudio.com/items?itemName=humao.rest-client)kan worden gemaakt en verzonden naar deze bewerking.

U ziet dat de header van de **fout opsporing van OCP-APIM** al is toegevoegd aan de aanvraag. Deze header is vereist en de waarde moet worden ingesteld op de abonnee sleutel voor alle toegang op service niveau om de fout opsporing in de API Management-Gateway te activeren.

Wijzig de HTTP-aanvraag in de editor volgens uw test scenario. Klik vervolgens op **aanvraag verzenden** om de test aanvraag naar de API Management-Gateway te verzenden.

![een test aanvraag verzenden](media/api-management-debug-policies/rest-client.png)

## <a name="debug-policies"></a>Fouten opsporen in beleid
Nadat de test-HTTP-aanvraag is verzonden, wordt in de uitbrei ding het venster fout opsporing geopend met het effectief beleid van deze bewerking en wordt het eerste effectief beleid gestopt. 

![beleid voor fout opsporing](media/api-management-debug-policies/main-window.png)

Als u de beleids pijplijn wilt volgen, kunt u een afzonderlijke stap door lopen of een onderbrekings punt op een beleid instellen en rechtstreeks naar dat beleid gaan. 

In het deel venster **variabelen** kunt u waarden controleren van door het systeem gemaakte en door de gebruiker gemaakte variabelen. In het paneel **onderbrekings punten** ziet u de lijst met alle onderbrekings punten die zijn ingesteld. In het deel venster **call stack** ziet u het huidige effectief beleids bereik. 

Als er een fout optreedt tijdens het uitvoeren van het beleid, ziet u de details van de fout in het beleid waar het is gebeurd. 

![uitzonderingen](media/api-management-debug-policies/exception.png)

> [!TIP]
> Vergeet niet om de foutopsporingssessie te sluiten door te klikken op de knop **stoppen** wanneer u klaar bent.


## <a name="next-steps"></a>Volgende stappen

+ Meer informatie over de [uitbrei ding van de API Management voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement). 
+ Problemen melden in de [github-opslag plaats](https://github.com/Microsoft/vscode-apimanagement)

