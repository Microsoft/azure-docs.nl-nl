---
title: Diagnostische logboeken voor Hybride verbindingen
description: Dit artikel bevat een overzicht van alle activiteiten en Diagnostische logboeken die beschikbaar zijn voor Azure Relay.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 7ca6dc95567a4867d3c58e0efad0fc74289e586c
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106079094"
---
# <a name="enable-diagnostics-logs-for-azure-relay-hybrid-connections"></a>Diagnostische logboeken inschakelen voor Azure Relay Hybride verbindingen
Wanneer u begint met het gebruik van uw Azure Relay Hybride verbindingen, wilt u misschien controleren hoe en wanneer uw listeners en afzenders worden geopend en gesloten, en hoe uw Hybride verbindingen worden gemaakt en berichten worden verzonden. Dit artikel bevat een overzicht van activiteiten en Diagnostische logboeken die worden geleverd door de Azure Relay-service. 

U kunt twee typen logboeken voor Azure Relay weer geven:

- [Activiteiten logboeken](../azure-monitor/essentials/platform-logs-overview.md): deze logboeken bevatten informatie over bewerkingen die worden uitgevoerd op uw naam ruimte in de Azure portal of via Azure Resource Manager sjabloon. Deze logboeken zijn altijd ingeschakeld. Bijvoorbeeld: "naam ruimte maken of bijwerken", "hybride verbinding maken of bijwerken". 
- [Diagnostische logboeken](../azure-monitor/essentials/platform-logs-overview.md): u kunt Diagnostische logboeken configureren voor een uitgebreidere weer gave van alles wat er gebeurt met bewerkingen en acties die worden uitgevoerd op uw naam ruimte met behulp van de API of via de taal-SDK.

## <a name="view-activity-logs"></a>Activiteitenlogboeken bekijken
Als u activiteiten logboeken voor uw Azure Relay-naam ruimte wilt weer geven, gaat u naar de pagina **activiteiten logboek** in de Azure Portal.

![Azure Relay-activiteiten logboek](./media/diagnostic-logs/activity-log.png)

## <a name="enable-diagnostic-logs"></a>Diagnostische logboeken inschakelen

> [!NOTE]
> Diagnostische logboeken zijn alleen beschikbaar voor de Hybride verbindingen, niet voor het door geven van de Windows Communication Foundation (WCF).

Voer de volgende stappen uit om Diagnostische logboeken in te scha kelen:

1. Ga in het [Azure Portal](https://portal.azure.com)naar uw Azure relay-naam ruimte en selecteer vervolgens **Diagnostische instellingen** onder **bewaking**.
1. Selecteer op de pagina **Diagnostische instellingen** de optie **Diagnostische instelling toevoegen**.  

   ![De koppeling diagnostische instelling toevoegen](./media/diagnostic-logs/add-diagnostic-setting.png)

1. Configureer de diagnostische instellingen door de volgende stappen uit te voeren:
    1. Voer in het vak **naam** een naam in voor de diagnostische instellingen.  
    2. Selecteer **HybridConnectionsEvent** voor het type logboek. 
    3. Selecteer een van de volgende drie **bestemmingen** voor uw Diagnostische logboeken:  
        1. Als u **archiveren naar een opslag account** selecteert, configureert u het opslag account waarin de diagnostische logboeken worden opgeslagen.  
        2. Als u **Stream naar een event hub** selecteert, configureert u de Event hub waarnaar u de diagnostische logboeken wilt streamen.
        3. Als u **verzenden naar log Analytics** selecteert, geeft u op van welk exemplaar van log Analytics de diagnostische gegevens worden verzonden.  

        ![Voor beeld van diagnostische instellingen](./media/diagnostic-logs/sample-diagnostic-settings.png)
1. Selecteer **Opslaan** op de werk balk om de instellingen op te slaan.

De nieuwe instellingen worden in ongeveer 10 minuten van kracht. De logboeken worden weer gegeven in het geconfigureerde archiverings doel in het deel venster **Diagnostische logboeken** . Zie het [overzicht van Azure Diagnostics-logboeken](../azure-monitor/essentials/platform-logs-overview.md)voor meer informatie over het configureren van diagnostische instellingen.


## <a name="schema-for-hybrid-connections-events"></a>Schema voor hybride verbindings gebeurtenissen
Hybride verbindingen gebeurtenis logboek JSON-teken reeksen bevatten de elementen die in de volgende tabel worden weer gegeven:

| Naam | Beschrijving |
| ------- | ------- |
| ResourceId | Resource-ID Azure Resource Manager |
| ActivityId: | Interne ID, gebruikt om de opgegeven bewerking te identificeren. Kan ook worden aangeduid als ' TrackingId ' |
| Eindpunt | Het adres van de relay-resource |
| OperationName | Het type van de Hybride verbindingen bewerking dat wordt geregistreerd |
| EventTimeString | De UTC-tijds tempel van de logboek record |
| Bericht | Het gedetailleerde bericht van de gebeurtenis |
| Categorie | De categorie van de gebeurtenis. Momenteel is er slechts `HybridConnectionsEvents` . 


## <a name="sample-hybrid-connections-event"></a>Gebeurtenis voor beeld hybride verbindingen
Hier volgt een voor beeld van Hybrid Connections-gebeurtenissen in JSON-indeling. 

```json
{
    "resourceId": "/SUBSCRIPTIONS/0000000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/MyResourceGroup/PROVIDERS/MICROSOFT.RELAY/NAMESPACES/MyRelayNamespace",
    "ActivityId": "7006a0db-27eb-445c-939b-ce86133014cc",
    "endpoint": "sb://myrelaynamespace.servicebus.windows.net/myhybridconnection/7006a0db-27eb-445c-939b-ce86133014cc_G5",
    "operationName": "Microsoft.Relay/HybridConnections/NewSenderRegistering",
    "EventTimeString": "2020-04-27T20:27:57.3842810Z",
    "message": "A new sender is registering.",
    "category": "HybridConnectionsEvent"
}
```

## <a name="events-and-operations-captured-in-diagnostic-logs"></a>Gebeurtenissen en bewerkingen vastgelegd in Diagnostische logboeken

| Bewerking                           | Beschrijving                                                     |
|-------------------------------------|-----------------------------------------------------------------|
| AuthorizationFailed                 | Autorisatie is mislukt.                                           |
| InvalidSasToken                     | Ongeldige SAS-token.                                              |
| ListenerAcceptingConnection         | De listener accepteert de verbinding.                           |
| ListenerAcceptingConnectionTimeout  | Er is een time-out opgetreden tijdens de listener die de verbinding heeft geaccepteerd.                |
| ListenerAcceptingHttpRequestFailed  | De door de listener geaccepteerde HTTP-aanvraag is mislukt vanwege een uitzonde ring. |
| ListenerAcceptingRequestTimeout     | Er is een time-out opgetreden tijdens het accepteren van de listener.                   |
| ListenerClosingFromExpiredToken     | De listener wordt gesloten omdat het beveiligings token is verlopen. |
| ListenerRejectedConnection          | De listener heeft de verbinding geweigerd.                       |
| ListenerReturningHttpResponse       | De listener retourneert een HTTP-antwoord.                     |
| ListenerReturningHttpResponseFailed | De listener retourneert een HTTP-antwoord met een fout code. |
| ListenerSentHttpResponse            | De Relay-service heeft een HTTP-antwoord ontvangen van de listener.  |
| ListenerUnregistered                | De registratie van de listener is ongedaan gemaakt.                                   |
| ListenerUnresponsive                | De listener reageert niet wanneer een antwoord wordt geretourneerd.         |
| MessageSendingToListener            | Het bericht wordt naar de listener verzonden.                              |
| MessageSentToListener               | Het bericht wordt verzonden naar de listener.                                    |
| NewListenerRegistered               | De nieuwe listener is geregistreerd.                                        |
| NewSenderRegistering                | De nieuwe afzender wordt geregistreerd.                                      |
| ProcessingRequestFailed             | De verwerking van een hybride verbindings bewerking is mislukt.     |
| SenderConnectionClosed              | De verbinding voor de afzender is gesloten.                                |
| SenderListenerConnectionEstablished | De verzender en listener hebben verbinding gemaakt.    |
| SenderSentHttpRequest               | De afzender heeft een HTTP-aanvraag verzonden.                                |


## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over Azure Relay raadpleegt u:

* [Inleiding tot Azure Relay](relay-what-is-it.md)
* [Aan de slag met hybride Relay-verbindingen](relay-hybrid-connections-dotnet-get-started.md)
