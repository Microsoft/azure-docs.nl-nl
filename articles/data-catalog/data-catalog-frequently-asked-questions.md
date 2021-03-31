---
title: Veelgestelde vragen over Azure Data Catalog
description: Veelgestelde vragen over Azure Data Catalog, met inbegrip van mogelijkheden voor het detecteren van gegevens bronnen, aantekening en beheer.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 98854a4588b59cf0c19877da870e6124fa7c3b9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674679"
---
# <a name="azure-data-catalog-frequently-asked-questions"></a>Veelgestelde vragen over Azure Data Catalog

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

In dit artikel vindt u antwoorden op veelgestelde vragen met betrekking tot de Azure Data Catalog-service.

## <a name="what-is-azure-data-catalog"></a>Wat is Azure Data Catalog?
Data Catalog is een volledig beheerde service, die wordt gehost in Microsoft Azure, die fungeert als een systeem voor registratie en detectie voor zakelijke gegevens bronnen. Met Data Catalog kunnen alle gebruikers, van analisten tot gegevens wetenschappers en ontwikkel aars, gegevens bronnen registreren, detecteren, begrijpen en gebruiken.

## <a name="what-customer-challenges-does-it-solve"></a>Wat voor uitdagingen van klanten wordt het opgelost?
Data Catalog verhelpt de uitdagingen van de detectie van gegevens bronnen en ' donkere gegevens ', zodat gebruikers ondernemings gegevens bron kunnen ontdekken en begrijpen.

## <a name="what-are-its-target-audiences"></a>Wat zijn de doel groepen?
Data Catalog is ontworpen voor technische en niet-technische gebruikers, waaronder:

* Ontwikkel aars van gegevens en BI-en analyse professionals: mensen die verantwoordelijk zijn voor het produceren van gegevens en analyse-inhoud die anderen kunnen gebruiken.
* Data-procedures: mensen die kennis hebben van de gegevens, wat het betekent en hoe deze moeten worden gebruikt.
* Data consumers: mensen die de gegevens die ze nodig hebben om hun werk te doen, gemakkelijk kunnen detecteren, begrijpen en er verbinding mee moeten maken, door gebruik te maken van het hulp programma van hun keuze.
* IT: mensen die honderden gegevens bronnen kunnen detecteren door zakelijke gebruikers, en die het toezicht moeten behouden over de manier waarop gegevens worden gebruikt en door wie.

## <a name="what-is-its-availability-by-region"></a>Wat is de beschik baarheid per regio?
Data Catalog Services zijn momenteel beschikbaar in de volgende data centers:

* VS - west
* VS - oost
* Europa -west
* Europa - noord
* Australië - oost
* Azië - zuidoost

## <a name="what-are-its-limits-on-the-number-of-data-assets"></a>Wat zijn de limieten voor het aantal gegevensassets?
De gratis versie van Data Catalog is beperkt tot 5.000 geregistreerde gegevensassets.

De Standard-editie van Data Catalog ondersteunt Maxi maal 100.000 geregistreerde gegevensassets.

Elk object dat is geregistreerd in Data Catalog, zoals tabellen, weer gaven, bestanden en rapporten, telt als gegevensasset.

## <a name="what-are-its-supported-data-source-and-asset-types"></a>Wat zijn de ondersteunde gegevens bron en activa typen?
Zie [Data CATALOG DSR](data-catalog-dsr.md)voor een lijst met gegevens bronnen die momenteel worden ondersteund.

## <a name="how-do-i-request-support-for-another-data-source"></a>Hoe kan ik ondersteuning aanvragen voor een andere gegevens bron?
Als u functie aanvragen en andere feedback wilt verzenden, gaat u naar de [Data Catalog op de Azure feedback forums](https://feedback.azure.com/forums/906052-data-catalog/category/320788-data-sources).

## <a name="why-do-i-get-an-error-catalog-already-exists-when-i-try-to-create-a-new-catalog"></a>Waarom verschijnt er al een fout *catalogus* wanneer ik probeer een nieuwe catalogus te maken?

Wanneer u Office 365 E5 aanschaft met Power BI Pro licentie, maakt micro soft automatisch een standaard catalogus in de regio van het abonnement. Deze catalogus maakt gebruik van de gratis SKU. De gebruikers licentie voor Office 365/Power BI wordt beheerd op de pagina beheer. 

Dit type gegevens catalogus heeft echter geen **beheerders optie** en is niet zichtbaar in de **Azure Portal**. U kunt dit type gegevens catalogus niet verwijderen. Op dezelfde manier mag u de naam van de gegevens catalogus niet wijzigen en kunt u deze niet naar een andere regio verplaatsen. 

Gebruikers accounts waaraan een Power BI Pro-licentie is toegewezen, hebben automatisch toegang tot de Data Catalog als gevolg van de licentie overeenkomst wanneer ze zich hebben geregistreerd voor Office 365 E5 met de Power BI Pro-licentie. Dit type gebruiker heeft volledige toegang tot assets van data Catalogs zonder beheerders bevoegdheden. Dit type gebruiker maakt *geen* deel uit van de gebruikersrol **Catalog** in azure Data Catalog.


## <a name="how-do-i-get-started-with-data-catalog"></a>Hoe kan ik aan de slag met Data Catalog?
De beste manier om aan de slag te gaan, is aan de slag [met Data Catalog](data-catalog-get-started.md). Dit artikel is een end-to-end-overzicht van de mogelijkheden van de service.

## <a name="how-do-i-register-my-data"></a>Hoe kan ik mijn gegevens registreren?
Uw gegevens registreren in Data Catalog:
1. Start in de Azure Data Catalog Portal in het gedeelte **publiceren** het Azure Data Catalog registratie programma. 
2. Meld u in het Data Catalog hulp programma voor gegevens bron registratie aan met dezelfde referenties die u gebruikt om toegang te krijgen tot de Data Catalog-Portal.
3. Selecteer de gegevens bron en de specifieke assets die u wilt registreren.

## <a name="what-properties-does-it-extract-for-data-assets-that-are-registered"></a>Welke eigenschappen worden uitgepakt voor gegevensassets die zijn geregistreerd?
De specifieke eigenschappen verschillen van de gegevens bron tot de gegevens bron, maar in het algemeen haalt de Data Catalog Publishing-service de volgende informatie op:

* Activa naam
* Assettype
* Beschrijving van activum
* Kenmerk/kolom namen
* Gegevens typen voor kenmerken/kolommen
* Beschrijving van kenmerk/kolom

> [!IMPORTANT]
> Door gegevensassets te registreren met Data Catalog worden uw gegevens niet naar de Cloud verplaatst of gekopieerd. Als u assets van een gegevens bron registreert, worden de meta gegevens van de assets naar Azure gekopieerd, maar de gegevens blijven op de bestaande locatie van de gegevens bron staan. De uitzonde ring op deze regel is als u ervoor kiest om voorbeeld records of een gegevens profiel te uploaden wanneer u de activa registreert. Wanneer u een preview-versie opneemt, worden Maxi maal twintig records van elke Asset gekopieerd en opgeslagen als een moment opname in Data Catalog. Wanneer u een gegevens profiel opneemt, worden de verzamelde gegevens berekend en opgenomen in de meta gegevens die zijn opgeslagen in de catalogus. Statistische gegevens kunnen de omvang van tabellen, het percentage Null-waarden per kolom of de minimum-, maximum-en gemiddelde waarden voor kolommen bevatten. 
>
>

> [!NOTE]
> Voor gegevens bronnen, zoals SQL Server Analysis Services die een eigenschap van de klasse **Description** hebben, haalt het hulp programma voor het registreren van Data Catalog gegevens die eigenschaps waarde uit. Voor *on-premises* SQL Server relationele data bases die geen eigenschap topklasse **Description** hebben, haalt het Data Catalog hulp programma voor gegevens bron registratie de waarde uit de uitgebreide eigenschap **ms_description** voor objecten en kolommen. Deze eigenschap wordt niet ondersteund voor SQL Azure. Zie voor meer informatie [uitgebreide eigenschappen gebruiken voor database objecten](/previous-versions/sql/sql-server-2008-r2/ms190243(v=sql.105)).
>
>

## <a name="how-long-should-it-take-for-newly-registered-assets-to-appear-in-the-catalog"></a>Hoe lang duurt het voordat nieuwe geregistreerde assets in de catalogus worden weer gegeven?
Nadat u assets bij Data Catalog hebt geregistreerd, kan het een periode van 5 tot 10 seconden duren voordat deze in de Data Catalog Portal worden weer gegeven.

## <a name="how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>Hoe kan ik aantekeningen maken en de meta gegevens verrijken voor mijn geregistreerde gegevensassets?
De eenvoudigste manier om meta gegevens voor geregistreerde assets te bieden is door de Asset te selecteren in de Data Catalog Portal en vervolgens de waarden in het deel venster Eigenschappen of het schema deel venster voor het geselecteerde object in te voeren.

Tijdens het registratie proces kunt u ook meta gegevens, zoals experts en tags, opgeven. De waarden die u opgeeft in de Data Catalog Publishing-service, zijn van toepassing op alle activa die op dat moment worden geregistreerd. Als u de onlangs geregistreerde objecten in de portal wilt weer geven voor aanvullende aantekening, selecteert u de knop **Portal weer geven** in het laatste scherm van het hulp programma voor registratie van gegevens bronnen Data Catalog.

## <a name="how-do-i-delete-my-registered-data-objects"></a>Hoe kan ik mijn geregistreerde gegevens objecten verwijderen?
U kunt een object verwijderen uit Data Catalog door het object te selecteren in de portal en vervolgens op de knop **verwijderen** te klikken. Als u het object verwijdert, worden de meta gegevens van Data Catalog verwijderd, maar heeft dit geen invloed op de onderliggende gegevens bron.

## <a name="what-is-an-expert"></a>Wat is een expert?
Een expert is een persoon die een geïnformeerd perspectief over een gegevens object heeft. Een object kan meerdere experts hebben. Een expert hoeft niet de eigenaar van een object te zijn, maar is gewoon iemand die weet hoe de gegevens kunnen en moeten worden gebruikt.

## <a name="how-do-i-share-information-with-the-data-catalog-team-if-i-encounter-problems"></a>Hoe kan ik informatie met het Data Catalog team delen als er problemen optreden?
Ga naar het [Azure Data Catalog forum](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)om problemen te melden, informatie te delen en vragen te stellen.

## <a name="does-the-catalog-work-with-another-data-source-that-im-interested-in"></a>Werkt de catalogus met een andere gegevens bron waarin ik geïnteresseerd ben?
Er wordt actief gewerkt aan het toevoegen van meer gegevens bronnen aan Data Catalog. Als u een specifieke gegevens bron wilt weer geven, kunt u deze Voorst Ellen (of uw ondersteunings gesprek ondervragen als deze al is voorgesteld) door naar de [Data Catalog op de Azure feedback forums](https://feedback.azure.com/forums/906052-data-catalog)te gaan.

## <a name="what-permissions-do-i-need-to-register-assets-with-data-catalog"></a>Welke machtigingen heb ik nodig om assets te registreren bij Data Catalog?
Als u het hulp programma voor het registreren van Data Catalog wilt uitvoeren, hebt u machtigingen nodig voor de gegevens bron waarmee u de meta gegevens van de bron kunt lezen. Als u ook een preview wilt toevoegen, moet u machtigingen hebben waarmee u de gegevens van de geregistreerde objecten kunt lezen.

Data Catalog kunnen Catalog-beheerders ook beperken welke gebruikers en groepen meta gegevens kunnen toevoegen aan de catalogus. Zie [toegang tot Data Catalog en](data-catalog-how-to-secure-catalog.md)gegevensassets beveiligen voor meer informatie.

## <a name="will-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>Worden er ook Data Catalog beschikbaar gemaakt voor on-premises implementaties?
Data Catalog is een Cloud service die kan samen werken met zowel Cloud-als on-premises gegevens bronnen om een hybride oplossing voor gegevens bron detectie te leveren. Er zijn momenteel geen plannen voor een versie van de Data Catalog-service die on-premises wordt uitgevoerd.

## <a name="can-i-extract-more-or-richer-metadata-from-the-data-sources-i-register"></a>Kan ik meer of rijkere meta gegevens ophalen uit de gegevens bronnen die ik registreer?
We werken actief om de mogelijkheden van Data Catalog uit te breiden. Als u extra meta gegevens wilt ophalen uit de gegevens bron tijdens de registratie, kunt u deze Voorst Ellen (of stem ermee in als deze al is voorgesteld) in het [Data Catalog op de Azure feedback forums](https://feedback.azure.com/forums/906052-data-catalog). 

Als u meta gegevens van kolommen, voor beelden of gegevens profielen wilt toevoegen voor gegevens bronnen waarin deze meta gegevens niet worden geëxtraheerd door het hulp programma voor gegevens bron registratie, kunt u de Data Catalog-API gebruiken om deze meta gegevens toe te voegen. Zie [Azure Data Catalog rest API](/rest/api/datacatalog/)voor meer informatie.

## <a name="how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>Hoe kan ik de zicht baarheid van geregistreerde gegevensassets beperken, zodat alleen bepaalde personen deze kunnen ontdekken?
Selecteer de gegevensassets in de Data Catalog en klik vervolgens op de knop **eigenaar worden** . Eigen aren van gegevensassets in Data Catalog kunnen de zichtbaarheids instellingen wijzigen zodat alle gebruikers de eigendoms activa kunnen detecteren of de zicht baarheid kunnen beperken tot specifieke gebruikers. Zie gegevensassets [beheren in azure Data Catalog](data-catalog-how-to-manage.md)voor meer informatie.

## <a name="how-do-i-update-the-registration-for-a-data-asset-so-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>Hoe kan ik de registratie voor een gegevensasset bijwerken zodat wijzigingen in de gegevens bron worden weer gegeven in de catalogus?
Als u de meta gegevens wilt bijwerken voor gegevensassets die al in de catalogus zijn geregistreerd, registreert u de gegevens bron die de assets bevat opnieuw. Wijzigingen in de gegevens bron, zoals kolommen die worden toegevoegd of verwijderd uit tabellen of weer gaven, worden bijgewerkt in de catalogus, maar alle aantekeningen die door gebruikers worden aangeboden, blijven behouden.

## <a name="my-question-isnt-answered-here-where-can-i-go-for-answers"></a>Mijn vraag wordt hier niet beantwoord. Waar kan ik terecht voor antwoorden?
Ga naar het [Azure Data Catalog forum](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). Vragen die u hier kunt vinden, vindt u hier.