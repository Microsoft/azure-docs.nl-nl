---
title: B2B-berichten beveiligen met certificaten
description: Voeg certificaten toe om B2B-berichten in Azure Logic Apps te beveiligen met de Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/17/2018
ms.openlocfilehash: 03fc17c0d071cef4c8de92c6b50d60d961d18aef
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91565256"
---
# <a name="improve-security-for-b2b-messages-by-using-certificates"></a>De beveiliging van B2B-berichten verbeteren met behulp van certificaten

Wanneer u B2B-communicatie vertrouwelijk wilt blijven gebruiken, kunt u de beveiliging van B2B-communicatie in uw bedrijfs integratie-apps, met name Logic apps, verhogen door certificaten aan uw integratie account toe te voegen. Certificaten zijn digitale documenten die de identiteiten van de deel nemers in elektronische communicatie controleren en u helpen communicatie op de volgende manieren te beveiligen:

* Bericht inhoud versleutelen.
* Berichten digitaal ondertekenen.

U kunt deze certificaten gebruiken in uw Enter prise Integration-apps:

* [Open bare certificaten](https://en.wikipedia.org/wiki/Public_key_certificate), die u moet aanschaffen bij een open bare Internet [certificerings instantie (CA)](https://en.wikipedia.org/wiki/Certificate_authority) , maar waarvoor geen sleutels zijn vereist. 

* Persoonlijke certificaten of [*zelfondertekende certificaten*](https://en.wikipedia.org/wiki/Self-signed_certificate), die u zelf maakt en zelf kunt uitgeven, maar waarvoor ook persoonlijke sleutels zijn vereist. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upload-a-public-certificate"></a>Een openbaar certificaat uploaden

Als u een *openbaar certificaat* wilt gebruiken in Logic apps die B2B-mogelijkheden hebben, moet u het certificaat eerst uploaden naar uw integratie account. Nadat u de eigenschappen hebt gedefinieerd in de [overeenkomsten](logic-apps-enterprise-integration-agreements.md) die u hebt gemaakt, is het certificaat beschikbaar om u te helpen bij het beveiligen van uw B2B-berichten.

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **alle resources** in het hoofd menu van Azure. Voer in het zoekvak de naam van uw integratie account in en selecteer vervolgens het gewenste integratie account.

   ![Uw integratie account zoeken en selecteren](media/logic-apps-enterprise-integration-certificates/select-integration-account.png)  

2. Kies onder **onderdelen** de tegel **certificaten** .

   ![Kies certificaten](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

3. Klik onder **certificaten** op **toevoegen**. Geef onder **certificaat toevoegen** deze gegevens voor uw certificaat op. Als u klaar bent, kiest u **Done**.

   | Eigenschap | Waarde | Beschrijving | 
   |----------|-------|-------------|
   | **Naam** | <*certificaat naam*> | De naam van uw certificaat, dat is "publicCert" in dit voor beeld | 
   | **Certificaattype** | Openbaar | Het type van het certificaat |
   | **Certificaat** | <*certificaat bestand-naam*> | Klik op het mappictogram naast het vak **certificaat** om het certificaat bestand te zoeken en te selecteren dat u wilt uploaden. |
   ||||

   ![Scherm afbeelding laat zien waar u toevoegen selecteert om certificaat details op te geven.](media/logic-apps-enterprise-integration-certificates/public-certificate-details.png)

   Nadat uw selectie door Azure is gevalideerd, uploadt Azure uw certificaat.

   ![Scherm afbeelding die laat zien waar het nieuwe certificaat wordt weer gegeven in Azure.](media/logic-apps-enterprise-integration-certificates/new-public-certificate.png) 

## <a name="upload-a-private-certificate"></a>Een persoonlijk certificaat uploaden

Als u een *persoonlijk certificaat* wilt gebruiken in Logic apps die B2B-mogelijkheden hebben, moet u het certificaat eerst uploaden naar uw integratie account. U moet ook een persoonlijke sleutel hebben die u eerst toevoegt aan [Azure Key Vault](../key-vault/general/overview.md). 

Nadat u de eigenschappen hebt gedefinieerd in de [overeenkomsten](logic-apps-enterprise-integration-agreements.md) die u hebt gemaakt, is het certificaat beschikbaar om u te helpen bij het beveiligen van uw B2B-berichten.

> [!NOTE]
> Voor persoonlijke certificaten moet u een bijbehorend openbaar certificaat toevoegen dat wordt weer gegeven in de instellingen voor **verzenden en ontvangen** van de [AS2-overeenkomst](logic-apps-enterprise-integration-as2.md) voor het ondertekenen en versleutelen van berichten.

1. [Voeg uw persoonlijke sleutel toe aan Azure Key Vault](../key-vault/certificates/certificate-scenarios.md#import-a-certificate) en geef een **sleutel naam** op.
   
2. Azure Logic Apps machtigen voor het uitvoeren van bewerkingen op Azure Key Vault. Als u toegang wilt verlenen tot de Logic Apps Service-Principal, gebruikt u de Power shell-opdracht [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy), bijvoorbeeld:

   `Set-AzKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **alle resources** in het hoofd menu van Azure. Voer in het zoekvak de naam van uw integratie account in en selecteer vervolgens het gewenste integratie account.

   ![Uw integratie account zoeken](media/logic-apps-enterprise-integration-certificates/select-integration-account.png) 

4. Kies onder **onderdelen** de tegel **certificaten** .  

   ![De tegel certificaten kiezen](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

5. Klik onder **certificaten** op **toevoegen**. Geef onder **certificaat toevoegen** deze gegevens voor uw certificaat op. Als u klaar bent, kiest u **Done**.

   | Eigenschap | Waarde | Beschrijving | 
   |----------|-------|-------------|
   | **Naam** | <*certificaat naam*> | De naam van uw certificaat, dat is "privateCert" in dit voor beeld | 
   | **Certificaattype** | Persoonlijk | Het type van het certificaat |
   | **Certificaat** | <*certificaat bestand-naam*> | Klik op het mappictogram naast het vak **certificaat** om het certificaat bestand te zoeken en te selecteren dat u wilt uploaden. Wanneer u een sleutel kluis voor de persoonlijke sleutel gebruikt, is het geüploade bestand het open bare certificaat. | 
   | **Resourcegroep** | <*Integration-account-Resource-Group*> | De resource groep van uw integratie account, dat wil zeggen ' MyResourceGroup ' in dit voor beeld | 
   | **Key Vault** | <*sleutel-kluis-naam*> | De naam van uw Azure-sleutel kluis |
   | **Sleutelnaam** | <*sleutel naam*> | De naam van uw sleutel |
   ||||

   ![Kies toevoegen, geef certificaat details op](media/logic-apps-enterprise-integration-certificates/private-certificate-details.png)

   Nadat uw selectie door Azure is gevalideerd, uploadt Azure uw certificaat.

   ![In azure wordt het nieuwe certificaat weer gegeven](media/logic-apps-enterprise-integration-certificates/new-private-certificate.png) 

## <a name="next-steps"></a>Volgende stappen

* [Een B2B-overeenkomst maken](logic-apps-enterprise-integration-agreements.md)
