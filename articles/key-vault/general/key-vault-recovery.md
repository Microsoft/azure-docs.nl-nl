---
title: Azure Key Vault overzicht van herstel | Microsoft Docs
description: Key Vault Recovery-functies zijn ontworpen om te voorkomen dat uw sleutelkluis en geheimen, sleutels en certificaten die zijn opgeslagen in de sleutelkluis onbedoeld of opzettelijk worden verwijderd.
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.author: mbaldwin
author: msmbaldwin
ms.date: 09/30/2020
ms.openlocfilehash: b9c249bedd0432458b3e6f5c010cdc5ff39dff44
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815662"
---
# <a name="azure-key-vault-recovery-management-with-soft-delete-and-purge-protection"></a>Azure Key Vault herstelbeheer met bescherming tegen zacht verwijderen en opsisten

In dit artikel worden twee herstelfuncties van Azure Key Vault beschreven, zoals de beveiliging tegen zacht verwijderen en opsisten. Dit document bevat een overzicht van deze functies en laat zien hoe u deze kunt beheren via de Azure Portal, Azure CLI en Azure PowerShell.

Zie voor meer informatie Key Vault
- [Key Vault overzicht](overview.md)
- [Azure Key Vault overzicht van sleutels, geheimen en certificaten](about-keys-secrets-certificates.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement - [Een gratis abonnement maken](https://azure.microsoft.com/free/dotnet)
* [PowerShell-module](/powershell/azure/install-az-ps).
* [Azure-CLI](/cli/azure/install-azure-cli)
* Een sleutelkluis: u kunt er een maken met behulp van [Azure Portal](../general/quick-create-portal.md), [Azure CLI](../general/quick-create-cli.md) of [Azure PowerShell](../general/quick-create-powershell.md)
* De gebruiker heeft de volgende machtigingen (op abonnementsniveau) nodig om bewerkingen uit te voeren op soft-leted kluizen:

  | Machtiging | Beschrijving |
  |---|---|
  |Microsoft.KeyVault/locations/deletedVaults/read|De eigenschappen van een zacht verwijderde sleutelkluis weergeven|
  |Microsoft.KeyVault/locations/deletedVaults/purge/action|Een zacht verwijderde sleutelkluis leeg maken|


## <a name="what-are-soft-delete-and-purge-protection"></a>Wat is de bescherming tegen zacht verwijderen en opseen?

[Beveiliging tegen zacht](soft-delete-overview.md) verwijderen en opsluizen zijn twee verschillende herstelfuncties voor sleutelkluizen.

> [!IMPORTANT]
> Het in-/uitschakelen van een zachte verwijdering is essentieel om ervoor te zorgen dat uw sleutelkluizen en referenties zijn beveiligd tegen onbedoeld verwijderen. Het in-/uitschakelen van een zachte delete wordt echter beschouwd als een wijziging die de wijziging doorbreekt, omdat u mogelijk uw toepassingslogica moet wijzigen of extra machtigingen moet verlenen aan uw service-principals. Zorg ervoor dat uw toepassing compatibel is met de wijziging met behulp van dit document, voordat u de soft delete int met behulp van de [ **onderstaande instructies.**](soft-delete-change.md)

**U kunt de** sleutelkluis en sleutels, geheimen en certificaten die in de sleutelkluis zijn opgeslagen, niet per ongeluk verwijderen. U kunt soft-delete zien als een prullenbak. Wanneer u een sleutelkluis of een sleutelkluisobject verwijdert, blijft deze herstelbaar voor een door de gebruiker configureerbare bewaarperiode of een standaardperiode van 90 dagen. Sleutelkluizen met de status 'soft deleted' kunnen ook worden **verwijderd,** wat betekent dat ze permanent worden verwijderd. Hiermee kunt u sleutelkluizen en sleutelkluisobjecten opnieuw maken met dezelfde naam. Voor het herstellen en verwijderen van sleutelkluizen en objecten zijn verhoogde toegangsbeleidmachtigingen vereist. **Zodra de functie voor het verwijderen van de functie voor het verwijderen van gegevens is ingeschakeld, kan deze niet meer worden uitgeschakeld.**

Het is belangrijk te weten dat sleutelkluisnamen wereldwijd uniek **zijn,** zodat u geen sleutelkluis met dezelfde naam als een sleutelkluis met de status Voorgehist kunt maken. Op dezelfde manier zijn de namen van sleutels, geheimen en certificaten uniek binnen een sleutelkluis. U kunt geen geheim, sleutel of certificaat maken met dezelfde naam als een ander certificaat met de status Verwijderd.

**Beveiliging tegen opsluizen** is ontworpen om te voorkomen dat uw sleutelkluis, sleutels, geheimen en certificaten worden verwijderd door een kwaadwillende insider. U kunt dit zien als een prullenbak met een vergrendeling op basis van tijd. U kunt items op elk moment herstellen tijdens de configureerbare bewaarperiode. **U kunt een sleutelkluis pas permanent verwijderen of opslappen als de retentieperiode is verstreken.** Zodra de bewaarperiode is verstreken, wordt de sleutelkluis of het sleutelkluisobject automatisch verwijderd.

> [!NOTE]
> Beveiliging tegen opseen is zo ontworpen dat geen enkele beheerdersrol of -machtiging beveiliging kan overschrijven, uitschakelen of omzeilen. **Zodra beveiliging tegen ops manier ops manier is ingeschakeld, kan deze niet worden uitgeschakeld of overschrijven door iedereen, inclusief Microsoft.** Dit betekent dat u een verwijderde sleutelkluis moet herstellen of moet wachten tot de bewaarperiode is verstreken voordat u de naam van de sleutelkluis opnieuw gebruikt.

Zie voor meer informatie over soft-delete [Azure Key Vault overzicht van soft-delete](soft-delete-overview.md)

# <a name="azure-portal"></a>[Azure-portal](#tab/azure-portal)

## <a name="verify-if-soft-delete-is-enabled-on-a-key-vault-and-enable-soft-delete"></a>Controleer of soft delete is ingeschakeld voor een sleutelkluis en schakel de functie voor soft delete in

1. Meld u aan bij Azure Portal.
1. Selecteer uw sleutelkluis.
1. Klik op de blade Eigenschappen.
1. Controleer of het radiorondje naast de functie Voor verwijderen is ingesteld op Herstel inschakelen.
1. Als de functie voor het verwijderen van de sleutelkluis niet is ingeschakeld, klikt u op het radiorondje om de functie Voor verwijderen in te stellen en klikt u op Opslaan.

:::image type="content" source="../media/key-vault-recovery-1.png" alt-text="Bij Eigenschappen is Soft-Delete gemarkeerd, evenals de waarde om deze in teschakelen.":::

## <a name="grant-access-to-a-service-principal-to-purge-and-recover-deleted-secrets"></a>Toegang verlenen tot een service-principal om verwijderde geheimen op te leeg te maken en te herstellen

1. Meld u aan bij Azure Portal.
1. Selecteer uw sleutelkluis.
1. Klik op de blade Toegangsbeleid.
1. Zoek in de tabel de rij van de beveiligingsprincipaal die u toegang wilt verlenen (of voeg een nieuwe beveiligingsprincipaal toe).
1. Klik op de vervolgkeuzepagina voor sleutels, certificaten en geheimen.
1. Schuif naar de onderkant van de vervolgkeuzepagina en klik op 'Herstellen' en 'Opsnuit'
1. Beveiligings-principals hebben ook get- en list-functionaliteit nodig om de meeste bewerkingen uit te voeren.

:::image type="content" source="../media/key-vault-recovery-2.png" alt-text="In het linkernavigatiedeelvenster is Toegangsbeleid gemarkeerd. Bij Toegangsbeleid wordt de vervolgkeuzelijst Geheime posities weergegeven en zijn vier items geselecteerd: Get, List, Recover en Purge.":::

## <a name="list-recover-or-purge-a-soft-deleted-key-vault"></a>Een soft-leted sleutelkluis opsluizen, herstellen of leeg maken

1. Meld u aan bij Azure Portal.
1. Klik op de zoekbalk boven aan de pagina.
1. Klik onder Recente services op Key Vault. Klik niet op een afzonderlijke sleutelkluis.
1. Klik bovenaan het scherm op de optie Verwijderde kluizen beheren
1. Aan de rechterkant van het scherm wordt een contextvenster geopend.
1. Selecteer uw abonnement.
1. Als uw sleutelkluis is verwijderd, wordt deze weergegeven in het contextvenster aan de rechterkant.
1. Als er te veel kluizen zijn, kunt u onder aan het contextvenster op Meer laden klikken of CLI of PowerShell gebruiken om de resultaten te krijgen.
1. Zodra u de kluis hebt gevonden die u wilt herstellen of opsluizen, selecteert u het selectievakje er naast.
1. Selecteer de optie Herstellen onderaan het contextvenster als u de sleutelkluis wilt herstellen.
1. Selecteer de optie opsluizen als u de sleutelkluis permanent wilt verwijderen.

:::image type="content" source="../media/key-vault-recovery-3.png" alt-text="In Sleutelkluizen is de optie Verwijderde kluizen beheren gemarkeerd.":::

:::image type="content" source="../media/key-vault-recovery-4.png" alt-text="In Verwijderde sleutelkluizen beheren is de enige vermelde sleutelkluis gemarkeerd en geselecteerd en is de knop Herstellen gemarkeerd.":::

## <a name="list-recover-or-purge-soft-deleted-secrets-keys-and-certificates"></a>Lijst met, herstellen of ops leeg maken van de geheimen, sleutels en certificaten die u hebt verwijderd

1. Meld u aan bij Azure Portal.
1. Selecteer uw sleutelkluis.
1. Selecteer de blade die overeenkomt met het geheimtype dat u wilt beheren (sleutels, geheimen of certificaten).
1. Klik bovenaan het scherm op Verwijderde items beheren (sleutels, geheimen of certificaten)
1. Aan de rechterkant van het scherm wordt een contextvenster weergegeven.
1. Als uw geheim, sleutel of certificaat niet in de lijst wordt weergegeven, heeft het niet de status Soft-Deleted.
1. Selecteer het geheim, de sleutel of het certificaat dat u wilt beheren.
1. Selecteer de optie om te herstellen of op te halen onderaan het contextvenster.

:::image type="content" source="../media/key-vault-recovery-5.png" alt-text="In Sleutels is de optie Verwijderde sleutels beheren gemarkeerd.":::

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

## <a name="key-vault-cli"></a>Key Vault (CLI)

* Controleren of voor een sleutelkluis soft-delete is ingeschakeld

    ```azurecli
    az keyvault show --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* Soft-delete inschakelen voor sleutelkluis

    Voor alle nieuwe sleutelkluizen is standaard een functie voor het verwijderen van gegevens ingeschakeld. Als u momenteel een sleutelkluis hebt waar voorlopig verwijderen niet is ingeschakeld, gebruikt u de volgende opdracht om voorlopig verwijderen in teschakelen.

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-soft-delete true
    ```

* Sleutelkluis verwijderen (herstelbaar als de functie voor soft delete is ingeschakeld)

    ```azurecli
    az keyvault delete --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* Een lijst maken van alle sleutelkluizen die zijn verwijderd

    ```azurecli
    az keyvault list-deleted --subscription {SUBSCRIPTION ID} --resource-type vault
    ```

* Snel verwijderde sleutelkluis herstellen

    ```azurecli
    az keyvault recover --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* De sleutelkluis die u hebt verwijderd, leeg **maken (WAARSCHUWING! MET DEZE BEWERKING WORDT UW SLEUTELKLUIS PERMANENT VERWIJDERD)**

    ```azurecli
    az keyvault purge --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* Beveiliging tegen opsluizen in de sleutelkluis inschakelen

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-purge-protection true
    ```

## <a name="certificates-cli"></a>Certificaten (CLI)

* Toegang verlenen om certificaten op te halen en te herstellen

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --certificate-permissions recover purge
    ```

* Certificaat verwijderen

    ```azurecli
    az keyvault certificate delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* Verwijderde certificaten op een lijst zetten

    ```azurecli
    az keyvault certificate list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Verwijderd certificaat herstellen

    ```azurecli
    az keyvault certificate recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* Zacht verwijderd certificaat ops leeg **maken (WAARSCHUWING! MET DEZE BEWERKING WORDT UW CERTIFICAAT PERMANENT VERWIJDERD)**

    ```azurecli
    az keyvault certificate purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

## <a name="keys-cli"></a>Sleutels (CLI)

* Toegang verlenen om sleutels op te halen en te herstellen

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --key-permissions recover purge
    ```

* Sleutel verwijderen

    ```azurecli
    az keyvault key delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* Verwijderde sleutels vermelden

    ```azurecli
    az keyvault key list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Verwijderde sleutel herstellen

    ```azurecli
    az keyvault key recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* Zacht verwijderde sleutel leeg maken **(WAARSCHUWING! MET DEZE BEWERKING WORDT UW SLEUTEL PERMANENT VERWIJDERD)**

    ```azurecli
    az keyvault key purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

## <a name="secrets-cli"></a>Geheimen (CLI)

* Toegang verlenen om geheimen op te halen en te herstellen

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --secret-permissions recover purge
    ```

* Geheim verwijderen

    ```azurecli
    az keyvault secret delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* Verwijderde geheimen op een lijst zetten

    ```azurecli
    az keyvault secret list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Verwijderd geheim herstellen

    ```azurecli
    az keyvault secret recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* Een zacht verwijderd geheim leeg maken **(WAARSCHUWING! MET DEZE BEWERKING WORDT UW GEHEIM PERMANENT VERWIJDERD)**

    ```azurecli
    az keyvault secret purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

## <a name="key-vault-powershell"></a>Key Vault (PowerShell)

* Controleren of voor een sleutelkluis soft-delete is ingeschakeld

    ```powershell
    Get-AzKeyVault -VaultName "ContosoVault"
    ```

* Sleutelkluis verwijderen

    ```powershell
    Remove-AzKeyVault -VaultName 'ContosoVault'
    ```

* Een lijst maken met alle sleutelkluizen die zijn verwijderd

    ```powershell
    Get-AzKeyVault -InRemovedState
    ```

* Zacht verwijderde sleutelkluis herstellen

    ```powershell
    Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
    ```

* De sleutelkluis die u hebt verwijderd, opsluizen **(WAARSCHUWING! MET DEZE BEWERKING WORDT UW SLEUTELKLUIS PERMANENT VERWIJDERD)**

    ```powershell
    Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
    ```

* Beveiliging tegen opsluizen in de sleutelkluis inschakelen

    ```powershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"

    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

## <a name="certificates-powershell"></a>Certificaten (PowerShell)

* Machtigingen verlenen om certificaten te herstellen en op te halen

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToCertificates recover,purge
    ```

* Een certificaat verwijderen

  ```powershell
  Remove-AzKeyVaultCertificate -VaultName ContosoVault -Name 'MyCert'
  ```

* Een lijst met alle verwijderde certificaten in een sleutelkluis maken

  ```powershell
  Get-AzKeyVaultCertificate -VaultName ContosoVault -InRemovedState
  ```

* Een certificaat met de status verwijderd herstellen

  ```powershell
  Undo-AzKeyVaultCertificateRemoval -VaultName ContosoVault -Name 'MyCert'
  ```

* Een licht verwijderd certificaat leeg maken **(WAARSCHUWING! MET DEZE BEWERKING WORDT UW CERTIFICAAT PERMANENT VERWIJDERD)**

  ```powershell
  Remove-AzKeyVaultcertificate -VaultName ContosoVault -Name 'MyCert' -InRemovedState
  ```

## <a name="keys-powershell"></a>Sleutels (PowerShell)

* Machtigingen verlenen om sleutels te herstellen en op te halen

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys recover,purge
    ```

* Een sleutel verwijderen

  ```powershell
  Remove-AzKeyVaultKey -VaultName ContosoVault -Name 'MyKey'
  ```

* Alle verwijderde certificaten in een sleutelkluis opsluizen

  ```powershell
  Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
  ```

* Een zacht verwijderde sleutel herstellen

    ```powershell
    Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
    ```

* Een zacht verwijderde sleutel leeg maken **(WAARSCHUWING! MET DEZE BEWERKING WORDT UW SLEUTEL PERMANENT VERWIJDERD)**

    ```powershell
    Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
    ```

## <a name="secrets-powershell"></a>Geheimen (PowerShell)

* Machtigingen verlenen om geheimen te herstellen en op te halen

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToSecrets recover,purge
    ```

* Een geheim met de naam SQLPassword verwijderen

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
  ```

* Een lijst met alle verwijderde geheimen in een sleutelkluis maken

  ```powershell
  Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
  ```

* Een geheim met de status Verwijderd herstellen

  ```powershell
  Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
  ```

* Een geheim met de status Verwijderd leeg maken **(WAARSCHUWING! MET DEZE BEWERKING WORDT UW SLEUTEL PERMANENT VERWIJDERD)**

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```
---

## <a name="next-steps"></a>Volgende stappen

- [Azure Key Vault PowerShell-cmdlets](/powershell/module/az.keyvault)
- [Key Vault Azure CLI-opdrachten](/cli/azure/keyvault)
- [Back-up voor Azure Key Vault](backup.md)
- [Key Vault-logboekregistratie inschakelen](howto-logging.md)
- [Azure Key Vault beveiligingsfuncties](security-features.md)
- [Gids voor Azure Key Vault-ontwikkelaars](developers-guide.md)