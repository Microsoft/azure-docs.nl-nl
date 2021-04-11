---
title: Vm's beheren op Azure Stack Edge Pro GPU, Pro R, mini-R via Azure Portal
description: Meer informatie over het beheren van schijven, waaronder het toevoegen of ontkoppelen van een gegevens schijf op virtuele machines die zijn geïmplementeerd op uw Azure Stack Edge Pro GPU, Azure Stack Edge Pro R en Azure Stack Edge mini-R via de Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/30/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to manage disks on a VM running on an Azure Stack Edge Pro device so that I can use it to run applications using Edge compute before sending it to Azure.
ms.openlocfilehash: dff3f4bafdb35d89e8f1792c1aca68824a9bc685
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080319"
---
# <a name="use-the-azure-portal-to-manage-disks-on-the-vms-on-your-azure-stack-edge-pro-gpu"></a>Gebruik de Azure Portal voor het beheren van schijven op de virtuele machines op uw Azure Stack Edge Pro GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

U kunt met behulp van de Azure Portal schijven op de virtuele machines (Vm's) inrichten die op uw Azure Stack Edge Pro-apparaat zijn geïmplementeerd. De schijven worden ingericht op het apparaat via de lokale Azure Resource Manager en verbruikt de capaciteit van het apparaat. De bewerkingen, zoals het toevoegen van een schijf, het ontkoppelen van een schijf, kunnen worden uitgevoerd via de Azure Portal, die op zijn beurt aanroepen naar de lokale Azure Resource Manager om de opslag in te richten. 

In dit artikel wordt uitgelegd hoe u een gegevens schijf aan een bestaande virtuele machine toevoegt, hoe u een gegevens schijf loskoppelt en ten slotte het formaat van de virtuele machine zelf wijzigt via de Azure Portal. 

        
## <a name="about-disks-on-vms"></a>Over schijven op Vm's

Uw virtuele machine kan een besturingssysteem schijf en een gegevens schijf bevatten. Elke virtuele machine die op het apparaat is geïmplementeerd, heeft een gekoppelde besturingssysteem schijf. Deze besturingssysteem schijf heeft een vooraf geïnstalleerd besturings systeem dat is geselecteerd toen de virtuele machine werd gemaakt. Deze schijf bevat het opstartvolume.

> [!NOTE]
> U kunt de grootte van de besturingssysteem schijf voor de virtuele machine op het apparaat niet wijzigen. De grootte van de besturingssysteem schijf wordt bepaald door de grootte van de virtuele machine die u hebt geselecteerd. 


Een gegevens schijf aan de andere kant is een beheerde schijf die is gekoppeld aan de virtuele machine die op uw apparaat wordt uitgevoerd. Er wordt een gegevens schijf gebruikt om toepassings gegevens op te slaan. Gegevens schijven zijn doorgaans SCSI-schijven. De grootte van de virtuele machine bepaalt hoeveel gegevens schijven u kunt koppelen aan een virtuele machine. Premium-opslag wordt standaard gebruikt voor het hosten van de schijven.

Een VM die op het apparaat is geïmplementeerd, kan soms een tijdelijke schijf bevatten. De tijdelijke schijf biedt kortetermijnbeveiliging voor toepassingen en processen en is bedoeld om alleen gegevens op te slaan, zoals pagina-of Wissel bestanden. Gegevens op de tijdelijke schijf kunnen verloren gaan tijdens een onderhoudsgebeurtenis of wanneer u een virtuele machine opnieuw implementeert. Tijdens een geslaagde standaard herstart van de virtuele machine blijven de gegevens op de tijdelijke schijf behouden. 


## <a name="prerequisites"></a>Vereisten

Voordat u begint met het beheren van schijven op de virtuele machines die op uw apparaat worden uitgevoerd via de Azure Portal, moet u ervoor zorgen dat:


1. U hebt toegang tot een geactiveerd Azure Stack Edge Pro GPU-apparaat. U hebt ook een netwerk interface ingeschakeld voor Compute op het apparaat. Met deze actie maakt u een virtuele switch op de netwerk interface van de virtuele machine. 
    1. Ga in de lokale gebruikers interface van uw apparaat naar **Compute**. Selecteer de netwerkinterface die u wilt gebruiken om een virtuele switch te maken.

        > [!IMPORTANT] 
        > U kunt slechts één poort configureren voor berekeningen.

    1. Schakel Compute in op de netwerkinterface. Met Azure Stack Edge Pro GPU wordt een virtuele switch gemaakt en beheerd die overeenkomt met die netwerk interface.

1. Er is ten minste één virtuele machine op het apparaat geïmplementeerd. Als u deze virtuele machine wilt maken, raadpleegt u de instructies in [virtuele machine implementeren op uw Azure stack Edge Pro via de Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).



## <a name="add-a-data-disk"></a>Een gegevens schijf toevoegen

Volg deze stappen om een schijf toe te voegen aan een virtuele machine die op het apparaat is geïmplementeerd. 

1. Ga naar de virtuele machine waaraan u een gegevens schijf wilt toevoegen en ga vervolgens naar de pagina **overzicht** . Selecteer **Schijven**.
    
    ![Schijven selecteren op de pagina overzicht](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-1.png)

1. Selecteer op de Blade **schijven** onder **gegevens schijven** **maken en voeg een nieuwe schijf toe**.

    ![Een nieuwe schijf maken en koppelen](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-2.png)

1. Voer op de Blade **een nieuwe schijf maken** de volgende para meters in:

    
    |Veld  |Beschrijving  |
    |---------|---------|
    |Name     | Een unieke naam binnen de resource groep. De naam kan niet worden gewijzigd nadat de gegevens schijf is gemaakt.     |
    |Grootte| De grootte van uw gegevens schijf in GiB. De maximale grootte van een gegevens schijf wordt bepaald door de grootte van de virtuele machine die u hebt geselecteerd. Bij het inrichten van een schijf moet u ook rekening houden met de werkelijke ruimte op het apparaat en andere VM-workloads die worden uitgevoerd en die gebruikmaken van capaciteit.  |         

    ![Een nieuwe schijf-Blade maken](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-3.png)

    Selecteer **OK** en ga door.

1. Op de pagina **overzicht** ziet u onder **schijven** een vermelding die overeenkomt met de nieuwe schijf. Accepteer de standaard waarde of wijs een geldig LUN (Logical Unit Number) toe aan de schijf en selecteer **Opslaan**. Een LUN is een unieke id voor een SCSI-schijf. Zie [Wat is een LUN?](../virtual-machines/linux/azure-to-guest-disk-mapping.md#what-is-a-lun)voor meer informatie.

    ![Nieuwe schijf op overzichts pagina](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-4.png)

1. Er wordt een melding weer geven dat het maken van de schijf wordt uitgevoerd. Nadat de schijf is gemaakt, wordt de virtuele machine bijgewerkt. 

    ![Melding voor het maken van schijven](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-5.png)

1. Ga terug naar de pagina **overzicht** . De lijst met schijven die moeten worden bijgewerkt om de zojuist gemaakte gegevens schijf weer te geven.

    ![Bijgewerkte lijst met gegevens schijven](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-6.png)


## <a name="change-a-data-disk"></a>Een gegevens schijf wijzigen

Volg deze stappen om een schijf die is gekoppeld aan een virtuele machine die op het apparaat is geïmplementeerd, te wijzigen.

1. Ga naar de virtuele machine met de gegevens schijf die u wilt wijzigen en ga naar de pagina **overzicht** . Selecteer **Schijven**.

1. Selecteer in de lijst met gegevens schijven de schijf die u wilt wijzigen. Selecteer helemaal rechts van de schijf die u hebt geselecteerd het bewerkings pictogram (potlood).  

    ![Een te wijzigen schijf selecteren](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/edit-data-disk-1.png)

1. Op de Blade **schijf wijzigen** kunt u alleen de grootte van de schijf wijzigen. De naam die is gekoppeld aan de schijf kan niet worden gewijzigd nadat deze is gemaakt. Wijzig de **grootte** en sla de wijzigingen op.

    ![Grootte van de gegevens schijf wijzigen](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/edit-data-disk-2.png)

    > [!NOTE]
    > U kunt een gegevens schijf alleen uitbreiden, u kunt de schijf niet verkleinen.

1. Op de pagina **overzicht** wordt de lijst met schijven vernieuwd om de bijgewerkte schijf weer te geven.


## <a name="attach-an-existing-disk"></a>Een bestaande schijf koppelen

Voer de volgende stappen uit om een bestaande schijf te koppelen aan de virtuele machine die op het apparaat is geïmplementeerd.

1. Ga naar de virtuele machine waarnaar u de bestaande schijf wilt koppelen en ga vervolgens naar de pagina **overzicht** . Selecteer **Schijven**.
    
    ![Schijven selecteren ](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-1.png)

1. Selecteer op de Blade **schijven** onder **gegevens schijven** de optie **een bestaande schijf koppelen**.

    ![Selecteer een bestaande schijf koppelen](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/attach-existing-data-disk-1.png)

1. Accepteer de standaard-LUN of wijs een geldige LUN toe. Kies een bestaande gegevens schijf in de vervolg keuzelijst. Selecteer Opslaan.

    ![Een bestaande schijf selecteren](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/attach-existing-data-disk-2.png)

    Selecteer **Opslaan** en door gaan.

1. U ziet een melding dat de virtuele machine is bijgewerkt. Nadat de VM is bijgewerkt, gaat u terug naar de pagina **overzicht** . Vernieuw de pagina om de zojuist gekoppelde schijf weer te geven in de lijst met gegevens schijven.

    ![Bijgewerkte lijst met gegevens schijven weer geven op de pagina overzicht](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-2.png)


## <a name="detach-a-data-disk"></a>Een gegevensschijf ontkoppelen

Volg deze stappen voor het ontkoppelen of verwijderen van een gegevens schijf die is gekoppeld aan een virtuele machine die op het apparaat is geïmplementeerd.

> [!NOTE]
> - U kunt een gegevens schijf verwijderen terwijl de virtuele machine wordt uitgevoerd. Zorg ervoor dat er niets actief gebruikmaakt van de schijf voordat u deze loskoppelt van de virtuele machine.
> - Als u een schijf loskoppelt, wordt deze niet automatisch verwijderd.

1. Ga naar de virtuele machine van waaruit u een gegevens schijf wilt loskoppelen en ga naar de pagina **overzicht** . Selecteer **Schijven**.

    ![Schijven selecteren](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-1.png)

1. Selecteer in de lijst met schijven de schijf die u wilt loskoppelen. Selecteer helemaal rechts van de schijf die u hebt geselecteerd het pictogram loskoppelen (kruisen). Het geselecteerde item wordt ontkoppeld. Selecteer **Opslaan**. 

    ![Een te ontkoppelen schijf selecteren](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/detach-data-disk-1.png)

1. Nadat de schijf is ontkoppeld, wordt de virtuele machine bijgewerkt. Vernieuw de pagina **overzicht** om de bijgewerkte lijst met gegevens schijven weer te geven.

    ![Selecteer opslaan](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-2.png)


## <a name="next-steps"></a>Volgende stappen

Zie [virtuele machines implementeren via de Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)voor meer informatie over het implementeren van virtuele machines op uw Azure stack Edge Pro-apparaat.
