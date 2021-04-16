---
title: Vergelijking op basis van functies van de Azure API Management-lagen | Microsoft Docs
description: Vergelijk API Management lagen op basis van de functies die ze bieden. Bekijk een tabel met een overzicht van de belangrijkste functies die beschikbaar zijn in elke prijscategorie.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 04/13/2021
ms.author: apimpm
ms.openlocfilehash: f111729d7d7707ed4f40ce8f89ce76975fb47400
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536454"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Vergelijking op basis van functies van de Azure API Management-lagen

Elke API Management [prijscategorie biedt](https://aka.ms/apimpricing) een afzonderlijke set functies en capaciteit per [eenheid.](api-management-capacity.md) De volgende tabel bevat een overzicht van de belangrijkste functies die beschikbaar zijn in elk van de lagen. Sommige functies werken mogelijk anders of hebben verschillende mogelijkheden, afhankelijk van de laag. In dergelijke gevallen worden de verschillen beschreven in de documentatieartikelen waarin deze afzonderlijke functies worden beschreven.

> [!IMPORTANT]
> Houd er rekening mee dat de Developer-laag is voor niet-productiegebruiksgevallen en evaluaties. Er wordt geen SLA aangeboden.

| Functie                                                                                      | Verbruik | Ontwikkelaar | Basic | Standard | Premium |
| -------------------------------------------------------------------------------------------- | ----------- | --------- | ----- | -------- | ------- |
| Azure<sup>AD-integratie 1</sup>                                                             | Nee          | Ja       | Nee    | Ja      | Ja     |
| Virtual Network (VNet)-ondersteuning                                                               | Nee          | Ja       | Nee    | Nee       | Ja     |
| Implementatie in meerdere regio's                                                                      | Nee          | Nee        | Nee    | Nee       | Ja     |
| Beschikbaarheidszones                                                                           | Nee          | Nee        | Nee    | Nee       | Ja     |
| Meerdere aangepaste domeinnamen                                                                 | Nee          | Ja        | Nee    | Nee       | Ja     |
| Ontwikkelaarsportal<sup>2</sup>                                                                 | Nee          | Ja       | Ja   | Ja      | Ja     |
| Ingebouwde cache                                                                               | Nee          | Ja       | Ja   | Ja      | Ja     |
| Ingebouwde analyse                                                                           | Nee          | Ja       | Ja   | Ja      | Ja     |
| [Zelf-hostende gateway](self-hosted-gateway-overview.md)<sup>3</sup>                           | Nee          | Ja       | Nee    | Nee       | Ja     |
| [TLS-instellingen](api-management-howto-manage-protocols-ciphers.md)                             | Ja         | Ja       | Ja   | Ja      | Ja     |
| [Externe cache](./api-management-howto-cache-external.md)                                                    | Ja         | Ja       | Ja   | Ja      | Ja     |
| [Verificatie van clientcertificaten](api-management-howto-mutual-certificates-for-clients.md) | Ja         | Ja       | Ja   | Ja      | Ja     |
| [Back-up en herstel](api-management-howto-disaster-recovery-backup-restore.md)               | Nee          | Ja       | Ja   | Ja      | Ja     |
| [Beheer via Git](api-management-configuration-repository-git.md)                        | Nee          | Ja       | Ja   | Ja      | Ja     |
| Directe beheer-API                                                                        | Nee          | Ja       | Ja   | Ja      | Ja     |
| Azure Monitor logboeken en metrische gegevens                                                               | Nee          | Ja       | Ja   | Ja      | Ja     |
| Statisch IP-adres                                                                                    | Nee          | Ja       | Ja   | Ja      | Ja     |

<sup>1</sup> Hiermee schakelt u het gebruik van Azure AD (en Azure AD B2C) in als een id-provider voor het aanmelden van gebruikers in de ontwikkelaarsportal.<br/>
<sup>2</sup> Inclusief gerelateerde functionaliteit, zoals gebruikers, groepen, problemen, toepassingen en e-mailsjablonen en meldingen.<br/>
<sup>3</sup> In de ontwikkelaarslaag zijn zelf-hostende gateways beperkt tot één gateway-knooppunt.<br/>