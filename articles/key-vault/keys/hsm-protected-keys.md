---
title: Met HSM beveiligde sleutels genereren en overdragen - Azure Key Vault
description: Meer informatie over het plannen, genereren en overdragen van uw eigen met HSM beveiligde sleutels voor gebruik met Azure Key Vault. Ook wel bekend als BYOK of Bring Your Own Key.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 02/24/2021
ms.author: ambapat
ms.openlocfilehash: a7e709ba9a4de5ff77524a2d2b1b64a5933131a2
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102489409"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>Met HSM beveiligde sleutels importeren in Key Vault

Als u Azure Key Vault gebruikt, kunt u het beste sleutels in HSM's (Hardware Security Modules) importeren of genereren die de HSM-grens nooit overschrijden. Dit scenario wordt vaak *Bring Your Own Key* of BYOK genoemd. Azure Key Vault maakt gebruik van de nCipher nShield-serie van HSM's (gevalideerd voor FIPS 140-2 Level 2) om uw sleutels te beveiligen.

Deze functionaliteit is niet beschikbaar voor Azure China 21Vianet.

> [!NOTE]
> Zie [Wat is Azure Key Vault?](../general/overview.md) voor meer informatie over Azure Key Vault.  
> Zie [Wat is Azure Key Vault?](../general/overview.md) voor een zelfstudie waarmee u een sleutelkluis kunt maken voor met HSM beveiligde sleutels.

## <a name="supported-hsms"></a>Ondersteunde HSM's

Het overdragen van met HSM beveiligde sleutels naar Key Vault wordt ondersteund door twee verschillende methoden, afhankelijk van de HSM's die u gebruikt. Gebruik de onderstaande tabel om te bepalen welke methode moet worden gebruikt voor het genereren van uw HSM's. Draag vervolgens uw eigen met HSM beveiligde sleutels over voor gebruik met Azure Key Vault. 

|Naam van leverancier|Type leverancier|Ondersteunde HSM-modellen|Ondersteunde methode voor het overdragen van HSM-sleutels|
|---|---|---|---|
|[nCipher](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|Fabrikant,<br/>HSM as a Service|<ul><li>nShield-serie van HSM's</li><li>nShield as a service</ul>|**Methode 1:** [nCipher BYOK](hsm-protected-keys-ncipher.md) (afgeschaft)<br/>**Methode 2:** de [New BYOK-methode gebruiken](hsm-protected-keys-byok.md) (aanbevolen)|
|Thales|Fabrikant|<ul><li>Luna HSM 7-serie met firmwareversie 7.3 of hoger</li></ul>| [De nieuwe BYOK-methode gebruiken](hsm-protected-keys-byok.md)|
|Fortanix|Fabrikant,<br/>HSM as a Service|<ul><li>Self-Defending Key Management Service (SDKMS)</li><li>Equinix SmartKey</li></ul>|[De nieuwe BYOK-methode gebruiken](hsm-protected-keys-byok.md)|
|Marvell|Fabrikant|Alle LiquidSecurity-HSM's met<ul><li>Firmwareversie 2.0.4 of hoger</li><li>Firmwareversie 3.2 of hoger</li></ul>|[De nieuwe BYOK-methode gebruiken](hsm-protected-keys-byok.md)|
|Cryptomathic|ISV (Enterprise Key Management System)|Meerdere HSM-merken en -modellen, inclusief<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>Zie de [site van Cryptomathic voor meer informatie](https://www.cryptomathic.com/azurebyok)|[De nieuwe BYOK-methode gebruiken](hsm-protected-keys-byok.md)|
|Securosys SA|Fabrikant,<br/>HSM as a service|Primus HSM-serie, Securosys Clouds HSM|[De nieuwe BYOK-methode gebruiken](hsm-protected-keys-byok.md)|
|StorMagic|ISV (Enterprise Key Management System)|Meerdere HSM-merken en -modellen, inclusief<ul><li>Utimaco</li><li>Thales</li><li>nCipher</li></ul>Raadpleeg de [StorMagic-site voor informatie](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|[De nieuwe BYOK-methode gebruiken](hsm-protected-keys-byok.md)|
|IBM|Fabrikant|IBM 476x, CryptoExpress|[De nieuwe BYOK-methode gebruiken](hsm-protected-keys-byok.md)|
|Utimaco|Fabrikant,<br/>HSM as a service|u. Trust-anker, CryptoServer|[De nieuwe BYOK-methode gebruiken](hsm-protected-keys-byok.md)|
|||||

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg het [Overzicht voor Key Vault-beveiliging](../general/security-overview.md) om beveiliging, duurzaamheid en bewaking voor uw sleutels te garanderen.
* Raadpleeg [BYOK-specificatie](./byok-specification.md) voor een volledige beschrijving van de nieuwe BYOK-methode