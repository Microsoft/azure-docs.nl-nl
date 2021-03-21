---
title: Azure-portalinstellingen en -voorkeuren beheren
description: U kunt Azure Portal standaard instellingen wijzigen om te voldoen aan uw eigen voor keuren. De instellingen omvatten een inactieve sessietime-out, de standaard weergave, de menu modus, het contrast, het thema, de meldingen en de taal-en regionale notaties
keywords: instellingen, time-out, taal, regionale
ms.date: 08/05/2020
ms.topic: how-to
ms.openlocfilehash: 591b1ce6c2c069f9c0266588bf32f7303bf49708
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100588799"
---
# <a name="manage-azure-portal-settings-and-preferences"></a>Azure-portalinstellingen en -voorkeuren beheren

U kunt de standaard instellingen van de Azure Portal wijzigen om te voldoen aan uw eigen voor keuren. De meeste instellingen zijn beschikbaar via het menu **instellingen** in de koptekst van de algemene pagina.

![Scherm opname van globale paginakop tekst pictogrammen met gemarkeerde instellingen](./media/set-preferences/header-settings.png)


## <a name="choose-your-default-subscription"></a>Uw standaard abonnement kiezen

U kunt het abonnement dat standaard wordt geopend, wijzigen wanneer u zich aanmeldt bij de Azure Portal. Dit is handig als u een primair abonnement hebt waarmee u werkt, maar af en toe gebruikt. 

:::image type="content" source="media/set-preferences/filter-subscription-default-view.png" alt-text="Resource lijst filteren op abonnement.":::

1. Selecteer het pictogram voor de map en het abonnement in de koptekst van de algemene pagina.

1. Selecteer de abonnementen die u wilt als de standaard abonnementen wanneer u de portal start. 

    :::image type="content" source="media/set-preferences/default-directory-subscription-filter.png" alt-text="Selecteer de abonnementen die u wilt als de standaard abonnementen wanneer u de portal start."::: 


## <a name="choose-your-default-view"></a>Kies uw standaard weergave 

U kunt de pagina die standaard wordt geopend, wijzigen wanneer u zich aanmeldt bij de Azure Portal.

![Scherm opname met Azure Portal instellingen met standaard weergave gemarkeerd](./media/set-preferences/default-view.png)

- **Start pagina** kan niet worden aangepast.  Het bevat snelkoppelingen naar populaire Azure-Services en geeft een lijst van de resources die u het laatst hebt gebruikt. We bieden u ook nuttige koppelingen naar resources zoals Microsoft Learn en het Azure-schema.

- Dash boards kunnen worden aangepast om een werk ruimte te maken die alleen voor u is ontworpen. U kunt bijvoorbeeld een dash board maken dat is gericht op project, taak of rol. Als u **dash board** selecteert, gaat uw standaard weergave naar het meest recent gebruikte dash board. Zie [Dashboards maken en delen in de Azure Portal](azure-portal-dashboards.md) voor meer informatie.

## <a name="choose-a-portal-menu-mode"></a>Kies een portal menu modus

De standaard modus voor het menu Portal bepaalt hoeveel ruimte het menu van de portal op de pagina inneemt.

![Scherm afbeelding die laat zien hoe u de standaard modus voor het menu van de portal kunt instellen.](./media/set-preferences/menu-mode.png)

- Wanneer het menu van de portal in de **flyout** wordt weer gegeven, wordt het verborgen tot u het nodig hebt. Selecteer het menu-pictogram om het menu te openen of te sluiten.

- Als u de **modus gedokt** kiest voor het menu Portal, is deze altijd zichtbaar. U kunt het menu samenvouwen om meer werkruimte te creëren.

## <a name="choose-a-theme-or-enable-high-contrast"></a>Kies een thema of schakel hoog contrast in

Het thema dat u kiest, is van invloed op de achtergrond-en lettertype kleuren die in de Azure Portal worden weer gegeven. U kunt kiezen uit een van de vier vooraf ingestelde kleuren Thema's. Selecteer elke miniatuur om het thema te vinden dat het beste bij u past.

U kunt ook een van de Thema's met een hoog contrast kiezen. Met de Thema's met hoog contrast kan de Azure Portal gemakkelijker te lezen zijn voor mensen die een visuele waardevermindering hebben ondergaan; ze overschrijven alle andere thema selecties.

![Scherm opname van Azure Portal instellingen met Thema's gemarkeerd](./media/set-preferences/theme.png)

## <a name="enable-or-disable-pop-up-notifications"></a>Pop-upmeldingen in-of uitschakelen

Meldingen zijn systeem berichten die betrekking hebben op uw huidige sessie. Ze bieden informatie zoals het huidige tegoed, wanneer de resources die u zojuist hebt gemaakt beschikbaar zijn of uw laatste actie bevestigen, bijvoorbeeld. Wanneer pop-upmeldingen zijn ingeschakeld, worden de berichten kort weer gegeven in de bovenste hoek van het scherm. 

Schakel **pop-upmeldingen** in of uit om pop-upmeldingen in of uit te scha kelen.

![Scherm opname van Azure Portal instellingen met pop-upmeldingen gemarkeerd](./media/set-preferences/popup-notifications.png)

Als u alle meldingen wilt lezen die tijdens de huidige sessie zijn ontvangen, selecteert u **meldingen** in de globale koptekst.

![Scherm opname van Azure Portal globale koptekst met gemarkeerde meldingen](./media/set-preferences/read-notifications.png)

Als u meldingen van eerdere sessies wilt lezen, zoekt u naar gebeurtenissen in het activiteiten logboek. Zie [het activiteiten logboek weer geven](../azure-monitor/essentials/activity-log.md#view-the-activity-log)voor meer informatie. 

## <a name="change-the-inactivity-timeout-setting"></a>De instelling van de time-out voor inactiviteit wijzigen

Met de instelling time-out voor inactiviteit kunt u resources beveiligen tegen onbevoegde toegang als u uw werk station wilt beveiligen. Nadat u een tijdje niet langer inactief bent, wordt u automatisch afgemeld bij uw Azure Portal-sessie. Als individu kunt u de time-outinstelling voor uzelf wijzigen. Als u een beheerder bent, kunt u deze op mapniveau instellen voor alle gebruikers in de Directory.

### <a name="change-your-individual-timeout-setting-user"></a>De instelling voor de afzonderlijke time-out wijzigen (gebruiker)

Selecteer de vervolg keuzelijst onder **aanmelden wanneer** deze niet actief is. Kies de duur waarna uw Azure Portal-sessie wordt afgemeld als u niet actief bent.

![Scherm opname van Portal instellingen met inactieve time-outinstellingen gemarkeerd](./media/set-preferences/inactive-signout-user.png)

De wijziging wordt automatisch opgeslagen. Als u inactief bent, wordt uw Azure Portal-sessie afgemeld na de periode die u hebt ingesteld.

Als uw beheerder een time-outbeleid voor inactiviteit heeft ingeschakeld, kunt u nog steeds uw eigen waarde instellen, zolang deze minder is dan de instelling voor het niveau van de map. Selecteer **het beleid voor de time-out voor de Directory inactiviteit negeren** en stel vervolgens een tijds interval in.

![Scherm opname van Portal instellingen met onderdrukking van de beleids instelling voor de time-out voor inactiviteit gemarkeerd](./media/set-preferences/inactive-signout-override.png)

### <a name="change-the-directory-timeout-setting-admin"></a>De time-outinstelling van de map wijzigen (beheerder)

Beheerders in de [rol globale beheerder](../active-directory/roles/permissions-reference.md#global-administrator) kunnen het maximum aantal niet-actieve tijd afdwingen voordat een sessie wordt afgemeld. De instelling time-out voor inactiviteit is van toepassing op het niveau van de Directory. De instelling wordt van kracht voor nieuwe sessies. Deze functie wordt niet onmiddellijk toegepast op alle gebruikers die al zijn aangemeld. Zie [Active Directory Domain Services Overview](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)voor meer informatie over directory's.

Als u een globale beheerder bent en u een instelling voor een inactieve time-out wilt afdwingen voor alle gebruikers van de Azure Portal, voert u de volgende stappen uit:

1. Selecteer de koppeling tekst **configureren time-out** mapniveau.

    ![Scherm opname van de Portal instellingen met een koppeling tekst gemarkeerd](./media/set-preferences/settings-admin.png)

1. Selecteer op de pagina mapniveau op **Directory niveau configureren** de optie time-out voor inactiviteit op mapniveau **inschakelen voor de Azure Portal** om de instelling in te scha kelen.

1. Voer vervolgens de **uren** en **minuten** in voor de maximale tijds duur dat een gebruiker inactief mag zijn voordat de sessie automatisch wordt afgemeld.

1. Selecteer **Toepassen**.

    ![Scherm afbeelding van de pagina voor het instellen van de time-out voor inactiviteit op Directory niveau](./media/set-preferences/configure.png)

Als u wilt controleren of het beleid voor time-out van inactiviteit juist is ingesteld, selecteert u **meldingen** in de koptekst van de globale pagina. Controleer of er een melding over geslaagde meldingen wordt weer gegeven.

![Scherm opname van bericht met geslaagde melding voor time-out voor inactiviteit op Directory niveau](./media/set-preferences/confirmation.png)

## <a name="restore-default-settings"></a>Standaardinstellingen herstellen

Als u wijzigingen hebt aangebracht in de Azure Portal instellingen en deze wilt negeren, selecteert u **standaard instellingen herstellen**. Wijzigingen die u in de Portal instellingen hebt aangebracht, gaan verloren. Deze optie is niet van invloed op aanpassingen van het dash board.

![Scherm opname van het herstellen van standaard instellingen](./media/set-preferences/useful-links-restore-defaults.png)

## <a name="export-user-settings"></a>Gebruikers instellingen exporteren

Informatie over uw aangepaste instellingen wordt opgeslagen in Azure. U kunt de volgende gebruikers gegevens exporteren:

* Persoonlijke Dash boards in de Azure Portal
* Gebruikers instellingen zoals favoriete abonnementen of directory's en laatst aangemelde Directory
* Thema's en andere aangepaste Portal instellingen

Het is een goed idee om uw instellingen te exporteren en te controleren als u deze wilt verwijderen. Het opnieuw opbouwen van Dash boards of het opnieuw uitvoeren van instellingen kan tijdrovend zijn.

Als u uw Portal instellingen wilt exporteren, selecteert u **alle instellingen exporteren**.

![Scherm opname van het exporteren van instellingen](./media/set-preferences/useful-links-export-settings.png)

Als u instellingen exporteert, wordt een *JSON* -bestand gemaakt dat uw gebruikers instellingen bevat, zoals uw kleuren thema, favorieten en persoonlijke Dash boards. Als gevolg van de dynamische aard van de gebruikers instellingen en het risico op beschadiging van gegevens, kunt u geen instellingen importeren uit het *JSON* -bestand.

## <a name="delete-user-settings-and-dashboards"></a>Gebruikers instellingen en-dash boards verwijderen

Informatie over uw aangepaste instellingen wordt opgeslagen in Azure. U kunt de volgende gebruikers gegevens verwijderen:

* Persoonlijke Dash boards in de Azure Portal
* Gebruikers instellingen zoals favoriete abonnementen of directory's en laatst aangemelde Directory
* Thema's en andere aangepaste Portal instellingen

Het is een goed idee om uw instellingen te exporteren en te controleren voordat u deze verwijdert. Het opnieuw opbouwen van Dash boards of het opnieuw uitvoeren van aangepaste instellingen kan tijdrovend zijn.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

Als u uw Portal instellingen wilt verwijderen, selecteert u **alle instellingen en persoonlijke Dash boards verwijderen**.

![Scherm opname van het verwijderen van instellingen](./media/set-preferences/useful-links-delete-settings.png)

## <a name="change-language-and-regional-settings"></a>Taal-en land instellingen wijzigen

Er zijn twee instellingen die bepalen hoe de tekst in het Azure Portal wordt weer gegeven: 
- De **taal** instelling bepaalt de taal die u ziet voor tekst in de Azure Portal. 

- **Regionale notatie** bepaalt de manier waarop datums, tijden, getallen en valuta worden weer gegeven.

Als u de taal wilt wijzigen die wordt gebruikt in de Azure Portal, gebruikt u de vervolg keuzelijst om te selecteren in de lijst met beschik bare talen.

De regionale indelings selectie wordt gewijzigd zodat land instellingen alleen worden weer gegeven voor de geselecteerde taal. Als u deze automatische selectie wilt wijzigen, gebruikt u de vervolg keuzelijst om de gewenste regionale indeling te kiezen.

Als u bijvoorbeeld Engels als taal selecteert en vervolgens Verenigde Staten selecteert als regionale indeling, wordt valuta weer gegeven in Amerikaanse dollars. Als u Engels als taal selecteert en vervolgens Europa selecteert als regionale indeling, wordt valuta weer gegeven in euro.

Selecteer **Toep assen** om uw instellingen voor taal en regionale indeling bij te werken.

   ![Scherm opname van instellingen voor taal en land indeling](./media/set-preferences/language.png)

>[!NOTE]
>Deze taal-en land instellingen zijn alleen van invloed op de Azure Portal. Documentatie voor koppelingen die in een nieuw tabblad of venster worden geopend, worden de taal instellingen van uw browser gebruikt om te bepalen welke taal moet worden weer gegeven.
>

## <a name="next-steps"></a>Volgende stappen

- [Sneltoetsen in Azure Portal](azure-portal-keyboard-shortcuts.md)
- [Ondersteunde browsers en apparaten](azure-portal-supported-browsers-devices.md)
- [Favorieten toevoegen, verwijderen en opnieuw ordenen](azure-portal-add-remove-sort-favorites.md)
- [Aangepaste dashboards maken en delen](azure-portal-dashboards.md)
- [Videoserie met instructies voor de Azure-portal](azure-portal-video-series.md)
