---
title: Publicatie handleiding voor container aanbiedingen in azure Marketplace
description: In dit artikel worden de vereisten beschreven voor het publiceren van container aanbiedingen in azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 11/30/2020
ms.openlocfilehash: 83c575aa40b80d9a8e39263e89a5e7860c8f8774
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95741658"
---
# <a name="publishing-guide-for-azure-container-offers"></a>Publicatie handleiding voor Azure container-aanbiedingen

Met Azure container kunt u uw container installatie kopie naar Azure Marketplace publiceren. Gebruik deze hand leiding om inzicht te krijgen in de vereisten voor dit aanbiedings type.

Azure container-aanbiedingen zijn transactie aanbiedingen die worden geïmplementeerd en gefactureerd via Azure Marketplace. De optie voor het weer geven van een gebruiker is ' nu ophalen '.

Gebruik het Azure container-aanbod type wanneer uw oplossing een docker-container installatie kopie is die is ingesteld als een Kubernetes Azure-container exemplaar.

> [!NOTE]
> Een Azure-container exemplaar is een run time docker-exemplaar dat de snelste en eenvoudigste manier biedt om een container in azure uit te voeren, zonder dat u virtuele machines hoeft te beheren en zonder dat u een service van een hoger niveau hoeft te gebruiken. Container instanties kunnen rechtstreeks worden geïmplementeerd in azure of worden gedistribueerd door Azure Kubernetes Services of Azure Kubernetes service engine.  

Micro soft biedt momenteel ondersteuning voor de BYOL-licentie modellen (gratis en meebrengen van eigen licentie).

## <a name="container-offer-requirements"></a>Vereisten voor container aanbod

| Vereiste | Details |  
|:--- |:--- |  
| Facturering en meting | Ondersteuning voor het gratis of BYOL facturerings model.<br><br> |  
| Installatie kopie die is gebouwd op basis van een Dockerfile | Container installatie kopieën moeten zijn gebaseerd op de specificatie van de docker-installatie kopie en zijn gebouwd op basis van een Dockerfile.<br> <br>Voor meer informatie over het bouwen van docker-installatie kopieën raadpleegt u de sectie ' gebruik ' van [Dockerfile Reference](https://docs.docker.com/engine/reference/builder/#usage).<br><br> |  
| Hosting in een Azure Container Registry opslagplaats | Container installatie kopieën moeten worden gehost in een Azure Container Registry opslag plaats.<br> <br>Voor meer informatie over het werken met Azure Container Registry raadpleegt [u Quick Start: een persoonlijk container register maken met behulp van de Azure Portal](../container-registry/container-registry-get-started-portal.md).<br><br> |  
| Afbeeldingen taggen | Container installatie kopieën moeten ten minste één tag bevatten (maximum aantal Tags: 16).<br><br>Zie de `docker tag` pagina op de documentatie site van [docker](https://docs.docker.com/engine/reference/commandline/tag) voor meer informatie over het coderen van een afbeelding.<br><br> |  

## <a name="next-steps"></a>Volgende stappen

- Zie [technische activa van Azure-container maken](create-azure-container-technical-assets.md)voor het voorbereiden van technische assets voor een container aanbieding.

- Als u een Azure-container aanbod wilt maken, raadpleegt u [een Azure-container aanbieding maken in azure Marketplace](create-azure-container-offer.md) voor meer informatie.
