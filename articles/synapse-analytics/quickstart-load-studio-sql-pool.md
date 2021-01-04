---
title: 'Quickstart: Gegevens bulksgewijs laden met toegewezen SQL-pools'
description: Gebruik Synapse Studio om gegevens bulksgewijs te laden in een toegewezen SQL-pool in Azure Synapse Analytics.
services: synapse-analytics
author: kevinvngo
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: quickstart
ms.date: 12/11/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 86ef610af605c657868824eefe2e6e706f6963ac
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97360174"
---
# <a name="quickstart-bulk-loading-with-synapse-sql"></a>Snelstart: Bulksgewijs laden met Synapse SQL

U kunt eenvoudig gegevens laden met de wizard Bulksgewijs laden van Synapse Studio. In deze wizard maakt u een T-SQL-script met de [COPY-instructie](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) om gegevens bulksgewijs te laden. 

## <a name="entry-points-to-the-bulk-load-wizard"></a>Invoerpunten voor de wizard Bulksgewijs laden

U kunt gegevens eenvoudig bulksgewijs laden met toegewezen SQL-pools. Klik hiervoor met de rechtermuisknop op de volgende gebieden in Synapse Studio:

- Een bestand of map uit een Azure-opslagaccount, toegevoegd aan uw werkruimte ![Met de rechtermuisknop klikken op een bestand of map uit een opslagaccount](./sql/media/bulk-load/bulk-load-entry-point-0.png)

## <a name="prerequisites"></a>Vereisten

- Deze wizard genereert een COPY-instructie die gebruikmaakt van Azure Active Directory-passthrough voor verificatie. Uw [Azure Active Directory-gebruiker moet toegang hebben](
./sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples.md#d-azure-active-directory-authentication) tot de werkruimte met ten minste de Azure-rol Inzender voor Storage Blob-gegevens voor het ADLS Gen2-account. 

- U moet beschikken over de vereiste [machtigingen om de COPY-instructie te gebruiken](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#permissions) en ook over machtigingen om een tabel te maken, als u gegevens in een nieuwe tabel wilt laden.

- De service die aan het ADLS Gen2-account is gekoppeld, **moet toegang hebben tot het bestand dat**/**de map die** moet worden geladen. Als de verificatiemethode van de gekoppelde service bijvoorbeeld Beheerde identiteit is, moet de beheerde identiteit voor de werkruimte ten minste over de machtiging Lezer van opslagblob beschikken voor het opslagaccount.

- Als VNet is ingeschakeld voor uw werkruimte, controleert u of interactieve creatie is ingeschakeld voor de geïntegreerde runtime van de gekoppelde services van het ADLS Gen2-account voor de locatie van de brongegevens en foutbestanden. Interactieve creatie is vereist om schema's automatisch te detecteren, previews van de inhoud van het bronbestand te bekijken en door ADLS Gen2-opslagaccounts in de wizard te bladeren.

### <a name="steps"></a>Stappen

1. Selecteer het opslagaccount en het bestand of de map waar vanuit u gegevens wilt laden in het venster 'Opslaglocatie van bron'. Met de wizard wordt automatisch geprobeerd om Parquet-bestanden en CSV-bestanden (tekstbestanden met scheidingstekens) te detecteren, waaronder het toewijzen van de bronvelden uit het bestand aan de juiste doel-SQL-gegevenstypen. 

   ![Bronlocatie selecteren](./sql/media/bulk-load/bulk-load-source-location.png)

2. Selecteer de instellingen voor de bestandsindeling, inclusief de foutinstellingen voor wanneer er rijen zijn geweigerd tijdens het bulksgewijs laden. U kunt ook Voorbeeld van gegevens weergeven selecteren om te zien hoe het bestand wordt geparseerd met de COPY-instructie. Configureer op basis hiervan de instellingen voor bestandsindeling. Telkens wanneer u een instelling voor de bestandsindeling wijzigt, selecteert u ‘Voorbeeld van gegevens weergeven’ om te zien hoe de COPY-instructie het bestand parseert met de bijgewerkte instelling:

   ![Voorbeeld van gegevens weergeven](./sql/media/bulk-load/bulk-load-file-format-settings-preview-data.png) 

> [!NOTE]  
>
> - Het bekijken van een voorbeeld van de gegevens met veldeindtekens voor meerdere tekens, wordt niet ondersteund door de wizard voor bulksgewijs laden. De wizard voor bulksgewijs laden geeft een voorbeeld van de gegevens in één kolom wanneer een veldeindteken voor meerdere tekens is opgegeven. 
> - Wanneer u Kolomnamen afleiden selecteert, worden met de wizard voor bulksgewijs laden de kolomnamen geparseerd uit de eerste rij, opgegeven in het veld Eerste rij. Met de wizard voor bulksgewijs laden wordt de waarde FIRSTROW in de COPY-instructie automatisch met 1 verhoogd, om deze koprij te negeren. 
> - Het opgeven van rij-eindtekens voor meerdere tekens wordt ondersteund in de COPY-instructie. Dit wordt echter niet ondersteund in de wizard voor bulksgewijs laden waar een fout wordt geretourneerd.

3. Selecteer de toegewezen SQL-pool die u gebruikt om gegevens te laden, en geef aan of de gegevens naar een bestaande of nieuwe tabel moeten worden geladen: ![Doellocatie selecteren](./sql/media/bulk-load/bulk-load-target-location.png)
4. Klik op ‘Kolomtoewijzing configureren’ om te controleren of u de juiste kolomtoewijzing gebruikt. Let op: de namen van kolommen worden automatisch gedetecteerd als 'Leid kolomnamen af' is ingeschakeld. Bij nieuwe tabellen is het van cruciaal belang dat u de kolomtoewijzing configureert om de gegevenstypen van de doelkolom bij te werken:

   ![Kolomtoewijzing configureren](./sql/media/bulk-load/bulk-load-target-location-column-mapping.png)
5. Selecteer ‘Script openen’. Er wordt een T-SQL-script gegenereerd met de COPY-instructie om gegevens uit uw data lake te laden: ![Het SQL-script openen](./sql/media/bulk-load/bulk-load-target-final-script.png)

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg het artikel over de [COPY-instructie](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax) voor meer informatie over de mogelijkheden van COPY.
- Bekijk het overzichtsartikel over [gegevens laden](./sql-data-warehouse/design-elt-data-loading.md#what-is-elt).
