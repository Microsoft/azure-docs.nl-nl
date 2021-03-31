---
title: Gebruik de Azure Portal voor het configureren van door de klant beheerde sleutels voor de import/export-service
description: Meer informatie over het gebruik van de Azure Portal voor het configureren van door de klant beheerde sleutels met Azure Key Vault voor Azure import/export-service. Door de klant beheerde sleutels bieden u de mogelijkheid om toegangs beheer te maken, te draaien, uit te scha kelen en in te trekken.
services: storage
author: alkohli
ms.service: storage
ms.topic: how-to
ms.date: 01/14/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 71c322385c9b70eb46e1c478c1322752ee4561e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98706492"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-importexport-service"></a>Door de klant beheerde sleutels gebruiken in Azure Key Vault voor de import/export-service

Azure import/export beschermt de BitLocker-sleutels die worden gebruikt om de stations te vergren delen via een versleutelings sleutel. BitLocker-sleutels worden standaard versleuteld met door micro soft beheerde sleutels. Voor extra controle over versleutelings sleutels kunt u ook door de klant beheerde sleutels opgeven.

Door de klant beheerde sleutels moeten worden gemaakt en opgeslagen in een Azure Key Vault. Zie [Wat is Azure Key Vault?](../key-vault/general/overview.md) voor meer informatie over Azure Key Vault.

In dit artikel wordt beschreven hoe u door de klant beheerde sleutels kunt gebruiken met de import/export-service in de [Azure Portal](https://portal.azure.com/).

## <a name="prerequisites"></a>Vereisten

Zorg voordat u begint voor het volgende:

1. U hebt een import-of export taak gemaakt volgens de instructies in:

    - [Een import taak maken voor blobs](storage-import-export-data-to-blobs.md).
    - [Een import taak maken voor bestanden](storage-import-export-data-to-files.md).
    - [Een export taak maken voor blobs](storage-import-export-data-from-blobs.md)

2. U hebt een bestaande Azure Key Vault met een sleutel die u kunt gebruiken om uw BitLocker-sleutel te beveiligen. Ga voor meer informatie over het maken van een sleutel kluis met behulp van de Azure Portal naar [Quick Start: een Azure Key Vault maken met behulp van de Azure Portal](../key-vault/general/quick-create-portal.md).

    - **Voorlopig verwijderen** en **niet wissen** worden ingesteld op uw bestaande Key Vault. Deze eigenschappen zijn niet standaard ingeschakeld. Als u deze eigenschappen wilt inschakelen, raadpleegt u de secties het **inschakelen van zacht verwijderen** en het inschakelen van de **beveiliging opschonen** in een van de volgende artikelen:

        - [Voorlopig verwijderen gebruiken met Power shell](../key-vault/general/key-vault-recovery.md).
        - [Voorlopig verwijderen gebruiken met CLI](../key-vault/general/key-vault-recovery.md).
    - De bestaande sleutel kluis moet een RSA-sleutel hebben van 2048-grootte of meer. Zie [about Keys](../key-vault/keys/about-keys.md)(Engelstalig) voor meer informatie over sleutels.
    - De sleutel kluis moet zich in dezelfde regio bevinden als het opslag account voor uw gegevens.  
    - Als u geen bestaande Azure Key Vault hebt, kunt u deze inline ook maken zoals beschreven in de volgende sectie.

## <a name="enable-keys"></a>Sleutels inschakelen

Het configureren van door de klant beheerde sleutel voor uw import/export-service is optioneel. De import/export-service maakt standaard gebruik van een door micro soft beheerde sleutel voor het beveiligen van uw BitLocker-sleutel. Voer de volgende stappen uit om door de klant beheerde sleutels in te scha kelen in de Azure Portal:

1. Ga naar de Blade **overzicht** voor uw import taak.
2. Selecteer in het rechterdeel venster **kiezen hoe uw BitLocker-sleutels moeten worden versleuteld**.

    ![Versleutelings optie kiezen](./media/storage-import-export-encryption-key-portal/encryption-key-1.png)

3. Op de Blade **versleuteling** kunt u de BitLocker-sleutel van het apparaat weer geven en kopiëren. Onder **versleutelings type** kunt u kiezen hoe u uw BitLocker-sleutel wilt beveiligen. Standaard wordt een door micro soft beheerde sleutel gebruikt.

    ![BitLocker-sleutel weer geven](./media/storage-import-export-encryption-key-portal/encryption-key-2.png)

4. U hebt de optie om een door de klant beheerde sleutel op te geven. Nadat u de door de klant beheerde sleutel hebt geselecteerd, **selecteert u sleutel kluis en een sleutel**.

    ![Door de klant beheerde sleutel selecteren](./media/storage-import-export-encryption-key-portal/encryption-key-3.png)

5. Het abonnement wordt automatisch ingevuld in de blade **Sleutel selecteren in Azure Key Vault**. Voor **Sleutelkluis** kunt u een bestaande sleutelkluis selecteren in de vervolgkeuzelijst.

    ![Azure Key Vault selecteren of maken](./media/storage-import-export-encryption-key-portal/encryption-key-4.png)

6. U kunt ook **Nieuwe maken** selecteren om een nieuwe sleutelkluis te maken. Voer op de **Blade sleutel kluis maken** de resource groep en de naam van de sleutel kluis in. Accepteer alle overige standaard waarden. Selecteer **Controleren + maken**.

    ![Nieuwe Azure Key Vault maken](./media/storage-import-export-encryption-key-portal/encryption-key-5.png)

7. Controleer de informatie die is gekoppeld aan uw sleutel kluis en selecteer **maken**. Wacht enkele minuten totdat de sleutel kluis is gemaakt.

    ![Azure Key Vault maken](./media/storage-import-export-encryption-key-portal/encryption-key-6.png)

8. U kunt in de **sleutel Select from Azure Key Vault** een sleutel selecteren in de bestaande sleutel kluis.

9. Als u een nieuwe sleutel kluis hebt gemaakt, selecteert u **nieuwe maken** om een sleutel te maken. RSA-sleutel grootte kan 2048 of hoger zijn.

    ![Nieuwe sleutel maken in Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-7.png)

    Als de beveiliging voor voorlopig verwijderen en leegmaken niet is ingeschakeld wanneer u de sleutel kluis maakt, wordt de sleutel kluis bijgewerkt zodat de beveiliging van de functie voor verwijderen en leegmaken is ingeschakeld.

10. Geef de naam voor de sleutel op, accepteer de andere standaard waarden en selecteer **maken**.

    ![Nieuwe sleutel maken](./media/storage-import-export-encryption-key-portal/encryption-key-8.png)

11. Selecteer de **versie** en kies vervolgens **selecteren**. U wordt gewaarschuwd dat er een sleutel in uw sleutel kluis is gemaakt.

    ![Nieuwe sleutel gemaakt in sleutelkluis](./media/storage-import-export-encryption-key-portal/encryption-key-9.png)

Op de Blade **versleuteling** ziet u de sleutel kluis en de sleutel die is geselecteerd voor de door de klant beheerde sleutel.

> [!IMPORTANT]
> U kunt alleen door micro soft beheerde sleutels uitschakelen en verplaatsen naar door de klant beheerde sleutels in elke fase van de import/export-taak. U kunt de door de klant beheerde sleutel echter niet uitschakelen wanneer u deze hebt gemaakt.

## <a name="troubleshoot-customer-managed-key-errors"></a>Problemen oplossen met door de klant beheerde sleutel fouten

Als u fouten met betrekking tot de door de klant beheerde sleutel ontvangt, gebruikt u de volgende tabel om problemen op te lossen:

| Foutcode     |Details     | Herstel bare?    |
|----------------|------------|-----------------|
| CmkErrorAccessRevoked | De toegang tot de door de klant beheerde sleutel is ingetrokken.                                                       | Ja, controleren of: <ol><li>Sleutel kluis heeft nog steeds de MSI in het toegangs beleid.</li><li>Voor het toegangs beleid zijn de machtigingen ophalen, verpakken en uitpakken ingeschakeld.</li><li>Als de sleutel kluis zich in een VNet achter de firewall bevindt, controleert u of **micro soft Trusted Services toestaan** is ingeschakeld.</li><li>Controleer of de MSI van de taak resource is ingesteld op het `None` gebruik van api's.<br>Zo ja, dan stelt u de waarde weer in op `Identity = SystemAssigned` . Hiermee wordt de identiteit voor de taak resource opnieuw gemaakt.<br>Nadat de nieuwe identiteit is gemaakt, ingeschakeld `Get` , `Wrap` en `Unwrap` machtigingen voor de nieuwe identiteit in het toegangs beleid van de sleutel kluis</li></ol>                                                                                            |
| CmkErrorKeyDisabled      | De door de klant beheerde sleutel is uitgeschakeld.                                         | Ja, door de sleutel versie in te scha kelen     |
| CmkErrorKeyNotFound      | De door de klant beheerde sleutel is niet gevonden. | Ja, als de sleutel is verwijderd, maar deze nog steeds binnen de duur van het opschonen valt, met behulp van [sleutel kluis ongedaan maken verwijderen](/powershell/module/az.keyvault/undo-azkeyvaultkeyremoval).<br>Hierin <ol><li>Ja, als de klant een back-up van de sleutel heeft gemaakt en deze herstelt.</li><li>Nee, anders.</li></ol>
| CmkErrorVaultNotFound |Kan de sleutel kluis van de door de klant beheerde sleutel niet vinden. |   Als de sleutel kluis is verwijderd:<ol><li>Ja, als het zich in de duur van de schone beveiliging bevindt, gebruikt u de stappen in [een sleutel kluis herstellen](../key-vault/general/soft-delete-overview.md#key-vault-recovery).</li><li>Nee, als het na de duur van de schone beveiliging valt.</li></ol><br>Als de sleutel kluis is gemigreerd naar een andere Tenant, ja, kan deze worden hersteld met behulp van een van de volgende stappen:<ol><li>Herstel de sleutel kluis terug naar de oude Tenant.</li><li>Stel `Identity = None` in en stel vervolgens de waarde weer in op `Identity = SystemAssigned` . Hiermee wordt de identiteit verwijderd en opnieuw gemaakt zodra de nieuwe identiteit is gemaakt. Inschakelen `Get` , `Wrap` , en `Unwrap` machtigingen voor de nieuwe identiteit in het toegangs beleid van de sleutel kluis.</li></ol>|

## <a name="next-steps"></a>Volgende stappen

- [Wat is Azure Key Vault](../key-vault/general/overview.md)?