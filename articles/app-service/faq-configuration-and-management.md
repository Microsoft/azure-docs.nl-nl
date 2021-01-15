---
title: Veelgestelde vragen over configuratie
description: Krijg antwoorden op veelgestelde vragen over problemen met de configuratie en het beheer van Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: df4bd0ae0884feae8bd21e33f4d27b6ceb207337
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98233998"
---
# <a name="configuration-and-management-faqs-for-web-apps-in-azure"></a>Veelgestelde vragen over configuratie en beheer voor Web Apps in azure

In dit artikel vindt u antwoorden op veelgestelde vragen over configuratie-en beheer problemen voor de [Web apps functie van Azure app service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="are-there-limitations-i-should-be-aware-of-if-i-want-to-move-app-service-resources"></a>Zijn er beperkingen waar ik rekening mee moet houden als ik App Service resources wil verplaatsen?

Als u van plan bent App Service resources te verplaatsen naar een nieuwe resource groep of een nieuw abonnement, zijn er enkele beperkingen waar u rekening mee moet houden. Zie [app service beperkingen](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)voor meer informatie.

## <a name="how-do-i-use-a-custom-domain-name-for-my-web-app"></a>Hoe kan ik een aangepaste domein naam voor mijn web-app gebruiken?

Voor antwoorden op veelgestelde vragen over het gebruik van een aangepaste domein naam met uw Azure-web-app, zie onze video over zeven minuten [een aangepaste domein naam toevoegen](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name). De video biedt een overzicht van het toevoegen van een aangepaste domein naam. Hierin wordt beschreven hoe u uw eigen URL gebruikt in plaats van de *. azurewebsites.net-URL met uw App Service web-app. U ziet ook een gedetailleerd overzicht van [hoe u een aangepaste domein naam kunt toewijzen](app-service-web-tutorial-custom-domain.md).


## <a name="how-do-i-purchase-a-new-custom-domain-for-my-web-app"></a>Hoe kan ik een nieuw aangepast domein voor mijn web-app aanschaffen?

Zie [een aangepaste domein naam kopen en configureren in app service](manage-custom-dns-buy-domain.md)voor meer informatie over het kopen en instellen van een aangepast domein voor uw app service Web-app.


## <a name="how-do-i-upload-and-configure-an-existing-tlsssl-certificate-for-my-web-app"></a>Hoe kan ik een bestaand TLS/SSL-certificaat voor mijn web-app uploaden en configureren?

Zie [een TLS/SSL-certificaat toevoegen aan uw app service-app](configure-ssl-certificate.md)voor meer informatie over het uploaden en instellen van een bestaand aangepast TLS/SSL-certificaat.


## <a name="how-do-i-purchase-and-configure-a-new-tlsssl-certificate-in-azure-for-my-web-app"></a>Hoe kan ik kopen en configureren van een nieuw TLS/SSL-certificaat in azure voor mijn web-app?

Zie [een TLS/SSL-certificaat toevoegen aan uw app service app](configure-ssl-certificate.md)voor meer informatie over het aanschaffen en instellen van een TLS/SSL-certificaat voor uw app service Web-app.


## <a name="how-do-i-move-application-insights-resources"></a>Application Insights resources Hoe kan ik verplaatsen?

Azure-toepassing Insights biedt momenteel geen ondersteuning voor de verplaatsings bewerking. Als uw oorspronkelijke resource groep een Application Insights resource bevat, kunt u die resource niet verplaatsen. Als u de Application Insights resource opneemt wanneer u een App Service app probeert te verplaatsen, mislukt de hele verplaatsings bewerking. Application Insights en het App Service plan hoeven echter niet in dezelfde resource groep te staan als de app om de app correct te laten functioneren.

Zie [app service beperkingen](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)voor meer informatie.

## <a name="where-can-i-find-a-guidance-checklist-and-learn-more-about-resource-move-operations"></a>Waar vind ik een controle lijst met instructies en meer informatie over het verplaatsen van resources?

[App service beperkingen](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md) laten zien hoe u resources verplaatst naar een nieuw abonnement of naar een nieuwe resource groep in hetzelfde abonnement. U kunt informatie krijgen over de controle lijst voor het verplaatsen van resources, informatie over de services die de verplaatsings bewerking ondersteunen en meer informatie over App Service beperkingen en andere onderwerpen.

## <a name="how-do-i-set-the-server-time-zone-for-my-web-app"></a>Hoe kan ik de tijd zone van de server instellen voor mijn web-app?

De tijd zone van de server instellen voor uw web-app:

1. Ga in het Azure Portal, in uw App Service-abonnement, naar het menu **Toepassings instellingen** .
2. Voeg onder **app-instellingen** deze instelling toe:
    * Sleutel = WEBSITE_TIME_ZONE
    * Waarde = *de tijd zone die u wilt*
3. Selecteer **Opslaan**.

Zie de uitvoer van de Windows-opdracht voor de app-services die worden uitgevoerd in Windows `tzutil /L` . Gebruik de waarde van de tweede regel van elke vermelding. Bijvoorbeeld: "Tonga (standaard tijd)". Sommige van deze waarden worden ook weer gegeven in de kolom **tijd zone** in de [standaard tijd zones](/windows-hardware/manufacture/desktop/default-time-zones).

Voor de app-services die worden uitgevoerd op Linux, stelt u een waarde in van de [IANA TZ-data base](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones). Bijvoorbeeld: "America/Adak".

## <a name="why-do-my-continuous-webjobs-sometimes-fail"></a>Waarom mislukken mijn doorlopende webtaken?

Web-apps worden standaard uit het geheugen verwijderd als ze gedurende een bepaalde periode niet actief zijn. Hiermee kan het systeem bronnen besparen. In Basic-en Standard-abonnementen kunt u de instelling **altijd op** inschakelen om de web-app voortdurend te laden. Als uw web-app doorlopende webtaken uitvoert, moet u **altijd** inschakelen of de webjobs worden mogelijk niet betrouwbaar uitgevoerd. Zie [een voortdurend actieve Webtaak maken](webjobs-create.md#CreateContinuous)voor meer informatie.

## <a name="how-do-i-get-the-outbound-ip-address-for-my-web-app"></a>Hoe kan ik het uitgaande IP-adres voor mijn web-app ophalen?

U kunt als volgt de lijst met uitgaande IP-adressen voor uw web-app ophalen:

1. Ga in de Azure Portal op de Blade van de web-app naar het menu **Eigenschappen** .
2. Zoek naar **uitgaande IP-adressen**.

De lijst met uitgaande IP-adressen wordt weer gegeven.

Zie [uitgaande netwerk adressen](environment/app-service-app-service-environment-network-architecture-overview.md#outbound-network-addresses)voor meer informatie over het ophalen van het uitgaande IP-adres als uw website wordt gehost in een app service environment.

## <a name="how-do-i-get-a-reserved-or-dedicated-inbound-ip-address-for-my-web-app"></a>Hoe kan ik een gereserveerd of specifiek inkomend IP-adres voor mijn web-app ophalen?

Voor het instellen van een toegewezen of gereserveerd IP-adres voor inkomende oproepen naar uw Azure app-website, installeert en configureert u een TLS/SSL-certificaat op basis van IP.

Houd er rekening mee dat voor het gebruik van een toegewezen of gereserveerd IP-adres voor inkomende oproepen uw App Service plan moet zijn opgenomen in een basis-of hoger service abonnement.

## <a name="can-i-export-my-app-service-certificate-to-use-outside-azure-such-as-for-a-website-hosted-elsewhere"></a>Kan ik mijn App Service-certificaat exporteren voor gebruik buiten Azure, zoals voor een website die elders wordt gehost? 

Ja, u kunt ze exporteren voor gebruik buiten Azure. Zie [Veelgestelde vragen voor app service certificaten en aangepaste domeinen](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview)voor meer informatie.

## <a name="can-i-export-my-app-service-certificate-to-use-with-other-azure-cloud-services"></a>Kan ik mijn App Service-certificaat exporteren voor gebruik met andere Azure-Cloud Services?

De portal biedt een eersteklas ervaring voor het implementeren van een App Service-certificaat via Azure Key Vault tot App Service apps. We hebben echter aanvragen ontvangen van klanten voor het gebruik van deze certificaten buiten het App Service-platform, bijvoorbeeld met Azure Virtual Machines. Zie [een lokale pfx-kopie maken van een app service certificaat](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/)voor meer informatie over het maken van een lokale pfx-kopie van uw app service-certificaat zodat u het certificaat met andere Azure-resources kunt gebruiken.

Zie [Veelgestelde vragen voor app service certificaten en aangepaste domeinen](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview)voor meer informatie.


## <a name="why-do-i-see-the-message-partially-succeeded-when-i-try-to-back-up-my-web-app"></a>Waarom wordt het bericht ' gedeeltelijk geslaagd ' weer gegeven wanneer ik een back-up wil maken van mijn web-app?

Een veelvoorkomende oorzaak van het mislukken van de back-up is dat sommige bestanden door de toepassing worden gebruikt. Bestanden die in gebruik zijn, worden vergrendeld tijdens het uitvoeren van de back-up. Hiermee wordt voor komen dat er een back-up van deze bestanden wordt gemaakt. Dit kan resulteren in een ' gedeeltelijk geslaagde ' status. U kunt dit voor komen door bestanden van het back-upproces uit te sluiten. U kunt ervoor kiezen om alleen een back-up te maken van wat er nodig is. Zie voor meer informatie [back-ups maken van alleen de belang rijke onderdelen van uw site met Azure web apps](https://zainrizvi.io/blog/creating-partial-backups-of-your-site-with-azure-web-apps/).

## <a name="how-do-i-remove-a-header-from-the-http-response"></a>Hoe kan ik een header uit het HTTP-antwoord verwijderen?

Als u de kopteksten van het HTTP-antwoord wilt verwijderen, moet u het web.config-bestand van de site bijwerken. Zie [standaard server headers op uw Azure-websites verwijderen](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)voor meer informatie.

## <a name="is-app-service-compliant-with-pci-standard-30-and-31"></a>Is App Service compatibel met de PCI-standaard 3,0 en 3,1?

Op dit moment is de Web Apps-functie van Azure App Service in overeenstemming met de versie 1 van het PCI Data Security Standard (DSS) 3,0. PCI DSS versie 3,1 is op ons plan. Er wordt al gepland hoe de invoering van de nieuwste standaard wordt voortgezet.

Voor PCI DSS versie 3,1-certificering is het uitschakelen van Transport Layer Security (TLS) 1,0 vereist. Op dit moment is het uitschakelen van TLS 1,0 geen optie voor de meeste App Service-abonnementen. Als u echter App Service Environment gebruikt of als u bereid bent om uw werk belasting naar App Service Environment te migreren, kunt u meer controle krijgen over uw omgeving. Hiervoor moet u TLS 1,0 uitschakelen door contact op te nemen met de ondersteuning van Azure. In de nabije toekomst gaan we deze instellingen voor gebruikers toegankelijk maken.

Zie [Microsoft Azure app service Web-app-compatibiliteit met PCI Standard 3,0 en 3,1](https://support.microsoft.com/help/3124528)voor meer informatie.

## <a name="how-do-i-use-the-staging-environment-and-deployment-slots"></a>Hoe kan ik de faserings omgeving en implementatie sleuven gebruiken?

Wanneer u uw Web-App Service app implementeert in Standard-en Premium App Service-abonnementen, kunt u deze implementeren op een afzonderlijke implementatie site in plaats van naar de standaard productie sleuf. Implementatie sites zijn live web apps met hun eigen hostnamen. Inhoud van de web-app en configuratie-elementen kunnen worden omgewisseld tussen twee implementatie sleuven, met inbegrip van de productie site.

Zie [een faserings omgeving instellen in app service](deploy-staging-slots.md)voor meer informatie over het gebruik van implementatie sites.

## <a name="how-do-i-access-and-review-webjob-logs"></a>Hoe kan ik toegang en de weer geven webtaaks logboeken?

Webtaaks logboeken bekijken:

1. Meld u aan bij uw **kudu-website** ( `https://*yourwebsitename*.scm.azurewebsites.net` ).
2. Selecteer de Webtaak.
3. Selecteer de knop **uitvoer in-/uitschakelen** .
4. Selecteer de **Download** koppeling om het uitvoer bestand te downloaden.
5. Selecteer **afzonderlijke aanroepen** voor afzonderlijke uitvoeringen.
6. Selecteer de knop **uitvoer in-/uitschakelen** .
7. Selecteer de download koppeling.

## <a name="im-trying-to-use-hybrid-connections-with-sql-server-why-do-i-see-the-message-systemoverflowexception-arithmetic-operation-resulted-in-an-overflow"></a>Ik probeer Hybride verbindingen te gebruiken met SQL Server. Waarom wordt het bericht ' System. OverflowException: reken kundige bewerking resulteerde in een overloop '?

Als u Hybride verbindingen gebruikt voor toegang tot SQL Server, kan een Microsoft .NET update op 10 mei 2016 ertoe leiden dat verbindingen mislukken. Dit bericht kan worden weer gegeven:

```
Exception: System.Data.Entity.Core.EntityException: The underlying provider failed on Open. —> System.OverflowException: Arithmetic operation resulted in an overflow. or (64 bit Web app) System.OverflowException: Array dimensions exceeded supported range, at System.Data.SqlClient.TdsParser.ConsumePreLoginHandshake
```

### <a name="resolution"></a>Oplossing

De uitzonde ring is veroorzaakt door een probleem met de Hybrid Connection Manager dat sindsdien is opgelost. Zorg ervoor dat u [uw Hybrid Connection Manager bijwerkt](https://go.microsoft.com/fwlink/?LinkID=841308) om dit probleem op te lossen.

## <a name="how-do-i-add-a-url-rewrite-rule"></a>Hoe kan ik een regel voor het opnieuw schrijven van een URL toevoegen?

Als u een regel voor het herschrijven van een URL wilt toevoegen, maakt u een web.config bestand met de relevante configuratie vermeldingen in de map **wwwroot** . Zie [Azure-app Services: informatie over het herschrijven van url's](/archive/blogs/madhurabharadwaj/azure-app-services-understanding-url-re-write)voor meer informatie.

## <a name="how-do-i-control-inbound-traffic-to-app-service"></a>Hoe kan ik inkomend verkeer naar App Service beheren?

Op site niveau hebt u twee opties voor het beheren van inkomend verkeer naar App Service:

* Schakel dynamische IP-beperkingen in. Zie [IP-en domein beperkingen voor Azure websites](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/)voor meer informatie over het inschakelen van dynamische IP-beperkingen.
* Module beveiliging inschakelen. Zie [ModSecurity Web Application firewall op Azure websites](https://azure.microsoft.com/blog/modsecurity-for-azure-websites/)voor meer informatie over het inschakelen van module beveiliging.

Als u App Service Environment gebruikt, kunt u [Barracuda-firewall](https://azure.microsoft.com/blog/configuring-barracuda-web-application-firewall-for-azure-app-service-environment/)gebruiken.

## <a name="how-do-i-block-ports-in-an-app-service-web-app"></a>Poorten in een App Service web-app Hoe kan ik blok keren?

In de App Service gedeelde Tenant omgeving is het niet mogelijk specifieke poorten te blok keren vanwege de aard van de infra structuur. De TCP-poorten 4020, 4022 en 4024 kunnen ook zijn geopend voor fout opsporing op afstand van Visual Studio.

In App Service Environment hebt u volledige controle over binnenkomend en uitgaand verkeer. U kunt netwerk beveiligings groepen gebruiken om specifieke poorten te beperken of blok keren. Zie [Inleiding tot app service Environment](https://azure.microsoft.com/blog/introducing-app-service-environment/)voor meer informatie over app service environment.

## <a name="how-do-i-capture-an-f12-trace"></a>Hoe kan ik een F12-tracering vastleggen?

U hebt twee opties voor het vastleggen van een F12-tracering:

* F12 HTTP-tracering
* F12-console-uitvoer

### <a name="f12-http-trace"></a>F12 HTTP-tracering

1. Ga in Internet Explorer naar uw website. Het is belang rijk dat u zich aanmeldt voordat u de volgende stappen uitvoert. Als dat niet het geval is, worden met de F12-tracering gevoelige aanmeldings gegevens vastgelegd.
2. Druk op F12.
3. Controleer of het tabblad **netwerk** is geselecteerd en selecteer vervolgens de knop groen **afspelen** .
4. Voer de stappen uit om het probleem te reproduceren.
5. Selecteer de knop rood **stoppen** .
6. Selecteer de knop **Opslaan** (schijf pictogram) en sla het har-bestand op (in Internet Explorer en micro soft Edge) *of* Klik met de rechter muisknop op het har-bestand en selecteer vervolgens **Opslaan als har met inhoud** (Chrome).

### <a name="f12-console-output"></a>F12-console-uitvoer

1. Selecteer het tabblad **console** .
2. Voor elk tabblad met meer dan nul items, selecteert u het tabblad (**fout**, **waarschuwing** of **informatie**). Als het tabblad niet is geselecteerd, is het pictogram van het tabblad grijs of zwart wanneer u de cursor uit het verplaatst.
3. Klik met de rechter muisknop in het bericht gebied van het deel venster en selecteer vervolgens **Alles kopiëren**.
4. Plak de gekopieerde tekst in een bestand en sla het bestand op.

U kunt de [har-Viewer](http://www.softwareishard.com/har/viewer/)gebruiken om een har-bestand weer te geven.

## <a name="why-do-i-get-an-error-when-i-try-to-connect-an-app-service-web-app-to-a-virtual-network-that-is-connected-to-expressroute"></a>Waarom krijg ik een fout melding wanneer ik een App Service web-app probeer te verbinden met een virtueel netwerk dat is verbonden met ExpressRoute?

Als u probeert een Azure-web-app te verbinden met een virtueel netwerk dat is verbonden met Azure ExpressRoute, mislukt dit. Het volgende bericht wordt weer gegeven: gateway is geen VPN-gateway.

Op dit moment kunt u geen punt-naar-site-VPN-verbindingen hebben met een virtueel netwerk dat is verbonden met ExpressRoute. Een punt-naar-site-VPN en ExpressRoute kunnen niet worden gecombineerd voor hetzelfde virtuele netwerk. Zie [limieten en beperkingen voor ExpressRoute-en site-naar-site-VPN-verbindingen](../expressroute/expressroute-howto-coexist-classic.md#limits-and-limitations)voor meer informatie.

## <a name="how-do-i-connect-an-app-service-web-app-to-a-virtual-network-that-has-a-static-routing-policy-based-gateway"></a>Hoe kan ik een App Service web-app verbinden met een virtueel netwerk dat een statische routerings gateway (op basis van beleid) heeft?

Op dit moment wordt het verbinden van een App Service web-app met een virtueel netwerk met een statische routerings gateway (op beleid gebaseerd) niet ondersteund. Als het virtuele netwerk van het doel al bestaat, moet er een punt-naar-site-VPN zijn ingeschakeld, met een dynamische routerings gateway voordat het kan worden verbonden met een app. Als uw gateway is ingesteld op statische route ring, kunt u geen punt-naar-site-VPN inschakelen. 

Zie [een app integreren met een virtueel Azure-netwerk](web-sites-integrate-with-vnet.md)voor meer informatie.

## <a name="in-my-app-service-environment-why-can-i-create-only-one-app-service-plan-even-though-i-have-two-workers-available"></a>Waarom kan ik in mijn App Service Environment slechts één App Service plan maken, zelfs als ik twee werk rollen heb?

Om fout tolerantie te bieden, vereist App Service Environment dat elke werk groep ten minste één extra reken resource nodig heeft. Er kan geen werk belasting worden toegewezen aan de extra Compute-resource.

Zie [How to Create a app service Environment](environment/app-service-web-how-to-create-an-app-service-environment.md)voor meer informatie.

## <a name="why-do-i-see-timeouts-when-i-try-to-create-an-app-service-environment"></a>Waarom zie ik time-outs wanneer ik een App Service Environment probeer te maken?

Soms mislukt het maken van een App Service Environment. In dat geval ziet u de volgende fout in de activiteiten logboeken:
```
ResourceID: /subscriptions/{SubscriptionID}/resourceGroups/Default-Networking/providers/Microsoft.Web/hostingEnvironments/{ASEname}
Error:{"error":{"code":"ResourceDeploymentFailure","message":"The resource provision operation did not complete within the allowed timeout period."}}
```

U kunt dit oplossen door ervoor te zorgen dat geen van de volgende voor waarden waar is:
* Het subnet is te klein.
* Het subnet is niet leeg.
* ExpressRoute voor komt dat de netwerk connectiviteits vereisten van een App Service Environment.
* Een slechte netwerk beveiligings groep voor komt de netwerk verbindings vereisten van een App Service Environment.
* Geforceerde tunneling is ingeschakeld.

Zie [frequente problemen bij het implementeren (maken) van een nieuwe Azure app service Environment](/archive/blogs/waws/most-frequent-issues-when-deploying-creating-a-new-azure-app-service-environment-ase)voor meer informatie.

## <a name="why-cant-i-delete-my-app-service-plan"></a>Waarom kan ik mijn App Service-abonnement niet verwijderen?

U kunt een App Service plan niet verwijderen als er App Service apps zijn gekoppeld aan het App Service plan. Voordat u een App Service-abonnement verwijdert, verwijdert u alle gekoppelde App Service apps uit het App Service plan.

## <a name="how-do-i-schedule-a-webjob"></a>Hoe kan ik een Webtaak plannen?

U kunt een geplande Webtaak maken met behulp van cron-expressies:

1. Maak een bestand met de instellingen. job.
2. Neem in dit JSON-bestand een eigenschap schema op met behulp van een cron-expressie: 
    ```json
    { "schedule": "{second}
    {minute} {hour} {day}
    {month} {day of the week}" }
    ```

Zie [een geplande Webtaak maken met behulp van een cron-expressie](webjobs-create.md#CreateScheduledCRON)voor meer informatie over geplande webjobs.

## <a name="how-do-i-perform-penetration-testing-for-my-app-service-app"></a>Hoe kan ik indringings tests uitvoeren voor mijn App Service-app?

[Dien een aanvraag](https://portal.msrc.microsoft.com/engage/pentest)in om indringings tests uit te voeren.

## <a name="how-do-i-configure-a-custom-domain-name-for-an-app-service-web-app-that-uses-traffic-manager"></a>Hoe kan ik een aangepaste domein naam configureren voor een App Service web-app die gebruikmaakt van Traffic Manager?

Zie [een aangepaste domein naam configureren voor een Azure-web-app met Traffic Manager](configure-domain-traffic-manager.md)voor meer informatie over het gebruik van een aangepaste domein naam met een app service-app die gebruikmaakt van Azure Traffic Manager voor taak verdeling.

## <a name="my-app-service-certificate-is-flagged-for-fraud-how-do-i-resolve-this"></a>Mijn App Service-certificaat is gemarkeerd voor fraude. Hoe los ik dit op?

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Tijdens de domein verificatie van een App Service certificaat kan het volgende bericht worden weer gegeven:

"Uw certificaat is gemarkeerd voor mogelijke fraude. De aanvraag wordt momenteel gecontroleerd. Als het certificaat binnen 24 uur niet kan worden gebruikt, neemt u contact op met de ondersteuning van Azure. "

Wanneer het bericht aangeeft, kan het tot 24 uur duren voordat het proces voor fraude verificatie is voltooid. Gedurende deze periode wordt het bericht nog steeds weer gegeven.

Als uw App Service-certificaat dit bericht na 24 uur blijft weer geven, voert u het volgende Power shell-script uit. Het script neemt rechtstreeks contact op met de [certificaat provider](https://www.godaddy.com/) om het probleem op te lossen.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subId>
$actionProperties = @{
    "Name"= "<Customer Email Address>"
    };
Invoke-AzResourceAction -ResourceGroupName "<App Service Certificate Resource Group Name>" -ResourceType Microsoft.CertificateRegistration/certificateOrders -ResourceName "<App Service Certificate Resource Name>" -Action resendRequestEmails -Parameters $actionProperties -ApiVersion 2015-08-01 -Force   
```

## <a name="how-do-authentication-and-authorization-work-in-app-service"></a>Hoe werken verificatie en autorisatie in App Service?

Zie voor gedetailleerde documentatie voor verificatie en autorisatie in App Service documenten voor verschillende aanmeldingen voor identiteits providers:
* [Azure Active Directory](configure-authentication-provider-aad.md)
* [Facebook](configure-authentication-provider-facebook.md)
* [Google](configure-authentication-provider-google.md)
* [Microsoft-account](configure-authentication-provider-microsoft.md)
* [Twitter](configure-authentication-provider-twitter.md)

## <a name="how-do-i-redirect-the-default-azurewebsitesnet-domain-to-my-azure-web-apps-custom-domain"></a>Hoe kan ik het standaard domein *. azurewebsites.net omleiden naar het aangepaste domein van mijn Azure-web-app?

Wanneer u een nieuwe website maakt met behulp van Web Apps in azure, wordt een standaard *site naam*. azurewebsites.net domein aan uw site toegewezen. Als u een aangepaste hostnaam aan uw site toevoegt en niet wilt dat gebruikers toegang hebben tot uw standaard-azurewebsites.net-domein, kunt u de standaard-URL omleiden. Zie [het standaard domein omleiden naar uw aangepaste domein in azure web apps](https://zainrizvi.io/blog/block-default-azure-websites-domain/)voor meer informatie over het omleiden van alle verkeer van het standaard domein van uw website naar uw aangepaste domein.

## <a name="how-do-i-determine-which-version-of-net-version-is-installed-in-app-service"></a>Hoe kan ik bepalen welke versie van .NET version in App Service is geïnstalleerd?

De snelste manier om te zoeken naar de versie van Microsoft .NET die in App Service is geïnstalleerd, is met behulp van de kudu-console. U kunt de kudu-console openen vanuit de portal of met behulp van de URL van uw App Service-app. Zie voor gedetailleerde instructies [de geïnstalleerde .net-versie bepalen in app service](/archive/blogs/waws/how-to-determine-the-installed-net-version-in-azure-app-services).

## <a name="why-isnt-autoscale-working-as-expected"></a>Waarom werkt de functie voor automatisch schalen niet zoals verwacht?

Als Azure automatisch schalen niet is geschaald in of geschaald naar verwachting van het web-app-exemplaar, is het mogelijk dat er een scenario wordt gebruikt waarin wordt beoogd niet te schalen om een oneindige lus te voor komen vanwege ' gaat en neer '. Dit gebeurt meestal wanneer er geen geschikte marge is tussen de drempel waarden voor uitschalen en schalen. Zie [Aanbevolen procedures voor automatisch schalen](../azure-monitor/platform/autoscale-best-practices.md#autoscale-best-practices)voor meer informatie over het voor komen van ' gaat en neer ' en het lezen van de best practices voor automatisch schalen.

## <a name="why-does-autoscale-sometimes-scale-only-partially"></a>Waarom wordt de functie voor automatisch schalen soms slechts gedeeltelijk geschaald?

Automatisch schalen wordt geactiveerd wanneer metrische gegevens de vooraf geconfigureerde grenzen overschrijden. Soms zult u merken dat de capaciteit slechts gedeeltelijk is gevuld vergeleken met wat u verwacht. Dit kan gebeuren wanneer het aantal exemplaren dat u wilt, niet beschikbaar is. In dat scenario wordt automatisch schalen gedeeltelijk gevuld met het beschik bare aantal exemplaren. Automatisch schalen voert vervolgens de herverdelings logica uit om meer capaciteit te krijgen. De overige instanties worden toegewezen. Houd er rekening mee dat dit enkele minuten kan duren.

Als u het verwachte aantal exemplaren na een paar minuten niet ziet, kan het zijn dat de gedeeltelijke aanvulling genoeg is om de metrische gegevens binnen de grenzen te brengen. Het kan ook zijn dat de functie voor automatisch schalen omlaag is geschaald omdat de grens van de lagere metrieken is bereikt.

Als geen van deze voor waarden van toepassing is en het probleem zich blijft voordoen, dient u een ondersteunings aanvraag in.

## <a name="how-do-i-turn-on-http-compression-for-my-content"></a>Hoe kan ik HTTP-compressie voor mijn inhoud inschakelen?

Als u compressie wilt inschakelen voor statische en dynamische inhouds typen, voegt u de volgende code toe aan het web.config-bestand op toepassings niveau:

```xml
<system.webServer>
    <urlCompression doStaticCompression="true" doDynamicCompression="true" />
</system.webServer>
```

U kunt ook de specifieke dynamische en statische MIME-typen opgeven die u wilt comprimeren. Zie voor meer informatie onze reactie op een forum vraag in [httpCompression-instellingen op een eenvoudige Azure-website](https://social.msdn.microsoft.com/Forums/azure/890b6d25-f7dd-4272-8970-da7798bcf25d/httpcompression-settings-on-a-simple-azure-website?forum=windowsazurewebsitespreview).

## <a name="how-do-i-migrate-from-an-on-premises-environment-to-app-service"></a>Hoe kan ik migreren van een on-premises omgeving naar App Service?

Als u sites van Windows-en Linux-webservers wilt migreren naar App Service, kunt u Azure App Service Migration Assistant gebruiken. Het hulp programma voor migratie maakt indien nodig web-apps en data bases en publiceert vervolgens de inhoud. Zie [Azure App Service Migration Assistant](https://appmigration.microsoft.com/)voor meer informatie.

## <a name="why-is-my-certificate-issued-for-11-months-and-not-for-a-full-year"></a>Waarom is mijn certificaat uitgegeven voor 11 maanden en niet voor een volledig jaar?

Voor alle certificaten die na 1/9/2020 zijn uitgegeven, is de maximale duur nu 397 dagen. Certificaten die zijn uitgegeven vóór 1/9/2020, hebben een maximale geldigheidsduur van 825 dagen tot ze worden verlengd, opnieuw versleuteld enz. Alle certificaten die na 1/9/2020 worden verlengd, worden beïnvloed door deze wijziging en gebruikers kunnen een korte geldigheidsduur van de verlengde certificaten opmerken.
GoDaddy heeft een abonnementsservice geïmplementeerd die voldoet aan de nieuwe vereisten terwijl bestaande klantcertificaten worden gehonoreerd. Dertig dagen voordat het zojuist uitgegeven certificaat verloopt, geeft de service automatisch een tweede certificaat uit dat de duur uitbreidt naar de oorspronkelijke vervaldatum. App Service werkt samen met GoDaddy om deze wijziging aan te pakken en ervoor te zorgen dat onze klanten de volledige duur van hun certificaten ontvangen.
