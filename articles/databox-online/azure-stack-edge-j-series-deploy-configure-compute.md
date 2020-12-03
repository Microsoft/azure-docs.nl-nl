---
title: Zelfstudie voor het filteren en analyseren van gegevens met rekenproces voor Azure Stack Edge Pro met GPU | Microsoft Docs
description: Leer hoe u een rekenprocesrol configureert op een Azure Stack Edge Pro GPU-apparaat en deze gebruikt om gegevens te transformeren voordat ze naar Azure worden verzonden.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 75428b28095b0e425a1670caffcf960aa6ae58f6
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185501"
---
# <a name="tutorial-transform-data-with-azure-stack-edge-pro"></a>Zelfstudie: Gegevens transformeren met Azure Stack Edge Pro

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

In deze zelfstudie wordt beschreven hoe u een rekenprocesrol configureert op uw Azure Stack Edge Pro-apparaat. Wanneer u de rekenrol hebt geconfigureerd, kan Azure Stack Edge Pro gegevens transformeren voordat deze naar Azure worden verzonden.

Deze procedure neemt 10-15 minuten in beslag.


In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Rekenproces configureren
> * Shares toevoegen
> * Een rekenprocesmodule toevoegen
> * Gegevenstransformatie controleren en gegevens overdragen

 
## <a name="prerequisites"></a>Vereisten

Zorg dat aan deze voorwaarde wordt voldaan voordat u een rekenprocesrol configureert op uw Azure Stack Edge Pro-apparaat:

- U hebt uw Azure Stack Edge Pro-apparaat geactiveerd zoals beschreven in [Azure Stack Edge Pro activeren](azure-stack-edge-gpu-deploy-activate.md).


## <a name="configure-compute"></a>Rekenproces configureren

Als u de rekenproces wilt configureren voor uw Azure Stack Edge Pro, maakt u een IoT Hub-resource.

1. Ga in Azure Portal van uw Azure Stack Edge-resource naar **Overzicht**. Selecteer in het rechterdeelvenster op de tegel **Compute** **Aan de slag**.

    ![Aan de slag met rekenproces](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-1.png)

2. Selecteer op de tegel **Het Edge-rekenproces configureren** **Rekenproces configureren**.

    ![Rekenproces configureren](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-2.png)

3. Voer op de blade **Het Edge-rekenproces configureren** het volgende in:

   
    |Veld  |Waarde  |
    |---------|---------|
    |IoT Hub     | Kies uit **Nieuwe** of **Bestaande**. <br> Standaard wordt er een standaard-laag (S1) gebruikt voor het maken van een IoT-resource. Als u een IoT-resource in een gratis laag wilt gebruiken, maakt u er een en selecteert u vervolgens de bestaande resource. <br> In elk geval gebruikt de IoT Hub-resource hetzelfde abonnement en dezelfde resourcegroep die wordt gebruikt door de resource Azure Stack Edge.     |
    |Naam     |Voer een naam in voor uw IoT Hub-resource.         |

    ![Aan de slag met rekenproces 2](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-3.png)

4. Selecteer **Maken**. Het maken van de IoT Hub-resource duurt enkele minuten. Nadat de IoT Hub-resource is gemaakt, wordt de tegel **Rekenproces configureren** bijgewerkt om de configuratie van het rekenproces weer te geven. 

    ![Aan de slag met rekenproces 3](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-4.png)

5. Als u wilt bevestigen dat de Edge-rekenprocesrol is geconfigureerd, selecteert u **Rekenproces weergeven** op de tegel **Rekenproces configureren**.
    
    ![Aan de slag met rekenproces 4](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-5.png)

    > [!NOTE]
    > Als het dialoogvenster **Rekenproces configureren** wordt gesloten voordat de IoT Hub aan het Azure Stack Edge Pro-apparaat is gekoppeld, wordt de IoT Hub gemaakt, maar wordt deze niet weergegeven in de rekenprocesconfiguratie. 
    
    Wanneer de Edge-rekenprocesrol wordt geconfigureerd op het Edge-apparaat, worden er twee apparaten aangemaakt: een IoT-apparaat en een IoT Edge-apparaat. Beide apparaten kunnen worden weergegeven in de IoT Hub-resource. Er wordt ook een IoT Edge-runtime op dit IoT Edge-apparaat uitgevoerd. Op dit moment is alleen het Linux-platform beschikbaar voor uw IoT Edge-apparaat.


## <a name="add-shares"></a>Shares toevoegen

Voor de eenvoudige implementatie in deze zelfstudie hebt u twee shares nodig: één Edge-share en een andere lokale share.

1. U kunt met de volgende stappen een Edge-share toevoegen aan het apparaat:

    1. Ga in uw Azure Stack Edge-resource naar **Edge-rekenproces > Aan de slag**.
    2. Selecteer **Toevoegen** op de tegel **Share(s) toevoegen**.

        ![Tegel Share toevoegen](./media/azure-stack-edge-j-series-deploy-configure-compute/add-share-1.png) 

    3. Geef op de blade **Share toevoegen** een naam op voor de share en selecteer het sharetype.
    4. Als u de Edge-share wilt koppelen, schakelt u het selectievakje voor **De share met Edge-rekenproces** gebruiken.
    5. Selecteer de **Storage-account**, **Storage-service**, een bestaande gebruiker en selecteer **Maken**.

        ![Een Edge-share toevoegen](./media/azure-stack-edge-j-series-deploy-configure-compute/add-edge-share-1.png) 

    Als u een lokale NFS-share hebt gemaakt, gebruikt u de volgende optie van de opdracht rsync (op afstand synchroniseren) om bestanden naar de share te kopiëren:

    `rsync <source file path> < destination file path>`

    Voor meer informatie over de opdracht `rsync` raadpleegt u de [Rsync-documentatie](https://www.computerhope.com/unix/rsync.htm).

    > [!NOTE]
    > Om de NFS-share te koppelen aan het rekenproces, moet het rekennetwerk zijn geconfigureerd op hetzelfde subnet als het virtuele IP-adres van NFS. Ga naar [Rekennetwerk inschakelen op uw Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md) voor meer informatie over het configureren van rekennetwerken.

    De Edge-share wordt gemaakt en u ontvangt de melding dat het maken is voltooid. De lijst shares wordt mogelijk bijgewerkt. U moet echter wachten tot het maken van de share is voltooid.

2. Voeg een lokale Edge-share op het Edge-apparaat toe door alle stappen in de voorgaande stap te herhalen en het selectievakje in te schakelen voor **Configureren als lokale Edge-share**. De gegevens op de lokale share blijven op het apparaat.

    ![Een lokale Edge-share toevoegen](./media/azure-stack-edge-j-series-deploy-configure-compute/add-edge-share-2.png)

  
3. Selecteer **Share(s) toevoegen** om de bijgewerkte lijst met shares weer te geven.

    ![Bijgewerkte lijst met shares](./media/azure-stack-edge-j-series-deploy-configure-compute/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Een module toevoegen

U kunt een aangepaste of vooraf gemaakte module toevoegen. Er zijn geen aangepaste modules aanwezig op dit Edge-apparaat. Ga naar [Een C#-module ontwikkelen voor uw Azure Stack Edge Pro-apparaat](azure-stack-edge-j-series-create-iot-edge-module.md) voor meer informatie over het maken van een aangepaste module.

In dit gedeelte voegt u een aangepaste module toe aan het IoT Edge-apparaat dat u hebt gemaakt in [Een C#-module ontwikkelen voor uw Azure Stack Edge Pro](azure-stack-edge-j-series-create-iot-edge-module.md). Deze aangepaste module neemt bestanden van een lokale Edge-share op het Edge-apparaat en deze verplaatst naar een Edge-(cloud)share op het apparaat. De cloudshare pusht de bestanden vervolgens naar het Azure-opslagaccount dat is gekoppeld aan de cloudshare.

1. Ga naar **Edge-rekenproces > Aan de slag**. Selecteer op de tegel **Modules toevoegen** het scenariotype als **Eenvoudig**. Selecteer **Toevoegen**.
2. Voer op de blade **Module configureren en toevoegen** de volgende waarden in:

    
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

    ![Een module toevoegen en configureren](./media/azure-stack-edge-j-series-deploy-configure-compute/add-module-1.png)

3. Selecteer **Toevoegen**. De module wordt toegevoegd. Ga naar de pagina **Overzicht**. De tegel **Modules** wordt bijgewerkt om aan te geven dat de module is geïmplementeerd. 

    ![Module geïmplementeerd](./media/azure-stack-edge-j-series-deploy-configure-compute/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Gegevenstransformatie controleren en gegevens overdragen

De laatste stap is controleren of de module is verbonden en wordt uitgevoerd zoals verwacht. De runtimestatus van de module moet 'Wordt uitgevoerd' zijn voor uw IoT Edge-apparaat in de IoT Hub-resource.

Doe het volgende om te controleren of de module wordt uitgevoerd:

1. Selecteer de tegel **Module toevoegen**. Hiermee gaat u naar de blade **Modules**. Zoek in de lijst met modules de module die u hebt geïmplementeerd. De runtimestatus van de module die u hebt toegevoegd, moet *Wordt uitgevoerd* zijn.

    ![Geïmplementeerde module weergeven](./media/azure-stack-edge-j-series-deploy-configure-compute/add-module-3.png)
 
1. Maak via Verkenner verbinding met zowel de lokale Edge-share als de Edge-shares die u eerder hebt gemaakt.

    ![Gegevenstransformatie controleren](./media/azure-stack-edge-j-series-deploy-configure-compute/verify-data-2.png) 
 
1. Voeg gegevens toe aan de lokale share.

    ![Gegevenstransformatie controleren](./media/azure-stack-edge-j-series-deploy-configure-compute/verify-data-3.png) 
 
   De gegevens worden verplaatst naar de cloudshare.

    ![Gegevenstransformatie controleren](./media/azure-stack-edge-j-series-deploy-configure-compute/verify-data-4.png)  

   De gegevens worden vervolgens vanuit de cloudshare naar het opslagaccount gepusht. U kunt Storage Explorer gebruiken om de gegevens te bekijken.

    <!--![Verify data transform](./media/azure-stack-edge-j-series-deploy-configure-compute/verify-data-5.png)-->
 
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
> [De lokale webgebruikersinterface gebruiken voor het beheren van een Azure Stack Edge Pro](azure-stack-edge-manage-access-power-connectivity-mode.md)
