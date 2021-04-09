---
title: VHD-bestand uploaden naar Azure DevTest Labs met behulp van AzCopy | Microsoft Docs
description: In dit artikel vindt u een overzicht van het gebruik van het AzCopy-opdracht regel programma voor het uploaden van een VHD-bestand naar het opslag account van een lab in Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1d8ede0f78726b04ac862a00b559b8d42c3ed1cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88650771"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-azcopy"></a>VHD-bestand uploaden naar het opslag account van de Lab met behulp van AzCopy

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

In Azure DevTest Labs kunnen VHD-bestanden worden gebruikt om aangepaste installatie kopieën te maken die worden gebruikt om virtuele machines in te richten. De volgende stappen begeleiden u bij het gebruik van het AzCopy-opdracht regel programma voor het uploaden van een VHD-bestand naar het opslag account van een lab. Zodra u uw VHD-bestand hebt geüpload, worden in de [sectie volgende stappen](#next-steps) een aantal artikelen weer gegeven waarin wordt uitgelegd hoe u een aangepaste installatie kopie maakt op basis van het GEÜPLOADe VHD-bestand. Zie [Introduction to Managed disks](../virtual-machines/managed-disks-overview.md) (Engelstalig) voor meer informatie over schijven en Vhd's in azure

> [!NOTE] 
>  
> AzCopy is een alleen-Windows-opdracht regel programma.

## <a name="step-by-step-instructions"></a>Stapsgewijze instructies

De volgende stappen begeleiden u bij het uploaden van een VHD-bestand naar Azure DevTest Labs met behulp van [AzCopy](https://aka.ms/downloadazcopy). 

1. Haal de naam van het opslag account van de Lab op met behulp van de Azure Portal:

1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecteer **alle services** en selecteer vervolgens **DevTest Labs** in de lijst.

1. Selecteer in de lijst met Labs het gewenste Lab.  

1. Selecteer **configuratie** op de Blade van het lab. 

1. Selecteer **aangepaste installatie kopieën (vhd's)** op de Blade Lab- **configuratie** .

1. Selecteer op de Blade **aangepaste installatie kopieën** **+ toevoegen**. 

1. Selecteer **VHD** op de Blade **aangepaste installatie kopie** .

1. Selecteer op  de Blade VHD **een VHD uploaden met behulp van Power shell**.

    ![VHD uploaden met Power shell](./media/devtest-lab-upload-vhd-using-azcopy/upload-image-using-psh.png)

1. In de Blade **een afbeelding uploaden met Power shell** wordt een aanroep van de cmdlet **add-AzureVhd** weer gegeven. De eerste para meter (*bestemming*) bevat de URI voor een BLOB-container (*uploads*) in de volgende indeling:

    ```
    https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...
    ``` 

1. Noteer de volledige URI zoals deze wordt gebruikt in latere stappen.

1. Upload het VHD-bestand met behulp van AzCopy:
 
1. [Down load en installeer de meest recente versie van AzCopy](https://aka.ms/downloadazcopy).

1. Open een opdracht venster en navigeer naar de AzCopy-installatiemap. Desgewenst kunt u de installatie locatie van de AzCopy toevoegen aan het systeempad. AzCopy wordt standaard geïnstalleerd in de volgende map:

    ```command-line
    %ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy
    ```

1. Voer de volgende opdracht uit vanaf de opdracht regel met behulp van de sleutel voor het opslag account en de URI van de BLOB-container. De *vhdFileName* -waarde moet tussen aanhalings tekens staan. Het uploaden van een VHD-bestand kan lang duren, afhankelijk van de grootte van het VHD-bestand en de verbindings snelheid.   

    ```command-line
    AzCopy /Source:<sourceDirectory> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Pattern:"<vhdFileName>" /BlobType:page
    ```

## <a name="next-steps"></a>Volgende stappen

- [Een aangepaste installatie kopie maken in Azure DevTest Labs van een VHD-bestand met behulp van de Azure Portal](devtest-lab-create-template.md)
- [Een aangepaste installatie kopie maken in Azure DevTest Labs van een VHD-bestand met behulp van Power shell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)