---
title: Edge-vereisten voor de certificering van basis beveiliging
description: Edge-vereisten voor beveiligd basis certificerings programma
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: Edge Secured-core Certification Requirements
ms.service: certification
ms.openlocfilehash: 5bb02f939bb63fd1c6365fd4570996f09119e958
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166901"
---
# <a name="azure-certified-device---edge-secured-core-preview"></a>Azure Certified apparaat-Edge Secure-core (preview-versie) #

## <a name="edge-secured-core-certification-requirements"></a>Vereisten voor de certificering van Edge-Secured-Core ##

In dit document vindt u een overzicht van de specifieke mogelijkheden en vereisten voor apparaten waaraan wordt voldaan om de certificering te volt ooien en een apparaat in de Azure IoT-apparaatinstantie te vermelden met het rand bestand met de naam beveiligde kernen.

### <a name="program-purpose"></a>Programma doel ###
Edge Secure-Core is een incrementele certificering in het Azure Certified Device-programma voor IoT-apparaten met een volledig besturings systeem, zoals Linux of Windows 10 IoT. met dit programma kunnen apparaat-partners hun apparaten onderscheiden door aan een extra set beveiligings criteria te voldoen. Apparaten die voldoen aan deze criteria, bieden de volgende beloften:
1. Apparaat-id op basis van hardware 
2. Kan de systeem integriteit afdwingen 
3. Blijft up-to-date en kan extern worden beheerd
4. Biedt beveiliging van gegevens op rest
5. Biedt gegevens in transit beveiliging
6. Ingebouwde beveiligings agent en-beveiliging
### <a name="requirements"></a>Vereisten ###

---
|Name|SecuredCore. ingebouwde beveiliging|
|:---|:---|
|Status|Vereist|
|Beschrijving|Het doel van de test is om ervoor te zorgen dat apparaten beveiligings gegevens en gebeurtenissen kunnen rapporteren door gegevens naar Azure Defender voor IoT te verzenden.|
|Beschik baarheid doel|2021|
|Van toepassing op|Elk apparaat|
|Besturingssysteem|Agnostisch|
|Validatie type|Hand matig/extra|
|Validatie |Het apparaat moet beveiligings logboeken en waarschuwingen genereren. Logboeken en waarschuwings berichten van het apparaat worden Azure Security Center.<ol><li>De beveiligings agent downloaden en implementeren via GitHub</li><li>Valideer het waarschuwings bericht van Azure Defender voor IoT.</li></ol>|
|Resources|[Azure docs IoT Defender voor IoT](../defender-for-iot/how-to-configure-agent-based-solution.md)|

---
|Name|SecuredCore. encryption. Storage|
|:---|:---|
|Status|Vereist|
|Beschrijving|Het doel van de test om te valideren dat gevoelige gegevens kunnen worden versleuteld op niet-vluchtige opslag.|
|Beschik baarheid doel|2021|
|Van toepassing op|Elk apparaat|
|Besturingssysteem|Agnostisch|
|Validatie type|Hand matig/extra|
|Validatie|Het apparaat moet worden gevalideerd via de toolset om ervoor te zorgen dat opslag versleuteling is ingeschakeld en de standaard algoritme XTS-AES is, met sleutel lengte 128 bits of hoger.|
|Resources||

---
|Name|SecuredCore. hardware. SecureEnclave|
|:---|:---|
|Status|Optioneel|
|Description|Het doel van de test om te controleren of een beveiligde enclave bestaat en of de enclave toegankelijk is vanuit een veilige agent.|
|Beschik baarheid doel|2021|
|Van toepassing op|Elk apparaat|
|Besturingssysteem|Agnostisch|
|Validatie type|Hand matig/extra|
|Validatie|Het apparaat moet worden gevalideerd met de toolset om ervoor te zorgen dat de Azure-beveiligings agent kan communiceren met de beveiligde enclave|
|Resources|https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesLinux.md|

---
|Name|SecuredCore. hardware. Identity|
|:---|:---|
|Status|Vereist|
|Beschrijving|Het doel van de test is om te valideren of het identificeren van het apparaat is geroot in hardware.|
|Beschik baarheid doel|2021|
|Van toepassing op|Elk apparaat|
|Besturingssysteem|Agnostisch|
|Validatie type|Hand matig/extra|
|Validatie|Het apparaat moet worden gevalideerd via toolset om ervoor te zorgen dat er een TPM aanwezig is op het apparaat en dat het kan worden ingericht via IoT Hub met behulp van TPM-goedkeurings sleutel.|
|Resources|[Setup automatisch inrichten met DPS](../iot-dps/quick-setup-auto-provision.md)|

---
|Name|SecuredCore. update|
|:---|:---|
|Status|Vereist|
|Beschrijving|Het doel van de test is om te valideren dat het apparaat de firmware en software kan ontvangen en bijwerken.|
|Beschik baarheid doel|2021|
|Van toepassing op|Elk apparaat|
|Besturingssysteem|Agnostisch|
|Validatie type|Hand matig/extra|
|Validatie|Partner bevestiging dat er via micro soft update, Azure update of andere goedgekeurde Services een update naar het apparaat kan worden verzonden.|
|Resources|[Update van het apparaat voor IoT Hub](../iot-hub-device-update/index.yml)|

---
|Name|SecuredCore.Manageability.Configuratie|
|:---|:---|
|Status|Vereist|
|Beschrijving|Het doel van de test is het valideren van de apparaten die extern beveiligings beheer ondersteunen.|
|Beschik baarheid doel|2021|
|Van toepassing op|Elk apparaat|
|Besturingssysteem|Agnostisch|
|Validatie type|Hand matig/extra|
|Validatie|Het apparaat moet worden gevalideerd met de toolset om ervoor te zorgen dat het apparaat de mogelijkheid biedt om extern te beheren en te voldoen aan de specifieke beveiligings configuraties. En de status wordt weer gegeven aan IoT Hub/Azure Defender voor IoT.|
|Resources||

---
|Name|SecuredCore. managed. reset|
|:---|:---|
|Status|Vereist|
|Beschrijving|Het doel van deze test is om het apparaat te valideren op basis van twee gebruiks voorbeelden: a) de mogelijkheid om een herstel bewerking uit te voeren (gebruikers gegevens verwijderen, gebruikers configuratie verwijderen), b) het apparaat herstellen naar het laatst bekende goede in het geval van een update die problemen veroorzaakt.|
|Beschik baarheid doel|2021|
|Van toepassing op|Elk apparaat|
|Besturingssysteem|Agnostisch|
|Validatie type|Hand matig/extra|
|Validatie|Het apparaat dat moet worden gevalideerd door een combi natie van toolset en documentatie die het apparaat ondersteunt deze functionaliteit. De fabrikant van het apparaat kan bepalen of deze mogelijkheden moeten worden geïmplementeerd ter ondersteuning van externe Reset of alleen lokaal opnieuw instellen.|
|Resources||

---
|Name|SecuredCore. updates. duur|
|:---|:---|
|Status|Vereist|
|Beschrijving|Het doel van dit beleid is om ervoor te zorgen dat het apparaat beveiligd blijft.|
|Beschik baarheid doel|2021|
|Van toepassing op|Elk apparaat|
|Besturingssysteem|Agnostisch|
|Validatie type|Handmatig|
|Validatie|De toezeg ging van het indienen van apparaten is vereist om de apparaten up-to-date te houden voor 60 maanden na de datum van verzen ding. Specificaties die op een of andere manier beschikbaar zijn voor de koper en apparaten, moeten de duur aangeven waarop de software wordt bijgewerkt.|
|Resources||

---
|Name|SecuredCore. policy. vuln. openbaar making|
|:---|:---|
|Status|Vereist|
|Beschrijving|Het doel van dit beleid is om ervoor te zorgen dat er een mechanisme is voor het verzamelen en distribueren van rapporten van beveiligings problemen in het product.|
|Beschik baarheid doel|2021|
|Van toepassing op|Elk apparaat|
|Besturingssysteem|Agnostisch|
|Validatie type|Handmatig|
|Validatie|Documentatie over het proces voor het verzenden en ontvangen van beveiligings problemen voor gecertificeerde apparaten wordt gecontroleerd.|
|Resources||

---
|Name|SecuredCore. policy. vuln. fixes|
|:---|:---|
|Status|Vereist|
|Beschrijving|Het doel van dit beleid is om ervoor te zorgen dat hoge/essentiële beveiligings problemen (met behulp van CVSS 3,0) worden opgelost binnen 180 dagen nadat de oplossing beschikbaar is.|
|Beschik baarheid doel|2021|
|Van toepassing op|Elk apparaat|
|Besturingssysteem|Agnostisch|
|Validatie type|Handmatig|
|Validatie|Documentatie over het proces voor het verzenden en ontvangen van beveiligings problemen voor gecertificeerde apparaten wordt gecontroleerd.|
|Resources||


---
|Name|SecuredCore. encryption. TLS|
|:---|:---|
|Status|Vereist|
|Beschrijving|Het doel van de test is het valideren van de ondersteuning voor vereiste TLS-versies en coderings suites.|
|Beschik baarheid doel|2021|
|Van toepassing op|Elk apparaat|
|Besturingssysteem|Agnostisch|
|Validatie type|Hand matig/extra|
Validatie|Het apparaat moet worden gevalideerd met de toolset om ervoor te zorgen dat het apparaat een minimale TLS-versie van 1,2 ondersteunt en de volgende vereiste TLS-coderings suites ondersteunt.<ul><li>TLS_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_RSA_WITH_AES_128_CBC_SHA256</li><li>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256</li><li>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256</li><li>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256</li></ul>|
|Resources| [TLS-ondersteuning in IoT Hub](../iot-hub/iot-hub-tls-support.md) <br /> [TLS-coderings suites in Windows 10](https://docs.microsoft.com/windows/win32/secauthn/tls-cipher-suites-in-windows-10-v1903) |

---
|Name|SecuredCore. Protection. SignedUpdates|
|:---|:---|
|Status|Vereist|
|Beschrijving|Het doel van de test is te controleren of de updates moeten worden ondertekend.|
|Beschik baarheid doel|2021|
|Van toepassing op|Elk apparaat|
|Besturingssysteem|Agnostisch|
|Validatie type|Hand matig/extra|
|Validatie|Het apparaat moet worden gevalideerd via toolset om ervoor te zorgen dat updates van het besturings systeem, stuur Programma's, toepassings software, Bibliotheken, pakketten en firmware niet worden toegepast, tenzij het goed is ondertekend en gevalideerd.
|Resources||

---
|Name|SecuredCore. firmware. SecureBoot|
|:---|:---|
|Status|Vereist|
|Beschrijving|Het doel van de test is het valideren van de opstart integriteit van het apparaat.|
|Beschik baarheid doel|2021|
|Van toepassing op|Elk apparaat|
|Besturingssysteem|Agnostisch|
|Validatie type|Hand matig/extra|
|Validatie|Het apparaat moet worden gevalideerd via toolset om ervoor te zorgen dat de firmware en kernel-hand tekeningen worden gevalideerd wanneer het apparaat wordt opgestart. <ul><li>UEFI: beveiligd opstarten is ingeschakeld</li><li>Uboot: gecontroleerd opstarten is ingeschakeld</li></ul>|
|Resources||

---
|Name|SecuredCore. Protection. CodeIntegrity|
|:---|:---|
|Status|Vereist|
|Beschrijving|Het doel van deze test is om te valideren dat code-integriteit op dit apparaat beschikbaar is.|
|Beschik baarheid doel|2021|
|Van toepassing op|Elk apparaat|
|Besturingssysteem|Agnostisch|
|Validatie type|Hand matig/extra|
|Validatie|Het apparaat moet worden gevalideerd met de toolset om ervoor te zorgen dat code-integriteit is ingeschakeld. </br> Windows: HVCI </br> Linux: DM-Verity en IMA|
|Resources||

---
|Name|SecuredCore. Protection. NetworkServices|
|:---|:---|
|Status|Vereist|
|Beschrijving|Het doel van de test is te valideren dat toepassingen die invoer van het netwerk accepteren, niet worden uitgevoerd met verhoogde bevoegdheden.|
|Beschik baarheid doel|2021|
|Van toepassing op|Elk apparaat|
|Besturingssysteem|Agnostisch|
|Validatie type|Hand matig/extra|
|Validatie|Het apparaat moet worden gevalideerd via toolset om ervoor te zorgen dat de services die netwerk verbindingen accepteren, niet worden uitgevoerd met systeem-of hoofd bevoegdheden.|
|Resources||

---
|Name|SecuredCore. Protection. basis lijnen|
|:---|:---|
|Status|Vereist|
|Beschrijving|Het doel van de test is om te controleren of het systeem voldoet aan een basis beveiligings configuratie.|
|Beschik baarheid doel|2021|
|Van toepassing op|Elk apparaat|
|Besturingssysteem|Agnostisch|
|Validatie type|Hand matig/extra|
|Validatie|Het apparaat moet worden gevalideerd met de toolset om ervoor te zorgen dat er voor de configuratie van een Defender IOT-systeem een bench Mark is uitgevoerd.|
|Resources| https://techcommunity.microsoft.com/t5/microsoft-security-baselines/bg-p/Microsoft-Security-Baselines <br> https://www.cisecurity.org/cis-benchmarks/ |

---
|Name|SecuredCore. firmware. Protection|
|:---|:---|
|Status|Vereist|
|Beschrijving|Het doel van de test is om ervoor te zorgen dat het apparaat voldoende oplossingen heeft tegen bedreigingen voor de beveiliging van de firmware.|
|Beschik baarheid doel|2021|
|Van toepassing op|Elk apparaat|
|Besturingssysteem|Agnostisch|
|Validatie type|Hand matig/extra|
|Validatie|Het apparaat moet worden gevalideerd door middel van de hulpprogram ma's om te bevestigen dat het wordt beschermd tegen bedreigingen voor de beveiliging van de firmware via een van de volgende benaderingen: <ul><li>DRTM + UEFI-beheer modus beperkende factoren</li><li>DRTM + UEFI-beveiliging van de beheer modus</li><li>Goedgekeurde FW die SRTM + runtime-firmware-beveiliging ondersteunt</li></ul> |
|Resources| https://trustedcomputinggroup.org/ |

---
|Name|SecuredCore. firmware. Attestation|
|:---|:---|
|Status|Vereist|
|Beschrijving|Het doel van de test is om ervoor te zorgen dat het apparaat extern kan worden verklaard voor de Microsoft Azure Attestation-service.|
|Beschik baarheid doel|2021|
|Van toepassing op|Elk apparaat|
|Besturingssysteem|Agnostisch|
|Validatie type|Hand matig/extra|
|Validatie|Het apparaat moet worden gevalideerd met de toolset om ervoor te zorgen dat de opstart logboeken van het platform en de metingen van de opstart activiteit kunnen worden verzameld en op afstand worden verklaard voor de Microsoft Azure Attestation-service.|
|Resources| [Microsoft Azure Attestation](../attestation/index.yml) |

---
|Name|SecuredCore. hardware. MemoryProtection|
|:---|:---|
|Status|Vereist|
|Beschrijving|Het doel van de test is te controleren of DMA niet is ingeschakeld op extern toegankelijke poorten.|
|Beschik baarheid doel|2021|
|Van toepassing op|Elk apparaat|
|Besturingssysteem|Agnostisch|
|Validatie type|Hand matig/extra|
|Validatie|Als er DMA-compatibele externe poorten bestaan op het apparaat, kunt u met de toolset controleren of de IOMMU of SMMU is ingeschakeld en geconfigureerd voor die poorten.|
|Resources||

---
|Name|SecuredCore. Protection. debug|
|:---|:---|
|Status|Vereist|
|Beschrijving|Het doel van de test is te controleren of de functie voor fout opsporing op het apparaat is uitgeschakeld.|
|Beschik baarheid doel|2021|
|Van toepassing op|Elk apparaat|
|Besturingssysteem|Agnostisch|
|Validatie type|Hand matig/extra|
|Validatie|Het apparaat moet worden gevalideerd met de toolset om ervoor te zorgen dat de functie voor fout opsporing autorisatie vereist.|
|Resources||
