---
title: Netwerk toegangs beheer configureren
titleSuffix: Azure SignalR Service
description: Configureer netwerk toegangs beheer voor uw Azure signalerings service.
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: 24e56736e0d033420f9aaf976b0fb7d9727c1a5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92143231"
---
# <a name="configure-network-access-control"></a>Netwerk toegangs beheer configureren

Met de Azure signalerings service kunt u het toegangs niveau voor uw service-eind punt beveiligen en beheren op basis van het aanvraag type en de subset van netwerken die worden gebruikt. Wanneer netwerk regels zijn geconfigureerd, hebben alleen toepassingen die gegevens aanvragen via de opgegeven set netwerken toegang tot uw Azure signalerings service.

De Azure signalerings service heeft een openbaar eind punt dat toegankelijk is via internet. U kunt ook [privé-eind punten maken voor uw Azure signalerings service](howto-private-endpoints.md). Met een persoonlijk eind punt wordt een privé-IP-adres van uw VNet toegewezen aan de Azure Signalr-service en wordt alle verkeer tussen uw VNet en de Azure signalerings service via een persoonlijke verbinding beveiligd. Het netwerk toegangs beheer van de Azure Signalr-service biedt toegangs beheer voor zowel open bare eind punten als persoonlijke eind punten.

Desgewenst kunt u bepaalde typen aanvragen voor openbaar eind punt en elk persoonlijk eind punt toestaan of weigeren. U kunt bijvoorbeeld alle [Server verbindingen](signalr-concept-internals.md#server-connections) van het open bare eind punt blok keren en er zeker van zijn dat ze alleen afkomstig zijn van een specifiek VNet.

Een toepassing die toegang heeft tot een Azure signalerings service wanneer regels voor netwerk toegangs beheer actief zijn, vereist nog steeds juiste autorisatie voor de aanvraag.

## <a name="scenario-a---no-public-traffic"></a>Scenario A: geen openbaar verkeer

Als u al het open bare verkeer volledig wilt weigeren, moet u eerst de regel voor het open bare netwerk configureren om geen aanvraag type toe te staan. Vervolgens configureert u regels die toegang verlenen tot verkeer van specifieke VNets. Met deze configuratie kunt u een beveiligde netwerk grens maken voor uw toepassingen.

## <a name="scenario-b---only-client-connections-from-public-network"></a>Scenario B: alleen client verbindingen van een openbaar netwerk

In dit scenario kunt u de regel voor het open bare netwerk zodanig configureren dat alleen [client verbindingen](signalr-concept-internals.md#client-connections) van een openbaar netwerk worden toegestaan. U kunt vervolgens regels voor privé netwerken configureren om andere typen aanvragen die afkomstig zijn van een specifiek VNet, toe te staan. Deze configuratie verbergt uw app-servers van het open bare netwerk en brengt beveiligde verbindingen tot stand tussen uw app-servers en de Azure signalerings service.

## <a name="managing-network-access-control"></a>Beheer van netwerk toegangs beheer

U kunt netwerk toegangs beheer voor de Azure signalerings service beheren via de Azure Portal.

### <a name="azure-portal"></a>Azure Portal

1. Ga naar de Azure signalerings service die u wilt beveiligen.

1. Klik op het menu instellingen met de naam **netwerk toegangs beheer**.

    ![Netwerk toegangs beheer lijst op Portal](media/howto-network-access-control/portal.png)

1. Als u de standaard actie wilt bewerken, schakelt u de knop **toestaan/weigeren** in.

    > [!TIP]
    > De standaard actie is de actie die wordt uitgevoerd wanneer er geen regels voor de toegangs beheer lijst worden gevonden. Als de standaard actie bijvoorbeeld **weigeren** is, worden aanvraag typen die niet expliciet zijn goedgekeurd hieronder, geweigerd.

1. Als u de regel voor het open bare netwerk wilt bewerken, selecteert u toegestane typen aanvragen onder **openbaar netwerk**.

    ![Open bare netwerk toegangs beheer lijst in portal bewerken ](media/howto-network-access-control/portal-public-network.png)

1. Als u regels voor een privé-eindpunt netwerk wilt bewerken, selecteert u toegestane typen aanvragen in elke rij onder **particuliere endpoint-verbindingen**.

    ![De ACL voor een privé-eind punt op de portal bewerken ](media/howto-network-access-control/portal-private-endpoint.png)

1. Klik op **Opslaan** om uw wijzigingen toe te passen.

## <a name="next-steps"></a>Volgende stappen

Lees meer over [Azure Private Link](../private-link/private-link-overview.md).