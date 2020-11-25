---
title: Waarschuwingen voor StorSimple 8000 Series-apparaat weer geven en beheren
description: Beschrijft StorSimple-waarschuwings voorwaarden en ernst, het configureren van waarschuwings meldingen en het gebruik van de StorSimple Apparaatbeheer-service voor het beheren van waarschuwingen.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: 36ed87a75d590a8647e5347b3e90e6f5159dc6b5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016842"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-storsimple-alerts"></a>De StorSimple Apparaatbeheer-service gebruiken voor het weer geven en beheren van StorSimple-waarschuwingen

## <a name="overview"></a>Overzicht

De Blade **waarschuwingen** in de StorSimple-Apparaatbeheer service biedt u een manier om op basis van StorSimple-gerelateerde waarschuwingen te controleren en te wissen. Op deze Blade kunt u de status problemen van uw StorSimple-apparaten en de algehele Microsoft Azure StorSimple oplossing centraal bewaken.

In deze zelf studie worden veelvoorkomende waarschuwings voorwaarden en waarschuwings Ernst niveaus beschreven en wordt uitgelegd hoe u waarschuwings meldingen kunt configureren. Daarnaast bevat het een snelle naslag tabel voor waarschuwingen, waarmee u snel een specifieke waarschuwing kunt vinden en op de juiste wijze kunt reageren.

![Pagina waarschuwingen](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="common-alert-conditions"></a>Algemene waarschuwings voorwaarden

Uw StorSimple-apparaat genereert waarschuwingen als reactie op verschillende voor waarden. Hier volgen de meest voorkomende typen waarschuwings voorwaarden:

* **Hardwareproblemen** : deze waarschuwingen geven u informatie over de status van uw hardware. Ze laten u weten of firmware-upgrades nodig zijn, als een netwerk interface problemen heeft of als er een probleem is met een van uw gegevens stations.
* **Connectiviteits problemen** : deze waarschuwingen treden op wanneer er problemen zijn bij het overbrengen van gegevens. Er kunnen communicatie problemen optreden tijdens de overdracht van gegevens naar en van het Azure-opslag account of vanwege een gebrek aan connectiviteit tussen de apparaten en de StorSimple-Apparaatbeheer service. Communicatie problemen zijn moeilijk te verhelpen, omdat er zoveel storings punten zijn. U moet altijd eerst controleren of de netwerk verbinding en Internet toegang beschikbaar zijn voordat u doorgaat met geavanceerdere probleem oplossing. Voor hulp bij het oplossen van problemen gaat u naar [problemen oplossen met de cmdlet Test-Connection](storsimple-8000-troubleshoot-deployment.md).
* **Prestatie problemen** : deze waarschuwingen worden veroorzaakt wanneer uw systeem niet optimaal presteert, bijvoorbeeld wanneer het een zware belasting is.

Daarnaast ziet u mogelijk waarschuwingen met betrekking tot beveiliging, updates of mislukte taken.

## <a name="alert-severity-levels"></a>Ernst niveaus van waarschuwingen

Waarschuwingen hebben verschillende ernst niveaus, afhankelijk van de impact die de waarschuwings situatie heeft en de nood zaak van een reactie op de waarschuwing. Dit zijn de vijf ernstniveaus:

* **Kritiek** : deze waarschuwing is in reactie op een voor waarde die van invloed is op de geslaagde prestaties van uw systeem. Actie is vereist om ervoor te zorgen dat de StorSimple-service niet wordt onderbroken.
* **Waarschuwing** : dit probleem kan cruciaal worden als het niet is opgelost. U moet de situatie onderzoeken en alle acties uitvoeren die nodig zijn om het probleem op te lossen.
* **Informatie** : deze waarschuwing bevat informatie die nuttig kan zijn bij het volgen en beheren van uw systeem.

## <a name="configure-alert-settings"></a>Waarschuwingsinstellingen configureren

U kunt kiezen of u per e-mail wilt worden geïnformeerd over waarschuwings voorwaarden voor elk van uw StorSimple-apparaten. Daarnaast kunt u andere ontvangers van waarschuwings meldingen identificeren door hun e-mail adressen in te voeren in het vak **andere ontvangers van e-mail** , gescheiden door punt komma's.

> [!NOTE]
> U kunt Maxi maal 20 e-mail adressen per apparaat invoeren.

Nadat u e-mail meldingen voor een apparaat hebt ingeschakeld, ontvangen leden van de lijst met meldingen een e-mail bericht telkens wanneer een kritieke waarschuwing wordt weer gegeven. De berichten worden verzonden via *storsimple-Alerts- \@ mail.windowsazure.com* en de waarschuwings voorwaarde wordt beschreven. Ontvangers kunnen klikken op **Afmelden** om zichzelf te verwijderen uit de lijst met e-mail meldingen.

#### <a name="to-enable-email-notification-of-alerts-for-a-device"></a>E-mail meldingen van waarschuwingen voor een apparaat inschakelen
1. Ga naar uw StorSimple-apparaatbeheerservice. Selecteer in de lijst met apparaten en klik op het apparaat dat u wilt configureren.
2. Ga naar **instellingen**  >  **Algemeen** voor het apparaat.

   ![Blade waarschuwingen](./media/storsimple-8000-manage-alerts/configure-alerts-email2.png)
   
2. Ga op de Blade **algemene instellingen** naar **instellingen voor waarschuwingen** en stel het volgende in:
   
   1. Selecteer **Ja** in het veld **e-mail melding verzenden** .
   2. Selecteer in het veld **e-mail Service Administrators** de optie **Ja** om de service beheerder en alle mede beheerders de waarschuwings meldingen te laten ontvangen.
   3. Voer in het veld **andere e-mail ontvangers** de e-mail adressen in van alle andere ontvangers die de meldingen moeten ontvangen. Voer de namen in de indeling *iemand \@ somewhere.com* in. Gebruik punt komma's om de e-mail adressen van elkaar te scheiden. U kunt Maxi maal 20 e-mail adressen per apparaat configureren. 
      
3. Als u een melding voor een test bericht wilt verzenden, klikt u op **test bericht verzenden**. In de StorSimple-Apparaatbeheer service worden status berichten weer gegeven wanneer de test melding wordt doorgestuurd.

    ![Waarschuwings instellingen](./media/storsimple-8000-manage-alerts/configure-alerts-email3.png)

4. U ziet een melding wanneer de test-e-mail wordt verzonden. 
   
    ![E-mail meldingen test melding verzonden](./media/storsimple-8000-manage-alerts/configure-alerts-email4.png)
   
   > [!NOTE]
   > Als het bericht van de test melding niet kan worden verzonden, wordt in de StorSimple-Apparaatbeheer-service een geschikt fout bericht weer gegeven. Wacht een paar minuten en probeer het bericht over het testen opnieuw te verzenden. 

5. Wanneer u de configuratie hebt voltooid, klikt u op **Opslaan**. Klik op **Ja** als u om bevestiging wordt gevraagd.

     ![E-mail berichten test melding verzonden 2](./media/storsimple-8000-manage-alerts/configure-alerts-email5.png)

## <a name="view-and-track-alerts"></a>Waarschuwingen weergeven en bijhouden

De Blade StorSimple Apparaatbeheer service overzicht bevat een kort overzicht van het aantal waarschuwingen op uw apparaten, gerangschikt op Ernst niveau.

![Waarschuwingen dashboard](./media/storsimple-8000-manage-alerts/device-summary4.png)

Als u op het niveau Ernst klikt, wordt de Blade **waarschuwingen** geopend. De resultaten omvatten alleen de waarschuwingen die overeenkomen met het Ernst niveau.

Als u op een waarschuwing in de lijst klikt, krijgt u extra informatie over de waarschuwing, waaronder de laatste keer dat de waarschuwing is gerapporteerd, het aantal instanties van de waarschuwing op het apparaat en de aanbevolen actie om de waarschuwing op te lossen. Als het een hardware-waarschuwing is, wordt het hardware-onderdeel ook geïdentificeerd.

![Voor beeld van hardware-waarschuwing](./media/storsimple-8000-manage-alerts/configure-alerts-email14.png)

U kunt de waarschuwings Details naar een tekst bestand kopiëren als u de informatie naar Microsoft Ondersteuning moet verzenden. Nadat u de aanbeveling hebt gevolgd en de waarschuwings voorwaarde on-premises hebt opgelost, moet u de waarschuwing van het apparaat wissen door de waarschuwing te selecteren op de Blade **waarschuwingen** en op **wissen** te klikken. Als u meerdere waarschuwingen wilt wissen, selecteert u elke waarschuwing, klikt u op een wille keurige kolom, behalve de kolom **waarschuwing** , en klikt u vervolgens op **wissen** nadat u alle waarschuwingen hebt geselecteerd die moeten worden gewist. Houd er rekening mee dat sommige waarschuwingen automatisch worden gewist wanneer het probleem is opgelost of wanneer de waarschuwing door het systeem wordt bijgewerkt met nieuwe gegevens.

Wanneer u op **wissen** klikt, kunt u opmerkingen over de waarschuwing geven en de stappen die u hebt genomen om het probleem op te lossen. Sommige gebeurtenissen worden door het systeem gewist als een andere gebeurtenis wordt geactiveerd met nieuwe gegevens. In dat geval wordt het volgende bericht weer gegeven.

![Waarschuwings bericht wissen](./media/storsimple-manage-alerts/admin_alerts_system_clear.png)

## <a name="sort-and-review-alerts"></a>Waarschuwingen sorteren en controleren

Mogelijk vindt u het efficiënter om rapporten uit te voeren op waarschuwingen zodat u ze in groepen kunt controleren en wissen. Daarnaast kunnen de Blade **waarschuwingen** maxi maal 250 waarschuwingen weer geven. Als u het aantal waarschuwingen hebt overschreden, worden niet alle waarschuwingen weer gegeven in de standaard weergave. U kunt de volgende velden combi neren om aan te passen welke waarschuwingen worden weer gegeven:

* **Status** : u kunt **actieve** of **gewiste** waarschuwingen weer geven. Actieve waarschuwingen worden nog steeds op uw systeem geactiveerd, terwijl gewiste waarschuwingen hand matig zijn gewist door een beheerder of programmatisch zijn gewist omdat het systeem de waarschuwings voorwaarde heeft bijgewerkt met nieuwe gegevens.
* **Ernst** : u kunt waarschuwingen weer geven van alle ernst niveaus (kritiek, waarschuwing, informatie) of alleen een bepaalde ernst, zoals alleen kritieke waarschuwingen.
* **Bron** : u kunt waarschuwingen van alle bronnen weer geven of de waarschuwingen beperken tot de meldingen die afkomstig zijn van de service of van een of meer van de apparaten.
* **Tijds bereik** : door de **begin** -en eind datum en tijds tempels op **te** geven, kunt u waarschuwingen bekijken tijdens de periode waarin u bent geïnteresseerd.

![Lijst met waarschuwingen](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="alerts-quick-reference"></a>Naslag informatie voor waarschuwingen

De volgende tabellen geven een lijst van enkele van de Microsoft Azure StorSimple waarschuwingen die u kunt tegen komen, evenals aanvullende informatie en aanbevelingen waar beschikbaar. Waarschuwingen voor StorSimple-apparaten vallen in een van de volgende categorieën:

* [Connectiviteits waarschuwingen voor de Cloud](#cloud-connectivity-alerts)
* [Cluster waarschuwingen](#cluster-alerts)
* [Waarschuwingen voor herstel na nood gevallen](#disaster-recovery-alerts)
* [Waarschuwingen voor hardware](#hardware-alerts)
* [Taak fout meldingen](#job-failure-alerts)
* [Waarschuwingen lokaal vastgemaakt volume](#locally-pinned-volume-alerts)
* [Netwerk waarschuwingen](#networking-alerts)
* [Prestatie waarschuwingen](#performance-alerts)
* [Beveiligingswaarschuwingen](#security-alerts)
* [Ondersteunings pakket waarschuwingen](#support-package-alerts)
* [Meldingen van behuizing-omgeving](#enclosure-environment-alerts)

### <a name="cloud-connectivity-alerts"></a>Connectiviteits waarschuwingen voor de Cloud

| Waarschuwings tekst | Gebeurtenis | Meer informatie/aanbevolen acties |
|:--- |:--- |:--- |
| Er kan geen verbinding worden gemaakt met <naam van de *Cloud referentie*>. |Kan geen verbinding maken met het opslag account. |Het lijkt erop dat er een probleem is met de connectiviteit van uw apparaat. Voer de `Test-HcsmConnection` cmdlet uit vanuit de Windows Power shell-interface voor StorSimple op uw apparaat om het probleem te identificeren en op te lossen. Als de instellingen juist zijn, is het probleem mogelijk te maken met de referenties van het opslag account waarvoor de waarschuwing is gegenereerd. In dit geval gebruikt u de `Test-HcsStorageAccountCredential` cmdlet om te bepalen of er problemen zijn die u kunt oplossen.<ul><li>Controleer de netwerk instellingen.</li><li>Controleer de referenties van uw opslag account.</li></ul> |
| Er is geen heartbeat van uw apparaat ontvangen voor het laatste <*nummer*> minuten. |Kan geen verbinding maken met het apparaat. |Het lijkt erop dat er een probleem is met de connectiviteit met uw apparaat. Gebruik de `Test-HcsmConnection` cmdlet uit de Windows Power shell-interface voor StorSimple op uw apparaat om het probleem te identificeren en op te lossen of neem contact op met de netwerk beheerder. |

### <a name="storsimple-behavior-when-cloud-connectivity-fails"></a>StorSimple-gedrag wanneer de verbinding met de Cloud mislukt

Wat gebeurt er als de Cloud connectiviteit mislukt voor mijn StorSimple-apparaat dat wordt uitgevoerd in de productie omgeving?

Als de verbinding met de Cloud op uw StorSimple-productie apparaat mislukt, kunnen de volgende problemen optreden, afhankelijk van de status van uw apparaat:

* **Voor de lokale gegevens op uw apparaat**: voor enige tijd is er geen onderbreking en worden Lees bewerkingen nog steeds uitgevoerd. Omdat het aantal uitstaand IOs toeneemt en de limiet overschrijdt, kan de Lees bewerking echter worden gestart.

    Afhankelijk van de hoeveelheid gegevens op uw apparaat, blijft de schrijf bewerkingen ook gedurende de eerste paar uur na de onderbreking van de verbinding met de Cloud plaatsvinden. De schrijf bewerkingen worden vervolgens langzamer en uiteindelijk mislukken als de verbinding van de Cloud gedurende enkele uren wordt onderbroken. (Er is een tijdelijke opslag op het apparaat voor gegevens die naar de Cloud moeten worden gepusht. Dit gebied wordt leeg gemaakt wanneer de gegevens worden verzonden. Als de verbinding mislukt, worden de gegevens in dit opslag gebied niet naar de Cloud gepusht en mislukt de IO.)
* **Voor de gegevens in de Cloud**: voor de meeste connectiviteits fouten in de Cloud wordt een fout geretourneerd. Zodra de verbinding is hersteld, wordt de IOs hervat zonder dat de gebruiker het volume online kan brengen. In zeldzame gevallen kan het nodig zijn om het volume online terug te brengen vanaf de Azure Portal.
* **Voor de uitvoering van Cloud momentopnamen**: de bewerking wordt binnen 4-5 uur opnieuw uitgevoerd. als de verbinding niet wordt hersteld, mislukken de moment opnamen van de Cloud.

### <a name="cluster-alerts"></a>Cluster waarschuwingen

| Waarschuwings tekst | Gebeurtenis | Meer informatie/aanbevolen acties |
|:--- |:--- |:--- |
| Er is een failover voor het apparaat uitgevoerd naar <*apparaatnaam*>. |Het apparaat bevindt zich in de onderhouds modus. |Er is een failover voor het apparaat uitgevoerd vanwege het invoeren of afsluiten van de onderhouds modus. Dit is normaal en er is geen actie vereist. Nadat u deze waarschuwing hebt bevestigd, wist u deze van de pagina waarschuwingen. |
| Er is een failover voor het apparaat uitgevoerd naar <*apparaatnaam*>. |De firmware of software van het apparaat is zojuist bijgewerkt. |Er is een failover van het cluster opgetreden vanwege een update. Dit is normaal en er is geen actie vereist. Nadat u deze waarschuwing hebt bevestigd, wist u deze van de pagina waarschuwingen. |
| Er is een failover voor het apparaat uitgevoerd naar <*apparaatnaam*>. |De controller is afgesloten of opnieuw opgestart. |Er is een failover uitgevoerd voor het apparaat omdat de actieve controller is afgesloten of opnieuw is opgestart door een beheerder. Er is geen actie nodig. Nadat u deze waarschuwing hebt bevestigd, wist u deze van de pagina waarschuwingen. |
| Er is een failover voor het apparaat uitgevoerd naar <*apparaatnaam*>. |Geplande failover. |Controleer of dit een geplande failover is. Nadat u de juiste actie hebt ondernomen, wist u deze waarschuwing van de pagina waarschuwingen. |
| Er is een failover voor het apparaat uitgevoerd naar <*apparaatnaam*>. |Niet-geplande failover. |StorSimple is gebouwd om automatisch te herstellen van ongeplande failovers. Als er een groot aantal waarschuwingen wordt weer geven, neemt u contact op met Microsoft Ondersteuning. |
| Er is een failover voor het apparaat uitgevoerd naar <*apparaatnaam*>. |Andere/onbekende oorzaak. |Als er een groot aantal waarschuwingen wordt weer geven, neemt u contact op met Microsoft Ondersteuning. Nadat het probleem is opgelost, wist u deze waarschuwing van de pagina waarschuwingen. |
| Een kritieke status van de service rapporten van apparaten als mislukt. |DataPath-service fout. |Neem contact op met Microsoft Ondersteuning voor hulp. |
| Het virtuele IP-adres voor de netwerk interface <*gegevens #*> de status van het rapport is mislukt. |Andere/onbekende oorzaak. |Soms kunnen tijdelijke voor waarden deze waarschuwingen veroorzaken. Als dit het geval is, wordt deze waarschuwing na enige tijd automatisch gewist. Als het probleem zich blijft voordoen, neemt u contact op met Microsoft Ondersteuning. |
| Het virtuele IP-adres voor de netwerk interface <*gegevens #*> de status van het rapport is mislukt. |Interface naam: <*gegevens #*> IP-adres `<IP address>` kan niet online worden gebracht omdat er een dubbel IP-adres op het netwerk is gedetecteerd. |Zorg ervoor dat het dubbele IP-adres is verwijderd van het netwerk of configureer de interface opnieuw met een ander IP-adres. |

### <a name="disaster-recovery-alerts"></a>Waarschuwingen voor herstel na nood gevallen

| Waarschuwings tekst | Gebeurtenis | Meer informatie/aanbevolen acties |
|:--- |:--- |:--- |
| De herstel bewerkingen kunnen niet alle instellingen voor deze service herstellen. De configuratie gegevens van het apparaat hebben een inconsistente status voor sommige apparaten. |Er is een inconsistentie gedetecteerd in de gegevens na een nood herstel. |Versleutelde gegevens op de service zijn niet gesynchroniseerd met die op het apparaat. Autoriseer het apparaat <*apparaatnaam*> van StorSimple Apparaatbeheer om het synchronisatie proces te starten. Gebruik de Windows Power shell-interface voor StorSimple om de `Restore-HcsmEncryptedServiceData` -cmdlet op apparaat <*apparaatnaam*> uit te voeren, waarbij u het oude wacht woord opgeeft als invoer voor deze cmdlet om het beveiligings profiel te herstellen. Voer vervolgens de `Invoke-HcsmServiceDataEncryptionKeyChange` cmdlet uit om de versleutelings sleutel voor de service gegevens bij te werken. Nadat u de juiste actie hebt ondernomen, wist u deze waarschuwing van de pagina waarschuwingen. |

### <a name="hardware-alerts"></a>Waarschuwingen voor hardware

| Waarschuwings tekst | Gebeurtenis | Meer informatie/aanbevolen acties |
|:--- |:--- |:--- |
| Het hardware-onderdeel <*onderdeel-ID*> status rapporteren als <*status*>. | |Soms kunnen tijdelijke voor waarden deze waarschuwingen veroorzaken. Als dit het geval is, wordt deze waarschuwing na enige tijd automatisch gewist. Als het probleem zich blijft voordoen, neemt u contact op met Microsoft Ondersteuning. |
| De passieve controller functioneert niet goed. |De passieve controller (secundair) werkt niet. |Het apparaat is operationeel, maar een van uw controllers functioneert niet goed. Probeer die controller opnieuw op te starten. Als het probleem niet is opgelost, neemt u contact op met Microsoft Ondersteuning. |

### <a name="job-failure-alerts"></a>Taak fout meldingen

| Waarschuwings tekst | Gebeurtenis | Meer informatie/aanbevolen acties |
|:--- |:--- |:--- |
| Het maken van een back-up van de <*-bron volume groep-ID*> is mislukt |De back-uptaak is mislukt. |De back-upbewerking kan niet worden voltooid vanwege verbindings problemen. Als er geen verbindings problemen zijn, hebt u mogelijk het maximum aantal back-ups bereikt. Verwijder alle back-ups die niet meer nodig zijn en voer de bewerking opnieuw uit. Nadat u de juiste actie hebt ondernomen, wist u deze waarschuwing van de pagina waarschuwingen. |
| De kloon van de <*bron-id van het back-upelement*> naar <*serie nummers van het doel volume*> mislukt. |De kloon taak is mislukt. |Vernieuw de lijst met back-ups om te controleren of de back-up nog geldig is. Als de back-up geldig is, is het mogelijk dat er problemen met de verbinding met de Cloud optreden waardoor de kloon bewerking niet kan worden voltooid. Als er geen verbindings problemen zijn, hebt u mogelijk de opslag limiet bereikt. Verwijder alle back-ups die niet meer nodig zijn en voer de bewerking opnieuw uit. Nadat u de juiste actie hebt ondernomen om het probleem op te lossen, wist u deze waarschuwing van de pagina waarschuwingen. |
| Herstellen van <*bron-id van back-upelement*> is mislukt. |De herstel taak is mislukt. |Vernieuw de lijst met back-ups om te controleren of de back-up nog geldig is. Als de back-up geldig is, is het mogelijk dat er problemen met de verbinding met de Cloud zijn waardoor de herstel bewerking niet kan worden voltooid. Als er geen verbindings problemen zijn, hebt u mogelijk de opslag limiet bereikt. Verwijder alle back-ups die niet meer nodig zijn en voer de bewerking opnieuw uit. Nadat u de juiste actie hebt ondernomen om het probleem op te lossen, wist u deze waarschuwing van de pagina waarschuwingen. |

### <a name="locally-pinned-volume-alerts"></a>Waarschuwingen lokaal vastgemaakt volume

| Waarschuwings tekst | Gebeurtenis | Meer informatie/aanbevolen acties |
|:--- |:--- |:--- |
| Het maken van het lokale volume <de *volume naam*> is mislukt. |De taak voor het maken van een volume is mislukt. <*Fout bericht dat overeenkomt met de mislukte fout code*>. |Connectiviteits problemen kunnen ertoe leiden dat de bewerking voor het maken van de ruimte niet kan worden voltooid. Lokaal vastgemaakte volumes zijn dik ingericht en het proces voor het maken van ruimte omvat het overlopen van gelaagde volumes naar de Cloud. Als er geen verbindings problemen zijn, hebt u mogelijk de lokale ruimte op het apparaat uitgeput. Bepaal of er ruimte op het apparaat aanwezig is voordat u deze bewerking opnieuw uitvoert. |
| Uitbrei ding van lokale volume <*volume naam*> mislukt. |De volume wijzigings taak is mislukt vanwege <*fout bericht dat overeenkomt met de mislukte fout code*>. |Vanwege verbindings problemen kan worden voor komen dat de volume-uitbreidings bewerking is voltooid. Lokaal vastgemaakte volumes zijn dik ingericht en het proces van het uitbreiden van de bestaande ruimte omvat het overlopen van gelaagde volumes naar de Cloud. Als er geen verbindings problemen zijn, hebt u mogelijk de lokale ruimte op het apparaat uitgeput. Bepaal of er ruimte op het apparaat aanwezig is voordat u deze bewerking opnieuw uitvoert. |
| Het converteren van de volume *naam* van het volume <> is mislukt. |De taak volume conversie voor het converteren van het volume type van lokaal vastgemaakt naar gelaagd is mislukt. |De conversie van het volume van het type lokaal vastgemaakt naar een laag kan niet worden voltooid. Zorg ervoor dat er geen verbindings problemen zijn waardoor de bewerking niet kan worden voltooid. Ga voor het oplossen van verbindings problemen naar [problemen oplossen met de cmdlet Test-HcsmConnection](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>Het oorspronkelijke lokale vastgemaakte volume is nu gemarkeerd als een gelaagd volume omdat sommige gegevens van het lokaal vastgemaakte volume tijdens de conversie naar de Cloud zijn overgelopen. Het resulterende gelaagde volume neemt nog steeds lokale ruimte op het apparaat in beslag dat niet kan worden vrijgemaakt voor toekomstige lokale volumes.<br>Los eventuele verbindings problemen op, Wis de waarschuwing en converteer dit volume terug naar het oorspronkelijke lokaal vastgemaakte volume type om ervoor te zorgen dat alle gegevens lokaal beschikbaar worden gemaakt. |
| Het converteren van de volume *naam* van het volume <> is mislukt. |De taak voor volume conversie waarmee het volume type wordt geconverteerd van gelaagd naar lokaal vastgemaakt, is mislukt. |De conversie van het volume van een gelaagd type naar lokaal vastgemaakt kan niet worden voltooid. Zorg ervoor dat er geen verbindings problemen zijn waardoor de bewerking niet kan worden voltooid. Ga voor het oplossen van verbindings problemen naar [problemen oplossen met de cmdlet Test-HcsmConnection](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>Het oorspronkelijke gelaagde volume dat nu als onderdeel van het conversie proces is gemarkeerd als een lokaal vastgemaakt volume, blijft gegevens in de Cloud, terwijl de brede ingerichte ruimte op het apparaat voor dit volume niet meer kan worden vrijgemaakt voor toekomstige lokale volumes.<br>Los eventuele verbindings problemen op, Wis de waarschuwing en converteer dit volume terug naar het oorspronkelijke gelaagde volume type om ervoor te zorgen dat de lokale ruimte die op het apparaat wordt ingericht, dik kan worden vrijgemaakt. |
| Het gebruik van lokale ruimte in de buurt van de lokale moment opnamen van <naam van de *volume groep*> |Lokale moment opnamen voor het back-upbeleid kunnen binnenkort bijna geen ruimte meer in beslag nemen en kunnen niet worden gevalideerd om het schrijven van hosts te voor komen. |Frequente lokale moment opnamen naast een hoog gegevens verloop in de volumes die zijn gekoppeld aan deze back-upbeleids groep, zorgen ervoor dat de lokale ruimte op het apparaat snel wordt verbruikt. Verwijder alle lokale moment opnamen die u niet meer nodig hebt. Werk ook de lokale planningen voor moment opnamen voor dit back-upbeleid bij om minder frequente lokale moment opnamen te maken, en zorg ervoor dat er regel matig Cloud momentopnamen worden gemaakt. Als deze acties niet worden uitgevoerd, worden de lokale ruimte voor deze moment opnamen mogelijk binnenkort uitgeput en worden ze automatisch verwijderd om ervoor te zorgen dat de schrijf bewerkingen van de host nog steeds worden uitgevoerd. |
| Lokale moment opnamen voor de <naam van de *volume groep*> zijn ongeldig gemaakt. |De lokale moment opnamen voor <*naam van de volume groep*> ongeldig en vervolgens verwijderd omdat ze de lokale ruimte op het apparaat overschrijden. |Om ervoor te zorgen dat dit niet in de toekomst wordt herhaald, bekijkt u de lokale momentopname schema's voor dit back-upbeleid en verwijdert u de lokale moment opnamen die u niet meer nodig hebt. Veelvuldige lokale moment opnamen naast een hoog gegevens verloop in de volumes die zijn gekoppeld aan deze back-upbeleids groep, kunnen ertoe leiden dat lokale ruimte op het apparaat snel wordt verbruikt. |
| Herstellen van <*bron-id van back-upelement*> is mislukt. |De herstel taak is mislukt. |Als u lokaal vastgemaakte of een combi natie van lokaal vastgemaakte en gelaagde volumes in dit back-upbeleid hebt, moet u de back-uplijst vernieuwen om te controleren of de back-up nog geldig is. Als de back-up geldig is, is het mogelijk dat er problemen met de verbinding met de Cloud zijn waardoor de herstel bewerking niet kan worden voltooid. De lokaal vastgemaakte volumes die als onderdeel van deze momentopname groep zijn hersteld, hebben niet alle gegevens gedownload naar het apparaat. Als u een combi natie van gelaagde en lokaal vastgemaakte volumes in deze momentopname groep hebt, worden deze niet met elkaar gesynchroniseerd. Als u de herstel bewerking wilt volt ooien, neemt u de volumes in deze groep offline op de host en voert u de herstel bewerking opnieuw uit. Houd er rekening mee dat wijzigingen in de volume gegevens die tijdens het herstel proces werden uitgevoerd, verloren gaan. |

### <a name="networking-alerts"></a>Netwerk waarschuwingen

| Waarschuwings tekst | Gebeurtenis | Meer informatie/aanbevolen acties |
|:--- |:--- |:--- |
| Kan de StorSimple-service (s) niet starten. |DataPath-fout |Als het probleem zich blijft voordoen, neemt u contact op met Microsoft Ondersteuning. |
| Er is een dubbel IP-adres gedetecteerd voor ' Data0 '. | |Het systeem heeft een conflict gevonden voor het IP-adres 10.0.0.1. De netwerk bron ' Data0 ' op het apparaat *\<device1>* is offline. Zorg ervoor dat dit IP-adres niet wordt gebruikt door een andere entiteit in dit netwerk. Als u netwerk problemen wilt oplossen, gaat u naar [problemen oplossen met de cmdlet Get-NetAdapter](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). Neem contact op met uw netwerk beheerder om dit probleem op te lossen. Als het probleem zich blijft voordoen, neemt u contact op met Microsoft Ondersteuning. |
| Het IPv4-of IPv6-adres voor Data0 is offline. | |De netwerk resource Data0 met het IP-adres 10.0.0.1. de lengte van het voor voegsel ' 22 ' op het apparaat *\<device1>* is offline. Zorg ervoor dat de switch poorten waarmee deze interface is verbonden, operationeel zijn. Als u netwerk problemen wilt oplossen, gaat u naar [problemen oplossen met de cmdlet Get-NetAdapter](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). |
| Kan geen verbinding maken met de verificatie service. |DataPath-fout |De URLthat wordt gebruikt om te verifiëren is niet bereikbaar. Zorg ervoor dat de firewall regels de URL-patronen bevatten die zijn opgegeven voor het StorSimple-apparaat. Ga naar https:/aka.ms/ss-8000-network-reqs voor meer informatie over URL-patronen in Azure Portal \/ . Als u Azure Government Cloud gebruikt, gaat u naar de URL-patronen in https: \/ /aka.MS/ss8000-gov-Network-reqs.|

### <a name="performance-alerts"></a>Prestatie waarschuwingen

| Waarschuwings tekst | Gebeurtenis | Meer informatie/aanbevolen acties |
|:--- |:--- |:--- |
| De belasting van het apparaat heeft <*drempel waarde*> overschreden. |Trager dan de verwachte reactie tijden. |Uw apparaat rapporteert gebruik onder een zware invoer/uitvoer belasting. Dit kan ervoor zorgen dat uw apparaat niet werkt en het zou moeten doen. Bekijk de werk belastingen die u hebt gekoppeld aan het apparaat en bepaal of er een van de workloads kan worden verplaatst naar een ander apparaat of dat ze niet meer nodig zijn.|
| Kan de StorSimple-service (s) niet starten. |DataPath-fout |Als het probleem zich blijft voordoen, neemt u contact op met Microsoft Ondersteuning. |

### <a name="security-alerts"></a>Beveiligingswaarschuwingen

| Waarschuwings tekst | Gebeurtenis | Meer informatie/aanbevolen acties |
|:--- |:--- |:--- |
| Microsoft Ondersteuning sessie is gestart. |Ondersteunings sessie van derden. |Controleer of deze toegang is geautoriseerd. Nadat u de juiste actie hebt ondernomen, wist u deze waarschuwing van de pagina waarschuwingen. |
| Het wacht woord voor <*element*> verloopt over <*tijds duur*>. |Het verlopen van het wacht woord is bijna bereikt. |Wijzig uw wacht woord voordat dit verloopt. |
| Er ontbreken gegevens over de beveiligings configuratie voor de <*element-ID*>. | |De volumes die aan deze volume container zijn gekoppeld, kunnen niet worden gebruikt om uw StorSimple-configuratie te repliceren. Om ervoor te zorgen dat uw gegevens veilig worden opgeslagen, raden we u aan om de volume container en de volumes die aan de volume container zijn gekoppeld, te verwijderen. Nadat u de juiste actie hebt ondernomen, wist u deze waarschuwing van de pagina waarschuwingen. |
| <*aantal*> aanmeldings pogingen mislukt voor <*element-id*>. |Meerdere mislukte aanmeldings pogingen. |Uw apparaat wordt mogelijk aangevallen of een gemachtigde gebruiker probeert verbinding te maken met een onjuist wacht woord.<ul><li>Neem contact op met uw gemachtigde gebruikers en controleer of deze pogingen van een legitieme bron zijn. Als u een groot aantal mislukte aanmeldings pogingen blijft zien, kunt u overwegen om extern beheer uit te scha kelen en contact op te nemen met de netwerk beheerder. Nadat u de juiste actie hebt ondernomen, wist u deze waarschuwing van de pagina waarschuwingen.</li><li>Controleer of uw Snapshot Manager exemplaren zijn geconfigureerd met het juiste wacht woord. Nadat u de juiste actie hebt ondernomen, wist u deze waarschuwing van de pagina waarschuwingen.</li></ul>Ga voor meer informatie naar [een verlopen apparaatwachtwoord wijzigen](storsimple-snapshot-manager-manage-devices.md#change-an-expired-device-password). |
| Er zijn een of meer fouten opgetreden tijdens het wijzigen van de versleutelings sleutel voor de service gegevens. | |Er zijn fouten opgetreden tijdens het wijzigen van de versleutelings sleutel voor de service gegevens. Nadat u de fout voorwaarden hebt opgelost, voert u de `Invoke-HcsmServiceDataEncryptionKeyChange` cmdlet uit vanuit de Windows Power shell-interface voor StorSimple op uw apparaat om de service bij te werken. Neem contact op met micro soft ondersteuning als dit probleem zich blijft voordoen. Nadat u het probleem hebt opgelost, wist u deze waarschuwing van de pagina waarschuwingen. |

### <a name="support-package-alerts"></a>Ondersteunings pakket waarschuwingen

| Waarschuwings tekst | Gebeurtenis | Meer informatie/aanbevolen acties |
|:--- |:--- |:--- |
| Het maken van het ondersteunings pakket is mislukt. |StorSimple kan het pakket niet genereren. |Probeer deze bewerking opnieuw uit te voeren. Als het probleem zich blijft voordoen, neemt u contact op met Microsoft Ondersteuning. Nadat u het probleem hebt opgelost, wist u deze waarschuwing van de pagina waarschuwingen. |

### <a name="enclosure-environment-alerts"></a>Meldingen van behuizing-omgeving

| Waarschuwings tekst | Gebeurtenis | Meer informatie/aanbevolen acties |
|:--- |:--- |:--- |
| Status van omgevings temperatuur sensor van hardwareonderdeel wordt niet gerapporteerd als mislukt.  | Type behuizing: hoofd behuizing | Deze waarschuwing wordt geactiveerd wanneer de omgeving buiten de Tempe ratuur rond StorSimple ligt boven een acceptabel bereik. Controleer of de omgeving buiten de Tempe ratuur of de lucht stroom is van de AC-ventilator in het Data Center. Wanneer de Tempe ratuur op normaal wordt ingesteld, wordt de waarschuwing automatisch gewist nadat de tijd is verstreken. Als het probleem zich blijft voordoen, neemt u contact op met Microsoft-ondersteuning.   |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [StorSimple-fouten en het oplossen van problemen met de implementatie van apparaten](storsimple-8000-troubleshoot-deployment.md).
