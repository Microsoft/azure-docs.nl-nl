---
title: Azure Service Bus met .NET en AMQP 1,0 | Microsoft Docs
description: In dit artikel wordt beschreven hoe u Azure Service Bus kunt gebruiken vanuit een .NET-toepassing met behulp van AMQP (Advanced Messa ging Queuing protocol).
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 20800363327aefda073cd484dc737b28e60466a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98632847"
---
# <a name="use-service-bus-from-net-with-amqp-10"></a>Gebruik Service Bus van .NET met AMQP 1,0

AMQP 1,0-ondersteuning is beschikbaar in de Service Bus pakket versie 2,1 of hoger. U kunt ervoor zorgen dat u de nieuwste versie hebt door de Service Bus bits te downloaden van [NuGet][NuGet].

> [!NOTE]
> U kunt Advanced Message Queueing Protocol (AMQP) of Service Bus Messa ging Protocol (SBMP) gebruiken met de .NET-bibliotheek voor Service Bus. AMQP is het standaard protocol dat door de .NET-bibliotheek wordt gebruikt. U wordt aangeraden het AMQP-Protocol (dit is de standaard instelling) te gebruiken en dit niet te overschrijven. 

## <a name="configure-net-applications-to-use-amqp-10"></a>.NET-toepassingen configureren voor het gebruik van AMQP 1,0

Standaard communiceert de Service Bus .NET-client bibliotheek met de Service Bus-service met behulp van het AMQP-protocol. U kunt ook expliciet AMQP als het transport type opgeven, zoals wordt weer gegeven in de volgende sectie. 

In de huidige versie zijn er een aantal API-functies die niet worden ondersteund bij het gebruik van AMQP. Deze niet-ondersteunde functies worden weer gegeven in de sectie [gedrags verschillen](#behavioral-differences). Sommige van de geavanceerde configuratie-instellingen hebben ook een andere betekenis wanneer AMQP wordt gebruikt.

### <a name="configuration-using-appconfig"></a>Configuratie met behulp van App.config

Het is een goede gewoonte dat toepassingen het App.config configuratie bestand gebruiken om instellingen op te slaan. Voor Service Bus toepassingen kunt u App.config gebruiken om de Service Bus connection string op te slaan. Een voor beeld App.config bestand is als volgt:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
    </appSettings>
</configuration>
```

De waarde van de `Microsoft.ServiceBus.ConnectionString` instelling is de Service Bus Connection String die wordt gebruikt voor het configureren van de verbinding met Service Bus. De indeling is als volgt:

`Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp`

Waar `namespace` en `SAS key` worden opgehaald van de [Azure Portal][Azure portal] wanneer u een service bus naam ruimte maakt. Zie [een service bus naam ruimte maken met behulp van de Azure Portal][Create a Service Bus namespace using the Azure portal]voor meer informatie.

Wanneer u AMQP gebruikt, voegt u de connection string toe met `;TransportType=Amqp` . Deze notatie geeft de client bibliotheek de opdracht om verbinding te maken met Service Bus met behulp van AMQP 1,0.

### <a name="amqp-over-websockets"></a>AMQP via WebSockets
Als u AMQP over websockets wilt gebruiken, stelt `TransportType` u in het Connection String in op `AmqpWebSockets` . Bijvoorbeeld: `Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=AmqpWebSockets`. 

Als u .NET Micro soft. Azure. ServiceBus-bibliotheek gebruikt, stelt u de [ServiceBusConnection. transport type](/dotnet/api/microsoft.azure.servicebus.servicebusconnection.transporttype) in op AmqpWebSockets van [transport type Enum](/dotnet/api/microsoft.azure.servicebus.transporttype).

Als u .NET Azure. Messa ging. ServiceBus-bibliotheek gebruikt, stelt u de [ServiceBusClient. transport type](/dotnet/api/azure.messaging.servicebus.servicebusclient.transporttype) in op AmqpWebSockets van [ServiceBusTransportType Enum](/dotnet/api/azure.messaging.servicebus.servicebustransporttype).


## <a name="message-serialization"></a>Bericht-serialisatie

Wanneer u het standaard protocol gebruikt, is het standaard gedrag van serialisatie van de .NET-client bibliotheek het gebruik van het [DataContractSerializer][DataContractSerializer] -type om een [BrokeredMessage][BrokeredMessage] -exemplaar te serialiseren voor Trans Port tussen de client bibliotheek en de service bus service. Bij gebruik van de AMQP-transport modus maakt de client bibliotheek gebruik van het AMQP-type systeem voor serialisatie van het [brokered-bericht][BrokeredMessage] in een AMQP-bericht. Met deze serialisatie kan het bericht worden ontvangen en geïnterpreteerd door een ontvangende toepassing die mogelijk wordt uitgevoerd op een ander platform, bijvoorbeeld een Java-toepassing die gebruikmaakt van de JMS-API voor toegang tot Service Bus.

Wanneer u een [BrokeredMessage][BrokeredMessage] -exemplaar bouwt, kunt u een .net-object als para meter voor de constructor opgeven om als hoofd tekst van het bericht te dienen. Voor objecten die kunnen worden toegewezen aan AMQP primitieve typen, wordt de hoofd tekst geserialiseerd in AMQP-gegevens typen. Als het object niet rechtstreeks kan worden toegewezen aan een AMQP primitief type. dat wil zeggen, een aangepast type dat is gedefinieerd door de toepassing, het object wordt geserialiseerd met behulp van de [DataContractSerializer][DataContractSerializer]en de geserialiseerde bytes worden verzonden in een AMQP-gegevens bericht.

Gebruik voor het vereenvoudigen van de interoperabiliteit met non-.NET-clients alleen .NET-typen die rechtstreeks kunnen worden geserialiseerd in AMQP-typen voor de hoofd tekst van het bericht. De volgende tabel bevat informatie over de typen en de bijbehorende toewijzing aan het AMQP-type systeem.

| Object type .NET-tekst | Type toegewezen AMQP | AMQP tekst sectie Type |
| --- | --- | --- |
| booleaans |booleaans |Waarde AMQP |
| DBCS |ubyte |Waarde AMQP |
| USHORT |USHORT |Waarde AMQP |
| uint |uint |Waarde AMQP |
| ULONG |ULONG |Waarde AMQP |
| sbyte |DBCS |Waarde AMQP |
| korte |korte |Waarde AMQP |
| int |int |Waarde AMQP |
| long |long |Waarde AMQP |
| float |float |Waarde AMQP |
| double |double |Waarde AMQP |
| decimal |decimal128 |Waarde AMQP |
| char |char |Waarde AMQP |
| DateTime |tijdstempel |Waarde AMQP |
| Guid |uuid |Waarde AMQP |
| byte [] |binair |Waarde AMQP |
| tekenreeks |tekenreeks |Waarde AMQP |
| System. Collections. IList |list |AMQP waarde: items in de verzameling kunnen alleen die in deze tabel zijn gedefinieerd. |
| System. matrix |matrix |AMQP waarde: items in de verzameling kunnen alleen die in deze tabel zijn gedefinieerd. |
| System. Collections. IDictionary |map |AMQP waarde: items in de verzameling kunnen alleen die in deze tabel zijn gedefinieerd. Opmerking: alleen teken reeks sleutels worden ondersteund. |
| URI |Beschrijving van de teken reeks (Zie de volgende tabel) |Waarde AMQP |
| Date time offset |Lange beschrijving (Zie de volgende tabel) |Waarde AMQP |
| TimeSpan |Lange beschrijving (Zie het volgende) |Waarde AMQP |
| Stream |binair |AMQP-gegevens (mogelijk meerdere). De gegevens secties bevatten de onbewerkte bytes die zijn gelezen van het Stream-object. |
| Ander object |binair |AMQP-gegevens (mogelijk meerdere). Bevat het geserialiseerde binaire bestand van het object dat gebruikmaakt van de DataContractSerializer of een serialisatiefunctie die door de toepassing wordt geleverd. |

| .NET-type | Type beschrijving van toegewezen AMQP | Notities |
| --- | --- | --- |
| URI |`<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>` |URI. AbsoluteUri |
| Date time offset |`<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` |Date time offset. UtcTicks |
| TimeSpan |`<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type>` |Time span. Ticks |

## <a name="behavioral-differences"></a>Gedrags verschillen

Er zijn enkele kleine verschillen in het gedrag van de Service Bus .NET API bij het gebruik van AMQP, vergeleken met het standaard Protocol:

* De eigenschap [OperationTimeout][OperationTimeout] wordt genegeerd.
* `MessageReceiver.Receive(TimeSpan.Zero)` wordt geïmplementeerd als `MessageReceiver.Receive(TimeSpan.FromSeconds(10))` .
* Het volt ooien van berichten door de vergrendelings tokens kan alleen worden uitgevoerd door de ontvangers van het bericht waarin de berichten voor het eerst zijn ontvangen.

## <a name="control-amqp-protocol-settings"></a>Instellingen voor AMQP-protocol bepalen

De [.net-api's](/dotnet/api/) bieden verschillende instellingen voor het beheren van het gedrag van het AMQP-Protocol:

* **[MessageReceiver. PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver.prefetchcount#Microsoft_ServiceBus_Messaging_MessageReceiver_PrefetchCount)**: Hiermee beheert u het eerste tegoed dat wordt toegepast op een koppeling. De standaardwaarde is 0.
* **[MessagingFactorySettings. AmqpTransportSettings. MaxFrameSize](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.maxframesize#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_MaxFrameSize)**: bepaalt de maximale AMQP frame grootte die tijdens de onderhandeling wordt aangeboden tijdens de open tijd van de verbinding. De standaard waarde is 65.536 bytes.
* **[MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.batchflushinterval#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_BatchFlushInterval)**: als de overdrachten batchgewijs zijn, bepaalt deze waarde de maximale vertraging voor het verzenden van disposities. Standaard overgenomen door afzenders/ontvangers. Individuele afzender/ontvanger kan de standaard waarde overschrijven, wat 20 milliseconden is.
* **[MessagingFactorySettings. AmqpTransportSettings. UseSslStreamSecurity](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.usesslstreamsecurity#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_UseSslStreamSecurity)**: bepaalt of AMQP-verbindingen tot stand worden gebracht via een TLS-verbinding. De standaard waarde is **True**.

## <a name="next-steps"></a>Volgende stappen

Wilt u meer weten? Ga naar de volgende koppelingen:

* [Overzicht van Service Bus AMQP]
* [AMQP 1.0-protocolhandleiding]

[Create a Service Bus namespace using the Azure portal]: service-bus-create-namespace-portal.md
[DataContractSerializer]: /dotnet/api/system.runtime.serialization.datacontractserializer
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory.acceptmessagesession#Microsoft_ServiceBus_Messaging_MessagingFactory_AcceptMessageSession
[OperationTimeout]: /dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout
[NuGet]: https://nuget.org/packages/WindowsAzure.ServiceBus/
[Azure portal]: https://portal.azure.com
[Overzicht van Service Bus AMQP]: service-bus-amqp-overview.md
[AMQP 1.0-protocolhandleiding]: service-bus-amqp-protocol-guide.md