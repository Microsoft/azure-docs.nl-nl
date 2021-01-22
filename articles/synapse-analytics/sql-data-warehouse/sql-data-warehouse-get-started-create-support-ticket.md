---
title: Vraag quota toe en ontvang ondersteuning
description: Een ondersteunings aanvraag maken in de Azure Portal voor Azure Synapse Analytics. Aanvraag quotum verhoogt of krijgt ondersteuning bij het oplossen van problemen.
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/10/2020
author: kevinvngo
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: acc05d6367a0dc4334bc1d9e3cbb340bdb8d24c0
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98677420"
---
# <a name="request-quota-increases-and-get-support-for-azure-synapse-analytics"></a>Vraag quota toe en ontvang ondersteuning voor Azure Synapse Analytics

In dit artikel wordt beschreven hoe u een ondersteunings ticket kunt verzenden in de Azure Portal voor Azure Synapse Analytics. Met dit proces kunt u een quotum verhoging aanvragen of een aanvraag voor technische ondersteuning indienen voor het technische ondersteunings team.

## <a name="create-a-support-ticket"></a>Een ondersteuningsticket maken

Gebruik de volgende stappen om een nieuwe ondersteunings aanvraag te maken op basis van de Azure Portal voor Azure Synapse Analytics.

1. Selecteer **Help en ondersteuning** in het menu [Azure Portal](https://portal.azure.com) .

   ![De koppeling Help en ondersteuning](./media/sql-data-warehouse-get-started-create-support-ticket/help-plus-support.png)


1. Selecteer **nieuwe ondersteunings aanvraag** in **Help en ondersteuning**.

    ![Een nieuwe ondersteunings aanvraag maken](./media/sql-data-warehouse-get-started-create-support-ticket/new-support-request.png)

1. Controleer uw [ondersteunings abonnement voor Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).

   * Ondersteuning voor het **beheren van facturering, quota en abonnementen** is op alle ondersteuningsniveaus beschikbaar.
   * Ondersteuning voor **het oplossen** van problemen wordt geboden via [Developer](https://azure.microsoft.com/support/plans/developer/), [Standard](https://azure.microsoft.com/support/plans/standard/), [Professional direct](https://azure.microsoft.com/support/plans/prodirect/)of [premier](https://azure.microsoft.com/support/plans/premier/) support. Het gaat hierbij om problemen die klanten ondervinden tijdens het gebruik van Azure waarbij er een redelijke verwachting is dat het probleem is veroorzaakt door Microsoft.
   * **Begeleiding van ontwikkelaars** en **adviesdiensten** zijn beschikbaar op de ondersteuningsniveaus [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) en [Premier](https://azure.microsoft.com/support/plans/premier/).

   Als u een premier-ondersteunings abonnement hebt, kunt u ook problemen met Azure Synapse Analytics melden op de [micro soft Premier Online-Portal](https://premier.microsoft.com/). Raadpleeg [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) voor meer informatie over de verschillende ondersteunings plannen, waaronder bereik, reactie tijden, prijzen enzovoort.  Zie Veelgestelde vragen over [ondersteuning](https://azure.microsoft.com/support/faq/)van Azure voor meer informatie over ondersteuning voor Azure.

1. Selecteer bij **probleem type** het juiste probleem type. Selecteer **Technical** voor problemen oplossen. Voor quota verhogings aanvragen selecteert u **service-en abonnements limieten (quota's)**.

   ![Selecteer een probleem type](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-issue-type.png)  

   > [!NOTE]
   > In deze rest van dit artikel focusses de aanvragen voor quotum verhoging. Maar u kunt hier ook **technisch** selecteren voor ondersteunings aanvragen voor probleem oplossing. Als u **technisch** selecteert, wordt u gevraagd een samen vatting op te geven en vervolgens een probleem type te identificeren door **probleem type selecteren** te selecteren. Mogelijk ziet u oplossingen om het probleem op te lossen. Als het probleem niet wordt opgelost met de oplossingen die worden weer gegeven, selecteert u **volgende: Details** en vult u het formulier in om het ondersteunings ticket in te dienen.

1. Voor quota verhogings aanvragen selecteert u **Azure Synapse Analytics** voor het **quota type**. Selecteer vervolgens **volgende: oplossingen >>**.

   ![Een quotum type selecteren](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-type.png)

1. Selecteer in het **detail** venster **Details invoeren** om aanvullende informatie in te voeren.

   ![De koppeling Details opgeven](./media/sql-data-warehouse-get-started-create-support-ticket/provide-details-link.png)

## <a name="quota-request-types"></a>Typen quotum aanvragen

Als u **Details invoeren** selecteert, wordt het venster **quotum Details** weer gegeven waarin u aanvullende informatie kunt toevoegen. In de volgende secties worden de verschillende quota aanvragen beschreven die beschikbaar zijn voor Azure Synapse Analytics.

### <a name="synapse-sql-pool-data-warehouse-units-dwus-per-server"></a>Synapse SQL pool data warehouse units (Dwu's) per server

Gebruik de volgende stappen om een verhoging van de Dwu's per server aan te vragen.

1. Selecteer het **Synapse SQL-groep dwu's per server** -quotum type.

1. Selecteer de **resource** waarop u de quota toename wilt Toep assen met behulp van de vervolg keuzelijst.

1. Voer uw nieuwe quota in het gedeelte **aanvraag quotum** in.

1. Selecteer **Opslaan en doorgaan**.

   ![Details van DWU-quotum](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-dwus.png)


### <a name="servers-per-subscription"></a>Servers per abonnement

Als u een verhoging van het aantal servers per abonnement wilt aanvragen, moet u de volgende stappen uitvoeren:

1. Selecteer de **SQL-servers per abonnement** als het quota type.

1. Selecteer in de lijst **locatie** de Azure-regio die u wilt gebruiken. Het quotum is per abonnement in elke regio.

1. Voer in het veld **aanvraag quotum** uw aanvraag in voor het maximum aantal servers in die regio.

   ![Details van servers-quota](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-servers.png)



1. Selecteer **Opslaan en doorgaan**.

Sommige aanbiedings typen zijn niet beschikbaar in elke regio. Mogelijk wordt de volgende fout weer geven:

![Fout bij regio toegang](./media/sql-data-warehouse-get-started-create-support-ticket/region-access-error.png)

### <a name="enable-subscription-access-to-a-region"></a>Abonnements toegang tot een regio inschakelen

Als u de regio toegang voor een abonnement wilt inschakelen, moet u de volgende stappen uitvoeren:  

1. Selecteer het **toegangs quotum type Synapse SQL-pool (Data Warehouse)** .

1. Selecteer de regio door een **locatie** in de vervolg keuzelijst te kiezen.

1. Geef uw DWU-prestatie vereiste op in de sectie **DWU is vereist** .

1. Voer uw **Beschrijving van zakelijke vereisten in**. 

1. Selecteer **Opslaan en doorgaan**.

![Regiotoegang](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-region.png)


### <a name="for-other-quota-requests"></a>Voor andere quotum aanvragen

Selecteer **andere quotum aanvraag** in het vervolg keuzemenu quotum type voor andere typen quotum aanvragen:

![Andere quotum Details](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details.png)

## <a name="submit-your-request"></a>De aanvraag verzenden

De laatste stap is het invullen van de resterende Details van de ondersteunings aanvraag van SQL Database. Selecteer **volgende: controleren +>>maken**.

![Details van maken controleren](./media/sql-data-warehouse-get-started-create-support-ticket/review-create-details.png)

Nadat u de details van de aanvraag hebt bekeken, selecteert u **maken** om de aanvraag in te dienen.

![Ticket maken](./media/sql-data-warehouse-get-started-create-support-ticket/create-ticket.png)

## <a name="monitor-a-support-ticket"></a>Een ondersteuningsticket bewaken

Nadat u het ondersteunings verzoek hebt ingediend, neemt het ondersteunings team van Azure contact met u op. Als u de aanvraag status en-details wilt controleren, selecteert u **alle ondersteunings aanvragen** op het dash board.

![Status controleren](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Meer informatie

U kunt ook verbinding maken met de Azure Synapse Analytics-Community op [stack overflow](https://stackoverflow.com/questions/tagged/azure-synapse+or+azure-sql-data-warehouse) of via de [pagina micro soft Q&een vraag voor Azure Synapse Analytics](/answers/topics/azure-synapse-analytics.html).