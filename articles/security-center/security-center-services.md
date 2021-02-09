---
title: De functies van Azure Security Center, afhankelijk van het besturingssysteem, het machinetype en de cloud
description: Meer informatie over welke Azure Security Center-functies beschikbaar zijn per besturingssysteem, type en cloud-implementatie.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 870ebc8d-1fad-435b-9bf9-c477f472ab17
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2021
ms.author: memildin
ms.openlocfilehash: e827178d8ccb0f7de8d32433d03502a7412d1139
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99834047"
---
# <a name="feature-coverage-for-machines"></a>Functiedekking voor machines

De twee tabbladen hieronder tonen de functies van Azure Security Center die beschikbaar zijn voor virtuele Windows- en Linux-machines en -servers.

## <a name="supported-features-for-virtual-machines-and-servers"></a>Ondersteunde functies voor virtuele machines en servers <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Windows-machines**](#tab/features-windows)

|**Functie**|**Azure Virtual Machines**|**Virtuele Azure-machineschaalsets**|**Machines met Azure Arc**|**Azure Defender vereist**
|----|:----:|:----:|:----:|:----:|
|[Microsoft Defender for Endpoint-integratie](security-center-wdatp.md)|✔</br>(in ondersteunde versies)|✔</br>(in ondersteunde versies)|✔|Ja|
|[Gedragsanalyse van virtuele machine (en beveiligingswaarschuwingen)](alerts-reference.md)|✔|✔|✔|Ja|
|[Bestandsloze beveiligingswaarschuwingen](alerts-reference.md#alerts-windows)|✔|✔|✔|Ja|
|[Op netwerk gebaseerde beveiligingswaarschuwingen](other-threat-protections.md#network-layer)|✔|✔|-|Ja|
|[Just-in-time-toegang voor virtuele machines](security-center-just-in-time.md)|✔|-|-|Ja|
|[Systeemeigen evaluatie van beveiligingsproblemen](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|✔|Ja|
|[Bestandsintegriteit controleren](security-center-file-integrity-monitoring.md)|✔|✔|✔|Ja|
|[Adaptieve toepassingsbesturingselementen](security-center-adaptive-application.md)|✔|-|✔|Ja|
|[Netwerkoverzicht](security-center-network-recommendations.md#network-map)|✔|✔|-|Ja|
|[Adaptieve netwerkbeveiliging](security-center-adaptive-network-hardening.md)|✔|-|-|Ja|
|[Dashboard en rapporten voor naleving van regelgeving](security-center-compliance-dashboard.md)|✔|✔|✔|Ja|
|Aanbevelingen en bedreigingsbeveiliging in door Docker gehoste IaaS-containers|-|-|-|Ja|
|Evaluatie van ontbrekende OS-patches|✔|✔|✔|Azure: Nee<br><br>Met Arc: Ja|
|Evaluatie van onjuiste beveiligingsconfiguratie|✔|✔|✔|Azure: Nee<br><br>Met Arc: Ja|
|[Evaluatie van eindpuntbeveiliging](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure: Nee<br><br>Met Arc: Ja|
|Evaluatie van schijfversleuteling|✔</br>(voor [ondersteunde scenario's](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios))|✔|-|Nee|
|Evaluatie van beveiligingsproblemen van derden|✔|-|✔|Nee|
|[Evaluatie van netwerkbeveiliging](security-center-network-recommendations.md)|✔|✔|-|Nee|


### <a name="linux-machines"></a>[**Linux-machines**](#tab/features-linux)

|**Functie**|**Azure Virtual Machines**|**Virtuele Azure-machineschaalsets**|**Machines met Azure Arc**|**Azure Defender vereist**
|----|:----:|:----:|:----:|:----:|
|[Microsoft Defender for Endpoint-integratie](security-center-wdatp.md)|-|-|-|-|
|[Gedragsanalyse van virtuele machine (en beveiligingswaarschuwingen)](./azure-defender.md)|✔</br>(in ondersteunde versies)|✔</br>(in ondersteunde versies)|✔|Ja|
|[Bestandsloze beveiligingswaarschuwingen](alerts-reference.md#alerts-windows)|-|-|-|Ja|
|[Op netwerk gebaseerde beveiligingswaarschuwingen](other-threat-protections.md#network-layer)|✔|✔|-|Ja|
|[Just-in-time-toegang voor virtuele machines](security-center-just-in-time.md)|✔|-|-|Ja|
|[Systeemeigen evaluatie van beveiligingsproblemen](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|✔|Ja|
|[Bestandsintegriteit controleren](security-center-file-integrity-monitoring.md)|✔|✔|✔|Ja|
|[Adaptieve toepassingsbesturingselementen](security-center-adaptive-application.md)|✔|-|✔|Ja|
|[Netwerkoverzicht](security-center-network-recommendations.md#network-map)|✔|✔|-|Ja|
|[Adaptieve netwerkbeveiliging](security-center-adaptive-network-hardening.md)|✔|-|-|Ja|
|[Dashboard en rapporten voor naleving van regelgeving](security-center-compliance-dashboard.md)|✔|✔|✔|Ja|
|Aanbevelingen en bedreigingsbeveiliging in door Docker gehoste IaaS-containers|✔|✔|✔|Ja|
|Evaluatie van ontbrekende OS-patches|✔|✔|✔|Azure: Nee<br><br>Met Arc: Ja|
|Evaluatie van onjuiste beveiligingsconfiguratie|✔|✔|✔|Azure: Nee<br><br>Met Arc: Ja|
|[Evaluatie van eindpuntbeveiliging](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Nee|
|Evaluatie van schijfversleuteling|✔</br>(voor [ondersteunde scenario's](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios))|✔|-|Nee|
|Evaluatie van beveiligingsproblemen van derden|✔|-|✔|Nee|
|[Evaluatie van netwerkbeveiliging](security-center-network-recommendations.md)|✔|✔|-|Nee|

--- 


> [!TIP]
>Als u wilt experimenteren met functies die alleen beschikbaar zijn met Azure Defender, kunt u zich inschrijven voor een proefversie van 30 dagen. Zie de pagina [prijzen](https://azure.microsoft.com/pricing/details/security-center/) voor meer informatie.


## <a name="supported-endpoint-protection-solutions"></a>Ondersteunde oplossingen voor eindpuntbeveiliging<a name="endpoint-supported"></a>

De volgende tabel bevat een matrix van:

 - Of u Azure Security Center kunt gebruiken om elke oplossing voor u te installeren.
 - Welke oplossingen voor eindpuntbeveiliging Security Center kan detecteren. Als er een oplossing voor eindpuntbeveiliging uit deze lijst wordt gedetecteerd, raadt Security Center niet aan er een te installeren.

Zie [Evaluatie van eindpuntbeveiliging en aanbevelingen](security-center-endpoint-protection.md) voor meer informatie over wanneer er aanbevelingen worden gegenereerd voor elk van deze soorten beveiliging.

| Endpoint Protection| Platformen | Security Center-installatie | Security Center Discovery |
|------|------|-----|-----|
| Microsoft Defender Antivirus| Windows Server 2016 of hoger| Nee, ingebouwd in besturingssysteem| Ja |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2, 2012, 2008 R2 (zie opmerking hieronder) | Via extensie | Ja |
| Trend Micro - Deep Security | Windows Server-familie  | Nee | Ja |
| Symantec v12.1.1100+| Windows Server-familie  | Nee | Ja |
| McAfee v10+ | Windows Server-familie  | Nee | Ja |
| McAfee v10+ | Linux Server-familie  | Nee | Ja **\*** |
| Sophos V9+| Linux Server-familie  | Nee | Ja  **\***  |

 **\*** De status van de dekking en de ondersteunende gegevens zijn momenteel alleen beschikbaar in de Log Analytics-werkruimte die aan uw beveiligde abonnementen is gekoppeld. Deze wordt niet weergegeven in de Azure Security Center-portal.

> [!NOTE]
> Voor de detectie van System Center Endpoint Protection (SCEP) op een virtuele machine met Windows Server 2008 R2 moet SCEP worden geïnstalleerd na PowerShell (v 3.0 of nieuwer).



## <a name="feature-support-in-government-clouds"></a>Functieondersteuning in overheidsclouds

| Service / functie | US Gov | China Gov |
|------|:----:|:----:|
|[Just-in-time-toegang voor virtuele machines](security-center-just-in-time.md) (1)|✔|✔|
|[Controle van de bestandsintegriteit](security-center-file-integrity-monitoring.md) (1)|✔|✔|
|[Adaptieve toepassingsregelaars](security-center-adaptive-application.md) (1)|✔|✔|
|[Adaptieve netwerkbeveiliging](security-center-adaptive-network-hardening.md) (1)|-|-|
|[Docker-hostbeveiliging](harden-docker-hosts.md) (1)|✔|✔|
|[Geïntegreerde evaluatie van beveiligingsproblemen voor machines](deploy-vulnerability-assessment-vm.md) (1)|-|-|
|[Microsoft Defender for Endpoint](harden-docker-hosts.md) (1)|✔|-|
|[Verbinding maken met AWS-accounts](quickstart-onboard-aws.md) (1)|-|-|
|[Verbinding maken met GCP-accounts](quickstart-onboard-gcp.md) (1)|-|-|
|[Continue export](continuous-export.md)|✔|✔ (2)|
|[Werkstroomautomatisering](workflow-automation.md)|✔|✔|
|[Regels voor uitzonderingen voor aanbevelingen](exempt-resource.md)|-|-|
|[Regels voor waarschuwingsonderdrukking](alerts-suppression-rules.md)|✔|✔|
|[E-mailmeldingen voor beveiligingswaarschuwingen](security-center-provide-security-contact-details.md)|✔|✔|
|[Inventarisatie van assets](asset-inventory.md)|✔|✔|
|[Azure Defender voor App Service](defender-for-app-service-introduction.md)|-|-|
|[Azure Defender voor Storage](defender-for-storage-introduction.md)|✔|-|
|[Azure Defender voor SQL](defender-for-sql-introduction.md)|✔|✔ (2)|
|[Azure Defender voor Key Vault](defender-for-key-vault-introduction.md)|-|-|
|[Azure Defender voor Resource Manager](defender-for-resource-manager-introduction.md)|-|-|
|[Azure Defender voor DNS](defender-for-dns-introduction.md)|-|-|
|[Azure Defender voor containerregisters](defender-for-container-registries-introduction.md)|✔ (2)|✔ (2)|
|[Azure Defender voor Kubernetes](defender-for-kubernetes-introduction.md)|✔|✔ (2)|
|[Beveiliging van Kubernetes-werk belasting](kubernetes-workload-protections.md)|-|-|
|||

(1) vereist **Azure Defender voor servers**

(2) Gedeeltelijk


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over hoe [Security Center gegevens verzamelt met behulp van de Log Analytics-agent](security-center-enable-data-collection.md).
- Meer informatie over hoe [Security Center gegevens beheert en beveiligt](security-center-data-security.md).
- Bekijk de [platforms die ondersteuning bieden voor Security Center](security-center-os-coverage.md).