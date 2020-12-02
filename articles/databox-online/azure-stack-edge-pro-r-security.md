---
title: Azure Stack Edge Pro R-beveiliging | Microsoft Docs
description: Hierin worden de functies voor beveiliging en privacy beschreven die uw Azure Stack Edge Pro R-en Azure Stack Edge mini-R-apparaten,-service en-gegevens on-premises en in de Cloud beveiligen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: f7d81d14ca561e6d4d897994088b2fc01b2c7701
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466850"
---
# <a name="security-and-data-protection-for-azure-stack-edge-pro-r-and-azure-stack-edge-mini-r"></a>Beveiliging en gegevens bescherming voor Azure Stack Edge Pro R en Azure Stack Edge mini-R

[!INCLUDE [applies-to-r-skus](../../includes/azure-stack-edge-applies-to-r-sku.md)]

Beveiliging is een belang rijk probleem bij het aannemen van een nieuwe technologie, met name als de technologie wordt gebruikt met vertrouwelijke of bedrijfs eigen gegevens. Azure Stack Edge Pro R en Azure Stack Edge mini-R helpen u ervoor te zorgen dat alleen geautoriseerde entiteiten uw gegevens kunnen bekijken, wijzigen of verwijderen.

In dit artikel worden de beveiligings functies van de Azure Stack Edge Pro R en Azure Stack Edge beschreven, waarmee u elk van de oplossings onderdelen en de gegevens die erin zijn opgeslagen, kunt beveiligen.

De oplossing bestaat uit vier hoofd onderdelen die met elkaar communiceren:

- **Azure stack Edge-service, gehost in azure Public of Azure Government Cloud**. De beheer resource die u gebruikt om de volg orde van apparaten te maken, het apparaat te configureren en de volg orde bij te werken.
- **Azure stack Edge-robuust apparaat**. Het robuuste, fysieke apparaat dat naar u wordt verzonden, zodat u uw on-premises gegevens kunt importeren in azure Public of Azure Government Cloud. Het apparaat kan worden Azure Stack Edge Pro R of Azure Stack Edge mini-R.
- **Clients/hosts die zijn verbonden met het apparaat**. De clients in uw infra structuur die verbinding maken met het apparaat en gegevens bevatten die moeten worden beveiligd.
- **Cloud opslag**. De locatie in het Azure-Cloud platform waar gegevens worden opgeslagen. Deze locatie is doorgaans het opslag account dat is gekoppeld aan de Azure Stack Edge-resource die u maakt.

## <a name="service-protection"></a>Service beveiliging

De service Azure Stack Edge is een beheer service die wordt gehost in Azure. De service wordt gebruikt om het apparaat te configureren en te beheren.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-service-protection.md)]

## <a name="device-protection"></a>Apparaatbeveiliging

Het robuuste apparaat is een on-premises apparaat waarmee u uw gegevens kunt transformeren door deze lokaal te verwerken en deze vervolgens naar Azure te verzenden. Uw apparaat:

- Er is een activerings sleutel nodig om toegang te krijgen tot de Azure Stack Edge-service.
- Is te allen tijde beveiligd door een apparaatwachtwoord.
- Is een vergrendeld apparaat. Het apparaat-Base Board management controller (BMC) en BIOS zijn beveiligd met een wacht woord. De BMC wordt beveiligd door beperkte gebruikers toegang.
- Is beveiligd opstarten ingeschakeld, zodat het apparaat alleen wordt opgestart met behulp van de vertrouwde software van micro soft.
- Voert Windows Defender Application Control (WDAC) uit. Met WDAC kunt u alleen vertrouwde toepassingen uitvoeren die u in uw code-integriteits beleid definieert.
- Heeft een Trusted Platform Module (TPM) waarmee op hardware gebaseerde, beveiligings functies worden uitgevoerd. In het bijzonder beheert en beschermt de TPM geheimen en gegevens die op het apparaat moeten worden bewaard.
- Alleen de vereiste poorten worden geopend op het apparaat en alle andere poorten worden geblokkeerd. Zie de lijst met [poort vereisten voor het apparaat](azure-stack-edge-pro-r-system-requirements.md) voor meer informatie.
- Alle toegang tot de hardware van het apparaat en de software wordt geregistreerd. 
    - Voor de apparaatsoftware worden standaard firewall logboeken verzameld voor binnenkomend en uitgaand verkeer van het apparaat. Deze logboeken zijn gebundeld in het ondersteunings pakket.
    - Voor de hardware van het apparaat worden alle chassis gebeurtenissen, zoals het openen en sluiten van het chassis, geregistreerd op het apparaat.

    Ga naar [Geavanceerde beveiligings logboeken](azure-stack-edge-gpu-troubleshoot.md)voor meer informatie over de specifieke logboeken die de gebeurtenissen over de hardware en software hebben ontvangen en over het ophalen van de logboeken.


### <a name="protect-the-device-via-activation-key"></a>Het apparaat beveiligen via de activerings sleutel

Alleen een geautoriseerd Azure Stack Edge Pro R-of Azure Stack Edge mini-R-apparaat mag lid worden van de Azure Stack Edge-service die u in uw Azure-abonnement hebt gemaakt. Als u een apparaat wilt autoriseren, moet u een activerings sleutel gebruiken om het apparaat te activeren met de Azure Stack Edge-service.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-activation-key.md)]

Zie [een activerings sleutel ophalen](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key)voor meer informatie.

### <a name="protect-the-device-via-password"></a>Het apparaat beveiligen via wacht woord

Wacht woorden zorgen ervoor dat alleen geautoriseerde gebruikers toegang hebben tot uw gegevens. Azure Stack Edge Pro R-apparaten opstarten in een vergrendelde status.

U kunt:

- Verbinding maken met de lokale web-UI van het apparaat via een browser en vervolgens een wacht woord opgeven om zich aan te melden bij het apparaat.
- Extern verbinding maken met de apparaat-Power shell-interface via HTTP. Extern beheer is standaard ingeschakeld. Extern beheer is ook geconfigureerd voor het gebruik van net genoeg beheer (JEA) om te beperken wat gebruikers kunnen doen. Vervolgens kunt u het wacht woord van het apparaat opgeven om u aan te melden bij het apparaat. Zie [extern verbinding maken met uw apparaat](azure-stack-edge-gpu-connect-powershell-interface.md)voor meer informatie.
- De lokale Edge-gebruiker op het apparaat heeft beperkte toegang tot het apparaat voor de eerste configuratie en het oplossen van problemen. De reken werkbelastingen die worden uitgevoerd op het apparaat, gegevens overdracht en de opslag, kunnen allemaal worden geopend vanuit de open bare of overheids portal van Azure voor de resource in de Cloud.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-password-best-practices.md)]
- Gebruik de lokale web-UI om [het wacht woord te wijzigen](azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#change-device-password). Als u het wacht woord wijzigt, moet u alle RAS-gebruikers hiervan op de hoogte stellen, zodat er geen problemen zijn bij het aanmelden.

### <a name="establish-trust-with-the-device-via-certificates"></a>Een vertrouwens relatie met het apparaat tot stand brengen via certificaten

Met Azure Stack Edge robuuste apparaat kunt u uw eigen certificaten meenemen en installeren die voor alle open bare eind punten worden gebruikt. Ga naar [uw certificaat uploaden](azure-stack-edge-j-series-manage-certificates.md#upload-certificates)voor meer informatie. Voor een lijst met alle certificaten die op uw apparaat kunnen worden geïnstalleerd, gaat u naar [certificaten op uw apparaat beheren](azure-stack-edge-j-series-manage-certificates.md).

- Wanneer u Compute op uw apparaat configureert, worden er een IoT-apparaat en een IoT Edge apparaat gemaakt. Aan deze apparaten worden automatisch symmetrische toegangs sleutels toegewezen. Als beveiligings best practice worden deze sleutels regel matig gedraaid via de IoT Hub-service.

## <a name="protect-your-data"></a>Uw gegevens beveiligen

In deze sectie worden de beveiligings functies beschreven die in-transit en opgeslagen gegevens beveiligen.

### <a name="protect-data-at-rest"></a>Data-at-rest beschermen

Alle gegevens op het apparaat zijn versleuteld, de toegang tot gegevens wordt bepaald en wanneer het apparaat wordt gedeactiveerd, worden de gegevens veilig gewist van de gegevens schijven.

#### <a name="double-encryption-of-data"></a>Dubbele versleuteling van gegevens

Gegevens op uw schijven worden beveiligd met twee versleutelings lagen:

- De eerste laag van versleuteling is de BitLocker XTS-AES 256-bits versleuteling op de gegevens volumes.
- Tweede laag is de vaste schijven met een ingebouwde versleuteling.
- Het volume van het besturings systeem heeft BitLocker als de enkele laag versleuteling.

> [!NOTE]
> De besturingssysteem schijf heeft een BitLocker-XTS-AES-256-software-versleuteling van één laag.

Wanneer het apparaat wordt geactiveerd, wordt u gevraagd een sleutel bestand op te slaan dat herstel sleutels bevat waarmee de gegevens op het apparaat kunnen worden hersteld als het apparaat niet wordt opgestart. Er zijn twee sleutels in het bestand:

- Met één sleutel herstelt u de apparaatconfiguratie op de besturingssysteem volumes.
<!-- - Second key is to unlock the BitLocker on the data disks. -->
- Met de tweede sleutel wordt de hardware-versleuteling in de gegevens schijven ontgrendeld.

> [!IMPORTANT]
> Sla het sleutel bestand op een beveiligde locatie op buiten het apparaat zelf. Als het apparaat niet wordt opgestart en u de sleutel niet hebt, kan dit leiden tot gegevens verlies.

- Bij bepaalde herstel scenario's wordt u gevraagd naar het sleutel bestand dat u hebt opgeslagen. 
<!--- If a node isn't booting up, you will need to perform a node replacement. You will have the option to swap the data disks from the failed node to the new node. For a 4-node device, you won't need a key file. For a 1-node device, you will be prompted to provide a key file.-->

#### <a name="restricted-access-to-data"></a>Beperkte toegang tot gegevens

Toegang tot gegevens die zijn opgeslagen in shares en opslag accounts is beperkt.

- SMB-clients die toegang hebben tot share gegevens hebben gebruikers referenties nodig die aan de share zijn gekoppeld. Deze referenties worden gedefinieerd wanneer de share wordt gemaakt.
- NFS-clients die toegang hebben tot een share moeten hun IP-adres expliciet toevoegen wanneer de share wordt gemaakt.
- De Edge-opslag accounts die op het apparaat zijn gemaakt, zijn lokaal en worden beveiligd door de versleuteling op de gegevens schijven. De Azure-opslag accounts waaraan deze Edge-opslag accounts zijn toegewezen, worden beveiligd door een abonnement en een 2 512-bits opslag toegangs sleutel die is gekoppeld aan het Edge Storage-account (deze sleutels zijn anders dan die welke zijn gekoppeld aan uw Azure Storage-accounts). Zie [gegevens beveiligen in opslag accounts](#protect-data-in-storage-accounts)voor meer informatie.
- BitLocker XTS-AES 256-bits versleuteling wordt gebruikt voor het beveiligen van lokale gegevens.

#### <a name="secure-data-erasure"></a>Gegevens verwijdering beveiligen

Wanneer het apparaat een harde reset ondergaat, wordt er een veilig wissen uitgevoerd op het apparaat. Veilig wissen voert het verwijderen van gegevens uit op de schijven met behulp van het NIST SP 800-88r1.

### <a name="protect-data-in-flight"></a>Gegevens in Flight beveiligen

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-data-flight.md)]

### <a name="protect-data-in-storage-accounts"></a>Gegevens in opslag accounts beveiligen

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-protect-data-storage-accounts.md)]

- Roteer en [Synchroniseer vervolgens uw opslag account sleutels](azure-stack-edge-j-series-manage-storage-accounts.md) regel matig om uw opslag account te beschermen tegen onbevoegde gebruikers.

## <a name="manage-personal-information"></a>Persoonlijke gegevens beheren

De service Azure Stack Edge verzamelt persoonlijke gegevens in de volgende scenario's:

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-manage-personal-data.md)]

Volg de stappen in [shares beheren op de Azure stack Edge](azure-stack-edge-j-series-manage-shares.md)om de lijst met gebruikers weer te geven die een share kunnen openen of verwijderen.

Raadpleeg het privacybeleid van micro soft in het [vertrouwens centrum](https://www.microsoft.com/trustcenter)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

[Uw Azure Stack Edge Pro R-apparaat implementeren](azure-stack-edge-gpu-deploy-prep.md)
