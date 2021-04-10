---
title: Azure Kinect sensor SDK-systeem vereisten
description: Meer informatie over de systeem vereisten voor de Azure Kinect sensor SDK op Windows en Linux.
author: qm13
ms.author: quentinm
ms.custom:
- CI 115266
- CSSTroubleshooting
manager: dcscontentpm
ms.prod: kinect-dk
ms.date: 03/05/2021
ms.topic: article
keywords: Azure, kinect, systeem vereisten, CPU, GPU, USB, installeren, instellen, minimum, vereisten
ms.openlocfilehash: 558c1b9ca264874fa808aeba5f1e8a809a8f722d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104656971"
---
# <a name="azure-kinect-sensor-sdk-system-requirements"></a>Azure Kinect sensor SDK-systeem vereisten

Dit document bevat informatie over de systeem vereisten die nodig zijn voor het installeren van de sensor SDK en het implementeren van uw Azure Kinect DK.

## <a name="supported-operating-systems-and-architectures"></a>Ondersteunde besturingssystemen en architecturen

- Windows 10 april 2018 (versie 1803, OS Build 17134) release (x64) of een latere versie
- Linux Ubuntu 18,04 (x64), met een GPU-stuur programma dat gebruikmaakt van OpenGLv 4.4 of een hogere versie

De sensor-SDK is beschikbaar voor de Windows-API (Win32) voor native C/C++-Windows-toepassingen. De SDK is momenteel niet beschikbaar voor UWP-toepassingen. Azure Kinect DK wordt niet ondersteund voor de modus S van Windows 10.

## <a name="development-environment-requirements"></a>Vereisten voor de ontwikkel omgeving

Ga naar [github](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK)om bij te dragen aan de ontwikkeling van de sensor-SDK.

## <a name="minimum-host-pc-hardware-requirements"></a>Minimale hardwarevereisten voor host-PC'S

De hardware-vereiste voor de PC-host is afhankelijk van de frequentie/resolutie van de toepassing/het algoritme/het sensor frame dat wordt uitgevoerd op de hostcomputer. De aanbevolen minimale sensor configuratie voor Windows is:

- Zevende generatie Intel &reg; CoreTM i3-processor (Dual Core 2,4 GHz met HD620 GPU of sneller)
- 4 GB geheugen
- Toegewezen USB3-poort
- Ondersteuning voor grafische Stuur Programma's voor OpenGL 4,4 of DirectX 11,0

Het kan ook voor komen dat de onderliggende of oudere Cpu's werken, afhankelijk van uw gebruiks aanvraag.

De prestaties verschillen ook tussen Windows/Linux-besturings systemen en grafische Stuur Programma's die in gebruik zijn.

## <a name="body-tracking-host-pc-hardware-requirements"></a>De hardware-vereisten voor het bijhouden van host-PC'S

De serie vereiste voor het bijhouden van de PC-host is strikter dan de algemene PC-host vereiste. De aanbevolen minimale tracking-configuratie voor de hoofd tekst voor Windows is:

- Zevende generatie Intel &reg; CoreTM i5-processor (Quad Core 2,4 GHz of sneller)
- 4 GB geheugen
- NVIDIA GEFORCE GTX 1050 of gelijkwaardig
- Toegewezen USB3-poort

Bij de aanbevolen minimale configuratie wordt uitgegaan van K4A_DEPTH_MODE_NFOV_UNBINNED diepte modus bij het volgen van 30fps vijf personen. Lagere CPU-of oudere Cpu's en NVIDIA-Gpu's kunnen ook worden gebruikt, afhankelijk van uw gebruiks aanvraag.

## <a name="usb3"></a>USB3

Er zijn bekende compatibiliteits problemen met USB-hostcontrollers. Meer informatie vindt u op de [pagina problemen oplossen](troubleshooting.md#usb3-host-controller-compatibility)

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure Kinect DK](about-azure-kinect-dk.md)

- [Azure Kinect DK installeren](set-up-azure-kinect-dk.md)

- [Bijhouden van de hoofd tekst van Azure Kinect instellen](body-sdk-setup.md)
