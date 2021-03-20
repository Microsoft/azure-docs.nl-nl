---
title: Bewaar geheimen in een sleutel kluis in Azure DevTest Labs | Microsoft Docs
description: Meer informatie over het opslaan van geheimen in een Azure Key Vault en het gebruik ervan tijdens het maken van een VM, formule of omgeving.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5714279ef183cb930d643575466dae3d6cb69bba
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85481643"
---
# <a name="store-secrets-in-a-key-vault-in-azure-devtest-labs"></a>Bewaar geheimen in een sleutel kluis in Azure DevTest Labs
Mogelijk moet u een complex geheim invoeren wanneer u Azure DevTest Labs: wacht woord voor uw Windows-VM, een open bare SSH-sleutel voor uw Linux-VM of uw persoonlijke toegangs token gebruikt om uw Git-opslag plaats via een artefact te klonen. Geheimen zijn meestal lang en bevatten wille keurige tekens. Het invoeren van deze kan daarom lastig en onhandig zijn, met name als u hetzelfde geheim meerdere keren gebruikt.

DevTest Labs biedt ondersteuning voor het opslaan van geheimen in een [Azure-sleutel kluis](../key-vault/general/overview.md)om dit probleem op te lossen en uw geheimen ook op een veilige plek te bewaren. Wanneer een gebruiker een geheim opslaat voor de eerste keer, maakt de DevTest Labs-service automatisch een sleutel kluis in dezelfde resource groep die het Lab bevat en slaat het geheim op in de sleutel kluis. DevTest Labs maakt voor elke gebruiker een afzonderlijke sleutel kluis. 

De test gebruiker moet eerst een virtuele machine voor het lab maken voordat ze een geheim kunnen maken in de sleutel kluis. Dit is omdat de DevTest-Lab-service de test gebruiker moet koppelen aan een geldig gebruikers document voordat ze geheimen mogen maken en opslaan in de sleutel kluis. 


## <a name="save-a-secret-in-azure-key-vault"></a>Een geheim opslaan in Azure Key Vault
Voer de volgende stappen uit om uw geheim op te slaan in Azure Key Vault:

1. Selecteer **mijn geheimen** in het menu links.
2. Voer een **naam** in voor het geheim. U ziet deze naam in de vervolg keuzelijst bij het maken van een VM, formule of omgeving. 
3. Geef het geheim op als **waarde**.

    ![Archief geheim](media/devtest-lab-store-secrets-in-key-vault/store-secret.png)

## <a name="use-a-secret-from-azure-key-vault"></a>Een geheim gebruiken van Azure Key Vault
Wanneer u een geheim moet invoeren om een VM, formule of omgeving te maken, kunt u een geheim hand matig invoeren of een opgeslagen geheim selecteren in de sleutel kluis. Als u een geheim wilt gebruiken dat is opgeslagen in uw sleutel kluis, voert u de volgende acties uit:

1. Selecteer **een opgeslagen geheim gebruiken**. 
2. Selecteer uw geheim in de vervolg keuzelijst voor **een geheim kiezen**. 

    ![Geheim gebruiken in VM](media/devtest-lab-store-secrets-in-key-vault/secret-store-pick-a-secret.png)

## <a name="use-a-secret-in-an-azure-resource-manager-template"></a>Een geheim in een Azure Resource Manager sjabloon gebruiken
U kunt uw geheime naam opgeven in een Azure Resource Manager sjabloon die wordt gebruikt voor het maken van een virtuele machine, zoals wordt weer gegeven in het volgende voor beeld:

![Geheim gebruiken in formule of omgeving](media/devtest-lab-store-secrets-in-key-vault/secret-store-arm-template.png)

## <a name="next-steps"></a>Volgende stappen

- [Een virtuele machine maken met behulp van het geheim](devtest-lab-add-vm.md) 
- [Een formule maken met behulp van het geheim](devtest-lab-manage-formulas.md)
- [Een omgeving maken met behulp van het geheim](devtest-lab-create-environment-from-arm.md)
