---
title: Versleuteling van back-upgegevens met door de klant beheerde sleutels
description: Meer informatie over hoe u met Azure Backup uw back-upgegevens kunt versleutelen met behulp van door de klant beheerde sleutels (CMK).
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 474f4238276f460abde3d600422e309171875a0c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101716734"
---
# <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Versleuteling van back-upgegevens met door de klant beheerde sleutels

Met Azure Backup kunt u uw back-upgegevens versleutelen met door de klant beheerde sleutels (CMK) in plaats van door het platform beheerde sleutels te gebruiken. Dit is standaard ingeschakeld. De sleutels die worden gebruikt voor het versleutelen van de back-upgegevens, moeten worden opgeslagen in [Azure Key Vault](../key-vault/index.yml).

De versleutelings sleutel die wordt gebruikt voor het versleutelen van back-ups is mogelijk anders dan die voor de bron. De gegevens worden beveiligd met behulp van een AES 256-gegevens versleutelings sleutel (DEK), die op zijn beurt wordt beveiligd met behulp van uw sleutels (KEK). Hiermee krijgt u volledige controle over de gegevens en de sleutels. Als u versleuteling wilt toestaan, moet de Recovery Services-kluis toegang krijgen tot de versleutelings sleutel in de Azure Key Vault. U kunt de sleutel wijzigen als en wanneer dat nodig is.

In dit artikel komen de volgende onderwerpen aan bod:

- Een Recovery Services kluis maken
- Uw Recovery Services kluis configureren voor het versleutelen van back-upgegevens met door de klant beheerde sleutels
- Back-ups uitvoeren op kluizen die zijn versleuteld met door de klant beheerde sleutels
- Gegevens herstellen vanuit een back-up

## <a name="before-you-start"></a>Voordat u begint

- Met deze functie kunt u **alleen nieuwe Recovery Services kluizen** versleutelen. Kluizen die bestaande items bevatten die zijn geregistreerd of proberen te worden geregistreerd, worden niet ondersteund.

- Wanneer een Recovery Services kluis is ingeschakeld, kan de versleuteling met door de klant beheerde sleutels niet worden teruggedraaid naar het gebruik van door het platform beheerde sleutels (standaard). U kunt de versleutelings sleutels wijzigen volgens uw vereisten.

- Deze functie **biedt momenteel geen ondersteuning voor het maken van back-ups met Mars agent** en u kunt mogelijk geen met CMK versleutelde kluis gebruiken. De MARS-agent maakt gebruik van een code ring op basis van een wachtwoordzin. Deze functie biedt ook geen ondersteuning voor het maken van back-ups van klassieke virtuele machines.

- Deze functie is niet gerelateerd aan [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md), die gebruikmaakt van versleuteling op basis van een gast van de schijven van een virtuele machine met behulp van BitLocker (voor Windows) en DM-Crypt (voor Linux)

- De Recovery Services kluis kan alleen worden versleuteld met sleutels die zijn opgeslagen in een Azure Key Vault, die zich in **dezelfde regio** bevinden. Daarnaast moeten sleutels alleen **RSA 2048-sleutels** zijn en de status **ingeschakeld** hebben.

- Het verplaatsen van CMK versleutelde Recovery Services kluis over resource groepen en abonnementen wordt momenteel niet ondersteund.
- Wanneer u een Recovery Services kluis verplaatst die al is versleuteld met door de klant beheerde sleutels naar een nieuwe Tenant, moet u de Recovery Services kluis bijwerken om de beheerde identiteit en CMK van de kluis opnieuw te maken en opnieuw te configureren (deze moet zich in de nieuwe Tenant bevinden). Als dat niet het geval is, mislukken de back-up-en herstel bewerkingen. Daarnaast moeten alle RBAC-machtigingen (op rollen gebaseerd toegangs beheer) die in het abonnement zijn ingesteld, opnieuw worden geconfigureerd.

- Deze functie kan worden geconfigureerd via de Azure Portal en Power shell.

    >[!NOTE]
    >Gebruik AZ module 5.3.0 of hoger voor het gebruik van door de klant beheerde sleutels voor back-ups in de Recovery Services kluis.

Als u uw Recovery Services kluis nog niet hebt gemaakt en geconfigureerd, kunt u [hier lezen hoe](backup-create-rs-vault.md)u dit doet.

## <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>Een kluis configureren om te versleutelen met door de klant beheerde sleutels

Deze sectie omvat de volgende stappen:

1. Beheerde identiteit inschakelen voor uw Recovery Services kluis

1. Machtigingen toewijzen aan de kluis om toegang te krijgen tot de versleutelings sleutel in de Azure Key Vault

1. Zacht verwijderen inschakelen en beveiliging opschonen op de Azure Key Vault

1. De versleutelings sleutel toewijzen aan de Recovery Services kluis

Het is nood zakelijk dat al deze stappen in de bovenstaande volg orde worden gevolgd om de beoogde resultaten te behaalt. Elke stap wordt hieronder uitvoerig besproken.

### <a name="enable-managed-identity-for-your-recovery-services-vault"></a>Beheerde identiteit inschakelen voor uw Recovery Services kluis

Azure Backup maakt gebruik van door het systeem toegewezen beheerde identiteit om de Recovery Services kluis te verifiëren voor toegang tot versleutelings sleutels die zijn opgeslagen in de Azure Key Vault. Volg de onderstaande stappen om de beheerde identiteit voor uw Recovery Services kluis in te scha kelen.

>[!NOTE]
>Wanneer de beheerde identiteit is ingeschakeld, mag deze **niet** worden uitgeschakeld (zelfs tijdelijk). Het uitschakelen van de beheerde identiteit kan leiden tot inconsistent gedrag.

**In de portal:**

1. Ga naar uw Recovery Services kluis-> **identiteit**

    ![Identiteits instellingen](./media/encryption-at-rest-with-cmk/managed-identity.png)

1. Wijzig de **status** in **op aan** en selecteer **Opslaan**.

1. Er wordt een object-ID gegenereerd. Dit is de door het systeem toegewezen beheerde identiteit van de kluis.

**Met Power shell:**

Gebruik de opdracht [Update-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/update-azrecoveryservicesvault) om door het systeem toegewezen beheerde identiteit voor de Recovery Services-kluis in te scha kelen.

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

### <a name="assign-permissions-to-the-recovery-services-vault-to-access-the-encryption-key-in-the-azure-key-vault"></a>Wijs machtigingen toe aan de Recovery Services kluis om toegang te krijgen tot de versleutelings sleutel in de Azure Key Vault

U moet nu toestaan dat de Recovery Services kluis toegang heeft tot de Azure Key Vault die de versleutelings sleutel bevat. Dit wordt gedaan door de beheerde identiteit van de Recovery Services kluis toegang te geven tot de Key Vault.

**In de portal**:

1. Ga naar uw Azure Key Vault-> **toegangs beleid**. Ga door naar **+ toegangs beleid toevoegen**.

    ![Toegangs beleid toevoegen](./media/encryption-at-rest-with-cmk/access-policies.png)

1. Selecteer onder **sleutel machtigingen** **ophalen**, **lijst**, **uitpakken van sleutel** en **ingepakte sleutel** bewerkingen. Hiermee geeft u de acties op de sleutel die wordt toegestaan.

    ![Sleutel machtigingen toewijzen](./media/encryption-at-rest-with-cmk/key-permissions.png)

1. Ga naar **Select Principal** en zoek in het zoekvak naar uw kluis met behulp van de naam of beheerde identiteit. Zodra deze wordt weer gegeven, selecteert u de kluis en kiest **u selecteren** onder aan het deel venster.

    ![Principal selecteren](./media/encryption-at-rest-with-cmk/select-principal.png)

1. Wanneer u klaar bent, selecteert u **toevoegen** om het nieuwe toegangs beleid toe te voegen.

1. Selecteer **Opslaan** om de wijzigingen op te slaan in het toegangs beleid van de Azure Key Vault.

### <a name="enable-soft-delete-and-purge-protection-on-the-azure-key-vault"></a>Zacht verwijderen inschakelen en beveiliging opschonen op de Azure Key Vault

U moet **voorlopig verwijderen en beveiliging opschonen inschakelen** op uw Azure Key Vault waarin uw versleutelings sleutel wordt opgeslagen. U kunt dit doen vanuit de Azure Key Vault-gebruikers interface, zoals hieronder wordt weer gegeven. (U kunt deze eigenschappen ook instellen tijdens het maken van de Key Vault). Meer informatie over deze Key Vault eigenschappen [vindt u hier](../key-vault/general/soft-delete-overview.md).

![Voorlopig verwijderen en opschonen beschermen inschakelen](./media/encryption-at-rest-with-cmk/soft-delete-purge-protection.png)

U kunt met behulp van de volgende stappen ook de beveiliging van zacht verwijderen en leegmaken via Power shell inschakelen:

1. Meld u aan bij uw Azure-account.

    ```azurepowershell
    Login-AzAccount
    ```

1. Selecteer het abonnement met uw kluis.

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

1. Leegmaken van beveiliging inschakelen

    ```azurepowershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "AzureKeyVaultName").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"
    ```

    ```azurepowershell
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

### <a name="assign-encryption-key-to-the-rs-vault"></a>Versleutelings sleutel toewijzen aan de RS-kluis

>[!NOTE]
> Voordat u verder gaat, controleert u het volgende:
>
> - Alle hierboven genoemde stappen zijn voltooid:
>   - De beheerde identiteit van de Recovery Services kluis is ingeschakeld en de vereiste machtigingen zijn toegewezen
>   - De Azure Key Vault heeft het voorlopig verwijderen en leegmaken van de beveiliging ingeschakeld
> - De Recovery Services kluis waarvoor u CMK-versleuteling wilt inschakelen, heeft **geen** items die worden beveiligd of geregistreerd

Zodra het bovenstaande is gewaarborgd, gaat u door met het selecteren van de versleutelings sleutel voor uw kluis.

#### <a name="to-assign-the-key-in-the-portal"></a>De sleutel in de portal toewijzen

1. Ga naar uw Recovery Services kluis-> **Eigenschappen**

    ![Versleutelingsinstellingen](./media/encryption-at-rest-with-cmk/encryption-settings.png)

1. Selecteer **Update** onder **versleutelings instellingen**.

1. Selecteer in het deel venster versleutelings instellingen de optie **uw eigen sleutel gebruiken** en ga door met het opgeven van de sleutel op een van de volgende manieren. **Zorg ervoor dat de sleutel die u wilt gebruiken een RSA 2048-sleutel is die de status ingeschakeld heeft.**

    1. Voer de **sleutel-URI** in waarmee u de gegevens in deze Recovery Services kluis wilt versleutelen. U moet ook het abonnement opgeven waarin de Azure Key Vault (die deze sleutel bevat) aanwezig is. Deze sleutel-URI kan worden opgehaald uit de bijbehorende sleutel in uw Azure Key Vault. Zorg ervoor dat de sleutel-URI correct is gekopieerd. Het is raadzaam om de knop **kopiëren naar klem bord te** gebruiken die is opgenomen in de sleutel-id.

        >[!NOTE]
        >Wanneer u de versleutelings sleutel opgeeft met behulp van de sleutel-URI, wordt de sleutel niet automatisch gedraaid. Sleutel updates moeten dus hand matig worden uitgevoerd door de nieuwe sleutel op te geven wanneer dat nodig is.

        ![Sleutel-URI invoeren](./media/encryption-at-rest-with-cmk/key-uri.png)

    1. Blader en selecteer de sleutel in de Key Vault in het deel venster sleutel kiezer.

        >[!NOTE]
        >Wanneer u de versleutelings sleutel opgeeft met het deel venster sleutel kiezer, wordt de sleutel automatisch gedraaid wanneer een nieuwe versie van de sleutel is ingeschakeld.

        ![Selecteer een sleutel in de sleutel kluis](./media/encryption-at-rest-with-cmk/key-vault.png)

1. Selecteer **Opslaan**.

1. **Voortgang en status van versleutelings sleutel bijwerken**: u kunt de voortgang en status van de toewijzing van de versleutelings sleutel bijhouden met behulp van de weer gave **back-uptaken** op de linkernavigatiebalk. De status moet binnenkort worden gewijzigd in **voltooid**. In uw kluis worden nu alle gegevens met de opgegeven sleutel als KEK versleuteld.

    ![Status voltooid](./media/encryption-at-rest-with-cmk/status-succeeded.png)

    De versleutelings sleutel updates worden ook geregistreerd in het activiteiten logboek van de kluis.

    ![Activiteitenlogboek](./media/encryption-at-rest-with-cmk/activity-log.png)

#### <a name="to-assign-the-key-with-powershell"></a>De sleutel toewijzen met Power shell

Gebruik de opdracht [set-AzRecoveryServicesVaultProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultproperty) om versleuteling in te scha kelen met door de klant beheerde sleutels en om de versleutelings sleutel die moet worden gebruikt, toe te wijzen of bij te werken.

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
> Dit proces blijft hetzelfde wanneer u de versleutelings sleutel wilt bijwerken of wijzigen. Als u een sleutel van een andere Key Vault wilt bijwerken en gebruiken (anders dan de versie die momenteel wordt gebruikt), zorgt u ervoor dat:
>
> - De sleutel kluis bevindt zich in dezelfde regio als de Recovery Services kluis
>
> - De sleutel kluis heeft de beveiliging voorlopig verwijderen en leegmaken is ingeschakeld
>
> - De Recovery Services kluis heeft de vereiste machtigingen voor toegang tot de sleutel kluis.

## <a name="backing-up-to-a-vault-encrypted-with-customer-managed-keys"></a>Back-ups maken naar een kluis die is versleuteld met door de klant beheerde sleutels

Voordat u kunt door gaan met het configureren van de beveiliging, wordt u aangeraden te controleren of aan de volgende controle lijst is gereageerd. Dit is belang rijk omdat als er een back-up van een item is geconfigureerd (of geprobeerd te worden geconfigureerd) naar een niet-CMK versleutelde kluis, versleuteling met door de klant beheerde sleutels niet kan worden ingeschakeld en de door het platform beheerde sleutels blijven gebruiken.

>[!IMPORTANT]
> Voordat u kunt door gaan met het configureren van de beveiliging, moet u de volgende **stappen hebben voltooid** :
>
>1. Uw back-upkluis gemaakt
>1. De door het systeem toegewezen beheerde identiteit van de back-upkluis is ingeschakeld
>1. De machtigingen voor de back-upkluis zijn toegewezen voor toegang tot de versleutelings sleutels van uw Key Vault
>1. Zacht verwijderen ingeschakeld en de beveiliging opschonen voor uw Key Vault
>1. Er is een geldige versleutelings sleutel toegewezen aan de back-upkluis
>
>Als alle bovenstaande stappen zijn bevestigd, gaat u alleen verder met het configureren van back-up.

Het proces voor het configureren en uitvoeren van back-ups op een Recovery Services kluis die is versleuteld met door de klant beheerde sleutels is hetzelfde als voor een kluis die door het platform beheerde sleutels gebruikt, zonder **wijzigingen in de ervaring**. Dit geldt voor [back-ups van virtuele Azure-machines](./quick-backup-vm-portal.md) en voor het maken van een back-up van werk belastingen die worden uitgevoerd in een virtuele machine (bijvoorbeeld [SAP Hana](./tutorial-backup-sap-hana-db.md), [SQL Server](./tutorial-sql-backup.md) data bases).

## <a name="restoring-data-from-backup"></a>Gegevens terugzetten vanuit een back-up

### <a name="vm-backup"></a>VM-back-up

Gegevens die zijn opgeslagen in de Recovery Services kluis, kunnen worden hersteld volgens de stappen die [hier](./backup-azure-arm-restore-vms.md)worden beschreven. Bij het herstellen van een Recovery Services kluis die is versleuteld met door de klant beheerde sleutels, kunt u ervoor kiezen om de herstelde gegevens te versleutelen met een schijf Encryption set (DES).

#### <a name="restoring-vm--disk"></a>VM/schijf herstellen

1. Bij het herstellen van de schijf/VM vanaf een herstel punt van een moment opname worden de herstelde gegevens versleuteld met de DES die wordt gebruikt voor het versleutelen van de schijven van de bron-VM.

1. Bij het herstellen van een schijf/VM vanaf een herstel punt met een herstel type als ' kluis ', kunt u ervoor kiezen om de herstelde gegevens te versleutelen met behulp van een DES, opgegeven op het moment van herstel. U kunt er ook voor kiezen om door te gaan met het herstellen van de gegevens zonder een DES op te geven. in dat geval wordt de code versleuteld met door micro soft beheerde sleutels.

U kunt de herstelde schijf/VM versleutelen nadat de herstel bewerking is voltooid, ongeacht de selectie die is gemaakt tijdens het initiëren van de herstel bewerking.

![Herstel punten](./media/encryption-at-rest-with-cmk/restore-points.png)

#### <a name="select-a-disk-encryption-set-while-restoring-from-vault-recovery-point"></a>Een schijf versleutelings selecteren tijdens het herstellen van het herstel punt van de kluis

**In de portal**:

De schijf versleutelings set is opgegeven onder versleutelings instellingen in het deel venster herstellen, zoals hieronder wordt weer gegeven:

1. Selecteer in de **schijf of schijven versleutelen met behulp van de sleutel** **Ja**.

1. Selecteer in de vervolg keuzelijst de DES die u wilt gebruiken voor de herstelde schijven. **Zorg ervoor dat u toegang hebt tot de DES.**

>[!NOTE]
>De mogelijkheid om een DES te kiezen tijdens het herstellen is niet beschikbaar als u een virtuele machine herstelt die gebruikmaakt van Azure Disk Encryption.

![Schijf versleutelen met behulp van uw sleutel](./media/encryption-at-rest-with-cmk/encrypt-disk-using-your-key.png)

**Met Power shell**:

Gebruik de opdracht [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) met de para meter [ `-DiskEncryptionSetId <string>` ] om [de des](/powershell/module/az.compute/get-azdiskencryptionset) op te geven die moet worden gebruikt voor het versleutelen van de herstelde schijf. Zie [dit artikel](./backup-azure-vms-automation.md#restore-an-azure-vm)voor meer informatie over het herstellen van schijven uit een back-up van de virtuele machine.

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

Wanneer u een bestand herstelt, worden de herstelde gegevens versleuteld met de sleutel die wordt gebruikt voor het versleutelen van de doel locatie.

### <a name="restoring-sap-hanasql-databases-in-azure-vms"></a>SAP HANA/SQL-data bases herstellen in azure Vm's

Bij het herstellen van een back-up SAP HANA/SQL database die wordt uitgevoerd in een Azure-VM, worden de herstelde gegevens versleuteld met behulp van de versleutelings sleutel die wordt gebruikt op de doel opslag locatie. Dit kan een door de klant beheerde sleutel zijn of een door een platform beheerde sleutel die wordt gebruikt voor het versleutelen van de schijven van de virtuele machine.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="can-i-encrypt-an-existing-backup-vault-with-customer-managed-keys"></a>Kan ik een bestaande back-upkluis versleutelen met door de klant beheerde sleutels?

Nee, CMK-versleuteling kan alleen worden ingeschakeld voor nieuwe kluizen. De kluis mag dus nooit items bevatten die erop zijn beveiligd. Het is niet mogelijk om items te beschermen voordat u versleuteling inschakelt met door de klant beheerde sleutels.

### <a name="i-tried-to-protect-an-item-to-my-vault-but-it-failed-and-the-vault-still-doesnt-contain-any-items-protected-to-it-can-i-enable-cmk-encryption-for-this-vault"></a>Ik heb geprobeerd een item te beveiligen voor mijn kluis, maar dit is mislukt en de kluis bevat nog steeds geen items die ermee zijn beveiligd. Kan ik CMK-versleuteling inschakelen voor deze kluis?

Nee, de kluis mag geen pogingen hebben gedaan om de items in het verleden te beveiligen.

### <a name="i-have-a-vault-thats-using-cmk-encryption-can-i-later-revert-to-encryption-using-platform-managed-keys-even-if-i-have-backup-items-protected-to-the-vault"></a>Ik heb een kluis die gebruikmaakt van CMK-versleuteling. Kan ik later teruggaan naar versleuteling met behulp van door het platform beheerde sleutels, zelfs als er back-upitems zijn die zijn beveiligd met de kluis?

Nee, wanneer u CMK-versleuteling hebt ingeschakeld, kan het niet worden hersteld voor het gebruik van door het platform beheerde sleutels. U kunt de sleutels die op basis van uw vereisten worden gebruikt, wijzigen.

### <a name="does-cmk-encryption-for-azure-backup-also-apply-to-azure-site-recovery"></a>Is CMK-versleuteling voor Azure Backup ook van toepassing op Azure Site Recovery?

Nee, in dit artikel wordt alleen de versleuteling van back-upgegevens beschreven. Voor Azure Site Recovery moet u de eigenschap afzonderlijk instellen als beschikbaar in de service.

### <a name="i-missed-one-of-the-steps-in-this-article-and-went-on-to-protect-my-data-source-can-i-still-use-cmk-encryption"></a>Ik heb een van de stappen in dit artikel gemist en is ingeschakeld om mijn gegevens bron te beveiligen. Kan ik nog steeds CMK-versleuteling gebruiken?

Het is niet mogelijk om de stappen in het artikel te volgen en door te gaan met het beveiligen van items kan ertoe leiden dat de kluis geen versleuteling kan gebruiken met door de klant beheerde sleutels. Daarom wordt u aangeraden [deze controle lijst](#backing-up-to-a-vault-encrypted-with-customer-managed-keys) te raadplegen voordat u doorgaat met het beveiligen van items.

### <a name="does-using-cmk-encryption-add-to-the-cost-of-my-backups"></a>Maakt gebruik van CMK-versleuteling voor de kosten van mijn back-ups?

Als u CMK-versleuteling voor back-up gebruikt, worden er geen extra kosten in rekening gebracht. U kunt echter nog steeds kosten in rekening brengen voor het gebruik van uw Azure Key Vault waar uw sleutel is opgeslagen.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van beveiligings functies in Azure Backup](security-overview.md)
