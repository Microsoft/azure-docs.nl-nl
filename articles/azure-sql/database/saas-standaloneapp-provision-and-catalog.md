---
title: Zelfstudie voor SaaS-scenario's met meerdere tenants
description: Nieuwe tenants inrichten en catalogiseren met behulp van het patroon voor zelfstandige toepassing
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: 2343800f8801105ca75f285972b441ecb027d1a0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92793242"
---
# <a name="provision-and-catalog-new-tenants-using-the--application-per-tenant-saas-pattern"></a>Nieuwe tenants inrichten en catalogiseren met behulp van het SaaS-patroon voor een toepassing per tenant
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In dit artikel vindt u informatie over het inrichten en catalogiseren van nieuwe tenants met behulp van het SaaS-patroon voor een zelfstandige toepassing per tenant.
Dit artikel bestaat uit twee hoofdonderdelen:
* Conceptuele discussie over het inrichten en catalogiseren van nieuwe tenants
* Een zelfstudie die de nadruk legt op de PowerShell-voorbeeldcode waarmee het inrichten en catalogiseren wordt uitgevoerd
    * In de zelfstudie wordt gebruikgemaakt van de SaaS-toepassing Wingtip Tickets, aangepast aan patroon voor een zelfstandige toepassing per tenant.

## <a name="standalone-application-per-tenant-pattern"></a>Patroon voor een zelfstandige toepassing per tenant

Het patroon voor een zelfstandige toepassing per tenant is er een uit verschillende patronen voor SaaS-toepassingen met meerdere tenants.  In dit patroon wordt een zelfstandige app ingericht voor elke tenant. De toepassing omvat onderdelen op toepassingsniveau en een Azure SQL-database.  Elke tenant-app kan worden geïmplementeerd in het abonnement van de leverancier.  Azure biedt ook een [programma voor beheerde toepassingen](../../azure-resource-manager/managed-applications/overview.md) waarin een app kan worden geïmplementeerd in het abonnement van een tenant en namens de tenant door de leverancier worden beheerd.

   ![patroon voor app per tenant](./media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern.png)

Wanneer u een toepassing voor een tenant implementeert, worden de app en de database ingericht in een nieuwe resourcegroep die voor de tenant is gemaakt.  Door afzonderlijke resourcegroepen te gebruiken, worden de toepassingsresources van elke tenant geïsoleerd en kunnen ze onafhankelijk worden beheerd. Binnen elke resourcegroep wordt elk toepassingsexemplaar geconfigureerd voor rechtstreekse toegang tot de bijbehorende database.  Dit verbindingsmodel contrasteert met andere patronen, die een catalogus gebruiken om verbindingen tussen de app en de database te regelen.  Aangezien er geen resources worden gedeeld, moet elke tenantdatabase met voldoende resources worden ingericht om de piekbelasting te kunnen verwerken. Dit patroon wordt meestal gebruikt voor SaaS-toepassingen met minder tenants, waarbij de nadruk ligt op de isolatie van tenants en minder nadruk op resourcekosten.

## <a name="using-a-tenant-catalog-with-the-application-per-tenant-pattern"></a>Een tenantcatalogus met het patroon voor een toepassing per tenant gebruiken

Hoewel de app en de database van elke tenant volledig geïsoleerd zijn, kunnen verschillende beheer- en analysescenario's op alle tenants worden toegepast.  Voor het toepassen van een schemawijziging voor een nieuwe release van de toepassing, moet het schema van elke tenantdatabase worden gewijzigd. Voor rapportage- en analysescenario's is mogelijk ook toegang nodig tot alle tenantdatabases, ongeacht waar ze worden geïmplementeerd.

   ![Diagram met een illustratie van het gebruik van een tenantcatalogus met het patroon voor een toepassing per tenant.](./media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern-with-catalog.png)

De tenantcatalogus bevat een toewijzing tussen een tenant-id en een tenantdatabase, zodat een id kan worden omgezet naar een server- en databasenaam.  In de Wingtip SaaS-app wordt de tenant-id berekend als een hash van de tenantnaam, hoewel andere schema's kunnen worden gebruikt.  Hoewel voor zelfstandige toepassingen de catalogus niet nodig is om verbindingen te beheren, kan de catalogus worden gebruikt om andere acties te beperken tot een set tenantdatabases. Elastische query's kunnen bijvoorbeeld van de catalogus gebruikmaken om de set databases vast te stellen waarover query's voor rapportage over meerdere tenants worden gedistribueerd.

## <a name="elastic-database-client-library"></a>Elastic Database Client Library

In de Wingtip-voorbeeldtoepassing wordt de catalogus geïmplementeerd door de shard-beheerfuncties van [Elastic Database Client Library](elastic-database-client-library.md) (EDCL).  Met de bibliotheek kan een toepassing een shard-toewijzing maken, beheren en gebruiken die wordt opgeslagen in een database. In het Wingtip Tickets-voorbeeld wordt de catalogus opgeslagen in de database van de *tenantcatalogus*.  De shard wijst een tenantsleutel toe aan de shard (database) waarin de gegevens van de tenant zijn opgeslagen.  Met EDCL-functies wordt een *globale shard-toewijzing* beheerd die is opgeslagen in tabellen in de database van de *tenantcatalogus* en een *lokale shard-toewijzing* die in elke shard is opgeslagen.

EDCL-functies kunnen worden gebruikt vanuit toepassingen of PowerShell-scripts om de vermeldingen in de shard-toewijzing te maken en beheren. Andere EDCL-functies kunnen worden gebruikt om de set shards op te halen of om verbinding te maken met de juiste database voor de opgegeven tenantsleutel.

> [!IMPORTANT]
> De gegevens in de catalogusdatabase of de lokale shard-toewijzing in de tenantdatabases mogen niet rechtstreeks worden bewerkt. Rechtstreekse updates worden niet ondersteund vanwege het hoge risico op beschadiging van gegevens. Bewerk in plaats daarvan de toewijzingsgegevens met behulp van EDCL-API's.

## <a name="tenant-provisioning"></a>Inrichten van tenants

Voor elke tenant is een nieuwe Azure-resourcegroep vereist. Deze moet worden gemaakt voordat er resources in kunnen worden ingericht. Zodra de resourcegroep bestaat, kunt u een Azure-resourcebeheersjabloon gebruiken om de toepassingsonderdelen en de database te implementeren en vervolgens de databaseverbinding te configureren. Voor het initialiseren van het databaseschema, kan de sjabloon een BACPAC-bestand importeren.  De database kan ook worden gemaakt als een kopie van een 'sjabloon'-database.  De database wordt vervolgens verder bijgewerkt met de initiële locatiegegevens en geregistreerd in de catalogus.

## <a name="tutorial"></a>Zelfstudie

In deze zelfstudie leert u het volgende:

* Een catalogus inrichten
* De voorbeeldtenantdatabases registreren die u eerder in de catalogus hebt geïmplementeerd
* Een extra tenant inrichten en registreren in de catalogus

Er wordt een Azure Resource Manager-sjabloon gebruikt om de toepassing te implementeren en te configureren, de tenantdatabase te maken en vervolgens een BACPAC-bestand te importeren om deze te initialiseren. De aanvraag voor het importeren kan enkele minuten duren voordat deze wordt uitgevoerd.

Aan het einde van deze zelfstudie beschikt u over een set zelfstandige tenanttoepassingen, waarbij elke database in de catalogus wordt geregistreerd.

## <a name="prerequisites"></a>Vereisten

U kunt deze zelfstudie alleen voltooien als aan de volgende vereisten wordt voldaan:

* Azure PowerShell is geïnstalleerd. Zie [Aan de slag met Azure PowerShell](/powershell/azure/get-started-azureps) voor meer informatie.
* De drie voorbeeldtenant-apps worden geïmplementeerd. Zie [De zelfstandige SaaS-toepassing Wingtip Tickets implementeren en verkennen](./saas-standaloneapp-get-started-deploy.md) om deze apps binnen vijf minuten te implementeren.

## <a name="provision-the-catalog"></a>De catalogus inrichten

In deze taak leert u hoe u de catalogus inricht die wordt gebruikt voor het registreren van alle tenantdatabases. U gaat het volgende doen:

* **De catalogusdatabase inrichten** met behulp van een Azure-resourcebeheersjabloon. De database wordt geïnitialiseerd door een BACPAC-bestand te importeren.
* **Registreer de voorbeeldtenant-apps** die u eerder hebt geïmplementeerd.  Elke tenant wordt geregistreerd met behulp van een sleutel die is gemaakt op basis van een hash van de tenantnaam.  De naam van de tenant wordt ook opgeslagen in een uitbreidingstabel in de catalogus.

1. Open *...\Learning Modules\UserConfig.psm* in PowerShell ISE en werk de waarde **\<user\>** bij naar de waarde die u hebt gebruikt bij het implementeren van de drie voorbeeldtoepassingen.  **Sla het bestand op**.
1. Open *...\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* in PowerShell ISE en stel **$Scenario in op 1**. Implementeer de tenantcatalogus en registreer de vooraf gedefinieerde tenants.

1. Voeg een onderbrekingspunt toe door de cursor ergens op de regel met `& $PSScriptRoot\New-Catalog.ps1` te plaatsen en druk op **F9**.

    ![een onderbrekingspunt voor tracering instellen](./media/saas-standaloneapp-provision-and-catalog/breakpoint.png)

1. Voer het script uit door op **F5** te drukken.
1.  Wanneer de uitvoering van het script stopt bij het onderbrekingspunt, drukt u op **F11** om het New-Catalog.ps1-script stapsgewijs uit te voeren.
1.  Bekijk de uitvoering van het script via opties F10 en F11 van het menu Fouten opsporen om aangeroepen functies over te slaan of stapsgewijs uit te voeren.
    *   Zie [Tips voor het werken met en opsporen van fouten in PowerShell-scripts](/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise) voor meer informatie over foutopsporing in PowerShell-scripts.

Zodra het script is voltooid, bestaat de catalogus en worden alle tenantvoorbeelden geregistreerd.

Bekijk nu de resources die u hebt gemaakt.

1. Open [Azure Portal](https://portal.azure.com/) en blader naar de resourcegroepen.  Open de resourcegroep **wingtip-sa-catalog-\<user\>** en noteer de catalogusserver en de database.
1. Open de database in de portal en selecteer *Data Explorer* in het menu aan de linkerkant.  Klik op de aanmeldingsopdracht en voer het wachtwoord, **P\@ssword1**, in.


1. Bekijk het schema van de *tenantcatalogusdatabase*.
   * De objecten in het `__ShardManagement`-schema worden allemaal verstrekt door Elastic Database Client Library.
   * De `Tenants`-tabel en de `TenantsExtended`-weergave zijn uitbreidingen die in het voorbeeld zijn toegevoegd, en laten zien hoe u de catalogus kunt uitbreiden voor extra waarde.
1. Voer de query `SELECT * FROM dbo.TenantsExtended` uit.

   ![data explorer](./media/saas-standaloneapp-provision-and-catalog/data-explorer-tenantsextended.png)

    Als alternatief voor het gebruik van Data Explorer kunt u vanuit SQL Server Management Studio verbinding maken met de database. Hiertoe maakt u verbinding met de server wingtip-


    U mag de gegevens niet rechtstreeks in de catalogus bewerken - gebruik altijd de shard-beheer-API's.

## <a name="provision-a-new-tenant-application"></a>Een nieuwe tenanttoepassing inrichten

In deze taak leert u hoe u één tenanttoepassing kunt inrichten. U gaat het volgende doen:

* **Maak een nieuwe resourcegroep** voor de tenant.
* **Richt de toepassing en database** in de nieuwe resourcegroep in met behulp van een Azure-resourcebeheersjabloon.  Deze actie omvat het initialiseren van de database met algemene schema- en referentiegegevens door een BACPAC-bestand te importeren.
* **Initialiseer de database met basisinformatie over de tenant**. Deze actie omvat het opgeven van het locatietype, op basis waarvan de foto wordt gemaakt die wordt gebruikt als achtergrond van de evenementenwebsite.
* **Registreer de database in de catalogusdatabase**.

1. Open *...\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* in PowerShell ISE en stel **$Scenario in op 2**. Implementeer de tenantcatalogus en registreer de vooraf gedefinieerde tenants

1. Voeg een onderbrekingspunt aan het script toe door de cursor ergens op regel 49 (die met `& $PSScriptRoot\New-TenantApp.ps1`) te plaatsen en druk op **F9**.
1. Voer het script uit door op **F5** te drukken.
1.  Wanneer de uitvoering van het script stopt bij het onderbrekingspunt, drukt u op **F11** om het New-Catalog.ps1-script stapsgewijs uit te voeren.
1.  Bekijk de uitvoering van het script via opties F10 en F11 van het menu Fouten opsporen om aangeroepen functies over te slaan of stapsgewijs uit te voeren.

Nadat de tenant is ingericht, wordt de evenementenwebsite van de nieuwe tenant geopend.

   ![red maple racing](./media/saas-standaloneapp-provision-and-catalog/redmapleracing.png)

U kunt de zojuist gemaakte resources in Azure Portal inspecteren.

   ![resources voor red maple racing](./media/saas-standaloneapp-provision-and-catalog/redmapleracing-resources.png)


## <a name="to-stop-billing-delete-resource-groups"></a>Verwijder resourcegroepen om de facturering te stoppen

Wanneer u klaar bent met het inspecteren van het voorbeeld, verwijdert u alle resourcegroepen die u hebt gemaakt om de facturering hiervoor te stoppen.

## <a name="additional-resources"></a>Aanvullende bronnen

- Meer informatie over SaaS-toepassingen met meerdere tenants vindt u in [Ontwerppatronen voor SaaS-toepassingen met meerdere tenants](saas-tenancy-app-design-patterns.md).

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * De zelfstandige SaaS-app Wingtip Tickets implementeren.
> * Informatie over de servers en databases die deel uitmaken van de app.
> * Voorbeeldresources verwijderen om gerelateerde facturering te stoppen.

U kunt zien hoe de catalogus wordt gebruikt voor het ondersteunen van diverse tenant-overschrijdende scenario's met behulp van de database-per-tenantversie van de [SaaS-toepassing Wingtip Tickets](./saas-dbpertenant-wingtip-app-overview.md).