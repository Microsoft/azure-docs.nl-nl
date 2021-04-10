---
title: Uw eigen sleutel specificatie meenemen-Azure Key Vault | Microsoft Docs
description: In dit document wordt beschreven hoe u uw eigen sleutel specificatie meebrengt.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: ambapat
ms.openlocfilehash: 141abea0c0946c98b6dfe627f32f01682a18be44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99581020"
---
# <a name="bring-your-own-key-specification"></a>BYOK-specificatie (Bring Your Own Key)

In dit document worden de specificaties beschreven voor het importeren van met HSM beveiligde sleutels van on-premises Hsm's van klanten in Key Vault.

## <a name="scenario"></a>Scenario

Een Key Vault klant wil een sleutel van hun on-premises HSM buiten Azure veilig overdragen naar de HSM-back-Azure Key Vault. Het proces voor het importeren van een sleutel die buiten Key Vault is gegenereerd, wordt in het algemeen aangeduid als Bring Your Own Key (BYOK).

Hier volgen de vereisten:
* De sleutel die moet worden overgedragen, bestaat nooit buiten een HSM in tekst zonder opmaak.
* Buiten een HSM wordt de sleutel die moet worden overgedragen, altijd beveiligd door een sleutel die wordt opgeslagen in de Azure Key Vault HSM

## <a name="terminology"></a>Terminologie

|Sleutel naam|Sleutel type|Oorsprong|Beschrijving|
|---|---|---|---|
|Key Exchange Key (KEK)|RSA|Azure Key Vault-HSM|Een HSM-sleutel paar met back-ups dat is gegenereerd in Azure Key Vault
Toets inpakken|AES|HSM-leverancier|Een [kortstondig] AES-sleutel gegenereerd door HSM on-premises
Doel sleutel|RSA, EC, AES (alleen beheerde HSM)|HSM-leverancier|De sleutel die moet worden overgedragen naar de Azure Key Vault-HSM

Sleutel **uitwisselings sleutel**: een door HSM ondersteunde sleutel die door de klant wordt gegenereerd in de sleutel kluis waar de BYOK-sleutel wordt geïmporteerd. Deze KEK moet de volgende eigenschappen hebben:

* Het is een RSA-HSM-sleutel (4096-bits of 3072-bits of 2048-bits)
* Het heeft een vaste key_ops (alleen importeren), zodat deze alleen tijdens BYOK kan worden gebruikt
* Moet zich in dezelfde kluis bekomen waar de doel sleutel wordt geïmporteerd

## <a name="user-steps"></a>Gebruikers stappen

Een gebruiker voert de volgende stappen uit om een sleutel overdracht uit te voeren:

1. KEK genereren.
2. De open bare sleutel van de KEK ophalen.
3. Het BYOK-hulp programma voor de leverancier van HSM gebruiken: Importeer de KEK in de doel-HSM en exporteert de doel sleutel die wordt beveiligd door de KEK.
4. Importeer de beveiligde doel sleutel naar Azure Key Vault.

Klanten gebruiken het BYOK-hulp programma en de documentatie van de HSM-leverancier om stap 3 uit te voeren. Het produceert een sleutel overdracht-BLOB (een '. byok-bestand).


## <a name="hsm-constraints"></a>HSM-beperkingen

Bestaande HSM kan beperkingen Toep assen op de sleutel die ze beheren, met inbegrip van:
* De HSM moet mogelijk worden geconfigureerd om op sleutel terugloop gebaseerde export toe te staan
* De doel sleutel moet mogelijk worden gemarkeerd CKA_EXTRACTABLE voor de HSM voor het toestaan van beheerde export
* In sommige gevallen moet de KEK-en inpakken-sleutel mogelijk als CKA_TRUSTED worden gemarkeerd. Hierdoor kan het worden gebruikt om de sleutels in de HSM op te slaan.

De configuratie van de bron-HSM is doorgaans buiten het bereik van deze specificatie. Micro soft verwacht dat de HSM-leverancier documentatie opneemt die het BYOK-hulp programma vergezelt om dergelijke configuratie stappen op te nemen.

> [!NOTE]
> De stappen 1, 2 en 4 die hieronder worden beschreven, kunnen worden uitgevoerd met andere interfaces, zoals Azure PowerShell en Azure Portal. Ze kunnen ook programmatisch worden uitgevoerd met behulp van gelijkwaardige functies in Key Vault SDK.

### <a name="step-1-generate-kek"></a>Stap 1: KEK genereren

Gebruik de opdracht **AZ sleutel kluis Key Create** om KEK te maken waarvoor sleutel bewerkingen zijn ingesteld om te worden geïmporteerd. Noteer de sleutel-id ' Kid ' die wordt geretourneerd door de onderstaande opdracht.

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-retrieve-the-public-key-of-the-kek"></a>Stap 2: de open bare sleutel van de KEK ophalen

Down load het gedeelte met de open bare sleutel van de KEK en sla het op in een PEM-bestand.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

### <a name="steps-3-generate-key-transfer-blob-using-hsm-vendor-provided-byok-tool"></a>Stap 3: sleutel overdracht genereren met behulp van HSM Vendor BYOK tool

De klant gebruikt een HSM-leverancier die het BYOK-hulp programma heeft verschaft voor het maken van een BLOB voor sleutel overdracht (opgeslagen als een '. BYOK-bestand). De open bare sleutel KEK (als een. pem-bestand) is een van de invoer voor dit hulp programma.

#### <a name="key-transfer-blob"></a>BLOB voor sleutel overdracht
Lange termijn: micro soft wil het PKCS # 11-CKM_RSA_AES_KEY_WRAP mechanisme gebruiken om de doel sleutel over te dragen naar Azure Key Vault omdat dit mechanisme een enkele BLOB produceert en belang rijker is dat de tussenliggende AES-sleutel wordt verwerkt door de twee Hsm's en is gegarandeerd dat deze kortstondig is. Dit mechanisme is momenteel niet beschikbaar in sommige Hsm's, maar de combi natie van het beveiligen van de doel sleutel met CKM_AES_KEY_WRAP_PAD met behulp van een AES-sleutel en het beveiligen van de AES-sleutel met CKM_RSA_PKCS_OAEP produceert een equivalente blob.

De Lees bare tekst van de doel sleutel is afhankelijk van het sleutel type: 
* Voor een RSA-sleutel is de persoonlijke sleutel ASN. 1 DER encoding [RFC3447] verpakt in PKCS # 8 [RFC5208] 
* Voor een EC-sleutel is de persoonlijke sleutel ASN. 1 DER encoding [RFC5915] verpakt in PKCS # 8 [RFC5208]
* Voor een octet sleutel worden de onbewerkte bytes van de sleutel

De bytes voor de sleutel voor de Lees bare tekst worden vervolgens getransformeerd met behulp van het CKM_RSA_AES_KEY_WRAP-mechanisme:
* Er wordt een tijdelijke AES-sleutel gegenereerd en versleuteld met de coderings sleutel RSA met behulp van RSA-OAEP met SHA1.
* De gecodeerde Lees bare sleutel wordt versleuteld met behulp van de AES-sleutel met behulp van AES-sleutel terugloop met opvulling.
* De versleutelde AES-sleutel en de versleutelde Lees bare sleutel worden samengevoegd om de uiteindelijke gecodeerde BLOB te maken.

De indeling van de overdracht-BLOB gebruikt RFC7516 (JSON Web Encryption compact serialisatie) voornamelijk als een voer tuig voor het leveren van de vereiste meta gegevens aan de service voor juiste ontsleuteling.

Als CKM_RSA_AES_KEY_WRAP_PAD wordt gebruikt, zou de JSON-serialisatie van de overdracht-BLOB er als volgt uitziet:

```json
{
  "schema_version": "1.0.0",
  "header":
  {
    "kid": "<key identifier of the KEK>",
    "alg": "dir",
    "enc": "CKM_RSA_AES_KEY_WRAP"
  },
  "ciphertext":"BASE64URL(<ciphertext contents>)",
  "generator": "BYOK tool name and version; source HSM name and firmware version"
}

```

* Kid = sleutel-id van KEK. Voor Key Vault sleutels ziet het er als volgt uit: https://ContosoKeyVaultHSM.vault.azure.net/keys/mykek/eba63d27e4e34e028839b53fac905621
* Alg = algoritme. 
* dir = directe modus, d.w.z. de verwijzende tekst wordt gebruikt om de gecodeerde tekst direct te beveiligen. Dit is een nauw keurige weer gave van CKM_RSA_AES_KEY_WRAP
* Generator = een informatief veld waarin de naam en de versie van het BYOK-hulp programma en de bron-HSM-fabrikant en het model worden aangeduid. Deze informatie is bedoeld voor gebruik in probleem oplossing en ondersteuning.

De JSON-BLOB wordt opgeslagen in een bestand met de extensie '. byok ' zodat de Azure PowerShell/CLI-clients correct worden verwerkt als ' add-AzKeyVaultKey ' (PSH) of ' AZ file kluis Key import ' (CLI)-opdrachten worden gebruikt.

### <a name="step-4-upload-key-transfer-blob-to-import-hsm-key"></a>Stap 4: de BLOB voor sleutel overdracht uploaden om een HSM-sleutel te importeren

De klant stuurt de BLOB voor sleutel overdracht ('. byok ') over naar een online werk station en voert vervolgens een opdracht **AZ file kluis Key import** uit om deze BLOB te importeren als een nieuwe met HSM ondersteunde sleutel in Key Vault. 

Als u een RSA-sleutel wilt importeren, gebruikt u deze opdracht:
```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok --ops encrypt decrypt
```
Als u een EC-sleutel wilt importeren, moet u sleutel type en de naam van de curve opgeven.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file --kty EC-HSM --curve-name "P-256" KeyTransferPackage-ContosoFirstHSMkey.byok --ops sign verify
```


Wanneer de bovenstaande opdracht wordt uitgevoerd, wordt een REST API aanvraag als volgt verzonden:

```
PUT https://contosokeyvaulthsm.vault.azure.net/keys/ContosoFirstHSMKey?api-version=7.0
```

Hoofd tekst van aanvraag bij het importeren van een RSA-sleutel:
```json
{
  "key": {
    "kty": "RSA-HSM",
    "key_ops": [
      "decrypt",
      "encrypt"
    ],
    "key_hsm": "<Base64 encoded BYOK_BLOB>"
  },
  "attributes": {
    "enabled": true
  }
}
```

Hoofd tekst van aanvraag bij het importeren van een EC-sleutel:
```json
{
  "key": {
    "kty": "EC-HSM",
    "crv": "P-256",
    "key_ops": [
      "sign",
      "verify"
    ],
    "key_hsm": "<Base64 encoded BYOK_BLOB>"
  },
  "attributes": {
    "enabled": true
  }
}
```

de waarde ' key_hsm ' is de volledige inhoud van de KeyTransferPackage-ContosoFirstHSMkey. byok-code ring in de Base64-indeling.

## <a name="references"></a>Referenties
- [Gids voor Key Vault-ontwikkelaars](../general/developers-guide.md)

## <a name="next-steps"></a>Volgende stappen
* Stapsgewijze instructies voor BYOK: [Importeer met HSM beveiligde sleutels naar Key Vault (BYOK)](hsm-protected-keys-byok.md)

