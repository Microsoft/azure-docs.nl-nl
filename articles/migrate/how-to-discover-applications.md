---
title: Apps op on-premises servers detecteren met Azure Migrate
description: Meer informatie over het detecteren van apps, functies en onderdelen op on-premises servers met Azure Migrate server-evaluatie.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/10/2020
ms.openlocfilehash: 8266b585881546b37bbb21b82780ab26d85dada7
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048077"
---
# <a name="discover-installed-applications-roles-and-features-software-inventory-and-sql-server-instances-and-databases"></a>Geïnstalleerde toepassingen, functies en onderdelen (software-inventarisatie) en SQL Server instanties en data bases detecteren

In dit artikel wordt beschreven hoe u geïnstalleerde toepassingen, functies en onderdelen (software-inventarisatie) en SQL Server instanties en data bases op servers met in uw VMware-omgeving detecteert met behulp van Azure Migrate: Server Assessment.

Door software-inventarisatie uit te voeren, kunt u een pad voor de migratie naar Azure identificeren en aanpassen voor uw workloads. Software-inventarisatie gebruikt het Azure Migrate apparaat om detectie uit te voeren met behulp van Server referenties. Het is volledig zonder agent: er zijn geen agents geïnstalleerd op de servers om deze gegevens te verzamelen.

> [!NOTE]
> Software-inventarisatie is momenteel als preview beschikbaar voor servers die alleen worden uitgevoerd in de VMware-omgeving en is beperkt tot alleen detectie. Momenteel bieden we geen evaluatie op basis van toepassingen.<br/> Detectie en evaluatie van SQL Server instanties en data bases die worden uitgevoerd in uw VMware-omgeving is nu beschikbaar als preview-versie. Gebruik [**deze koppeling**](https://aka.ms/AzureMigrate/SQL) om een project te maken in **Australië-Oost** regio om deze functie uit te proberen. Als u al een project in Australië-oost hebt en u deze functie wilt uitproberen, moet u ervoor zorgen dat u deze [**vereisten**](how-to-discover-sql-existing-project.md) hebt voltooid op de portal.

## <a name="before-you-start"></a>Voordat u begint

- Zorg ervoor dat u [een Azure migrate-project hebt gemaakt](./create-manage-projects.md) met het Azure migrate: Server assessment tool is toegevoegd.
- Raadpleeg de [VMware-vereisten](migrate-support-matrix-vmware.md#vmware-requirements) om software-inventarisatie uit te voeren.
- Controleer de [vereisten voor apparaten](migrate-support-matrix-vmware.md#azure-migrate-appliance-requirements) voordat u het apparaat instelt.
- Bekijk [vereisten voor toepassings detectie](migrate-support-matrix-vmware.md#application-discovery-requirements) voordat de software-inventaris op servers wordt gestart.

## <a name="deploy-and-configure-the-azure-migrate-appliance"></a>Het Azure Migrate apparaat implementeren en configureren

1. [Bekijk](migrate-appliance.md#appliance---vmware) de vereisten voor het implementeren van het Azure migrate apparaat.
2. Bekijk de Azure-Url's die het apparaat nodig heeft voor toegang tot de [open bare](migrate-appliance.md#public-cloud-urls) en [overheids Clouds](migrate-appliance.md#government-cloud-urls).
3. [Bekijk gegevens](migrate-appliance.md#collected-data---vmware) die door het apparaat worden verzameld tijdens de detectie en evaluatie.
4. [Noteer](migrate-support-matrix-vmware.md#port-access-requirements) de toegangs vereisten voor poorten voor het apparaat.
5. [Implementeer het Azure migrate apparaat om de](how-to-set-up-appliance-vmware.md) detectie te starten. Als u het apparaat wilt implementeren, downloadt en importeert u een eicellen-sjabloon in VMware om een server te maken die wordt uitgevoerd in uw vCenter Server. Nadat u het apparaat hebt geïmplementeerd, moet u dit registreren bij het Azure Migrate-project en het configureren om de detectie te initiëren.
6. Wanneer u het apparaat configureert, moet u het volgende opgeven in de configuratie beheer van het apparaat:
    - De details van de vCenter Server waarmee u verbinding wilt maken.
    - vCenter Server het bereik van referenties voor het detecteren van de servers in uw VMware-omgeving.
    - Server referenties die domein-/Windows-(niet-domein)/Linux-referenties (niet-domein) kunnen zijn. Meer [informatie](add-server-credentials.md) over hoe u referenties kunt opgeven en hoe u deze kunt afhandelen.

## <a name="verify-permissions"></a>Machtigingen controleren

- U moet [een vCenter Server alleen-lezen-account maken](./tutorial-discover-vmware.md#prepare-vmware) voor detectie en evaluatie. Voor de alleen-lezen-account zijn bevoegdheden nodig die zijn ingeschakeld voor **virtual machines**  >  -**gast bewerkingen**, om te kunnen communiceren met de servers om software-inventarisatie uit te voeren.
- U kunt meerdere domein-en niet-domein referenties (Windows/Linux) toevoegen aan het configuratie beheer van het apparaat voor toepassings detectie. U hebt een gast gebruikers account voor Windows-servers en een reguliere/normale gebruikers account (niet-sudo toegang) nodig voor alle Linux-servers. Meer [informatie](add-server-credentials.md) over hoe u referenties kunt opgeven en hoe u deze kunt afhandelen.

### <a name="add-credentials-and-initiate-discovery"></a>Referenties toevoegen en detectie starten

1. Open de configuratie van het apparaatconfiguratie-beheer, voer de vereiste controles en registratie van het apparaat uit.
2. Navigeer naar het paneel **referenties en detectie bronnen beheren** .
1.  In **stap 1: geef vCenter Server referenties** op en klik op **referenties toevoegen** om referenties op te geven voor het vCenter Server account dat het apparaat gebruikt om servers te detecteren die worden uitgevoerd op de vCenter Server.
1. In **stap 2: geef vCenter Server Details** op, klik op **detectie bron toevoegen** om de beschrijvende naam voor referenties in de vervolg keuzelijst te selecteren, geef het **IP-adres/de FQDN** van de vCenter Server instantie :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="paneel 3 op Apparaatbeheer voor vCenter Server Details op"::: .
1. In **stap 3: Server referenties opgeven voor het uitvoeren van software-inventaris, afhankelijkheids analyse zonder agent en detectie van SQL Server instanties en data bases**, klikt u op **referenties toevoegen** om meerdere Server referenties op te geven om software-inventaris te initiëren.
1. Klik op **detectie starten** om te beginnen met de detectie van vCenter Server.

 Nadat de vCenter Server detectie is voltooid, start het apparaat de detectie van geïnstalleerde toepassingen, functies en onderdelen (software-inventarisatie). De duur is afhankelijk van het aantal gedetecteerde servers. Voor 500-servers duurt het ongeveer een uur voordat de gedetecteerde inventaris wordt weer gegeven in de Azure Migrate Portal.

## <a name="review-and-export-the-inventory"></a>De inventaris controleren en exporteren

Nadat de software-inventarisatie is voltooid, kunt u de inventaris bekijken en exporteren in de Azure Portal.

1. Klik in **Azure migrate servers**  >  **Azure migrate: Server evaluatie** op de weer gegeven aantal om de pagina **gedetecteerde servers** te openen.

    > [!NOTE]
    > In deze fase kunt u eventueel ook afhankelijkheids analyse inschakelen voor de gedetecteerde servers, zodat u afhankelijkheden kunt visualiseren tussen servers die u wilt beoordelen. Meer [informatie](concepts-dependency-visualization.md) over afhankelijkheids analyse.

2. Klik in de kolom **gedetecteerde toepassingen** op het aantal weer gegeven items om de gedetecteerde toepassingen, functies en onderdelen te bekijken.
4. Als u de inventaris wilt exporteren, klikt u op **app-inventaris exporteren** in **gedetecteerde servers**.

De inventarisatie van toepassingen wordt geëxporteerd en gedownload in Excel-indeling. Op de **inventarisatie** pagina van de toepassing worden alle gedetecteerde apps op alle servers weer gegeven.

## <a name="discover-sql-server-instances-and-databases"></a>SQL Server instanties en data bases detecteren

- Toepassings detectie identificeert ook de SQL Server exemplaren die worden uitgevoerd in uw VMware-omgeving.
- Als u geen Windows-verificatie-of SQL Server authenticatie referenties hebt ingevoerd op de configuratie beheer van het apparaat, voegt u de referenties toe zodat het apparaat deze kan gebruiken om verbinding te maken met de respectieve SQL Server instanties.

Zodra het apparaat is verbonden, worden configuratie-en prestatie gegevens van SQL Server instanties en data bases verzameld. De SQL Server configuratie gegevens worden elke 24 uur bijgewerkt en de prestatie gegevens worden elke 30 seconden vastgelegd. Elke wijziging in de eigenschappen van de SQL Server-instantie en data bases, zoals de database status, het compatibiliteits niveau, enzovoort, kan tot wel 24 uur duren om bij te werken op de portal.

## <a name="next-steps"></a>Volgende stappen

- [Een evaluatie maken](how-to-create-assessment.md) voor gedetecteerde servers.
- [SQL-servers beoordelen](./tutorial-assess-sql.md) voor migratie naar Azure SQL.
