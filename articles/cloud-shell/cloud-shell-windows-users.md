---
title: Azure Cloud Shell voor Windows-gebruikers | Microsoft Docs
description: Gids voor gebruikers die niet bekend zijn met Linux-systemen
services: azure
documentationcenter: ''
author: maertendMSFT
manager: hemantm
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/03/2018
ms.author: damaerte
ms.openlocfilehash: 8cc1934cc97ecf821de8644dda45d867b3ca3e85
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102508943"
---
# <a name="powershell-in-azure-cloud-shell-for-windows-users"></a>PowerShell in Azure Cloud Shell voor Windows-gebruikers

In mei 2018 werden wijzigingen [aangekondigd](https://azure.microsoft.com/blog/pscloudshellrefresh/) in Power shell in azure Cloud shell.
De Power shell-ervaring in Azure Cloud Shell voert nu [Power shell Core 6](https://github.com/powershell/powershell) uit in een Linux-omgeving.
Met deze wijziging zijn er mogelijk enkele verschillen in de Power shell-ervaring in Cloud Shell vergeleken met wat er wordt verwacht in een Windows Power shell-ervaring.

## <a name="file-system-case-sensitivity"></a>Hoofdletter gevoeligheid van bestands systeem

Het bestands systeem is hoofdletter gevoelig in Windows, terwijl het bestands systeem op Linux hoofdletter gevoelig is.
Voorheen `file.txt` en `FILE.txt` werden beschouwd als hetzelfde bestand, maar nu worden ze beschouwd als andere bestanden.
De juiste behuizing moet worden gebruikt `tab-completing` in het bestands systeem.
Specifieke Power shell-ervaringen, zoals `tab-completing` namen van cmdlets, para meters en waarden, zijn niet hoofdletter gevoelig.

## <a name="windows-powershell-aliases-vs-linux-utilities"></a>Windows Power shell-aliassen versus Linux-hulpprogram ma's

Sommige bestaande Power shell-aliassen hebben dezelfde namen als ingebouwde Linux-opdrachten, zoals `cat` ,, `ls` `sort` , `sleep` enzovoort. In Power shell Core 6 worden aliassen die conflicteren met ingebouwde Linux-opdrachten, verwijderd.
Hieronder ziet u de algemene aliassen die zijn verwijderd, evenals de gelijkwaardige opdrachten:  

|Verwijderde alias   |Overeenkomende opdracht   |
|---|---|
|`cat`    | `Get-Content` |
|`curl`   | `Invoke-WebRequest` |
|`diff`   | `Compare-Object` |
|`ls`     | `dir` <br> `Get-ChildItem` |
|`mv`     | `Move-Item`   |
|`rm`     | `Remove-Item` |
|`sleep`  | `Start-Sleep` |
|`sort`   | `Sort-Object` |
|`wget`   | `Invoke-WebRequest` |

## <a name="persisting-home"></a>$HOME persistent maken

Eerdere gebruikers konden alleen scripts en andere bestanden in hun Cloud station persistent maken.
De $HOME directory van de gebruiker wordt nu ook opgeslagen in sessies.

## <a name="powershell-profile"></a>Power shell-profiel

Standaard wordt het Power shell-Profiel van een gebruiker niet gemaakt.
Als u uw profiel wilt maken, maakt u een `PowerShell` map onder `$HOME/.config` .

```azurepowershell-interactive
mkdir (Split-Path $profile.CurrentUserAllHosts)
```

Onder `$HOME/.config/PowerShell` kunt u uw profiel bestanden maken- `profile.ps1` en/of `Microsoft.PowerShell_profile.ps1` .

## <a name="whats-new-in-powershell-core-6"></a>Wat is er nieuw in Power shell Core 6

Voor meer informatie over wat er nieuw is in Power shell Core 6, verwijzen wij u naar de [Power shell-documenten](/powershell/scripting/whats-new/what-s-new-in-powershell-70) en aan de slag [met Power shell core](https://blogs.msdn.microsoft.com/powershell/2017/06/09/getting-started-with-powershell-core-on-windows-mac-and-linux/) blog post.