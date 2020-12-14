---
title: bestand opnemen
description: bestand opnemen
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/11/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 0056e18b6cb3aad2a4504bbe20b3b3421793489e
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97356261"
---
Deze voorbeelden laten zien hoe u Azure Monitor kunt gebruiken om waarschuwingen te maken voor abonnementen waarvoor het beheer van gedelegeerde Azure-resources is vrijgegeven.

| **Sjabloon** | **Beschrijving** |
|---------|---------|
| [monitor-delegation-changes](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) | Voert een query uit op activiteit van de afgelopen dag in een beheerde tenant en [rapporteert over toegevoegde of verwijderde delegaties](../articles/lighthouse/how-to/monitor-delegation-changes.md) (of pogingen die niet succesvol waren).|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/alert-using-actiongroup) | Met deze sjabloon maakt u een Azure-waarschuwing en maakt u verbinding met een bestaande actiegroep.|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/multiple-loganalytics-alerts) | Hiermee maakt u meerdere waarschuwingen voor een logboek op basis van Kusto-query's.|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegation-alert-for-customer) | Hiermee wordt een waarschuwing in een tenant geïmplementeerd wanneer een gebruiker een abonnement naar een beheertenant delegeert.|
| [workbook-activitylogs-by-domain](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/workbook-activitylogs-by-domain) | Hiermee worden Azure-activiteitenlogboeken in alle abonnementen weergegeven met een optie om te filteren op domeinnaam. |
