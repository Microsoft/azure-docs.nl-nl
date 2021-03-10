---
title: Problemen met de implementatie van StorSimple 8000 Series oplossen | Microsoft Docs
description: Hierin wordt beschreven hoe u fouten opspoort en corrigeert die optreden wanneer u de eerste keer StorSimple implementeert.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/25/2021
ms.author: alkohli
ms.openlocfilehash: f1bce5c0ecdab4a9c96c3f2cc0e9734247bdfd66
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102566342"
---
# <a name="troubleshoot-storsimple-device-deployment-issues"></a>Problemen met de implementatie van StorSimple-apparaten oplossen
## <a name="overview"></a>Overzicht
In dit artikel vindt u nuttige richt lijnen voor probleem oplossing voor uw Microsoft Azure StorSimple-implementatie. Hierin worden veelvoorkomende problemen, mogelijke oorzaken en aanbevolen stappen beschreven om u te helpen bij het oplossen van problemen die zich kunnen voordoen bij het configureren van StorSimple. 

Deze informatie is van toepassing op het fysieke StorSimple-apparaat van de 8000-serie en de StorSimple Cloud Appliance.

> [!NOTE]
> Problemen met betrekking tot het configureren van apparaten kunnen zich voordoen wanneer u het apparaat voor de eerste keer implementeert, of ze kunnen later worden uitgevoerd wanneer het apparaat operationeel is. Dit artikel richt zich op het oplossen van problemen met de implementatie van voor het eerst. Als u problemen met een operationeel apparaat wilt oplossen, gaat u naar [het hulp programma Diagnostics gebruiken om problemen met een operationeel apparaat op te lossen](storsimple-8000-diagnostics.md).

In dit artikel worden ook de hulpprogram ma's beschreven voor het oplossen van problemen met StorSimple-implementaties en vindt u een voor beeld van stapsgewijze probleem oplossing.

## <a name="first-time-deployment-issues"></a>Implementatie problemen voor de eerste keer
Als u een probleem ondervindt bij het implementeren van uw apparaat voor de eerste keer, moet u rekening houden met de volgende richt lijnen:

* Als u problemen met een fysiek apparaat oplost, moet u ervoor zorgen dat de hardware is geïnstalleerd en geconfigureerd zoals wordt beschreven in [uw StorSimple 8100-apparaat installeren](storsimple-8100-hardware-installation.md) of [uw StorSimple 8600-apparaat installeren](storsimple-8600-hardware-installation.md).
* Controleer de vereisten voor de implementatie. Zorg ervoor dat u beschikt over alle informatie die wordt beschreven in de [controle lijst voor implementatie configuratie](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist).
* Bekijk de opmerkingen bij de release van StorSimple om te controleren of het probleem wordt beschreven. De release opmerkingen bevatten tijdelijke oplossingen voor bekende installatie problemen. 

Tijdens de implementatie van het apparaat worden de meest voorkomende problemen beschreven die gebruikers ondervinden wanneer ze de installatie wizard uitvoeren en wanneer ze het apparaat registreren via Windows PowerShell voor StorSimple. (U gebruikt Windows PowerShell voor StorSimple om uw StorSimple-apparaat te registreren en te configureren. Zie voor meer informatie over apparaatregistratie, [stap 3: uw apparaat configureren en registreren via Windows PowerShell voor StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)).

De volgende secties kunnen u helpen bij het oplossen van problemen die zich voordoen wanneer u het StorSimple-apparaat voor de eerste keer configureert.

## <a name="first-time-setup-wizard-process"></a>Wizard voor de eerste keer dat de installatie wordt uitgevoerd
In de volgende stappen wordt het installatie wizard-proces samenvatten. Zie [uw on-premises StorSimple-apparaat implementeren](storsimple-8000-deployment-walkthrough-u2.md)voor gedetailleerde informatie over de installatie.

1. Voer de cmdlet [invoke-hcssetupwizard uit](/previous-versions/windows/powershell-scripting/dn688135(v=wps.630)) uit om de installatie wizard te starten, waarmee u de resterende stappen doorloopt. 
2. Het netwerk configureren: met de wizard Setup kunt u netwerk instellingen configureren voor de DATA 0-netwerk interface op uw StorSimple-apparaat. De volgende instellingen zijn opgenomen:
   * Virtueel IP-adres (VIP), subnetmasker en gateway: de cmdlet [set-HcsNetInterface](/previous-versions/windows/powershell-scripting/dn688161(v=wps.630)) wordt op de achtergrond uitgevoerd. Hiermee configureert u het IP-adres, subnetmasker en de gateway voor de DATA 0-netwerk interface op uw StorSimple-apparaat.
   * Primaire DNS-server: de cmdlet [set-HcsDnsClientServerAddress](/previous-versions/windows/powershell-scripting/dn688172(v=wps.630)) wordt uitgevoerd op de achtergrond. De DNS-instellingen voor uw StorSimple-oplossing worden geconfigureerd.
   * NTP-server: de cmdlet [set-HcsNtpClientServerAddress](/previous-versions/windows/powershell-scripting/dn688138(v=wps.630)) wordt uitgevoerd op de achtergrond. Hiermee configureert u de instellingen van de NTP-server voor uw StorSimple-oplossing.
   * Optionele webproxy: de cmdlet [set-HcsWebProxy](/previous-versions/windows/powershell-scripting/dn688154(v=wps.630)) wordt uitgevoerd op de achtergrond. Hiermee wordt de configuratie van de webproxy voor uw StorSimple-oplossing ingesteld en ingeschakeld.
3. Stel het wacht woord in: de volgende stap is het instellen van het beheerders wachtwoord voor het apparaat.
   Het beheerders wachtwoord voor het apparaat wordt gebruikt om u aan te melden bij uw apparaat. Het standaardapparaatwachtwoord is **Password1**.
        
     > [!IMPORTANT]
     > Wacht woorden worden vóór de registratie verzameld, maar pas toegepast nadat u het apparaat hebt geregistreerd. Als er een fout is opgetreden bij het Toep assen van een wacht woord, wordt u gevraagd het wacht woord opnieuw op te geven totdat de vereiste wacht woorden (die voldoen aan de complexiteits vereisten) worden verzameld.
     
4. Het apparaat registreren: de laatste stap is het registreren van het apparaat bij de StorSimple Apparaatbeheer-service die wordt uitgevoerd in Microsoft Azure. Voor de registratie moet u [de service registratie sleutel](storsimple-8000-manage-service.md#get-the-service-registration-key) van de Azure Portal ophalen en deze opgeven in de wizard Setup. **Nadat het apparaat is geregistreerd, wordt er een versleutelings sleutel voor service gegevens aan u door gegeven. Zorg ervoor dat u deze versleutelings sleutel op een veilige locatie behoudt, omdat het vereist is om alle toekomstige apparaten bij de service te registreren.**

## <a name="common-errors-during-device-deployment"></a>Veelvoorkomende fouten tijdens de implementatie van het apparaat
In de volgende tabellen worden veelvoorkomende fouten vermeld die u kunt voordoen wanneer u:

* Configureer de vereiste netwerk instellingen.
* Configureer de optionele web proxy-instellingen.
* Stel het beheerders wachtwoord voor het apparaat in.
* Registreer het apparaat.

## <a name="errors-during-the-required-network-settings"></a>Fouten tijdens de vereiste netwerk instellingen
| Nee. | Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
| --- | --- | --- | --- |
| 1 |Invoke-Hcssetupwizard uit: deze opdracht kan alleen worden uitgevoerd op de actieve controller. |De configuratie is uitgevoerd op de passieve controller. |Voer deze opdracht uit vanaf de actieve controller. Zie [een actieve controller identificeren op het apparaat](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device)voor meer informatie. |
| 2 |Invoke-Hcssetupwizard uit: het apparaat is niet gereed. |Er zijn problemen met de netwerk verbinding op DATA 0. |Controleer de fysieke netwerk verbinding op gegevens 0. |
| 3 |Invoke-Hcssetupwizard uit: er is een IP-adres conflict met een ander systeem op het netwerk (uitzonde ring van HRESULT: 0x80070263). |Het IP-adres dat is opgegeven voor de gegevens 0, is al in gebruik door een ander systeem. |Geef een nieuw IP-adres op dat niet in gebruik is. |
| 4 |Invoke-Hcssetupwizard uit: een cluster bron is mislukt. (Uitzonde ring van HRESULT: 0x800713AE). |Duplicaat VIP. Het opgegeven IP-adres wordt al gebruikt. |Geef een nieuw IP-adres op dat niet in gebruik is. |
| 5 |Invoke-Hcssetupwizard uit: ongeldig IPv4-adres. |Het IP-adres is in een onjuiste indeling opgenomen. |Controleer de indeling en geef uw IP-adres opnieuw op. Zie [IPv4-adres Sering][1]voor meer informatie. |
| 6 |Invoke-Hcssetupwizard uit: ongeldig IPv6-adres. |Het IP-adres is in een onjuiste indeling opgenomen. |Controleer de indeling en geef uw IP-adres opnieuw op. Zie [IPv6 Addressing][2](Engelstalig) voor meer informatie. |
| 7 |Invoke-Hcssetupwizard uit: er zijn geen eind punten meer beschikbaar in de eindpunttoewijzer. (Uitzonde ring van HRESULT: 0x800706D9) |De cluster functionaliteit werkt niet. |[Neem contact op met Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md) voor de volgende stappen. |

## <a name="errors-during-the-optional-web-proxy-settings"></a>Fouten tijdens de optionele web proxy-instellingen
| Nee. | Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
| --- | --- | --- | --- |
| 1 |Invoke-Hcssetupwizard uit: ongeldige para meter (uitzonde ring van HRESULT: 0x80070057) |Een van de opgegeven para meters voor de proxy-instellingen is ongeldig. |De URI is niet in de juiste indeling opgenomen. Gebruik de volgende indeling: http:// *\<IP address or FQDN of the web proxy server>* :*\<TCP port number>* |
| 2 |Invoke-Hcssetupwizard uit: RPC-server niet beschikbaar (uitzonde ring van HRESULT: 0x800706ba) |De hoofd oorzaak is een van de volgende:<ol><li>Het cluster is niet actief.</li><li>De passieve controller kan niet communiceren met de actieve controller en de opdracht wordt uitgevoerd vanuit de passieve controller.</li></ol> |Afhankelijk van de hoofd oorzaak:<ol><li>[Neem contact op met Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md) om te controleren of het cluster actief is.</li><li>Voer de opdracht uit vanaf de actieve controller. Als u de opdracht van de passieve controller wilt uitvoeren, moet u ervoor zorgen dat de passieve controller kan communiceren met de actieve controller. U moet [contact opnemen met Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md) als deze verbinding is verbroken.</li></ol> |
| 3 |Invoke-Hcssetupwizard uit: RPC-aanroep is mislukt (uitzonde ring van HRESULT: 0x800706be) |Het cluster is niet beschikbaar. |[Neem contact op met Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md) om te controleren of het cluster actief is. |
| 4 |Invoke-Hcssetupwizard uit: cluster bron niet gevonden (uitzonde ring van HRESULT: 0x8007138f) |De cluster bron is niet gevonden. Dit kan gebeuren wanneer de installatie niet juist is. |Mogelijk moet u het apparaat opnieuw instellen op de standaard fabrieks instellingen. [Neem contact op met Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md) om een cluster bron te maken. |
| 5 |Invoke-Hcssetupwizard uit: cluster bron niet online (uitzonde ring van HRESULT: 0x8007138c) |Cluster bronnen zijn niet online. |[Neem contact op met Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md) voor de volgende stappen. |

## <a name="errors-related-to-device-administrator-password"></a>Fouten met betrekking tot het beheerders wachtwoord voor het apparaat
Het standaard beheerders wachtwoord voor het apparaat is **Wachtwoord1**. Dit wacht woord verloopt na de eerste aanmelding; Daarom moet u de installatie wizard gebruiken om deze te wijzigen. U moet een nieuw beheerders wachtwoord voor het apparaat opgeven wanneer u het apparaat voor de eerste keer registreert. 

Zorg ervoor dat uw wacht woorden voldoen aan de volgende vereisten:

* Het beheerders wachtwoord voor uw apparaat moet tussen de 8 en 15 tekens lang zijn.
* Wacht woorden moeten drie van de volgende teken typen bevatten: kleine letters, hoofd letters, cijfers en speciale tekens. 
* Uw wacht woord mag niet hetzelfde zijn als de laatste 24 wacht woorden.

Houd er bovendien rekening mee dat wacht woorden elk jaar verlopen en alleen kunnen worden gewijzigd nadat het apparaat is geregistreerd. Als de registratie om welke reden dan ook mislukt, worden de wacht woorden niet gewijzigd.

Ga voor meer informatie over het beheerders wachtwoord voor het apparaat naar [het gebruik van de StorSimple Apparaatbeheer-service om uw StorSimple-wacht woord te wijzigen](storsimple-8000-change-passwords.md).

U kunt een of meer van de volgende fouten tegen komen bij het instellen van de StorSimple Snapshot Manager wacht woorden.

| Nee. | Foutbericht | Aanbevolen actie |
| --- | --- | --- |
| 1 |Het wacht woord overschrijdt de maximum lengte. |Het beheerders wachtwoord voor uw apparaat moet tussen de 8 en 15 tekens lang zijn. |
| 2 |Het wacht woord voldoet niet aan de vereiste lengte. |Het beheerders wachtwoord voor uw apparaat moet tussen de 8 en 15 tekens lang zijn.|
| 3 |Het wacht woord mag alleen kleine letters bevatten. |Wacht woorden moeten drie van de volgende vier teken typen bevatten: kleine letters, hoofd letters, cijfers en speciale tekens. Zorg ervoor dat uw wacht woord voldoet aan deze vereisten. |
| 4 |Het wacht woord moet numerieke tekens bevatten. |Wacht woorden moeten drie van de volgende vier teken typen bevatten: kleine letters, hoofd letters, cijfers en speciale tekens. Zorg ervoor dat uw wacht woord voldoet aan deze vereisten. |
| 5 |Het wacht woord moet speciale tekens bevatten. |Wacht woorden moeten drie van de volgende vier teken typen bevatten: kleine letters, hoofd letters, cijfers en speciale tekens. Zorg ervoor dat uw wacht woord voldoet aan deze vereisten. |
| 6 |Het wacht woord moet drie van de volgende vier teken typen bevatten: hoofd letters, kleine letters, cijfers en speciale tekens. |Uw wacht woord bevat niet de vereiste typen tekens. Zorg ervoor dat uw wacht woord voldoet aan deze vereisten. |
| 7 |De para meter komt niet overeen met de bevestiging. |Zorg ervoor dat uw wacht woord voldoet aan alle vereisten en of u het correct hebt ingevoerd. |
| 8 |Uw wacht woord mag niet overeenkomen met de standaard waarde. |Het standaardapparaatwachtwoord is *Wachtwoord1*. Als u zich voor de eerste keer aanmeldt, moet u dit wacht woord wijzigen. |
| 9 |Het wacht woord dat u hebt opgegeven, komt niet overeen met het wacht woord van het apparaat. Voer het wacht woord opnieuw in. |Controleer het wacht woord en typ het opnieuw. |

Wacht woorden worden verzameld voordat het apparaat wordt geregistreerd, maar worden pas na een geslaagde registratie toegepast. De werk stroom voor wachtwoord herstel vereist dat het apparaat wordt geregistreerd.

> [!IMPORTANT]
> Over het algemeen geldt dat als een poging om een wacht woord toe te passen mislukt, de software herhaaldelijk probeert het wacht woord te verzamelen totdat het is gelukt. In zeldzame gevallen kan het wacht woord niet worden toegepast. In dit geval kunt u het apparaat registreren en door gaan, maar de wacht woorden worden niet gewijzigd. U kunt het beheerders wachtwoord van het apparaat na de registratie van de Azure Portal wijzigen.


U kunt het wacht woord opnieuw instellen in de Azure Portal via de StorSimple Apparaatbeheer-service. Ga voor meer informatie naar [het beheerders wachtwoord van het apparaat wijzigen](storsimple-8000-change-passwords.md#change-the-device-administrator-password).

## <a name="errors-during-device-registration"></a>Fouten tijdens het registreren van apparaten
U gebruikt de StorSimple Apparaatbeheer-service die wordt uitgevoerd in Microsoft Azure om het apparaat te registreren. Tijdens het registreren van het apparaat kunt u een of meer van de volgende problemen tegen komen.

| Nee. | Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
| --- | --- | --- | --- |
| 1 |Fout 350027: kan het apparaat niet registreren bij de StorSimple-Apparaatbeheer. | |Wacht een paar minuten en probeer het opnieuw. Als het probleem zich blijft voordoen, [neemt u contact op met Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md). |
| 2 |Fout 350013: er is een fout opgetreden bij het registreren van het apparaat. Dit kan worden veroorzaakt door een onjuiste service registratie sleutel. | |Registreer het apparaat opnieuw met de juiste service registratie sleutel. Zie [de service registratie sleutel ophalen](storsimple-8000-manage-service.md#get-the-service-registration-key) voor meer informatie. |
| 3 |Fout 350063: er is een verificatie voor de StorSimple-Apparaatbeheer service voltooid, maar de registratie is mislukt. Voer de bewerking na enige tijd opnieuw uit. |Deze fout geeft aan dat de verificatie met ACS is geslaagd, maar dat de registratie aanroep voor de service is mislukt. Dit kan het gevolg zijn van een sporadisch netwerk fout. |Als het probleem zich blijft voordoen, neemt u [contact op met Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md). |
| 4 |Fout 350049: de service is niet bereikbaar tijdens de registratie. |Wanneer de aanroep naar de service wordt gedaan, wordt er een webuitzondering ontvangen. In sommige gevallen kan dit worden opgelost door de bewerking later opnieuw uit te voeren. |Controleer uw IP-adres en DNS-naam en voer de bewerking opnieuw uit. Als het probleem zich blijft voordoen, [neemt u contact op met Microsoft ondersteuning.](storsimple-8000-contact-microsoft-support.md) |
| 5 |Fout 350031: het apparaat is al geregistreerd. | |Geen actie vereist. |
| 6 |Fout 350016: registratie van het apparaat is mislukt. | |Controleer of de registratie sleutel juist is. |
| 7 |Invoke-Hcssetupwizard uit: er is een fout opgetreden tijdens het registreren van uw apparaat. Dit kan worden veroorzaakt door een onjuist IP-adres of een onjuiste DNS-naam. Controleer de netwerk instellingen en probeer het opnieuw. Als het probleem zich blijft voordoen, [neemt u contact op met Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md). (Fout 350050) |Zorg ervoor dat het apparaat het externe netwerk kan pingen. Als u geen verbinding met een extern netwerk hebt, kan de registratie mislukken met deze fout. Deze fout kan bestaan uit een combi natie van een of meer van de volgende:<ul><li>Onjuist IP-adres</li><li>Onjuist subnet</li><li>Onjuiste gateway</li><li>Onjuiste DNS-instellingen</li></ul> |Raadpleeg de stappen in het [voor beeld van stapsgewijze probleem oplossing](#step-by-step-storsimple-troubleshooting-example). |
| 8 |Invoke-Hcssetupwizard uit: de huidige bewerking is mislukt vanwege een interne service fout [0x1FBE2]. Voer de bewerking na enige tijd opnieuw uit. Als het probleem zich blijft voordoen, neemt u contact op met Microsoft Ondersteuning. |Dit is een algemene fout die wordt gegenereerd voor alle gebruikers onzichtbare fouten van de service of de agent. De meest voorkomende reden is mogelijk dat de ACS-verificatie is mislukt. Een mogelijke oorzaak van de fout is dat er problemen zijn met de configuratie van de NTP-server en dat de tijd op het apparaat niet juist is ingesteld. |Corrigeer de tijd (als er problemen zijn) en voer de registratie bewerking opnieuw uit. Als u de tijd zone kunt aanpassen met behulp van de opdracht Set-HcsSystem-zone, typt u elk woord in de tijd zone (bijvoorbeeld ' Pacific (standaard tijd) ').  Als dit probleem zich blijft voordoen, [neemt u contact op met Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md) voor de volgende stappen. |
| 9 |Waarschuwing: kan het apparaat niet activeren. Uw StorSimple-wacht woord voor de apparaat-en gebruikers Snapshot Manager zijn niet gewijzigd. |Als de registratie mislukt, worden de StorSimple en de wacht woorden van de apparaat-Snapshot Manager niet gewijzigd. | |

## <a name="tools-for-troubleshooting-storsimple-deployments"></a>Hulpprogramma's voor het oplossen van problemen met StorSimple-implementaties
StorSimple bevat verschillende hulpprogram ma's die u kunt gebruiken om problemen met uw StorSimple-oplossing op te lossen. Tot deze hulpmiddelen behoren onder meer:

* Ondersteuning voor pakketten en logboeken van apparaten.
* Cmdlets die zijn bedoeld voor het oplossen van problemen.

## <a name="support-packages-and-device-logs-available-for-troubleshooting"></a>Ondersteunings pakketten en logboeken voor apparaten beschikbaar voor het oplossen van problemen
Een ondersteunings pakket bevat alle relevante logboeken die het Microsoft Ondersteuning team kunnen helpen bij het oplossen van problemen met apparaten. U kunt Windows PowerShell voor StorSimple gebruiken om een versleuteld ondersteunings pakket te genereren dat u vervolgens met ondersteunings medewerkers kunt delen.

### <a name="to-view-the-logs-or-the-contents-of-the-support-package"></a>De logboeken of de inhoud van het ondersteunings pakket weer geven
1. Gebruik Windows PowerShell voor StorSimple om een ondersteunings pakket te genereren, zoals beschreven in [een ondersteunings pakket maken en beheren](storsimple-8000-create-manage-support-package.md).
2. Down load het [script voor ontsleuteling](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) lokaal op uw client computer.
3. Gebruik deze [Stapsgewijze procedure](storsimple-8000-create-manage-support-package.md#edit-a-support-package) om het ondersteunings pakket te openen en te ontsleutelen.
4. De ontsleutelde ondersteunings pakket logboeken bevinden zich in de indeling etw/etvx. U kunt de volgende stappen uitvoeren om deze bestanden weer te geven in Windows Logboeken:
   
   1. Voer de opdracht **eventvwr** uit op uw Windows-client om de logboeken te starten.
   2. Klik in het deel venster **acties** op **opgeslagen logboek openen** en wijs de logboek bestanden in etvx/etw-indeling (het ondersteunings pakket) aan. U kunt het bestand nu weer geven. Nadat u het bestand hebt geopend, kunt u met de rechter muisknop klikken en het bestand opslaan als tekst.
      
      > [!IMPORTANT]
      > U kunt ook de cmdlet **Get-Wine vent** gebruiken om deze bestanden te openen in Windows Power shell. Zie [Get-Wine vent](/powershell/module/microsoft.powershell.diagnostics/get-winevent) in de naslag documentatie voor Windows Power shell-cmdlets voor meer informatie.
     
5. Wanneer de logboeken in Logboeken zijn geopend, zoekt u de volgende logboeken die problemen bevatten met betrekking tot de apparaatconfiguratie:
   
   * hcs_pfconfig/Operational-logboek
   * hcs_pfconfig/config
6. Zoek in de logboek bestanden naar teken reeksen met betrekking tot de cmdlets die worden aangeroepen door de installatie wizard. Zie de wizard voor het installeren van de [eerste keer](#first-time-setup-wizard-process) voor een lijst met deze cmdlets.
7. Als u de oorzaak van het probleem niet kunt achterhalen, kunt u [contact opnemen met Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md) voor de volgende stappen. Volg de stappen in [een ondersteunings aanvraag maken](storsimple-8000-contact-microsoft-support.md#create-a-support-request) wanneer u contact opneemt met Microsoft ondersteuning voor hulp.

## <a name="cmdlets-available-for-troubleshooting"></a>Cmdlets die beschikbaar zijn voor het oplossen van problemen
Gebruik de volgende Windows Power shell-cmdlets om connectiviteits fouten te detecteren.

* `Get-NetAdapter`: Gebruik deze cmdlet om de status van netwerk interfaces te detecteren.
* `Test-Connection`: Gebruik deze cmdlet om de netwerk verbinding binnen en buiten het netwerk te controleren.
* `Test-HcsmConnection`: Gebruik deze cmdlet om de connectiviteit van een geregistreerd apparaat te controleren.
* `Sync-HcsTime`: Gebruik deze cmdlet om de tijd van het apparaat weer te geven en een tijd synchronisatie met de NTP-server af te dwingen.
* `Enable-HcsPing` en `Disable-HcsPing` : met deze cmdlets kunnen de hosts de netwerk interfaces op uw StorSimple-apparaat pingen. Standaard reageren de StorSimple-netwerk interfaces niet op ping-aanvragen.
* `Trace-HcsRoute`: Gebruik deze cmdlet als een hulp programma voor route tracering. Er worden pakketten naar elke router verzonden naar een eind bestemming gedurende een bepaalde periode en vervolgens worden de resultaten berekend op basis van de pakketten die worden geretourneerd door elke hop. Aangezien `Trace-HcsRoute` de mate van pakket verlies bij elke router of koppeling wordt weer gegeven, kunt u bepalen welke routers of koppelingen mogelijk netwerk problemen veroorzaken.
* `Get-HcsRoutingTable`: Gebruik deze cmdlet om de lokale IP-routerings tabel weer te geven.

## <a name="troubleshoot-with-the-get-netadapter-cmdlet"></a>Problemen oplossen met de cmdlet Get-NetAdapter
Wanneer u netwerk interfaces configureert voor een implementatie voor de eerste keer dat het apparaat wordt geïmplementeerd, is de hardware-status niet beschikbaar in de gebruikers interface van de StorSimple Apparaatbeheer-service omdat het apparaat nog niet is geregistreerd bij de service. Daarnaast is het mogelijk dat de Blade **Hardware-status** niet altijd goed overeenkomt met de status van het apparaat, met name als er problemen zijn die van invloed zijn op de service synchronisatie. In deze gevallen kunt u de- `Get-NetAdapter` cmdlet gebruiken om de status en status van uw netwerk interfaces te bepalen.

### <a name="to-see-a-list-of-all-the-network-adapters-on-your-device"></a>Een lijst met alle netwerk adapters op het apparaat weer geven
1. Start Windows PowerShell voor StorSimple en typ `Get-NetAdapter` . 
2. Gebruik de uitvoer van de `Get-NetAdapter` cmdlet en de volgende richt lijnen om inzicht te krijgen in de status van uw netwerk interface.
   
   * Als de interface in orde is en is ingeschakeld, wordt de status **van** de **ifIndex** weer gegeven.
   * Als de interface is in orde, maar niet fysiek is verbonden (door een netwerk kabel), wordt de **ifIndex** als **uitgeschakeld** weer gegeven.
   * Als de interface in orde is, maar niet is ingeschakeld, wordt de status van **ifIndex** weer gegeven als **NotPresent**.
   * Als de interface niet bestaat, wordt deze niet weer gegeven in deze lijst. In de gebruikers interface van de StorSimple-Apparaatbeheer-service wordt deze interface nog steeds weer gegeven met de status mislukt.

Ga voor meer informatie over het gebruik van deze cmdlet naar [Get-netadapter](/powershell/module/netadapter/get-netadapter) in de naslag gids voor Windows Power shell-cmdlets.

In de volgende secties worden voor beelden van uitvoer van de cmdlet weer gegeven `Get-NetAdapter` .

 In deze voor beelden was controller 0 de passieve controller en is deze als volgt geconfigureerd:

* DATA 0-, DATA 1-, DATA 2-en DATA 3-netwerk interfaces bestonden op het apparaat.
* DATA 4-en DATA 5-netwerk interface kaarten zijn niet aanwezig; Daarom worden ze niet weer gegeven in de uitvoer.
* DATA 0 is ingeschakeld.

Controller 1 was de actieve controller en is als volgt geconfigureerd:

* DATA 0, DATA 1, DATA 2, DATA 3, DATA 4 en DATA 5-netwerk interfaces bestonden op het apparaat.
* DATA 0 is ingeschakeld.

**Voorbeeld uitvoer – controller 0**

De volgende voorbeeld gegevens zijn de uitvoer van controller 0 (de passieve controller). De gegevens 1, DATA 2 en DATA 3 zijn niet verbonden. DATA 4 en DATA 5 worden niet weer gegeven, omdat ze niet aanwezig zijn op het apparaat.

```output
Controller0>Get-NetAdapter
Name                 InterfaceDescription                        ifIndex  Status
------               --------------------                        -------  ----------
DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
Ethernet 2           HCS VNIC                                    13       Up
DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
```


**Voorbeeld uitvoer – controller 1**

De volgende voorbeeld gegevens zijn de uitvoer van controller 1 (de actieve controller). Alleen de DATA 0-netwerk interface op het apparaat is geconfigureerd en werkt.

```output
Controller1>Get-NetAdapter
Name                 InterfaceDescription                        ifIndex  Status
------               --------------------                        -------  ----------
DATA3                Mellanox ConnectX-3 Ethernet Adapter        18       NotPresent
DATA2                Mellanox ConnectX-3 Ethernet Adapter #2     19       NotPresent
DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
Ethernet 2           HCS VNIC                                    13       Up
DATA5                Intel(R) Gigabit ET Dual Port Server...     14       NotPresent
DATA4                Intel(R) Gigabit ET Dual Port Serv...#2     17       NotPresent
```


## <a name="troubleshoot-with-the-test-connection-cmdlet"></a>Problemen oplossen met de cmdlet Test-Connection
U kunt de `Test-Connection` cmdlet gebruiken om te bepalen of het StorSimple-apparaat verbinding kan maken met het externe netwerk. Als alle netwerk parameters, met inbegrip van de DNS, correct zijn geconfigureerd in de installatie wizard, kunt u de `Test-Connection` cmdlet gebruiken om een bekend adres buiten het netwerk te pingen, zoals Outlook.com.

Als de ping-cmdlet is uitgeschakeld, moet u Ping inschakelen voor gebruik bij het oplossen van verbindings problemen.

Bekijk de volgende voor beelden van uitvoer van de `Test-Connection` cmdlet.

> [!NOTE]
> In het eerste voor beeld is het apparaat geconfigureerd met een onjuiste DNS. In het tweede voor beeld is de DNS juist.

**Voorbeeld uitvoer – onjuiste DNS**

Het volgende voor beeld bevat geen uitvoer voor de IPV4-en IPV6-adressen, wat aangeeft dat de DNS niet is omgezet. Er is geen verbinding met het externe netwerk en er moet een correcte DNS worden opgegeven.

```output
Source        Destination     IPV4Address      IPV6Address
------        -----------     -----------      -----------
HCSNODE0      outlook.com
HCSNODE0      outlook.com
HCSNODE0      outlook.com
HCSNODE0      outlook.com
```

**Voorbeeld uitvoer-juiste DNS**

In het volgende voor beeld retourneert de DNS het IPV4-adres, waarmee wordt aangegeven dat de DNS juist is geconfigureerd. De uitvoer bevestigt dat er verbinding is met het externe netwerk.

```output
Source        Destination     IPV4Address      IPV6Address
------        -----------     -----------      -----------
HCSNODE0      outlook.com     132.245.92.194
HCSNODE0      outlook.com     132.245.92.194
HCSNODE0      outlook.com     132.245.92.194
HCSNODE0      outlook.com     132.245.92.194
```

## <a name="troubleshoot-with-the-test-hcsmconnection-cmdlet"></a>Problemen oplossen met de cmdlet Test-HcsmConnection
Gebruik de `Test-HcsmConnection` cmdlet voor een apparaat dat al is verbonden met en geregistreerd bij uw StorSimple-Apparaatbeheer service. Met deze cmdlet kunt u de connectiviteit controleren tussen een geregistreerd apparaat en de bijbehorende StorSimple-Apparaatbeheer service. U kunt deze opdracht uitvoeren op Windows PowerShell voor StorSimple.

### <a name="to-run-the-test-hcsmconnection-cmdlet"></a>De cmdlet Test-HcsmConnection uitvoeren
1. Zorg ervoor dat het apparaat is geregistreerd.
2. Controleer de Apparaatstatus. Als het apparaat wordt gedeactiveerd, in de onderhouds modus of offline, ziet u mogelijk een van de volgende fouten:
   
   * Error code. CiSDeviceDecommissioned: geeft aan dat het apparaat is gedeactiveerd.
   * Error code. DeviceNotReady: geeft aan dat het apparaat zich in de onderhouds modus bevindt.
   * Error code. DeviceNotReady: geeft aan dat het apparaat niet online is.
3. Controleer of de StorSimple-Apparaatbeheer service wordt uitgevoerd (gebruik de cmdlet [Get-ClusterResource](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee461004(v=technet.10)) ). Als de service niet wordt uitgevoerd, ziet u mogelijk de volgende fouten:
   
   * Error code. CiSApplianceAgentNotOnline
   * Error code. CisPowershellScriptHcsError: geeft aan dat er een uitzonde ring is opgetreden bij het uitvoeren van Get-ClusterResource.
4. Controleer het Access Control Service-token (ACS). Als er een webuitzondering wordt gegenereerd, kan dit het resultaat zijn van een gateway probleem, een ontbrekende proxy verificatie, een onjuiste DNS of een verificatie fout. Mogelijk worden de volgende fouten weer geven:
   
   * Error code. CiSApplianceGateway: geeft een HTTP status code. BadGateway-uitzonde ring aan: de naam resolver-service kan de hostnaam niet omzetten.
   * Error code. CiSApplianceProxy: geeft een HTTP status code. ProxyAuthenticationRequired-uitzonde ring aan (HTTP-status code 407): de client kan niet worden geverifieerd bij de proxy server.
   * Error code. CiSApplianceDNSError: geeft een WebExceptionStatus. NameResolutionFailure-uitzonde ring aan: de naam resolver-service kan de hostnaam niet omzetten.
   * Error code. CiSApplianceACSError: geeft aan dat de service een verificatie fout heeft geretourneerd, maar dat er verbinding is.
     
     Als er geen web-uitzonde ring wordt gegenereerd, controleert u op error code. CiSApplianceFailure, waarmee wordt aangegeven dat het apparaat is mislukt.
5. Controleer de verbinding met de Cloud service. Als de service een web-uitzonde ring genereert, ziet u mogelijk de volgende fouten:
   
   * Error code. CiSApplianceGateway: geeft een HTTP status code. BadGateway-uitzonde ring aan: een tussenliggende proxy server heeft een ongeldige aanvraag ontvangen van een andere proxy of van de oorspronkelijke server.
   * Error code. CiSApplianceProxy: geeft een HTTP status code. ProxyAuthenticationRequired-uitzonde ring aan (HTTP-status code 407): de client kan niet worden geverifieerd bij de proxy server.
   * Error code. CiSApplianceDNSError: geeft een WebExceptionStatus. NameResolutionFailure-uitzonde ring aan: de naam resolver-service kan de hostnaam niet omzetten.
   * Error code. CiSApplianceACSError: geeft aan dat de service een verificatie fout heeft geretourneerd, maar er is een verbinding.
     
     Als er geen web-uitzonde ring wordt gegenereerd, controleert u op error code. CiSApplianceSaasServiceError, waarmee wordt aangegeven dat er een probleem is met de StorSimple-Apparaatbeheer service.
6. Controleer de Azure Service Bus-verbinding. Error code. CiSApplianceServiceBusError geeft aan dat het apparaat geen verbinding kan maken met de Service Bus.

De logboek bestanden CiSCommandletLog0Curr. errlog en CiSAgentsvc0Curr. errlog hebben meer informatie, zoals Details van uitzonde ringen.

Ga naar [test-HcsmConnection](/previous-versions/windows/powershell-scripting/dn715782(v=wps.630)) in de Windows Power shell-referentie documentatie voor meer informatie over het gebruik van de cmdlet.

> [!IMPORTANT]
> U kunt deze cmdlet uitvoeren voor zowel de actieve als de passieve controller.

Bekijk de volgende voor beelden van uitvoer van de `Test-HcsmConnection` cmdlet.

**Voor beeld van uitvoer: het apparaat is geregistreerd met StorSimple update 3**

```output
Controller1>Test-HcsmConnection

Checking device registration state  ... Success
Device registered successfully

Checking primary NTP server [time.windows.com] ... Success

Checking web proxy  ... NOT SET

Checking primary IPv4 DNS server [10.222.118.154] ... Success
Checking primary IPv6 DNS server  ... NOT SET
Checking secondary IPv4 DNS server [10.222.120.24] ... Success
Checking secondary IPv6 DNS server  ... NOT SET

Checking device online  ... Success

Checking device authentication  ... This will take a few minutes.
Checking device authentication  ... Success

Checking connectivity from device to service  ... This will take a few minutes.

Checking connectivity from device to service  ... Success

Checking connectivity from service to device  ... Success

Checking connectivity to Microsoft Update servers  ... Success
Controller1>
```

**Voorbeeld uitvoer – offline apparaat** 

Dit voor beeld is afkomstig van een apparaat met de status **offline** in het Azure Portal.

```output
Checking device registrationstate: Success
Device is registered successfully
Checking connectivity from device to SaaS.. Failure
```

Het apparaat kan geen verbinding maken met behulp van de huidige web proxy-configuratie. Er is mogelijk een probleem met de configuratie van de webproxy of een probleem met de netwerk verbinding. In dit geval moet u er zeker van zijn dat uw webproxy-instellingen juist zijn en uw webproxyservers online en bereikbaar zijn.

## <a name="troubleshoot-with-the-sync-hcstime-cmdlet"></a>Problemen oplossen met de cmdlet Sync-HcsTime
Gebruik deze cmdlet om de tijd van het apparaat weer te geven. Als de apparaattijd een offset heeft met de NTP-server, kunt u deze cmdlet vervolgens gebruiken om de tijd te synchroniseren met uw NTP-server.
- Als de offset tussen het apparaat en de NTP-server langer is dan vijf minuten, wordt er een waarschuwing weer gegeven. 
- Als de offset 15 minuten overschrijdt, wordt het apparaat offline gezet. U kunt deze cmdlet nog steeds gebruiken voor het afdwingen van een tijd synchronisatie. 
- Als de offset echter 15 uur overschrijdt, kunt u de tijd niet meer synchroniseren en wordt er een fout bericht weer gegeven.

**Voorbeeld uitvoer – geforceerde tijd synchronisatie met Sync-HcsTime**

```output
Controller0>Sync-HcsTime
The current device time is 4/24/2015 4:05:40 PM UTC.

Time difference between NTP server and appliance is 00.0824069 seconds. Do you want to resync time with NTP server?
[Y] Yes [N] No (Default is "Y"): Y
Controller0>
```

## <a name="troubleshoot-with-the-enable-hcsping-and-disable-hcsping-cmdlets"></a>Problemen oplossen met de Enable-HcsPing-en Disable-HcsPing-cmdlets
Gebruik deze cmdlets om ervoor te zorgen dat de netwerk interfaces op uw apparaat reageren op ICMP-ping-aanvragen. Standaard reageren de StorSimple-netwerk interfaces niet op ping-aanvragen. Het gebruik van deze cmdlet is de eenvoudigste manier om erachter te komen of uw apparaat online en bereikbaar is.

**Voorbeeld uitvoer – Enable-HcsPing en Disable-HcsPing**

```output
Controller0>
Controller0>Enable-HcsPing
Successfully enabled ping.
Controller0>
Controller0>
Controller0>Disable-HcsPing
Successfully disabled ping.
Controller0>
```

## <a name="troubleshoot-with-the-trace-hcsroute-cmdlet"></a>Problemen oplossen met de cmdlet Trace-HcsRoute
Gebruik deze cmdlet als een hulp programma voor route tracering. Er worden pakketten naar elke router verzonden naar een eind bestemming gedurende een bepaalde periode en vervolgens worden de resultaten berekend op basis van de pakketten die worden geretourneerd door elke hop. Omdat met de cmdlet de mate van pakket verlies bij een router of koppeling wordt weer gegeven, kunt u bepalen welke routers of koppelingen mogelijk netwerk problemen veroorzaken.

**Voorbeeld uitvoer waarin wordt getoond hoe u de route van een pakket traceert met Trace-HcsRoute**

```output
Controller0>Trace-HcsRoute -Target 10.126.174.25

Tracing route to contoso.com [10.126.174.25]
over a maximum of 30 hops:
   0  HCSNode0 [10.126.173.90]
   1  contoso.com [10.126.174.25]

Computing statistics for 25 seconds...
            Source to Here   This Node/Link
Hop  RTT    Lost/Sent = Pct  Lost/Sent = Pct  Address
   0                                           HCSNode0 [10.126.173.90]
                                 0/ 100 =  0%   |
   1    0ms     0/ 100 =  0%     0/ 100 =  0%  contoso.com
[10.126.174.25]

Trace complete.
```

## <a name="troubleshoot-with-the-get-hcsroutingtable-cmdlet"></a>Problemen oplossen met de cmdlet Get-HcsRoutingTable
Gebruik deze cmdlet om de routerings tabel voor uw StorSimple-apparaat weer te geven. Een routerings tabel is een set regels die u kan helpen bepalen waar gegevens pakketten die via een Internet Protocol-netwerk worden verzonden, worden omgeleid.

De routerings tabel toont de interfaces en de gateway die de gegevens naar de opgegeven netwerken routeren. Het biedt ook de metrische gegevens van de route ring, de maker van het pad dat wordt gebruikt om een bepaald doel te bereiken. Hoe lager de routerings metriek, des te hoger de voor keur.

Stel bijvoorbeeld dat u twee netwerk interfaces, DATA 2 en DATA 3 hebt die zijn verbonden met internet. Als de metrische gegevens van de route ring voor DATA 2 en DATA 3 respectievelijk 15 en 261 zijn, is gegevens 2 met de laagste routerings metriek de voorkeurs interface die wordt gebruikt om het Internet te bereiken.

Als u update 1 op uw StorSimple-apparaat uitvoert, heeft uw DATA 0-netwerk interface de hoogste voor keur voor het Cloud verkeer. Met deze configuratie, zelfs als er andere interfaces in de Cloud zijn ingeschakeld, wordt het grootste deel van het Cloud verkeer doorgestuurd via gegevens 0.<!--This implies that even if there are other cloud-enabled interfaces, the cloud traffic would be routed through DATA 0. - Preceding sentence replaces this one. Use of "This implies" is a bit murky. DATA 0 will be the preferred network interface? Is my translation OK?-->

Als u de `Get-HcsRoutingTable` cmdlet uitvoert zonder para meters op te geven (zoals in het volgende voor beeld), voert de cmdlet zowel IPv4-als IPv6-routerings tabellen uit. U kunt ook `Get-HcsRoutingTable -IPv4` `Get-HcsRoutingTable -IPv6`  een relevante routerings tabel opgeven of ophalen.

```output
Controller0>
Controller0>Get-HcsRoutingTable
===========================================================================
Interface List
   14...00 50 cc 79 63 40 ......Intel(R) 82574L Gigabit Network Connection
   12...02 9a 0a 5b 98 1f ......Microsoft Failover Cluster Virtual Adapter
   13...28 18 78 bc 4b 85 ......HCS VNIC
   1...........................Software Loopback Interface 1
   21...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
   22...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #3
===========================================================================

IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
            0.0.0.0          0.0.0.0  192.168.111.100  192.168.111.101     15
         127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
         127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
   127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
      169.254.0.0      255.255.0.0         On-link     169.254.1.235    261
      169.254.1.235  255.255.255.255         On-link     169.254.1.235    261
   169.254.255.255  255.255.255.255         On-link     169.254.1.235    261
      192.168.111.0    255.255.255.0         On-link   192.168.111.101    266
   192.168.111.101  255.255.255.255         On-link   192.168.111.101    266
   192.168.111.255  255.255.255.255         On-link   192.168.111.101    266
         224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
         224.0.0.0        240.0.0.0         On-link     169.254.1.235    261
         224.0.0.0        240.0.0.0         On-link   192.168.111.101    266
   255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
   255.255.255.255  255.255.255.255         On-link     169.254.1.235    261
   255.255.255.255  255.255.255.255         On-link   192.168.111.101    266
===========================================================================
Persistent Routes:
   Network Address          Netmask  Gateway Address  Metric
            0.0.0.0          0.0.0.0  192.168.111.100       5
===========================================================================

IPv6 Route Table
===========================================================================
Active Routes:
   If Metric Network Destination      Gateway
   1    306 ::1/128                  On-link
   13    276 fd99:4c5b:5525:d80b::/64 On-link
   13    276 fd99:4c5b:5525:d80b::1/128
                                    On-link
   13    276 fd99:4c5b:5525:d80b::3/128
                                    On-link
   13    276 fe80::/64                On-link
   12    261 fe80::/64                On-link
   13    276 fe80::17a:4eba:7c80:727f/128
                                    On-link
   12    261 fe80::fc97:1a53:e81a:3454/128
                                    On-link
   1    306 ff00::/8                 On-link
   13    276 ff00::/8                 On-link
   12    261 ff00::/8                 On-link
   14    266 ff00::/8                 On-link
===========================================================================
Persistent Routes:
   None

Controller0>
```

## <a name="step-by-step-storsimple-troubleshooting-example"></a>Voor beeld van Step-by-Step StorSimple Troubleshooting
In het volgende voor beeld ziet u stapsgewijze oplossingen voor een StorSimple-implementatie. In het voorbeeld scenario mislukt apparaatregistratie met een fout bericht dat aangeeft dat de netwerk instellingen of de DNS-naam onjuist zijn.

Het geretourneerde fout bericht is:

```output
Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
+CategoryInfo: Not specified
+FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand
```

De fout kan een van de volgende oorzaken hebben:

* Onjuiste hardware-installatie
* Defecte netwerk interface (s)
* Onjuist IP-adres, subnetmasker, gateway, primaire DNS-server of webproxy
* Onjuiste registratie sleutel
* Onjuiste firewall instellingen

### <a name="to-locate-and-fix-the-device-registration-problem"></a>Het registratie probleem van het apparaat zoeken en oplossen
1. Controleer de apparaatconfiguratie: Voer op de actieve controller uit `Invoke-HcsSetupWizard` .
   
   > [!NOTE]
   > De wizard Setup moet worden uitgevoerd op de actieve controller. Als u wilt controleren of u verbinding hebt met de actieve controller, bekijkt u de banner die in de seriële console wordt weer gegeven. De banner geeft aan of u verbonden bent met controller 0 of controller 1 en of de controller actief of passief is. Ga voor meer informatie naar [een actieve controller op het apparaat identificeren](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
   
2. Controleer of het apparaat op de juiste wijze is aangesloten: Controleer de netwerk kabels op het apparaat van de back-upvlak. De bekabeling is specifiek voor het model van het apparaat. Ga voor meer informatie naar [uw StorSimple 8100-apparaat installeren](storsimple-8100-hardware-installation.md) of [installeer uw StorSimple 8600-apparaat](storsimple-8600-hardware-installation.md).
   
   > [!NOTE]
   > Als u 10 GbE-netwerk poorten gebruikt, moet u de meegeleverde QSFP-SFP-adapters en SFP-kabels gebruiken. Zie voor meer informatie de [lijst met kabels, switches en transceivers die worden aanbevolen voor de 10 GbE-poorten](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
  
3. Controleer de status van de netwerk interface:
   
   * Gebruik de Get-NetAdapter cmdlet om de status van de netwerk interfaces voor de gegevens 0 te detecteren. 
   * Als de koppeling niet werkt, wordt in de `ifindex` status aangegeven dat de interface niet beschikbaar is. Vervolgens moet u de netwerk verbinding van de poort naar het apparaat en de switch controleren. U moet ook onjuiste kabels uitchecken. 
   * Als u vermoedt dat de gegevens 0-poort op de actieve controller is mislukt, kunt u bevestigen dat u verbinding maakt met de DATA 0-poort op de controller 1. Verbreek de verbinding met de netwerk kabel van de achterkant van het apparaat van controller 0, sluit de kabel aan op controller 1 en voer de Get-NetAdapter cmdlet opnieuw uit.
   
     Als de DATA 0-poort op een controller mislukt, [neemt u contact op met Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md) voor de volgende stappen. Mogelijk moet u de controller op het systeem vervangen.
4. Controleer de verbinding met de switch:
   
   * Zorg ervoor dat de gegevens 0 netwerk interfaces op controller 0 en controller 1 in uw primaire behuizing zich in hetzelfde subnet bevinden. 
   * Controleer de hub of router. Normaal gesp roken moet u beide controllers verbinden met dezelfde hub of router. 
   * Zorg ervoor dat de switches die u voor de verbinding gebruikt, gegevens 0 hebben voor beide controllers in hetzelfde vLAN.
5. Gebruikers fouten elimineren:
   
   * Voer de installatie wizard opnieuw uit (Voer **invoke-hcssetupwizard uit** uit) en geef de waarden opnieuw op om ervoor te zorgen dat er geen fouten zijn. 
   * Controleer de gebruikte registratie sleutel. Dezelfde registratie sleutel kan worden gebruikt om meerdere apparaten te verbinden met een StorSimple-Apparaatbeheer service. Gebruik de procedure in [down load de service registratie sleutel](storsimple-8000-manage-service.md#get-the-service-registration-key) om ervoor te zorgen dat u de juiste registratie sleutel gebruikt.
     
     > [!IMPORTANT]
     > Als er meerdere services worden uitgevoerd, moet u ervoor zorgen dat de registratie sleutel voor de juiste service wordt gebruikt om het apparaat te registreren. Als u een apparaat met de verkeerde StorSimple-Apparaatbeheer service hebt geregistreerd, moet u [contact opnemen met Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md) om de volgende stappen uit te voeren. Mogelijk moet u de fabrieks instellingen van het apparaat opnieuw instellen (wat kan leiden tot gegevens verlies) om deze vervolgens te verbinden met de gewenste service.
     > 
     > 
6. Gebruik de Test-Connection-cmdlet om te controleren of u verbinding hebt met het externe netwerk. Ga voor meer informatie naar [problemen oplossen met de cmdlet Test-Connection](#troubleshoot-with-the-test-connection-cmdlet).
7. Controleren op Firewall interferentie. Als u hebt gecontroleerd of de instellingen voor de virtuele IP (VIP), het subnet, de gateway en DNS juist zijn en er nog steeds verbindings problemen worden weer geven, is het mogelijk dat de firewall de communicatie blokkeert tussen uw apparaat en het externe netwerk. Zorg ervoor dat de poorten 80 en 443 op uw StorSimple-apparaat beschikbaar zijn voor uitgaande communicatie. Zie [netwerk vereisten voor uw StorSimple-apparaat](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device)voor meer informatie.
8. Bekijk de logboeken. Ga naar [ondersteunings pakketten en logboeken voor apparaten die beschikbaar zijn voor het oplossen van problemen](#support-packages-and-device-logs-available-for-troubleshooting).
9. Als de voor gaande stappen het probleem niet oplossen, [neemt u contact op met Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md) voor hulp.

## <a name="next-steps"></a>Volgende stappen
[Meer informatie over het gebruik van het diagnostische hulp programma voor het oplossen van problemen met een StorSimple-apparaat](storsimple-8000-diagnostics.md).

<!--Link references-->

[1]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd379547(v=ws.10)
[2]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd392266(v=ws.10)