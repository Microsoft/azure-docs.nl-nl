---
title: In-place wijziging van SQL Server versie
description: Meer informatie over het wijzigen van de versie van uw SQL Server virtuele machine in Azure.
services: virtual-machines-windows
documentationcenter: na
author: ramakoni1
manager: ramakoni1
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/08/2020
ms.author: RamaKoni
ms.reviewer: sqlblt, daleche
ms.custom: seo-lt-2019
ms.openlocfilehash: 5dbd5725c666afe04d57d4432f0a4798fcb34c03
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97358840"
---
# <a name="in-place-change-of-sql-server-version-on-azure-vm"></a>In-place wijziging van de SQL Server-versie op de Azure-VM

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In dit artikel wordt beschreven hoe u de versie van Microsoft SQL Server wijzigt op een virtuele Windows-machine (VM) in Microsoft Azure.

## <a name="prerequisites"></a>Vereisten

Voor een in-place upgrade van SQL Server gelden de volgende voor waarden:

- De installatie media van de gewenste versie van SQL Server zijn vereist. Klanten die beschikken over [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) kunnen hun installatiemedia downloaden van het [Volume Licensing Center](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Klanten die geen Software Assurance hebben, kunnen het installatie medium van een Azure Marketplace gebruiken SQL Server VM-installatie kopie met een latere versie van SQL Server (meestal opgeslagen in C:\SQLServerFull).
- Editie-upgrades moeten de [ondersteunings upgrade paden](/sql/database-engine/install-windows/supported-version-and-edition-upgrades-version-15)volgen.

## <a name="planning-for-version-change"></a>Planning voor versie wijziging

We raden u aan de volgende items te controleren voordat u de versie wijzigt:

1. Controleer wat er nieuw is in de versie die u wilt upgraden naar:

   - Wat is er nieuw in [SQL 2019](/sql/sql-server/what-s-new-in-sql-server-ver15)
   - Wat is er nieuw in [SQL 2017](/sql/sql-server/what-s-new-in-sql-server-2017)
   - Wat is er nieuw in [SQL 2016](/sql/sql-server/what-s-new-in-sql-server-2016)


1. Het is raadzaam om de [compatibiliteits certificering](/sql/database-engine/install-windows/compatibility-certification) te controleren voor de versie die u gaat wijzigen, zodat u de database compatibiliteits modi kunt gebruiken om het effect van de upgrade te minimaliseren.
1. U kunt de volgende artikelen raadplegen om te zorgen voor een geslaagd resultaat:

   - [Video: modernere SQL Server | Pam Lahoud & Pedro Lopes | 20 jaar na slagen](https://www.youtube.com/watch?v=5RPkuQHcxxs&feature=youtu.be)
   - [Testen van Database Experimentation Assistant voor AB](/sql/dea/database-experimentation-assistant-overview)
   - [Data bases bijwerken met behulp van de opdracht voor het afstemmen van Query's](/sql/relational-databases/performance/upgrade-dbcompat-using-qta)
   - [Het database compatibiliteits niveau wijzigen en het query archief gebruiken](/sql/database-engine/install-windows/change-the-database-compatibility-mode-and-use-the-query-store)

## <a name="upgrade-sql-version"></a>Upgrade uitvoeren voor SQL-versie

> [!WARNING]
> Als u de versie van SQL Server bijwerkt, wordt de service voor SQL Server opnieuw gestart, naast eventuele gekoppelde services, zoals Analysis Services en R-Services.

Als u de versie van SQL Server wilt bijwerken, moet u de SQL Server Setup-media verkrijgen voor de latere versie die [het upgrade traject](/sql/database-engine/install-windows/supported-version-and-edition-upgrades-version-15) van SQL Server zou ondersteunen. Voer de volgende stappen uit:

1. Maak een back-up van de data bases, inclusief systeem (met uitzonde ring van tempdb) en gebruikers databases voordat u het proces start. U kunt ook een toepassings consistente back-up op VM-niveau maken met behulp van Azure Backup Services.
1. Start Setup.exe vanaf de SQL Server-installatie media.
1. De installatie wizard start het SQL Server-installatie centrum. Als u een bestaand exemplaar van SQL Server wilt bijwerken, selecteert u **installatie** in het navigatie venster en selecteert u vervolgens **upgrade van een eerdere versie van SQL Server**.

   :::image type="content" source="./media/change-sql-server-version/upgrade.png" alt-text="Selectie voor het bijwerken van de versie van SQL Server":::

1. Selecteer op de pagina **product code** een optie om aan te geven of u een upgrade uitvoert naar een gratis versie van SQL Server of dat u een PID-sleutel hebt voor een productie versie van het product. Zie [edities en ondersteunde functies van SQL Server 2019 (15. x)](/sql/sql-server/editions-and-components-of-sql-server-version-15) en [ondersteunde versie-en editie-Upgrades (SQL Server 2016)](/sql/database-engine/install-windows/supported-version-and-edition-upgrades)voor meer informatie.
1. Selecteer **volgende** totdat u de pagina **gereed voor de upgrade** hebt bereikt en selecteer vervolgens **upgrade**. Het installatie venster reageert mogelijk enkele minuten niet meer wanneer de wijziging wordt doorgevoerd. Op een **volledige** pagina wordt bevestigd dat uw upgrade is voltooid. Zie [de volledige procedure](/sql/database-engine/install-windows/upgrade-sql-server-using-the-installation-wizard-setup#procedure)voor een stapsgewijze procedure voor het uitvoeren van een upgrade.

   :::image type="content" source="./media/change-sql-server-version/complete-page.png" alt-text="Volledige pagina":::

Als u de SQL Server-editie hebt gewijzigd naast het wijzigen van de versie, moet u ook de editie bijwerken en raadpleegt u de sectie **versie en editie controleren in de portal** om de SQL-VM-instantie te wijzigen.

   :::image type="content" source="./media/change-sql-server-version/change-portal.png" alt-text="Meta gegevens van de versie wijzigen":::

## <a name="downgrade-the-version-of-sql-server"></a>De versie van SQL Server downgrade

Als u de versie van SQL Server wilt downgradeen, moet u SQL Server volledig verwijderen en opnieuw installeren met behulp van de gewenste versie. Dit is vergelijkbaar met een nieuwe installatie van SQL Server omdat u de eerdere data base niet kunt herstellen van een nieuwere versie naar de zojuist geïnstalleerde eerdere versie. De data bases moeten volledig opnieuw worden gemaakt. Als u tijdens de upgrade ook de editie van SQL Server hebt gewijzigd, wijzigt u de eigenschap **Edition** van de SQL Server VM in de Azure Portal in de nieuwe editie waarde. Hiermee worden de meta gegevens en facturering bijgewerkt die aan deze virtuele machine zijn gekoppeld.

> [!WARNING]
> Een in-place downgrade van SQL Server wordt niet ondersteund.

U kunt de versie van SQL Server downgrade door de volgende stappen uit te voeren:

1. Zorg ervoor dat u geen gebruik maakt van een functie die [alleen beschikbaar is in de nieuwere versie](https://social.technet.microsoft.com/wiki/contents/articles/24222.find-enterprise-only-features-in-your-database.aspx).
1. Maak een back-up van alle data bases, inclusief systeem (behalve TempDB) en gebruikers databases.
1. Exporteer alle benodigde objecten op server niveau (zoals server triggers, rollen, aanmeldingen, gekoppelde servers, taken, referenties en certificaten).
1. Als u geen scripts hebt om uw gebruikers databases opnieuw te maken in de eerdere versie, moet u scripts uitvoeren voor alle objecten en alle gegevens exporteren met behulp van BCP.exe, SSIS of DACPAC.

   Zorg ervoor dat u de juiste opties selecteert wanneer u scripteert voor de doel versie, afhankelijke objecten en geavanceerde opties.

   :::image type="content" source="./media/change-sql-server-version/scripting-options.png" alt-text="Script opties":::

1. SQL Server en alle gekoppelde services volledig verwijderen.
1. Start de VM opnieuw op.
1. Installeer SQL Server met behulp van de media voor de gewenste versie van het programma.
1. Installeer de nieuwste service packs en cumulatieve updates.
1. Importeer alle benodigde objecten op server niveau (die zijn geëxporteerd in stap 3).
1. Alle benodigde gebruikers databases helemaal opnieuw maken (met behulp van gemaakte scripts of de bestanden uit stap 4).

## <a name="verify-the-version-and-edition-in-the-portal"></a>De versie en editie in de portal controleren

Nadat u de versie van SQL Server hebt gewijzigd, registreert u uw SQL Server VM opnieuw met de [extensie SQL IaaS agent](sql-agent-extension-manually-register-single-vm.md) zodat u de Azure Portal kunt gebruiken om de versie van SQL Server weer te geven. Het vermelde versie nummer moet nu overeenkomen met de zojuist bijgewerkte versie en editie van uw SQL Server-installatie.

:::image type="content" source="./media/change-sql-server-version/verify-portal.png" alt-text="Versie controleren":::

> [!NOTE]
> Als u zich al hebt geregistreerd bij de SQL IaaS agent-extensie, maakt u de [registratie van de RP ongedaan](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) en [registreert u de SQL VM-resource](sql-agent-extension-manually-register-single-vm.md#register-with-extension) vervolgens opnieuw zodat de juiste versie en editie van SQL Server worden gedetecteerd die op de virtuele machine is geïnstalleerd. Hiermee worden de meta gegevens en facturerings gegevens bijgewerkt die aan deze virtuele machine zijn gekoppeld.

## <a name="remarks"></a>Opmerkingen

- Het is raadzaam om back-ups te initiëren/statistieken bij te werken/indexen opnieuw samen te stellen en de consistentie te controleren nadat de upgrade is voltooid. U kunt ook de compatibiliteits niveaus van afzonderlijke data bases controleren om ervoor te zorgen dat ze overeenkomen met het gewenste niveau.
- Nadat SQL Server op de virtuele machine is bijgewerkt, moet u ervoor zorgen dat de eigenschap **Edition** van SQL Server in de Azure Portal overeenkomt met het geïnstalleerde versie nummer voor facturering.
- De mogelijkheid om [de editie te wijzigen](change-sql-server-edition.md#change-edition-in-portal) , is een functie van de SQL IaaS agent-extensie. Als u een installatie kopie van Azure Marketplace implementeert via de Azure Portal registreert u automatisch een SQL Server virtuele machine met de extensie. Klanten die zelf een SQL Server installeren, moeten echter [hun SQL Server virtuele machine](sql-agent-extension-manually-register-single-vm.md)hand matig registreren.
- Als u uw SQL Server VM-resource verwijdert, wordt de in code vastgelegde versie van de installatie kopie hersteld.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie de volgende artikelen:

- [Overzicht van SQL Server op een Windows-VM](sql-server-on-azure-vm-iaas-what-is-overview.md)
- [Veelgestelde vragen over SQL Server op een Windows-VM](frequently-asked-questions-faq.md)
- [Prijs informatie voor SQL Server op een Windows-VM](pricing-guidance.md)
- [Release opmerkingen voor SQL Server op een Windows-VM](doc-changes-updates-release-notes.md)