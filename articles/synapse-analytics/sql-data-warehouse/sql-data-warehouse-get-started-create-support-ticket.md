---
title: Verhoging van quotum aanvragen en ondersteuning krijgen
description: Een ondersteuningsaanvraag maken in de Azure Portal voor Azure Synapse Analytics. Vraag quotumverhogingen aan of vraag ondersteuning voor probleemoplossing.
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/10/2020
author: julieMSFT
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 5256c6d25a9c7acfc45cdffee05c95fb3407c24a
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568315"
---
# <a name="request-quota-increases-and-get-support-for-azure-synapse-analytics"></a>Verhogingen van quotum aanvragen en ondersteuning voor Azure Synapse Analytics

In dit artikel wordt beschreven hoe u een ondersteuningsticket kunt indienen in Azure Portal voor Azure Synapse Analytics. Met dit proces kunt u een quotumverhoging aanvragen of een technische ondersteuningsaanvraag indienen voor het technische ondersteuningsteam.

## <a name="create-a-support-ticket"></a>Een ondersteuningsticket maken

Gebruik de volgende stappen om een nieuwe ondersteuningsaanvraag te maken op Azure Portal voor Azure Synapse Analytics.

1. Selecteer in [Azure Portal](https://portal.azure.com) menu Help **en ondersteuning.**

   ![De koppeling Help en ondersteuning](./media/sql-data-warehouse-get-started-create-support-ticket/help-plus-support.png)


1. Selecteer **in Help en ondersteuning** de optie Nieuwe **ondersteuningsaanvraag.**

    ![Een nieuwe ondersteuningsaanvraag maken](./media/sql-data-warehouse-get-started-create-support-ticket/new-support-request.png)

1. Controleer uw [ondersteuning voor Azure abonnement](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).

   * Ondersteuning voor het **beheren van facturering, quota en abonnementen** is op alle ondersteuningsniveaus beschikbaar.
   * **Ondersteuning voor het oplossen** van problemen wordt geboden via [Developer-,](https://azure.microsoft.com/support/plans/developer/) [Standard-,](https://azure.microsoft.com/support/plans/standard/) [Professional Direct-](https://azure.microsoft.com/support/plans/prodirect/)of [Premier-ondersteuning.](https://azure.microsoft.com/support/plans/premier/) Het gaat hierbij om problemen die klanten ondervinden tijdens het gebruik van Azure waarbij er een redelijke verwachting is dat het probleem is veroorzaakt door Microsoft.
   * **Begeleiding van ontwikkelaars** en **adviesdiensten** zijn beschikbaar op de ondersteuningsniveaus [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) en [Premier](https://azure.microsoft.com/support/plans/premier/).

   Als u een Premier-ondersteuningsplan hebt, kunt u ook Azure Synapse Analytics melden via de [Microsoft Premier Online-portal.](https://premier.microsoft.com/) Zie [ondersteuning voor Azure plannen](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) voor meer informatie over de verschillende ondersteuningsplannen, waaronder bereik, reactietijden, prijzen, enzovoort.  Zie veelgestelde vragen ondersteuning voor Azure [veelgestelde vragen ondersteuning voor Azure veelgestelde vragen](https://azure.microsoft.com/support/faq/)over uw vragen.

1. Selecteer **bij Probleemtype** het juiste probleemtype. Voor problemen met het oplossen van fouten selecteert u **Technisch**. Selecteer service- en **abonnementslimieten (quota) voor** aanvragen voor quotumverhoging.

   ![Selecteer een type probleem](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-issue-type.png)  

   > [!NOTE]
   > In de rest van dit artikel ligt de nadruk op aanvragen voor quotumverhoging. Maar u kunt hier ook Technisch **selecteren** voor ondersteuningsaanvragen voor probleemoplossing. Als u Technisch **selecteert,** wordt u gevraagd een samenvatting op te geven en vervolgens een probleemtype te identificeren door **Probleemtype selecteren te selecteren.** Mogelijk ziet u oplossingen om uw probleem op te lossen. Als de aangeboden oplossingen uw probleem niet oplossen, selecteert u **Volgende:Details** en vult u het formulier in om het ondersteuningsticket in te dienen.

1. Voor aanvragen voor quotumverhoging selecteert **Azure Synapse Analytics** het **quotumtype**. Selecteer vervolgens **Volgende: Oplossingen >>.**

   ![Een quotumtype selecteren](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-type.png)

1. Selecteer in **het venster Details** de optie Details invoeren **om** aanvullende informatie in te voeren.

   ![De koppeling 'Details verstrekken'](./media/sql-data-warehouse-get-started-create-support-ticket/provide-details-link.png)

## <a name="quota-request-types"></a>Typen quotumaanvraag

Als u **Details invoeren selecteert,** **wordt het** venster Quotumdetails weergegeven waarmee u aanvullende informatie kunt toevoegen. In de volgende secties worden de verschillende quotumaanvragen beschreven die beschikbaar zijn voor Azure Synapse Analytics.

### <a name="synapse-sql-pool-data-warehouse-units-dwus-per-server"></a>Synapse SQL groep Data Warehouse eenheden (DWE's) per server

Gebruik de volgende stappen om een verhoging van de DWE's per server aan te vragen.

1. Selecteer het **quotumtype Synapse SQL per server voor** de groep.

1. Selecteer de **Resource** op wie u de quotumverhoging wilt toepassen met behulp van de vervolgkeuzelijst.

1. Voer uw nieuwe quotum in de **sectie Quota aanvragen** in.

1. Selecteer **Opslaan en doorgaan**.

   ![DWU-quotumdetails](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-dwus.png)


### <a name="servers-per-subscription"></a>Servers per abonnement

Als u een verhoging van het aantal servers per abonnement wilt aanvragen, moet u de volgende stappen voltooien:

1. Selecteer de **SQL-servers per abonnement als** quotumtype.

1. Selecteer in **de** lijst Locatie de Azure-regio die u wilt gebruiken. Het quotum is per abonnement in elke regio.

1. Voer in **het veld Aanvraagquotum** uw aanvraag in voor het maximum aantal servers in die regio.

   ![Quotumdetails voor servers](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-servers.png)



1. Selecteer **Opslaan en doorgaan**.

Sommige aanbiedingstypen zijn niet in elke regio beschikbaar. Mogelijk ziet u de volgende fout:

![Fout bij toegang tot regio's](./media/sql-data-warehouse-get-started-create-support-ticket/region-access-error.png)

### <a name="enable-subscription-access-to-a-region"></a>Abonnementstoegang tot een regio inschakelen

Als u toegang tot regio's voor een abonnement wilt inschakelen, moet u de volgende stappen voltooien:  

1. Selecteer het **toegangsquotumtype voor de Synapse SQL pool (datawarehouse).**

1. Selecteer de regio door een Locatie **te kiezen** in de vervolgkeuzelijst.

1. Geef uw DWU-prestatievereiste aan in **de sectie DWU** vereist.

1. Voer uw **beschrijving van zakelijke vereisten in.** 

1. Selecteer **Opslaan en doorgaan**.

![Regiotoegang](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-region.png)


### <a name="for-other-quota-requests"></a>Voor andere quotumaanvragen

Selecteer **Andere quotumaanvraag** in de vervolgkeuzelijst Quotumtype voor andere typen quotumaanvraag:

![Andere quotumdetails](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details.png)

## <a name="submit-your-request"></a>De aanvraag verzenden

De laatste stap bestaat uit het invullen van de resterende gegevens van uw SQL Database ondersteuningsaanvraag. Selecteer vervolgens **Volgende: Controleren en maken>>.**

![Details over het maken bekijken](./media/sql-data-warehouse-get-started-create-support-ticket/review-create-details.png)

Nadat u de aanvraagdetails heeft beoordeeld, selecteert **u Maken om** de aanvraag in te dienen.

![Ticket maken](./media/sql-data-warehouse-get-started-create-support-ticket/create-ticket.png)

## <a name="monitor-a-support-ticket"></a>Een ondersteuningsticket bewaken

Nadat u de ondersteuningsaanvraag hebt ingediend, neemt het ondersteuning voor Azure contact met u op. Als u de status en details van uw aanvraag wilt controleren, selecteert **u Alle ondersteuningsaanvragen** op het dashboard.

![Status controleren](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Meer informatie

U kunt ook verbinding maken met de Azure Synapse Analytics-community [op Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse+or+azure-sql-data-warehouse) of via de Microsoft Q&A question page [for Azure Synapse Analytics](/answers/topics/azure-synapse-analytics.html).