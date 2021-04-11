---
title: Zelfstudie voor het activeren van een Azure Stack Edge Pro-apparaat met GPU in de Azure-portal | Microsoft Docs
description: In de zelfstudie voor het implementeren van Azure Stack Edge Pro GPU krijgt u instructie om uw fysieke apparaat te activeren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/07/2020
ms.author: alkohli
ms.openlocfilehash: 8e88fb2f6f2fc9ad50911bfda2245cd95ae33236
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058745"
---
# <a name="tutorial-activate-azure-stack-edge-pro-with-gpu"></a>Zelfstudie: Azure Stack Edge Pro met GPU activeren

In deze zelfstudie wordt beschreven hoe u uw Azure Stack Edge Pro-apparaat kunt activeren met een onboard GPU door de lokale webgebruikersinterface te gebruiken.

Het activeringsproces kan ongeveer 5 minuten duren.

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Vereisten
> * Het fysieke apparaat activeren

## <a name="prerequisites"></a>Vereisten

Zorg dat aan deze voorwaarden wordt voldaan voordat u uw Azure Stack Edge Pro-apparaat met GPU configureert en instelt:

* Voor uw fysieke apparaat: 
    
    - U hebt het fysieke apparaat geïnstalleerd zoals beschreven in [Azure Stack Edge Pro installeren](azure-stack-edge-gpu-deploy-install.md).
    - U hebt het netwerk en de rekennetwerkinstellingen geconfigureerd zoals beschreven in [Netwerk, rekennetwerk en webproxy configureren](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)
    - U hebt uw eigen apparaatcertificaten geüpload of de apparaatcertificaten gegenereerd op uw apparaat als u de apparaatnaam of het DNS-domein hebt gewijzigd via de pagina **Apparaat**. Als u deze stap niet hebt uitgevoerd, ziet u een foutbericht tijdens de apparaatactivering en wordt de activering geblokkeerd. Ga naar [Certificaten configureren](azure-stack-edge-gpu-deploy-configure-certificates.md) voor meer informatie.
    
* U hebt de activeringssleutel van de Azure Stack Edge-service die u hebt gemaakt om het Azure Stack Edge Pro-apparaat te beheren. Ga voor meer informatie naar [Voorbereiding voor implementatie van Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md).


## <a name="activate-the-device"></a>Het apparaat activeren

1. Ga in de lokale webgebruikersinterface van het apparaat naar de pagina **Aan de slag**.
2. Op de tegel **Activering** selecteert u **Activeren**. 

    ![Pagina 'Clouddetails' van lokale webgebruikersinterface](./media/azure-stack-edge-gpu-deploy-activate/activate-1.png)
    
3. Voer in het deelvenster **Activeren** de **Activeringssleutel** in die u hebt ontvangen in [De activeringssleutel krijgen voor Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md#get-the-activation-key).

4. Selecteer **Toepassen**.

    ![Pagina 2 'Clouddetails' van lokale webgebruikersinterface](./media/azure-stack-edge-gpu-deploy-activate/activate-2.png)


5. Eerst wordt het apparaat geactiveerd. Vervolgens wordt u gevraagd het sleutelbestand te downloaden.
    
    ![Pagina 3 'Clouddetails' van lokale webgebruikersinterface](./media/azure-stack-edge-gpu-deploy-activate/activate-3.png)
    
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

Als u wilt weten hoe u gegevens overdraagt met uw Azure Stack Edge Pro-apparaat, gaat u naar:

> [!div class="nextstepaction"]
> [Gegevens overdragen met Azure Stack Edge Pro](./azure-stack-edge-gpu-deploy-add-shares.md)