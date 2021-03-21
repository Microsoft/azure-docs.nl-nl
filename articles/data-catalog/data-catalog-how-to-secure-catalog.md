---
title: Toegang tot Azure Data Catalog beveiligen
description: In dit artikel wordt uitgelegd hoe u een gegevens catalogus en de gegevensassets in Azure Data Catalog kunt beveiligen.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 537254d7fb153e182be8ece13675a357a9a3e24e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674815"
---
# <a name="how-to-secure-access-to-data-catalog-and-data-assets"></a>Toegang tot Data Catalog-en gegevensassets beveiligen

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

> [!IMPORTANT]
> Deze functie is alleen beschikbaar in de Standard-editie van Azure Data Catalog.

Met Azure Data Catalog kunt u opgeven wie toegang heeft tot de gegevens catalogus en welke bewerkingen (REGI Steren, aantekeningen maken, eigenaar worden) op meta gegevens kunnen worden uitgevoerd in de catalogus. 

## <a name="catalog-users-and-permissions"></a>Gebruikers en machtigingen van de catalogus

Een gebruiker of groep toegang verlenen tot een gegevens catalogus en machtigingen instellen:

1. Klik op de [Start pagina van de gegevens catalogus](https://www.azuredatacatalog.com)op **instellingen** op de werk balk.

   ![Knop instellingen van Azure Data Catalog start pagina](media/data-catalog-how-to-secure-catalog/data-catalog-settings.png)

2. Vouw op de pagina instellingen het gedeelte **catalogus gebruikers** uit.

   ![Knop toevoegen Azure Data Catalog gebruikers](media/data-catalog-how-to-secure-catalog/data-catalog-add-button.png)

3. Klik op **Add**.

4. Voer de volledig gekwalificeerde **gebruikers naam** of naam in van de **beveiligings groep** in de Azure Active Directory (Aad) die aan de catalogus zijn gekoppeld. Gebruik een komma (', ') als scheidings teken als u meer dan één gebruiker of groep toevoegt.

   ![Azure Data Catalog gebruikers-gebruikers of groepen](media/data-catalog-how-to-secure-catalog/data-catalog-users-groups.png)

5. Druk op **Enter** of **Tab** uit het tekstvak. 

6. Controleer of alle machtigingen (**aantekeningen** maken, **registreren** en **eigenaar** blijven) standaard worden toegewezen aan deze gebruikers of groepen. Dat wil zeggen dat de gebruiker of groep [gegevens assets kan registreren]( data-catalog-how-to-register.md), gegevensassets kan [aantekeningen]( data-catalog-how-to-annotate.md)maken en [eigenaar van]( data-catalog-how-to-manage.md)gegevensassets kan worden. 

   ![Azure Data Catalog gebruikers-standaard machtigingen](media/data-catalog-how-to-secure-catalog/data-catalog-default-permissions.png)

7. Als u een gebruiker of groep alleen lees toegang wilt geven tot de catalogus, wist u de optie **aantekeningen** voor die gebruiker of groep. Wanneer u dit doet, kan de gebruiker of groep geen aantekeningen toevoegen aan gegevensassets in de catalogus, maar ze kunnen ze weer geven. 

8. Als u wilt voor komen dat een gebruiker of groep gegevens assets registreert, schakelt u de **registratie** optie voor die gebruiker of groep uit.

9. Als u wilt voor komen dat een gebruiker eigenaar kan worden van een gegevensasset, schakelt u de optie **eigenaar nemen** voor die gebruiker of groep uit. 

10. Als u een gebruiker of groep uit catalogus gebruikers wilt verwijderen, klikt u op **x** voor de gebruiker/groep onder aan de lijst. 

   ![Azure Data Catalog catalogus gebruikers-pictogram gebruiker X verwijderen](media/data-catalog-how-to-secure-catalog/data-catalog-delete-user.png)

   > [!IMPORTANT]
   > Het is raadzaam om beveiligings groepen toe te voegen aan Catalog-gebruikers in plaats van gebruikers rechtstreeks toe te voegen en machtigingen toe te wijzen. Voeg vervolgens gebruikers toe aan de beveiligings groepen die overeenkomen met hun rollen en de vereiste toegang tot de catalogus.

## <a name="special-considerations"></a>Speciale overwegingen

- De machtigingen die zijn toegewezen aan beveiligings groepen, zijn additief. Stel dat een gebruiker zich in twee groepen bevindt. Een groep heeft aantekeningen machtigingen en een andere groep heeft geen machtigingen voor aantekeningen. Vervolgens heeft de gebruiker aantekeningen machtigingen. 
- De machtigingen die expliciet aan een gebruiker worden toegewezen, overschrijven de machtigingen die zijn toegewezen aan groepen waartoe de gebruiker behoort. In het vorige voor beeld hebt u de gebruiker expliciet toegevoegd aan catalogus gebruikers en geen machtigingen voor aantekeningen toewijzen. De gebruiker kan geen aantekeningen toevoegen aan gegevensassets, zelfs als de gebruiker lid is van een groep die machtigingen voor aantekeningen heeft.

## <a name="next-steps"></a>Volgende stappen

- [Aan de slag met Azure Data Catalog](data-catalog-get-started.md)
