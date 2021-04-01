---
title: In-place wijziging van SQL Server Edition
description: Meer informatie over het wijzigen van de editie van uw SQL Server virtuele machine in azure om de kosten te verlagen of een upgrade uit te voeren om meer functies in te scha kelen.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/14/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: b6ea6f947b2cd8d7d9c38a0e5e440ce5fd273bb7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97358823"
---
# <a name="in-place-change-of-sql-server-edition-on-azure-vm"></a>In-place wijziging van SQL Server-editie op Azure VM
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In dit artikel wordt beschreven hoe u de editie van SQL Server kunt wijzigen op een virtuele Windows-machine in Azure. 

De versie van SQL Server wordt bepaald door de product code en wordt opgegeven tijdens het installatie proces met behulp van de-installatie media. De versie bepaalt welke [functies](/sql/sql-server/editions-and-components-of-sql-server-2017) beschikbaar zijn in het SQL Server product. U kunt de SQL Server Edition met de installatie media wijzigen en een downgrade uitvoeren om de kosten te verlagen of een upgrade uit te voeren om meer functies in te scha kelen.

Zodra de versie van SQL Server intern is gewijzigd in de SQL Server VM, moet u de editie-eigenschap van SQL Server in de Azure Portal bijwerken voor facturerings doeleinden. 

## <a name="prerequisites"></a>Vereisten

Als u een in-place wijziging van de editie van SQL Server wilt uitvoeren, hebt u het volgende nodig: 

- Een [Azure-abonnement](https://azure.microsoft.com/free/).
- Een [SQL Server VM in Windows](./create-sql-vm-portal.md) die is geregistreerd met de [SQL IaaS agent-extensie](sql-agent-extension-manually-register-single-vm.md).
- Stel media in met de **gewenste versie** van SQL Server. Klanten die beschikken over [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) kunnen hun installatiemedia downloaden van het [Volume Licensing Center](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Klanten die geen Software Assurance hebben, kunnen het installatie medium van een Azure Marketplace gebruiken SQL Server VM-installatie kopie met de gewenste versie (meestal zich in `C:\SQLServerFull` ). 


## <a name="upgrade-an-edition"></a>Een editie bijwerken

> [!WARNING]
> Als u de versie van SQL Server bijwerkt, wordt de service voor SQL Server opnieuw gestart, samen met de bijbehorende services, zoals Analysis Services en R-Services. 

Als u de versie van SQL Server wilt bijwerken, moet u de SQL Server Setup-media verkrijgen voor de gewenste versie van SQL Server en vervolgens het volgende doen:

1. Open Setup.exe vanaf de SQL Server-installatie media. 
1. Ga naar **onderhoud** en kies de optie voor **editie-upgrades** . 

   ![Selectie voor het bijwerken van de versie van SQL Server](./media/change-sql-server-edition/edition-upgrade.png)

1. Selecteer **volgende** totdat u de pagina **gereed voor de upgrade-editie** hebt bereikt en selecteer vervolgens **upgrade**. Het installatie venster reageert mogelijk enkele minuten niet meer wanneer de wijziging wordt doorgevoerd. Op een **volledige** pagina wordt bevestigd dat uw editie-upgrade is voltooid. 

Nadat de SQL Server-editie is bijgewerkt, wijzigt u de eigenschap Edition van de virtuele machine SQL Server in de Azure Portal. Hiermee worden de meta gegevens en de facturering bijgewerkt die aan deze virtuele machine zijn gekoppeld.

## <a name="downgrade-an-edition"></a>Een editie downgradeen

Als u de editie van SQL Server wilt downgradeen, moet u SQL Server volledig verwijderen en opnieuw installeren met de gewenste installatie media van de editie. 

> [!WARNING]
> Het verwijderen van SQL Server kan extra downtime opleveren. 

U kunt de editie van SQL Server downgrade door de volgende stappen uit te voeren:

1. Maak een back-up van alle data bases, met inbegrip van de systeem databases. 
1. Verplaats systeem databases (Master, model en msdb) naar een nieuwe locatie. 
1. SQL Server en alle gekoppelde services volledig verwijderen. 
1. Start de virtuele machine opnieuw op. 
1. Installeer SQL Server met behulp van de media met de gewenste versie van SQL Server.
1. Installeer de nieuwste service packs en cumulatieve updates.  
1. Vervang de nieuwe systeem databases die tijdens de installatie zijn gemaakt met de systeem databases die u eerder naar een andere locatie hebt verplaatst. 

Nadat de SQL Server-editie is gedowngraded, wijzigt u de eigenschap Edition van de virtuele machine SQL Server in de Azure Portal. Hiermee worden de meta gegevens en de facturering bijgewerkt die aan deze virtuele machine zijn gekoppeld.

## <a name="change-edition-in-portal"></a>Editie wijzigen in Portal 

Nadat u de editie van SQL Server hebt gewijzigd met behulp van de installatie media en u uw SQL Server VM hebt geregistreerd met de [SQL IaaS agent-extensie](sql-agent-extension-manually-register-single-vm.md), kunt u vervolgens de Azure Portal gebruiken om de eigenschap Edition van de SQL Server virtuele machine te wijzigen voor facturerings doeleinden. Voer hiervoor de volgende stappen uit: 

1. Meld u aan bij [Azure Portal](https://portal.azure.com). 
1. Ga naar uw SQL Server virtuele machine bron. 
1. Selecteer **configureren** onder **instellingen**. Selecteer vervolgens de gewenste versie van SQL Server in de vervolg keuzelijst onder **editie**. 

   ![Meta gegevens van de editie wijzigen](./media/change-sql-server-edition/edition-change-in-portal.png)

1. Bekijk de waarschuwing met de melding dat u eerst de SQL Server Edition moet wijzigen en dat de editie-eigenschap moet overeenkomen met de SQL Server Edition. 
1. Selecteer **Toep assen** om de wijzigingen in de meta gegevens van de editie toe te passen. 


## <a name="remarks"></a>Opmerkingen

- De editie-eigenschap voor de SQL Server virtuele machine moet overeenkomen met de editie van het SQL Server exemplaar dat is geïnstalleerd voor alle SQL Server virtual machines, inclusief betalen per gebruik en uw eigen licentie typen licenties.
- Als u uw SQL Server VM-resource weghaalt, gaat u terug naar de in code vastgelegde editie van de installatie kopie.
- De mogelijkheid om de editie te wijzigen, is een functie van de SQL IaaS agent-extensie. Als u een installatie kopie van een Azure Marketplace implementeert via de Azure Portal wordt automatisch een SQL Server virtuele machine geregistreerd met de SQL IaaS agent-extensie. Klanten die zelf een SQL Server installeren, moeten echter [hun SQL Server virtuele machine](sql-agent-extension-manually-register-single-vm.md)hand matig registreren.
- Voor het toevoegen van een SQL Server-VM aan een beschikbaarheidsset moet de virtuele machine opnieuw worden gemaakt. Alle Vm's die zijn toegevoegd aan een beschikbaarheidsset, worden teruggestuurd naar de standaard editie en de editie moet opnieuw worden gewijzigd.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie de volgende artikelen: 

* [Overzicht van SQL Server op een Windows-VM](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Veelgestelde vragen over SQL Server op een Windows-VM](frequently-asked-questions-faq.md)
* [Prijs informatie voor SQL Server op een Windows-VM](pricing-guidance.md)
* [Release opmerkingen voor SQL Server op een Windows-VM](doc-changes-updates-release-notes.md)