---
title: Scripts voor Azure IoT Edge met Windows-containers | Microsoft Docs
description: Naslag informatie voor het IoT Edge van Power shell-scripts om Windows-apparaten te installeren, te verwijderen of bij te werken
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/06/2020
ms.topic: reference
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: a919238e4a62ae8954e101cb21a2fd4943191f6a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103489521"
---
# <a name="powershell-scripts-for-iot-edge-with-windows-containers"></a>Power shell-scripts voor IoT Edge met Windows-containers

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Meer informatie over de Power shell-scripts waarmee u IoT Edge op Windows-apparaten installeert, bijwerkt of verwijdert.

De opdrachten die in dit artikel worden beschreven, zijn afkomstig uit het `IoTEdgeSecurityDaemon.ps1` bestand dat bij elke [IOT Edge versie](https://github.com/Azure/azure-iotedge/releases)wordt uitgebracht. De meest recente versie van het script is altijd beschikbaar op aka.ms/iotedge-win.

U kunt een van de opdrachten uitvoeren met de `Invoke-WebRequest` cmdlet om toegang te krijgen tot de meest recente script versie. Bijvoorbeeld:

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge
```

U kunt dit script of een versie van het script van een specifieke versie ook downloaden om de opdrachten uit te voeren. Bijvoorbeeld:

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Deploy-IoTEdge
```

Het gegeven script is ondertekend om de beveiliging te verbeteren. U kunt de hand tekening controleren door het script te downloaden naar uw apparaat en vervolgens de volgende Power shell-opdracht uit te voeren:

```powershell
Get-AuthenticodeSignature "C:\<path>\IotEdgeSecurityDaemon.ps1"
```

De uitvoer status is **geldig** als de hand tekening wordt geverifieerd.

## <a name="deploy-iotedge"></a>Deploy-IoTEdge

Met de Deploy-IoTEdge opdracht worden de IoT Edge Security daemon en de bijbehorende afhankelijkheden gedownload en geïmplementeerd. De implementatie opdracht accepteert deze algemene para meters, onder andere. Gebruik de opdracht voor de volledige lijst `Get-Help Deploy-IoTEdge -full` .  

| Parameter | Geaccepteerde waarden | Opmerkingen |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** of **Linux** | Als er geen container besturings systeem is opgegeven, is Windows de standaard waarde.<br><br>Voor Windows-containers maakt IoT Edge gebruik van de Moby-container engine die is opgenomen in de installatie. Voor Linux-containers moet u een container Engine installeren voordat u de installatie start. |
| **Proxy** | Proxy-URL | Neem deze para meter op als uw apparaat een proxy server moet door lopen om het Internet te bereiken. Zie [een IOT edge apparaat configureren om te communiceren via een proxy server](how-to-configure-proxy-support.md)voor meer informatie. |
| **OfflineInstallationPath** | Mappad | Als deze para meter is opgenomen, controleert het installatie programma de vermelde map voor de IoT Edge cab-en VC runtime-bestanden die nodig zijn voor de installatie. Bestanden die niet in de map zijn gevonden, worden gedownload. Als beide bestanden zich in de map bevinden, kunt u IoT Edge installeren zonder een Internet verbinding. U kunt deze para meter ook gebruiken om een specifieke versie te gebruiken. |
| **InvokeWebRequestParameters** | Hashtabel van para meters en waarden | Tijdens de installatie worden er diverse webaanvragen gedaan. Gebruik dit veld om para meters in te stellen voor deze webaanvragen. Deze para meter is handig voor het configureren van referenties voor proxy servers. Zie [een IOT edge apparaat configureren om te communiceren via een proxy server](how-to-configure-proxy-support.md)voor meer informatie. |
| **RestartIfNeeded** | geen | Met deze vlag kan het implementatie script de computer opnieuw opstarten zonder te vragen, indien nodig. |

## <a name="initialize-iotedge"></a>Initialize-IoTEdge

Met de Initialize-IoTEdge-opdracht configureert u IoT Edge met uw apparaat connection string en operationele gegevens. Veel van de gegevens die door deze opdracht worden gegenereerd, worden vervolgens opgeslagen in het iotedge\config.yaml-bestand. De initialisatie opdracht accepteert deze algemene para meters, onder andere. Gebruik de opdracht voor de volledige lijst `Get-Help Initialize-IoTEdge -full` .

| Parameter | Geaccepteerde waarden | Opmerkingen |
| --------- | --------------- | -------- |
| **ManualConnectionString** | Geen | **Switch-para meter**. **Standaard waarde**. Als er geen inrichtings type is opgegeven, is hand matige inrichting met een connection string de standaard waarde.<br><br>Declareert dat u een apparaat connection string opgeeft om het apparaat hand matig in te richten. |
| **ManualX509** | Geen | **Switch-para meter**. Als er geen inrichtings type is opgegeven, is hand matige inrichting met een connection string de standaard waarde.<br><br>Declareert dat u een identiteits certificaat en een persoonlijke sleutel moet opgeven om het apparaat hand matig in te richten.
| **DpsTpm** | Geen | **Switch-para meter**. Als er geen inrichtings type is opgegeven, is hand matige inrichting met een connection string de standaard waarde.<br><br>Declareert dat u een DPS-scope-ID (Device Provisioning Service) opgeeft en de registratie-ID van uw apparaat moet worden ingericht via DPS.  |
| **DpsSymmetricKey** | Geen | **Switch-para meter**. Als er geen inrichtings type is opgegeven, is hand matige inrichting met een connection string de standaard waarde.<br><br>Declareert dat u een DPS-scope-ID (Device Provisioning Service) en de registratie-ID van uw apparaat opgeeft om in te richten via DPS, samen met een symmetrische sleutel voor Attestation. |
| **DpsX509** | Geen | **Switch-para meter**. Als er geen inrichtings type is opgegeven, is hand matige inrichting met een connection string de standaard waarde.<br><br>Declareert dat u een DPS-scope-ID (Device Provisioning Service) en de registratie-ID van uw apparaat opgeeft voor het inrichten via DPS, samen met een X. 509-identiteits certificaat en een persoonlijke sleutel voor Attestation.  |
| **DeviceConnectionString** | Een connection string van een IoT Edge apparaat dat is geregistreerd in een IoT Hub, in enkele aanhalings tekens | **Vereist** voor hand matige inrichting met een Connection String. Als u geen connection string in de script parameters opgeeft, wordt u gevraagd om een. |
| **IotHubHostName** | De hostnaam van de IoT-hub waarmee een apparaat verbinding maakt. | **Vereist** voor hand matige inrichting met X. 509-certificaten. Neemt de indeling *{hub name}. Azure-devices.net*. |
| **DeviceId** | De apparaat-ID van de id van een geregistreerde apparaat in IoT Hub. | **Vereist** voor hand matige inrichting met X. 509-certificaten. |
| **Scope** | Een bereik-ID van een instantie van Device Provisioning Service die is gekoppeld aan uw IoT Hub. | **Vereist** voor DPS-inrichting. Als u geen bereik-ID opgeeft in de script parameters, wordt u gevraagd om een scope nummer. |
| **Registratie** | Een registratie-ID die is gegenereerd door uw apparaat | **Vereist** voor DPS-inrichting bij gebruik van TPM of symmetrische sleutel Attestation. **Optioneel** als u X. 509-certificaat attest gebruikt. |
| **X509IdentityCertificate** | Het URI-pad naar het X. 509 apparaat-identiteits certificaat op het apparaat. | **Vereist** voor hand MATIGE of DPS-inrichting bij het gebruik van X. 509-certificaat Attestation. |
| **X509IdentityPrivateKey** | Het URI-pad naar de X. 509 apparaat-ID certificaat sleutel op het apparaat. | **Vereist** voor hand MATIGE of DPS-inrichting bij het gebruik van X. 509-certificaat Attestation. |
| **SymmetricKey** | De symmetrische sleutel die wordt gebruikt om de IoT Edge apparaat-id in te richten wanneer DPS wordt gebruikt | **Vereist** voor DPS-inrichting bij gebruik van symmetrische sleutel Attestation. |
| **ContainerOs** | **Windows** of **Linux** | Als er geen container besturings systeem is opgegeven, is Windows de standaard waarde.<br><br>Voor Windows-containers maakt IoT Edge gebruik van de Moby-container engine die is opgenomen in de installatie. Voor Linux-containers moet u een container Engine installeren voordat u de installatie start. |
| **DeviceCACertificate** | Het URI-pad naar het X. 509-CA-certificaat op het apparaat. | Kan ook worden geconfigureerd in het `C:\ProgramData\iotedge\config.yaml` bestand. Zie [certificaten beheren op een IOT edge apparaat](how-to-manage-device-certificates.md)voor meer informatie. |
| **DeviceCAPrivateKey** | Het URI-pad naar de X. 509-CERTIFICERINGs sleutel voor het apparaat op het apparaat. | Kan ook worden geconfigureerd in het `C:\ProgramData\iotedge\config.yaml` bestand. Zie [certificaten beheren op een IOT edge apparaat](how-to-manage-device-certificates.md)voor meer informatie. |
| **InvokeWebRequestParameters** | Hashtabel van para meters en waarden | Tijdens de installatie worden er diverse webaanvragen gedaan. Gebruik dit veld om para meters in te stellen voor deze webaanvragen. Deze para meter is handig voor het configureren van referenties voor proxy servers. Zie [een IOT edge apparaat configureren om te communiceren via een proxy server](how-to-configure-proxy-support.md)voor meer informatie. |
| **AgentImage** | URI-installatie kopie van IoT Edge agent | Een nieuwe IoT Edge-installatie maakt standaard gebruik van de nieuwste roulerende tag voor de installatie kopie van de IoT Edge agent. Gebruik deze para meter om een specifieke tag voor de installatie kopie versie in te stellen of om uw eigen agent installatie kopie op te geven. Zie voor meer informatie [IOT Edge Tags begrijpen](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Gebruikersnaam** | Gebruikers naam container register | Gebruik deze para meter alleen als u de para meter-AgentImage instelt op een container in een persoonlijk REGI ster. Geef een gebruikers naam op die toegang heeft tot het REGI ster. |
| **Wachtwoord** | Teken reeks met beveiligd wacht woord | Gebruik deze para meter alleen als u de para meter-AgentImage instelt op een container in een persoonlijk REGI ster. Geef het wacht woord op voor toegang tot het REGI ster. |

## <a name="update-iotedge"></a>Update-IoTEdge

| Parameter | Geaccepteerde waarden | Opmerkingen |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** of **Linux** | Als er geen container besturingssysteem is opgegeven, is Windows de standaard waarde. Voor Windows-containers wordt een container engine opgenomen in de installatie. Voor Linux-containers moet u een container Engine installeren voordat u de installatie start. |
| **Proxy** | Proxy-URL | Neem deze para meter op als uw apparaat een proxy server moet door lopen om het Internet te bereiken. Zie [een IOT edge apparaat configureren om te communiceren via een proxy server](how-to-configure-proxy-support.md)voor meer informatie. |
| **InvokeWebRequestParameters** | Hashtabel van para meters en waarden | Tijdens de installatie worden er diverse webaanvragen gedaan. Gebruik dit veld om para meters in te stellen voor deze webaanvragen. Deze para meter is handig voor het configureren van referenties voor proxy servers. Zie [een IOT edge apparaat configureren om te communiceren via een proxy server](how-to-configure-proxy-support.md)voor meer informatie. |
| **OfflineInstallationPath** | Mappad | Als deze para meter is opgenomen, controleert het installatie programma de vermelde map voor de IoT Edge cab-en VC runtime-bestanden die nodig zijn voor de installatie. Bestanden die niet in de map zijn gevonden, worden gedownload. Als beide bestanden zich in de map bevinden, kunt u IoT Edge installeren zonder een Internet verbinding. U kunt deze para meter ook gebruiken om een specifieke versie te gebruiken. |
| **RestartIfNeeded** | geen | Met deze vlag kan het implementatie script de computer opnieuw opstarten zonder te vragen, indien nodig. |

## <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| Parameter | Geaccepteerde waarden | Opmerkingen |
| --------- | --------------- | -------- |
| **Verkopers** | geen | Met deze vlag wordt de verwijdering afgedwongen als de vorige poging om te verwijderen is mislukt.
| **RestartIfNeeded** | geen | Met deze vlag kan het uninstall-script de computer opnieuw opstarten zonder te vragen, indien nodig. |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het gebruik van deze opdrachten in het volgende artikel:

* [Azure IoT Edge voor Windows installeren of verwijderen](how-to-install-iot-edge-windows-on-windows.md)
