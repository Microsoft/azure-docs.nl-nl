---
title: Algemeen gebruikerstoegangsbeheer definiëren
description: In grote organisaties kunnen gebruikers machtigingen complex zijn en kunnen worden bepaald door een algemene organisatie structuur, naast de standaard site-en zone structuur.
ms.date: 12/08/2020
ms.topic: article
ms.openlocfilehash: 8f5f8df56a5dcb4152fc0ae9fcae3d504d6cf3e2
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784285"
---
# <a name="define-global-access-control"></a>Algemeen toegangs beheer definiëren

In grote organisaties kunnen gebruikers machtigingen complex zijn en kunnen worden bepaald door een algemene organisatie structuur, naast de standaard site-en zone structuur.

Ter ondersteuning van de vraag naar gebruikers toegangs machtigingen die globaal en complexer zijn, kunt u een wereld wijde bedrijfs topologie maken op basis van bedrijfs eenheden, regio's en sites. Vervolgens kunt u gebruikers toegangs machtigingen voor deze entiteiten definiëren.

Werken met toegangs hulpprogramma's voor bedrijfs topologie helpt organisaties bij het implementeren van niet-vertrouwde strategieën door beter te bepalen waar gebruikers apparaten in het Azure Defender voor IoT-platform beheren en analyseren.

## <a name="about-access-groups"></a>Over toegangs groepen

Wereld wijd toegangs beheer wordt tot stand gebracht door het maken van groepen voor gebruikers toegang. Toegangs groepen bestaan uit regels waarvoor gebruikers toegang hebben tot specifieke bedrijfs entiteiten. Als u met groepen werkt, kunt u de weer gave-en configuratie toegang tot Defender voor IoT beheren voor specifieke gebruikers rollen in relevante bedrijfs eenheden, regio's en sites.

U kunt bijvoorbeeld beveiligings analisten van een Active Directory groep gebruiken om toegang te krijgen tot alle West-Europese auto-en glas productie lijnen, samen met een plastic lijn in één regio.

:::image type="content" source="media/how-to-define-global-user-access-control/sa-diagram.png" alt-text="Diagram van de Active Directory groep van de beveiligings analist.":::

Voordat u toegangs groepen maakt, raden we u aan:

- Stel uw bedrijfs topologie zorgvuldig in. Zie [werken met site kaart weergaven](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views)voor meer informatie over de bedrijfs topologie.

- Plan welke gebruikers zijn gekoppeld aan de toegangs groepen die u maakt. Er zijn twee opties beschikbaar om gebruikers toe te wijzen aan toegangs groepen:

  - **Groepen van Active Directory groepen toewijzen**: Controleer of u een Active Directory instantie hebt ingesteld die u wilt integreren met de on-premises beheer console.
  
  - **Lokale gebruikers toewijzen**: Controleer of u gebruikers hebt gemaakt. Zie [gebruikers definiëren](how-to-create-and-manage-users.md#define-users)voor meer informatie.

Gebruikers met beheerders rechten kunnen niet worden toegewezen aan toegangs groepen. Deze gebruikers hebben standaard toegang tot alle bedrijfs topologie-entiteiten.

## <a name="create-access-groups"></a>Toegangs groepen maken

In deze sectie wordt beschreven hoe u toegangs groepen maakt. De standaard wereld wijde business units en regio's worden gemaakt voor de eerste groep die u maakt. U kunt de standaard entiteiten bewerken wanneer u uw eerste groep definieert.

Groepen maken:

1. Selecteer **toegangs groepen** in het menu aan de zijkant van de on-premises beheer console.

2. Selecteer :::image type="icon" source="media/how-to-define-global-user-access-control/add-icon.png" border="false":::. Voer in het dialoog venster **toegangs groep toevoegen** een naam in voor de toegangs groep. De-console ondersteunt 64 tekens. Wijs de naam op een manier toe waarmee u deze groep eenvoudig kunt onderscheiden van andere groepen.

   :::image type="content" source="media/how-to-define-global-user-access-control/add-access-group.png" alt-text="In het dialoog venster toegangs groep toevoegen kunt u toegangs groepen maken.":::

3. Als de optie **een Active Directory groep toewijzen** wordt weer gegeven, kunt u één Active Directory groep gebruikers aan deze toegangs groep toewijzen.

   :::image type="content" source="media/how-to-define-global-user-access-control/add-access-group.png" alt-text="Wijs een Active Directory groep toe in het dialoog venster toegangs groep maken.":::

   Als de optie niet wordt weer gegeven en u Active Directory groepen wilt opnemen in toegangs groepen, selecteert u **systeem instellingen**. Definieer de groepen in het deel venster **integraties** . Voer een groeps naam precies zoals deze wordt weer gegeven in de Active Directory configuraties, en in kleine letters.

5. Wijs in het deel venster **gebruikers** zo veel gebruikers toe als vereist voor de groep. U kunt gebruikers ook toewijzen aan verschillende groepen. Als u op deze manier werkt, moet u de toegangs groep en-regels maken en opslaan en vervolgens gebruikers toewijzen aan de groep in het deel venster **gebruikers** .

   :::image type="content" source="media/how-to-define-global-user-access-control/role-management.png" alt-text="Beheer de rollen van uw gebruikers en wijs deze toe als dat nodig is.":::

6. Regels maken in het dialoog venster **regels voor *naam* toevoegen** op basis van de toegangs vereisten van uw bedrijfs topologie. De opties die hier worden weer gegeven, zijn gebaseerd op de topologie die u hebt gemaakt in de vensters **bedrijfs weergave** en **site beheer** . 

   U kunt meer dan één regel per groep maken. Mogelijk moet u meer dan één regel per groep maken wanneer u werkt met een complexe toegangs granulatie op meerdere sites. 

   :::image type="content" source="media/how-to-define-global-user-access-control/add-rule.png" alt-text="Voeg een regel toe aan het systeem.":::

De regels die u maakt, worden weer gegeven in het dialoog venster **toegangs groep toevoegen** . Daar kunt u ze verwijderen of bewerken.

:::image type="content" source="media/how-to-define-global-user-access-control/edit-access-groups.png" alt-text="Bekijk en bewerk al uw toegangs groepen in dit venster.":::

### <a name="about-rules"></a>Over regels

Wanneer u regels maakt, moet u rekening houden met de volgende informatie:

- Wanneer een toegangs groep meerdere regels bevat, worden alle regels door de regel logica geaggregeerd. Bijvoorbeeld: het gebruik van regels en logica, niet of logica.

- Als u een regel wilt Toep assen, moet u Sens oren toewijzen aan zones in het venster **site beheer** .

- U kunt slechts één element per regel toewijzen. U kunt bijvoorbeeld één Business Unit, een regio en één site toewijzen voor elke regel. Maak meer regels voor de groep als u bijvoorbeeld wilt dat gebruikers in één Active Directory groep toegang hebben tot verschillende bedrijfs units in verschillende regio's.

- Als u een entiteit wijzigt en de wijziging van invloed is op de regel logica, wordt de regel verwijderd. Als wijzigingen aan een topologie-entiteit invloed hebben op de regel logica, zodat alle regels worden verwijderd, blijft de toegangs groep, maar kunnen gebruikers zich niet aanmelden bij de on-premises beheer console. Gebruikers worden gewaarschuwd om contact op te nemen met de beheerder om zich aan te melden.

- Als er geen bedrijfs eenheid of regio is geselecteerd, hebben gebruikers toegang tot alle gedefinieerde bedrijfs units en regio's.

## <a name="see-also"></a>Zie ook

[Over Defender voor IoT-console gebruikers](how-to-create-and-manage-users.md)
