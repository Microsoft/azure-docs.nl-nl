---
title: Veelgestelde vragen over Azure SignalR Service
description: Krijg antwoord op veelgestelde vragen over Azure SignalR Service, waaronder probleemoplossing en typische gebruiksscenario's.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: c65bc7e92d925f819a48fd8ab9a8160bc3eb72e3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100579308"
---
# <a name="azure-signalr-service-faq"></a>Veelgestelde vragen over Azure SignalR Service

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Is Azure SignalR Service gereed voor gebruik in productieomgevingen?

Ja, de ondersteuning voor [ASP.NET Core SignalR](https://dotnet.microsoft.com/apps/aspnet/signalr) en [ASP.NET SignalR](/aspnet/signalr/overview/getting-started/introduction-to-signalr) is algemeen beschikbaar.

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>Als er meerdere toepassingsservers zijn, worden clientberichten dan naar alle servers of slechts één ervan verzonden?

Er is sprake van een één-op-één-toewijzing tussen client en een toepassingsserver. Berichten van één client worden altijd naar dezelfde toepassingsserver verzonden.

De toewijzing wordt gehandhaafd totdat de verbinding met de client of toepassingsserver wordt verbroken.

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>Als een van mijn toepassingsservers offline is, hoe kan ik deze dan vinden en hoe word ik erover geïnformeerd?

Azure SignalR Service controleert de heartbeats van toepassingsservers.
Als er binnen een opgegeven periode geen heartbeats worden ontvangen, wordt de toepassingsserver als offline beschouwd. De verbinding met alle clients die aan deze toepassingsserver zijn toegewezen, wordt verbroken.

## <a name="why-does-my-custom-iuseridprovider-throw-an-exception-when-im-switching-from-aspnet-core-signalr-sdk-to-azure-signalr-service-sdk"></a>Waarom genereert mijn aangepaste `IUserIdProvider` een uitzondering wanneer ik overschakel van de ASP.NET Core SignalR-SDK naar de Azure SignalR Service-SDK?

De parameter `HubConnectionContext context` is verschillend in de ASP.NET Core SignalR-SDK en de Azure SignalR Service-SDK wanneer `IUserIdProvider` wordt aangeroepen.

In ASP.NET Core SignalR is `HubConnectionContext context` de context van de fysieke clientverbinding met geldige waarden voor alle eigenschappen.

In de Azure SignalR Service-SDK is `HubConnectionContext context` de context van de logische clientverbinding. De fysieke client is verbonden met de Azure SignalR Service-instantie, zodat er slechts een beperkt aantal eigenschappen wordt geboden.

Op dit moment zijn alleen `HubConnectionContext.GetHttpContext()` en `HubConnectionContext.User` beschikbaar voor toegang.
U kunt [de broncode controleren](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs).

## <a name="can-i-configure-the-transports-available-in-azure-signalr-service-on-the-server-side-with-aspnet-core-signalr-for-example-can-i-disable-websocket-transport"></a>Kan ik de transporten die beschikbaar zijn in Azure SignalR Service aan serverzijde configureren met ASP.NET Core SignalR? Kan ik WebSocket-transport bijvoorbeeld uitschakelen?

Nee.

Azure SignalR Service levert standaard alle drie de transporten die ASP.NET Core SignalR ondersteunt. Dit kan niet worden geconfigureerd. Azure SignalR Service verwerkt verbindingen en transporten voor alle clientverbindingen.

U kunt transporten aan clientzijde configureren zoals wordt beschreven in [ASP.NET Core SignalR-configuratie](/aspnet/core/signalr/configuration#configure-allowed-transports-1).

## <a name="what-is-the-meaning-of-metrics-like-message-count-or-connection-count-shown-in-the-azure-portal-which-kind-of-aggregation-type-should-i-choose"></a>Wat is de betekenis van metrische gegevens, zoals het aantal berichten of het aantal verbindingen in Azure Portal? Welk soort aggregatie moet ik kiezen?

U vindt meer informatie over de berekening van deze metrische gegevens in [Berichten en verbindingen in Azure SignalR Service](signalr-concept-messages-and-connections.md).

In het overzichtsvenster met Azure SignalR Service-resources hebben we al het juiste aggregatietype voor u gekozen. Als u naar het deelvenster met metrische gegevens gaat, kunt u het aggregatietype meenemen naar [Berichten en verbindingen in Azure SignalR Service](../azure-monitor/essentials/metrics-supported.md#microsoftsignalrservicesignalr) als een referentie.

## <a name="what-is-the-meaning-of-the-default-serverless-and-classic-service-modes-how-can-i-choose"></a>Wat is de betekenis van de servicemodi `Default`, `Serverless` en `Classic`? Hoe kan ik kiezen?

Voor nieuwe toepassingen moeten alleen de standaardmodus en serverloze modus worden gebruikt. Het belangrijkste verschil is of u toepassingsservers hebt die serververbindingen met de service maken (bijvoorbeeld `AddAzureSignalR()` gebruiken om verbinding te maken met de service). Indien ja: gebruik de standaardmodus. Gebruik anders de serverloze modus.

De klassieke modus is ontworpen voor achterwaartse compatibiliteit met bestaande toepassingen en mag niet worden gebruikt voor nieuwe toepassingen.

Meer informatie over de servicemodus is te vinden in [dit document](concept-service-mode.md).

## <a name="can-i-send-message-from-client-in-serverless-mode"></a>Kan ik een bericht van de client verzenden in de serverloze modus?

U kunt een bericht van de client verzenden als u upstream configureert in uw SignalR-exemplaar. Upstream is een set eindpunten die berichten en verbindingsgebeurtenissen van de SignalR-service ontvangen. Als er geen upstream is geconfigureerd, worden berichten van de client genegeerd.

Raadpleeg [dit document](concept-upstream.md) voor meer informatie over upstream.

Upstream is momenteel in openbare preview.

## <a name="are-there-any-feature-differences-in-using-azure-signalr-service-with-aspnet-signalr"></a>Zijn er verschillen tussen de functies in Azure SignalR Service en ASP.NET SignalR?

Als u Azure SignalR Service gebruikt, worden sommige API's en functies van ASP.NET SignalR niet ondersteund:
- De mogelijkheid om een willekeurige status door te geven tussen clients en de hub (vaak aangeduid als `HubState`) wordt niet ondersteund.
- De klasse `PersistentConnection` wordt niet ondersteund.
- *Forever Frame Transport* wordt niet ondersteund.
- Azure SignalR Service speelt berichten die naar de client zijn verzonden niet langer af als de client offline is.
- Wanneer u Azure SignalR Service gebruikt, wordt het verkeer voor één clientverbinding altijd doorgestuurd (ook wel *tijdelijk* genoemd) naar één appserverinstantie voor de duur van de verbinding.

Ondersteuning voor ASP.NET SignalR is gericht op compatibiliteit. Daarom worden niet alle nieuwe functies van ASP.NET Core SignalR ondersteund. *MessagePack* en *Streaming* zijn bijvoorbeeld alleen beschikbaar voor ASP.NET Core SignalR-apps.

U kunt Azure SignalR Service configureren voor verschillende servicemodi: `Classic`, `Default` en `Serverless`. De modus `Serverless` wordt niet ondersteund voor ASP.NET. Ook de REST API voor gegevenslagen wordt niet ondersteund.

## <a name="where-does-my-data-reside"></a>Waar worden mijn gegevens opgeslagen?

Azure SignalR Service werkt als een gegevensverwerkingsservice. Er wordt standaard geen inhoud van klanten opgeslagen. Ook staat de gegevenslocatie vast. Als u Azure SignalR Service gebruikt in combinatie met andere Azure-services, zoals Azure Storage voor diagnostische gegevens, raadpleegt u [dit technische document](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) voor richtlijnen over het hanteren van gegevenslocaties in Azure-regio's.