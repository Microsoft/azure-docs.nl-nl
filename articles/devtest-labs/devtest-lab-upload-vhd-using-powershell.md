---
title: VHD-bestand uploaden naar Azure DevTest Labs met behulp van Power shell | Microsoft Docs
description: Dit artikel bevat een overzicht waarin wordt uitgelegd hoe u een VHD-bestand uploadt naar Azure DevTest Labs met behulp van Power shell.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2b393b886a50f60a918690ee2a5583f9623dbe39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88650754"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-powershell"></a>VHD-bestand uploaden naar het opslag account van de Lab met behulp van Power shell

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

In Azure DevTest Labs kunnen VHD-bestanden worden gebruikt om aangepaste installatie kopieën te maken die worden gebruikt om virtuele machines in te richten. De volgende stappen helpen u bij het gebruik van Power shell om een VHD-bestand te uploaden naar een opslag account van een lab. Zodra u uw VHD-bestand hebt geüpload, worden in de [sectie volgende stappen](#next-steps) een aantal artikelen weer gegeven waarin wordt uitgelegd hoe u een aangepaste installatie kopie maakt op basis van het GEÜPLOADe VHD-bestand. Zie [Introduction to Managed disks](../virtual-machines/managed-disks-overview.md) (Engelstalig) voor meer informatie over schijven en Vhd's in azure

## <a name="step-by-step-instructions"></a>Stapsgewijze instructies

De volgende stappen begeleiden u bij het uploaden van een VHD-bestand naar Azure DevTest Labs met behulp van Power shell. 

1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecteer **alle services** en selecteer vervolgens **DevTest Labs** in de lijst.

1. Selecteer in de lijst met Labs het gewenste Lab.  

1. Selecteer **configuratie** op de Blade van het lab. 

1. Selecteer **aangepaste installatie kopieën (vhd's)** op de Blade Lab- **configuratie** .

1. Selecteer op de Blade **aangepaste installatie kopieën** **+ toevoegen**. 

1. Selecteer **VHD** op de Blade **aangepaste installatie kopie** .

1. Selecteer op  de Blade VHD **een VHD uploaden met behulp van Power shell**.

    ![VHD uploaden met Power shell](./media/devtest-lab-upload-vhd-using-powershell/upload-image-using-psh.png)

1. Kopieer op de Blade **een afbeelding uploaden met Power shell** het gegenereerde Power shell-script naar een tekst editor.

1. Wijzig de para meter **LocalFilePath** van de cmdlet **add-AzureVhd** zodat deze verwijst naar de locatie van het VHD-bestand dat u wilt uploaden.

1. Voer bij een Power shell-prompt de cmdlet **add-AzureVhd** (met de para meter modified **LocalFilePath** ) uit.

> [!WARNING] 
> 
> Het uploaden van een VHD-bestand kan lang duren, afhankelijk van de grootte van het VHD-bestand en de verbindings snelheid.

## <a name="next-steps"></a>Volgende stappen

- [Een aangepaste installatie kopie maken in Azure DevTest Labs van een VHD-bestand met behulp van de Azure Portal](devtest-lab-create-template.md)
- [Een aangepaste installatie kopie maken in Azure DevTest Labs van een VHD-bestand met behulp van Power shell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
