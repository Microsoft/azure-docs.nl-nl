---
title: Verbinding maken met Azure Analysis Services met Power BI | Microsoft Docs
description: Leer hoe u verbinding maakt met een Azure Analysis Services server met behulp van Power BI. Zodra er verbinding is gemaakt, kunnen gebruikers modelgegevens verkennen.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 4/20/2021
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c5a430c5bb24032a2665ad078311dcb5137d2bb9
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816025"
---
# <a name="connect-with-power-bi"></a>Verbinden met Power BI

Zodra u een server in Azure hebt gemaakt en er een tabellaire model op hebt geïmplementeerd, zijn gebruikers in uw organisatie klaar om verbinding te maken en gegevens te verkennen. 

> [!NOTE]
> Als u een Power BI Desktop model publiceert naar de Power BI-service, controleert u op de Azure Analysis Services-server of de eigenschap Case-Sensitive-collatieserver niet is geselecteerd (standaard). De Case-Sensitive server kan worden ingesteld met behulp van SQL Server Management Studio.
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Verbinden maken in Power BI Desktop

1. Klik in Power BI Desktop op **Gegevens ophalen** > **Azure** > **Azure Analysis Services-database**.

2. Voer **in Server** de servernaam in. Zorg ervoor dat u de volledige URL op te nemen; Bijvoorbeeld: asazure://westcentralus.asazure.windows.net/advworks.

3. Als **u in Database** de naam weet van de database of het perspectief van het tabellaire model waar u verbinding mee wilt maken, plakt u deze hier. Anders kunt u dit veld leeg laten en later een database of perspectief selecteren.

4. Selecteer een verbindingsoptie en druk vervolgens op **Verbinden.** 

    De **opties Live verbinden** en **Importeren** worden ondersteund. We raden u echter aan om liveverbindingen te gebruiken, omdat de importmodus enkele beperkingen heeft; Met name kunnen de serverprestaties worden beïnvloed tijdens het importeren. Als het model moet worden vernieuwd in de Power BI-service,  is de instelling Toegang vanuit Power BI alleen van toepassing wanneer u **Live verbinden kiest.**

5. Voer uw aanmeldingsreferenties in als u hier om wordt gevraagd. 

   > [!NOTE]
   > OTP-accounts (One-Time Passcode) worden niet ondersteund. 

6. Vouw **in Navigator** de server uit, selecteer het model of perspectief dat u wilt verbinden en klik vervolgens op **Verbinden.** Klik op een model of perspectief om alle objecten voor die weergave weer te geven.

    Het model wordt geopend in Power BI Desktop met een leeg rapport in de rapportweergave. De lijst Velden bevat alle niet-verborgen modelobjecten. De verbindingsstatus wordt rechtsonder weergegeven.

## <a name="connect-in-power-bi-service"></a>Verbinding maken in Power BI (service)

1. Maak een Power BI Desktop met een liveverbinding met uw model op uw server.
2. Klik [Power BI](https://powerbi.microsoft.com)op **Gegevensbestanden downloaden** en zoek en selecteer vervolgens uw  >  PBIX-bestand.

## <a name="see-also"></a>Zie ook
[Verbinding maken met Azure Analysis Services](analysis-services-connect.md)   
[Clientbibliotheken](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true)
