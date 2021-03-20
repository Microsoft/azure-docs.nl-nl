---
title: Problemen met Azure CDN eind punten oplossen-404-status code
description: Meer informatie over het oplossen van problemen met Azure Content Delivery Network-eind punten die 404 HTTP-antwoord status codes retour neren.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: b588a1eb-ab69-4fc7-ae4d-157c3e46f4a8
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: d6ad0b8b37bd4f04c22ed52d4ac6717202f22889
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88192491"
---
# <a name="troubleshooting-azure-cdn-endpoints-that-return-a-404-status-code"></a>Troubleshooting Azure CDN-eind punten die een 404-status code retour neren
Dit artikel biedt u de mogelijkheid om problemen met Azure Content Delivery Network (CDN)-eind punten op te lossen die de status codes van 404 HTTP-antwoorden retour neren.

Als u op elk moment in dit artikel meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op [MSDN Azure en de stack overflow-forums](https://azure.microsoft.com/support/forums/). U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer **ondersteuning verkrijgen**.

## <a name="symptom"></a>Symptoom
U hebt een CDN-profiel en een eind punt gemaakt, maar uw inhoud lijkt niet beschikbaar te zijn op het CDN. Gebruikers die toegang proberen te krijgen tot uw inhoud via de CDN-URL, ontvangen een HTTP 404-status code. 

## <a name="cause"></a>Oorzaak
Er zijn verschillende mogelijke oorzaken, zoals:

* De oorsprong van het bestand is niet zichtbaar voor het CDN.
* Het eind punt is onjuist geconfigureerd, waardoor het CDN op de verkeerde plaats zoekt.
* De host-header van het CDN wordt door de host afgewezen.
* Het eind punt heeft geen tijd door gegeven in het CDN.

## <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing
> [!IMPORTANT]
> Nadat u een CDN-eind punt hebt gemaakt, is het niet direct beschikbaar voor gebruik, omdat het tijd kost om de registratie door te geven via het CDN:
> - Voor profielen van **Azure CDN Standard van Microsoft** is het doorgeven gewoonlijk binnen tien minuten voltooid. 
> - Profielen van **Azure CDN Standard van Akamai** worden doorgaans binnen één minuut doorgegeven. 
> - Profielen van **Azure CDN Standard van Verizon** en **Azure CDN Premium van Verizon** worden doorgaans binnen 90 minuten doorgegeven, maar in sommige gevallen kan dit langer duren. 
> 
> Als u de stappen in dit document hebt voltooid en er nog 404 reacties worden ontvangen, moet u een paar uur wachten om opnieuw te controleren voordat u een ondersteunings ticket opent.
> 
> 

### <a name="check-the-origin-file"></a>Het bron bestand controleren
Controleer eerst of het bestand dat in de cache is opgeslagen, beschikbaar is op de oorspronkelijke server en toegankelijk is via internet. De snelste manier om dat te doen is door een browser in een persoonlijke of incognito-sessie te openen en rechtstreeks naar het bestand te bladeren. Typ of plak de URL in het vak Adres en controleer of het bestand de verwachte resultaten heeft. Stel bijvoorbeeld dat u een bestand hebt in een Azure Storage-account dat toegankelijk is op https: \/ /cdndocdemo.blob.core.windows.net/publicblob/lorem.txt. Als u de inhoud van dit bestand kunt laden, wordt de test door gegeven.

![Gelukt!](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [!WARNING]
> Dit is de snelste en eenvoudigste manier om te controleren of uw bestand openbaar beschikbaar is, maar sommige netwerk configuraties in uw organisatie kunnen erop wijzen dat een bestand openbaar beschikbaar is wanneer dit in feite alleen zichtbaar is voor gebruikers van uw netwerk (zelfs als het wordt gehost in Azure). Om ervoor te zorgen dat dit niet het geval is, test u het bestand met een externe browser, zoals een mobiel apparaat dat niet is verbonden met het netwerk van uw organisatie of een virtuele machine in Azure.
> 
> 

### <a name="check-the-origin-settings"></a>Controleer de instellingen voor de oorsprong
Nadat u hebt gecontroleerd of het bestand openbaar beschikbaar is op internet, controleert u de oorspronkelijke instellingen. Blader in [Azure Portal](https://portal.azure.com)naar uw CDN-profiel en selecteer het eind punt dat u wilt oplossen. Selecteer op de pagina resulterende **eind punt** de oorsprong.  

![Pagina eind punt met gemarkeerde oorsprong](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

De pagina **oorsprong** wordt weer gegeven. 

![Oorspronkelijke pagina](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>Type oorsprong en hostnaam
Controleer of de waarden van het **type oorsprong** en de hostnaam van de **oorsprong** juist zijn. In dit voor beeld is https: \/ /cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, het hostname-gedeelte van de URL is *cdndocdemo.blob.core.Windows.net*, dat juist is. Omdat Azure Storage, Web-apps en Cloud service-oorsprong een vervolg keuzelijst waarde gebruiken voor het veld **hostnaam hostnamen** , zijn onjuiste spellen geen problemen. Als u echter een aangepaste oorsprong gebruikt, controleert u of de hostnaam juist is gespeld.

#### <a name="http-and-https-ports"></a>HTTP-en HTTPS-poorten
Controleer uw **http-** en **HTTPS-poorten**. In de meeste gevallen zijn 80 en 443 juist, en u hoeft geen wijzigingen aan te brengen.  Als de oorspronkelijke server op een andere poort luistert, moet deze hier worden weer gegeven. Als u het niet zeker weet, bekijkt u de URL van het bron bestand. De specificatie van HTTP-en HTTPS-poorten 80 en 443 worden als standaard waarden gebruikt. In het voor beeld-URL, https: \/ /cdndocdemo.blob.core.windows.net/publicblob/lorem.txt is een poort niet opgegeven, dus wordt de standaard waarde van 443 gebruikt en zijn de instellingen juist.  

Stel echter dat de URL voor het oorspronkelijke bestand dat u eerder hebt getest http: \/ /www.contoso.com:8080/file.txt. Noteer het gedeelte *: 8080* aan het einde van het hostname-segment. Met dit nummer geeft u aan dat de browser poort 8080 moet gebruiken om verbinding te maken met de webserver op www \. -contoso.com. Daarom moet u *8080* invoeren in het veld **http-poort** . Het is belang rijk te weten dat deze poort instellingen alleen van invloed zijn op de poort die door het eind punt wordt gebruikt om informatie op te halen van de oorsprong.

> [!NOTE]
> **Azure CDN standaard van Akamai** -eind punten staan niet het volledige TCP-poort bereik voor de oorsprong toe.  Zie [Azure CDN from Akamai Allowed Origin Ports](/previous-versions/azure/mt757337(v=azure.100)) (Door Azure CDN van Akamai toegestane poorten van oorsprong) voor een lijst met poorten van oorsprong die niet zijn toegestaan.  
> 
> 

### <a name="check-the-endpoint-settings"></a>Controleer de instellingen van het eind punt
Selecteer op de pagina **eind punt** de knop **configureren** .

![De pagina eind punt met de knop configureren gemarkeerd](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

De pagina CDN-eind punt **configureren** wordt weer gegeven.

![Pagina configureren](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>Protocollen
Voor **protocollen**, controleert u of het protocol dat door de clients wordt gebruikt, is geselecteerd. Omdat hetzelfde protocol dat wordt gebruikt door de client wordt gebruikt voor toegang tot de bron, is het belang rijk dat de oorspronkelijke poorten correct zijn geconfigureerd in de vorige sectie. Het CDN-eind punt luistert alleen op de standaard-HTTP-en HTTPS-poorten (80 en 443), ongeacht de poort van oorsprong.

We gaan terug naar ons hypothetische voor beeld met http: \/ /www.contoso.com:8080/file.txt.  Zoals u herinnert, heeft Contoso opgegeven *8080* als http-poort, maar laten we ook aannemen dat *44300* als HTTPS-poort is opgegeven.  Als ze een eind punt hebben gemaakt met de naam *Contoso*, wordt de hostnaam van het CDN-eind punt *contoso.azureedge.net*.  Een aanvraag voor http: \/ /contoso.azureedge.net/file.txt is een HTTP-aanvraag. Daarom gebruikt het eind punt http op poort 8080 om het op te halen van de oorsprong.  Een beveiligde aanvraag via HTTPS, https: \/ /contoso.azureedge.net/file.txt, zorgt ervoor dat het eind punt https gebruikt op poort 44300 bij het ophalen van het bestand van de oorsprong.

#### <a name="origin-host-header"></a>Host-header van oorsprong
De **oorsprong** van de host-header is de waarde van de host-header die met elke aanvraag wordt verzonden naar de oorsprong.  In de meeste gevallen moet dit hetzelfde zijn als de hostnaam van de **oorsprong** die we eerder hebben geverifieerd.  Een onjuiste waarde in dit veld veroorzaakt doorgaans geen 404-statussen, maar veroorzaakt waarschijnlijk andere 4xx-statussen, afhankelijk van wat de oorsprong verwacht.

#### <a name="origin-path"></a>Pad voor de oorsprong
Ten slotte moeten we het **oorspronkelijke pad** verifiëren.  Dit is standaard leeg.  U moet dit veld alleen gebruiken als u het bereik wilt beperken van de bron-hostende resources die u beschikbaar wilt maken op het CDN.  

In het voor beeld-eind punt wilden alle resources op het opslag account beschikbaar zijn, dus het **bronpad** is leeg gelaten.  Dit betekent dat een aanvraag voor https: \/ /cdndocdemo.azureedge.net/publicblob/lorem.txt resulteert in een verbinding van het eind punt naar cdndocdemo.core.Windows.net die */publicblob/lorem.txt* aanvraagt.  Op dezelfde manier wordt een aanvraag voor https: \/ /cdndocdemo.azureedge.net/donotcache/status.png resultaten opgeleverd in het eind punt dat */donotcache/status.png* aanvraagt van de oorsprong.

Maar wat als u het CDN niet wilt gebruiken voor elk pad op uw oorsprong?  Stel dat u het pad naar de *publicblob* wilt weer geven.  Als we */publicblob* in het veld **bronpad** invoeren, zorgt dat ervoor dat het eind punt */publicblob* invoegt voordat elke aanvraag wordt ingediend bij de oorsprong.  Dit betekent dat de aanvraag voor https: \/ /cdndocdemo.azureedge.net/publicblob/lorem.txt nu daad werkelijk het aanvraag gedeelte van de URL, */publicblob/lorem.txt* en de */publicblob* toevoegen aan het begin. Dit resulteert in een aanvraag voor */publicblob/publicblob/lorem.txt* van de oorsprong.  Als dat pad niet kan worden omgezet naar een echt bestand, wordt de status van de 404 geretourneerd.  De juiste URL voor het ophalen van lorem.txt in dit voor beeld is in werkelijkheid https: \/ /cdndocdemo.azureedge.net/lorem.txt.  Houd er rekening mee dat het pad van de */publicblob* helemaal niet is opgenomen, omdat het aanvraag gedeelte van de URL */lorem.txt* is en het eind punt */publicblob* toevoegt, wat resulteert in */publicblob/lorem.txt* de aanvraag wordt door gegeven aan de oorsprong.

