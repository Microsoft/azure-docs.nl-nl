---
title: Beveiligde toegang tot een beheerde HSM - Door Azure Key Vault beheerde HSM
description: Meer informatie over het beveiligen van de toegang tot beheerde HSM met behulp van Azure RBAC en lokaal op rollen gebaseerd toegangsbeheer van beheerde HSM
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 5a3fcc10f318f2a8065550a48eb2bfb4bbdd4915
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102218397"
---
# <a name="secure-access-to-your-managed-hsms"></a>Veilige toegang tot uw beheerde HSM's

Door Azure Key Vault beheerde HSM is een cloudservice die versleutelingssleutels beveiligt. Omdat deze gegevens vertrouwelijk en bedrijfskritiek zijn, is het belangrijk om de toegang tot uw beheerde HSM's te beveiligen, zodat alleen gemachtigde toepassingen en gebruikers toegang hebben tot uw beheerde HSM's. Dit artikel bevat een overzicht van het toegangsbeheermodel voor beheerde HSM's. Hierin worden verificatie en autorisatie uitgelegd en wordt beschreven hoe u de toegang tot uw beheerde HSM's kunt beveiligen.

In deze zelfstudie doorloopt u een eenvoudig voorbeeld waarin wordt uitgelegd hoe u een scheiding van taken en toegangsbeheer met Azure RBAC en lokaal op rollen gebaseerd toegangsbeheer van beheerde HSM's kunt bereiken. Zie [Toegangsbeheer van beheerde HSM](access-control.md) voor meer informatie over het toegangsbeheermodel voor beheerde HSM's.

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

Zie [Aanmelden met de Azure CLI](/cli/azure/authenticate-azure-cli) voor meer informatie over opties voor aanmelding via de CLI

## <a name="example"></a>Voorbeeld

In dit voorbeeld ontwikkelen we een toepassing waarin een 2048-bits RSA-sleutel wordt gebruikt voor tekenbewerkingen. De toepassing wordt uitgevoerd op een virtuele machine (VM) van Azure met een [beheerde identiteit](../../active-directory/managed-identities-azure-resources/overview.md). De RSA-sleutel die wordt gebruikt voor ondertekening, wordt opgeslagen in de beheerde HSM.

We hebben de volgende rollen geïdentificeerd die onze toepassing beheren, implementeren en controleren:

- **Beveiligingsteam**: IT-personeel van het kantoor van de CSO (Chief Security Officer) of soortgelijke medewerkers. Het beveiligingsteam is verantwoordelijk voor het veilig bewaren van sleutels. Dat zijn RSA- of EC-sleutels voor ondertekening en RSA- of AES-sleutels voor gegevensversleuteling.
- **Ontwikkelaars en operators**: De medewerkers die de toepassing ontwikkelen en implementeren in Azure. De leden van dit team maken geen deel uit van het beveiligingspersoneel. Ze mogen geen toegang hebben tot gevoelige gegevens, zoals RSA-sleutels. Alleen de toepassing die ze implementeren, mag toegang hebben tot deze gevoelige gegevens.
- **Auditors**: Deze rol is voor medewerkers die niet behoren tot het team van ontwikkelaars of algemene IT-medewerkers. Ze controleren het gebruik en onderhoud van certificaten, sleutels en geheimen en zorgen voor de naleving van beveiligingsstandaarden.

Er is nog een rol die zich buiten het bereik van de toepassing bevindt: de beheerder van het abonnement (of de resourcegroep). De abonnementsbeheerder stelt de initiële toegangsmachtigingen voor het beveiligingsteam in. Ze verlenen toegang tot het beveiligingsteam door gebruik te maken van een resourcegroep met de benodigde resources voor de toepassing.

We moeten de volgende bewerkingen voor onze rollen autoriseren:

**Beveiligingsteam**
- De beheerde HSM maken.
- Het beveiligingsdomein voor de beheerde HSM downloaden (voor herstel na een noodgeval)
- Logboekregistratie inschakelen.
- Sleutels genereren of importeren
- Back-ups van de beheerde HSM maken voor herstel na een noodgeval.
- Lokaal op rollen gebaseerd toegangsbeheer voor de beheerde HSM instellen om machtigingen te verlenen aan gebruikers en toepassingen voor specifieke bewerkingen.
- De sleutels periodiek roteren.

**Ontwikkelaars en operators**
- Referentie (sleutel-URI) van het beveiligingsteam ophalen voor de RSA-sleutel die wordt gebruikt voor ondertekening.
- De toepassing voor programmatische toegang tot de sleutel ontwikkelen en implementeren.

**Auditors**
- Vervaldatums voor sleutels controleren om ervoor te zorgen dat sleutels up-to-date zijn
- Roltoewijzingen controleren om ervoor te zorgen dat alleen geautoriseerde gebruikers/toepassingen toegang tot de sleutels hebben
- De logboeken van de beheerde HSM bekijken om te bevestigen dat sleutels op de juiste manier en in overeenstemming met de beveiligingsnormen voor gegevens worden gebruikt.

De volgende tabel bevat een overzicht van de roltoewijzingen aan teams en resources voor toegang tot de beheerde HSM.

| Rol | Rol Beheervlak | Rol Gegevensvlak |
| --- | --- | --- |
| Beveiligingsteam | Inzender van beheerde HSM | Beheerder van beheerde HSM |
| Ontwikkelaars en operators | Geen | Geen |
| Auditors | Geen | Cryptoauditor van beheerde HSM |
| Beheerde identiteit van de VM die door de toepassing wordt gebruikt| Geen | Cryptogebruiker van beheerde HSM |
| Beheerde identiteit van het opslagaccount dat door de toepassing wordt gebruikt| Geen| Serviceversleuteling van beheerde HSM |

De drie teamrollen hebben toegang tot andere resources, samen met beheerde HSM-machtigingen. Ontwikkelaars en operators hebben `Contributor`-toegang tot de resourcetypen nodig om VM's (of de Web Apps-functie van Azure App Service) te implementeren. Auditors hebben leestoegang nodig tot het opslagaccount waarin de logboeken van de beheerde HSM worden opgeslagen.

Als u beheervlakrollen (Azure RBAC) wilt toewijzen, kunt u Azure Portal of een van de andere beheerinterfaces gebruiken, zoals Azure CLI of Azure PowerShell. Als u beheerde HSM-gegevensvlakrollen wilt toewijzen, moet u Azure CLI gebruiken.

De Azure CLI-fragmenten in deze sectie zijn gebaseerd op de volgende veronderstellingen:

- De Azure Active Directory-beheerder heeft beveiligingsgroepen gemaakt voor de drie rollen: Contoso Security Team, Contoso App DevOps, en Contoso App Auditors. De beheerder heeft gebruikers toegevoegd aan hun respectieve groepen.
- Alle resources bevinden zich in de resourcegroep **ContosoAppRG**.
- De beheerde HSM-logboeken worden opgeslagen in het opslagaccount **contosologstorage**.
- De beheerde HSM **ContosoMHSM** en het opslagaccount **contosologstorage** bevinden zich in dezelfde Azure-locatie.

De abonnementsbeheerder wijst de rol `Managed HSM Contributor` toe aan het beveiligingsteam. Deze rol staat het beveiligingsteam toe om bestaande beheerde HSM's te beheren en nieuwe te maken. Als er bestaande beheerde HSM's zijn, moet aan de leden van het beveiligingsteam de rol Beheerder van beheerde HSM worden toegewezen zodat ze de HSM's kunnen beheren.

```azurecli-interactive
# This role assignment allows Contoso Security Team to create new Managed HSMs
az role assignment create --assignee-object-id $(az ad group show -g 'Contoso Security Team' --query 'objectId' -o tsv) --assignee-principal-type Group --role "Managed HSM Contributor"

# This role assignment allows Contoso Security Team to become administrator of existing managed HSM
az keyvault role assignment create  --hsm-name ContosoMHSM --assignee $(az ad group show -g 'Contoso Security Team' --query 'objectId' -o tsv) --scope / --role "Managed HSM Administrator"
```

Het beveiligingsteam stelt logboekregistratie in en wijst rollen toe aan auditors en de VM-toepassing.

```azurecli-interactive
# Enable logging
hsmresource=$(az keyvault show --hsm-name ContosoMHSM --query id -o tsv)
storageresource=$(az storage account show --name contosologstorage --query id -o tsv)
az monitor diagnostic-settings create --name MHSM-Diagnostics --resource $hsmresource --logs    '[{"category": "AuditEvent","enabled": true}]' --storage-account $storageresource

# Assign the "Crypto Auditor" role to Contoso App Auditors group. It only allows them to read.
az keyvault role assignment create  --hsm-name ContosoMHSM --assignee $(az ad group show -g 'Contoso App Auditors' --query 'objectId' -o tsv) --scope / --role "Managed HSM Crypto Auditor"

# Grant the "Crypto User" role to the VM's managed identity. It allows to create and use keys. 
# However it cannot permanently delete (purge) keys
az keyvault role assignment create  --hsm-name ContosoMHSM --assignee $(az vm identity show --name "vmname" --resource-group "ContosoAppRG" --query objectId -o tsv) --scope / --role "Managed HSM Crypto Auditor"

# Assign "Managed HSM Crypto Service Encryption" role to the Storage account ID
storage_account_principal=$(az storage account show --id $storageresource --query identity.principalId -o tsv)
# (if no identity exists), then assign a new one
[ "$storage_account_principal" ] || storage_account_principal=$(az storage account update --assign-identity --id $storageresource)

az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Service Encryption" --assignee $storage_account_principal
```

In deze zelfstudie worden voornamelijk acties weergegeven die relevant zijn voor het toegangsbeheer. Andere acties en bewerkingen met betrekking tot het implementeren van een toepassing in uw VM, het inschakelen van versleuteling met een door de klant beheerde sleutel voor een opslagaccount en het maken van een beheerde HSM, worden hier niet weergegeven omdat dit voorbeeld is toegespitst op toegangsbeheer en rolbeheer.

In ons voorbeeld wordt een eenvoudig scenario beschreven. In de praktijk kunnen scenario's complexer zijn. U kunt machtigingen voor uw sleutelkluis aanpassen op basis van uw behoeften. We gaan ervan uit dat het beveiligingsteam de sleutel- en geheimreferenties (URI's en vingerafdrukken) aanlevert die worden gebruikt door het DevOps-personeel in hun toepassingen. Ontwikkelaars en operators hebben geen toegang tot een gegevensvlak nodig. We richten ons op de beveiliging van uw sleutelkluis. Het is ook belangrijk om [uw VM's](https://azure.microsoft.com/services/virtual-machines/security/), [opslagaccounts](../../storage/blobs/security-recommendations.md) en andere Azure-resources te beveiligen.

## <a name="resources"></a>Resources

- [Documentatie voor Azure RBAC](../../role-based-access-control/overview.md)
- [Azure RBAC: Ingebouwde rollen](../../role-based-access-control/built-in-roles.md)
- [Azure RBAC beheren met Azure CLI](../../role-based-access-control/role-assignments-cli.md)

## <a name="next-steps"></a>Volgende stappen

Zie [Wat is beheerde HSM?](overview.md) voor een inleidende zelfstudie voor beheerders.

Zie [Logboekregistratie van beheerde HSM](logging.md) voor meer informatie over logboekregistratie van het gebruik van beheerde HSM's.
