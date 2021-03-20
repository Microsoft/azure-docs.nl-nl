---
title: Meer informatie over de prijzen van Azure data share
description: Meer informatie over de prijzen van Azure data share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 1c2c58e206a70a3801c712df3b5582409712d3c8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88136926"
---
# <a name="understand-azure-data-share-pricing"></a>Meer informatie over de prijzen van Azure data share

Voor het delen op basis van moment opnamen worden Azure data share-kosten in rekening gebracht voor het maken van moment opnamen van de gegevensset In dit artikel wordt uitgelegd hoe u het uitvoeren van moment opnamen van gegevensset en moment opnamen kunt schatten met behulp van momentopname geschiedenis gegevens. Op dit moment wordt de gegevens provider gefactureerd voor de moment opname van de gegevensset en het uitvoeren van de moment opname.

## <a name="dataset-snapshot"></a>Moment opname van gegevensset

Moment opname van gegevensset is de bewerking voor het verplaatsen van de gegevensset van de bron naar het doel. Wanneer een moment opname wordt gemaakt voor een share, is de moment opname van elke gegevensset in de share een momentopname bewerking voor een gegevensset. Volg de onderstaande stappen om een lijst met moment opnamen van gegevensset weer te geven. 

1. Ga in Azure Portal naar uw gegevens share bron.

1. Selecteer een share in een verzonden share of een ontvangen share.

1. Klik op het tabblad **geschiedenis** .

1. Klik op Start tijd van een moment opname.
 
    ![Momentopname geschiedenis](./media/concepts/concepts-pricing/pricing-snapshot-history.png "Momentopname geschiedenis") 

1. De lijst met momentopname bewerkingen voor gegevensset weer geven. Elk regel item komt overeen met een momentopname bewerking voor een gegevensset. In dit voor beeld zijn er twee moment opnamen van de gegevensset.

    ![Momentopname bewerking voor gegevensset](./media/concepts/concepts-pricing/pricing-dataset-snapshot.png "Momentopname bewerking voor gegevensset")

## <a name="snapshot-execution"></a>Uitvoering van moment opname

De uitvoering van de moment opname bevat de resources die nodig zijn om een gegevensset van de bron naar het doel te verplaatsen. Voor elke momentopname bewerking voor een gegevensset wordt de uitvoering van de moment opname berekend als aantal vCores vermenigvuldigd met de duur van de moment opname. Kosten worden in rekening gebracht per minuut en naar boven afgerond. Het aantal vCore wordt dynamisch geselecteerd op basis van het bron doel paar en het gegevens patroon. Volg de onderstaande stappen om begin tijd van moment opname weer te geven, eind tijd en vCores die worden gebruikt voor de momentopname bewerking voor een gegevensset.

1. Ga in Azure Portal naar uw gegevens share bron.

1. Selecteer een share in een verzonden share of een ontvangen share.

1. Klik op het tabblad **geschiedenis** .

1. Klik op Start tijd van een moment opname.

    ![Momentopname geschiedenis](./media/concepts/concepts-pricing/pricing-snapshot-history.png "Momentopname geschiedenis") 

1. Klik op status van een momentopname bewerking voor een gegevensset.

    ![Momentopname status van gegevensset](./media/concepts/concepts-pricing/pricing-snapshot-status.png "Momentopname status van gegevensset")

1. De begin tijd, eind tijd en vCores weer geven die worden gebruikt voor de momentopname bewerking van deze gegevensset. 

    ![Uitvoering van moment opname](./media/concepts/concepts-pricing/pricing-snapshot-execution.png "Uitvoering van moment opname")

## <a name="next-steps"></a>Volgende stappen

- Ontvang de meest recente prijs informatie- [prijzen van Azure data share](https://azure.microsoft.com/pricing/details/data-share/)
- Azure-prijs calculator gebruiken om de kosten te schatten- [Azure-prijs calculator](https://azure.microsoft.com/pricing/calculator/)
