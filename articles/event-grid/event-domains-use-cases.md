---
title: Cases gebruiken voor gebeurtenis domeinen in Azure Event Grid
description: In dit artikel worden enkele gebruiks voorbeelden beschreven voor het gebruik van gebeurtenis domeinen in Azure Event Grid.
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 00318fc78053ed55e3599c329746d89d2eee4f99
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102204344"
---
# <a name="use-cases-for-event-domains-in-azure-event-grid"></a>Cases gebruiken voor gebeurtenis domeinen in Azure Event Grid
In dit artikel worden enkele gebruiks voorbeelden beschreven voor het gebruik van gebeurtenis domeinen in Azure Event Grid. 

## <a name="use-case-1"></a>Use-case 1 
[!INCLUDE [event-grid-domain-example-use-case.md](../../includes/event-grid-domain-example-use-case.md)]

## <a name="use-case-2"></a>Use-case 2
Bij het gebruik van systeem onderwerpen geldt een limiet van 500 gebeurtenis abonnementen. Als u meer dan 500 gebeurtenis abonnementen nodig hebt voor een systeem onderwerp, kunt u domeinen gebruiken. 

Stel, u hebt een systeem onderwerp voor een Azure-Blob Storage gemaakt en u moet meer dan 500 abonnementen maken voor het onderwerp, maar dit is niet mogelijk vanwege de limiet (500 abonnementen per onderwerp). In dit geval kunt u de volgende oplossing gebruiken die gebruikmaakt van een gebeurtenis domein. 

1. Maak een domein, dat ondersteuning biedt voor Maxi maal 100.000 onderwerpen en elk domein onderwerp kan 500 gebeurtenis abonnementen hebben. Dit model geeft u 500 * 100.000 gebeurtenis abonnementen. 
1. Maak een Azure function-abonnement voor het onderwerp Azure Storage systeem. Wanneer een functie gebeurtenissen van Azure Storage ontvangt, kan deze gebeurtenissen verrijken en publiceren naar een geschikt domein onderwerp. De functie kan bijvoorbeeld de naam van het BLOB-bestand parseren om het onderwerp van het doel domein te bepalen en het onderwerp gebeurtenis te publiceren in het domein. 

:::image type="content" source="./media/event-domains-use-cases/use-case-2.jpg" alt-text="Evenementen grid-domeinen-use-case 2":::


## <a name="next-steps"></a>Volgende stappen
Zie [gebeurtenis domeinen beheren](./how-to-event-domains.md)voor meer informatie over het instellen van gebeurtenis domeinen, het maken van onderwerpen, het maken van gebeurtenis abonnementen en het publiceren van gebeurtenissen.
