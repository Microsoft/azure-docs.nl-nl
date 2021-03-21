---
title: Gegevens importeren of exporteren met Azure-app configuratie
description: Meer informatie over het importeren of exporteren van configuratie gegevens naar of van Azure-app configuratie. Gegevens uitwisselen tussen uw app-configuratie archief en het code project.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: alkemper
ms.openlocfilehash: 5e4eeb37bb5efa11a656600072e4aa364fc71500
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96931789"
---
# <a name="import-or-export-configuration-data"></a>Configuratiegegevens importeren of exporteren

De configuratie van Azure-app ondersteunt het importeren en exporteren van gegevens. Gebruik deze bewerkingen om met configuratie gegevens in bulk te werken en gegevens uit te wisselen tussen uw app-configuratie archief en het code project. U kunt bijvoorbeeld één app-configuratie archief instellen voor test doeleinden en een andere voor productie. U kunt toepassings instellingen ertussen kopiëren zodat u gegevens niet twee keer hoeft in te voeren.

Dit artikel bevat een hand leiding voor het importeren en exporteren van gegevens met app-configuratie. Als u een doorlopende synchronisatie met uw GitHub-opslag plaats wilt instellen, Bekijk dan onze github- [actie](./concept-github-action.md).

## <a name="import-data"></a>Gegevens importeren

Importeren brengt configuratie gegevens over naar een app-configuratie archief van een bestaande bron. Gebruik de functie Import om gegevens te migreren naar een app-configuratie archief of om gegevens uit meerdere bronnen samen te voegen. App-configuratie ondersteunt het importeren vanuit een JSON-, YAML-of properties-bestand.

Gegevens importeren met behulp van de [Azure Portal](https://portal.azure.com) of de [Azure cli](./scripts/cli-import.md). Volg deze stappen vanuit Azure Portal:

1. Blader naar het configuratie archief van uw app en selecteer **importeren/exporteren** in het menu **bewerkingen** .

1. Selecteer  **bron service**  >  **configuratie bestand** op het tabblad importeren.

1. Selecteer **voor taal** en selecteer het gewenste invoer type.

1. Selecteer het mappictogram en blader **naar het bestand** dat u wilt importeren.

    ![Bestand importeren](./media/import-file.png)

1. Selecteer een **scheidings teken** en geef eventueel een **voor voegsel** op dat moet worden gebruikt voor geïmporteerde sleutel namen.

1. Selecteer desgewenst een **Label**.

1. Selecteer **Toep assen** om de import te volt ooien.

    ![Het import bestand is voltooid](./media/import-file-complete.png)

## <a name="export-data"></a>Gegevens exporteren

Export schrijft configuratie gegevens die zijn opgeslagen in de app-configuratie naar een andere bestemming. Gebruik de functie exporteren om bijvoorbeeld gegevens in een app-configuratie archief op te slaan in een bestand dat is inge sloten met uw toepassings code tijdens de implementatie.

Gegevens exporteren met behulp van de [Azure Portal](https://portal.azure.com) of de [Azure cli](./scripts/cli-export.md). Volg deze stappen vanuit Azure Portal:

1. Blader naar het configuratie archief van uw app en selecteer **importeren/exporteren**.

1. Selecteer op het tabblad **exporteren** het   >  **configuratie bestand** van de doel service.

1. Geef eventueel een **voor voegsel** op en selecteer een **Label** en een tijdstip voor de sleutels die moeten worden geëxporteerd.

1. Selecteer een   >  **scheidings teken** voor het bestands type.

1. Selecteer **Toep assen** om de export te volt ooien.

    ![Het export bestand is voltooid](./media/export-file-complete.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een ASP.NET Core-web-app maken](./quickstart-aspnet-core-app.md)