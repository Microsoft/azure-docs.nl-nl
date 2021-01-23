---
title: Certificaten opslaan en gebruiken in Azure Cloud Services (uitgebreide ondersteuning)
description: Processen voor het opslaan en gebruiken van certificaten in azure Cloud Services (uitgebreide ondersteuning)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 9e69b4e9279f9147c2ee13d42a42aec0c5a15d96
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744475"
---
# <a name="use-certificates-with-azure-cloud-services-extended-support"></a>Certificaten gebruiken met Azure Cloud Services (uitgebreide ondersteuning)

Key Vault wordt gebruikt om certificaten op te slaan die zijn gekoppeld aan Cloud Services (uitgebreide ondersteuning). Sleutel kluizen kunnen worden gemaakt via [Azure Portal](https://docs.microsoft.com/azure/key-vault/general/quick-create-portal) en [Power shell](https://docs.microsoft.com/azure/key-vault/general/quick-create-powershell). Voeg de certificaten toe aan Key Vault en verwijs vervolgens naar de certificaat vingerafdrukken in het service configuratie bestand. U moet ook Key Vault inschakelen voor de juiste machtigingen, zodat Cloud Services (uitgebreide ondersteunings resource) het certificaat kan ophalen dat is opgeslagen als geheimen van Key Vault.  

## <a name="upload-a-certificate-to-key-vault"></a>Een certificaat uploaden naar Key Vault 

1.  Meld u aan bij de Azure Portal en ga naar de Key Vault. Als u geen Key Vault hebt ingesteld, kunt u ervoor kiezen om er een te maken in hetzelfde venster.

2. **Toegangs beleid** selecteren

    :::image type="content" source="media/certs-and-key-vault-1.png" alt-text="Afbeelding toont het selecteren van toegangs beleid op de Blade sleutel kluis.":::

3. Zorg ervoor dat het toegangs beleid de volgende eigenschappen bevat:
    - **Toegang tot Azure Virtual Machines inschakelen voor implementatie**
    - **Toegang tot Azure Resource Manager voor sjabloon implementatie inschakelen** 

    :::image type="content" source="media/certs-and-key-vault-2.png" alt-text="Afbeelding toont het venster toegangs beleid in de Azure Portal.":::
 
4.  **Certificaten** selecteren 

    :::image type="content" source="media/certs-and-key-vault-3.png" alt-text="Afbeelding toont het selecteren van de optie certificaten in het venster sleutel kluis Blade beleid in de Azure Portal.":::

5. Selecteer **genereren/importeren**

    :::image type="content" source="media/certs-and-key-vault-4.png" alt-text="Afbeelding toont het selecteren van de optie genereren/importeren":::

4.  Vul de vereiste gegevens in om het uploaden van het certificaat te volt ooien. 

    :::image type="content" source="media/certs-and-key-vault-5.png" alt-text="Afbeelding toont het import venster in het Azure Portal.":::

5.  Voeg de certificaat gegevens toe aan uw rol in het service configuratie bestand (. cscfg). Zorg ervoor dat de vinger afdruk van het certificaat in de Azure Portal overeenkomt met de vinger afdruk in het bestand met de service configuratie (. cscfg). 
    
    ```json
    <Certificate name="<your cert name>" thumbprint="<thumbprint in key vault" thumbprintAlgorithm="sha1" /> 
    ```

## <a name="next-steps"></a>Volgende stappen 
- Controleer de [vereisten voor implementatie](deploy-prerequisite.md) voor Cloud Services (uitgebreide ondersteuning).
- Bekijk [Veelgestelde vragen](faq.md) over Cloud Services (uitgebreide ondersteuning).
- Implementeer een Cloud service (uitgebreide ondersteuning) met behulp van de [Azure Portal](deploy-portal.md), [Power shell](deploy-powershell.md), [sjabloon](deploy-template.md) of [Visual Studio](deploy-visual-studio.md).