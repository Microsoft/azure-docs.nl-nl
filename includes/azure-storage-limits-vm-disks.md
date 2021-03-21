---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: ffb07220267a2c192b4aad2405185c80bd9abbc0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94523720"
---
U kunt een aantal gegevensschijven koppelen aan een virtuele Azure-machine. Op basis van schaalbaarheid- en prestatiedoelen voor de gegevensschijven van een VM kunt u het aantal en type schijven bepalen dat u nodig hebt om aan uw prestatie- en capaciteitsvereisten te voldoen.

> [!IMPORTANT]
> Voor optimale prestaties moet u het aantal intensief gebruikte schijven dat u aan de virtuele machine koppelt, echter zo laag mogelijk houden. Zo voorkomt u dat u tegen beperkingen aanloopt. Als alle gekoppelde schijven niet tegelijkertijd maximaal worden gebruikt, kan de virtuele machine een groot aantal schijven ondersteunen.

**Voor beheerde Azure-schijven:**

In de volgende tabel ziet u de standaard- en maximumlimieten van het aantal resources per regio per abonnement. De limieten blijven dezelfde, ongeacht of schijven zijn versleuteld met door platform beheerde sleutels of door klant beheerde sleutels. Er is geen limiet voor het aantal beheerde schijven, momentopnamen en afbeelding per resourcegroep.  

> | Resource | Limiet |
> | --- | --- |
> | Standaard beheerde schijven | 50,000 |
> | Standaard beheerde SSD-schijven | 50,000 |
> | Premium beheerde schijven | 50,000 |
> | Standard_LRS-momentopnamen | 50,000 |
> | Standard_ZRS-momentopnamen | 50,000 |
> | Beheerde installatiekopie | 50,000 |

**Voor Standard-opslagaccounts:** Een standaardopslagaccount heeft een maximale totale aanvraagsnelheid van 20.000 IOP's. Het totale aantal IOP's van alle schijven van een virtuele machine in een standaardopslagaccount mag niet groter zijn dan deze limiet.
  
U kunt het aantal intensief gebruikte schijven dat binnen één standaardopslagaccount wordt ondersteund, ruwweg berekenen op basis van de limiet voor het aantal aanvragen. Voor bijvoorbeeld een VM in de Basic-laag is het maximumaantal hoog gebruikte schrijven ongeveer 66, wat neerkomt op 20.000/300 IOPS per schijf. Het maximumaantal hoog gebruikte schijven voor een Standard-VM is ongeveer 40, wat neerkomt op 20.000/500 IOPS per schijf. 

**Voor Premium Storage-accounts:** Premium-opslagaccounts bieden een maximale totale doorvoersnelheid van 50 Gbps. De totale doorvoer op alle schijven in uw virtuele machine mag deze limiet niet overschrijden.

