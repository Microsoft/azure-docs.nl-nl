---
title: Richt lijnen voor herstel na nood gevallen voor Azure Data Lake Storage Gen1 | Microsoft Docs
description: Meer informatie over het verder beschermen van uw gegevens tegen regionale storingen of onopzettelijke verwijderingen buiten de lokaal redundante opslag van Azure Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: twooley
ms.openlocfilehash: 48136f8d9172c3674e849e24efca4ae5070f83ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92109116"
---
# <a name="high-availability-and-disaster-recovery-guidance-for-data-lake-storage-gen1"></a>Richt lijnen voor hoge Beschik baarheid en herstel na nood gevallen voor Data Lake Storage Gen1

Data Lake Storage Gen1 biedt lokaal redundante opslag (LRS). Daarom is de gegevens in uw Data Lake Storage Gen1-account robuust tegen tijdelijke hardwarestoringen binnen een Data Center via geautomatiseerde replica's. Dit zorgt voor duurzaamheid en hoge Beschik baarheid, die voldoen aan de Data Lake Storage Gen1 SLA. Dit artikel bevat richt lijnen voor het verder beschermen van uw gegevens tegen zeldzame regionale onderbrekingen of onopzettelijke verwijderingen.

## <a name="disaster-recovery-guidance"></a>Richtlijnen voor herstel na noodgevallen

Het is essentieel om een plan voor nood herstel voor te bereiden. Lees de informatie in dit artikel en deze aanvullende bronnen om u te helpen uw eigen plan te maken.

* [Herstel na noodgevallen en hoge beschikbaarheid voor Azure-toepassingen](/azure/architecture/framework/resiliency/backup-and-recovery)
* [Technische richtlijnen voor flexibiliteit van Azure](/azure/architecture/framework/resiliency/overview)

### <a name="best-practice-recommendations"></a>Aanbevelingen voor best practices

U wordt aangeraden uw kritieke gegevens naar een andere Data Lake Storage Gen1 account in een andere regio te kopiëren met een frequentie die is afgestemd op de behoeften van uw plan voor herstel na nood gevallen. Er zijn verschillende methoden om gegevens te kopiëren, waaronder [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md)of [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md). Azure Data Factory is een handige service voor het regelmatig maken en implementeren van pijplijnen voor gegevensverplaatsing.

Als er een regionale storing optreedt, kunt u vervolgens toegang krijgen tot uw gegevens in de regio waar de gegevens zijn gekopieerd. U kunt het [Azure service Health-dash board](https://azure.microsoft.com/status/) bewaken om de status van de Azure-service wereld wijd te bepalen.

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>Herstelrichtlijnen voor gegevensbeschadiging en onbedoelde verwijdering

Hoewel Data Lake Storage Gen1 gegevens tolerantie biedt via geautomatiseerde replica's, wordt hiermee niet voor komen dat uw toepassing (of ontwikkel aars/gebruikers) gegevens beschadigt of per ongeluk worden verwijderd.

Als u onbedoeld verwijderen wilt voor komen, wordt u aangeraden eerst het juiste toegangs beleid voor uw Data Lake Storage Gen1-account in te stellen. Dit geldt ook voor het Toep assen van [Azure-resource vergrendelingen](../azure-resource-manager/management/lock-resources.md) voor het vergren delen van belang rijke resources en het Toep assen van toegangs beheer op basis van de beschik bare [Data Lake Storage gen1 beveiligings functies](data-lake-store-security-overview.md). We raden u ook aan om regel matig kopieën te maken van uw kritieke gegevens met behulp van [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) of [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) in een ander data Lake Storage gen1 account, map of Azure-abonnement. Dit kan worden gebruikt om gegevens te herstellen nadat ze beschadigd zijn geraakt of per ongeluk zijn verwijderd. Azure Data Factory is een handige service voor het regelmatig maken en implementeren van pijplijnen voor gegevensverplaatsing.

U kunt ook [Diagnostische logboek registratie](data-lake-store-diagnostic-logs.md) inschakelen voor een Data Lake Storage gen1 account voor het verzamelen van gegevens toegangscontrole. De controle spoor bevat informatie over wie een bestand kan hebben verwijderd of bijgewerkt.

## <a name="next-steps"></a>Volgende stappen

* [Aan de slag met Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* [Gegevens beveiligen in Data Lake Storage Gen1](data-lake-store-secure-data.md)