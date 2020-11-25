---
title: bestand opnemen
description: bestand opnemen
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7d0286b63703c165dda6cd12bb625fc64272aac1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011752"
---
Azure Files biedt vier verschillende opslaglagen: premium, geoptimaliseerd voor transacties, dynamisch en statisch. Hiermee kunt u uw shares aanpassen aan de prestaties en prijsvereisten van uw scenario:

- **Premium**: Premium-bestandsshares worden ondersteund door SSD's (Solid-state drives) en worden geïmplementeerd in het **FileStorage-opslag account**. Premium-bestandsshares bieden consistente hoge prestaties en lage latentie in milliseconden voor de meeste IO-bewerkingen, voor IO-intensieve workloads. Premium-bestandsshares zijn geschikt voor een groot aantal werkbelastingen, zoals databases, hosting van websites en ontwikkelomgevingen. Premium-bestandsshares kunnen worden gebruikt in combinatie met SMB-protocollen (Server Message Block) en NFS-protocollen (Network File System).
- **Geoptimaliseerd voor transacties**: Voor transacties geoptimaliseerde bestandsshares maken werkbelastingen mogelijk met veel transacties die niet de latentie hebben van Premium bestandsshares. Voor transacties geoptimaliseerde bestandsshares worden aangeboden in de standaard opslaghardware van HDD's en geïmplementeerd in **opslagaccounts voor algemeen gebruik versie 2 (GPv2)** . Transactiegeoptimaliseerd is vaak 'Standaard' genoemd, hoewel dit verwijst naar de opslagmedia in plaats van de laag zelf (de dynamische en statische lagen zijn ook 'standaard' lagen, omdat ze zich in standaard opslaghardware bevinden).
- **Dynamisch**: Dynamische bestandsshares bieden opslag die is geoptimaliseerd voor scenario's met algemeen gebruik, bijvoorbeeld teamshares en Azure File Sync. Dynamische bestandsshares worden aangeboden in de standaard opslaghardware van HDD's en geïmplementeerd in **opslagaccounts voor algemeen gebruik versie 2 (GPv2)** .
- **Statisch**: Statische bestandsshares bieden kostenefficiënte opslag die is geoptimaliseerd voor online archieven. Azure File Sync past ook goed bij werkbelastingen met minder verloop. Statische bestandsshares worden aangeboden in de standaard opslaghardware van HDD's en geïmplementeerd in **opslagaccounts voor algemeen gebruik versie 2 (GPv2)** .

Premium bestandsshares zijn alleen beschikbaar in een ingericht factureringsmodel. Raadpleeg [Inzicht in inrichting voor premium bestandsshares](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares) voor meer informatie over ingerichte factureringsmodellen voor premium bestandsshares. Standaard bestandsshares, zoals geoptimaliseerde, dynamische en statische bestandsshares zijn beschikbaar bij betalen als u gaat factureren.

Dynamische en statische bestandsshares zijn beschikbaar in alle openbare Azure-regio's en Azure Government-regio's. Geoptimaliseerde bestandsshares zijn beschikbaar in alle Azure-regio's, met inbegrip van Azure China 21Vianet en Microsoft Azure Duitsland.

> [!Important]  
> U kunt bestandsshares verplaatsen tussen lagen binnen GPv2-opslagaccounttypen (geoptimaliseerd voor transacties, dynamisch en statisch). Als u shares tussen lagen verplaatst, worden er kosten in rekening gebracht: als u van een dynamischere laag verplaatst naar een statischere laag, worden er schrijfkosten in rekeningen gebracht in de statische laag voor elk bestand in de share, terwijl er van de statischere laag naar de dynamischere laag leeskosten in rekening worden gebracht voor de statische laag voor elk bestand in de share.