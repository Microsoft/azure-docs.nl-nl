---
title: Vm's implementeren op uw Azure Stack Edge Pro via de Azure Portal
description: Meer informatie over het maken en beheren van virtuele machines op uw Azure Stack Edge Pro via de Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro device so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 6054e7e79acaa6abf304508221c63143b9d14a45
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102436529"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-the-azure-portal"></a>Implementeer Vm's op uw Azure Stack Edge Pro GPU-apparaat via de Azure Portal

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

U kunt virtuele machines (Vm's) maken en beheren op een Azure Stack edge-apparaat met behulp van Azure Portal, sjablonen, Azure PowerShell-cmdlets en via Azure CLI/python-scripts. In dit artikel wordt beschreven hoe u een virtuele machine op uw Azure Stack edge-apparaat maakt en beheert met behulp van de Azure Portal. 

Dit artikel is van toepassing op Azure Stack Edge Pro GPU, Azure Stack Edge Pro R en Azure Stack Edge mini-R-apparaten. 

> [!IMPORTANT] 
> U wordt aangeraden multi-factor Authentication in te scha kelen voor de gebruiker die virtuele machines beheert die in de Cloud op uw apparaat zijn geïmplementeerd.
        
## <a name="vm-deployment-workflow"></a>VM-implementatiewerkstroom

De samen vatting van het hoge niveau van de implementatie werk stroom is als volgt:

1. Een netwerk interface voor Compute op uw Azure Stack edge-apparaat inschakelen. Hiermee maakt u een virtuele switch op de opgegeven netwerk interface.
1. Schakel Cloud beheer van virtuele machines van Azure Portal in.
1. Upload een VHD naar een Azure Storage-account met behulp van Storage Explorer. 
1. Gebruik de geüploade VHD om de VHD op het apparaat te downloaden en een VM-installatie kopie te maken van de VHD. 
1. Gebruik de resources die zijn gemaakt in de vorige stappen:
    1. VM-installatie kopie die u hebt gemaakt.
    1. VSwitch gekoppeld aan de netwerk interface waarvoor u Compute hebt ingeschakeld.
    1. Het subnet dat is gekoppeld aan de VSwitch.

    En maak of specificeer de volgende resources inline:
    1. VM-naam, kies een ondersteunde VM-grootte, aanmeldings referenties voor de virtuele machine. 
    1. Maak nieuwe gegevens schijven of koppel bestaande gegevens schijven.
    1. Configureer een statisch of dynamisch IP-adres voor de virtuele machine. Als u een statisch IP-adres hebt opgegeven, kiest u uit een gratis IP-adres in het subnet-bereik van de netwerk interface die voor Compute is ingeschakeld.

    Gebruik de bovenstaande resources om een virtuele machine te maken.


## <a name="prerequisites"></a>Vereisten

Voordat u begint met het maken en beheren van virtuele machines op uw apparaat via de Azure Portal, moet u ervoor zorgen dat:

1. U hebt de netwerk instellingen op uw Azure Stack Edge Pro-apparaat voltooid, zoals wordt beschreven in [stap 1: Azure stack Edge Pro-apparaat configureren](azure-stack-edge-j-series-connect-resource-manager.md#step-1-configure-azure-stack-edge-pro-device).

    1. U hebt een netwerk interface ingeschakeld voor compute. Het IP-adres van de netwerkinterface wordt gebruikt om een virtuele switch te maken voor de VM-implementatie. Ga in de lokale gebruikers interface van uw apparaat naar **Compute**. Selecteer de netwerkinterface die u wilt gebruiken om een virtuele switch te maken.

        > [!IMPORTANT] 
        > U kunt slechts één poort configureren voor berekeningen.

    1. Schakel Compute in op de netwerkinterface. Azure Stack Edge Pro maakt en beheert een virtuele switch die overeenkomt met die netwerkinterface.

1. U hebt toegang tot een VHD met Windows of Linux die u wilt gebruiken voor het maken van de VM-installatie kopie voor de virtuele machine die u wilt maken.

## <a name="deploy-a-vm"></a>Een virtuele machine implementeren

Volg deze stappen om een virtuele machine te maken op uw Azure Stack edge-apparaat.

### <a name="add-a-vm-image"></a>Een VM-installatie kopie toevoegen

1. Upload een VHD naar een Azure Storage-account. Volg de stappen in [een VHD uploaden met behulp van Azure Storage Explorer](../devtest-labs/devtest-lab-upload-vhd-using-storage-explorer.md).

1. Ga in het Azure Portal naar de resource Azure Stack Edge voor uw Azure Stack edge-apparaat. Ga naar **Edge compute > virtual machines**.

    ![VM-installatie kopie 1 toevoegen](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-1.png)

1. Selecteer **virtual machines** om naar de pagina **overzicht** te gaan. Cloud beheer van virtuele machines **inschakelen** .
    ![VM-installatie kopie 2 toevoegen](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-2.png)

1. De eerste stap is het toevoegen van een VM-installatie kopie. U hebt al een VHD geüpload naar het opslag account in de vorige stap. U gebruikt deze VHD om een VM-installatie kopie te maken.

    Selecteer **afbeelding toevoegen** om de VHD te downloaden van het opslag account en toe te voegen aan het apparaat. Het download proces duurt enkele minuten, afhankelijk van de grootte van de VHD en de beschik bare Internet bandbreedte voor de down load. 

    ![VM-installatie kopie toevoegen 3](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-3.png)

1. Voer op de Blade **afbeelding toevoegen** de volgende para meters in. Selecteer **Toevoegen**.


    |Parameter  |Beschrijving  |
    |---------|---------|
    |Downloaden van opslag-BLOB    |Blader naar de locatie van de opslag-Blob in het opslag account waarnaar u de VHD hebt geüpload.         |
    |Downloaden naar    | Automatisch ingesteld op het huidige apparaat waarop u de virtuele machine implementeert.        |
    |Afbeelding opslaan als      | De naam van de VM-installatie kopie die u maakt op basis van de VHD die u hebt geüpload naar het opslag account.        |
    |Type besturings systeem     |Kies uit Windows of Linux als het besturings systeem van de VHD die u gaat gebruiken voor het maken van de VM-installatie kopie.         |
   

    ![VM-installatie kopie toevoegen 4](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-6.png)

1. De VHD wordt gedownload en de VM-installatie kopie wordt gemaakt. Het maken van de installatie kopie duurt enkele minuten. U ziet een melding voor de geslaagde voltooiing van de VM-installatie kopie.

    ![VM-installatie kopie 5 toevoegen](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-8.png)


1. Nadat de VM-installatie kopie is gemaakt, wordt deze toegevoegd aan de lijst met installatie kopieën op de Blade **afbeeldingen** .
    ![VM-installatie kopie toevoegen 6](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-9.png)

    De Blade **implementaties** wordt bijgewerkt om de status van de implementatie aan te geven.

    ![VM-installatie kopie toevoegen 7](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-10.png)

    De zojuist toegevoegde afbeelding wordt ook weer gegeven op de pagina **overzicht** .
    ![VM-installatie kopie toevoegen 8](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-11.png)


### <a name="add-a-vm"></a>Een VM toevoegen

Volg deze stappen om een virtuele machine te maken nadat u een VM-installatie kopie hebt gemaakt.

1. Selecteer op de pagina **overzicht** de optie **virtuele machine toevoegen**.

    ![VM 1 toevoegen](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-1.png)

1. Voer op het tabblad **basis beginselen** de volgende para meters in.


    |Parameter |Beschrijving  |
    |---------|---------|
    |Naam van de virtuele machine     |         |
    |Installatiekopie     | Selecteer een van de VM-installatie kopieën die beschikbaar zijn op het apparaat.        |
    |Grootte     | Kies uit de [ondersteunde VM-grootten](azure-stack-edge-gpu-virtual-machine-sizes.md).        |
    |Gebruikersnaam     | Gebruik de standaard gebruikers naam *azureuser*.        |
    |Verificatietype    | U kunt kiezen uit een open bare SSH-sleutel of een door de gebruiker gedefinieerd wacht woord.       |
    |Wachtwoord     | Voer een wacht woord in om u aan te melden bij de virtuele machine. Het wacht woord moet ten minste 12 tekens lang zijn en voldoen aan de gedefinieerde [complexiteits vereisten](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).        |
    |Wachtwoord bevestigen    | Voer het wachtwoord nogmaals in.        |


    ![VM 2 toevoegen](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-basics-1.png)

    Selecteer **Volgende: Schijven**.

1. Op het tabblad **schijven** koppelt u schijven aan de VM. 
    
    1. U kunt ervoor kiezen om **een nieuwe schijf te maken en te koppelen** of om **een bestaande schijf toe te voegen**.

        ![VM 3 toevoegen](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-1.png)

    1. Selecteer **maken en voeg een nieuwe schijf toe**. Geef op de Blade **nieuwe schijf maken** een naam op voor de schijf en de grootte in GiB.

        ![VM 4 toevoegen](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-2.png)

    1.  Herhaal de bovenstaande stappen om meer schijven toe te voegen. Nadat de schijven zijn gemaakt, worden ze weer gegeven op het tabblad **schijven** .

        ![VM toevoegen 5](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-3.png)

        Selecteer **Volgende: Netwerken**.

1. Op het tabblad **netwerken** configureert u de netwerk verbinding voor uw VM.

    
    |Parameter  |Beschrijving |
    |---------|---------|
    |Virtueel netwerk    | Selecteer in de vervolg keuzelijst de virtuele switch die op uw Azure Stack edge-apparaat is gemaakt wanneer u Compute op de netwerk interface hebt ingeschakeld.    |
    |Subnet     | Dit veld wordt automatisch ingevuld met het subnet dat is gekoppeld aan de netwerk interface waarvoor u Compute hebt ingeschakeld.         |
    |IP-adres     | Geef een statisch of dynamisch IP-adres voor uw virtuele machine op. Het statische IP-adres moet een beschikbaar, vrij IP-adres zijn van het opgegeven subnet-bereik.        |

    ![VM 6 toevoegen](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-networking-1.png)

    Selecteer **Volgende: Beoordelen en maken**.

1. Controleer op het tabblad **controleren en maken** de specificaties voor de virtuele machine en selecteer **maken**.

    ![VM 7 toevoegen](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-review-create-1.png)

1. Het maken van de VM begint en kan Maxi maal 20 minuten duren. U kunt naar **implementaties** gaan om het maken van de virtuele machine te controleren.

    ![VM 8 toevoegen](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-deployments-page-1.png)

    
1. Nadat de VM is gemaakt, wordt de **overzichts** pagina bijgewerkt om de nieuwe VM weer te geven.

    ![VM 9 toevoegen](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-overview-page-1.png)

1. Selecteer de zojuist gemaakte VM om naar **virtuele machines** te gaan.

    ![VM toevoegen 10](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-page-1.png)

    Selecteer de virtuele machine om de details weer te geven. 

    ![VM 11 toevoegen](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-details-1.png)

## <a name="connect-to-a-vm"></a>Verbinding maken met een virtuele machine

Afhankelijk van of u een Windows-of een Linux-VM hebt gemaakt, kunnen de stappen om verbinding te maken verschillend zijn. U kunt via de Azure Portal geen verbinding maken met de Vm's die op uw apparaat zijn geïmplementeerd. U moet de volgende stappen uitvoeren om verbinding te maken met uw Linux-of Windows-VM.

### <a name="connect-to-linux-vm"></a>Verbinding maken met een virtuele Linux-machine

Volg deze stappen om verbinding te maken met een virtuele Linux-machine.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

### <a name="connect-to-windows-vm"></a>Verbinding maken met Windows VM

Volg deze stappen om verbinding te maken met een virtuele Windows-machine.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het beheren van uw Azure Stack Edge Pro-apparaat de[lokale web-UI gebruiken om een Azure stack Edge Pro te beheren](azure-stack-edge-manage-access-power-connectivity-mode.md).
