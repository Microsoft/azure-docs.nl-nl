---
title: Bekende problemen met Azure percept
description: Meer informatie over bekende problemen met Azure percept en hun tijdelijke oplossingen
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: reference
ms.date: 03/03/2021
ms.openlocfilehash: a04e53c8444a01bc42f3ce71393fc842f3419e74
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102193475"
---
# <a name="known-issues"></a>Bekende problemen

Als u een van deze problemen ondervindt, hoeft u geen bug te openen. Als u problemen ondervindt met een van de tijdelijke oplossingen, kunt u een probleem openen.

|Gebied|Beschrijving van het probleem|Tijdelijke oplossing|
|-------|---------|---------|
| On-boarding-ervaring | Kan geen ervaring volt ooien tenzij Wi-Fi van het apparaat is geconfigureerd (Azure-aanmelding mislukt). | 1. SSH naar het toegangs punt van het apparaat (10.1.1.1) <br> 2. het IP-adres van het Ethernet-apparaat identificeren en kopiëren <br> 3. verbinding maken met de on-boarding-ervaring met de gekopieerde Ethernet IP-gebaseerde URL |
| On-boarding-ervaring | Wanneer u op koppelingen in de gebruiksrecht overeenkomst klikt tijdens de on-boarding-ervaring, wordt er soms geen nieuwe webpagina geopend. | Kopieer de koppeling en open deze in een afzonderlijk browser venster. |
| On-boarding-ervaring | Kan niet door de gebruikers ervaring werken als er verbinding is met een mobiele Wi-Fi hotspot. | Verbind uw apparaat rechtstreeks met de SoftAP, een Wi-Fi netwerk of een netwerk via Ethernet. |
| Wi-Fi-SoftAP | Het SoftAP kan soms worden verbroken of verwijderd. | Er wordt onderzocht.  Als het apparaat opnieuw wordt opgestart, wordt het normaal gesp roken weer hersteld. |
| Wi-Fi | De knop hardware waarmee de Wi-Fi SoftAP in-en uitschakelt, werkt soms niet. | Ga door en druk op de knop of start het apparaat opnieuw op. |
| Wi-Fi | Gebruikers zien mogelijk een bericht nadat er verbinding is gemaakt met Wi-Fi ' dit Wi-Fi netwerk maakt gebruik van een oudere beveiligings standaard. ' | De hotspot/SoftAP van de Devkit maakt gebruik van het WEP-versleutelings algoritme. |
| Wi-Fi | Kan geen verbinding maken met SoftAP vanaf Windows 10 PC met het volgende fout bericht: <br> ' Kan geen verbinding maken met dit netwerk ' | Start zowel de Devkit als de computer opnieuw op. |
| Apparaat bijwerken | Containers worden niet uitgevoerd na een OTA-update. | SSH in op het apparaat en start de IoT Edge-container opnieuw met deze opdracht `systemctl restart iotedge` . Hiermee worden alle containers opnieuw opgestart. |
| Apparaat bijwerken | Gebruikers kunnen een bericht ontvangen dat de update is mislukt, zelfs als deze is geslaagd. | Controleer of het apparaat is bijgewerkt door te navigeren naar het apparaat dubbele voor het apparaat in IoT Hub. Dit wordt opgelost na de eerste update. |
| Apparaat bijwerken | Gebruikers kunnen hun Wi-Fi Verbindings instellingen na hun eerste update kwijt raken. | Voer de ervaring na het bijwerken uit om de Wi-Fi verbinding in te stellen. Dit wordt opgelost na de eerste update. |
| Apparaat bijwerken | Na het uitvoeren van een OTA-update kunnen gebruikers zich niet meer aanmelden via SSH met behulp van eerder gemaakte gebruikers accounts en kunnen nieuwe SSH-gebruikers niet worden gemaakt via de on-boarding-ervaring. Dit probleem is van invloed op systemen die OTA-updates uitvoeren van de volgende vooraf geïnstalleerde installatie kopie versies: 2020.110.114.105 en 2020.109.101.105. | Voer de volgende stappen uit na de OTA-update om uw gebruikers profielen te herstellen: <br> [Ssh in uw Devkit](./how-to-ssh-into-percept-dk.md) met "root" als de gebruikers naam. Als u de SSH-gebruiker aanmelden met de gebruikers ervaring hebt uitgeschakeld, moet u deze weer inschakelen. Voer deze opdracht uit nadat u verbinding hebt gemaakt: <br> ```mkdir -p /var/custom-configs/home; chmod 755 /var/custom-configs/home``` <br> Voer de volgende opdracht uit om de start gegevens van de vorige gebruiker te herstellen: <br> ```mkdir -p /tmp/prev-rootfs && mount /dev/mmcblk0p3 /tmp/prev-rootfs && [ ! -L /tmp/prev-rootfs/home ] && cp -a /tmp/prev-rootfs/home/* /var/custom-configs/home/. && echo "User home migrated!"; umount /tmp/prev-rootfs``` |
| Apparaat bijwerken | Wanneer u een OTA-update hebt door lopen, gaan er Update groepen verloren. | Werk de tag van het apparaat bij door [deze instructies](https://docs.microsoft.com/azure/azure-percept/how-to-update-over-the-air#create-a-device-update-group)te volgen. |
| Installatie programma voor dev tools pack | Optionele Caffe-installatie kan mislukken als docker niet op de juiste wijze op het systeem wordt uitgevoerd. | Zorg ervoor dat docker is geïnstalleerd en wordt uitgevoerd en voer de installatie van Caffe opnieuw uit. |
| Installatie programma voor dev tools pack | Optionele CUDA-installatie mislukt op niet-compatibele systemen. | Controleer de systeem compatibiliteit met CUDA voordat u het installatie programma uitvoert. |
| Docker, netwerk, IoT Edge | Als uw interne netwerk 172. x. x. x gebruikt, kunnen docker-containers geen verbinding maken met de rand. | Voeg als volgt een speciale sectie bip toe aan de/etc/docker/-daemon.js: `{    "bip": "192.168.168.1/24"}` |
|Azure percept Studio | De koppelingen ' stream weer geven ' in azure percept Studio openen geen nieuw venster waarin de webstream van het apparaat wordt weer gegeven. | 1. Open de [Azure Portal](https://portal.azure.com) en selecteer **IOT hub**. <br> 2. Klik op het IoT Hub waarmee het apparaat is verbonden. <br> 3. Selecteer **IOT Edge** onder **automatische apparaatbeheer** op uw IOT hub pagina. <br> 4. Selecteer uw apparaat in de lijst. <br> 5. Selecteer **modules instellen** boven aan de pagina apparaat. <br> 6. Klik op het prullenbak pictogram naast **HostIpModule** om de module te verwijderen. <br> 7. om de actie te bevestigen, klikt u op **controleren + maken** en vervolgens op **maken**. <br> 8. Open [Azure percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) en klik op **apparaten** in het linkerdeel venster. <br> 9. Selecteer uw apparaat in de lijst. <br> 10. Klik op het tabblad **Vision** op de **Stream van uw apparaat weer geven**. Op uw apparaat wordt een nieuwe versie van HostIpModule gedownload en een browser tabblad geopend met de webstream van uw apparaat. |