---
title: Een verwijderd opslagaccount herstellen
titleSuffix: Azure Storage
description: Meer informatie over het herstellen van een verwijderd opslag account in de Azure Portal.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: f57cd3361d7888d9d7f747955257d96282274fd6
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97357342"
---
# <a name="recover-a-deleted-storage-account"></a>Een verwijderd opslagaccount herstellen

Een verwijderd opslag account kan in sommige gevallen worden hersteld vanuit de Azure Portal. Als u een opslag account wilt herstellen, moet aan de volgende voor waarden worden voldaan:

- Het opslag account is in de afgelopen 14 dagen verwijderd.
- Het opslag account is gemaakt met het Azure Resource Manager-implementatie model.
- Er is geen nieuw opslag account gemaakt met dezelfde naam nadat het oorspronkelijke account is verwijderd.

Voordat u probeert een verwijderd opslag account te herstellen, moet u ervoor zorgen dat de resource groep voor dat account bestaat. Als de resource groep is verwijderd, moet u deze opnieuw maken. Het herstellen van een resource groep is niet mogelijk. Zie [resource groepen beheren](../../azure-resource-manager/management/manage-resource-groups-portal.md)voor meer informatie over.

Als het verwijderde opslag account een door de klant beheerde sleutels met Azure Key Vault heeft gebruikt en de sleutel kluis ook is verwijderd, moet u de sleutel kluis herstellen voordat u het opslag account herstelt. Zie [Azure Key Vault Recovery Overview (overzicht van herstel](../../key-vault/general/key-vault-recovery.md)) voor meer informatie.

> [!IMPORTANT]
> Het herstellen van een verwijderd opslag account is niet gegarandeerd. Herstel is een beste poging. Micro soft adviseert het vergren delen van resources om te voor komen dat onbedoeld account wordt verwijderd. Zie [resources vergren delen om wijzigingen te voor komen](../../azure-resource-manager/management/lock-resources.md)voor meer informatie over bron vergrendelingen.
>
> Een andere best practice om te voor komen dat het account wordt verwijderd, is het beperken van het aantal gebruikers dat gemachtigd is om een account te verwijderen via op rollen gebaseerd toegangs beheer (Azure RBAC). Zie [Aanbevolen procedures voor Azure RBAC](../../role-based-access-control/best-practices.md)voor meer informatie.

## <a name="recover-a-deleted-account-from-the-azure-portal"></a>Een verwijderd account herstellen vanuit de Azure Portal

Voer de volgende stappen uit om een verwijderd opslag account te herstellen vanuit een ander opslag account:

1. Ga naar de overzichts pagina voor een bestaand opslag account in de Azure Portal.
1. Selecteer in de sectie **ondersteuning en probleem oplossing** de optie **verwijderd account herstellen**.
1. Selecteer in de vervolg keuzelijst het account dat u wilt herstellen, zoals wordt weer gegeven in de volgende afbeelding. Als het opslag account dat u wilt herstellen, zich niet in de vervolg keuzelijst bevindt, kan het niet worden hersteld.

    :::image type="content" source="media/storage-account-recover/recover-account-portal.png" alt-text="Scherm afbeelding die laat zien hoe u het opslag account in Azure Portal kunt herstellen":::

1. Selecteer de knop **herstellen** om het account te herstellen. In de portal wordt een melding weer gegeven dat het herstel wordt uitgevoerd.

## <a name="recover-a-deleted-account-via-a-support-ticket"></a>Een verwijderd account herstellen via een ondersteunings ticket

1. Ga in het Azure Portal naar **Help en ondersteuning**.
1. Selecteer **Nieuwe ondersteuningsaanvraag**.
1. Selecteer op het tabblad **basis beginselen** in het veld **probleem type** de optie **technisch**.
1. Selecteer in het veld **abonnement** het abonnement dat het verwijderde opslag account bevat.
1. Selecteer in het veld **service** de optie **opslag account beheer**.
1. Selecteer in het veld **resource** een bron van een opslag account. Het verwijderde opslag account wordt niet weer gegeven in de lijst.
1. Voeg een korte samen vatting van het probleem toe.
1. Selecteer in het veld **probleem type** de optie **verwijderen en herstellen**.
1. Selecteer in het veld **type probleem** de optie **verwijderde opslag account herstellen**. In de volgende afbeelding ziet u een voor beeld van het tabblad **basis informatie** dat wordt ingevuld:

    :::image type="content" source="media/storage-account-recover/recover-account-support-basics.png" alt-text="Scherm afbeelding die laat zien hoe u een opslag account herstelt via het tabblad ondersteuning van het ticket":::

1. Vervolgens gaat u naar het tabblad **oplossingen** en selecteert u **herstel door de klant beheerde opslag accounts**, zoals wordt weer gegeven in de volgende afbeelding:

    :::image type="content" source="media/storage-account-recover/recover-account-support-solutions.png" alt-text="Scherm afbeelding die laat zien hoe u een opslag account herstelt via het tabblad support ticket-Solutions":::

1. Selecteer in de vervolg keuzelijst het account dat u wilt herstellen, zoals wordt weer gegeven in de volgende afbeelding. Als het opslag account dat u wilt herstellen, zich niet in de vervolg keuzelijst bevindt, kan het niet worden hersteld.

    :::image type="content" source="media/storage-account-recover/recover-account-support.png" alt-text="Scherm afbeelding die laat zien hoe u een opslag account herstelt via een ondersteunings ticket":::

1. Selecteer de knop **herstellen** om het account te herstellen. In de portal wordt een melding weer gegeven dat het herstel wordt uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van opslagaccounts](storage-account-overview.md)
- [Een opslagaccount maken](storage-account-create.md)
- [Upgraden naar een V2-opslagaccount voor algemeen gebruik](storage-account-upgrade.md)
- [Een Azure Storage-account naar een andere regio verplaatsen](storage-account-move.md)