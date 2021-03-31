---
title: VHD-bestand uploaden naar Azure DevTest Labs met behulp van Storage Explorer
description: VHD-bestand uploaden naar het opslag account van de Lab met behulp van Microsoft Azure Storage Explorer
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d4e421932ebba5d4f389000c12bcf44ac2f37599
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91282524"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-microsoft-azure-storage-explorer"></a>VHD-bestand uploaden naar het opslag account van de Lab met behulp van Microsoft Azure Storage Explorer

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

In Azure DevTest Labs kunnen VHD-bestanden worden gebruikt om aangepaste installatie kopieën te maken die worden gebruikt om virtuele machines in te richten. In dit artikel wordt beschreven hoe u [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) kunt gebruiken om een VHD-bestand te uploaden naar een opslag account van een lab. Zodra u uw VHD-bestand hebt geüpload, worden in de [sectie volgende stappen](#next-steps) een aantal artikelen weer gegeven waarin wordt uitgelegd hoe u een aangepaste installatie kopie maakt op basis van het GEÜPLOADe VHD-bestand. Zie [Introduction to Managed disks](../virtual-machines/managed-disks-overview.md) (Engelstalig) voor meer informatie over schijven en Vhd's in azure

## <a name="step-by-step-instructions"></a>Stapsgewijze instructies

De volgende stappen begeleiden u bij het uploaden van een VHD-bestand naar DevTest Labs met behulp van [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).

1. [Down load en installeer de nieuwste versie van de Microsoft Azure Storage Explorer](https://www.storageexplorer.com).

1. Haal de naam van het opslag account van de Lab op met behulp van de Azure Portal:

    1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
    
    1. Selecteer **alle services** en selecteer vervolgens **DevTest Labs** in de lijst.
    
    1. Selecteer in de lijst met Labs het gewenste Lab.  
    
    1. Selecteer **configuratie** op de Blade van het lab. 
    
    1. Selecteer **aangepaste installatie kopieën (vhd's)** op de Blade Lab- **configuratie** .
    
    1. Selecteer op de Blade **aangepaste installatie kopieën** **+ toevoegen**. 
    
    1. Selecteer **VHD** op de Blade **aangepaste installatie kopie** .
    
    1. Selecteer op  de Blade VHD **een VHD uploaden met behulp van Power shell**.
    
        ![VHD uploaden met Power shell][0]
    
    1. In de Blade **een afbeelding uploaden met Power shell** wordt een aanroep van de cmdlet **add-AzureVhd** weer gegeven. De eerste para meter (*doel*) bevat de naam van het opslag account voor het lab in de volgende indeling:
    
        `https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...`

    1. Noteer de naam van het opslag account zoals deze wordt gebruikt in latere stappen.
    
1. Maak verbinding met een account van een Azure-abonnement met behulp van Storage Explorer.

    > [!TIP] 
    > 
    > Storage Explorer ondersteunt verschillende verbindings opties. In deze sectie ziet u hoe u verbinding maakt met een opslag account dat is gekoppeld aan uw Azure-abonnement. Raadpleeg het artikel aan de [slag met Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md)voor een overzicht van de andere verbindings opties die door Storage Explorer worden ondersteund.
 
    1. Open Storage Explorer.
    
    1. Selecteer in Storage Explorer **Azure-account instellingen**. 
    
        ![Azure-accountinstellingen][1]
    
    1. In het linkerdeel venster worden de micro soft-accounts weer gegeven waarvoor u bent aangemeld. Selecteer **Een account toevoegen** om verbinding te maken met een ander account en volg de dialoogvensters om aan te melden met een Microsoft-account dat is gekoppeld aan ten minste één actief Azure-abonnement.
    
        ![Een account toevoegen][2]
    
    1. Wanneer u bent aangemeld met een Microsoft-account, worden in het linkerdeelvenster de Azure-abonnementen weergegeven die aan dat account zijn gekoppeld. Selecteer de Azure-abonnementen waarmee u wilt werken en selecteer vervolgens **Toepassen**. (Door **alle abonnementen** te selecteren, schakelt u de selectie van alle of geen van de vermelde Azure-abonnementen.)
    
        ![Selecteer Azure-abonnementen][3]
    
    1. In het linkerdeelvenster worden de opslagaccounts weergegeven die aan de geselecteerde Azure-abonnementen zijn gekoppeld.
    
        ![Geselecteerde Azure-abonnementen][4]

1. Zoek het opslag account van de test omgeving:

    1. Zoek in het linkerdeel venster Storage Explorer het knoop punt voor het Azure-abonnement dat eigenaar is van het lab en vouw dit uit.
    
    1. Vouw **opslag accounts** uit onder het knoop punt van het abonnement.

    1. Vouw het knoop punt opslag account van het lab uit om knoop punten weer te geven voor **BLOB-containers**, **Bestands shares**, **wacht rijen** en **tabellen**.
    
    1. Vouw het knooppunt **Blobcontainers** uit.
    
    1. Selecteer de BLOB-container uploads om de inhoud ervan weer te geven in het rechterdeel venster.
        
        ![Map uploaden][5]

1. Upload het VHD-bestand met behulp van Storage Explorer:

    1. In het Storage Explorer rechterdeel venster ziet u een lijst met de blobs in de container **uploads** blob van het opslag account van de test omgeving. Selecteer **uploaden** op de werk balk van de BLOB-editor 
        
        ![Scherm opname van het Storage Explorer rechterdeel venster met de knop uploaden geselecteerd.][6]
    
    1. Selecteer in de vervolg keuzelijst **uploaden** de optie **bestanden uploaden...**.
    
    1. Selecteer het beletsel teken in het dialoog venster **bestanden uploaden** .
        
        ![Scherm opname van het dialoog venster ' bestanden uploaden ' met het beletsel teken dat is geselecteerd.][8]  

    1. Blader in het dialoog venster **Selecteer de bestanden die u wilt uploaden** naar het gewenste VHD-bestand, selecteer het en selecteer vervolgens **openen**.
    
    1. Wanneer u terugkeert naar het dialoog venster **bestanden uploaden** , wijzigt u het **type BLOB** in **pagina-BLOB**.
    
    1. Selecteer **Uploaden**.

        ![Bestand selecteren][9]  
    
    1. In het deel venster Storage Explorer **activiteiten logboek** wordt de Download status weer gegeven (samen met koppelingen om de upload te annuleren). Het uploaden van een VHD-bestand kan lang duren, afhankelijk van de grootte van het VHD-bestand en de verbindings snelheid. 

        ![Upload-bestands status][10]  

## <a name="next-steps"></a>Volgende stappen

- [Een aangepaste installatie kopie maken in Azure DevTest Labs van een VHD-bestand met behulp van de Azure Portal](devtest-lab-create-template.md)
- [Een aangepaste installatie kopie maken in Azure DevTest Labs van een VHD-bestand met behulp van Power shell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)

[0]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-image-using-psh.png
[1]: ./media/devtest-lab-upload-vhd-using-storage-explorer/settings-icon.png
[2]: ./media/devtest-lab-upload-vhd-using-storage-explorer/add-account-link.png
[3]: ./media/devtest-lab-upload-vhd-using-storage-explorer/subscriptions-list.png
[4]: ./media/devtest-lab-upload-vhd-using-storage-explorer/storage-accounts-list.png
[5]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-dir.png
[6]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-button.png
[7]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-files.png
[8]: ./media/devtest-lab-upload-vhd-using-storage-explorer/select-file.png
[9]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-file.png
[10]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-status.png
