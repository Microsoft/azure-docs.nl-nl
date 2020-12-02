---
title: 'Azure-snelstart: Een geheim uit Key Vault instellen en ophalen met Azure Portal | Microsoft Docs'
description: Snelstart waarin wordt getoond hoe u een geheim uit Azure Key Vault instelt en ophaalt met behulp van de Azure Portal
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 3d7e6357fd8f1091509cbf27875c028d3af310cb
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445907"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-the-azure-portal"></a>Quickstart: Een geheim uit Azure Key Vault instellen en ophalen met behulp van de Azure Portal

Azure Key Vault is een cloudservice die werkt als een beveiligd archief voor geheimen. U kunt veilig sleutels, wachtwoorden, certificaten en andere geheime informatie opslaan. Azure-sleutelkluizen kunnen worden gemaakt en beheerd via Azure Portal. In deze snelstart kunt u een sleutelkluis maken en daarin een geheim opslaan. 

Zie voor meer informatie 
- [Overzicht van Key Vault](../general/overview.md)
- [Overzicht van geheimen](about-secrets.md).

## <a name="prerequisites"></a>Vereisten

U hebt een Azure-abonnement nodig voor toegang tot Azure Key Vault. Als u nog geen abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

Alle toegang tot geheimen vindt plaats via Azure Key Vault. Voor deze snelstart gaat u een sleutelkluis maken met de [Azure Portal](../general/quick-create-portal.md), [Azure CLI](../general/quick-create-cli.md) of [Azure PowerShell](../general/quick-create-powershell.md).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="add-a-secret-to-key-vault"></a>Een geheim toevoegen aan Key Vault

Volg de stappen om een geheim toe te voegen aan de kluis:

1. Navigeer in de Azure Portal naar uw nieuwe sleutelkluis
1. Selecteer op de Key Vault-instellingspagina **Geheimen**.
1. Klik op **Genereren/importeren**.
1. Kies in het scherm **Een geheim maken** de volgende waarden:
    - **Uploadopties**: Handmatig.
    - **Naam**: Typ een naam voor het nieuwe geheim. De geheime naam moet uniek zijn binnen een Key Vault. De naam moet een reeks van 1-127 tekens zijn die begint met een letter en alleen 0-9, a-z, A-Z en - bevat. Zie voor meer informatie over naamgeving [Key Vault-objecten, -id's en -versiebeheer](../general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning)
    - **Waarde**: Typ een waarde voor het nieuwe geheim. Key Vault-API's accepteert en retourneert waarden als tekenreeksen. 
    - Houd voor de overige waarden de standaardwaarden aan. Klik op **Create**.

Zodra u het bericht ontvangt dat het geheim met succes is gemaakt, kunt u erop klikken in de lijst. 

Zie voor meer informatie over geheimen en kenmerken [Over Azure Key Vault-geheimen](./about-secrets.md)

## <a name="retrieve-a-secret-from-key-vault"></a>Een geheim lezen uit Key Vault

Als u op de huidige versie klikt, ziet u de waarde die u hebt opgegeven in de vorige stap.

![Geheimeigenschappen](../media/quick-create-portal/current-version-hidden.png)

Door in het rechterdeelvenster op 'Geheimwaarde weergeven' te klikken, kunt u de verborgen waarde zien. 

![Geheime waarde zichtbaar gemaakt](../media/quick-create-portal/current-version-shown.png)

U kunt ook [Azure CLI]() gebruiken of [Azure PowerShell]() om een eerder gemaakt geheim op te halen.

## <a name="clean-up-resources"></a>Resources opschonen

Andere Key Vault-snelstarts en zelfstudies bouwen voort op deze snelstart. Als u van plan bent om verder te gaan met volgende snelstarts en zelfstudies, kunt u deze resources intact laten.
Als u die niet meer nodig hebt, verwijdert u de resourcegroep. Hierdoor worden ook de sleutelkluis en de gerelateerde resources verwijderd. De resourcegroep verwijderen via de portal:

1. Voer de naam van uw resourcegroep in het vak Zoeken bovenaan de portal in. Wanneer u de in deze snelstart gebruikte resourcegroep in de zoekresultaten ziet, selecteert u deze.
2. Selecteer **Resourcegroep verwijderen**.
3. Typ in het vak **TYP DE NAAM VAN DE RESOURCEGROEP** de naam van de resourcegroep en selecteer **Verwijderen**.

> [!NOTE]
> Het is belangrijk te weten dat wanneer een geheim, sleutel, certificaat of sleutelkluis wordt verwijderd, dit kan worden hersteld voor een configureerbare periode van 7 tot 90 kalender dagen. Als er geen configuratie is opgegeven, wordt de standaard herstelperiode ingesteld op 90 dagen. Dit biedt gebruikers voldoende tijd om een onbedoeld geheim te verwijderen en te reageren. Voor meer informatie over het verwijderen en herstellen van sleutelkluizen en sleutelkluisobjecten raadpleegt u [Overzicht van voorlopig verwijderen in Azure Key Vault](../general/soft-delete-overview.md)

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Key Vault gemaakt en daar een geheim in opgeslagen. Voor meer informatie over Key Vault en hoe u Key Vault integreert met uw toepassingen gaat u verder naar de artikelen hieronder.

- Lees een [Overzicht van Azure Key Vault](../general/overview.md)
- Lees [Veilige toegang tot een sleutelkluis](../general/secure-your-key-vault.md)
- Zie [Key Vault gebruiken met App Service-web-app](../general/tutorial-net-create-vault-azure-web-app.md)
- Zie [Key Vault gebruiken met toepassing die is geïmplementeerd op VM](../general/tutorial-net-virtual-machine.md)
- Zie de [Gids voor Azure Key Vault-ontwikkelaars](../general/developers-guide.md)
- Bekijk de [best practices voor Azure Key Vault](../general/best-practices.md)