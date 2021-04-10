---
title: 'Azure Quickstart: Een certificaat uit Key Vault instellen en ophalen met behulp van Azure Portal | Microsoft Docs'
description: Quickstart waarin wordt getoond hoe u een certificaat uit Azure Key Vault instelt en ophaalt met behulp van de Azure Portal
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/24/2020
ms.author: mbaldwin
ms.openlocfilehash: e55c0832638105ad681f74cbeb6429a6704b7fb2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97935135"
---
# <a name="quickstart-set-and-retrieve-a-certificate-from-azure-key-vault-using-the-azure-portal"></a>Quickstart: Een certificaat uit Azure Key Vault instellen en ophalen met behulp van de Azure-portal

Azure Key Vault is een cloudservice die werkt als een beveiligd archief voor geheimen. U kunt veilig sleutels, wachtwoorden, certificaten en andere geheime informatie opslaan. Azure-sleutelkluizen kunnen worden gemaakt en beheerd via Azure Portal. In deze quickstart kunt u een sleutelkluis maken en daarin een certificaat opslaan. Raadpleeg het [Overzicht](../general/overview.md) voor meer informatie over Key Vault.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-a-vault"></a>Een kluis maken

1. Selecteer in het menu van de Azure-portal of op de **startpagina** de optie **Een resource maken**.
2. Typ **Sleutelkluis** in het zoekvak.
3. Kies **Sleutelkluis** in de lijst met resultaten.
4. Kies **Maken** in de sectie Sleutelkluis.
5. Geef in de sectie **Sleutelkluis maken** de volgende gegevens op:
    - **Naam**: geef een unieke naam op. Voor deze quickstart gebruiken we **Example-Vault**. 
    - **Abonnement**: Kies een abonnement.
    - Kies **Nieuwe maken** bij **Resourcegroep** en voer de naam van een resourcegroep in.
    - Kies een locatie in de vervolgkeuzelijst **Locatie**.
    - Houd voor de overige opties de standaardwaarden aan.
6. Selecteer na het opgeven van de bovenstaande gegevens **Maken**.

Let op de onderstaande twee eigenschappen:

* **Kluisnaam**: In het voorbeeld is dat **Example-Vault**. U gebruikt deze naam voor andere stappen.
* **Kluis-URI**: in het voorbeeld is dat `https://example-vault.vault.azure.net/`. Toepassingen die via de REST API gebruikmaken van uw kluis, moeten deze URI gebruiken.

Vanaf dit punt is uw Azure-account nu als enige gemachtigd om bewerkingen op deze nieuwe kluis uit te voeren.

![Uitvoer nadat het maken van de sleutelkluis is voltooid](../media/certificates/quick-create-portal/vault-properties.png)

## <a name="add-a-certificate-to-key-vault"></a>Een certificaat toevoegen aan Key Vault

Als u een certificaat wilt toevoegen aan de kluis, hoeft u maar een paar extra stappen uit te voeren. In dit geval voegen we een zelfondertekend certificaat toe dat door een toepassing kan worden gebruikt. Het certificaat wordt **ExampleCertificate** genoemd.

1. Selecteer op de eigenschappenpagina's van de sleutelkluis **Certificaten**.
2. Klik op **Genereren/importeren**.
3. Kies op het scherm **Een certificaat maken** de volgende waarden:
    - **Methode voor het maken van certificaten**: Genereren.
    - **Naam van het certificaat**: ExampleCertificate.
    - **Onderwerp**: CN=ExampleDomain
    - Houd voor de overige waarden de standaardwaarden aan. Klik op **Create**.

Zodra u het bericht ontvangt dat het certificaat met succes is gemaakt, kunt u erop klikken in de lijst. Vervolgens ziet u enkele van de eigenschappen. Als u op de huidige versie klikt, ziet u de waarde die u hebt opgegeven in de vorige stap.

![Certificaateigenschappen](../media/certificates/quick-create-portal/current-version-hidden.png)

## <a name="export-certificate-from-key-vault"></a>Certificaat uit Key Vault exporteren
Als u klikt op de knop “Downloaden in CER-indeling” of “Downloaden in PFX/PEM-indeling”, kunt u het certificaat downloaden. 

![Certificaat downloaden](../media/certificates/quick-create-portal/current-version-shown.png)

## <a name="clean-up-resources"></a>Resources opschonen

Andere Key Vault-snelstarts en zelfstudies bouwen voort op deze snelstart. Als u van plan bent om verder te gaan met volgende snelstarts en zelfstudies, kunt u deze resources intact laten.
Als u die niet meer nodig hebt, verwijdert u de resourcegroep. Hierdoor worden ook de sleutelkluis en de gerelateerde resources verwijderd. De resourcegroep verwijderen via de portal:

1. Voer de naam van uw resourcegroep in het vak Zoeken bovenaan de portal in. Wanneer u de in deze snelstart gebruikte resourcegroep in de zoekresultaten ziet, selecteert u deze.
2. Selecteer **Resourcegroep verwijderen**.
3. Typ in het vak **TYP DE NAAM VAN DE RESOURCEGROEP** de naam van de resourcegroep en selecteer **Verwijderen**.


## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Key Vault gemaakt en daar een certificaat in opgeslagen. Voor meer informatie over Key Vault en hoe u Key Vault integreert met uw toepassingen gaat u verder naar de artikelen hieronder.

- Lees een [Overzicht van Azure Key Vault](../general/overview.md)
- Zie de [Gids voor Azure Key Vault-ontwikkelaars](../general/developers-guide.md)
- Raadpleeg het [Overzicht voor Key Vault-beveiliging](../general/security-overview.md)
