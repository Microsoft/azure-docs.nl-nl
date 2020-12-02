---
title: Ontdek apps, functies en onderdelen op on-premises servers met Azure Migrate
description: Meer informatie over het detecteren van apps, functies en onderdelen op on-premises servers met Azure Migrate server-evaluatie.
ms.topic: article
ms.date: 06/10/2020
ms.openlocfilehash: 960877ac4eab4d8f23b5616dc04628142964b46a
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483450"
---
# <a name="discover-machine-apps-roles-and-features"></a>Machine-apps,-functies en-functies detecteren

In dit artikel wordt beschreven hoe u toepassingen, functies en onderdelen detecteert op on-premises servers met behulp van Azure Migrate: Server evaluatie.

Het detecteren van de inventaris van apps, rollen en functies die worden uitgevoerd op on-premises machines, helpt bij het identificeren en aanpassen van een migratie naar Azure voor uw workloads. App-Discovery gebruikt het Azure Migrate apparaat om detectie uit te voeren, met behulp van VM-gast referenties. App-Discovery is zonder agent. Er is niets geïnstalleerd op Vm's.

> [!NOTE]
> App-detectie is momenteel alleen beschikbaar als Preview voor VMware-Vm's en is beperkt tot alleen detectie. We bieden nog geen evaluatie op basis van een app. 


## <a name="before-you-start"></a>Voordat u begint

- Zorg ervoor dat u:
    - Een Azure Migrate-project [gemaakt](./create-manage-projects.md) .
    - Het hulp programma voor het evalueren van Azure Migrate: Server is [toegevoegd](how-to-assess.md) aan een project.
- Bekijk [ondersteuning en vereisten voor app-detectie](migrate-support-matrix-vmware.md#vmware-requirements).
- Zorg ervoor dat de virtuele machines waarop de app-detectie wordt uitgevoerd, Power shell-versie 2,0 of hoger hebben geïnstalleerd en dat er VMware-Hulpprogram Ma's (later dan 10.2.0) is geïnstalleerd.
- Controleer de [vereisten](migrate-appliance.md) voor het implementeren van het Azure migrate apparaat.


## <a name="deploy-the-azure-migrate-appliance"></a>Het Azure Migrate-apparaat implementeren

1. [Bekijk](migrate-appliance.md#appliance---vmware) de vereisten voor het implementeren van het Azure migrate apparaat.
2. Bekijk de Azure-Url's die het apparaat nodig heeft voor toegang tot de [open bare](migrate-appliance.md#public-cloud-urls) en [overheids Clouds](migrate-appliance.md#government-cloud-urls).
3. [Bekijk gegevens](migrate-appliance.md#collected-data---vmware) die door het apparaat worden verzameld tijdens de detectie en evaluatie.
4. [Noteer](migrate-support-matrix-vmware.md#port-access-requirements) de toegangs vereisten voor poorten voor het apparaat.
5. [Implementeer het Azure migrate apparaat om de](how-to-set-up-appliance-vmware.md) detectie te starten. Als u het apparaat wilt implementeren, downloadt en importeert u een eicellen-sjabloon in VMware om het apparaat als een virtuele VMware-machine te maken. U configureert het apparaat en registreert het vervolgens met Azure Migrate.
6. Wanneer u het apparaat implementeert, moet u het volgende opgeven om de continue detectie te starten:
    - De naam van de vCenter Server waarmee u verbinding wilt maken.
    - Referenties die u hebt gemaakt voor het apparaat om verbinding te maken met vCenter Server.
    - De account referenties die u hebt gemaakt voor het apparaat om verbinding te maken met Vm's van Windows/Linux.

Nadat het apparaat is geïmplementeerd en u referenties hebt gegeven, start het apparaat doorlopende detectie van VM-meta gegevens en prestatie gegevens, samen met en detectie van apps, functies en rollen.  De duur van het detecteren van apps hangt af van het aantal Vm's dat u hebt. Het duurt doorgaans een uur voor app-detectie van 500 Vm's.

## <a name="verify-permissions"></a>Machtigingen controleren

U hebt [een vCenter Server alleen-lezen account gemaakt](./tutorial-discover-vmware.md#prepare-vmware) voor detectie en evaluatie. Voor de alleen-lezen-account zijn bevoegdheden nodig die zijn ingeschakeld voor **virtual machines**  >  -**gast bewerkingen**, om te kunnen communiceren met de virtuele machine voor app-detectie.

### <a name="add-the-user-account-to-the-appliance"></a>Het gebruikers account toevoegen aan het apparaat

Voeg het gebruikers account als volgt toe:

1. Open de app voor het beheren van apparaten. 
2. Navigeer naar het deel venster **vCenter-gegevens opgeven** .
3. Klik in **toepassing en afhankelijkheden van Vm's detecteren** op **referenties toevoegen**
3. Kies het **besturings systeem**, geef een beschrijvende naam op voor het account en het **User name** / **wacht woord** voor de gebruikers naam
6. Klik op **Opslaan**.
7. Klik op **opslaan en detectie starten**.

    ![VM-gebruikers account toevoegen](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)


## <a name="review-and-export-the-inventory"></a>De inventaris controleren en exporteren

Nadat de detectie is beëindigd, kunt u de app-inventaris bekijken en exporteren in de Azure Portal als u referenties hebt ingesteld voor app-detectie.

1. Klik in **Azure migrate servers**  >  **Azure migrate: Server evaluatie** op de weer gegeven aantal om de pagina **gedetecteerde servers** te openen.

    > [!NOTE]
    > In deze fase kunt u ook een afhankelijkheids analyse instellen voor gedetecteerde computers, zodat u afhankelijkheden kunt visualiseren tussen computers die u wilt beoordelen. Meer [informatie](concepts-dependency-visualization.md) over afhankelijkheids analyse.

2. In **gedetecteerde toepassingen** klikt u op het aantal weer gegeven.
3. In de **toepassings inventaris** kunt u de gedetecteerde apps, rollen en functies bekijken.
4. Als u de inventaris wilt exporteren, klikt u op **app-inventaris exporteren** in **gedetecteerde servers**.

De app-inventarisatie wordt geëxporteerd en gedownload in Excel-indeling. Op de **inventarisatie** pagina van de toepassing worden alle apps weer gegeven die op alle computers zijn gedetecteerd.

## <a name="next-steps"></a>Volgende stappen

- [Een evaluatie maken](how-to-create-assessment.md) voor gedetecteerde servers.
- Evalueer SQL Server data bases met behulp van [Azure migrate: data base-evaluatie](/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017).