---
title: Clienttools installeren
description: Installeer azdata, kubectl, Azure CLI, psql, Azure Data Studio (insiders) en de Arc-extensie voor Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 6f42f712ecca77c00020304b63f5a1b0dbd77ad0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102172317"
---
# <a name="install-client-tools-for-deploying-and-managing-azure-arc-enabled-data-services"></a>Installeer de clienthulpprogramma's voor het implementeren en beheren van gegevensservices met Azure Arc

> [!IMPORTANT]
> Als u een nieuwe maandelijkse versie bijwerkt, moet u er ook voor zorgen dat u de nieuwste versie van Azure Data Studio, het [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] hulp programma en de- [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] en Azure Arc-extensies voor Azure Data Studio bijwerkt.

In dit document wordt stapsgewijs uitgelegd hoe u de [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] Azure Data Studio, Azure cli (AZ) en het KUBERNETES cli-hulp programma (kubectl) installeert op uw client computer.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="tools-for-creating-and-managing-azure-arc-enabled-data-services"></a>Hulpprogram ma's voor het maken en beheren van gegevens services die geschikt zijn voor Azure Arc 

De volgende tabel bevat algemene hulpprogram ma's die nodig zijn voor het maken en beheren van Azure Arc-gegevens Services en het installeren van deze hulpprogram ma's:

| Hulpprogramma | Vereist | Beschrijving | Installatie |
|---|---|---|---|
| [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] | Ja | Opdracht regel programma voor het installeren en beheren van een big data cluster. [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] bevat ook een opdracht regel programma om verbinding te maken met en query's uit te voeren op Azure SQL-en SQL Server-instanties en post gres-servers met behulp van de opdrachten `azdata sql query` (een enkele query uitvoeren vanaf de opdracht regel), `azdata sql shell` (een interactieve shell), `azdata postgres query` en `azdata postgres shell` . | [Installeren](/sql/azdata/install/deploy-install-azdata?toc=/azure/azure-arc/data/toc.json&bc=/azure/azure-arc/data/breadcrumb/toc.json) |
| Azure Data Studio | Ja | Hulp programma voor uitgebreide ervaring voor het maken van verbinding met en het uitvoeren van query's op diverse data bases, waaronder Azure SQL, SQL Server, PostrgreSQL en MySQL. Uitbrei dingen van Azure Data Studio bieden een beheer ervaring voor gegevens services die zijn ingeschakeld voor Azure Arc. | [Installeren](/sql/azure-data-studio/download-azure-data-studio) |
| [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] extensie voor Azure Data Studio | Ja | Extensie voor Azure Data Studio die wordt geïnstalleerd [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] Als u deze nog niet hebt.| Installeren uit de galerie met extensies in Azure Data Studio.|
| Azure Arc-extensie voor Azure Data Studio | Ja | Uitbrei ding voor Azure Data Studio die een beheer ervaring biedt voor Azure Arc ingeschakelde gegevens Services. Er is een afhankelijkheid van de [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] extensie voor Azure Data Studio. | Installeren uit de galerie met extensies in Azure Data Studio.|
| PostgreSQL-extensie in Azure Data Studio | Nee | PostgreSQL-extensie voor Azure Data Studio die beheer mogelijkheden biedt voor PostgreSQL. | <!--{need link} [Install](../azure-data-studio/data-virtualization-extension.md) --> Installeren uit de galerie met extensies in Azure Data Studio.|
| Azure CLI (AZ)<sup>1</sup> | Ja | Moderne opdracht regel interface voor het beheren van Azure-Services. Wordt gebruikt in combi natie met AKS-implementaties en voor het uploaden van gegevens Services-inventarisatie en facturerings gegevens van Azure-Arc ingeschakeld naar Azure. ([Meer informatie](/cli/azure/)). | [Installeren](/cli/azure/install-azure-cli) |
| Kubernetes CLI (kubectl)<sup>2</sup> | Ja | Opdracht regel programma voor het beheren van het Kubernetes-cluster ([meer informatie](https://kubernetes.io/docs/tasks/tools/install-kubectl/)). | [Windows](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-with-powershell-from-psgallery) \| [Linux](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-using-native-package-management) |
| krul <sup>3</sup> | Vereist voor een aantal voorbeeld scripts. | Opdracht regel programma voor het overdragen van gegevens met Url's. | [Windows](https://curl.haxx.se/windows/) \| Linux: een krul pakket installeren |
| OC | Vereist voor Red Hat open Shift en Azure RedHat-openstaande implementaties. |`oc` is de open Shift-opdracht regel interface (CLI). | [De CLI installeren](https://docs.openshift.com/container-platform/4.4/cli_reference/openshift_cli/getting-started-cli.html#installing-the-cli)



<sup>1</sup> u moet Azure CLI versie 2.0.4 of hoger gebruiken. Voer uit `az --version` om de versie te vinden, indien nodig.

<sup>2</sup> u moet `kubectl` versie 1,13 of hoger gebruiken. Daarnaast moet de versie van de `kubectl` één secundaire versie van uw Kubernetes-cluster plus of min. Als u een specifieke versie op de client wilt installeren `kubectl` , raadpleegt u [ `kubectl` binary installeren via krul](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-binary-using-curl) (in windows 10 gebruikt u cmd.exe en niet Windows Power shell om krul uit te voeren).

<sup>3</sup> als u Power shell gebruikt, is krul een alias voor de Invoke-WebRequest-cmdlet.

## <a name="next-steps"></a>Volgende stappen

[De Azure Arc-gegevens controller maken](create-data-controller.md)