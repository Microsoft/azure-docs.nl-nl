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
ms.openlocfilehash: 6d06a46d2eaaad362890f1e3e44dbc746fa10898
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98633506"
---
Azure Files biedt vier verschillende opslaglagen: premium, geoptimaliseerd voor transacties, dynamisch en statisch. Hiermee kunt u uw shares aanpassen aan de prestaties en prijsvereisten van uw scenario:

- **Premium**: Premium-bestandsshares worden ondersteund door SSD's (solid-state drives) en bieden consistente hoge prestaties en lage latentie in milliseconden voor de meeste IO-bewerkingen, voor IO-intensieve workloads. Premium-bestandsshares zijn geschikt voor een groot aantal werkbelastingen, zoals databases, hosting van websites en ontwikkelomgevingen. Premium-bestandsshares kunnen worden gebruikt in combinatie met SMB-protocollen (Server Message Block) en NFS-protocollen (Network File System).
- **Geoptimaliseerd voor transacties**: Voor transacties geoptimaliseerde bestandsshares maken werkbelastingen mogelijk met veel transacties die niet de latentie hebben van Premium bestandsshares. Voor transactie geoptimaliseerde bestandsshares worden aangeboden voor standaard opslaghardware die wordt ondersteund door harde schijven. Transactiegeoptimaliseerd is vaak 'Standaard' genoemd, hoewel dit verwijst naar de opslagmedia in plaats van de laag zelf (de dynamische en statische lagen zijn ook 'standaard' lagen, omdat ze zich in standaard opslaghardware bevinden).
- **Dynamisch**: Dynamische bestandsshares bieden opslag die is geoptimaliseerd voor scenario's met algemeen gebruik, bijvoorbeeld teamshares. Dynamische bestandsshares worden aangeboden via de standaardopslag die worden ondersteund door HDD's.
- **Statisch**: Statische bestandsshares bieden kostenefficiënte opslag die is geoptimaliseerd voor online archieven. Statische bestandsshares worden aangeboden via de standaardopslag die worden ondersteund door HDD's.

Premium bestandsshares worden geïmplementeerd in het **FileStorage-opslagaccount** en zijn alleen beschikbaar in een ingericht factureringsmodel. Raadpleeg [Inzicht in inrichting voor premium bestandsshares](../articles/storage/files/understanding-billing.md#provisioned-model) voor meer informatie over ingerichte factureringsmodellen voor premium bestandsshares. Standaard bestandsshares, zoals geoptimaliseerde, dynamische en statische bestandsshares, worden geïmplementeerd in het **opslagaccount voor algemeen gebruik versie 2 (GPv2)** en zijn beschikbaar bij betalen als u gaat factureren. Dynamische en statische bestandsshares zijn beschikbaar in alle openbare Azure-regio's en Azure Government-regio's. Geoptimaliseerde bestandsshares zijn beschikbaar in alle Azure-regio's, met inbegrip van Azure China 21Vianet en Microsoft Azure Duitsland.

Wanneer u een opslaglaag selecteer voor uw werkbelasting. kunt u uw prestatie- en gebruiksvereisten overwegen. Als voor uw werkbelasting een hogere latentie van één cijfer nodig is, of als u SSD-opslagmedia on-premises gebruikt, is de premium laag waarschijnlijk de beste keuze. Als lage latentie geen probleem is, bijvoorbeeld met teamshares die on-premises zijn gekoppeld aan Azure, of die zich on-premises in de cache bevinden met Azure File Sync, is standaardopslag mogelijk een betere keuze vanuit een kostenperspectief.

Wanneer u een bestandsshare hebt gemaakt in een opslaglaag, kunt u die niet verplaatsen naar andere soorten opslagaccounts. Als u bijvoorbeeld een voor transactie geoptimaliseerde bestandsshare verplaatst naar de premium laag, moet u een nieuwe bestandsshare maken in een FileStorage-opslagaccount en de gegevens kopiëren van uw oorspronkelijke share naar een nieuwe bestandsshare in het FileStorage-account. We raden aan AzCopy te gebruiken om gegevens tussen Azure-bestandsshares te kopiëren, maar u kunt ook hulpprogramma's gebruiken als `robocopy` op Windows of `rsync` voor macOS en Linux. 

Bestandsshares die geïmplementeerd zijn binnen GPv2-opslagaccounts, kunnen worden verplaatst tussen de standaardlagen (voor transactie geoptimaliseerd, dynamisch en statisch) zonder een nieuw opslagaccount te maken en gegevens migreren, maar er worden transactiekosten in rekening gebracht wanneer u uw laag veranderd. Wanneer u een bestandsshare van een dynamischere laag naar een statischere laag verplaatst, worden de schrijftransactiekosten voor de statischere laag in rekening gebracht voor elk bestand in de share. Wanneer u een bestandsshare verplaatst vaan een statischere laag naar een dynamischere laag, worden de leestransactiekosten in rekening gebracht voor elk bestand in de share.

Raadpleeg [Azure Files-facturering begrijpen](../articles/storage/files/understanding-billing.md) voor meer informatie.