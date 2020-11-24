---
title: Azure Service Bus berichten-wacht rijen, onderwerpen en abonnementen
description: Dit artikel bevat een overzicht van Azure Service Bus Messa ging-entiteiten (wacht rijen, onderwerpen en abonnementen).
ms.topic: article
ms.date: 11/04/2020
ms.openlocfilehash: 54b6a1fd2d4e8e5ef5bb6522374646257213e4b4
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95791610"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Service Bus-wachtrijen, -onderwerpen en -abonnementen
Azure Service Bus biedt ondersteuning voor een set Cloud, op berichten gebaseerde middleware-technologieën, waaronder betrouw bare Message Queuing en duurzame berichten over publiceren/abonneren. Deze Brokered Messaging mogelijkheden kunnen worden beschouwd als losgekoppelde berichten functies die ondersteuning bieden voor publiceren-abonneren, tijdelijke loskoppelingen en taakverdelings scenario's met behulp van de werk belasting van Service Bus berichten. Ontkoppelde communicatie heeft veel voor delen. Zo kunnen clients en servers zo nodig verbinding maken en hun bewerkingen op asynchrone wijze uitvoeren.

De Messa ging-entiteiten die de kern vormen van de mogelijkheden voor berichten in Service Bus zijn **wacht rijen**, **onderwerpen en abonnementen**, en regels/acties.

## <a name="queues"></a>Wachtrijen
Wacht rijen bieden de aflevering van het FIFO-bericht ( **First in, first out** ) aan een of meer concurrerende consumenten. Dat wil zeggen dat ontvangers berichten doorgaans ontvangen en verwerken in de volg orde waarin ze zijn toegevoegd aan de wachtrij. En slechts één bericht verbruiker ontvangt en verwerkt elk bericht. Een belang rijk voor deel van het gebruik van wacht rijen is het behalen **van tijdelijke ontkoppeling van toepassings onderdelen**. Met andere woorden, de producenten (afzenders) en consumenten (ontvangers) hoeven niet tegelijkertijd berichten te verzenden en te ontvangen. Dat komt doordat berichten worden opgeslagen blijvend in de wachtrij. Bovendien hoeft de producent niet te wachten op een antwoord van de gebruiker om berichten te kunnen verwerken en te verzenden.

Een gerelateerd voor deel is **belasting niveau**, waarmee producenten en consumenten berichten kunnen verzenden en ontvangen tegen verschillende tarieven. In veel toepassingen varieert de systeem belasting in de loop van de tijd. De verwerkings tijd die voor elke werk eenheid is vereist, is meestal echter constant. Door de verwerking van bericht producenten en consumenten met een wachtrij houdt dit in dat de verbruikte toepassing alleen de gemiddelde belasting moet kunnen verwerken in plaats van piek belasting. De lengte van de wachtrij neemt toe of af, al naargelang het binnenkomende verkeer. Deze functie bespaart direct geld met betrekking tot de hoeveelheid infra structuur die nodig is om de belasting van de toepassing te onderhouden. Naarmate de belasting toeneemt, kunnen er meer werk processen worden toegevoegd om uit de wachtrij te lezen. Elk bericht worden door slechts één van de werkprocessen verwerkt. Daarnaast kunt u met deze op pull gebaseerde taak verdeling het beste gebruik maken van de werk computers, zelfs als de worker-computers met het laden van Power pull-berichten aan hun eigen maximum snelheid. Dit patroon wordt vaak aangeduid als het **concurrerend consumenten**-patroon.

Door gebruik te maken van wacht rijen tussen bericht producenten en consumenten, biedt een inherente, soepele koppeling tussen de onderdelen. Omdat producenten en consumenten niet op de hoogte zijn van elkaar, kan een consument worden bijgewerkt zonder dat dit van invloed is op de producent.

### <a name="create-queues"></a>Wacht rijen maken
U kunt wacht rijen maken met behulp van de sjablonen [Azure Portal](service-bus-quickstart-portal.md), [Power shell](service-bus-quickstart-powershell.md), [cli](service-bus-quickstart-cli.md)en [Resource Manager](service-bus-resource-manager-namespace-queue.md). Verzend en ontvang vervolgens berichten met behulp van clients die zijn geschreven in [C#](service-bus-dotnet-get-started-with-queues.md), [Java](service-bus-java-how-to-use-queues.md), [python](service-bus-python-how-to-use-queues.md), [Java script](service-bus-nodejs-how-to-use-queues.md), [php](service-bus-php-how-to-use-queues.md)en [ruby](service-bus-ruby-how-to-use-queues.md). 

### <a name="receive-modes"></a>Ontvangst modi
U kunt twee verschillende modi opgeven waarin Service Bus berichten ontvangt: **ReceiveAndDelete** of **PeekLock**. Als Service Bus de aanvraag van de consument ontvangt in de [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) -modus, wordt het bericht gemarkeerd als verbruikt en wordt het naar de consumenten toepassing geretourneerd. Deze modus is het eenvoudigste model. Het werkt het beste voor scenario's waarin de toepassing een bericht niet verwerkt als er een fout optreedt. Als u meer inzicht wilt hebben in dit scenario, kunt u het beste een scenario waarin de consumer de ontvangst aanvraag uitgeeft, afhandelen voordat deze wordt verwerkt. Als Service Bus het bericht markeert als verbruikt, begint de toepassing met het gebruiken van berichten bij het opnieuw opstarten. Het bericht dat het heeft geconsumeerd voordat het vastloopt, wordt genegeerd.

In de [PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) -modus wordt de receive-bewerking twee fasen, waardoor het mogelijk is om toepassingen te ondersteunen die geen ontbrekende berichten kunnen verdragen. Wanneer Service Bus de aanvraag ontvangt, worden de volgende bewerkingen uitgevoerd:

1. Hiermee wordt het volgende bericht gezocht dat moet worden gebruikt.
1. Hiermee vergrendelt u het om te voor komen dat andere gebruikers het ontvangen.
1. Ga vervolgens terug naar de toepassing. 

Nadat de toepassing klaar is met de verwerking van het bericht of het op betrouw bare wijze opslaat voor toekomstige verwerking, wordt de tweede fase van het ontvangst proces voltooid door [`CompleteAsync`](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) het bericht aan te roepen. Wanneer Service Bus de **CompleteAsync** -aanvraag ontvangt, wordt het bericht gemarkeerd als verbruikt.

Als de toepassing het bericht om de een of andere reden niet kan verwerken, kan de [`AbandonAsync`](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) methode in het bericht worden aangeroepen (in plaats van [`CompleteAsync`](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) ). Met deze methode kan Service Bus het bericht ontgrendelen en het beschikbaar maken zodat het opnieuw kan worden ontvangen, hetzij door dezelfde consument hetzij door een andere concurrerende consument. Ten tweede is er een time-out gekoppeld aan de vergren deling. Als de toepassing het bericht niet kan verwerken voordat de time-out van de vergren deling is verlopen, wordt het bericht Service Bus ontgrendeld en wordt het beschikbaar gemaakt om opnieuw te worden ontvangen.

Als de toepassing vastloopt na het verwerken van het bericht, maar voordat het wordt aangeroepen [`CompleteAsync`](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) , service bus het bericht in de toepassing opnieuw afleveren wanneer het opnieuw wordt gestart. Dit proces wordt vaak **ten minste eenmaal** verwerkt. Dat wil zeggen dat elk bericht ten minste één keer wordt verwerkt. In bepaalde situaties kan hetzelfde bericht echter opnieuw worden bezorgd. Als uw scenario dubbele verwerking niet kan verdragen, voegt u extra logica toe aan uw toepassing om dubbele waarden te detecteren. U kunt dit doen met behulp van de eigenschap [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) van het bericht, dat constant blijft tijdens bezorgings pogingen. Deze functie wordt **precies eenmaal** verwerkt.

## <a name="topics-and-subscriptions"></a>Onderwerpen en abonnementen
Met een wachtrij kan een bericht worden verwerkt door één gebruiker. In tegens telling tot wacht rijen bieden onderwerpen en abonnementen een een-op-veel communicatie vorm in een **publicatie-en Abonneer** patroon. Het is handig om te schalen naar een groot aantal geadresseerden. Elk gepubliceerd bericht wordt beschikbaar gesteld voor elk abonnement dat is geregistreerd bij het onderwerp. Publisher verzendt een bericht naar een onderwerp en een of meer abonnees ontvangen een kopie van het bericht, afhankelijk van de filter regels die zijn ingesteld voor deze abonnementen. De abonnementen kunnen extra filters gebruiken om de berichten te beperken die ze willen ontvangen. Uitgevers verzenden berichten naar een onderwerp op dezelfde manier als wanneer ze berichten verzenden naar een wachtrij. Maar, consumenten ontvangen geen berichten rechtstreeks vanuit het onderwerp. In plaats daarvan ontvangen gebruikers berichten van abonnementen van het onderwerp. Een onderwerps abonnement lijkt op een virtuele wachtrij die kopieën ontvangt van de berichten die naar het onderwerp worden verzonden. Consumenten ontvangen berichten van een abonnement die identiek zijn met de manier waarop ze berichten ontvangen van een wachtrij.

De functionaliteit voor het verzenden van berichten van een wachtrij wijst rechtstreeks aan een onderwerp en de functionaliteit voor het ontvangen van berichten wordt toegewezen aan een abonnement. Deze functie betekent onder andere dat abonnementen ondersteuning bieden voor dezelfde patronen die eerder in deze sectie zijn beschreven, met betrekking tot wacht rijen: concurrerende consument, tijdelijke ontkoppeling, taak verdeling en taak verdeling.

### <a name="create-topics-and-subscriptions"></a>Onderwerpen en abonnementen maken
Het maken van een onderwerp is vergelijkbaar met het maken van een wachtrij, zoals beschreven in de vorige sectie. U kunt onderwerpen en abonnementen maken met behulp van de sjablonen [Azure Portal](service-bus-quickstart-topics-subscriptions-portal.md), [Power shell](service-bus-quickstart-powershell.md), [cli](service-bus-tutorial-topics-subscriptions-cli.md)en [Resource Manager](service-bus-resource-manager-namespace-topic.md). Verzend vervolgens berichten naar een onderwerp en ontvang berichten van abonnementen met behulp van clients die zijn geschreven in [C#](service-bus-dotnet-how-to-use-topics-subscriptions.md), [Java](service-bus-java-how-to-use-topics-subscriptions.md), [python](service-bus-python-how-to-use-topics-subscriptions.md), [Java script](service-bus-nodejs-how-to-use-topics-subscriptions.md), [php](service-bus-php-how-to-use-topics-subscriptions.md)en [ruby](service-bus-ruby-how-to-use-topics-subscriptions.md). 

### <a name="rules-and-actions"></a>Regels en acties
In veel gevallen moeten berichten met specifieke kenmerken op verschillende manieren worden verwerkt. Als u deze verwerking wilt inschakelen, kunt u abonnementen configureren om berichten te vinden die gewenste eigenschappen hebben en vervolgens bepaalde wijzigingen aan die eigenschappen uit te voeren. Hoewel Service Bus-abonnementen alle berichten zien die naar het onderwerp worden verzonden, kunt u alleen een subset van die berichten kopiëren naar de wachtrij voor virtuele abonnementen. Dit filter wordt uitgevoerd met behulp van abonnements filters. Dergelijke wijzigingen worden **filter acties** genoemd. Wanneer een abonnement is gemaakt, kunt u een filter expressie opgeven die op de eigenschappen van het bericht werkt. De eigenschappen kunnen zowel de systeem eigenschappen (zoals label) als aangepaste toepassings eigenschappen (bijvoorbeeld **Store** **naam**) zijn. In dit geval is de SQL-filter expressie optioneel. Zonder een SQL-filter expressie wordt elke filter actie die op een abonnement is gedefinieerd, uitgevoerd op alle berichten voor dat abonnement.

Zie voor een volledig werk voorbeeld het TopicSubscriptionWithRuleOperationsSample-voor [beeld](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/TopicSubscriptionWithRuleOperationsSample) op github.

Zie de documentatie voor de klassen [SqlFilter](/dotnet/api/microsoft.azure.servicebus.sqlfilter) en [SqlRuleAction](/dotnet/api/microsoft.azure.servicebus.sqlruleaction) voor meer informatie over mogelijke filter waarden.

## <a name="java-message-service-jms-20-entities-preview"></a>JMS (Java Message Service) 2,0-entiteiten (preview-versie)
De volgende entiteiten zijn toegankelijk via de JMS-API (Java Message Service) 2,0.

  * Tijdelijke wacht rijen
  * Tijdelijke onderwerpen
  * Gedeelde duurzame abonnementen
  * Niet-gedeelde duurzame abonnementen
  * Gedeelde niet-duurzame abonnementen
  * Niet-verdeelde niet-duurzame abonnementen

Meer informatie over de [JMS 2,0-entiteiten](java-message-service-20-entities.md) en over hoe u [deze kunt gebruiken](how-to-use-java-message-service-20.md).

## <a name="next-steps"></a>Volgende stappen

Zie de volgende geavanceerde onderwerpen voor meer informatie en voor beelden van het gebruik van Service Bus Messa ging:

* [Overzicht van Service Bus berichten](service-bus-messaging-overview.md)
* [Snelstart: Berichten verzenden en ontvangen met behulp van Azure Portal en .NET](service-bus-quickstart-portal.md)
* [Zelfstudie: Voorraad bijwerken met Azure Portal en onderwerpen/abonnementen](service-bus-tutorial-topics-subscriptions-portal.md)


