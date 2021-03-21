---
title: bestand opnemen
description: bestand opnemen
services: backup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: include
ms.date: 10/18/2018
ms.author: dacurwin
ms.custom: include file
ms.openlocfilehash: 69259023484457249acd4c7b4a65c4cbe3e660ca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96184740"
---
## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

Een Recovery Services-kluis is een beheerentiteit waarmee herstelpunten worden opgeslagen die in de loop van de tijd zijn gemaakt en die een interface biedt voor het uitvoeren van back-upbewerkingen. Dit omvat het maken van back-ups op aanvraag, het uitvoeren van herstelbewerkingen en het maken van back-upbeleid.

Voer de volgende stappen uit om een Recovery Services-kluis te maken.

1. Meld u aan bij uw abonnement in [Azure Portal](https://portal.azure.com/).

1. Selecteer in het menu links **Alle services**.

    ![Alle services selecteren](./media/backup-create-rs-vault/click-all-services.png)

1. In het dialoogvenster **Alle services** voert u *Recovery Services* in. De lijst met resources wordt gefilterd op basis van uw invoer. In de lijst met resources selecteert u **Recovery Service-kluizen**.

    ![Recovery Services-kluizen invoeren en kiezen](./media/backup-create-rs-vault/all-services.png)

    De lijst met Recovery Services-kluizen in het abonnement wordt weergeven.

1. Op het dashboard van de **Recovery Services**-kluizen selecteert u **Toevoegen**.

    ![Een Recovery Services-kluis toevoegen](./media/backup-create-rs-vault/add-button-create-vault.png)

    Het dialoogvenster **Recovery Service-kluis** wordt geopend. Geef waarden op voor **Naam**, **Abonnement**, **Resourcegroep** en **Locatie**.

    ![De Recovery Services-kluis configureren](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Naam**: Voer een beschrijvende naam in om de kluis aan te duiden. De naam moet uniek zijn voor het Azure-abonnement. Geef een naam op van minimaal 2 en maximaal 50 tekens. De naam moet beginnen met een letter en mag alleen uit letters, cijfers en afbreekstreepjes bestaan.
   - **Abonnement**: Kies het abonnement dat u wilt gebruiken. Als u lid bent van maar één abonnement, ziet u die naam. Als u niet zeker weet welk abonnement u moet gebruiken, gebruikt u het standaardabonnement (voorgesteld). Er zijn alleen meerdere mogelijkheden als uw werk- of schoolaccount is gekoppeld aan meerdere Azure-abonnementen.
   - **Resourcegroep**: Gebruik een bestaande resourcegroep of maak een nieuwe. Als u de lijst met beschikbare resourcegroepen in uw abonnement wilt weergeven, selecteert u **Bestaande gebruiken** en vervolgens selecteert u een resource in de vervolgkeuzelijst. Als u een nieuwe resourcegroep wilt maken, selecteert u **Nieuwe maken** en voert u de naam in. Zie [Overzicht van Azure Resource Manager](../articles/azure-resource-manager/management/overview.md) voor meer informatie over resourcegroepen.
   - **Locatie**: Selecteer de geografische regio voor de kluis. Als u een kluis voor het beveiligen van elke willekeurige gegevensbron wilt maken, *moet* de kluis zich in dezelfde regio bevinden als de gegevensbron.

      > [!IMPORTANT]
      > Als u niet zeker bent van de locatie van de gegevensbron, sluit u het dialoogvenster. Ga naar de lijst met uw resources in de portal. Als u gegevensbronnen in meerdere regio's hebt, moet u voor elke regio een Recovery Services-kluis maken. Maak de kluis op de eerste locatie voordat u de kluis maakt voor een andere locatie. U hoeft geen opslagaccounts op te geven om de back-upgegevens op te slaan. De Recovery Services-kluis en Azure Backup verwerken dat automatisch.
      >
      >

1. Selecteer **Beoordelen en maken** nadat u de waarden hebt opgegeven.

    ![Schermopname met de knop Beoordelen + maken in het proces Recovery Services-kluis maken.](./media/backup-create-rs-vault/review-and-create.png)

1. Wanneer u klaar bent om de Recovery Services-kluis te maken, selecteert u **Maken**.

    ![De Recovery Services-kluis maken](./media/backup-create-rs-vault/click-create-button.png)

    Het kan even duren voordat de Recovery Services-kluis is gemaakt. Controleer de statusmeldingen rechtsboven in het gebied **Meldingen** in de rechterbovenhoek van de portal. Als de kluis is gemaakt, is deze zichtbaar in de lijst met Recovery Services-kluizen. Als uw kluis niet wordt weergegeven, selecteert u **Vernieuwen**.

     ![De lijst met back-upkluizen vernieuwen](./media/backup-create-rs-vault/refresh-button.png)

>[!IMPORTANT]
> We raden u ten zeerste aan de standaardinstellingen voor **Type opslagreplicatie** en **Beveiligingsinstellingen** te controleren voordat u back-ups in de kluis configureert. Zie de sectie [Opslagredundantie instellen](../articles/backup/backup-create-rs-vault.md#set-storage-redundancy) voor meer informatie.