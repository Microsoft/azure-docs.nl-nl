---
title: Azure Data Box Gateway beveiliging | Microsoft Docs
description: Hierin worden de beveiligings-en privacyfuncties beschreven voor het beveiligen van uw Azure Data Box Gateway virtuele apparaten, services en gegevens, on-premises en in de Cloud.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: 13d3809611714992f24a66a96c22074e69fba9bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98786653"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Beveiligings-en gegevens beveiliging Azure Data Box Gateway

Beveiliging is een belang rijk probleem bij het aannemen van een nieuwe technologie, met name als de technologie wordt gebruikt met vertrouwelijke of bedrijfs eigen gegevens. Azure Data Box Gateway helpt u ervoor te zorgen dat alleen geautoriseerde entiteiten uw gegevens kunnen bekijken, wijzigen of verwijderen.

In dit artikel worden de Azure Data Box Gateway beveiligings functies beschreven waarmee u elk van de oplossings onderdelen en de gegevens die erin zijn opgeslagen, kunt beveiligen.

De Data Box Gateway oplossing bestaat uit vier hoofd onderdelen die met elkaar communiceren:

- **Data Box Gateway-Service, gehost in azure**. De beheer resource die u gebruikt om de volg orde van apparaten te maken, het apparaat te configureren en de volg orde bij te werken.
- **Data Box gateway apparaat**. Het virtuele apparaat dat u inricht in de Hyper Visor van het systeem dat u opgeeft. Dit virtuele apparaat wordt gebruikt voor het importeren van uw on-premises gegevens in Azure.
- **Clients/hosts die zijn verbonden met het apparaat**. De clients in uw infra structuur die verbinding maken met het Data Box Gateway apparaat en gegevens bevatten die moeten worden beveiligd.
- **Cloud opslag**. De locatie in het Azure-Cloud platform waar gegevens worden opgeslagen. Deze locatie is doorgaans het opslag account dat is gekoppeld aan de Data Box Gateway bron die u maakt.

## <a name="data-box-gateway-service-protection"></a>Data Box Gateway Service beveiliging

De Data Box Gateway-Service is een beheer service die wordt gehost in Azure. De service wordt gebruikt om het apparaat te configureren en te beheren.

[!INCLUDE [data-box-gateway-service-protection](../../includes/data-box-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>Beveiliging van Data Box Gateway apparaat

Het Data Box Gateway apparaat is een virtueel apparaat dat is ingericht in de Hyper Visor van een on-premises systeem dat u opgeeft. Het apparaat helpt bij het verzenden van gegevens naar Azure. Uw apparaat:

- Er is een activerings sleutel nodig om toegang te krijgen tot de Azure Stack Edge Pro/Data Box Gateway-Service.
- Is te allen tijde beveiligd door een apparaatwachtwoord.
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>Het apparaat beveiligen via de activerings sleutel

Alleen een geautoriseerd Data Box Gateway apparaat mag lid worden van de Data Box Gateway-Service die u in uw Azure-abonnement hebt gemaakt. Als u een apparaat wilt autoriseren, moet u een activerings sleutel gebruiken om het apparaat te activeren met de Data Box Gateway-Service.

[!INCLUDE [data-box-gateway-activation-key](../../includes/data-box-gateway-activation-key.md)]

Zie [een activerings sleutel ophalen](data-box-gateway-deploy-prep.md#get-the-activation-key)voor meer informatie.

### <a name="protect-the-device-via-password"></a>Het apparaat beveiligen via wacht woord

Wacht woorden zorgen ervoor dat alleen geautoriseerde gebruikers toegang hebben tot uw gegevens. Data Box Gateway apparaten worden opgestart met een vergrendelde status.

U kunt:

- Verbinding maken met de lokale web-UI van het apparaat via een browser en vervolgens een wacht woord opgeven om zich aan te melden bij het apparaat.
- Extern verbinding maken met de Power shell-interface van het apparaat via HTTP. Extern beheer is standaard ingeschakeld. Vervolgens kunt u het wacht woord van het apparaat opgeven om u aan te melden bij het apparaat. Zie [extern verbinding maken met uw data Box gateway apparaat](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface)voor meer informatie.

[!INCLUDE [data-box-gateway-password-best-practices](../../includes/data-box-gateway-password-best-practices.md)]
- Gebruik de lokale web-UI om [het wacht woord te wijzigen](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access). Als u het wacht woord wijzigt, moet u alle RAS-gebruikers hiervan op de hoogte stellen, zodat er geen problemen zijn bij het aanmelden.

## <a name="protect-your-data"></a>Uw gegevens beveiligen

In deze sectie worden de Data Box Gateway beveiligings functies beschreven die in-transit en opgeslagen gegevens beveiligen.

### <a name="protect-data-at-rest"></a>Data-at-rest beschermen

[!INCLUDE [data-box-gateway-data-rest](../../includes/data-box-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Gegevens in Flight beveiligen

[!INCLUDE [data-box-gateway-data-flight](../../includes/data-box-gateway-data-flight.md)]

### <a name="protect-data-using-storage-accounts"></a>Gegevens beveiligen met opslag accounts

[!INCLUDE [data-box-gateway-data-storage-accounts](../../includes/data-box-gateway-protect-data-storage-accounts.md)]

- Roteer en [Synchroniseer vervolgens uw opslag account sleutels](data-box-gateway-manage-shares.md#sync-storage-keys) regel matig om uw opslag account te beschermen tegen onbevoegde gebruikers.

### <a name="protect-the-device-data-using-bitlocker"></a>De apparaatgegevens beveiligen met BitLocker

Als u de virtuele schijven op uw Data Box Gateway virtuele machine wilt beveiligen, wordt u aangeraden BitLocker in te scha kelen. BitLocker is standaard niet ingeschakeld. Zie voor meer informatie:

- [Instellingen voor ondersteuning van versleuteling in Hyper-V Manager](/windows-server/virtualization/hyper-v/learn-more/generation-2-virtual-machine-security-settings-for-hyper-v#encryption-support-settings-in-hyper-v-manager)
- [BitLocker-ondersteuning op een virtuele machine](https://kb.vmware.com/s/article/2036142)

## <a name="manage-personal-information"></a>Persoonlijke gegevens beheren

De Data Box Gateway-Service verzamelt persoonlijke gegevens in de volgende scenario's:

[!INCLUDE [data-box-gateway-manage-personal-data](../../includes/data-box-gateway-manage-personal-data.md)]

Volg de stappen in [shares beheren op het data Box gateway](data-box-gateway-manage-shares.md)om de lijst weer te geven met gebruikers die een share kunnen openen of verwijderen.

Raadpleeg het privacybeleid van micro soft in het [vertrouwens centrum](https://www.microsoft.com/trustcenter)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

[Uw Data Box Gateway-apparaat implementeren](data-box-gateway-deploy-prep.md)
