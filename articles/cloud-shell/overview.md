---
title: Overzicht van Azure Cloud Shell | Microsoft Docs
description: Overzicht van de Azure Cloud Shell.
services: ''
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
ms.date: 07/20/2020
ms.author: damaerte
ms.openlocfilehash: f824bddf833a1e2c01a3b779abc2c5252d8e0547
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89468654"
---
# <a name="overview-of-azure-cloud-shell"></a>Overzicht van Azure Cloud Shell

Azure Cloud Shell is een interactieve, geverifieerde en vanuit de browser toegankelijke shell voor het beheer van Azure-resources. Het biedt de flexibiliteit om de shell-ervaring te kiezen die het beste past bij de manier waarop u werkt, dan wel Bash of PowerShell.

U kunt op drie manieren toegang krijgen tot de Cloud Shell:

- **Directe koppeling**: Open een browser naar [https://shell.azure.com](https://shell.azure.com) .

- **Azure Portal**: selecteer het Cloud shell pictogram op de [Azure Portal](https://portal.azure.com):

    ![Pictogram voor het starten van de Cloud Shell van de Azure Portal](media/overview/portal-launch-icon.png)

- **Code fragmenten**: op [docs.Microsoft.com]() en [Microsoft Learn](/learn/), selecteert u de knop **try it** die wordt weer gegeven met Azure CLI en Azure PowerShell code fragmenten:

    ```azurecli-interactive
    az account show
    ```

    ```azurepowershell-interactive
    Get-AzSubscription
    ```

    Met de knop **try it** wordt de Cloud shell direct naast de documentatie geopend met behulp van bash (voor Azure cli-fragmenten) of Power shell (voor Azure PowerShell-fragmenten).

    Als u de opdracht wilt uitvoeren, gebruikt u **kopiëren** in het code fragment, gebruikt u **CTRL** + **+ SHIFT** + **v** (Windows/Linux) of **cmd** + **SHIFT** + **v** (macOS) om de opdracht te plakken en drukt u vervolgens op **Enter**.

## <a name="features"></a>Functies

### <a name="browser-based-shell-experience"></a>Shell-ervaring op basis van een browser

Cloud Shell biedt toegang tot een op een browser gebaseerde opdracht regel ervaring die is gebouwd met Azure-beheer taken. Maak gebruik van Cloud Shell om niet-vastmaken aan de hand van een lokale machine te werken op een manier die alleen in de cloud kan worden verstrekt.

### <a name="choice-of-preferred-shell-experience"></a>Keuze van de voorkeurs shell-ervaring

Gebruikers kunnen kiezen tussen bash of Power shell.

1. Selecteer **Cloud shell**.

    ![Cloud Shell pictogram](media/overview/overview-cloudshell-icon.png)

2. Selecteer **bash** of **Power shell**.

    ![Kies bash of Power shell](media/overview/overview-choices.png)

    Nadat u de eerste keer hebt gestart, kunt u de vervolg keuzelijst shell type gebruiken om over te scha kelen tussen bash en Power shell:

    ![Besturings element vervolg keuzelijst om bash of Power shell te selecteren](media/overview/select-shell-drop-down.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Geverifieerd en geconfigureerd Azure-werk station

Cloud Shell wordt beheerd door micro soft, zodat het beschikt over populaire opdracht regel Programma's en taal ondersteuning. Cloud Shell wordt ook automatisch geauthenticeerd voor directe toegang tot uw resources via de Azure CLI-of Azure PowerShell-cmdlets.

Bekijk de volledige [lijst met hulpprogram ma's die zijn geïnstalleerd in Cloud shell.](features.md#tools)

### <a name="integrated-cloud-shell-editor"></a>Geïntegreerde Cloud Shell editor

Cloud Shell biedt een geïntegreerde grafische tekst editor op basis van de open-source-Monaco-editor. U kunt eenvoudig configuratie bestanden maken en bewerken door uit te voeren `code .` voor naadloze implementatie via Azure CLI of Azure PowerShell.

Meer [informatie over de Cloud shell editor](using-cloud-shell-editor.md).

### <a name="multiple-access-points"></a>Meerdere toegangspunten

Cloud Shell is een flexibel hulp programma dat kan worden gebruikt vanuit:

* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Documentatie voor Azure CLI](/cli/azure)
* [Documentatie voor Azure PowerShell](/powershell/azure/)
* [Mobiele Azure-app](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Visual Studio code Azure-account extensie](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>De opslag van uw Microsoft Azure-bestanden verbinden

Cloud Shell machines zijn tijdelijk, maar uw bestanden worden op twee manieren bewaard: via een schijf installatie kopie en via een gekoppelde bestands share met de naam `clouddrive` . De eerste keer dat u Cloud Shell opstart, verschijnt de vraag of er namens u een resourcegroep, opslagaccount en Azure Files-share kan worden gemaakt. Dit is een eenmalige stap en ze worden voor alle volgende sessies automatisch toegevoegd. Eén bestands share kan worden toegewezen en wordt gebruikt door zowel bash als Power shell in Cloud Shell.

Lees meer voor meer informatie over het koppelen van een [Nieuw of bestaand opslag account](persisting-shell-storage.md) of voor meer informatie over de [persistentie mechanismen die worden gebruikt in Cloud shell](persisting-shell-storage.md#how-cloud-shell-storage-works).

> [!NOTE]
> Azure Storage-firewall wordt niet ondersteund voor Cloud shell-opslag accounts.

## <a name="concepts"></a>Concepten

* Cloud Shell wordt uitgevoerd op een tijdelijke host die wordt verstrekt per sessie per gebruiker
* Cloud Shell na 20 minuten een time-out zonder interactieve activiteit
* Cloud Shell vereist dat een Azure-bestands share wordt gekoppeld
* Cloud Shell gebruikt dezelfde Azure-bestands share voor zowel bash als Power shell
* Aan Cloud Shell is één computer per gebruikers account toegewezen
* Cloud Shell blijft $HOME met behulp van een installatie kopie van 5 GB die is opgeslagen in uw bestands share
* Machtigingen worden ingesteld als een reguliere Linux-gebruiker in bash

Meer informatie over functies in [bash in Cloud shell](features.md) en [power shell in Cloud shell](./features.md).

## <a name="pricing"></a>Prijzen

De machine die Cloud Shell host, is gratis, met een vereiste van een gekoppelde Azure Files share. Normale opslag kosten zijn van toepassing.

## <a name="next-steps"></a>Volgende stappen

[Bash in Cloud Shell Quick Start](quickstart.md) <br>
[Power shell in Cloud Shell Quick Start](quickstart-powershell.md)