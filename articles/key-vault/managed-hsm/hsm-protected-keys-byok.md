---
title: Met HSM beveiligde sleutels genereren en overdragen voor Azure Key Vault beheerde HSM-Azure Key Vault | Microsoft Docs
description: Gebruik dit artikel om u te helpen bij het plannen, genereren en overdragen van uw eigen met HSM beveiligde sleutels voor gebruik met beheerde HSM. Ook wel bekend als Bring Your Own Key (BYOK).
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: ambapat
ms.openlocfilehash: dd5b38a858ceba12f5d48f1782da5b85228c4b06
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102212107"
---
# <a name="import-hsm-protected-keys-to-managed-hsm-byok"></a>Met HSM beveiligde sleutels importeren in beheerde HSM (BYOK)

 Azure Key Vault beheerde HSM ondersteunt het importeren van sleutels die zijn gegenereerd in uw on-premises Hardware Security module (HSM). de sleutels verlaten nooit de grens van de HSM-beveiliging. Dit scenario wordt vaak aangeduid als *Bring Your Own Key* (BYOK). Beheerde HSM gebruikt de Marvell LiquidSecurity HSM-adapters (FIPS 140-2 level 3 gevalideerd) om uw sleutels te beveiligen.

Gebruik de informatie in dit artikel om u te helpen bij het plannen, genereren en overdragen van uw eigen met HSM beveiligde sleutels voor gebruik met beheerde HSM.

> [!NOTE]
> Deze functionaliteit is niet beschikbaar voor Azure China 21Vianet. Deze importmethode is alleen beschikbaar voor [ondersteunde HSM's](#supported-hsms). 

Zie [Wat is beheerde HSM?](overview.md)voor meer informatie en voor een zelf studie om aan de slag te gaan met beheerde HSM.

## <a name="overview"></a>Overzicht

Hier volgt een overzicht van het proces. De specifieke uit te voeren stappen worden verderop in het artikel beschreven.

* Genereer in beheerde HSM een sleutel (aangeduid als een *sleutel uitwisselings sleutel* (KEK)). De KEK moet een RSA-HSM-sleutel zijn die alleen de sleutelbewerking `import` bevat. 
* Download de openbare KEK-sleutel als een .pem-bestand.
* Draag de openbare KEK-sleutel over naar een offline computer die is verbonden met een on-premises HSM.
* Gebruik op de offline computer het BYOK-hulpprogramma van de HSM-leverancier om een BYOK-bestand te maken. 
* De doel sleutel is versleuteld met een KEK, die versleuteld blijft totdat deze wordt overgedragen naar de beheerde HSM. Alleen de versleutelde versie van de sleutel verlaat de on-premises HSM.
* Een KEK die wordt gegenereerd binnen een beheerde HSM kan niet worden geëxporteerd. Hsm's dwingt de regel af dat er geen duidelijke versie van een KEK bestaat buiten een beheerde HSM.
* De KEK moet zich in dezelfde beheerde HSM bekomen waar de doel sleutel wordt geïmporteerd.
* Wanneer het BYOK-bestand wordt geüpload naar een beheerde HSM, gebruikt een beheerde HSM de persoonlijke sleutel KEK om het doel sleutel materiaal te ontsleutelen en te importeren als een HSM-sleutel. Deze bewerking vindt volledig plaats in de HSM. De doelsleutel blijft altijd binnen de beschermingsgrens van de HSM.


## <a name="prerequisites"></a>Vereisten

Als u de Azure CLI-opdrachten in dit artikel wilt gebruiken, hebt u het volgende nodig:

* Een abonnement op Microsoft Azure Als u nog geen abonnement hebt, kunt u zich aanmelden voor een [gratis proefabonnement](https://azure.microsoft.com/pricing/free-trial).
* Azure CLI versie 2.12.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli).
* Een beheerde HSM de [lijst met ondersteunde hsm's](#supported-hsms) in uw abonnement. Zie [Quickstart: Een beheerde HSM inrichten en activeren met behulp van de Azure CLI](quick-create-cli.md) om een beheerde HSM in te richten en te activeren.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u zich wilt aanmelden bij Azure met behulp van de CLI, typt u:

```azurecli
az login
```

Zie [Aanmelden met Azure CLI](/cli/azure/authenticate-azure-cli) voor meer informatie over opties voor aanmelding via de CLI

## <a name="supported-hsms"></a>Ondersteunde HSM's

|Naam van leverancier|Type leverancier|Ondersteunde HSM-modellen|Meer informatie|
|---|---|---|---|
|nCipher|Fabrikant,<br/>HSM as a service|<ul><li>nShield-serie van HSM's</li><li>nShield as a service</ul>|[Nieuw BYOK-hulpprogramma en -documentatie van nCipher](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|
|Thales|Fabrikant|<ul><li>Luna HSM 7-serie met firmwareversie 7.3 of hoger</li></ul>| [BYOK-hulpprogramma en -documentatie van Luna](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Fortanix|Fabrikant,<br/>HSM as a service|<ul><li>Self-Defending Key Management Service (SDKMS)</li><li>Equinix SmartKey</li></ul>|[SDKMS-sleutels exporteren naar cloudproviders voor BYOK - Azure Key Vault](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|
|Marvell|Fabrikant|Alle LiquidSecurity-HSM's met<ul><li>Firmwareversie 2.0.4 of hoger</li><li>Firmwareversie 3.2 of hoger</li></ul>|[BYOK-hulpprogramma en -documentatie van Marvell](https://www.marvell.com/products/security-solutions/nitrox-hs-adapters/exporting-marvell-hsm-keys-to-cloud-azure-key-vault.html)|
|Cryptomathic|ISV (Enterprise Key Management System)|Meerdere HSM-merken en -modellen, inclusief<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>Zie de [site van Cryptomathic voor meer informatie](https://www.cryptomathic.com/azurebyok)|[BYOK-hulpprogramma en -documentatie van Cryptomathic](https://www.cryptomathic.com/azurebyok)|
|Securosys SA|Fabrikant, HSM als een service|Primus HSM-serie, Securosys Clouds HSM|[BYOK-hulpprogramma en -documentatie van Primus](https://www.securosys.com/primus-azure-byok)|
|StorMagic|ISV (Enterprise Key Management System)|Meerdere HSM-merken en -modellen, inclusief<ul><li>Utimaco</li><li>Thales</li><li>nCipher</li></ul>Raadpleeg de [StorMagic-site voor informatie](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|[SvKMS en Azure Key Vault BYOK](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|
|IBM|Fabrikant|IBM 476x, CryptoExpress|[IBM Enter prise Key Management Foundation](https://www.ibm.com/security/key-management/ekmf-bring-your-own-key-azure)|
|Utimaco|Fabrikant,<br/>HSM as a service|u. Trust-anker, CryptoServer|[UTIMACO BYOK-hulp programma en integratie handleiding](https://support.hsm.utimaco.com/support/downloads/byok)|
||||


## <a name="supported-key-types"></a>Ondersteunde sleuteltypen

|Sleutelnaam|Type sleutel|Sleutel grootte/-curve|Oorsprong|Beschrijving|
|---|---|---|---|---|
|Key Exchange Key (KEK)|RSA| 2048-bits<br />3072-bits<br />4096-bits|Beheerde HSM|Een met HSM ondersteund RSA-sleutel paar dat is gegenereerd in een beheerde HSM|
|Doelsleutel|
||RSA|2048-bits<br />3072-bits<br />4096-bits|HSM-leverancier|De sleutel die moet worden overgedragen naar de beheerde HSM|
||EC|P-256<br />P-384<br />P-521|HSM-leverancier|De sleutel die moet worden overgedragen naar de beheerde HSM|
||Symmetrische sleutel (OCT-HSM)|128-bits<br />192-bits<br />256-bits|HSM-leverancier|De sleutel die moet worden overgedragen naar de beheerde HSM|
||||
## <a name="generate-and-transfer-your-key-to-the-managed-hsm"></a>Uw sleutel genereren en overdragen naar de beheerde HSM

Uw sleutel genereren en overdragen naar een beheerde HSM:

* [Stap 1: Een KEK genereren](#step-1-generate-a-kek)
* [Stap 2: De openbare KEK-sleutel downloaden](#step-2-download-the-kek-public-key)
* [Stap 3: De sleutel genereren en voorbereiden op overdracht](#step-3-generate-and-prepare-your-key-for-transfer)
* [Stap 4: uw sleutel overdragen naar een beheerde HSM](#step-4-transfer-your-key-to-managed-hsm)

### <a name="step-1-generate-a-kek"></a>Stap 1: Een KEK genereren

Een KEK is een RSA-sleutel die wordt gegenereerd in een beheerde HSM. De KEK wordt gebruikt voor het versleutelen van de sleutel die u wilt importeren (de *doelsleutel*).

De KEK moet aan het volgende voldoen:
- Een RSA-HSM-sleutel (2048-bits, 3072-bits of 4096-bits)
- Gegenereerd in dezelfde beheerde HSM waar u de doel sleutel wilt importeren
- Gemaakt met toegestane sleutelbewerkingen ingesteld op `import`

> [!NOTE]
> De KEK moet 'import' bevatten als de enige toegestane sleutelbewerking. 'import' en alle andere sleutelbewerkingen sluiten elkaar wederzijds uit.

Gebruik de opdracht [az keyvault key create](/cli/azure/keyvault/key#az-keyvault-key-create) om een KEK te maken waarbij de sleutelbewerkingen zijn ingesteld op `import`. Noteer de sleutel-id (`kid`) die wordt geretourneerd met de volgende opdracht. (U gebruikt de waarde `kid` in [stap 3](#step-3-generate-and-prepare-your-key-for-transfer).)

```azurecli-interactive
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --hsm-name ContosoKeyVaultHSM
```
---


### <a name="step-2-download-the-kek-public-key"></a>Stap 2: De openbare KEK-sleutel downloaden

Gebruik [az keyvault key download](/cli/azure/keyvault/key#az-keyvault-key-download) om de openbare KEK-sleutel te downloaden naar een .pem-bestand. De doelsleutel die u importeert, wordt versleuteld met behulp van de openbare KEK-sleutel.

```azurecli-interactive
az keyvault key download --name KEKforBYOK --hsm-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```
---

Draag het bestand KEKforBYOK.publickey.pem over naar uw offline computer. U hebt dit bestand in de volgende stap nodig.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>Stap 3: De sleutel genereren en voorbereiden op overdracht

Raadpleeg de documentatie van uw HSM-leverancier om het BYOK-hulpprogramma te downloaden en te installeren. Volg de instructies van de HSM-leverancier om een doelsleutel te genereren en maak vervolgens een sleuteloverdrachtspakket (een BYOK-bestand). Het BYOK-hulpprogramma gebruikt de `kid` van [stap 1](#step-1-generate-a-kek) en het bestand KEKforBYOK.publickey.pem dat u in [stap 2](#step-2-download-the-kek-public-key) hebt gedownload om een versleutelde doelsleutel in een BYOK-bestand te genereren.

Draag het BYOK-bestand over naar de verbonden computer.

> [!NOTE] 
> Het importeren van 1024-bits RSA-sleutels wordt niet ondersteund. Het importeren van een EC-sleutel (Elliptic Curve) wordt momenteel niet ondersteund.
>
> **Bekend probleem**: Het importeren van een RSA 4K-doelsleutel van Luna-HSM's wordt alleen ondersteund met firmware 7.4.0 of nieuwer.

### <a name="step-4-transfer-your-key-to-managed-hsm"></a>Stap 4: uw sleutel overdragen naar een beheerde HSM

Om de sleutelimport te voltooien, draagt u het sleuteloverdrachtspakket (een BYOK-bestand) over van de niet-verbonden computer naar de computer met internetverbinding. Gebruik de opdracht [AZ file kluis Key import](/cli/azure/keyvault/key#az-keyvault-key-import) om het BYOK-bestand te uploaden naar de beheerde HSM.

```azurecli-interactive
az keyvault key import --hsm-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Als het uploaden is gelukt, worden in Azure CLI de eigenschappen van de geïmporteerde sleutel weergegeven.

## <a name="next-steps"></a>Volgende stappen

U kunt deze met HSM beschermde sleutel nu gebruiken in uw beheerde HSM. Zie voor meer informatie [deze vergelijking van prijzen en functies](https://azure.microsoft.com/pricing/details/key-vault/).



