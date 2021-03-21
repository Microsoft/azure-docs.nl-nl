---
title: Zelfstudie voor het activeren van een Azure Stack Edge Pro R-apparaat in Azure Portal
description: In de zelfstudie voor het implementeren van Azure Stack Edge Pro R krijgt u de instructie om uw fysieke apparaat te activeren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/23/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to activate Azure Stack Edge Pro R device so I can use it to transfer data to Azure.
ms.openlocfilehash: bae12b5ad7ce6c079161f42e1d6fe3e6c08e3f4b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101727529"
---
# <a name="tutorial-activate-azure-stack-edge-pro-r-device"></a>Zelfstudie: Azure Stack Edge Pro R-apparaat activeren

In deze zelfstudie wordt beschreven hoe u uw Azure Stack Edge Pro R-apparaat kunt activeren door de lokale webgebruikersinterface te gebruiken.

Het activeringsproces kan ongeveer 5 minuten duren.  

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Vereisten
> * Het fysieke apparaat activeren

## <a name="prerequisites"></a>Vereisten

Zorg dat aan deze voorwaarden wordt voldaan voordat u uw Azure Stack Edge Pro R-apparaat configureert en instelt:

* Voor uw fysieke apparaat: 
    
    - U hebt het fysieke apparaat geïnstalleerd zoals beschreven in [Azure Stack Edge Pro R installeren](azure-stack-edge-pro-r-deploy-install.md).
    - U hebt het netwerk en de rekennetwerkinstellingen geconfigureerd zoals beschreven in [Netwerk, rekennetwerk en webproxy configureren](azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy.md)
    - U hebt uw eigen apparaatcertificaten geüpload of de apparaatcertificaten gegenereerd op uw apparaat als u de apparaatnaam of het DNS-domein hebt gewijzigd via de pagina **Apparaat**. Als u deze stap niet hebt uitgevoerd, ziet u een foutbericht tijdens de apparaatactivering en wordt de activering geblokkeerd. Ga naar [Certificaten configureren](azure-stack-edge-placeholder.md) voor meer informatie.
    
* U hebt de activeringssleutel van de Azure Stack Edge-service die u hebt gemaakt om het Azure Stack Edge Pro R-apparaat te beheren. Ga voor meer informatie naar [Voorbereiding voor implementatie van Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-prep.md).


## <a name="activate-the-device"></a>Het apparaat activeren

1. Ga in de lokale webgebruikersinterface van het apparaat naar de pagina **Aan de slag**.
2. Op de tegel **Activering** selecteert u **Activeren**. 

    ![Pagina 1 'Clouddetails' van lokale webgebruikersinterface](./media/azure-stack-edge-pro-r-deploy-activate/activate-1.png)
    
3. In het deelvenster **Activeren**:
    1. Voer de **Activeringssleutel** in die u hebt ontvangen in [De activeringssleutel krijgen voor Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key).

    1. U kunt proactieve logboekverzameling inschakelen om Microsoft logboeken te laten verzamelen op basis van de integriteitsstatus van het apparaat. De logboeken die op deze manier zijn verzameld, worden geüpload naar een Azure Storage-account.
    
    1. Selecteer **Toepassen**.

    ![Pagina 2 'Clouddetails' van lokale webgebruikersinterface](./media/azure-stack-edge-pro-r-deploy-activate/activate-2.png)


5. Eerst wordt het apparaat geactiveerd. Vervolgens wordt u gevraagd het sleutelbestand te downloaden.
    
    ![Pagina 3 'Clouddetails' van lokale webgebruikersinterface](./media/azure-stack-edge-pro-r-deploy-activate/activate-3.png)
    
    Selecteer **Downloaden en doorgaan** en sla het bestand *device-serial-no.json* op een veilige locatie buiten het apparaat op. **Dit sleutelbestand bevat de herstelsleutels voor de besturingssysteemschijf en gegevensschijven op uw apparaat**. Deze sleutels kunnen nodig zijn om een toekomstig herstel van het systeem mogelijk te maken.

    Hier volgt de inhoud van het *json*-bestand:

        
    ```json
    {
      "Id": "<Device ID>",
      "DataVolumeBitLockerExternalKeys": {
        "hcsinternal": "<BitLocker key for data disk>",
        "hcsdata": "<BitLocker key for data disk>"
      },
      "SystemVolumeBitLockerRecoveryKey": "<BitLocker key for system volume>",
      "ServiceEncryptionKey": "<Azure service encryption key>"
    }
    ```
        
 
    De volgende tabel bevat uitleg over de verschillende sleutels:
    
    |Veld  |Beschrijving  |
    |---------|---------|
    |`Id`    | Dit is de id van het apparaat.        |
    |`DataVolumeBitLockerExternalKeys`|Dit zijn de BitLockers-sleutels voor de gegevensschijven en deze worden gebruikt om de lokale gegevens op uw apparaat te herstellen.|
    |`SystemVolumeBitLockerRecoveryKey`| Dit is de BitLocker-sleutel voor het systeemvolume. Deze sleutel helpt met het herstel van de systeemconfiguratie en de systeemgegevens voor uw apparaat. |
    |`ServiceEncryptionKey`| Deze sleutel beveiligt de gegevens die door de Azure-service stromen. Deze sleutel verzekert dat opgeslagen gegevens niet zullen worden gecompromitteerd als de Azure-service wordt gecompromitteerd. |

6. Ga naar de pagina **Overzicht**. De apparaatstatus zou **Geactiveerd** moeten zijn.

    ![Pagina 4 'Clouddetails' van lokale webgebruikersinterface](./media/azure-stack-edge-gpu-deploy-activate/activate-4.png)
 
De apparaatactivering is voltooid. U kunt nu shares toevoegen op uw apparaat.

Als u problemen ondervindt tijdens de activering, gaat u naar [Problemen met activering en Azure Key Vault oplossen](azure-stack-edge-gpu-troubleshoot-activation.md#activation-errors).

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Vereisten
> * Het fysieke apparaat activeren

Als u wilt weten hoe u gegevens overdraagt met uw Azure Stack Edge Pro R-apparaat, gaat u naar:

> [!div class="nextstepaction"]
> [Gegevens overdragen met Azure Stack Edge Pro R](./azure-stack-edge-j-series-deploy-add-shares.md)
