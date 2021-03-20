---
title: AMQP 1,0-aanvraag/antwoord-bewerkingen in Azure Service Bus
description: In dit artikel wordt de lijst met AMQP bewerkingen op aanvraag/antwoord in Microsoft Azure Service Bus gedefinieerd.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: b845f4086ee1ac4fe868571c1754caf6d29b9021
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88064411"
---
# <a name="amqp-10-in-microsoft-azure-service-bus-request-response-based-operations"></a>AMQP 1,0 in Microsoft Azure Service Bus: bewerkingen op basis van aanvraag-respons

In dit artikel wordt de lijst met Microsoft Azure Service Bus bewerkingen op aanvraag/antwoord gedefinieerd. Deze informatie is gebaseerd op het werk concept AMQP-beheer versie 1,0.  
  
Voor een gedetailleerde AMQP 1,0-protocol handleiding op Wire-niveau, waarin wordt uitgelegd hoe Service Bus implementeert en bouwt op de OASIS AMQP Technical Specification, raadpleegt u de [AMQP 1,0 in azure service bus en Event hubs-protocol handleiding][AMQP 1,0 protocol]Guide.  
  
## <a name="concepts"></a>Concepten  
  
### <a name="entity-description"></a>Beschrijving van entiteit  

Een beschrijving van een entiteit verwijst naar een Service Bus klasse- [QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription), [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription)-klasse of [SubscriptionDescription-klasse](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) -object.  
  
### <a name="brokered-message"></a>Brokered bericht  

Vertegenwoordigt een bericht in Service Bus dat is toegewezen aan een AMQP-bericht. De toewijzing is gedefinieerd in de [Service Bus AMQP protocol Guide](service-bus-amqp-protocol-guide.md).  
  
## <a name="attach-to-entity-management-node"></a>Koppelen aan het knoop punt voor entiteits beheer  

Voor alle bewerkingen die in dit document worden beschreven, wordt een aanvraag/antwoord patroon gevolgd, wordt het bereik van een entiteit bepaald en moet er worden gekoppeld aan een knoop punt voor entiteits beheer.  
  
### <a name="create-link-for-sending-requests"></a>Koppeling maken voor het verzenden van aanvragen  

Hiermee maakt u een koppeling naar het beheer knooppunt voor het verzenden van aanvragen.  
  
```
requestLink = session.attach(
role: SENDER,
    target: { address: "<entity address>/$management" },
    source: { address: ""<my request link unique address>" }
)

```
  
### <a name="create-link-for-receiving-responses"></a>Koppeling maken voor het ontvangen van antwoorden  

Hiermee maakt u een koppeling voor het ontvangen van antwoorden van het beheer knooppunt.  
  
```
responseLink = session.attach(
role: RECEIVER,
    source: { address: "<entity address>/$management" }
    target: { address: "<my response link unique address>" }
)

```
  
### <a name="transfer-a-request-message"></a>Een aanvraag bericht overdragen  

Hiermee wordt een aanvraag bericht overgedragen.  
U kunt desgewenst een transactie status toevoegen voor bewerkingen die trans acties ondersteunen.

```
requestLink.sendTransfer(
        Message(
                properties: {
                        message-id: <request id>,
                        reply-to: "<my response link unique address>"
                },
                application-properties: {
                        "operation" -> "<operation>",
                }
        ),
        [Optional] State = transactional-state: {
                txn-id: <txn-id>
        }
)
```
  
### <a name="receive-a-response-message"></a>Een antwoord bericht ontvangen  

Hiermee wordt het antwoord bericht van de antwoord koppeling ontvangen.  
  
```
responseMessage = responseLink.receiveTransfer()
```
  
Het antwoord bericht bevindt zich in de volgende vorm:
  
```
Message(
properties: {
        correlation-id: <request id>
    },
    application-properties: {
            "statusCode" -> <status code>,
            "statusDescription" -> <status description>,
           },
)

```
  
### <a name="service-bus-entity-address"></a>Service Bus entiteits adres  

Service Bus entiteiten moeten als volgt worden geadresseerd:  
  
|Entiteitstype|Adres|Voorbeeld|  
|-----------------|-------------|-------------|  
|wachtrij|`<queue_name>`|`“myQueue”`<br /><br /> `“site1/myQueue”`|  
|onderwerp|`<topic_name>`|`“myTopic”`<br /><br /> `“site2/page1/myQueue”`|  
|abonnement|`<topic_name>/Subscriptions/<subscription_name>`|`“myTopic/Subscriptions/MySub”`|  
  
## <a name="message-operations"></a>Bericht bewerkingen  
  
### <a name="message-renew-lock"></a>Vergren deling voor berichten vernieuwen  

Breidt de vergren deling van een bericht uit op basis van de tijd die is opgegeven in de beschrijving van de entiteit.  
  
#### <a name="request"></a>Aanvraag  

Het aanvraag bericht moet de volgende toepassings eigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|bewerking|tekenreeks|Ja|`com.microsoft:renew-lock`|  
|`com.microsoft:server-timeout`|uint|Nee|Time-out van bewerking server in milliseconden.|  
  
 De hoofd tekst van het aanvraag bericht moet bestaan uit een AMQP sectie met een kaart met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|`lock-tokens`|matrix van uuid|Ja|De tokens voor bericht vergrendeling die moeten worden verlengd.|  

> [!NOTE]
> Lock-tokens zijn de `DeliveryTag` eigenschap van ontvangen berichten. Zie het volgende voor beeld in de [.NET SDK](https://github.com/Azure/azure-service-bus-dotnet/blob/6f144e91310dcc7bd37aba4e8aebd535d13fa31a/src/Microsoft.Azure.ServiceBus/Amqp/AmqpMessageConverter.cs#L336) om deze op te halen. Het token kan ook worden weer gegeven in de ' DeliveryAnnotations ' als ' x-opt-Lock-token ', maar dit is niet gegarandeerd en de `DeliveryTag` moet de voor keur hebben. 
> 
  
#### <a name="response"></a>Antwoord  

Het antwoord bericht moet de volgende toepassings eigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|statuscode|int|Ja|HTTP-antwoord code [RFC2616]<br /><br /> 200: OK – geslaagd, anders mislukt.|  
|statusDescription|tekenreeks|No|De beschrijving van de status.|  
  
De hoofd tekst van het antwoord moet bestaan uit een AMQP sectie met een kaart met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|verloop items|matrix van tijds tempel|Ja|Het token voor het vergren delen van berichten is nieuw dat overeenkomt met de tokens voor aanvraag vergrendeling.|  
  
### <a name="peek-message"></a>Bericht bekijken  

Bekijkt berichten zonder vergren deling.  
  
#### <a name="request"></a>Aanvraag  

Het aanvraag bericht moet de volgende toepassings eigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|bewerking|tekenreeks|Ja|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Nee|Time-out van bewerking server in milliseconden.|  
  
De hoofd tekst van het aanvraag bericht moet bestaan uit een **AMQP** sectie met een **kaart** met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|`from-sequence-number`|long|Ja|Volg nummer waarvan u wilt beginnen met bekijken.|  
|`message-count`|int|Ja|Het maximum aantal berichten dat moet worden weer geven.|  
  
#### <a name="response"></a>Antwoord  

Het antwoord bericht moet de volgende toepassings eigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|statuscode|int|Ja|HTTP-antwoord code [RFC2616]<br /><br /> 200: OK: heeft meer berichten<br /><br /> 204: geen inhoud: geen berichten meer|  
|statusDescription|tekenreeks|No|De beschrijving van de status.|  
  
De hoofd tekst van het antwoord moet bestaan uit een **AMQP** sectie met een **kaart** met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|berichten|lijst met kaarten|Ja|Lijst met berichten waarin elke kaart een bericht vertegenwoordigt.|  
  
De toewijzing die een bericht weergeeft, moet de volgende vermeldingen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|message|matrix van byte|Ja|AMQP 1,0 Wire-gecodeerd bericht.|  
  
### <a name="schedule-message"></a>Bericht plannen  

Hiermee worden berichten gepland. Deze bewerking biedt ondersteuning voor trans acties.
  
#### <a name="request"></a>Aanvraag  

Het aanvraag bericht moet de volgende toepassings eigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|bewerking|tekenreeks|Ja|`com.microsoft:schedule-message`|  
|`com.microsoft:server-timeout`|uint|Nee|Time-out van bewerking server in milliseconden.|  
  
De hoofd tekst van het aanvraag bericht moet bestaan uit een **AMQP** sectie met een **kaart** met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|berichten|lijst met kaarten|Ja|Lijst met berichten waarin elke kaart een bericht vertegenwoordigt.|  
  
De toewijzing die een bericht weergeeft, moet de volgende vermeldingen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|bericht-id|tekenreeks|Ja|`amqpMessage.Properties.MessageId` Als teken reeks|  
|sessie-id|tekenreeks|No|`amqpMessage.Properties.GroupId as string`|  
|partitie sleutel|tekenreeks|No|`amqpMessage.MessageAnnotations.”x-opt-partition-key"`|
|via-Partition-sleutel|tekenreeks|No|`amqpMessage.MessageAnnotations."x-opt-via-partition-key"`|
|message|matrix van byte|Ja|AMQP 1,0 Wire-gecodeerd bericht.|  
  
#### <a name="response"></a>Antwoord  

Het antwoord bericht moet de volgende toepassings eigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|statuscode|int|Ja|HTTP-antwoord code [RFC2616]<br /><br /> 200: OK – geslaagd, anders mislukt.|  
|statusDescription|tekenreeks|No|De beschrijving van de status.|  
  
De hoofd tekst van het antwoord moet bestaan uit een **AMQP** sectie met een kaart met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|Volg nummers|matrix van Long|Ja|Volg nummer van de geplande berichten. Volg nummer wordt gebruikt om te annuleren.|  
  
### <a name="cancel-scheduled-message"></a>Gepland bericht annuleren  

Hiermee worden geplande berichten geannuleerd.  
  
#### <a name="request"></a>Aanvraag  

Het aanvraag bericht moet de volgende toepassings eigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|bewerking|tekenreeks|Ja|`com.microsoft:cancel-scheduled-message`|  
|`com.microsoft:server-timeout`|uint|Nee|Time-out van bewerking server in milliseconden.|  
  
De hoofd tekst van het aanvraag bericht moet bestaan uit een **AMQP** sectie met een **kaart** met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|Volg nummers|matrix van Long|Ja|Volg nummers van geplande berichten die moeten worden geannuleerd.|  
  
#### <a name="response"></a>Antwoord  

Het antwoord bericht moet de volgende toepassings eigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|statuscode|int|Ja|HTTP-antwoord code [RFC2616]<br /><br /> 200: OK – geslaagd, anders mislukt.|  
|statusDescription|tekenreeks|No|De beschrijving van de status.|   
  
## <a name="session-operations"></a>Sessie bewerkingen  
  
### <a name="session-renew-lock"></a>Vergren deling van sessie vernieuwen  

Breidt de vergren deling van een bericht uit op basis van de tijd die is opgegeven in de beschrijving van de entiteit.  
  
#### <a name="request"></a>Aanvraag  

Het aanvraag bericht moet de volgende toepassings eigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|bewerking|tekenreeks|Ja|`com.microsoft:renew-session-lock`|  
|`com.microsoft:server-timeout`|uint|Nee|Time-out van bewerking server in milliseconden.|  
  
De hoofd tekst van het aanvraag bericht moet bestaan uit een **AMQP** sectie met een **kaart** met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|sessie-id|tekenreeks|Ja|Sessie-ID.|  
  
#### <a name="response"></a>Antwoord  

Het antwoord bericht moet de volgende toepassings eigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|statuscode|int|Ja|HTTP-antwoord code [RFC2616]<br /><br /> 200: OK: heeft meer berichten<br /><br /> 204: geen inhoud: geen berichten meer|  
|statusDescription|tekenreeks|No|De beschrijving van de status.|  
  
De hoofd tekst van het antwoord moet bestaan uit een **AMQP** sectie met een kaart met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|verval|tijdstempel|Ja|Nieuwe verval datum.|  
  
### <a name="peek-session-message"></a>Sessie bericht bekijken  

Bekijkt sessie berichten zonder vergren deling.  
  
#### <a name="request"></a>Aanvraag  

Het aanvraag bericht moet de volgende toepassings eigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|bewerking|tekenreeks|Ja|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Nee|Time-out van bewerking server in milliseconden.|  
  
De hoofd tekst van het aanvraag bericht moet bestaan uit een **AMQP** sectie met een **kaart** met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|van-Sequence-Number|long|Ja|Volg nummer waarvan u wilt beginnen met bekijken.|  
|bericht-aantal|int|Ja|Het maximum aantal berichten dat moet worden weer geven.|  
|sessie-id|tekenreeks|Ja|Sessie-ID.|  
  
#### <a name="response"></a>Antwoord  

Het antwoord bericht moet de volgende toepassings eigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|statuscode|int|Ja|HTTP-antwoord code [RFC2616]<br /><br /> 200: OK: heeft meer berichten<br /><br /> 204: geen inhoud: geen berichten meer|  
|statusDescription|tekenreeks|No|De beschrijving van de status.|  
  
De hoofd tekst van het antwoord moet bestaan uit een **AMQP** sectie met een kaart met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|berichten|lijst met kaarten|Ja|Lijst met berichten waarin elke kaart een bericht vertegenwoordigt.|  
  
 De toewijzing die een bericht weergeeft, moet de volgende vermeldingen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|message|matrix van byte|Ja|AMQP 1,0 Wire-gecodeerd bericht.|  
  
### <a name="set-session-state"></a>Sessie status instellen  

Hiermee stelt u de status van een sessie in.  
  
#### <a name="request"></a>Aanvraag  

Het aanvraag bericht moet de volgende toepassings eigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|bewerking|tekenreeks|Ja|`com.microsoft:set-session-state`|  
|`com.microsoft:server-timeout`|uint|Nee|Time-out van bewerking server in milliseconden.|  
  
De hoofd tekst van het aanvraag bericht moet bestaan uit een **AMQP** sectie met een **kaart** met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|sessie-id|tekenreeks|Ja|Sessie-ID.|  
|sessie status|byte matrix|Ja|Ondoorzichtige binaire gegevens.|  
  
#### <a name="response"></a>Antwoord  

Het antwoord bericht moet de volgende toepassings eigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|statuscode|int|Ja|HTTP-antwoord code [RFC2616]<br /><br /> 200: OK – geslaagd, anders mislukt|  
|statusDescription|tekenreeks|No|De beschrijving van de status.|  
  
### <a name="get-session-state"></a>Sessie status ophalen  

Hiermee wordt de status van een sessie opgehaald.  
  
#### <a name="request"></a>Aanvraag  

Het aanvraag bericht moet de volgende toepassings eigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|bewerking|tekenreeks|Ja|`com.microsoft:get-session-state`|  
|`com.microsoft:server-timeout`|uint|Nee|Time-out van bewerking server in milliseconden.|  
  
De hoofd tekst van het aanvraag bericht moet bestaan uit een **AMQP** sectie met een **kaart** met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|sessie-id|tekenreeks|Ja|Sessie-ID.|  
  
#### <a name="response"></a>Antwoord  

Het antwoord bericht moet de volgende toepassings eigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|statuscode|int|Ja|HTTP-antwoord code [RFC2616]<br /><br /> 200: OK – geslaagd, anders mislukt|  
|statusDescription|tekenreeks|No|De beschrijving van de status.|  
  
De hoofd tekst van het antwoord moet bestaan uit een **AMQP** sectie met een **kaart** met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|sessie status|byte matrix|Ja|Ondoorzichtige binaire gegevens.|  
  
### <a name="enumerate-sessions"></a>Sessies opsommen  

De sessies van een bericht entiteit inventariseren.  
  
#### <a name="request"></a>Aanvraag  

Het aanvraag bericht moet de volgende toepassings eigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|bewerking|tekenreeks|Ja|`com.microsoft:get-message-sessions`|  
|`com.microsoft:server-timeout`|uint|Nee|Time-out van bewerking server in milliseconden.|  
  
De hoofd tekst van het aanvraag bericht moet bestaan uit een **AMQP** sectie met een **kaart** met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|laatste-bijgewerkt-tijd|tijdstempel|Ja|Filter zo dat er alleen sessies worden bijgewerkt die na een bepaalde tijd zijn geüpdatet.|  
|skip|int|Ja|Een aantal sessies overs Laan.|  
|top|int|Ja|Het maximum aantal sessies.|  
  
#### <a name="response"></a>Antwoord  

Het antwoord bericht moet de volgende toepassings eigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|statuscode|int|Ja|HTTP-antwoord code [RFC2616]<br /><br /> 200: OK: heeft meer berichten<br /><br /> 204: geen inhoud: geen berichten meer|  
|statusDescription|tekenreeks|No|De beschrijving van de status.|  
  
De hoofd tekst van het antwoord moet bestaan uit een **AMQP** sectie met een **kaart** met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|skip|int|Ja|Aantal overgeslagen sessies als de status code 200 is.|  
|sessies-id's|tekenreeksmatrix|Ja|Matrix van sessie-Id's als de status code 200 is.|  
  
## <a name="rule-operations"></a>Regel bewerkingen  
  
### <a name="add-rule"></a>Regel toevoegen  
  
#### <a name="request"></a>Aanvraag  

Het aanvraag bericht moet de volgende toepassings eigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|bewerking|tekenreeks|Ja|`com.microsoft:add-rule`|  
|`com.microsoft:server-timeout`|uint|Nee|Time-out van bewerking server in milliseconden.|  
  
De hoofd tekst van het aanvraag bericht moet bestaan uit een **AMQP** sectie met een **kaart** met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|regel naam|tekenreeks|Ja|De naam van de regel, exclusief de namen van abonnementen en onderwerpen.|  
|regel-beschrijving|map|Ja|Beschrijving van de regel, zoals opgegeven in de volgende sectie.|  
  
De **regel naam** toewijzing moet de volgende vermeldingen bevatten, waarbij **SQL-filter** en **correlatie filter** elkaar wederzijds uitsluiten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|SQL-filter|map|Ja|`sql-filter`, zoals opgegeven in de volgende sectie.|  
|correlatie filter|map|Ja|`correlation-filter`, zoals opgegeven in de volgende sectie.|  
|SQL-regel actie|map|Ja|`sql-rule-action`, zoals opgegeven in de volgende sectie.|  
  
De SQL-filter toewijzing moet de volgende vermeldingen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|expressie|tekenreeks|Ja|Expressie voor SQL-filter.|  
  
De **correlatie filter** toewijzing moet ten minste één van de volgende vermeldingen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|correlation-id|tekenreeks|No||  
|bericht-id|tekenreeks|No||  
|tot|tekenreeks|No||  
|beantwoorden|tekenreeks|No||  
|label|tekenreeks|No||  
|sessie-id|tekenreeks|No||  
|antwoord-to-Session-ID|tekenreeks|No||  
|inhouds type|tekenreeks|No||  
|properties|map|Nee|Wordt toegewezen aan Service Bus [BrokeredMessage. Properties](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).|  
  
De toewijzing **SQL-regel-Action** moet de volgende vermeldingen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|expressie|tekenreeks|Ja|SQL-actie-expressie.|  
  
#### <a name="response"></a>Antwoord  

Het antwoord bericht moet de volgende toepassings eigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|statuscode|int|Ja|HTTP-antwoord code [RFC2616]<br /><br /> 200: OK – geslaagd, anders mislukt|  
|statusDescription|tekenreeks|No|De beschrijving van de status.|  
  
### <a name="remove-rule"></a>Regel verwijderen  
  
#### <a name="request"></a>Aanvraag  

Het aanvraag bericht moet de volgende toepassings eigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|bewerking|tekenreeks|Ja|`com.microsoft:remove-rule`|  
|`com.microsoft:server-timeout`|uint|Nee|Time-out van bewerking server in milliseconden.|  
  
De hoofd tekst van het aanvraag bericht moet bestaan uit een **AMQP** sectie met een **kaart** met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|regel naam|tekenreeks|Ja|De naam van de regel, exclusief de namen van abonnementen en onderwerpen.|  
  
#### <a name="response"></a>Antwoord  

Het antwoord bericht moet de volgende toepassings eigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|statuscode|int|Ja|HTTP-antwoord code [RFC2616]<br /><br /> 200: OK – geslaagd, anders mislukt|  
|statusDescription|tekenreeks|No|De beschrijving van de status.|  
  
### <a name="get-rules"></a>Regels ophalen

#### <a name="request"></a>Aanvraag

Het aanvraag bericht moet de volgende toepassings eigenschappen bevatten:

|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|bewerking|tekenreeks|Ja|`com.microsoft:enumerate-rules`|  
|`com.microsoft:server-timeout`|uint|Nee|Time-out van bewerking server in milliseconden.|  

De hoofd tekst van het aanvraag bericht moet bestaan uit een **AMQP** sectie met een **kaart** met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|top|int|Ja|Het aantal regels dat op de pagina moet worden opgehaald.|  
|skip|int|Ja|Het aantal regels dat moet worden overgeslagen. Definieert de begin index (+ 1) in de lijst met regels. | 

#### <a name="response"></a>Antwoord

Het antwoord bericht bevat de volgende eigenschappen:

|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|statuscode|int|Ja|HTTP-antwoord code [RFC2616]<br /><br /> 200: OK – geslaagd, anders mislukt|  
|regels| matrix van kaart|Ja|Matrix van regels. Elke regel wordt vertegenwoordigd door een kaart.|

Elk toewijzings item in de matrix bevat de volgende eigenschappen:

|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|regel-beschrijving|matrix van beschreven objecten|Ja|`com.microsoft:rule-description:list` met AMQP-code 0x0000013700000004| 

`com.microsoft.rule-description:list` is een matrix met objecten die worden beschreven. De matrix bevat het volgende:

|Index|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
| 0 | matrix van beschreven objecten | Ja | `filter` zoals hieronder is opgegeven. |
| 1 | matrix van beschreven object | Ja | `ruleAction` zoals hieronder is opgegeven. |
| 2 | tekenreeks | Ja | de naam van de regel. |

`filter` Dit kan een van de volgende typen zijn:

| Descriptor naam | Descriptor code | Waarde |
| --- | --- | ---|
| `com.microsoft:sql-filter:list` | 0x000001370000006 | SQL-filter |
| `com.microsoft:correlation-filter:list` | 0x000001370000009 | Correlatie filter |
| `com.microsoft:true-filter:list` | 0x000001370000007 | True-filter dat staat voor 1 = 1 |
| `com.microsoft:false-filter:list` | 0x000001370000008 | Onjuist filter vertegenwoordigt 1 = 0 |

`com.microsoft:sql-filter:list` is een beschreven matrix met de volgende opties:

|Index|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
| 0 | tekenreeks | Ja | SQL-filter expressie |

`com.microsoft:correlation-filter:list` is een beschreven matrix met de volgende opties:

|Index (indien aanwezig)|Waardetype|Inhoud van waarde|  
|---------|----------------|--------------|
| 0 | tekenreeks | Correlatie-id |
| 1 | tekenreeks | Bericht-ID |
| 2 | tekenreeks | Tot |
| 3 | tekenreeks | Beantwoorden |
| 4 | tekenreeks | Label |
| 5 | tekenreeks | Sessie-id |
| 6 | tekenreeks | Antwoord op sessie-ID|
| 7 | tekenreeks | Type inhoud |
| 8 | Kaart | Toewijzing van door toepassing gedefinieerde eigenschappen |

`ruleAction` Dit kan een van de volgende typen zijn:

| Descriptor naam | Descriptor code | Waarde |
| --- | --- | ---|
| `com.microsoft:empty-rule-action:list` | 0x0000013700000005 | Lege regel actie-er is geen regel actie aanwezig |
| `com.microsoft:sql-rule-action:list` | 0x0000013700000006 | SQL-regel actie |

`com.microsoft:sql-rule-action:list` is een matrix met beschreven objecten waarvan de eerste vermelding een teken reeks is die de expressie van de SQL-regel actie bevat.

## <a name="deferred-message-operations"></a>Uitgestelde bericht bewerkingen  
  
### <a name="receive-by-sequence-number"></a>Ontvangen op Volg nummer  

Hiermee worden uitgestelde berichten ontvangen op Volg nummer.  
  
#### <a name="request"></a>Aanvraag  

Het aanvraag bericht moet de volgende toepassings eigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|bewerking|tekenreeks|Ja|`com.microsoft:receive-by-sequence-number`|  
|`com.microsoft:server-timeout`|uint|Nee|Time-out van bewerking server in milliseconden.|  
  
De hoofd tekst van het aanvraag bericht moet bestaan uit een **AMQP** sectie met een **kaart** met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|Volg nummers|matrix van Long|Ja|Volg nummers.|  
|ontvanger-afrekenen-modus|ubyte|Ja|De verwerkings modus van de **ontvanger** zoals opgegeven in AMQP core v 1.0.|  
  
#### <a name="response"></a>Antwoord  

Het antwoord bericht moet de volgende toepassings eigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|statuscode|int|Ja|HTTP-antwoord code [RFC2616]<br /><br /> 200: OK – geslaagd, anders mislukt|  
|statusDescription|tekenreeks|No|De beschrijving van de status.|  
  
De hoofd tekst van het antwoord moet bestaan uit een **AMQP** sectie met een **kaart** met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|berichten|lijst met kaarten|Ja|Lijst met berichten waarin elke kaart een bericht vertegenwoordigt.|  
  
De toewijzing die een bericht weergeeft, moet de volgende vermeldingen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|Lock-token|uuid|Ja|Lock-token als dat `receiver-settle-mode` 1 is.|  
|message|matrix van byte|Ja|AMQP 1,0 Wire-gecodeerd bericht.|  
  
### <a name="update-disposition-status"></a>Status van de functie voor bijwerken  

Hiermee wordt de status van de verplaatste berichten bijgewerkt. Deze bewerking biedt ondersteuning voor trans acties.
  
#### <a name="request"></a>Aanvraag  

Het aanvraag bericht moet de volgende toepassings eigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|bewerking|tekenreeks|Ja|`com.microsoft:update-disposition`|  
|`com.microsoft:server-timeout`|uint|Nee|Time-out van bewerking server in milliseconden.|  
  
De hoofd tekst van het aanvraag bericht moet bestaan uit een **AMQP** sectie met een **kaart** met de volgende vermeldingen:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|toestand: status|tekenreeks|Ja|gevuld<br /><br /> geannuleerd<br /><br /> onderbroken|  
|Lock-tokens|matrix van uuid|Ja|Tokens voor bericht vergrendeling om de status van de toestand bij te werken.|  
|deadletter-reden|tekenreeks|No|Kan worden ingesteld als de status van de bestemming is ingesteld op **opgeschort**.|  
|deadletter-beschrijving|tekenreeks|No|Kan worden ingesteld als de status van de bestemming is ingesteld op **opgeschort**.|  
|eigenschappen-naar-Modify|map|Nee|Lijst met Service Bus brokered-bericht eigenschappen die moeten worden gewijzigd.|  
  
#### <a name="response"></a>Antwoord  

Het antwoord bericht moet de volgende toepassings eigenschappen bevatten:  
  
|Sleutel|Waardetype|Vereist|Inhoud van waarde|  
|---------|----------------|--------------|--------------------|  
|statuscode|int|Ja|HTTP-antwoord code [RFC2616]<br /><br /> 200: OK – geslaagd, anders mislukt|  
|statusDescription|tekenreeks|No|De beschrijving van de status.|

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie over AMQP en Service Bus naar de volgende koppelingen:

* [Overzicht van Service Bus AMQP]
* [AMQP 1.0-protocolhandleiding]
* [AMQP in Service Bus voor Windows Server]

[Overzicht van Service Bus AMQP]: service-bus-amqp-overview.md
[AMQP 1.0-protocolhandleiding]: service-bus-amqp-protocol-guide.md
[AMQP in Service Bus voor Windows Server]: /previous-versions/service-bus-archive/dn282144(v=azure.100)