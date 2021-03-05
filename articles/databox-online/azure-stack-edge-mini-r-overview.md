---
title: Overzicht van Microsoft Azure Stack Edge Mini R | Microsoft Docs
description: Hierin wordt Microsoft Azure Stack Edge Mini R beschreven, een opslagoplossing voor militaire toepassingen die gebruikmaakt van een draagbaar fysiek apparaat met een batterij voor netwerkoverdracht naar Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 03/03/2021
ms.author: alkohli
ms.openlocfilehash: 7c28eb604de4f6e23f37b6ca2fcab3fdc976f237
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102125198"
---
# <a name="what-is-the-azure-stack-edge-mini-r"></a>Wat is de Azure Stack Edge Mini R?

Azure Stack Edge Mini R is een uiterst draagbaar, robuust apparaat voor Edge-rekenprocessen dat is ontworpen voor gebruik in gevaarlijke omgevingen. Azure Stack Edge Mini R wordt geleverd als een hardware-as-a-service-oplossing. Microsoft stuurt u een in de cloud beheerd apparaat dat werkt als een netwerkopslaggateway met een ingebouwde VPU (Vision Processing Unit) die versnelde AI-deductie mogelijk maakt.

Dit artikel biedt een overzicht van de Azure Stack Edge Mini R-oplossing, belangrijkste mogelijkheden en de scenario’s waarin u dit apparaat kunt implementeren.


## <a name="key-capabilities"></a>Belangrijkste mogelijkheden

Azure Stack Edge Mini R biedt de volgende mogelijkheden:

|Mogelijkheid |Beschrijving  |
|---------|---------|
|Robuuste hardware| Robuuste hardware, ontworpen voor gevaarlijke omgevingen.|
|Ultra-draagbaar| Ultra-draagbare, accu-aangedreven vormfactor.|
|Beheerd via de cloud|Het apparaat en de service worden beheerd via de Azure-portal.|
|Workloads voor Edge-computing|Gegevens kunnen worden geanalyseerd, verwerkt of gefilterd.<br>Ondersteunt VM's en workloads in containers. |
|Versnelde AI-deductie| Mogelijk gemaakt door de Intel Movidius Myriad X VPU.|
|Bekabeld en draadloos | Staat bekabelde en draadloze gegevensoverdrachten toe.|
|Toegang tot gegevens     | Rechtstreekse gegevenstoegang vanuit Azure Storage Blobs en Azure Files met behulp van cloud-API’s voor aanvullende gegevensverwerking in de cloud. Lokale cache op het apparaat wordt gebruikt voor snelle toegang tot laatst gebruikte bestanden.|
|Niet-verbonden modus|  Het apparaat en de service kunnen desgewenst worden beheerd via Azure Stack Hub. Toepassingen implementeren, uitvoeren en beheren in de offline modus. <br> Modus zonder verbinding ondersteunt scenario’s voor offline uploaden.|
|Ondersteunde protocollen voor bestandsoverdracht      |Ondersteuning voor de standaardprotocollen SMB, NFS en REST voor gegevensopname. <br> Ga naar [Systeemvereisten voor Azure Stack Edge Mini R](azure-stack-edge-gpu-system-requirements.md) voor meer informatie over ondersteunde versies.|
|Gegevensvernieuwing     | Mogelijkheid om lokale bestanden te vernieuwen met de meest recente uit de cloud.|
|Dubbele versleuteling    | Het gebruik van zichzelf versleutelende schijven biedt de eerste versleutelingslaag. VPN biedt de tweede versleutelingslaag. BitLocker-ondersteuning om gegevens lokaal te versleutelen en de gegevensoverdracht naar de cloud via *https* te beveiligen.|
|Bandbreedtebeperking| Beperking om het bandbreedtegebruik tijdens piekuren te verminderen.|

## <a name="use-cases"></a>Gebruiksvoorbeelden

Hier volgen de verschillende scenario's waarbij Azure Stack Edge Mini R kan worden gebruikt voor snelle machine learning-deductie aan de rand en het voorbewerken van gegevens voordat deze naar Azure worden verzonden.

- **Deductie met Azure Machine Learning**: met Azure Stack Edge Mini R kunt u ML-modellen uitvoeren om snel resultaten te verkrijgen op basis waarvan actie kan worden ondernomen voordat de gegevens naar de cloud worden verzonden. De volledige gegevensset kan desgewenst worden overgedragen om uw ML-modellen te blijven trainen en verbeteren. Zie [Hardware-versnelde Azure ML-modellen implementeren op Azure Stack Edge Mini R](../machine-learning/how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server) voor meer informatie over het gebruiken van de met hardware versnelde Azure ML-modellen op het Azure Stack Edge Mini R-apparaat.

- **Gegevens voorbewerken**: transformeer gegevens voordat deze naar Azure worden verzonden via compute-opties zoals containerwerkbelastingen en virtuele machines om een meer op maat gemaakte gegevensset te maken. Voorverwerking kan worden gebruikt om:

    - Gegevens samen te voegen.
    - Gegevens te wijzigen, bijvoorbeeld om persoonlijke gegevens te verwijderen.
    - Een subset van gegevens maken voor het optimaliseren van opslag en bandbreedte, of voor verdere analyse.
    - IoT-gebeurtenissen te analyseren en erop te reageren.

- **Gegevens via een netwerk naar Azure overdragen**: Gebruik Azure Stack Edge Mini R om gegevens gemakkelijk en snel naar Azure over te dragen voor verdere berekeningen en analyses of voor archivering.

## <a name="components"></a>Onderdelen

De Azure Stack Edge Mini R-oplossing bestaat uit een Azure Stack Edge-resource, een ultra draagbaar robuust Azure Stack Edge Mini R-fysiek apparaat en een lokale webinterface.

* **Fysiek Azure Stack Edge Mini R-apparaat**: een uiterst draagbaar, robuust apparaat voor rekenprocessen en opslag, geleverd door Microsoft. Het apparaat heeft een ingebouwde batterij en weegt minder dan 7 lbs.

    ![Azure Stack Edge Mini R-apparaat](media/azure-stack-edge-mini-r-overview/perspective-view-1.png)

* **Azure Stack Edge-resource**: een resource in de Azure Portal waarmee u een robuust Azure Stack Edge Mini R-apparaat kunt beheren via een webinterface waartoe u toegang hebt vanaf verschillende geografische locaties. Gebruik de Azure Stack Edge-resource om resources te maken en beheren, apparaten en waarschuwingen te bekijken en beheren, en shares te beheren.  

* **Lokale Azure Stack Edge Pro R-webinterface**: een lokale gebruikersinterface in de browser op uw Azure Stack Edge Mini R-apparaat die voornamelijk bedoeld is voor de eerste configuratie van het apparaat. Gebruik de lokale webinterface ook om diagnoses uit te voeren, het Azure Stack Edge Pro-apparaat uit te schakelen of opnieuw op te starten, logboeken met kopieerbewerkingen te bekijken en contact op te nemen met Microsoft Ondersteuning om een serviceaanvraag in te dienen.


## <a name="region-availability"></a>Beschikbaarheid in regio’s

Het fysieke Azure Stack Edge Mini R-apparaat, de Azure-resource en het doelopslagaccount waarnaar u gegevens overdraagt hoeven zich niet allemaal in dezelfde regio te bevinden.

- **Beschikbaarheid van resources**: ga voor een lijst van alle regio's waarin de Azure Stack Edge-resource beschikbaar is naar [Azure-producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). 

- **Beschikbaarheid van het apparaat**: om een lijst in te zien van alle landen/regio's waar het Azure Stack Edge Mini R-apparaat beschikbaar is, gaat u naar de sectie Beschikbaarheid op het tabblad Azure Stack Edge Mini R voor [Azure Stack Edge Mini R-prijzen](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgeMiniR).

- **Doelopslagaccounts**: De opslagaccounts waarin de gegevens worden opgeslagen, zijn beschikbaar in alle Azure-regio’s. De regio's waar de opslagaccounts Azure Stack Edge Mini R-gegevens opslaan, moeten zich voor optimale prestaties dicht bij het apparaat bevinden. Een opslagaccount dat zich ver van het apparaat vandaan bevindt, resulteert in lange latenties en tragere prestaties.


## <a name="next-steps"></a>Volgende stappen

- De [Systeemvereisten voor Azure Stack Edge Mini R ](azure-stack-edge-gpu-system-requirements.md) lezen.