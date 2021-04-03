---
title: Quickstart - een in Azure beheerde HSM inrichten en activeren
description: Quickstart die u laat zien hoe u een beheerde HSM kunt inrichten en activeren met behulp van Azure CLI
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: quickstart
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 86d0a336a7d3f5d12ed8e53de802616f839f9eba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91756811"
---
# <a name="quickstart-provision-and-activate-a-managed-hsm-using-azure-cli"></a>Quickstart: Een beheerde HSM inrichten en activeren met behulp van Azure CLI

Azure Key Vault Managed HSM is een met standaarden compatibele cloudservice met hoge beschikbaarheid en een enkele tenant, die volledig beheerd is. Met deze cloudservice kunt u cryptografische sleutels voor uw cloudtoepassingen beveiligen, met behulp van via **FIPS 140-2 niveau 3** gevalideerde HSM's. U kunt het [Overzicht](overview.md) raadplegen voor meer informatie over beheerde HSM's. 

In deze quickstart maakt en activeert u een beheerde HSM met Azure CLI. Nadat u dat hebt gedaan, gaat u een geheim opslaan.

## <a name="prerequisites"></a>Vereisten

U moet over de volgende items beschikken om de stappen in dit artikel uit te kunnen voeren:

* Een abonnement op Microsoft Azure Als u nog geen abonnement hebt, kunt u zich aanmelden voor een [gratis proefabonnement](https://azure.microsoft.com/pricing/free-trial).
* Azure CLI versie 2.12.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli).
* Een beheerde HSM in uw abonnement. Zie [Quickstart: Een beheerde HSM inrichten en activeren met behulp van de Azure CLI](quick-create-cli.md) om een beheerde HSM in te richten en te activeren.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Als u zich wilt aanmelden bij Azure met behulp van de CLI, typt u:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. In het volgende voorbeeld wordt een resourcegroep gemaakt met de naam *ContosoResourceGroup* in de locatie *eastus2*.

```azurecli-interactive
az group create --name "ContosoResourceGroup" --location eastus2
```

## <a name="create-a-managed-hsm"></a>Een beheerde HSM maken

U kunt in twee stappen een beheerde HSM maken:
1. Richt een beheerde HSM-resource in.
1. Activeer uw beheerde HSM door het beveiligingsdomein te downloaden.

### <a name="provision-a-managed-hsm"></a>Een beheerde HSM inrichten

Gebruik de opdracht `az keyvault create` om een beheerde HSM te maken. Het script heeft drie verplichte parameters: een resourcegroepnaam, een HSM-naam en de geografische locatie.

U dient de volgende invoer op te geven om een beheerde HSM-resource te maken:
- De resourcegroep waar deze in uw abonnement wordt geplaatst.
- Azure-locatie.
- Een lijst met initiële beheerders.

In het volgende voorbeeld wordt een HSM met de naam **ContosoMHSM** gemaakt in resourcegroep **ContosoResourceGroup**, die zich op locatie **US - oost 2** bevindt met **de huidige aangemelde gebruiker** als enige beheerder.

```azurecli-interactive
oid=$(az ad signed-in-user show --query objectId -o tsv)
az keyvault create --hsm-name "ContosoMHSM" --resource-group "ContosoResourceGroup" --location "East US 2" --administrators $oid
```

> [!NOTE]
> Het kan enkele minuten duren voordat de maakopdracht is uitgevoerd. Zodra de opdracht is voltooid, bent u klaar om uw HSM te activeren.

In de uitvoer van deze opdracht worden de eigenschappen weergegeven van de beheerde HSM die u hebt gemaakt. De twee belangrijkste eigenschappen zijn:

* **name**: In het voorbeeld is ContosoMHSM de naam. U gebruikt deze naam voor andere Key Vault-opdrachten.
* **hsmUri**: In het voorbeeld is de URI https://contosohsm.managedhsm.azure.net. Toepassingen die via de REST API gebruikmaken van uw HSM, moeten deze URI gebruiken.

Uw Azure-account is nu gemachtigd om alle bewerkingen op deze beheerde HSM uit te voeren. Op dit moment is nog niemand anders gemachtigd.

### <a name="activate-your-managed-hsm"></a>Uw beheerde HSM activeren

Alle gegevensvlakopdrachten zijn uitgeschakeld totdat de HSM wordt geactiveerd. U kunt geen sleutels maken of rollen toewijzen. Alleen de aangewezen beheerders, die zijn toegewezen tijdens het maken van de opdracht, kunnen de HSM activeren. Als u de HSM wilt activeren, moet u het [Beveiligingsdomein](security-domain.md) downloaden.

Om uw HSM te activeren, hebt u het volgende nodig:
- Minimaal 3 RSA-sleutelparen (maximaal 10)
- Geef het minimum aantal sleutels op dat vereist is voor het ontsleutelen van het beveiligingsdomein (quorum)

Als u de HSM wilt activeren, stuurt u ten minste 3 (maximaal 10) openbare RSA-sleutels naar de HSM. De HSM versleutelt het beveiligingsdomein met deze sleutels en stuurt het terug. Als het downloaden van dit beveiligingsdomein voltooid is, is uw HSM klaar voor gebruik. U moet ook een quorum opgeven. Dit is het minimale aantal persoonlijke sleutels dat vereist is voor het ontsleutelen van het beveiligingsdomein.

In het onderstaande voorbeeld ziet u hoe u `openssl` kunt gebruiken om 3 zelfondertekende certificaten te genereren.

```azurecli-interactive
openssl req -newkey rsa:2048 -nodes -keyout cert_0.key -x509 -days 365 -out cert_0.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_1.key -x509 -days 365 -out cert_1.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_2.key -x509 -days 365 -out cert_2.cer
```

> [!IMPORTANT]
> Maak de RSA-sleutelparen en het beveiligingsdomeinbestand dat in deze stap is gegenereerd en sla ze op een veilige manier op.

Gebruik de `az keyvault security-domain download` opdracht om het beveiligingsdomein te downloaden en uw beheerde HSM te activeren. In het onderstaande voorbeeld wordt gebruikgemaakt van 3 RSA-sleutelparen (alleen openbare sleutels zijn vereist voor deze opdracht) en wordt het quorum ingesteld op 2.

```azurecli-interactive
az keyvault security-domain download --hsm-name ContosoMHSM --sd-wrapping-keys ./certs/cert_0.cer ./certs/cert_1.cer ./certs/cert_2.cer --sd-quorum 2 --security-domain-file ContosoMHSM-SD.json
```

Sla het beveiligingsdomeinbestand en de RSA-sleutelparen veilig op. U hebt deze nodig voor herstel na noodgevallen of voor het maken van een andere beheerde HSM die hetzelfde beveiligingsdomein deelt, zodat ze sleutels kunnen delen.

Nadat het beveiligingsdomein is gedownload, krijgt uw HSM de status actief en kunt u uw HSM gebruiken.

## <a name="clean-up-resources"></a>Resources opschonen

Andere snelstartgidsen en zelfstudies in deze verzameling zijn gebaseerd op deze snelstartgids. Als u van plan bent om verder te gaan met volgende snelstarts en zelfstudies, kunt u deze resources intact laten.

U kunt de opdracht [az group delete](/cli/azure/group) gebruiken om de resourcegroep en alle gerelateerde resources te verwijderen wanneer u ze niet meer nodig hebt. U kunt de resources als volgt verwijderen:

```azurecli-interactive
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een sleutelkluis gemaakt en daar een geheim in opgeslagen. Voor meer informatie over Key Vault en hoe u Key Vault integreert met uw toepassingen gaat u verder naar de artikelen hieronder.

- Bekijk een [Overzicht van beheerde HSM's](overview.md)
- Meer informatie over het [Beheren van sleutels in een beheerde HSM](key-management.md)
- Beoordeel [Best practices voor beheerde HSM's](best-practices.md)
