---
title: 'Zelfstudie: Een certificaat in Key Vault importeren met behulp van de Azure-portal | Microsoft Docs'
description: Zelfstudie waarin u leert hoe u een certificaat importeert in Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc, devx-track-azurecli
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: 626d7f161d23e6105eea0ca160cd2c9be0ed0ea0
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935934"
---
# <a name="tutorial-import-a-certificate-in-azure-key-vault"></a>Zelfstudie: Een certificaat importeren met behulp van Azure Key Vault

Azure Key Vault is een cloudservice die werkt als een beveiligd archief voor geheimen. U kunt veilig sleutels, wachtwoorden, certificaten en andere geheime informatie opslaan. Azure-sleutelkluizen kunnen worden gemaakt en beheerd via Azure Portal. In deze zelfstudie kunt u een sleutelkluis maken en daarin een certificaat importeren. Raadpleeg het [Overzicht](../general/overview.md) voor meer informatie over Key Vault.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een sleutelkluis maken.
> * Een certificaat importeren in Key Vault met de portal.
> * Een certificaat importeren in Key Vault met de CLI.
> * Een certificaat importeren in Key Vault met PowerShell.


Lees voordat u verdergaat eerst [Basisconcepten van Key Vault](../general/basic-concepts.md). 

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
* **Kluis-URI**: in het voorbeeld is dat https://example-vault.vault.azure.net/. Toepassingen die via de REST API gebruikmaken van uw kluis, moeten deze URI gebruiken.

Vanaf dit punt is uw Azure-account nu als enige gemachtigd om bewerkingen op deze nieuwe kluis uit te voeren.

![Uitvoer nadat het maken van de sleutelkluis is voltooid](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="import-a-certificate-to-key-vault"></a>Een certificaat importeren naar Key Vault

Als u een certificaat naar de kluis wilt importeren, moet u een PEM-of PFX-certificaatbestand op schijf hebben. In dit geval importeren we een certificaat met de bestandsnaam **ExampleCertificate**.

> [!IMPORTANT]
> In Azure Key Vault zijn de ondersteunde indelingen voor certificaten PFX en PEM. 
> - .pem-bestandsindeling bevat een of meer x509-certificaatbestanden.
> - pfx-bestandsindeling is een archiefbestandsindeling voor het opslaan van verschillende cryptografische objecten in één bestand, d.w.z. servercertificaat (uitgegeven voor uw domein), een overeenkomende persoonlijke sleutel en kan eventueel een tussenliggende CA bevatten.  

1. Selecteer op de eigenschappenpagina's van de sleutelkluis **Certificaten**.
2. Klik op **Genereren/importeren**.
3. Kies op het scherm **Een certificaat maken** de volgende waarden:
    - **Methode voor het maken van certificaten**: Importeren.
    - **Naam van het certificaat**: ExampleCertificate.
    - **Certificaatbestand uploaden**: selecteer het certificaatbestand van de schijf
    - **Wachtwoord** : Als u een certificaatbestand met wachtwoordbeveiliging uploadt, geeft u het wachtwoord hier op. Als dat niet het geval is, laat u dit veld leeg. Zodra het certificaatbestand is geïmporteerd, wordt dat wachtwoord door sleutelkluis verwijderd.
4. Klik op **Create**.

![Certificaateigenschappen](../media/certificates/tutorial-import-cert/cert-import.png)

Door een certificaat toe te voegen met behulp van de methode **Importeren**, vult Azure Key Vault automatisch certificaatparameters in (dat wil zeggen de geldigheidsperiode, de naam van de verlener, de activeringsdatum, enzovoort).

Zodra u het bericht ontvangt dat het certificaat met succes is geïmporteerd, kunt u erop klikken in de lijst om de eigenschappen te bekijken. 

![Schermopname van de locatie waar u de certificaateigenschappen kunt bekijken.](../media/certificates/tutorial-import-cert/current-version-hidden.png)

## <a name="import-a-certificate-using-azure-cli"></a>Een certificaat importeren met behulp van Azure CLI

Een certificaat importeren naar een opgegeven sleutelkluis. Om in Azure Key Vault een bestaand geldig certificaat met een persoonlijke sleutel te importeren kan certificaat dat moet worden geïmporteerd de PFX- of PEM-indeling hebben. Als het certificaat zich de PEM-indeling bevat, moet het PEM-bestand de sleutel en de x509-certificaten bevatten. Voor deze bewerking is de machtiging certificaten/importeren vereist.

```azurecli
az keyvault certificate import --file
                               --name
                               --vault-name
                               [--disabled {false, true}]
                               [--only-show-errors]
                               [--password]
                               [--policy]
                               [--subscription]
                               [--tags]
```

Meer informatie over de [parameters](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-import).

Nadat u het certificaat hebt geïmporteerd, kunt u het certificaat weergeven met behulp van [Certificaat weergeven](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show)


```azurecli
az keyvault certificate show [--id]
                             [--name]
                             [--only-show-errors]
                             [--subscription]
                             [--vault-name]
                             [--version]
```

Nu hebt u een sleutelkluis gemaakt, een certificaat geïmporteerd en de eigenschappen van het certificaat weergegeven.

## <a name="import-a-certificate-using-azure-powershell"></a>Een certificaat importeren met behulp van Azure PowerShell

```
Import-AzureKeyVaultCertificate
      [-VaultName] <String>
      [-Name] <String>
      -FilePath <String>
      [-Password <SecureString>]
      [-Tag <Hashtable>]
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
```

Meer informatie over de [parameters](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?view=azurermps-6.13.0).


## <a name="clean-up-resources"></a>Resources opschonen

Andere Key Vault-snelstarts en zelfstudies bouwen voort op deze snelstart. Als u van plan bent om verder te gaan met volgende snelstarts en zelfstudies, kunt u deze resources intact laten.
Als u die niet meer nodig hebt, verwijdert u de resourcegroep. Hierdoor worden ook de sleutelkluis en de gerelateerde resources verwijderd. De resourcegroep verwijderen via de portal:

1. Voer de naam van uw resourcegroep in het vak Zoeken bovenaan de portal in. Wanneer u de in deze snelstart gebruikte resourcegroep in de zoekresultaten ziet, selecteert u deze.
2. Selecteer **Resourcegroep verwijderen**.
3. Typ in het vak **TYP DE NAAM VAN DE RESOURCEGROEP** de naam van de resourcegroep en selecteer **Verwijderen**.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een Key Vault gemaakt en daar een certificaat in geïmporteerd. Voor meer informatie over Key Vault en hoe u Key Vault integreert met uw toepassingen gaat u verder naar de artikelen hieronder.

- Lees meer over [Certificaten maken beheren in Azure Key Vault](./create-certificate-scenarios.md)
- Voorbeelden van [Het importeren van certificaten met behulp van REST-API's](/rest/api/keyvault/importcertificate/importcertificate)
- Bekijk het [Overzicht van de Key Vault-beveiliging](../general/security-overview.md)