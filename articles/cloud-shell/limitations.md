---
title: Azure Cloud Shell beperkingen | Microsoft Docs
description: Overzicht van beperkingen van Azure Cloud Shell
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: damaerte
ms.openlocfilehash: 092dccab82326bb9983f11ff64fe50aee7b1084d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "74951476"
---
# <a name="limitations-of-azure-cloud-shell"></a>Beperkingen van Azure Cloud Shell

Azure Cloud Shell heeft de volgende bekende beperkingen:

## <a name="general-limitations"></a>Algemene beperkingen

### <a name="system-state-and-persistence"></a>Systeem status en persistentie

De computer die uw Cloud Shell-sessie levert, is tijdelijk en wordt gerecycled nadat uw sessie 20 minuten niet actief is. Cloud Shell moet een Azure-bestands share worden gekoppeld. Als gevolg hiervan moet uw abonnement opslag bronnen kunnen instellen voor toegang tot Cloud Shell. Andere overwegingen zijn onder andere:

* Bij gekoppelde opslag worden alleen wijzigingen in de `$Home` Directory bewaard.
* Azure-bestands shares kunnen alleen worden gekoppeld vanuit uw [toegewezen regio](persisting-shell-storage.md#mount-a-new-clouddrive).
  * Voer in bash `env` de opdracht uit om uw regio in te stellen als `ACC_LOCATION` .

### <a name="browser-support"></a>Browserondersteuning

Cloud Shell ondersteunt de nieuwste versies van micro soft Edge, micro soft Internet Explorer, Google Chrome, Mozilla Firefox en Apple Safari. Safari in de privé modus wordt niet ondersteund.

### <a name="copy-and-paste"></a>Kopiëren en plakken

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>Voor een bepaalde gebruiker kan slechts één shell actief zijn

Gebruikers kunnen slechts één type shell tegelijk starten, hetzij **bash** of **Power shell**. Het is echter mogelijk dat er meerdere exemplaren van bash of Power shell tegelijk worden uitgevoerd. Als u het menu verwisselt tussen bash of Power shell, worden Cloud Shell opnieuw opgestart, waardoor bestaande sessies worden beëindigd. U kunt ook bash uitvoeren in Power shell door te typen `bash` en Power shell in bash uit te voeren door te typen `pwsh` .

### <a name="usage-limits"></a>Gebruiksbeperkingen

Cloud Shell is bedoeld voor interactieve use-cases. Als gevolg hiervan worden langlopende niet-interactieve sessies beëindigd zonder dat dit wordt gewaarschuwd.

## <a name="bash-limitations"></a>Bash-beperkingen

### <a name="user-permissions"></a>Gebruikersmachtigingen

Machtigingen worden ingesteld als gewone gebruikers zonder sudo-toegang. Een installatie buiten uw `$Home` map wordt niet bewaard.

### <a name="editing-bashrc-or-profile"></a>Bewerken. bashrc of $PROFILE

Let op wanneer u het bestand bewerkt. bashrc of Power shell $PROFILE. Dit kan leiden tot onverwachte fouten in Cloud Shell.

## <a name="powershell-limitations"></a>Power shell-beperkingen

### <a name="azuread-module-name"></a>`AzureAD` module naam

De `AzureAD` module naam is momenteel `AzureAD.Standard.Preview` , de module biedt dezelfde functionaliteit.

### <a name="sqlserver-module-functionality"></a>`SqlServer` module functionaliteit

De `SqlServer` module die is opgenomen in Cloud Shell heeft alleen ondersteuning voor de Prerelease van Power shell core. Met name `Invoke-SqlCmd` is nog niet beschikbaar.

### <a name="default-file-location-when-created-from-azure-drive"></a>Standaard bestands locatie bij het maken van een Azure-station:

Met Power shell-cmdlets kunnen gebruikers geen bestanden maken in het Azure:-station. Wanneer gebruikers nieuwe bestanden maken met andere hulpprogram ma's, zoals vim of nano, worden de bestanden standaard opgeslagen op de `$HOME` . 

### <a name="gui-applications-are-not-supported"></a>GUI-toepassingen worden niet ondersteund

Als de gebruiker een opdracht uitvoert waarmee een Windows-dialoog venster wordt gemaakt, ziet u een fout bericht zoals: `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)` .

### <a name="large-gap-after-displaying-progress-bar"></a>Grote tussen ruimte na het weer geven van de voortgangs balk

Als de gebruiker een actie uitvoert waarmee een voortgangs balk wordt weer gegeven, zoals een tabblad die in het `Azure:` station wordt ingevuld, is het mogelijk dat de cursor niet op de juiste wijze is ingesteld en dat er een lege ruimte wordt weer gegeven waarin de voortgangs balk zich eerder bevond.

## <a name="next-steps"></a>Volgende stappen

[Problemen met Cloud Shell oplossen](troubleshooting.md) <br>
[Snelstartgids voor Bash](quickstart.md) <br>
[Snelstartgids voor PowerShell](quickstart-powershell.md)
