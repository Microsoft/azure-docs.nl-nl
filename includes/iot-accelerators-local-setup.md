---
title: bestand opnemen
description: bestand opnemen
services: iot-accelerators
author: avneet723
ms.service: iot-accelerators
ms.topic: include
ms.date: 01/17/2019
ms.author: avneet723
ms.custom: include file
ms.openlocfilehash: 1f567b3d083853f9bb342bfad462e8545caa6480
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020235"
---
## <a name="download-the-source-code"></a>De bron code downloaden

De opslag plaatsen voor de bron code voor externe controle zijn de bron code en de docker-configuratie bestanden die u nodig hebt om de micro Services docker-installatie kopieën uit te voeren.

Als u een lokale versie van de opslag plaats wilt klonen en maken, gebruikt u de opdracht regel omgeving om naar een geschikte map op uw lokale computer te gaan. Voer vervolgens een van de volgende sets opdrachten uit om de .NET-opslag plaats te klonen:

Als u de nieuwste versie van de .NET Micro service-implementaties wilt downloaden, voert u de volgende handelingen uit:

```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-dotnet
git submodule foreach git pull origin master
```

> [!NOTE]
> Met deze opdrachten wordt de bron code voor alle micro Services gedownload naast de scripts die u gebruikt om de micro services lokaal uit te voeren. Hoewel u de bron code niet nodig hebt om de micro services uit te voeren in docker, is de bron code nuttig als u later de oplossings versneller wilt wijzigen en uw wijzigingen lokaal wilt testen.

## <a name="deploy-the-azure-services"></a>De Azure-Services implementeren

Hoewel in dit artikel wordt uitgelegd hoe u de micro services lokaal kunt uitvoeren, zijn ze afhankelijk van Azure-Services die in de cloud worden uitgevoerd. Gebruik het volgende script om de Azure-Services te implementeren. In de volgende script voorbeelden wordt ervan uitgegaan dat u de .NET-opslag plaats op een Windows-computer gebruikt. Als u in een andere omgeving werkt, past u de paden, bestands extensies en pad scheidings tekens op de juiste wijze aan.

### <a name="create-new-azure-resources"></a>Nieuwe Azure-resources maken

Als u de vereiste Azure-resources nog niet hebt gemaakt, voert u de volgende stappen uit:

1. In uw opdracht regel omgeving gaat u naar de map **\services\scripts\local\launch** in de gekloonde kopie van de opslag plaats.

1. Voer de volgende opdrachten uit om het hulp programma **pc's** CLI te installeren en u aan te melden bij uw Azure-account:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Voer het script **Start. cmd** uit. Het script vraagt u om de volgende informatie:
   * De naam van een oplossing.
   * Het te gebruiken Azure-abonnement.
   * De locatie van het Azure-Data Center dat moet worden gebruikt.

     Met het script wordt een resource groep in azure gemaakt met de naam van uw oplossing. Deze resource groep bevat de Azure-resources die door de oplossings versneller worden gebruikt. U kunt deze resource groep verwijderen als u de bijbehorende resources niet meer nodig hebt.

     Het script voegt ook een set omgevings variabelen met een voor voegsel- **pc's** toe aan uw lokale computer. Deze omgevings variabelen bieden de Details voor externe controle om te kunnen lezen van een Azure Key Vault bron. Deze Key Vault resource is waar de configuratie waarden van de externe controle van worden gelezen.

     > [!TIP]
     > Wanneer het script is voltooid, worden de omgevings variabelen ook opgeslagen in een bestand met de naam **\<your home folder\> \\ PCs. \\ \<solution name\> env**. U kunt deze gebruiken voor toekomstige oplossings versnelle implementaties. Houd er rekening mee dat omgevings variabelen die op uw lokale computer zijn ingesteld, waarden in het bestand **\\ \\ Local \\ . env** van de Services-scripts worden overschreven wanneer u **docker-opstellen** uitvoert.

1. Sluit af vanaf uw opdracht regel omgeving.

### <a name="use-existing-azure-resources"></a>Bestaande Azure-resources gebruiken

Als u de vereiste Azure-resources al hebt gemaakt, maakt u de bijbehorende omgevings variabelen op uw lokale machine.
Stel de omgevings variabelen voor het volgende in:
* **PCS_KEYVAULT_NAME** -naam van de Azure Key Vault resource
* **PCS_AAD_APPID** -de Aad-toepassings-id
* **PCS_AAD_APPSECRET** -het Aad-toepassings geheim

Configuratie waarden worden gelezen uit deze Azure Key Vault bron. Deze omgevings variabelen kunnen worden opgeslagen in het bestand **\<your home folder\> \\ . pc's \\ \<solution name\> . env** van de implementatie. Houd er rekening mee dat omgevings variabelen die op uw lokale computer zijn ingesteld, waarden in het bestand **\\ \\ Local \\ . env van Services-scripts** worden vervangen wanneer u **docker-opstellen** uitvoert.

Een deel van de configuratie die nodig is voor de micro service, wordt opgeslagen in een exemplaar van **Key Vault** dat is gemaakt tijdens de eerste implementatie. De bijbehorende variabelen in de sleutel kluis moeten zo nodig worden gewijzigd.