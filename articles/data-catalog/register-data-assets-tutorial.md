---
title: 'Zelfstudie: Gegevensassets registreren in Azure Data Catalog'
description: In deze zelfstudie wordt beschreven hoe u gegevensassets registreert in uw Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: tutorial
ms.date: 08/01/2019
ms.openlocfilehash: de85cad8232d76706358c36806ce19a7c1df893f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104675002"
---
# <a name="tutorial-register-data-assets-in-azure-data-catalog"></a>Zelfstudie: Gegevensassets registreren in Azure Data Catalog

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

In deze zelfstudie gebruikt u het hulpprogramma voor registratie om gegevensassets uit de databasesample te registreren bij de catalogus. Registratie is het proces waarbij belangrijke structurele metagegevens, zoals namen, typen en locaties, worden geëxtraheerd uit de gegevensbron en bijbehorende assets, en worden gekopieerd naar de catalogus. De gegevensbronnen en gegevensassets blijven waar ze zijn, maar de metagegevens worden door de catalogus gebruikt om de gegevensbronnen en gegevensassets eenvoudiger te detecteren en te begrijpen.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Gegevensassets registreren 
> * Gegevensassets zoeken
> * Aantekeningen toevoegen aan gegevensassets
> * Verbinding maken met gegevensassets
> * Gegevensassets beheren
> * Gegevensassets verwijderen

## <a name="prerequisites"></a>Vereisten

U moet de [quickstart](register-data-assets-tutorial.md) voltooien om aan de slag te gaan.

* Een [Microsoft Azure](https://azure.microsoft.com/)-abonnement.
* U moet uw eigen [Azure Active Directory-tenant](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) hebben.

Voor het instellen van Data Catalog moet u eigenaar of mede-eigenaar van een Azure-abonnement zijn.

## <a name="register-data-assets"></a>Gegevensassets registreren

### <a name="register-a-data-source"></a>Een gegevensbron registreren

U registreert gegevensassets (tabellen) uit een [databasesample](../azure-sql/database/single-database-create-quickstart.md) voor Azure SQL Database, maar u kunt elke ondersteunde gegevensbron gebruiken als u liever werkt met gegevens die vertrouwd en relevant zijn voor uw rol. Zie voor een lijst met ondersteunde gegevensbronnen [Ondersteunde gegevensbronnen](data-catalog-dsr.md).

De naam van de database die in deze zelfstudie wordt gebruikt, is *RLSTest*.

U kunt nu gegevensassets uit de databasesample registreren met behulp van Azure Data Catalog.

1. Ga naar de [startpagina van Azure Data Catalog](http://azuredatacatalog.com) en selecteer **Gegevens publiceren**.

   ![Azure Data Catalog - de knop Gegevens publiceren](media/register-data-assets-tutorial/data-catalog-publish-data.png)

2. Selecteer **Toepassing starten** om het hulpprogramma voor registratie te downloaden, te installeren en uit te voeren op uw computer.

   ![Azure Data Catalog - de knop Starten](media/register-data-assets-tutorial/data-catalog-launch-application.png)

3. Selecteer op de **Welkomstpagina** de optie **Aanmelden** en voer uw referenties in.

    ![Azure Data Catalog - Welkomstpagina](media/register-data-assets-tutorial/data-catalog-welcome-dialog.png)

4. Selecteer op de pagina **Microsoft Azure Data Catalog** de optie **SQL Server** en **Volgende**.

    ![Azure Data Catalog - gegevensbronnen](media/register-data-assets-tutorial/data-catalog-data-sources.png)

5. Voer de SQL Server-verbindingseigenschappen voor uw databasesample in en selecteer **VERBINDING MAKEN**.

   ![Azure Data Catalog - SQL Server-verbindingsinstellingen](media/register-data-assets-tutorial/data-catalog-sql-server-connection.png)

6. Registreer de metagegevens van uw gegevensasset. In dit voorbeeld registreert u **Product**-objecten van de naamruimte van de databasesample:

   1. Vouw in de structuur **Serverhiërarchie** uw databasesample uit en selecteer **SalesLT**.

   2. Selecteer **Product**, **ProductCategory**, **ProductDescription** en **ProductModel** door Ctrl+selecteren te gebruiken.

   3. Selecteer de **pijl voor het verplaatsen van geselecteerde items** ( **>** ). Met deze actie worden alle geselecteerde objecten verplaatst naar de lijst **Te registreren objecten**.

      ![Zelfstudie voor Azure Data Catalog - bladeren door objecten en objecten selecteren](media/register-data-assets-tutorial/data-catalog-server-hierarchy.png)

   4. Selecteer **Preview opnemen** om een snapshotvoorbeeld van de gegevens op te nemen. De momentopname bevat maximaal 20 records uit elke tabel en wordt gekopieerd in de catalogus.

   5. Selecteer **Gegevensprofiel opnemen** om een snapshot van de objectstatistieken op te nemen voor het gegevensprofiel (bijvoorbeeld: minimale, maximale en gemiddelde waarden voor een kolom, het aantal rijen).

   6. In het veld **Tags toevoegen** voert u **sales, product, azure sql** in. Met deze actie worden zoektags voor deze gegevensassets toegevoegd. Tags zijn voor gebruikers een uitstekende manier om een geregistreerde gegevensbron te zoeken.

   7. Geef de naam op van een **expert** met betrekking tot deze gegevens (optioneel).

      ![Zelfstudie voor Azure Data Catalog - te registreren objecten](media/register-data-assets-tutorial/data-catalog-objects-register.png)

   8. Selecteer **REGISTREREN**. Azure Data Catalog registreert uw geselecteerde objecten. In deze oefening worden de geselecteerde objecten van uw databasesample geregistreerd. Het hulpprogramma voor registratie extraheert metagegevens uit de gegevensasset en kopieert deze gegevens naar de Azure Data Catalog-catalogus. De gegevens blijven waar deze zich op dat moment bevinden. De gegevens blijven in beheer bij de beheerders en blijven vallen onder de beleidsregels van het oorspronkelijke systeem.

      ![Azure Data Catalog - geregistreerde objecten](media/register-data-assets-tutorial/data-catalog-registered-objects.png)

    9. Als u uw geregistreerde gegevensbronobjecten wilt zien, selecteert u **Portal weergeven**. Controleer in de Azure Data Catalog-portal of u alle vier de tabellen en de database in de rasterweergave ziet (controleer of de zoekbalk leeg is).

       ![Objecten in de Azure Data Catalog-portal](media/register-data-assets-tutorial/data-catalog-view-portal.png)

In deze oefening hebt u objecten uit de databasesample voor Azure SQL Database geregistreerd, zodat deze eenvoudig kunnen worden gevonden door gebruikers in uw organisatie.

In de volgende oefening leert u hoe u geregistreerde gegevensassets kunt detecteren.

## <a name="discover-data-assets"></a>Gegevensassets detecteren

Voor detectie in Azure Data Catalog worden twee primaire mechanismen gebruikt: zoeken en filteren.

Zoeken is zowel intuïtief als krachtig. Standaard worden de zoektermen vergeleken met een eigenschap in de catalogus, met inbegrip van door de gebruiker gemaakte aantekeningen.

Filteren is bedoeld als aanvulling op het zoeken. U kunt specifieke kenmerken, zoals experts, gegevensbrontype, objecttype en tags, selecteren om overeenkomende gegevensassets weer te geven en om uw zoekresultaten te beperken tot overeenkomende assets.

Met behulp van een combinatie van zoeken en filteren kunt u snel navigeren door de gegevensbronnen die bij Azure Data Catalog zijn geregistreerd.

In deze oefening gebruikt u de Azure Data Catalog-portal voor het detecteren van de gegevensassets die u in de vorige stap hebt geregistreerd. Zie [Verwijzing naar de zoeksyntaxis van Data Catalog](/rest/api/datacatalog/#search-syntax-reference) voor meer informatie over de zoeksyntaxis.

Hieronder volgen enkele voorbeelden van het detecteren van gegevensassets in de catalogus.  

### <a name="discover-data-assets-with-basic-search"></a>Gegevensassets met een basiszoekopdracht detecteren

Met een basiszoekopdracht kunt u in een catalogus zoeken met behulp van een of meer zoektermen. De resultaten die worden opgehaald, zijn assets die wat betreft een willekeurige eigenschap overeenkomen met een of meer van de opgegeven voorwaarden.

1. Selecteer **Startpagina** in de Azure Data Catalog-portal. Als u de webbrowser hebt gesloten, gaat u naar de [startpagina van Azure Data Catalog](https://www.azuredatacatalog.com).

2. Typ `product` in het zoekvak en druk op **ENTER**.

   ![Azure Data Catalog - basiszoekopdracht naar tekst](media/register-data-assets-tutorial/data-catalog-basic-text-search.png)

3. Controleer of u alle vier de tabellen en de database in de resultaten ziet. U kunt schakelen tussen **rasterweergave** en **lijstweergave** door knoppen op de werkbalk te selecteren, zoals weergegeven in de volgende afbeelding. U ziet dat het zoekwoord wordt gemarkeerd in de zoekresultaten. Dit gebeurt omdat de optie **Markeren** is **ingeschakeld**. U kunt ook het aantal **resultaten per pagina** in zoekresultaten opgeven.

   ![Azure Data Catalog - resultaten van basiszoekopdracht naar tekst](media/register-data-assets-tutorial/data-catalog-basic-text-search-results.png)

   Het deelvenster **Zoekopdrachten** bevindt zich aan de linkerkant. Het deelvenster **Eigenschappen** bevindt zich aan de rechterkant. In het deelvenster **Zoekopdrachten** kunt u zoekcriteria wijzigen en resultaten filteren. In het deelvenster **Eigenschappen** worden de eigenschappen van een geselecteerd object weergegeven in het raster of de lijst.

4. Selecteer **Product** in de zoekresultaten. Selecteer de tabbladen **Preview**, **Kolommen**, **Gegevensprofiel** en **Documentatie** of selecteer de pijl om het onderste deelvenster uit te vouwen.  

   ![Azure Data Catalog - onderste deelvenster](media/register-data-assets-tutorial/data-catalog-data-asset-preview.png)

   Op het tabblad **Preview** ziet u een voorbeeld van de gegevens in de tabel **Product**.

5. Selecteer het tabblad **Kolommen** voor meer informatie over kolommen (zoals **naam** en **gegevenstype**) in de gegevensasset.

6. Selecteer het tabblad **Gegevensprofiel** om de profielsamenstelling van gegevens te bekijken (bijvoorbeeld het aantal rijen, de grootte van gegevens of de minimumwaarde in een kolom) in de gegevensasset.

### <a name="discover-data-assets-with-property-scoping"></a>Gegevens detecteren door het bereik van eigenschappen te definiëren

Door het bereik van eigenschappen te definiëren, kunt u gegevensassets detecteren waarbij de zoekterm overeenkomt met de opgegeven eigenschap.

1. Schakel het filter **Tabel** onder **Objecttype** in **Filters** uit.  

2. Typ `tags:product` in het zoekvak en druk op **ENTER**. Zie [Verwijzing naar de zoeksyntaxis van Data Catalog](/rest/api/datacatalog/#search-syntax-reference) voor alle eigenschappen die u kunt gebruiken voor het zoeken in de gegevenscatalogus.

3. Bevestig dat u alle tabellen en de database in de resultaten ziet.  

   ![Data Catalog - resultaten van het definiëren van het bereik van eigenschappen](media/register-data-assets-tutorial/data-catalog-property-scoping-results.png)

### <a name="save-the-search"></a>De zoekopdracht opslaan

1. Voer in het deelvenster **Zoekopdrachten** in de sectie **Huidige zoekopdracht** een naam voor de zoekopdracht in en selecteer **Opslaan**.

   ![Azure Data Catalog - zoekopdracht opslaan](media/register-data-assets-tutorial/data-catalog-save-search.png)

2. Bevestig dat de opgeslagen zoekopdracht wordt weergegeven onder **Opgeslagen zoekopdrachten**.

3. Selecteer een van de acties die kunnen worden uitgevoerd op de opgeslagen zoekopdracht (**Naam wijzigen**, **Verwijderen**, **Opslaan als standaard** (zoekopdracht)).

### <a name="grouping-with-parentheses"></a>Groeperen met haakjes

Door te groeperen met behulp van haakjes, kunt u delen van de query groeperen voor logische isolatie, met name in combinatie met Booleaanse operators.

1. Typ `name:product AND (tags:product AND objectType:table)` in het zoekvak en druk op **ENTER**.

2. Bevestig dat u alleen de tabel **Product** in de zoekresultaten ziet.

   ![Azure Data Catalog - zoekopdracht groeperen](media/register-data-assets-tutorial/data-catalog-grouping-search.png)

### <a name="comparison-operators"></a>Vergelijkingsoperators

Met vergelijkingsoperators kunt u andere vergelijkingen dan gelijkheid gebruiken voor eigenschappen die de gegevenstypen numeriek en datum hebben.

1. Typ `lastRegisteredTime:>"06/09/2016"` in het zoekvak.

2. Schakel het filter **Tabel** onder **Objecttype** uit.

3. Druk op **ENTER**.

4. Bevestig dat u in de zoekresultaten de tabellen **Product**, **ProductCategory** en **ProductDescription** en de geregistreerde SQL-database ziet.

   ![Azure Data Catalog - vergelijking van zoekresultaten](media/register-data-assets-tutorial/data-catalog-comparison-operator-results.png)

Zie [Gegevensassets detecteren](data-catalog-how-to-discover.md) voor gedetailleerde informatie over het detecteren van gegevensassets. Zie [Verwijzing naar de zoeksyntaxis van Data Catalog](/rest/api/datacatalog/#search-syntax-reference) voor meer informatie over de zoeksyntaxis.

## <a name="annotate-data-assets"></a>Aantekeningen toevoegen aan gegevensassets

In deze oefening gebruikt u de portal van Azure Data Catalog om aantekeningen te maken (informatie zoals beschrijvingen, tags of experts toe te voegen) bij bestaande gegevensassets in de catalogus. De aantekeningen vormen een aanvulling op de structurele metagegevens die tijdens de registratie zijn geëxtraheerd uit de gegevensbron. Met aantekeningen zijn gegevensassets veel eenvoudiger te detecteren en te begrijpen.

In deze oefening maakt u aantekeningen voor een enkele gegevensasset (ProductPhoto). U voegt een beschrijvende naam en een beschrijving toe aan de gegevensasset ProductPhoto.  

1. Ga naar de [startpagina van Azure Data Catalog](https://www.azuredatacatalog.com) en zoek met `tags:product` naar de gegevensassets die u hebt geregistreerd.

2. Selecteer **ProductModel** in de zoekresultaten.  

3. Voer **Productafbeeldingen** in voor **Beschrijvende naam** en **Productfoto’s voor marketingmateriaal** voor de **Beschrijving**.

    ![Azure Data Catalog - beschrijving van ProductPhoto](media/register-data-assets-tutorial/data-catalog-productmodel-description.png)

    De **Beschrijving** helpt anderen om de geselecteerde gegevensasset te detecteren en te begrijpen waarom en hoe ze deze kunnen gebruiken. U kunt ook meer tags toevoegen en kolommen weergeven. U kunt gegevensbronnen zoeken en filteren door gebruik te maken van de beschrijvende metagegevens die u aan de catalogus hebt toegevoegd.

U kunt ook de volgende stappen op deze pagina uitvoeren:

* Experts toevoegen voor de gegevensasset. Selecteer **Toevoegen** in het gebied **Experts**.

* Tags toevoegen op het niveau van de gegevensset. Selecteer **Toevoegen** in het gebied **Tags**. Een label kan een gebruikerstag of een woordenlijsttag zijn. De Standard-editie van Data Catalog bevat een zakelijke woordenlijst waarmee catalogusbeheerders een centrale zakelijke taxonomie kunnen definiëren. Catalogusgebruikers kunnen vervolgens aantekeningen toevoegen aan gegevensassets met behulp van termen in de woordenlijst. Zie voor meer informatie [De zakelijke woordenlijst instellen voor Governed Tagging](data-catalog-how-to-business-glossary.md)

* Tags toevoegen op kolomniveau. Selecteer **Toevoegen** onder **Tags** voor de kolom waaraan u aantekeningen wilt toevoegen.

* Beschrijving toevoegen op kolomniveau. Voer een **Beschrijving** in voor een kolom. U kunt ook de metagegevens van de beschrijving bekijken die uit de gegevensbron zijn geëxtraheerd.

* Informatie over **Verzoeken tot toegang** toevoegen die gebruikers laat zien hoe ze toegang kunnen krijgen tot de gegevensasset.
  
* Ga naar het tabblad **Documentatie** en verstrek hier documentatie over de gegevensasset. Met Azure Data Catalog -documentatie kunt u de gegevenscatalogus gebruiken als een opslagplaats voor inhoud om een volledig overzicht te maken van uw gegevensassets.
  
U kunt ook een aantekening toevoegen aan meerdere gegevensassets. U kunt bijvoorbeeld alle geregistreerde gegevensassets selecteren en er een expert voor opgeven.

![Azure Data Catalog - aantekeningen toevoegen aan meerdere gegevensassets](media/register-data-assets-tutorial/data-catalog-multi-select-annotate.png)

Azure Data Catalog ondersteunt een benadering voor aantekeningen via crowdsourcing. Elke Data Catalog-gebruiker kan gebruikers- of woordenlijsttags, beschrijvingen en andere metagegevens toevoegen. Hiermee voegen gebruikers perspectief toe aan een gegevensasset en het gebruik ervan en delen ze dat perspectief met andere gebruikers.

Zie [Aantekeningen toevoegen aan gegevensbronnen](data-catalog-how-to-annotate.md) voor gedetailleerde informatie over het toevoegen van aantekeningen aan gegevens.

## <a name="connect-to-data-assets"></a>Verbinding maken met gegevensassets

In deze oefening opent u gegevensassets in geïntegreerde clienthulpprogramma's en een niet-geïntegreerd hulpprogramma (SQL Server Management Studio) met behulp van verbindingsgegevens.

> [!NOTE]
> Het is belangrijk om te weten dat Azure Data Catalog u geen toegang geeft tot de werkelijke gegevensbron. Het wordt gewoon eenvoudiger voor u om ze te detecteren en te begrijpen. Wanneer u verbinding maakt met een gegevensbron, worden uw Windows-referenties gebruikt of wordt u indien nodig om referenties gevraagd. Als u niet eerder toegang hebt gekregen tot de gegevensbron, moet aan u toegang worden verleend voordat u verbinding kunt maken.

### <a name="connect-to-a-data-asset-from-excel"></a>Verbinding maken met een gegevensasset vanuit Excel

1. Selecteer in de zoekresultaten de optie **Product**. Selecteer **Openen in** op de werkbalk en selecteer **Excel**.

   ![Azure Data Catalog - verbinding maken met gegevensasset](media/register-data-assets-tutorial/data-catalog-connect1.png)

2. Selecteer **Openen** in het downloadpop-upvenster. Deze ervaring kan variëren afhankelijk van de browser.

3. Selecteer **Inschakelen** in het venster **Beveiligingsbericht Microsoft Excel**.

   ![Azure Data Catalog - beveiligingspop-upvenster van Excel](media/register-data-assets-tutorial/data-catalog-excel-security-popup.png)

4. Behoud de standaardinstellingen in het dialoogvenster **Gegevens importeren** en selecteer **OK**.

   ![Azure Data Catalog - Excel-gegevens importeren](media/register-data-assets-tutorial/data-catalog-excel-import-data.png)

5. Bekijk de gegevensbron in Excel.

   ![Azure Data Catalog - producttabel in Excel](media/register-data-assets-tutorial/data-catalog-connect2.png)

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

In deze oefening hebt u verbinding gemaakt met gegevensasssets die zijn gedetecteerd via Azure Data Catalog. Via de portal van Azure Data Catalog kunt u direct verbinding maken via de clienttoepassingen die zijn geïntegreerd in het menu **Openen in**. U kunt ook verbinding maken met behulp van een andere toepassing naar keuze. Hierbij wordt gebruikgemaakt van de locatiegegevens van de verbinding in de metagegevens van de asset. U kunt bijvoorbeeld SQL Server Management Studio gebruiken om verbinding te maken met Azure SQL Database om toegang te krijgen tot de gegevens in de gegevensassets die in deze zelfstudie zijn geregistreerd.

1. Open **SQL Server Management Studio**.

2. Voer in het dialoogvenster **Verbinding maken met server** de naam in van de server uit het deelvenster **Eigenschappen** in de Azure Data Catalog-portal.

3. Gebruik de juiste verificatieprocedure en aanmeldingsgegevens voor toegang tot de gegevensasset. Gebruik de informatie in het veld **Verzoeken tot toegang** als u geen toegang hebt.

   ![Azure Data Catalog - verzoeken tot toegang](media/register-data-assets-tutorial/data-catalog-request-access.png)

Selecteer **Verbindingsreeksen weergeven** om ADO.NET-, ODBC- en OLEDB-verbindingsreeksen te bekijken en te kopiëren naar het klembord voor gebruik in uw toepassing.

## <a name="manage-data-assets"></a>Gegevensassets beheren

In deze stap ziet u hoe u beveiliging kunt instellen voor uw gegevensassets. Data Catalog geeft gebruikers geen toegang tot de gegevens zelf. De eigenaar van de gegevensbron bepaalt de toegang tot gegevens.

Met Data Catalog kunt u gegevensbronnen detecteren en de metagegevens bekijken die gerelateerd zijn aan de bronnen die in de catalogus zijn geregistreerd. Mogelijk zijn er echter situaties echter waarin gegevensbronnen alleen zichtbaar moeten zijn voor specifieke gebruikers of specifieke groepen. Voor deze scenario's kunt u via Data Catalog eigenaar worden van geregistreerde gegevensassets en zelf de zichtbaarheid van uw assets bepalen.

> [!NOTE]
> De beheermogelijkheden die in deze taak worden beschreven, zijn alleen beschikbaar in de Standard-editie van Azure Data Catalog, niet in de gratis editie.
> In Azure Data Catalog kunt u eigenaar worden van gegevensassets, mede-eigenaren aan gegevensassets toevoegen en de zichtbaarheid van gegevensassets instellen.

### <a name="take-ownership-of-data-assets-and-restrict-visibility"></a>Eigenaar worden van gegevensassets en de zichtbaarheid beperken

1. Ga naar de [startpagina van Azure Data Catalog](https://www.azuredatacatalog.com). Typ `tags:cycles` in het tekstvak **Zoeken** en druk op **ENTER**.

2. Selecteer een item in de lijst met resultaten en selecteer **Eigenaar worden** in de werkbalk.

3. Selecteer in de sectie **Beheer** van het deelvenster **Eigenschappen** de optie **Eigenaar worden**.

    ![Azure Data Catalog - eigenaar worden](media/register-data-assets-tutorial/data-catalog-take-ownership.png)

4. Als u de zichtbaarheid wilt beperken, kiest u **Eigenaars en deze gebruikers** in de sectie **Zichtbaarheid** en selecteert u **Toevoegen**. Voer in het tekstvak de e-mailadressen van de betreffende gebruikers in en druk op **ENTER**.

    ![Azure Data Catalog - toegang beperken](media/register-data-assets-tutorial/data-catalog-ownership.png)

## <a name="remove-data-assets"></a>Gegevensassets verwijderen

In deze oefening gebruikt u de portal van Azure Data Catalog om voorbeeldgegevens te verwijderen uit geregistreerde gegevensassets en om gegevensassets te verwijderen uit de catalogus.

In Azure Data Catalog kunt u een individuele asset of meerdere assets verwijderen.

1. Ga naar de [startpagina van Azure Data Catalog](https://www.azuredatacatalog.com).

2. Typ `tags:cycles` in het tekstvak **Zoeken** en selecteer **ENTER**.

3. Selecteer een item in de lijst met resultaten en selecteer op de werkbalk **Verwijderen**, zoals weergegeven in de volgende afbeelding:

   ![Azure Data Catalog - rasteritem verwijderen](media/register-data-assets-tutorial/data-catalog-delete-grid-item.png)

   Als u de lijstweergave gebruikt, wordt het selectievakje aan de linkerkant van het item weergegeven, zoals weergegeven in de volgende afbeelding:

   ![Azure Data Catalog - lijstitem verwijderen](media/register-data-assets-tutorial/data-catalog-delete-list-item.png)

   U kunt ook meerdere gegevensassets selecteren en deze verwijderen zoals op de volgende afbeelding wordt aangegeven:

   ![Azure Data Catalog - meerdere gegevensassets verwijderen](media/register-data-assets-tutorial/data-catalog-delete-assets.png)

> [!NOTE]
> Het standaardgedrag van de catalogus is om elke gebruiker toe te staan een gegevensbron te registreren en om elke gebruiker toe te staan een geregistreerde gegevensasset te verwijderen. De beheermogelijkheden die in de Standard-editie van Azure Data Catalog zijn opgenomen, bieden aanvullende opties om eigenaar te worden van assets, te beperken wie er assets kunnen detecteren en te beperken wie er assets kunnen verwijderen.

## <a name="clean-up-resources"></a>Resources opschonen

Volg de stappen voor [Gegevensassets verwijderen](#remove-data-assets) om eventuele assets op te schonen die u hebt gebruikt tijdens het volgen van deze zelfstudie.

## <a name="summary"></a>Samenvatting

In deze zelfstudie hebt u essentiële mogelijkheden van Azure Data Catalog verkend, inclusief het registreren, annoteren, detecteren en beheren van zakelijke gegevensassets. Nu u de zelfstudie hebt voltooid, is het tijd om aan de slag te gaan. U kunt vandaag nog beginnen de gegevensbronnen waarop u en uw team vertrouwen te registreren en collega's uit te nodigen om de catalogus te gebruiken.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Ondersteunde gegevensbronnen](data-catalog-dsr.md)
