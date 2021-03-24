---
title: VM-installatie kopieën maken van een gegeneraliseerde installatie kopie van Windows VHD voor uw Azure Stack Edge Pro GPU-apparaat
description: Hierin wordt beschreven hoe u VM-installatie kopieën van gegeneraliseerde installatie kopieën vanaf een Windows-VHD of VHDX. Gebruik deze gegeneraliseerde installatie kopie om VM-installatie kopieën te maken voor gebruik met Vm's die op uw Azure Stack Edge Pro GPU-apparaat zijn geïmplementeerd.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/18/2021
ms.author: alkohli
ms.openlocfilehash: 978099accd57d15c750a27f77b2e220f569a2dd0
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104962524"
---
# <a name="use-generalized-image-from-windows-vhd-to-create-a-vm-image-for-your-azure-stack-edge-pro-device"></a>Gegeneraliseerde installatie kopie van Windows VHD gebruiken om een VM-installatie kopie te maken voor uw Azure Stack Edge Pro-apparaat

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Om VM's te implementeren op uw Azure Stack Edge Pro-apparaat, moet u aangepaste VM-installatiekopieën kunnen maken om VM's mee te maken. In dit artikel worden de stappen beschreven die nodig zijn om een Windows-VHD of VHDX voor te bereiden om een gegeneraliseerde installatie kopie te maken. Deze gegeneraliseerde installatie kopie wordt vervolgens gebruikt voor het maken van een VM-installatie kopie voor uw Azure Stack Edge Pro-apparaat. 

## <a name="about-preparing-windows-vhd"></a>Over het voorbereiden van Windows VHD

Een VHD of VHDX van Windows kan worden gebruikt om een *gegeneraliseerde* installatie kopie of een *gespecialiseerde* installatie kopie te maken. De volgende tabel bevat een overzicht van de belangrijkste verschillen tussen de *gegeneraliseerde* en de *gespecialiseerde* installatie kopieën.


|Type installatiekopie  |Gegeneraliseerd  |Gespecialiseerd  |
|---------|---------|---------|
|Doel     |Geïmplementeerd op elk systeem         | Gericht op een specifiek systeem        |
|Setup na opstarten     | Setup is vereist om de virtuele machine eerst op te starten.          | Setup is niet nodig. <br> De virtuele machine wordt ingeschakeld op het platform.        |
|Configuratie     |Hostnaam, admin-gebruiker en andere VM-specifieke instellingen vereist.         |Volledig vooraf geconfigureerd.         |
|Gebruikt wanneer     |Er worden meerdere nieuwe Vm's gemaakt op basis van dezelfde installatie kopie.         |Een specifieke computer migreren of een virtuele machine herstellen vanaf de vorige back-up.         |


In dit artikel worden de stappen besproken die nodig zijn om te implementeren vanuit een gegeneraliseerde installatie kopie. Zie [speciale Windows-VHD gebruiken](azure-stack-edge-placeholder.md) voor uw apparaat voor meer informatie over het implementeren van een gespecialiseerde installatie kopie.

> [!IMPORTANT]
> Deze procedure geldt niet voor gevallen waarin de bron-VHD is geconfigureerd met aangepaste configuraties en instellingen. Er zijn bijvoorbeeld extra acties vereist om een VHD met aangepaste firewall regels of proxy-instellingen te generaliseren. Zie [een Windows-VHD voorbereiden om te uploaden naar Azure-azure virtual machines](../virtual-machines/windows/prepare-for-upload-vhd-image.md)voor meer informatie over deze aanvullende acties.


## <a name="vm-image-workflow"></a>Werkstroom voor VM-installatiekopie

De werk stroom op hoog niveau voor het voorbereiden van een Windows VHD voor gebruik als een gegeneraliseerde installatie kopie heeft de volgende stappen:

1. Converteer de bron-VHD of VHDX naar een VHD met een vaste grootte.
1. Maak een virtuele machine in Hyper-V met behulp van de vaste VHD.
1. Maak verbinding met de Hyper-V-VM.
1. Generaliseer de VHD met het hulp programma *Sysprep* . 
1. Kopieer de gegeneraliseerde installatie kopie naar Blob Storage.
1. Gebruik de gegeneraliseerde installatie kopie om Vm's op uw apparaat te implementeren. Zie [een virtuele machine implementeren via Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md) of [een VM implementeren via Power shell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)voor meer informatie.


## <a name="prerequisites"></a>Vereisten

Voordat u een Windows-VHD voorbereidt voor gebruik als een gegeneraliseerde installatie kopie op Azure Stack Edge, moet u ervoor zorgen dat:

- U hebt een VHD of een VHDX die een ondersteunde versie van Windows bevat. Zie [Ondersteunde gast]() besturingssystemen voor uw Azure stack Edge Pro. 
- U hebt toegang tot een Windows-client waarop Hyper-V-beheer is geïnstalleerd. 
- U hebt toegang tot een Azure Blob-opslag account om uw VHD op te slaan nadat deze is voor bereid.

## <a name="prepare-a-generalized-windows-image-from-vhd"></a>Een gegeneraliseerde Windows-installatie kopie voorbereiden vanaf de VHD

## <a name="convert-to-a-fixed-vhd"></a>Converteren naar een vaste VHD 

Voor uw apparaat hebt u harde schijven met een vaste grootte nodig om VM-installatie kopieën te maken. U moet uw bron-Windows VHD of VHDX converteren naar een vaste VHD. Volg deze stappen:

1. Open Hyper-V-beheer op het client systeem. Ga naar de **schijf bewerken**.

    ![Hyper-V-beheer openen](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-1.png)

1. Selecteer op de pagina **voordat u begint** de optie **volgende>**.

1. Blader op de pagina **virtuele harde schijf zoeken** naar de locatie van de bron-Windows VHD of VHDX die u wilt converteren. Selecteer **volgende>**.

    ![Pagina virtuele harde schijf zoeken](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-2.png)

1. Selecteer op de pagina **actie kiezen** de optie **converteren** en selecteer **volgende>**.

    ![Actie pagina kiezen](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-3.png)

1. Selecteer op de pagina **schijf indeling kiezen** de optie **VHD** -indeling en selecteer vervolgens **volgende>**.

   ![Pagina schijf indeling kiezen](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-4.png)


1. Kies op de pagina **schijf type kiezen** de optie **vaste grootte** en selecteer **volgende>**.

   ![Pagina schijf type kiezen](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-5.png)


1. Blader op de pagina **schijf configureren** naar de locatie en geef een naam op voor de VHD-schijf met vaste grootte. Selecteer **volgende>**.

   ![Schijf pagina configureren](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-6.png)


1. Controleer de samen vatting en selecteer **volt ooien**. De VHD-of VHDX-conversie neemt enkele minuten in beslag. De tijd voor de conversie is afhankelijk van de grootte van de bron schijf. 

<!--
1. Run PowerShell on your Windows client.
1. Run the following command:

    ```powershell
    Convert-VHD -Path <source VHD path> -DestinationPath <destination-path.vhd> -VHDType Fixed 
    ```
-->
U gebruikt deze vaste VHD voor alle volgende stappen in dit artikel.


## <a name="create-a-hyper-v-vm-from-fixed-vhd"></a>Een Hyper-V-VM maken op basis van een vaste VHD

1. Klik in **Hyper-V-beheer** in het deel venster bereik met de rechter muisknop op uw systeem knooppunt om het context menu te openen en selecteer vervolgens **nieuwe**  >  **virtuele machine**.

    ![Een nieuwe virtuele machine selecteren in het deel venster bereik](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-1.png)

1. Selecteer op de pagina **voordat u begint** van de wizard Nieuwe virtuele machine de optie **volgende**.

1. Geef op de pagina **naam en locatie opgeven** een **naam** en **locatie** op voor de virtuele machine. Selecteer **Next**.

    ![Geef een naam en locatie op voor uw virtuele machine](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-2.png)

1. Kies op de pagina **generatie opgeven** de optie **generatie 1** voor het installatie kopie type VHD-apparaat en selecteer vervolgens **volgende**.    

    ![Generatie opgeven](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-3.png)

1. Wijs de gewenste geheugen-en netwerk configuraties toe.

1. Kies op de pagina **virtuele harde schijf aansluiten** de optie **een bestaande virtuele harde schijf gebruiken**, geef de locatie op van de vaste VHD van Windows die we eerder hebben gemaakt en selecteer **volgende**.

    ![Pagina voor verbinden van virtuele harde schijf](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-4.png)

1. Controleer de **samen vatting** en selecteer vervolgens **volt ooien** om de virtuele machine te maken.

Het maken van de virtuele machine duurt enkele minuten.
     

## <a name="connect-to-the-hyper-v-vm"></a>Verbinding maken met de Hyper-V-VM

De VM wordt weer gegeven in de lijst met virtuele machines op het client systeem. 


1. Selecteer de virtuele machine, klik met de rechter muisknop en selecteer **starten**. 

    ![VM selecteren en starten](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/connect-virtual-machine-2.png)

2. De VM wordt weer gegeven als **actief**. Selecteer de virtuele machine, klik met de rechter muisknop en selecteer **verbinding maken**.

    ![Verbinding maken met de virtuele machine](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/connect-virtual-machine-4.png)

Nadat u verbinding hebt gemaakt met de virtuele machine, voltooit u de wizard voor de installatie van de computer en meldt u zich aan bij de VM.


## <a name="generalize-the-vhd"></a>De VHD generaliseren  

Gebruik het hulp programma *Sysprep* om de VHD te generaliseren. 

1. Open een opdracht prompt in de virtuele machine.
1. Voer de volgende opdracht uit om de VHD te generaliseren. 

    ```
    c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm
    ```
    Zie  [Sysprep (systeem voorbereiding) Overview (Engelstalig)](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)voor meer informatie.
1.  Nadat de opdracht is voltooid, wordt de VM afgesloten. **Start de VM niet opnieuw**.

## <a name="upload-the-vhd-to-azure-blob-storage"></a>De VHD uploaden naar Azure Blob Storage

Uw VHD kan nu worden gebruikt voor het maken van een gegeneraliseerde installatie kopie op Azure Stack Edge. 

1. Upload de VHD naar Azure Blob-opslag. Zie de gedetailleerde instructies in [een VHD uploaden met behulp van Azure Storage Explorer](../devtest-labs/devtest-lab-upload-vhd-using-storage-explorer.md).
1. Nadat het uploaden is voltooid, kunt u de geüploade installatie kopie gebruiken om VM-installatie kopieën en Vm's te maken. 

<!-- this should be added to deploy VM articles - If you experience any issues creating VMs from your new image, you can use VM console access to help troubleshoot. For information on console access, see [link].-->



## <a name="next-steps"></a>Volgende stappen

Afhankelijk van de aard van de implementatie, kunt u een van de volgende procedures kiezen.

- [Een virtuele machine implementeren vanuit een gegeneraliseerde installatie kopie via Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
- [Een virtuele machine implementeren vanuit een gegeneraliseerde installatie kopie via Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)  
