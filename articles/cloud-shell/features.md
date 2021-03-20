---
title: Azure Cloud Shell-functies | Microsoft Docs
description: Overzicht van functies in Azure Cloud Shell
services: Azure
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
ms.date: 04/26/2019
ms.author: damaerte
ms.openlocfilehash: a052364b06ac1b9b30cef76db10a79e8ed85b9a3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89470150"
---
# <a name="features--tools-for-azure-cloud-shell"></a>Functies &-hulpprogram ma's voor Azure Cloud Shell

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

Azure Cloud Shell wordt uitgevoerd op `Ubuntu 16.04 LTS` .

## <a name="features"></a>Functies

### <a name="secure-automatic-authentication"></a>Automatische verificatie beveiligen

Cloud Shell veilig en automatisch account toegang verifiëren voor de Azure CLI en Azure PowerShell.

### <a name="home-persistence-across-sessions"></a>$HOME persistentie over sessies

Cloud Shell begeleidt u bij het koppelen van een Azure-bestands share bij de eerste start om bestanden in verschillende sessies te behouden.
Zodra dit is voltooid Cloud Shell, wordt uw opslag (gekoppeld als `$HOME\clouddrive` ) automatisch gekoppeld voor alle toekomstige sessies.
Bovendien wordt uw `$HOME` Directory persistent gemaakt als. img in uw Azure-bestands share.
Bestanden buiten `$HOME` en computer status blijven niet behouden in sessies. Gebruik aanbevolen procedures voor het opslaan van geheimen zoals SSH-sleutels. Services als [Azure Key Vault hebben zelf studies voor Setup](../key-vault/general/manage-with-cli2.md#prerequisites).

[Meer informatie over het persistent maken van bestanden in Cloud Shell.](persisting-shell-storage.md)

### <a name="azure-drive-azure"></a>Azure-station (Azure:)

Power shell in Cloud Shell biedt het Azure-station ( `Azure:` ). U kunt overschakelen naar het Azure-station met `cd Azure:` en weer naar uw basismap met `cd  ~` .
Het Azure-station biedt eenvoudige detectie en navigatie van Azure-resources, zoals compute, netwerk, opslag, enzovoort, vergelijkbaar met de navigatie van het bestands systeem.
U kunt de vertrouwde [Azure PowerShell-cmdlets](/powershell/azure) blijven gebruiken om deze bronnen te beheren, ongeacht het station waarin u zich bevindt.
Wijzigingen die zijn aangebracht in de Azure-resources, direct in Azure Portal of via Azure PowerShell-cmdlets, worden weer gegeven in het Azure-station.  U kunt uitvoeren `dir -Force` om uw resources te vernieuwen.

![Scherm opname van een Azure Cloud Shell die wordt geïnitialiseerd en een lijst met Directory bronnen.](media/features-powershell/azure-drive.png)

### <a name="manage-exchange-online"></a>Exchange Online beheren

Power shell in Cloud Shell bevat een persoonlijke build van de module Exchange Online.  Voer uit `Connect-EXOPSSession` om uw Exchange-cmdlets op te halen.

![Scherm afbeelding van een Azure Cloud Shell het uitvoeren van de opdrachten Connect-EXOPSSession en Get-gebruiker.](media/features-powershell/exchangeonline.png)

 `Get-Command -Module tmp_*` uitvoeren
> [!NOTE]
> De module naam moet beginnen met `tmp_` , als u modules met hetzelfde voor voegsel hebt geïnstalleerd, worden de bijbehorende cmdlets ook weer gegeven. 

![Scherm opname van een Azure Cloud Shell met de opdracht Get-Command-module tmp_ *.](media/features-powershell/exchangeonlinecmdlets.png)

### <a name="deep-integration-with-open-source-tooling"></a>Diep gaande integratie met open source-hulp programma

Cloud Shell bevat vooraf geconfigureerde authenticatie voor open source-hulpprogram ma's, zoals terraform, Ansible en chef-specificatie. Probeer het uit in de voorbeeld scenario's.

## <a name="tools"></a>Hulpprogramma's

|Categorie   |Name   |
|---|---|
|Linux-hulpprogram ma's            |bash<br> zsh<br> &<br> tmux<br> graven<br>               |
|Azure-hulpprogramma's            |[Azure cli](https://github.com/Azure/azure-cli) en [Azure Classic cli](https://github.com/Azure/azure-xplat-cli)<br> [AzCopy](../storage/common/storage-use-azcopy-v10.md)<br> [Azure Functions CLI](https://github.com/Azure/azure-functions-core-tools)<br> [Service Fabric-CLI](../service-fabric/service-fabric-cli.md)<br> [Batch Shipyard](https://github.com/Azure/batch-shipyard)<br> [blobxfer](https://github.com/Azure/blobxfer)|
|Tekst editors           |code (Cloud Shell-editor)<br> vim<br> nano<br> emacs    |
|Broncodebeheer         |git                    |
|Hulpprogramma's om te bouwen            |make<br> maven<br> npm<br> gooien         |
|Containers             |[Docker Machine](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Helm](https://github.com/kubernetes/helm)<br> [DC/OS CLI](https://github.com/dcos/dcos-cli)         |
|Databases              |MySQL-client<br> PostgreSql-client<br> [Hulpprogramma sqlcmd](/sql/tools/sqlcmd-utility)<br> [MSSQL-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|Anders                  |iPython-client<br> [Cloud Foundry CLI](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> [Ansible](https://www.ansible.com/microsoft-azure)<br> [Chef-specificatie](https://www.chef.io/inspec/)<br> [Puppet-flits](https://puppet.com/docs/bolt/latest/bolt.html)<br> [HashiCorp Packer](https://www.packer.io/)<br> [Office 365 CLI](https://pnp.github.io/office365-cli/)|

## <a name="language-support"></a>Taalondersteuning

|Taal   |Versie   |
|---|---|
|.NET Core  |[3.1.302](https://github.com/dotnet/core/blob/master/release-notes/3.1/3.1.6/3.1.302-download.md)       |
|Go         |1,9        |
|Java       |1.8        |
|Node.js    |8.16.0      |
|PowerShell |[7.0.0](https://github.com/PowerShell/powershell/releases)       |
|Python     |2,7 en 3,5 (standaard)|

## <a name="next-steps"></a>Volgende stappen
[Bash in Cloud Shell Quick Start](quickstart.md) <br>
[Power shell in Cloud Shell Quick Start](quickstart-powershell.md) <br>
[Meer informatie over Azure CLI](/cli/azure/) <br>
[Meer informatie over Azure PowerShell](/powershell/azure/) <br>