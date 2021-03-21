---
title: Gegevenslocatie
description: Beschrijft gegevens locatie bij het gebruik van externe rendering van Azure.
author: rapete
ms.author: rapete
ms.date: 02/04/2021
ms.topic: reference
ms.openlocfilehash: f20b3bb3de877ac627f5f6909c98cb9e1553f2a9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99576878"
---
# <a name="azure-remote-rendering-data-residency"></a>Azure remote rendering data locatie 
In dit artikel wordt uitgelegd wat het concept van gegevens locatie is en hoe het van toepassing is op de externe rendering van Azure. 

## <a name="data-residency"></a>Gegevenslocatie 
Azure remote rendering maakt gebruik van de door de gebruiker verschafte Azure Blob-containers voor model opslag. Wanneer het model niet in gebruik is, blijft het in de door de gebruiker ingevoerde Azure Blob Storage-regio. Wanneer het model wordt gebruikt voor rendering, worden de gegevens gekopieerd naar de regio die de gebruiker kiest bij het starten van de rendering-sessie.

## <a name="next-steps"></a>Volgende stappen
Als u wilt weten hoe u een Azure Blob Storage-container kunt instellen voor de externe rendering van Azure, kunt u [het beste een model converteren voor rendering](../quickstarts/convert-model.md)uitchecken.
