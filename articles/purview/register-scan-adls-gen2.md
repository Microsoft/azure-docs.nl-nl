---
title: Azure Data Lake Storage registreren en scannen (ADLS) Gen2
description: In deze zelf studie wordt beschreven hoe u Azure Data Lake Storage Gen2 scant.
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 4b7f71b5405708cc1988fafa5ca9c4628fe0d80b
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98882396"
---
# <a name="register-and-scan-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 registreren en scannen

In dit artikel wordt beschreven hoe u Azure Data Lake Storage Gen2 als gegevens bron registreert in azure controle sfeer liggen en een scan instelt.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

De Azure Data Lake Storage Gen2 gegevens bron ondersteunt de volgende functionaliteit:

- **Volledige en incrementele scans** voor het vastleggen van meta gegevens en classificatie in azure data Lake Storage Gen2

- **Afkomst** tussen de gegevensassets voor ADF-Kopieer-en gegevensstroom activiteiten

## <a name="prerequisites"></a>Vereisten

Voordat u gegevens bronnen registreert, maakt u een Azure controle sfeer liggen-account. Zie [Quick Start: een Azure controle sfeer liggen-account maken](create-catalog-portal.md)voor meer informatie over het maken van een controle sfeer liggen-account.

### <a name="setting-up-authentication-for-a-scan"></a>Verificatie voor een scan instellen

De volgende verificatie methoden worden ondersteund voor Azure Data Lake Storage Gen2:

- Beheerde identiteit
- Service-principal
- Account sleutel

#### <a name="managed-identity-recommended"></a>Beheerde identiteit (aanbevolen)

Wanneer u **beheerde identiteit** kiest om de verbinding in te stellen, moet u eerst uw controle sfeer liggen-account de machtiging geven om de gegevens bron te scannen:

1. Navigeer naar uw ADLS Gen2 Storage-account.
1. Selecteer **Access Control (IAM)** in het linker navigatiemenu. 
1. Selecteer **+ Toevoegen**.
1. Stel de **rol** in op **Storage BLOB data Reader** en voer uw Azure controle sfeer liggen-account naam in onder invoervak **selecteren** . Selecteer vervolgens **Opslaan** om deze rol toe te wijzen aan uw Purview-account.

> [!Note]
> Raadpleeg de stappen in [toegang verlenen tot blobs en wacht rijen met Azure Active Directory](../storage/common/storage-auth-aad.md) voor meer informatie.

#### <a name="account-key"></a>Account sleutel

Wanneer de geselecteerde verificatie methode de **account sleutel** is, moet u uw toegangs sleutel ophalen en opslaan in de sleutel kluis:

1. Navigeer naar uw ADLS Gne2-opslag account
1. **Instellingen > toegangs sleutels** selecteren
1. Kopieer uw *sleutel* en sla deze ergens op om de volgende stappen uit te voeren
1. Navigeer naar uw sleutelkluis
1. Selecteer **Instellingen > Geheimen**
1. Selecteer **+ genereren/importeren** en voer de **naam** en **waarde** in als de *sleutel* van uw opslag account
1. Selecteer **Maken** om te voltooien
1. Als uw sleutelkluis nog niet is verbonden met Purview, moet u [een nieuwe sleutelkluisverbinding maken](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Maak ten slotte [een nieuwe referentie](manage-credentials.md#create-a-new-credential) met behulp van de sleutel voor het instellen van de scan

#### <a name="service-principal"></a>Service-principal

Als u een service-principal wilt gebruiken, kunt u een bestaande gebruiken of een nieuwe maken. 

> [!Note]
> Als u een nieuwe service-principal moet maken, volgt u deze stappen:
> 1. Navigeer naar [Azure Portal](https://portal.azure.com).
> 1. Selecteer **Azure Active Directory** in het menu aan de linkerkant.
> 1. Selecteer **App-registraties**.
> 1. Selecteer **+ Nieuwe toepassing registreren**.
> 1. Voer een naam in voor de **toepassing** (de service-principal-naam).
> 1. Selecteer **Alleen accounts in deze organisatiemap**.
> 1. Bij Omleidings-URI selecteert u **Web** en voert u de gewenste URL in. Dit hoeft geen echte of werkende URL te zijn.
> 1. Selecteer vervolgens **Registreren**.

Het is vereist om de toepassings-ID en het geheim van de Service-Principal op te halen:

1. Navigeer naar uw service-principal in [Azure Portal](https://portal.azure.com)
1. Kopieer de waarde van de **Toepassings-id (client)** uit **Overzicht** en van **Clientgeheim** uit **Certificaten & geheimen**.
1. Navigeer naar uw sleutelkluis
1. Selecteer **Instellingen > Geheimen**
1. Selecteer **+ Genereren/importeren** en voer de **Naam** van uw keuze in en de **Waarde** als het **Clientgeheim** van uw service-principal
1. Selecteer **Maken** om te voltooien
1. Als uw sleutelkluis nog niet is verbonden met Purview, moet u [een nieuwe sleutelkluisverbinding maken](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Maak tot slot [een nieuwe referentie](manage-credentials.md#create-a-new-credential) met behulp van de service-principal om uw scan in te stellen

##### <a name="granting-the-service-principal-access-to-your-adls-gen2-account"></a>De Service-Principal toegang verlenen tot uw ADLS Gen2-account

1. Ga naar uw opslagaccount.
1. Selecteer **Access Control (IAM)** in het linker navigatiemenu. 
1. Selecteer **+ Toevoegen**.
1. Stel de **rol** in op **Storage BLOB data Reader** en voer uw service principal name of object-id in onder invoervak **selecteren** . Selecteer vervolgens **Opslaan** om deze roltoewijzing aan uw Service-Principal toe te wijzen.
### <a name="firewall-settings"></a>Firewallinstellingen

> [!NOTE]
> Als u firewall hebt ingeschakeld voor het opslag account, moet u de methode **beheerde identiteits** verificatie gebruiken bij het instellen van een scan.

1. Ga naar uw ASLS Gen2-opslag account in [Azure Portal](https://portal.azure.com)
1. Navigeer naar **instellingen > netwerken** en
1. Selecteer **geselecteerde netwerken** onder **toegang toestaan vanaf**
1. Selecteer in de sectie **uitzonde ringen** de optie **vertrouwde micro soft-Services toegang geven tot dit opslag account** en druk op **Opslaan**

:::image type="content" source="./media/register-scan-adls-gen2/firewall-setting.png" alt-text="Scherm opname van firewall instelling":::

## <a name="register-azure-data-lake-storage-gen2-data-source"></a>Azure Data Lake Storage Gen2 gegevens bron registreren

Ga als volgt te werk om een nieuw ADLS Gen2-account in uw Data Catalog te registreren:

1. Ga naar uw Purview-account
2. Selecteer **Bronnen** in het linkernavigatievenster
3. Selecteer **Registreren**
4. Selecteer **Azure data Lake Storage Gen2** bij **bron registreren**
5. Selecteer **Doorgaan**

Ga als volgt te werk op het scherm **bronnen registreren (Azure data Lake Storage Gen2)** :

1. Voer een **Naam** in waarvan de gegevensbron wordt vermeld in de catalogus.
2. Kies uw abonnement om opslag accounts te filteren
3. Selecteer een opslagaccount
4. Selecteer een verzameling of maak een nieuwe (optioneel)
5. **Voltooi** om de gegevensbron te registreren.

:::image type="content" source="media/register-scan-adls-gen2/register-sources.png" alt-text="opties voor bronnen registreren" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Volgende stappen

- [Bladeren door de Azure Purview-gegevenscatalogus](how-to-browse-catalog.md)
- [Zoeken in de Azure Purview-gegevenscatalogus](how-to-search-catalog.md)