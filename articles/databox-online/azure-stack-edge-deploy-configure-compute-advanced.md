---
title: Zelfstudie voor het filteren en analyseren van gegevens voor geavanceerde implementatie met rekenproces voor Azure Stack Edge Pro | Microsoft Docs
description: Leer hoe u een rekenprocesrol configureert in Azure Stack Edge Pro en deze gebruikt om gegevens te transformeren voor een geavanceerde implementatiestroom voordat ze naar Azure worden verzonden.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/06/2021
ms.author: alkohli
ms.openlocfilehash: 81953f573c47d229fcaccd7c11f62155acd2f119
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063641"
---
# <a name="tutorial-transform-data-with-azure-stack-edge-pro-for-advanced-deployment-flow"></a>Zelfstudie: Gegevens transformeren met Azure Stack Edge Pro voor een geavanceerde implementatiestroom

In deze zelfstudie wordt beschreven hoe u een rekenprocesrol voor een geavanceerde implementatiestroom configureert op uw Azure Stack Edge Pro-apparaat. Wanneer u de rekenrol hebt geconfigureerd, kan Azure Stack Edge Pro gegevens transformeren voordat deze naar Azure worden verzonden.

Compute kan worden geconfigureerd voor een eenvoudige of geavanceerde implementatiestroom op uw apparaat.

| Criteria | Eenvoudige implementatie                                | Geavanceerde implementatie                   |
|------------------|--------------------------------------------------|---------------------------------------|
| Bedoeld voor     | IT-beheerders                                | Ontwikkelaars                            |
| Type             | Azure Stack Edge-service gebruiken voor implementatie van modules      | IoT Hub-service gebruiken voor implementatie van modules |
| Modules geïmplementeerd | Enkelvoudig                                           | Gekoppelde of meerdere modules           |


Deze procedure neemt ongeveer 20 tot 30 minuten in beslag.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Rekenproces configureren
> * Shares toevoegen
> * Een trigger toevoegen
> * Een rekenprocesmodule toevoegen
> * Gegevenstransformatie controleren en gegevens overdragen

 
## <a name="prerequisites"></a>Vereisten

Zorg dat aan deze voorwaarde wordt voldaan voordat u een rekenprocesrol configureert op uw Azure Stack Edge Pro-apparaat:

- U hebt het Azure Stack Edge Pro-apparaat geactiveerd zoals beschreven in [Azure Stack Edge Pro aansluiten, instellen en activeren](azure-stack-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Rekenproces configureren

Als u de rekenproces wilt configureren voor uw Azure Stack Edge Pro, maakt u een IoT Hub-resource.

1. Ga in Azure Portal van uw Azure Stack Edge-resource naar **Overzicht**. Selecteer in het rechterdeelvenster de tegel **IoT Edge**.

    ![Aan de slag met rekenproces](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-1.png)

2. Selecteer op de tegel **IoT Edge-service inschakelen** de optie **Toevoegen**. Met deze actie wordt IoT Edge-service ingeschakeld waarmee u IoT Edge-modules lokaal op uw apparaat kunt implementeren.

    ![Aan de slag met rekenproces 2](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-2.png)

3. Voer bij **IoT Edge-service maken** het volgende in:

   
    |Veld  |Waarde  |
    |---------|---------|
    |Abonnement     |Selecteer een abonnement voor uw IoT Hub-resource. U kunt hetzelfde abonnement selecteren dat wordt gebruikt door de Azure Stack Edge-resource.        |
    |Resourcegroep     |Voer een naam in voor de resourcegroep voor uw IoT Hub-resource. U kunt dezelfde resourcegroep selecteren die wordt gebruikt door de Azure Stack Edge-resource.         |
    |IoT Hub     | Kies uit **Nieuwe** of **Bestaande**. <br> Standaard wordt er een standaard-laag (S1) gebruikt voor het maken van een IoT-resource. Als u een IoT-resource in een gratis laag wilt gebruiken, maakt u er een en selecteert u vervolgens de bestaande resource.      |
    |Naam     |Accepteer de standaardwaarde of voer een naam in voor uw IoT Hub-resource.         |

    ![Aan de slag met rekenproces 3](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-3.png)

4. Selecteer **Controleren + maken**. Het maken van de IoT Hub-resource duurt enkele minuten. Nadat de IoT Hub-resource is gemaakt, wordt in het **Overzicht** aangegeven dat de IoT Edge-service wordt uitgevoerd. 

    Wanneer de IoT Edge-service wordt geconfigureerd op het Edge-apparaat, worden er twee apparaten gemaakt: een IoT-apparaat en een IoT Edge-apparaat. Beide apparaten kunnen worden weergegeven in de IoT Hub-resource. Er wordt ook een IoT Edge-runtime op dit IoT Edge-apparaat uitgevoerd. Op dit moment is alleen het Linux-platform beschikbaar voor uw IoT Edge-apparaat.

    Als u wilt bevestigen dat het Edge-rekenproces is geconfigureerd, selecteert u **IoT Edge-service > Eigenschappen** en bekijkt u het IoT-apparaat en het IoT Edge-apparaat. 

    ![Aan de slag met rekenproces 4](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-4.png)
    

## <a name="add-shares"></a>Shares toevoegen

Voor de geavanceerde implementatie in deze zelfstudie hebt u twee shares nodig: een Edge-share en een andere lokale Edge-share.

1. U kunt met de volgende stappen een Edge-share toevoegen aan het apparaat:

    1. Ga in uw Azure Stack Edge-resource naar **IoT Edge > Shares**.
    2. In de opdrachtbalk op de pagina **Shares** selecteert u **+Share toevoegen**.
    3. Geef op de blade **Share toevoegen** een naam op voor de share en selecteer het sharetype.
    4. Als u de Edge-share wilt koppelen, schakelt u het selectievakje voor **De share met Edge-rekenproces** gebruiken.
    5. Selecteer de **Storage-account**, **Storage-service**, een bestaande gebruiker en selecteer **Maken**.

        ![Een Edge-share toevoegen](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-1.png)

    Nadat de Edge-share is gemaakt, ontvangt u de melding dat het maken is voltooid. De lijst met shares wordt bijgewerkt op basis van de nieuwe share.

2. Voeg een lokale Edge-share op het Edge-apparaat toe door alle stappen in de voorgaande stap te herhalen en het selectievakje in te schakelen voor **Configureren als lokale Edge-share**. De gegevens op de lokale share blijven op het apparaat.

    ![Een lokale Edge-share toevoegen](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-2.png)

3. In het deelvenster **Shares** ziet u de bijgewerkte lijst met shares.

    ![Bijgewerkte lijst met shares](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-3.png)

4. Als u de eigenschappen van de zojuist gemaakte lokale share wilt bekijken, selecteert u de share in de lijst. Kopieer de waarde voor de share uit het vak **Lokaal koppelpunt voor Edge-rekenprocesmodules**.

    U gebruik dit lokale koppelpunt bij het implementeren van de module.

    ![Het vak Lokaal koppelpunt voor Edge-rekenprocesmodules](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-4.png)
 
5. Als u de eigenschappen wilt weergeven van de Edge-share die u hebt gemaakt, selecteert u de share in de lijst. Kopieer de waarde voor de share uit het vak **Lokaal koppelpunt voor Edge-rekenprocesmodules**.

    U gebruik dit lokale koppelpunt bij het implementeren van de module.

    ![Aangepaste module toevoegen](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-5.png)


## <a name="add-a-trigger"></a>Een trigger toevoegen

1. Ga naar uw Azure Stack Edge-resource en vervolgens naar **IoT Edge > Triggers**. Selecteer **+ Trigger toevoegen**.

    ![Trigger toevoegen](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-1.png)

2. Voer in het deelvenster **Trigger toevoegen** de volgende waarden in.

    |Veld  |Waarde  |
    |---------|---------|
    |Triggernaam     | Een unieke naam voor uw trigger.         |
    |Triggertype     | Selecteer de trigger **Bestand**. Er wordt een bestandstrigger geactiveerd wanneer zich een bestandsevenement voordoet zoals een bestand dat in de inputshare wordt geschreven. Een geplande trigger wordt echter geactiveerd op basis van een schema dat u instelt. Voor dit voorbeeld hebben we een bestandstrigger nodig.    |
    |Invoershare     | Selecteer een invoershare. De lokale Edge-share is in dit geval de invoershare. De module die hier wordt gebruikt, verplaatst bestanden van de lokale Edge-share naar een Edge-share waar ze in de cloud worden geüpload.        |

    ![Trigger toevoegen 2](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-2.png)

3. U wordt gewaarschuwd nadat de trigger is gemaakt. De lijst met triggers wordt bijgewerkt zodat de zojuist gemaakte trigger wordt weergegeven. Selecteer de trigger die u zojuist hebt gemaakt.

    ![Trigger toevoegen 3](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-3.png)

4. Kopieer de voorbeeldroute en sla deze op. U gaat deze voorbeeldroute wijzigen en later in de IoT Hub gebruiken.

    `"sampleroute&quot;: &quot;FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/modulename/inputs/input1\")"`

    ![Trigger toevoegen 4](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-4.png)

## <a name="add-a-module"></a>Een module toevoegen

Er zijn geen aangepaste modules aanwezig op dit Edge-apparaat. U kunt een aangepaste of vooraf gemaakte module toevoegen. Ga naar [Een C#-module ontwikkelen voor uw Azure Stack Edge Pro-apparaat](azure-stack-edge-create-iot-edge-module.md) voor meer informatie over het maken van een aangepaste module.

In dit gedeelte voegt u een aangepaste module toe aan het IoT Edge-apparaat dat u hebt gemaakt in [Een C#-module ontwikkelen voor uw Azure Stack Edge Pro](azure-stack-edge-create-iot-edge-module.md). Deze aangepaste module neemt bestanden van een lokale Edge-share op het Edge-apparaat en deze verplaatst naar een Edge-(cloud)share op het apparaat. De cloudshare pusht de bestanden vervolgens naar het Azure-opslagaccount dat is gekoppeld aan de cloudshare.

1. Ga naar uw Azure Stack Edge-resource en vervolgens naar **IoT Edge > Overzicht**. Selecteer **Ga naar Azure IoT Hub** op de tegel **Modules**.

    ![Selecteer Geavanceerde implementatie](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-1.png)

<!--2. In the **Configure and add module** blade, input the following values:  

    |Input share     | Select an input share. The Edge local share is the input share in this case. The module used here moves files from the Edge local share to an Edge share where they are uploaded into the cloud.        |
    |Output share     | Select an output share. The Edge share is the output share in this case.        |
-->

2. Ga in uw IoT Hub-resource naar **IoT Edge-apparaat** en selecteer uw IoT Edge-apparaat.

    ![Ga naar IoT Edge-apparaat in IoT Hub](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-2.png)

3. Selecteer onder **Apparaatdetails** de optie **Modules instellen**.

    ![De koppeling Modules instellen](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-3.png)

4. Doe het volgende in **Modules toevoegen**:

    1. Geef in de Containerregisterinstellingen waarden op voor Naam, Adres, Gebruikersnaam en Wachtwoord voor de aangepaste module.
    De naam, het adres en de vermelde referenties worden gebruikt om modules met een overeenkomende URL op te halen. Als u deze module wilt implementeren, selecteert u **IoT Edge-module** onder **Implementatiemodules**. Deze IoT Edge-module is een dockercontainer die u kunt implementeren op het IoT Edge-apparaat dat is gekoppeld aan uw Azure Stack Edge Pro-apparaat.

        ![De pagina Modules instellen](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-4.png) 
 
    2. Geef de instellingen voor de aangepaste IoT Edge-module op. Voer de volgende waarden in.
     
        |Veld  |Waarde  |
        |---------|---------|
        |Naam     | Een unieke naam voor de module. Deze module is een dockercontainer die u kunt implementeren op het IoT Edge-apparaat dat is gekoppeld aan uw Azure Stack Edge Pro.        |
        |URI installatiekopie     | De URI installatiekopie voor de bijbehorende containerinstallatiekopie voor de module.        |
        |Referenties vereist     | Als u dit selectievakje inschakelt, worden gebruikersnaam en wachtwoord gebruikt om modules op te halen met een overeenkomende URL.        |
    
        Voer in het vak **Opties container maken** de lokale koppelpunten in voor de Edge-modules die u in de voorgaande stappen hebt gekopieerd voor de Edge-share en lokale Edge-share.

        > [!IMPORTANT]
        > De hier gebruikte paden worden gekoppeld in uw container en moeten daarom overeenkomen met wat de functionaliteit in uw container verwacht. Als u [Een aangepaste module maken](azure-stack-edge-create-iot-edge-module.md#update-the-module-with-custom-code) volgt, verwacht de in die module opgegeven code de gekopieerde paden. Wijzig deze paden niet.
    
        In het vak **Opties Container maken** plakt u het volgende voorbeeld:
    
        ```
        {
          "HostConfig": 
          {
           "Binds": 
            [
             "/home/hcsshares/mydbesmbedgelocalshare1:/home/input",
             "/home/hcsshares/mydbesmbedgeshare1:/home/output"
            ]
           }
        }
        ```

        Geef eventuele omgevingsvariabelen op die u voor uw module hebt gebruikt. Omgevingsvariabelen leveren optionele informatie die helpt bij het definiëren van de omgeving waarin uw module wordt uitgevoerd.

        ![Het vak Opties Container maken](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-5.png) 
 
    4. Configureer indien nodig de geavanceerde runtime-instellingen voor Edge en klik dan op **Volgende**.

        ![Aangepaste module toevoegen 2](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-6.png)
 
5. Onder **Routes opgeven** kunt u routes tussen modules instellen.  
   
   ![De routes opgeven](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-7.png)

    U kunt de *route* vervangen door de routetekenreeks die u eerder hebt gekopieerd. In dit voorbeeld geeft u de naam op van de lokale share die gegevens naar de cloudshare pusht. Vervang de `modulename` door de naam van het bestand. Selecteer **Next**.
        
    ```
    "route&quot;: &quot;FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/filemove/inputs/input1\")"
    ```

    ![Het gedeelte Routes opgeven](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-8.png)

6. Controleer alle instellingen onder **Implementatie controleren**. Klik vervolgens op **Verzenden** om de module te verzenden voor implementatie.

   ![Pagina Modules instellen 2](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-9.png)
 
    Met deze actie wordt de implementatie van de module gestart. Nadat de implementatie is voltooid, wordt de **runtime-status** van module  **uitgevoerd**.

    ![Aangepaste module toevoegen 3](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-10.png)

## <a name="verify-data-transform-transfer"></a>Gegevenstransformatie controleren, gegevens overdragen

De laatste stap is controleren of de module is verbonden en wordt uitgevoerd zoals verwacht. De runtimestatus van de module moet 'Wordt uitgevoerd' zijn voor uw IoT Edge-apparaat in de IoT Hub-resource.

Voer de volgende stappen uit om de gegevenstransformatie te controleren en over te dragen naar Azure.
 
1. Maak via Verkenner verbinding met zowel de lokale Edge-share als de Edge-shares die u eerder hebt gemaakt.

   ![Gegevenstransformatie controleren](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-2.png)
 
1. Voeg gegevens toe aan de lokale share.

   ![Gegevenstransformatie controleren 2](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-3.png)
 
    De gegevens worden verplaatst naar de cloudshare.

    ![Gegevenstransformatie controleren 3](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-4.png)  

    De gegevens worden vervolgens vanuit de cloudshare naar het opslagaccount gepusht. Als u de gegevens wilt bekijken, gaat u naar uw opslagaccount en selecteert u **Storage Explorer**. U kunt de geüploade gegevens in uw opslagaccount weergeven.

    ![Gegevenstransformatie controleren 4](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-5.png)
 
U hebt het validatieproces voltooid.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Rekenproces configureren
> * Shares toevoegen
> * Een trigger toevoegen
> * Een rekenprocesmodule toevoegen
> * Gegevenstransformatie controleren en gegevens overdragen

Als u wilt weten hoe u uw Azure Stack Edge Pro-apparaat beheert, gaat u naar:

> [!div class="nextstepaction"]
> [De lokale webgebruikersinterface gebruiken voor het beheren van een Azure Stack Edge Pro](azure-stack-edge-manage-access-power-connectivity-mode.md)
