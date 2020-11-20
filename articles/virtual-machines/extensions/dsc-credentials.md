---
title: Referenties door geven aan Azure met behulp van desired state Configuration
description: Informatie over het veilig door geven van referenties aan virtuele Azure-machines met behulp van de desired state Configuration (DSC) van Power shell.
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: dsc
ms.assetid: ea76b7e8-b576-445a-8107-88ea2f3876b9
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: robreed
ms.openlocfilehash: f191ab5819cd69ce90699db60be1665ef77e10a9
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94955885"
---
# <a name="pass-credentials-to-the-azure-dscextension-handler"></a>Referenties door geven aan de Azure DSCExtension-handler

Dit artikel heeft betrekking op de desired state Configuration (DSC)-extensie voor Azure. Voor een overzicht van de DSC-extensie-handler raadpleegt [u Introduction to the Azure desired state Configuration extension handler](dsc-overview.md).

 

## <a name="pass-in-credentials"></a>Referenties door geven

Als onderdeel van het configuratie proces moet u mogelijk gebruikers accounts instellen, toegang krijgen tot services of een programma installeren in een gebruikers context. Als u deze dingen wilt doen, moet u referenties opgeven.

U kunt DSC gebruiken om configuraties met para meters in te stellen. In een configuratie met para meters worden referenties door gegeven aan de configuratie en veilig opgeslagen in MOF-bestanden. De Azure extension handler vereenvoudigt het beheer van referenties door het automatisch beheer van certificaten te bieden.

Met het volgende DSC-configuratie script maakt u een lokaal gebruikers account met het opgegeven wacht woord:

```powershell
configuration Main
{
    param(
        [Parameter(Mandatory=$true)]
        [ValidateNotNullorEmpty()]
        [PSCredential]
        $Credential
    )
    Node localhost {
        User LocalUserAccount
        {
            Username = $Credential.UserName
            Password = $Credential
            Disabled = $false
            Ensure = "Present"
            FullName = "Local User Account"
            Description = "Local User Account"
            PasswordNeverExpires = $true
        }
    }
}
```

Het is belang rijk dat u het **knoop punt localhost** als onderdeel van de configuratie opneemt. De extensie-handler zoekt specifiek naar de **node localhost** -instructie. Als deze instructie ontbreekt, werken de volgende stappen niet. Het is ook belang rijk dat u de typecast **[PsCredential]** opneemt. Met dit specifieke type wordt de extensie geactiveerd om de referentie te versleutelen.

Dit script publiceren naar Azure Blob-opslag:

`Publish-AzVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

De Azure DSC-extensie instellen en de referentie opgeven:

```powershell
$configurationName = 'Main'
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = 'user_configuration.ps1.zip'
$vm = Get-AzVM -Name 'example-1'

$vm = Set-AzVMDscExtension -VMName $vm -ConfigurationArchive $configurationArchive -ConfigurationName $configurationName -ConfigurationArgument @configurationArguments

$vm | Update-AzVM
```

## <a name="how-a-credential-is-secured"></a>Hoe een referentie is beveiligd

Het uitvoeren van deze code vraagt om een referentie. Nadat de referentie is ingesteld, wordt deze kort opgeslagen in het geheugen. Wanneer de referentie wordt gepubliceerd met behulp van de cmdlet **set-AzVMDscExtension** , wordt de referentie verzonden via https naar de virtuele machine. In de virtuele machine slaat Azure de referentie die is versleuteld op schijf op met behulp van het lokale VM-certificaat. De referentie wordt kort in het geheugen gedecodeerd en vervolgens opnieuw versleuteld om deze door te geven aan DSC.

Dit proces wijkt af [van het gebruik van beveiligde configuraties zonder de extensie-handler](/powershell/scripting/dsc/pull-server/securemof). De Azure-omgeving biedt een manier om configuratie gegevens veilig via certificaten te verzenden. Wanneer u de DSC-extensie-handler gebruikt, hoeft u geen **$CertificatePath** -of **$CertificateID** /  **$thumbprint** -vermelding op te geven in **ConfigurationData**.

## <a name="next-steps"></a>Volgende stappen

- Krijg een [Inleiding tot de Azure DSC-extensie-handler](dsc-overview.md).
- Bekijk de [Azure Resource Manager-sjabloon voor de DSC-extensie](dsc-template.md).
- Voor meer informatie over Power shell DSC gaat u naar het [Power shell-documentatie centrum](/powershell/scripting/dsc/overview/overview).
- Voor meer functionaliteit die u kunt beheren met Power shell DSC, en voor meer DSC-resources, gaat u naar de [Power shell Gallery](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).