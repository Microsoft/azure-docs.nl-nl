---
title: Back-up maken van geheim, sleutel of certificaat opgeslagen in Azure Key Vault | Microsoft Docs
description: Gebruik dit document om een back-up te maken van een geheim, sleutel of certificaat dat is opgeslagen in Azure Key Vault.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 3/18/2021
ms.author: sudbalas
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 399f55d379f99a784fed97d7e9f72c456eb1f914
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484809"
---
# <a name="azure-key-vault-backup"></a>Back-up voor Azure Key Vault

In dit document leest u hoe u een back-up maakt van geheimen, sleutels en certificaten die zijn opgeslagen in een sleutelkluis. Een back-up is bedoeld om u een offline kopie te bieden van al uw geheimen in het onwaarschijnlijke geval dat u geen toegang meer hebt tot uw sleutelkluis.

## <a name="overview"></a>Overzicht

Azure Key Vault biedt automatisch functies om de beschikbaarheid van gegevens te garanderen en gegevensverlies te voorkomen. Maak alleen back-ups van geheimen als dit vanuit bedrijfsoogpunt noodzakelijk is. Het maken van een back-up van geheimen in uw sleutelkluis kan operationele uitdagingen met zich meebrengen, zoals het onderhouden van meerdere logboeken, machtigingen en back-ups wanneer geheimen verlopen of roteren.

Key Vault onderhoudt de beschikbaarheid in noodscenario's en voert automatisch failoververzoeken uit naar een gekoppelde regio zonder dat tussenkomst van een gebruiker nodig is. Zie [Beschikbaarheid en redundantie in Azure Key Vault](./disaster-recovery-guidance.md) voor meer informatie.

Als u uw geheimen wilt beveiligen tegen onbedoelde of opzettelijke verwijdering, moet u de beveiligingsfuncties voor voorlopig verwijderen en opschonen voor uw sleutelkluis configureren. Zie [Azure Key Vault: overzicht van voorlopig verwijderen](./soft-delete-overview.md) voor meer informatie.

## <a name="limitations"></a>Beperkingen

> [!IMPORTANT]
> Key Vault ondersteunt de mogelijkheid niet om meer een back-up te maken van meer dan 500 eerdere versies van een sleutel, geheim of certificaatobject. Als u probeert een back-up te maken van een sleutel, geheim of certificaatobject, leidt dat mogelijk tot een fout. Het is niet mogelijk om eerdere versies van een sleutel, geheim of certificaat te verwijderen.

Key Vault biedt momenteel geen manier om in één bewerking een back-up te maken van een volledige sleutelkluis. Pogingen om de opdrachten in dit document te gebruiken om een automatische back-up van een sleutelkluis te maken, kunnen fouten veroorzaken en worden niet ondersteund door Microsoft of het Azure Key Vault-team. 

Houd ook rekening met het volgende:

* Het maken van een back-up van geheimen met meerdere versies kan leiden tot time-outfouten.
* Met een back-up wordt een momentopname gemaakt van een bepaald tijdstip. Geheimen kunnen tijdens een back-up worden vernieuwd, waardoor het kan gebeuren dat versleutelingssleutels niet overeenkomen.
* Als u de limieten voor de sleutelkluisservice voor aanvragen per seconde overschrijdt, wordt uw sleutelkluis beperkt en zal de back-up mislukken.

## <a name="design-considerations"></a>Overwegingen bij het ontwerpen

Wanneer u een back-up maakt van een object dat is opgeslagen in een sleutelkluis (zoals een geheim, sleutel of certificaat), wordt het object door de back-upbewerking gedownload als een versleutelde blob. Deze blob kan niet buiten Azure worden ontsleuteld. Als u bruikbare gegevens uit deze blob wilt ophalen, moet u de blob herstellen in een sleutelkluis binnen hetzelfde Azure-abonnement en dezelfde [Azure-geografie](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="prerequisites"></a>Vereisten

Als u een back-up van een sleutelkluisobject wilt maken, hebt u het volgende nodig: 

* Machtigingen op inzenderniveau of hoger voor een Azure-abonnement.
* Een primaire sleutelkluis met de geheimen waarvan u een back-up wilt maken.
* Een secundaire sleutelkluis waarnaar geheimen worden teruggezet.

## <a name="back-up-and-restore-from-the-azure-portal"></a>Back-ups maken en herstellen met de Azure-portal

Volg de stappen in deze sectie voor het maken en herstellen van back-ups van objecten met behulp van de Azure-portal.

### <a name="back-up"></a>Een back-up maken

1. Ga naar Azure Portal.
2. Selecteer uw sleutelkluis.
3. Ga naar het object (geheim, sleutel of certificaat) waarvan u een back-up wilt maken.

    ![Schermopname waarin wordt aangegeven waar de instellingen voor sleutels en een object in een sleutelkluis moeten worden geselecteerd.](../media/backup-1.png)

4. Selecteer het object.
5. Selecteer **Back-up downloaden**.

    ![Schermopname waarin wordt aangegeven waar u de knop Back-up downloaden selecteert in een sleutelkluis.](../media/backup-2.png)
    
6. Selecteer **Download**.

    ![Schermopname waarin wordt aangegeven waar u de knop Downloaden selecteert in een sleutelkluis.](../media/backup-3.png)
    
7. Sla de versleutelde blob op een veilige locatie op.

### <a name="restore"></a>Herstellen

1. Ga naar Azure Portal.
2. Selecteer uw sleutelkluis.
3. Ga naar het type object (geheim, sleutel of certificaat) dat u wilt herstellen.
4. Selecteer **Back-up terugzetten**.

    ![Schermopname waarin wordt aangegeven waar u de knop Back-up terugzetten selecteert in een sleutelkluis.](../media/backup-4.png)
    
5. Ga naar de locatie waar u de versleutelde blob hebt opgeslagen.
6. Selecteer **OK**.

## <a name="back-up-and-restore-from-the-azure-cli-or-azure-powershell"></a>Back-up en herstel vanuit de Azure CLI of Azure PowerShell

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
```azurecli
## Log in to Azure
az login

## Set your subscription
az account set --subscription {AZURE SUBSCRIPTION ID}

## Register Key Vault as a provider
az provider register -n Microsoft.KeyVault

## Back up a certificate in Key Vault
az keyvault certificate backup --file {File Path} --name {Certificate Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Back up a key in Key Vault
az keyvault key backup --file {File Path} --name {Key Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Back up a secret in Key Vault
az keyvault secret backup --file {File Path} --name {Secret Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a certificate in Key Vault
az keyvault certificate restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a key in Key Vault
az keyvault key restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a secret in Key Vault
az keyvault secret restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

```azurepowershell
## Log in to Azure
Connect-AzAccount

## Set your subscription
Set-AzContext -Subscription '{AZURE SUBSCRIPTION ID}'

## Back up a certificate in Key Vault
Backup-AzKeyVaultCertificate -VaultName '{Certificate Name}' -Name '{Key Vault Name}'

## Back up a key in Key Vault
Backup-AzKeyVaultKey -VaultName '{Key Name}' -Name '{Key Vault Name}'

## Back up a secret in Key Vault
Backup-AzKeyVaultSecret -VaultName '{Key Vault Name}' -Name '{Secret Name}'

## Restore a certificate in Key Vault
Restore-AzKeyVaultCertificate -VaultName '{Key Vault Name}' -InputFile '{File Path}'

## Restore a key in Key Vault
Restore-AzKeyVaultKey -VaultName '{Key Vault Name}' -InputFile '{File Path}'

## Restore a secret in Key Vault
Restore-AzKeyVaultSecret -VaultName '{Key Vault Name}' -InputFile '{File Path}'
```
---

## <a name="next-steps"></a>Volgende stappen

Schakel [logboekregistratie en -controle](./logging.md) in voor Key Vault.
