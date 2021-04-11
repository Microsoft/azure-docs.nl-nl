---
title: Wi-Fi profielen gebruiken met Azure Stack Edge mini-R-apparaten
description: Hierin wordt beschreven hoe u Wi-Fi profielen maakt voor Azure Stack Edge mini-R-apparaten op hoogwaardige bedrijfs netwerken en persoonlijke netwerken.
services: databox
author: v-dalc@microsoft.com
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/24/2021
ms.author: alkohli
ms.openlocfilehash: 90c7c238cef104eae78618e51fa4b284adcc8f42
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105050481"
---
# <a name="use-wi-fi-profiles-with-azure-stack-edge-mini-r-devices"></a>Wi-Fi profielen gebruiken met Azure Stack Edge mini-R-apparaten

In dit artikel wordt beschreven hoe u Wi-Fi-profielen (Wireless Network) gebruikt met uw Azure Stack Edge mini-R-apparaten.

Hoe u het Wi-Fi profiel voorbereidt, is afhankelijk van het type draadloze netwerk:

- Op een Wi-Fi WPA2 (Protected Access 2)-persoonlijk netwerk, zoals een thuis netwerk of Wi-Fi hotspot openen, kunt u een bestaand draadloos profiel downloaden en gebruiken met hetzelfde wacht woord dat u gebruikt met andere apparaten.

- In een bedrijfs omgeving met hoge beveiliging kunt u uw apparaat openen via een WPA2-Enter prise-netwerk. Op dit type netwerk heeft elke client computer een uniek Wi-Fi profiel en wordt het beleid geverifieerd via certificaten. U moet samen werken met de netwerk beheerder om de vereiste configuratie te bepalen.

De profiel vereisten voor beide typen netwerken worden later verder besproken.

In beide gevallen is het belang rijk om ervoor te zorgen dat het profiel voldoet aan de beveiligings vereisten van uw organisatie voordat u het profiel met uw apparaat test of gebruikt.

## <a name="about-wi-fi-profiles"></a>Over Wi-Fi profielen

Een Wi-Fi profiel bevat de SSID (Service Set Identifier, of **netwerk naam**), wachtwoord sleutel en beveiligings informatie die nodig zijn om uw Azure stack Edge mini-R-apparaat te verbinden met een draadloos netwerk.

In het volgende code voorbeeld ziet u basis instellingen voor een profiel dat u kunt gebruiken met een typisch draadloos netwerk:

* `SSID` is de netwerk naam.

* `name` is de gebruiks vriendelijke naam voor de Wi-Fi verbinding. Dit is de naam die gebruikers te zien krijgen wanneer ze door de beschik bare verbindingen op hun apparaat bladeren.

* Het profiel is zo geconfigureerd dat de computer automatisch wordt verbonden met het draadloze netwerk wanneer het zich binnen het bereik van het netwerk () bevindt `connectionMode`  =  `auto` .

```html
<?xml version="1.0"?>
<WLANProfile xmlns="http://www.contoso.com/networking/WLAN/profile/v1">
    <name>ContosoWIFICORP</name>
    <SSIDConfig>
        <SSID>
            <hex>1A234561234B5012</hex>
        </SSID>
        <nonBroadcast>false</nonBroadcast>
    </SSIDConfig>
    <connectionType>ESS</connectionType>
    <connectionMode>auto</connectionMode>
    <autoSwitch>false</autoSwitch>
```

Zie voor meer informatie over Wi-Fi Profiel instellingen **ondernemings profiel** in [Wi-Fi-instellingen toevoegen voor Windows 10 en nieuwere apparaten](/mem/intune/configuration/wi-fi-settings-windows#enterprise-profile)en Zie [Cisco Wi-Fi-profiel configureren](azure-stack-edge-mini-r-manage-wifi.md#configure-cisco-wi-fi-profile).

Als u draadloze verbindingen wilt inschakelen op een Azure Stack Edge mini-R-apparaat, configureert u de Wi-Fi poort op het apparaat en voegt u vervolgens de Wi-Fi profielen toe aan het apparaat. In een bedrijfs netwerk uploadt u ook certificaten naar het apparaat. U kunt vervolgens verbinding maken met een draadloos netwerk via de lokale webgebruikersinterface voor het apparaat. Zie [draadloze connectiviteit beheren op uw Azure stack Edge mini](./azure-stack-edge-mini-r-manage-wifi.md)maal voor meer informatie.

## <a name="profile-for-wpa2---personal-network"></a>Profiel voor WPA2-persoonlijk netwerk

Op een Wi-Fi WPA2 (Protected Access 2)-persoonlijk netwerk, zoals een thuis netwerk of Wi-Fi open-hotspot, kunnen meerdere apparaten gebruikmaken van hetzelfde profiel en hetzelfde wacht woord. Op uw thuis netwerk gebruiken uw mobiele telefoon en laptop hetzelfde draadloze profiel en wacht woord om verbinding te maken met het netwerk.

Een Windows 10-client kan bijvoorbeeld een runtime profiel voor u genereren. Wanneer u zich aanmeldt bij het draadloze netwerk, wordt u gevraagd om het wacht woord van Wi-Fi. Als u dit wacht woord hebt opgegeven, bent u verbonden. Er is geen certificaat nodig in deze omgeving.

Op dit type netwerk kunt u mogelijk een Wi-Fi Profiel van uw laptop exporteren en het vervolgens toevoegen aan uw Azure Stack Edge mini-R-apparaat. Zie [een Wi-Fi profiel exporteren](#export-a-wi-fi-profile)hieronder voor instructies.

> [!IMPORTANT]
> Neem contact op met uw netwerk beheerder voordat u een Wi-Fi profiel maakt voor uw Azure Stack Edge mini-R-apparaat om de beveiligings vereisten van de organisatie voor draadloze netwerken te achterhalen. U mag geen Wi-Fi profiel op uw apparaat testen of gebruiken totdat u weet dat het draadloze netwerk voldoet aan de vereisten.

## <a name="profiles-for-wpa2---enterprise-network"></a>Profielen voor WPA2-Enter prise-netwerk

Op een WPA2-netwerk (Wireless Protected Access 2): u moet met de netwerk beheerder samen werken om het benodigde Wi-Fi profiel en certificaat te verkrijgen om uw Azure Stack Edge mini-R-apparaat aan het netwerk te koppelen.

Voor netwerken met een hoge beveiliging kan het Azure-apparaat gebruikmaken van PEAP (Protected Extensible Authentication Protocol) met Extensible Authentication Protocol-Transport Layer Security (EAP-TLS). PEAP met EAP-TLS maakt gebruik van computer verificatie: de client en server gebruiken certificaten om hun identiteit aan elkaar te verifiëren.

> [!NOTE]
> * Verificatie van de gebruiker met behulp van PEAP micro soft Challenge Handshake Authentication Protocol versie 2 (PEAP MSCHAPv2) wordt niet ondersteund op Azure Stack Edge mini-R-apparaten.
> * EAP-TLS-verificatie is vereist om toegang te krijgen tot Azure Stack Edge mini-R-functionaliteit. Een draadloze verbinding die u met behulp van Active Directory hebt ingesteld, werkt niet.

De netwerk beheerder genereert een uniek Wi-Fi profiel en een client certificaat voor elke computer. De netwerk beheerder beslist of een afzonderlijk certificaat moet worden gebruikt voor elk apparaat of een gedeeld certificaat.

Als u op meer dan één fysieke locatie op de werk plek werkt, moet de netwerk beheerder mogelijk meer dan één sitespecifieke Wi-Fi profiel en certificaat voor uw draadloze verbindingen opgeven.

In een bedrijfs netwerk wordt u aangeraden geen instellingen te wijzigen in de Wi-Fi profielen die uw netwerk beheerder biedt. De enige aanpassing die u mogelijk wilt maken, is met de instellingen voor automatische verbinding. Zie voor meer informatie [Basic-profiel](/mem/intune/configuration/wi-fi-settings-windows#basic-profile) in Wi-Fi-instellingen voor Windows 10-en nieuwere apparaten.

In een bedrijfs omgeving met hoge beveiliging kunt u mogelijk een bestaand draadloos-netwerk profiel als sjabloon gebruiken:

* U kunt het draadloze bedrijfs netwerk profiel downloaden van uw werk computer. Zie [een Wi-Fi profiel exporteren](#export-a-wi-fi-profile)hieronder voor instructies.

* Als anderen in uw organisatie al via een draadloos netwerk verbinding maken met hun Azure Stack Edge mini-R-apparaten, kunnen ze het Wi-Fi profiel downloaden van het apparaat. Zie [Wi-Fi profiel downloaden](azure-stack-edge-mini-r-manage-wifi.md#download-wi-fi-profile)voor instructies.

## <a name="export-a-wi-fi-profile"></a>Een Wi-Fi-profiel exporteren

Ga als volgt te werk om een profiel voor de Wi-Fi-interface op uw computer te exporteren:

1. Als u de draadloze profielen op uw computer wilt zien, opent u in het menu **Start** de **opdracht prompt** (cmd.exe) en voert u de volgende opdracht in:

   `netsh wlan show profiles`

   De uitvoer ziet er ongeveer uit zoals in dit voorbeeld:

   ```dos
   Profiles on interface Wi-Fi:

   Group policy profiles (read only)
   ---------------------------------
       <None>

   User profiles
   -------------
       All User Profile     : ContosoCORP
       All User Profile     : ContosoFTINET
       All User Profile     : GusIS2809
       All User Profile     : GusGuests
       All User Profile     : SeaTacGUEST
       All User Profile     : Boat
   ```

2. Als u een profiel wilt exporteren, voert u de volgende opdracht in:

   `netsh wlan export profile name=”<profileName>” folder=”<path>\<profileName>"`

   Met de volgende opdracht wordt bijvoorbeeld het ContosoFTINET-profiel in XML-indeling opgeslagen in de map down loads voor de gebruiker met de naam `gusp` .

   ```dos
   C:\Users\gusp>netsh wlan export profile name="ContosoFTINET" folder=c:Downloads

   Interface profile "ContosoFTINET" is saved in file "c:Downloads\ContosoFTINET.xml" successfully.
   ```

## <a name="add-certificate-wi-fi-profile-to-device"></a>Certificaat Wi-Fi profiel toevoegen aan apparaat

Wanneer u de Wi-Fi profielen en certificaten hebt die u nodig hebt, voert u de volgende stappen uit om uw Azure Stack Edge mini-R-apparaat te configureren voor draadloze verbindingen:

1. Voor een WPA2-Enter prise-netwerk uploadt u de benodigde certificaten naar het apparaat volgens de richt lijnen in [Upload certificaten](./azure-stack-edge-gpu-manage-certificates.md#upload-certificates).

1. Upload het Wi-Fi Profiel (en) naar het mini-R-apparaat en maak er verbinding mee door de instructies in [toevoegen te volgen, verbinding maken met Wi-Fi profiel](./azure-stack-edge-mini-r-manage-wifi.md#add-connect-to-wi-fi-profile).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [configureren van een netwerk voor Azure stack Edge mini-R](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md).
- Meer informatie over het [beheren van Wi-Fi op de Azure stack Edge mini-R](azure-stack-edge-mini-r-manage-wifi.md).
