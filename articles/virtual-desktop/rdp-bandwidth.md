---
title: Remote Desktop Protocol bandbreedte vereisten Windows virtueel bureau blad-Azure
titleSuffix: Azure
description: Informatie over RDP-bandbreedte vereisten voor virtuele Windows-Bureau bladen.
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: c9ef72241ac62efd8555de59bb52949321364035
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96325176"
---
# <a name="remote-desktop-protocol-rdp-bandwidth-requirements"></a>Bandbreedte vereisten voor Remote Desktop Protocol (RDP)

Remote Desktop Protocol (RDP) is een geavanceerde technologie die gebruikmaakt van verschillende technieken om de levering van de externe grafische server naar het client apparaat te perfectioneren. Afhankelijk van de use-case, Beschik baarheid van computer bronnen en netwerk bandbreedte, worden door RDP verschillende para meters voor de beste gebruikers ervaring dynamisch aangepast.

Remote Desktop Protocol multiplext meerdere dynamische virtuele kanalen (DVCs) in één gegevens kanaal dat via verschillende netwerk transporten wordt verzonden. Er zijn afzonderlijke DVCs voor externe afbeeldingen, invoer, apparaatomleiding, afdrukken en andere. WVD-partners kunnen ook hun uitbrei dingen implementeren die gebruikmaken van DVC-interfaces.
De hoeveelheid gegevens die via RDP wordt verzonden, is afhankelijk van de gebruikers activiteit. Een gebruiker kan bijvoorbeeld werken met eenvoudige tekstuele inhoud voor de meeste sessie en minimale band breedte verbruiken, maar vervolgens een afdruk van een 200-pagina document op de lokale printer genereren. Deze afdruk taak maakt gebruik van een aanzienlijke hoeveelheid netwerk bandbreedte.

Wanneer u een externe sessie gebruikt, is de beschik bare band breedte van uw netwerk aanzienlijk van invloed op de kwaliteit van uw ervaring. Voor verschillende toepassingen en weergave resoluties zijn verschillende netwerk configuraties vereist. Daarom is het essentieel om ervoor te zorgen dat uw netwerk configuratie aan uw behoeften voldoet.

## <a name="estimating-bandwidth-utilization"></a>Het bandbreedte gebruik schatten

RDP gebruikt verschillende compressie algoritmen voor verschillende soorten gegevens. De onderstaande tabel helpt bij het schatten van de gegevens overdrachten:

| Gegevenstype | Richting | Schatting |
|---|---|---|
| Externe grafische | Sessie-host aan client | [Raadpleeg de gedetailleerde richt lijnen](#estimating-bandwidth-used-by-remote-graphics) |
| Heartbeats | Beide richtingen | ~ 20 bytes elke 5 seconden  |
| Invoer | Client naar sessie-host | Hoeveelheid gegevens is gebaseerd op de gebruikers activiteit, kleiner dan 100 bytes voor de meeste bewerkingen  |
| Bestands overdrachten | Beide richtingen | Bestands overdrachten maken gebruik van bulk compressie. Zip-compressie gebruiken voor benadering |
| Afdrukken | Sessie-host aan client | De overdracht van afdruk taken is afhankelijk van het stuur programma en het gebruik van bulk compressie. zip-compressie voor benadering |

Andere scenario's kunnen de bandbreedte vereisten wijzigen, afhankelijk van hoe u ze gebruikt, zoals:

* Telefonische of video vergaderingen
* Real-time communicatie
* Video van 4.000 kB streamen

## <a name="estimating-bandwidth-used-by-remote-graphics"></a>De band breedte die wordt gebruikt door externe afbeeldingen schatten

Het is moeilijk om bandbreedte gebruik door het externe bureau blad te voors pellen. De gebruikers activiteiten genereren het meren deel van het verkeer van extern bureau blad. Elke gebruiker is uniek en verschillen in hun werk patronen kunnen het netwerk gebruik aanzienlijk wijzigen.

De beste manier om de bandbreedte vereisten te begrijpen is door echte gebruikers verbindingen te controleren. Bewaking kan worden uitgevoerd door de ingebouwde prestatie meter items of door de netwerk apparatuur.

In veel gevallen kunt u echter netwerk gebruik ramen door te weten hoe Remote Desktop Protocol werkt en door de werk patronen van uw gebruikers te analyseren.

Het externe protocol levert de afbeeldingen die door de externe server worden gegenereerd om deze weer te geven op een lokale monitor. Meer in het bijzonder biedt het de bureau blad-bitmap volledig samengesteld op de server.
Tijdens het verzenden van een desktop-bitmap lijkt het alsof een eenvoudige taak bij de eerste benadering een aanzienlijke hoeveelheid resources nodig heeft. Een voor beeld: een 1080p-Desktop afbeelding in het ongecomprimeerde formulier is ongeveer 8 MB groot. Voor het weer geven van deze installatie kopie op de lokaal verbonden monitor met een bescheiden scherm vernieuwings frequentie van 30 Hz is een band breedte van ongeveer 237 MB/s vereist.

Om de hoeveelheid gegevens die via het netwerk worden overgedragen, te verminderen, gebruikt RDP de combi natie van meerdere technieken, inclusief, maar niet beperkt tot

* Frame frequentie-optimalisaties
* Inhouds classificatie voor scherm
* Leveranciersspecifieke codecs
* Code ring van progressieve afbeelding
* Caching aan client zijde

Houd rekening met het volgende als u meer wilt weten over externe grafische oplossingen:

* Hoe rijker de grafische oplossing, hoe meer band breedte er nodig is
  * Tekst, gebruikers interface-elementen en effen kleur gebieden gebruiken minder band breedte dan iets anders.
  * Natuurlijke afbeeldingen zijn de belangrijkste mede werkers voor het gebruik van band breedte. Maar caching aan de client zijde helpt bij het verminderen.
* Alleen gewijzigde delen van het scherm worden verzonden. Als er geen zicht bare updates op het scherm zijn, worden er geen updates verzonden.
* Video weergave en andere hoogwaardige inhoud zijn in wezen een diapresentatie met afbeeldingen. RDP gebruikt dynamisch de juiste video-codecs om ze te leveren met de oorspronkelijke frame frequentie. Het is echter nog steeds grafische afbeeldingen en is nog steeds het belangrijkste bijdrager aan het bandbreedte gebruik.
* Niet-actieve tijd in extern bureau blad betekent dat er geen of minimale scherm updates zijn. het gebruik van het netwerk is dus mini maal tijdens niet-actieve tijden.
* Wanneer een extern bureau blad-client venster is geminimaliseerd, worden er geen grafische updates verzonden vanaf de sessiehost.

Houd er wel voor dat de stress die in uw netwerk wordt geplaatst, afhankelijk is van het uitvoer frame en de weergave resolutie van uw app-werk belasting. Als de frame frequentie of de weergave resolutie toeneemt, neemt de bandbreedte vereiste ook toe. Een lichte workload met een weer gave met een hoge resolutie vereist bijvoorbeeld meer beschik bare band breedte dan een lichte workload met een normale of lage resolutie. Voor verschillende weergave resoluties zijn verschillende beschik bare band breedten vereist.

De volgende tabel bevat informatie over het schatten van de gegevens die worden gebruikt door de verschillende grafische scenario's. Deze nummers zijn van toepassing op een configuratie met één monitor met 1920-resolutie en met zowel de standaard grafische modus als de H. 264/AVC 444 grafische modus.

| Scenario | Standaard modus | H. 264/AVC 444-modus | Voorbeeld | Beschrijving van het scenario |
|:---|---:|---:|---|---|
| Niet-actief | 0,3 kbps | 0,3 kbps |:::image type="content" source="media/idle.png" alt-text="Scherm opname van de niet-actieve verbinding":::| De gebruiker heeft het werk onderbroken en er zijn geen updates actief op het scherm |
| Microsoft Word | 100-150 kbps | 200-300 kbps |:::image type="content" source="media/word.gif" alt-text="Animatie van micro soft Word":::| Gebruiker werkt actief samen met micro soft Word, typen, afbeeldingen plakken en scha kelen tussen documenten |
| Microsoft Excel | 150-200 kbps | 400-500 Kbps |:::image type="content" source="media/excel.gif" alt-text="Animatie van micro soft Excel":::| Gebruiker werkt samen met micro soft Excel, meerdere cellen met formules en grafieken tegelijk worden bijgewerkt |
| Microsoft PowerPoint | 4-4,5 Mbps | 1,6-1,8 Mbps |:::image type="content" source="media/powerpoint.gif" alt-text="Animatie van micro soft power point":::| Gebruiker werkt actief samen met micro soft power point, typen, plakken. Gebruiker wijzigt ook geavanceerde grafische elementen en gebruikt overgangs effecten voor dia's |
| Surfen op Internet | 6-6,5 Mbps | 0,9-1 Mbps |:::image type="content" source="media/web.gif" alt-text="Animatie van surfen op Internet":::| De gebruiker werkt actief met een grafisch uitgebreide website met meerdere statische en bewegende installatie kopieën. Gebruiker schuift de pagina's zowel horizon taal als verticaal |
| Galerie met installatie kopieën | 3,3-3,6 Mbps | 0,7-0,8 Mbps |:::image type="content" source="media/image-gallery.gif" alt-text="Animatie van afbeeldings galerie":::| De gebruiker werkt actief met de toepassing afbeeldingen galerie. Bladeren, inzoomen, formaat wijzigen en draaien van afbeeldingen |
| Video afspelen | 8,5-9,5 Mbps | 2,5-2,8 Mbps |:::image type="content" source="media/video.gif" alt-text="Animatie van het afspelen van video":::| De gebruiker kijkt naar een 30 FPS video die 1/2 van het scherm verbruikt |
| Afspelen van video op volledig scherm | 7,5-8,5 Mbps | 2,5-3,1 Mbps |:::image type="content" source="media/fullscreen-video.gif" alt-text="Animatie van het afspelen van video op volledig scherm":::| De gebruiker bekijkt een 30 FPS-video die is gemaximaliseerd tot een volledig scherm |

## <a name="dynamic-bandwidth-allocation"></a>Toewijzing van dynamische band breedte

Remote Desktop Protocol is een modern protocol dat dynamisch kan worden aangepast aan de veranderende netwerk omstandigheden.
In plaats van de vaste limieten voor bandbreedte gebruik te gebruiken, gebruikt RDP voortdurende netwerk detectie die actief de beschik bare netwerk bandbreedte en pakket retour tijd bewaakt. Op basis van de bevindingen selecteert RDP de opties voor grafische code ring en wijst de band breedte voor apparaatomleiding en andere virtuele kanalen toe.  
Met deze technologie kan RDP de volledige netwerk-pipe gebruiken wanneer deze beschikbaar zijn en snel weer worden uitgeschakeld wanneer het netwerk nodig is voor iets anders.
RDP detecteert dat de afbeeldings kwaliteit, de frame frequentie of de compressie algoritmen worden aangepast als andere toepassingen het netwerk aanvragen.

## <a name="limit-network-bandwidth-use-with-throttle-rate"></a>Gebruik van de netwerk bandbreedte beperken met vertragings snelheid

In de meeste gevallen hoeft u het bandbreedte gebruik niet te beperken, omdat beperken de gebruikers ervaring kan beïnvloeden. In de beperkte netwerken wilt u mogelijk het netwerk gebruik beperken. Een ander voor beeld is geleasde netwerken die in rekening worden gebracht voor de hoeveelheid verkeer die wordt gebruikt.

In dergelijke gevallen kunt u een uitgaand netwerk verkeer van RDP beperken door een beperkings snelheid op te geven in het QoS-beleid.

  >[!NOTE]
  > [Zorg ervoor dat RDP-shortpath is ingeschakeld](./shortpath.md) : beperking van de vertraagde snelheid wordt niet ondersteund voor reverse Connect-Trans Port.

### <a name="implement-throttle-rate-limiting-on-session-host-using-group-policy"></a>Beperkings limieten voor de sessie host implementeren met behulp van groepsbeleid

U kunt op beleid gebaseerde Quality of Service (QoS) in groepsbeleid gebruiken om de vooraf gedefinieerde vertragings factor in te stellen.

Als u een QoS-beleid wilt maken voor hosts die lid zijn van een domein, meldt u zich eerst aan bij een computer waarop groepsbeleid-beheer is geïnstalleerd. Open groepsbeleid beheer (Selecteer Start, wijs systeem beheer aan en selecteer vervolgens groepsbeleid beheer) en voer vervolgens de volgende stappen uit:

1. Zoek in groepsbeleid beheer de container waar het nieuwe beleid moet worden gemaakt. Als al uw sessie wordt gehost op computers bevinden zich in een organisatie-eenheid met de naam **sessie-hosts**, moet het nieuwe beleid worden gemaakt in de sessie host van de organisatie-eenheid.

2. Klik met de rechter muisknop op de juiste container en selecteer vervolgens **een groeps beleidsobject in dit domein maken en hier een koppeling**.

3. Typ in het dialoog venster **Nieuw groeps beleidsobject** een naam voor het nieuwe Groepsbeleid-object in het vak **naam** en selecteer vervolgens **OK**.

4. Klik met de rechter muisknop op het zojuist gemaakte beleid en selecteer vervolgens **bewerken**.

5. Vouw in het Groepsbeleidsbeheer-editor **computer configuratie** uit, vouw **Windows-instellingen** uit, klik met de rechter muisknop op **op beleid gebaseerde QoS** en selecteer **Nieuw beleid maken**.

6. In het dialoog venster op **beleid gebaseerde QoS** typt u op de pagina openen een naam voor het nieuwe beleid in het vak **naam** . Selecteer **uitgaande vertragings snelheid opgeven** en stel de vereiste waarde in en selecteer **volgende**.

7. Selecteer op de volgende pagina **alleen toepassingen met deze naam voor het uitvoer bare bestand** en voer de naam **svchost.exe** in en selecteer **volgende**. Met deze instelling geeft u aan dat het beleid alleen prioriteit moet geven aan het overeenkomende verkeer van de Extern bureaublad-service.

8. Controleer op de derde pagina of zowel **een bron-IP-adres** als **een doel-IP-adres** is geselecteerd. Selecteer **Next**. Deze twee instellingen zorgen ervoor dat pakketten worden beheerd, ongeacht welke computer (IP-adres) de pakketten heeft verzonden en welke computer (IP-adres) de pakketten ontvangt.

9. Selecteer op pagina 4 de optie **UDP** in de vervolg keuzelijst **Selecteer het protocol waarop dit QoS-beleid van toepassing is** .

10. Onder de kop **Geef het poort nummer van de bron** op, selecteert u **uit deze bron poort of dit bereik**. Typ **3390** in het bijbehorende tekstvak. Selecteer **Finish**.

Het nieuwe beleid dat u hebt gemaakt, wordt pas van kracht nadat groepsbeleid is vernieuwd op uw Session Host-computers. Hoewel groepsbeleid regel matig vernieuwt, kunt u een onmiddellijke vernieuwing afdwingen door de volgende stappen uit te voeren:

1. Open een opdracht prompt als beheerder (als *administrator uitvoeren*) op elke sessiehost waarvoor u Groepsbeleid wilt vernieuwen.

2. Voer bij de opdracht prompt

   ```console
   gpupdate /force
   ```

### <a name="implement-throttle-rate-limiting-on-session-host-using-powershell"></a>Beperkings limieten voor de sessie host implementeren met behulp van Power shell

U kunt de snelheids factor van RDP-shortpath instellen met de Power shell-cmdlet hieronder:

```powershell
New-NetQosPolicy -Name "RDP Shortpath" -AppPathNameMatchCondition "svchost.exe" -IPProtocolMatchCondition UDP -IPSrcPortStartMatchCondition 3390 -IPSrcPortEndMatchCondition 3390 -DSCPAction 46 -NetworkProfile All
```

## <a name="next-steps"></a>Volgende stappen

* Zie [vereisten voor de band breedte van Remote Desktop Protocol (RDP) voor Windows Virtual Desktop](rdp-bandwidth.md)voor meer informatie over de bandbreedte vereisten voor virtuele Windows-Bureau bladen.
* Zie wat is Windows Virtual Desktop- [netwerk](network-connectivity.md)connectiviteit? voor meer informatie over de Windows-verbinding met virtueel bureau blad-netwerken.
* Als u aan de slag wilt gaan met Quality of Service (QoS) voor virtueel bureau blad van Windows, raadpleegt u [implementing Quality of service (QoS) voor Windows virtueel bureau blad](rdp-quality-of-service-qos.md).
