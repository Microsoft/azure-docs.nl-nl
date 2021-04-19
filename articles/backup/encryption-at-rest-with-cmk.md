---
title: Versleuteling van back-upgegevens met door de klant beheerde sleutels
description: Meer informatie over Azure Backup u uw back-upgegevens kunt versleutelen met behulp van door de klant beheerde sleutels (CMK).
ms.topic: conceptual
ms.date: 04/19/2021
ms.openlocfilehash: bd51be06e707674f3e35b3478d7f99d096be912a
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718769"
---
# <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Versleuteling van back-upgegevens met door de klant beheerde sleutels

Azure Backup kunt u uw back-upgegevens versleutelen met behulp van door de klant beheerde sleutels (CMK) in plaats van door het platform beheerde sleutels te gebruiken, wat standaard is ingeschakeld. Uw sleutels die worden gebruikt voor het versleutelen van de back-upgegevens moeten worden opgeslagen in [Azure Key Vault](../key-vault/index.yml).

De versleutelingssleutel die wordt gebruikt voor het versleutelen van back-ups, kan verschillen van de versleutelingssleutel die wordt gebruikt voor de bron. De gegevens worden beveiligd met een op AES 256 gebaseerde gegevensversleutelingssleutel (DEK), die op zijn beurt wordt beveiligd met uw sleutels (KEK). Dit geeft u volledige controle over de gegevens en de sleutels. Als u versleuteling wilt toestaan, moet aan de Recovery Services-kluis toegang worden verleend tot de versleutelingssleutel in de Azure Key Vault. U kunt de sleutel wijzigen zoals en wanneer dat nodig is.

In dit artikel wordt het volgende besproken:

- Een Recovery Services-kluis maken
- Uw Recovery Services-kluis configureren voor het versleutelen van back-upgegevens met door de klant beheerde sleutels
- Back-up uitvoeren naar kluizen die zijn versleuteld met door de klant beheerde sleutels
- Gegevens herstellen vanuit back-ups

## <a name="before-you-start"></a>Voordat u begint

- Met deze functie kunt u alleen **nieuwe Recovery Services-kluizen versleutelen.** Alle kluizen met bestaande items die zijn geregistreerd of geprobeerd te worden geregistreerd, worden niet ondersteund.

- Zodra deze is ingeschakeld voor een Recovery Services-kluis, kan versleuteling met behulp van door de klant beheerde sleutels niet meer worden teruggedraaid naar door het platform beheerde sleutels (standaard). U kunt de versleutelingssleutels wijzigen op basis van uw vereisten.

- Deze functie biedt **momenteel geen ondersteuning voor** back-ups met marsagent en mogelijk kunt u hiervoor geen met CMK versleutelde kluis gebruiken. De MARS-agent gebruikt een versleuteling op basis van een wachtwoordzin. Deze functie biedt ook geen ondersteuning voor back-ups van klassieke VM's.

- Deze functie is niet gerelateerd aan [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md), dat gebruikmaakt van gastversleuteling van de schijven van een VM met behulp van BitLocker (voor Windows) en DM-Crypt (voor Linux)

- De Recovery Services-kluis kan alleen worden versleuteld met sleutels die zijn opgeslagen in een Azure Key Vault, die zich in **dezelfde regio bevindt.** Sleutels mogen ook alleen **RSA-sleutels** zijn en moeten de **status Ingeschakeld** hebben.

- Het verplaatsen van een met CMK versleutelde Recovery Services-kluis tussen resourcegroepen en abonnementen wordt momenteel niet ondersteund.
- Wanneer u een Recovery Services-kluis verplaatst die al is versleuteld met door de klant beheerde sleutels naar een nieuwe tenant, moet u de Recovery Services-kluis bijwerken om de beheerde identiteit en CMK van de kluis opnieuw te maken en opnieuw te configureren (die in de nieuwe tenant moeten zijn). Als dit niet is gebeurd, mislukken de back-up- en herstelbewerkingen. Ook moeten alle RBAC-machtigingen (op rollen gebaseerd toegangsbeheer) die in het abonnement zijn ingesteld, opnieuw worden geconfigureerd.

- Deze functie kan worden geconfigureerd via de Azure Portal en PowerShell.

    >[!NOTE]
    >Gebruik Az-module 5.3.0 of hoger om door de klant beheerde sleutels te gebruiken voor back-ups in de Recovery Services-kluis.
    
    >[!Warning]
    >Als u PowerShell gebruikt voor het beheren van versleutelingssleutels voor Back-up, raden we u aan de sleutels niet bij te werken vanuit de portal.<br>Als u de sleutel bij werkt vanuit de portal, kunt u PowerShell niet gebruiken om de versleutelingssleutel verder bij te werken, totdat er een PowerShell-update beschikbaar is ter ondersteuning van het nieuwe model. U kunt echter doorgaan met het bijwerken van de sleutel vanuit Azure Portal.

Als u uw Recovery Services-kluis nog niet hebt gemaakt en geconfigureerd, kunt u hier lezen hoe [u dit doet.](backup-create-rs-vault.md)

## <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>Een kluis configureren voor versleuteling met behulp van door de klant beheerde sleutels

Deze sectie omvat de volgende stappen:

1. Beheerde identiteit inschakelen voor uw Recovery Services-kluis

1. Machtigingen toewijzen aan de kluis voor toegang tot de versleutelingssleutel in de Azure Key Vault

1. Schakel de beveiliging voor het verwijderen en opseen van de Azure Key Vault

1. De versleutelingssleutel toewijzen aan de Recovery Services-kluis

Het is noodzakelijk dat al deze stappen worden gevolgd in de hierboven genoemde volgorde om de beoogde resultaten te bereiken. Elke stap wordt hieronder in detail besproken.

## <a name="enable-managed-identity-for-your-recovery-services-vault"></a>Beheerde identiteit inschakelen voor uw Recovery Services-kluis

Azure Backup gebruikt door het systeem toegewezen beheerde identiteiten en door de gebruiker toegewezen beheerde identiteiten om de Recovery Services-kluis te verifiëren voor toegang tot versleutelingssleutels die zijn opgeslagen in de Azure Key Vault. Als u een beheerde identiteit wilt inschakelen voor uw Recovery Services-kluis, volgt u de onderstaande stappen.

>[!NOTE]
>Zodra deze is ingeschakeld, mag de beheerde identiteit **niet** (zelfs tijdelijk) worden uitgeschakeld. Het uitschakelen van de beheerde identiteit kan leiden tot inconsistent gedrag.

### <a name="enable-system-assigned-managed-identity-for-the-vault"></a>Door het systeem toegewezen beheerde identiteit inschakelen voor de kluis

**In de portal:**

1. Ga naar uw Recovery Services-kluis -> **identity**

    ![Identiteitsinstellingen](media/encryption-at-rest-with-cmk/enable-system-assigned-managed-identity-for-vault.png)

1. Navigeer naar **het tabblad Systeem** toegewezen.

1. Wijzig de **Status** in **Aan.**

1. Klik **op Opslaan** om de identiteit voor de kluis in te stellen.

Er wordt een object-id gegenereerd. Dit is de door het systeem toegewezen beheerde identiteit van de kluis.

>[!NOTE]
>Zodra deze is ingeschakeld, mag de beheerde identiteit niet (zelfs tijdelijk) worden uitgeschakeld. Het uitschakelen van de beheerde identiteit kan leiden tot inconsistent gedrag.


**Met PowerShell:**

Gebruik de [opdracht Update-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/update-azrecoveryservicesvault) om een door het systeem toegewezen beheerde identiteit in te stellen voor de Recovery Services-kluis.

Voorbeeld:

```AzurePowerShell
$vault=Get-AzRecoveryServicesVault -ResourceGroupName "testrg" -Name "testvault"

Update-AzRecoveryServicesVault -IdentityType SystemAssigned -VaultId $vault.ID

$vault.Identity | fl
```

Uitvoer:

```output
PrincipalId : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
TenantId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Type        : SystemAssigned
```

### <a name="assign-user-assigned-managed-identity-to-the-vault"></a>Door de gebruiker toegewezen beheerde identiteit toewijzen aan de kluis

Voer de volgende stappen uit om de door de gebruiker toegewezen beheerde identiteit toe te wijzen voor uw Recovery Services-kluis:

1.  Ga naar uw Recovery Services-kluis -> **Identity**

    ![Door de gebruiker toegewezen beheerde identiteit toewijzen aan de kluis](media/encryption-at-rest-with-cmk/assign-user-assigned-managed-identity-to-vault.png)

1.  Navigeer **naar het tabblad Door de gebruiker** toegewezen.

1.  Klik **op +Toevoegen** om een door de gebruiker toegewezen beheerde identiteit toe te voegen.

1.  Selecteer op de blade Door de gebruiker toegewezen **beheerde identiteit toevoegen** die wordt geopend het abonnement voor uw identiteit.

1.  Selecteer de identiteit in de lijst. U kunt ook filteren op de naam van de identiteit of de resourcegroep.

1.  Als u klaar bent, **klikt u op** Toevoegen om de toewijzing van de identiteit te voltooien.

## <a name="assign-permissions-to-the-recovery-services-vault-to-access-the-encryption-key-in-the-azure-key-vault"></a>Machtigingen toewijzen aan de Recovery Services-kluis voor toegang tot de versleutelingssleutel in de Azure Key Vault

>[!Note]
>Als u door de gebruiker toegewezen identiteiten gebruikt, moeten dezelfde machtigingen worden toegewezen aan de door de gebruiker toegewezen identiteit.

U moet nu de Recovery Services-kluis toegang geven tot de Azure Key Vault de versleutelingssleutel bevat. Dit wordt gedaan door de beheerde identiteit van de Recovery Services-kluis toegang te geven tot de Key Vault.

**In de portal**:

1. Ga naar uw Azure Key Vault -> **Access Policies**. Ga door naar **+Toegangsbeleid toevoegen.**

    ![Toegangsbeleid toevoegen](./media/encryption-at-rest-with-cmk/access-policies.png)

1. Selecteer **onder Sleutelmachtigingen** **De bewerkingen Get**, **List,** **Unwrap Key** en Wrap **Key.** Hiermee geeft u de acties op de sleutel op die zijn toegestaan.

    ![Sleutelmachtigingen toewijzen](./media/encryption-at-rest-with-cmk/key-permissions.png)

1. Ga naar **Principal selecteren** en zoek uw kluis in het zoekvak met de naam of beheerde identiteit. Zodra deze wordt weergegeven, selecteert u de kluis en kiest **u** Selecteren onderaan het deelvenster.

    ![Principal selecteren](./media/encryption-at-rest-with-cmk/select-principal.png)

1. Als u klaar bent, **selecteert u Toevoegen** om het nieuwe toegangsbeleid toe te voegen.

1. Selecteer **Opslaan om** wijzigingen op te slaan die zijn aangebracht in het toegangsbeleid van de Azure Key Vault.

## <a name="enable-soft-delete-and-purge-protection-on-the-azure-key-vault"></a>Schakel de beveiliging voor het verwijderen en ops manier van verwijderen op de Azure Key Vault

U moet de beveiliging voor zacht verwijderen en **ops manier inschakelen** op uw Azure Key Vault uw versleutelingssleutel op te slaat. U kunt dit doen vanuit de Azure Key Vault zoals hieronder wordt weergegeven. (U kunt deze eigenschappen ook instellen tijdens het maken van de Key Vault). Lees hier meer over Key Vault [eigenschappen.](../key-vault/general/soft-delete-overview.md)

![Voorlopig verwijderen en opschonen beschermen inschakelen](./media/encryption-at-rest-with-cmk/soft-delete-purge-protection.png)

U kunt ook de beveiliging tegen zacht verwijderen en ops manier inschakelen via PowerShell met behulp van de onderstaande stappen:

1. Meld u aan bij uw Azure-account.

    ```azurepowershell
    Login-AzAccount
    ```

1. Selecteer het abonnement dat uw kluis bevat.

    ```azurepowershell
    Set-AzContext -SubscriptionId SubscriptionId
    ```

1. Voorlopig verwijderen inschakelen

    ```azurepowershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "AzureKeyVaultName").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    ```

    ```azurepowershell
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

1. Beveiliging tegen ops manier inschakelen

    ```azurepowershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "AzureKeyVaultName").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"
    ```

    ```azurepowershell
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

## <a name="assign-encryption-key-to-the-rs-vault"></a>Versleutelingssleutel toewijzen aan de RS-kluis

>[!NOTE]
> Voordat u verdergaat, controleert u het volgende:
>
> - Alle bovenstaande stappen zijn voltooid:
>   - De beheerde identiteit van de Recovery Services-kluis is ingeschakeld en de vereiste machtigingen zijn toegewezen
>   - Op Azure Key Vault is soft-delete en opstingsbeveiliging ingeschakeld
> - In de Recovery Services-kluis waarvoor u  CMK-versleuteling wilt inschakelen, zijn geen items beveiligd of geregistreerd

Zodra het bovenstaande is gegarandeerd, gaat u verder met het selecteren van de versleutelingssleutel voor uw kluis.

### <a name="to-assign-the-key-in-the-portal"></a>De sleutel toewijzen in de portal

1. Ga naar uw Recovery Services-kluis -> **eigenschappen**

    ![Versleutelingsinstellingen](./media/encryption-at-rest-with-cmk/encryption-settings.png)

1. Selecteer **Bijwerken onder** **Versleutelingsinstellingen.**

1. Selecteer in het deelvenster Versleutelingsinstellingen de optie **Uw eigen** sleutel gebruiken en geef de sleutel op een van de volgende manieren op. **Zorg ervoor dat de sleutel die u wilt gebruiken een RSA 2048-sleutel is met de status Ingeschakeld.**

    1. Voer de **sleutel-URI** in waarmee u de gegevens in deze Recovery Services-kluis wilt versleutelen. U moet ook het abonnement opgeven waarin de Azure Key Vault (die deze sleutel bevat) aanwezig is. Deze sleutel-URI kan worden verkregen van de bijbehorende sleutel in uw Azure Key Vault. Zorg ervoor dat de sleutel-URI correct is gekopieerd. Het is raadzaam om de knop Kopiëren naar **klembord te** gebruiken die is meegeleverd met de sleutel-id.

        >[!NOTE]
        >Wanneer u de versleutelingssleutel opgeeft met behulp van de sleutel-URI, wordt de sleutel niet automatisch geroteerd. Belangrijke updates moeten dus handmatig worden uitgevoerd door de nieuwe sleutel op te geven wanneer dat nodig is.

        ![Sleutel-URI invoeren](./media/encryption-at-rest-with-cmk/key-uri.png)

    1. Blader en selecteer de sleutel in de Key Vault in het deelvenster Sleutel kiezen.

        >[!NOTE]
        >Wanneer u de versleutelingssleutel opgeeft met behulp van het deelvenster Sleutel kiezen, wordt de sleutel automatisch geroteerd wanneer een nieuwe versie voor de sleutel wordt ingeschakeld. [Meer informatie over het](#enabling-auto-rotation-of-encryption-keys) inschakelen van automatische rotatie van versleutelingssleutels.

        ![Sleutel selecteren in sleutelkluis](./media/encryption-at-rest-with-cmk/key-vault.png)

1. Selecteer **Opslaan**.

1. **Voortgang en status van update van** versleutelingssleutel bijhouden: u  kunt de voortgang en status van de toewijzing van de versleutelingssleutel bijhouden met behulp van de weergave Back-uptaken in de linkernavigatiebalk. De status wordt binnenkort gewijzigd in **Voltooid.** Uw kluis versleutelt nu alle gegevens met de opgegeven sleutel als KEK.

    ![Status voltooid](./media/encryption-at-rest-with-cmk/status-succeeded.png)

    De updates van de versleutelingssleutel worden ook vastgelegd in het activiteitenlogboek van de kluis.

    ![Activiteitenlogboek](./media/encryption-at-rest-with-cmk/activity-log.png)

### <a name="to-assign-the-key-with-powershell"></a>De sleutel toewijzen met PowerShell

Gebruik de [opdracht Set-AzRecoveryServicesVaultProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultproperty) om versleuteling in te stellen met behulp van door de klant beheerde sleutels en om de te gebruiken versleutelingssleutel toe te wijzen of bij te werken.

Voorbeeld:

```azurepowershell
$keyVault = Get-AzKeyVault -VaultName "testkeyvault" -ResourceGroupName "testrg" 
$key = Get-AzKeyVaultKey -VaultName $keyVault -Name "testkey" 
Set-AzRecoveryServicesVaultProperty -EncryptionKeyId $key.ID -KeyVaultSubscriptionId "xxxx-yyyy-zzzz"  -VaultId $vault.ID


$enc=Get-AzRecoveryServicesVaultProperty -VaultId $vault.ID
$enc.encryptionProperties | fl
```

Uitvoer:

```output
EncryptionAtRestType          : CustomerManaged
KeyUri                        : testkey
SubscriptionId                : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx 
LastUpdateStatus              : Succeeded
InfrastructureEncryptionState : Disabled
```

>[!NOTE]
> Dit proces blijft hetzelfde wanneer u de versleutelingssleutel wilt bijwerken of wijzigen. Als u een sleutel van een andere Key Vault wilt bijwerken en gebruiken (anders dan de sleutel die momenteel wordt gebruikt), moet u ervoor zorgen dat:
>
> - De sleutelkluis bevindt zich in dezelfde regio als de Recovery Services-kluis
>
> - Voor de sleutelkluis is de beveiliging tegen zacht verwijderen en opsluizen ingeschakeld
>
> - De Recovery Services-kluis beschikt over de vereiste machtigingen voor toegang tot de sleutelkluis.

## <a name="backing-up-to-a-vault-encrypted-with-customer-managed-keys"></a>Back-up maken naar een kluis die is versleuteld met door de klant beheerde sleutels

Voordat u doorgaat met het configureren van de beveiliging, raden we u ten zeerste aan de volgende controlelijst te volgen. Dit is belangrijk omdat versleuteling met behulp van door de klant beheerde sleutels niet kan worden ingeschakeld en platform-beheerde sleutels blijven gebruiken zodra een item is geconfigureerd om een back-up te maken van een niet-CMK-versleutelde kluis (of een poging om te worden geconfigureerd).

>[!IMPORTANT]
> Voordat u doorgaat met het configureren van de beveiliging, **moet** u de volgende stappen hebben voltooid:
>
>1. Uw Backup-kluis gemaakt
>1. De door het systeem toegewezen beheerde identiteit van de Recovery Services-kluis is ingeschakeld of er is een door de gebruiker toegewezen beheerde identiteit aan de kluis toegewezen
>1. Toegewezen machtigingen voor uw Backup Vault (of de door de gebruiker toegewezen beheerde identiteit) voor toegang tot versleutelingssleutels van uw Key Vault
>1. De beveiliging voor het verwijderen en opseen van gegevens voor uw Key Vault
>1. Er is een geldige versleutelingssleutel toegewezen voor uw Back-upkluis
>
>Als alle bovenstaande stappen zijn bevestigd, kunt u alleen doorgaan met het configureren van de back-up.

Het proces voor het configureren en uitvoeren van back-ups naar een Recovery Services-kluis die is versleuteld met door de klant beheerde sleutels is hetzelfde als voor een kluis die gebruikmaakt van door het platform beheerde sleutels, zonder wijzigingen in de **ervaring**. Dit geldt voor [back-ups van azure-VM's](./quick-backup-vm-portal.md) en voor het maken van back-ups van workloads die worden uitgevoerd binnen een VM (SAP HANA [,](./tutorial-backup-sap-hana-db.md) [SQL Server](./tutorial-sql-backup.md) databases).

## <a name="restoring-data-from-backup"></a>Gegevens herstellen vanuit een back-up

### <a name="vm-backup"></a>VM-back-up

Gegevens die zijn opgeslagen in de Recovery Services-kluis kunnen worden hersteld volgens de stappen die hier worden [beschreven.](./backup-azure-arm-restore-vms.md) Wanneer u herstelt vanuit een Recovery Services-kluis die is versleuteld met door de klant beheerde sleutels, kunt u ervoor kiezen om de herstelde gegevens te versleutelen met een DES (Disk Encryption Set).

#### <a name="restoring-vm--disk"></a>VM/schijf herstellen

1. Wanneer u de schijf/VM herstelt vanaf een momentopnameherstelpunt, worden de herstelde gegevens versleuteld met de DES die wordt gebruikt voor het versleutelen van de schijven van de bron-VM.

1. Wanneer u een schijf/VM herstelt vanaf een herstelpunt met hersteltype als 'Kluis', kunt u ervoor kiezen om de herstelde gegevens te versleutelen met behulp van een DES die is opgegeven op het moment van herstellen. U kunt er ook voor kiezen om door te gaan met het herstellen van de gegevens zonder een DES op te geven. In dat geval worden deze versleuteld met door Microsoft beheerde sleutels.

U kunt de herstelde schijf/VM versleutelen nadat het herstellen is voltooid, ongeacht de selectie die is gemaakt tijdens het initiëren van de herstelprocedure.

![Herstelpunten](./media/encryption-at-rest-with-cmk/restore-points.png)

#### <a name="select-a-disk-encryption-set-while-restoring-from-vault-recovery-point"></a>Een schijfversleutelingsset selecteren tijdens het herstellen van het kluisherstelpunt

**In de portal**:

De schijfversleutelingsset wordt opgegeven onder Versleutelingsinstellingen in het herstelvenster, zoals hieronder wordt weergegeven:

1. Selecteer Ja **bij Schijf(s) versleutelen met uw** **sleutel.**

1. Selecteer in de vervolgkeuzelijst de DES die u wilt gebruiken voor de herstelde schijven. **Zorg ervoor dat u toegang hebt tot de DES.**

>[!NOTE]
>De mogelijkheid om een DES te kiezen tijdens het herstellen is niet beschikbaar als u een VM herstelt die gebruikmaakt van Azure Disk Encryption.

![Schijf versleutelen met uw sleutel](./media/encryption-at-rest-with-cmk/encrypt-disk-using-your-key.png)

**Met PowerShell:**

Gebruik de [opdracht Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) met de parameter [ ] om op te geven welke DES moet worden gebruikt voor het versleutelen van `-DiskEncryptionSetId <string>` de herstelde schijf. [](/powershell/module/az.compute/get-azdiskencryptionset) Zie dit artikel voor meer informatie over het herstellen van schijven vanuit een [VM-back-up.](./backup-azure-vms-automation.md#restore-an-azure-vm)

Voorbeeld:

```azurepowershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $vault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $vault.ID
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $vault.ID
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -DiskEncryptionSetId “testdes1” -VaultId $vault.ID
```

#### <a name="restoring-files"></a>Bestanden herstellen

Bij het herstellen van een bestand worden de herstelde gegevens versleuteld met de sleutel die wordt gebruikt voor het versleutelen van de doellocatie.

### <a name="restoring-sap-hanasql-databases-in-azure-vms"></a>Herstellen SAP HANA/SQL-databases in Azure-VM's

Bij het herstellen vanaf een back-up van een SAP HANA/SQL database die wordt uitgevoerd op een Azure-VM, worden de herstelde gegevens versleuteld met behulp van de versleutelingssleutel die wordt gebruikt op de doelopslaglocatie. Het kan een door de klant beheerde sleutel zijn of een door het platform beheerde sleutel die wordt gebruikt voor het versleutelen van de schijven van de virtueleM.

## <a name="additional-topics"></a>Extra onderwerpen

### <a name="enable-encryption-using-customer-managed-keys-at-vault-creation-in-preview"></a>Versleuteling inschakelen met behulp van door de klant beheerde sleutels bij het maken van de kluis (in preview)

>[!NOTE]
>Het inschakelen van versleuteling tijdens het maken van de kluis met behulp van door de klant beheerde sleutels is een beperkte openbare preview-versie en vereist een lijst met toegestane abonnementen. Als u zich wilt registreren voor de preview, vult u het [formulier in](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURDNTVVhGOUxXSVBZMEwxUU5FNDkyQkU4Ny4u) en schrijft u naar ons op [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

Wanneer uw abonnement is toegestaan, wordt het tabblad **Back-upversleuteling** weergegeven. Hiermee kunt u versleuteling voor de back-up inschakelen met behulp van door de klant beheerde sleutels tijdens het maken van een nieuwe Recovery Services-kluis. Voer de volgende stappen uit om de versleuteling in teschakelen:

1. Geef naast het **tabblad Basisinformatie** op **het** tabblad Back-upversleuteling de versleutelingssleutel en de identiteit op die moet worden gebruikt voor versleuteling.

   ![Versleuteling op kluisniveau inschakelen](media/encryption-at-rest-with-cmk/enable-encryption-using-cmk-at-vault.png)


   >[!NOTE]
   >De instellingen zijn alleen van toepassing op Back-up en zijn optioneel.

1. Selecteer **Door de klant beheerde sleutel gebruiken** als versleutelingstype.

1. Als u de sleutel wilt opgeven die moet worden gebruikt voor versleuteling, selecteert u de juiste optie.

   U kunt de URI voor de versleutelingssleutel verstrekken of bladeren en de sleutel selecteren. Wanneer u de sleutel opgeeft met behulp van de optie **Key Vault** selecteren, wordt automatische rotatie van de versleutelingssleutel automatisch ingeschakeld. [Meer informatie over automatische rotatie.](#enabling-auto-rotation-of-encryption-keys) 

1. Geef de door de gebruiker toegewezen beheerde identiteit op voor het beheren van versleuteling met door de klant beheerde sleutels. Klik **op Selecteren** om te bladeren en selecteer de vereiste identiteit.

1. Als u klaar bent, voegt u Tags toe (optioneel) en gaat u verder met het maken van de kluis.

### <a name="enabling-auto-rotation-of-encryption-keys"></a>Automatische rotatie van versleutelingssleutels inschakelen

Wanneer u de door de klant beheerde sleutel opgeeft die moet worden gebruikt om back-ups te versleutelen, gebruikt u de volgende methoden om deze op te geven:

- Voer de sleutel-URI in
- Selecteer uit Key Vault

Met behulp **van de optie Key Vault** kunt u automatische rotatie inschakelen voor de geselecteerde sleutel. Hierdoor hoeft u niet meer handmatig bij te werken naar de volgende versie. Gebruik echter deze optie:
- Het kan een uur duren voordat de sleutelversie is bijgewerkt.
- Wanneer een nieuwe versie van de sleutel van kracht wordt, moet de oude versie ook beschikbaar zijn (met ingeschakelde status) voor ten minste één volgende back-up job nadat de sleutelupdate van kracht is geworden.

### <a name="using-azure-policies-for-auditing-and-enforcing-encryption-utilizing-customer-managed-keys-in-preview"></a>Azure-beleid gebruiken voor het controleren en afdwingen van versleuteling met behulp van door de klant beheerde sleutels (in preview)

Azure Backup kunt u Azure-beleid gebruiken om versleuteling van gegevens in de Recovery Services-kluis te controleren en af te dwingen met behulp van door de klant beheerde sleutels. Het Azure-beleid gebruiken:

- Het controlebeleid kan worden gebruikt voor het controleren van kluizen met versleuteling met behulp van door de klant beheerde sleutels die na 01-04-2021 zijn ingeschakeld. Voor kluizen waarop de CMK-versleuteling vóór deze datum is ingeschakeld, kan het beleid mogelijk niet worden toegepast  of kunnen er fout-negatieve resultaten worden weer geven (dat wil zeggen dat deze kluizen kunnen worden gerapporteerd als niet-compatibel, ondanks dat CMK-versleuteling is ingeschakeld).
- Als u het controlebeleid wilt  gebruiken voor het controleren van kluizen met CMK-versleuteling ingeschakeld vóór 01-04-2021, gebruikt u de Azure Portal om een versleutelingssleutel bij te werken. Dit helpt bij het upgraden naar het nieuwe model. Als u de versleutelingssleutel niet wilt wijzigen, geeft u dezelfde sleutel opnieuw op via de sleutel-URI of de sleutelselectieoptie. 

   >[!Warning]
    >Als u PowerShell gebruikt voor het beheren van versleutelingssleutels voor Back-up, raden we u aan de sleutels niet bij te werken vanuit de portal.<br>Als u de sleutel bij werkt vanuit de portal, kunt u PowerShell niet gebruiken om de versleutelingssleutel verder bij te werken, totdat er een PowerShell-update beschikbaar is ter ondersteuning van het nieuwe model. U kunt echter doorgaan met het bijwerken van de sleutel vanuit Azure Portal.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="can-i-encrypt-an-existing-backup-vault-with-customer-managed-keys"></a>Kan ik een bestaande Backup-kluis versleutelen met door de klant beheerde sleutels?

Nee, CMK-versleuteling kan alleen worden ingeschakeld voor nieuwe kluizen. De kluis mag dus nooit items hebben beveiligd. Er moeten in feite geen pogingen worden gedaan om items naar de kluis te beveiligen voordat u versleuteling inschakelen met behulp van door de klant beheerde sleutels.

### <a name="i-tried-to-protect-an-item-to-my-vault-but-it-failed-and-the-vault-still-doesnt-contain-any-items-protected-to-it-can-i-enable-cmk-encryption-for-this-vault"></a>Ik heb geprobeerd een item in mijn kluis te beveiligen, maar dit is mislukt en de kluis bevat nog steeds geen items die beveiligen. Kan ik CMK-versleuteling inschakelen voor deze kluis?

Nee, de kluis mag in het verleden geen pogingen hebben gedaan om er items voor te beveiligen.

### <a name="i-have-a-vault-thats-using-cmk-encryption-can-i-later-revert-to-encryption-using-platform-managed-keys-even-if-i-have-backup-items-protected-to-the-vault"></a>Ik heb een kluis die CMK-versleuteling gebruikt. Kan ik later terugkeren naar versleuteling met behulp van door het platform beheerde sleutels, zelfs als ik back-upitems heb beveiligd voor de kluis?

Nee, nadat u CMK-versleuteling hebt ingeschakeld, kan deze niet meer worden teruggedraaid voor het gebruik van door het platform beheerde sleutels. U kunt de sleutels wijzigen die worden gebruikt volgens uw vereisten.

### <a name="does-cmk-encryption-for-azure-backup-also-apply-to-azure-site-recovery"></a>Is CMK-versleuteling voor Azure Backup ook van toepassing op Azure Site Recovery?

Nee, in dit artikel wordt alleen versleuteling van back-upgegevens besproken. Voor Azure Site Recovery moet u de eigenschap afzonderlijk instellen als beschikbaar vanuit de service.

### <a name="i-missed-one-of-the-steps-in-this-article-and-went-on-to-protect-my-data-source-can-i-still-use-cmk-encryption"></a>Ik heb een van de stappen in dit artikel gemist en ben verder gegaan met het beveiligen van mijn gegevensbron. Kan ik nog steeds CMK-versleuteling gebruiken?

Als u de stappen in het artikel niet volgt en items blijft beveiligen, kan dit ertoe leiden dat de kluis geen versleuteling kan gebruiken met behulp van door de klant beheerde sleutels. Daarom is het raadzaam om naar deze controlelijst te [verwijzen voordat](#backing-up-to-a-vault-encrypted-with-customer-managed-keys) u doorgaat met het beveiligen van items.

### <a name="does-using-cmk-encryption-add-to-the-cost-of-my-backups"></a>Voegt het gebruik van CMK-versleuteling toe aan de kosten van mijn back-ups?

Als u CMK-versleuteling voor Backup gebruikt, worden er geen extra kosten voor u in rekening brengen. U kunt echter nog steeds kosten in rekening brengen voor het gebruik van uw Azure Key Vault waar uw sleutel wordt opgeslagen.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van beveiligingsfuncties in Azure Backup](security-overview.md)
