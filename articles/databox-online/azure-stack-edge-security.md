---
title: Azure Stack Edge Pro-beveiliging | Microsoft Docs
description: Hierin worden de functies voor beveiliging en privacy beschreven waarmee uw Azure Stack Edge Pro-apparaat,-service en-gegevens on-premises en in de cloud worden beschermd.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: b5c5143c7bc92487aeabc3cdc1afea5061466027
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90903165"
---
# <a name="azure-stack-edge-pro-security-and-data-protection"></a>Azure Stack Edge Pro-beveiliging en gegevens bescherming

Beveiliging is een belang rijk probleem bij het aannemen van een nieuwe technologie, met name als de technologie wordt gebruikt met vertrouwelijke of bedrijfs eigen gegevens. Azure Stack Edge Pro helpt u ervoor te zorgen dat alleen geautoriseerde entiteiten uw gegevens kunnen bekijken, wijzigen of verwijderen.

In dit artikel worden de Azure Stack Edge Pro-beveiligings functies beschreven waarmee u elk van de oplossings onderdelen en de gegevens die erin zijn opgeslagen kunt beveiligen.

Azure Stack Edge Pro bestaat uit vier hoofd onderdelen die met elkaar communiceren:

- **Azure stack Edge-service, gehost in azure**. De beheer resource die u gebruikt om de volg orde van apparaten te maken, het apparaat te configureren en de volg orde bij te werken.
- **Azure stack Edge Pro-apparaat**. Het overdrachts apparaat dat naar u wordt verzonden, zodat u uw on-premises gegevens kunt importeren in Azure.
- **Clients/hosts die zijn verbonden met het apparaat**. De clients in uw infra structuur die verbinding maken met het Azure Stack Edge Pro-apparaat en gegevens bevatten die moeten worden beveiligd.
- **Cloud opslag**. De locatie in het Azure-Cloud platform waar gegevens worden opgeslagen. Deze locatie is doorgaans het opslag account dat is gekoppeld aan de Azure Stack Edge-resource die u maakt.

## <a name="azure-stack-edge-service-protection"></a>Azure Stack Edge-service beveiliging

De service Azure Stack Edge is een beheer service die wordt gehost in Azure. De service wordt gebruikt om het apparaat te configureren en te beheren.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="azure-stack-edge-pro-device-protection"></a>Azure Stack Edge Pro-beveiliging van apparaten

Het Azure Stack Edge Pro-apparaat is een on-premises apparaat waarmee u uw gegevens kunt transformeren door deze lokaal te verwerken en deze vervolgens naar Azure te verzenden. Uw apparaat:

- Er is een activerings sleutel nodig om toegang te krijgen tot de Azure Stack Edge-service.
- Is te allen tijde beveiligd door een apparaatwachtwoord.
- Is een vergrendeld apparaat. De BMC en BIOS van het apparaat zijn beveiligd met een wacht woord. Het BIOS wordt beveiligd door beperkte gebruikers toegang.
- Beveiligd opstarten is ingeschakeld.
- Windows Defender Device Guard wordt uitgevoerd. Met Device Guard kunt u alleen vertrouwde toepassingen uitvoeren die u in uw beleid voor code-integriteit definieert.

### <a name="protect-the-device-via-activation-key"></a>Het apparaat beveiligen via de activerings sleutel

Alleen een geautoriseerd Azure Stack Edge Pro-apparaat mag lid worden van de Azure Stack Edge-service die u in uw Azure-abonnement hebt gemaakt. Als u een apparaat wilt autoriseren, moet u een activerings sleutel gebruiken om het apparaat te activeren met de Azure Stack Edge-service.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Zie [een activerings sleutel ophalen](azure-stack-edge-deploy-prep.md#get-the-activation-key)voor meer informatie.

### <a name="protect-the-device-via-password"></a>Het apparaat beveiligen via wacht woord

Wacht woorden zorgen ervoor dat alleen geautoriseerde gebruikers toegang hebben tot uw gegevens. Azure Stack Edge Pro-apparaten opstarten in een vergrendelde status.

U kunt:

- Verbinding maken met de lokale web-UI van het apparaat via een browser en vervolgens een wacht woord opgeven om zich aan te melden bij het apparaat.
- Extern verbinding maken met de apparaat-Power shell-interface via HTTP. Extern beheer is standaard ingeschakeld. Vervolgens kunt u het wacht woord van het apparaat opgeven om u aan te melden bij het apparaat. Zie [extern verbinding maken met uw Azure stack Edge Pro-apparaat](azure-stack-edge-connect-powershell-interface.md#connect-to-the-powershell-interface)voor meer informatie.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Gebruik de lokale web-UI om [het wacht woord te wijzigen](azure-stack-edge-manage-access-power-connectivity-mode.md#manage-device-access). Als u het wacht woord wijzigt, moet u alle RAS-gebruikers hiervan op de hoogte stellen, zodat er geen problemen zijn bij het aanmelden.

## <a name="protect-your-data"></a>Uw gegevens beveiligen

In deze sectie worden de functies van Azure Stack Edge Pro-beveiliging beschreven die in-transit en opgeslagen gegevens beveiligen.

### <a name="protect-data-at-rest"></a>Data-at-rest beschermen

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]
- BitLocker XTS-AES 256-bits versleuteling wordt gebruikt voor het beveiligen van lokale gegevens.


### <a name="protect-data-in-flight"></a>Gegevens in Flight beveiligen

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Gegevens beveiligen via opslag accounts

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Roteer en [Synchroniseer vervolgens uw opslag account sleutels](azure-stack-edge-manage-shares.md#sync-storage-keys) regel matig om uw opslag account te beschermen tegen onbevoegde gebruikers.

## <a name="manage-personal-information"></a>Persoonlijke gegevens beheren

De service Azure Stack Edge verzamelt persoonlijke gegevens in de volgende scenario's:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Als u de lijst wilt weer geven met gebruikers die een share kunnen openen of verwijderen, volgt u de stappen in [shares beheren op de Azure stack Edge Pro](azure-stack-edge-manage-shares.md).

Raadpleeg het privacybeleid van micro soft in het [vertrouwens centrum](https://www.microsoft.com/trustcenter)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

[Uw Azure Stack Edge Pro-apparaat implementeren](azure-stack-edge-deploy-prep.md)
