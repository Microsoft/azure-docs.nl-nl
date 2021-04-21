---
title: Beheerde connectors voor Azure Logic Apps
description: Gebruik door Microsoft beheerde triggers en acties om geautomatiseerde werkstromen te maken die andere apps, gegevens, services en systemen integreren met behulp van Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 04/20/2021
ms.openlocfilehash: d50cf1009fb674f2d3d5914715dcbd9699565489
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796728"
---
# <a name="managed-connectors-for-logic-apps"></a>Beheerde connectors voor Logic Apps

[Beheerde connectors](apis-list.md) bieden u manieren om toegang te krijgen tot andere services en systemen waar [ingebouwde triggers](built-in.md) en acties niet beschikbaar zijn. U kunt deze triggers en acties gebruiken om werkstromen te maken die gegevens, apps, cloudservices en on-premises systemen integreren. Vergeleken met ingebouwde triggers en acties zijn deze connectors meestal gekoppeld aan een specifieke service of een specifiek systeem, zoals Azure Blob Storage-, Office 365-, SQL-, Salesforce- of SFTP-servers. Beheerd door Microsoft en gehost in Azure. Voor beheerde connectors moet u meestal eerst een verbinding maken vanuit uw werkstroom en uw identiteit verifiëren. Triggers op basis van zowel terugkeerpatroon als webhook zijn beschikbaar. Als u dus een trigger op basis van een terugkeerpatroon gebruikt, bekijkt u overzicht van [terugkeergedrag.](apis-list.md#recurrence-behavior)

Voor een aantal services, systemen en protocollen, zoals Azure Service Bus, Azure Functions, SQL, AS2, Logic Apps, biedt Logic Apps ook ingebouwde versies. Het aantal en het bereik variëren afhankelijk van of u een logische app met meerdere tenants of een logische app met één tenant maakt. In een paar gevallen zijn zowel een ingebouwde versie als een versie van een beheerde connector beschikbaar. In de meeste gevallen biedt de ingebouwde versie betere prestaties, mogelijkheden, prijzen, en meer. Als u bijvoorbeeld B2B-berichten wilt uitwisselen met behulp van het [AS2-protocol,](../logic-apps/logic-apps-enterprise-integration-as2.md)selecteert u de ingebouwde versie, tenzij u traceringsmogelijkheden nodig hebt, die alleen beschikbaar zijn in de (afgeschafte) beheerde connectorversie.

Sommige beheerde connectors voor Logic Apps behoren tot meerdere subcategorieën. De SAP-connector is bijvoorbeeld zowel een [bedrijfsconnector als](#enterprise-connectors) [een on-premises connector.](#on-premises-connectors)

* [Standaardconnectoren](#standard-connectors) bieden toegang tot services zoals Azure Blob Storage, Office 365, SharePoint, Salesforce, Power BI, OneDrive en nog veel meer.
* [On-premises connectors](#on-premises-connectors) bieden toegang tot on-premises systemen zoals SQL Server, SharePoint Server, SAP, Oracle DB, bestands shares en andere.
* [Integratieaccountconnectoren](#integration-account-connectors) helpen u bij het transformeren en valideren van XML, het coderen en decoderen van platte bestanden en het verwerken van B2B-berichten (Business-to-Business) met behulp van AS2-, EDIFACT- en X12-protocollen. 

## <a name="standard-connectors"></a>Standaardconnectoren

Azure Logic Apps deze populaire Standard-connectors voor het bouwen van geautomatiseerde werkstromen met behulp van deze services en systemen. Sommige Standard-connectors ondersteunen ook [on-premises systemen of](#on-premises-connectors) [integratieaccounts.](#integration-account-connectors)

Sommige Logic Apps Standard-connectors ondersteunen [on-premises systemen of](#on-premises-connectors) [integratieaccounts.](#integration-account-connectors)

:::row:::
    :::column:::
        [![Azure Service Bus beheerde connectorpictogram in Logic Apps][azure-service-bus-icon]][azure-service-bus-doc]
        \
        \
        [**Azure Service Bus**][azure-service-bus-doc]
        \
        \
        Beheer asynchrone berichten, sessies en abonnementen op onderwerpen met behulp van de meest gebruikte connector in Logic Apps.
    :::column-end:::
    :::column:::
        [![SQL Server beheerde connector in Logic Apps][sql-server-icon]][sql-server-doc]
        \
        \
        [**SQL Server**][sql-server-doc]
        \
        \
        Maak verbinding met uw SQL Server on-premises of een Azure SQL Database in de cloud, zodat u records kunt beheren, opgeslagen procedures kunt uitvoeren of query's kunt uitvoeren.
    :::column-end:::
    :::column:::
        [![Pictogram beheerde Azure Storage-connector in Logic Apps][azure-blob-storage-icon]][azure-blob-storage-doc]
        \
        \
        [**Azure Blog Storage**][azure-blob-storage-doc]
        \
        \
        Maak verbinding met Azure Storage account, zodat u blob-inhoud kunt maken en beheren.
    :::column-end:::
    :::column:::
        [![Pictogram beheerde Office 365 Outlook-connector in Logic Apps][office-365-outlook-icon]][office-365-outlook-doc]
        \
        \
        [**Office 365 Outlook**][office-365-outlook-doc]
        \
        \
        Maak verbinding met uw e-mailaccount voor werk of school, zodat u e-mailberichten, taken, agendagebeurtenissen en vergaderingen, contactpersonen, aanvragen en meer kunt maken en beheren.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Pictogram van beheerde STFP-SSH-connector in Logic Apps][sftp-ssh-icon]][sftp-ssh-doc]
        \
        \
        [**STFP-SSH**][sftp-ssh-doc]
        \
        \
        Maak verbinding met SFTP-servers die vanaf internet met SSH toegankelijk zijn, zodat u met uw bestanden en mappen kunt werken.
    :::column-end:::
    :::column:::
        [![Pictogram beheerde SharePoint Online-connector in Logic Apps][sharepoint-online-icon]][sharepoint-online-doc]
        \
        \
        [**SharePoint Online**][sharepoint-online-doc]
        \
        \
        Om verbinding te maken met SharePoint Online, zodat u bestanden, bijlagen, mappen en meer kunt beheren.
    :::column-end:::
    :::column:::
        [![Pictogram beheerde Azure Queues-connector in Logic Apps][azure-queues-icon]][azure-queues-doc]
        \
        \
        [**Azure-wachtrijen**][azure-queues-doc]
        \
        \
        Maak verbinding met Azure Storage-account, zodat u wachtrijen en berichten kunt maken en beheren.
    :::column-end:::
    :::column:::
        [![Pictogram beheerde FTP-connector in Logic Apps][ftp-icon]][ftp-doc]
        \
        \
        [**Ftp**][ftp-doc]
        \
        \
        Maak verbinding met FTP-servers die u via internet kunt openen, zodat u met uw bestanden en mappen kunt werken.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Pictogram beheerde file system-connector in Logic Apps][file-system-icon]][file-system-doc]
        \
        \
        [**Bestandssysteem**][file-system-doc]
        \
        \
        Maak verbinding met uw on-premises bestands share zodat u bestanden kunt maken en beheren.
    :::column-end:::
    :::column:::
        [![Azure Event Hubs beheerde connectorpictogram in Logic Apps][azure-event-hubs-icon]][azure-event-hubs-doc]
        \
        \
        [**Azure Event Hubs**][azure-event-hubs-doc]
        \
        \
        Hiermee kunt u gebeurtenissen verbruiken en publiceren via een Event Hub. U kunt bijvoorbeeld uitvoer van uw logische app ophalen met Event Hubs en de uitvoer vervolgens verzenden naar een realtime analytics-provider.
    :::column-end:::
    :::column:::
        [![Azure Event Grid beheerde connectorpictogram in Logic Apps][azure-event-grid-icon]][azure-event-grid-doc]
        \
        \
        [**Azure Event Grid**][azure-event-grid-doc]
        \
        \
        Gebeurtenissen bewaken die door een Event Grid, bijvoorbeeld wanneer Azure-resources of resources van derden worden gewijzigd.
    :::column-end:::
    :::column:::
        [![Pictogram beheerde Salesforce-connector in Logic Apps][salesforce-icon]][salesforce-doc]
        \
        \
        [**Salesforce**][salesforce-doc]
        \
        \
        Maak verbinding met uw Salesforce-account, zodat u items zoals records, taken, objecten en meer kunt maken en beheren.
    :::column-end:::
:::row-end:::


## <a name="on-premises-connectors"></a>On-premises connectors

Voordat u een verbinding met een on-premises systeem kunt maken, moet u eerst een [on-premises gegevensgateway downloaden, installeren en instellen.][gateway-doc] Deze gateway biedt een beveiligd communicatiekanaal zonder dat u de benodigde netwerkinfrastructuur hoeft in te stellen. 

De volgende connectors zijn enkele veelgebruikte [Standard-connectors](#standard-connectors) die Logic Apps voor toegang tot gegevens en resources in on-premises systemen. Zie Ondersteunde gegevensbronnen voor de lijst met on-premises [connectors.](../logic-apps/logic-apps-gateway-connection.md#supported-connections)

:::row:::
    :::column:::
        [![Pictogram van biztalk Server on-premises connector in Logic Apps][biztalk-server-icon]][biztalk-server-doc]
        \
        \
        [**Biztalk Server**][biztalk-server-doc]
    :::column-end:::
    :::column:::
        [![Pictogram van de on-premises file system-connector in Logic Apps][file-system-icon]][file-system-doc]
        \
        \
        [**Bestandssysteem**][file-system-doc]
    :::column-end:::
    :::column:::
        [![Pictogram van de on-premises IBM Db2-connector in Logic Apps][ibm-db2-icon]][ibm-db2-doc]
        \
        \
        [**IBM Db2**][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![Pictogram van de on-premises IBM Informix-connector in Logic Apps][ibm-informix-icon]][ibm-informix-doc]
        \
        \
        [**IBM Informix**][ibm-informix-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Pictogram mySQL on-premises connector in Logic Apps][mysql-icon]][mysql-doc]
        \
        \
        [**MySQL**][mysql-doc]
    :::column-end:::
    :::column:::
        [![Oracle DB on-premises connectorpictogram in Logic Apps][oracle-db-icon]][oracle-db-doc]
        \
        \
        [**Oracle DB**][oracle-db-doc]
    :::column-end:::
    :::column:::
        [![Pictogram postgreSQL on-premises connector in Logic Apps][postgre-sql-icon]][postgre-sql-doc]
        \
        \
        [**Postgresql**][postgre-sql-doc]
    :::column-end:::
    :::column:::
        [![Pictogram van on-premises SharePoint Server-connector in Logic Apps][sharepoint-server-icon]][sharepoint-server-doc]
        \
        \
        [**SharePoint Server**][sharepoint-server-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![SQL Server on-premises connectorpictogram in Logic Apps][sql-server-icon]][sql-server-doc]
        \
        \
        [**SQL Server**][sql-server-doc]
    :::column-end:::
    :::column:::
        [![Pictogram van teradata on-premises connector in Logic Apps][teradata-icon]][teradata-doc]
        \
        \
        [**Teradata**][teradata-doc]
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="integration-account-connectors"></a>Integratieaccountconnectoren

Integratieaccountconnectoren bieden specifieke ondersteuning [voor B2B-communicatiescenario's (Business-to-Business)](../logic-apps/logic-apps-enterprise-integration-overview.md) in Azure Logic Apps. Nadat [](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) u een integratieaccount hebt gemaakt en uw B2B-artefacten hebt gedefinieerd, zoals handelspartners, overeenkomsten, kaarten en schema's, kunt u integratieaccountconnectoren gebruiken om berichten te coderen en decoderen, inhoud te transformeren en meer.

Als u bijvoorbeeld Microsoft BizTalk Server, kunt u een verbinding maken vanuit uw werkstroom met behulp van [BizTalk Server on-premises connector](#on-premises-connectors). Vervolgens kunt u BizTalk-achtige bewerkingen in uw werkstroom uitbreiden of uitvoeren met behulp van deze integratieaccountconnectoren.

> [!NOTE]
> Voordat u integratieaccountconnectoren kunt gebruiken, moet u [uw logische app koppelen aan een integratieaccount.](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)


:::row:::
    :::column:::
        [![As2-decodeeractiepictogram in Logic Apps][as2-icon]][as2-doc]
        \
        \
        [**AS2-decoderen**][as2-doc]
    :::column-end:::
    :::column:::
        [![Actiepictogram voor AS2-codering in Logic Apps][as2-icon]][as2-doc]
        \
        \
        [**AS2-codering**][as2-doc]
    :::column-end:::
    :::column:::
        [![Het actiepictogram voor EDIFACT-decoderen in Logic Apps][edifact-icon]][edifact-decode-doc]
        \
        \
        [**EDIFACT-decodering**][edifact-decode-doc]
    :::column-end:::
    :::column:::
        [![Actiepictogram voor EDIFACT-codering in Logic Apps][edifact-icon]][edifact-encode-doc]
        \
        \
        [**EDIFACT-codering**][edifact-encode-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Actiepictogram voor decoderen van plat bestand in Logic Apps][flat-file-decode-icon]][flat-file-decode-doc]
        \
        \
        [**Decoderen van plat bestand**][flat-file-decode-doc]
    :::column-end:::
    :::column:::
        [![Actiepictogram voor het coderen van plat bestand in Logic Apps][flat-file-encode-icon]][flat-file-encode-doc]
        \
        \
        [**Codering van plat bestand**][flat-file-encode-doc]
    :::column-end:::
    :::column:::
        [![Actiepictogram integratieaccount in Logic Apps][integration-account-icon]][integration-account-doc]
        \
        \
        [**Integratieaccount**][integration-account-doc]
    :::column-end:::
    :::column:::
        [![Het actiepictogram Liquid transforms in Logic Apps][liquid-icon]][json-liquid-transform-doc]
        \
        \
        [**Liquid-transformaties**][json-liquid-transform-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Pictogram van de actie voor X12-decoderen in Logic Apps][x12-icon]][x12-decode-doc]
        \
        \
        [**X12-decodering**][x12-decode-doc]
    :::column-end:::
    :::column:::
        [![Actiepictogram voor X12-codering in Logic Apps][x12-icon]][x12-encode-doc]
        \
        \
        [**X12-codering**][x12-encode-doc]
    :::column-end:::
    :::column:::
        [![Het actiepictogram XML transformeert in Logic Apps][xml-transform-icon]][xml-transform-doc]
        \
        \
        [**XML-transformaties**][xml-transform-doc]
    :::column-end:::
    :::column:::
        [![Pictogram xml-validatieactie in Logic Apps][xml-validate-icon]][xml-validate-doc]
        \
        \
        [**XML-validatie**][xml-validate-doc]
    :::column-end:::
:::row-end:::

## <a name="enterprise-connectors"></a>Bedrijfsconnectoren

De volgende connectors bieden toegang tot bedrijfssystemen tegen extra kosten:

:::row:::
    :::column:::
        [![Pictogram van IBM 3270 Enterprise-connector in Logic Apps][ibm-3270-icon]][ibm-3270-doc]
        \
        \
        [**IBM 3270** Enterprise-connector][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![Pictogram voor IBM MQ Enterprise-connector in Logic Apps][ibm-mq-icon]][ibm-mq-doc]
        \
        \
        [**IBM MQ** Enterprise-connector][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![Pictogram sap enterprise-connector in Logic Apps][sap-icon]][sap-connector-doc]
        \
        \
        [**SAP** Enterprise-connector][sap-connector-doc]
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::


## <a name="ise-connectors"></a>ISE-connectors

In een ISE (Integration Service Environment) hebben deze beheerde connectors ook [ISE-versies,](apis-list.md#ise-and-connectors)die andere mogelijkheden hebben dan de versies met meerdere tenants:

> [!NOTE]
> Logische apps die worden uitgevoerd in een ISE en hun connectors, ongeacht waar deze connectors worden uitgevoerd, volgen een vast prijsplan versus het prijsplan op basis van verbruik. Zie prijsmodel en Logic Apps [prijsinformatie](../logic-apps/logic-apps-pricing.md) voor [Logic Apps meer informatie.](https://azure.microsoft.com/pricing/details/logic-apps/)

:::row:::
    :::column:::
        [![As2 ISE-connectorpictogram in Logic Apps][as2-icon]][as2-doc]
        \
        \
        [**AS2** Ise][as2-doc]
    :::column-end:::
    :::column:::
        [![Azure Automation ISE-connectorpictogram in Logic Apps][azure-automation-icon]][azure-automation-doc]
        \
        \
        [**Azure Automation** Ise][azure-automation-doc]
    :::column-end:::
    :::column:::
        [![Azure Blob Storage ISE-connectorpictogram in Logic Apps][azure-blob-storage-icon]][azure-blob-storage-doc]
        \
        \
        [**Azure Blob Storage** Ise][azure-blob-storage-doc]
    :::column-end:::
    :::column:::
        [![Azure Cosmos DB ISE-connectorpictogram in Logic Apps][azure-cosmos-db-icon]][azure-cosmos-db-doc]
        \
        \
        [**Azure Cosmos DB** Ise][azure-cosmos-db-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Azure Event Hubs ISE-connectorpictogram in Logic Apps][azure-event-hubs-icon]][azure-event-hubs-doc]
        \
        \
        [**Azure Event Hubs** Ise][azure-event-hubs-doc]
    :::column-end:::
    :::column:::
        [![Azure Event Grid ISE-connectorpictogram in Logic Apps][azure-event-grid-icon]][azure-event-grid-doc]
        \
        \
        [**Azure Event Grid** Ise][azure-event-grid-doc]
    :::column-end:::
    :::column:::
        [![Pictogram van azure File Storage ISE-connector in Logic Apps][azure-file-storage-icon]][azure-file-storage-doc]
        \
        \
        [**Azure File Storage** Ise][azure-file-storage-doc]
    :::column-end:::
    :::column:::
        [![Azure Key Vault ISE-connectorpictogram in Logic Apps][azure-key-vault-icon]][azure-key-vault-doc]
        \
        \
        [**Azure Key Vault** Ise][azure-key-vault-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Azure Monitor ise-connectorpictogram logs in Logic Apps][azure-monitor-logs-icon]][azure-monitor-logs-doc]
        \
        \
        [**Azure Monitor logboeken** Ise][azure-monitor-logs-doc]
    :::column-end:::
    :::column:::
        [![Azure Service Bus ISE-connectorpictogram in Logic Apps][azure-service-bus-icon]][azure-service-bus-doc]
        \
        \
        [**Azure Service Bus** Ise][azure-service-bus-doc]
    :::column-end:::
    :::column:::
        [![Azure Synapse Analytics ISE-connectorpictogram in Logic Apps][azure-sql-data-warehouse-icon]][azure-sql-data-warehouse-doc]
        \
        \
        [**Azure Synapse Analytics** Ise][azure-sql-data-warehouse-doc]
    :::column-end:::
    :::column:::
        [![Pictogram van azure Table Storage ISE-connector in Logic Apps][azure-table-storage-icon]][azure-table-storage-doc]
        \
        \
        [**Azure Table Storage** Ise][azure-table-storage-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Pictogram van azure Queues ISE-connector in Logic Apps][azure-queues-icon]][azure-queues-doc]
        \
        \
        [**Azure-wachtrijen** Ise][azure-queues-doc]
    :::column-end:::
    :::column:::
        [![Pictogram EDIFACT ISE-connector in Logic Apps][edifact-icon]][edifact-doc]
        \
        \
        [**EDIFACT** Ise][edifact-doc]
    :::column-end:::
    :::column:::
        [![Pictogram van de ISE-connector voor bestandssysteem in Logic Apps][file-system-icon]][file-system-doc]
        \
        \
        [**Bestandssysteem** Ise][file-system-doc]
    :::column-end:::
    :::column:::
        [![Pictogram FTP ISE-connector in Logic Apps][ftp-icon]][ftp-doc]
        \
        \
        [**FTP** Ise][ftp-doc]
    :::column-end:::
:::row-end:::   
:::row:::
    :::column:::
        [![Pictogram ibm 3270 ISE-connector in Logic Apps][ibm-3270-icon]][ibm-3270-doc]
        \
        \
        [**IBM 3270** Ise][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![Pictogram van IBM DB2 ISE-connector in Logic Apps][ibm-db2-icon]][ibm-db2-doc]
        \
        \
        [**IBM DB2** Ise][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![Pictogram van DE IBM MQ ISE-connector in Logic Apps][ibm-mq-icon]][ibm-mq-doc]
        \
        \
        [**IBM MQ** Ise][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![Pictogram van SAP ISE-connector in Logic Apps][sap-icon]][sap-connector-doc]
        \
        \
        [**SAP** Ise][sap-connector-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![SFTP-SSH ISE-connectorpictogram in Logic Apps][sftp-ssh-icon]][sftp-ssh-doc]
        \
        \
        [**SFTP-SSH** Ise][sftp-ssh-doc]
    :::column-end:::
    :::column:::
        [![Pictogram VAN DE SMTP ISE-connector in Logic Apps][smtp-icon]][smtp-doc]
        \
        \
        [**SMTP** Ise][smtp-doc]
    :::column-end:::
    :::column:::
        [![SQL Server ISE-connectorpictogram in Logic Apps][sql-server-icon]][sql-server-doc]
        \
        \
        [**SQL Server** Ise][sql-server-doc]
    :::column-end:::
    :::column:::
        [![Pictogram van X12 ISE-connector in Logic Apps][x12-icon]][x12-doc]
        \
        \
        [**X12** Ise][x12-doc]
    :::column-end:::
:::row-end:::

Raadpleeg de volgende onderwerpen voor meer informatie:

* [Toegang tot virtuele Azure-netwerkbronnen vanuit Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Prijsmodel voor logische apps](../logic-apps/logic-apps-pricing.md)
* [Verbinding maken met virtuele Azure-netwerken vanuit Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aangepaste API's maken die u kunt aanroepen vanuit Logic Apps](/logic-apps/logic-apps-create-api-app)

<!--Managed connector icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-cosmos-db-icon]: ./media/apis-list/azure-cosmos-db.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-devops-icon]: ./media/apis-list/azure-devops.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-file-storage-icon]: ./media/apis-list/azure-file-storage.png
[azure-key-vault-icon]: ./media/apis-list/azure-key-vault.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-monitor-logs-icon]: ./media/apis-list/azure-monitor-logs.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
[azure-sql-data-warehouse-icon]: ./media/apis-list/azure-sql-data-warehouse.png
[azure-table-storage-icon]: ./media/apis-list/azure-table-storage.png
[basecamp-3-icon]: ./media/apis-list/basecamp.png
[bitbucket-icon]: ./media/apis-list/bitbucket.png
[bitly-icon]: ./media/apis-list/bitly.png
[biztalk-server-icon]: ./media/apis-list/biztalk.png
[blogger-icon]: ./media/apis-list/blogger.png
[campfire-icon]: ./media/apis-list/campfire.png
[common-data-service-icon]: ./media/apis-list/common-data-service.png
[dynamics-365-financials-icon]: ./media/apis-list/dynamics-365-financials.png
[dynamics-365-operations-icon]: ./media/apis-list/dynamics-365-operations.png
[easy-redmine-icon]: ./media/apis-list/easyredmine.png
[file-system-icon]: ./media/apis-list/file-system.png
[ftp-icon]: ./media/apis-list/ftp.png
[github-icon]: ./media/apis-list/github.png
[google-calendar-icon]: ./media/apis-list/google-calendar.png
[google-drive-icon]: ./media/apis-list/google-drive.png
[google-sheets-icon]: ./media/apis-list/google-sheet.png
[google-tasks-icon]: ./media/apis-list/google-tasks.png
[hipchat-icon]: ./media/apis-list/hipchat.png
[ibm-3270-icon]: ./media/apis-list/ibm-3270.png
[ibm-db2-icon]: ./media/apis-list/ibm-db2.png
[ibm-informix-icon]: ./media/apis-list/ibm-informix.png
[ibm-mq-icon]: ./media/apis-list/ibm-mq.png
[insightly-icon]: ./media/apis-list/insightly.png
[instagram-icon]: ./media/apis-list/instagram.png
[instapaper-icon]: ./media/apis-list/instapaper.png
[jira-icon]: ./media/apis-list/jira.png
[mandrill-icon]: ./media/apis-list/mandrill.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[onedrive-icon]: ./media/apis-list/onedrive.png
[onedrive-for-business-icon]: ./media/apis-list/onedrive-business.png
[oracle-db-icon]: ./media/apis-list/oracle-db.png
[outlook.com-icon]: ./media/apis-list/outlook.png
[pagerduty-icon]: ./media/apis-list/pagerduty.png
[pinterest-icon]: ./media/apis-list/pinterest.png
[postgre-sql-icon]: ./media/apis-list/postgre-sql.png
[project-online-icon]: ./media/apis-list/projecton-line.png
[redmine-icon]: ./media/apis-list/redmine.png
[salesforce-icon]: ./media/apis-list/salesforce.png
[sap-icon]: ./media/apis-list/sap.png
[send-grid-icon]: ./media/apis-list/sendgrid.png
[sftp-ssh-icon]: ./media/apis-list/sftp.png
[sharepoint-online-icon]: ./media/apis-list/sharepoint-online.png
[sharepoint-server-icon]: ./media/apis-list/sharepoint-server.png
[slack-icon]: ./media/apis-list/slack.png
[smartsheet-icon]: ./media/apis-list/smartsheet.png
[smtp-icon]: ./media/apis-list/smtp.png
[sparkpost-icon]: ./media/apis-list/sparkpost.png
[sql-server-icon]: ./media/apis-list/sql.png
[teradata-icon]: ./media/apis-list/teradata.png
[todoist-icon]: ./media/apis-list/todoist.png
[twilio-icon]: ./media/apis-list/twilio.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[wordpress-icon]: ./media/apis-list/wordpress.png
[youtube-icon]: ./media/apis-list/youtube.png


<!--Managed connector doc links-->
[azure-automation-doc]: /connectors/azureautomation/ "Automatiseringstaken voor uw cloud- en on-premises infrastructuur maken en beheren"
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Bestanden in uw blobcontainer beheren met Azure Blob Storage-container."
[azure-cosmos-db-doc]: /connectors/documentdb/ "Verbinding maken met Azure Cosmos DB, zodat u toegang hebt tot documenten en opgeslagen procedures"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "Gebeurtenissen bewaken die door een Event Grid, bijvoorbeeld wanneer Azure-resources of resources van derden worden gewijzigd"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Maak verbinding met Azure Event Hubs, zodat u gebeurtenissen kunt ontvangen en verzenden tussen logische apps en Event Hubs"
[azure-file-storage-doc]: /connectors/azurefile/ "Maak verbinding met Azure Storage-account, zodat u bestanden kunt maken, bijwerken, downloaden en verwijderen"
[azure-key-vault-doc]: /connectors/keyvault/ "Maak verbinding met uw Azure Key Vault zodat u uw geheimen en sleutels kunt beheren"
[azure-monitor-logs-doc]: /connectors/azuremonitorlogs/ "Query's uitvoeren op Azure Monitor logboeken in Log Analytics-werkruimten en Application Insights onderdelen"
[azure-queues-doc]: /connectors/azurequeues/ "Maak verbinding met Azure Storage-account, zodat u wachtrijen en berichten kunt maken en beheren"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Berichten verzenden vanuit Service Bus wachtrijen en onderwerpen en berichten ontvangen van Service Bus wachtrijen en abonnementen"
[azure-sql-data-warehouse-doc]: /connectors/sqldw/ "Verbinding maken met Azure Synapse Analytics, zodat u uw gegevens kunt bekijken"
[azure-table-storage-doc]: /connectors/azuretables/ "Maak verbinding met uw Azure Storage account, zodat u onder andere tabellen kunt maken, bijwerken en er query's op kunt uitvoeren"
[biztalk-server-doc]: /connectors/biztalk/ "Maak verbinding met uw BizTalk Server zodat u op BizTalk gebaseerde toepassingen naast elkaar kunt uitvoeren Azure Logic Apps"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "Verbinding maken met een on-premises bestandssysteem"
[ftp-doc]: ./connectors-create-api-ftp.md "Verbinding maken met een FTP-/FTPS-server voor FTP-taken, zoals het uploaden, ophalen en verwijderen van bestanden, en meer."
[github-doc]: ./connectors-create-api-github.md "Verbinding maken met GitHub en problemen bijhouden"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Verbinding maken met Google Calendar en agenda beheren"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Verbinding maken met Google Sheets, zodat u uw werkbladen kunt wijzigen"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "Maakt verbinding met Google Tasks, zodat u uw taken kunt beheren"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "Verbinding maken met 3270 apps op IBM-mainframes"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Maak verbinding met IBM DB2 in de cloud of on-premises. Een rij bijwerken, een tabel op halen, en meer"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Maak verbinding met Informix in de cloud of on-premises. Lees een rij, vermeld de tabellen en meer"
[ibm-mq-doc]: ./connectors-create-api-mq.md "Verbinding maken met IBM MQ on-premises of in Azure om berichten te verzenden en te ontvangen"
[instagram-doc]: ./connectors-create-api-instagram.md "Maak verbinding met Band. Gebeurtenissen activeren of er actie op ondernemen"
[mandrill-doc]: ./connectors-create-api-mandrill.md "Verbinding maken met Mandrill voor communicatie"
[mysql-doc]: /connectors/mysql/ "Verbinding maken met uw on-premises MySQL-database, zodat u gegevens kunt lezen en schrijven"
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "Maak verbinding met uw werk- of schoolaccount, zodat u e-mailberichten kunt verzenden en ontvangen, uw agenda en contactpersonen kunt beheren, en meer"
[onedrive-doc]: ./connectors-create-api-onedrive.md "Maak verbinding met uw persoonlijke Microsoft OneDrive zodat u bestanden kunt uploaden, verwijderen, opslijst en meer"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "Maak verbinding met uw zakelijke Microsoft OneDrive, zodat u uw bestanden kunt uploaden, verwijderen, in een lijst kunt bekijken en meer"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Verbinding maken met een Oracle-database, zodat u rijen kunt toevoegen, invoegen, verwijderen en meer"
[outlook.com-doc]: ./connectors-create-api-outlook.md "Verbinding maken met uw Outlook-postvak, zodat u uw e-mail, agenda's, contactpersonen en meer kunt beheren"
[postgre-sql-doc]: /connectors/postgresql/ "Verbinding maken met uw PostgreSQL-database, zodat u gegevens uit tabellen kunt lezen"
[salesforce-doc]: ./connectors-create-api-salesforce.md "Maak verbinding met uw Salesforce-account. Accounts, leads, verkoopkansen en meer beheren"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "Verbinding maken met een on-premises SAP-systeem"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "Maak verbinding met SendGrid. E-mail verzenden en lijsten met ontvangers beheren"
[sftp-ssh-doc]: ./connectors-sftp-ssh.md "Maak verbinding met uw SFTP-account via SSH. Bestanden uploaden, downloaden, verwijderen en meer"
[sharepoint-server-doc]: ./connectors-create-api-sharepoint.md "Verbinding maken met de on-premises SharePoint-server. Documenten, lijstitems en meer beheren"
[sharepoint-online-doc]: ./connectors-create-api-sharepoint.md "Verbinding maken met SharePoint Online. Documenten, lijstitems en meer beheren"
[slack-doc]: ./connectors-create-api-slack.md "Verbinding maken met Slack en berichten posten in Slack-kanalen"
[smtp-doc]: ./connectors-create-api-smtp.md "Verbinding maken met een SMTP-server en e-mail met bijlagen verzenden"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "Maakt verbinding met SparkPost voor communicatie"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Maak verbinding met Azure SQL Database of SQL Server. Vermeldingen in een tabel maken, bijwerken, SQL database verwijderen"
[teradata-doc]: /connectors/teradata/ "Verbinding maken met uw Teradata-database om gegevens uit tabellen te lezen"
[twilio-doc]: ./connectors-create-api-twilio.md "Maak verbinding met Twilio. Berichten verzenden en ontvangen, beschikbare nummers verkrijgen, binnenkomende telefoonnummers beheren, en meer"
[youtube-doc]: ./connectors-create-api-youtube.md "Maak verbinding met YouTube. Uw video's en kanalen beheren"

<!--Integration account connector icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png

<!-- Integration account connector docs -->

[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Berichten coderen en decoderen die gebruikmaken van het AS2-protocol"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "Berichten coderen en decoderen die gebruikmaken van het EDIFACT-protocol"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Berichten decoderen die gebruikmaken van het EDIFACT-protocol"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Berichten coderen die gebruikmaken van het EDIFACT-protocol"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Meer informatie over enterprise integration flat file"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Meer informatie over enterprise integration flat file"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Metagegevens voor integratieaccountartefacten beheren"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "JSON transformeren met Liquid-sjablonen"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Berichten coderen en decoderen die gebruikmaken van het X12-protocol"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Berichten decoderen die gebruikmaken van het X12-protocol"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Berichten coderen die gebruikmaken van het X12-protocol"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "XML-berichten transformeren"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "XML-berichten valideren"


<!--Other doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Verbinding maken met on-premises gegevensbronnen vanuit logische apps met on-premises gegevensgateway"



<!--Integration account connector icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png

<!-- Integration account connector docs -->

[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Berichten coderen en decoderen die gebruikmaken van het AS2-protocol"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "Berichten coderen en decoderen die gebruikmaken van het EDIFACT-protocol"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Berichten decoderen die gebruikmaken van het EDIFACT-protocol"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Berichten coderen die gebruikmaken van het EDIFACT-protocol"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Meer informatie over enterprise integration flat file"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Meer informatie over enterprise integration flat file"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Metagegevens voor integratieaccountartefacten beheren"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "JSON transformeren met Liquid-sjablonen"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Berichten coderen en decoderen die gebruikmaken van het X12-protocol"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Berichten decoderen die gebruikmaken van het X12-protocol"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Berichten coderen die gebruikmaken van het X12-protocol"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "XML-berichten transformeren"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "XML-berichten valideren"
