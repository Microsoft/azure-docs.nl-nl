---
title: bestand opnemen
description: bestand opnemen
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/19/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 55b545471cbe45fe35e28879270e4340cf9d3834
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028201"
---
Bij het implementeren van resources naar Azure hebt u enorm veel flexibiliteit bij het bepalen welke typen resources u wilt implementeren, waar ze zich bevinden en hoe u ze instelt. Deze flexibiliteit biedt mogelijk echter meer opties dan u in uw organisatie wilt toestaan. Wanneer u de implementatie van resources naar Azure overweegt, vraagt u zich misschien het volgende af:

* Hoe kan ik voldoen aan wettelijke vereisten voor de gegevenssoevereiniteit in bepaalde landen/regio's?
* Hoe kan ik de kosten in de hand houden?
* Hoe kan ik voorkomen dat iemand per ongeluk een kritisch systeem verandert?
* Hoe kan ik resourcekosten bijhouden en deze nauwkeurig factureren?

In dit artikel worden deze vragen behandeld. U kunt met name:

> [!div class="checklist"]
> * Gebruikers toewijzen aan rollen en de rollen toewijzen aan een bereik, zodat gebruikers gemachtigd zijn om verwachte acties uit te voeren, maar niet meer acties.
> * Beleidsregels toepassen die conventies voor resources in uw abonnement voorschrijven.
> * Resources vergrendelen die essentieel zijn voor uw systeem.
> * Tags toevoegen aan resources zodat u ze kunt bijhouden op basis van waarden die zinvol zijn voor uw organisatie.

Dit artikel is gericht op de taken voor het implementeren van governance. Zie [Governance in Azure](../articles/governance/index.yml) voor een uitgebreidere bespreking van de concepten.