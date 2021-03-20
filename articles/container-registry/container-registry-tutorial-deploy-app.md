---
title: Zelfstudie - Implementeren vanuit geo-gerepliceerd register
description: Een op Linux gebaseerde web-app implementeren in twee verschillende Azure-regio's met behulp van een containerinstallatiekopie van een Azure-containerregister met geo-replicatie. Deel twee van een serie van drie.
ms.topic: tutorial
ms.date: 08/20/2018
ms.custom: seodec18, mvc
ms.openlocfilehash: 7a203bfc9b1317bc258e4a93ae4ac03ecbdc7a15
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92148418"
---
# <a name="tutorial-deploy-a-web-app-from-a-geo-replicated-azure-container-registry"></a>Zelfstudie: Een web-app implementeren vanuit een Azure-containerregister met geo-replicatie

Dit is deel twee van een serie met drie zelfstudies. In [deel één](container-registry-tutorial-prepare-registry.md) is er een geo-gerepliceerd privécontainerregister gemaakt en is er een containerinstallatiekopie gemaakt op basis van de bron en naar het register gepusht. In dit artikel implementeert u de container naar twee web-app-instanties in twee verschillende Azure-regio's om ervan te profiteren dat het geo-gerepliceerde register dicht bij het netwerk is. Met de containerinstallatiekopie wordt vervolgens elke instantie opgehaald uit het dichtstbijzijnde register.

In deze zelfstudie, het tweede deel in de serie, leert u het volgende:

> [!div class="checklist"]
> * Een containerinstallatiekopie implementeren naar twee *Web App for Containers*-instanties
> * De geïmplementeerde app verifiëren

Als u nog niet een geo-gerepliceerd register hebt gemaakt en de installatiekopie van de voorbeeld-app in een container nog niet naar het register hebt gepusht, gaat u naar de vorige zelfstudie in de serie: [Een Azure-containerregister met geo-replicatie voorbereiden](container-registry-tutorial-prepare-registry.md).

In het volgende artikel uit de reeks werkt u de toepassing bij, en pusht u de bijgewerkte containerinstallatiekopie vervolgens naar het register. Ten slotte bladert u naar elke actieve web-app-instantie om te controleren of de wijziging automatisch in beide is doorgevoerd en om geo-replicatie van Azure Container Registry en webhooks in actie te bekijken.

## <a name="automatic-deployment-to-web-apps-for-containers"></a>Automatische implementatie naar Web App for Containers

Azure Container Registry biedt ondersteuning voor het rechtstreeks implementeren van apps in containers naar [Web App for Containers](../app-service/index.yml). In deze zelfstudie gebruikt u Azure Portal om de containerinstallatiekopie die is gemaakt in de vorige zelfstudie, te implementeren naar twee web-app-abonnementen in verschillende Azure-regio's.

Wanneer u een web-app vanuit een containerinstallatiekopie in het register implementeert en u een geo-gerepliceerd register in dezelfde regio hebt, maakt Azure Container Registry een [webhook](container-registry-webhook.md) voor de implementatie van de installatiekopie. Wanneer u een nieuwe installatiekopie naar uw containeropslagplaats pusht, detecteert de webhook de wijziging en implementeert deze automatisch de nieuwe containerinstallatiekopie naar uw web-app.

## <a name="deploy-a-web-app-for-containers-instance"></a>Een Web App for Containers-instantie implementeren

In deze stap maakt u een Web App for Containers-instantie in de regio *VS - west*.

Meld u aan bij [Azure Portal](https://portal.azure.com) en navigeer naar het register dat u in de vorige zelfstudie hebt gemaakt.

Selecteer **Opslagplaatsen** > **acr-helloworld**, klik onder **Tags** met de rechtermuisknop op de tag **v1** en selecteer **Deploy to web app**:

![Naar App Service implementeren in Azure Portal][deploy-app-portal-01]

Als Deploy to web app is uitgeschakeld, is de gebruiker met beheerdersrechten voor het register mogelijk niet beschikbaar, zoals in de eerste zelfstudie is beschreven in [Een containerregister maken](container-registry-tutorial-prepare-registry.md#create-a-container-registry). U kunt de gebruiker met beheerdersrechten in de Azure-portal inschakelen via **Instellingen** > **Toegangssleutels**.

Onder **Web App for Containers**, dat wordt weergegeven na selectie van Deploy to web app, geeft u de volgende waarden voor elke instelling op:

| Instelling | Waarde |
|---|---|
| **Sitenaam** | Een globaal unieke naam voor de web-app. In dit voorbeeld gebruiken we de indeling `<acrName>-westus` om eenvoudig het register en de regio te identificeren van waaruit de web-app wordt geïmplementeerd. |
| **Resourcegroep** | **Bestaande gebruiken** > `myResourceGroup` |
| **App Service-plan/-locatie** | Maak een nieuw abonnement met de naam `plan-westus` in de regio **VS - west**. |
| **Installatiekopie** | `acr-helloworld:v1` |
| **Besturingssysteem** | Linux |

> [!NOTE]
> Wanneer u een nieuw appservice-abonnement maakt om uw container-app te implementeren, wordt automatisch een standaard abonnement geselecteerd om uw toepassing te hosten. Het standaard abonnement hangt af van de instelling van het besturingssysteem.

Selecteer **Maken** om de web-app voor de regio *VS - west* in te richten.

![Schermopname toont de Web App for Containers met de knop Maken uitgelicht.][deploy-app-portal-02]

## <a name="view-the-deployed-web-app"></a>De geïmplementeerde web-app weergeven

Wanneer de implementatie is voltooid, kunt u de actieve app weergeven door in uw browser naar de URL te navigeren.

Selecteer **App Services** in de portal en selecteer vervolgens de web-app die u in de vorige stap hebt ingericht. De web-app in dit voorbeeld heet *uniqueregistryname-westus*.

Selecteer in de rechterbovenhoek van het **App Service**-overzicht de hyperlink-URL van de web-app om de actieve app in uw browser weer te geven.

![Schermopname toont het App Service-overzicht met de URL van de web-app uitgelicht.][deploy-app-portal-04]

Zodra de Docker-installatiekopie vanuit het geo-gerepliceerde containerregister is geïmplementeerd, toont de site een weergave van de Azure-regio die als host fungeert voor het containerregister.

![Schermopname toont de geïmplementeerde web-app die in een browser weergegeven wordt.][deployed-app-westus]

## <a name="deploy-second-web-app-for-containers-instance"></a>Tweede Web App for Containers-instantie implementeren

Gebruik de procedure die in het vorige gedeelte is beschreven om een tweede web-app naar de regio *VS - oost* te implementeren. Geef onder **Web App for Containers** de volgende waarden op:

| Instelling | Waarde |
|---|---|
| **Sitenaam** | Een globaal unieke naam voor de web-app. In dit voorbeeld gebruiken we de indeling `<acrName>-eastus` om eenvoudig het register en de regio te identificeren van waaruit de web-app wordt geïmplementeerd. |
| **Resourcegroep** | **Bestaande gebruiken** > `myResourceGroup` |
| **App Service-plan/-locatie** | Maak een nieuw abonnement met de naam `plan-eastus` in de regio **VS - oost**. |
| **Installatiekopie** | `acr-helloworld:v1` |
| **Besturingssysteem** | Linux |

Selecteer **Maken** om de web-app voor de regio *VS - oost* in te richten.

![Schermopname toont het maakvenster, voor de Web App for Containers, met de knop Maken uitgelicht.][deploy-app-portal-06]

## <a name="view-the-second-deployed-web-app"></a>De tweede geïmplementeerde web-app weergeven

U kunt de actieve app weergeven door in uw browser naar de URL te navigeren.

Selecteer **App Services** in de portal en selecteer vervolgens de web-app die u in de vorige stap hebt ingericht. De web-app in dit voorbeeld heet *uniqueregistryname-eastus*.

Selecteer in de rechterbovenhoek van het **App Service-overzicht** de hyperlink-URL van de web-app om de actieve app in uw browser weer te geven.

![Web-app op Linux-configuratie in Azure Portal][deploy-app-portal-07]

Zodra de Docker-installatiekopie vanuit het geo-gerepliceerde containerregister is geïmplementeerd, toont de site een weergave van de Azure-regio die als host fungeert voor het containerregister.

![Geïmplementeerde web-app weergegeven in een browser][deployed-app-eastus]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u twee Web App for Containers-instanties geïmplementeerd vanuit een geo-gerepliceerd Azure-containerregister.

Ga naar de volgende zelfstudie om een nieuwe containerinstallatiekopie bij te werken en vervolgens naar het containerregister te implementeren, en om daarna te controleren of de web-apps in beide regio's automatisch zijn bijgewerkt.

> [!div class="nextstepaction"]
> [Een update implementeren naar de geo-gerepliceerde containerinstallatiekopie](./container-registry-tutorial-deploy-update.md)

<!-- IMAGES -->
[deploy-app-portal-01]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-01.png
[deploy-app-portal-02]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-02.png
[deploy-app-portal-03]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-03.png
[deploy-app-portal-04]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-04.png
[deploy-app-portal-05]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-05.png
[deploy-app-portal-06]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-06.png
[deploy-app-portal-07]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-07.png
[deployed-app-westus]: ./media/container-registry-tutorial-deploy-app/deployed-app-westus.png
[deployed-app-eastus]: ./media/container-registry-tutorial-deploy-app/deployed-app-eastus.png