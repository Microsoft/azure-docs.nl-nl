---
title: bestand opnemen
description: bestand opnemen
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 01/28/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 1327ac49920af353d6adbbe592ab4189417e60e8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996888"
---
## <a name="use-cli-shell"></a>CLI-shell gebruiken

Het is raadzaam om [Azure Cloud Shell](../articles/cloud-shell/overview.md?view=azure-cli-latest) te gebruiken om CLI-opdrachten uit te voeren. **Cloud Shell** is een gratis, interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. Er zijn vooraf algemene Azure-hulpprogramma's geïnstalleerd en geconfigureerd in Cloud Shell die u kunt gebruiken bij uw account. Deze biedt de mogelijkheid om zelf de shell-ervaring te kiezen die het beste past bij uw manier van werken. Linux-gebruikers kunnen kiezen voor een Bash-ervaring en Windows-gebruikers kunnen kiezen voor PowerShell.

U kunt de CLI ook lokaal installeren. Zie [Install the Azure CLI](/cli/azure/install-azure-cli) (Azure CLI installeren) voor instructies voor uw platform.

### <a name="sign-in"></a>Aanmelden

Als u een lokale installatie van de CLI wilt gebruiken, moet u zich aanmelden bij Azure. Deze stap is niet vereist voor Azure Cloud Shell. Meld u aan met de opdracht `az login`.

Als de CLI uw standaardbrowser kan openen, gebeurt dat ook en wordt er een aanmeldingspagina gedownload. Anders moet u een browserpagina openen en de instructies in de opdrachtregel volgen om in uw browser naar https://aka.ms/devicelogin te navigeren en een autorisatiecode in te voeren.

### <a name="specify-location-of-files"></a>Locatie van bestanden opgeven

Met veel Media Services CLI-opdrachten kunt u een parameter doorgeven aan een bestandsnaam. Als u **Cloud Shell** gebruikt, kunt u uw bestand uploaden naar uw clouddrive (met behulp van Bash of PowerShell). 

![Bestanden uploaden]

Of u nu een lokale CLI of **Cloud Shell** gebruikt, u moet het bestandspad opgeven op basis van het besturingssysteem of de Cloud Shell (Bash of PowerShell) die u gebruikt. Hier volgen enkele voorbeelden:

Relatief pad naar het bestand (alle besturingssystemen)

* `@"mytestfile.json"`
* `@"../mytestfile.json"`

Absoluut bestandspad in Linux-, Mac- en Windows-besturingssystemen

* `@ "/usr/home/mytestfile.json"`
*    `@"c:\tmp\user\mytestfile.json"`

Gebruik `{file}` als de opdracht om een pad naar het bestand gaat vragen. Bijvoorbeeld `az ams transform create -a amsaccount -g resourceGroup -n custom --preset .\customPreset.json`. <br/> Gebruik `@{file}` als de opdracht het opgegeven bestand moet gaan laden. Bijvoorbeeld `az ams account-filter create -a amsaccount -g resourceGroup -n filterName --tracks @tracks.json`.

[Bestanden uploaden]: ./media/media-services-cli/upload-download-files.png