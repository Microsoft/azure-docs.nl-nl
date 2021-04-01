---
title: Logboeken bewaken met Azure Firewall werkmap
description: Azure Firewall werkmappen bieden een flexibel canvas voor Azure Firewall gegevens analyse en het maken van uitgebreide visuele rapporten in de Azure Portal.
services: firewall
author: gopimsft
ms.service: firewall
ms.topic: how-to
ms.date: 09/22/2020
ms.author: victorh
ms.openlocfilehash: e63336b0e84d303b51eda56f90ca6fb453d8ae0d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831838"
---
# <a name="monitor-logs-using-azure-firewall-workbook"></a>Logboeken bewaken met Azure Firewall werkmap

Azure Firewall werkmap biedt een flexibel canvas voor het Azure Firewall van gegevens analyse. U kunt deze gebruiken om uitgebreide visuele rapporten te maken in de Azure Portal. U kunt tikken op meerdere firewalls die zijn geïmplementeerd in Azure en deze combi neren in Unified Interactive-ervaringen.

U kunt inzicht krijgen in Azure Firewall gebeurtenissen, meer informatie over uw toepassing en netwerk regels, en statistische gegevens weer geven voor Firewall activiteiten in Url's, poorten en adressen. Azure Firewall werkmap kunt u uw firewalls en resource groepen filteren, en dynamisch filteren per categorie met gemakkelijk te lezen gegevens sets bij het onderzoeken van een probleem in uw logboeken. 

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u [Diagnostische logboek registratie inschakelen](firewall-diagnostics.md#enable-diagnostic-logging-through-the-azure-portal) via de Azure Portal. Lees ook [Azure firewall logboeken en metrische gegevens](logs-and-metrics.md) voor een overzicht van de diagnostische logboeken en metrische gegevens die beschikbaar zijn voor Azure firewall.

## <a name="get-started"></a>Aan de slag

Als u de werkmap wilt implementeren, gaat u naar [Azure monitor werkmap voor Azure firewall](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20Firewall/Workbook%20-%20Azure%20Firewall%20Monitor%20Workbook) en volgt u de instructies op de pagina. Azure Firewall werkmap is ontworpen om te werken in meerdere tenants, meerdere abonnementen en kan worden gefilterd op meerdere firewalls.

## <a name="overview-page"></a>Overzichtspagina

De overzichts pagina biedt een manier om te filteren op werk ruimten, tijd en firewalls. Er worden gebeurtenissen op tijd weer gegeven over firewalls en logboek typen (toepassing, netwerken, bedreigingen Intel, DNS-proxy).

:::image type="content" source="./media/firewall-workbook/firewall-workbook-overview.png" alt-text="Overzicht van Azure Firewall werkmap":::

## <a name="application-rule-log-statistics"></a>Logboek statistieken toepassings regels

Op deze pagina worden unieke bronnen van het IP-adres weer gegeven gedurende een periode, het gebruik van toepassings regels, de FQDN-limiet voor de tijd en gefilterde gegevens. U kunt gegevens filteren op basis van het IP-adres.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-application-rule.png" alt-text="Toepassings regel logboek Azure Firewall werkmap":::

## <a name="network-rule-log-statistics"></a>Statistieken voor het netwerk regel logboek

Deze pagina bevat een weer gave van de regel actie: toestaan/weigeren, doel poort op basis van IP en DNAT in de loop van de tijd. U kunt ook filteren op actie, poort en doel type.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-network-rule.png" alt-text="Regel logboek van Azure Firewall werkmap netwerk":::

U kunt Logboeken ook filteren op basis van tijd venster:

:::image type="content" source="./media/firewall-workbook/firewall-workbook-network-rule-time.png" alt-text="Logboek tijd venster Azure Firewall werkmap netwerk regel":::

## <a name="investigations"></a>Onderzoeken

U kunt de logboeken bekijken en meer informatie over de resource vinden op basis van het bron-IP-adres. U kunt informatie verkrijgen, zoals de naam van de virtuele machine en de naam van de netwerk interface. Het is eenvoudig om te filteren op de resource uit de logboeken.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-investigation.png" alt-text="Onderzoek naar Azure Firewall werkmap":::

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure firewall diagnostische gegevens](firewall-diagnostics.md)
