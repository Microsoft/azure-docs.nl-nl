---
title: 'Quick Start: een python-app maken in de Azure Portal'
description: Ga aan de slag met Azure App Service door uw eerste python-app te implementeren in een Linux-container in App Service met behulp van de Azure Portal.
ms.topic: quickstart
ms.date: 04/01/2021
ms.custom: devx-track-python
robots: noindex
ms.openlocfilehash: 42f29152521da7bf90a550248e8429e51b728b24
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012353"
---
# <a name="quickstart-create-a-python-app-using-azure-app-service-on-linux-azure-portal"></a>Snelstartgids: een python-app maken met behulp van Azure App Service op Linux (Azure Portal)

In deze quickstart implementeert u een Python-web-app op [App Service op Linux](overview.md#app-service-on-linux), een uiterst schaalbare webhostingservice van Azure. U gebruikt de Azure Portal om een voor beeld te implementeren met behulp van de erlenmeyer of Django frameworks. De web-app die u configureert, maakt gebruik van een basis App Service-laag die een kleine kosten in uw Azure-abonnement omkeert.

## <a name="configure-accounts"></a>Accounts configureren

- Als u nog geen Azure-account hebt met een actief abonnement, [maakt u gratis een account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- Als u geen GitHub-account hebt, gaat u naar [github.com](https://github.com) om er een te maken. 

## <a name="fork-the-sample-github-repository"></a>De voor beeld-GitHub-opslag plaats splitsen

1. Open [github.com](https://github.com) en meld u aan.

1. Navigeer naar een van de volgende voor beelden van opslag plaatsen:
    - [Erlenmeyer Hallo wereld](https://github.com/Azure-Samples/python-docs-hello-world)
    - [Django Hallo wereld](https://github.com/Azure-Samples/python-docs-hello-django)

1. Selecteer in de rechter bovenhoek van de pagina GitHub **Fork** om een kopie van de opslag plaats in uw eigen github-account te maken:

    ![Github Fork, opdracht](media/quickstart-python-portal/github-fork-command.png)

    Azure vereist dat u toegang hebt tot de GitHub-organisatie met de opslag plaats. Door het voor beeld te splitsen op uw eigen GitHub-account, hebt u automatisch de benodigde toegang en kunt u ook wijzigingen aanbrengen in de code.

## <a name="provision-the-app-service-web-app"></a>De App Service-web-app inrichten

Een App Service web-app is de webserver waarnaar u uw code implementeert.

1. Open de Azure Portal op [https://portal.azure.com](https://portal.azure.com) en meld u indien nodig aan.

1. Voer in de zoek balk boven aan het Azure Portal ' App Service ' in en selecteer vervolgens **app Services**.

    ![Portal-zoek balk en App Service selecteren](media/quickstart-python-portal/portal-search-bar.png)

1. Selecteer op de pagina **app Services** de optie **+ toevoegen**:

    ![App Service opdracht toevoegen](media/quickstart-python-portal/add-app-service.png)

1. Voer op de pagina **Web-app maken** de volgende acties uit:
    
    | Veld | Actie |
    | --- | --- |
    | Abonnement | Selecteer het Azure-abonnement dat u wilt gebruiken. |
    | Resourcegroep | Selecteer **nieuwe maken** onder de vervolg keuzelijst. Typ ' AppService-PythonQuickstart ' in het pop-upvenster en selecteer **OK**. |
    | Name | Voer een naam in die uniek is voor alle Azure, meestal met een combi natie van uw persoonlijke of bedrijfs namen, zoals *Contoso-testapp-123*. |
    | Publiceren | Selecteer **Code**. |
    | Runtimestack | Selecteer **Python 3,8**. |
    | Besturingssysteem | Selecteer **Linux** (python wordt alleen ondersteund op Linux). |
    | Regio | Selecteer een regio bij u in de buurt. |
    | Linux-plan | Selecteer een App Service plan afsluiten of gebruik **nieuwe maken** om een nieuw abonnement te maken. We raden u aan het **Basic B1** -abonnement te gebruiken. |

    ![Maak een web-app-formulier op het Azure Portal](media/quickstart-python-portal/create-web-app.png)

1. Selecteer onder aan de pagina **bekijken + maken**, Bekijk de details en selecteer vervolgens **maken**.

1. Wanneer het inrichten is voltooid, selecteert u **naar resource gaan** en gaat u naar de pagina Nieuw app service. Uw web-app op dit moment bevat alleen een standaard pagina, dus de volgende stap implementeert voorbeeld code.

Ondervindt u problemen? [Laat het ons weten](https://aka.ms/FlaskPortalQuickstartHelp).

## <a name="deploy-the-sample-code"></a>De voorbeeld code implementeren

1. Selecteer op de pagina Web-app op het Azure Portal **Deployment Center**:
    
    ![Deployment Center-opdracht in het menu App Service](media/quickstart-python-portal/deployment-center-command.png)


1. Selecteer op de pagina **implementatie centrum** het tabblad **instellingen** als deze nog niet is geopend:

    ![Tabblad instellingen van Deployment Center](media/quickstart-python-portal/deployment-center-settings-tab.png)

1. Onder **bron** selecteert u **github** en voert u vervolgens de volgende acties uit op het **github** formulier dat wordt weer gegeven:

    | Veld | Actie |
    | --- | --- |
    | Aangemeld als | Als u zich nog niet hebt aangemeld bij GitHub, meldt u zich nu aan of selecteert u **account wijzigen* als dat nodig is. |
    | Organisatie | Selecteer uw GitHub-organisatie, indien nodig. |
    | Opslagplaats | Selecteer de naam van de voor beeld-opslag plaats die u eerder hebt opgesplitst, **python-docs-Hello-World** (fles) of **python-docs-Hello-Django** (Django). |
    | Vertakking | Selecteer **hoofd**. |

    ![Configuratie van implementatie centrum GitHub-bron](media/quickstart-python-portal/deployment-center-configure-github-source.png)

1. Selecteer boven aan de pagina **Opslaan** om de instellingen toe te passen.:

    ![Sla de configuratie van de GitHub-bron op in het implementatie centrum](media/quickstart-python-portal/deployment-center-configure-save.png)

1. Selecteer het tabblad **Logboeken** om de status van de implementatie weer te geven. Het duurt enkele minuten om het voor beeld te maken en te implementeren en er worden extra logboeken weer gegeven tijdens het proces. Na voltooiing moet de logboeken de status **geslaagd (actief)** hebben:

    ![Tabblad logboeken van het implementatie centrum](media/quickstart-python-portal/deployment-center-logs.png)

Ondervindt u problemen? [Laat het ons weten](https://aka.ms/FlaskPortalQuickstartHelp).

## <a name="browse-to-the-app"></a>Bladeren naar de app

1. Zodra de implementatie is voltooid, selecteert u **overzicht** in het menu aan de linkerkant om terug te keren naar de hoofd pagina van de web-app.

1. Selecteer de **URL** die het adres van de Web-App bevat:

    ![URL van web-app op de pagina overzicht](media/quickstart-python-portal/web-app-url.png)

1. Controleer of de uitvoer van de app "Hallo, wereld!" is:

    ![App die wordt uitgevoerd na de eerste implementatie](media/quickstart-python-portal/web-app-first-deploy-results.png)

Ondervindt u problemen? Raadpleeg eerst de [Handleiding voor het oplossen van problemen](configure-language-python.md#troubleshooting). Als u er niet uitkomt, [laat het ons weten](https://aka.ms/FlaskPortalQuickstartHelp).

## <a name="make-a-change-and-redeploy"></a>Een wijziging aanbrengen en opnieuw implementeren

Omdat u App Service hebt verbonden met uw opslag plaats, worden wijzigingen die u doorvoert in de bron opslagplaats automatisch geïmplementeerd naar de web-app.

1. U kunt direct wijzigingen aanbrengen in uw gevorkte opslag plaats op GitHub, of u kunt de opslag plaats lokaal klonen, wijzigingen aanbrengen en door voeren en vervolgens deze wijzigingen naar GitHub pushen. Een van beide methoden resulteert in een wijziging in de opslag plaats die is verbonden met App Service.

1. Wijzig in de opgesplitste **opslag plaats** het bericht van de app van "Hallo, wereld!" op ' Hello, Azure! ' als volgt:
    - Fles (python-docs-Hallo-wereld-voor beeld): Wijzig de tekst teken reeks in regel 6 van het *Application.py* -bestand.
    - Django (python-docs-Hello-Django-voor beeld): Wijzig de tekst teken reeks op regel 5 van het *views.py* -bestand in de map *Hello* .

1. Voer de wijziging door in de opslag plaats.

    Als u een lokale kloon gebruikt, pusht u deze wijzigingen ook naar GitHub.

1. Op de Azure Portal voor de web-app gaat u terug naar het **implementatie centrum**, selecteert u het tabblad **Logboeken** en noteert u de nieuwe implementatie activiteit die moet worden uitgevoerd.

1. Wanneer de implementatie is voltooid, gaat u terug naar de **overzichts** pagina van de web-app, opent u de URL van de web-app opnieuw en bekijkt u de wijzigingen in de app:

    ![App die wordt uitgevoerd na opnieuw implementeren](media/quickstart-python-portal/web-app-second-deploy-results.png)

Ondervindt u problemen? Raadpleeg eerst de [Handleiding voor het oplossen van problemen](configure-language-python.md#troubleshooting). Als u er niet uitkomt, [laat het ons weten](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="clean-up-resources"></a>Resources opschonen

In de voor gaande stappen hebt u Azure-resources gemaakt in een resource groep met de naam ' AppService-PythonQuickstart ', die wordt weer gegeven op de pagina *overzicht* van de web-app. Als u de web-app blijvend uitvoert, worden er enige kosten in rekening gebracht (zie [Prijzen voor App Service](https://azure.microsoft.com/pricing/details/app-service/linux/)).

Als u deze resources in de toekomst niet meer nodig hebt, selecteert u de naam van de resource groep op de pagina **overzicht** van de web-app om naar het overzicht van resource groepen te navigeren. Deze selecteert u **resource groep verwijderen** en volg de aanwijzingen.

![De resourcegroep verwijderen](media/quickstart-python-portal/delete-resource-group.png)


Ondervindt u problemen? [Laat het ons weten](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Python-web-app (Django) met PostgreSQL](/azure/developer/python/tutorial-python-postgresql-app-portal)

> [!div class="nextstepaction"]
> [Python-app configureren](configure-language-python.md)

> [!div class="nextstepaction"]
> [Gebruikersaanmelding toevoegen aan een Python-web-app](../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [Zelfstudie: Een Python-app in een aangepaste container uitvoeren](tutorial-custom-container.md)
