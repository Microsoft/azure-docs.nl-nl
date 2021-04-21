---
title: Problemen met domein- en TLS/SSL-certificaten oplossen
description: Zoek oplossingen voor de veelvoorkomende problemen die kunnen ontstaan bij het configureren van een domein of TLS/SSL-certificaat in Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.topic: article
ms.date: 03/01/2019
ms.author: genli
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: c2c09e1a30c9cef4d65b2d5443481c84ab779af8
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833828"
---
# <a name="troubleshoot-domain-and-tlsssl-certificate-problems-in-azure-app-service"></a>Problemen met domein- en TLS/SSL-certificaten in Azure App Service

In dit artikel worden veelvoorkomende problemen beschreven die kunnen ontstaan wanneer u een domein of TLS/SSL-certificaat configureert voor uw web-apps in Azure App Service. Ook worden mogelijke oorzaken en oplossingen voor deze problemen beschreven.

Als u op enig moment in dit artikel meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op de [MSDN-](https://azure.microsoft.com/support/forums/)en Stack Overflow forums. U kunt ook een incident ondersteuning voor Azure indienen. Ga naar de [Ondersteuning voor Azure site en](https://azure.microsoft.com/support/options/) selecteer Ondersteuning **krijgen.**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="certificate-problems"></a>Certificaatproblemen

### <a name="you-cant-add-a-tlsssl-certificate-binding-to-an-app"></a>U kunt geen TLS/SSL-certificaatbinding toevoegen aan een app 

#### <a name="symptom"></a>Symptoom

Wanneer u een TLS-binding toevoegt, ontvangt u het volgende foutbericht:

'Kan SSL-binding niet toevoegen. Kan geen certificaat instellen voor bestaand VIP omdat een ander VIP dat certificaat al gebruikt.

#### <a name="cause"></a>Oorzaak

Dit probleem kan optreden als u meerdere op IP gebaseerde SSL-bindingen hebt voor hetzelfde IP-adres in meerdere apps. App A heeft bijvoorbeeld een op IP gebaseerde SSL met een oud certificaat. App B heeft een op IP gebaseerde SSL met een nieuw certificaat voor hetzelfde IP-adres. Wanneer u de app-TLS-binding met het nieuwe certificaat bij werkt, mislukt deze fout omdat hetzelfde IP-adres wordt gebruikt voor een andere app. 

#### <a name="solution"></a>Oplossing 

Gebruik een van de volgende methoden om dit probleem op te lossen:

- Verwijder de SSL-binding op basis van IP voor de app die gebruikmaakt van het oude certificaat. 
- Maak een nieuwe SSL-binding op basis van IP die gebruikmaakt van het nieuwe certificaat.

### <a name="you-cant-delete-a-certificate"></a>U kunt een certificaat niet verwijderen 

#### <a name="symptom"></a>Symptoom

Wanneer u probeert een certificaat te verwijderen, ontvangt u het volgende foutbericht:

"Kan het certificaat niet verwijderen omdat het momenteel wordt gebruikt in een TLS/SSL-binding. De TLS-binding moet worden verwijderd voordat u het certificaat kunt verwijderen.

#### <a name="cause"></a>Oorzaak

Dit probleem kan optreden als een andere app gebruikmaakt van het certificaat.

#### <a name="solution"></a>Oplossing

Verwijder de TLS-binding voor dat certificaat uit de apps. Probeer vervolgens het certificaat te verwijderen. Als u het certificaat nog steeds niet kunt verwijderen, verwijdert u de cache van de internetbrowser en opent u de Azure Portal opnieuw in een nieuw browservenster. Probeer vervolgens het certificaat te verwijderen.

### <a name="you-cant-purchase-an-app-service-certificate"></a>U kunt geen certificaat voor App Service aanschaffen 

#### <a name="symptom"></a>Symptoom
U kunt geen certificaat voor [Azure App Service kopen](./configure-ssl-certificate.md#import-an-app-service-certificate) via de Azure Portal.

#### <a name="cause-and-solution"></a>Oorzaak en oplossing
Dit probleem kan om een van de volgende redenen optreden:

- Het App Service abonnement is Gratis of Gedeeld. Deze prijscategorie biedt geen ondersteuning voor TLS. 

    **Oplossing:** upgrade het App Service app naar Standard.

- Het abonnement heeft geen geldige creditcard.

    **Oplossing:** Voeg een geldige creditcard toe aan uw abonnement. 

- De abonnementsaanbieding biedt geen ondersteuning voor het kopen van App Service certificaat zoals Microsoft Student.  

    **Oplossing:** upgrade uw abonnement. 

- Het abonnement heeft de limiet bereikt voor aankopen die zijn toegestaan voor een abonnement.

    **Oplossing:** App Service hebben een limiet van 10 certificaataankopen voor de abonnementstypen Betalen per gebruik en EA. Voor andere abonnementstypen is de limiet 3. Als u de limiet wilt verhogen, neem dan contact [op met ondersteuning voor Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Het App Service is gemarkeerd als fraude. U hebt het volgende foutbericht ontvangen: 'Uw certificaat is gemarkeerd voor mogelijke fraude. De aanvraag wordt momenteel beoordeeld. Als het certificaat niet binnen 24 uur bruikbaar wordt, neemt u contact op met Ondersteuning voor Azure.

    **Oplossing:** als het certificaat is gemarkeerd als fraude en na 24 uur niet is opgelost, volgt u deze stappen:

    1. Meld u aan bij [Azure Portal](https://portal.azure.com).
    2. Ga naar **App Service certificaten** en selecteer het certificaat.
    3. Selecteer **Certificaatconfiguratie**  >  **Stap 2: domeinverificatie**  >  **verifiëren.** In deze stap wordt een e-mailbericht verzonden naar de Azure-certificaatprovider om het probleem op te lossen.

## <a name="custom-domain-problems"></a>Problemen met aangepast domein

### <a name="a-custom-domain-returns-a-404-error"></a>Een aangepast domein retourneert een 404-fout 

#### <a name="symptom"></a>Symptoom

Wanneer u naar de site bladert met behulp van de aangepaste domeinnaam, ontvangt u het volgende foutbericht:

Fout 404-Web-app niet gevonden.

#### <a name="cause-and-solution"></a>Oorzaak en oplossing

**Oorzaak 1** 

Er ontbreekt een CNAME- of A-record in het aangepaste domein dat u hebt geconfigureerd. 

**Oplossing voor oorzaak 1**

- Als u een A-record hebt toegevoegd, moet u ervoor zorgen dat er ook een TXT-record wordt toegevoegd. Zie Create [the A record (De A-record maken) voor meer informatie.](./app-service-web-tutorial-custom-domain.md#create-the-a-record)
- Als u het hoofddomein voor uw app niet hoeft te gebruiken, raden we u aan een CNAME-record te gebruiken in plaats van een A-record.
- Gebruik niet zowel een CNAME-record als een A-record voor hetzelfde domein. Dit probleem kan een conflict veroorzaken en voorkomen dat het domein wordt opgelost. 

**Oorzaak 2** 

Het is mogelijk dat de internetbrowser nog steeds het oude IP-adres voor uw domein in deaching opsport. 

**Oplossing voor oorzaak 2**

De browser verwijderen. Voor Windows-apparaten kunt u de opdracht `ipconfig /flushdns` uitvoeren. Gebruik [WhatsmyDNS.net](https://www.whatsmydns.net/) om te controleren of uw domein naar het IP-adres van de app wijst.

### <a name="you-cant-add-a-subdomain"></a>U kunt geen subdomein toevoegen 

#### <a name="symptom"></a>Symptoom

U kunt geen nieuwe hostnaam toevoegen aan een app om een subdomein toe te wijzen.

#### <a name="solution"></a>Oplossing

- Neem contact op met de abonnementsbeheerder om er zeker van te zijn dat u machtigingen hebt om een hostnaam aan de app toe te voegen.
- Als u meer subdomeinen nodig hebt, raden we u aan de domeinhosting te wijzigen in Azure Domain Name Service (DNS). Met behulp Azure DNS kunt u 500 hostnamen toevoegen aan uw app. Zie Een subdomein toevoegen [voor meer informatie.](/archive/blogs/waws/mapping-a-custom-subdomain-to-an-azure-website)

### <a name="dns-cant-be-resolved"></a>DNS kan niet worden opgelost

#### <a name="symptom"></a>Symptoom

U hebt het volgende foutbericht ontvangen:

"De DNS-record kan niet worden gevonden."

#### <a name="cause"></a>Oorzaak
Dit probleem doet zich voor om een van de volgende redenen:

- De TTL-periode (Time to Live) is niet verlopen. Controleer de DNS-configuratie voor uw domein om de TTL-waarde te bepalen en wacht tot de periode is verlopen.
- De DNS-configuratie is onjuist.

#### <a name="solution"></a>Oplossing
- Wacht 48 uur totdat dit probleem zich vanzelf heeft opgelost.
- Als u de TTL-instelling in uw DNS-configuratie kunt wijzigen, wijzigt u de waarde in 5 minuten om te zien of dit het probleem oplost.
- Gebruik [WhatsmyDNS.net](https://www.whatsmydns.net/) om te controleren of uw domein naar het IP-adres van de app wijst. Als dit niet het juiste IP-adres van de app is, configureert u de A-record.

### <a name="you-need-to-restore-a-deleted-domain"></a>U moet een verwijderd domein herstellen 

#### <a name="symptom"></a>Symptoom
Uw domein is niet meer zichtbaar in de Azure Portal.

#### <a name="cause"></a>Oorzaak 
De eigenaar van het abonnement heeft het domein mogelijk per ongeluk verwijderd.

#### <a name="solution"></a>Oplossing
Als uw domein minder dan zeven dagen geleden is verwijderd, is het verwijderingsproces nog niet gestart door het domein. In dit geval kunt u hetzelfde domein opnieuw kopen op de Azure Portal onder hetzelfde abonnement. (Typ de exacte domeinnaam in het zoekvak.) Er worden geen kosten meer in rekening gebracht voor dit domein. Als het domein meer dan zeven dagen geleden is verwijderd, kunt u contact opnemen met [ondersteuning voor Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) voor hulp bij het herstellen van het domein.

## <a name="domain-problems"></a>Domeinproblemen

### <a name="you-purchased-a-tlsssl-certificate-for-the-wrong-domain"></a>U hebt een TLS/SSL-certificaat aangeschaft voor het verkeerde domein

#### <a name="symptom"></a>Symptoom

U hebt een App Service voor het verkeerde domein aangeschaft. U kunt het certificaat niet bijwerken om het juiste domein te gebruiken.

#### <a name="solution"></a>Oplossing

Verwijder dat certificaat en koop vervolgens een nieuw certificaat.

Als het huidige certificaat dat gebruikmaakt van het verkeerde domein de status 'Uitgegeven' heeft, wordt u ook gefactureerd voor dat certificaat. App Service certificaten kunnen niet worden terugbetaald, maar u kunt contact opnemen [met](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ondersteuning voor Azure om te zien of er andere opties zijn. 

### <a name="an-app-service-certificate-was-renewed-but-the-app-shows-the-old-certificate"></a>Een App Service is vernieuwd, maar de app toont het oude certificaat 

#### <a name="symptom"></a>Symptoom

Het App Service certificaat is vernieuwd, maar de app die het App Service gebruikt, maakt nog steeds gebruik van het oude certificaat. U hebt ook een waarschuwing ontvangen dat het HTTPS-protocol vereist is.

#### <a name="cause"></a>Oorzaak 
App Service wordt uw certificaat automatisch binnen 48 uur gesynchroniseerd. Wanneer u een certificaat roteert of bij werkt, haalt de toepassing soms nog steeds het oude certificaat op en niet het zojuist bijgewerkte certificaat. De reden hiervoor is dat de taak voor het synchroniseren van de certificaatresource nog niet is uitgevoerd. Klik op Synchroniseren. De synchronisatiebewerking werkt automatisch de hostnaambindingen voor het certificaat in App Service zonder downtime voor uw apps te veroorzaken.

#### <a name="solution"></a>Oplossing

U kunt een synchronisatie van het certificaat forcen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **App Service certificaten** en selecteer vervolgens het certificaat.
2. Selecteer **Opnieuw versleutelen en** synchroniseren en selecteer vervolgens **Synchroniseren.** Het duurt enige tijd om de synchronisatie te voltooien. 
3. Wanneer de synchronisatie is voltooid, ziet u de volgende melding: 'Alle resources zijn bijgewerkt met het meest recente certificaat'.

### <a name="domain-verification-is-not-working"></a>Domeinverificatie werkt niet 

#### <a name="symptom"></a>Symptoom 
Het App Service vereist domeinverificatie voordat het certificaat gereed is voor gebruik. Wanneer u Verifiëren **selecteert,** mislukt het proces.

#### <a name="solution"></a>Oplossing
Controleer uw domein handmatig door een TXT-record toe te voegen:

1. Ga naar de Domain Name Service (DNS)-provider die als host fungeert voor uw domeinnaam.
1. Voeg een TXT-record voor uw domein toe die gebruikmaakt van de waarde van het domeintoken dat wordt weergegeven in de Azure Portal. 

Wacht enkele minuten totdat DNS-doorloop is uitgevoerd en selecteer vervolgens de knop **Vernieuwen** om de verificatie te activeren. 

Als alternatief kunt u de HTML-webpaginamethode gebruiken om uw domein handmatig te verifiëren. Met deze methode kan de certificeringsinstantie het domeineigendom bevestigen van het domein waar het certificaat voor is uitgegeven.

1. Maak een HTML-bestand met de naam {domeinverificatie token}.html. De inhoud van dit bestand moet de waarde van het domeinverificatie-token zijn.
1. Upload dit bestand in de hoofdmap van de webserver die als host voor uw domein wordt gebruikt.
1. Selecteer **Vernieuwen om** de certificaatstatus te controleren. Het kan enkele minuten duren voordat de verificatie is uitgevoerd.

Als u bijvoorbeeld een standaardcertificaat koopt voor azure.com met het domeinverificatie-token 1234abcd, moet een webaanvraag naar https://azure.com/1234abcd.html 1234abcd retourneren. 

> [!IMPORTANT]
> Een certificaatorder heeft slechts 15 dagen om de domeinverificatiebewerking te voltooien. Na 15 dagen wordt het certificaat door de certificeringsinstantie niet meer in rekening gebracht en worden er geen kosten in rekening gebracht voor het certificaat. In dit geval verwijdert u dit certificaat en probeert u het opnieuw.
>
> 

### <a name="you-cant-purchase-a-domain"></a>U kunt geen domein aanschaffen

#### <a name="symptom"></a>Symptoom
U kunt geen App Service in de Azure Portal.

#### <a name="cause-and-solution"></a>Oorzaak en oplossing

Dit probleem doet zich voor om een van de volgende redenen:

- Er is geen creditcard in het Azure-abonnement, of de creditcard is ongeldig.

    **Oplossing:** voeg een geldige creditcard toe aan uw abonnement.

- U bent niet de eigenaar van het abonnement, dus u bent niet gemachtigd om een domein aan te schaffen.

    **Oplossing:** [wijs de rol Eigenaar toe](../role-based-access-control/role-assignments-portal.md) aan uw account. Of neem contact op met de abonnementsbeheerder om toestemming te krijgen om een domein aan te schaffen.
- U hebt de limiet voor het aanschaffen van domeinen in uw abonnement bereikt. De huidige limiet is 20.

    **Oplossing:** als u een verhoging van de limiet wilt aanvragen, neem dan contact [op ondersteuning voor Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Uw type Azure-abonnement biedt geen ondersteuning voor de aanschaf van een App Service-domein.

    **Oplossing:** upgrade uw Azure-abonnement naar een ander abonnementstype, zoals een abonnement met betalen per gebruik.

### <a name="you-cant-add-a-host-name-to-an-app"></a>U kunt geen hostnaam toevoegen aan een app 

#### <a name="symptom"></a>Symptoom

Wanneer u een hostnaam toevoegt, kan het domein niet worden gevalideerd en geverifieerd.

#### <a name="cause"></a>Oorzaak 

Dit probleem doet zich voor om een van de volgende redenen:

- U bent niet machtigingen om een hostnaam toe te voegen.

    **Oplossing:** vraag de abonnementsbeheerder u toestemming te geven om een hostnaam toe te voegen.
- Uw domeineigendom kan niet worden geverifieerd.

    **Oplossing:** controleer of uw CNAME- of A-record correct is geconfigureerd. Als u een aangepast domein wilt toevoegen aan een app, maakt u een CNAME-record of een A-record. Als u een hoofddomein wilt gebruiken, moet u A- en TXT-records gebruiken:

    |Recordtype|Host|Naar wijzen|
    |------|------|-----|
    |A|@|IP-adres voor een app|
    |TXT|@|`<app-name>.azurewebsites.net`|
    |CNAME|www|`<app-name>.azurewebsites.net`|

## <a name="faq"></a>Veelgestelde vragen

**Moet ik mijn aangepaste domein voor mijn website configureren zodra ik het heb gekocht?**

Wanneer u een domein aanschaft bij de Azure Portal, wordt App Service toepassing automatisch geconfigureerd voor het gebruik van dat aangepaste domein. U hoeft geen extra stappen uit te voeren. Bekijk voor meer informatie [Azure App Service Self Help: Add a Custom Domain Name](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name) on Channel9 (Zelfhulp: een naam Custom Domain toevoegen op Channel 9).

**Kan ik een domein dat is gekocht in de Azure Portal in plaats daarvan naar een Azure-VM laten wijzen?**

Ja, u kunt het domein naar een VM laten wijzen. Zie [Use Azure DNS to provide custom domain settings for an Azure service](../dns/dns-custom-domain.md) (Azure DNS gebruiken om aangepaste domeininstellingen te verstrekken voor een Azure-service) voor meer informatie.

**Wordt mijn domein gehost door GoDaddy of Azure DNS?**

App Service domeinen gebruiken GoDaddy voor domeinregistratie en Azure DNS om de domeinen te hosten. 

**Ik heb automatisch vernieuwen ingeschakeld, maar ik heb nog steeds een verlengingsbericht ontvangen voor mijn domein via e-mail. Wat moet ik doen?**

Als u automatisch vernieuwen hebt ingeschakeld, hoeft u geen actie te ondernemen. De kennisgevingsmail wordt verzonden om u te informeren dat het domein bijna is verlopen en handmatig te vernieuwen als automatisch verlengen niet is ingeschakeld.

**Worden er kosten in rekening gebracht voor Azure DNS mijn domein hosten?**

De initiële kosten van domeinaankoop zijn alleen van toepassing op domeinregistratie. Naast de registratiekosten worden er kosten in rekening gebracht voor Azure DNS op basis van uw gebruik. Zie prijzen Azure DNS [meer](https://azure.microsoft.com/pricing/details/dns/) informatie voor meer informatie.

**Ik heb mijn domein eerder gekocht bij de Azure Portal en wil van GoDaddy-hosting naar Azure DNS hosting. Hoe kan ik dit doen?**

Het is niet verplicht om te migreren naar Azure DNS hosting. Als u wilt migreren naar Azure DNS, biedt de domeinbeheerervaring in de Azure Portal over informatie over de stappen die nodig zijn om naar de Azure DNS. Als het domein is gekocht via App Service, is de migratie van GoDaddy-hosting naar Azure DNS relatief naadloze procedure.

**Ik wil mijn domein aanschaffen bij App Service Domain, maar kan ik mijn domein hosten op GoDaddy in plaats van Azure DNS?**

Vanaf 24 juli 2017 worden App Service in de portal aangeschafte domeinen gehost op Azure DNS. Als u liever een andere hostingprovider gebruikt, moet u naar hun website gaan om een domeinhostingoplossing te verkrijgen.

**Moet ik betalen voor privacybescherming voor mijn domein?**

Wanneer u een domein aanschaft via Azure Portal, kunt u ervoor kiezen om privacy toe te voegen zonder extra kosten. Dit is een van de voordelen van het kopen van uw domein via Azure App Service.

**Als ik besluit dat ik mijn domein niet meer wil, kan ik dan mijn geld terug krijgen?**

Wanneer u een domein aanschaft, worden er geen kosten in rekening gebracht voor een periode van vijf dagen, waarin u kunt besluiten dat u het domein niet wilt. Als u besluit dat u het domein niet binnen die periode van vijf dagen wilt hebben, worden er geen kosten in rekening gebracht. (UK-domeinen vormen een uitzondering hierop. Als u een .uk-domein aanschaft, worden er onmiddellijk kosten in rekening gebracht en kunt u geen restitutie krijgen.)

**Kan ik het domein gebruiken in een andere Azure App Service-app in mijn abonnement?**

Ja. Wanneer u de blade Aangepaste domeinen en TLS in de Azure Portal, ziet u de domeinen die u hebt aangeschaft. U kunt uw app configureren voor het gebruik van een van deze domeinen.

**Kan ik een domein overdragen van het ene naar het andere abonnement?**

U kunt een domein verplaatsen naar een ander abonnement of een andere resourcegroep met behulp van de [PowerShell-cmdlet Move-AzResource.](/powershell/module/az.Resources/Move-azResource)

**Hoe kan ik mijn aangepaste domein beheren als ik momenteel geen Azure App Service app?**

U kunt uw domein beheren, zelfs als u geen web-app App Service web-app. Domein kan worden gebruikt voor Azure-services zoals virtuele machine, opslag, enzovoort. Als u van plan bent om het domein voor App Service Web Apps te gebruiken, moet u een web-app opnemen die zich niet in het gratis App Service-abonnement voor staat om het domein te binden aan uw web-app.

**Kan ik een web-app met een aangepast domein verplaatsen naar een ander abonnement of van App Service Environment v1 naar V2?**

Ja, u kunt uw web-app verplaatsen tussen abonnementen. Volg de richtlijnen in [Resources verplaatsen in Azure](../azure-resource-manager/management/move-resource-group-and-subscription.md). Er gelden enkele beperkingen bij het verplaatsen van de web-app. Zie Beperkingen voor het verplaatsen [van App Service resources voor meer informatie.](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)

Na het verplaatsen van de web-app moeten de hostnaambindingen van de domeinen binnen de aangepaste domeininstelling hetzelfde blijven. Er zijn geen extra stappen vereist om de hostnaambindingen te configureren.
