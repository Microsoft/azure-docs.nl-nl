---
title: Poort instellingen Azure Relay | Microsoft Docs
description: Dit artikel bevat een tabel met een beschrijving van de vereiste configuratie voor poort waarden voor Azure Relay.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 97640debe81041ff7e2b082c6a9ac606d6088664
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85314269"
---
# <a name="azure-relay-port-settings"></a>Poort instellingen Azure Relay

In de volgende tabel wordt de vereiste configuratie voor poort waarden voor Azure Relay beschreven.

## <a name="hybrid-connections"></a>Hybride verbindingen

Hybride verbindingen maakt gebruik van websockets op poort 443 met TLS als onderliggend transport mechanisme dat alleen **https** gebruikt. 

## <a name="wcf-relays"></a>WCF-relays
  
|Binding|Transport beveiliging|Poort|  
|-------------|------------------------|----------|  
|[BasicHttpRelayBinding-klasse](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (client)|Ja|HTTPS| 
|" |Nee|HTTP|  
|[BasicHttpRelayBinding-klasse](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (Service)|Ofwel|9351/HTTP|  
|[NetEventRelayBinding-klasse](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (client)|Ja|9351/HTTPS|  
|" |Nee|9350/HTTP|  
|[NetEventRelayBinding-klasse](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (Service)|Ofwel|9351/HTTP|  
|[NetTcpRelayBinding-klasse](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (client/service)|Ofwel|5671/9352/HTTP (9352/9353 als u hybride gebruikt)|  
|[NetOnewayRelayBinding-klasse](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (client)|Ja|9351/HTTPS|  
|" |Nee|9350/HTTP|  
|[NetOnewayRelayBinding-klasse](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (Service)|Ofwel|9351/HTTP|  
|[WebHttpRelayBinding-klasse](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (client)|Ja|HTTPS|  
|" |Nee|HTTP|  
|[WebHttpRelayBinding-klasse](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (Service)|Ofwel|9351/HTTP|  
|[WS2007HttpRelayBinding-klasse](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (client)|Ja|HTTPS|  
|" |Nee|HTTP|  
|[WS2007HttpRelayBinding-klasse](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (Service)|Ofwel|9351/HTTP|

## <a name="next-steps"></a>Volgende stappen
Ga voor meer informatie over Azure Relay naar deze koppelingen:
* [Wat is Azure Relay?](relay-what-is-it.md)
* [Veelgestelde vragen over Relay](relay-faq.md)