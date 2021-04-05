---
title: Gegevens versleuteling-Azure Portal-Azure Database for MySQL
description: Meer informatie over het instellen en beheren van gegevens versleuteling voor uw Azure Database for MySQL met behulp van de Azure Portal.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 01/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 00670746c1686bca354adc989ddce6c9dd336491
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96519056"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-portal"></a>Gegevens versleuteling voor Azure Database for MySQL met behulp van de Azure Portal

Meer informatie over het gebruik van de Azure Portal voor het instellen en beheren van gegevens versleuteling voor uw Azure Database for MySQL.

## <a name="prerequisites-for-azure-cli"></a>Vereisten voor Azure CLI

* U moet een Azure-abonnement hebben en een beheerder van dat abonnement zijn.
* In Azure Key Vault maakt u een sleutel kluis en een sleutel die u kunt gebruiken voor een door de klant beheerde sleutel.
* De sleutel kluis moet de volgende eigenschappen hebben voor gebruik als een door de klant beheerde sleutel:
  * [Voorlopig verwijderen](../key-vault/general/soft-delete-overview.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Beveiligd leegmaken](../key-vault/general/soft-delete-overview.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```
  * De Bewaar dagen zijn ingesteld op 90 dagen
  
    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --retention-days 90
    ```

* De sleutel moet de volgende kenmerken hebben om te kunnen worden gebruikt als een door de klant beheerde sleutel:
  * Geen vervaldatum
  * Niet uitgeschakeld
  * **Get**-, **wrap**-en **Unwrap** -bewerkingen uitvoeren
  * kenmerk recoverylevel is ingesteld op **hersteld** (hiervoor moet voorlopig verwijderen zijn ingeschakeld met de Bewaar periode ingesteld op 90 dagen)
  * Beveiliging tegen leegmaken is ingeschakeld

U kunt de bovenstaande kenmerken van de sleutel controleren met behulp van de volgende opdracht:

```azurecli-interactive
az keyvault key show --vault-name <key_vault_name> -n <key_name>
```

## <a name="set-the-right-permissions-for-key-operations"></a>De juiste machtigingen voor sleutel bewerkingen instellen

1. Selecteer in Key Vault **toegangs** beleid toegangs  >  **beleid toevoegen**.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png" alt-text="Scherm opname van Key Vault, met beleids regels voor toegang en toegangs beleid toevoegen gemarkeerd":::

2. Selecteer **sleutel machtigingen** en selecteer **ophalen**, **teruglopen**, **uitpakken** en de **Principal**. Dit is de naam van de mysql-server. Als uw server-Principal niet kan worden gevonden in de lijst met bestaande principals, moet u deze registreren. U wordt gevraagd om de serverprincipal te registreren wanneer u de gegevens versleuteling voor de eerste keer probeert in te stellen. dit mislukt.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png" alt-text="Overzicht van toegangs beleid":::

3. Selecteer **Opslaan**.

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>Gegevens versleuteling instellen voor Azure Database for MySQL

1. Selecteer in Azure Database for MySQL **gegevens versleuteling** om de door de klant beheerde sleutel in te stellen.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png" alt-text="Scherm opname van Azure Database for MySQL, met de gegevens versleuteling gemarkeerd":::

2. U kunt een sleutel kluis en sleutel paar selecteren of een sleutel-id invoeren.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png" alt-text="Scherm opname van Azure Database for MySQL, met opties voor gegevens versleuteling gemarkeerd":::

3. Selecteer **Opslaan**.

4. Start de server opnieuw op om ervoor te zorgen dat alle bestanden (inclusief tijdelijke bestanden) volledig zijn versleuteld.

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Gegevens versleuteling gebruiken voor herstel-of replica servers

Nadat Azure Database for MySQL is versleuteld met een door de klant beheerde sleutel die is opgeslagen in Key Vault, wordt een nieuw gemaakt exemplaar van de server ook versleuteld. U kunt deze nieuwe kopie maken via een lokale of geo-herstel bewerking, of via een replica (lokale/Kruis regio). Voor een versleutelde MySQL-server kunt u dus de volgende stappen gebruiken om een versleutelde herstelde server te maken.

1. Selecteer **overzicht**  >  **herstellen** op uw server.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-restore.png" alt-text="Scherm opname van Azure Database for MySQL, met overzicht en herstellen gemarkeerd":::

   Of voor een server waarvoor replicatie is ingeschakeld, selecteert u in de kop **instellingen** de optie **replicatie**.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/mysql-replica.png" alt-text="Scherm opname van Azure Database for MySQL, waarbij de replicatie is gemarkeerd":::

2. Nadat de herstel bewerking is voltooid, wordt de nieuwe gemaakte server versleuteld met de sleutel van de primaire server. De functies en opties op de server zijn echter uitgeschakeld en de server is niet toegankelijk. Dit voor komt dat gegevens worden gemanipuleerd, omdat de identiteit van de nieuwe server nog niet is gemachtigd voor toegang tot de sleutel kluis.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png" alt-text="Scherm opname van Azure Database for MySQL, met een niet-toegankelijke status gemarkeerd":::

3. Valideer de sleutel op de herstelde server om de server toegankelijk te maken. Selecteer   >  **sleutel voor hervalideren** van gegevens versleuteling.

   > [!NOTE]
   > De eerste poging om opnieuw te valideren, mislukt, omdat de service-principal van de nieuwe server toegang moet krijgen tot de sleutel kluis. Als u de Service-Principal wilt genereren, selecteert u **sleutel opnieuw valideren**. er wordt dan een fout weer gegeven, maar de service-principal wordt gegenereerd. Ga daarna naar [deze stappen](#set-the-right-permissions-for-key-operations) eerder in dit artikel.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png" alt-text="Scherm opname van Azure Database for MySQL, waarbij de stap voor hervalidatie is gemarkeerd":::

   U moet de sleutel kluis toegang verlenen tot de nieuwe server.

4. Nadat u de Service-Principal hebt geregistreerd, moet u de sleutel opnieuw valideren en wordt de normale functionaliteit van de server hervat.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/restore-successful.png" alt-text="Scherm opname van Azure Database for MySQL, met de herstelde functionaliteit":::

## <a name="next-steps"></a>Volgende stappen

 Zie [Azure database for MySQL Data Encryption with door de klant beheerde sleutel](concepts-data-encryption-mysql.md)voor meer informatie over gegevens versleuteling.
