---
title: Block Chain-App-versie beheer-Azure Block Chain workbench
description: Toepassings versies gebruiken in azure Block Chain Workbench preview.
ms.date: 11/20/2019
ms.topic: how-to
ms.reviewer: brendal
ms.openlocfilehash: 2a70112fd0ab6e2f664ca48265c121936b01e58b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85209875"
---
# <a name="azure-blockchain-workbench-preview-application-versioning"></a>Azure Block Chain Workbench preview-versie van de toepassing

U kunt meerdere versies van een Azure Block Chain Workbench preview-app maken en gebruiken. Als er meerdere versies van dezelfde toepassing worden geüpload, is er een versie geschiedenis beschikbaar en kunnen gebruikers kiezen welke versie ze willen gebruiken.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* Een Blockchain Workbench-implementatie. Zie [Azure Blockchain Workbench implementeren](deploy.md) voor meer informatie.
* Een geïmplementeerde blockchain-toepassing in Blockchain Workbench. Zie [Een blockchain-toepassing maken in Azure Blockchain Workbench](create-app.md) voor meer informatie.

## <a name="add-an-app-version"></a>Een app-versie toevoegen

Als u een nieuwe versie wilt toevoegen, uploadt u de nieuwe configuratie-en slimme contract bestanden naar Block Chain Workbench.

1. Ga in een webbrowser naar het webadres van Blockchain Workbench, `https://{workbench URL}.azurewebsites.net/`Zie [Block Chain WORKBENCH web URL](deploy.md#blockchain-workbench-web-url) (Engelstalig) voor meer informatie over het vinden van uw Block Chain Workbench-webadres
2. Meld u aan als een [Blockchain Workbench-beheerder](manage-users.md#manage-blockchain-workbench-administrators).
3. Selecteer de Block Chain-toepassing die u wilt bijwerken met een andere versie.
4. Selecteer **Versie toevoegen**. Het deel venster **versie toevoegen** wordt weer gegeven.
5. Kies de nieuwe versie contract configuratie en de contract code bestanden die u wilt uploaden. Het configuratiebestand wordt automatisch gevalideerd. Herstel eventuele validatie fouten voordat u de toepassing implementeert.
6. Selecteer **versie toevoegen** om de nieuwe versie van de Block Chain-toepassing toe te voegen.

    ![Een nieuwe versie toevoegen](media/version-app/add-version.png)

De implementatie van de Block Chain-toepassing kan enkele minuten duren. Wanneer de implementatie is voltooid, vernieuwt u de toepassings pagina. Als u de toepassing kiest en de knop **versie geschiedenis** selecteert, wordt de versie geschiedenis van de toepassing weer gegeven.

> [!IMPORTANT]
> Eerdere versies van de toepassing zijn uitgeschakeld. U kunt eerdere versies afzonderlijk opnieuw inschakelen.
>
> Mogelijk moet u leden opnieuw toevoegen aan toepassings rollen als er wijzigingen zijn aangebracht in de toepassings rollen in de nieuwe versie.

## <a name="using-app-versions"></a>App-versies gebruiken

De meest recente versie van de toepassing wordt standaard gebruikt in Block Chain Workbench. Als u een eerdere versie van een toepassing wilt gebruiken, moet u eerst de versie kiezen op de pagina toepassing.

1. Selecteer in de sectie Block Chain Workbench-toepassing het selectie vakje van de toepassing die het contract bevat dat u wilt gebruiken. Als vorige versies zijn ingeschakeld, is de knop versie geschiedenis beschikbaar.
2. Selecteer de knop **versie geschiedenis** .
3. Kies in het deel venster versie geschiedenis de versie van de toepassing door de koppeling in de kolom *datum gewijzigd* te selecteren.

    ![Een vorige versie kiezen](media/version-app/use-version.png)

    U kunt nieuwe contracten maken of acties ondernemen voor eerdere versies van contracten. De versie van de toepassing wordt weer gegeven na de naam van de toepassing en er wordt een waarschuwing weer gegeven over de oudere versie.

## <a name="next-steps"></a>Volgende stappen

* [Problemen met Azure Block Chain Workbench oplossen](troubleshooting.md)
