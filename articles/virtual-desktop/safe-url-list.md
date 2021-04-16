---
title: Windows Virtual Desktop lijst met vereiste URL's - Azure
description: Een lijst met URL's die u moet deblokkeren om ervoor te zorgen Windows Virtual Desktop implementatie werkt zoals bedoeld.
author: Heidilohr
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 00ae761af44b9e6537149c96607c0ba00e6439c8
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514976"
---
# <a name="required-url-list"></a>Vereiste URL-lijst

Als u een Windows Virtual Desktop wilt implementeren en gebruiken, moet u bepaalde URL's deblokkeren zodat uw virtuele machines (VM's) er op elk gewenst moment toegang toe hebben. In dit artikel worden de vereiste URL's vermeld die u moet deblokkeren om ervoor te zorgen Windows Virtual Desktop goed werkt. 

>[!IMPORTANT]
>Windows Virtual Desktop biedt geen ondersteuning voor implementaties die de URL's blokkeren die in dit artikel worden vermeld.

## <a name="required-url-check-tool"></a>Vereist hulpprogramma voor URL-controle

Het hulpprogramma Vereiste URL-controle valideert URL's en geeft weer of de URL's die de virtuele machine nodig heeft, toegankelijk zijn. Zo niet, dan vermeldt het hulpprogramma de niet-toegankelijke URL's, zodat u deze zo nodig kunt deblokkeren.

Het is belangrijk dat u rekening houdt met het volgende:

- U kunt het hulpprogramma Required URL Check alleen gebruiken voor implementaties in commerciële clouds.
- Het hulpprogramma Vereiste URL-controle kan URL's met jokertekens niet controleren, dus zorg ervoor dat u deze URL's eerst deblokkeert.

### <a name="requirements"></a>Vereisten

U hebt het volgende nodig om het hulpprogramma Vereiste URL-controle te gebruiken:

- Uw VM moet een .NET 4.6.2-framework hebben
- RDAgent versie 1.0.2944.400 of hoger
- Het WVDAgentUrlTool.exe moet zich in dezelfde map als het WVDAgentUrlTool.config-bestand

### <a name="how-to-use-the-required-url-check-tool"></a>Het hulpprogramma Vereiste URL-controle gebruiken

Het hulpprogramma Vereiste URL-controle gebruiken:

1. Open een opdrachtprompt als beheerder op uw VM.
2. Voer de volgende opdracht uit om de map te wijzigen in dezelfde map als de buildagent:

    ```console
    cd C:\Program Files\Microsoft RDInfra\RDAgent_1.0.2944.1200
    ```

3. Voer de volgende opdracht uit:

    ```console
    WVDAgentUrlTool.exe
    ```
 
4. Zodra u het bestand hebt uitgevoerd, ziet u een lijst met toegankelijke en ontoegankelijke URL's.

    In de volgende schermopname ziet u bijvoorbeeld een scenario waarin u twee vereiste URL's zonder jokertekens moet deblokkeren:

    > [!div class="mx-imgBorder"]
    > ![Schermopname van niet-toegankelijke URL-uitvoer.](media/noaccess.png)
    
    Zo zou de uitvoer eruit moeten zien nadat u alle vereiste URL's zonder jokertekens hebt geblokkeerd:

    > [!div class="mx-imgBorder"]
    > ![Schermopname van de uitvoer van toegankelijke URL's.](media/access.png)

## <a name="virtual-machines"></a>Virtuele machines

De virtuele Azure-machines die u Windows Virtual Desktop, moeten toegang hebben tot de volgende URL's in de commerciële cloud van Azure:

|Adres|Uitgaande TCP-poort|Doel|Servicetag|
|---|---|---|---|
|*.wvd.microsoft.com|443|Serviceverkeer|WindowsVirtualDesktop|
|gcs.prod.monitoring.core.windows.net|443|Agent-verkeer|AzureCloud|
|production.diagnostics.monitoring.core.windows.net|443|Agent-verkeer|AzureCloud|
|*xt.blob.core.windows.net|443|Agent-verkeer|AzureCloud|
|*eh.servicebus.windows.net|443|Agent-verkeer|AzureCloud|
|*xt.table.core.windows.net|443|Agent-verkeer|AzureCloud|
|*xt.queue.core.windows.net|443|Agent-verkeer|AzureCloud|
|catalogartifact.azureedge.net|443|Azure Marketplace|AzureCloud|
|kms.core.windows.net|1688|Windows-activering|Internet|
|mrsglobalsteus2prod.blob.core.windows.net|443|Updates van Agent- en SXS-stack|AzureCloud|
|wvdportalstorageblob.blob.core.windows.net|443|Ondersteuning Azure-portal|AzureCloud|
| 169.254.169.254 | 80 | [Azure Instance Metadata Service-eindpunt](../virtual-machines/windows/instance-metadata-service.md) | N.v.t. |
| 168.63.129.16 | 80 | [Statuscontrole van sessiehost](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | N.v.t. |

>[!IMPORTANT]
>Windows Virtual Desktop biedt nu ondersteuning voor de FQDN-tag. Zie [Azure Firewall gebruiken om Windows Virtual Desktop-implementaties te beveiligen](../firewall/protect-windows-virtual-desktop.md) voor meer informatie.
>
>We raden u aan om FQDN-tags of servicetags te gebruiken in de plaats van URL's om serviceproblemen te vermijden. De vermelde URL's en tags komen enkel overeen met Windows Virtual Desktop-sites en -resources. Ze bevatten geen URL's voor andere services zoals Azure Active Directory.

De virtuele Azure-machines die u Windows Virtual Desktop, moeten toegang hebben tot de volgende URL's in Azure Government cloud:

|Adres|Uitgaande TCP-poort|Doel|Servicetag|
|---|---|---|---|
|*.wvd.microsoft.us|443|Serviceverkeer|WindowsVirtualDesktop|
|gcs.monitoring.core.usgovcloudapi.net|443|Agent-verkeer|AzureCloud|
|monitoring.core.usgovcloudapi.net|443|Agent-verkeer|AzureCloud|
|fairfax.warmpath.usgovcloudapi.net|443|Agent-verkeer|AzureCloud|
|*xt.blob.core.usgovcloudapi.net|443|Agent-verkeer|AzureCloud|
|*.servicebus.usgovcloudapi.net|443|Agent-verkeer|AzureCloud|
|*xt.table.core.usgovcloudapi.net|443|Agent-verkeer|AzureCloud|
|Kms.core.usgovcloudapi.net|1688|Windows-activering|Internet|
|mrsglobalstugviffx.blob.core.usgovcloudapi.net|443|Updates van Agent- en SXS-stack|AzureCloud|
|wvdportalstorageblob.blob.core.usgovcloudapi.net|443|Ondersteuning Azure-portal|AzureCloud|
| 169.254.169.254 | 80 | [Azure Instance Metadata-service-eindpunt](../virtual-machines/windows/instance-metadata-service.md) | N.v.t. |
| 168.63.129.16 | 80 | [Statuscontrole van sessiehost](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | N.v.t. |

In de volgende tabel vindt u optionele URL's waar uw virtuele Azure-machines toegang tot kunnen hebben.

|Adres|Uitgaande TCP-poort|Doel|Azure Gov|
|---|---|---|---|
|*.microsoftonline.com|443|Verificatie voor Microsoft Online Services|login.microsoftonline.us|
|*.events.data.microsoft.com|443|Telemetrieservice|Geen|
|www.msftconnecttest.com|443|Detecteert of het besturingssysteem een verbinding heeft met het internet|Geen|
|*.prod.do.dsp.mp.microsoft.com|443|Windows Update|Geen|
|login.windows.net|443|Aanmelden bij Microsoft Online Services, Microsoft 365|login.microsoftonline.us|
|*.sfx.ms|443|Updates voor OneDrive-clientsoftware|oneclient.sfx.ms|
|*.digicert.com|443|Controle van certificaatintrekking|Geen|
|*.azure-dns.com|443|Azure DNS oplossing|Geen|
|*.azure-dns.net|443|Azure DNS oplossing|Geen|

>[!NOTE]
>Windows Virtual Desktop momenteel geen lijst met IP-adresbereiken die u kunt deblokkeren om netwerkverkeer toe te staan. We ondersteunen op dit moment alleen het deblokkeren van specifieke URL's.
>
>Als u een Next Generation Firewall (NGFW) gebruikt, moet u een dynamische lijst gebruiken die speciaal is gemaakt voor Azure-IP's om ervoor te zorgen dat u verbinding kunt maken.
>
>Zie [Office 365-URL's](/office365/enterprise/urls-and-ip-address-ranges)en IP-adresbereiken voor een lijst met veilige, aan Office gerelateerde URL's, inclusief de vereiste URL Azure Active Directory s.
>
>U moet het jokerteken (*) gebruiken voor URL's met serviceverkeer. Als u liever geen * gebruikt voor agent-gerelateerd verkeer, dan kunt u als volgt URL's zonder jokers vinden:
>
>1. Registreer uw virtuele machines bij de Windows Virtual Desktop-hostgroep.
>2. Open **Logboeken,** ga naar **Windows-logboeken**  >  **Toepassing**  >  **WVD-Agent** en zoek gebeurtenis-id 3701.
>3. De blokkering opheffen van de URL's die u vindt onder Gebeurtenis-id 3701. De URL's onder Gebeurtenis-id 3701 zijn regio-specifiek. U moet het blokkeringsproces herhalen met de relevante URL's voor elke regio waarin u uw virtuele machines wilt implementeren.

## <a name="remote-desktop-clients"></a>Extern bureaublad-clients

Alle Extern bureaublad die u gebruikt, moeten toegang hebben tot de volgende URL's:

|Adres|Uitgaande TCP-poort|Doel|Client(s)|Azure Gov|
|---|---|---|---|---|
|*.wvd.microsoft.com|443|Serviceverkeer|Alle|*.wvd.microsoft.us|
|*.servicebus.windows.net|443|Probleemoplossingsgegevens|Alle|*.servicebus.usgovcloudapi.net|
|go.microsoft.com|443|Microsoft FWLinks|Alle|Geen|
|aka.ms|443|Microsoft URL-verkorter|Alle|Geen|
|docs.microsoft.com|443|Documentatie|Alle|Geen|
|privacy.microsoft.com|443|Privacyverklaring|Alle|Geen|
|query.prod.cms.rt.microsoft.com|443|Clientupdates|Windows-pc|Geen|

>[!IMPORTANT]
>Het openen van deze URL's is essentieel voor een betrouwbare clientervaring. Toegang blokkeren tot deze URL's wordt niet ondersteund en heeft gevolgen voor de functionaliteit van de service.
>
>Deze URL's komen alleen overeen met clientsites en -resources. Deze lijst bevat geen URL's voor andere services, zoals Azure Active Directory. Azure Active Directory-URL's vindt u onder ID 56 in de [Office 365-URL's en IP-adresbereiken.](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online)
