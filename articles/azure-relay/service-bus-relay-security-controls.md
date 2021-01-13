---
title: Beveiligings controles voor Azure Relay
description: In deze artikelen vindt u een controle lijst met ingebouwde beveiligings controles voor het evalueren van Azure Relay.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 5d55026bfb6e3d6fe955a540b7596a85707398d6
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133341"
---
# <a name="security-controls-for-azure-relay"></a>Beveiligings controles voor Azure Relay

In dit artikel worden de beveiligings besturings elementen gedocumenteerd die zijn ingebouwd in Azure Relay.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligingsmaatregelen | Ja/Nee | Notities | Documentatie |
|---|---|--|--|
| Ondersteuning voor privé-eind punten| Nee |  |   |
| Ondersteuning voor netwerk isolatie en firewalling| Nee |  |   |
| Ondersteuning voor geforceerde tunneling| N.v.t. | Relay is de TLS-tunnel  |   |

## <a name="monitoring--logging"></a>& logboek registratie controleren

| Beveiligingsmaatregelen | Ja/Nee | Notities| Documentatie |
|---|---|--|--|
| Ondersteuning voor Azure-bewaking (log Analytics, app Insights, enz.)| Ja | |   |
| Logboek registratie en controle op het vlak van controle en beheer| Ja | Via [Azure Resource Manager](../azure-resource-manager/index.yml). |   |
| Logboek registratie en controle van het gegevens vlak| Ja | Geslaagde/mislukte verbindingen en Logboeken.  |   |

## <a name="identity"></a>Identiteit

| Beveiligingsmaatregelen | Ja/Nee | Notities| Documentatie |
|---|---|--|--|
| Verificatie| Ja | Via SAS. | [Verificatie en autorisatie Azure Relay](relay-authentication-and-authorization.md) |
| Autorisatie|  Ja | Via SAS. | [Verificatie en autorisatie Azure Relay](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>Gegevensbescherming

| Beveiligingsmaatregelen | Ja/Nee | Notities | Documentatie |
|---|---|--|--|
| Versleuteling aan server zijde op rest: door micro soft beheerde sleutels |  N.v.t. | Relay is een WebSocket en er worden geen gegevens bewaard. |   |
| Versleuteling aan server zijde op rest: door de klant beheerde sleutels (BYOK) | Nee | Maakt gebruik van alleen micro soft TLS-certificaten.  |   |
| Versleuteling op kolom niveau (Azure Data Services)| N.v.t. | |   |
| Versleuteling in transit (zoals ExpressRoute-versleuteling, in VNet-versleuteling en VNet-VNet versleuteling)| Ja | Voor service is TLS vereist. |   |
| Versleutelde API-aanroepen| Ja | HTTPS. |


## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligingsmaatregelen | Ja/Nee | Notities| Documentatie |
|---|---|--|--|
| Ondersteuning voor configuratie beheer (versie van configuratie, enz.)| Ja | Via [Azure Resource Manager](../azure-resource-manager/index.yml).|   |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligings controles in Azure-Services](../security/fundamentals/security-controls.md).