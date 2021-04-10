---
title: Over Azure Migrate
description: Meer informatie over de Azure Migrate-service.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: overview
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: fd8806a02e48481042eb756a0a077d801583cd2e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104870769"
---
# <a name="about-azure-migrate"></a>Over Azure Migrate

In dit artikel vindt u een kort overzicht van de service Azure Migrate.

Azure Migrate is een gecentraliseerde hub voor het beoordelen en migreren naar on-premises servers, infrastructuur, toepassingen en gegevens in Azure. Deze biedt het volgende:

- **Geïntegreerd migratieplatform**: Eén portal om uw migratie naar Azure te starten, uit te voeren en te volgen.
- **Aantal hulpprogramma's**: Een reeks hulpprogramma's voor evaluatie en migratie. Azure Migrate-hulpprogram ma's zijn onder andere Azure Migrate: detectie en evaluatie en Azure Migrate: Server migratie. Azure Migrate integreert ook met andere Azure-services en -hulpprogramma's, en met aanbiedingen van onafhankelijke softwareleveranciers (ISV’s).
- **Evaluatie en migratie**: In de Azure Migrate hub kunt u het volgende evalueren en migreren:
    - **Windows, Linux en SQL Server**: Evalueer on-premises servers met inbegrip van SQL Server instanties en migreer ze naar Azure virtual machines of de Azure VMware-oplossing (AVS) (preview).
    - **Databases**: Evalueer on-premises databases en migreer deze naar Azure SQL Database of SQL Managed Instance.
    - **Webtoepassingen**: Evalueer on-premises webtoepassingen en migreer deze naar Azure App Service met behulp van de Azure App Service Migration Assistant.
    - **Virtuele bureaubladen**: Evalueer uw on-premises VDI (Virtual Desktop Infrastructure) en migreer deze naar Windows Virtual Desktop in Azure.
    - **Gegevens**: Migreer grote hoeveelheden gegevens snel en tegen aanvaardbare kosten naar Azure met behulp van Azure Data Box-producten.

## <a name="integrated-tools"></a>Geïntegreerde hulpprogramma's

De Azure Migrate-hub bevat deze hulpprogramma's:

**Hulpprogramma** | **Evalueren en migreren** | **Details**
--- | --- | ---
**Azure Migrate: detectie en evaluatie** | Servers met SQL detecteren en beoordelen | Detecteer en evalueer on-premises VMware-VM's, Hyper-V-VM's en fysieke servers in voorbereiding op migratie naar Azure.
**Azure Migrate: Server Migration** | Servers migreren | Migreer VMware-Vm's, Hyper-V-Vm's, fysieke servers, andere gevirtualiseerde servers en open bare Cloud-Vm's naar Azure.
**Gegevensmigratieassistent** | Evalueer SQL Server-databases voor migratie naar Azure SQL Database, Azure SQL Managed Instance of Azure-VM's waarop SQL Server wordt uitgevoerd. | Data Migration Assistant is een zelfstandig hulp programma voor het beoordelen van SQL Severs.It helpt mogelijke problemen te voor komen met het blok keren van de migratie. De assistent identificeert niet-ondersteunde functies, nieuwe functies die na de migratie van pas kunnen komen en het juiste traject voor databasemigratie. [Meer informatie](/sql/dma/dma-overview).
**Azure Database Migration Service** | On-premises data bases migreren naar Azure-Vm's waarop SQL Server, Azure SQL Database of door SQL beheerde exemplaren worden uitgevoerd | [Lees meer](../dms/dms-overview.md) over Database Migration Service.
**Movere** | Servers evalueren | [Lees meer](#movere) over Movere.
**Web App Migration Assistant** | Hiermee kunt u on-premises web-apps beoordelen en migreren naar Azure. |  Gebruik Azure App Service Migration Assistant om on-premises websites te evalueren voor migratie naar Azure App Service.<br/><br/> Gebruik Migration Assistant om .NET- en PHP-web-apps te migreren naar Azure. [Lees meer](https://appmigration.microsoft.com/) over Azure App Service Migration Assistant.
**Azure Data Box** | Offline gegevens migreren | Gebruik Azure Data Box-producten om grote hoeveelheden offline gegevens naar Azure te verplaatsen. [Meer informatie](../databox/index.yml).

> [!NOTE]
> Als u zich in Azure Government bevindt, kunnen externe, geïntegreerde hulpprogram ma's en ISV-aanbiedingen geen gegevens verzenden naar Azure Migrate. U kunt hulpprogramma's onafhankelijk van elkaar gebruiken.

## <a name="isv-integration"></a>ISV-integratie

Azure Migrate kan worden geïntegreerd met verschillende ISV-aanbiedingen. 

**ISV**    | **Functie**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Servers migreren.
[Cloudamize](https://www.cloudamize.com/platform) | Servers evalueren.
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | Servers evalueren en migreren.
[Device42](https://docs.device42.com/) | Servers evalueren.
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | VDI evalueren.
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | Servers migreren.
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Servers evalueren.
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Servers en databases evalueren.
[Zerto](https://go.microsoft.com/fwlink/?linkid=2152102) | Servers migreren.

## <a name="azure-migrate-discovery-and-assessment-tool"></a>Azure Migrate: hulp programma voor detectie en evaluatie

De Azure Migrate: het hulp programma detectie en evaluatie detecteert en evalueert on-premises VMware-Vm's, Hyper-V-Vm's en fysieke servers voor migratie naar Azure. 

Het hulpprogramma doet het volgende:

- **Azure Readiness**: evalueert of on-premises servers gereed zijn voor migratie naar Azure.
- **Azure-grootte**: Hiermee wordt de grootte van Azure-Vm's/Azure SQL-configuratie/aantal Azure VMware-oplossings knooppunten geschat na de migratie.
- **Azure-kostenraming**: Schat de kosten voor het uitvoeren van on-premises servers in Azure.
- **Afhankelijkheidsanalyse**: Identificeert afhankelijkheden tussen servers en optimalisatiestrategieën voor het verplaatsen van gerelateerde servers naar Azure. Meer informatie over detectie en evaluatie met [afhankelijkheids analyse](concepts-dependency-visualization.md).

Detectie en evaluatie maakt gebruik van een licht gewicht [Azure migrate apparaat](migrate-appliance.md) dat u on-premises implementeert.

- Het apparaat wordt uitgevoerd op een VM of op een fysieke server. U kunt het apparaat eenvoudig installeren met behulp van een gedownloade sjabloon.
- Het apparaat detecteert on-premises servers. Daarnaast verzendt het voortdurend de meta gegevens en prestatie gegevens van de server naar Azure Migrate.
- De detectie van apparaten verloopt zonder agent. Er is niets geïnstalleerd op gedetecteerde servers.
- Na de detectie van het apparaat kunt u gedetecteerde servers in groepen verzamelen en evaluaties uitvoeren voor elke groep.


## <a name="azure-migrate-server-migration-tool"></a>Azure Migrate: Hulpprogramma voor de migratie van servers

De Azure Migrate: hulp programma voor Server Migratie helpt bij het migreren van servers naar Azure:

**Migreren** | **Details**
--- | ---
On-premises VMware VM's | Migreer VM's naar Azure via migratie met of zonder agent.<br/><br/> Voor migratie zonder agent gebruikt server migratie hetzelfde Azure Migrate apparaat dat ook kan worden gebruikt door detectie en evaluatie voor detectie en evaluatie van virtuele VMware-machines.<br/><br/> Voor migratie met agent maakt Server Migration gebruik van een replicatie-apparaat.
On-premises Hyper-V-VM's | Migreer VM’s naar Azure.<br/><br/> Voor de migratie maakt Server Migration gebruik van provideragents die op de Hyper-V-host zijn geïnstalleerd.
On-premises fysieke servers of servers die worden gehost op andere Clouds | U kunt fysieke servers migreren naar Azure. U kunt ook andere gevirtualiseerde servers en virtuele machines uit andere open bare Clouds migreren door ze te behandelen als fysieke servers voor het doel van de migratie. Voor de migratie maakt Server Migration gebruik van een replicatie-apparaat.


## <a name="selecting-assessment-and-migration-tools"></a>Hulpprogramma's voor evaluatie en migratie selecteren

In de Azure Migrate hub selecteert u het hulp programma dat u wilt gebruiken voor evaluatie of migratie en voegt u dit toe aan een project. Als u een ISV-hulpprogramma of Movere toevoegt:

- Als u aan de slag wilt, moet u een licentie aanvragen of u registreren voor een gratis proefversie door de instructies van het hulpprogramma te volgen. Elke ISV of elk hulpprogramma geeft aan welke licentie nodig is.
- Elk hulpprogramma heeft een optie om verbinding te maken met Azure Migrate. Volg de instructies van het hulpprogramma om verbinding te maken.
- Volg de migratie van alle hulpprogram ma's vanuit het project.

## <a name="movere"></a>Movere

Movere is een SaaS-platform (Software as a Service). Het verbetert de business intelligence door binnen een dag een nauwkeurig beeld te geven van volledige IT-omgevingen. Organisaties en ondernemingen groeien, veranderen en kiezen voor digitale optimalisatie. Tijdens deze processen zorgt Movere ervoor dat ze met het nodige vertrouwen hun omgevingen kunnen visualiseren en beheren, ongeacht het platform, de toepassing of geografie.

Microsoft heeft Movere [overgenomen](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) en het product wordt niet meer als zelfstandige oplossing aangeboden. Movere is beschikbaar via Microsoft Solution Assessment en het Microsoft Cloud Economics-programma. [Lees meer](https://www.movere.io) over Movere.

We raden u aan om ook eens te kijken naar Azure Migrate, onze ingebouwde migratieservice. Azure Migrate biedt een centrale hub om uw cloudmigratie te vereenvoudigen. De hub biedt uitgebreide ondersteuning voor werkbelastingen zoals fysieke en virtuele servers, databases en toepassingen. Met end-to-end zichtbaarheid kunt u de voortgang tijdens detectie, evaluatie en migratie eenvoudig volgen.

Zowel de hulpprogramma's van Azure als van partner-ISV's zijn ingebouwd, waardoor Azure Migrate een uitgebreid scala aan functies te bieden heeft, waaronder:

- Detectie van virtuele en fysieke servers.
- Bepalen van juiste grootte op basis van prestaties.
- Kostenplanning.
- Evaluaties op basis van import.
- Afhankelijkheidsanalyse van toepassingen zonder agent.

Als u op zoek bent naar deskundige hulp om aan de slag te gaan, kan Microsoft u verwijzen naar bekwaam [Azure Expert Managed Service Providers](https://azure.microsoft.com/partners) om u te helpen. Ga voor meer informatie naar de [Azure Migrate-website](https://azure.microsoft.com/services/azure-migrate/). 

## <a name="azure-migrate-versions"></a>Versies van Azure Migrate

Er zijn twee versies van de Azure Migrate-service.

- **Huidige versie**: gebruik deze versie om projecten te maken, on-premises servers te detecteren en beoordelingen en migraties te organiseren. [Lees meer](whats-new.md) over wat er nieuw is in deze versie.
- **Vorige versie**: de vorige versie van Azure migrate, ook wel klassiek Azure migrate genoemd, ondersteunt alleen de evaluatie van on-premises VMware-vm's. Klassieke Azure Migrate buiten gebruik worden gesteld in februari 2024. Na februari 2024 wordt de klassieke versie van Azure Migrate niet meer ondersteund en worden de meta gegevens van de inventaris in klassieke projecten verwijderd. U kunt projecten of onderdelen in de vorige versie niet upgraden naar de nieuwe versie. U moet [een nieuw project maken](create-manage-projects.md)en er [hulpprogram ma's voor de evaluatie en migratie aan toevoegen](./create-manage-projects.md) . Gebruik de zelfstudies om te begrijpen hoe u de beschikbare hulpprogramma’s voor evaluatie en migratie kunt gebruiken. Als u een Log Analytics werk ruimte aan een klassiek project hebt gekoppeld, kunt u deze koppelen aan een project van de huidige versie nadat u het klassieke project hebt verwijderd.

    Als u toegang wilt krijgen tot bestaande projecten in de Azure-portal, zoekt en selecteert u **Azure Migrate**. Het **Azure migrate** dash board heeft een melding en een koppeling voor toegang tot oude projecten.

## <a name="next-steps"></a>Volgende stappen

- Probeer onze zelfstudies om [VMware-VM's](./tutorial-discover-vmware.md), [Hyper-V-VM's](./tutorial-discover-hyper-v.md)of [fysieke servers](./tutorial-discover-physical.md) te evalueren.
- [Bekijk de veelgestelde vragen](resources-faq.md) over Azure Migrate.