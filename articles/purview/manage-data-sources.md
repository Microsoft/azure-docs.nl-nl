---
title: Gegevens bronnen beheren in azure controle sfeer liggen (preview)
description: Meer informatie over het registreren van nieuwe gegevens bronnen, het beheren van verzamelingen gegevens bronnen en het weer geven van bronnen in azure controle sfeer liggen (preview).
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: 8714c3c3794186d6c21a0513bd7700764c000b6d
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/19/2020
ms.locfileid: "97694793"
---
# <a name="manage-data-sources-in-azure-purview-preview"></a>Gegevens bronnen beheren in azure controle sfeer liggen (preview)

In dit artikel vindt u informatie over het registreren van nieuwe gegevens bronnen, het beheren van verzamelingen gegevens bronnen en het weer geven van bronnen in azure controle sfeer liggen (preview). Azure controle sfeer liggen ondersteunt de volgende gegevens bronnen:

* On-premises SQL Server
* Azure Data Lake Storage Gen1 
* Azure Data Lake Storage Gen2
* Azure Blob Storage
* Azure Data Explorer
* Azure SQL Database
* Beheerd exemplaar van Azure SQL data base
* Azure Synapse Analytics (voorheen SQL DW)
* Azure Cosmos DB
* Power BI

## <a name="register-a-new-source"></a>Een nieuwe bron registreren

Gebruik de volgende stappen om een nieuwe bron te registreren.

1. Open controle sfeer liggen Studio en selecteer de tegel **bronnen registreren** .

   :::image type="content" source="media/manage-data-sources/purview-studio.png" alt-text="Azure controle sfeer liggen Studio":::

1. Selecteer **registreren** en selecteer vervolgens een bron type. In dit voor beeld wordt Azure Blob Storage gebruikt. Selecteer **Doorgaan**.

   :::image type="content" source="media/manage-data-sources/select-source-type.png" alt-text="Selecteer een gegevens bron type op de pagina bronnen registreren":::

1. Vul het formulier in op de pagina **bronnen registreren** . Selecteer een naam voor de bron en voer de relevante gegevens in. Als u een **Azure-abonnement** hebt gekozen als uw methode voor het selecteren van uw account, worden de bronnen in uw abonnement weer gegeven in een vervolg keuzelijst. U kunt ook uw bron gegevens hand matig invoeren.

   :::image type="content" source="media/manage-data-sources/register-sources-form.png" alt-text="Formulier voor gegevens bron gegevens":::

1. Selecteer **Finish**.

## <a name="view-sources"></a>Bronnen weer geven

U kunt alle geregistreerde bronnen weer geven op het tabblad **bronnen** van Azure controle sfeer liggen Studio. Er zijn twee weergave typen: kaart weergave en lijst weergave.

### <a name="map-view"></a>Kaartweergave

In de kaart weergave kunt u al uw bronnen en verzamelingen bekijken. In de volgende afbeelding is er één Azure Blob Storage-bron. Vanuit elke bron tegel kunt u de bron bewerken, een nieuwe scan starten of de bron gegevens weer geven.

:::image type="content" source="media/manage-data-sources/map-view.png" alt-text="Weer gave Azure controle sfeer liggen-gegevens bron toewijzing":::

### <a name="list-view"></a>Lijstweergave

In de lijst weergave kunt u een sorteer bare lijst met bronnen zien. Beweeg de muis aanwijzer over de bron om opties te bewerken, een nieuwe scan te starten of te verwijderen.

:::image type="content" source="media/manage-data-sources/list-view.png" alt-text="Lijst weergave voor Azure controle sfeer liggen-gegevens bronnen":::

## <a name="manage-collections"></a>Verzamelingen beheren

U kunt uw gegevens bronnen groeperen in verzamelingen. Als u een nieuwe verzameling wilt maken, selecteert u **+ nieuwe verzameling** op de pagina *bronnen* van Azure controle sfeer liggen Studio. Geef de verzameling een naam en selecteer *geen* als bovenliggend element. De nieuwe verzameling wordt weer gegeven in de kaart weergave.

Als u bronnen aan een verzameling wilt toevoegen, selecteert u het potlood **bewerken** op de bron en kiest u een verzameling in de vervolg keuzelijst **Selecteer een verzameling** .

Als u een hiërarchie van verzamelingen wilt maken, wijst u verzamelingen van een hoger niveau toe als bovenliggend item voor verzamelingen op een lager niveau. In de volgende afbeelding is *fabrikam* een bovenliggend item van de verzameling *Financiën* , die een Azure Blob Storage-gegevens bron bevat. U kunt verzamelingen samen vouwen of uitvouwen door te klikken op de cirkel die aan de pijl tussen niveaus is gekoppeld.

:::image type="content" source="media/manage-data-sources/collections.png" alt-text="Een hiërarchie van verzamelingen in azure controle sfeer liggen Studio":::

U kunt bronnen verwijderen uit een-hiërarchie door *geen* te selecteren voor het bovenliggende item. Niet-bovenliggende bronnen worden gegroepeerd in een gestippeld vak in de kaart weergave zonder dat er pijlen aan ouders worden gekoppeld.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het registreren en scannen van verschillende gegevens bronnen:

* [Azure Data Lake Storage Gen 2](register-scan-adls-gen2.md)
* [Power BI Tenant](register-scan-power-bi-tenant.md)
* [Azure SQL Database](register-scan-azure-sql-database.md)
