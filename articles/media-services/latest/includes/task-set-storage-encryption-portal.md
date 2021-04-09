---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 11/04/2020
ms.author: inhenkel
ms.custom: portal
ms.openlocfilehash: c3a27218f03980e9b42a4a56fa739203c43754a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96013224"
---
<!--Set the encryption on storage account in the portal-->

## <a name="set-the-encryption-on-a-storage-account"></a>De versleuteling instellen voor een opslagaccount

1. Voer in de Azure Portal de naam in van het opslagaccount dat u wilt versleutelen in het veld **Zoeken** bovenaan het scherm.  De overeenkomsten worden onder het zoekveld weergegeven.
1. Selecteer het opslagaccount dat u zoekt. Het scherm voor het opslagaccount wordt weergegeven.
1. Selecteer **Versleuteling**.
1. Selecteer door Microsoft beheerde sleutels of door de klant beheerde sleutels.

### <a name="use-microsoft-managed-keys"></a>Gebruik door Microsoft beheerde sleutels

Gegevens in het opslagaccount worden standaard versleuteld met door Microsoft beheerde sleutels.

### <a name="use-customer-managed-keys"></a>Door de klant beheerde sleutels gebruiken

1. Selecteer **Door de klant beheerde sleutels**.
1. Selecteer **Sleutel-URI invoeren** of **Selecteren in sleutelkluis**.
    1. Als u **Sleutel-URI invoeren** selecteert, voert u de sleutel-URI in het veld Sleutel-URI in en selecteert u het abonnement. (Dit is mogelijk al voor u geselecteerd.)
    1. Als u **Selecteren in sleutelkluis** selecteert, dan selecteert u daarna **Een sleutelkluis en sleutel selecteren**. Het scherm Sleutel selecteren in Azure Key Vault wordt weergegeven.
1. Selecteer de **Key Vault** die u wilt gebruiken en selecteer een sleutel die u al in uw sleutelkluis hebt of selecteer **een nieuwe sleutel maken**.
    1. Als u ervoor kiest om een nieuwe sleutel te maken, selecteert u **Genereren** of **Importeren** uit de vervolgkeuzelijst **Opties**. U kunt alleen RSA-sleutels importeren.
    1. Als u een nieuwe sleutel wilt genereren, geeft u de sleutel een naam in het veld **Naam** en selecteert u vervolgens het Sleuteltype:
        1. RSA - Sleutelgroottes:  2048, 3072 of 4096. Dit is wat de meeste klanten kiezen.
        1. EC - Namen van elliptische curven: P-256, P-384, P-521 of P-256K
        1. U kunt desgewenst de activerings- en vervaldatum van de sleutel instellen.
        1. Selecteer **Ja** om automatische sleutelrotatie in te schakelen.
        1. Selecteer **Maken**.
    1. Als u een sleutel wilt importeren, selecteert u het bestand dat u wilt uploaden door te klikken in het veld **Een bestand selecteren**.
        1. Geef de sleutel een naam in het veld **Naam**.
        1. U kunt desgewenst de activerings- en vervaldatum van de sleutel instellen.
        1. Selecteer **Ja** om automatische sleutelrotatie in te schakelen.
        1. Selecteer **Maken**.
    1. Selecteer **Selecteren** om deze sleutel te selecteren voor het versleutelen van uw opslagaccount. U wordt weer teruggeleid naar het scherm voor Versleuteling.
1. **BELANGRIJK!** Selecteer **Opslaan** om uw versleutelingsinstellingen op te slaan want anders gaat alles wat u zojuist hebt gedaan verloren.
