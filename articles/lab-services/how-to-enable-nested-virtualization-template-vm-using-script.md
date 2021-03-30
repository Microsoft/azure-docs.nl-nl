---
title: Geneste virtualisatie inschakelen op een sjabloon-VM in Azure Lab Services (script) | Microsoft Docs
description: Meer informatie over het maken van een sjabloon-VM met meerdere virtuele machines in met behulp van een script.  Met andere woorden, schakel geneste virtualisatie in op een sjabloon-VM in Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5ae50bd11ab9a8adb769920f6d473a2ff2ce9342
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91251492"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-using-a-script"></a>Geneste virtualisatie inschakelen op een virtuele machine van een sjabloon in Azure Lab Services met behulp van een script

Met geneste virtualisatie kunt u een multi-VM-omgeving maken in de virtuele machine van een lab. Als u de sjabloon publiceert, wordt elke gebruiker in het lab voorzien van een virtuele machine die is ingesteld met meerdere virtuele machines in de test omgeving.  Zie voor meer informatie over geneste virtualisatie en Azure Lab Services [geneste virtualisatie inschakelen op een virtuele machine van een sjabloon in Azure Lab Services](how-to-enable-nested-virtualization-template-vm.md).

De stappen in dit artikel zijn gericht op het instellen van geneste virtualisatie voor Windows Server 2016, Windows Server 2019 of Windows 10. U gebruikt een script voor het instellen van de sjabloon computer met Hyper-V.  De volgende stappen begeleiden u bij het gebruik van de [Lab-Services Hyper-V-scripts](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV).

>[!IMPORTANT]
>Selecteer **groot (geneste virtualisatie)** of **Gemiddeld (geneste virtualisatie)** voor de grootte van de virtuele machine bij het maken van het lab.  Geneste virtualisatie werkt anders niet.  

## <a name="run-script"></a>Script uitvoeren

1. Als u Internet Explorer gebruikt, moet u mogelijk toevoegen `https://github.com` aan de lijst met vertrouwde websites.
    1. Open Internet Explorer.
    1. Selecteer het tandwiel pictogram en kies **Internet opties**.  
    1. Wanneer het dialoog venster **Internet opties** wordt weer gegeven, selecteert u **beveiliging**, selecteer **vertrouwde sites** en klikt u op de knop **sites** .
    1. Wanneer het dialoog venster **vertrouwde sites** wordt weer gegeven, voegt `https://github.com` u toe aan de lijst met vertrouwde websites en selecteert u **sluiten**.

        ![Vertrouwde sites](./media/how-to-enable-nested-virtualization-template-vm-using-script/trusted-sites-dialog.png)
1. Down load de Git-opslagplaats bestanden zoals beschreven in de volgende stappen.
    1. Ga naar  [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/) .
    1. Klik op de knop **klonen of downloaden** .
    1. Klik op **down load zip**.
    1. Het ZIP-bestand uitpakken

    >[!TIP]
    >U kunt ook de Git-opslag plaats klonen op [https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git) .

1. Start **Power shell** in de **beheerders** modus.
1. Navigeer in het Power shell-venster naar de map met het gedownloade script. Als u vanuit de bovenste map van de opslagplaats bestanden navigeert, bevindt het script zich op `azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\` .
1. Mogelijk moet u het uitvoerings beleid wijzigen om het script uit te voeren. Voer de volgende opdracht uit:

    ```powershell
    Set-ExecutionPolicy bypass -force
    ```

1. Voer het volgende script uit:

    ```powershell
    .\SetupForNestedVirtualization.ps1
    ```

    > [!NOTE]
    > Het script vereist mogelijk dat de computer opnieuw wordt opgestart. Volg de instructies in het script en voer het script opnieuw uit totdat het **script is voltooid** wordt weer gegeven in de uitvoer.
1. Vergeet niet om het uitvoerings beleid opnieuw in te stellen. Voer de volgende opdracht uit:

    ```powershell
    Set-ExecutionPolicy default -force
    ```

## <a name="conclusion"></a>Conclusie

Uw sjabloon machine is nu gereed voor het maken van virtuele Hyper-V-machines. Zie [een virtuele machine maken in Hyper-v](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) voor instructies over het maken van virtuele Hyper-v-machines. Zie ook [micro soft Evaluation Center](https://www.microsoft.com/evalcenter/) om beschik bare besturings systemen en software te bekijken.  

## <a name="next-steps"></a>Volgende stappen

De volgende stappen zijn gebruikelijk voor het instellen van elk lab.

- [Gebruikers toevoegen](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quota instellen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Een planning instellen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-mail registratie koppelingen naar studenten](how-to-configure-student-usage.md#send-invitations-to-users)