---
title: Power shell-scripts voor Azure IoT Edge voor Linux op Windows | Microsoft Docs
description: Naslag informatie voor Azure IoT Edge voor Linux op Windows Power shell-scripts voor het implementeren, inrichten en status IoT Edge voor Linux op virtuele Windows-machines.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 02/16/2021
ms.topic: reference
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f0af571f67862c91371b01bee6227d5fb6b291be
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746098"
---
# <a name="powershell-scripts-for-iot-edge-for-linux-on-windows"></a>Power shell-scripts voor IoT Edge voor Linux in Windows

Meer informatie over de Power shell-scripts waarmee u de status van uw IoT Edge voor Linux op virtuele Windows-machines kunt implementeren, inrichten en ophalen.

De opdrachten die in dit artikel worden beschreven, zijn afkomstig uit het `AzureEFLOW.psm1` bestand, dat u kunt vinden op uw systeem in uw `WindowsPowerShell` Directory `C:\Program Files\WindowsPowerShellModules\AzureEFLOW` .

## <a name="deploy-eflow"></a>Deploy-Eflow

De opdracht **Deploy-Eflow** is de belangrijkste implementatie methode. Met de implementatie opdracht maakt u de virtuele machine, stelt u bestanden in en implementeert u de module IoT Edge agent. Hoewel geen van de para meters vereist zijn, kunnen ze worden gebruikt voor het inrichten van uw IoT Edge-apparaat tijdens de implementatie en tijdens het maken van de instellingen voor de virtuele machine. Gebruik de opdracht voor een volledige lijst `Get-Help Deploy-Eflow -full` .  

>[!NOTE]
>Voor het inrichtings type verwijst **x509** momenteel uitsluitend naar x509-inrichting met behulp van een [Azure-IOT hub Device Provisioning Service](../iot-dps/about-iot-dps.md). De hand matige x509-inrichtings methode wordt momenteel niet ondersteund.

| Parameter | Geaccepteerde waarden | Opmerkingen |
| --------- | --------------- | -------- |
| eflowVhdxDir | Mappad | De map waar het VHDX-bestand voor de VM wordt opgeslagen. |
| provisioningType | **hand matig**, **TPM**, **x509** of **symmetrisch** |  Hiermee definieert u het type inrichting dat u wilt gebruiken voor uw IoT Edge-apparaat. |
| devConnString | Het apparaat connection string van een bestaand IoT Edge apparaat | Apparaat connection string voor het hand matig inrichten van een IoT Edge apparaat (**hand matig**). |
| symmKey | De primaire sleutel voor een bestaande DPS-inschrijving of de primaire sleutel van een bestaand IoT Edge apparaat dat is geregistreerd met behulp van symmetrische sleutels | Symmetrische sleutel voor het inrichten van een IoT Edge apparaat (**x509** of **symmetrisch**). |
| Scope | De bereik-ID voor een bestaande DPS-instantie. | De bereik-ID voor het inrichten van een IoT Edge apparaat (**x509** of **symmetrisch**). |
| Registratie | De registratie-ID van een bestaand IoT Edge apparaat | Registratie-ID voor het inrichten van een IoT Edge apparaat (**x509** of **symmetrisch**). |
| identityCertLocVm | Mappad; moet zich in een map bevindt die eigendom kan zijn van de `iotedge` service | Het absolute doelpad van het identiteits certificaat op uw virtuele machine voor het inrichten van een IoT Edge apparaat (**x509** of **symmetrisch**). |
| identityCertLocWin | Mappad | Het absolute bronpad van het identiteits certificaat in Windows voor het inrichten van een IoT Edge apparaat (**x509** of **symmetrisch**). |
| identityPkLocVm |  | Mappad; moet zich in een map bevindt die eigendom kan zijn van de `iotedge` service | Absoluut doelpad van de identiteits-persoonlijke sleutel op uw virtuele machine voor het inrichten van een IoT Edge apparaat (**x509** of **symmetrisch**). |
| identityPkLocWin | Mappad | Het absolute bronpad van de identiteits-persoonlijke sleutel in Windows voor het inrichten van een IoT Edge apparaat (**x509** of **symmetrisch**). |
| vmSizeDefintion | Maxi maal 30 tekens | Definitie van het aantal kernen en het beschik bare RAM-geheugen voor de virtuele machine. **Standaard waarde**: Standard_K8S_v1. |
| vmDiskSize | Tussen 8 GB en 256 GB | Maximale schijf grootte van de dynamisch uitbreid bare virtuele harde schijf. **Standaard waarde**: 16 GB. |
| vmUser | Maxi maal 30 tekens | Gebruikers naam voor aanmelding bij de virtuele machine. |
| vnetType | **Transparant** of **ICS** | Het type virtuele switch. **Standaard waarde**: transparant. |
| vnetName | Maxi maal 64 tekens | De naam van de virtuele switch. **Standaard waarde**: extern. |
| enableVtpm | Geen | **Switch-para meter**. Maak de virtuele machine met TPM ingeschakeld of uitgeschakeld. |
| mobyPackageVersion | Maxi maal 30 tekens |  De versie van het Moby-pakket dat moet worden gecontroleerd of geïnstalleerd op de virtuele machine.  **Standaard waarde:** 19.03.11. |
| iotedgePackageVersion | Maxi maal 30 tekens | Versie van IoT Edge pakket dat moet worden gecontroleerd of geïnstalleerd op de virtuele machine. **Standaard waarde:** 1.1.0. |
| installPackages | Geen | **Switch-para meter**. Wanneer u deze optie inschakelt, probeert het script de Moby-en IoT Edge-pakketten te installeren in plaats van alleen te controleren of de pakketten aanwezig zijn. |

## <a name="verify-eflowvm"></a>Verify-EflowVm

De opdracht **verify-EflowVm** is een beschik bare functie waarmee u kunt controleren of de IOT Edge voor Linux op virtuele Windows-machines is gemaakt. Het kost alleen algemene para meters en retourneert **waar** als de virtuele machine is gemaakt en **Onwaar** als dat niet het geval is. Gebruik de opdracht voor aanvullende informatie `Get-Help Verify-EflowVm -full` .

## <a name="provision-eflowvm"></a>Provision-EflowVm

Met de opdracht **inrichten-EflowVm** worden de inrichtings gegevens voor uw IOT edge apparaat toegevoegd aan het IOT Edge-bestand van de virtuele machine `config.yaml` . Het inrichten kan ook worden uitgevoerd tijdens de implementatie fase door para meters in te stellen in de opdracht **Deploy-Eflow** . Gebruik de opdracht voor aanvullende informatie `Get-Help Provision-EflowVm -full` .

| Parameter | Geaccepteerde waarden | Opmerkingen |
| --------- | --------------- | -------- |
| vmUser | Maxi maal 30 tekens | Gebruikers naam voor aanmelding bij de virtuele machine. |
| provisioningType | **hand matig**, **TPM**, **x509** of **symmetrisch** |  Hiermee definieert u het type inrichting dat u wilt gebruiken voor uw IoT Edge-apparaat. |
| devConnString | Het apparaat connection string van een bestaand IoT Edge apparaat | Apparaat connection string voor het hand matig inrichten van een IoT Edge apparaat (**hand matig**). |
| symmKey | De primaire sleutel voor een bestaande DPS-inschrijving of de primaire sleutel van een bestaand IoT Edge apparaat dat is geregistreerd met behulp van symmetrische sleutels | Symmetrische sleutel voor het inrichten van een IoT Edge apparaat (**DPS**, **symmetrisch**). |
| Scope | De bereik-ID voor een bestaande DPS-instantie. | Bereik-ID voor het inrichten van een IoT Edge apparaat (**DPS**). |
| Registratie | De registratie-ID van een bestaand IoT Edge apparaat | Registratie-ID voor het inrichten van een IoT Edge apparaat (**DPS**). |
| identityCertLocVm | Mappad; moet zich in een map bevindt die eigendom kan zijn van de `iotedge` service | Het absolute doelpad van het identiteits certificaat op uw virtuele machine voor het inrichten van een IoT Edge apparaat (**DPS**, **x509**). |
| identityCertLocWin | Mappad | Het absolute bronpad van het identiteits certificaat in Windows voor het inrichten van een IoT Edge apparaat (**DPS**, **x509**). |
| identityPkLocVm |  | Mappad; moet zich in een map bevindt die eigendom kan zijn van de `iotedge` service | Absoluut doelpad van de identiteits-persoonlijke sleutel op uw virtuele machine voor het inrichten van een IoT Edge apparaat (**DPS**, **x509**). |
| identityPkLocWin | Mappad | Het absolute bronpad van de identiteits-persoonlijke sleutel in Windows voor het inrichten van een IoT Edge apparaat (**DPS**, **x509**). |

## <a name="get-eflowvmname"></a>Get-EflowVmName

De opdracht **Get-EflowVmName** wordt gebruikt om een query uit te geven op de huidige hostnaam van de virtuele machine. Deze opdracht bestaat uit het feit dat de Windows-hostnaam na verloop van tijd kan worden gewijzigd. Er worden alleen algemene para meters gebruikt, en de huidige hostnaam van de virtuele machine wordt geretourneerd. Gebruik de opdracht voor aanvullende informatie `Get-Help Get-EflowVmName -full` .

## <a name="get-eflowlogs"></a>Get-EflowLogs

De opdracht **Get-EflowLogs** wordt gebruikt voor het verzamelen en bundelen van logboeken van de IOT Edge voor Linux op Windows-implementatie. De gebundelde logboeken worden uitgevoerd in de vorm van een `.zip` map. Gebruik de opdracht voor aanvullende informatie `Get-Help Get-EflowLogs -full` .

| Parameter | Geaccepteerde waarden | Opmerkingen |
| --------- | --------------- | -------- |
| vmUser | Maxi maal 30 tekens | Gebruikers naam voor aanmelding bij de virtuele machine. |

## <a name="get-eflowvmtpmprovisioninginfo"></a>Get-EflowVmTpmProvisioningInfo

De opdracht **Get-EflowVmTpmProvisioningInfo** wordt gebruikt om de vTPM-inrichtings gegevens van de virtuele machine te verzamelen en weer te geven. Als de virtuele machine is gemaakt zonder vTPM, wordt met de opdracht geretourneerd dat de TPM-inrichtings informatie niet kan worden gevonden. Gebruik de opdracht voor aanvullende informatie `Get-Help Get-EflowVmTpmProvisioningInfo -full` .

| Parameter | Geaccepteerde waarden | Opmerkingen |
| --------- | --------------- | -------- |
| vmUser | Maxi maal 30 tekens | Gebruikers naam voor aanmelding bij de virtuele machine. |

## <a name="get-eflowvmaddr"></a>Get-EflowVmAddr

De opdracht **Get-EflowVmAddr** wordt gebruikt om de IP-en MAC-adressen van de virtuele machine te zoeken en weer te geven. Er worden alleen algemene para meters gebruikt. Gebruik de opdracht voor aanvullende informatie `Get-Help Get-EflowVmAddr -full` .

## <a name="get-eflowvmsysteminformation"></a>Get-EflowVmSystemInformation

De opdracht **Get-EflowVmSystemInformation** wordt gebruikt voor het verzamelen en weer geven van systeem gegevens van de virtuele machine, zoals geheugen en opslag gebruik. Gebruik de opdracht voor aanvullende informatie `Get-Help Get-EflowVmSystemInformation -full` .

| Parameter | Geaccepteerde waarden | Opmerkingen |
| --------- | --------------- | -------- |
| vmUser | Maxi maal 30 tekens | Gebruikers naam voor aanmelding bij de virtuele machine. |

## <a name="get-eflowvmedgeinformation"></a>Get-EflowVmEdgeInformation

De opdracht **Get-EflowVmEdgeInformation** wordt gebruikt voor het verzamelen en weer geven van IOT Edge informatie van de virtuele machine, zoals de versie van IOT Edge de virtuele machine wordt uitgevoerd. Gebruik de opdracht voor aanvullende informatie `Get-Help Get-EflowVmEdgeInformation -full` .

| Parameter | Geaccepteerde waarden | Opmerkingen |
| --------- | --------------- | -------- |
| vmUser | Maxi maal 30 tekens | Gebruikers naam voor aanmelding bij de virtuele machine. |

## <a name="get-eflowvmedgemodulelist"></a>Get-EflowVmEdgeModuleList

De opdracht **Get-EflowVmEdgeModuleList** wordt gebruikt om de lijst met IOT Edge-modules die op de virtuele machine worden uitgevoerd, op te vragen en weer te geven. Gebruik de opdracht voor aanvullende informatie `Get-Help Get-EflowVmEdgeModuleList -full` .

| Parameter | Geaccepteerde waarden | Opmerkingen |
| --------- | --------------- | -------- |
| vmUser | Maxi maal 30 tekens | Gebruikers naam voor aanmelding bij de virtuele machine. |

## <a name="get-eflowvmedgestatus"></a>Get-EflowVmEdgeStatus

De opdracht **Get-EflowVmEdgeStatus** wordt gebruikt om de status van IOT Edge runtime op de virtuele machine op te vragen en weer te geven. Gebruik de opdracht voor aanvullende informatie `Get-Help Get-EflowVmEdgeStatus -full` .

| Parameter | Geaccepteerde waarden | Opmerkingen |
| --------- | --------------- | -------- |
| vmUser | Maxi maal 30 tekens | Gebruikers naam voor aanmelding bij de virtuele machine. |

## <a name="get-eflowvmusername"></a>Get-EflowVmUserName

De opdracht **Get-EflowVmUserName** wordt gebruikt om de gebruikers naam van de virtuele machine die is gebruikt voor het maken van de virtuele machine uit het REGI ster, te zoeken en weer te geven. Er worden alleen algemene para meters gebruikt. Gebruik de opdracht voor aanvullende informatie `Get-Help Get-EflowVmUserName -full` .

## <a name="get-eflowvmsshkey"></a>Get-EflowVmSshKey

De opdracht **Get-EflowVmSshKey** wordt gebruikt voor het opvragen en weer geven van de SSH-sleutel die wordt gebruikt door de virtuele machine. Er worden alleen algemene para meters gebruikt. Gebruik de opdracht voor aanvullende informatie `Get-Help Get-EflowVmSshKey -full` .

## <a name="ssh-eflowvm"></a>Ssh-EflowVm

De **SSH-EflowVm-** opdracht wordt gebruikt om te SSHen naar de virtuele machine. Gebruik de opdracht voor aanvullende informatie `Get-Help Ssh-EflowVm -full` .

| Parameter | Geaccepteerde waarden | Opmerkingen |
| --------- | --------------- | -------- |
| vmUser | Maxi maal 30 tekens | Gebruikers naam voor aanmelding bij de virtuele machine. |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het gebruik van deze opdrachten in het volgende artikel:

* [Azure IoT Edge voor Linux installeren op Windows](how-to-install-iot-edge-windows.md)

* Raadpleeg [de naslag informatie over het IOT Edge voor Linux op Windows Power shell-scripts](reference-iot-edge-for-linux-on-windows-scripts.md#Deploy-Eflow) voor alle opdrachten die beschikbaar zijn via Power shell.
