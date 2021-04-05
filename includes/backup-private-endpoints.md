---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: fd7124ad83a446d7dde39a836c337a97b12a0a0a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95560260"
---
U kunt nu [persoonlijke eind punten](../articles/private-link/private-endpoint-overview.md) gebruiken om een back-up te maken van uw gegevens op een veilige manier van servers in een virtueel netwerk naar uw Recovery Services kluis. Het privé eindpunt gebruikt een IP-adres van de VNET-adresruimte voor uw kluis. Het netwerkverkeer tussen uw resources in het virtuele netwerk en de kluis loopt via het virtuele netwerk en een privé-koppeling in het Microsoft-backbonenetwerk. Dit voorkomt blootstelling aan het openbare internet. Privé-eind punten kunnen worden gebruikt voor het maken van back-ups en het herstellen van uw SQL-en SAP HANA-data bases die worden uitgevoerd in uw Azure-Vm's. Het kan ook worden gebruikt voor uw on-premises servers met behulp van de MARS-agent.

Voor Azure VM Backup is geen verbinding met Internet vereist en hiervoor zijn geen persoonlijke eind punten vereist om netwerk isolatie toe te staan.

Meer informatie over privé-eindpunten voor Azure Backup vindt u [hier](../articles/backup/private-endpoints.md).