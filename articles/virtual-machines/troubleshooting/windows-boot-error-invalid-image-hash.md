---
title: 'Windows Boot Manager-fout: 0xC0000428-status ongeldige afbeeldings-hash'
titlesuffix: Azure Virtual Machines
description: Dit artikel bevat stappen voor het oplossen van problemen waarbij een preview-installatie kopie is gebruikt en de proef periode is verlopen, waardoor het opstarten van een virtuele machine van Azure (VM) wordt voor komen.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 26c411f9-6c22-4f45-a445-c5781e547828
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/03/2020
ms.author: v-miegge
ms.openlocfilehash: 22db9c7966b6f988ca0ea799104275f3f86c77ea
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629569"
---
# <a name="windows-boot-manager-error---0xc0000428-status-invalid-image-hash"></a>Windows Boot Manager-fout: 0xC0000428-status ongeldige afbeeldings-hash

Dit artikel bevat stappen voor het oplossen van problemen waarbij een preview-installatie kopie is gebruikt en de proef periode is verlopen, waardoor het opstarten van een virtuele machine van Azure (VM) wordt voor komen.

## <a name="symptom"></a>Symptoom

Wanneer u [Diagnostische gegevens over opstarten](./boot-diagnostics.md) gebruikt om de scherm opname van de virtuele machine weer te geven, ziet u dat in de scherm opname de Windows-opstart beheer wordt weer gegeven met het volgende bericht:

  `File: \windows\system32\boot\winload.exe`

  `Status: 0xc0000428`

  `Info: Windows cannot verify the digital signature for this file.`

  ![In afbeelding 1 wordt Windows Boot Manager weer gegeven met de status ' Ox0000428 ' en de informatie ' Windows kan de digitale hand tekening voor dit bestand niet verifiëren '.](./media/windows-boot-error-invalid-image-hash/1-cannot-verify-signature.png)

of het bericht:

  `File: \Windows\system32\winload.exe`

  `Status: 0xc0000428`

  `Info: The digital signature for this file couldn't be verified.`
    
  ![In afbeelding 2 wordt Windows Boot Manager weer gegeven met de status ' Ox0000428 ' en ' de informatie ' kan de digitale hand tekening voor dit bestand niet verifiëren '.](./media/windows-boot-error-invalid-image-hash/2-digital-signature-not-verified.png)

## <a name="cause"></a>Oorzaak

De installatie kopie die is gebruikt voor het bouwen van de virtuele machine was een voorbeeld installatie kopie met een verval datum in plaats van een RTM-installatie kopie (Release to Manufacturing). 

Preview-afbeeldingen hebben een bepaalde levens cyclus en de scherm opname die u ziet wordt weer gegeven wanneer u de verval datum doorgeeft, wat inhoudt dat de proef versie van de afbeelding is voltooid.

### <a name="example-of-preview-images"></a>Voor beeld van voorbeeld afbeeldingen

`a699494373c04fc0bc8f2bb1389d6106__Windows-Server-Technical-Preview-201505.01-en.us-127GB.vhd`

U kunt de vervaldatum van een preview-installatiekopie niet verlengen. Zodra de preview is verlopen, kan de virtuele machine niet meer worden opgestart.

- Afhankelijk van het product kan de proef periode variëren. Windows preview-installatie kopieën hebben bijvoorbeeld een proef periode van 180 dagen.

- In azure bevatten alle installatie kopieën voor Windows die Preview-versies zijn, een opmerking in hun beschrijving die niet bedoeld is voor productie en die alleen beschikbaar zijn voor gebruik voor een opgegeven proef periode of als een preview-versie.

## <a name="solution"></a>Oplossing

> [!TIP]
> Als u een recente back-up van de virtuele machine hebt, kunt u proberen [de virtuele machine terug te zetten vanaf de back-up](../../backup/backup-azure-arm-restore-vms.md) om het opstart probleem op te lossen.

Als uw installatie kopie een voorbeeld afbeelding is, is er geen manier om de verval datum voor de gebruikte installatie kopie uit te breiden, moet u [een nieuwe VM implementeren](../windows/quick-create-portal.md) met een niet-preview-installatie kopie. De volgende stappen helpen u bij het identificeren van een voorbeeld installatie kopie en het leveren van resources die u kunnen helpen bij het overbrengen van gegevens van deze VM naar een nieuwe VM. Als u de afbeelding hebt geïdentificeerd als een preview-afbeelding, kan de afbeelding niet worden hersteld, omdat deze nu is verlopen.

Afhankelijk van uw voor keur kunt u Azure PowerShell of Azure CLI gebruiken om uw installatie kopie op te vragen om te bepalen of het een preview-installatie kopie is. U kunt deze opdrachten gebruiken om te bevestigen dat de afbeelding een voorbeeld afbeelding is.

### <a name="query-using-azure-powershell"></a>Query's uitvoeren met Azure PowerShell

1. Open de Windows Power shell-toepassing.
1. Voer de volgende opdrachten uit:

   ```powershell
   $locName = "<LOCATION>" 
   $pubName = "<PUBLISHER NAME>" 
   $offerName = "<OFFER NAME>" 
   $skuName= "<YEAR WHEN THIS IMAGE WAS RELEASED>"
   Get-AzVMImagePublisher -Location $locName | Select $pubName
   Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
   Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
   Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName | Select Version
   ```

- In de vorige opdrachten vervangt `<LOCATION>` ,, `<PUBLISHER NAME>` `<OFFER NAME>` en `<YEAR WHEN THIS IMAGE WAS RELEASED>` met de opgegeven informatie. Verwijder ook de symbolen < en >.

  Zie het volgende voorbeeld:

  ```powershell
  $locName = "West US" 
  $pubName = "MicrosoftWindowsServer" 
  $offerName = "WindowsServer" 
  $skuName= "2016-Datacenter"
  Get-AzVMImagePublisher -Location $locName | Select $pubName
  Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
  Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
  Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName | Select Version
  ```

### <a name="query-using-the-azure-cli"></a>Query's uitvoeren met de Azure CLI

1. Als u dat nog niet hebt gedaan, moet u [de Azure cli installeren](/cli/azure/install-azure-cli).
1. Nadat het is gedownload, gebruikt u een opdracht prompt of Power shell om de `az login` opdracht in te voeren en meldt u zich aan met uw account referenties.
1. Wanneer u bent aangemeld, voert u de volgende opdrachten in:

   ```powershell
   az vm image list-publishers --location "<LOCATION>"
   az vm image list-offers --location "West US" --publisher "<PUBLISHER NAME>"
   az vm image list-skus --location "West US" --publisher "<PUBLISHER NAME>" --offer "<OFFER NAME>"
   az vm image list  --location "West US" --publisher "<PUBLISHER NAME>" --offer "<OFFER NAME>" --sku "<YEAR WHEN THIS IMAGE WAS RELEASED>"
   ```

- In de vorige opdrachten vervangt `<LOCATION>` ,, `<PUBLISHER NAME>` `<OFFER NAME>` en `<YEAR WHEN THIS IMAGE WAS RELEASED>` met de opgegeven informatie. Verwijder ook de symbolen < en >.

  Zie het volgende voorbeeld:

  ```powershell
  az vm image list-publishers --location "West US"
  az vm image list-offers --location "West US" --publisher "MicrosoftWindowsServer"
  az vm image list-skus --location "West US" --publisher "MicrosoftWindowsServer" --offer "WindowsServer"
  az vm image list  --location "West US" --publisher "MicrosoftWindowsServer" --offer "WindowsServer" --sku "2016-Datacenter"
  ```