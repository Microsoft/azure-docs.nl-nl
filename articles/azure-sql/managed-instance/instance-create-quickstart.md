---
title: 'Quick Start: een door Azure SQL beheerd exemplaar maken (Portal)'
description: Maak in deze quickstart een beheerd exemplaar, netwerkomgeving en client-VM voor toegang met behulp van de Microsoft Azure-portal.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: danimir
ms.author: danil
ms.reviewer: sstein
ms.date: 1/29/2021
ms.openlocfilehash: d356cad1b4754875574e19be732fdf6481c61e22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101691209"
---
# <a name="quickstart-create-an-azure-sql-managed-instance"></a>Quickstart: Een Azure SQL Managed Instance maken
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

In deze quickstart leert u hoe u een [met Azure SQL beheerd exemplaar](sql-managed-instance-paas-overview.md) maakt in de Azure-portal.

> [!IMPORTANT]
> Raadpleeg [Ondersteunde regio’s](resource-limits.md#supported-regions) en [Ondersteunde abonnementstypen](resource-limits.md#supported-subscription-types) voor de beperkingen.

## <a name="create-an-azure-sql-managed-instance"></a>Een Azure SQL Managed Instance maken

Voer de volgende stappen uit om een SQL Managed instance te maken: 

### <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Als u nog geen abonnement op Azure hebt, [maak dan een gratis account](https://azure.microsoft.com/free/).

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
1. Selecteer **Azure SQL** in het linkermenu van de Microsoft Azure-portal. Als **Azure SQL** niet in de lijst staat, selecteert u **Alle services** en voert u vervolgens **Azure SQL** in het zoekvak in.
1. Selecteer **+ Toevoegen** om de pagina **SQL-implementatieoptie selecteren** te openen. U kunt aanvullende informatie over een met Azure SQL beheerd exemplaar bekijken door **Details weergeven** te selecteren op de tegel **SQL Managed Instance**.
1. Selecteer **Maken**.

   ![Een beheerd exemplaar maken](./media/instance-create-quickstart/create-azure-sql-managed-instance.png)

4. Gebruik de tabbladen in het inrichtingsformulier **Een met Azure SQL beheerd exemplaar maken** om vereiste en optionele informatie toe te voegen. In de volgende secties worden deze tabbladen in meer detail beschreven.

### <a name="basics-tab"></a>Tabblad Basisbeginselen

- Vul verplichte gegevens in die vereist zijn op het tabblad **Basisbeginselen**. Dit is een set informatie die minimaal is vereist voor het inrichten van een met SQL beheerd exemplaar.

   ![Tabblad Basisbeginselen voor het maken van een met SQL beheerd exemplaar](./media/instance-create-quickstart/azure-sql-managed-instance-create-tab-basics.png)

   Gebruik de onderstaande tabel als referentie voor informatie die op dit tabblad is vereist.

   | Instelling| Voorgestelde waarde | Beschrijving |
   | ------ | --------------- | ----------- |
   | **Abonnement** | Uw abonnement. | Een abonnement met toestemming voor het maken van nieuwe resources. |
   | **Resourcegroep** | een nieuwe of bestaande resourcegroep.|Zie [Naming conventions](/azure/architecture/best-practices/resource-naming) (Naamgevingsconventies) voor geldige resourcegroepnamen.|
   | **Naam van het beheerde exemplaar** | Een geldige naam.|Zie [Naming conventions](/azure/architecture/best-practices/resource-naming) (Naamgevingsconventies) voor geldige namen.|
   | **Regio** |De regio waarin u het beheerde exemplaar wilt maken.|Zie [Azure-regio's](https://azure.microsoft.com/regions/) voor informatie over regio's.|
   | **Beheerdersaanmeldgegevens voor het beheerde exemplaar** | Een geldige gebruikersnaam. | Zie [Naming conventions](/azure/architecture/best-practices/resource-naming) (Naamgevingsconventies) voor geldige namen. Maak geen gebruik van 'serverbeheerder' aangezien dit een rol is die op serverniveau is gereserveerd.|
   | **Wachtwoord** | Een geldig wachtwoord.| Het wachtwoord moet minstens 16 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|

- Selecteer **Beheerd exemplaar configureren** om de grootte van reken- en opslagresources te bepalen en de prijscategorieën te controleren. Gebruik de schuifregelaars of tekstvakken om de hoeveelheid opslagruimte en het aantal virtuele kernen op te geven. Wanneer u klaar bent, selecteert u **Toepassen** om uw selectie op te slaan. 

   ![Formulier voor beheerd exemplaar](./media/instance-create-quickstart/azure-sql-managed-instance-create-tab-configure-performance.png)

| Instelling| Voorgestelde waarde | Beschrijving |
| ------ | --------------- | ----------- |
| **Servicelaag** | Selecteer een van de opties. | Selecteer op basis van uw scenario een van de volgende opties: </br> <ul><li>**Algemeen**: voor de meeste productiewerk belastingen en de standaard optie.</li><li>**Bedrijfskritiek**: ontworpen voor workloads met lage latentie met hoge tolerantie voor fouten en snelle failovers.</li></ul><BR>Zie voor meer informatie [Azure SQL database en Azure SQL Managed instance service-lagen](../../azure-sql/database/service-tiers-general-purpose-business-critical.md) en Lees [overzicht van de resource limieten voor Azure SQL Managed instance](../../azure-sql/managed-instance/resource-limits.md).|
| **Hardware genereren** | Selecteer een van de opties. | Het genereren van de hardware definieert doorgaans de reken-en geheugen limieten en andere kenmerken die van invloed zijn op de prestaties van de werk belasting. **GEN5** is de standaard instelling.|
| **vCore Compute model** | Selecteer een optie. | vCores vertegenwoordigen een exacte hoeveelheid reken resources die altijd worden ingericht voor uw werk belasting. **Acht vCores** is de standaard instelling.|
| **Opslag in GB** | Selecteer een optie. | Opslag grootte in GB, selecteert u op basis van de verwachte gegevens grootte. Zie [migratie Overview: SQL Server naar SQL Managed instance](../../azure-sql/migration-guides/managed-instance/sql-server-to-managed-instance-overview.md)(Engelstalig) als u bestaande gegevens van on-premises of op verschillende Cloud platforms wilt migreren.|
| **Azure Hybrid Benefit** | Schakel de optie in, indien van toepassing. | Voor het gebruik van een bestaande licentie voor Azure. Zie [Azure Hybrid Benefit-Azure SQL Database & SQL Managed instance](../../azure-sql/azure-hybrid-benefit.md)(Engelstalig) voor meer informatie. |
| **Opslag redundantie van back-ups** | Selecteer **geografisch redundante back-upopslag**. | Opslag redundantie binnen Azure voor back-upopslag. Houd er rekening mee dat deze waarde niet later kan worden gewijzigd. Geografisch redundante back-upopslag is standaard en aanbevolen, hoewel zone en lokale redundantie zijn toegestaan voor meer kosten en gegevens locatie met één regio. Zie [back-upopslag redundantie](../database/automated-backups-overview.md?tabs=managed-instance#backup-storage-redundancy)voor meer informatie.|


- Als u uw keuzes wilt bekijken voordat u een met SQL beheerd exemplaar maakt, selecteert u **Controleren en maken**. U kunt ook de netwerkopties configureren door te selecteren **Volgende: Netwerken**.

### <a name="networking-tab"></a>Tabblad Netwerken

- Vul optionele informatie in op het tabblad **Netwerken**. Als u deze informatie weglaat, worden de standaardinstellingen toegepast door de portal.

   ![Tabblad 'Netwerken' voor het maken van een beheerd exemplaar](./media/instance-create-quickstart/azure-sql-managed-instance-create-tab-networking.png)

   Gebruik de onderstaande tabel als referentie voor informatie die op dit tabblad is vereist.

   | Instelling| Voorgestelde waarde | Beschrijving |
   | ------ | --------------- | ----------- |
   | **Virtueel netwerk** | Selecteer **Nieuw virtueel netwerk maken** of een geldig virtueel netwerk en subnet.| Als een netwerk/subnet niet beschikbaar is, moet het worden [gewijzigd om te voldoen aan de netwerkvereisten](vnet-existing-add-subnet.md) voordat u het als doel voor het nieuwe beheerde exemplaar kunt selecteren. Zie [Een virtueel netwerk configureren voor een met SQL beheerd exemplaar](connectivity-architecture-overview.md) voor informatie over de vereisten voor het configureren van de netwerkomgeving voor een met SQL beheerd exemplaar. |
   | **Verbindingstype** | Kies tussen het verbindingstype Proxy of Omleiding.|Zie [Verbindingstype voor Azure SQL Managed Instance](../database/connectivity-architecture.md#connection-policy) voor meer informatie over verbindingstypen.|
   | **Openbaar eindpunt**  | Selecteer **Uitschakelen**. | Als u wilt dat een beheerd exemplaar toegankelijk is via het eindpunt voor openbare gegevens, moet u deze optie inschakelen. | 
   | **Toegang toestaan vanaf** (als **Openbaar eindpunt** is ingeschakeld) | **Geen toegang** selecteren  |In de portal kunt u een beveiligingsgroep configureren met een openbaar eindpunt. </br> </br> Selecteer op basis van uw scenario een van de volgende opties: </br> <ul> <li>**Azure-services**: We raden deze optie aan wanneer u verbinding maakt vanuit Power BI of een andere service voor meerdere tenants. </li> <li> **Internet**: Gebruik dit om te testen wanneer u snel een beheerd exemplaar wilt maken. Deze optie wordt niet aanbevolen in productieomgevingen. </li> <li> **Geen toegang**: Met deze optie maakt u de beveiligingsregel **Weigeren**. Wijzig deze regel om een beheerd exemplaar toegankelijk te maken via een openbaar eindpunt. </li> </ul> </br> Zie [Een met Azure SQL beheerd exemplaar veilig gebruiken met een openbaar eindpunt](public-endpoint-overview.md) voor meer informatie over de beveiliging van een openbaar eindpunt.|

- Als u uw keuzes wilt bekijken voordat u een beheerd exemplaar maakt, kunt u **Bekijken en maken** selecteren. U kunt ook meer aangepaste instellingen configureren door  **te selecteren. We gaan nu verder met: Aanvullende instellingen**.


### <a name="additional-settings"></a>Aanvullende instellingen

- Vul optionele informatie in op het tabblad **Aanvullende instellingen**. Als u deze informatie weglaat, worden de standaardinstellingen toegepast door de portal.

   ![Tabblad 'Aanvullende instellingen' voor het maken van een beheerd exemplaar](./media/instance-create-quickstart/azure-sql-managed-instance-create-tab-additional-settings.png)

   Gebruik de onderstaande tabel als referentie voor informatie die op dit tabblad is vereist.

   | Instelling| Voorgestelde waarde | Beschrijving |
   | ------ | --------------- | ----------- |
   | **Sortering** | Kies de sortering die u wilt gebruiken voor uw beheerde exemplaar. Als u SQL Server-databases wilt migreren, moet u de bronsortering controleren met `SELECT SERVERPROPERTY(N'Collation')` en die waarde gebruiken.| Raadpleeg [De serversortering instellen of wijzigen](/sql/relational-databases/collations/set-or-change-the-server-collation) voor informatie over sorteringen.|   
   | **Tijdzone** | Selecteer de tijdzone die het beheerde exemplaar moet gebruiken.|Zie [Tijdzones](timezones-overview.md) voor meer informatie.|
   | **Gebruiken als secundaire failover** | Selecteer **Ja**. | Schakel deze optie in als u het beheerde exemplaar wilt gebruiken als een secundaire failovergroep.|
   | **Primair met SQL beheerd exemplaar** (als **Gebruiken als secundaire failover** is ingesteld op **Ja**) | Kies een bestaand primair beheerd exemplaar dat wordt toegevoegd aan dezelfde DNS-zone met het beheerde exemplaar dat u nu maakt. | Met deze stap wordt de configuratie van de failovergroep na het maken ingeschakeld. Zie [Zelfstudie: een beheerd exemplaar aan een failovergroep toevoegen](failover-group-add-instance-tutorial.md).|

- Als u uw keuzes wilt bekijken voordat u een beheerd exemplaar maakt, kunt u **Bekijken en maken** selecteren. U kunt ook Azure-Tags configureren door volgende te selecteren **: Tags** (aanbevolen).

### <a name="tags"></a>Tags

- Tags toevoegen aan resources in uw Azure Resource Manager-sjabloon (ARM-sjabloon). [Tags](../../azure-resource-manager/management/tag-resources.md) zijn een hulpmiddel bij het logisch ordenen van uw resources. De label waarden worden weer gegeven in kosten rapporten en bieden andere beheer activiteiten per tag. 

- U kunt het beste uw nieuwe SQL Managed instance met de tag owner identificeren om te bepalen wie is gemaakt, en het label Environment om te bepalen of dit systeem productie, ontwikkeling, enz. is. Zie [uw naamgevings strategie ontwikkelen voor Azure-resources](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)voor meer informatie.
 
- Selecteer **controleren + maken** om door te gaan.

## <a name="review--create"></a>Beoordelen en maken

1. Als u uw keuzes wilt bekijken voordat u een beheerd exemplaar maakt, kunt u **Bekijken en maken** selecteren.

   ![Tabblad voor het bekijken en maken van een beheerd exemplaar](./media/instance-create-quickstart/azure-sql-managed-instance-create-tab-review-create.png)

1. Selecteer **Maken** om het inrichten van het beheerde exemplaar te starten.

> [!IMPORTANT]
> Het implementeren van een beheerd exemplaar is een langdurende bewerking. De implementatie van het eerste exemplaar in het subnet duurt doorgaans veel langer dan de implementatie in een subnet met bestaande beheerde exemplaren. Zie [overzicht van Azure SQL Managed instance Management-bewerkingen](management-operations-overview.md#duration)voor gemiddelde inrichtings tijden.

## <a name="monitor-deployment-progress"></a>Implementatievoortgang bewaken

1. Selecteer het pictogram **Meldingen** om de status van de implementatie te bekijken.

   ![Implementatievoortgang van een SQL Managed Instance-implementatie](./media/instance-create-quickstart/azure-sql-managed-instance-create-deployment-in-progress.png)

1. Selecteer **Implementatie in uitvoering** in de melding om het SQL Managed Instance-venster te openen en de implementatievoortgang verder te bewaken. 

> [!TIP]
> - Als u de webbrowser hebt gesloten of van het scherm voortgang van de implementatie hebt verwijderd, kunt u de inrichtings bewerking bewaken via de pagina **overzicht** van het beheerde exemplaar of via Power shell of de Azure cli. Zie [bewerkingen controleren](management-operations-monitor.md#monitor-operations)voor meer informatie. 
> - U kunt het inrichtings proces annuleren via Azure Portal, of via Power shell of via de Azure CLI of met behulp van de REST API. Zie [Azure SQL Managed instance Management-bewerkingen annuleren](management-operations-cancel.md).

> [!IMPORTANT]
> - Het starten van het maken van een SQL Managed Instance kan worden vertraagd wanneer er andere intensieve bewerkingen actief zijn, zoals het uitvoeren van grote herstel- of schaalbewerkingen op andere beheerde exemplaren in hetzelfde subnet. Zie [Management operations cross-impact](management-operations-overview.md#management-operations-cross-impact) (Wederzijdse impact van beheerbewerkingen) voor meer informatie.
> - Als u de status van het maken van beheerde exemplaren wilt ophalen, moet u **leesrechten hebben** voor de resourcegroep. Als u deze machtigingen niet hebt of als deze zijn ingetrokken tijdens het maken van het beheerde exemplaar, is het met SQL beheerd exemplaar mogelijk niet zichtbaar in de lijst met implementaties voor resourcegroepen.
>

## <a name="view-resources-created"></a>Gemaakte resources weergeven

Na een geslaagde implementatie van het beheerde exemplaar kunt u de resources die zijn aangemaakt, als volgt bekijken:

1. Open de resourcegroep voor uw beheerde exemplaar. 

   ![SQL Managed Instance-resources](./media/instance-create-quickstart/azure-sql-managed-instance-resources.png)

## <a name="view-and-fine-tune-network-settings"></a>Netwerkinstellingen weergeven en aanpassen

Als u de netwerkinstellingen wilt aanpassen, bekijkt u het volgende:

1. Selecteer in de lijst met resources de route tabel om het door de gebruiker gedefinieerde route tabel (UDR)-object te controleren dat is gemaakt.

2. Bekijk de vermeldingen in de routetabel om verkeer vanuit en binnen het virtuele netwerk van het met SQL beheerde exemplaar door te sturen. Als u de route tabel hand matig maakt of configureert, maakt u deze vermeldingen in de route tabel van SQL Managed instance.

   ![Vermelding voor een SQL Managed Instance-subnet naar lokaal](./media/instance-create-quickstart/azure-sql-managed-instance-route-table-user-defined-route.png)

    Als u routes wilt wijzigen of toevoegen, opent u de **routes** in de instellingen van de route tabel.

3. Ga terug naar de resource groep en selecteer het NSG-object (netwerk beveiligings groep) dat is gemaakt.

4. Bekijk de inkomende en uitgaande beveiligingsregels. 

   ![Beveiligingsregels](./media/instance-create-quickstart/azure-sql-managed-instance-security-rules.png)

    Als u regels wilt wijzigen of toevoegen, opent u de regels voor **binnenkomende beveiliging** en **uitgaande beveiliging** in de instellingen voor de netwerk beveiligings groep.

> [!IMPORTANT]
> Als u een openbaar eindpunt hebt geconfigureerd voor het met SQL beheerde exemplaar, moet u poorten openen voor netwerkverkeer dat verbindingen met SQL Managed Instance toestaat vanaf het openbaar internet. Zie [Configure a Public endpoint for SQL Managed instance](public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group)(Engelstalig) voor meer informatie.
>

## <a name="retrieve-connection-details-to-sql-managed-instance"></a>Details over de verbinding met SQL Managed Instance ophalen

Als u verbinding wilt maken met SQL Managed Instance, voert u de volgende stappen uit om de hostnaam en de FQDN (Fully Qualified Domain Name) op te halen:

1. Ga terug naar de resource groep en selecteer het SQL Managed instance-object dat is gemaakt.

2. Ga naar het tabblad **Overzicht** en zoek de eigenschap **Host**. Kopieer de hostnaam naar het klem bord voor het beheerde exemplaar voor gebruik in de volgende Snelstartgids door te klikken op de knop **kopiëren naar klem bord** .

   ![Hostnaam](./media/instance-create-quickstart/azure-sql-managed-instance-host-name.png)

   De gekopieerde waarde vertegenwoordigt een FQDN die kan worden gebruikt om verbinding te maken met SQL Managed Instance. Deze is vergelijkbaar met het volgende voorbeeld: *uw_hostnaam.a1b2c3d4e5f6.database.windows.net*.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het verbinding maken met SQL Managed Instance:
- Zie [Uw toepassingen verbinden met SQL Managed Instance](connect-application-instance.md) voor een overzicht van de verbindingsopties voor toepassingen.
- Zie [Een verbinding vanaf een virtuele Azure-machine configureren](connect-vm-instance-configure.md) voor een quickstart over verbinding maken met SQL Managed Instance vanaf een virtuele Azure-machine.
- Zie [Een punt-naar-site-verbinding configureren](point-to-site-p2s-configure.md) voor een quickstart over verbinding maken met SQL Managed Instance vanaf een on-premises clientcomputer met behulp van een punt-naar-site-verbinding.

Volg de volgende stappen als u een bestaande SQL Server-database vanaf on-premises wilt herstellen naar een SQL Managed Instance: 
- Gebruik de [Azure Database Migration Service voor migratie](../../dms/tutorial-sql-server-to-managed-instance.md) om te herstellen vanuit een back-upbestand van de database. 
- Gebruik de [opdracht T-SQL RESTORE](restore-sample-database-quickstart.md) om te herstellen vanuit een back-upbestand van een database.

Zie [Azure SQL Managed Instance bewaken met Azure SQL-analyse](../../azure-monitor/insights/azure-sql.md) voor geavanceerde bewaking van de databaseprestaties in SQL Managed Instance, met ingebouwde intelligentie voor het oplossen van problemen.