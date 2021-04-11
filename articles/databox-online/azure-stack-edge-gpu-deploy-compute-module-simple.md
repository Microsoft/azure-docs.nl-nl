---
title: Een IoT Edge module gebruiken om reken werk belasting te implementeren op Azure Stack Edge Pro met GPU | Microsoft Docs
description: Meer informatie over het uitvoeren van een compute-workload met een vooraf gemaakte IoT Edge module op uw Azure Stack Edge Pro GPU-apparaat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: d10e27c80a9253de7482644debd19debce8f4e50
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055294"
---
# <a name="tutorial-run-a-compute-workload-with-iot-edge-module-on-azure-stack-edge-pro-gpu"></a>Zelf studie: een compute-workload uitvoeren met IoT Edge module op Azure Stack Edge Pro GPU

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

In deze zelf studie wordt beschreven hoe u een reken werkbelasting uitvoert met behulp van een IoT Edge module op uw Azure Stack Edge Pro GPU-apparaat. Nadat u de compute hebt geconfigureerd, worden de gegevens door het apparaat getransformeerd voordat deze naar Azure worden verzonden.

Deze procedure neemt 10-15 minuten in beslag.


In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Rekenproces configureren
> * Shares toevoegen
> * Een rekenprocesmodule toevoegen
> * Gegevenstransformatie controleren en gegevens overdragen

 
## <a name="prerequisites"></a>Vereisten

Voordat u een compute-functie op uw Azure Stack Edge Pro GPU-apparaat instelt, moet u het volgende doen:

- U hebt uw Azure Stack Edge Pro-apparaat geactiveerd zoals beschreven in [Azure Stack Edge Pro activeren](azure-stack-edge-gpu-deploy-activate.md).
- U hebt een IoT Edge-module die u kunt uitvoeren op uw gegevens. In deze zelf studie hebben we een `filemove2` module gebruikt waarmee gegevens van de lokale share op uw apparaat worden verplaatst naar de Edge-share van waaruit de gegevens naar Azure Storage account gaan.


## <a name="configure-compute"></a>Rekenproces configureren

[!INCLUDE [configure-compute](../../includes/azure-stack-edge-gateway-configure-compute.md)]


## <a name="add-shares"></a>Shares toevoegen

Voor de eenvoudige implementatie in deze zelfstudie hebt u twee shares nodig: één Edge-share en een andere lokale share.

1. Voer de volgende stappen uit om een rand share op het apparaat toe te voegen:

    1. Ga in uw Azure Stack Edge-resource naar **Cloud Storage gateway > shares**.
    2. Selecteer **+Share toevoegen** in de opdrachtbalk.
    3. Geef op de blade **Share toevoegen** een naam op voor de share en selecteer het sharetype.
    4. Als u de Edge-share wilt koppelen, schakelt u het selectievakje voor **De share met Edge-rekenproces** gebruiken.
    5. Selecteer de **Storage-account**, **Storage-service**, een bestaande gebruiker en selecteer **Maken**.

        ![Een Edge-share toevoegen](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-1.png) 


    > [!NOTE]
    > Om de NFS-share te koppelen aan het rekenproces, moet het rekennetwerk zijn geconfigureerd op hetzelfde subnet als het virtuele IP-adres van NFS. Ga naar [Rekennetwerk inschakelen op uw Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md) voor meer informatie over het configureren van rekennetwerken.

    De Edge-share wordt gemaakt en u ontvangt de melding dat het maken is voltooid. De lijst shares wordt mogelijk bijgewerkt. U moet echter wachten tot het maken van de share is voltooid.

2. Als u een Edge-lokale share op het apparaat wilt toevoegen, herhaalt u de stappen in de vorige stap en schakelt u het selectie vakje voor **configureren als Edge-lokale share** in. De gegevens op de lokale share blijven op het apparaat.

    ![Een lokale Edge-share toevoegen](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-2.png)

    Als u een lokale NFS-share hebt gemaakt, gebruikt u de volgende optie van de opdracht rsync (op afstand synchroniseren) om bestanden naar de share te kopiëren:

    `rsync <source file path> < destination file path>`

    Voor meer informatie over de opdracht `rsync` raadpleegt u de [`Rsync` documentatie](https://www.computerhope.com/unix/rsync.htm).
 
3. Ga naar de **Cloud Storage gateway > shares** om de bijgewerkte lijst met shares weer te geven.

    ![Bijgewerkte lijst met shares](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Een module toevoegen

U kunt een aangepaste of vooraf gemaakte module toevoegen. Het apparaat wordt niet geleverd met vooraf ontwikkelde of aangepaste modules. Ga naar [Een C#-module ontwikkelen voor uw Azure Stack Edge Pro-apparaat](./azure-stack-edge-gpu-create-iot-edge-module.md) voor meer informatie over het maken van een aangepaste module.

In dit gedeelte voegt u een aangepaste module toe aan het IoT Edge-apparaat dat u hebt gemaakt in [Een C#-module ontwikkelen voor uw Azure Stack Edge Pro](./azure-stack-edge-gpu-create-iot-edge-module.md). Deze aangepaste module neemt bestanden van een lokale Edge-share op het Edge-apparaat en deze verplaatst naar een Edge-(cloud)share op het apparaat. De cloudshare pusht de bestanden vervolgens naar het Azure-opslagaccount dat is gekoppeld aan de cloudshare.

Voer de volgende stappen uit om een module toe te voegen:

1. Ga naar **IoT Edge > Modules**. Selecteer op de opdracht balk **+ module toevoegen**. 

2. Voer op de Blade **module toevoegen** de volgende waarden in:

    
    |Veld  |Waarde  |
    |---------|---------|
    |Naam     | Een unieke naam voor de module. Deze module is een Docker-container die u kunt implementeren op het IoT Edge-apparaat dat is gekoppeld aan uw Azure Stack Edge Pro-apparaat.        |
    |URI installatiekopie     | De URI installatiekopie voor de bijbehorende containerinstallatiekopie voor de module.        |
    |Referenties vereist     | Als u dit selectievakje inschakelt, worden gebruikersnaam en wachtwoord gebruikt om modules op te halen met een overeenkomende URL.        |
    |Invoershare     | Selecteer een invoershare. De lokale Edge-share is in dit geval de invoershare. De module die hier wordt gebruikt, verplaatst bestanden van de lokale Edge-share naar een Edge-share waar ze in de cloud worden geüpload.        |
    |Uitvoershare     | Selecteer een uitvoershare. De Edge-share is in dit geval de uitvoershare.        |
    |Triggertype     | Selecteer uit **Bestand** of **Planning**. Er wordt een bestandstrigger geactiveerd wanneer zich een bestandsevenement voordoet zoals een bestand dat in de inputshare wordt geschreven. Een geplande trigger wordt geactiveerd op basis van een schema dat u instelt.         |
    |Triggernaam     | Een unieke naam voor uw trigger.         |
    |Omgevingsvariabelen| Optionele informatie die helpt bij het definiëren van de omgeving waarin uw module wordt uitgevoerd.   |

    ![Een module toevoegen en configureren](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-module-1.png)

3. Selecteer **Toevoegen**. De module wordt toegevoegd. De pagina **IoT Edge > modules** wordt bijgewerkt om aan te geven dat de module is geïmplementeerd. De runtimestatus van de module die u hebt toegevoegd, moet *Wordt uitgevoerd* zijn.

    ![Module geïmplementeerd](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Gegevenstransformatie controleren en gegevens overdragen

De laatste stap is om ervoor te zorgen dat de module wordt uitgevoerd en de gegevens naar verwachting worden verwerkt. De runtimestatus van de module moet 'Wordt uitgevoerd' zijn voor uw IoT Edge-apparaat in de IoT Hub-resource.

Ga als volgt te werk om te controleren of de module wordt uitgevoerd en de verwachte gegevens worden verwerkt:


1. Maak via Verkenner verbinding met zowel de lokale Edge-share als de Edge-shares die u eerder hebt gemaakt. Zie de stappen 

    ![Verbinding maken met de Edge-Cloud shares voor lokale en Edge](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-1.png) 
 
1. Voeg gegevens toe aan de lokale share.

    ![Het bestand is gekopieerd naar de lokale share van de rand](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-2.png) 
 
   De gegevens worden verplaatst naar de cloudshare.

    ![Het bestand is verplaatst naar de Edge-Cloud share](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-3.png)  

   De gegevens worden vervolgens vanuit de cloudshare naar het opslagaccount gepusht. Als u de gegevens wilt bekijken, kunt u Storage Explorer of Azure Storage in de portal gebruiken.

    ![Gegevens in het opslag account verifiëren](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-4.png)
 
U hebt het validatieproces voltooid.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Rekenproces configureren
> * Shares toevoegen
> * Een rekenprocesmodule toevoegen
> * Gegevenstransformatie controleren en gegevens overdragen

Als u wilt weten hoe u uw Azure Stack Edge Pro-apparaat beheert, gaat u naar:

> [!div class="nextstepaction"]
> [De lokale webgebruikersinterface gebruiken voor het beheren van een Azure Stack Edge Pro](azure-stack-edge-gpu-manage-access-power-connectivity-mode.md)