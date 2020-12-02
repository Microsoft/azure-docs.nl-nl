---
title: Overzicht van Microsoft Azure Stack Edge Pro met GPU | Microsoft Docs
description: Hierin wordt Azure Stack Edge Pro met GPU beschreven, een opslagoplossing die gebruikmaakt van een fysiek apparaat voor netwerkoverdracht naar Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 09/23/2020
ms.author: alkohli
ms.openlocfilehash: 3fb4085ba275bda1fe784f368eb5d92ac0c668f0
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96448519"
---
# <a name="what-is-azure-stack-edge-pro-with-gpu"></a>Wat is Azure Stack Edge Pro met GPU?

Azure Stack Edge Pro met GPU is een Edge-rekenapparaat met AI dat mogelijkheden voor netwerkgegevensoverdracht biedt. Dit artikel geeft een overzicht van de Azure Stack Edge Pro-oplossing, voordelen, belangrijkste mogelijkheden en de scenario’s waarin u dit apparaat kunt implementeren.

Azure Stack Edge Pro met GPU is een hardware-as-a-service-oplossing. Microsoft stuurt u een via de cloud beheerd apparaat dat werkt als een netwerkopslaggateway met een ingebouwde GPU (Graphical Processing Unit) die versnelde AI-deductie mogelijk maakt. 

## <a name="use-cases"></a>Gebruiksvoorbeelden

Hier volgen de verschillende scenario's waarbij Azure Stack Edge Pro kan worden gebruikt voor snelle machine learning-deductie aan de rand en het voorbewerken van gegevens voordat deze naar Azure worden verzonden.

- **Deductie met Azure Machine Learning**: met Azure Stack Edge Pro kunt u ML-modellen uitvoeren om snel resultaten te verkrijgen op basis waarvan actie kan worden ondernomen voordat de gegevens naar de cloud worden verzonden. De volledige gegevensset kan desgewenst worden overgedragen om uw ML-modellen te blijven trainen en verbeteren. Zie [Hardware-versnelde Azure ML-modellen implementeren op Azure Stack Edge Pro](../machine-learning/how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server) voor meer informatie over het gebruiken van de met hardware versnelde Azure ML-modellen op het Azure Stack Edge Pro-apparaat.

- **Gegevens voorbewerken**: Transformeer gegevens voordat deze naar Azure worden verzonden via compute-opties zoals containerwerkbelastingen en virtuele machines om een meer op maat gemaakte gegevensset te maken. Voorverwerking kan worden gebruikt om: 

    - Gegevens samen te voegen.
    - Gegevens te wijzigen, bijvoorbeeld om persoonlijke gegevens te verwijderen.
    - Een subset van gegevens maken voor het optimaliseren van opslag en bandbreedte, of voor verdere analyse.
    - IoT-gebeurtenissen te analyseren en erop te reageren. 

- **Gegevens via een netwerk naar Azure overdragen**: Gebruik Azure Stack Edge Pro om gegevens gemakkelijk en snel naar Azure over te dragen voor verdere berekeningen en analyses of voor archivering. 

## <a name="key-capabilities"></a>Belangrijkste mogelijkheden

Azure Stack Edge Pro biedt de volgende mogelijkheden:

|Mogelijkheid |Beschrijving  |
|---------|---------|
|Versnelde AI-deductie| Mogelijk gemaakt door de ingebouwde GPU (een of twee, afhankelijk van het model).|
|Edge-computing      |Ondersteunt VM- en containerwerkbelastingen voor analyse, verwerking en filteren van gegevens. |
|Toegang tot gegevens     | Rechtstreekse gegevenstoegang vanuit Azure Storage Blobs en Azure Files met behulp van cloud-API’s voor aanvullende gegevensverwerking in de cloud. Lokale cache op het apparaat wordt gebruikt voor snelle toegang tot laatst gebruikte bestanden.|
|Beheerd via de cloud     |Het apparaat en de service worden beheerd via de Azure-portal.  |
|Offline upload     | Modus zonder verbinding ondersteunt scenario’s voor offline uploaden.|
|Ondersteunde protocollen voor bestandsoverdracht      | Ondersteuning voor de standaardprotocollen SMB, NFS en REST voor gegevensopname. <br> Ga naar [Systeemvereisten voor Azure Stack Edge Pro](azure-stack-edge-system-requirements.md) voor meer informatie over ondersteunde versies.|
|Gegevensvernieuwing     | Mogelijkheid om lokale bestanden te vernieuwen met de meest recente uit de cloud.|
|Versleuteling    | BitLocker-ondersteuning om gegevens lokaal te versleutelen en de gegevensoverdracht naar de cloud via *HTTPS* te beveiligen.|
|Bandbreedtebeperking| Beperking om het bandbreedtegebruik tijdens piekuren te verminderen.|
<!--|ExpressRoute | Extra beveiliging via ExpressRoute. Gebruik peering-configuratie waarbij verkeer van lokale apparaten naar de cloudopslageindpunten via ExpressRoute wordt geleid. Zie [Overzicht van ExpressRoute](../expressroute/expressroute-introduction.md) voor meer informatie.-->

## <a name="components"></a>Onderdelen

De Azure Stack Edge Pro-oplossing bestaat uit een Azure Stack Edge-resource, een fysiek Azure Stack Edge Pro-apparaat en een lokale webinterface.

* **Fysiek Azure Stack Edge Pro-apparaat**: een 1U rackserver die door Microsoft wordt geleverd en die kan worden geconfigureerd om gegevens naar Azure te verzenden.
    
* **Azure Stack Edge-resource**: een resource in de Azure-portal waarmee u een Azure Stack Edge Pro-apparaat kunt beheren via een webinterface waartoe u toegang hebt vanaf verschillende geografische locaties. Gebruik de Azure Stack Edge-resource om resources te maken en beheren, apparaten en waarschuwingen te bekijken en beheren, en shares te beheren.  

    Ga voor meer informatie naar [Uw Azure Stack Edge Pro-apparaat bestellen](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource).

* **Lokale webinterface van Azure Stack Edge Pro**: een lokale gebruikersinterface in de browser op uw Azure Stack Edge Pro-apparaat die voornamelijk bedoeld is voor de eerste configuratie van het apparaat. Gebruik de lokale webinterface ook om diagnoses uit te voeren, het Azure Stack Edge Pro-apparaat uit te schakelen of opnieuw op te starten, logboeken met kopieerbewerkingen te bekijken en contact op te nemen met Microsoft Ondersteuning om een serviceaanvraag in te dienen.

    Ga naar [De webgebaseerde gebruikersinterface gebruiken om uw Azure Stack Edge Pro te beheren](azure-stack-edge-manage-access-power-connectivity-mode.md) voor informatie over het gebruik van de webgebaseerde gebruikersinterface.

## <a name="region-availability"></a>Beschikbaarheid in regio’s

Het fysieke Azure Stack Edge Pro-apparaat, de Azure-resource en het doelopslagaccount waarnaar u gegevens overdraagt hoeven zich niet allemaal in dezelfde regio te bevinden.

- **Beschikbaarheid van resource**: voor deze preview-versie is de resource beschikbaar in de regio's US - oost, EU - west en Azië - zuidoost.

- **Beschikbaarheid van het apparaat** - Om een lijst in te zien van alle landen/regio's waar het Azure Stack Edge Pro-apparaat beschikbaar is, gaat u naar de tab **Azure Stack Edge Pro** voor [Azure Stack Edge Pro prijzen](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgePro) en vervolgens naar de sectie **Beschikbaarheid**.
    
- **Doelopslagaccounts**: De opslagaccounts waarin de gegevens worden opgeslagen, zijn beschikbaar in alle Azure-regio’s. De regio's waar de opslagaccounts Azure Stack Edge Pro-gegevens opslaan, moeten zich voor optimale prestaties dicht bij het apparaat bevinden. Een opslagaccount dat zich ver van het apparaat vandaan bevindt, resulteert in lange latenties en tragere prestaties.

## <a name="next-steps"></a>Volgende stappen

- De [Systeemvereisten voor Azure Stack Edge Pro](azure-stack-edge-gpu-system-requirements.md) lezen.

- De [Limieten voor Azure Stack Edge Pro](azure-stack-edge-limits.md) begrijpen.
- [Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md) in de Azure-portal implementeren.