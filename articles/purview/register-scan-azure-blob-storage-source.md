---
title: De Azure Storage-BLOB scannen
description: Meer informatie over het scannen van Azure Blob-opslag in uw Azure controle sfeer liggen Data Catalog.
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: b27b46c68d018d2ddf79d284b20cc05b51640891
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98880638"
---
# <a name="register-and-scan-azure-blob-storage"></a>Azure Blob Storage registreren en scannen

In dit artikel wordt beschreven hoe u een Azure Blob Storage-account registreert in controle sfeer liggen en hoe u een scan instelt.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Azure Blob Storage ondersteunt volledige en incrementele scans voor het vastleggen van de meta gegevens en het schema. De gegevens worden ook automatisch geclassificeerd op basis van systeem-en aangepaste classificatie regels.

## <a name="prerequisites"></a>Vereisten

- Voordat u gegevens bronnen registreert, maakt u een Azure controle sfeer liggen-account. Zie [Quick Start: een Azure controle sfeer liggen-account maken](create-catalog-portal.md)voor meer informatie over het maken van een controle sfeer liggen-account.
- U moet een Azure controle sfeer liggen-gegevens bron beheerder zijn

## <a name="setting-up-authentication-for-a-scan"></a>Verificatie voor een scan instellen

Er zijn drie manieren om verificatie voor Azure Blob-opslag in te stellen:

- Beheerde identiteit
- Account sleutel
- Service-principal

### <a name="managed-identity-recommended"></a>Beheerde identiteit (aanbevolen)

Wanneer u **beheerde identiteit** kiest om de verbinding in te stellen, moet u eerst uw controle sfeer liggen-account de machtiging geven om de gegevens bron te scannen:

1. Ga naar uw opslagaccount.
1. Selecteer **Access Control (IAM)** in het linker navigatiemenu. 
1. Selecteer **+ Toevoegen**.
1. Stel de **rol** in op **Storage BLOB data Reader** en voer uw Azure controle sfeer liggen-account naam in onder invoervak **selecteren** . Selecteer vervolgens **Opslaan** om deze rol toe te wijzen aan uw Purview-account.

> [!Note]
> Raadpleeg de stappen in [toegang verlenen tot blobs en wacht rijen met Azure Active Directory](../storage/common/storage-auth-aad.md) voor meer informatie.

### <a name="account-key"></a>Account sleutel

Wanneer de geselecteerde verificatie methode de **account sleutel** is, moet u uw toegangs sleutel ophalen en opslaan in de sleutel kluis:

1. Navigeer naar uw opslag account
1. **Instellingen > toegangs sleutels** selecteren
1. Kopieer uw *sleutel* en sla deze ergens op om de volgende stappen uit te voeren
1. Navigeer naar uw sleutelkluis
1. Selecteer **Instellingen > Geheimen**
1. Selecteer **+ genereren/importeren** en voer de **naam** en **waarde** in als de *sleutel* van uw opslag account
1. Selecteer **Maken** om te voltooien
1. Als uw sleutelkluis nog niet is verbonden met Purview, moet u [een nieuwe sleutelkluisverbinding maken](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Maak ten slotte [een nieuwe referentie](manage-credentials.md#create-a-new-credential) met behulp van de sleutel voor het instellen van de scan

### <a name="service-principal"></a>Service-principal

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

#### <a name="granting-the-service-principal-access-to-your-blob-storage"></a>De Service-Principal toegang verlenen tot uw Blob-opslag

1. Ga naar uw opslagaccount.
1. Selecteer **Access Control (IAM)** in het linker navigatiemenu. 
1. Selecteer **+ Toevoegen**.
1. Stel de **rol** in op **Storage BLOB data Reader** en voer uw service principal name of object-id in onder invoervak **selecteren** . Selecteer vervolgens **Opslaan** om deze roltoewijzing aan uw Service-Principal toe te wijzen.

## <a name="firewall-settings"></a>Firewallinstellingen

> [!NOTE]
> Als u firewall hebt ingeschakeld voor het opslag account, moet u de methode **beheerde identiteits** verificatie gebruiken bij het instellen van een scan.

1. Ga naar uw opslag account in [Azure Portal](https://portal.azure.com)
1. Navigeer naar **instellingen > netwerken** en
1. Selecteer **geselecteerde netwerken** onder **toegang toestaan vanaf**
1. Selecteer in de sectie **firewall** de optie **vertrouwde micro soft-Services toegang geven tot dit opslag account** en druk op **Opslaan**

:::image type="content" source="./media/register-scan-azure-blob-storage-source/firewall-setting.png" alt-text="Scherm opname van firewall instelling":::

## <a name="register-an-azure-blob-storage-account"></a>Een Azure Blob Storage-account registreren

Ga als volgt te werk om een nieuw BLOB-account in uw Data Catalog te registreren:

1. Ga naar uw Purview-account
1. Selecteer **Bronnen** in het linkernavigatievenster
1. Selecteer **Registreren**
1. Selecteer **Azure Blob Storage** bij **bronnen registreren**
1. Selecteer **Doorgaan**

Ga als volgt te werk op het scherm **bronnen registreren (Azure Blob Storage)** :

1. Voer een **Naam** in waarvan de gegevensbron wordt vermeld in de catalogus. 
1. Kies uw abonnement om opslag accounts te filteren
1. Selecteer een opslagaccount
1. Selecteer een verzameling of maak een nieuwe (optioneel)
1. **Voltooi** om de gegevensbron te registreren.

:::image type="content" source="media/register-scan-azure-blob-storage-source/register-sources.png" alt-text="opties voor bronnen registreren" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Volgende stappen

- [Bladeren door de Azure Purview-gegevenscatalogus](how-to-browse-catalog.md)
- [Zoeken in de Azure Purview-gegevenscatalogus](how-to-search-catalog.md)