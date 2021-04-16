---
title: Een Linux-VHD downloaden van Azure
description: Download een Linux-VHD met behulp van de Azure CLI en de Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: how-to
ms.date: 08/03/2020
ms.author: cynthn
ms.openlocfilehash: 8def06990b72d6e08127e8c4f16e0dfd87905d4f
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565182"
---
# <a name="download-a-linux-vhd-from-azure"></a>Een Linux-VHD downloaden van Azure

In dit artikel leert u hoe u een VHD-bestand (virtuele harde schijf) voor Linux kunt downloaden van Azure met behulp van de Azure Portal. 

## <a name="stop-the-vm"></a>De virtuele machine stoppen

Een VHD kan niet worden gedownload van Azure als deze is gekoppeld aan een virtuele VM die wordt uitgevoerd. Als u de VM actief wilt houden, kunt u een [momentopname maken en vervolgens de momentopname downloaden.](#alternative-snapshot-the-vm-disk)

De VM stoppen:

1.  Meld u aan bij [Azure Portal](https://portal.azure.com/).
2.  Selecteer in het menu links **Virtual Machines**.
3.  Selecteer de VM in de lijst.
4.  Selecteer stoppen op de pagina voor **de** VM.

    :::image type="content" source="./media/download-vhd/export-stop.PNG" alt-text="Toont de menuknop om de VM te stoppen.":::

### <a name="alternative-snapshot-the-vm-disk"></a>Alternatief: Momentopname maken van de VM-schijf

Maak een momentopname van de schijf die u wilt downloaden.

1. Selecteer de VM in de [portal](https://portal.azure.com).
2. Selecteer **Schijven in het** menu links en selecteer vervolgens de schijf waarop u een momentopname wilt maken. De details van de schijf worden weergegeven.  
3. Selecteer **Momentopname** maken in het menu bovenaan de pagina. De **pagina Momentopname** maken wordt geopend.
4. In **Naam** typt u een naam voor de momentopname. 
5. Bij **Type momentopname** selecteert **u Volledig** of **Incrementeel.**
6. Als u klaar bent, selecteert u **Beoordelen en maken**.

Uw momentopname wordt binnenkort gemaakt en kan vervolgens worden gebruikt om een andere VM te downloaden of te maken.

> [!NOTE]
> Als u de VM niet eerst stopt, is de momentopname niet schoon. De momentopname heeft dezelfde status als wanneer de VM is gecrasht of gecrasht op het moment dat de momentopname is gemaakt.  Hoewel het meestal veilig is, kan dit problemen veroorzaken als de toepassingen die worden uitgevoerd een tijdstip niet crash-bestand zijn.
>  
> Deze methode wordt alleen aanbevolen voor VM's met één besturingssysteemschijf. VM's met een of meer gegevensschijven moeten worden gestopt voordat ze worden gedownload of voordat u een momentopname maakt voor de besturingssysteemschijf en elke gegevensschijf.

## <a name="generate-sas-url"></a>SAS-URL genereren

Als u het VHD-bestand wilt downloaden, moet u een [SAS-URL (Shared Access Signature)](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) genereren. Wanneer de URL wordt gegenereerd, wordt er een verlooptijd toegewezen aan de URL.

1. Selecteer Schijven in het menu van de pagina voor **de** VM.
2. Selecteer de besturingssysteemschijf voor de VM en selecteer vervolgens **Schijfexport.**
1. Werk indien nodig de waarde van **URL bij in (seconden)** om u voldoende tijd te geven om het downloaden te voltooien. De standaardwaarde is 3600 seconden (één uur).
3. Selecteer **URL genereren.**
 
      
## <a name="download-vhd"></a>VHD downloaden

1.  Selecteer het **VHD-bestand** downloaden onder de URL die is gegenereerd.

    :::image type="content" source="./media/download-vhd/export-download.PNG" alt-text="Toont de knop voor het downloaden van de VHD.":::

2.  Mogelijk moet u Opslaan in **de** browser selecteren om het downloaden te starten. De standaardnaam voor het VHD-bestand is *abcd*.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [uploaden en maken van een linux-VM op een aangepaste schijf met de Azure CLI](upload-vhd.md). 
- [Azure-schijven beheren met de Azure CLI.](tutorial-manage-disks.md)
