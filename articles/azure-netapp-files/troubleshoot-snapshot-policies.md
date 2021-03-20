---
title: Problemen met het momentopname beleid voor Azure NetApp Files oplossen | Microsoft Docs
description: Hierin worden fout berichten en oplossingen beschreven die u kunnen helpen bij het oplossen van problemen met momentopname beleids beheer voor Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/23/2020
ms.author: b-juche
ms.openlocfilehash: 6ba8b18876bdae2754a6a772ce3909ff2f5a71b7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91651001"
---
# <a name="troubleshoot-snapshot-policies"></a>Problemen met momentopnamebeleid oplossen

In dit artikel worden de fout scenario's beschreven die u kunt tegen komen bij het beheren van Azure NetApp Files snap shot-beleids regels. Het bevat ook oplossingen die u kunnen helpen bij het oplossen van de problemen.

## <a name="error-conditions-and-resolutions"></a>Fout voorwaarden en oplossingen 

|     Foutvoorwaarde    |     Oplossing    |
|-|-|
| Maken van het momentopname beleid mislukt met ongeldige naam voor het momentopname beleid. | Er treedt een fout op tijdens het maken van het momentopname beleid als de naam van uw momentopname beleid ongeldig is. De volgende richt lijnen zijn van toepassing op naam van het momentopname beleid:  <ul><li> De naam van het momentopname beleid mag geen niet-ASCII of speciale tekens bevatten. </li> <li> De naam van het momentopname beleid moet beginnen met een letter of een cijfer en mag alleen letters, cijfers, onderstrepings tekens (' _ ') en afbreek streepjes ('-') bevatten. </li> <li> De naam van het momentopname beleid moet tussen 1 en 64 tekens lang zijn.  </li></ul> Wijzig de naam van het momentopname beleid volgens de richt lijnen.  |
| Het maken van het momentopname beleid mislukt met ongeldige waarden. | Azure NetApp Files kan geen momentopname beleid maken als u een ongeldige waarde voor een veld opgeeft, zoals `Number of snapshots to keep` of `Minute on the hour to take snapshot` . De geldige waarden zijn als volgt:  <ul><li>De waarde moet een geldig getal zijn.</li> <li>De waarde moet tussen 0 en 59.</li></ul> Zorg ervoor dat er een geldige waarde wordt gegeven voor de velden. | 
| Het maken van het momentopname beleid mislukt met de fout `Total number of snapshots to keep exceeds 255` . | Elk volume kan [Maxi maal 255 moment opnamen](azure-netapp-files-resource-limits.md)hebben. Het maximum omvat de som van de moment opnamen van elk uur, dagelijks, wekelijks en maandelijks. <br> Verklein de `Snapshots to keep` waarde en probeer het opnieuw. |
| Het toewijzen van beleid aan een volume mislukt met de fout `Total snapshot policy is over the max '255'` . | Elk volume kan [Maxi maal 255 moment opnamen](azure-netapp-files-resource-limits.md)hebben. Wanneer de som van alle on-demand-, uur-, dagelijkse, wekelijkse en maandelijkse moment opnamen het maximum overschrijdt, treedt er een fout op. <br> Verklein de `snapshots to keep` waarde of verwijder een aantal moment opnamen op aanvraag en probeer het opnieuw. | 

## <a name="next-steps"></a>Volgende stappen  

* [Momentopname beleid beheren](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)
