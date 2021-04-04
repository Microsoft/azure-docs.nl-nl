---
title: De validatie van de Azure Database for MySQL gegevens versleuteling controleren
description: Meer informatie over het valideren van de versleuteling van de Azure Database for MySQL gegevens versleuteling met behulp van de door de klant beheerde sleutel.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/28/2020
ms.openlocfilehash: 1f47b3d5679b70461eaba64b5815770162cb6fda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93241069"
---
# <a name="validating-data-encryption-for-azure-database-for-mysql"></a>Gegevens versleuteling valideren voor Azure Database for MySQL

Dit artikel helpt u bij het valideren of gegevens versleuteling met door de klant beheerde sleutel voor Azure Database for MySQL werkt zoals verwacht.

## <a name="check-the-encryption-status"></a>De versleutelings status controleren

### <a name="from-portal"></a>Vanuit de portal

1. Als u wilt controleren of de sleutel van de klant wordt gebruikt voor versleuteling, voert u de volgende stappen uit:

    * Ga in het Azure Portal naar de **Azure Key Vault**  ->  **sleutels**
    * Selecteer de sleutel die wordt gebruikt voor Server versleuteling.
    * Stel de status van de sleutel **ingeschakeld** in op **Nee**.
  
       Na enige tijd (**~ 15 min**) moet de Azure database for mysql-server **status** niet **toegankelijk** zijn. Bij een I/O-bewerking op de server wordt gecontroleerd of de server inderdaad is versleuteld met de sleutel klanten en de sleutel momenteel niet geldig is.
    
       Als u de server **beschikbaar** wilt maken, kunt u de sleutel opnieuw valideren. 
    
    * Stel de status van de sleutel in de Key Vault in op **Ja**.
    * Selecteer op de server **gegevens versleuteling** de optie **sleutel opnieuw valideren**.
    * Nadat de hervalidatie van de sleutel is geslaagd, wordt de server **status** gewijzigd in **beschikbaar**.

2. Als u op het Azure Portal kunt controleren of de versleutelings sleutel is ingesteld, worden de gegevens versleuteld met behulp van de sleutel klanten die worden gebruikt in de Azure Portal.

  :::image type="content" source="media/concepts-data-access-and-security-data-encryption/byok-validate.png" alt-text="Overzicht van toegangs beleid":::

### <a name="from-cli"></a>Van CLI

1. We kunnen *AZ cli* opdracht gebruiken om de belangrijkste resources te valideren die voor de Azure database for mysql-server worden gebruikt.

    ```azurecli-interactive
   az mysql server key list --name  '<server_name>'  -g '<resource_group_name>'
    ```

    Voor een server zonder gegevens versleutelings resulteert deze opdracht in een lege set [].

### <a name="azure-audit-reports"></a>Controle rapporten van Azure

[Audit rapporten](https://servicetrust.microsoft.com) kunnen ook worden gecontroleerd met informatie over de naleving van normen voor gegevens bescherming en regelgeving.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure database for MySQL Data Encryption with door de klant beheerde sleutel](concepts-data-encryption-mysql.md)voor meer informatie over gegevens versleuteling.