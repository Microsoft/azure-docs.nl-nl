---
title: Zelf studie-een persoonlijke cloud van Azure VMware-oplossing verwijderen
description: Meer informatie over het verwijderen van een persoonlijke cloud van Azure VMware-oplossing die u niet meer nodig hebt.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 453e7a3316c342cd724a951eafea0ae9fa045506
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103462096"
---
# <a name="tutorial-delete-an-azure-vmware-solution-private-cloud"></a>Zelf studie: een persoonlijke cloud van Azure VMware-oplossing verwijderen

Als u een Azure VMware Solution-privécloud hebt die u niet meer nodig hebt, kunt u deze verwijderen. De privécloud bevat een geïsoleerd netwerk domein, een of meer ingerichte vSphere-clusters op dedicated server hosts en verschillende virtuele machines (Vm's). Wanneer u een privécloud verwijdert, worden alle Vm's, hun gegevens en clusters verwijderd. De toegewezen hosts van de Azure VMware-oplossing worden veilig gewist en geretourneerd naar de gratis groep. De ingerichte netwerk adres ruimte wordt ook verwijderd.  

> [!CAUTION]
> Het verwijderen van de privécloud is een onomkeerbare bewerking. Zodra de privécloud is verwijderd, kunnen de gegevens niet worden hersteld. Dit komt doordat alle actieve workloads en onderdelen worden beëindigd en alle privécloudgegevens en configuratie-instellingen, met inbegrip van openbare IP-adressen, worden vernietigd.

## <a name="prerequisites"></a>Vereisten

Als u de virtuele machines en de bijbehorende gegevens later nodig hebt, moet u een back-up van de gegevens maken voordat u de privécloud verwijdert.  Er is geen manier om de Vm's en de bijbehorende gegevens te herstellen.


## <a name="delete-the-private-cloud"></a>De privécloud verwijderen

1. Open de Azure VMware-oplossingen console in de [Azure Portal](https://portal.azure.com).

2. Selecteer de privécloud die u wilt verwijderen.
 
3. Voer de naam van de privécloud in en selecteer **Ja**. 

>[!NOTE]
>Het verwijderings proces duurt enkele uren.  
