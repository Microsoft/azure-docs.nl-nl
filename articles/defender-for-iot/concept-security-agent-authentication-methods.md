---
title: Verificatie methoden voor beveiligings agenten
description: Meer informatie over de verschillende verificatie methoden die beschikbaar zijn bij het gebruik van de Defender voor IoT-service.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: shhazam
ms.openlocfilehash: 4aeaa703b5e3203eeb7bc355051e0e8f48f898ac
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103494118"
---
# <a name="security-agent-authentication-methods"></a>Verificatie methoden voor beveiligings agenten

In dit artikel worden de verschillende verificatie methoden beschreven die u met de AzureIoTSecurity-agent kunt gebruiken om te verifiëren met de IoT Hub.

Voor elk apparaat dat in het IoT Hub op Defender voor IoT is geïnstalleerd, is een Defender-IoT-micro-agent vereist. Om het apparaat te verifiëren, kan Defender voor IoT een van de volgende twee methoden gebruiken. Kies de methode die het beste werkt voor uw bestaande IoT-oplossing.

- SecurityModule optie
- Optie apparaat

## <a name="authentication-methods"></a>Verificatiemethoden

De twee methoden voor de Defender voor IoT AzureIoTSecurity-agent om verificatie uit te voeren:

- **Defender-IOT-verificatie modus micro agent**<br>
De agent wordt geverifieerd aan de hand van de identiteit van de Defender-IoT-micro agent onafhankelijk van de apparaat-id.
Gebruik dit verificatie type als u wilt dat de beveiligings agent een specifieke verificatie methode gebruikt via de Defender-IoT-micro-agent (alleen symmetrische sleutel).

- Verificatie modus **apparaat**<br>
In deze methode verifieert de beveiligings agent eerst met de apparaat-id. Na de initiële verificatie voert de Defender voor IoT-agent een **rest** -aanroep naar de IOT hub met behulp van de rest API met de verificatie gegevens van het apparaat. De Defender voor IoT-agent vraagt vervolgens de Defender-IoT-micro agent-verificatie methode en gegevens van de IoT Hub. In de laatste stap voert de Defender voor IoT-agent een verificatie uit op basis van de Defender voor IoT-module.

Gebruik dit verificatie type als u wilt dat de beveiligings agent een bestaande verificatie methode voor apparaten (zelfondertekend certificaat of symmetrische sleutel) opnieuw gebruikt.

Zie [para meters voor de beveiligings agent installeren](#security-agent-installation-parameters) voor meer informatie over het configureren van.

## <a name="authentication-methods-known-limitations"></a>Bekende beperkingen van de verificatie methoden

- De **SecurityModule** -verificatie modus ondersteunt alleen symmetrische sleutel verificatie.
- CA-Signed certificaat wordt niet ondersteund door de verificatie modus van het **apparaat** .

## <a name="security-agent-installation-parameters"></a>Beveiligings agent-installatie parameters

Wanneer u [een beveiligings agent implementeert](how-to-deploy-agent.md), moeten de verificatie gegevens als argumenten worden gegeven.
Deze argumenten worden beschreven in de volgende tabel.

|Naam van Linux-para meter | Naam van Windows-para meter | Steno parameter |Beschrijving|Opties|
|---------------------|---------------|---------|---------------|---------------|
|verificatie-identiteit|AuthenticationIdentity|aui|Verificatie-identiteit| **SecurityModule** of **apparaat**|
|verificatie-methode|Authentic|aum|Verificatiemethode|**SymmetricKey** of **SelfSignedCertificate**|
|bestandspad|Bestandspad|f|Absoluut volledig pad voor het bestand met het certificaat of de symmetrische sleutel| |
|hostnaam|HostName|hn|FQDN-namen van de IoT Hub|Voor beeld: ContosoIotHub.azure-devices.net|
|apparaat-id|DeviceId|di|Apparaat-ID|Voor beeld: MyDevice1|
|certificaat locatie-soort|CertificateLocationKind|cl|Opslag locatie van certificaat|**Lokaal** bestand of **Archief**|
|

Wanneer u het script voor de installatie van de beveiligings agent gebruikt, wordt de volgende configuratie automatisch uitgevoerd. Bewerk het configuratie bestand om de verificatie van de beveiligings agent hand matig te bewerken.

## <a name="change-authentication-method-after-deployment"></a>Verificatie methode na implementatie wijzigen

Wanneer u een beveiligings agent met een installatie script implementeert, wordt automatisch een configuratie bestand gemaakt.

Als u verificatie methoden na de implementatie wilt wijzigen, is hand matig bewerken van het configuratie bestand vereist.

### <a name="c-based-security-agent"></a>Beveiligings agent op basis van C#

Bewerk _Authentication.config_ met de volgende para meters:

```xml
<Authentication>
  <add key="deviceId" value=""/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value=""/>
  <add key="type" value=""/>
  <add key="identity" value=""/>
  <add key="certificateLocationKind" value="" />
</Authentication>
```

### <a name="c-based-security-agent"></a>Op C gebaseerde beveiligings agent

Bewerk _LocalConfiguration.js_ met de volgende para meters:

```json
"Authentication" : {
    "Identity" : "",
    "AuthenticationMethod" : "",
    "FilePath" : "",
    "DeviceId" : "",
    "HostName" : ""
}
```

## <a name="see-also"></a>Zie ook

- [Overzicht van beveiligings agenten](security-agent-architecture.md)
- [Beveiligings agent implementeren](how-to-deploy-agent.md)
- [Toegang tot onbewerkte beveiligingsgegevens](how-to-security-data-access.md)
