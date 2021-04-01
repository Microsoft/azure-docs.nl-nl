---
title: bestand opnemen
titleSuffix: Azure
description: bestand opnemen
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: beffb2babefd86c2807e21e9337cba66f42fcfc2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "81678506"
---
Installeer en importeer de vereiste modules voordat u begint met de configuratie. U hebt Administrator-bevoegdheden nodig om modules in Power shell te installeren.

1. Installeer en importeer de AZ-module.
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. Installeer en importeer de AZ. peering-module.
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. Controleer of de modules correct zijn geïmporteerd met behulp van deze opdracht:
    ```powershell
    Get-Module
    ```
1. Meld u aan bij uw Azure-account met behulp van deze opdracht:
    ```powershell
    Connect-AzAccount
    ```
1. Controleer de abonnementen voor het account en selecteer het abonnement waarin u een peering wilt maken.
    ```powershell
    Get-AzSubscription
    Select-AzSubscription -SubscriptionId "subscription-id"
    ```
1. Als u nog geen resource groep hebt, moet u er een maken voordat u een peering maakt. U doet dit door de volgende opdracht uit te voeren:

    ```powershell
    New-AzResourceGroup -Name "PeeringResourceGroup" -Location "Central US"
    ```
> [!IMPORTANT]
> Als u uw ASN en abonnement nog niet hebt gekoppeld, volgt u de stappen in [peer-ASN koppelen](../howto-subscription-association-powershell.md). Deze actie is vereist om een peering aan te vragen.

> [!NOTE]
> De locatie van een resource groep is onafhankelijk van de locatie waar u voor het instellen van een peering kiest.
&nbsp;
