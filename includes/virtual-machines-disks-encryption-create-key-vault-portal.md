---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/23/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a967777b65c06cf23239a47e8e691fb3a29231b4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "88815467"
---
Door door de klant beheerde sleutels voor uw schijven in te stellen, moet u resources in een bepaalde volg orde maken, als u deze voor de eerste keer uitvoert. Eerst moet u een Azure Key Vault maken en instellen.

## <a name="set-up-your-azure-key-vault"></a>Uw Azure Key Vault instellen

1. Meld u aan bij de [Azure Portal](https://aka.ms/diskencryptionupdates).
1. Zoek en selecteer **sleutel kluizen**.

    [![Scherm afbeelding van het Azure Portal in het dialoog venster zoeken is uitgevouwen.](./media/virtual-machines-disk-encryption-portal/server-side-encryption-key-vault-portal-search.png)](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > De Azure-sleutel kluis, de schijf versleutelings, de virtuele machine, de schijven en moment opnamen moeten allemaal in dezelfde regio en hetzelfde abonnement staan om te kunnen worden geïmplementeerd.

1. Selecteer **+ toevoegen** om een nieuw Key Vault te maken.
1. Een nieuwe resourcegroep maken.
1. Voer een naam voor de sleutel kluis in, selecteer een regio en selecteer een prijs categorie.

    > [!NOTE]
    > Wanneer u het Key Vault-exemplaar maakt, moet u de beveiliging voor voorlopig verwijderen en leegmaken inschakelen. Met zacht verwijderen zorgt u ervoor dat de Key Vault een verwijderde sleutel bevat voor een bepaalde Bewaar periode (standaard 90 dagen). Bij het opschonen van de beveiliging wordt ervoor gezorgd dat een verwijderde sleutel pas definitief kan worden verwijderd als de retentie periode is verstreken. Met deze instellingen wordt voor komen dat gegevens verloren gaan als gevolg van onbedoeld verwijderen. Deze instellingen zijn verplicht wanneer u een Key Vault gebruikt voor het versleutelen van Managed disks.

1. Selecteer **controleren + maken**, Controleer uw keuzes en selecteer vervolgens **maken**.

    ![Scherm opname van de Azure Key Vault maken. De specifieke waarden weer geven die u maakt](./media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-vault.png)

1. Als uw sleutel kluis is geïmplementeerd, selecteert u deze.
1. Selecteer **sleutels** onder **instellingen**.
1. Selecteer **Genereren/importeren**.

    ![Scherm opname van het deel venster Key Vault resource-instellingen. Hiermee wordt de knop genereren/importeren in instellingen weer gegeven.](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-generate-settings.png)

1. Zorg dat het **sleutel type** is ingesteld op **RSA** en **RSA-sleutel grootte** ingesteld op **2048**.
1. Vul de resterende selecties in zoals u wilt en selecteer vervolgens **maken**.

    ![Scherm afbeelding van de Blade een sleutel maken die wordt weer gegeven nadat de knop genereren/importeren is geselecteerd](./media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-generate.png)

## <a name="set-up-your-disk-encryption-set"></a>Uw schijf versleutelings set instellen

1. Zoek naar **schijf versleutelings sets** en selecteer deze.
1. Selecteer **+ toevoegen** op de Blade **schijf versleutelings sets** .

    ![Scherm afbeelding van het hoofd scherm van de portal voor schijf versleuteling. De knop toevoegen markeren](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-create-disk-encryption-set.png)

1. Selecteer uw resource groep, geef uw versleutelings set een naam en selecteer dezelfde regio als uw sleutel kluis.
1. Voor **versleutelings type** selecteert u **versleuteling op rest met een door de klant beheerde sleutel**.

    > [!NOTE]
    > Wanneer u een schijf versleutelings met een bepaald versleutelings type hebt gemaakt, kan deze niet meer worden gewijzigd. Als u een ander versleutelings type wilt gebruiken, moet u een nieuwe schijf versleutelings maken.

1. Selecteer **klikken om een sleutel te selecteren**.
1. Selecteer de sleutel kluis en de sleutel die u eerder hebt gemaakt, evenals de versie.
1. Druk op **selecteren**.
1. Selecteer **Controleren en maken**, en vervolgens **Maken**.

    ![Scherm opname van de Blade voor het maken van schijf versleuteling. Het abonnement, de resource groep, de naam van de schijf versleutelings, de regio en de sleutel kluis + sleutel kiezer worden weer gegeven.](./media/virtual-machines-disk-encryption-portal/server-side-encryption-disk-set-blade.png)

1. Open de schijf versleutelings nadat deze is gemaakt en selecteer de waarschuwing die wordt weer gegeven.

    ![Scherm afbeelding van pop-up van waarschuwing: ' als u een schijf, installatie kopie of moment opname aan een schijf versleutelings wilt koppelen, moet u machtigingen verlenen aan de sleutel kluis. Selecteer deze waarschuwing om door te gaan](./media/virtual-machines-disk-encryption-portal/server-side-encryption-disk-encryption-set-alert-fix.png)

    Er worden twee meldingen over de pop-up en geslaagd. Hierdoor kunt u de schijf versleuteling instellen met uw sleutel kluis.

    ![Scherm opname van geslaagde machtiging en roltoewijzing voor uw sleutel kluis.](./media/virtual-machines-disk-encryption-portal/disk-encryption-notification-success.png)