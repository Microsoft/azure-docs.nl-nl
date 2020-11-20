---
title: Webproxy instellen voor StorSimple 8000 Series-apparaat | Microsoft Docs
description: Meer informatie over het gebruik van Windows PowerShell voor StorSimple voor het configureren van web proxy-instellingen voor uw StorSimple-apparaat.
services: storsimple
documentationcenter: ''
author: alkohli
manager: ''
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: alkohli
ms.openlocfilehash: 48671e7558ea1bd613d33372c96fa3c563407e81
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966221"
---
# <a name="configure-web-proxy-for-your-storsimple-device"></a>Webproxy configureren voor uw StorSimple-apparaat

## <a name="overview"></a>Overzicht

In deze zelf studie wordt beschreven hoe u Windows PowerShell voor StorSimple kunt gebruiken om webproxy-instellingen voor uw StorSimple-apparaat te configureren en weer te geven. De web proxy-instellingen worden gebruikt door het StorSimple-apparaat bij de communicatie met de Cloud. Er wordt een webproxyserver gebruikt voor het toevoegen van een extra beveiligingslaag, het filteren van inhoud, cache om bandbreedte vereisten te vereenvoudigen of zelfs te helpen bij het analyseren.

De instructies in deze zelf studie zijn alleen van toepassing op fysieke apparaten uit de StorSimple-serie van 8000. De web proxy-configuratie wordt niet ondersteund op de StorSimple Cloud Appliance (8010 en 8020).

Webproxy is een _optionele_ configuratie voor uw StorSimple-apparaat. U kunt webproxy alleen configureren via Windows PowerShell voor StorSimple. De configuratie is als volgt in twee stappen:

1. U configureert eerst webproxy-instellingen via de installatie wizard of Windows PowerShell voor StorSimple-cmdlets.
2. Vervolgens schakelt u de geconfigureerde web proxy-instellingen in via Windows PowerShell voor StorSimple-cmdlets.

Nadat de configuratie van de webproxy is voltooid, kunt u de geconfigureerde web proxy-instellingen weer geven in de Microsoft Azure StorSimple Apparaatbeheer-service en de Windows PowerShell voor StorSimple.

Na het lezen van deze zelf studie kunt u het volgende doen:

* Configureer webproxy met behulp van de wizard Setup en cmdlets.
* Webproxy inschakelen met behulp van-cmdlets.
* Webproxy-instellingen weer geven in de Azure Portal.
* Fouten tijdens de configuratie van de webproxy oplossen.


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple"></a>Webproxy configureren via Windows PowerShell voor StorSimple

U kunt een van de volgende opties gebruiken om proxy-instellingen te configureren:

* De installatie wizard om u te begeleiden bij de configuratie stappen.
* Cmdlets in Windows PowerShell voor StorSimple.

Elk van deze methoden wordt in de volgende secties besproken.

## <a name="configure-web-proxy-via-the-setup-wizard"></a>Webproxy configureren via de installatie wizard

Gebruik de wizard Setup om u te begeleiden bij de stappen voor de configuratie van de webproxy. Voer de volgende stappen uit om de webproxy op uw apparaat te configureren.

#### <a name="to-configure-web-proxy-via-the-setup-wizard"></a>Webproxy configureren via de installatie wizard

1. Kies in het menu van de seriële console optie 1, **Meld u aan met volledige toegang** en geef het **beheerders wachtwoord** voor het apparaat op. Typ de volgende opdracht om een installatie wizard-sessie te starten:
   
    `Invoke-HcsSetupWizard`
2. Als dit de eerste keer is dat u de wizard Setup voor apparaatregistratie hebt gebruikt, moet u alle vereiste netwerk instellingen configureren totdat u de configuratie van de webproxy hebt bereikt. Als uw apparaat al is geregistreerd, accepteert u alle geconfigureerde netwerk instellingen totdat u de configuratie van de webproxy hebt bereikt. In de installatie wizard, wanneer u wordt gevraagd om de instellingen voor de webproxy te configureren, typt u **Ja**.
3. Geef voor de **webproxy-URL** het IP-adres of de Fully QUALIFIED domain name (FQDN) op van de webproxyserver en het TCP-poort nummer dat u wilt gebruiken voor de communicatie met de Cloud. Gebruik de volgende indeling:
   
    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`
   
    TCP-poort nummer 8080 is standaard opgegeven.
4. Kies het verificatie type als **NTLM**, **Basic** of **geen**. Basic is de minst veilige verificatie voor de proxy server configuratie. NT LAN Manager (NTLM) is een zeer veilig en complex verificatie protocol dat gebruikmaakt van een bericht systeem in drie richtingen (soms vier als er extra integriteit is vereist) om een gebruiker te verifiëren. De standaard verificatie is NTLM. Zie [Basic](https://hc.apache.org/httpclient-3.x/authentication.html) -en [NTLM-verificatie](https://hc.apache.org/httpclient-3.x/authentication.html)voor meer informatie. 
   
   > [!IMPORTANT]
   > **In de StorSimple-Apparaatbeheer-service werken de grafieken voor het controleren van apparaten niet wanneer Basic-of NTLM-verificatie is ingeschakeld in de proxyserver configuratie voor het apparaat. Voor de werking van de bewakings grafieken moet u ervoor zorgen dat verificatie is ingesteld op geen.**
  
5. Als u de verificatie hebt ingeschakeld, geeft u een **webproxy-gebruikers naam** en een **webproxy-wacht woord** op. U moet ook het wacht woord bevestigen.
   
    ![Webproxy configureren op StorSimple Device1](./media/storsimple-configure-web-proxy/IC751830.png)

Als u uw apparaat voor de eerste keer registreert, gaat u door met de registratie. Als uw apparaat al is geregistreerd, wordt de wizard afgesloten. De geconfigureerde instellingen worden opgeslagen.

Web proxy is nu ingeschakeld. U kunt de stap voor het inschakelen van de [webproxy](#enable-web-proxy) overs Laan en direct gaan om de [webproxy-instellingen weer te geven in de Azure Portal](#view-web-proxy-settings-in-the-azure-portal).

## <a name="configure-web-proxy-via-windows-powershell-for-storsimple-cmdlets"></a>Webproxy configureren via Windows PowerShell voor StorSimple-cmdlets

Een andere manier om webproxy-instellingen te configureren, is via de Windows PowerShell voor StorSimple-cmdlets. Voer de volgende stappen uit om de webproxy te configureren.

#### <a name="to-configure-web-proxy-via-cmdlets"></a>Webproxy configureren via cmdlets
1. Kies in het menu van de seriële console optie 1, **Meld u aan met volledige toegang**. Geef het **beheerders wachtwoord** voor het apparaat op wanneer u hierom wordt gevraagd. Het standaard wachtwoord is `Password1` .
2. Typ in de opdrachtprompt:
   
    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`
   
    Geef het wacht woord op en bevestig dit wanneer u hierom wordt gevraagd.
   
    ![Webproxy configureren op StorSimple Device3](./media/storsimple-configure-web-proxy/IC751831.png)

De webproxy is nu geconfigureerd en moet worden ingeschakeld.

## <a name="enable-web-proxy"></a>Webproxy inschakelen

Webproxy is standaard uitgeschakeld. Nadat u de web proxy-instellingen op uw StorSimple-apparaat hebt geconfigureerd, gebruikt u de Windows PowerShell voor StorSimple om de webproxy-instellingen in te scha kelen.

> [!NOTE]
> **Deze stap is niet vereist als u de wizard Setup voor het configureren van de webproxy hebt gebruikt. Webproxy wordt na een installatie wizard-sessie automatisch standaard ingeschakeld.**


Voer de volgende stappen uit in Windows PowerShell voor StorSimple om webproxy op uw apparaat in te scha kelen:

#### <a name="to-enable-web-proxy"></a>Webproxy inschakelen
1. Kies in het menu van de seriële console optie 1, **Meld u aan met volledige toegang**. Geef het **beheerders wachtwoord** voor het apparaat op wanneer u hierom wordt gevraagd. Het standaard wachtwoord is `Password1` .
2. Typ in de opdrachtprompt:
   
    `Enable-HcsWebProxy`
   
    U hebt nu de configuratie van de webproxy ingeschakeld op uw StorSimple-apparaat.
   
    ![Webproxy configureren op StorSimple Device4](./media/storsimple-configure-web-proxy/IC751832.png)

## <a name="view-web-proxy-settings-in-the-azure-portal"></a>Webproxy-instellingen weer geven in de Azure Portal

De web proxy-instellingen worden geconfigureerd via de Windows Power shell-interface en kunnen niet worden gewijzigd vanuit de portal. U kunt deze geconfigureerde instellingen echter bekijken in de portal. Voer de volgende stappen uit om de webproxy weer te geven.

#### <a name="to-view-web-proxy-settings"></a>Webproxy-instellingen weer geven
1. Ga naar **StorSimple Apparaatbeheer service >-apparaten**. Selecteer en klik op een apparaat en ga vervolgens naar **apparaatinstellingen > netwerk**.

    ![Klik op netwerk](./media/storsimple-8000-configure-web-proxy/view-web-proxy-1.png)

2. Klik op de Blade **netwerk instellingen** op de tegel **webproxy** .

    ![Klik op web proxy](./media/storsimple-8000-configure-web-proxy/view-web-proxy-2.png)

3. Controleer op de Blade **webproxy** de geconfigureerde web proxy-instellingen op uw StorSimple-apparaat.
   
    ![Webproxy-instellingen weer geven](./media/storsimple-8000-configure-web-proxy/view-web-proxy-3.png)


## <a name="errors-during-web-proxy-configuration"></a>Fouten tijdens de configuratie van de webproxy

Als de webproxy-instellingen onjuist zijn geconfigureerd, worden er fout berichten weer gegeven aan de gebruiker in Windows PowerShell voor StorSimple. In de volgende tabel worden enkele van deze fout berichten, hun waarschijnlijke oorzaken en aanbevolen acties uitgelegd.

| Serienr. | HRESULT-fout code | Mogelijke hoofd oorzaak | Aanbevolen actie |
|:--- |:--- |:--- |:--- |
| 1. |0x80070001 |De opdracht wordt uitgevoerd vanaf de passieve controller en kan niet communiceren met de actieve controller. |Voer de opdracht uit op de actieve controller. Als u de opdracht vanaf de passieve controller wilt uitvoeren, moet u de verbinding van passieve naar actieve controller verhelpen. U moet Microsoft Ondersteuning als deze verbinding is verbroken. |
| 2. |0x800710dd-de bewerkings-id is ongeldig |Proxy-instellingen worden niet ondersteund op StorSimple Cloud Appliance. |Proxy-instellingen worden niet ondersteund op StorSimple Cloud Appliance. Deze kunnen alleen worden geconfigureerd op een fysiek StorSimple-apparaat. |
| 3. |0x80070057-ongeldige para meter |Een van de opgegeven para meters voor de proxy-instellingen is ongeldig. |De URI is niet in de juiste indeling opgenomen. Gebruik de volgende indeling: `http://<IP address or FQDN of the web proxy server>:<TCP port number>` |
| 4. |0x800706ba-RPC-server niet beschikbaar |De hoofd oorzaak is een van de volgende:</br></br>Het cluster is niet actief. </br></br>De DataPath-service wordt niet uitgevoerd.</br></br>De opdracht wordt uitgevoerd vanaf de passieve controller en kan niet communiceren met de actieve controller. |Zorg Microsoft Ondersteuning om ervoor te zorgen dat het cluster actief is en dat de DataPath-service wordt uitgevoerd.</br></br>Voer de opdracht uit vanaf de actieve controller. Als u de opdracht van de passieve controller wilt uitvoeren, moet u ervoor zorgen dat de passieve controller kan communiceren met de actieve controller. U moet Microsoft Ondersteuning als deze verbinding is verbroken. |
| 5. |0x800706be-RPC-aanroep is mislukt |Het cluster is niet beschikbaar. |Zorg Microsoft Ondersteuning om ervoor te zorgen dat het cluster actief is. |
| 6. |0x8007138f-cluster bron niet gevonden |De cluster bron van de platform service is niet gevonden. Dit kan gebeuren wanneer de installatie niet de juiste is. |Mogelijk moet u de fabrieks instellingen herstellen op het apparaat. Mogelijk moet u een platform bron maken. Neem contact op met Microsoft Ondersteuning voor volgende stappen. |
| 7. |0x8007138c: cluster bron is niet online |De cluster bronnen platform of DataPath zijn niet online. |Neem contact op met Microsoft Ondersteuning om ervoor te zorgen dat de service bronnen DataPath en platform online zijn. |

> [!NOTE]
> * De bovenstaande lijst met fout berichten is niet limitatief.
> * Fouten met betrekking tot web proxy-instellingen worden niet weer gegeven in de Azure Portal in uw StorSimple Apparaatbeheer-service. Als er een probleem is met de webproxy nadat de configuratie is voltooid, wordt de apparaatstatus gewijzigd in **offline** in de klassieke Portal. |

## <a name="next-steps"></a>Volgende stappen
* Raadpleeg [problemen met de implementatie van uw StorSimple-apparaat oplossen](./storsimple-8000-troubleshoot-deployment.md)als u problemen ondervindt bij het implementeren van uw apparaat of het configureren van de webproxy-instellingen.
* Ga voor meer informatie over het gebruik van de StorSimple Apparaatbeheer-service naar [de StorSimple Apparaatbeheer-service gebruiken om uw StorSimple-apparaat te beheren](storsimple-8000-manager-service-administration.md).