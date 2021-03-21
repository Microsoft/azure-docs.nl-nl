---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/02/2019
ms.author: alkohli
ms.openlocfilehash: 203c977fe9109cd8b2b6de561e975e20aacf700e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96185533"
---
Als dit de eerste keer is dat u Storage Explorer gebruikt, moet u de volgende stappen uitvoeren.

1. Ga in de bovenste opdrachtbalk naar **Bewerken > Azure Stack-API’s als doel**.

    ![Storage Explorer configureren](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-1.png)

2. Start Storage Explorer opnieuw op om de wijzigingen door te voeren.


Volg deze stappen om verbinding te maken met het opslagaccount en de verbinding te controleren.

1. Selecteer opslagaccounts in Storage Explorer. Klik met de rechtermuisknop en selecteer de optie **Verbinding maken met Azure Storage**. 

    ![Storage Explorer configureren 2](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-2.png)

2. Selecteer in het dialoogvenster **Verbinding maken met Azure Storage** de optie **Een opslagaccountnaam en -sleutel gebruiken**.

    ![Storage Explorer configureren 3](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-3.png)

2. Voer in het dialoogvenster **Verbinding maken met naam en sleutel** de volgende stappen uit:

    1. Voer een weergavenaam voor uw Edge-opslagaccount in. 
    2. Geef de naam voor het Edge-opslagaccount op.
    3. Plak de toegangssleutel die u via Azure Resource Manager hebt opgehaald uit de lokale API’s van het apparaat.
    4. Selecteer **Andere (hieronder opgeven)** als opslagdomein en geef het achtervoegsel van het blobservice-eindpunt op in de indeling: `<appliance name>.<DNSdomain>`. 
    5. Selecteer de optie **HTTP gebruiken** voor overdracht via *HTTP*. 
    6. Selecteer **Next**.

    ![Storage Explorer configureren 4](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-4.png)    

3. Controleer in het dialoogvenster **Samenvatting verbinding** de gegevens die u hebt opgegeven. Selecteer **Verbinding maken**.

    ![Storage Explorer configureren 5](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-5.png)

4. Het account dat is toegevoegd, wordt weergegeven in het linkerdeelvenster van Storage Explorer met (Externe, Andere) toegevoegd aan de naam. Selecteer **Blobcontainers** om de container weer te geven.

    ![Blobcontainers weergeven](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-6.png)

De volgende stap is te verifiëren dat de gegevensoverdracht goed werkt via deze verbinding.

Voer de volgende stappen uit om gegevens te laden in uw Edge-opslagaccount op het apparaat. Ze zouden automatisch moeten worden gelaagd in het toegewezen Azure Storage-account.

1. Selecteer de container waarin u de gegevens in uw Edge-opslagaccount wilt laden. Selecteer **Uploaden** en selecteer vervolgens **Bestanden uploaden**.

    ![Gegevensoverdracht verifiëren](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-1.png)

2. In het dialoogvenster **Bestanden uploaden** navigeert u naar en selecteert u de bestanden die u wilt uploaden. Selecteer **Next**.

    ![Gegevensoverdracht verifiëren 2](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-2.png)

3. Verifieer dat de bestanden zijn geüpload. De geüploade bestanden verschijnen in de container.

    ![Gegevensoverdracht verifiëren 3](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-3.png)

4. Vervolgens maakt u verbinding met het Azure Storage-account dat is toegewezen aan dit Edge-opslagaccount. Gegevens die naar het Edge-opslagaccount worden geüpload, zouden automatisch moeten worden gelaagd in het Azure Storage-account. 
    
    Om de verbindingsreeks voor het Azure Storage-account op te halen, gaat u naar het **Azure Storage-account > Toegangssleutels** en kopieert u de verbindingsreeks.

    ![Gegevensoverdracht verifiëren 4](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-5.png)

    Gebruik de verbindingsreeks om verbinding te maken met het Azure Storage-account.  

    ![Gegevensoverdracht verifiëren 5](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-4.png)


5. Controleer in het dialoogvenster **Samenvatting verbinding** de gegevens die u hebt opgegeven. Selecteer **Verbinding maken**.

    ![Gegevensoverdracht verifiëren 6](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-6.png)

6. U ziet dat de bestanden die u hebt geüpload in het Edge-opslagaccount, zijn overgedragen naar het Azure Storage-account.

    ![Gegevensoverdracht verifiëren 7](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-7.png)
