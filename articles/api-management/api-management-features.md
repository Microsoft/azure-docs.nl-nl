---
title: Vergelijking op basis van functies van de Azure API Management-lagen | Microsoft Docs
description: Vergelijk API Management lagen op basis van de functies die ze bieden. Bekijk een tabel met een overzicht van de belangrijkste functies die beschikbaar zijn in elke prijs categorie.
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/15/2020
ms.author: apimpm
ms.openlocfilehash: eec913237ba978e52bb64fbd4c1f043a9214ffc8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92077844"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Vergelijking op basis van functies van de Azure API Management-lagen

Elke API Management [prijs categorie](https://aka.ms/apimpricing) biedt een afzonderlijke set functies en per eenheids [capaciteit](api-management-capacity.md). De volgende tabel bevat een overzicht van de belangrijkste functies die beschikbaar zijn in elk van de lagen. Sommige functies werken mogelijk anders of hebben verschillende mogelijkheden, afhankelijk van de laag. In dergelijke gevallen worden de verschillen genoemd in de documentatie artikelen waarin deze afzonderlijke functies worden beschreven.

> [!IMPORTANT]
> Let op: de Developer-laag is voor niet-productie gebruik en evaluaties. Het biedt geen SLA.

| Functie                                                                                      | Verbruik | Ontwikkelaar | Basic | Standard | Premium |
| -------------------------------------------------------------------------------------------- | ----------- | --------- | ----- | -------- | ------- |
| Azure AD-integratie<sup>1</sup>                                                             | Nee          | Ja       | Nee    | Ja      | Ja     |
| Ondersteuning voor Virtual Network (VNet)                                                               | Nee          | Ja       | Nee    | Nee       | Ja     |
| Implementatie in meerdere regio's                                                                      | Nee          | Nee        | Nee    | Nee       | Ja     |
| Meerdere aangepaste domein namen                                                                 | Nee          | Ja        | Nee    | Nee       | Ja     |
| Ontwikkelaars Portal<sup>2</sup>                                                                 | Nee          | Ja       | Ja   | Ja      | Ja     |
| Ingebouwde cache                                                                               | Nee          | Ja       | Ja   | Ja      | Ja     |
| Ingebouwde analyse                                                                           | Nee          | Ja       | Ja   | Ja      | Ja     |
| [Zelf-hostende gateway](self-hosted-gateway-overview.md)<sup>3</sup>                           | Nee          | Ja       | Nee    | Nee       | Ja     |
| [TLS-instellingen](api-management-howto-manage-protocols-ciphers.md)                             | Ja         | Ja       | Ja   | Ja      | Ja     |
| [Externe cache](./api-management-howto-cache-external.md)                                                    | Ja         | Ja       | Ja   | Ja      | Ja     |
| [Verificatie van client certificaten](api-management-howto-mutual-certificates-for-clients.md) | Ja         | Ja       | Ja   | Ja      | Ja     |
| [Back-up en herstel](api-management-howto-disaster-recovery-backup-restore.md)               | Nee          | Ja       | Ja   | Ja      | Ja     |
| [Beheer via git](api-management-configuration-repository-git.md)                        | Nee          | Ja       | Ja   | Ja      | Ja     |
| API voor direct beheer                                                                        | Nee          | Ja       | Ja   | Ja      | Ja     |
| Logboeken en metrische gegevens Azure Monitor                                                               | Ja         | Ja       | Ja   | Ja      | Ja     |
| Statisch IP-adres                                                                                    | Nee          | Ja       | Ja   | Ja      | Ja     |

<sup>1</sup> Hiermee maakt u het gebruik van Azure AD (en Azure AD B2C) als een id-provider voor gebruikers aanmelding op de ontwikkelaars Portal.<br/>
<sup>2</sup> inclusief gerelateerde functionaliteit, zoals gebruikers, groepen, problemen, toepassingen en e-mail sjablonen en-meldingen.<br/>
<sup>3</sup> in de zelf-hostende gateways van het ontwikkelaars niveau zijn beperkt tot één gateway-knoop punt.<br/>