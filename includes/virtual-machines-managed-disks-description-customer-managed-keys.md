---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/02/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 52b9bee1d43c0f136889a6a54277d4bb45dd4a45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101750613"
---
U kunt ervoor kiezen om versleuteling te beheren op het niveau van elke beheerde schijf, met uw eigen sleutels. Versleuteling aan de server zijde voor Managed disks met door de klant beheerde sleutels biedt een geïntegreerde ervaring met Azure Key Vault. U kunt [uw RSA-sleutels](../articles/key-vault/keys/hsm-protected-keys.md) importeren naar uw Key Vault of nieuwe RSA-sleutels in azure Key Vault genereren. 

Met Azure Managed disks wordt de versleuteling en ontsleuteling op een volledig transparante manier verwerkt met behulp van [envelop versleuteling](../articles/storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique). Hiermee worden gegevens versleuteld met behulp van een [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256-gegevens versleutelings sleutel (dek), die op zijn beurt wordt beveiligd met uw sleutels. De opslag service genereert gegevens versleutelings sleutels en versleutelt deze met door de klant beheerde sleutels met behulp van RSA-versleuteling. Met de envelop versleuteling kunt u uw sleutels regel matig draaien (wijzigen) volgens uw nalevings beleid zonder dat dit van invloed is op uw Vm's. Wanneer u uw sleutels roteert, versleutelt de opslag service de versleutelings sleutels opnieuw met de nieuwe door de klant beheerde sleutels. 

#### <a name="full-control-of-your-keys"></a>Volledig beheer van uw sleutels

U moet toegang tot beheerde schijven in uw Key Vault verlenen om uw sleutels te gebruiken voor het versleutelen en ontsleutelen van de DEK. Op die manier kunt u uw gegevens en sleutels volledig beheren. U kunt uw sleutels uitschakelen of de toegang tot beheerde schijven op elk gewenst moment intrekken. U kunt ook het gebruik van de versleutelings sleutel met Azure Key Vault bewaking controleren om ervoor te zorgen dat alleen beheerde schijven of andere vertrouwde Azure-Services toegang krijgen tot uw sleutels.

Voor Premium Ssd's, Standard Ssd's en Standard Hdd's: wanneer u uw sleutel uitschakelt of verwijdert, worden alle Vm's met schijven die gebruikmaken van die sleutel automatisch afgesloten. Daarna kan de virtuele machines niet worden gebruikt, tenzij de sleutel opnieuw wordt ingeschakeld of u een nieuwe sleutel toewijst.    

Voor Ultra schijven: wanneer u een sleutel uitschakelt of verwijdert, worden alle Vm's met ultra schijven die gebruikmaken van de sleutel, niet automatisch afgesloten. Zodra u de toewijzing van de Vm's ongedaan maakt en opnieuw start, worden de schijven niet meer gebruikt en worden de Vm's niet weer online gezet. Als u de virtuele machines weer online wilt zetten, moet u een nieuwe sleutel toewijzen of de bestaande sleutel inschakelen.    

In het volgende diagram ziet u hoe beheerde schijven gebruikmaken van Azure Active Directory en Azure Key Vault om aanvragen te maken met de door de klant beheerde sleutel:

:::image type="content" source="media/virtual-machines-managed-disks-description-customer-managed-keys/customer-managed-keys-sse-managed-disks-workflow.png" alt-text="Werk stroom voor beheerde schijven en door de klant beheerde sleutels. Een beheerder maakt een Azure Key Vault, maakt vervolgens een set schijf versleuteling en stelt de schijf versleuteling in. De set is gekoppeld aan een virtuele machine, waardoor de schijf gebruik kan maken van Azure AD voor verificatie":::

In de volgende lijst wordt het diagram gedetailleerd beschreven:

1. Een Azure Key Vault beheerder maakt essentiële bronnen voor de kluis.
1. Met de sleutel kluis beheerder worden de RSA-sleutels geïmporteerd in Key Vault of nieuwe RSA-sleutels in Key Vault gegenereerd.
1. Die beheerder maakt een exemplaar van de bron voor de schijf versleutelings en geeft een Azure Key Vault-id en een sleutel-URL op. Schijf Encryption set is een nieuwe bron die is geïntroduceerd voor het vereenvoudigen van het sleutel beheer voor beheerde schijven. 
1. Wanneer een schijf versleutelings set wordt gemaakt, wordt een door het [systeem toegewezen beheerde identiteit](../articles/active-directory/managed-identities-azure-resources/overview.md) gemaakt in azure Active Directory (AD) en gekoppeld aan de schijf versleuteling. 
1. De beheerder van de Azure-sleutel kluis verleent vervolgens de beheerde identiteits machtiging voor het uitvoeren van bewerkingen in de sleutel kluis.
1. Een VM-gebruiker maakt schijven door deze te koppelen aan de schijf versleutelings. De VM-gebruiker kan ook versleuteling aan de server zijde inschakelen met door de klant beheerde sleutels voor bestaande resources door ze te koppelen aan de schijf versleutelings. 
1. Beheerde schijven gebruiken de beheerde identiteit voor het verzenden van aanvragen naar de Azure Key Vault.
1. Voor het lezen of schrijven van gegevens, worden met beheerde schijven aanvragen verzonden naar Azure Key Vault om de gegevens versleutelings sleutel te versleutelen en ontsleutelen (uitpakken), zodat de gegevens kunnen worden versleuteld en ontsleuteld. 

Zie [Azure Key Vault Power shell](/powershell/module/azurerm.keyvault/) en [Azure Key Vault cli](/cli/azure/keyvault)voor meer informatie over het intrekken van de toegang tot door de klant beheerde sleutels. Als u toegang intrekt, wordt de toegang tot alle gegevens in het opslag account effectief geblokkeerd, omdat de versleutelings sleutel niet toegankelijk is voor Azure Storage.

#### <a name="automatic-key-rotation-of-customer-managed-keys-preview"></a>Automatische rotatie van de sleutel van door de klant beheerde sleutels (preview-versie)

U kunt ervoor kiezen om de automatische rotatie van de sleutel in te scha kelen naar de nieuwste sleutel versie. Een schijf verwijst naar een sleutel via de bijbehorende schijf versleuteling. Wanneer u automatisch draaien inschakelt voor een schijf versleutelings, worden alle beheerde schijven, moment opnamen en installatie kopieën die verwijzen naar de schijf versleuteling, automatisch bijgewerkt om de nieuwe versie van de sleutel binnen één uur te gebruiken. De functie is momenteel beschikbaar in beperkte regio's in de preview-versie. Zie de sectie [ondersteunde regio's](#supported-regions) voor regionale Beschik baarheid.