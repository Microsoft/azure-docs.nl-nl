---
title: Integration Runtimes maken en beheren
description: In dit artikel worden de stappen beschreven voor het maken en beheren van Integration Runtimes in azure controle sfeer liggen.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/16/2020
ms.openlocfilehash: 0d365787ea3603ef0adb8ad0b48bef9792ffb003
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552503"
---
# <a name="create-and-manage-a-self-hosted-integration-runtime"></a>Een zelf-hostende Integration runtime maken en beheren

In dit artikel wordt beschreven hoe u een zelf-hostende Integration runtime (SHIR) maakt en beheert om te helpen bij het scannen van gegevens bronnen.

## <a name="create-a-self-hosted-integration-runtime"></a>Een zelf-hostende Integration Runtime maken

1. Op de start pagina van controle sfeer liggen Studio selecteert u **beheer centrum** in het navigatie deel venster links.

2. Selecteer onder **bronnen en scannen** in het linkerdeel venster de optie **Integration Runtimes** en selecteer **+ Nieuw**.

    :::image type="content" source="media/manage-integration-runtimes/select-integration-runtimes.png" alt-text="Klik op IR.":::

3. Op de pagina **Integration runtime Setup** selecteert u **zelf-host** om een Self-Hosted IR te maken en selecteert u vervolgens **door gaan**.

    :::image type="content" source="media/manage-integration-runtimes/select-self-hosted-ir.png" alt-text="nieuwe SHIR maken.":::

4. Voer een naam in voor uw IR en selecteer maken.

5. Volg de stappen in de sectie **hand matige installatie** op de pagina **instellingen van Integration runtime** . U moet de Integration runtime downloaden van de download site naar een virtuele machine of computer waarop u deze wilt uitvoeren.

    :::image type="content" source="media/manage-integration-runtimes/integration-runtime-settings.png" alt-text="sleutel ophalen":::

    a. Kopieer en plak de verificatie sleutel.
        
    b. Down load de zelf-hostende Integration runtime van [Azure Data Factory Integration runtime](https://www.microsoft.com/download/details.aspx?id=39717) op een lokale Windows-computer. Voer het installatieprogramma uit.
        
    c. Plak op de pagina **Integration runtime (zelf-hostend) registreren** een van de twee sleutels die u eerder hebt opgeslagen en selecteer **registreren**.

    :::image type="content" source="media/manage-integration-runtimes/register-integration-runtime.png" alt-text="invoer sleutel.":::

    d. Selecteer **Voltooien** op de pagina **Nieuw knooppunt voor Integration Runtime (zelf-hostend)** .

6. Nadat de zelf-hostende Integration runtime is geregistreerd, ziet u het volgende venster:

    :::image type="content" source="media/manage-integration-runtimes/successfully-registered.png" alt-text="de registratie is voltooid.":::

## <a name="manage-a-self-hosted-integration-runtime"></a>Een zelf-hostende Integration runtime beheren

U kunt een zelf-hostende Integration runtime bewerken door te navigeren naar **Integration Runtimes** in het **Management Center**, de IR te selecteren en vervolgens op bewerken te klikken. U kunt nu de beschrijving bijwerken, de sleutel kopiëren of nieuwe sleutels genereren.

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime.png" alt-text="Bewerk IR.":::

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime-settings.png" alt-text="IR-gegevens bewerken.":::

U kunt een zelf-hostende Integration runtime verwijderen door te navigeren naar **Integration Runtimes** in het Management Center, de IR te selecteren en vervolgens op **verwijderen** te klikken. Wanneer een IR wordt verwijderd, mislukken alle lopende scans die erop worden gebaseerd.

## <a name="next-steps"></a>Volgende stappen

* [Hoe scannen verwijderde assets detecteert](concept-detect-deleted-assets.md)
