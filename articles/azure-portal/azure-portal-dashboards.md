---
title: Een dashboard maken in de Azure Portal
description: In dit artikel wordt beschreven hoe u een dashboard in de Azure Portal.
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.topic: how-to
ms.date: 04/15/2021
ms.openlocfilehash: 0666a9f8ca9df2fa44a7eaa4045c9b5e9a724ff5
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726080"
---
# <a name="create-a-dashboard-in-the-azure-portal"></a>Een dashboard maken in de Azure Portal

Dashboards zijn een gerichte en georganiseerde weergave van uw cloudbronnen in de Azure Portal. Gebruik dashboards als een werkruimte waar u resources kunt bewaken en snel taken kunt starten voor dagelijkse bewerkingen. Bouw bijvoorbeeld aangepaste dashboards op basis van projecten, taken of gebruikersrollen.

De Azure Portal biedt een standaarddashboard als uitgangspunt. U kunt het standaarddashboard bewerken en extra dashboards maken en aanpassen.

> [!NOTE]
> Elke gebruiker kan maximaal 100 privédashboards maken. Als u [het dashboard publiceert en deelt,](azure-portal-dashboard-share-access.md)wordt het geïmplementeerd als een Azure-resource in uw abonnement en wordt deze limiet niet meegetelde.

In dit artikel wordt beschreven hoe u een nieuw dashboard maakt en dit kunt aanpassen. Zie [Azure-dashboards](azure-portal-dashboard-share-access.md)delen met behulp van op rollen gebaseerd toegangsbeheer van Azure voor meer informatie over het delen van dashboards.

## <a name="create-a-new-dashboard"></a>Een nieuw dashboard maken

In dit voorbeeld ziet u hoe u een nieuw privédashboard maakt met een toegewezen naam. Alle dashboards zijn privé wanneer ze worden gemaakt, maar u kunt ervoor kiezen om uw dashboard te publiceren en te delen met andere gebruikers in uw organisatie als u dat wilt.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer in Azure Portal menu Dashboard **.** De standaardweergave is mogelijk al ingesteld op dashboard.

    ![Schermopname van de Azure Portal met Dashboard geselecteerd.](./media/azure-portal-dashboards/portal-menu-dashboard.png)

1. Selecteer **Nieuw dashboard en** vervolgens Leeg **dashboard.**

    ![Schermopname van de opties voor Nieuw dashboard.](./media/azure-portal-dashboards/create-new-dashboard.png)

    Met deze actie wordt de Tegelgalerie geopend, waaruit u tegels kunt selecteren, en een leeg raster waarin u de tegels rangschikt.

1. Selecteer de **tekst Mijn dashboard** in het dashboardlabel en voer een naam in die u helpt het aangepaste dashboard gemakkelijk te identificeren.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-name.png" alt-text="Schermopname van een leeg raster met de Tegelgalerie.":::

1. Als u het dashboard zo wilt opslaan, selecteert **u Aanpassen is uitgevoerd** in de paginakoptekst. U kunt ook doorgaan met de volgende sectie om tegels toe te voegen en uw dashboard op te slaan.

In de dashboardweergave wordt nu uw nieuwe dashboard weergeven. Selecteer de pijl naast de naam van het dashboard om de beschikbare dashboards weer te geven. De lijst kan dashboards bevatten die andere gebruikers hebben gemaakt en gedeeld.

## <a name="edit-a-dashboard"></a>Een dashboard bewerken

Nu gaan we het dashboard bewerken om tegels die uw Azure-resources vertegenwoordigen toe te voegen, het wijzigen van de inhoud en het rangschikken van tegels.

### <a name="add-tiles-from-the-tile-gallery"></a>Tegels toevoegen vanuit de tegelgalerie

Volg deze stappen om tegels toe te voegen aan een dashboard:

1. Selecteer ![ het ](./media/azure-portal-dashboards/dashboard-edit-icon.png) **bewerkingspictogram Bewerken** in de paginakoptekst van het dashboard.

    ![Schermopname van het dashboard met de optie Bewerken.](./media/azure-portal-dashboards/dashboard-edit.png)

1. Blader door **de Tegelgalerie** of gebruik het zoekveld om een bepaalde tegel te vinden. Selecteer de tegel die u aan uw dashboard wilt toevoegen.

   :::image type="content" source="media/azure-portal-dashboards/dashboard-tile-gallery.png" alt-text="Schermopname van de Tegelgalerie.":::

1. Selecteer **Toevoegen** om de tegel toe te voegen aan het dashboard met een standaardgrootte en locatie. Of sleep de tegel naar het raster en plaats deze waar u wilt. Voeg de tegels toe die u wilt, maar hier zijn een aantal ideeën:

    - Voeg **Alle resources toe** om alle resources te zien die u al hebt gemaakt.

    - Als u met meer dan één  organisatie werkt, voegt u de tegel Organisatie-identiteit toe aan uw dashboard om duidelijk weer te geven tot welke organisatie de resources behoren.

1. Indien gewenst, kunt u de tegel het beste het gewenste niveau geven door de rechterbenedenhoek van de tegel te slepen en te laten vallen.

1. Als u uw wijzigingen wilt opslaan, **selecteert u Opslaan** in de koptekst van de pagina. U kunt ook een voorbeeld van de wijzigingen bekijken zonder op te slaan door **Voorbeeld te selecteren** in de paginakoptekst. In het voorbeeldscherm kunt  u Opslaan selecteren om de wijzigingen  te **behouden,** Verwijderen om ze te verwijderen of Bewerken selecteren om terug te gaan naar de bewerkingsopties en verdere wijzigingen aan te brengen.

   :::image type="content" source="media/azure-portal-dashboards/dashboard-save.png" alt-text="Schermopname van de opties Preview, Opslaan en Verwijderen.":::

### <a name="pin-content-from-a-resource-page"></a>Inhoud vastmaken vanaf een resourcepagina

Een andere manier om tegels aan uw dashboard toe te voegen, is rechtstreeks vanaf een resourcepagina.

Veel resourcepagina's bevatten een speldpictogram in de opdrachtbalk. Als u dit pictogram selecteert, kunt u een tegel die de bronpagina vertegenwoordigt vastmaken aan een bestaand dashboard of aan een nieuw dashboard dat u maakt.

![Schermopname van de opdrachtbalk van de pagina met het speldpictogram](./media/azure-portal-dashboards/dashboard-pin-blade.png)

In sommige gevallen kan een speldpictogram ook worden weergegeven door specifieke inhoud op een pagina, wat betekent dat u een tegel voor die specifieke inhoud kunt vastmaken in plaats van de hele pagina.

### <a name="resize-or-rearrange-tiles"></a>Tegels het ize of opnieuw rangschikken

Als u de grootte van een tegel wilt wijzigen of de tegels op een dashboard opnieuw wilt rangschikt, volgt u deze stappen:

1. Selecteer ![ het ](./media/azure-portal-dashboards/dashboard-edit-icon.png) **bewerkingspictogram Bewerken** in de paginakoptekst.

1. Selecteer het contextmenu in de rechterbovenhoek van een tegel. Kies vervolgens een tegelgrootte. Tegels die elke grootte ondersteunen, bevatten ook een 'greep' in de rechterbenedenhoek waarmee u de tegel naar de juiste grootte kunt slepen.

    :::image type="content" source="media/azure-portal-dashboards/dashboard-tile-resize.png" alt-text="Schermopname van het dashboard met het menu Tegelgrootte geopend.":::

1. Selecteer een tegel en sleep deze naar een nieuwe locatie in het raster om uw dashboard te rangschikt.

### <a name="additional-tile-configuration"></a>Aanvullende tegelconfiguratie

Voor sommige tegels is mogelijk meer configuratie vereist om de persoonsgegevens weer te geven. De tegel Grafiek met **metrische** gegevens moet bijvoorbeeld worden ingesteld om een metrische gegevens van de Azure Monitor. U kunt ook tegelgegevens aanpassen om de standaardinstellingen voor tijd van het dashboard te overschrijven.

Een tegel die moet worden ingesteld, geeft een banner weer totdat u de tegel hebt aangepast. Voor de **grafiek Metrische gegevens** is de banner **Bewerken in Metrische gegevens.** De tegel aanpassen:

1. Selecteer opslaan in de **paginakoptekst om** de bewerkingsmodus af te sluiten.

1. Selecteer de banner en doe vervolgens de vereiste installatie.

    ![Schermopname van tegel die configuratie vereist](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> Met een Markdown-tegel kunt u aangepaste, statische inhoud op uw dashboard weergeven. Dit kunnen basisinstructies, een afbeelding, een set hyperlinks of zelfs contactgegevens zijn. Zie Een Markdown-tegel op Azure-dashboards gebruiken om aangepaste inhoud weer te geven voor meer informatie over het gebruik [van een Markdown-tegel.](azure-portal-markdown-tile.md)

### <a name="customize-tile-data"></a>Tegelgegevens aanpassen

Gegevens op het dashboard geven automatisch activiteiten weer voor de afgelopen 24 uur. Volg deze stappen om alleen voor deze tegel een andere tijdspanne weer te geven:

1. Selecteer **Tegelgegevens aanpassen** in het contextmenu of in het ![ ](./media/azure-portal-dashboards/dashboard-filter.png) filterpictogramfilter in de linkerbovenhoek van de tegel.

    ![Schermopname van het contextmenu van de tegel.](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

1. Schakel het selectievakje in om de instellingen **voor de dashboardtijd op tegelniveau te overschrijven.**

    ![Schermopname van het dialoogvenster voor het configureren van de tijdinstellingen voor tegels.](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

1. Kies de tijdspanne die voor deze tegel moet worden weergegeven. U kunt kiezen tussen de afgelopen 30 minuten en de afgelopen 30 dagen of een aangepast bereik definiëren.

1. Kies de tijdgranulatie die moet worden weergegeven. U kunt een stap van één minuut tot één maand laten zien.

1. Selecteer **Toepassen**.

## <a name="delete-a-tile"></a>Een tegel verwijderen

Ga als volgt te werk om een tegel uit een dashboard te verwijderen:

- Selecteer het contextmenu in de rechterbovenhoek van de tegel en selecteer **vervolgens Verwijderen uit dashboard.**

- Selecteer ![ het bewerkingspictogram ](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Bewerken om** de aanpassingsmodus in te schakelen. Beweeg de muisaanwijzer in de rechterbovenhoek van de tegel en selecteer vervolgens het verwijderpictogram om de tegel uit ![ het dashboard te ](./media/azure-portal-dashboards/dashboard-delete-icon.png) verwijderen.

   ![Schermopname die laat zien hoe u een tegel uit het dashboard verwijdert.](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>Een dashboard klonen

Als u een bestaand dashboard als sjabloon voor een nieuw dashboard wilt gebruiken, volgt u deze stappen:

1. Zorg ervoor dat in de dashboardweergave het dashboard wordt weergegeven dat u wilt kopiëren.

1. Selecteer in de paginakoptekst ![ kloonpictogram ](./media/azure-portal-dashboards/dashboard-clone.png) **Kloon**.

1. Er wordt een kopie van het dashboard met **de naam Kloon van** de naam van uw *dashboard* geopend in de bewerkingsmodus. Gebruik de voorgaande stappen in dit artikel om de naam van het dashboard te wijzigen en aan te passen.

## <a name="publish-and-share-a-dashboard"></a>Een dashboard publiceren en delen

Wanneer u een dashboard maakt, is het standaard privé, wat betekent dat u de enige bent die het dashboard kan zien. Als u dashboards beschikbaar wilt maken voor anderen, kunt u ze publiceren en delen. Zie [Azure-dashboards delen met behulp van op rollen gebaseerd toegangsbeheer](azure-portal-dashboard-share-access.md) voor meer informatie.

### <a name="open-a-shared-dashboard"></a>Een gedeeld dashboard openen

Volg deze stappen om een gedeeld dashboard te zoeken en te openen:

1. Selecteer de pijl naast de naam van het dashboard.

1. Selecteer in de weergegeven lijst met dashboards. Als het dashboard dat u wilt openen niet wordt weergegeven:

    1. Selecteer **Door alle dashboards bladeren.**

        ![Schermopname van het menu dashboardselectie](./media/azure-portal-dashboards/dashboard-browse.png)

    1. Selecteer **gedeelde** dashboards in **het veld Type.**

        ![Schermopname van het selectiemenu voor alle dashboards](./media/azure-portal-dashboards/dashboard-browse-all.png)

    1. Selecteer een of meer abonnementen. U kunt ook tekst invoeren om dashboards te filteren op naam.

    1. Selecteer een dashboard in de lijst met gedeelde dashboards.

## <a name="delete-a-dashboard"></a>Een dashboard verwijderen

Als u een privé- of gedeeld dashboard permanent wilt verwijderen, volgt u deze stappen:

1. Selecteer het dashboard dat u wilt verwijderen in de lijst naast de naam van het dashboard.

1. Selecteer ![ het pictogram Verwijderen ](./media/azure-portal-dashboards/dashboard-delete-icon.png) **Verwijderen** in de koptekst van de pagina.

1. Voor een privédashboard selecteert **u OK** in het bevestigingsvenster om het dashboard te verwijderen. Schakel voor een gedeeld dashboard in het bevestigingsvenster het selectievakje in om te bevestigen dat het gepubliceerde dashboard niet meer door anderen kan worden weergegeven. Selecteer vervolgens **OK**.

    ![Schermopname van bevestiging van verwijderen.](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="recover-a-deleted-dashboard"></a>Een verwijderd dashboard herstellen

Als u zich in de wereldwijde Azure-cloud en u een gepubliceerd _dashboard_ in de Azure Portal verwijdert, kunt u dat dashboard binnen 14 dagen na het verwijderen herstellen. Zie Een verwijderd dashboard herstellen in de Azure Portal [voor meer Azure Portal.](recover-shared-deleted-dashboard.md)

## <a name="next-steps"></a>Volgende stappen

- [Azure-dashboards delen met behulp van op rollen gebaseerd toegangsbeheer van Azure](azure-portal-dashboard-share-access.md)
- [Op programmatische wijze Azure-dashboards maken](azure-portal-dashboards-create-programmatically.md)
