---
title: Verificatie en autorisatie Azure Relay | Microsoft Docs
description: Dit artikel bevat een overzicht van Shared Access Signature (SAS)-verificatie met de Azure Relay-service.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 4b0a5c7a092155a006419eedd170a63abed42bb3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87033374"
---
# <a name="azure-relay-authentication-and-authorization"></a>Verificatie en autorisatie Azure Relay

Toepassingen kunnen worden geverifieerd bij Azure Relay met behulp van Shared Access Signature-verificatie (SAS). Met SAS-verificatie kunnen toepassingen bij de Azure Relay-service worden geverifieerd met behulp van een toegangs sleutel die is geconfigureerd op de relay-naam ruimte. U kunt deze sleutel vervolgens gebruiken om een Shared Access Signature token te genereren dat clients kunnen gebruiken om zich te verifiëren bij de Relay-service.

## <a name="shared-access-signature-authentication"></a>Shared Access Signature-verificatie

Met [SAS-verificatie](../service-bus-messaging/service-bus-sas.md) kunt u een gebruiker toegang verlenen tot Azure relay resources met specifieke rechten. SAS-verificatie omvat de configuratie van een cryptografische sleutel met bijbehorende rechten voor een bron. Clients kunnen vervolgens toegang krijgen tot deze bron door een SAS-token te presen teren, dat bestaat uit de bron-URI waartoe toegang wordt verkregen en een verloop datum dat met de geconfigureerde sleutel is ondertekend.

U kunt sleutels voor SAS op een relay-naam ruimte configureren. In tegens telling tot Service Bus-berichten ondersteunt [Relay hybride verbindingen](relay-hybrid-connections-protocol.md) niet-geautoriseerde of anonieme afzenders. U kunt anonieme toegang voor de entiteit inschakelen wanneer u deze maakt, zoals wordt weer gegeven in de volgende scherm afbeelding van de portal:

![Een dialoog venster met de titel ' hybride verbinding maken ' heeft een tekstvak ' naam ' en een selectie vakje ' client verificatie vereist ', dat is ingeschakeld.][0]

Als u SAS wilt gebruiken, kunt u een [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) -object configureren op een relay-naam ruimte die uit het volgende bestaat:

* De *naam* van de regel.
* *PrimaryKey* is een cryptografische sleutel die wordt gebruikt voor het ondertekenen/valideren van SAS-tokens.
* *Secundaire sleutel* is een cryptografische sleutel die wordt gebruikt voor het ondertekenen/valideren van SAS-tokens.
* *Rechten* die de verzameling van geluisterde, verzonden of beheerde rechten vertegenwoordigen.

Autorisatie regels die op het niveau van de naam ruimte zijn geconfigureerd, kunnen toegang verlenen tot alle relay-verbindingen in een naam ruimte voor clients met tokens die zijn ondertekend met behulp van de bijbehorende sleutel. Er kunnen Maxi maal 12 dergelijke autorisatie regels worden geconfigureerd op een relay-naam ruimte. Standaard wordt een [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) met alle rechten voor elke naam ruimte geconfigureerd wanneer deze voor het eerst wordt ingericht.

Voor toegang tot een entiteit vereist de client een SAS-token dat is gegenereerd met behulp van een specifieke [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Het SAS-token wordt gegenereerd met behulp van de HMAC-SHA256 van een resource teken reeks die bestaat uit de resource-URI waarvoor toegang wordt gevraagd en een verval datum met een cryptografische sleutel die is gekoppeld aan de autorisatie regel.

Ondersteuning voor SAS-verificatie voor Azure Relay is opgenomen in de Azure .NET SDK-versie 2,0 en hoger. SAS bevat ondersteuning voor een [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Alle Api's die een connection string als para meter accepteren, bieden ondersteuning voor SAS-verbindings reeksen.

## <a name="next-steps"></a>Volgende stappen

- Ga door met het lezen van [Service Bus verificatie met hand tekeningen voor gedeelde toegang](../service-bus-messaging/service-bus-sas.md) voor meer informatie over SAS.
- Raadpleeg de [hand leiding Azure Relay hybride verbindingen protocol](relay-hybrid-connections-protocol.md) voor gedetailleerde informatie over de hybride verbindingen mogelijkheid.
- Zie voor informatie over Service Bus Messa ging-verificatie en-autorisatie [Service Bus verificatie en autorisatie](../service-bus-messaging/service-bus-authentication-and-authorization.md). 

[0]: ./media/relay-authentication-and-authorization/hcanon.png