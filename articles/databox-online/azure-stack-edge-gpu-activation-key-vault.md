---
title: Integratie van Azure Key Vault met Azure Stack rand resource en apparaat activeren
description: Hierin wordt beschreven hoe Azure Key Vault is gekoppeld aan het geheime beheer tijdens de activering van Azure Stack Edge Pro-apparaat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: c18f875dde43ab6df512375152d76a31c30a9e77
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102439864"
---
# <a name="azure-key-vault-integration-with-azure-stack-edge"></a>Integratie met Azure Stack Edge Azure Key Vault 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Key Vault is geïntegreerd met Azure Stack Edge-resource voor geheim beheer. In dit artikel vindt u informatie over de manier waarop een Azure Key Vault voor Azure Stack Edge-resource wordt gemaakt tijdens het activeren van apparaten en vervolgens wordt gebruikt voor het beheer van geheime gegevens. 


## <a name="about-key-vault-and-azure-stack-edge"></a>Over sleutel kluis en Azure Stack Edge

Azure Key Vault Cloud service wordt gebruikt om de toegang tot tokens, wacht woorden, certificaten, API-sleutels en andere geheimen veilig op te slaan en te beheren. Key Vault maakt het eenvoudig om de versleutelings sleutels te maken en te beheren die worden gebruikt voor het versleutelen van uw gegevens. Zie de [prijzen voor Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/)voor meer informatie over toegestane trans acties en bijbehorende kosten.

Voor Azure Stack Edge-service is een van de gebruikte geheimen de Channel Integrity Key (CIK). Met deze sleutel kunt u uw geheimen versleutelen. Met de integratie van de sleutel kluis wordt de CIK veilig opgeslagen in de sleutel kluis. Zie Secure [Store Secrets and keys](../key-vault/general/overview.md#securely-store-secrets-and-keys)(Engelstalig) voor meer informatie.


## <a name="key-vault-creation"></a>Sleutel kluis maken

Er wordt een sleutel kluis gemaakt voor Azure Stack Edge-resource tijdens het genereren van de activerings sleutel. 

- Wanneer u Azure Stack Edge-resource maakt, moet u de resource provider *micro soft.-kluis* registreren. De resource provider wordt automatisch geregistreerd als u eigenaar of Inzender toegang hebt tot het abonnement. De sleutel kluis wordt gemaakt in hetzelfde abonnement en de resource groep als de Azure Stack Edge-resource. 

- Wanneer u een Azure Stack Edge-resource maakt, wordt er ook een Managed Service Identity (MSI) gemaakt die persistent is voor de levens duur van de resource en communiceert met de resource provider in de Cloud. 

    Wanneer het MSI-bestand is ingeschakeld, maakt Azure een vertrouwde identiteit voor de Azure Stack Edge-resource.

- Nadat u de Azure Stack-rand resource hebt gemaakt en u een activerings sleutel van de Azure Portal genereert, wordt een sleutel kluis gemaakt. Deze sleutel kluis wordt gebruikt voor geheim beheer en blijft behouden zolang de Azure Stack Edge-resource bestaat. 

    ![Key Vault gemaakt tijdens genereren van activerings sleutel](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-3.png)

- U kunt ervoor kiezen de standaard sleutel naam te accepteren of een aangepaste naam op te geven voor de sleutel kluis. De naam van de sleutel kluis moet 3 tot 24 tekens lang zijn. U kunt geen sleutel kluis gebruiken die al in gebruik is. <!--The MSI is then used to authenticate to key vault to retrieve secrets.--> 

    ![MSI gemaakt tijdens het maken van Azure Stack rand bronnen](media/azure-stack-edge-gpu-deploy-prep/create-resource-8.png)

- Als u naar de Azure-sleutel kluis wilt bladeren, gaat u naar de **Eigenschappen** in uw Azure stack Edge-resource en selecteert u de naam van de sleutel kluis. 

- Een resource vergrendeling is ingeschakeld op de sleutel kluis om onbedoelde verwijdering te voor komen. Er is ook een voorlopig verwijderen ingeschakeld op de sleutel kluis waarmee de sleutel kluis binnen 90 dagen kan worden hersteld als er een ongeluk is verwijderd. Zie [Azure Key Vault overzicht van voorlopig verwijderen](../key-vault/general/soft-delete-overview.md) voor meer informatie

    Als de sleutel kluis per ongeluk wordt verwijderd en de duur voor het leegmaken van de beveiliging van 90 dagen niet is verstreken, voert u de volgende stappen uit om [de sleutel kluis te herstellen](../key-vault/general/key-vault-recovery.md#list-recover-or-purge-soft-deleted-secrets-keys-and-certificates). 

- Als u een bestaande Azure Stack Edge-resource had voordat de Azure Key Vault werd geïntegreerd met Azure Stack-Edge-resource, heeft dit geen invloed op dit probleem. U kunt uw bestaande Azure Stack Edge-resource blijven gebruiken. 

- Wanneer uw Azure Stack Edge-resource wordt verwijderd, wordt de Azure Key Vault ook verwijderd met de resource. U wordt gevraagd om bevestiging. Als u deze sleutel kluis niet wilt verwijderen, kunt u ervoor kiezen geen toestemming te geven. Alleen de Azure Stack Edge-resource wordt verwijderd, waardoor de sleutel kluis intact blijft. 

- Als deze sleutel kluis is gebruikt om andere sleutels op te slaan, kunt u deze nog binnen 90 dagen na de verwijdering herstellen. Tijdens deze opschoon beveiligings periode kan de naam van de sleutel kluis niet worden gebruikt voor het maken van een nieuwe sleutel kluis.

Zie problemen [met het activeren van apparaten oplossen](azure-stack-edge-gpu-troubleshoot-activation.md)als u problemen ondervindt met de sleutel kluis en de activering van apparaten.

<!--## Key vault secret management

When you generate an activation key, the following events occur:

1. You request an activation key in the Azure portal. The request is then sent to Key Vault resource provider. 
1. A standard tier key vault with access policy is created and is locked by default. This key vault uses the default name or the custom name that you specified.
1. The key vault authenticates with MSI the request to generate activation key. The MSI is also added to the key vault access policy and a channel integrity key is generated and placed in the key vault.
1. The activation key is returned to the Azure portal. You can then copy this key and use it in the local UI to activate your device.-->



## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het genereren van een [activerings sleutel](azure-stack-edge-gpu-deploy-prep.md#get-the-activation-key).