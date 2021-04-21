---
title: Veelgestelde vragen over configuratie
description: Krijg antwoorden op veelgestelde vragen over configuratie- en beheerproblemen voor Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/30/2018
ms.author: genli
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d4b6ceef837d985db004e9d925b554c54c287424
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834530"
---
# <a name="configuration-and-management-faqs-for-web-apps-in-azure"></a>Veelgestelde vragen over configuratie en beheer voor Web Apps in Azure

Dit artikel biedt antwoorden op veelgestelde vragen over configuratie- en beheerproblemen voor de [Web Apps-functie van Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="are-there-limitations-i-should-be-aware-of-if-i-want-to-move-app-service-resources"></a>Zijn er beperkingen waar ik rekening mee moet houden als ik mijn App Service verplaatsen?

Als u van plan bent om App Service te verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement, zijn er enkele beperkingen waar u rekening mee moet houden. Zie beperkingen voor App Service [meer informatie.](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)

## <a name="how-do-i-use-a-custom-domain-name-for-my-web-app"></a>Hoe kan ik een aangepaste domeinnaam gebruiken voor mijn web-app?

Zie onze video van zeven minuten Een aangepaste domeinnaam toevoegen voor antwoorden op veelvoorkomende vragen over het gebruik van een aangepaste [domeinnaam met uw Azure-web-app.](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name) De video biedt een overzicht van het toevoegen van een aangepaste domeinnaam. In dit artikel wordt beschreven hoe u uw eigen URL gebruikt in plaats van de *.azurewebsites.net-URL met uw App Service web-app. U kunt ook een gedetailleerd overzicht bekijken van het toevoegen van [een aangepaste domeinnaam.](app-service-web-tutorial-custom-domain.md)


## <a name="how-do-i-purchase-a-new-custom-domain-for-my-web-app"></a>Hoe kan ik een nieuw aangepast domein aanschaffen voor mijn web-app?

Zie Een aangepaste domeinnaam kopen en configureren in App Service voor meer informatie over het kopen en instellen van een aangepast [domein](manage-custom-dns-buy-domain.md)voor uw App Service-web-app.


## <a name="how-do-i-upload-and-configure-an-existing-tlsssl-certificate-for-my-web-app"></a>Hoe kan ik een bestaand TLS/SSL-certificaat voor mijn web-app uploaden en configureren?

Zie Een TLS/SSL-certificaat toevoegen aan uw App Service-app voor meer informatie over het uploaden en instellen van [een bestaand aangepast TLS/SSL-certificaat.](configure-ssl-certificate.md)


## <a name="how-do-i-purchase-and-configure-a-new-tlsssl-certificate-in-azure-for-my-web-app"></a>Hoe kan ik een nieuw TLS/SSL-certificaat kopen en configureren in Azure voor mijn web-app?

Zie Een TLS/SSL-certificaat toevoegen aan uw App Service-app voor meer informatie over het kopen en instellen van een [TLS/SSL-certificaat](configure-ssl-certificate.md)voor uw App Service-web-app.


## <a name="how-do-i-move-application-insights-resources"></a>Hoe kan ik resources Application Insights verplaatsen?

Momenteel biedt Azure-toepassing Insights geen ondersteuning voor de bewerking voor verplaatsen. Als uw oorspronkelijke resourcegroep een Application Insights bevat, kunt u die resource niet verplaatsen. Als u de Application Insights opgeeft wanneer u een app App Service verplaatst, mislukt de hele bewerking voor verplaatsen. De Application Insights en het App Service hoeven zich echter niet in dezelfde resourcegroep te hebben als de app om de app correct te laten werken.

Zie beperkingen voor App Service [meer informatie.](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)

## <a name="where-can-i-find-a-guidance-checklist-and-learn-more-about-resource-move-operations"></a>Waar vind ik een controlelijst voor richtlijnen en meer informatie over het verplaatsen van resources?

[App Service beperkingen](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md) ziet u hoe u resources verplaatst naar een nieuw abonnement of naar een nieuwe resourcegroep in hetzelfde abonnement. U kunt informatie krijgen over de controlelijst voor het verplaatsen van resources, leren welke services de bewerking voor verplaatsen ondersteunen en meer informatie over App Service beperkingen en andere onderwerpen.

## <a name="how-do-i-set-the-server-time-zone-for-my-web-app"></a>Hoe kan ik de servertijdzone voor mijn web-app instellen?

De servertijdzone voor uw web-app instellen:

1. Ga in Azure Portal uw App Service naar het menu **Toepassingsinstellingen.**
2. Voeg **onder App-instellingen** deze instelling toe:
    * Sleutel = WEBSITE_TIME_ZONE
    * Waarde = *de tijdzone die u wilt*
3. Selecteer **Opslaan**.

Zie de uitvoer van de Windows-opdracht voor de App Services die worden uitgevoerd in `tzutil /L` Windows. Gebruik de waarde van de tweede regel van elk item. Bijvoorbeeld: 'Standaardtijd voor Reguliere tijd'. Sommige van deze waarden worden ook vermeld in de **kolom Tijdzone** in [Standaardtijdzones.](/windows-hardware/manufacture/desktop/default-time-zones)

Voor de App Services die worden uitgevoerd op Linux stelt u een waarde in vanuit de [IANA TZ-database](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones). Bijvoorbeeld: 'America/Adak'.

## <a name="why-do-my-continuous-webjobs-sometimes-fail"></a>Waarom mislukken mijn doorlopende webjobs soms?

Web-apps worden standaard verwijderd als ze een bepaalde periode niet actief zijn. Hierdoor kan het systeem resources besparen. In Basic- en Standard-abonnementen kunt u de instelling **Altijd** aan in om de web-app altijd te laden. Als in uw web-app doorlopende webjobs worden uitgevoerd, moet u **Always On** in- of de webjobs mogelijk niet betrouwbaar uitvoeren. Zie Create [a continuously running WebJob (Een continu lopende webjob maken) voor meer informatie.](webjobs-create.md#CreateContinuous)

## <a name="how-do-i-get-the-outbound-ip-address-for-my-web-app"></a>Hoe kan ik het uitgaande IP-adres voor mijn web-app op?

De lijst met uitgaande IP-adressen voor uw web-app op te halen:

1. Ga in Azure Portal blade van uw web-app naar **het** menu Eigenschappen.
2. Zoek naar **uitgaande IP-adressen.**

De lijst met uitgaande IP-adressen wordt weergegeven.

Zie Uitgaande [netwerkadressen](environment/app-service-app-service-environment-network-architecture-overview.md#outbound-network-addresses)voor meer informatie over het op halen van het uitgaande IP-adres als uw website wordt gehost in een App Service Environment.

## <a name="how-do-i-get-a-reserved-or-dedicated-inbound-ip-address-for-my-web-app"></a>Hoe kan ik een gereserveerd of toegewezen inkomende IP-adres voor mijn web-app?

Als u een toegewezen of gereserveerd IP-adres wilt instellen voor binnenkomende aanroepen naar de website van uw Azure-app, installeert en configureert u een TLS/SSL-certificaat op basis van IP.

Als u een toegewezen of gereserveerd IP-adres wilt gebruiken voor binnenkomende oproepen, moet uw App Service een Basic- of hoger serviceplan hebben.

## <a name="can-i-export-my-app-service-certificate-to-use-outside-azure-such-as-for-a-website-hosted-elsewhere"></a>Kan ik mijn certificaat App Service om buiten Azure te gebruiken, bijvoorbeeld voor een website die elders wordt gehost? 

Ja, u kunt ze exporteren voor gebruik buiten Azure. Zie Veelgestelde vragen over het verkrijgen van [App Service en aangepaste domeinen voor meer informatie.](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview)

## <a name="can-i-export-my-app-service-certificate-to-use-with-other-azure-cloud-services"></a>Kan ik mijn certificaat App Service voor gebruik met andere Azure-cloudservices exporteren?

De portal biedt een eersteklas ervaring voor het implementeren van een App Service certificaat via Azure Key Vault in App Service apps. We ontvangen echter aanvragen van klanten om deze certificaten buiten het App Service gebruiken, bijvoorbeeld met Azure Virtual Machines. Zie Een lokale PFX-kopie van een App Service-certificaat maken voor meer informatie over het maken van een lokale PFX-kopie van uw [App Service-certificaat,](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/)zodat u het certificaat met andere Azure-resources kunt gebruiken.

Zie Veelgestelde vragen over App Service [en aangepaste domeinen voor meer informatie.](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview)


## <a name="why-do-i-see-the-message-partially-succeeded-when-i-try-to-back-up-my-web-app"></a>Waarom zie ik het bericht Gedeeltelijk geslaagd wanneer ik een back-up van mijn web-app probeer te maken?

Een veelvoorkomende oorzaak van een back-upfout is dat sommige bestanden door de toepassing worden gebruikt. Bestanden die in gebruik zijn, worden vergrendeld terwijl u de back-up maakt. Dit voorkomt dat er een back-up van deze bestanden wordt maken en kan leiden tot de status Gedeeltelijk geslaagd. U kunt dit voorkomen door bestanden uit te sluiten van het back-upproces. U kunt ervoor kiezen alleen een back-up te maken van de benodigde back-up. Zie Backup [just the important parts of your site with Azure web apps (Back-up maken van alleen de belangrijke onderdelen van uw site met Azure-web-apps) voor meer informatie.](https://zainrizvi.io/blog/creating-partial-backups-of-your-site-with-azure-web-apps/)

## <a name="how-do-i-remove-a-header-from-the-http-response"></a>Hoe kan ik header verwijderen uit het HTTP-antwoord?

Als u de headers uit het HTTP-antwoord wilt verwijderen, moet u het web.config site bijwerken. Zie Standaardserverheaders verwijderen op uw [Azure-websites voor meer informatie.](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)

## <a name="is-app-service-compliant-with-pci-standard-30-and-31"></a>Voldoet App Service aan PCI Standard 3.0 en 3.1?

Momenteel is Web Apps functie van Azure App Service compatibel met PCI Data Security Standard (DSS) versie 3.0 Level 1. PCI DSS versie 3.1 staat op de planning. Er wordt al gepland hoe de implementatie van de meest recente standaard wordt verlopen.

PCI DSS versie 3.1-certificering moet Transport Layer Security (TLS) 1.0 worden uitschakelen. Het uitschakelen van TLS 1.0 is momenteel geen optie voor de meeste App Service-abonnementen. Als u echter gebruik App Service Environment of bereid bent om uw workload te migreren naar App Service Environment, krijgt u meer controle over uw omgeving. Dit omvat het uitschakelen van TLS 1.0 door contact op te nemen met Ondersteuning voor Azure. In de nabije toekomst zijn we van plan deze instellingen toegankelijk te maken voor gebruikers.

Zie naleving van Microsoft Azure App Service [web-apps met PCI Standard 3.0 en 3.1 voor meer informatie.](https://support.microsoft.com/help/3124528)

## <a name="how-do-i-use-the-staging-environment-and-deployment-slots"></a>Hoe kan ik de faseringsomgeving en implementatiesleuven gebruiken?

Wanneer u in Standard- App Service Premium-abonnementen uw web-app implementeert in App Service, kunt u implementeren naar een afzonderlijke implementatiesleuf in plaats van naar de standaardproductiesleuf. Implementatiesleuven zijn live web-apps met hun eigen hostnamen. Inhoud van web-apps en configuratie-elementen kunnen worden gewisseld tussen twee implementatiesleuven, waaronder de productiesleuf.

Zie Een faseringsomgeving instellen in App Service voor meer informatie over het gebruik [van implementatiesleuven.](deploy-staging-slots.md)

## <a name="how-do-i-access-and-review-webjob-logs"></a>Hoe kan ik webjoblogboeken openen en controleren?

WebJob-logboeken controleren:

1. Meld u aan bij uw **Kudu-website** ( `https://*yourwebsitename*.scm.azurewebsites.net` ).
2. Selecteer de webjob.
3. Selecteer de knop Uitvoer in-/uitschakelen. 
4. Selecteer de koppeling Downloaden om het uitvoerbestand **te** downloaden.
5. Voor afzonderlijke runs selecteert u **Afzonderlijke aanroepen.**
6. Selecteer de knop Uitvoer in-/uitschakelen. 
7. Selecteer de downloadkoppeling.

## <a name="im-trying-to-use-hybrid-connections-with-sql-server-why-do-i-see-the-message-systemoverflowexception-arithmetic-operation-resulted-in-an-overflow"></a>Ik probeer hybride verbindingen te gebruiken met SQL Server. Waarom zie ik het bericht System.OverflowException: Arithmetic operation resulted in an overflow? (System.OverflowException: Rekenkundige bewerking heeft geleid tot een overloop)?

Als u hybride verbindingen gebruikt voor toegang tot SQL Server, kan een Microsoft .NET-update op 10 mei 2016 ertoe leiden dat verbindingen mislukken. Mogelijk ziet u dit bericht:

```
Exception: System.Data.Entity.Core.EntityException: The underlying provider failed on Open. —> System.OverflowException: Arithmetic operation resulted in an overflow. or (64 bit Web app) System.OverflowException: Array dimensions exceeded supported range, at System.Data.SqlClient.TdsParser.ConsumePreLoginHandshake
```

### <a name="resolution"></a>Oplossing

De uitzondering is veroorzaakt door een probleem met de Hybrid Connection Manager die sindsdien is opgelost. Zorg ervoor dat u [uw Hybrid Connection Manager](https://go.microsoft.com/fwlink/?LinkID=841308) om dit probleem op te lossen.

## <a name="how-do-i-add-a-url-rewrite-rule"></a>Hoe kan ik een regel voor het herschrijven van URL's toevoegen?

Als u een regel voor het herschrijven van een URL wilt toevoegen, maakt u web.config bestand met de relevante configuratie-vermeldingen in de **map wwwroot.** Zie voor meer informatie [Azure-app Services: Understanding URL rewrite ( Url herschrijven).](/archive/blogs/madhurabharadwaj/azure-app-services-understanding-url-re-write)

## <a name="how-do-i-control-inbound-traffic-to-app-service"></a>Hoe kan ik het inkomende verkeer naar App Service?

Op siteniveau hebt u twee opties voor het beheren van inkomende verkeer naar App Service:

* Schakel dynamische IP-beperkingen in. Zie IP- en domeinbeperkingen voor Azure-websites voor meer informatie over het in-/uitvoeren van [dynamische IP-beperkingen.](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/)
* Schakel Modulebeveiliging in. Zie ModSecurity web application firewall on [Azure websites (ModSecurity-webtoepassingsfirewall op Azure-websites)](https://azure.microsoft.com/blog/modsecurity-for-azure-websites/)voor meer informatie over het in-/uitvoeren van modulebeveiliging.

Als u een App Service Environment, kunt u [barracuda-firewall gebruiken.](https://azure.microsoft.com/blog/configuring-barracuda-web-application-firewall-for-azure-app-service-environment/)

## <a name="how-do-i-block-ports-in-an-app-service-web-app"></a>Hoe kan ik poorten blokkeren in een App Service-web-app?

In de App Service gedeelde tenantomgeving is het niet mogelijk om specifieke poorten te blokkeren vanwege de aard van de infrastructuur. TCP-poorten 4020, 4022 en 4024 zijn mogelijk ook geopend voor Visual Studio externe debugging.

In App Service Environment hebt u volledige controle over het inkomende en uitgaande verkeer. U kunt netwerkbeveiligingsgroepen gebruiken om specifieke poorten te beperken of te blokkeren. Zie Inleiding tot App Service Environment voor meer [informatie over App Service Environment.](https://azure.microsoft.com/blog/introducing-app-service-environment/)

## <a name="how-do-i-capture-an-f12-trace"></a>Hoe kan ik een F12-traceer vastleggen?

U hebt twee opties voor het vastleggen van een F12-trace:

* F12 HTTP-trace
* F12-console-uitvoer

### <a name="f12-http-trace"></a>F12 HTTP-trace

1. Ga Internet Explorer naar uw website. Het is belangrijk om u aan te melden voordat u de volgende stappen gaat volgen. Anders legt de F12-traceer gevoelige aanmeldingsgegevens vast.
2. Druk op F12.
3. Controleer of het **tabblad** Netwerk is geselecteerd en selecteer vervolgens de groene knop **Afspelen.**
4. De stappen volgen om het probleem te reproduceren.
5. Selecteer de rode **knop Stoppen.**
6. Selecteer  de knop Opslaan (schijfpictogram) en sla het HAR-bestand op  (in Internet Explorer en Microsoft Edge) of klik met de rechtermuisknop op het HAR-bestand en selecteer vervolgens Opslaan als **HAR** met inhoud (in Chrome).

### <a name="f12-console-output"></a>F12-console-uitvoer

1. Selecteer het **tabblad** Console.
2. Selecteer voor elk tabblad met meer dan nul items het tabblad (**Fout,** **Waarschuwing** of **Informatie).** Als het tabblad niet is geselecteerd, is het tabbladpictogram grijs of zwart wanneer u de cursor weg verplaatst.
3. Klik met de rechtermuisknop in het berichtgebied van het deelvenster en selecteer **alles kopiëren.**
4. Plak de gekopieerde tekst in een bestand en sla het bestand op.

Als u een HAR-bestand wilt weergeven, kunt u de [HAR-viewer gebruiken.](http://www.softwareishard.com/har/viewer/)

## <a name="why-do-i-get-an-error-when-i-try-to-connect-an-app-service-web-app-to-a-virtual-network-that-is-connected-to-expressroute"></a>Waarom krijg ik een foutmelding wanneer ik probeer een App Service web-app te verbinden met een virtueel netwerk dat is verbonden met ExpressRoute?

Als u probeert een Azure-web-app te verbinden met een virtueel netwerk dat is verbonden met Azure ExpressRoute, mislukt dit. Het volgende bericht wordt weergegeven: Gateway is geen VPN-gateway.

Op dit moment kunt u geen punt-naar-site-VPN-verbindingen hebben met een virtueel netwerk dat is verbonden met ExpressRoute. Een punt-naar-site-VPN en ExpressRoute kunnen niet naast elkaar bestaan voor hetzelfde virtuele netwerk. Zie Limieten en beperkingen [voor ExpressRoute- en site-naar-site-VPN-verbindingen voor meer informatie.](../expressroute/expressroute-howto-coexist-classic.md#limits-and-limitations)

## <a name="how-do-i-connect-an-app-service-web-app-to-a-virtual-network-that-has-a-static-routing-policy-based-gateway"></a>Hoe kan ik een App Service-web-app verbinden met een virtueel netwerk met een gateway voor statische routering (op basis van beleid) ?

Momenteel wordt het verbinden van App Service web-app met een virtueel netwerk met een gateway voor statische routering (op beleid gebaseerd) niet ondersteund. Als uw virtuele doelnetwerk al bestaat, moet punt-naar-site-VPN met een gateway voor dynamische routering zijn ingeschakeld voordat het kan worden verbonden met een app. Als uw gateway is ingesteld op statische routering, kunt u geen punt-naar-site-VPN inschakelen. 

Zie Een app integreren met een virtueel [Azure-netwerk voor meer informatie.](web-sites-integrate-with-vnet.md)

## <a name="in-my-app-service-environment-why-can-i-create-only-one-app-service-plan-even-though-i-have-two-workers-available"></a>Waarom kan App Service Environment in mijn App Service slechts één abonnement maken, ook al heb ik twee werknemers beschikbaar?

Om fouttolerantie te kunnen bieden, App Service Environment elke werkgroep ten minste één extra rekenresource nodig. Aan de extra rekenresource kan geen workload worden toegewezen.

Zie How [to create an App Service Environment (Een App Service Environment) voor meer informatie.](environment/app-service-web-how-to-create-an-app-service-environment.md)

## <a name="why-do-i-see-timeouts-when-i-try-to-create-an-app-service-environment"></a>Waarom zie ik time-outs wanneer ik een nieuwe App Service Environment?

Soms kan het maken van App Service Environment mislukt. In dat geval ziet u de volgende fout in de activiteitenlogboeken:
```
ResourceID: /subscriptions/{SubscriptionID}/resourceGroups/Default-Networking/providers/Microsoft.Web/hostingEnvironments/{ASEname}
Error:{"error":{"code":"ResourceDeploymentFailure","message":"The resource provision operation did not complete within the allowed timeout period."}}
```

U kunt dit oplossen door ervoor te zorgen dat aan geen van de volgende voorwaarden wordt voldaan:
* Het subnet is te klein.
* Het subnet is niet leeg.
* ExpressRoute voorkomt de netwerkverbindingsvereisten van een App Service Environment.
* Een slechte netwerkbeveiligingsgroep voorkomt de netwerkverbindingsvereisten van een App Service Environment.
* Geforceerd tunnelen is ingeschakeld.

Zie Frequent [issues when deploying (creating) a new Azure App Service Environment (Veelgestelde](/archive/blogs/waws/most-frequent-issues-when-deploying-creating-a-new-azure-app-service-environment-ase)problemen bij het implementeren (maken) van een Azure App Service Environment.

## <a name="why-cant-i-delete-my-app-service-plan"></a>Waarom kan ik mijn abonnement App Service verwijderen?

U kunt een abonnement App Service verwijderen als er App Service zijn gekoppeld aan het App Service abonnement. Voordat u een App Service verwijdert, verwijdert u alle gekoppelde App Service-apps uit het App Service abonnement.

## <a name="how-do-i-schedule-a-webjob"></a>Hoe kan ik webjob plannen?

U kunt een geplande webjob maken met behulp van Cron-expressies:

1. Maak een settings.job-bestand.
2. Neem in dit JSON-bestand een schema-eigenschap op met behulp van een Cron-expressie: 
    ```json
    { "schedule": "{second}
    {minute} {hour} {day}
    {month} {day of the week}" }
    ```

Zie Een geplande webjob maken met behulp van een Cron-expressie voor meer informatie over [geplande webjobs.](webjobs-create.md#CreateScheduledCRON)

## <a name="how-do-i-perform-penetration-testing-for-my-app-service-app"></a>Hoe kan ik penetratietests uitvoeren voor mijn App Service app?

Als u penetratietests wilt uitvoeren, [dient u een aanvraag in.](https://portal.msrc.microsoft.com/engage/pentest)

## <a name="how-do-i-configure-a-custom-domain-name-for-an-app-service-web-app-that-uses-traffic-manager"></a>Hoe kan ik een aangepaste domeinnaam configureren voor een App Service web-app die gebruikmaakt van Traffic Manager?

Zie Een aangepaste domeinnaam configureren voor een [Azure-web-app](configure-domain-traffic-manager.md)met Traffic Manager voor meer informatie over het gebruik van een aangepaste domeinnaam met een App Service-app die gebruikmaakt van Azure Traffic Manager voor taakverdeling.

## <a name="my-app-service-certificate-is-flagged-for-fraud-how-do-i-resolve-this"></a>Mijn App Service-certificaat is gemarkeerd voor fraude. Hoe los ik dit op?

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Tijdens de domeinverificatie van een App Service certificaataankoop ziet u mogelijk het volgende bericht:

'Uw certificaat is gemarkeerd voor mogelijke fraude. De aanvraag wordt momenteel beoordeeld. Als het certificaat niet binnen 24 uur kan worden bruikbaar, neemt u contact op met Ondersteuning voor Azure.

Zoals in het bericht wordt aangegeven, kan het tot 24 uur duren voordat dit fraudecontroleproces is voltooid. Gedurende deze tijd blijft u het bericht zien.

Als uw App Service dit bericht na 24 uur nog steeds wordt weergegeven, moet u het volgende PowerShell-script uitvoeren. Het script neemt rechtstreeks contact op [met de certificaatprovider](https://www.godaddy.com/) om het probleem op te lossen.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subId>
$actionProperties = @{
    "Name"= "<Customer Email Address>"
    };
Invoke-AzResourceAction -ResourceGroupName "<App Service Certificate Resource Group Name>" -ResourceType Microsoft.CertificateRegistration/certificateOrders -ResourceName "<App Service Certificate Resource Name>" -Action resendRequestEmails -Parameters $actionProperties -ApiVersion 2015-08-01 -Force   
```

## <a name="how-do-authentication-and-authorization-work-in-app-service"></a>Hoe werken verificatie en autorisatie in App Service?

Voor gedetailleerde documentatie over verificatie en autorisatie in App Service, zie docs for various identify provider sign-ins:
* [Azure Active Directory](configure-authentication-provider-aad.md)
* [Facebook](configure-authentication-provider-facebook.md)
* [Google](configure-authentication-provider-google.md)
* [Microsoft-account](configure-authentication-provider-microsoft.md)
* [Twitter](configure-authentication-provider-twitter.md)

## <a name="how-do-i-redirect-the-default-azurewebsitesnet-domain-to-my-azure-web-apps-custom-domain"></a>Hoe kan ik standaarddomein *.azurewebsites.net omleiden naar het aangepaste domein van mijn Azure-web-app?

Wanneer u een nieuwe website maakt met behulp van Web Apps in Azure, wordt een *standaardsitenaam*.azurewebsites.net-domein toegewezen aan uw site. Als u een aangepaste hostnaam aan uw site toevoegt en niet wilt dat gebruikers toegang hebben tot uw standaarddomein *.azurewebsites.net, kunt u de standaard-URL omleiden. Zie Het standaarddomein omleiden naar uw aangepaste domein in [Azure-web-apps](https://zainrizvi.io/blog/block-default-azure-websites-domain/)voor meer informatie over het omleiden van al het verkeer van het standaarddomein van uw website naar uw aangepaste domein.

## <a name="how-do-i-determine-which-version-of-net-version-is-installed-in-app-service"></a>Hoe kan ik bepalen welke versie van .NET is geïnstalleerd in App Service?

De snelste manier om de versie te vinden van Microsoft .NET die is geïnstalleerd in App Service is met behulp van de Kudu-console. U hebt toegang tot de Kudu-console vanuit de portal of via de URL van uw App Service app. Zie De geïnstalleerde [.NET-versie](/archive/blogs/waws/how-to-determine-the-installed-net-version-in-azure-app-services)bepalen in App Service voor gedetailleerde instructies.

## <a name="why-isnt-autoscale-working-as-expected"></a>Waarom werkt automatisch schalen niet zoals verwacht?

Als automatisch schalen van Azure niet is in- of uitschalen van het web-app-exemplaar zoals verwacht, komt u mogelijk tegen een scenario aan waarin we er opzettelijk voor kiezen om niet te schalen om een oneindige lus te voorkomen vanwege 'flapping'. Dit gebeurt meestal wanneer er geen voldoende marge is tussen de drempelwaarden voor uitschalen en inschalen. Zie Best practices voor automatisch schalen voor meer informatie over het voorkomen van 'flapping' en voor meer informatie over andere best practices voor automatisch [schalen.](../azure-monitor/autoscale/autoscale-best-practices.md#autoscale-best-practices)

## <a name="why-does-autoscale-sometimes-scale-only-partially"></a>Waarom wordt automatisch schalen soms slechts gedeeltelijk geschaald?

Automatisch schalen wordt geactiveerd wanneer metrische gegevens de vooraf geconfigureerde grenzen overschrijden. Soms merkt u misschien dat de capaciteit slechts gedeeltelijk is gevuld in vergelijking met wat u verwacht. Dit kan gebeuren wanneer het aantal instanties dat u wilt niet beschikbaar zijn. In dat scenario wordt Automatisch schalen gedeeltelijk gevuld met het beschikbare aantal exemplaren. Automatisch schalen voert vervolgens de logica voor opnieuw in balans brengen uit om meer capaciteit te krijgen. De resterende exemplaren worden toegewezen. Houd er rekening mee dat dit enkele minuten kan duren.

Als u na een paar minuten niet het verwachte aantal exemplaren ziet, kan dit zijn omdat de gedeeltelijke metrische gegevens voldoende waren om de metrische gegevens binnen de grenzen te brengen. Het is ook mogelijk dat automatisch schalen omlaag is geschaald omdat de ondergrens voor metrische gegevens is bereikt.

Als geen van deze voorwaarden van toepassing is en het probleem zich blijft voordoen, dient u een ondersteuningsaanvraag in.

## <a name="how-do-i-turn-on-http-compression-for-my-content"></a>Hoe kan ik HTTP-compressie voor mijn inhoud in?

Als u compressie zowel voor statische als dynamische inhoudstypen wilt in- of in- of uitdrukken, voegt u de volgende code toe aan het web.config toepassingsniveau:

```xml
<system.webServer>
    <urlCompression doStaticCompression="true" doDynamicCompression="true" />
</system.webServer>
```

U kunt ook de specifieke dynamische en statische MIME-typen opgeven die u wilt comprimeren. Zie onze reactie op een forumvraag in [httpCompression-instellingen op een eenvoudige Azure-website](https://social.msdn.microsoft.com/Forums/azure/890b6d25-f7dd-4272-8970-da7798bcf25d/httpcompression-settings-on-a-simple-azure-website?forum=windowsazurewebsitespreview)voor meer informatie.

## <a name="how-do-i-migrate-from-an-on-premises-environment-to-app-service"></a>Hoe kan ik migreren van een on-premises omgeving naar App Service?

Als u sites wilt migreren van Windows- en Linux-webservers naar App Service, kunt u Azure App Service Migration Assistant. Het hulpprogramma voor migratie maakt zo nodig web-apps en databases in Azure en publiceert vervolgens de inhoud. Zie voor meer informatie [Azure App Service Migration Assistant](https://appmigration.microsoft.com/).

## <a name="why-is-my-certificate-issued-for-11-months-and-not-for-a-full-year"></a>Waarom is mijn certificaat uitgegeven voor 11 maanden en niet voor een volledig jaar?

Voor alle certificaten die na 1/9/2020 zijn uitgegeven, is de maximale duur nu 397 dagen. Certificaten die zijn uitgegeven vóór 1/9/2020, hebben een maximale geldigheidsduur van 825 dagen tot ze worden verlengd, opnieuw versleuteld enz. Alle certificaten die na 1/9/2020 worden verlengd, worden beïnvloed door deze wijziging en gebruikers kunnen een korte geldigheidsduur van de verlengde certificaten opmerken.
GoDaddy heeft een abonnementsservice geïmplementeerd die voldoet aan de nieuwe vereisten terwijl bestaande klantcertificaten worden gehonoreerd. Dertig dagen voordat het zojuist uitgegeven certificaat verloopt, geeft de service automatisch een tweede certificaat uit dat de duur uitbreidt naar de oorspronkelijke vervaldatum. App Service werkt samen met GoDaddy om deze wijziging aan te pakken en ervoor te zorgen dat onze klanten de volledige duur van hun certificaten ontvangen.
