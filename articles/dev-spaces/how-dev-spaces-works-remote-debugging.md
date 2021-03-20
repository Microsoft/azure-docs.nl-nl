---
title: Hoe kan ik de code op afstand opsporen met Azure dev Spaces?
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Beschrijft de processen voor externe fout opsporing op de Azure Kubernetes-service met Azure dev Spaces
keywords: Azure dev Spaces, dev Spaces, docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers
ms.openlocfilehash: 0487b80d23974a66bafe93ee1fbdf9b796d0ab53
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91975039"
---
# <a name="how-remote-debugging-your-code-with-azure-dev-spaces-works"></a>Hoe kan ik de code op afstand opsporen met Azure dev Spaces?

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Met Azure dev Spaces kunt u op verschillende manieren snel en probleem oplossing voor Kubernetes-toepassingen en samen werken met uw team op een AKS-cluster (Azure Kubernetes service). Zodra het project wordt uitgevoerd in een dev Space, biedt Azure dev Spaces een manier om een actieve toepassing in AKS te koppelen en fouten op te sporen.

In dit artikel wordt beschreven hoe externe fout opsporing met ontwikkel ruimten werkt.

## <a name="debug-your-code"></a>Fouten opsporen in uw code

Voor Java, .NET core en Node.js toepassingen kunt u fouten opsporen in uw toepassing die rechtstreeks wordt uitgevoerd in uw ontwikkel ruimte met Visual Studio code of Visual Studio. Visual Studio code en Visual Studio bieden hulp middelen om verbinding te maken met uw ontwikkel ruimte, uw toepassing te starten en een fout opsporingsprogramma te koppelen. Na `azds prep` het uitvoeren van kunt u het project openen in Visual Studio code of Visual Studio. Visual Studio code of Visual Studio genereert hun eigen configuratie bestanden om verbinding te maken die niet wordt uitgevoerd `azds prep` . Vanuit Visual Studio code of Visual Studio kunt u onderbrekings punten instellen en uw toepassing starten op uw dev-ruimte.

![Fout opsporing voor uw code](media/get-started-node/debug-configuration-nodejs2.png)

Wanneer u uw toepassing start met Visual Studio code of Visual Studio voor het opsporen van fouten, worden de start-en verbinding maken met uw ontwikkel ruimte op dezelfde manier uitgevoerd als met `azds up` . Daarnaast bieden het programma aan de client zijde in Visual Studio code en Visual Studio elk een extra para meter met specifieke informatie voor fout opsporing. De para meter bevat de naam van de afbeelding van het fout opsporingsprogramma, de locatie van het fout opsporingsprogramma in de installatie kopie van de fout opsporing en de doel locatie binnen de container van de toepassing om de map met fout opsporing te koppelen.

De afbeelding van het fout opsporingsprogramma wordt automatisch bepaald door het hulp programma aan de client zijde. Er wordt gebruikgemaakt van een methode die vergelijkbaar is met die in de Dockerfile-en helm-grafiek wordt gegenereerd wanneer deze wordt uitgevoerd `azds prep` . Nadat het fout opsporingsprogramma in de installatie kopie van de toepassing is gekoppeld, wordt het uitgevoerd met `azds exec` .

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe Azure Dev Spaces werkt.

> [!div class="nextstepaction"]
> [Hoe Azure Dev Spaces werkt](how-dev-spaces-works.md)
