---
title: Een Azure-Cloud service implementeren (uitgebreide ondersteuning)-Azure Portal
description: Implementeer een Azure-Cloud service (uitgebreide ondersteuning) met behulp van de Azure Portal
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 306294cc654e46dbe8af80b25cb9c709071fad20
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166323"
---
# <a name="deploy-a-azure-cloud-services-extended-support-using-the-azure-portal"></a>Een Azure Cloud Services (uitgebreide ondersteuning) implementeren met behulp van de Azure Portal
In dit artikel wordt uitgelegd hoe u de Azure Portal gebruikt voor het maken van een implementatie van een Cloud service (uitgebreide ondersteuning). 

## <a name="before-you-begin"></a>Voordat u begint

Controleer de [vereisten voor implementatie](deploy-prerequisite.md) voor Cloud Services (uitgebreide ondersteuning) en maak de bijbehorende resources. 

## <a name="deploy-a-cloud-services-extended-support"></a>Een Cloud Services implementeren (uitgebreide ondersteuning) 
1. Meld u aan bij [Azure Portal](https://portal.azure.com)

2.  Zoek en selecteer **Cloud Services (uitgebreide ondersteuning)** met behulp van de zoek balk aan de bovenkant van de Azure Portal.

    :::image type="content" source="media/deploy-portal-1.png" alt-text="Afbeelding toont de Blade alle resources in de Azure Portal.":::
 
3.  Selecteer **maken** in het deel venster Cloud Services (uitgebreide ondersteuning). 

    :::image type="content" source="media/deploy-portal-2.png" alt-text="Afbeelding toont de aanschaf van een Cloud service vanuit de Marketplace.":::

4. Het venster voor het maken van Cloud Services (uitgebreide ondersteuning) wordt geopend op het tabblad **basis beginselen** . 
    - Selecteer een abonnement.
    - Kies een resourcegroep of maak een nieuwe.
    - Voer de gewenste naam in voor de implementatie van uw Cloud service (uitgebreide ondersteuning).
        - De DNS-naam van de Cloud service is gescheiden en wordt opgegeven door het DNS-naam label van het open bare IP-adres en kan worden gewijzigd in het gedeelte openbaar IP op het tabblad Configuratie.
    -  Selecteer de regio waarin u wilt implementeren.

    :::image type="content" source="media/deploy-portal-3.png" alt-text="Afbeelding toont de Blade start pagina van Cloud Services (uitgebreide ondersteuning).":::

5. Voeg uw Cloud service configuratie, pakket-en definitie bestanden toe. U kunt bestaande bestanden uit de Blob-opslag toevoegen of deze uploaden vanaf uw lokale computer. Als u het uploadt van uw lokale computer, worden deze opgeslagen in een opslag account. 

    :::image type="content" source="media/deploy-portal-4.png" alt-text="Afbeelding toont de sectie uploaden van het tabblad basis beginselen tijdens het maken.":::

6. Wanneer alle velden zijn voltooid, gaat u naar en voltooit u het tabblad **configuratie** . 
    - Selecteer een virtueel netwerk dat u wilt koppelen aan de Cloud service of maak een nieuw. 
        - Implementaties van Cloud service (uitgebreide ondersteuning) **moeten** zich in een virtueel netwerk bestaan. In het service configuratie bestand (. cscfg) onder de sectie **moet** ook naar het virtuele netwerk worden verwezen `NetworkConfiguration` .
    - Selecteer een bestaand openbaar IP-adres om te koppelen aan de Cloud service of maak een nieuwe.
        - Als er **IP-invoer eindpunten** zijn gedefinieerd in het bestand met de service definitie (. csdef), moet er een openbaar IP-adres worden gemaakt voor uw Cloud service. 
        - Cloud Services (uitgebreide ondersteuning) ondersteunt alleen de basis-SKU voor IP-adressen.
        - Als uw service configuratie (. cscfg) een gereserveerd IP-adres bevat, moet het toewijzings type voor het open bare IP-pakket tp **static** worden ingesteld. 
        - U kunt eventueel een DNS-naam voor het eind punt van de Cloud service toewijzen door de DNS-label eigenschap van het open bare IP-adres dat is gekoppeld aan de Cloud service bij te werken.  
    - Beschrijving Start de Cloud service. Kies starten of start de service niet onmiddellijk na het maken.
    - Een Key Vault selecteren 
        - Key Vault is vereist wanneer u een of meer certificaten opgeeft in het bestand met de service configuratie (. cscfg). Wanneer u een sleutel kluis selecteert, zullen we proberen de geselecteerde certificaten te vinden in het bestand met de service configuratie (. cscfg) op basis van hun vinger afdrukken. Als er certificaten ontbreken in uw sleutel kluis, kunt u deze nu uploaden en op **vernieuwen** klikken.   

 :::image type="content" source="media/deploy-portal-5.png" alt-text="Afbeelding toont de Blade configuratie in het Azure Portal bij het maken van een Cloud Services (uitgebreide ondersteuning).":::

7. Zodra alle velden zijn voltooid, gaat u naar het tabblad **controleren en maken** om uw implementatie configuratie te valideren en maakt u uw Cloud service (uitgebreide ondersteuning).

## <a name="next-steps"></a>Volgende stappen 
- Bekijk [Veelgestelde vragen](faq.md) over Cloud Services (uitgebreide ondersteuning).
- Implementeer een Cloud service (uitgebreide ondersteuning) met behulp van de [Azure Portal](deploy-portal.md), [Power shell](deploy-powershell.md), [sjabloon](deploy-template.md) of [Visual Studio](deploy-visual-studio.md).
- Ga naar de [opslag plaats voor beelden van Cloud Services (uitgebreide ondersteuning)](https://github.com/Azure-Samples/cloud-services-extended-support)
