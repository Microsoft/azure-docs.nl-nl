---
title: Overzicht van Microsoft Azure Stack Edge Pro | Microsoft Docs
description: Hierin wordt Azure Stack Edge Pro met GPU beschreven, een opslagoplossing die gebruikmaakt van een fysiek apparaat voor netwerkoverdracht naar Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 03/15/2021
ms.author: alkohli
ms.openlocfilehash: e6cd1f8a1f7d1777e786ab91637b4065a2c5e850
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104585941"
---
# <a name="what-is-azure-stack-edge-pro-with-fpga"></a>Wat is Azure Stack Edge Pro met FPGA?

[!INCLUDE [Azure Stack Edge Pro FPGA end-of-life](../../includes/azure-stack-edge-fpga-eol.md)]

Azure Stack Edge Pro met FPGA is een Edge-rekenapparaat met AI dat mogelijkheden voor netwerkgegevensoverdracht biedt. In dit artikel vindt u een overzicht van de Azure Stack Edge Pro met FPGA-oplossing, voor delen, de belangrijkste mogelijkheden en implementatie scenario's.

Azure Stack Edge Pro met FPGA is een hardware-as-a-service-oplossing. Microsoft stuurt u een in de cloud beheerd apparaat met een ingebouwde Field Programmable Gate Array (FPGA) die versnelde AI-interferentie mogelijk maakt en alle mogelijkheden van een gateway voor netwerkopslag heeft.

Azure Data Box Edge heeft onlangs de nieuwe naam Azure Stack Edge gekregen.

## <a name="use-cases"></a>Gebruiksvoorbeelden

Hier volgen de verschillende scenario's waarbij Azure Stack Edge Pro kan worden gebruikt voor snelle machine learning-deductie aan de rand en het voorbewerken van gegevens voordat deze naar Azure worden verzonden.

- **Deductie met Azure Machine Learning**: met Azure Stack Edge Pro kunt u ML-modellen uitvoeren om snel resultaten te verkrijgen op basis waarvan actie kan worden ondernomen voordat de gegevens naar de cloud worden verzonden. De volledige gegevensset kan desgewenst worden overgedragen om uw ML-modellen te blijven trainen en verbeteren. Zie [Hardware-versnelde Azure ML-modellen implementeren op Azure Stack Edge Pro](../machine-learning/how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server) voor meer informatie over het gebruiken van de hardware-versnelde Azure ML-modellen op het Azure Stack Edge Pro-apparaat.

- **Gegevens voorbewerken** gegevens transformeren voordat ze naar Azure worden verzonden om een bruikbaardere gegevensset te creëren. Voorverwerking kan worden gebruikt om: 

    - Gegevens samen te voegen.
    - Gegevens te wijzigen, bijvoorbeeld om persoonlijke gegevens te verwijderen.
    - Een subset van gegevens maken voor het optimaliseren van opslag en bandbreedte, of voor verdere analyse.
    - IoT-gebeurtenissen te analyseren en erop te reageren. 

- **Gegevens via een netwerk naar Azure overdragen**: Gebruik Azure Stack Edge Pro om gegevens gemakkelijk en snel naar Azure over te dragen voor verdere berekeningen en analyses of voor archivering. 

## <a name="key-capabilities"></a>Belangrijkste mogelijkheden

Azure Stack Edge Pro biedt de volgende mogelijkheden:

|Mogelijkheid |Beschrijving  |
|---------|---------|
|Versnelde AI-deductie| Mogelijk gemaakt door de ingebouwde FPGA.|
|Berekenen       |Gegevens kunnen worden geanalyseerd, verwerkt of gefilterd.|
|Hoge prestaties | High-Performance Compute en gegevens overdracht.|
|Toegang tot gegevens     | Rechtstreekse gegevenstoegang vanuit Azure Storage Blobs en Azure Files met behulp van cloud-API’s voor aanvullende gegevensverwerking in de cloud. Lokale cache op het apparaat wordt gebruikt voor snelle toegang tot laatst gebruikte bestanden.|
|Beheerd via de cloud     |Het apparaat en de service worden beheerd via de Azure-portal.  |
|Offline upload     | Modus zonder verbinding ondersteunt scenario’s voor offline uploaden.|
|Ondersteunde protocollen     | Ondersteuning voor het standaard SMB- en NFS-protocol voor gegevensopname. <br> Ga naar [Systeemvereisten voor Azure Stack Edge Pro](azure-stack-edge-system-requirements.md) voor meer informatie over ondersteunde versies.|
|Gegevensvernieuwing     | Mogelijkheid om lokale bestanden te vernieuwen met de meest recente uit de cloud.|
|Versleuteling    | BitLocker-ondersteuning om gegevens lokaal te versleutelen en de gegevensoverdracht naar de cloud via *HTTPS* te beveiligen.|
|Bandbreedtebeperking| Beperking om het bandbreedtegebruik tijdens piekuren te verminderen.|
|ExpressRoute | Extra beveiliging via ExpressRoute. Gebruik peering-configuratie waarbij verkeer van lokale apparaten naar de cloudopslageindpunten via ExpressRoute wordt geleid. Raadpleeg [Overzicht van ExpressRoute](../expressroute/expressroute-introduction.md) voor meer informatie.

## <a name="components"></a>Onderdelen

De Azure Stack Edge Pro-oplossing bestaat uit een Azure Stack Edge-resource, een fysiek Azure Stack Edge Pro-apparaat en een lokale webinterface.

* **Azure stack Edge Pro-fysiek apparaat**: een 1U-gekoppelde server die door micro soft is geleverd en die kan worden geconfigureerd om gegevens naar Azure te verzenden.
    
* **Azure stack Edge-resource**: een resource in de Azure Portal waarmee u een Azure stack Edge Pro-apparaat kunt beheren vanuit een webinterface waartoe u toegang hebt vanaf verschillende geografische locaties. Gebruik de resource Azure Stack Edge om resources te maken en te beheren, shares te beheren en apparaten en waarschuwingen weer te geven en te beheren.
  
   <!--[The Azure Stack Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

   Als Azure Stack Edge Pro het einde van de levens duur nadert, worden er geen bestellingen voor nieuwe Azure Stack Edge Pro-apparaten gevuld. Als u een nieuwe klant bent, raden we u aan om te verkennen met behulp van Azure Stack Edge Pro GPU-apparaten voor uw workloads. Ga voor meer informatie naar [Wat is Azure stack Edge Pro met GPU](azure-stack-edge-gpu-overview.md). Voor informatie over het best Ellen van een Azure Stack Edge Pro met GPU-apparaat, gaat u naar [een nieuwe resource maken voor Azure stack Edge Pro-GPU](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource).

   Als u een bestaande klant bent, kunt u nog steeds een nieuwe Azure Stack Edge Pro-resource maken als u uw bestaande Azure Stack Edge Pro-apparaat moet vervangen of opnieuw wilt instellen. Ga voor instructies naar [een bestelling maken voor uw Azure stack Edge Pro-apparaat](azure-stack-edge-deploy-prep.md#create-new-resource-for-existing-device).

* **Lokale Azure Stack Edge Pro-webinterface**: Gebruik de lokale webinterface om diagnoses uit te voeren, het Azure Stack Edge Pro-apparaat uit te schakelen of opnieuw op te starten, logboeken met kopieerbewerkingen te bekijken en contact op te nemen met Microsoft Ondersteuning om een serviceaanvraag in te dienen.

    <!--![The Azure Stack Edge Pro local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    Ga naar [De webgebaseerde gebruikersinterface gebruiken om uw Azure Stack Edge Pro te beheren](azure-stack-edge-manage-access-power-connectivity-mode.md) voor informatie over het gebruik van de webgebaseerde gebruikersinterface.

## <a name="region-availability"></a>Beschikbaarheid in regio’s

Het fysieke Azure Stack Edge Pro-apparaat, de Azure-resource en het doelopslagaccount waarnaar u gegevens overdraagt hoeven zich niet allemaal in dezelfde regio te bevinden.

- **Beschikbaarheid van resources**: ga voor een lijst van alle regio's waarin de Azure Stack Edge-resource beschikbaar is naar [Azure-producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Azure Stack Edge Pro kan ook in de Azure Government Cloud worden geïmplementeerd. Zie [Wat is Azure Government?](../azure-government/documentation-government-welcome.md) voor meer informatie.
    
- **Doelopslagaccounts**: De opslagaccounts waarin de gegevens worden opgeslagen, zijn beschikbaar in alle Azure-regio’s. De regio's waar de opslagaccounts Azure Stack Edge Pro-gegevens opslaan, moeten zich voor optimale prestaties dicht bij het apparaat bevinden. Een opslagaccount dat zich ver van het apparaat vandaan bevindt, resulteert in lange latenties en tragere prestaties.

Azure Stack Edge-service is een niet-regionale service. Zie [regio's en Beschikbaarheidszones in azure](https://docs.microsoft.com/azure/availability-zones/az-overview)voor meer informatie. Azure Stack Edge-service heeft geen afhankelijkheid van een specifieke Azure-regio, waardoor deze robuust is voor de zone-ruime onderbrekingen en de regionale storingen.

## <a name="next-steps"></a>Volgende stappen

- De [Systeemvereisten voor Azure Stack Edge Pro](azure-stack-edge-system-requirements.md) lezen.
- De [Limieten voor Azure Stack Edge Pro](azure-stack-edge-limits.md) begrijpen.
- [Azure Stack Edge Pro](azure-stack-edge-deploy-prep.md) in de Azure-portal implementeren.
