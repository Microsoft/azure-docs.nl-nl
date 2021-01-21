---
title: Azure Relay uitzonde ringen en oplossingen oplossen | Microsoft Docs
description: Lijst met Azure Relay uitzonde ringen en voorgestelde acties die u kunt uitvoeren om ze te helpen oplossen.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 44eeba6eb7b8cfd4e81a923c2d9a3155f1709f2c
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98625145"
---
# <a name="azure-relay-exceptions"></a>Azure Relay uitzonde ringen

In dit artikel worden enkele uitzonde ringen vermeld die kunnen worden gegenereerd door de Azure Relay-Api's. Deze verwijzing is onderhevig aan wijzigingen. Controleer daarom opnieuw op updates.

## <a name="exception-categories"></a>Uitzonderings Categorieën

De relay-Api's genereren uitzonde ringen die in de volgende categorieën kunnen vallen. Ook worden de voorgestelde acties beschreven die u kunt uitvoeren om de uitzonde ringen op te lossen.

*   **Gebruikers coderings fout**: [System. ArgumentException](/dotnet/api/system.argumentexception), [System. InvalidOperationException](/dotnet/api/system.invalidoperationexception), [System. OperationCanceledException](/dotnet/api/system.operationcanceledexception), [System. runtime. serialisatie. SerializationException](/dotnet/api/system.runtime.serialization.serializationexception). 

    **Algemene actie**: Probeer de code te corrigeren voordat u doorgaat.
*   **Setup/configuratie fout**: [System. UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception). 

    **Algemene actie**: Controleer uw configuratie. Wijzig indien nodig de configuratie.
*   **Tijdelijke uitzonde ringen**: [micro soft. ServiceBus. Messa ging. MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [micro soft. ServiceBus. Messa ging. ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception), [micro soft. ServiceBus. Messa ging. MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). 

    **Algemene actie**: Probeer de bewerking opnieuw uit te voeren of gebruikers te informeren.
*   **Andere uitzonde ringen**: [System. Trans actions. TransactionException](/dotnet/api/system.transactions.transactionexception), [System. TimeoutException](/dotnet/api/system.timeoutexception). 

    **Algemene actie**: specifiek voor het uitzonderings type. Zie de tabel in de volgende sectie. 

## <a name="exception-types"></a>Uitzonderings typen

De volgende tabel bevat een lijst met uitzonderings typen van berichten en de oorzaken daarvan. Er worden ook aanbevolen acties beschreven die u kunt uitvoeren om de uitzonde ringen op te lossen.

| **Uitzonderingstype** | **Beschrijving** | **Voorgestelde actie** | **Opmerking bij automatische of directe nieuwe poging** |
| --- | --- | --- | --- |
| [Out](/dotnet/api/system.timeoutexception) |De server heeft niet binnen de opgegeven tijd gereageerd op de aangevraagde bewerking, die wordt beheerd door [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout). De server heeft mogelijk de aangevraagde bewerking voltooid. Dit kan gebeuren vanwege vertragingen in het netwerk of andere infra structuur. |Controleer de systeem status op consistentie en probeer het vervolgens opnieuw, indien nodig. Zie [TimeoutException](#timeoutexception). |In sommige gevallen kan het nodig zijn om het opnieuw te proberen. Voeg logica voor opnieuw proberen toe aan code. |
| [Ongeldige bewerking](/dotnet/api/system.invalidoperationexception) |De aangevraagde gebruikers bewerking is niet toegestaan in de server of de service. Zie het uitzonderings bericht voor meer informatie. |Controleer de code en de documentatie. Zorg ervoor dat de aangevraagde bewerking geldig is. |Opnieuw proberen wordt niet geholpen. |
| [Bewerking is geannuleerd](/dotnet/api/system.operationcanceledexception) |Er wordt geprobeerd een bewerking aan te roepen voor een object dat al is gesloten, afgebroken of verwijderd. In zeldzame gevallen is de ambient trans actie al verwijderd. |Controleer de code en zorg ervoor dat deze geen bewerkingen aanroept op een verwijderd object. |Opnieuw proberen wordt niet geholpen. |
| [Onbevoegde toegang](/dotnet/api/system.unauthorizedaccessexception) |Het [token provider](/dotnet/api/microsoft.servicebus.tokenprovider) -object kan geen token verkrijgen, het token is ongeldig of het token bevat niet de claims die vereist zijn om de bewerking uit te voeren. |Zorg ervoor dat de token provider is gemaakt met de juiste waarden. Controleer de configuratie van de Access Control-service. |In sommige gevallen kan het nodig zijn om het opnieuw te proberen. Voeg logica voor opnieuw proberen toe aan code. |
| [Argument uitzondering](/dotnet/api/system.argumentexception),<br /> [Argument null](/dotnet/api/system.argumentnullexception),<br />[Het argument valt buiten het bereik](/dotnet/api/system.argumentoutofrangeexception) |Een of meer van de volgende fout is opgetreden:<br />Een of meer argumenten die zijn opgegeven voor de methode, zijn ongeldig.<br /> De URI die is geleverd aan [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) of [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) bevat een of meer padsegmenten.<br />Het URI-schema dat is opgegeven aan [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) of [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) , is ongeldig. <br />De waarde van de eigenschap is groter dan 32 KB. |Controleer de aanroepende code en controleer of de argumenten juist zijn. |Opnieuw proberen wordt niet geholpen. |
| [Server bezet](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) |De aanvraag kan op dit moment niet door de service worden verwerkt. |De client kan wachten op een bepaalde tijd en vervolgens de bewerking opnieuw uitvoeren. |De client kan na een bepaald interval opnieuw proberen. Als een nieuwe poging resulteert in een andere uitzonde ring, controleert u het gedrag voor opnieuw proberen van deze uitzonde ring. |
| [Quotum overschreden](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |De bericht entiteit heeft de Maxi maal toegestane grootte bereikt. |Maak ruimte in de entiteit door berichten van de entiteit of de bijbehorende subwachtrijen te ontvangen. Zie [QuotaExceededException](#quotaexceededexception). |Opnieuw proberen kan helpen als er in de tussen tijd berichten zijn verwijderd. |
| [De bericht grootte is overschreden](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Een bericht lading overschrijdt de limiet van 256 KB. Houd er rekening mee dat de limiet van 256 KB de totale grootte van het bericht is. De totale bericht grootte kan systeem eigenschappen en eventuele Microsoft .NET overhead bevatten. |Verminder de grootte van de bericht lading en voer de bewerking opnieuw uit. |Opnieuw proberen wordt niet geholpen. |

## <a name="quotaexceededexception"></a>QuotaExceededException

[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) geeft aan dat een quotum voor een bepaalde entiteit is overschreden.

Voor relay verloopt deze uitzonde ring in het [System. service model. QuotaExceededException](/dotnet/api/system.servicemodel.quotaexceededexception), wat aangeeft dat het maximum aantal listeners voor dit eind punt is overschreden. Dit wordt aangegeven in de waarde **MaximumListenersPerEndpoint** van het uitzonderings bericht.

## <a name="timeoutexception"></a>TimeoutException
Een [TimeoutException](/dotnet/api/system.timeoutexception) geeft aan dat een door de gebruiker geïnitieerde bewerking langer duurt dan de time-out van de bewerking. 

Controleer de waarde van de eigenschap [ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) . Het bereiken van deze limiet kan ook een [TimeoutException](/dotnet/api/system.timeoutexception)veroorzaken.

Voor relay kunt u time-outuitzonderingen ontvangen wanneer u voor het eerst een verbinding voor een relay-afzender opent. Er zijn twee veelvoorkomende oorzaken voor deze uitzonde ring:

*   De open [time-](/previous-versions/) outwaarde is mogelijk te klein (als dit een fractie van een seconde is).
* Een on-premises relay-listener reageert mogelijk niet (of er kunnen problemen met de firewall regels optreden waardoor listeners geen nieuwe client verbindingen meer accepteren). de [Opentime-outwaarde](/previous-versions/) is minder dan ongeveer 20 seconden.

Voorbeeld:

```
'System.TimeoutException’: The operation did not complete within the allotted timeout of 00:00:10.
The time allotted to this operation may have been a portion of a longer timeout.
```

### <a name="common-causes"></a>Veelvoorkomende oorzaken
Er zijn twee veelvoorkomende oorzaken voor deze fout:

*   **Onjuiste configuratie**
    
    De time-out van de bewerking is mogelijk te klein voor de operationele voor waarde. De standaard waarde voor de time-out van de bewerking in de client-SDK is 60 seconden. Controleer of de waarde in de code is ingesteld op iets te klein. Houd er rekening mee dat het CPU-gebruik en de voor waarde van het netwerk invloed kunnen hebben op de tijd die nodig is om een bewerking te volt ooien. Het is een goed idee om de time-out voor de bewerking niet in te stellen op een zeer kleine waarde.
*   **Tijdelijke service fout**

    In sommige gevallen kan de Relay-service vertragingen ondervinden bij het verwerken van aanvragen. Dit kan bijvoorbeeld gebeuren tijdens peri Oden met veel verkeer. Als dit het geval is, voert u de bewerking na een vertraging opnieuw uit totdat de bewerking is voltooid. Als dezelfde bewerking blijft mislukken na meerdere pogingen, controleert u de [Azure-service status site](https://azure.microsoft.com/status/) om te zien of er sprake is van bekende service storingen.

## <a name="next-steps"></a>Volgende stappen
* [Veelgestelde vragen over Azure Relay](relay-faq.md)
* [Een relay-naam ruimte maken](relay-create-namespace-portal.md)
* [Aan de slag met Azure Relay en .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Aan de slag met Azure Relay en het knoop punt](relay-hybrid-connections-node-get-started.md)
