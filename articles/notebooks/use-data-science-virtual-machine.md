---
title: Azure data Science Virtual Machines gebruiken
description: Meer informatie over hoe u verbinding maakt met een Azure Data Science Virtual Machine (DSVM) om de reken kracht uit te breiden die beschikbaar is voor Azure Notebooks preview.
author: getroyer
manager: andneil
ms.author: getroyer
ms.topic: how-to
ms.date: 06/13/2019
ms.openlocfilehash: 7d65a39c1a1d302e565b59db1644af2c08befb6b
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843159"
---
# <a name="use-azure-data-science-virtual-machines"></a>Azure data Science Virtual Machines gebruiken

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Standaard worden projecten uitgevoerd op de **gratis Compute** -laag. Dit is beperkt tot 4 GB aan geheugen en 1 GB aan gegevens om misbruik te voor komen. U kunt deze beperkingen overs Laan door een andere virtuele machine te gebruiken die u in een Azure-abonnement hebt ingericht. Voor dit doel is de beste keuze een Azure Data Science Virtual Machine (DSVM) met behulp **van de data Science virtual machine voor Linux-installatie kopie (Ubuntu)** . Een dergelijke DSVM is vooraf geconfigureerd met alles wat u nodig hebt voor Azure Notebooks en wordt automatisch weer gegeven in de vervolg keuzelijst voor het **uitvoeren** van Azure notebooks.

> [!Note]
> Azure Notebooks wordt alleen ondersteund op Dsvm die zijn gemaakt met de Ubuntu-installatie kopie op Linux. Notebooks worden niet ondersteund in Windows 2012-, Windows 2016-of Linux CentOS-installatie kopieën.

## <a name="create-a-dsvm-instance"></a>Een DSVM-exemplaar maken

Als u een nieuw DSVM-exemplaar wilt maken, volgt u de instructies in [een Ubuntu maken data Science VM](../machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro.md). Zie [Data Science virtual machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)voor meer informatie, inclusief prijs informatie.

## <a name="connect-to-the-dsvm"></a>Verbinding maken met de DSVM

Zodra u de DSVM hebt gemaakt, selecteert u de vervolg keuzelijst **uitvoeren** in het dash board van Azure notebooks project en selecteert u het juiste DSVM-exemplaar. In de vervolg keuzelijst worden DSVM-exemplaren weer gegeven als aan de volgende voor waarden wordt voldaan:

- U bent aangemeld bij Azure Notebooks met een account dat gebruikmaakt van Azure Active Directory (AAD), zoals een bedrijfs account.
- Uw account is verbonden met een Azure-abonnement.
- U hebt een of meer virtuele machines in dat abonnement, met mini maal lezers toegang, die gebruikmaken van de Data Science Virtual Machine-installatie kopie voor Linux (Ubuntu).)

![Instanties in de vervolg keuzelijst van het project dashboard Data Science Virtual Machine](media/project-compute-tier-dsvm.png)

Wanneer u een DSVM-exemplaar selecteert Azure Notebooks, wordt u mogelijk gevraagd om de specifieke computer referenties die worden gebruikt bij het maken van de virtuele machine.

> [!Important]
> De gebruikers naam moet een kleine letter zijn om deze te gebruiken met JupyterHub.

Als aan een van de voor waarden niet wordt voldaan, kunt u nog steeds verbinding maken met de DSVM. Selecteer in de vervolg keuzelijst de optie **Directe Compute** , waarin u wordt gevraagd een naam (om weer te geven in de lijst), het IP-adres en de poort van de virtuele machine (meestal 8000, de standaard poort waarnaar JupyterHub luistert) en de VM-referenties:

![Vragen om Server gegevens te verzamelen voor de optie directe compute](media/project-compute-tier-direct.png)

U kunt deze waarden verkrijgen op de pagina DSVM in de Azure Portal.

## <a name="accessing-azure-notebooks-files-from-the-dsvm"></a>Azure Notebooks-bestanden openen vanuit de DSVM

Toegang tot bestands systeem wordt ondersteund voor DSVM-versies 19.06.15 of hoger. Als u de versie wilt controleren, maakt u eerst verbinding met uw DSVM via SSH en voert u vervolgens de volgende opdracht uit: `curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2018-10-01"` (u moet het exacte IP-adres gebruiken dat hier wordt weer gegeven). Het versie nummer wordt weer gegeven in de uitvoer voor ' versie '.

Als u de pariteit van bestands paden met de **gratis Compute** -laag wilt behouden, kunt u slechts één project tegelijk op een DSVM openen. Als u een nieuw project wilt openen, moet u eerst het openstaande project afsluiten.

Wanneer een project wordt uitgevoerd op een virtuele machine, worden de bestanden gekoppeld aan de hoofdmap van de Jupyter-server (de map die wordt weer gegeven in JupyterHub), waarbij de standaard bestanden voor Azure Notebooks worden vervangen. Wanneer u de virtuele machine afsluit met behulp van de knop **Afsluiten** in de gebruikers interface van het notitie blok, worden de standaard bestanden door Azure notebooks hersteld.

![De knop Afsluiten in Azure Notebooks](media/shutdown.png)

## <a name="create-new-dsvm-users"></a>Nieuwe DSVM-gebruikers maken

Als meerdere gebruikers een DSVM delen, kunt u voor komen dat elkaar worden geblokkeerd door een DSVM-gebruiker te maken en te gebruiken voor elke notebook-gebruiker:

1. Ga op het [Azure Portal](https://portal.azure.com)naar de virtuele machine.
1. Onder **ondersteuning en probleem oplossing** in de linkermarge selecteert u **wacht woord opnieuw instellen**.
1. Geef een nieuwe **gebruikers naam** op. De gebruikers naam moet een kleine letter zijn om deze te gebruiken met JupyterHub. Voer een wachtwoord in. Selecteer vervolgens **bijwerken**. (Bestaande gebruikers namen worden niet beïnvloed.)
1. Herhaal de vorige stap voor extra gebruikers.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Dsvm vindt [u in de inleiding tot Azure data Science virtual machines](../machine-learning/data-science-virtual-machine/overview.md).