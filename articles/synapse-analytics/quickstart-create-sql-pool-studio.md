---
title: 'Quickstart: Een toegewezen SQL-pool maken met Synapse Studio'
description: Maak een toegewezen Synapse SQL-pool met behulp van Synapse Studio door de stappen in deze handleiding uit te voeren.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 10/16/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: f3ed5f3940a9b02701ad2f3c779b791e128e288c
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506342"
---
# <a name="quickstart-create-a-dedicated-sql-pool-using-synapse-studio"></a>Quickstart: Een toegewezen SQL-pool maken met Synapse Studio

Azure Synapse Analytics biedt diverse analyse-engines waarmee u uw gegevens kunt opnemen, transformeren, modelleren en analyseren. Een toegewezen SQL-pool biedt op T-SQL gebaseerde reken- en opslagmogelijkheden. Nadat u een toegewezen SQL-pool in uw Synapse-werkruimte hebt gemaakt, kunnen gegevens worden geladen, gemodelleerd, verwerkt en geleverd voor een snellere analyse.

In deze quickstart wordt stapsgewijs beschreven hoe u een toegewezen SQL-pool in een Synapse-werkruimte maakt met behulp van Synapse Studio.

Als u geen Azure-abonnement hebt, [maakt u een gratis account voordat u begint](https://azure.microsoft.com/free/).


## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [u kunt een gratis abonnement nemen](https://azure.microsoft.com/free/)
- [Synapse-werkruimte](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Navigeer naar de Synapse-werkruimte

1. Navigeer naar de Synapse-werkruimte waar de toegewezen SQL-pool wordt gemaakt, door de servicenaam (of de resourcenaam) rechtstreeks in de zoekbalk te typen.

    ![Azure-portal-zoekbalk waarin Synapse-werkruimten zijn ingevoerd.](media/quickstart-create-sql-pool/create-sql-pool-00a.png)
1. Typ in de lijst met werkruimten de naam (of een deel van de naam) van de werkruimte die u wilt openen. In dit voorbeeld gebruiken we een werkruimte met de naam **contosoanalytics**.

    ![Lijst met gefilterde Synapse-werkruimten, zodat de werkruimten met de naam contoso worden weergegeven.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="launch-synapse-studio"></a>Synapse Studio starten

1. Selecteer de **web-URL voor de werkruimte** in het werkruimteoverzicht om Synapse Studio te starten.

    ![Synapse-werkruimteoverzicht in Azure Portal met de web-URL voor de werkruimte gemarkeerd.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-20.png)

## <a name="create-a-dedicated-sql-pool-in-synapse-studio"></a>Een toegewezen SQL-pool maken in Synapse Studio

1. Ga op de startpagina van Synapse Studio naar de **Beheerhub** in de linkernavigatiebalk door het pictogram **Beheren** te selecteren.

    ![Startpagina van Synapse Studio met het gedeelte Beheerhub gemarkeerd.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-21.png)

1. Ga in de Beheerhub naar het gedeelte **SQL-pools** voor een overzicht van de huidige lijst met SQL-pools die beschikbaar zijn in de werkruimte.

    ![Synapse Studio-beheerhub met navigatie door SQL-pools geselecteerd](media/quickstart-create-sql-pool/create-sql-pool-studio-22.png)

1. Selecteer opdracht **en Nieuw**. De wizard Nieuwe SQL-pool maken wordt weergegeven. 

    ![Lijst in Synapse Studio-beheerhub met SQL-pools.](media/quickstart-create-sql-pool/create-sql-pool-studio-23.png)

1. Voer de volgende gegevens in op het tabblad **Basisinformatie**:

    | Instelling | Voorgestelde waarde | Beschrijving |
    | :------ | :-------------- | :---------- |
    | **Naam van SQL-pool** | contosoedw | Dit is de naam die de toegewezen SQL-pool krijgt. |
    | **Prestatieniveau** | DW100c | Stel dit in op de kleinste grootte om de kosten voor deze quickstart te verlagen |

    ![Stroom voor het maken van SQL-pools - tabblad Basisinformatie](media/quickstart-create-sql-pool/create-sql-pool-studio-24.png)
    > [!IMPORTANT]
    > Er gelden specifieke beperkingen voor de namen die toegewezen SQL-pools kunnen krijgen. Namen mogen geen speciale tekens bevatten, mogen niet langer zijn dan 15 tekens, mogen geen gereserveerde woorden bevatten en moeten uniek zijn in de werkruimte.

4. Selecteer op het volgende tabblad **Extra instellingen** **Geen** om de SQL-pool zonder gegevens in te richten. Behoud de geselecteerde standaardsortering.

    Als u uw toegewezen SQL-pool vanaf een herstelpunt wilt herstellen, selecteert u **Herstelpunt**. Zie voor meer informatie over het uitvoeren van een herstelbewerking [Uitleg: Een bestaande toegewezen SQL-pool herstellen](backuprestore/restore-sql-pool.md)

    ![Stroom voor het maken van SQL-pool - tabblad Aanvullende instellingen.](media/quickstart-create-sql-pool/create-sql-pool-studio-25.png)

1. We voegen nu geen tags toe. Selecteer daarom vervolgens **Beoordelen en maken**.

1. Controleer op het tabblad **Beoordelen en maken** of de gegevens juist zijn en zijn gebaseerd op wat eerder is ingevoerd. Druk daarna op **Maken**. 

    ![Stroom voor het maken van SQL-pool - tabblad Instellingen controleren.](media/quickstart-create-sql-pool/create-sql-pool-studio-26.png)

1. Op dit punt wordt de stroom voor de resource-inrichting gestart.

1. Als u nadat het inrichten is voltooid weer naar de werkruimte gaat, wordt hier een nieuwe vermelding voor de zojuist gemaakte SQL-pool weergegeven.

    ![Stroom voor het maken van SQL-pool - resource-inrichting.](media/quickstart-create-sql-pool/create-sql-pool-studio-27.png)

1. Nadat de toegewezen SQL-pool is gemaakt, is deze beschikbaar in de werkruimte voor het laden van gegevens, het verwerken van stromen, het lezen vanuit de lake, enzovoort.

## <a name="clean-up-dedicated-sql-pool-using-synapse-studio"></a>Een toegewezen SQL-pool opschonen met Synapse Studio    

Volg de onderstaande stappen om de toegewezen SQL-pool uit de werkruimte te verwijderen met behulp van Synapse Studio.
> [!WARNING]
> Als u een toegewezen SQL-pool verwijdert, wordt de analyse-engine uit de werkruimte verwijderd. Er kan dan geen verbinding meer worden gemaakt met de pool, en alle query's, pijplijnen en scripts die deze toegewezen SQL-pool gebruikt, werken niet meer.

Ga als volgt te werk om de toegewezen SQL-pool te verwijderen:

1. Navigeer naar de SQL-pools in de Beheerhub in Synapse Studio.
1. Selecteer de ellips in de toegewezen SQL-pool die u wilt verwijderen (in dit geval **contosoedw**), om de opdrachten voor de toegewezen SQL-pool weer te geven:

    ![Lijst met SQL-pools, waarbij de zojuist gemaakte groep is geselecteerd.](media/quickstart-create-sql-pool/create-sql-pool-studio-28.png)
1. Druk op **Verwijderen**.
1. Bevestig dat u de werkruimte wilt verwijderen en selecteer de knop **Verwijderen**.
1. Wanneer het proces is voltooid, wordt de toegewezen SQL-pool niet meer weergegeven in de werkruimteresources.

## <a name="next-steps"></a>Volgende stappen 
- Zie [Quickstart: Een Apache Spark-notebook maken](quickstart-apache-spark-notebook.md).
- Zie [Quickstart: Een toegewezen SQL-pool maken met behulp van Azure Portal](quickstart-create-sql-pool-portal.md).
