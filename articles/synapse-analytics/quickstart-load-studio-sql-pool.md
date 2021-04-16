---
title: 'Quickstart: Gegevens bulksgewijs laden met een toegewezen SQL-pool'
description: Gebruik Synapse Studio om gegevens bulksgewijs te laden in een toegewezen SQL-pool in Azure Synapse Analytics.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: quickstart
ms.date: 12/11/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 838138fb6ca6f64b4296b54a81bc2764c3f1399c
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567907"
---
# <a name="quickstart-bulk-loading-with-synapse-studio"></a>Quickstart: Bulksgewijs laden met Synapse Studio

U kunt eenvoudig gegevens laden met de wizard Bulksgewijs laden van Synapse Studio. Synapse Studio is een functie van Azure Synapse Analytics. In de wizard Bulksgewijs laden maakt u een T-SQL-script met de [COPY-instructie](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) om gegevens bulksgewijs te laden in een toegewezen SQL-pool. 

## <a name="entry-points-to-the-bulk-load-wizard"></a>Invoerpunten voor de wizard Bulksgewijs laden

U kunt gegevens bulksgewijs laden door met de rechtermuisknop op het volgende gebied in Synapse Studio te klikken: een bestand of map van een Azure-opslagaccount dat is gekoppeld aan uw werkruimte.

![Schermopname van het met de rechtermuisknop klikken op een bestand of map vanuit een opslagaccount.](./sql/media/bulk-load/bulk-load-entry-point-0.png)

## <a name="prerequisites"></a>Vereisten

- Deze wizard genereert een COPY-instructie die voor verificatie gebruikmaakt van Azure Active Directory (Azure AD)-passthrough. Uw [Azure AD-gebruiker moet toegang hebben](./sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples.md#d-azure-active-directory-authentication) tot de werkruimte met ten minste de Azure-rol Inzender voor Storage Blob-gegevens voor het Azure Data Lake Storage Gen2-account. 

- U moet beschikken over de vereiste [machtigingen om de COPY-instructie te gebruiken](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#permissions) en ook over machtigingen om een tabel te maken (Create Table), als u gegevens in een nieuwe tabel wilt laden.

- De service die aan het Azure Data Lake Storage Gen2-account is gekoppeld, *moet toegang hebben tot het bestand of de map* die moet worden geladen. Als de verificatiemethode voor de gekoppelde service bijvoorbeeld een beheerde identiteit is, moet de beheerde identiteit voor de werkruimte ten minste over de machtiging Storage Blob Data Reader beschikken voor het opslagaccount.

- Als een virtueel netwerk is ingeschakeld voor uw werkruimte, controleert u of interactieve creatie is ingeschakeld voor de geïntegreerde runtime van de gekoppelde services van het Data Lake Storage Gen2-account voor de locatie van de brongegevens en foutbestanden. Interactieve creatie is vereist om schema's automatisch te detecteren, previews van de inhoud van het bronbestand te bekijken en door Data Lake Storage Gen2-opslagaccounts in de wizard te bladeren.

## <a name="steps"></a>Stappen

1. Selecteer het opslagaccount en het bestand of de map waar vanuit u gegevens wilt laden in het deelvenster **Opslaglocatie van bron**. Met de wizard wordt automatisch geprobeerd om Parquet-bestanden en CSV-bestanden (tekstbestanden met scheidingstekens) te detecteren, waaronder het toewijzen van de bronvelden uit het bestand aan de juiste SQL-doelgegevenstypen. 

   ![Schermopname van de selectie van een bronlocatie.](./sql/media/bulk-load/bulk-load-source-location.png)

2. Selecteer de instellingen voor de bestandsindeling, inclusief de foutinstellingen voor wanneer er rijen zijn geweigerd tijdens het bulksgewijs laden. U kunt ook **Voorbeeld van gegevens weergeven** selecteren om te zien hoe het bestand wordt geparseerd met de COPY-instructie. Configureer op basis hiervan de instellingen voor bestandsindeling. Telkens wanneer u een instelling voor de bestandsindeling wijzigt, selecteert u **Voorbeeld van gegevens weergeven** om te zien hoe met de COPY-instructie het bestand wordt geparseerd met de bijgewerkte instelling.

   ![Schermopname waarin voorbeelden van gegevens worden weergegeven.](./sql/media/bulk-load/bulk-load-file-format-settings-preview-data.png) 

   > [!NOTE]  
   >
   > - De wizard Bulksgewijs laden biedt geen ondersteuning voor het bekijken van een voorbeeld van de gegevens met veldeindtekens met meerdere tekens. De wizard geeft een voorbeeld van de gegevens in één kolom weer wanneer een veldeindteken met meerdere tekens wordt opgegeven. 
   > - Wanneer u **Kolomnamen afleiden** selecteert, worden met de wizard Bulksgewijs laden de kolomnamen geparseerd uit de eerste rij, opgegeven in het veld **Eerste rij**. Met de wizard Bulksgewijs laden wordt de waarde `FIRSTROW` in de COPY-instructie automatisch met 1 verhoogd om deze koprij te negeren. 
   > - Het opgeven van rij-afsluitingen met meerdere tekens wordt ondersteund in de COPY-instructie. De wizard Bulksgewijs laden ondersteunt dit echter niet en er wordt een fout gegenereerd.

3. Selecteer de toegewezen SQL-pool die u gebruikt om gegevens te laden, en geef aan of de gegevens naar een bestaande of nieuwe tabel moeten worden geladen.
   ![Schermopname van de selectie van een doellocatie.](./sql/media/bulk-load/bulk-load-target-location.png)
4. Selecteer **Kolomtoewijzing configureren** om te controleren of u de juiste kolomtoewijzing gebruikt. De namen van kolommen worden automatisch gedetecteerd als **Kolomnamen afleiden** is ingeschakeld. Bij nieuwe tabellen is het van cruciaal belang dat u de kolomtoewijzing configureert om de gegevenstypen van de doelkolom bij te werken.

   ![Schermopname van het configureren van de kolomtoewijzing.](./sql/media/bulk-load/bulk-load-target-location-column-mapping.png)
5. Selecteer **Script openen**. Er wordt een T-SQL-script gegenereerd met de COPY-instructie om gegevens uit uw data lake te laden.
   ![Schermopname waarin het SQL-script wordt geopend.](./sql/media/bulk-load/bulk-load-target-final-script.png)

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg het artikel over de [COPY-instructie](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax) voor meer informatie over de mogelijkheden van COPY.
- Controleer het artikel [Overzicht gegevens laden](./sql-data-warehouse/design-elt-data-loading.md#what-is-elt) artikel voor meer informatie over het gebruik van een proces voor uitpakken, transformeren en laden (ETL).
