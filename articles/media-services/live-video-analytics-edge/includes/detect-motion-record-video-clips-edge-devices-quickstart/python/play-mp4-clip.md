---
ms.openlocfilehash: dfb887004cd29b5bd9f1d9886b7dfa5f43c83dbe
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99532159"
---
De MP4-bestanden worden geschreven naar een map op het apparaat Edge dat u hebt geconfigureerd in het *. env* -bestand met behulp van de VIDEO_OUTPUT_FOLDER_ON_DEVICE sleutel. Als u de standaardwaarde hebt gebruikt, bevinden de resultaten zich in de map */var/media/* .

Om de MP4-clip af te spelen:

1. Ga naar uw resourcegroep, zoek de VM en maak vervolgens verbinding met behulp van Azure Bastion.

    ![Resourcegroep](../../../media/quickstarts/resource-group.png)
    
    ![VM](../../../media/quickstarts/virtual-machine.png)
1. Meld u aan met de aanmeldingsgegevens die zijn gegenereerd bij het [instellen van uw Azure-resources](../../../detect-motion-emit-events-quickstart.md#set-up-azure-resources). 
1. Ga met de opdrachtprompt naar de betreffende map. De standaardlocatie is */var/media*. U vindt de MP4-bestanden in de map.

    ![Uitvoer](../../../media/quickstarts/samples-output.png) 

1. Gebruik [Secure Copy (SCP)](../../../../../virtual-machines/linux/copy-files-to-linux-vm-using-scp.md) om de bestanden naar uw lokale machine te kopiëren. 
1. Speel de bestanden af met [VLC media player](https://www.videolan.org/vlc/) of een andere MP4-speler.
