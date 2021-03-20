---
title: ASP.NET Core SignalR schalen met Azure SignalR
description: Een overzicht van het gebruik van Azure SignalR Service voor het schalen van ASP.NET Core SignalR-toepassingen.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 90853b5ff769b710c6c95e4f6e62b3a4aa19fadf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92151080"
---
# <a name="scale-aspnet-core-signalr-applications-with-azure-signalr-service"></a>ASP.NET Core SignalR-toepassingen schalen met Azure SignalR Service

## <a name="developing-signalr-apps"></a>SignalR-apps ontwikkelen

Er zijn momenteel [twee versies](/aspnet/core/signalr/version-differences) van de signaalr die u kunt gebruiken met uw webtoepassingen: signalr voor ASP.NET en ASP.net core signalr, de nieuwste versie. De service Azure SignalR is een beheerde Azure-service op basis van ASP.NET Core SignalR.

ASP.NET Core SignalR is gebaseerd op de vorige versie, maar is herschreven. ASP.NET Core SignalR is daardoor niet achterwaarts compatibel met de oudere versie van SignalR. De API's en het gedrag ervan zijn anders. De SDK van ASP.NET Core SignalR is bedoeld voor .NET Standard zodat u deze nog steeds kunt gebruiken in combinatie met .NET Framework. U moet dan wel de nieuwe API's gebruiken in plaats van de oude. Als u SignalR gebruikt en wilt overstappen op ASP.NET Core SignalR of de service Azure SignalR, moet u uw programmacode aanpassen aan de verschillen in de API's.

Bij de service Azure SignalR wordt het servergedeelte van ASP.NET Core SignalR gehost in Azure. Maar omdat de technologie is gebaseerd op ASP.NET Core, hebt u de mogelijkheid om uw werkelijke webtoepassing uit te voeren op meerdere platforms (Windows, Linux en Mac OS) terwijl deze wordt gehost in [Azure App Service](../app-service/overview.md), [IIS](/aspnet/core/host-and-deploy/iis/index), [Nginx](/aspnet/core/host-and-deploy/linux-nginx), [Apache](/aspnet/core/host-and-deploy/linux-apache) of [Docker](/aspnet/core/host-and-deploy/docker/index). U kunt de toepassing ook zelf hosten in uw eigen proces.

Als u met uw toepassing ondersteuning wilt bieden voor de meest recente functionaliteit voor het bijwerken van webclients met inhoud in realtime, en u de toepassing wilt uitvoeren op meerdere platforms (Azure, Windows, Linux en macOS) en wilt hosten in verschillende omgevingen, is Azure SignalR de beste keuze.

## <a name="why-not-deploy-signalr-myself"></a>Waarom zou ik SignalR niet zelf implementeren?

U kunt nog steeds uw eigen Azure-web-app met ondersteuning voor ASP.NET Core SignalR implementeren als onderdeel van de back-end van uw algemene webtoepassing.

Een van de belangrijkste redenen om de service Azure SignalR te gebruiken, is de eenvoud. Met de service Azure SignalR hoeft u zich niet te bekommeren om zaken als prestaties, schaalbaarheid en beschikbaarheid. Deze zaken worden voor u geregeld met een SLA van 99,9%.

WebSockets is ook de voorkeursmethode voor het bijwerken van inhoud in realtime. De taakverdeling van een groot aantal persistente WebSocket-verbindingen wordt echter een complexe puzzel als u de schaal wilt aanpassen. Veelgebruikte oplossingen hiervoor zijn taakverdeling via DNS, hardware en software. Dit probleem wordt automatisch voor u afgehandeld met de service Azure SignalR.

Een andere reden kan zijn dat u eigenlijk helemaal geen webtoepassing hoeft te hosten. De logica van uw webtoepassing is wellicht geschikt voor [serverloze computing](https://azure.microsoft.com/overview/serverless-computing/). Mogelijk wordt uw code alleen gehost en uitgevoerd op aanvraag met [Azure Functions](../azure-functions/index.yml)-triggers. Dit scenario kan lastig zijn omdat de code alleen op aanvraag wordt uitgevoerd en er geen lange verbindingen met clients worden onderhouden. Azure SignalR is geknipt voor deze situatie, omdat verbindingen al voor u worden beheerd via deze service. Zie het [overzicht van het gebruik van SignalR Service met Azure Functions](signalr-concept-azure-functions.md) voor meer informatie.

## <a name="how-does-it-scale"></a>Hoe werkt het schalen?

Het is gebruikelijk om SignalR te schalen met SQL Server, Azure Service Bus of Azure-cache voor Redis. De schaalmethode wordt voor u beheerd met de service Azure SignalR. De prestaties en kosten van deze service zijn vergelijkbaar met die van de andere services, maar dan zonder de complexiteit van het beheer van die services. U hoeft alleen maar het aantal eenheden voor uw service bij te werken. Elke eenheid ondersteunt maximaal 1000 clientverbindingen.

## <a name="next-steps"></a>Volgende stappen

* [Snelstart: Een chatruimte maken met Azure SignalR](signalr-quickstart-dotnet-core.md)