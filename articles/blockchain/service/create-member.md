---
title: Een Azure Blockchain Service-lid maken - Azure Portal
description: Maak een Azure Blockchain Service-lid voor een blockchain-consortium behulp van Azure Portal.
ms.date: 07/16/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.custom: references_regions
ms.openlocfilehash: 8a7f5aaea56f34e8107664ab786a14b59cd1cb7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91292724"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-the-azure-portal"></a>Quickstart: Een Azure Blockchain Service-blockchain-lid maken met behulp van Azure Portal

In deze snelstart implementeert u een nieuw blockchain-lid en -consortium in Azure Blockchain Service met behulp van Azure Portal.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Geen.

## <a name="create-a-blockchain-member"></a>Een blockchain-lid maken

Een lid van de Azure Blockchain Service is een blockchain-knooppunt in een privé-blockchain-netwerk van een consortium. Bij het inrichten van een lid kunt u een consortium-netwerk maken of het lid aan een netwerk toevoegen. U hebt voor een consortium-netwerk ten minste één lid nodig. Het aantal blockchain-leden dat deelnemers nodig hebben, is afhankelijk van uw scenario. Consortium-deelnemers kunnen een of meer blockchain-leden hebben of ze kunnen leden delen met andere deelnemers. Zie [Azure Blockchain Service-consortium](consortium.md) voor meer informatie over consortiums.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer in de linkerbovenhoek van Azure Portal **Een resource maken**.
1. Selecteer **Blockchain** > **Azure Blockchain Service (preview)** .

    ![Service maken](./media/create-member/create-member.png)

    Instelling | Beschrijving
    --------|------------
    Abonnement | Selecteer het Azure-abonnement dat u wilt gebruiken voor uw service. Als u meerdere abonnementen hebt, kiest u het abonnement waarin u wordt gefactureerd voor de resource.
    Resourcegroep | U kunt een nieuwe resourcegroepnaam maken of een bestaande naam uit uw abonnement kiezen.
    Regio | Kies een regio om het lid te maken. Alle leden van het consortium moeten zich op dezelfde locatie bevinden. Functies zijn mogelijk niet in alle regio's beschikbaar. Azure Blockchain Data Manager is beschikbaar in de volgende Azure-regio's: VS - oost en Europa - west.
    Protocol | Azure Blockchain Service (preview) ondersteunt momenteel het Quorum-protocol.
    Consortium | Voer een unieke naam in voor een nieuw consortium. Als u een consortium wilt toevoegen via een uitnodiging, kiest u het consortium waarvan u lid wilt worden. Zie [Azure Blockchain Service-consortium](consortium.md) voor meer informatie over consortiums.
    Naam | Kies een unieke naam voor het lid van de Azure Blockchain Service. De blockchain-naam mag alleen kleine letters en cijfers bevatten. Het eerste teken moet een letter zijn. De waarde moet minimaal 2 en maximaal 20 tekens lang zijn.
    Wachtwoord van het lidaccount | Het wachtwoord van het lidaccount wordt gebruikt voor het versleutelen van de persoonlijke sleutel voor het Ethereum-account dat voor het lid wordt gemaakt. U gebruikt het lidaccount en het wachtwoord van het lidaccount voor het beheer van consortiums.
    Prijzen | De knooppuntconfiguratie en kosten voor uw nieuwe service. Selecteer de koppeling **Wijzigen**  om te kiezen tussen de lagen **Standard** en **Basic**. Gebruik de categorie *Basic* voor ontwikkeling, tests en het testen van concepten. Gebruik de categorie *Standard* voor implementaties van productiekwaliteit. U moet de categorie *Standaard* ook gebruiken als u Blockchain Data Manager gebruikt of een groot aantal privétransacties verzendt. Wanneer een lid is gemaakt, kan de prijscategorie niet meer worden gewijzigd van Basic in Standard en andersom.
    Wachtwoord voor het knooppunt | Het wachtwoord voor het standaardtransactieknooppunt van het lid. Gebruik het wachtwoord voor basisverificatie als u verbinding maakt met het openbare eindpunt van het standaardtransactieknooppunt van het blockchain-lid.

1. Selecteer **Beoordelen en maken** om de instellingen te valideren. Selecteer **Maken** om de service in te richten. Het inrichten duurt ongeveer tien minuten.
1. Selecteer **Meldingen** op de werkbalk om het implementatieproces te bewaken.
1. Ga na de implementatie naar het blockchain-lid.

Selecteer **Overzicht**, zodat u de basisinformatie over uw service kunt weergeven, inclusief het RootContract-adres en het lidaccount.

![Overzicht van Blockchain-leden](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Resources opschonen

Voor de volgende snelstart of zelfstudie kunt u het lid gebruiken dat u hebt gemaakt. Als u ze niet meer nodig hebt, kunt u alle resources die u voor de snelstart hebt gemaakt, verwijderen door resourcegroep `myResourceGroup` te verwijderen.

Zo verwijdert u de resourcegroep:

1. Ga in de Azure Portal naar **Resourcegroep** in het linkernavigatievenster en selecteer de resourcegroep die u wilt verwijderen.
2. Selecteer **Resourcegroep verwijderen**. Controleer de verwijdering door de naam van de resourcegroep in te voeren en **Verwijderen** te selecteren.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een lid van de Azure Blockchain Service en een nieuw consortium geïmplementeerd. Probeer de volgende quickstart om de Azure Blockchain Development Kit voor Ethereum te gebruiken om een koppeling te maken met een lid van de Azure Block Chain Service.

> [!div class="nextstepaction"]
> [Visual Studio Code gebruiken om verbinding te maken met Azure Blockchain Service](connect-vscode.md)
