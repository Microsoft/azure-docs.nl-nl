---
title: Een Azure API Management-exemplaar maken met Visual Studio | Microsoft Docs
description: Visual Studio Code om een Azure API Management-exemplaar te maken.
ms.service: api-management
ms.workload: integration
author: vladvino
ms.author: apimpm
ms.topic: quickstart
ms.date: 09/14/2020
ms.openlocfilehash: fdf7e842959e09a8ec35017031f745cb16d13c6d
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400400"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-using-visual-studio-code"></a>Quickstart: Een nieuw exemplaar van het Azure API Management-service-exemplaar maken met Visual Studio Code

APIM (API Management) helpt organisaties bij het publiceren van API's naar externe en interne ontwikkelaars, en naar partnerontwikkelaars om alle mogelijkheden van hun gegevens en services te ontsluiten. API Management beschikt over de competenties die belangrijk zijn voor een geslaagd API-programma via ontwikkelaarsbetrokkenheid, zakelijke inzichten, analytische gegevens, beveiliging en bescherming. Met APIM kunt u moderne API-gateways maken en beheren voor bestaande back-endservices die op elke willekeurige locatie worden gehost. Bekijk het [Overzicht](api-management-key-concepts.md) voor meer informatie.

In deze quickstart worden de stappen beschreven voor het maken van een nieuw API Management-exemplaar met behulp van de *preview van de Azure API Management-extensie* voor Visual Studio Code. U kunt de extensie ook gebruiken om veelgebruikte beheerbewerkingen uit te voeren op uw API Management-exemplaar.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Zorg er bovendien voor dat u het volgende hebt geïnstalleerd:

- [Visual Studio Code](https://code.visualstudio.com/)

- [Azure API Management Extension for Visual Studio Code (Preview)](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement&ssr=false#overview) (Azure API Management-extensie voor Visual Studio Code (preview))

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Start Visual Studio Code en open de Azure-extensie. (Als u het Azure-pictogram niet op de activiteitenbalk ziet, controleert u of de *Azure API Management*-extensie is ingeschakeld.)

Selecteer **Aanmelden bij Azure...** om een browservenster te openen en u aan te melden bij uw Microsoft-account.

![Aanmelden bij Azure vanuit de API Management-extensie voor Visual Studio Code](./media/vscode-create-service-instance/vscode-apim-login.png)

## <a name="create-an-api-management-service"></a>Een API Management-service maken

Zodra u bent aangemeld bij uw Microsoft-account, wordt in het venster van de *Azure: API Management*-verkenner uw Azure-abonnement(en) weergegeven.

Klik met de rechtermuisknop op het abonnement dat u wilt gebruiken en selecteer **API Management maken in Azure**.

![Wizard API Management maken in VS Code](./media/vscode-create-service-instance/vscode-apim-create.png)

Geef in het deel venster dat wordt geopend een naam op voor het nieuwe API Management-exemplaar. De naam moet globaal uniek zijn in Azure en 1 tot 50 alfanumerieke tekens en/of afbreekstreepjes bevatten, en beginnen met een letter en eindigen op een alfanumeriek teken.

Er wordt een nieuw API Management-exemplaar (en bovenliggende resourcegroep) met de opgegeven naam gemaakt. Standaard wordt het exemplaar in de regio *US - west* gemaakt met SKU *Verbruik*.

> [!TIP]
> Als u **Geavanceerd maken** in de *instellingen voor de Azure API Management-extensie* inschakelt, kunt u ook een [API Management SKU](https://azure.microsoft.com/pricing/details/api-management/), een [Azure-regio](https://status.azure.com/en-us/status) en een [resourcegroep](../azure-resource-manager/management/overview.md) opgeven om uw API Management-exemplaar te implementeren.
>
> Terwijl het minder dan een minuut kost om de SKU *Verbruik* in te richten, duurt het meestal 30 tot 40 minuten om te maken.

U bent nu klaar om uw eerste API te importeren en te publiceren. Daarnaast kunt u ook veelvoorkomende API Management-bewerkingen uitvoeren binnen de extensie voor Visual Studio Code. Raadpleeg [de zelfstudie](visual-studio-code-tutorial.md) voor meer.

![Nieuw gemaakt API Management-exemplaar in het deelvenster API Management-extensie in VS Code](./media/vscode-create-service-instance/vscode-apim-instance.png)

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder het API Management-exemplaar als u dit niet meer nodig hebt, door met de rechtermuisknop te klikken en **Openen in portal** te selecteren om de [API Management-service](get-started-create-service-instance.md#clean-up-resources) en de bijbehorende resourcegroep te verwijderen.

U kunt ook **API Management verwijderen** selecteren om alleen het API Management-exemplaar te verwijderen (met deze bewerking wordt de resourcegroep niet verwijderd).

![API Management-exemplaar uit VS Code verwijderen](./media/vscode-create-service-instance/vscode-apim-delete.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [API's importeren en beheren met de API Management-extensie](visual-studio-code-tutorial.md)
