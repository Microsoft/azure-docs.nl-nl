---
title: CI/CD-pijp lijn met Azure DevOps starter-Azure IoT Edge | Microsoft Docs
description: Met Azure DevOps Starter kunt u eenvoudig aan de slag met Azure. U kunt in een paar snelle stappen een Azure IoT Edge-app van uw keuze starten.
author: kgremban
ms.author: kgremban
ms.date: 08/25/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 97dc0fe5a3720a41dd63583c222762d832d636ea
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436994"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-starter"></a>Een CI/CD-pijp lijn maken voor IoT Edge met Azure DevOps starter

Configureer doorlopende integratie (CI) en continue levering (CD) voor uw IoT Edge-toepassing met DevOps Projects. Azure DevOps Starter vereenvoudigt de eerste configuratie van een build- en release-pijplijn in Azure Pipelines.

Als u nog geen actief abonnement op Azure hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free) aan voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

In DevOps Starter wordt een CI/CD-pijplijn gemaakt in Azure DevOps. U kunt een nieuwe Azure DevOps-organisatie maken of een bestaande organisatie gebruiken. Met DevOps Starter worden ook Azure-resources gemaakt in het Azure-abonnement van uw keuze.

1. Meld u aan bij de [Microsoft Azure-portal](https://portal.azure.com).

1. Selecteer in het linkerdeel venster **een resource maken** en zoek naar **DevOps starter**.  

1. Selecteer **Maken**.

## <a name="create-a-new-application-pipeline"></a>Een nieuwe toepassings pijplijn maken

1. Uw Azure IOT Edge module (s) kunnen worden geschreven in [C#](tutorial-csharp-module.md), [Node.js](tutorial-node-module.md), [python](tutorial-python-module.md), [C](tutorial-c-module.md) en [Java](tutorial-java-module.md). Selecteer de taal van uw voor keur om een nieuwe toepassing te starten: **.net**, **Node.js**, **python**, **C** of **Java**. Selecteer **Volgende** om door te gaan.

   ![Taal selecteren voor het maken van een nieuwe toepassing](./media/how-to-devops-starter/select-language.png)

2. Selecteer **Simple IOT** als uw toepassings raamwerk en selecteer vervolgens **volgende**.

   ![Selecteer een eenvoudig IoT-Framework](media/how-to-devops-starter/select-iot.png)

3. Selecteer **IOT Edge** als de Azure-service waarmee uw toepassing wordt geïmplementeerd en selecteer **volgende**.

   ![IoT Edge service selecteren](media/how-to-devops-starter/select-iot-edge.png)

4. Maak gratis een nieuwe Azure DevOps-organisatie of kies een bestaande organisatie.

   1. Geef een naam op voor uw project.

   2. Selecteer uw Azure DevOps-organisatie. Als u geen bestaande organisatie hebt, selecteert u **extra instellingen** om een nieuwe te maken.

   3. Selecteer uw Azure-abonnement.

   4. Gebruik de IoT Hub naam die is gegenereerd door de naam van uw project of geef uw eigen namen op.

   5. Accepteer de standaard locatie of kies een van de besluitingen.

   6. Selecteer **extra instellingen** om de Azure-resources te configureren die DevOps starter namens u maakt.

   7. Selecteer **gereed** om het project te maken.

   ![Naam en project maken](media/how-to-devops-starter/create-project.png)

Na enkele minuten wordt het DevOps Starter-dashboard weergegeven in Azure Portal. Selecteer de naam van uw project om de voortgang weer te geven. U moet de pagina mogelijk vernieuwen. Een voor beeld-IoT Edge toepassing wordt ingesteld in een opslag plaats in uw Azure DevOps-organisatie, een build wordt uitgevoerd en uw toepassing wordt geïmplementeerd op het IoT Edge apparaat. Dit dashboard biedt meer inzicht in uw codeopslagplaats, CI/CD-pijplijn en toepassing in Azure.

   ![Project in Azure Portal weer geven](./media/how-to-devops-starter/portal.png)

## <a name="commit-code-changes-and-execute-cicd"></a>Codewijzigingen doorvoeren en CI/CD uitvoeren

DevOps starter heeft een Git-opslag plaats gemaakt voor uw project in azure opslag plaatsen. In deze sectie bekijkt u de opslag plaats en maakt u code wijzigingen in uw toepassing.

1. Als u wilt navigeren naar de opslag plaats die voor uw project is gemaakt, selecteert u **opslag** plaatsen in het menu van het project dashboard. Met deze koppeling opent u een browser tabblad en de Azure DevOps-opslag plaats voor het nieuwe project.

   ![In azure opslag plaatsen gegenereerde opslag plaats weer geven](./media/how-to-devops-starter/view-repositories.png)

   > [!NOTE]
   > In de volgende stappen wordt uitgelegd hoe u de webbrowser gebruikt om code wijzigingen aan te brengen. Als u uw opslag plaats lokaal wilt klonen, selecteert u **klonen** in de rechter bovenhoek van het venster. Gebruik de meegeleverde URL om uw Git-opslag plaats te klonen in Visual Studio code of uw favoriete ontwikkel programma.

2. De opslag plaats bevat al code voor een module met de naam **FilterModule** op basis van de toepassings taal die u hebt gekozen tijdens het maken van het proces. Open de **modules/FilterModule/module.jsin** het bestand.

   ![module.jsopenen voor een bestand in azure opslag plaatsen](./media/how-to-devops-starter/open-module-json.png)

3. U ziet dat in dit bestand [Azure DevOps build-variabelen](/azure/devops/pipelines/build/variables#build-variables) worden gebruikt in de **versie** parameter. Deze configuratie zorgt ervoor dat er een nieuwe versie van de module wordt gemaakt telkens wanneer een nieuwe build wordt uitgevoerd.

## <a name="examine-the-cicd-pipeline"></a>De CI/CD-pijplijn onderzoeken

In de vorige secties heeft Azure DevOps starter automatisch een volledige CI/CD-pijp lijn geconfigureerd voor uw IoT Edge-toepassing. Nu kunt u de pijp lijn verkennen en aanpassen als dat nodig is. Gebruik de volgende stappen om vertrouwd te raken met de Azure DevOps-build-en release-pijp lijnen.

1. Als u de build-pijp lijnen in uw DevOps-project wilt weer geven, selecteert u **pijp lijnen bouwen** in het menu van het project dashboard. Met deze koppeling worden een browsertabblad en de Azure DevOps build-pijplijn van Azure DevOps voor het nieuwe project geopend.

   ![Build-pijp lijnen weer geven in azure-pijp lijnen](./media/how-to-devops-starter/view-build-pipelines.png)

2. Open de automatisch gegenereerde build-pijp lijn en selecteer **bewerken** in de rechter bovenhoek.

    ![Build-pijp lijn bewerken](media/how-to-devops-starter/click-edit-button.png)

3. In het deel venster dat wordt geopend, kunt u de taken bekijken die optreden wanneer uw build-pijp lijn wordt uitgevoerd. De build-pijp lijn voert diverse taken uit, zoals het ophalen van bronnen uit de Git-opslag plaats, het bouwen van IoT Edge module-installatie kopieën, het pushen van IoT Edge modules naar een container register en het publiceren van uitvoer die worden gebruikt voor implementaties. Zie [Azure-pijp lijnen configureren voor continue integratie voor](how-to-continuous-integration-continuous-deployment-classic.md#create-a-build-pipeline-for-continuous-integration)meer informatie over Azure IOT Edge taken in azure DevOps.

4. Selecteer de koptekst van de **pijp lijn** boven aan de build-pijp lijn om de details van de pijp lijn te openen. Wijzig de naam van de build-pijplijn in een gebruiksvriendelijkere naam.

   ![De pijplijn Details bewerken](./media/how-to-devops-starter/edit-build-pipeline.png)

5. Selecteer **& wachtrij opslaan** en selecteer vervolgens **Opslaan**. Het is optioneel om een opmerking te plaatsen.

6. Selecteer **Triggers** in het menu Build pijplijn. In DevOps Starter is automatisch een CI-trigger gemaakt en met elke doorvoering naar de opslagplaats wordt een nieuwe build gestart.  U kunt desgewenst kiezen of u vertakkingen van het CI-proces wilt opnemen of uitsluiten.

7. Selecteer **Retentie**. Volg de koppeling om u te omleiden naar de project instellingen, waar het Bewaar beleid zich bevindt. Afhankelijk van het scenario kunt u beleidsregels opgeven om een bepaald aantal builds te behouden of te verwijderen.

8. Selecteer **geschiedenis**. Het deel venster Historie bevat een audittrail van recente wijzigingen aan de build. Azure Pipelines houdt alle wijzigingen in de build-pijplijn bij en biedt de mogelijkheid om versies te vergelijken.

9. Wanneer u klaar bent met het verkennen van de build-pijp lijn, gaat u naar de bijbehorende release pijplijn. Selecteer **releases** onder **pijp lijnen** en selecteer vervolgens **bewerken** om de details van de pijp lijn weer te geven.

    ![Release pijplijn weer geven](media/how-to-devops-starter/release-pipeline.png)

10. Onder **Artefacten** selecteert u **Neerzetten**. De bron die dit artefact controleert, is de uitvoer van de build-pijp lijn die u in de vorige stappen hebt onderzocht.

11. Selecteer naast het pictogram voor **neerzetten** de **continue implementatie trigger** die eruitziet als een bliksem schicht. Deze release pijplijn heeft de trigger ingeschakeld, waarmee een implementatie wordt uitgevoerd telkens wanneer er een nieuw build-artefact beschikbaar is. U kunt de trigger eventueel uitschakelen zodat de implementaties handmatig moeten worden uitgevoerd.  

12. Selecteer in het menu voor uw release pijplijn de optie **taken** en kies vervolgens de **ontwikkel** fase in de vervolg keuzelijst. DevOps Projects een release fase hebt gemaakt voor het maken van een IoT-hub, maakt een IoT Edge apparaat in die hub, implementeert de voorbeeld module van de build-pijp lijn en richt een virtuele machine in die moet worden uitgevoerd als uw IoT Edge-apparaat. Zie [Azure-pijp lijnen configureren voor continue implementatie](how-to-continuous-integration-continuous-deployment-classic.md#create-a-release-pipeline-for-continuous-deployment)voor meer informatie over Azure IOT Edge taken voor de cd.

    ![Taken voor continue implementatie weer geven](media/how-to-devops-starter/choose-release.png)

13. Selecteer aan de rechterkant **Releases weergeven**. In deze weergave wordt een versiesgeschiedenis weergegeven.

14. Selecteer de naam van een release om meer informatie hierover weer te geven.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt Azure App Service en gerelateerde resources die u hebt gemaakt, verwijderen wanneer u ze niet meer nodig hebt. Gebruik hiervoor de functionaliteit **Verwijderen** op het DevOps Starter-dashboard.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de taken voor Azure IoT Edge op Azure DevOps in [continue integratie en continue implementatie naar Azure IOT Edge](how-to-continuous-integration-continuous-deployment.md)
* Inzicht in de implementatie van IoT Edge in [IOT Edge implementaties voor één of op een bepaalde schaal](module-deployment-monitoring.md)
* Door loop de stappen om een implementatie te maken, bij te werken of te verwijderen in [IOT Edge modules implementeren en bewaken op schaal](how-to-deploy-at-scale.md).
