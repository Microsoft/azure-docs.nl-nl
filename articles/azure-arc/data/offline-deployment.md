---
title: Offline-implementatie
description: Met offline-implementatie kunt u container installatie kopieën uit een persoonlijk container register halen in plaats van uit de micro soft-Container Registry.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 5fa0b6ca41349d20614a64006536e78d8ee71844
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97955362"
---
# <a name="offline-deployment-overview"></a>Overzicht van offline-implementatie

Doorgaans worden de container installatie kopieën die worden gebruikt bij het maken van de Azure Arc-gegevens controller, de SQL Managed instances en de PostgreSQL grootschalige-Server groepen rechtstreeks opgehaald uit de micro soft-Container Registry (MCR). In sommige gevallen is de omgeving die u implementeert, geen verbinding met de micro soft-Container Registry.  In dit geval kunt u de container installatie kopieën ophalen met behulp van een computer, die toegang _heeft_ tot de micro soft-container Registry en deze vervolgens labelen en pushen naar een persoonlijk container register dat _kan worden_ gekoppeld vanuit de omgeving waarin u Azure Arc ingeschakelde gegevens Services wilt implementeren.

Omdat er maandelijkse updates worden gegeven voor Azure Arc-gegevens Services en er een groot aantal container installatie kopieën zijn, kunt u het beste dit proces voor het ophalen, labelen en het pushen van de container installatie kopieën naar een persoonlijk container register met behulp van een script uitvoeren.  Het script kan worden geautomatiseerd of hand matig worden uitgevoerd.

Een [voorbeeld script](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/scripts/pull-and-push-arc-data-services-images-to-private-registry.py) vindt u in de Azure Arc github-opslag plaats.

> [!NOTE]
> Dit script vereist de installatie van python en de [docker-cli](https://docs.docker.com/install/).

Het script vraagt om de volgende informatie.  Als u het script wilt uitvoeren zonder interactieve prompts, kunt u ook de bijbehorende omgevings variabelen instellen voordat u het script uitvoert.

|Vraag|Omgevings variabele|Notities|
|---|---|---|
|Geef een bron container register op en druk op ENTER voor het gebruik van `mcr.microsoft.com`|SOURCE_DOCKER_REGISTRY|Normaal gesp roken haalt u de installatie kopieën op uit de micro soft-Container Registry, maar als u deelneemt aan een persoonlijke Preview met een ander REGI ster, kunt u de informatie gebruiken die u als onderdeel van het preview-programma hebt gekregen.|
|Geef een bron container register opslagplaats op en druk op ENTER voor het gebruik van `arcdata` :|SOURCE_DOCKER_REPOSITORY|Als u de micro soft-Container Registry haalt, is de opslag plaats `arcdata` .|
|Geef een gebruikers naam op voor het bron container register. Druk op ENTER voor het gebruik van geen:|SOURCE_DOCKER_USERNAME|Geef alleen een waarde op als u container installatie kopieën wilt ophalen uit een bron waarvoor aanmelden is vereist.  Voor de micro soft-Container Registry is geen aanmelding vereist.|
|Geef een wacht woord op voor het bron container register. Druk op ENTER voor het gebruik van geen:|SOURCE_DOCKER_PASSWORD|Geef alleen een waarde op als u container installatie kopieën wilt ophalen uit een bron waarvoor aanmelden is vereist.  Voor de micro soft-Container Registry is geen aanmelding vereist. Dit is een gemaskeerde wachtwoord prompt.  Het wacht woord wordt niet weer geven als u het typt of plakt in.|
|Container installatie kopie-tag opgeven voor de installatie kopieën in de bron: druk op ENTER voor het gebruik van `<current monthly release tag>` :|SOURCE_DOCKER_TAG|De naam van de standaard label wordt maandelijks bijgewerkt, zodat deze overeenkomt met de maand en het jaar van de huidige release op de micro soft-Container Registry.|
|Geef de DNS-naam of het IP-adres van het doel container register op:|TARGET_DOCKER_REGISTRY|De DNS-naam of het IP-adres van het doel register.  Dit is het REGI _ster waarnaar de_ installatie kopieën worden gepusht.|
|Geef de register opslagplaats voor de doel container op:|TARGET_DOCKER_REPOSITORY|De opslag plaats in het doel register waarnaar de installatie kopieën worden gepusht.|
|Geef een gebruikers naam op voor het REGI ster van de doel container. Druk op ENTER voor het gebruik van geen:|TARGET_DOCKER_USERNAME|De gebruikers naam, indien aanwezig, die wordt gebruikt om u aan te melden bij het doel container register.|
|Geef een wacht woord op voor het doel container register. Druk op ENTER voor het gebruik van geen:|TARGET_DOCKER_PASSWORD|Het wacht woord, indien aanwezig, dat wordt gebruikt om u aan te melden bij het doel container register. Dit is een gemaskeerde wachtwoord prompt.  Het wacht woord wordt niet weer geven als u het typt of plakt in.|
|Geef een container afbeeldings label op voor de installatie kopieën op het doel:|TARGET_DOCKER_TAG|Normaal gesp roken gebruikt u dezelfde tag als de bron om Verwar ring te voor komen.|