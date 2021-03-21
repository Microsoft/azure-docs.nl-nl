---
title: Zelfstudie - Frequentie van automatische rotatie van certificaten in Key Vault | Microsoft Docs
description: Zelfstudie over hoe u de frequentie van de automatische rotatie van certificaten in Azure Key Vault bijwerkt met de Azure-portal
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: 78cdc0b59fd178049e755cca5e1e909ac24483f2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102204045"
---
# <a name="tutorial-configure-certificate-auto-rotation-in-key-vault"></a>Zelfstudie: Automatische rotatie van certificaten in Key Vault configureren

U kunt eenvoudig digitale certificaat inrichten, beheren en implementeren met behulp van Azure Key Vault. De certificaten kunnen openbare en privé SSL- (Secure Sockets Layer) of TSL-certificaten (Transport Layer Security) zijn en ondertekend zijn door een certificeringsinstantie (CA) of een zelfondertekend certificaat zijn. Key Vault kan ook nieuwe certificaten aanvragen en verlengen via partnerschappen met CA's, wat een robuuste oplossing biedt voor het levenscyclusbeheer van certificaten. In deze zelfstudie werkt u de geldigheidsperiode, frequentie van automatische rotatie en CA-kenmerken bij.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een certificaat beheren met de Azure-portal.
> * Een CA-provideraccount toevoegen.
> * De geldigheidsperiode van het certificaat bijwerken.
> * De frequentie van automatische rotatie van het certificaat bijwerken.
> * De kenmerken van het certificaat bijwerken met Azure PowerShell.

Lees voordat u verdergaat eerst [Basisconcepten van Key Vault](../general/basic-concepts.md).

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-a-vault"></a>Een kluis maken

Maak een Azure-sleutelkluis met [Azure Portal](../general/quick-create-portal.md), [Azure CLI](../general/quick-create-cli.md) of [Azure PowerShell](../general/quick-create-powershell.md). In het voorbeeld is de naam van de sleutelkluis **Voorbeeldkluis**.

![Uitvoer nadat de sleutelkluis is gemaakt](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="create-a-certificate-in-key-vault"></a>Een certificaat maken in Key Vault

Maak een certificaat of importeer een certificaat in de sleutelkluis (raadpleeg [Stappen om een certificaat te maken in Key Vault](../secrets/quick-create-portal.md)). In dit geval werkt u met een certificaat die **Voorbeeldcertificaat** heet.

## <a name="update-certificate-lifecycle-attributes"></a>De levenscycluskenmerken van een certificaat bijwerken

In Azure Key Vault kunt u de levenscycluskenmerken van een certificaat zowel voor als tijdens het maken van het certificaat bijwerken.

Een certificaat dat wordt gemaakt in Key Vault kan de volgende vorm aannemen:

- Een zelfondertekend certificaat.
- Een certificaat dat is gemaakt met een CA die partner is van Key Vault.
- Een certificaat met een CA die geen partner is van Key Vault.

De volgende CA's zijn momenteel partners van Key Vault:

- DigiCert: Key Vault biedt OV TLS-/SSL-certificaten.
- GlobalSign: Key Vault biedt OV TLS-/SSL-certificaten.

Key Vault roteert automatisch certificaten via gevestigde partnerschappen met CA's. Omdat Key Vault automatisch certificaten aanvraagt en vernieuwt via het partnerschap, is de mogelijkheid om automatisch te roteren niet van toepassing op certificaten die zijn gemaakt met certificeringsinstanties die geen partners zijn van Key Vault.

> [!NOTE]
> Een accountbeheerder voor een CA-provider maakt referenties die Key Vault gebruikt om TLS-/SSL-certificaten te maken, vernieuwen en gebruiken.
![Certificeringsinstantie](../media/certificates/tutorial-rotate-cert/cert-authority-create.png)
>

### <a name="update-certificate-lifecycle-attributes-at-the-time-of-creation"></a>Levenscycluskenmerken van het certificaat bijwerken op het moment van maken

1. Selecteer op de eigenschappenpagina's van de sleutelkluis **Certificaten**.
1. Selecteer **Genereren/importeren**.
1. Werk op het scherm **Een certificaat maken** de volgende waarden bij:

   - **Geldigheidsperiode**: Voer de waarde in (in maanden). Het maken van certificaten met een kort leven is een aanbevolen beveiligingspractice. Standaard is de geldigheidswaarde van nieuwe gemaakte certificaten 12 maanden.
   - **Actietype levensduur**: Selecteer de acties automatische verlenging en waarschuwing en werk vervolgens de **levensduurpercentage** of **Aantal dagen voor verlopen** bij. Standaard is de automatische verlenging van een certificaat ingesteld op 80% van de levensduur. Selecteer een van de volgende opties uit het vervolgkeuzemenu.

        |  Automatische verlenging op een bepaald moment| Alle contactpersonen e-mailen op een bepaald moment |
        |-----------|------|
        |Als u deze optie selecteert, wordt automatische rotatie *ingeschakeld*. | Als u deze optie selecteert, wordt automatische rotatie *niet ingeschakeld*, maar worden alleen contactpersonen gewaarschuwd.|

1. Selecteer **Maken**.

![Levenscyclus van een certificaat](../media/certificates/tutorial-rotate-cert/create-cert-lifecycle.png)

### <a name="update-lifecycle-attributes-of-a-stored-certificate"></a>De levenscycluskenmerken van een bewaard certificaat bijwerken

1. Selecteer de sleutelkluis.
1. Selecteer op de eigenschappenpagina's van de sleutelkluis **Certificaten**.
1. Selecteer het certificaat dat u wilt bijwerken. In dit geval werkt u met een certificaat die **Voorbeeldcertificaat** heet.
1. Selecteer **Uitgiftebeleid** in de bovenste menubalk.

   ![Schermopname waarop de knop Uitgiftebeleid is gemarkeerd.](../media/certificates/tutorial-rotate-cert/cert-issuance-policy.png)

1. Werk op het scherm **Uitgiftebeleid** de volgende waarden bij:

   - **Geldigheidsperiode**: Werk de waarde bij (in maanden).
   - **Actietype levensduur**: Selecteer de acties automatische verlenging en waarschuwing en werk vervolgens het **levensduurpercentage** of **Aantal dagen voor verlopen** bij.

   ![Certificaateigenschappen](../media/certificates/tutorial-rotate-cert/cert-policy-change.png)

1. Selecteer **Opslaan**.

> [!IMPORTANT]
> Als u de het actietype Levensduur van een certificaat verandert, worden bewerkingen voor bestaande certificaten onmiddellijk geregistreerd.


### <a name="update-certificate-attributes-by-using-powershell"></a>Certificaatkenmerken bijwerken met PowerShell

```azurepowershell


Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName 
                                   -Name $certificateName 
                                   -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
```

> [!TIP]
> Om het verlengingsbeleid voor een 
> <br/>
 `vault1,Cert1` <br/>
>  `vault2,Cert2`
>
>  ```azurepowershell
>  $file = Import-CSV C:\Users\myfolder\ReadCSVUsingPowershell\File.csv 
> foreach($line in $file)
> {
> Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName -Name $certificateName -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
> }
>  ```
> 
Raadpleeg [az keyvault certificate](/cli/azure/keyvault/certificate#az-keyvault-certificate-set-attributes) voor meer informatie over de parameters.

## <a name="clean-up-resources"></a>Resources opschonen

Andere Key Vault-zelfstudies borduren voort op deze zelfstudie. Als u van plan bent om met deze zelfstudies aan de slag te gaan, is het misschien handig om deze resources te bewaren.
Wanneer u die niet meer nodig hebt, verwijdert u de resourcegroep. Hierdoor worden ook de sleutelkluis en de gerelateerde resources verwijderd.

De resourcegroep verwijderen via de portal:

1. Typ de naam van uw resourcegroep in het **zoekvak** bovenaan de portal. Wanneer de in deze quickstart gebruikte resourcegroep in de zoekresultaten verschijnt, selecteert u deze.
1. Selecteer **Resourcegroep verwijderen**.
1. Typ in het vak **TYP DE NAAM VAN DE RESOURCEGROEP** de naam van de resourcegroep en selecteer vervolgens **Verwijderen**.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u de levenscycluskenmerken van een certificaat bijgewerkt. Voor meer informatie over Key Vault en hoe u Key Vault integreert met uw toepassingen gaat u verder met een van de volgende artikelen:

- Lees meer over [Certificaten maken beheren in Azure Key Vault](./create-certificate-scenarios.md).
- Raadpleeg het [Overzicht van Key Vault](../general/overview.md).
