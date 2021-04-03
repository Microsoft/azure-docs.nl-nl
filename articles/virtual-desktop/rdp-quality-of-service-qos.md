---
title: Quality of Service (QoS) voor virtuele Windows-Desktop (preview-versie) implementeren
titleSuffix: Azure
description: QoS instellen (preview) voor virtueel bureau blad van Windows.
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: b61faf74d96e2571e91f7bf9d10eac88cdbf8345
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94639252"
---
# <a name="implement-quality-of-service-qos-for-windows-virtual-desktop-preview"></a>Quality of Service (QoS) voor virtuele Windows-Desktop (preview-versie) implementeren

> [!IMPORTANT]
> Beleid voor Quality of Service (QoS) voor Windows Virtual Desktop is momenteel beschikbaar als open bare preview.
> Deze preview-versie is beschikbaar zonder service level agreement. het wordt niet aanbevolen om deze te gebruiken voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

[RDP-shortpath](./shortpath.md) biedt directe UDP-trans port tussen extern bureaublad-client en Session Host. Met RDP-shortpath kunt u de beleids regels voor de RDP-gegevens configureren Quality of Service (QoS).
QoS in virtueel bureau blad van Windows biedt realtime RDP-verkeer dat gevoelig is voor vertragingen in het netwerk voor verkeer dat minder gevoelig is. Een voor beeld van een dergelijk kleiner verkeer zou een nieuwe app kunnen downloaden, waarbij een extra seconde om te worden gedownload, geen grote deal is. QoS maakt gebruik van Windows groepsbeleid-objecten om alle pakketten in realtime stromen te identificeren en te markeren en om uw netwerk te helpen RDP-verkeer een toegewezen deel van de band breedte te geven.

Als u een grote groep gebruikers die een van de problemen ondervindt die in dit artikel wordt beschreven, ondersteunt, moet u waarschijnlijk QoS implementeren. Een klein bedrijf met weinig gebruikers heeft mogelijk geen QoS nodig, maar het moet zelfs daar handig van zijn.

Zonder enige vorm van QoS kunnen de volgende problemen optreden:

* Jitter: RDP-pakketten die aan verschillende tarieven aankomen, wat kan leiden tot visuele en geluids storingen
* Pakket verlies: pakketten die worden verwijderd, wat leidt tot een nieuwe verzen ding waarvoor extra tijd nodig is
* Vertraagde round-trip tijd (RTT): RDP-pakketten nemen lange tijd in beslag om hun bestemming te bereiken, waardoor er vertragingen optreden tussen invoer en reactie van de externe toepassing.

De minst gecompliceerde manier om deze problemen op te lossen is het verg Roten van de grootte van de gegevens verbindingen, zowel intern als via internet. Omdat het vaak voordelig is, biedt QoS een manier om de resources te beheren die u hebt in plaats van de band breedte effectiever toe te voegen. Voor het oplossen van kwaliteits problemen raden wij u aan eerst QoS te gebruiken en vervolgens band breedte alleen toe te voegen wanneer dit nodig is.

Om QoS effectief te maken, moet u consistente QoS-instellingen Toep assen binnen uw organisatie. Elk deel van het pad dat geen ondersteuning biedt voor uw QoS-prioriteiten, kan de RDP-sessie van de kwaliteit verminderen.

## <a name="introduction-to-qos-queues"></a>Inleiding tot QoS-wacht rijen

Om QoS te bieden, moeten netwerk apparaten verkeer kunnen classificeren en moeten ze RDP van ander netwerk verkeer onderscheiden.

Wanneer netwerk verkeer een router binnenkomt, wordt het verkeer in een wachtrij geplaatst. Als er geen QoS-beleid is geconfigureerd, is er slechts één wachtrij en worden alle gegevens behandeld als First-in, first-out met dezelfde prioriteit. Dit betekent dat RDP-verkeer mogelijk achterblijft achter het verkeer, waarbij een paar extra milliseconden geen probleem zou kunnen opleveren.

Wanneer u QoS implementeert, definieert u meerdere wacht rijen met behulp van een van de functies voor congestie beheer, zoals de prioriteits-Queuing van Cisco en de [op klassen gebaseerde gewogen billijke wachtrij (CBWFQ)](https://www.cisco.com/en/US/docs/ios/12_0t/12_0t5/feature/guide/cbwfq.html#wp17641) en congestie ontwijking-functies, zoals [gewogen wille keurig vroege detectie (WRED)](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/qos_conavd/configuration/15-mt/qos-conavd-15-mt-book/qos-conavd-cfg-wred.html).

Een eenvoudige analoge waarde is dat QoS virtuele ' carpool-banen ' maakt in uw gegevens netwerk. Sommige typen gegevens worden nooit of zelden een vertraging ondervinden. Zodra u deze banen hebt gemaakt, kunt u de relatieve grootte aanpassen en veel efficiënter de verbindings bandbreedte beheren die u hebt, terwijl u nog steeds ervaring hebt met de bedrijfs kwaliteit van de gebruikers van uw organisatie.

## <a name="qos-implementation-checklist"></a>Controle lijst voor QoS-implementatie

Ga op een hoog niveau als volgt te werk om QoS te implementeren:

1. [Zorg ervoor dat uw netwerk gereed is](#make-sure-your-network-is-ready)
2. [Zorg ervoor dat RDP-shortpath is ingeschakeld](./shortpath.md) : QoS-beleid wordt niet ondersteund voor reverse Connect-Trans Port
3. [Plaatsen van DSCP-markeringen](#insert-dscp-markers) op sessie-hosts implementeren

Bij de voor bereiding voor het implementeren van QoS, moet u rekening houden met de volgende richt lijnen:

* Het kortste pad naar de sessiehost is het beste
* Eventuele obstakels tussen, zoals proxy's of apparaten voor pakket inspectie, worden niet aanbevolen

## <a name="make-sure-your-network-is-ready"></a>Zorg ervoor dat uw netwerk gereed is

Als u een QoS-implementatie overweegt, moet u uw bandbreedte vereisten en andere [netwerk vereisten](/windows-server/remote/remote-desktop-services/network-guidance?context=/azure/virtual-desktop/context/context)al hebben bepaald.
  
Uitlopend verkeer in een netwerk zal de media kwaliteit aanzienlijk beïnvloeden. Een gebrek aan band breedte leidt tot verminderde prestaties en een slechte gebruikers ervaring. Als de acceptatie en het gebruik van Windows virtueel bureau blad groeit, kunt u [log Analytics](./diagnostics-log-analytics.md) gebruiken om problemen te identificeren en vervolgens aanpassingen aan te brengen met behulp van QoS en selectieve bandbreedte toevoegingen.

### <a name="vpn-considerations"></a>Overwegingen voor VPN

QoS werkt alleen zoals verwacht wanneer deze zijn geïmplementeerd op alle koppelingen tussen clients en sessie-hosts. Als u QoS gebruikt in een intern netwerk en een gebruiker zich aanmeldt vanaf een externe locatie, kunt u alleen prioriteiten binnen uw interne, beheerde netwerk instellen. Hoewel externe locaties een beheerde verbinding kunnen ontvangen door een virtueel particulier netwerk (VPN) te implementeren, voegt een VPN inherent pakket overhead toe en worden er vertragingen in realtime verkeer gegenereerd.

In een wereld wijde organisatie met beheerde koppelingen die over continenten beschikken, raden we u ten zeerste aan QoS te gebruiken, omdat de band breedte voor deze koppelingen beperkt is in vergelijking met het LAN.

## <a name="insert-dscp-markers"></a>DSCP-markeringen invoegen

U kunt QoS implementeren met behulp van een groepsbeleid object (GPO) om hosts te sturen om een DSCP-markering in IP-pakket headers in te voegen als een bepaald type verkeer. Routers en andere netwerk apparaten kunnen worden geconfigureerd om deze markeringen te herkennen en het verkeer in een afzonderlijke wachtrij met hogere prioriteit te plaatsen.

U kunt DSCP-markeringen vergelijken met post zegels die aangeven dat werk nemers over de urgente levering beschikken en hoe ze het beste kunnen sorteren op snelle levering. Zodra u uw netwerk hebt geconfigureerd om voor rang te geven aan RDP-stromen, gaan verloren pakketten en laat de pakketten aanzienlijk afnemen.

Zodra alle netwerk apparaten dezelfde classificaties, markeringen en prioriteiten gebruiken, is het mogelijk om vertragingen, verloren pakketten en jitter te verminderen of te elimineren. Vanuit het RDP-perspectief is de essentiële configuratie stap de classificatie en het markeren van pakketten. Voor end-to-end QoS moet u echter ook de RDP-configuratie zorgvuldig met de onderliggende netwerk configuratie uitlijnen.
De DSCP-waarde vertelt een overeenkomend geconfigureerd netwerk met de prioriteit voor het geven van een pakket of stream.

We raden u aan DSCP-waarde 46 te gebruiken die is toegewezen aan de DSCP-klasse voor **snelle door sturing (EF)** .

### <a name="implement-qos-on-session-host-using-group-policy"></a>QoS op sessiehost implementeren met behulp van groepsbeleid

U kunt op beleid gebaseerde Quality of Service (QoS) in groepsbeleid gebruiken om de vooraf gedefinieerde DSCP-waarde in te stellen.

Als u een QoS-beleid wilt maken voor hosts die lid zijn van een domein, meldt u zich eerst aan bij een computer waarop groepsbeleid-beheer is geïnstalleerd. Open groepsbeleid beheer (Selecteer Start, wijs systeem beheer aan en selecteer vervolgens groepsbeleid beheer) en voer vervolgens de volgende stappen uit:

1. Zoek in groepsbeleid beheer de container waar het nieuwe beleid moet worden gemaakt. Als bijvoorbeeld alle sessies worden gehost op computers bevinden zich in een organisatie-eenheid met de naam **' sessiehost '**, moet het nieuwe beleid worden gemaakt in de sessie host van de organisatie-eenheid.

2. Klik met de rechter muisknop op de juiste container en selecteer vervolgens **een groeps beleidsobject in dit domein maken en hier een koppeling**.

3. Typ in het dialoog venster **Nieuw groeps beleidsobject** een naam voor het nieuwe Groepsbeleid-object in het vak **naam** en selecteer vervolgens **OK**.

4. Klik met de rechter muisknop op het zojuist gemaakte beleid en selecteer vervolgens **bewerken**.

5. Vouw in het Groepsbeleidsbeheer-editor **computer configuratie** uit, vouw **Windows-instellingen** uit, klik met de rechter muisknop op **op beleid gebaseerde QoS** en selecteer **Nieuw beleid maken**.

6. In het dialoog venster op **beleid gebaseerde QoS** typt u op de pagina openen een naam voor het nieuwe beleid in het vak **naam** . Selecteer **DSCP-waarde opgeven** en stel de waarde in op **46**. Geef de optie **uitgaande vertragings snelheid opgeven** niet ingeschakeld en selecteer **volgende**.

7. Selecteer op de volgende pagina **alleen toepassingen met deze naam voor het uitvoer bare bestand** en voer de naam **svchost.exe** in en selecteer **volgende**. Met deze instelling geeft u aan dat het beleid alleen prioriteit moet geven aan het overeenkomende verkeer van de Extern bureaublad-service.

8. Zorg ervoor dat op de derde pagina zowel **een bron-IP-adres** als **een doel-IP-adres** is geselecteerd en selecteer **volgende**. Deze twee instellingen zorgen ervoor dat pakketten worden beheerd, ongeacht welke computer (IP-adres) de pakketten heeft verzonden en welke computer (IP-adres) de pakketten ontvangt.

9. Selecteer op pagina 4 de optie **UDP** in de vervolg keuzelijst **Selecteer het protocol waarop dit QoS-beleid van toepassing is** .

10. Onder de kop **Geef het poort nummer van de bron** op, selecteert u **uit deze bron poort of dit bereik**. Typ **3390** in het bijbehorende tekstvak. Selecteer **Finish**.

Het nieuwe beleid dat u hebt gemaakt, wordt pas van kracht nadat groepsbeleid is vernieuwd op uw Session Host-computers. Hoewel groepsbeleid regel matig vernieuwt, kunt u een onmiddellijke vernieuwing afdwingen door de volgende stappen uit te voeren:

1. Open een opdracht prompt als beheerder (als *administrator uitvoeren*) op elke sessiehost waarvoor u Groepsbeleid wilt vernieuwen.

1. Voer bij de opdracht prompt

   ```console
   gpupdate /force
   ```

### <a name="implement-qos-on-session-host-using-powershell"></a>QoS op sessiehost implementeren met behulp van Power shell

U kunt QoS voor RDP-shortpath instellen met behulp van de Power shell-cmdlet hieronder:

```powershell
New-NetQosPolicy -Name "RDP Shortpath" -AppPathNameMatchCondition "svchost.exe" -IPProtocolMatchCondition UDP -IPSrcPortStartMatchCondition 3390 -IPSrcPortEndMatchCondition 3390 -DSCPAction 46 -NetworkProfile All
```

## <a name="related-articles"></a>Verwante artikelen:

* [Beleid voor Quality of Service (QoS)](/windows-server/networking/technologies/qos/qos-policy-top)

## <a name="next-steps"></a>Volgende stappen

* Zie [vereisten voor de band breedte van Remote Desktop Protocol (RDP) voor Windows Virtual Desktop](rdp-bandwidth.md)voor meer informatie over de bandbreedte vereisten voor virtuele Windows-Bureau bladen.
* Zie wat is Windows Virtual Desktop- [netwerk](network-connectivity.md)connectiviteit? voor meer informatie over de Windows-verbinding met virtueel bureau blad-netwerken.
