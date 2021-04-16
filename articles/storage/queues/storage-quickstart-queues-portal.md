---
title: 'Quickstart: Azure Storage-wachtrijen maken in de portal'
description: Gebruik de Azure-portal om een wachtrij te maken. Gebruik vervolgens de Azure-portal om een bericht toe te voegen, de berichteigenschappen te bekijken en het bericht uit de wachtrij te verwijderen.
author: twooley
ms.author: twooley
ms.reviewer: dineshm
ms.date: 08/13/2020
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom:
- mode-portal
ms.openlocfilehash: f0e7a5a514f2d68fce025ea9fb354f29fa068561
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534400"
---
# <a name="quickstart-create-a-queue-and-add-a-message-with-the-azure-portal"></a>Quickstart: Een wachtrij maken en een bericht toevoegen met de Azure-portal

In deze quickstart leert u de [Azure-portal](https://portal.azure.com/) te gebruiken om een wachtrij te maken in Azure Storage, en om berichten toe te voegen aan en weer te verwijderen uit de wachtrij.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="create-a-queue"></a>Een wachtrij maken

Als u een wachtrij wilt maken in de Azure-portal, volgt u deze stappen:

1. Navigeer naar het nieuwe opslagaccount in Azure Portal.
2. Blader in het linkermenu van het opslagaccount naar de sectie **Queue Storage**, en selecteer vervolgens **Wachtrijen**.
3. Selecteer de knop **+ Wachtrij**.
4. Typ een naam voor de nieuwe wachtrij. De wachtrijnaam mag alleen uit kleine letters bestaan, moet beginnen met een letter of cijfer en mag alleen letters, cijfers en het streepje (-) bevatten.
6. Selecteer **OK** om de wachtrij te maken.

    ![Schermopname van het maken van een wachtrij in de Azure-portal](media/storage-quickstart-queues-portal/create-queue.png)

## <a name="add-a-message"></a>Een bericht toevoegen

Voeg vervolgens een bericht toe aan de nieuwe wachtrij. Een bericht kan maximaal 64 KB groot zijn.

1. Selecteer de nieuwe wachtrij uit de lijst met wachtrijen in het opslagaccount.
1. Selecteer de knop **+ Bericht toevoegen** om een bericht toe te voegen aan de wachtrij. Voer een bericht in het veld **Berichttekst** in.
1. Geef aan wanneer het bericht verloopt. Geldige waarden die kunnen worden ingevoerd in het veld **Verloopt over** liggen tussen 1 seconde en 7 dagen. Selecteer **Bericht verloopt nooit** om aan te geven dat een bericht in de wachtrij blijft staan totdat het expliciet wordt verwijderd.
1. Geef aan of het bericht als Base64 moet worden gecodeerd. Codering van binaire gegevens wordt aanbevolen.
1. Selecteer de knop **OK** om het bericht toe te voegen.

    ![Schermopname van het toevoegen van een bericht aan een wachtrij](media/storage-quickstart-queues-portal/add-message.png)

## <a name="view-message-properties"></a>Eigenschappen van berichten bekijken

Nadat u een bericht hebt toegevoegd, wordt in de Azure-portal een lijst van alle berichten in de wachtrij weergegeven. U kunt de bericht-ID, de inhoud van het bericht, de tijd waarop het bericht werd ingevoegd en de tijd waarop het bericht verloopt bekijken. U kunt ook zien hoe vaak dit bericht uit de wachtrij is verwijderd.

![Schermopname met berichteigenschappen](media/storage-quickstart-queues-portal/view-message-properties.png)

## <a name="dequeue-a-message"></a>Een bericht uit de wachtrij verwijderen

U kunt vanuit de Azure-portal een bericht uit het begin van de wachtrij verwijderen. Wanneer u een bericht uit de wachtrij verwijdert, is de verwijdering definitief.

Bij het verwijderen van berichten uit de wachtrij wordt altijd het oudste bericht in de wachtrij verwijderd.

![Schermopname van het verwijderen van een bericht uit de portal](media/storage-quickstart-queues-portal/dequeue-message.png)

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een wachtrij maakt, een bericht toevoegt, eigenschappen van berichten bekijkt en een bericht uit de wachtrij in de Azure-portal verwijdert.

> [!div class="nextstepaction"]
> [Wat is Azure Queue Storage?](storage-queues-introduction.md)
