---
title: Concepten-Azure Event Grid IoT Edge | Microsoft Docs
description: Concepten in Event Grid op IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 8314447e7d5d282eb428ec9316c4eef6844a7423
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98682376"
---
# <a name="event-grid-concepts"></a>Concepten binnen Event Grid

In dit artikel worden de belangrijkste concepten in Azure Event Grid beschreven.

## <a name="events"></a>Gebeurtenissen

Een gebeurtenis is de kleinste hoeveelheid informatie die in het systeem volledig beschrijft wat er is gebeurd. Elke gebeurtenis heeft algemene informatie, zoals: bron van de gebeurtenis, het tijdstip waarop het evenement plaatsvond en de unieke id. Elke gebeurtenis bevat ook specifieke informatie die alleen relevant is voor het specifieke gebeurtenis type. De ondersteuning voor een gebeurtenis met een grootte van Maxi maal 1 MB is momenteel als preview-versie beschikbaar.

Zie [Azure Event grid-gebeurtenis schema](event-schemas.md)voor de eigenschappen die in een gebeurtenis zijn opgenomen.

## <a name="publishers"></a>Uitgevers

Een uitgever is de gebruiker of organisatie die besluit gebeurtenissen te verzenden naar Event Grid. U kunt gebeurtenissen publiceren via uw eigen toepassing.

## <a name="event-sources"></a>Gebeurtenisbronnen

Een gebeurtenis bron is de plaats waar de gebeurtenis plaatsvindt. Elke gebeurtenisbron is gerelateerd aan één of meer gebeurtenistypen. Azure Storage is bijvoorbeeld de gebeurtenisbron bij door een blob gemaakte gebeurtenissen. Uw toepassing is de gebeurtenisbron bij aangepaste gebeurtenissen die u definieert. Gebeurtenisbronnen zijn verantwoordelijk voor het verzenden van gebeurtenissen naar Event Grid.

## <a name="topics"></a>Onderwerpen

Het event grid-onderwerp bevat een eind punt waar gebeurtenissen door de bron worden verzonden. De uitgever maakt het onderwerp Event grid en bepaalt of een gebeurtenis bron één onderwerp of meer dan één onderwerp nodig heeft. Er wordt een onderwerp gebruikt voor een verzameling van gerelateerde gebeurtenissen. Voor het reageren op bepaalde typen gebeurtenissen bepalen abonnees welke onderwerpen moeten worden geabonneerd.

Bij het ontwerpen van uw toepassing hebt u de flexibiliteit om te bepalen hoeveel onderwerpen u moet maken. Voor grote oplossingen maakt u een aangepast onderwerp voor elke categorie gerelateerde gebeurtenissen. Neem bijvoorbeeld een toepassing die gebeurtenissen over het bewerken van gebruikersaccounts en het verwerken van orders verzendt. De kans is klein dat er gebeurtenis-handlers zijn die beide gebeurteniscategorieën kunnen verwerken. Maak daarom twee aangepaste onderwerpen en laat gebeurtenis-handlers zich abonneren op het onderwerp dat hen interesseert. Voor kleine oplossingen kunt u de voor keur geven aan het verzenden van alle gebeurtenissen naar één onderwerp. Gebeurtenis abonnees kunnen filteren op de gewenste gebeurtenis typen.

Zie [rest API documentatie](api.md) over het beheren van onderwerpen in Event grid.

## <a name="event-subscriptions"></a>Gebeurtenisabonnementen

Met een abonnement wordt Event Grid welke gebeurtenissen in een onderwerp u wilt ontvangen. Wanneer u het abonnement maakt, geeft u een eind punt op voor het afhandelen van de gebeurtenis. U kunt de gebeurtenissen filteren die naar het eind punt worden verzonden. 

Zie [rest API documentatie](api.md) over het beheren van abonnementen in Event grid.

## <a name="event-handlers"></a>Event Handlers

Vanuit een Event Grid perspectief is een gebeurtenis-handler het punt waar de gebeurtenis wordt verzonden. De handler moet verdere actie ondernemen om de gebeurtenis te verwerken. Event Grid ondersteunt verschillende typen handlers. U kunt een ondersteunde Azure-service of uw eigen webhook gebruiken als handler. Afhankelijk van het type handler, worden Event Grid verschillende mechanismen gevolgd om de levering van de gebeurtenis te garanderen. Als de doel gebeurtenis-handler een HTTP-webhook is, wordt de gebeurtenis opnieuw geprobeerd totdat de handler de status code retourneert van `200 – OK` . Als de gebeurtenis wordt geleverd zonder uitzonde ring, wordt de hub als geslaagd beschouwd.

## <a name="security"></a>Beveiliging

Event Grid biedt beveiliging voor het abonneren op onderwerpen en het publiceren van onderwerpen. Zie [Event grid beveiliging en verificatie](security-authentication.md)voor meer informatie.

## <a name="event-delivery"></a>Gebeurtenisverzending

Als Event Grid niet kan bevestigen dat er een gebeurtenis is ontvangen door het eind punt van de abonnee, wordt de gebeurtenis opnieuw geleverd. Zie [Event grid aflevering van berichten en probeer het opnieuw](delivery-retry.md).

## <a name="batching"></a>Batchverwerking

Wanneer u een aangepast onderwerp gebruikt, moeten gebeurtenissen altijd in een matrix worden gepubliceerd. Voor scenario's met een lage door Voer geldt er slechts één waarde in de matrix. Voor grote hoeveel heden gebruik wordt u aangeraden meerdere gebeurtenissen per publicatie op te nemen voor een hogere efficiëntie. Batches kunnen Maxi maal 1 MB groot zijn. Elke gebeurtenis mag nog steeds niet groter zijn dan 1 MB (preview-versie).