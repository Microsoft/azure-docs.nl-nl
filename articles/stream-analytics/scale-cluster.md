---
title: De grootte van een Azure Stream Analytics-cluster wijzigen
description: Leer hoe u de grootte van een Azure Stream Analytics-cluster omhoog en omlaag kunt schalen.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: e469e9bfd13bf8d89084b66e954cb51b27c2ebc2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020023"
---
# <a name="resize-an-azure-stream-analytics-cluster"></a>De grootte van een Azure Stream Analytics-cluster wijzigen

De capaciteit van een Stream Analytics-cluster wordt gemeten in streaming-eenheden (SU). Er kunnen meerdere taken parallel in hetzelfde cluster worden uitgevoerd, zolang de som van de SU's die aan alle actieve taken zijn toegewezen niet groter is dan de capaciteit van het cluster.

De capaciteit van het cluster kan omhoog of omlaag worden geschaald overeenkomstig de grootte van uw streaming-werkbelastingen. Het schalen van een cluster vergt tijd, en het is niet de bedoeling om veelvuldig te schalen. U wordt aangeraden een cluster te plannen en in te richten met een aantal SU's dat overeenkomt met het aantal dat u denkt te gaan gebruiken.

## <a name="change-the-scale-of-your-cluster"></a>De schaal van uw cluster wijzigen

1. Zoek en selecteer uw Stream Analytics-cluster in Azure Portal.

1. Selecteer **Schaal** in de sectie **Overzicht**. U kunt zien hoeveel SU's aan uw cluster zijn toegewezen. Gebruik de selector om het aantal SU's naar behoefte te verhogen of te verlagen.

   ![cluster schalen](./media/scale-cluster/scale-cluster.png)

De schaalbewerking heeft geen invloed op taken die momenteel worden uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

U weet nu hoe u uw Stream Analytics-clusters omhoog en omlaag kunt schalen. Hierna kunt u meer te weten komen over het beheren van privé-eindpunten en het automatisch schalen van uw taken:

* [Privé-eindpunten maken en verwijderen in een Azure Stream Analytics-cluster](private-endpoints.md)
* [Taken beheren in een Azure Stream Analytics-cluster](manage-jobs-cluster.md)
