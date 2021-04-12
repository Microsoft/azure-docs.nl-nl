---
title: Azure Synapse-werk ruimten scannen
description: Meer informatie over het scannen van een Synapse-werk ruimte in uw Azure controle sfeer liggen Data Catalog.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 3/31/2021
ms.openlocfilehash: 230894b8e474c8d230322fb1e240f0317512d036
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107031405"
---
# <a name="register-and-scan-azure-synapse-workspaces"></a>Azure Synapse-werk ruimten registreren en scannen

In dit artikel wordt beschreven hoe u een Azure Synapse-werk ruimte registreert in controle sfeer liggen en hoe u er een scan op uitvoert.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Met de Azure Synapse-werk ruimte wordt er ondersteuning geboden voor het vastleggen van meta gegevens en schema voor exclusieve en serverloze SQL-data bases. De gegevens worden ook automatisch geclassificeerd op basis van systeem-en aangepaste classificatie regels.

## <a name="prerequisites"></a>Vereisten

- Voordat u gegevens bronnen registreert, maakt u een Azure controle sfeer liggen-account. Zie [Quick Start: een Azure controle sfeer liggen-account maken](create-catalog-portal.md)voor meer informatie over het maken van een controle sfeer liggen-account.
- U moet een Azure controle sfeer liggen-gegevens bron beheerder zijn
- Verificatie instellen, zoals beschreven in de onderstaande secties

### <a name="setting-up-authentication-for-enumerating-dedicated-sql-database-resources-under-a-synapse-workspace"></a>Verificatie instellen voor het inventariseren van toegewezen SQL database resources onder een Synapse-werk ruimte

1. Ga in het Azure Portal naar de **resource groep** of het **abonnement** waarin de Synapse-werk ruimte zich bevindt.  
1. Selecteer **Access Control (IAM)**   in het navigatie menu links 
1. U moet eigenaar of beheerder van de gebruikers toegang zijn om een rol toe te voegen aan de resource groep of het abonnement. Selecteer de knop *+ toevoegen* . 
1. Stel de rol van **lezer** in en voer de naam van het Azure controle sfeer liggen-account in (dat staat voor de bijbehorende MSI) onder invoervak selecteren. Klik op *Opslaan* om de roltoewijzing te volt ooien.
1. Volg stap 2 tot en met 4 hierboven om ook een rol voor **BLOB-gegevens lezer voor opslag** toe te voegen voor de Azure controle sfeer liggen MSI van de resource groep of het abonnement waarin de Synapse-werk ruimte zich bevindt.

### <a name="setting-up-authentication-for-enumerating-serverless-sql-database-resources-under-a-synapse-workspace"></a>Verificatie instellen voor het opsommen van serverloze SQL database resources onder een Synapse-werk ruimte

> [!NOTE]
> U moet een **Synapse-beheerder** zijn in de werk ruimte om deze opdrachten uit te voeren. Meer informatie over Synapse-machtigingen [vindt u hier](../synapse-analytics/security/how-to-set-up-access-control.md).

1. Navigeer naar uw Synapse-werk ruimte
1. Navigeer naar de sectie **gegevens** en naar een van de SERVERloze SQL-data bases
1. Klik op het pictogram met het weglatings teken en start een nieuw SQL-script
1. Voeg de MSI van het Azure controle sfeer liggen-account (aangeduid met de account naam) als **sysadmin** toe op de SERVERloze SQL-data bases door de onderstaande opdracht uit te voeren in uw SQL-script:
    ```sql
    CREATE LOGIN [PurviewAccountName] FROM EXTERNAL PROVIDER;
    ALTER SERVER ROLE sysadmin ADD MEMBER [PurviewAccountName];
    ```

### <a name="setting-up-authentication-to-scan-resources-under-a-synapse-workspace"></a>Verificatie instellen voor het scannen van resources onder een Synapse-werk ruimte

Er zijn drie manieren om verificatie voor een Azure Synapse-bron in te stellen:

- Beheerde identiteit
- Service-principal
 
#### <a name="using-managed-identity-for-dedicated-sql-databases"></a>Beheerde identiteit gebruiken voor exclusieve SQL-data bases

1. Navigeer naar uw **Synapse-werk ruimte**
1. Navigeer naar de sectie **gegevens** en naar een van de SERVERloze SQL-data bases
1. Klik op het pictogram met het weglatings teken en start een nieuw SQL-script
1. Voeg de MSI van het Azure controle sfeer liggen-account (vertegenwoordigd door de account naam) toe als **db_owner** op de toegewezen SQL database door de onderstaande opdracht uit te voeren in uw SQL-script:

    ```sql
    CREATE USER [PurviewAccountName] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_owner', [PurviewAccountName]
    GO
    ```
#### <a name="using-managed-identity-for-serverless-sql-databases"></a>Beheerde identiteit gebruiken voor Serverloze SQL-data bases

1. Navigeer naar uw **Synapse-werk ruimte**
1. Navigeer naar de sectie **gegevens** en naar een van de SERVERloze SQL-data bases
1. Klik op het pictogram met het weglatings teken en start een nieuw SQL-script
1. Voeg de MSI van het Azure controle sfeer liggen-account (aangeduid met de account naam) als **sysadmin** toe op de SERVERloze SQL-data bases door de onderstaande opdracht uit te voeren in uw SQL-script:
    ```sql
    CREATE LOGIN [PurviewAccountName] FROM EXTERNAL PROVIDER;
    ALTER SERVER ROLE sysadmin ADD MEMBER [PurviewAccountName];
    ```

#### <a name="using-service-principal-for-dedicated-sql-databases"></a>Service-Principal gebruiken voor exclusieve SQL-data bases

> [!NOTE]
> U moet eerst de [volgende instructies instellen](manage-credentials.md)voor een nieuwe **referentie** van het type service-principal.

1. Navigeer naar uw **Synapse-werk ruimte**
1. Navigeer naar de sectie **gegevens** en naar een van de SERVERloze SQL-data bases
1. Klik op het pictogram met het weglatings teken en start een nieuw SQL-script
1. Voeg de **Service-Principal-id** als **db_owner** toe op de toegewezen SQL database door de onderstaande opdracht uit te voeren in uw SQL-script:

    ```sql
    CREATE USER [ServicePrincipalID] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_owner', [ServicePrincipalID]
    GO
    ```

#### <a name="using-service-principal-for-serverless-sql-databases"></a>Service-Principal gebruiken voor Serverloze SQL-data bases

1. Navigeer naar uw **Synapse-werk ruimte**
1. Navigeer naar de sectie **gegevens** en naar een van de SERVERloze SQL-data bases
1. Klik op het pictogram met het weglatings teken en start een nieuw SQL-script
1. Voeg de MSI van het Azure controle sfeer liggen-account (aangeduid met de account naam) als **sysadmin** toe op de SERVERloze SQL-data bases door de onderstaande opdracht uit te voeren in uw SQL-script:
    ```sql
    CREATE LOGIN [ServicePrincipalID] FROM EXTERNAL PROVIDER;
    ALTER SERVER ROLE sysadmin ADD MEMBER [ServicePrincipalID];
    ```

> [!NOTE]
> U moet verificatie instellen voor elke toegewezen SQL database in uw Synapse-werk ruimte, die u wilt registreren en scannen. De machtigingen die hierboven worden vermeld voor Serverloze SQL database van toepassing zijn op alle gebruikers in uw werk ruimte, dat wil zeggen dat u deze slechts één keer hoeft uit te voeren.
    
## <a name="register-an-azure-synapse-source"></a>Een Azure Synapse-bron registreren

Ga als volgt te werk om een nieuwe Azure Synapse-bron in uw Data Catalog te registreren:

1. Ga naar uw Purview-account
1. Selecteer **Bronnen** in het linkernavigatievenster
1. Selecteer **Registreren**
1. Selecteer **Azure Synapse Analytics (meerdere)** bij **bronnen registreren**.
1. Selecteer **Doorgaan**

   :::image type="content" source="media/register-scan-synapse-workspace/register-synapse-source.png" alt-text="Azure Synapse-bron instellen":::

Ga als volgt te werk op het scherm **bronnen registreren (Azure Synapse Analytics)** :

1. Voer een **Naam** in waarvan de gegevensbron wordt vermeld in de catalogus.
1. Kies eventueel een **abonnement** om te filteren op.
1. **Selecteer een naam voor de Synapse-werk ruimte** in de vervolg keuzelijst. De SQL-eind punten worden automatisch gevuld op basis van uw werkruimte selectie. 
1. Een **verzameling** selecteren of een nieuwe maken (optioneel)
1. **Volt ooien** om de gegevens bron te registreren

    :::image type="content" source="media/register-scan-synapse-workspace/register-synapse-source-details.png" alt-text="Details van de opvulling voor de Azure Synapse-bron":::

## <a name="creating-and-running-a-scan"></a>Een scan maken en uitvoeren

Doe het volgende om een nieuwe scan te maken en uit te voeren:

1. Navigeer naar het gedeelte **bronnen** .

1. Selecteer de gegevensbron die u hebt geregistreerd.

1. Klik op Details weer geven en selecteer **+ nieuwe scan** of gebruik het pictogram voor snelle acties scannen op de bron tegel

1. Vul de *naam* in en selecteer alle typen resources die u in deze bron wilt scannen. **SQL database** is het enige type dat momenteel in een Synapse-werk ruimte wordt ondersteund.
   
    :::image type="content" source="media/register-scan-synapse-workspace/synapse-scan-setup.png" alt-text="Azure Synapse-bron scan":::

1. Selecteer de **referentie** om verbinding te maken met de resources in uw gegevens bron. 
  
1. U kunt binnen elk type selecteren om alle resources of een subset ervan te scannen op naam.
1.  Klik op **Doorgaan** om door te gaan. 

1.  Selecteer een **Scan regel sets** van het type Azure Synapse SQL. U kunt ook inline scan regel sets maken.

1. Kies de scantrigger. U kunt de app zo plannen dat deze **wekelijks of maandelijks** of **eenmaal** wordt uitgevoerd

1. Controleer de scan en selecteer Opslaan om de configuratie te volt ooien   

## <a name="viewing-your-scans-and-scan-runs"></a>Uw scans en scanuitvoeringen bekijken

1. Bekijk de bron Details door te klikken op **Details weer geven** op de tegel onder het gedeelte bronnen. 

      :::image type="content" source="media/register-scan-synapse-workspace/synapse-source-details.png" alt-text="Details van Azure Synapse-bron"::: 

1. Bekijk de details van de scan uitvoering door te navigeren naar de pagina **Scan Details** .
    1. De *status balk* is een korte samen vatting van de uitvoerings status van de onderliggende resources. Deze wordt weer gegeven op de scan op werkruimte niveau.
    1. Groen betekent dat de bewerking is geslaagd. Grijs betekent dat de scan nog steeds wordt uitgevoerd
    1. U kunt in elke scan klikken om meer gedetailleerde details weer te geven

      :::image type="content" source="media/register-scan-synapse-workspace/synapse-scan-details.png" alt-text="Details van de Azure Synapse-scan" lightbox="media/register-scan-synapse-workspace/synapse-scan-details.png"::: 

1. Bekijk een samen vatting van de recente mislukte scan uitvoeringen aan de onderkant van de pagina met bron gegevens. U kunt ook meer gedetailleerde details weer geven die betrekking hebben op deze uitvoeringen.

## <a name="manage-your-scans---edit-delete-or-cancel"></a>Uw scans beheren - bewerken, verwijderen of annuleren
Doe het volgende om een scan te beheren of te verwijderen:

- Ga naar het beheercentrum. Selecteer Gegevensbronnen in het gedeelte Bronnen en scannen en selecteer vervolgens de gewenste gegevensbron.

- Selecteer de share die u wilt beheren. U kunt de scan bewerken door Bewerken te selecteren.

- U kunt uw scan verwijderen door Verwijderen te selecteren.
- Als er een scan wordt uitgevoerd, kunt u deze ook annuleren.

## <a name="next-steps"></a>Volgende stappen

- [Bladeren door de Azure Purview-gegevenscatalogus](how-to-browse-catalog.md)
- [Zoeken in de Azure Purview-gegevenscatalogus](how-to-search-catalog.md)   