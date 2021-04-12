---
title: Hoge beschikbaarheid instellen
description: Verhoog de tolerantie van uw Defender voor IoT-implementatie door een on-premises beheer console te installeren. Implementaties met een hoge Beschik baarheid zorgen ervoor dat uw beheerde Sens oren continu rapporteren aan een actieve on-premises beheer console.
ms.date: 12/07/2020
ms.topic: how-to
ms.openlocfilehash: d0e09cd37fbae91d1903ca8f175c0592b567da6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104781650"
---
# <a name="about-high-availability"></a>Over hoge Beschik baarheid

Verhoog de tolerantie van uw Defender voor IoT-implementatie door een on-premises beheer console te installeren. Implementaties met een hoge Beschik baarheid zorgen ervoor dat uw beheerde Sens oren continu rapporteren aan een actieve on-premises beheer console.

Deze implementatie wordt geïmplementeerd met een on-premises beheer console paar dat een primair en secundair apparaat bevat.

## <a name="about-primary-and-secondary-communication"></a>Over primaire en secundaire communicatie

Wanneer een primaire en secundaire on-premises beheer console is gekoppeld:

- Een on-premises beheer console SSL geleid-certificaat wordt toegepast om een beveiligde verbinding te maken tussen de primaire en secundaire apparaten. De SSL geleid kan het zelfondertekende certificaat zijn dat standaard is geïnstalleerd of een certificaat dat door de klant is geïnstalleerd.

- Voor de primaire on-premises beheer console gegevens wordt elke 10 minuten automatisch een back-up gemaakt van de secundaire on-premises beheer console. Er wordt een back-up gemaakt van de configuraties van on-premises beheer console en apparaatgegevens. PCAP-bestanden en-logboeken worden niet opgenomen in de back-up. U kunt PCAPs en logboeken hand matig back-ups maken en herstellen.

- De primaire installatie van de beheer console is gedupliceerd op de secundaire; bijvoorbeeld systeem instellingen. Als deze instellingen op de primaire versie worden bijgewerkt, worden deze ook bijgewerkt op de secundaire.

- Voordat de licentie van het secundaire verloopt, moet u deze definiëren als primair om de licentie bij te werken.

## <a name="about-failover-and-failback"></a>Over failover en failback

Als een sensor geen verbinding kan maken met de primaire on-premises beheer console, wordt automatisch verbinding gemaakt met de secundaire. Uw systeem wordt zowel door de primaire als de secundaire gelijktijdig ondersteund als minder dan de helft van de Sens oren communiceert met de secundaire. Het secundaire gaat over als meer dan de helft van de Sens oren ermee communiceert. Failover van de primaire naar de secundaire tijd neemt ongeveer drie minuten in beslag. Wanneer de failover plaatsvindt, wordt de primaire on-premises beheer console geblokkeerd. Als dit gebeurt, kunt u zich met dezelfde aanmeldings referenties aanmelden bij de secundaire.

Tijdens de failover trachten Sens oren toch te communiceren met het primaire apparaat. Als meer dan de helft van de beheerde Sens oren met de primaire verbinding kan worden uitgevoerd, wordt de primaire herstel bewerking hersteld. Het volgende bericht wordt weer gegeven op de secundaire console wanneer de primaire wordt hersteld.

:::image type="content" source="media/how-to-set-up-high-availability/secondary-console-message.png" alt-text="Scherm afbeelding van een bericht dat wordt weer gegeven op de secundaire console wanneer de primaire wordt hersteld.":::

Meld u na omleiding weer aan bij het primaire apparaat.

## <a name="high-availability-setup-overview"></a>Overzicht van de installatie met hoge Beschik baarheid

De installatie-en configuratie procedures worden uitgevoerd in vier hoofd fasen:

1. Een on-premises beheer console primair apparaat installeren. 

2. Configureer het primaire apparaat van de on-premises beheer console. Bijvoorbeeld geplande back-upinstellingen, VLAN-instellingen. Raadpleeg de gebruikers handleiding van de on-premises beheer console voor meer informatie. Alle instellingen worden na het koppelen automatisch toegepast op het secundaire apparaat.

3. Installeer een secundair apparaat voor een on-premises beheer console. Zie voor meer informatie [over de installatie van Defender voor IOT](how-to-install-software.md).

4. Koppel de primaire en secundaire on-premises beheer console-apparaten, zoals [hier](https://infrascale.secure.force.com/pkb/articles/Support_Article/How-to-access-your-Appliance-Management-Console)wordt beschreven. De primaire on-premises beheer console moet ten minste twee Sens oren beheren om de installatie uit te voeren.

## <a name="high-availability-requirements"></a>Vereisten voor hoge Beschik baarheid

Controleer of u aan de volgende vereisten voor hoge Beschik baarheid hebt voldaan:

- Certificaatvereisten

- Software-en hardware-vereisten

- Netwerk toegangs vereisten

### <a name="software-and-hardware-requirements"></a>Software-en hardware-vereisten

- Op de primaire en secundaire on-premises beheer console apparaten moeten identieke hardware-modellen en software versies worden uitgevoerd.

- Het systeem voor maximale Beschik baarheid kan alleen worden ingesteld door Defender voor IoT-gebruikers met behulp van CLI-hulpprogram ma's.

### <a name="network-access-requirements"></a>Netwerk toegangs vereisten

U moet controleren of het beveiligings beleid van uw organisatie toegang biedt tot de volgende services op de primaire en secundaire on-premises beheer console. Deze services staan ook de verbinding tussen Sens oren en secundaire on-premises beheer console toe:

|Poort|Service|Beschrijving|
|----|-------|-----------|
|**443 of TCP**|HTTPS|Hiermee wordt toegang verleend tot de on-premises beheer console-webconsole.|
|**22 of TCP**|SSH|Synchroniseert de gegevens tussen de primaire en secundaire on-premises beheer console-apparaten|
|**123 of UDP**|NTP| De NTP-tijd synchronisatie van de on-premises beheer console. Controleer of de actieve en passieve apparaten zijn gedefinieerd met dezelfde tijd zone.|

## <a name="create-the-primary-and-secondary-pair"></a>Het primaire en secundaire paar maken

Controleer of de primaire en secundaire on-premises beheer console-apparaten zijn ingeschakeld voordat u de procedure start.  

### <a name="on-the-primary"></a>Op de primaire

1. Meld u aan bij de CLI als een Defender voor IoT-gebruiker.

2. Voer de volgende opdracht uit op de primaire:

```azurecli-interactive
sudo cyberx-management-trusted-hosts-add -ip <Secondary IP>
```

>[!NOTE]
>In dit document wordt de principal on-premises beheer console aangeduid als primair en wordt de agent aangeduid als de secundaire.

3. Voer het IP-adres in van het secundaire apparaat in het ```<Secondary ip>``` veld en selecteer ENTER. Het IP-adres wordt vervolgens gevalideerd en het SSL-certificaat wordt gedownload naar de primaire. Als u het IP-adres invoert, worden de Sens oren ook gekoppeld aan het secundaire apparaat.

4. Voer de volgende opdracht uit op de primaire om te controleren of het certificaat correct is geïnstalleerd:

```azurecli-interactive
sudo cyberx-management-trusted-hosts-apply
```

5. Voer de volgende opdracht uit op de primaire. **Niet uitvoeren met sudo.**

```azurecli-interactive
cyberx-management-deploy-ssh-key <Secondary IP>
```

Hiermee wordt de verbinding tussen de primaire en secundaire apparaten voor back-up-en herstel bewerkingen.

6. Voer het IP-adres van de secundaire in en selecteer ENTER.

### <a name="on-the-secondary"></a>Op de secundaire

1. Meld u aan bij de CLI als een Defender voor IoT-gebruiker.

2. Voer de volgende opdracht uit op de secundaire. **Niet uitvoeren met sudo**:

```azurecli-interactive
cyberx-management-deploy-ssh-key <Primary ip>
```

Hiermee wordt de verbinding tussen de primaire en secundaire apparaten voor back-up-en herstel bewerkingen.

3. Voer het IP-adres van de primaire en druk op ENTER.

### <a name="track-high-availability-activity"></a>Activiteiten met hoge Beschik baarheid bijhouden

De kern toepassings logboeken kunnen worden geëxporteerd naar de Defender voor IoT-ondersteunings team voor het afhandelen van problemen met hoge Beschik baarheid.  

De kern Logboeken openen:

1. Selecteer **exporteren** in het venster **systeem instellingen** .

## <a name="update-the-on-premises-management-console-with-high-availability"></a>De on-premises beheer console bijwerken met hoge Beschik baarheid

Voer de update voor hoge Beschik baarheid in de volgende volg orde uit. Zorg ervoor dat elke stap is voltooid voordat u begint met een nieuwe stap.

Bijwerken met hoge Beschik baarheid:

1. Werk de primaire on-premises beheer console bij.

2. Werk de secundaire on-premises beheer console bij.

3. De Sens oren bijwerken.

## <a name="see-also"></a>Zie ook

[Uw on-premises beheerconsole activeren en instellen](how-to-activate-and-set-up-your-on-premises-management-console.md)