---
title: Open bare omgevingen configureren en gebruiken in Azure DevTest Labs | Microsoft Docs
description: In dit artikel wordt beschreven hoe u open bare omgevingen (Azure Resource Manager sjablonen in een Git-opslag plaats) in Azure DevTest Labs kunt configureren en gebruiken.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 61cabdb296c3fff75137c7ce7e87652241fd2926
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85482663"
---
# <a name="configure-and-use-public-environments-in-azure-devtest-labs"></a>Open bare omgevingen configureren en gebruiken in Azure DevTest Labs
Azure DevTest Labs heeft een [open bare opslag plaats van Azure Resource Manager sjablonen](https://github.com/Azure/azure-devtestlab/tree/master/Environments) die u kunt gebruiken om omgevingen te maken zonder dat u zelf verbinding hoeft te maken met een externe github-bron. Deze opslag plaats bevat veelgebruikte sjablonen, zoals Azure Web Apps, Service Fabric cluster en ontwikkel share point-farm omgeving. Deze functie is vergelijkbaar met de open bare opslag plaats van artefacten die zijn opgenomen voor elk lab dat u maakt. Met de opslag plaats omgeving kunt u snel aan de slag met vooraf gemaakte omgevings sjablonen met minimale invoer parameters om u te voorzien van een soepele aan de slag-ervaring voor PaaS-resources binnen Labs. 

## <a name="configuring-public-environments"></a>Open bare omgevingen configureren
Als eigenaar van het lab kunt u de opslag plaats voor de open bare omgeving voor uw Lab inschakelen tijdens het maken van het lab. Als u open bare omgevingen voor uw Lab wilt inschakelen **, selecteert u** voor het veld **open bare omgevingen** tijdens het maken van een lab. 

![Open bare omgeving inschakelen voor een nieuw Lab](media/devtest-lab-configure-use-public-environments/enable-public-environment-new-lab.png)


Voor bestaande Labs is de opslag plaats open bare omgeving niet ingeschakeld. Schakel deze hand matig in om sjablonen in de opslag plaats te gebruiken. Voor Labs die is gemaakt met Resource Manager-sjablonen, is de opslag plaats ook standaard uitgeschakeld.

U kunt open bare omgevingen inschakelen/uitschakelen voor uw Lab en alleen specifieke omgevingen beschikbaar maken voor gebruikers met een lab door de volgende stappen uit te voeren: 

1. Selecteer **configuratie en beleid** voor uw Lab. 
2. Selecteer in het gedeelte **virtuele machine bases** de optie **open bare omgevingen**.
3. Als u open bare omgevingen voor het Lab wilt inschakelen, selecteert u **Ja**. Anders selecteert u **Nee**. 
4. Als u open bare omgevingen hebt ingeschakeld, worden alle omgevingen in de opslag plaats standaard ingeschakeld. U kunt de selectie van een omgeving ongedaan maken zodat deze niet beschikbaar is voor uw Lab-gebruikers. 

![Pagina open bare omgevingen](media/devtest-lab-configure-use-public-environments/public-environments-page.png)

## <a name="use-environment-templates-as-a-lab-user"></a>Omgevings sjablonen gebruiken als test gebruiker
Als test gebruiker kunt u een nieuwe omgeving maken op basis van de ingeschakelde lijst met omgevings sjablonen door simpelweg op de werk balk op de pagina Lab op **+ toevoegen** te klikken. De lijst met basissen bevat de sjablonen voor open bare omgevingen die zijn ingeschakeld door uw test beheerder boven aan de lijst.

![Sjablonen voor open bare omgevingen](media/devtest-lab-configure-use-public-environments/public-environment-templates.png)

## <a name="next-steps"></a>Volgende stappen
Deze opslag plaats is een open-source opslag plaats die u kunt bijdragen om veelgebruikte en handige Resource Manager-sjablonen zelf toe te voegen. Als u een bijdrage wilt leveren, moet u een pull-aanvraag indienen bij de opslag plaats.  
