---
title: Problemen met domein-en TLS/SSL-certificaten oplossen
description: Vind oplossingen voor de veelvoorkomende problemen die u kunt tegen komen wanneer u een domein-of TLS/SSL-certificaat in Azure App Service configureert.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.topic: article
ms.date: 03/01/2019
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 691cbd79e82432c8e919dcbb51642a76000296dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97607606"
---
# <a name="troubleshoot-domain-and-tlsssl-certificate-problems-in-azure-app-service"></a>Problemen met domein-en TLS/SSL-certificaten in Azure App Service oplossen

In dit artikel vindt u veelvoorkomende problemen die kunnen optreden wanneer u een domein-of TLS/SSL-certificaat voor uw web-apps configureert in Azure App Service. Hierin worden ook mogelijke oorzaken en oplossingen voor deze problemen beschreven.

Als u op elk moment in dit artikel meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op [MSDN en stack overflow forums](https://azure.microsoft.com/support/forums/). U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer **ondersteuning verkrijgen**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="certificate-problems"></a>Certificaat problemen

### <a name="you-cant-add-a-tlsssl-certificate-binding-to-an-app"></a>U kunt geen TLS/SSL-certificaat binding toevoegen aan een app 

#### <a name="symptom"></a>Symptoom

Wanneer u een TLS-binding toevoegt, wordt het volgende fout bericht weer gegeven:

Kan de SSL-binding niet toevoegen. Het certificaat kan niet worden ingesteld voor de bestaande VIP omdat een ander VIP al gebruikmaakt van dat certificaat.

#### <a name="cause"></a>Oorzaak

Dit probleem kan optreden als u meerdere op IP gebaseerde SSL-bindingen hebt voor hetzelfde IP-adres voor meerdere apps. App A heeft bijvoorbeeld een SSL op basis van IP met een oud certificaat. App B heeft een op IP gebaseerde SSL met een nieuw certificaat voor hetzelfde IP-adres. Wanneer u de TLS-binding van de app bijwerkt met het nieuwe certificaat, mislukt dit met deze fout omdat hetzelfde IP-adres wordt gebruikt voor een andere app. 

#### <a name="solution"></a>Oplossing 

Gebruik een van de volgende methoden om dit probleem op te lossen:

- Verwijder de op IP gebaseerde SSL-binding in de app die gebruikmaakt van het oude certificaat. 
- Maak een nieuwe SSL-binding op basis van IP die gebruikmaakt van het nieuwe certificaat.

### <a name="you-cant-delete-a-certificate"></a>U kunt een certificaat niet verwijderen 

#### <a name="symptom"></a>Symptoom

Wanneer u een certificaat probeert te verwijderen, wordt het volgende fout bericht weer gegeven:

Het certificaat kan niet worden verwijderd omdat het momenteel wordt gebruikt in een TLS/SSL-binding. De TLS-binding moet worden verwijderd voordat u het certificaat kunt verwijderen. "

#### <a name="cause"></a>Oorzaak

Dit probleem kan zich voordoen als een andere app het certificaat gebruikt.

#### <a name="solution"></a>Oplossing

Verwijder de TLS-binding voor dat certificaat uit de apps. Verwijder vervolgens het certificaat. Als u het certificaat nog steeds niet kunt verwijderen, wist u de cache van de Internet browser en opent u de Azure Portal opnieuw in een nieuw browser venster. Verwijder vervolgens het certificaat.

### <a name="you-cant-purchase-an-app-service-certificate"></a>U kunt geen App Service certificaat kopen 

#### <a name="symptom"></a>Symptoom
U kunt geen [Azure app service certificaat](./configure-ssl-certificate.md#import-an-app-service-certificate) kopen via de Azure Portal.

#### <a name="cause-and-solution"></a>Oorzaak en oplossing
Dit probleem kan om de volgende redenen optreden:

- Het App Service plan is gratis of gedeeld. Deze prijs categorieën bieden geen ondersteuning voor TLS. 

    **Oplossing**: Voer een upgrade uit voor het app service plan voor de app naar Standard.

- Het abonnement heeft geen geldige credit card.

    **Oplossing**: Voeg een geldige credit card toe aan uw abonnement. 

- Het abonnements aanbod biedt geen ondersteuning voor het aanschaffen van een App Service certificaat zoals micro soft student.  

    **Oplossing**: Voer een upgrade uit voor uw abonnement. 

- Het abonnement heeft de limiet voor het aantal aankopen dat is toegestaan voor een abonnement bereikt.

    **Oplossing**: voor app service certificaten geldt een limiet van 10 certificaat aankopen voor de typen betalen per gebruik en EA-abonnement. Voor andere typen abonnementen is de limiet 3. Neem contact op met de [ondersteuning van Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)om de limiet te verhogen.
- Het App Service certificaat is gemarkeerd als fraude. U hebt het volgende fout bericht ontvangen: ' uw certificaat is gemarkeerd voor mogelijke fraude. De aanvraag wordt momenteel gecontroleerd. Als het certificaat binnen 24 uur niet kan worden gebruikt, neemt u contact op met de ondersteuning van Azure. "

    **Oplossing**: als het certificaat is gemarkeerd als fraude en na 24 uur niet is opgelost, voert u de volgende stappen uit:

    1. Meld u aan bij [Azure Portal](https://portal.azure.com).
    2. Ga naar **app service certificaten** en selecteer het certificaat.
    3. Selecteer **certificaat configuratie**  >  **stap 2: Controleer** de verificatie van het  >  **domein**. Met deze stap verzendt u een e-mail bericht naar de Azure-certificaat provider om het probleem op te lossen.

## <a name="custom-domain-problems"></a>Problemen met aangepaste domeinen

### <a name="a-custom-domain-returns-a-404-error"></a>Een aangepast domein retourneert een 404-fout 

#### <a name="symptom"></a>Symptoom

Wanneer u naar de site bladert met behulp van de aangepaste domein naam, wordt het volgende fout bericht weer gegeven:

Fout 404-kan Web-app niet vinden.

#### <a name="cause-and-solution"></a>Oorzaak en oplossing

**Oorzaak 1** 

Voor het aangepaste domein dat u hebt geconfigureerd, ontbreekt een CNAME of een record. 

**Oplossing voor oorzaak 1**

- Als u een record hebt toegevoegd, moet u ervoor zorgen dat er ook een TXT-record is toegevoegd. Zie [de A-record maken](./app-service-web-tutorial-custom-domain.md#create-the-a-record)voor meer informatie.
- Als u het hoofd domein voor uw app niet hoeft te gebruiken, raden we u aan om een CNAME-record te gebruiken in plaats van een record.
- Gebruik niet zowel een CNAME-record als een record voor hetzelfde domein. Dit probleem kan een conflict veroorzaken en voor komen dat het domein wordt opgelost. 

**Oorzaak 2** 

De Internet browser kan nog steeds het oude IP-adres in de cache opslaan voor uw domein. 

**Oplossing voor oorzaak 2**

Wis de browser. Voor Windows-apparaten kunt u de opdracht uitvoeren `ipconfig /flushdns` . Gebruik [WhatsmyDNS.net](https://www.whatsmydns.net/) om te controleren of uw domein verwijst naar het IP-adres van de app.

### <a name="you-cant-add-a-subdomain"></a>U kunt geen subdomein toevoegen 

#### <a name="symptom"></a>Symptoom

U kunt geen nieuwe hostnaam toevoegen aan een app om een subdomein toe te wijzen.

#### <a name="solution"></a>Oplossing

- Neem contact op met de abonnements beheerder om ervoor te zorgen dat u gemachtigd bent om een hostnaam toe te voegen aan de app.
- Als u meer subdomeinen nodig hebt, raden we u aan om het domein hosting te wijzigen in azure Domain Name Service (DNS). U kunt met behulp van Azure DNS 500-hostnamen toevoegen aan uw app. Zie [een subdomein toevoegen](/archive/blogs/waws/mapping-a-custom-subdomain-to-an-azure-website)voor meer informatie.

### <a name="dns-cant-be-resolved"></a>DNS kan niet worden omgezet

#### <a name="symptom"></a>Symptoom

U hebt het volgende fout bericht ontvangen:

De DNS-record kan niet worden gevonden.

#### <a name="cause"></a>Oorzaak
Dit probleem treedt op om een van de volgende redenen:

- De TTL-periode (time to Live) is niet verlopen. Controleer de DNS-configuratie voor uw domein om de TTL-waarde te bepalen en wacht totdat de periode verloopt.
- De DNS-configuratie is onjuist.

#### <a name="solution"></a>Oplossing
- Wacht tot 48 uur voordat dit probleem is opgelost.
- Als u de TTL-instelling in uw DNS-configuratie kunt wijzigen, wijzigt u de waarde in 5 minuten om te zien of het probleem hiermee is opgelost.
- Gebruik [WhatsmyDNS.net](https://www.whatsmydns.net/) om te controleren of uw domein verwijst naar het IP-adres van de app. Als dat niet het geval is, configureert u de A-record in het juiste IP-adres van de app.

### <a name="you-need-to-restore-a-deleted-domain"></a>U moet een verwijderd domein herstellen 

#### <a name="symptom"></a>Symptoom
Uw domein is niet meer zichtbaar in de Azure Portal.

#### <a name="cause"></a>Oorzaak 
De eigenaar van het abonnement heeft het domein mogelijk per ongeluk verwijderd.

#### <a name="solution"></a>Oplossing
Als uw domein minder dan zeven dagen geleden is verwijderd, is de verwijdering van het domein nog niet gestart. In dit geval kunt u hetzelfde domein opnieuw kopen op het Azure Portal onder hetzelfde abonnement. (Zorg ervoor dat u de exacte domein naam in het zoekvak typt.) Voor dit domein worden er geen kosten meer in rekening gebracht. Als het domein meer dan zeven dagen geleden is verwijderd, neemt u contact op met [Azure-ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) voor hulp bij het herstellen van het domein.

## <a name="domain-problems"></a>Domein problemen

### <a name="you-purchased-a-tlsssl-certificate-for-the-wrong-domain"></a>U hebt een TLS/SSL-certificaat voor het verkeerde domein aangeschaft

#### <a name="symptom"></a>Symptoom

U hebt een App Service certificaat voor het verkeerde domein aangeschaft. U kunt het certificaat niet bijwerken om het juiste domein te gebruiken.

#### <a name="solution"></a>Oplossing

Verwijder het certificaat en koop vervolgens een nieuw certificaat.

Als het huidige certificaat dat gebruikmaakt van het onjuiste domein de status ' uitgereikt ' heeft, wordt u ook gefactureerd voor dat certificaat. App Service certificaten zijn niet teruggestort, maar u kunt contact opnemen met de [ondersteuning van Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om te controleren of er andere opties zijn. 

### <a name="an-app-service-certificate-was-renewed-but-the-app-shows-the-old-certificate"></a>Er is een App Service certificaat vernieuwd, maar in de app wordt het oude certificaat weer gegeven 

#### <a name="symptom"></a>Symptoom

Het App Service certificaat is vernieuwd, maar de app die gebruikmaakt van het App Service certificaat, maakt nog gebruik van het oude certificaat. Daarnaast hebt u een waarschuwing ontvangen dat het HTTPS-protocol vereist is.

#### <a name="cause"></a>Oorzaak 
App Service synchroniseert uw certificaat automatisch binnen 48 uur. Wanneer u een certificaat roteert of bijwerkt, wordt de toepassing toch nog steeds het oude certificaat opgehaald en niet het zojuist bijgewerkte certificaat. De reden hiervoor is dat de taak voor het synchroniseren van de certificaat bron nog niet is uitgevoerd. Klik op synchroniseren. Met de synchronisatie bewerking worden de hostname-bindingen voor het certificaat in App Service automatisch bijgewerkt zonder dat er uitval tijd voor uw apps wordt veroorzaakt.

#### <a name="solution"></a>Oplossing

U kunt de synchronisatie van het certificaat forceren:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **app service certificaten** en selecteer vervolgens het certificaat.
2. Selecteer opnieuw **genereren en synchroniseren** en selecteer vervolgens **synchroniseren**. Het kan enige tijd duren voordat de synchronisatie is voltooid. 
3. Wanneer de synchronisatie is voltooid, ziet u de volgende melding: alle resources zijn bijgewerkt met het nieuwste certificaat.

### <a name="domain-verification-is-not-working"></a>Domein verificatie werkt niet 

#### <a name="symptom"></a>Symptoom 
Het App Service certificaat vereist domein verificatie voordat het certificaat gereed is voor gebruik. Wanneer u **controleren** selecteert, mislukt het proces.

#### <a name="solution"></a>Oplossing
Bevestig uw domein hand matig door een TXT-record toe te voegen:

1. Ga naar de Domain Name Service (DNS)-provider die als host fungeert voor uw domeinnaam.
1. Voeg een TXT-record voor uw domein toe die gebruikmaakt van de waarde van het domeintoken dat wordt weergegeven in de Azure Portal. 

Wacht enkele minuten totdat de DNS-doorgifte is uitgevoerd en selecteer vervolgens de knop **vernieuwen** om de verificatie te activeren. 

Als alternatief kunt u de methode HTML-webpagina gebruiken om uw domein hand matig te verifiëren. Met deze methode kan de certificerings instantie het domein eigendom bevestigen van het domein waarvoor het certificaat is uitgegeven.

1. Maak een HTML-bestand met de naam {Domain verificatie token}. html. De inhoud van dit bestand moet de waarde zijn van het domein verificatie token.
1. Upload dit bestand in de hoofdmap van de webserver die als host fungeert voor uw domein.
1. Selecteer **vernieuwen** om de certificaat status te controleren. Het kan enkele minuten duren voordat de verificatie is voltooid.

Als u bijvoorbeeld een standaard certificaat voor azure.com koopt met het domein verificatie token 1234abcd, moet er een webaanvraag worden ingediend om https://azure.com/1234abcd.html 1234abcd te retour neren. 

> [!IMPORTANT]
> Een certificaat order heeft slechts 15 dagen voor het volt ooien van de domein verificatie bewerking. Na 15 dagen weigert de certificerings instantie het certificaat en worden er geen kosten in rekening gebracht voor het certificaat. In deze situatie verwijdert u dit certificaat en probeert u het opnieuw.
>
> 

### <a name="you-cant-purchase-a-domain"></a>U kunt geen domein kopen

#### <a name="symptom"></a>Symptoom
U kunt geen App Service domein kopen in de Azure Portal.

#### <a name="cause-and-solution"></a>Oorzaak en oplossing

Dit probleem treedt op om een van de volgende redenen:

- Er is geen creditcard in het Azure-abonnement, of de creditcard is ongeldig.

    **Oplossing**: Voeg een geldige credit card toe aan uw abonnement.

- U bent niet de eigenaar van het abonnement, dus u bent niet gemachtigd om een domein aan te schaffen.

    **Oplossing**: [Wijs de rol eigenaar](../role-based-access-control/role-assignments-portal.md) toe aan uw account. Of neem contact op met de abonnements beheerder om toestemming te krijgen om een domein aan te schaffen.
- U hebt de limiet voor het aanschaffen van domeinen in uw abonnement bereikt. De huidige limiet is 20.

    **Oplossing**: Neem contact op met de [ondersteuning van Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)om een verhoging van de limiet aan te vragen.
- Uw type Azure-abonnement biedt geen ondersteuning voor de aanschaf van een App Service-domein.

    **Oplossing**: Voer een upgrade uit van uw Azure-abonnement naar een ander abonnements type, zoals een abonnement op basis van betalen naar gebruik.

### <a name="you-cant-add-a-host-name-to-an-app"></a>U kunt geen hostnaam toevoegen aan een app 

#### <a name="symptom"></a>Symptoom

Wanneer u een hostnaam toevoegt, kan het proces het domein niet valideren en verifiëren.

#### <a name="cause"></a>Oorzaak 

Dit probleem treedt op om een van de volgende redenen:

- U bent niet gemachtigd om een hostnaam toe te voegen.

    **Oplossing**: vraag de abonnements beheerder u toestemming te geven om een hostnaam toe te voegen.
- Het eigendom van het domein kan niet worden geverifieerd.

    **Oplossing**: Controleer of de CNAME of een record juist is geconfigureerd. Als u een aangepast domein wilt toewijzen aan een app, moet u een CNAME-record of een record maken. Als u een hoofd domein wilt gebruiken, moet u een-en TXT-record gebruiken:

    |Recordtype|Host|Wijs naar|
    |------|------|-----|
    |A|@|IP-adres voor een app|
    |TXT|@|`<app-name>.azurewebsites.net`|
    |CNAME|www|`<app-name>.azurewebsites.net`|

## <a name="faq"></a>Veelgestelde vragen

**Moet ik mijn aangepaste domein voor mijn website configureren nadat ik het heb gekocht?**

Wanneer u een domein aanschaft vanuit de Azure Portal, wordt de App Service-toepassing automatisch geconfigureerd voor gebruik van dat aangepaste domein. U hoeft geen extra stappen uit te voeren. Bekijk [Azure app service Self Help: een aangepaste domein naam toevoegen](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name) aan Channel 9 voor meer informatie.

**Kan ik een in de Azure Portal gekocht domein gebruiken om in plaats daarvan naar een virtuele Azure-machine te verwijzen?**

Ja, u kunt het domein naar een virtuele machine laten wijzen. Zie [Use Azure DNS to provide custom domain settings for an Azure service](../dns/dns-custom-domain.md) (Azure DNS gebruiken om aangepaste domeininstellingen te verstrekken voor een Azure-service) voor meer informatie.

**Wordt mijn domein gehost door GoDaddy of Azure DNS?**

App Service domeinen gebruiken GoDaddy voor domein registratie en Azure DNS om de domeinen te hosten. 

**Ik heb automatische verlenging ingeschakeld, maar ik heb nog steeds een verlengings kennisgeving ontvangen voor mijn domein via e-mail. Wat moet ik doen?**

Als u automatisch verlengen hebt ingeschakeld, hoeft u geen actie te ondernemen. Het e-mail bericht wordt verstrekt om u te informeren dat het domein bijna verloopt en hand matig kan worden vernieuwd als automatisch verlengen niet is ingeschakeld.

**Worden er kosten in rekening gebracht voor Azure DNS mijn domein hosten?**

De initiële kosten voor de domein aankoop gelden alleen voor domein registratie. Naast de registratie kosten worden er kosten in rekening gebracht voor Azure DNS op basis van uw gebruik. Zie [Azure DNS prijzen](https://azure.microsoft.com/pricing/details/dns/) voor meer informatie.

**Ik heb mijn domein eerder aangeschaft bij de Azure Portal en wil van GoDaddy-hosting naar Azure DNS-hosting. Hoe kan ik dit doen?**

Het is niet verplicht om te migreren naar Azure DNS-hosting. Als u wilt migreren naar Azure DNS, vindt u in de domein beheer ervaring in het Azure Portal informatie over de stappen die nodig zijn om naar Azure DNS te gaan. Als het domein is aangeschaft via App Service, is de migratie van GoDaddy die op Azure DNS wordt gehost, een betrekkelijk naadloze procedure.

**Ik wil mijn domein kopen vanaf App Service domein, maar kan ik mijn domein hosten op GoDaddy in plaats van Azure DNS?**

Vanaf 24 juli 2017 worden App Service domeinen die in de portal zijn aangeschaft, gehost op Azure DNS. Als u liever een andere hosting provider gebruikt, moet u naar de website gaan om een oplossing voor het hosten van een domein te verkrijgen.

**Moet ik betalen voor privacy-bescherming voor mijn domein?**

Wanneer u een domein aanschaft via de Azure Portal, kunt u ervoor kiezen om privacy gratis toe te voegen. Dit is een van de voor delen van het aanschaffen van uw domein via Azure App Service.

**Als ik besluit dat ik mijn domein niet meer wil, kan ik mijn geld terug krijgen?**

Wanneer u een domein koopt, worden er geen kosten in rekening gebracht voor een periode van vijf dagen, gedurende welke tijd u kunt bepalen of u het domein niet wilt. Als u besluit dat u het domein niet binnen deze periode van vijf dagen wilt, moet u niet in rekening worden gebracht. (in UK-domeinen is dit een uitzonde ring. Als u een. uk-domein aanschaft, worden er direct kosten in rekening gebracht en u kunt geen restitutie maken.)

**Kan ik het domein in een andere Azure App Service-app gebruiken in mijn abonnement?**

Ja. Wanneer u de Blade aangepaste domeinen en TLS in de Azure Portal opent, ziet u de domeinen die u hebt aangeschaft. U kunt uw app configureren voor het gebruik van een van deze domeinen.

**Kan ik een domein van een abonnement overdragen naar een ander abonnement?**

Met de Power shell [-cmdlet Move-AzResource](/powershell/module/az.Resources/Move-azResource) kunt u een domein verplaatsen naar een ander abonnement of een andere resource groep.

**Hoe kan ik mijn aangepaste domein beheren als ik momenteel geen Azure App Service-app heb?**

U kunt uw domein beheren, zelfs als u geen App Service web-app hebt. Het domein kan worden gebruikt voor Azure-Services zoals virtuele machine, opslag, enzovoort. Als u van plan bent om het domein voor App Service Web Apps te gebruiken, moet u een web-app opnemen die zich niet op het Free App Service plan bevindt om het domein aan uw web-app te koppelen.

**Kan ik een web-app met een aangepast domein verplaatsen naar een ander abonnement of van App Service Environment v1 naar v2?**

Ja, u kunt uw web-app verplaatsen naar andere abonnementen. Volg de richt lijnen [voor het verplaatsen van resources in azure](../azure-resource-manager/management/move-resource-group-and-subscription.md). Er zijn enkele beperkingen bij het verplaatsen van de web-app. Zie [beperkingen voor het verplaatsen van app service resources](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)voor meer informatie.

Na het verplaatsen van de web-app, moeten de hostnamen van de domeinen binnen de instellingen van de aangepaste domeinen hetzelfde blijven. Er zijn geen extra stappen vereist om de bindingen met de hostnaam te configureren.