---
title: Concepten van Azure Data Catalog-ontwikkel aars
description: Inleiding tot de belangrijkste concepten in Azure Data Catalog conceptuele model, zoals wordt weer gegeven via de catalogus REST API.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: ddeab4838feb07d1101993cab4ebc86581b4d8b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674696"
---
# <a name="azure-data-catalog-developer-concepts"></a>Concepten van Azure Data Catalog-ontwikkel aars

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Micro soft **Azure Data Catalog** is een volledig beheerde Cloud service die mogelijkheden biedt voor het detecteren van gegevens bronnen en voor crowdsourcing van meta gegevens. Ontwikkel aars kunnen de service gebruiken via de REST Api's. Meer informatie over de concepten die in de service zijn geïmplementeerd, is belang rijk voor ontwikkel aars om te integreren met **Azure Data Catalog**.

## <a name="key-concepts"></a>Belangrijkste concepten 
Het conceptuele model van **Azure Data Catalog** is gebaseerd op vier belang rijke concepten: de **catalogus**, **gebruikers**, **assets** en **aantekeningen**.

![Illustratie van Azure Data Catalog concept model](./media/data-catalog-developer-concepts/concept2.png)

### <a name="catalog"></a>Catalogus
Een **catalogus** is de container op het hoogste niveau voor alle meta gegevens die door een organisatie worden opgeslagen. Er is één **catalogus** per Azure-account toegestaan. Catalogi zijn gekoppeld aan een Azure-abonnement, maar er kan slechts één **catalogus** worden gemaakt voor een bepaald Azure-account, zelfs als een account meerdere abonnementen kan hebben.

Een catalogus bevat **gebruikers** en **activa**.

### <a name="users"></a>Gebruikers
Gebruikers zijn beveiligings-principals die machtigingen hebben om acties uit te voeren (Zoek in de catalogus, items toe te voegen, te bewerken of te verwijderen, enzovoort) in de catalogus.

Er zijn verschillende rollen die een gebruiker kan hebben. Zie de sectie rollen en autorisatie voor meer informatie over rollen.

Afzonderlijke gebruikers en beveiligings groepen kunnen worden toegevoegd.

Azure Data Catalog gebruikt Azure Active Directory voor identiteits-en toegangs beheer. Elke catalogus gebruiker moet lid zijn van de Active Directory voor het account.

### <a name="assets"></a>Assets
Een **catalogus** bevat gegevensassets. **Assets** zijn de granulatie-eenheid die wordt beheerd door de catalogus.

De granulatie van een Asset verschilt per gegevens bron. Voor SQL Server of Oracle Database kan een activum een tabel of een weer gave zijn. Voor SQL Server Analysis Services kan een activum een meting, een dimensie of een Key Performance indicator (KPI) zijn. Voor SQL Server Reporting Services is een activum een rapport.

Een **activum** is het onderwerp dat u toevoegt aan of verwijdert uit een catalogus. Het is het resultaat van de **Zoek opdracht**.

Er wordt een **activum** gemaakt op basis van de naam, locatie en het type en annotaties die de toepassing verder beschrijven.

### <a name="annotations"></a>Aantekeningen
Aantekeningen zijn items die meta gegevens over assets vertegenwoordigen.

Voor beelden van aantekeningen zijn beschrijving, tags, schema, Documentatie, enzovoort. Zie de [sectie Asset object model](#asset-object-model) voor een volledige lijst van de typen activa en aantekening.

## <a name="crowdsourcing-annotations-and-user-perspective-multiplicity-of-opinion"></a>Crowdsourcing annotaties and User perspective (multipliciteit van het advies)
Een belang rijk aspect van Azure Data Catalog is het ondersteunen van de crowdsourcing van meta gegevens in het systeem. In tegens telling tot een wiki-benadering: wanneer er slechts één mening is en de laatste schrijver wint: het Azure Data Catalog model biedt meerdere adviezen voor Live naast elkaar in het systeem.

Deze benadering weerspiegelt de echte wereld van Bedrijfs gegevens, waarbij verschillende gebruikers verschillende perspectieven kunnen hebben voor een bepaalde Asset:

* Een database beheerder kan informatie geven over service overeenkomsten of het venster beschik bare verwerking voor het bulksgewijs uitvoeren van bewerkingen
* Een data steward kan informatie geven over de bedrijfs processen waarop het activum van toepassing is, of de classificaties die het bedrijf heeft toegepast
* Een financiële analist kan informatie geven over de manier waarop de gegevens worden gebruikt tijdens het einde van de periode voor rapportage taken

Ter ondersteuning van dit voor beeld kan elke gebruiker – de DBA, het data steward en de analist, een beschrijving toevoegen aan één tabel die is geregistreerd in de catalogus. Alle beschrijvingen worden bijgehouden in het systeem en in de Azure Data Catalog Portal worden alle beschrijvingen weer gegeven.

Dit patroon wordt toegepast op het meren deel van de items in het object model, dus object typen in de JSON-nettolading zijn vaak matrices voor eigenschappen waarvoor u een singleton kunt verwachten.

Onder de hoofd directory van assets bevindt zich bijvoorbeeld een matrix met beschrijvings objecten. De eigenschap matrix krijgt de naam "beschrijvingen". Een Description-object heeft één eigenschap-Description. Het patroon is dat elke gebruiker die de beschrijving typt, een beschrijvings object krijgt dat is gemaakt voor de waarde die de gebruiker heeft opgegeven.

De UX kan vervolgens kiezen hoe u de combi natie weergeeft. Er zijn drie verschillende patronen voor weer gave.

* Het eenvoudigste patroon is alles weer geven. In dit patroon worden alle objecten weer gegeven in een lijst weergave. Het Azure Data Catalog Portal UX gebruikt dit patroon voor de beschrijving.
* Een ander patroon is ' samen voegen '. In dit patroon worden alle waarden van de verschillende gebruikers samengevoegd samen met dubbele items verwijderd. Voor beelden van dit patroon in de Azure Data Catalog Portal UX zijn de eigenschappen Tags en experts.
* Een derde patroon is ' laatste schrijver '. In dit patroon wordt alleen de laatst getypte waarde weer gegeven. FriendlyName is een voor beeld van dit patroon.

## <a name="asset-object-model"></a>Asset-object model
Zoals geïntroduceerd in de sectie hoofd concepten bevat het **Azure Data Catalog** object model items, die assets of aantekeningen kunnen zijn. Items hebben eigenschappen, die optioneel of vereist kunnen zijn. Sommige eigenschappen zijn van toepassing op alle items. Sommige eigenschappen zijn van toepassing op alle assets. Sommige eigenschappen zijn alleen van toepassing op specifieke activa typen.

### <a name="system-properties"></a>Systeemeigenschappen
<table><tr><td><b>Eigenschapsnaam</b></td><td><b>Gegevens type</b></td><td><b>Opmerkingen</b></td></tr><tr><td>tijdstempel</td><td>DateTime</td><td>De laatste keer dat het item is gewijzigd. Dit veld wordt gegenereerd door de server wanneer een item wordt ingevoegd en telkens wanneer een item wordt bijgewerkt. De waarde van deze eigenschap wordt genegeerd bij het invoeren van publicatie bewerkingen.</td></tr><tr><td>Id</td><td>URI</td><td>De absolute URL van het item (alleen-lezen). Het is de unieke adresseer bare URI voor het item.  De waarde van deze eigenschap wordt genegeerd bij het invoeren van publicatie bewerkingen.</td></tr><tr><td>type</td><td>Tekenreeks</td><td>Het type van het activum (alleen-lezen).</td></tr><tr><td>ETAG</td><td>Tekenreeks</td><td>Een teken reeks die overeenkomt met de versie van het item dat kan worden gebruikt voor optimistisch gelijktijdigheids beheer bij het uitvoeren van bewerkingen waarmee items in de catalogus worden bijgewerkt. ' * ' kan worden gebruikt om een wille keurige waarde te vinden.</td></tr></table>

### <a name="common-properties"></a>Algemene eigenschappen
Deze eigenschappen zijn van toepassing op alle hoofd activa typen en alle aantekening typen.

<table>
<tr><td><b>Eigenschapsnaam</b></td><td><b>Gegevens type</b></td><td><b>Opmerkingen</b></td></tr>
<tr><td>fromSourceSystem</td><td>Booleaans</td><td>Geeft aan of de gegevens van het item worden afgeleid van een bron systeem (zoals SQL Server Data Base, Oracle Database) of door een gebruiker zijn geschreven.</td></tr>
</table>

### <a name="common-root-properties"></a>Algemene hoofd eigenschappen
<p>
Deze eigenschappen zijn van toepassing op alle hoofd activa typen.

<table><tr><td><b>Eigenschapsnaam</b></td><td><b>Gegevens type</b></td><td><b>Opmerkingen</b></td></tr><tr><td>naam</td><td>Tekenreeks</td><td>Een naam die is afgeleid van de informatie van de gegevens bron locatie</td></tr><tr><td>ADSL</td><td>DataSourceLocation</td><td>Een unieke beschrijving van de gegevens bron en een van de id's voor de Asset. (Zie sectie met dubbele identiteit).  De structuur van de DSL is afhankelijk van het protocol en het bron type.</td></tr><tr><td>Bron</td><td>DataSourceInfo</td><td>Meer details over het type Asset.</td></tr><tr><td>lastRegisteredBy</td><td>SecurityPrincipal</td><td>Beschrijft de gebruiker die het laatst deze asset heeft geregistreerd.  Bevat zowel de unieke ID voor de gebruiker (de UPN) als een weergave naam (achternaam en voor naam).</td></tr><tr><td>containerID</td><td>Tekenreeks</td><td>ID van het container element voor de gegevens bron. Deze eigenschap wordt niet ondersteund voor het container type.</td></tr></table>

### <a name="common-non-singleton-annotation-properties"></a>Eigenschappen van algemene niet-Singleton-aantekening
Deze eigenschappen zijn van toepassing op alle aantekeningen typen die niet van een singleton zijn (annotaties, die meerdere per activa mogen zijn).

<table>
<tr><td><b>Eigenschapsnaam</b></td><td><b>Gegevens type</b></td><td><b>Opmerkingen</b></td></tr>
<tr><td>sleutel</td><td>Tekenreeks</td><td>Een door de gebruiker opgegeven sleutel waarmee de aantekening in de huidige verzameling uniek wordt geïdentificeerd. De sleutel lengte mag niet langer zijn dan 256 tekens.</td></tr>
</table>

### <a name="root-asset-types"></a>Hoofd activa typen
Hoofd activa typen zijn de typen die de verschillende typen gegevensassets vertegenwoordigen die kunnen worden geregistreerd in de catalogus. Voor elk hoofd type is er een weer gave, waarin de activa en aantekeningen worden beschreven die in de weer gave zijn opgenomen. De weergave naam moet worden gebruikt in het bijbehorende URL-segment van {view_name} wanneer u een Asset publiceert met behulp van REST API.

<table><tr><td><b>Type activum (weergave naam)</b></td><td><b>Aanvullende eigenschappen</b></td><td><b>Gegevens type</b></td><td><b>Toegestane aantekeningen</b></td><td><b>Opmerkingen</b></td></tr><tr><td>Tabel ("Tables")</td><td></td><td></td><td>Description<p>FriendlyName<p>Tag<p>Schema<p>ColumnDescription<p>ColumnTag<p> Expert<p>Preview<p>AccessInstruction<p>TableDataProfile<p>ColumnDataProfile<p>ColumnDataClassification<p>Documentatie<p></td><td>Een tabel vertegenwoordigt alle tabellaire gegevens.  Bijvoorbeeld: SQL-tabel, SQL-weer gave, Analysis Services Tabellaire tabel, Analysis Services multidimensionale dimensie, Oracle-tabel, enzovoort.   </td></tr><tr><td>Meting ("metingen")</td><td></td><td></td><td>Description<p>FriendlyName<p>Tag<p>Expert<p>AccessInstruction<p>Documentatie<p></td><td>Dit type vertegenwoordigt een Analysis Services meting.</td></tr><tr><td></td><td>maateenheidfilters</td><td>Kolom</td><td></td><td>Meta gegevens die de meting beschrijven</td></tr><tr><td></td><td>isCalculated </td><td>Booleaans</td><td></td><td>Geeft aan of de meting wordt berekend of niet.</td></tr><tr><td></td><td>measureGroup</td><td>Tekenreeks</td><td></td><td>Fysieke container voor meting</td></tr><td>KPI ("kpi's")</td><td></td><td></td><td>Description<p>FriendlyName<p>Tag<p>Expert<p>AccessInstruction<p>Documentatie</td><td></td></tr><tr><td></td><td>measureGroup</td><td>Tekenreeks</td><td></td><td>Fysieke container voor meting</td></tr><tr><td></td><td>goalExpression</td><td>Tekenreeks</td><td></td><td>Een MDX-numerieke expressie of een berekening die de doel waarde van de KPI retourneert.</td></tr><tr><td></td><td>valueExpression</td><td>Tekenreeks</td><td></td><td>Een MDX-numerieke expressie die de werkelijke waarde van de KPI retourneert.</td></tr><tr><td></td><td>statusExpression</td><td>Tekenreeks</td><td></td><td>Een MDX-expressie die de status van de KPI vertegenwoordigt op een opgegeven moment.</td></tr><tr><td></td><td>trendExpression</td><td>Tekenreeks</td><td></td><td>Een MDX-expressie die de waarde van de KPI in de loop van de tijd evalueert. De trend kan elk op tijd gebaseerd criterium zijn dat nuttig is in een specifieke bedrijfs context.</td>
<tr><td>Rapport ("rapporten")</td><td></td><td></td><td>Description<p>FriendlyName<p>Tag<p>Expert<p>AccessInstruction<p>Documentatie<p></td><td>Dit type vertegenwoordigt een SQL Server Reporting Services rapport </td></tr><tr><td></td><td>assetCreatedDate</td><td>Tekenreeks</td><td></td><td></td></tr><tr><td></td><td>assetCreatedBy</td><td>Tekenreeks</td><td></td><td></td></tr><tr><td></td><td>assetModifiedDate</td><td>Tekenreeks</td><td></td><td></td></tr><tr><td></td><td>assetModifiedBy</td><td>Tekenreeks</td><td></td><td></td></tr><tr><td>Container ("containers")</td><td></td><td></td><td>Description<p>FriendlyName<p>Tag<p>Expert<p>AccessInstruction<p>Documentatie<p></td><td>Dit type vertegenwoordigt een container met andere assets, zoals een SQL database, een Azure blobs-container of een Analysis Services model.</td></tr></table>

### <a name="annotation-types"></a>Aantekening typen
Aantekening typen vertegenwoordigen typen meta gegevens die kunnen worden toegewezen aan andere typen in de catalogus.

<table>
<tr><td><b>Aantekening type (geneste weergave naam)</b></td><td><b>Aanvullende eigenschappen</b></td><td><b>Gegevens type</b></td><td><b>Opmerkingen</b></td></tr>

<tr><td>Beschrijving ("beschrijvingen")</td><td></td><td></td><td>Deze eigenschap bevat een beschrijving voor een Asset. Elke gebruiker van het systeem kan een eigen beschrijving toevoegen.  Alleen die gebruiker kan het beschrijvings object bewerken.  (Beheerders en eigen aren van activa kunnen het beschrijvings object verwijderen, maar niet bewerken). Het systeem onderhoudt de beschrijvingen van gebruikers afzonderlijk.  Er is dus een matrix met beschrijvingen voor elk activum (één voor elke gebruiker die de kennis van de Asset heeft bijgedragen, naast mogelijk een met informatie die is afgeleid van de gegevens bron).</td></tr>
<tr><td></td><td>beschrijving</td><td>tekenreeks</td><td>Een korte beschrijving (2-3 regels) van het activum</td></tr>

<tr><td>Tag ("Tags")</td><td></td><td></td><td>Met deze eigenschap wordt een label voor een Asset gedefinieerd. Elke gebruiker van het systeem kan meerdere labels voor een Asset toevoegen.  Alleen de gebruiker die label objecten heeft gemaakt, kan deze bewerken.  (Beheerders en eigen aren van activa kunnen het tag-object verwijderen, maar niet bewerken). Het systeem onderhoudt de labels van gebruikers afzonderlijk.  Er is dus een matrix met label objecten op elke Asset.</td></tr>
<tr><td></td><td>tag</td><td>tekenreeks</td><td>Een tag waarmee de Asset wordt beschreven.</td></tr>

<tr><td>Beschrijvendenaam ("FriendlyName")</td><td></td><td></td><td>Deze eigenschap bevat een beschrijvende naam voor een Asset. FriendlyName is een singleton-annotatie: er kan slechts één FriendlyName worden toegevoegd aan een Asset.  Alleen de gebruiker die het FriendlyName-object heeft gemaakt, kan deze bewerken. (Beheerders en eigen aren van activa kunnen het FriendlyName-object verwijderen, maar niet bewerken). Het systeem onderhoudt de beschrijvende namen van gebruikers afzonderlijk.</td></tr>
<tr><td></td><td>friendlyName</td><td>tekenreeks</td><td>Een beschrijvende naam van de Asset.</td></tr>

<tr><td>Schema ("schema")</td><td></td><td></td><td>In het schema wordt de structuur van de gegevens beschreven.  Het kenmerk bevat de namen van de kenmerken (kolom, kenmerk, veld, enzovoort), typen als andere meta gegevens.  Deze informatie is allemaal afgeleid van de gegevens bron.  Schema is een singleton-annotatie: er kan slechts één schema worden toegevoegd voor een Asset.</td></tr>
<tr><td></td><td>kolommen</td><td>Kolom []</td><td>Een matrix van kolom objecten. Hierin wordt de kolom beschreven met informatie die is afgeleid van de gegevens bron.</td></tr>

<tr><td>ColumnDescription ("columnDescriptions")</td><td></td><td></td><td>Deze eigenschap bevat een beschrijving van een kolom.  Elke gebruiker van het systeem kan hun eigen beschrijvingen toevoegen voor meerdere kolommen (Maxi maal één kolom). Alleen de gebruiker die ColumnDescription-objecten heeft gemaakt, kan deze bewerken.  (Beheerders en eigen aren van activa kunnen het ColumnDescription-object verwijderen, maar niet bewerken). Het systeem behoudt de kolom beschrijvingen van deze gebruikers afzonderlijk.  Er is dus een matrix met ColumnDescription-objecten op elke asset (één per kolom voor elke gebruiker die kennis heeft gegeven met betrekking tot de kolom, naast mogelijk een die informatie bevat die is afgeleid van de gegevens bron).  De ColumnDescription is losjes gebonden aan het schema zodat het niet kan worden gesynchroniseerd. De ColumnDescription kan een kolom beschrijven die niet meer in het schema voor komt.  Het is de schrijver om de beschrijving en het schema synchroon te laten blijven.  De gegevens bron bevat mogelijk ook informatie over de beschrijving van kolommen en ze zijn aanvullende ColumnDescription-objecten die tijdens het uitvoeren van het hulp programma worden gemaakt.</td></tr>
<tr><td></td><td>columnName</td><td>Tekenreeks</td><td>De naam van de kolom waarnaar deze beschrijving verwijst.</td></tr>
<tr><td></td><td>beschrijving</td><td>Tekenreeks</td><td>een korte beschrijving (2-3 regels) van de kolom.</td></tr>

<tr><td>ColumnTag ("columnTags")</td><td></td><td></td><td>Deze eigenschap bevat een label voor een kolom. Elke gebruiker van het systeem kan meerdere labels voor een bepaalde kolom toevoegen en tags voor meerdere kolommen toevoegen. Alleen de gebruiker die ColumnTag-objecten heeft gemaakt, kan deze bewerken. (Beheerders en eigen aren van activa kunnen het ColumnTag-object verwijderen, maar niet bewerken). Het systeem onderhoudt de kolom Tags van deze gebruikers afzonderlijk.  Er is dus een matrix met ColumnTag-objecten op elke Asset.  De ColumnTag is losjes gebonden aan het schema zodat het niet kan worden gesynchroniseerd. De ColumnTag kan een kolom beschrijven die niet meer in het schema voor komt.  Het is de schrijver om de kolom code en het schema synchroon te laten.</td></tr>
<tr><td></td><td>columnName</td><td>Tekenreeks</td><td>De naam van de kolom waarnaar deze tag verwijst.</td></tr>
<tr><td></td><td>tag</td><td>Tekenreeks</td><td>Een tag waarmee de kolom wordt beschreven.</td></tr>

<tr><td>Expert ("experts")</td><td></td><td></td><td>Deze eigenschap bevat een gebruiker die als een expert in de gegevensset wordt beschouwd. De adviezen van de experts (beschrijvingen) bellen naar de bovenkant van de UX wanneer u beschrijvingen van de vermeldingen vermeldt. Elke gebruiker kan hun eigen experts opgeven. Alleen die gebruiker kan het object van de expert bewerken. (Beheerders en eigen aren van middelen kunnen de expert objecten verwijderen, maar niet bewerken).</td></tr>
<tr><td></td><td>Geavanceerd</td><td>SecurityPrincipal</td><td></td></tr>

<tr><td>Voor beeld ("previews")</td><td></td><td></td><td>De preview bevat een moment opname van de bovenste 20 rijen met gegevens voor de Asset. Preview is alleen zinvol voor bepaalde typen assets (dit is zinvol voor tabellen, maar niet voor metingen).</td></tr>
<tr><td></td><td>preview</td><td>object []</td><td>Matrix van objecten die een kolom Voorst Ellen.  Elk object heeft een eigenschaps toewijzing met een kolom met een waarde voor die kolom voor de rij.</td></tr>

<tr><td>AccessInstruction ("accessInstructions")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>MIME type</td><td>tekenreeks</td><td>Het MIME-type van de inhoud.</td></tr>
<tr><td></td><td>inhoud</td><td>tekenreeks</td><td>De instructies voor het verkrijgen van toegang tot deze gegevens Asset. De inhoud kan een URL, een e-mail adres of een set instructies zijn.</td></tr>

<tr><td>TableDataProfile ("tableDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>, NumberOfRows</td></td><td>int</td><td>Het aantal rijen in de gegevensset</td></tr>
<tr><td></td><td>grootte</td><td>long</td><td>De grootte in bytes van de gegevensset.  </td></tr>
<tr><td></td><td>schemaModifiedTime</td><td>tekenreeks</td><td>De laatste keer dat het schema is gewijzigd</td></tr>
<tr><td></td><td>dataModifiedTime</td><td>tekenreeks</td><td>De laatste keer dat de gegevensset is gewijzigd (gegevens zijn toegevoegd, gewijzigd of verwijderd)</td></tr>

<tr><td>ColumnsDataProfile ("columnsDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>kolommen</td></td><td>ColumnDataProfile[]</td><td>Een matrix met kolom gegevens profielen.</td></tr>

<tr><td>ColumnDataClassification ("columnDataClassifications")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName</td><td>Tekenreeks</td><td>De naam van de kolom waarnaar deze classificatie verwijst.</td></tr>
<tr><td></td><td>classificatie</td><td>Tekenreeks</td><td>De classificatie van de gegevens in deze kolom.</td></tr>

<tr><td>Documentatie ("documentatie")</td><td></td><td></td><td>Aan een bepaalde activa kan slechts één documentatie zijn gekoppeld.</td></tr>
<tr><td></td><td>MIME type</td><td>tekenreeks</td><td>Het MIME-type van de inhoud.</td></tr>
<tr><td></td><td>inhoud</td><td>tekenreeks</td><td>De inhoud van de documentatie.</td></tr>

</table>

### <a name="common-types"></a>Algemene typen
Algemene typen kunnen worden gebruikt als de typen voor eigenschappen, maar zijn geen items.

<table>
<tr><td><b>Algemeen type</b></td><td><b>Eigenschappen</b></td><td><b>Gegevens type</b></td><td><b>Opmerkingen</b></td></tr>
<tr><td>DataSourceInfo</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Type</td><td>tekenreeks</td><td>Hiermee wordt het type gegevens bron beschreven.  Bijvoorbeeld: SQL Server, Oracle Database, enzovoort.  </td></tr>
<tr><td></td><td>Later</td><td>tekenreeks</td><td>Hiermee wordt het type van het object in de gegevens bron beschreven. Bijvoorbeeld: tabel, weer gave voor SQL Server.</td></tr>

<tr><td>DataSourceLocation</td><td></td><td></td><td></td></tr>
<tr><td></td><td>protocol</td><td>tekenreeks</td><td>Vereist. Beschrijft een protocol dat wordt gebruikt om met de gegevens bron te communiceren. Bijvoorbeeld: `tds` voor SQL Server, `oracle` voor Oracle, enzovoort. Raadpleeg de [referentie specificatie van de gegevens bron-DSL-structuur](data-catalog-dsr.md) voor de lijst met ondersteunde protocollen.</td></tr>
<tr><td></td><td>adres</td><td>Woordenboek &lt; teken reeks, object&gt;</td><td>Vereist. Address is een set gegevens die specifiek is voor het protocol dat wordt gebruikt om te identificeren van de gegevens bron waarnaar wordt verwezen. De adres gegevens bereiken een bepaald protocol, wat betekent dat dit niet het geval is zonder het protocol te weten.</td></tr>
<tr><td></td><td>verificatie</td><td>tekenreeks</td><td>Optioneel. Het verificatie schema dat wordt gebruikt om te communiceren met de gegevens bron. Bijvoorbeeld: Windows, OAuth, etc.</td></tr>
<tr><td></td><td>connectionProperties</td><td>Woordenboek &lt; teken reeks, object&gt;</td><td>Optioneel. Aanvullende informatie over het maken van verbinding met een gegevens bron.</td></tr>

<tr><td>SecurityPrincipal</td><td></td><td></td><td>De back-end voert geen validatie uit van de gegeven eigenschappen voor de Azure Active Directory tijdens het publiceren.</td></tr>
<tr><td></td><td>upn</td><td>tekenreeks</td><td>Uniek e-mail adres van de gebruiker. Moet worden opgegeven als objectId niet is opgegeven of in de context van de eigenschap lastRegisteredBy, anders optioneel.</td></tr>
<tr><td></td><td>objectId</td><td>Guid</td><td>De identiteit van de gebruiker of beveiligings groep Azure Active Directory. Optioneel. Moet worden opgegeven als UPN niet is opgegeven, anders optioneel.</td></tr>
<tr><td></td><td>voornaam</td><td>tekenreeks</td><td>De voor naam van de gebruiker (voor weergave doeleinden). Optioneel. Alleen geldig in de context van de eigenschap lastRegisteredBy. Kan niet worden opgegeven bij het leveren van de beveiligingsprincipal voor ' roles ', ' permissions ' en ' expert Programma's '.</td></tr>
<tr><td></td><td>achternaam</td><td>tekenreeks</td><td>De achternaam van de gebruiker (voor weer gave-doel einden). Optioneel. Alleen geldig in de context van de eigenschap lastRegisteredBy. Kan niet worden opgegeven bij het leveren van de beveiligingsprincipal voor ' roles ', ' permissions ' en ' expert Programma's '.</td></tr>

<tr><td>Kolom</td><td></td><td></td><td></td></tr>
<tr><td></td><td>naam</td><td>tekenreeks</td><td>De naam van de kolom of het kenmerk.</td></tr>
<tr><td></td><td>type</td><td>tekenreeks</td><td>het gegevens type van de kolom of het kenmerk. De toegestane typen zijn afhankelijk van de gegevens source type van de Asset.  Alleen een subset van typen wordt ondersteund.</td></tr>
<tr><td></td><td>Lengte</td><td>int</td><td>De maximale lengte die is toegestaan voor de kolom of het kenmerk. Afgeleid van de gegevens bron. Alleen van toepassing op bepaalde bron typen.</td></tr>
<tr><td></td><td>precisie</td><td>DBCS</td><td>De precisie voor de kolom of het kenmerk. Afgeleid van de gegevens bron. Alleen van toepassing op bepaalde bron typen.</td></tr>
<tr><td></td><td>isNullable</td><td>Booleaans</td><td>Hiermee wordt aangegeven of de kolom een null-waarde mag hebben of niet. Afgeleid van de gegevens bron. Alleen van toepassing op bepaalde bron typen.</td></tr>
<tr><td></td><td>expressie</td><td>tekenreeks</td><td>Als de waarde een berekende kolom is, bevat dit veld de expressie waarmee de waarde wordt uitgedrukt. Afgeleid van de gegevens bron. Alleen van toepassing op bepaalde bron typen.</td></tr>

<tr><td>ColumnDataProfile</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName </td><td>tekenreeks</td><td>De naam van de kolom</td></tr>
<tr><td></td><td>type </td><td>tekenreeks</td><td>Het type van de kolom</td></tr>
<tr><td></td><td>min. </td><td>tekenreeks</td><td>De minimum waarde in de gegevensset</td></tr>
<tr><td></td><td>max </td><td>tekenreeks</td><td>De maximale waarde in de gegevensset</td></tr>
<tr><td></td><td>gemiddeld </td><td>double</td><td>De gemiddelde waarde in de gegevensset</td></tr>
<tr><td></td><td>STDEV </td><td>double</td><td>De standaard afwijking voor de gegevensset</td></tr>
<tr><td></td><td>nullCount </td><td>int</td><td>Het aantal null-waarden in de gegevensset</td></tr>
<tr><td></td><td>distinctCount  </td><td>int</td><td>Het aantal afzonderlijke waarden in de gegevensset</td></tr>
</table>

## <a name="asset-identity"></a>Asset-identiteit
Azure Data Catalog maakt gebruik van ' Protocol ' en identiteits eigenschappen van de eigenschappen verzameling ' adres ' van de eigenschap DataSourceLocation ' DSL ' om de identiteit van de Asset te genereren, die wordt gebruikt om de activa in de catalogus op te lossen.
Het TDS-protocol (Tabular Data stream) heeft bijvoorbeeld identiteits eigenschappen "server", "data base", "schema" en "object". De combi Naties van het protocol en de identiteits eigenschappen worden gebruikt voor het genereren van de identiteit van de SQL Server Table-Asset.
Azure Data Catalog biedt verschillende ingebouwde protocollen voor gegevens bronnen, die worden vermeld in [referentie specificatie gegevens bron-DSL-structuur](data-catalog-dsr.md).
De set ondersteunde protocollen kan via een programma worden uitgebreid (Zie Data Catalog REST API Reference). Beheerders van de catalogus kunnen aangepaste gegevens bron protocollen registreren. In de volgende tabel worden de eigenschappen beschreven die nodig zijn om een aangepast protocol te registreren.

### <a name="custom-data-source-protocol-specification"></a>Aangepaste gegevens bron protocol specificatie
<table>
<tr><td><b>Type</b></td><td><b>Eigenschappen</b></td><td><b>Gegevens type</b></td><td><b>Opmerkingen</b></td></tr>

<tr><td>DataSourceProtocol</td><td></td><td></td><td></td></tr>
<tr><td></td><td>naamruimte</td><td>tekenreeks</td><td>De naam ruimte van het protocol. Naam ruimte moet 1 tot 255 tekens lang zijn, een of meer niet-lege onderdelen bevatten, gescheiden door punt (.). Elk deel moet 1 tot 255 tekens lang zijn, beginnen met een letter en mag alleen letters en cijfers bevatten.</td></tr>
<tr><td></td><td>naam</td><td>tekenreeks</td><td>De naam van het protocol. De naam moet 1 tot 255 tekens lang zijn, beginnen met een letter en mag alleen letters, cijfers en het koppel teken (-) bevatten.</td></tr>
<tr><td></td><td>identityProperties</td><td>DataSourceProtocolIdentityProperty[]</td><td>De lijst met identiteits eigenschappen moet ten minste één bevatten, maar niet meer dan 20 eigenschappen. Bijvoorbeeld: ' server ', ' data base ', ' schema ', ' object ' zijn identiteits eigenschappen van het TDS-protocol.</td></tr>
<tr><td></td><td>identitySets</td><td>DataSourceProtocolIdentitySet[]</td><td>Lijst met identiteits sets. Hiermee worden sets identiteits eigenschappen gedefinieerd, die de identiteit van een geldig Asset vertegenwoordigen. Moet ten minste één, maar niet meer dan 20 sets bevatten. Bijvoorbeeld: {"server", "data base", "schema" en "object"} is een identiteit die is ingesteld voor het TDS-protocol, waarmee de identiteit van SQL Server Table-Asset wordt gedefinieerd.</td></tr>

<tr><td>DataSourceProtocolIdentityProperty</td><td></td><td></td><td></td></tr>
<tr><td></td><td>naam</td><td>tekenreeks</td><td>De naam van de eigenschap. De naam moet 1 tot 100 tekens lang zijn, beginnen met een letter en mag alleen letters en cijfers bevatten.</td></tr>
<tr><td></td><td>type</td><td>tekenreeks</td><td>Het type van de eigenschap. Ondersteunde waarden: "BOOL", Boolean "," byte "," GUID "," int "," integer "," Long "," string "," URL "</td></tr>
<tr><td></td><td>ignoreCase</td><td>booleaans</td><td>Hiermee wordt aangegeven of case moet worden genegeerd wanneer de waarde van een eigenschap wordt gebruikt. Kan alleen worden opgegeven voor eigenschappen met het type String. De standaard waarde is False.</td></tr>
<tr><td></td><td>urlPathSegmentsIgnoreCase</td><td>BOOL []</td><td>Hiermee wordt aangegeven of case moet worden genegeerd voor elk segment van het URL-pad. Kan alleen worden opgegeven voor eigenschappen met het type URL. De standaard waarde is [False].</td></tr>

<tr><td>DataSourceProtocolIdentitySet</td><td></td><td></td><td></td></tr>
<tr><td></td><td>naam</td><td>tekenreeks</td><td>De naam van de identiteits.</td></tr>
<tr><td></td><td>properties</td><td>teken reeks []</td><td>De lijst met identiteits eigenschappen die zijn opgenomen in deze identiteits reeks. Het mag geen dubbele waarden bevatten. Elke eigenschap waarnaar wordt verwezen door de identiteitset, moet worden gedefinieerd in de lijst met ' identityProperties ' van het protocol.</td></tr>

</table>

## <a name="roles-and-authorization"></a>Rollen en autorisatie
Microsoft Azure Data Catalog biedt autorisatie mogelijkheden voor ruwe bewerkingen op activa en aantekeningen.

De Azure Data Catalog gebruikt twee autorisatie mechanismen:

* Op rollen gebaseerde autorisatie
* Autorisatie op basis van machtigingen

### <a name="roles"></a>Rollen
Er zijn drie rollen: **beheerder**, **eigenaar** en **Inzender**.  Elke rol heeft zijn bereik en rechten, die in de volgende tabel worden samenvatten.

<table><tr><td><b>Role</b></td><td><b>Scope</b></td><td><b>Machtiging</b></td></tr><tr><td>Beheerder</td><td>Catalogus (alle assets/aantekeningen in de catalogus)</td><td>ViewRoles lezen verwijderen

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Eigenaar</td><td>Elk activum (hoofd item)</td><td>ViewRoles lezen verwijderen

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Inzender</td><td>Elke afzonderlijke activa en aantekening</td><td>Lees de update ViewRoles Opmerking verwijderen: alle rechten worden ingetrokken als het Lees recht op het item van de Inzender wordt ingetrokken</td></tr></table>

> [!NOTE]
> **Lezen**, **bijwerken**, **verwijderen**, **ViewRoles** rechten zijn van toepassing op alle items (activa of aantekening) terwijl **TakeOwnership**, **ChangeOwnership**, **ChangeVisibility**, **ViewPermissions** alleen van toepassing zijn op de hoofd activa.
> 
> Het recht **verwijderen** is van toepassing op een item en eventuele onderliggende items of afzonderlijke items. Als u bijvoorbeeld een Asset verwijdert, worden ook alle aantekeningen voor dat activum verwijderd.
> 

### <a name="permissions"></a>Machtigingen
Machtiging is een lijst met vermeldingen voor toegangs beheer. Elke toegangscontrole vermelding wijst set rechten toe aan een beveiligingsprincipal. Machtigingen kunnen alleen worden opgegeven voor een asset (dat wil zeggen, hoofd item) en gelden voor het activum en eventuele subitems.

Tijdens de **Azure Data Catalog** Preview wordt alleen **Lees** recht ondersteund in de lijst met machtigingen om het scenario in te scha kelen om de zicht baarheid van een Asset te beperken.

Een geverifieerde gebruiker heeft standaard **Lees** rechten voor elk item in de catalogus, tenzij de zicht baarheid is beperkt tot de set principals in de machtigingen.

## <a name="rest-api"></a>REST-API
**Put** -en **post** -item weergave items kunnen worden gebruikt voor het beheren van rollen en machtigingen: naast de item lading kunnen twee systeem eigenschappen **rollen** en **machtigingen** worden opgegeven.

> [!NOTE]
> **machtigingen** die alleen van toepassing zijn op een hoofd item.
> 
> De rol van **eigenaar** is alleen van toepassing op een hoofd item.
> 
> Wanneer een item wordt gemaakt in de catalogus, wordt de **Inzender** standaard ingesteld op de momenteel geverifieerde gebruiker. Als het item kan worden bijgewerkt door iedereen, moet **Inzender** worden ingesteld op &lt; &gt; de speciale beveiligingsprincipal van iedereen in de eigenschap **roles** wanneer het item voor het eerst wordt gepubliceerd (Zie het volgende voor beeld). **Inzenders** kunnen niet worden gewijzigd en blijven hetzelfde tijdens de levens duur van een item (zelfs **beheerder** of **eigenaar** heeft niet het recht om de **Inzender** te wijzigen). De enige waarde die wordt ondersteund voor de expliciete instelling van de **Inzender** is &lt; iedereen &gt; : **Inzender** kan alleen een gebruiker zijn die een item of iedereen heeft gemaakt &lt; &gt; .
> 

### <a name="examples"></a>Voorbeelden
**Stel Inzender in op &lt; iedereen &gt; bij het publiceren van een item.**
Speciale beveiligingsprincipal- &lt; iedereen &gt; heeft objectId ' 00000000-0000-0000-0000-000000000201 '.
  **Post** https: \/ /API.azuredatacatalog.com/Catalogs/default/views/Tables/?API-Version=2016-03-30

> [!NOTE]
> Bij sommige HTTP-client implementaties worden aanvragen mogelijk automatisch opnieuw uitgegeven als reactie op een 302 van de server, maar doorgaans de header-autorisatie headers van de aanvraag. Aangezien de autorisatie-header vereist is om aanvragen voor Azure Data Catalog te maken, moet u ervoor zorgen dat de autorisatie-header nog steeds wordt opgegeven wanneer een aanvraag wordt verzonden naar een omleidings locatie die is opgegeven door Azure Data Catalog. De volgende voorbeeld code laat zien hoe u het .NET HttpWebRequest-object kunt gebruiken.
> 

**Hoofdtekst**
```json
    {
        "roles": [
            {
                "role": "Contributor",
                "members": [
                    {
                        "objectId": "00000000-0000-0000-0000-000000000201"
                    }
                ]
            }
        ]
    }
```

  **Eigen aren toewijzen en zicht baarheid beperken voor een bestaand hoofd item**: **put** https: \/ /API.azuredatacatalog.com/Catalogs/default/views/Tables/042297b0...1be45ecd462a?API-Version=2016-03-30

```json
    {
        "roles": [
            {
                "role": "Owner",
                "members": [
                    {
                        "objectId": "c4159539-846a-45af-bdfb-58efd3772b43",
                        "upn": "user1@contoso.com"
                    },
                    {
                        "objectId": "fdabd95b-7c56-47d6-a6ba-a7c5f264533f",
                        "upn": "user2@contoso.com"
                    }
                ]
            }
        ],
        "permissions": [
            {
                "principal": {
                    "objectId": "27b9a0eb-bb71-4297-9f1f-c462dab7192a",
                    "upn": "user3@contoso.com"
                },
                "rights": [
                    {
                        "right": "Read"
                    }
                ]
            },
            {
                "principal": {
                    "objectId": "4c8bc8ce-225c-4fcf-b09a-047030baab31",
                    "upn": "user4@contoso.com"
                },
                "rights": [
                    {
                        "right": "Read"
                    }
                ]
            }
        ]
    }
```

> [!NOTE]
> In is het niet nodig om een payload van een item op te geven in de hoofd tekst: PUT kan worden gebruikt om alleen rollen en/of machtigingen bij te werken.
> 

## <a name="next-steps"></a>Volgende stappen
[Naslag informatie over Azure Data Catalog REST API](/rest/api/datacatalog/)
