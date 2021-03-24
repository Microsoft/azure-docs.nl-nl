---
title: Met HSM beveiligde sleutels genereren en overdragen - BYOK - Azure Key Vault
description: Gebruik dit artikel bij het plannen, genereren en overbrengen van uw eigen, met HSM beveiligde sleutels voor gebruik met Azure Key Vault. Ook wel bekend als Bring Your Own Key (BYOK).
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: ambapat
ms.openlocfilehash: 4a3eaddd160acb8d4d2ae9f0da43ce6cb0236055
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "102198146"
---
# <a name="import-hsm-protected-keys-to-key-vault-byok"></a>Met HSM beveiligde sleutels importeren in Key Vault (BYOK)

Als u Azure Key Vault gebruikt, kunt u het beste sleutels in een HSM (Hardware Security Module) importeren of genereren; de sleutel zal de HSM-grens nooit overschrijden. Dit scenario wordt vaak aangeduid als *Bring Your Own Key* (BYOK). Key Vault maakt gebruik van HSM's uit de nCipher nShield-serie (gevalideerd voor FIPS 140-2 Level 2) om uw sleutels te beveiligen.

Gebruik de informatie in dit artikel bij het plannen, genereren en overbrengen van uw eigen, met HSM beveiligde sleutels voor gebruik met Azure Key Vault.

> [!NOTE]
> Deze functionaliteit is niet beschikbaar voor Azure China 21Vianet. 
> 
> Deze importmethode is alleen beschikbaar voor [ondersteunde HSM's](#supported-hsms). 

Zie [Wat is Azure Key Vault?](../general/overview.md) voor meer informatie en voor een zelfstudie om aan de slag te gaan met Key Vault (inclusief het maken van een sleutelkluis voor met HSM beveiligde sleutels).

## <a name="overview"></a>Overzicht

Hier volgt een overzicht van het proces. De specifieke uit te voeren stappen worden verderop in het artikel beschreven.

* Genereer in Key Vault een sleutel (aangeduid als een *Key Exchange Key* (KEK)). De KEK moet een RSA-HSM-sleutel zijn die alleen de sleutelbewerking `import` bevat. Alleen Key Vault Premium SKU ondersteunt RSA-HSM-sleutels.
* Download de openbare KEK-sleutel als een .pem-bestand.
* Draag de openbare KEK-sleutel over naar een offline computer die is verbonden met een on-premises HSM.
* Gebruik op de offline computer het BYOK-hulpprogramma van de HSM-leverancier om een BYOK-bestand te maken. 
* De doelsleutel wordt versleuteld met een KEK, die versleuteld blijft totdat de sleutel wordt overgebracht naar de Key Vault-HSM. Alleen de versleutelde versie van de sleutel verlaat de on-premises HSM.
* Een KEK die wordt gegenereerd in een Key Vault-HSM kan niet worden geëxporteerd. HSM's dwingen de regel af dat er geen duidelijke versie van een KEK mag bestaan buiten een Key Vault-HSM.
* De KEK moet zich in dezelfde sleutelkluis bevinden als waar de doelsleutel wordt geïmporteerd.
* Wanneer het BYOK-bestand wordt geüpload naar Key Vault, gebruikt een Key Vault-HSM de persoonlijke KEK-sleutel om het doelsleutelmateriaal te ontsleutelen en te importeren als een HSM-sleutel. Deze bewerking vindt volledig in een Key Vault-HSM plaats. De doelsleutel blijft altijd binnen de beschermingsgrens van de HSM.

## <a name="prerequisites"></a>Vereisten

De volgende tabel bevat de vereisten voor het gebruik van BYOK in Azure Key Vault:

| Vereiste | Meer informatie |
| --- | --- |
| Een Azure-abonnement |Als u een sleutelkluis in Azure Key Vault wilt maken, hebt u een Azure-abonnement nodig. [Registreer u voor een gratis proefversie](https://azure.microsoft.com/pricing/free-trial/). |
| Een Key Vault Premium-SKU voor het importeren van met HSM beveiligde sleutels |Zie [Prijzen van Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) voor meer informatie over de servicelagen en mogelijkheden van Azure Key Vault. |
| Een HSM uit de lijst met ondersteunde HSM's en een BYOK-hulpprogramma en instructies van uw HSM-leverancier | U moet over machtigingen beschikken voor een HSM en basiskennis van het gebruik van uw HSM hebben. Zie [Ondersteunde HSM's](#supported-hsms). |
| Azure CLI versie 2.1.0 of hoger | Raadpleeg [De Azure CLI installeren](/cli/azure/install-azure-cli).|

## <a name="supported-hsms"></a>Ondersteunde HSM's

|Naam van leverancier|Type leverancier|Ondersteunde HSM-modellen|Meer informatie|
|---|---|---|---|
|nCipher|Fabrikant,<br/>HSM as a service|<ul><li>nShield-serie van HSM's</li><li>nShield as a service</ul>|[Nieuw BYOK-hulpprogramma en -documentatie van nCipher](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|
|Thales|Fabrikant|<ul><li>Luna HSM 7-serie met firmwareversie 7.3 of hoger</li></ul>| [BYOK-hulpprogramma en -documentatie van Luna](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Fortanix|Fabrikant,<br/>HSM as a service|<ul><li>Self-Defending Key Management Service (SDKMS)</li><li>Equinix SmartKey</li></ul>|[SDKMS-sleutels exporteren naar cloudproviders voor BYOK - Azure Key Vault](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|
|Marvell|Fabrikant|Alle LiquidSecurity-HSM's met<ul><li>Firmwareversie 2.0.4 of hoger</li><li>Firmwareversie 3.2 of hoger</li></ul>|[BYOK-hulpprogramma en -documentatie van Marvell](https://www.marvell.com/products/security-solutions/nitrox-hs-adapters/exporting-marvell-hsm-keys-to-cloud-azure-key-vault.html)|
|Cryptomathic|ISV (Enterprise Key Management System)|Meerdere HSM-merken en -modellen, inclusief<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>Zie de [site van Cryptomathic voor meer informatie](https://www.cryptomathic.com/azurebyok)|[BYOK-hulpprogramma en -documentatie van Cryptomathic](https://www.cryptomathic.com/azurebyok)|
|Securosys SA|Fabrikant,<br/>HSM as a service|Primus HSM-serie, Securosys Clouds HSM|[BYOK-hulpprogramma en -documentatie van Primus](https://www.securosys.com/primus-azure-byok)|
|StorMagic|ISV (Enterprise Key Management System)|Meerdere HSM-merken en -modellen, inclusief<ul><li>Utimaco</li><li>Thales</li><li>nCipher</li></ul>Raadpleeg de [StorMagic-site voor informatie](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|[SvKMS en Azure Key Vault BYOK](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|
|IBM|Fabrikant|IBM 476x, CryptoExpress|[IBM Enter prise Key Management Foundation](https://www.ibm.com/security/key-management/ekmf-bring-your-own-key-azure)|
|Utimaco|Fabrikant,<br/>HSM as a service|u. Trust-anker, CryptoServer|[UTIMACO BYOK-hulp programma en integratie handleiding](https://support.hsm.utimaco.com/support/downloads/byok)|
||||


## <a name="supported-key-types"></a>Ondersteunde sleuteltypen

|Sleutelnaam|Type sleutel|Sleutel grootte/-curve|Oorsprong|Beschrijving|
|---|---|---|---|---|
|Key Exchange Key (KEK)|RSA| 2048-bits<br />3072-bits<br />4096-bits|Azure Key Vault-HSM|Een met HSM ondersteund RSA-sleutelpaar dat wordt gegenereerd in Azure Key Vault|
|Doelsleutel|
||RSA|2048-bits<br />3072-bits<br />4096-bits|HSM-leverancier|De sleutel die moet worden overgedragen naar de Azure Key Vault-HSM|
||EC|P-256<br />P-384<br />P-521|HSM-leverancier|De sleutel die moet worden overgedragen naar de Azure Key Vault-HSM|
||||

## <a name="generate-and-transfer-your-key-to-the-key-vault-hsm"></a>Uw sleutel genereren en overbrengen naar de Key Vault-HSM

Een sleutel genereren en overbrengen naar een Key Vault-HSM:

* [Stap 1: Een KEK genereren](#step-1-generate-a-kek)
* [Stap 2: De openbare KEK-sleutel downloaden](#step-2-download-the-kek-public-key)
* [Stap 3: De sleutel genereren en voorbereiden op overdracht](#step-3-generate-and-prepare-your-key-for-transfer)
* [Stap 4: De sleutel overdragen naar Azure Key Vault](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>Stap 1: Een KEK genereren

Een KEK is een RSA-sleutel die wordt gegenereerd in een Key Vault-HSM. De KEK wordt gebruikt voor het versleutelen van de sleutel die u wilt importeren (de *doelsleutel*).

De KEK moet aan het volgende voldoen:
- Een RSA-HSM-sleutel (2048-bits, 3072-bits of 4096-bits)
- Gegenereerd in de sleutelkluis waarin u de doelsleutel wilt importeren
- Gemaakt met toegestane sleutelbewerkingen ingesteld op `import`

> [!NOTE]
> De KEK moet 'import' bevatten als de enige toegestane sleutelbewerking. 'import' en alle andere sleutelbewerkingen sluiten elkaar wederzijds uit.

Gebruik de opdracht [az keyvault key create](/cli/azure/keyvault/key#az-keyvault-key-create) om een KEK te maken waarbij de sleutelbewerkingen zijn ingesteld op `import`. Noteer de sleutel-id (`kid`) die wordt geretourneerd met de volgende opdracht. (U gebruikt de waarde `kid` in [stap 3](#step-3-generate-and-prepare-your-key-for-transfer).)

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-the-kek-public-key"></a>Stap 2: De openbare KEK-sleutel downloaden

Gebruik [az keyvault key download](/cli/azure/keyvault/key#az-keyvault-key-download) om de openbare KEK-sleutel te downloaden naar een .pem-bestand. De doelsleutel die u importeert, wordt versleuteld met behulp van de openbare KEK-sleutel.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

Draag het bestand KEKforBYOK.publickey.pem over naar uw offline computer. U hebt dit bestand in de volgende stap nodig.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>Stap 3: De sleutel genereren en voorbereiden op overdracht

Raadpleeg de documentatie van uw HSM-leverancier om het BYOK-hulpprogramma te downloaden en te installeren. Volg de instructies van de HSM-leverancier om een doelsleutel te genereren en maak vervolgens een sleuteloverdrachtspakket (een BYOK-bestand). Het BYOK-hulpprogramma gebruikt de `kid` van [stap 1](#step-1-generate-a-kek) en het bestand KEKforBYOK.publickey.pem dat u in [stap 2](#step-2-download-the-kek-public-key) hebt gedownload om een versleutelde doelsleutel in een BYOK-bestand te genereren.

Draag het BYOK-bestand over naar de verbonden computer.

> [!NOTE] 
> Het importeren van 1024-bits RSA-sleutels wordt niet ondersteund. Het importeren van een elliptische curve sleutel met curve P-256 KB wordt niet ondersteund.
> 
> **Bekend probleem**: Het importeren van een RSA 4K-doelsleutel van Luna-HSM's wordt alleen ondersteund met firmware 7.4.0 of nieuwer.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>Stap 4: De sleutel overdragen naar Azure Key Vault

Om de sleutelimport te voltooien, draagt u het sleuteloverdrachtspakket (een BYOK-bestand) over van de niet-verbonden computer naar de computer met internetverbinding. Gebruik de opdracht [az keyvault key import](/cli/azure/keyvault/key#az-keyvault-key-import) om het BYOK-bestand te uploaden naar de Key Vault-HSM.

Als u een RSA-sleutel wilt importeren, gebruikt u de volgende opdracht. Para meter--KTY is optioneel en wordt standaard ingesteld op RSA-HSM.
```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Als u een EC-sleutel wilt importeren, moet u sleutel type en de naam van de curve opgeven.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file --kty EC-HSM --curve-name "P-256" KeyTransferPackage-ContosoFirstHSMkey.byok
```

Als het uploaden is gelukt, worden in Azure CLI de eigenschappen van de geïmporteerde sleutel weergegeven.

## <a name="next-steps"></a>Volgende stappen

U kunt deze met HSM beveiligde sleutel nu gebruiken in uw sleutelkluis. Zie voor meer informatie [deze vergelijking van prijzen en functies](https://azure.microsoft.com/pricing/details/key-vault/).



