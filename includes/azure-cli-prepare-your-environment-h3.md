---
ms.topic: include
ms.date: 09/10/2020
author: dbradish-microsoft
ms.author: dbradish
manager: barbkess
ms.technology: azure-cli
ms.service: azure-cli
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: 01e1f956270ebbc9b9d5d0ebdfdff52875cafedf
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000046"
---
### <a name="prepare-your-environment-for-the-azure-cli"></a>De omgeving voorbereiden op de Azure CLI

- Gebruik [Azure Cloud Shell](../articles/cloud-shell/quickstart.md) met behulp van de bash-omgeving.

   [![Starten insluiten](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell starten")](https://shell.azure.com)   
- [Installeer](/cli/azure/install-azure-cli) de Azure CLI, indien gewenst, om CLI-referentieopdrachten uit te voeren.
   - Als u een lokale installatie gebruikt, meldt u zich aan bij Azure CLI met behulp van de opdracht [AZ login](/cli/azure/reference-index#az-login).  Volg de stappen die worden weergegeven in de terminal, om het verificatieproces te voltooien.  Zie [Aanmelden met Azure CLI](/cli/azure/authenticate-azure-cli) voor aanvullende aanmeldingsopties.
  - Installeer de Azure CLI-extensie bij het eerste gebruik, wanneer u hierom wordt gevraagd.  Zie [Extensies gebruiken met Azure CLI](/cli/azure/azure-cli-extensions-overview) voor meer informatie over extensies.
  - Voer [az version](/cli/azure/reference-index?#az_version) uit om de geïnstalleerde versie en afhankelijke bibliotheken te vinden. Voer [az upgrade](/cli/azure/reference-index?#az_upgrade) uit om te upgraden naar de nieuwste versie.