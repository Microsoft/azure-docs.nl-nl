---
title: Problemen met Azure Point-to-site-verbindingen oplossen
titleSuffix: Azure VPN Gateway
description: Meer informatie over het oplossen en oplossen van veelvoorkomende punt-naar-site-verbindings problemen en andere fouten en problemen met het virtuele particuliere netwerk.
services: vpn-gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: genli
ms.openlocfilehash: c316aaf02979008b9d2ebc691d54c0fb95a5a52d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95994856"
---
# <a name="troubleshooting-azure-point-to-site-connection-problems"></a>Problemen oplossen: Azure Point-to-site-verbindings problemen

Dit artikel bevat een lijst met veelvoorkomende punt-naar-site-verbindings problemen die u kunt voordoen. Daarnaast worden mogelijke oorzaken en oplossingen voor deze problemen besproken.

## <a name="vpn-client-error-a-certificate-could-not-be-found"></a>VPN-client fout: er is geen certificaat gevonden

### <a name="symptom"></a>Symptoom

Wanneer u probeert verbinding te maken met een virtueel Azure-netwerk met behulp van de VPN-client, wordt het volgende fout bericht weer gegeven:

**Er is geen certificaat gevonden dat kan worden gebruikt met dit Extensible Authentication Protocol. (Fout 798)**

### <a name="cause"></a>Oorzaak

Dit probleem treedt op als het client certificaat ontbreekt in **Certificaten-Huidige gebruiker\Persoonlijk\Certificaten**.

### <a name="solution"></a>Oplossing

Voer de volgende stappen uit om dit probleem op te lossen:

1. Certificaat beheer openen: Klik op **Start**, typ **computer certificaten beheren** en klik vervolgens op **computer certificaten beheren** in het Zoek resultaat.

2. Zorg ervoor dat de volgende certificaten zich op de juiste locatie bevinden:

    | Certificaat | Locatie |
    | ------------- | ------------- |
    | Client. pfx  | Huidige Gebruiker\persoonlijk\certificaten |
    | AzureRoot. CER    | Lokale Computer\Trusted basis certificerings instanties|

3. Ga naar C:\Users \<UserName> \AppData\Roaming\Microsoft\Network\Connections\Cm \<GUID> , installeer het certificaat (*. cer-bestand) hand matig op de opslag van de gebruiker en de computer.

Zie [certificaten voor punt-naar-site-verbindingen genereren en exporteren](vpn-gateway-certificates-point-to-site.md)voor meer informatie over het installeren van het client certificaat.

> [!NOTE]
> Wanneer u het client certificaat importeert, moet u niet de optie **zware persoonlijke sleutel beveiliging inschakelen** selecteren.

## <a name="the-network-connection-between-your-computer-and-the-vpn-server-could-not-be-established-because-the-remote-server-is-not-responding"></a>De netwerk verbinding tussen uw computer en de VPN-server kan niet tot stand worden gebracht omdat de externe server niet reageert

### <a name="symptom"></a>Symptoom

Wanneer u probeert en verbinding maakt met een virtuele Azure-netwerk gateway met behulp van IKEv2 in Windows, wordt het volgende fout bericht weer gegeven:

**De netwerk verbinding tussen uw computer en de VPN-server kan niet tot stand worden gebracht omdat de externe server niet reageert**

### <a name="cause"></a>Oorzaak
 
 Het probleem treedt op als de versie van Windows geen ondersteuning biedt voor IKE-fragmentatie
 
### <a name="solution"></a>Oplossing

IKEv2 wordt ondersteund op Windows 10 en Server 2016. Als u IKEv2 wilt gebruiken, moet u lokaal updates installeren en een registersleutelwaarde instellen. Besturingssysteemversies voor Windows 10 worden niet ondersteund en kunnen alleen SSTP gebruiken.

Windows 10 of Server 2016 voorbereiden voor IKEv2:

1. Installeer de update.

   | Besturingssysteemversie | Date | Aantal/koppeling |
   |---|---|---|---|
   | Windows Server 2016<br>Windows 10 versie 1607 | 17 januari 2018 | [KB4057142](https://support.microsoft.com/help/4057142/windows-10-update-kb4057142) |
   | Windows 10 versie 1703 | 17 januari 2018 | [KB4057144](https://support.microsoft.com/help/4057144/windows-10-update-kb4057144) |
   | Windows 10 versie 1709 | 22 maart 2018 | [KB4089848](https://www.catalog.update.microsoft.com/search.aspx?q=kb4089848) |
   |  |  |  |  |

2. De registersleutelwaarde instellen. Maak of stel `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RasMan\ IKEv2\DisableCertReqPayload` REG_DWORD sleutel in het REGI ster in op 1.

## <a name="vpn-client-error-the-message-received-was-unexpected-or-badly-formatted"></a>VPN-client fout: het ontvangen bericht is niet verwacht of heeft een ongeldige indeling

### <a name="symptom"></a>Symptoom

Wanneer u probeert verbinding te maken met een virtueel Azure-netwerk met behulp van de VPN-client, wordt het volgende fout bericht weer gegeven:

**Het ontvangen bericht is onverwacht of onjuist opgemaakt. (Fout 0x80090326)**

### <a name="cause"></a>Oorzaak

Dit probleem treedt op als een van de volgende voor waarden van toepassing is:

- Het gebruik van door de gebruiker gedefinieerde routes (UDR) met de standaard route op het gateway-subnet is onjuist ingesteld.
- De open bare sleutel van het basis certificaat is niet geüpload naar de Azure VPN-gateway. 
- De sleutel is beschadigd of verlopen.

### <a name="solution"></a>Oplossing

Voer de volgende stappen uit om dit probleem op te lossen:

1. Verwijder UDR in het gateway-subnet. Zorg ervoor dat UDR alle verkeer correct stuurt.
2. Controleer de status van het basis certificaat in de Azure Portal om te zien of het is ingetrokken. Als deze niet is ingetrokken, probeert u het basis certificaat te verwijderen en opnieuw te uploaden. Zie [certificaten maken](vpn-gateway-howto-point-to-site-classic-azure-portal.md#generatecerts)voor meer informatie.

## <a name="vpn-client-error-a-certificate-chain-processed-but-terminated"></a>VPN-client fout: er is een certificaat keten verwerkt, maar beëindigd 

### <a name="symptom"></a>Symptoom 

Wanneer u probeert verbinding te maken met een virtueel Azure-netwerk met behulp van de VPN-client, wordt het volgende fout bericht weer gegeven:

**Er is een certificaat keten verwerkt, maar deze is beëindigd in een basis certificaat dat niet wordt vertrouwd door de vertrouwens provider.**

### <a name="solution"></a>Oplossing

1. Zorg ervoor dat de volgende certificaten zich op de juiste locatie bevinden:

    | Certificaat | Locatie |
    | ------------- | ------------- |
    | Client. pfx  | Huidige Gebruiker\persoonlijk\certificaten |
    | Azuregateway-*GUID*. cloudapp.net  | Huidige User\Trusted basis certificerings instanties|
    | AzureGateway-*GUID*. Cloudapp.net, AzureRoot. CER    | Lokale Computer\Trusted basis certificerings instanties|

2. Als de certificaten zich al op de locatie bevinden, probeert u de certificaten te verwijderen en opnieuw te installeren. Het **azuregateway-*GUID*. cloudapp.net-** certificaat bevindt zich in het configuratie pakket voor de VPN-client dat u hebt gedownload van de Azure Portal. U kunt bestands archiverers gebruiken om de bestanden uit het pakket uit te pakken.

## <a name="file-download-error-target-uri-is-not-specified"></a>Fout bij het downloaden van het bestand: er is geen doel-URI opgegeven

### <a name="symptom"></a>Symptoom

Het volgende fout bericht wordt weer gegeven:

**Fout bij downloaden van bestand. De doel-URI is niet opgegeven.**

### <a name="cause"></a>Oorzaak 

Dit probleem treedt op vanwege een onjuist gateway type. 

### <a name="solution"></a>Oplossing

Het VPN-gateway type moet **VPN** zijn en het VPN-type moet **RouteBased** zijn.

## <a name="vpn-client-error-azure-vpn-custom-script-failed"></a>VPN-client fout: aangepast Azure VPN-script is mislukt 

### <a name="symptom"></a>Symptoom

Wanneer u probeert verbinding te maken met een virtueel Azure-netwerk met behulp van de VPN-client, wordt het volgende fout bericht weer gegeven:

**Het aangepaste script (voor het bijwerken van de routerings tabel) is mislukt. (Fout 8007026f)**

### <a name="cause"></a>Oorzaak

Dit probleem kan zich voordoen als u de site-naar-punt-VPN-verbinding probeert te openen met behulp van een snelkoppeling.

### <a name="solution"></a>Oplossing 

Open het VPN-pakket rechtstreeks in plaats van het te openen via de snelkoppeling.

## <a name="cannot-install-the-vpn-client"></a>De VPN-client kan niet worden geïnstalleerd

### <a name="cause"></a>Oorzaak 

Er is een aanvullend certificaat vereist om de VPN-gateway voor uw virtuele netwerk te vertrouwen. Het certificaat is opgenomen in het configuratie pakket voor de VPN-client dat is gegenereerd op basis van de Azure Portal.

### <a name="solution"></a>Oplossing

Pak het configuratie pakket voor de VPN-client uit en zoek het CER-bestand. Voer de volgende stappen uit om het certificaat te installeren:

1. Open mmc.exe.
2. Voeg de module **certificaten** toe.
3. Selecteer het **computer** account voor de lokale computer.
4. Klik met de rechter muisknop op het knoop punt **vertrouwde basis certificerings instanties** . Klik op **alle taken**  >  **importeren** en blader naar het CER-bestand dat u hebt geëxtraheerd uit het configuratie pakket voor de VPN-client.
5. Start de computer opnieuw op. 
6. Probeer de VPN-client te installeren.

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-data-is-invalid"></a>Azure Portal fout: kan de VPN-gateway niet opslaan en de gegevens zijn ongeldig

### <a name="symptom"></a>Symptoom

Wanneer u probeert de wijzigingen voor de VPN-gateway op te slaan in de Azure Portal, wordt het volgende fout bericht weer gegeven:

**Kan de gateway naam van de virtuele netwerk gateway niet opslaan &lt;  &gt; . De gegevens voor de certificaat &lt; *certificaat-id* &gt; zijn ongeldig.**

### <a name="cause"></a>Oorzaak 

Dit probleem kan optreden als de open bare sleutel van het basis certificaat die u hebt geüpload, een ongeldig teken bevat, zoals een spatie.

### <a name="solution"></a>Oplossing

Zorg ervoor dat de gegevens in het certificaat geen ongeldige tekens bevatten, zoals regel einden (carriage returns). De gehele waarde moet één lange regel zijn. De volgende tekst is een voor beeld van het certificaat:

```text
-----BEGIN CERTIFICATE-----
MIIC5zCCAc+gAwIBAgIQFSwsLuUrCIdHwI3hzJbdBjANBgkqhkiG9w0BAQsFADAW
MRQwEgYDVQQDDAtQMlNSb290Q2VydDAeFw0xNzA2MTUwMjU4NDZaFw0xODA2MTUw
MzE4NDZaMBYxFDASBgNVBAMMC1AyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEF
AAOCAQ8AMIIBCgKCAQEAz8QUCWCxxxTrxF5yc5uUpL/bzwC5zZ804ltB1NpPa/PI
sa5uwLw/YFb8XG/JCWxUJpUzS/kHUKFluqkY80U+fAmRmTEMq5wcaMhp3wRfeq+1
G9OPBNTyqpnHe+i54QAnj1DjsHXXNL4AL1N8/TSzYTm7dkiq+EAIyRRMrZlYwije
407ChxIp0stB84MtMShhyoSm2hgl+3zfwuaGXoJQwWiXh715kMHVTSj9zFechYd7
5OLltoRRDyyxsf0qweTFKIgFj13Hn/bq/UJG3AcyQNvlCv1HwQnXO+hckVBB29wE
sF8QSYk2MMGimPDYYt4ZM5tmYLxxxvGmrGhc+HWXzMeQIDAQABozEwLzAOBgNVHQ8B
Af8EBAMCAgQwHQYDVR0OBBYEFBE9zZWhQftVLBQNATC/LHLvMb0OMA0GCSqGSIb3
DQEBCwUAA4IBAQB7k0ySFUQu72sfj3BdNxrXSyOT4L2rADLhxxxiK0U6gHUF6eWz
/0h6y4mNkg3NgLT3j/WclqzHXZruhWAXSF+VbAGkwcKA99xGWOcUJ+vKVYL/kDja
gaZrxHlhTYVVmwn4F7DWhteFqhzZ89/W9Mv6p180AimF96qDU8Ez8t860HQaFkU6
2Nw9ZMsGkvLePZZi78yVBDCWMogBMhrRVXG/xQkBajgvL5syLwFBo2kWGdC+wyWY
U/Z+EK9UuHnn3Hkq/vXEzRVsYuaxchta0X2UNRzRq+o706l+iyLTpe6fnvW6ilOi
e8Jcej7mzunzyjz4chN0/WVF94MtxbUkLkqP
-----END CERTIFICATE-----
```

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-resource-name-is-invalid"></a>Azure Portal fout: kan de VPN-gateway niet opslaan en de resource naam is ongeldig

### <a name="symptom"></a>Symptoom

Wanneer u probeert de wijzigingen voor de VPN-gateway op te slaan in de Azure Portal, wordt het volgende fout bericht weer gegeven: 

Kan de gateway naam van de **virtuele netwerk gateway niet opslaan &lt;  &gt; . De naam van het resource naam &lt; *certificaat dat u wilt uploaden* &gt; , is ongeldig**.

### <a name="cause"></a>Oorzaak

Dit probleem treedt op omdat de naam van het certificaat een ongeldig teken bevat, zoals een spatie. 

## <a name="azure-portal-error-vpn-package-file-download-error-503"></a>Azure Portal fout: fout bij het downloaden van het VPN-pakket bestand 503

### <a name="symptom"></a>Symptoom

Wanneer u het configuratie pakket voor de VPN-client probeert te downloaden, wordt het volgende fout bericht weer gegeven:

**Kan het bestand niet downloaden. Fout Details: fout 503. De server is bezet.**
 
### <a name="solution"></a>Oplossing

Deze fout kan worden veroorzaakt door een tijdelijk netwerk probleem. Probeer het VPN-pakket na een paar minuten opnieuw te downloaden.

## <a name="azure-vpn-gateway-upgrade-all-point-to-site-clients-are-unable-to-connect"></a>Upgrade van Azure VPN Gateway: alle punten naar site-clients kunnen geen verbinding maken

### <a name="cause"></a>Oorzaak

Als het certificaat langer is dan 50 procent gedurende de levens duur, wordt het certificaat doorgevoerd.

### <a name="solution"></a>Oplossing

U lost dit probleem op door het punt opnieuw te downloaden en te implementeren op een site pakket op alle clients.

## <a name="too-many-vpn-clients-connected-at-once"></a>Er zijn te veel VPN-clients tegelijk verbonden

Het maximum aantal toegestane verbindingen is bereikt. U kunt het totale aantal verbonden clients bekijken in de Azure Portal.

## <a name="vpn-client-cannot-access-network-file-shares"></a>VPN-client kan geen toegang krijgen tot netwerk bestands shares

### <a name="symptom"></a>Symptoom

De VPN-client is verbonden met het virtuele Azure-netwerk. De client heeft echter geen toegang tot netwerk shares.

### <a name="cause"></a>Oorzaak

Het SMB-protocol wordt gebruikt voor toegang tot bestands shares. Wanneer de verbinding wordt gestart, voegt de VPN-client de referenties van de sessie toe en treedt de fout op. Nadat de verbinding tot stand is gebracht, wordt de client gedwongen de cache referenties voor Kerberos-verificatie te gebruiken. Dit proces initieert query's naar de Key Distribution Center (een domein controller) om een token op te halen. Omdat de client verbinding maakt via internet, kan het zijn dat de domein controller niet kan worden bereikt. Daarom kan de client geen failover uitvoeren van Kerberos naar NTLM. 

Het enige tijdstip waarop de client wordt gevraagd om een referentie, is wanneer deze een geldig certificaat (met SAN = UPN) heeft dat is uitgegeven door het domein waaraan het is gekoppeld. De client moet ook fysiek zijn verbonden met het domein netwerk. In dit geval probeert de client het certificaat te gebruiken en de domein controller te bereiken. Vervolgens retourneert de Key Distribution Center de fout ' KDC_ERR_C_PRINCIPAL_UNKNOWN '. Failover wordt uitgevoerd voor de client. 

### <a name="solution"></a>Oplossing

U kunt het probleem omzeilen door het in cache opslaan van domein referenties uit de volgende registersubsleutel uit te scha kelen: 

`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\DisableDomainCreds - Set the value to 1`


## <a name="cannot-find-the-point-to-site-vpn-connection-in-windows-after-reinstalling-the-vpn-client"></a>Kan de punt-naar-site-VPN-verbinding niet vinden in Windows nadat de VPN-client opnieuw is geïnstalleerd

### <a name="symptom"></a>Symptoom

U verwijdert de punt-naar-site-VPN-verbinding en installeert vervolgens de VPN-client opnieuw. In dit geval is de VPN-verbinding niet geconfigureerd. De VPN-verbinding wordt niet weer geven in de instellingen voor **netwerk verbindingen** in Windows.

### <a name="solution"></a>Oplossing

Om het probleem op te lossen, verwijdert u de oude VPN-client configuratie bestanden van **C:\Users\UserName\AppData\Roaming\Microsoft\Network\Connections \<VirtualNetworkId>** en voert u het installatie programma voor de VPN-client opnieuw uit.

## <a name="point-to-site-vpn-client-cannot-resolve-the-fqdn-of-the-resources-in-the-local-domain"></a>Punt-naar-site-VPN-client kan de FQDN van de bronnen in het lokale domein niet omzetten

### <a name="symptom"></a>Symptoom

Wanneer de client verbinding maakt met Azure via een punt-naar-site-VPN-verbinding, kan de FQDN-naam van de resources in uw lokale domein niet worden omgezet.

### <a name="cause"></a>Oorzaak

Punt-naar-site-VPN-client maakt normaal gesp roken gebruik van Azure DNS servers die zijn geconfigureerd in het virtuele Azure-netwerk. De Azure DNS-servers hebben voor rang op de lokale DNS-servers die zijn geconfigureerd in de client (tenzij de metriek van de Ethernet-interface kleiner is), zodat alle DNS-query's naar de Azure DNS-servers worden verzonden. Als de Azure DNS servers niet over de records voor de lokale resources beschikken, mislukt de query.

### <a name="solution"></a>Oplossing

Om het probleem op te lossen, moet u ervoor zorgen dat de Azure DNS servers die in het virtuele netwerk van Azure worden gebruikt, de DNS-records voor lokale bronnen kunnen omzetten. Hiervoor kunt u DNS-doorstuur servers of voorwaardelijke doorstuur servers gebruiken. Zie [naam omzetting met uw eigen DNS-server](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) voor meer informatie.

## <a name="the-point-to-site-vpn-connection-is-established-but-you-still-cannot-connect-to-azure-resources"></a>De punt-naar-site-VPN-verbinding is tot stand gebracht, maar u kunt nog steeds geen verbinding maken met Azure-resources 

### <a name="cause"></a>Oorzaak

Dit probleem kan zich voordoen als de VPN-client de routes van de Azure VPN-gateway niet ontvangt.

### <a name="solution"></a>Oplossing

U kunt dit probleem oplossen door de [Azure VPN-gateway opnieuw](./reset-gateway.md)in te stellen. Om ervoor te zorgen dat de nieuwe routes worden gebruikt, moeten de punt-naar-site-VPN-clients opnieuw worden gedownload nadat de peering van het virtuele netwerk is geconfigureerd.

## <a name="error-the-revocation-function-was-unable-to-check-revocation-because-the-revocation-server-was-offlineerror-0x80092013"></a>Fout: de intrekkings functie kan het intrekken niet controleren omdat de intrekkings server offline is. (Fout 0x80092013) "

### <a name="causes"></a>Oorzaken
Dit fout bericht wordt weer gegeven als de client geen toegang heeft tot http://crl3.digicert.com/ssca-sha2-g1.crl en http://crl4.digicert.com/ssca-sha2-g1.crl .  Voor de intrekkings controle is toegang tot deze twee sites vereist.  Dit probleem doet zich meestal voor op de client waarop proxy server is geconfigureerd. Als de aanvragen van de proxy server niet worden door lopen, wordt deze in sommige omgevingen geweigerd op de Edge-firewall.

### <a name="solution"></a>Oplossing

Controleer de instellingen van de proxy server, zorg ervoor dat de-client toegang heeft tot http://crl3.digicert.com/ssca-sha2-g1.crl en http://crl4.digicert.com/ssca-sha2-g1.crl .

## <a name="vpn-client-error-the-connection-was-prevented-because-of-a-policy-configured-on-your-rasvpn-server-error-812"></a>VPN-client fout: de verbinding is vanwege een beleids instelling op uw RAS/VPN-server niet geconfigureerd. (Fout 812)

### <a name="cause"></a>Oorzaak

Deze fout treedt op als de RADIUS-server die u hebt gebruikt voor de verificatie van de VPN-client onjuiste instellingen heeft of als Azure gateway de RADIUS-server niet kan bereiken.

### <a name="solution"></a>Oplossing

Zorg ervoor dat de RADIUS-server correct is geconfigureerd. Zie [RADIUS-verificatie integreren met Azure AD multi-factor Authentication-Server](../active-directory/authentication/howto-mfaserver-dir-radius.md)voor meer informatie.

## <a name="error-405-when-you-download-root-certificate-from-vpn-gateway"></a>Fout 405 wanneer u het basis certificaat downloadt van VPN Gateway

### <a name="cause"></a>Oorzaak

Het basis certificaat is niet geïnstalleerd. Het basis certificaat is geïnstalleerd in het **vertrouwde certificaat** archief van de client.

## <a name="vpn-client-error-the-remote-connection-was-not-made-because-the-attempted-vpn-tunnels-failed-error-800"></a>VPN-client fout: de externe verbinding is niet gemaakt omdat de poging tot VPN-tunnels is mislukt. (Fout 800) 

### <a name="cause"></a>Oorzaak

Het NIC-stuur programma is verouderd.

### <a name="solution"></a>Oplossing

Het NIC-stuur programma bijwerken:

1. Klik op **Start**, typ **Apparaatbeheer** en selecteer deze in de lijst met resultaten. Als u wordt gevraagd om een beheerders wachtwoord of een bevestiging, typt u het wacht woord of geeft u een bevestiging.
2. Zoek in de categorieën **netwerk adapters** de NIC die u wilt bijwerken.  
3. Dubbel klik op de naam van het apparaat, selecteer **stuur programma bijwerken**, selecteer **automatisch zoeken naar bijgewerkte stuur programma-software**.
4.  Als Windows geen nieuw stuurprogramma kan vinden, kunt u ernaar zoeken op de website van de fabrikant van het apparaat en de aanwijzingen aldaar opvolgen.
5. Start de computer opnieuw op en voer de verbinding opnieuw uit.

## <a name="vpn-client-error-dialing-vpn-connection-vpn-connection-name-status--vpn-platform-did-not-trigger-connection"></a>VPN-client fout: VPN-verbinding <VPN Connection Name> wordt gebeld, status = VPN-platform heeft geen verbinding geactiveerd

Mogelijk ziet u ook de volgende fout in Logboeken van RasClient: ' de gebruiker <User> heeft een verbinding gekozen met de naam <VPN Connection Name> die is mislukt. De fout code die wordt geretourneerd bij de fout is 1460.

### <a name="cause"></a>Oorzaak

De Azure VPN-client beschikt niet over de machtiging ' achtergrond-apps ' die zijn ingeschakeld in de app-instellingen voor Windows.

### <a name="solution"></a>Oplossing

1. Ga in Windows naar instellingen-> privacy-> achtergrond-apps
2. De ' apps mogen op de achtergrond worden uitgevoerd ' in-of uitschakelen

## <a name="error-file-download-error-target-uri-is-not-specified"></a>Fout: er is geen doel-URI voor het downloaden van het bestand opgegeven

### <a name="cause"></a>Oorzaak

Dit wordt veroorzaakt doordat er een onjuist gateway type is geconfigureerd.

### <a name="solution"></a>Oplossing

Het type van de Azure VPN-gateway moet VPN zijn en het VPN-type moet **RouteBased** zijn.

## <a name="vpn-package-installer-doesnt-complete"></a>Het installatie programma voor het VPN-pakket is niet voltooid

### <a name="cause"></a>Oorzaak

Dit probleem kan worden veroorzaakt door de vorige VPN-client installaties. 

### <a name="solution"></a>Oplossing

Verwijder de oude configuratie bestanden voor de VPN-client van **C:\Users\UserName\AppData\Roaming\Microsoft\Network\Connections \<VirtualNetworkId>** en voer het installatie programma voor de VPN-client opnieuw uit. 

## <a name="the-vpn-client-hibernates-or-sleep-after-some-time"></a>De VPN-client wordt na enige tijd in de slaap stand gezet

### <a name="solution"></a>Oplossing

Controleer de instellingen voor de slaap stand en sluimer stand op de computer waarop de VPN-client wordt uitgevoerd.