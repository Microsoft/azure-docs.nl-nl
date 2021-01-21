---
title: Gevoeligheids labels automatisch Toep assen op uw gegevens
description: Meer informatie over het maken van gevoeligheids labels en het automatisch Toep assen op uw gegevens tijdens een scan.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 01/19/2021
ms.openlocfilehash: b376883ab7d8ef0ffd57a271e74862b684788ebd
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98630273"
---
# <a name="automatically-label-your-data-in-azure-purview"></a>Voorzie uw gegevens automatisch in azure controle sfeer liggen

In dit artikel wordt beschreven hoe u een micro soft Information Protection (MIP) Sensitive-labels maakt en deze automatisch toepast op uw Azure-assets in azure controle sfeer liggen.

## <a name="what-are-sensitivity-labels"></a>Wat zijn gevoeligheids labels? 

Om het werk gedaan te krijgen, werken personen in uw organisatie zowel binnen als buiten de organisatie samen met anderen. De gegevens blijven niet altijd in uw Cloud en zwerven vaak overal, op alle apparaten, apps en services. 

Wanneer uw gegevens worden geroamd, wilt u deze in een veilige, beveiligde manier doen die voldoet aan het bedrijfs-en nalevings beleid van uw organisatie.

Door gevoeligheids labels toe te passen, kunt u aangeven hoe gevoelige bepaalde gegevens zich in uw organisatie bevindt. Een specifieke project naam kan bijvoorbeeld zeer vertrouwelijk zijn binnen uw organisatie, terwijl dezelfde term niet vertrouwelijk is voor andere organisaties. 

### <a name="sensitivity-labels-in-azure-purview"></a>Gevoeligheids labels in azure controle sfeer liggen

In controle sfeer liggen zijn classificaties vergelijkbaar met de labels van tags en worden ze gebruikt om gegevens te markeren en te identificeren van een specifiek type dat is gevonden in uw data-erfgoed tijdens het scannen.

Controle sfeer liggen maakt gebruik van dezelfde classificaties, ook wel aangeduid als gevoelige informatie typen, als Microsoft 365.  MIP-gevoeligheids labels worden gemaakt in de Microsoft 365 Security and Compliance Center (SCC). Op deze manier kunt u uw bestaande gevoelige labels uitbreiden over uw Azure controle sfeer liggen-assets.

**Classificaties** worden rechtstreeks overeenkomen, zoals een sofi-nummer, dat een classificatie heeft van het **sofi-nummer**. 

In contrast worden **labels van gevoeligheid** toegepast wanneer een of meer classificaties en voor waarden samen worden gevonden. In deze context verwijzen de [voor waarden](/microsoft-365/compliance/apply-sensitivity-label-automatically) naar alle para meters die u kunt definiëren voor ongestructureerde gegevens, zoals *nabijheid van een andere classificatie* en *% betrouw baarheid*. 

Gevoeligheids labels in azure controle sfeer liggen kunnen worden gebruikt om automatisch labels toe te passen op bestanden en database kolommen.

Zie voor meer informatie:

- [Meer informatie over gevoeligheids labels](/microsoft-365/compliance/sensitivity-labels) in de Microsoft 365 documentatie
- [Wat zijn regels voor auto labeling?](#what-are-autolabeling-rules)
- [Ondersteunde gegevens typen voor gevoeligheids labels in azure controle sfeer liggen](#supported-data-types-for-sensitivity-labels-in-azure-purview)
- [Labelen voor SQL database kolommen](#labeling-for-sql-database-columns)

#### <a name="what-are-autolabeling-rules"></a>Wat zijn regels voor auto labeling?

Uw gegevens groeien voortdurend en veranderen. Het bijhouden van de gegevens die momenteel niet zijn gelabeld, en het uitvoeren van een actie om hand matig labels toe te passen, is niet alleen omslachtig, maar is ook onnodig handig. 

Regels voor het autolabelen zijn voor waarden die u opgeeft, waarbij wordt aangegeven wanneer een bepaald label moet worden toegepast. Wanneer aan deze voor waarden wordt voldaan, wordt het label automatisch toegewezen aan de gegevens en worden er op schaal consistentie van consistente gevoelige labels op uw gegevens bewaard.

Wanneer u uw etiketten maakt, moet u ervoor zorgen dat u regels voor automatisch labelen definieert voor zowel [bestanden](#define-autolabeling-rules-for-files) als [database kolommen](#define-autolabeling-rules-for-database-columns) om uw labels te laten Toep assen met elke gegevens scan. 

Nadat u uw gegevens in controle sfeer liggen hebt gescand, kunt u de labels weer geven die automatisch zijn toegepast in de controle sfeer liggen-catalogus en inzicht rapporten.
#### <a name="supported-data-types-for-sensitivity-labels-in-azure-purview"></a>Ondersteunde gegevens typen voor gevoeligheids labels in azure controle sfeer liggen

Gevoeligheids labels worden ondersteund in azure controle sfeer liggen voor de volgende gegevens typen:

|Gegevenstype  |Bronnen  |
|---------|---------|
|Automatische labeling voor bestanden     |     -Azure Blob Storage  </br>-Azure Data Lake Storage gen 1 en gen 2  |
|Automatische labeling voor database kolommen     |  -SQL Server </br>-Azure SQL database </br>-Azure SQL Database beheerd exemplaar   <br> -Azure Synapse  <br>-Azure Cosmos DB <br><br>Zie voor meer informatie [labelen voor SQL database kolommen](#labeling-for-sql-database-columns) hieronder.  |
| | |

#### <a name="labeling-for-sql-database-columns"></a>Labelen voor SQL database kolommen

Naast controle sfeer liggen-labels voor database kolommen ondersteunt micro soft ook labels voor SQL database kolommen met behulp van de SQL-gegevens classificatie in [SQL Server Management Studio (SSMS)](/sql/ssms/sql-server-management-studio-ssms). Hoewel controle sfeer liggen gebruikmaakt van de globale [MIP-gevoeligheids labels](/microsoft-365/compliance/sensitivity-labels), maakt SSMS alleen gebruik van labels die lokaal zijn gedefinieerd.

Labels in controle sfeer liggen en labeling in SSMS zijn afzonderlijke processen die momenteel niet met elkaar communiceren. Labels die in SSMS worden toegepast, worden daarom niet weer gegeven in controle sfeer liggen en vice versa. We raden Azure controle sfeer liggen aan voor het labelen van SQL-data bases, omdat hierin globale MIP-labels worden gebruikt die kunnen worden toegepast op meerdere platforms.

Zie de documentatie voor SQL- [gegevens detectie en-classificatie](/sql/relational-databases/security/sql-data-discovery-and-classification)voor meer informatie.

## <a name="how-to-create-sensitivity-labels-in-microsoft-365"></a>Gevoeligheids labels maken in Microsoft 365

Als u nog geen gevoeligheids labels hebt, moet u deze maken en deze beschikbaar maken voor Azure controle sfeer liggen. Bestaande gevoeligheids labels kunnen ook worden aangepast om ze beschikbaar te maken voor Azure controle sfeer liggen.

Zie voor meer informatie:

- [Licentievereisten](#licensing-requirements)
- [Gevoeligheids labels uitbreiden naar Azure controle sfeer liggen](#extending-sensitivity-labels-to-azure-purview)
- [Nieuwe gevoeligheids labels maken of bestaande labels wijzigen](#creating-new-sensitivity-labels-or-modifying-existing-labels)
### <a name="licensing-requirements"></a>Licentievereisten

MIP-gevoeligheids labels worden gemaakt en beheerd in het Microsoft 365 Security and Compliance Center. Als u gevoeligheids labels wilt maken voor gebruik in azure controle sfeer liggen, moet u een actieve Microsoft 365 E5-licentie hebben.

Als u nog niet beschikt over de vereiste licentie, kunt u zich aanmelden voor een proef versie van [Microsoft 365 E5](https://www.microsoft.com/microsoft-365/business/compliance-solutions#midpagectaregion).

### <a name="extending-sensitivity-labels-to-azure-purview"></a>Gevoeligheids labels uitbreiden naar Azure controle sfeer liggen

MIP-gevoeligheids labels zijn standaard alleen beschikbaar voor assets in Microsoft 365, waar u ze kunt Toep assen op bestanden en e-mail berichten.

Voor het Toep assen van MIP-gevoeligheids labels op Azure-assets in azure controle sfeer liggen moet u expliciet toestemming geven om de labels uit te breiden en de specifieke labels te selecteren die u beschikbaar wilt maken in controle sfeer liggen.

Door de gevoeligheids labels van MIP uit te breiden met Azure controle sfeer liggen, kunnen organisaties nu inzicht krijgen in de gevoeligheid van een breder scala aan gegevens bronnen en zo een grotere mate van de nalevings Risico's beperken.

> [!NOTE]
> Omdat Microsoft 365 en Azure controle sfeer liggen afzonderlijke services zijn, is het mogelijk dat ze in verschillende regio's worden geïmplementeerd. Label namen en aangepaste gevoelige informatie typen worden beschouwd als klant gegevens en worden op dezelfde geografische locatie bewaard, standaard om de gevoeligheid van uw gegevens te beschermen en AVG wetten te voor komen.
>
> Daarom worden labels en aangepaste gevoelige informatie typen niet standaard gedeeld met Azure controle sfeer liggen en vereisen uw toestemming om ze te gebruiken in azure controle sfeer liggen.

**Gevoeligheids labels uitbreiden naar controle sfeer liggen:**

Ga in Microsoft 365 naar de pagina **Information Protection** . Selecteer in de **uitbrei ding labelen naar assets in azure controle sfeer liggen** de knop **inschakelen** en selecteer vervolgens **Ja** in het bevestigings venster dat wordt weer gegeven.

Bijvoorbeeld:

:::image type="content" source="media/create-sensitivity-label/extend-sensitivity-labels-to-purview-small.png" alt-text="Selecteer * * * * inschakelen om gevoeligheids labels uit te breiden naar controle sfeer liggen" lightbox="media/create-sensitivity-label/extend-sensitivity-labels-to-purview.png":::
 
Wanneer u labels uitbreidt naar assets in azure controle sfeer liggen, kunt u de labels selecteren die u beschikbaar wilt maken in controle sfeer liggen. Zie [nieuwe gevoeligheids labels maken of bestaande labels wijzigen](#creating-new-sensitivity-labels-or-modifying-existing-labels)voor meer informatie.
### <a name="creating-new-sensitivity-labels-or-modifying-existing-labels"></a>Nieuwe gevoeligheids labels maken of bestaande labels wijzigen

1. Open het [Microsoft 365 Security and Compliance Center](https://protection.office.com/homepage). 

1. Onder **oplossingen** selecteert u **gegevens beveiliging** en selecteert u vervolgens **een label maken**. 

    :::image type="content" source="media/create-sensitivity-label/create-sensitivity-label-full-small.png" alt-text="Gevoeligheids labels maken in het Microsoft 365 Security and Compliance Center" lightbox="media/create-sensitivity-label/create-sensitivity-label-full.png":::

1. Noem het label. Selecteer vervolgens onder **het bereik voor dit label definiëren de** optie **bestanden en e-mail berichten** en **Azure controle sfeer liggen-assets**.
    
    :::image type="content" source="media/create-sensitivity-label/create-label-scope-small.png" alt-text="Uw etiket maken in het Microsoft 365 beveiligings-en nalevings centrum" lightbox="media/create-sensitivity-label/create-label-scope.png":::

1. Volg de overige aanwijzingen in de wizard voor de label instellingen. 

    Definieer met name regels voor het autolabelen voor bestanden en database kolommen:

    - [Regels voor het autolabelen van bestanden definiëren](#define-autolabeling-rules-for-files)
    - [Regels voor het autolabelen definiëren voor database kolommen](#define-autolabeling-rules-for-database-columns)

    Zie voor meer informatie over de wizard opties [wat gevoeligheids labels kunnen doen](/microsoft-365/compliance/sensitivity-labels#what-sensitivity-labels-can-do) in de Microsoft 365 documentatie.

1. Herhaal de hierboven vermelde stappen om meer labels te maken. 

    Als u een sublabel wilt maken, selecteert u het bovenliggende label > **...**  >  **Meer acties**  >  **Sublabel toevoegen**.

1. Als u bestaande labels wilt wijzigen, bladert u naar **Information Protection**  >  **labels** en selecteert u uw label. 

    Selecteer vervolgens **label bewerken** om de wizard **gevoeligheids label bewerken** opnieuw te openen, met alle instellingen die u tijdens het maken van het label hebt gedefinieerd.

    :::image type="content" source="media/create-sensitivity-label/edit-sensitivity-label-full-small.png" alt-text="Een bestaand gevoeligheids label bewerken" lightbox="media/create-sensitivity-label/edit-sensitivity-label-full.png":::

1. Wanneer u klaar bent met het maken van al uw etiketten, moet u ervoor zorgen dat u de volg orde van de labels bekijkt en de volg orde ervan indien nodig wijzigen. 

    Als u de volg orde van een label wilt wijzigen, selecteert u **...** **> meer acties**  >  **omhoog** of **omlaag gaan.** 

    Zie voor meer informatie [label prioriteit (kwesties best Ellen)](/microsoft-365/compliance/sensitivity-labels#label-priority-order-matters) in de Microsoft 365 documentatie.

> [!IMPORTANT]
> Verwijder geen label, tenzij u de impact van uw gebruikers begrijpt. 
>
> Zie [Labels verwijderen en verwijderen](/microsoft-365/compliance/create-sensitivity-labels#removing-and-deleting-labels) in de documentatie van Microsoft 365 voor meer informatie.

Ga door met [het scannen van uw gegevens om labels automatisch toe te passen](#scan-your-data-to-apply-labels-automatically)en vervolgens:

- [Labels op assets weer geven](#view-labels-on-assets)
- [Insight-rapporten voor classificaties en gevoeligheids labels weer geven](#view-insight-reports-for-the-classifications-and-sensitivity-labels)

#### <a name="define-autolabeling-rules-for-files"></a>Regels voor het autolabelen van bestanden definiëren

Definieer regels voor het autolabelen van bestanden in de wizard wanneer u een label maakt of bewerkt. 

Schakel op de pagina **automatische labeling voor Office** -apps **automatische labeling in voor Office-apps** en definieer vervolgens de voor waarden waarop u wilt dat het label automatisch wordt toegepast op uw gegevens.

Bijvoorbeeld:

:::image type="content" source="media/create-sensitivity-label/create-auto-labeling-rules-files-small.png" alt-text="Regels voor het autolabelen van bestanden definiëren in het Microsoft 365 beveiligings-en nalevings centrum" lightbox="media/create-sensitivity-label/create-auto-labeling-rules-files.png":::
 
Zie voor meer informatie [automatisch een gevoeligheids label Toep assen op de gegevens](/microsoft-365/compliance/apply-sensitivity-label-automatically#how-to-configure-auto-labeling-for-office-apps) in de Microsoft 365 documentatie. 

#### <a name="define-autolabeling-rules-for-database-columns"></a>Regels voor het autolabelen definiëren voor database kolommen

Definieer regels voor het autolabelen voor database kolommen in de wizard wanneer u een label maakt of bewerkt. 

Onder de optie **Azure controle sfeer liggen activa (preview)** :

1. Selecteer de schuif regelaar **automatische labeling voor database kolommen** .

1. Selecteer **gevoelige gegevens typen controleren** om de gevoelige info typen te kiezen die u wilt Toep assen op uw label.

Bijvoorbeeld:
        
:::image type="content" source="media/create-sensitivity-label/create-auto-labeling-rules-db-columns-small.png" alt-text="Regels voor het autolabelen van SQL-kolommen definiëren in het Microsoft 365 beveiligings-en nalevings centrum" lightbox="media/create-sensitivity-label/create-auto-labeling-rules-db-columns.png":::

## <a name="scan-your-data-to-apply-labels-automatically"></a>Uw gegevens scannen om labels automatisch toe te passen

Scan uw gegevens in azure controle sfeer liggen om automatisch de labels toe te passen die u hebt gemaakt, op basis van de automatische label regels die u hebt gedefinieerd. 

Voor meer informatie over het instellen van scans op diverse assets in azure controle sfeer liggen raadpleegt u:

|Bron  |Naslaginformatie  |
|---------|---------|
|**Azure Blob Storage**     |[Azure-Blob Storage registreren en controleren](register-scan-azure-blob-storage-source.md)         |
|**Azure Data Lake Storage**     |[Azure Data Lake Storage Gen1 registreren en scannen](register-scan-adls-gen1.md) </br>[Azure Data Lake Storage Gen2 registreren en scannen](register-scan-adls-gen2.md)         |
|**Azure SQL Databases**|[Een Azure SQL Database registeren en scannen](register-scan-azure-sql-database.md) </br>[Azure SQL Database Managed Instance registeren en scannen](register-scan-azure-sql-database-managed-instance.md)|
| | |

## <a name="view-labels-on-assets"></a>Labels op assets weer geven

Wanneer u regels voor automatisch labelen voor uw labels in Microsoft 365 hebt gedefinieerd en uw gegevens in azure controle sfeer liggen hebt gescand, worden labels automatisch toegepast op uw activa. 

**U kunt als volgt de labels weer geven die zijn toegepast op uw assets in de Azure controle sfeer liggen-catalogus:**

Gebruik in de Azure controle sfeer liggen-catalogus de opties voor het filteren van **labels** om alleen bestanden met specifieke labels weer te geven. Bijvoorbeeld: 

:::image type="content" source="media/create-sensitivity-label/filter-search-results-small.png" alt-text="Naar assets zoeken op label" lightbox="media/create-sensitivity-label/filter-search-results.png":::

Bijvoorbeeld:

:::image type="content" source="media/create-sensitivity-label/view-labeled-files-blob-storage-small.png" alt-text="Een gevoeligheids label weer geven voor een bestand in uw Azure Blob Storage" lightbox="media/create-sensitivity-label/view-labeled-files-blob-storage.png":::

## <a name="view-insight-reports-for-the-classifications-and-sensitivity-labels"></a>Insight-rapporten voor classificaties en gevoeligheids labels weer geven

Zoek inzichten op uw geclassificeerde en gelabelde gegevens in azure controle sfeer liggen met behulp van de **classificatie** -en **gevoeligheids rapporten labelen** .

> [!div class="nextstepaction"]
> [Classificatie inzichten](./classification-insights.md)

> [!div class="nextstepaction"]
> [Gevoeligheids label Insights](sensitivity-insights.md)


