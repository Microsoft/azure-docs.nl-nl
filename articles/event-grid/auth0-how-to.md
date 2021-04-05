---
title: Gebeurtenissen verzenden vanuit Auth0 naar Azure met Azure Event Grid
description: Gebeurtenissen van Auth0 naar Azure-Services beëindigen met Azure Event Grid.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: a66a60cb926b933a6b0628a67506d0d52ab7a905
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93077860"
---
# <a name="integrate-azure-event-grid-with-auth0"></a>Azure Event Grid integreren met Auth0

In dit artikel wordt beschreven hoe u uw Auth0-en Azure-accounts verbindt door een Event Grid partner onderwerp te maken.

Zie de [Auth0-gebeurtenis type codes](https://auth0.com/docs/logs/references/log-event-type-codes) voor een volledige lijst van de gebeurtenissen die door Auth0 worden ondersteund

## <a name="send-events-from-auth0-to-azure-event-grid"></a>Gebeurtenissen verzenden van Auth0 naar Azure Event Grid
Auth0-gebeurtenissen naar Azure verzenden:

1. Event Grid-resourceprovicer inschakelen
1. Een Auth0-partner onderwerp instellen in het Auth0-dash board
1. Het partner onderwerp in azure activeren
1. Abonneren op gebeurtenissen van Auth0

Zie Event Grid- [concepten](concepts.md)voor meer informatie over deze concepten.

### <a name="enable-event-grid-resource-provider"></a>Event Grid-resourceprovicer inschakelen
Tenzij u Event Grid eerder hebt gebruikt, moet u de Event Grid resource provider registreren. Als u Event Grid eerder hebt gebruikt, gaat u naar de volgende sectie.

In Azure Portal:
1. Selecteer in het menu links de optie abonnementen
1. Selecteer het abonnement dat u gebruikt voor Event Grid
1. Selecteer in het menu links onder instellingen de optie Resource providers
1. Micro soft. EventGrid zoeken
1. Selecteer Registreren
1. Vernieuwen om ervoor te zorgen dat de status wordt gewijzigd in geregistreerd

### <a name="set-up-an-auth0-partner-topic"></a>Een Auth0-partner onderwerp instellen
Een deel van het integratie proces is het instellen van Auth0 voor gebruik als een gebeurtenis bron (deze stap wordt uitgevoerd in uw [Auth0-dash board](https://manage.auth0.com/)).

1. Meld u aan bij het [Auth0-dash board](https://manage.auth0.com/).
1. Navigeer naar Logboeken > streams.
1. Klik op + stream maken.
1. Selecteer Azure Event Grid en voer een unieke naam in voor de nieuwe stroom.
1. Maak de gebeurtenis bron door uw Azure-abonnements-ID, Azure-regio en de naam van een resource groep op te geven. 
1. Klik op Opslaan.

### <a name="activate-your-auth0-partner-topic-in-azure"></a>Activeer uw Auth0-partner onderwerp in azure
Als u het onderwerp Auth0 in azure activeert, kunnen gebeurtenissen stromen van Auth0 naar Azure.

1. Meld u aan bij Azure Portal.
1. Zoek `Partner Topics` bovenaan en klik op `Event Grid Partner Topics` onder Services.
1. Klik op het onderwerp dat overeenkomt met de stroom die u hebt gemaakt in uw Auth0-dash board.
1. Bevestig dat het `Source` veld overeenkomt met uw Auth0-account.
1. Klik op Activeren.

### <a name="subscribe-to-auth0-events"></a>Abonneren op Auth0-gebeurtenissen

#### <a name="create-an-event-handler"></a>Een gebeurtenis-handler maken
Als u uw partner onderwerp wilt testen, moet u een gebeurtenis-handler hebben. Ga naar uw Azure-abonnement en stel een service in die wordt ondersteund als een [gebeurtenis-handler](event-handlers.md) , zoals een Azure- [functie](custom-event-to-function.md).

#### <a name="subscribe-to-your-auth0-partner-topic"></a>Abonneren op het onderwerp van uw Auth0-partner
Als u zich abonneert op het onderwerp van uw Auth0-partner, kunt u Event Grid waar u uw Auth0-evenementen naartoe wilt laten gaan.

1. Selecteer op de Blade partner onderwerp voor uw Auth0-integratie + gebeurtenis abonnement bovenaan.
1. Op de pagina gebeurtenis abonnement maken:
    1. Voer een naam in voor het gebeurtenisabonnement.
    1. Selecteer de Azure-service of webhook die u hebt gemaakt voor het type eind punt.
    1. Volg de instructies voor de betreffende service.
    1. Klik op Maken.

## <a name="testing"></a>Testen
De integratie van uw Auth0-partner onderwerp met Azure moet klaar zijn voor gebruik.

### <a name="verify-the-integration"></a>De integratie controleren
Controleren of de integratie werkt zoals verwacht:

1. Meld u aan bij het Auth0-dash board.
1. Navigeer naar Logboeken > streams.
1. Klik op uw Event Grid stream.
1. Klik eenmaal in de stroom op het tabblad status. De stroom moet actief zijn en zolang er geen fouten worden weer geven, werkt de stroom.

Probeer [een van de Auth0-acties aan te roepen die een gebeurtenis activeren die moet worden gepubliceerd](https://auth0.com/docs/logs/references/log-event-type-codes) om de gebeurtenissen stroom te bekijken.

## <a name="delivery-attempts-and-retries"></a>Bezorgings pogingen en nieuwe pogingen
Auth0-gebeurtenissen worden via een streaming-mechanisme aan Azure geleverd. Elke gebeurtenis wordt verzonden wanneer deze wordt geactiveerd in Auth0. Als Event Grid de gebeurtenis niet kunt ontvangen, zal Auth0 drie keer opnieuw proberen om de gebeurtenis te leveren. Anders wordt de fout bij het afleveren van het systeem door Auth0 geregistreerd.

## <a name="next-steps"></a>Volgende stappen

- [Auth0-partner onderwerp](auth0-overview.md)
- [Overzicht van de onderwerpen over partners](partner-events-overview.md)
- [Word een Event Grid partner](partner-onboarding-overview.md)