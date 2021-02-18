---
title: Veelgestelde vragen over configuratie-en beheer problemen
description: In dit artikel vindt u de veelgestelde vragen over de configuratie en het beheer van Microsoft Azure Cloud Services.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 04a30a2446061df75d133bdbd088b7e71c59cade
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578213"
---
# <a name="configuration-and-management-issues-for-azure-cloud-services-classic-frequently-asked-questions-faqs"></a>Configuratie-en beheer problemen voor Azure Cloud Services (klassiek): veelgestelde vragen (FAQ)

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.

In dit artikel vindt u veelgestelde vragen over configuratie-en beheer problemen voor [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). U kunt ook de [pagina Cloud Services VM-grootte](cloud-services-sizes-specs.md) raadplegen voor informatie over de grootte.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

**Certificaten**

- [Waarom is de certificaat keten van mijn Cloud service TLS/SSL-certificaat onvolledig?](#why-is-the-certificate-chain-of-my-cloud-service-tlsssl-certificate-incomplete)
- [Wat is het doel van het versleutelings certificaat voor Windows Azure-Hulpprogram Ma's voor uitbrei dingen?](#what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions)
- [Hoe kan ik een aanvraag voor certificaat ondertekening (CSR) zonder ' RDP-ING ' in het exemplaar genereren?](#how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance)
- [Mijn Cloud Service beheer certificaat verloopt. Hoe kan ik deze vernieuwen?](#my-cloud-service-management-certificate-is-expiring-how-to-renew-it)
- [Hoe kan ik de installatie van het hoofd-TLS/SSL-certificaat (. pfx) en het tussenliggende certificaat (. p7b) automatiseren?](#how-to-automate-the-installation-of-main-tlsssl-certificatepfx-and-intermediate-certificatep7b)
- [Wat is het doel van het certificaat ' Microsoft Azure Service Management for MachineKey '?](#what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate)

**Bewaking en registratie**

- [Wat zijn de toekomstige Cloud Services in de Azure Portal die u kunnen helpen bij het beheren en bewaken van toepassingen?](#what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications)
- [Waarom stopt IIS het schrijven naar de logboekmap?](#why-does-iis-stop-writing-to-the-log-directory)
- [Hoe kan ik WAD-logboek registratie inschakelen voor Cloud Services?](#how-do-i-enable-wad-logging-for-cloud-services)

**Netwerkconfiguratie**

- [Hoe kan ik de time-out voor inactiviteit voor Azure load balancer ingesteld?](#how-do-i-set-the-idle-timeout-for-azure-load-balancer)
- [Hoe kan ik een statisch IP-adres koppelen aan mijn Cloud service?](#how-do-i-associate-a-static-ip-address-to-my-cloud-service)
- [Wat zijn de functies en mogelijkheden die Azure Basic IP'S/ID'S en DDOS biedt?](#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)
- [Hoe kan ik HTTP/2 inschakelen op Cloud Services VM?](#how-to-enable-http2-on-cloud-services-vm)

**Machtigingen**

- [Kunnen interne technici van micro soft extern bureau blad naar Cloud service-instanties zonder toestemming?](#can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission)
- [Ik kan geen extern bureau blad naar Cloud service-VM maken met behulp van het RDP-bestand. Er wordt een fout melding weer geven: er is een verificatie fout opgetreden (code: 0x80004005)](#i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005)

**Schalen**

- [Ik kan niet meer dan X exemplaren schalen](#i-cannot-scale-beyond-x-instances)
- [Hoe kan ik automatisch schalen op basis van geheugen metrieken configureren?](#how-can-i-configure-auto-scale-based-on-memory-metrics)

**Algemeen**

- [Hoe kan ik toevoegen `nosniff` aan mijn website?](#how-do-i-add-nosniff-to-my-website)
- [Hoe kan ik IIS aanpassen voor een webrol?](#how-do-i-customize-iis-for-a-web-role)
- [Wat is de quotum limiet voor mijn Cloud service?](#what-is-the-quota-limit-for-my-cloud-service)
- [Waarom geeft het station op mijn Cloud service-VM weinig vrije schijf ruimte weer?](#why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space)
- [Hoe kan ik een anti-malware-extensie voor mijn Cloud Services op een geautomatiseerde manier toevoegen?](#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)
- [Servernaamindicatie (SNI) inschakelen voor Cloud Services?](#how-to-enable-server-name-indication-sni-for-cloud-services)
- [Hoe kan ik Tags toevoegen aan mijn Azure-Cloud service?](#how-can-i-add-tags-to-my-azure-cloud-service)
- [De Azure Portal de SDK-versie van mijn Cloud service wordt niet weer gegeven. Hoe kan ik dat doen?](#the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that)
- [Ik wil de Cloud service enkele maanden afsluiten. Hoe kan ik de facturerings kosten van de Cloud service verlagen zonder het IP-adres te verliezen?](#i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address)


## <a name="certificates"></a>Certificaten

### <a name="why-is-the-certificate-chain-of-my-cloud-service-tlsssl-certificate-incomplete"></a>Waarom is de certificaat keten van mijn Cloud service TLS/SSL-certificaat onvolledig?
    
Klanten wordt aangeraden de volledige certificaat keten (Leaf-certificaat, tussenliggende certificaten en basis certificaat) te installeren in plaats van alleen het Leaf-certificaat. Wanneer u alleen het Leaf-certificaat installeert, vertrouwt u op Windows om de certificaat keten te bouwen door de CTL te laten oplopen. Als er in Windows Update Azure onregelmatige netwerk-of DNS-problemen optreden bij het valideren van het certificaat, wordt het certificaat mogelijk als ongeldig beschouwd. Dit probleem kan worden vermeden door de volledige certificaat keten te installeren. De blog voor het [installeren van een gekoppeld SSL-certificaat](/archive/blogs/azuredevsupport/how-to-install-a-chained-ssl-certificate) laat zien hoe u dit doet.

### <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>Wat is het doel van het versleutelings certificaat voor Windows Azure-Hulpprogram Ma's voor uitbrei dingen?

Deze certificaten worden automatisch gemaakt wanneer een uitbrei ding wordt toegevoegd aan de Cloud service. Meestal is dit de WAD-uitbrei ding of de RDP-uitbrei ding, maar het kan ook andere zijn, zoals de antimalware-of logboek verzamelaar extensie. Deze certificaten worden alleen gebruikt voor het versleutelen en ontsleutelen van de persoonlijke configuratie voor de uitbrei ding. De verval datum wordt nooit gecontroleerd, dus het maakt niet uit of het certificaat is verlopen. 

U kunt deze certificaten negeren. Als u de certificaten wilt opschonen, kunt u deze allemaal verwijderen. Er wordt een fout gegenereerd als u probeert een certificaat te verwijderen dat in gebruik is.

### <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>Hoe kan ik een aanvraag voor certificaat ondertekening (CSR) zonder ' RDP-ING ' in het exemplaar genereren?

Raadpleeg het volgende document van de technische documentatie:

[Een certificaat verkrijgen voor gebruik met Windows Azure web sites (WAWS)](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/)

De CSR is slechts een tekst bestand. U hoeft deze niet te maken op de computer waarop het certificaat uiteindelijk zal worden gebruikt.Hoewel dit document is geschreven voor een App Service, is het maken van de CSR algemeen en geldt ook voor Cloud Services.

### <a name="my-cloud-service-management-certificate-is-expiring-how-to-renew-it"></a>Mijn Cloud Service beheer certificaat verloopt. Hoe kan ik deze vernieuwen?

U kunt de volgende Power shell-opdrachten gebruiken om uw beheer certificaten te vernieuwen:

```powershell
Add-AzureAccount
Select-AzureSubscription -Current -SubscriptionName <your subscription name>
Get-AzurePublishSettingsFile
```

De **Get-AzurePublishSettingsFile** maakt een nieuw beheer certificaat in **abonnements**  >  **beheer certificaten** in de Azure Portal. De naam van het nieuwe certificaat ziet eruit als ' YourSubscriptionNam]-[CurrentDate]-referenties '.

### <a name="how-to-automate-the-installation-of-main-tlsssl-certificatepfx-and-intermediate-certificatep7b"></a>Hoe kan ik de installatie van het hoofd-TLS/SSL-certificaat (. pfx) en het tussenliggende certificaat (. p7b) automatiseren?

U kunt deze taak automatiseren met behulp van een opstart script (batch/cmd/Power shell) en dat opstart script registreren in het service definitie bestand. Voeg zowel het opstart script als het certificaat (. p7b-bestand) toe aan de projectmap van dezelfde map van het opstart script.

### <a name="what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate"></a>Wat is het doel van het certificaat ' Microsoft Azure Service Management for MachineKey '?

Dit certificaat wordt gebruikt om computer sleutels op Azure-webrollen te versleutelen. Raadpleeg [dit advies](/security-updates/securityadvisories/2018/4092731)voor meer informatie.

Raadpleeg voor meer informatie de volgende artikelen:
- [Opstart taken voor een Cloud service configureren en uitvoeren](./cloud-services-startup-tasks.md)
- [Veelvoorkomende opstart taken voor de Cloud service](./cloud-services-startup-tasks-common.md)

## <a name="monitoring-and-logging"></a>Bewaking en registratie

### <a name="what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications"></a>Wat zijn de toekomstige Cloud Services in de Azure Portal die u kunnen helpen bij het beheren en bewaken van toepassingen?

De mogelijkheid om een nieuw certificaat te genereren voor Remote Desktop Protocol (RDP) is binnenkort beschikbaar. U kunt dit script ook uitvoeren:

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 20 48 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```
De mogelijkheid om BLOB of lokaal te kiezen voor uw csdef-en cscfg-upload locatie is binnenkort beschikbaar. Met behulp van [New-Azure](/powershell/module/servicemanagement/azure.service/new-azuredeployment?view=azuresmps-4.0.0&preserve-view=true)kunt u elke locatie waarde instellen.

De mogelijkheid om metrische gegevens op instantie niveau te bewaken. Er zijn extra bewakings mogelijkheden beschikbaar [voor het bewaken van Cloud Services](cloud-services-how-to-monitor.md).

### <a name="why-does-iis-stop-writing-to-the-log-directory"></a>Waarom stopt IIS het schrijven naar de logboekmap?
U hebt de lokale opslag limiet voor het schrijven naar de logboekmap uitgeput.Als u dit wilt corrigeren, kunt u een van de volgende drie dingen doen:
* Schakel diagnostische gegevens voor IIS in en laat de diagnostische gegevens regel matig verplaatsen naar Blob Storage.
* Verwijder de logboek bestanden hand matig uit de logboekmap.
* Verhoog de quotum limiet voor lokale bronnen.

Raadpleeg de volgende documenten voor meer informatie:
* [Diagnostische gegevens opslaan en weergeven in Azure Storage](../storage/common/storage-introduction.md)
* [IIS-logboeken stoppen met schrijven in Cloud service](/archive/blogs/cie/iis-logs-stops-writing-in-cloud-service)

### <a name="how-do-i-enable-wad-logging-for-cloud-services"></a>Hoe kan ik WAD-logboek registratie inschakelen voor Cloud Services?
U kunt de logboek registratie van Windows Azure Diagnostics (WAD) inschakelen via de volgende opties:
1. [Inschakelen vanuit Visual Studio](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)
2. [Via .NET-code inschakelen](./cloud-services-dotnet-diagnostics.md)
3. [Inschakelen via Power shell](./cloud-services-diagnostics-powershell.md)

Als u de huidige WAD-instellingen van uw Cloud service wilt ophalen, kunt u [Get-AzureServiceDiagnosticsExtensions](./cloud-services-diagnostics-powershell.md#get-current-diagnostics-extension-configuration) Power shell cmd gebruiken of kunt u deze weer geven via de portal op de blade Cloud Services-> uitbrei dingen.


## <a name="network-configuration"></a>Netwerkconfiguratie

### <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>Hoe kan ik de time-out voor inactiviteit voor Azure load balancer ingesteld?
U kunt de time-out in het bestand met de service definitie (csdef) als volgt opgeven:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="mgVS2015Worker" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10100"   idleTimeoutInMinutes="30" />
    </Endpoints>
  </WorkerRole>
```
Zie [Nieuw: Configureer bare time-out voor inactiviteit voor Azure Load Balancer](https://azure.microsoft.com/blog/new-configurable-idle-timeout-for-azure-load-balancer/) voor meer informatie.

### <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>Hoe kan ik een statisch IP-adres koppelen aan mijn Cloud service?
Als u een statisch IP-adres wilt instellen, moet u een gereserveerde IP maken. Dit gereserveerde IP-adres kan worden gekoppeld aan een nieuwe Cloud service of aan een bestaande implementatie. Raadpleeg de volgende documenten voor meer informatie:
* [Een gereserveerd IP-adres maken](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#manage-reserved-vips)
* [Het IP-adres van een bestaande Cloud service reserveren](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#reserve-the-ip-address-of-an-existing-cloud-service)
* [Een gereserveerd IP-adres koppelen aan een nieuwe Cloud service](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-new-cloud-service)
* [Een gereserveerd IP-adres aan een actieve implementatie koppelen](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-running-deployment)
* [Een gereserveerd IP-adres koppelen aan een Cloud service met behulp van een service configuratie bestand](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

### <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>Wat zijn de functies en mogelijkheden die Azure Basic IP'S/ID'S en DDOS biedt?
Azure heeft IP'S/ID'S in fysieke Data Center-servers om tegen bedreigingen te beschermen. Daarnaast kunnen klanten beveiligings oplossingen van derden implementeren, zoals firewalls voor webtoepassingen, netwerk firewalls, antimalware, detectie van indringers, preventie van systemen (ID'S/IP'S) en meer. Zie [uw gegevens en assets beveiligen en voldoen aan de algemene beveiligings normen](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity)voor meer informatie.

Micro soft bewaakt voortdurend servers, netwerken en toepassingen om bedreigingen te detecteren. De multipronged Threat-Management aanpak van Azure maakt gebruik van inbraak detectie, gedistribueerde Denial-of-service (DDoS) aanvallen voor komen, indringings tests, gedrags analyse, afwijkings detectie en machine learning om de verdediging voortdurend te versterken en risico's te verminderen. Micro soft antimalware voor Azure beveiligt Azure Cloud Services en virtuele machines. U hebt de mogelijkheid om ook beveiligings oplossingen van derden te implementeren, zoals fire walls van webtoepassingen, netwerk firewalls, antimalware, detectie van inbreuken en preventie systemen (ID'S/IP-adressen).

### <a name="how-to-enable-http2-on-cloud-services-vm"></a>Hoe kan ik HTTP/2 inschakelen op Cloud Services VM?

Windows 10 en Windows Server 2016 worden geleverd met ondersteuning voor HTTP/2 op client-en server zijde. Als uw client (browser) verbinding maakt met de IIS-server via TLS die HTTP/2 via TLS-uitbrei dingen onderhandelt, hoeft u geen wijzigingen aan te brengen aan de server zijde. Dit komt doordat via TLS de H2-14-header die het gebruik van HTTP/2 opgeeft, standaard wordt verzonden. Als uw client op de andere kant een upgrade-header verzendt om een upgrade naar HTTP/2 uit te voeren, moet u de wijziging hieronder aan de server zijde door nemen om ervoor te zorgen dat de upgrade werkt en dat er een HTTP/2-verbinding wordt gemaakt. 

1. Voer regedit.exe uit.
2. Blader naar de register sleutel: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters.
3. Maak een nieuwe DWORD-waarde met de naam **DuoEnabled**.
4. Stel de waarde in op 1.
5. Start de server opnieuw op.
6. Ga naar de **standaard website** en maak onder **bindingen** een nieuwe TLS-binding met het zelfondertekende certificaat dat zojuist is gemaakt. 

Zie voor meer informatie:

- [HTTP/2 op IIS](https://blogs.iis.net/davidso/http2)
- [Video: HTTP/2 in Windows 10: browser, apps en webserver](https://channel9.msdn.com/Events/Build/2015/3-88)
         

Deze stappen kunnen worden geautomatiseerd via een opstart taak, zodat wanneer een nieuw PaaS-exemplaar wordt gemaakt, de bovenstaande wijzigingen in het systeem register kunnen worden uitgevoerd. Zie [opstart taken voor een Cloud service configureren en uitvoeren](cloud-services-startup-tasks.md)voor meer informatie.

 
Zodra dit is gebeurd, kunt u controleren of de HTTP/2 is ingeschakeld of niet door een van de volgende methoden te gebruiken:

- Schakel de Protocol versie in IIS-logboeken in en zoek in de IIS-logboeken. Er wordt HTTP/2 weer gegeven in de logboeken. 
- Schakel het hulp programma F12-ontwikkel aars in Internet Explorer of micro soft Edge in en schakel over naar het tabblad netwerk om het protocol te controleren. 

Zie [http/2 op IIS](https://blogs.iis.net/davidso/http2)voor meer informatie.

## <a name="permissions"></a>Machtigingen

### <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>Hoe kan ik toegang op basis van rollen implementeren voor Cloud Services?
Cloud Services biedt geen ondersteuning voor het Azure RBAC-model (op rollen gebaseerd toegangs beheer) omdat het geen Azure Resource Manager-gebaseerde service is.

Zie [inzicht krijgen in de verschillende rollen in azure](../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="remote-desktop"></a>Extern bureau blad

### <a name="can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission"></a>Kunnen interne technici van micro soft extern bureau blad naar Cloud service-instanties zonder toestemming?
Micro soft volgt een strikt proces dat interne technici niet in staat stelt om extern bureau blad in uw Cloud service te plaatsen zonder schrijf machtiging (e-mail adres of andere schriftelijke communicatie) van de eigenaar of de gebruiker.

### <a name="i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005"></a>Ik kan geen extern bureau blad naar Cloud service-VM maken met behulp van het RDP-bestand. Er wordt een fout melding weer geven: er is een verificatie fout opgetreden (code: 0x80004005)

Deze fout kan optreden als u het RDP-bestand gebruikt van een computer die is toegevoegd aan Azure Active Directory. Volg deze stappen om dit probleem op te lossen:

1. Klik met de rechter muisknop op het RDP-bestand dat u hebt gedownload en selecteer vervolgens **bewerken**.
2. Voeg &#92; toe als voor voegsel voor de gebruikers naam. Gebruik bijvoorbeeld **.\username** in plaats van  **gebruikers naam**.

## <a name="scaling"></a>Schalen

### <a name="i-cannot-scale-beyond-x-instances"></a>Ik kan niet meer dan X exemplaren schalen
Uw Azure-abonnement heeft een limiet voor het aantal kernen dat u kunt gebruiken. Schalen werkt niet als u alle beschik bare kernen hebt gebruikt. Als u bijvoorbeeld een limiet van 100 kern geheugens hebt, betekent dit dat u 100 a1-groottes van virtuele machines kunt hebben voor uw Cloud service of 50 a2-grootte voor virtuele machines.

### <a name="how-can-i-configure-auto-scale-based-on-memory-metrics"></a>Hoe kan ik automatisch schalen op basis van geheugen metrieken configureren?

Automatisch schalen op basis van geheugen metrieken voor een Cloud Services wordt momenteel niet ondersteund. 

U kunt dit probleem omzeilen door Application Insights te gebruiken. Automatisch schalen ondersteunt Application Insights als metrische bron en kan het aantal rolinstanties schalen op basis van de metrische gegevens van de gast, zoals ' geheugen '.  U moet Application Insights configureren in uw bestand van het Cloud service project-pakket (*. cspkg) en Azure Diagnostics extensie inschakelen op de service om deze eenvoudig te implementeren.

Zie aan de [slag met automatisch schalen op basis van aangepaste metrische gegevens in azure](../azure-monitor/autoscale/autoscale-custom-metric.md) voor meer informatie over het gebruik van een aangepaste metriek via Application Insights voor het configureren van automatisch schalen op Cloud Services.

Voor meer informatie over het integreren van Azure Diagnostics met Application Insights voor Cloud Services raadpleegt [u Cloud service, virtuele machine of service Fabric diagnostische gegevens verzenden naar Application Insights](../azure-monitor/agents/diagnostics-extension-to-application-insights.md)

Zie [Application Insights voor Azure Cloud Services](../azure-monitor/app/cloudservices.md) voor meer informatie over het inschakelen van Application Insights voor Cloud Services

Zie voor meer informatie over het inschakelen van Azure Diagnostics logboek registratie voor Cloud Services voor het [instellen van diagnostische gegevens voor Azure Cloud Services en virtuele machines](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)

## <a name="generic"></a>Algemeen

### <a name="how-do-i-add-nosniff-to-my-website"></a>Hoe kan ik toevoegen `nosniff` aan mijn website?
Als u wilt voor komen dat clients de MIME-typen sniffen, voegt u een instelling toe in uw *web.config* -bestand.

```xml
<configuration>
   <system.webServer>
      <httpProtocol>
         <customHeaders>
            <add name="X-Content-Type-Options" value="nosniff" />
         </customHeaders>
      </httpProtocol>
   </system.webServer>
</configuration>
```

U kunt deze ook toevoegen als een instelling in IIS. Gebruik de volgende opdracht met het artikel [veelvoorkomende opstart taken](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) .

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

### <a name="how-do-i-customize-iis-for-a-web-role"></a>Hoe kan ik IIS aanpassen voor een webrol?
Gebruik het opstart script van IIS vanuit het artikel [veelvoorkomende opstart taken](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) .

### <a name="what-is-the-quota-limit-for-my-cloud-service"></a>Wat is de quotum limiet voor mijn Cloud service?
Zie [servicespecifieke limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits).

### <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>Waarom geeft het station op mijn Cloud service-VM weinig vrije schijf ruimte weer?
Dit is normaal gedrag en er mag geen problemen met uw toepassing optreden. Logboeken is ingeschakeld voor het% AppRoot%-station in azure PaaS-Vm's, wat in feite de hoeveelheid ruimte in beslag neemt die normaal gesp roken wordt geconsumeerd. Er zijn echter verschillende zaken waarmee u rekening moet houden, waardoor dit een niet-probleem is.

De grootte van het station% AppRoot% wordt berekend als <grootte van. cspkg + maximum grootte van het logboek en een marge vrije ruimte>, of 1,5 GB, afhankelijk van wat groter is. De grootte van uw virtuele machine heeft geen invloed op deze berekening. (De grootte van de virtuele machine is alleen van invloed op de grootte van het tijdelijke station C:.) 

Kan niet schrijven naar het station% AppRoot%. Als u naar de Azure-VM schrijft, moet u dit doen in een tijdelijke LocalStorage-resource (of een andere optie, zoals Blob Storage, Azure Files, enzovoort). De hoeveelheid vrije ruimte voor de map% AppRoot% is dus niet zinvol. Als u niet zeker weet of uw toepassing naar het% AppRoot%-station schrijft, kunt u de service in een paar dagen altijd laten uitvoeren en vervolgens de grootten ' voor ' en ' na ' vergelijkt. 

Azure schrijft niets naar het station% AppRoot%. Zodra de VHD is gemaakt van uw `.cspkg` en is gekoppeld aan de virtuele machine van Azure, is het enige wat u kunt schrijven naar dit station, uw toepassing. 

De logboek instellingen kunnen niet worden geconfigureerd, zodat u deze niet kunt uitschakelen.

### <a name="how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way"></a>Hoe kan ik een anti-malware-extensie voor mijn Cloud Services op een geautomatiseerde manier toevoegen?

U kunt de antimalware-uitbrei ding inschakelen met behulp van Power shell-script in de opstart taak. Volg de stappen in deze artikelen om deze te implementeren: 
 
- [Een Power shell-opstart taak maken](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)
- [Set-AzureServiceAntimalwareExtension](/powershell/module/servicemanagement/azure.service/Set-AzureServiceAntimalwareExtension?view=azuresmps-4.0.0&preserve-view=true)

Zie [antimalware-implementatie scenario's](../security/fundamentals/antimalware.md#antimalware-deployment-scenarios)voor meer informatie over antimalware-implementatie scenario's en hoe u deze kunt inschakelen vanuit de portal.

### <a name="how-to-enable-server-name-indication-sni-for-cloud-services"></a>Servernaamindicatie (SNI) inschakelen voor Cloud Services?

U kunt SNI inschakelen in Cloud Services met behulp van een van de volgende methoden:

**Methode 1: Power shell gebruiken**

De SNI-binding kan worden geconfigureerd met behulp van de Power shell-cmdlet **New-webbinding** in een opstart taak voor een Cloud service-rolinstantie, zoals hieronder wordt beschreven:

```powershell
New-WebBinding -Name $WebsiteName -Protocol "https" -Port 443 -IPAddress $IPAddress -HostHeader $HostHeader -SslFlags $sslFlags
```

Zoals [hier](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee790567(v=technet.10))wordt beschreven, kan de $sslFlags een van de volgende waarden zijn:

|Waarde|Betekenis|
------|------
|0|Geen SNI|
|1|SNI ingeschakeld|
|2|Niet-SNI binding die gebruikmaakt van een centraal certificaat archief|
|3|SNI-binding die gebruikmaakt van een centraal certificaat archief|
 
**Methode 2: code gebruiken**

De SNI-binding kan ook worden geconfigureerd via code in het opstart proces van de functie zoals beschreven in dit [blog bericht](/archive/blogs/jianwu/expose-ssl-service-to-multi-domains-from-the-same-cloud-service):

```csharp
//<code snip> 
                var serverManager = new ServerManager(); 
                var site = serverManager.Sites[0]; 
                var binding = site.Bindings.Add(":443:www.test1.com", newCert.GetCertHash(), "My"); 
                binding.SetAttributeValue("sslFlags", 1); //enables the SNI 
                serverManager.CommitChanges(); 
    //</code snip>
```
    
Als u een van de bovenstaande benaderingen gebruikt, moeten de respectieve certificaten (*. pfx) voor de specifieke hostnamen eerst worden geïnstalleerd in de rolinstanties met behulp van een opstart taak of via code om ervoor te zorgen dat de SNI-binding effectief is.

### <a name="how-can-i-add-tags-to-my-azure-cloud-service"></a>Hoe kan ik Tags toevoegen aan mijn Azure-Cloud service? 

Cloud service is een klassieke resource. Alleen resources die zijn gemaakt via Azure Resource Manager ondersteunings Tags. U kunt geen tags Toep assen op klassieke resources, zoals Cloud service. 

### <a name="the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that"></a>De Azure Portal de SDK-versie van mijn Cloud service wordt niet weer gegeven. Hoe kan ik dat doen?

We werken aan het gebruik van deze functie op het Azure Portal. Ondertussen kunt u de volgende Power shell-opdrachten gebruiken om de SDK-versie op te halen:

```powershell
Get-AzureService -ServiceName "<Cloud Service name>" | Get-AzureDeployment | Where-Object -Property SdkVersion -NE -Value "" | select ServiceName,SdkVersion,OSVersion,Slot
```

### <a name="i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address"></a>Ik wil de Cloud service enkele maanden afsluiten. Hoe kan ik de facturerings kosten van de Cloud service verlagen zonder het IP-adres te verliezen?

Een reeds geïmplementeerde Cloud service wordt gefactureerd voor de reken-en opslag capaciteit die wordt gebruikt. Ook als u de Azure-VM afsluit, wordt u nog steeds gefactureerd voor de opslag. 

Dit is wat u kunt doen om uw facturering te verminderen zonder het IP-adres voor uw service kwijt te raken:

1. [Reserveer het IP-adres](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) voordat u de implementaties verwijdert.  Er worden alleen kosten in rekening gebracht voor dit IP-adres. Zie [prijzen van IP-adressen](https://azure.microsoft.com/pricing/details/ip-addresses/)voor meer informatie over het factureren van het IP-adres.
2. Verwijder de implementaties. Verwijder de xxx.cloudapp.net niet, zodat u deze voor Taan kunt gebruiken.
3. Als u de Cloud service opnieuw wilt implementeren met behulp van hetzelfde gereserveerde IP-adres dat u in uw abonnement hebt gereserveerd, raadpleegt u [gereserveerd IP adressen voor Cloud Services en virtual machines](https://azure.microsoft.com/blog/reserved-ip-addresses/).