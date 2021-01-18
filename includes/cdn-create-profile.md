---
title: bestand opnemen
description: bestand opnemen
services: cdn
author: SyntaxC4
ms.service: azure-cdn
ms.topic: include
ms.date: 04/30/2020
ms.author: cfowler
ms.custom: include file
ms.openlocfilehash: c6352ee9d29e4e45aa4be449046a0715fee06047
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/13/2021
ms.locfileid: "98165906"
---
## <a name="create-a-new-cdn-profile"></a>Nieuwe CDN-profielen maken

Een CDN-profiel is een container voor CDN-eindpunten waarmee een prijscategorie wordt opgegeven.

1. Selecteer in de Azure Portal **Een resource maken** (linksboven). Het deelvenster **Nieuw** verschijnt.
   
1. Zoek en selecteer **CDN** en selecteer vervolgens **Maken**:
   
    ![CDN-resource selecteren](./media/cdn-create-profile/cdn-new-resource.png)

    Het deelvenster **CDN-profiel** verschijnt.

1. Voer de volgende waarden in:
   
    | Instelling  | Waarde |
    | -------- | ----- |
    | **Naam** | Voer *cdn-profile-123* in als profielnaam. Deze naam moet uniek zijn. Als deze al in gebruik is, moet u een andere naam invoeren. |
    | **Abonnement** | Kies een Azure-abonnement in de vervolgkeuzelijst. |
    | **Resourcegroep** | Selecteer **Nieuwe maken** en voer *CDNQuickstart-RG* in als naam van de resourcegroep of selecteer **Bestaande gebruiken** en kies *CDNQuickstart-RG* als u al een groep heeft. | 
    | **Resourcegroeplocatie** | Selecteer een locatie bij u in de buurt in de vervolgkeuzelijst. |
    | **Prijscategorie** | Selecteer een **Standaard Akamai**-optie in de vervolgkeuzelijst. (De implementatie van de Akamai-laag duurt ongeveer een minuut. Die voor de Microsoft-laag neemt ongeveer tien minuten in beslag en die voor de Verizon-lagen ongeveer dertig minuten.) |
    | **Nu een nieuw CDN-eindpunt maken** | Laat het selectievakje uitgeschakeld. |  
   
    ![Nieuw CDN-profiel](./media/cdn-create-profile/cdn-new-profile.png)

1. Selecteer **Maken** om het profiel te maken.

