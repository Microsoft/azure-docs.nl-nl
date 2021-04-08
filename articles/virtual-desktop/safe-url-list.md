---
title: Lijst met vereiste URL'S voor Windows virtueel bureau blad-Azure
description: Een lijst met Url's die u moet deblokkeren om ervoor te zorgen dat de implementatie van Windows virtueel bureau blad werkt zoals bedoeld.
author: Heidilohr
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 872a67d8d9f41c0c809df54304352b2a5f58e011
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98251757"
---
# <a name="required-url-list"></a>Vereiste URL-lijst

Als u Windows virtueel bureau blad wilt implementeren en gebruiken, moet u bepaalde Url's deblokkeren zodat uw virtuele machines (Vm's) op elk gewenst moment toegang hebben. Dit artikel bevat een lijst met de vereiste Url's die u moet deblokkeren om ervoor te zorgen dat virtuele Windows-Bureau bladen goed werken. 

>[!IMPORTANT]
>Windows Virtual Desktop biedt geen ondersteuning voor implementaties die de Url's blok keren die in dit artikel worden vermeld.

## <a name="virtual-machines"></a>Virtuele machines

De virtuele machines van Azure die u voor virtuele Windows-Bureau bladen maakt, moeten toegang hebben tot de volgende Url's in de commerciële cloud van Azure:

|Adres|Uitgaande TCP-poort|Doel|Servicetag|
|---|---|---|---|
|*.wvd.microsoft.com|443|Serviceverkeer|WindowsVirtualDesktop|
|gcs.prod.monitoring.core.windows.net|443|Agent-verkeer|AzureCloud|
|production.diagnostics.monitoring.core.windows.net|443|Agent-verkeer|AzureCloud|
|* xt.blob.core.windows.net|443|Agent-verkeer|AzureCloud|
|* eh.servicebus.windows.net|443|Agent-verkeer|AzureCloud|
|* xt.table.core.windows.net|443|Agent-verkeer|AzureCloud|
|* xt.queue.core.windows.net|443|Agent-verkeer|AzureCloud|
|catalogartifact.azureedge.net|443|Azure Marketplace|AzureCloud|
|kms.core.windows.net|1688|Windows-activering|Internet|
|mrsglobalsteus2prod.blob.core.windows.net|443|Updates van Agent- en SXS-stack|AzureCloud|
|wvdportalstorageblob.blob.core.windows.net|443|Ondersteuning Azure-portal|AzureCloud|
| 169.254.169.254 | 80 | [Azure instance meta data service-eind punt](../virtual-machines/windows/instance-metadata-service.md) | N.v.t. |
| 168.63.129.16 | 80 | [Status controle van sessie-host](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | N.v.t. |

>[!IMPORTANT]
>Windows Virtual Desktop biedt nu ondersteuning voor de FQDN-tag. Zie [Azure Firewall gebruiken om Windows Virtual Desktop-implementaties te beveiligen](../firewall/protect-windows-virtual-desktop.md) voor meer informatie.
>
>We raden u aan om FQDN-tags of servicetags te gebruiken in de plaats van URL's om serviceproblemen te vermijden. De vermelde URL's en tags komen enkel overeen met Windows Virtual Desktop-sites en -resources. Ze bevatten geen URL's voor andere services zoals Azure Active Directory.

De virtuele machines van Azure die u voor virtuele Windows-Bureau bladen maakt, moeten toegang hebben tot de volgende Url's in de Azure Government Cloud:

|Adres|Uitgaande TCP-poort|Doel|Servicetag|
|---|---|---|---|
|*. wvd.microsoft.us|443|Serviceverkeer|WindowsVirtualDesktop|
|gcs.monitoring.core.usgovcloudapi.net|443|Agent-verkeer|AzureCloud|
|monitoring.core.usgovcloudapi.net|443|Agent-verkeer|AzureCloud|
|fairfax.warmpath.usgovcloudapi.net|443|Agent-verkeer|AzureCloud|
|* xt.blob.core.usgovcloudapi.net|443|Agent-verkeer|AzureCloud|
|*. servicebus.usgovcloudapi.net|443|Agent-verkeer|AzureCloud|
|* xt.table.core.usgovcloudapi.net|443|Agent-verkeer|AzureCloud|
|Kms.core.usgovcloudapi.net|1688|Windows-activering|Internet|
|mrsglobalstugviffx.blob.core.usgovcloudapi.net|443|Updates van Agent- en SXS-stack|AzureCloud|
|wvdportalstorageblob.blob.core.usgovcloudapi.net|443|Ondersteuning Azure-portal|AzureCloud|
| 169.254.169.254 | 80 | [Azure instance meta data service-eind punt](../virtual-machines/windows/instance-metadata-service.md) | N.v.t. |
| 168.63.129.16 | 80 | [Status controle van sessie-host](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | N.v.t. |

In de volgende tabel vindt u optionele URL's waar uw virtuele Azure-machines toegang tot kunnen hebben.

|Adres|Uitgaande TCP-poort|Doel|Azure-gov|
|---|---|---|---|
|*.microsoftonline.com|443|Verificatie voor Microsoft Online Services|login.microsoftonline.us|
|*.events.data.microsoft.com|443|Telemetrieservice|Geen|
|www.msftconnecttest.com|443|Detecteert of het besturingssysteem een verbinding heeft met het internet|Geen|
|*.prod.do.dsp.mp.microsoft.com|443|Windows Update|Geen|
|login.windows.net|443|Aanmelden bij Microsoft Online Services, Microsoft 365|login.microsoftonline.us|
|*.sfx.ms|443|Updates voor OneDrive-clientsoftware|oneclient.sfx.ms|
|*.digicert.com|443|Controle van certificaatintrekking|Geen|
|*. azure-dns.com|443|Azure DNS resolutie|Geen|
|*. azure-dns.net|443|Azure DNS resolutie|Geen|

>[!NOTE]
>Het virtuele bureau blad van Windows heeft momenteel geen lijst met IP-adresbereiken die u kunt blok keren om netwerk verkeer toe te staan. Er wordt momenteel alleen ondersteuning geboden voor het opheffen van de blok kering van specifieke Url's.
>
>Als u een Next Generation firewall (NGFW) gebruikt, moet u een dynamische lijst gebruiken die speciaal is gemaakt voor Azure Ip's om ervoor te zorgen dat u verbinding kunt maken.
>
>Zie [Office 365 url's en IP-](/office365/enterprise/urls-and-ip-address-ranges)adresbereiken voor een lijst met veilige Office-url's, met inbegrip van vereiste url's voor Azure Active Directory.
>
>U moet het jokerteken (*) gebruiken voor URL's met serviceverkeer. Als u liever geen * gebruikt voor agent-gerelateerd verkeer, dan kunt u als volgt URL's zonder jokers vinden:
>
>1. Registreer uw virtuele machines bij de Windows Virtual Desktop-hostgroep.
>2. Open **Logboeken** en ga naar **Windows logs**  >  **Application**  >  **WVD-agent** en zoek naar gebeurtenis-id 3701.
>3. Deblokkeren van de Url's die u vindt onder gebeurtenis-ID 3701. De Url's onder gebeurtenis-ID 3701 zijn specifiek voor een regio. U moet het deblokkeren van het proces herhalen met de relevante Url's voor elke regio waarin u uw virtuele machines wilt implementeren.

## <a name="remote-desktop-clients"></a>Extern bureaublad-clients

Alle Extern bureaublad-clients die u gebruikt, moeten toegang hebben tot de volgende Url's:

|Adres|Uitgaande TCP-poort|Doel|Client(s)|Azure-gov|
|---|---|---|---|---|
|*.wvd.microsoft.com|443|Serviceverkeer|Alle|*. wvd.microsoft.us|
|*.servicebus.windows.net|443|Probleemoplossingsgegevens|Alle|*. servicebus.usgovcloudapi.net|
|go.microsoft.com|443|Microsoft FWLinks|Alle|Geen|
|aka.ms|443|Microsoft URL-verkorter|Alle|Geen|
|docs.microsoft.com|443|Documentatie|Alle|Geen|
|privacy.microsoft.com|443|Privacyverklaring|Alle|Geen|
|query.prod.cms.rt.microsoft.com|443|Clientupdates|Windows-pc|Geen|

>[!IMPORTANT]
>Het openen van deze URL's is essentieel voor een betrouwbare clientervaring. Toegang blokkeren tot deze URL's wordt niet ondersteund en heeft gevolgen voor de functionaliteit van de service.
>
>Deze Url's komen alleen overeen met client sites en-resources. Deze lijst bevat geen Url's voor andere services, zoals Azure Active Directory. Azure Active Directory Url's vindt u onder ID 56 in de [Office 365-url's en IP-](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online)adresbereiken.