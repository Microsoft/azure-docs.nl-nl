---
title: Met HSM beveiligde sleutels genereren en overbrengen voor Azure Key Vault - Azure Key Vault
description: Gebruik dit artikel bij het plannen, genereren en overdragen van uw eigen met HSM beveiligde sleutels voor gebruik met Azure Key Vault. Ook wel bekend als BYOK of Bring Your Own Key.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 02/24/2021
ms.author: ambapat
ms.openlocfilehash: f7761cf011a3a678bb7609e1063ac6ebec90d395
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102499183"
---
# <a name="import-hsm-protected-keys-for-key-vault-ncipher"></a>Met HSM beveiligde sleutels importeren voor Key Vault (nCipher)

> [!WARNING]
> De in dit document beschreven methode voor het importeren van de HSM-sleutel wordt **afgeschaft** en wordt in de toekomst niet meer ondersteund. Het werkt alleen met nCipher nShield-familie van Hsm's met firmware 12.40.2 of 12,50 met een hotfix. Het gebruik van de [nieuwe methode voor het importeren van HSM-sleutels](hsm-protected-keys-byok.md) wordt ten zeerste aanbevolen.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Als u Azure Key Vault gebruikt, kunt u het beste sleutels in HSM's (Hardware Security Modules) importeren of genereren die de HSM-grens nooit overschrijden. Dit scenario wordt vaak *Bring Your Own Key* of BYOK genoemd. Azure Key Vault maakt gebruik van de nCipher nShield-serie van HSM's (gevalideerd voor FIPS 140-2 Level 2) om uw sleutels te beveiligen.


Gebruik de informatie in dit artikel bij het plannen, genereren en ten slotte overbrengen van uw eigen, met HSM beveiligde sleutels voor gebruik met Azure Key Vault. 

Deze functionaliteit is niet beschikbaar voor Azure China 21Vianet.

> [!NOTE]
> Zie [Wat is Azure Key Vault?](../general/overview.md) voor meer informatie over Azure Key Vault.  
> Zie [Wat is Azure Key Vault?](../general/overview.md) voor een zelfstudie waarmee u een sleutelkluis kunt maken voor met HSM beveiligde sleutels.

Meer informatie over het genereren en overbrengen van een met HSM beveiligde sleutel via internet:

* U genereert de sleutel op een offline werkstation, waardoor de kwetsbaarheid voor aanvallen wordt verminderd.
* De sleutel wordt versleuteld met een KEK (Key Exchange Key), die versleuteld blijft totdat de sleutel wordt overgebracht naar de HSM's van Azure Key Vault. Alleen de versleutelde versie van uw sleutel verlaat het oorspronkelijke werkstation.
* De toolset stelt eigenschappen in voor uw tenantsleutel waarmee uw sleutel wordt gebonden aan de Security World van Azure Key Vault. Nadat de HSM's van Azure Key Vault de sleutel hebben ontvangen en ontsleuteld, kan de sleutel alleen door deze HSM's worden gebruikt. De sleutel kan niet worden geëxporteerd. Deze binding wordt afgedwongen door de nCipher-HSM's.
* De KEK (Key Exchange Key) die wordt gebruikt om uw sleutel te versleutelen, wordt gegenereerd binnen de HSM's van Azure Key Vault en kan niet worden geëxporteerd. De HSM's dwingen af dat er geen versie van de KEK buiten de HSM's kan bestaan. Daarnaast bevat de toolset een attest van nCipher dat de KEK niet exporteerbaar is en is gegenereerd binnen een geverifieerde HSM die is gefabriceerd door nCipher.
* De toolset bevat een attest van nCipher dat de Security World van Azure Key Vault ook is gegenereerd op een geverifieerde HSM die is gefabriceerd door nCipher. Dit attest bewijst dat Microsoft legitieme hardware gebruikt.
* Microsoft maakt gebruik van afzonderlijke KEK's en afzonderlijke Security Worlds in elke geografische regio. Deze scheiding zorgt ervoor dat uw sleutel alleen kan worden gebruikt in datacenters in de regio waarin u de sleutel hebt versleuteld. Zo kan een sleutel van een Europese klant niet worden gebruikt in datacenters in Noord-Amerika of Azië.

## <a name="more-information-about-ncipher-hsms-and-microsoft-services"></a>Meer informatie over nCipher-HSM's en Microsoft-services

nCipher Security, een Entrust Datacard-bedrijf, is een toonaangevend bedrijf leider op de HSM-markt voor algemeen gebruik. Het bedrijf ondersteunt vooraanstaande organisaties door vertrouwen, integriteit en controle te bieden voor hun bedrijfskritieke gegevens en toepassingen. De cryptografische oplossingen van nCipher beveiligen opkomende technologieën, zoals de cloud, IoT, blockchain en digitale betalingen. Daarnaast helpen ze om te voldoen aan nieuwe nalevingsmandaten, met behulp van dezelfde bewezen beproefde technologie die organisaties over de hele wereld gebruiken om zich te beschermen tegen bedreigingen van hun gevoelige gegevens, netwerkcommunicatie en bedrijfsinfrastructuur. nCipher zorgt ervoor dat bedrijfskritieke toepassingen met maximaal vertrouwen kunnen worden ingezet, waarbij de integriteit van gegevens wordt gegarandeerd en klanten altijd de volledige controle behouden.

Microsoft heeft samengewerkt met nCipher Security om de state-of-the-art voor HSM's te verbeteren. Dankzij deze verbeteringen kunt u profiteren van de gebruikelijke voordelen van gehoste services, zonder dat u de controle over uw sleutels verliest. Dankzij deze uitbreidingen kan Microsoft de HSM's beheren en hoeft u dat niet te doen. Azure Key Vault is een cloudservice en kan dus snel omhoog worden geschaald om gebruikspieken van uw organisatie op te vangen. Tegelijkertijd wordt uw sleutel beveiligd binnen de HSM's van Microsoft. U behoudt de controle over de levenscyclus van de sleutel, omdat u de sleutel genereert en deze overbrengt naar de HSM's van Microsoft.

## <a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Bring Your Own Key (BYOK) implementeren voor Azure Key Vault

Gebruik de volgende informatie en procedures als u uw eigen, met HSM beveiligde sleutel wilt genereren en deze vervolgens wilt overbrengen naar Azure Key Vault: het zogenaamde BYOK-scenario (Bring Your Own Key).

## <a name="prerequisites-for-byok"></a>Vereisten voor BYOK

Raadpleeg de volgende tabel voor een lijst met vereisten voor BYOK voor Azure Key Vault.

| Vereiste | Meer informatie |
| --- | --- |
| Een abonnement op Azure |Als u een Azure-sleutelkluis wilt maken, hebt u een Azure-abonnement nodig: meld u aan voor een [gratis proefversie](https://azure.microsoft.com/pricing/free-trial/). |
| De Premium-servicelaag van Azure Key Vault voor de ondersteuning van met HSM beveiligde sleutels |Zie de [website met prijzen voor Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) voor meer informatie over de servicelagen en mogelijkheden voor Azure Key Vault. |
| HSM's uit de nCipher nShield-serie, smartcards en ondersteunende software |U moet toegang hebben tot een HSM (Hardware Security Module) van nCipher en weten hoe een HSM uit de nCipher nShield-serie in grote lijnen werkt. Zie [deze website van nCipher (Engelstalig)](https://go.ncipher.com/rs/104-QOX-775/images/nCipher_nShield_Family_Brochure.pdf?_ga=2.106120835.1607422418.1590478092-577009923.1587131206) voor een lijst met compatibele modellen, of om een HSM te kopen als u er nog geen hebt. |
| De volgende hardware en software:<ol><li>Een offline x64-werkstation met minimaal Windows 7 en nCipher nShield-software van ten minste versie 11.50.<br/><br/>Als op dit werkstation Windows 7 wordt uitgevoerd, moet u [Microsoft .NET Framework 4.5 installeren](https://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe).</li><li>Een werkstation met een internetverbinding en minimaal Windows 7 en **minimaal versie 1.1.0** van [Azure PowerShell](/powershell/azure/).</li><li>Een USB-station of een ander draagbaar opslagapparaat met ten minste 16 MB beschikbare ruimte.</li></ol> |Uit veiligheidsoverwegingen raden we u aan het eerste werkstation niet aan te sluiten op een netwerk. Deze aanbeveling wordt echter niet programmatisch afgedwongen.<br/><br/>In de onderstaande instructies wordt dit werkstation het offline werkstation genoemd.</p></blockquote><br/>Als uw tenantsleutel is bedoeld voor een productienetwerk, adviseren we bovendien dat u een tweede, afzonderlijk werkstation gebruikt om de toolset te downloaden en de tenantsleutel te uploaden. Voor testdoeleinden kunt u echter hetzelfde werkstation gebruiken als het eerste.<br/><br/>In de onderstaande instructies wordt dit tweede werkstation het online werkstation genoemd.</p></blockquote><br/> |

## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Uw sleutel genereren en overbrengen naar een HSM van Azure Key Vault

U gebruikt de volgende vijf stappen om uw sleutel te genereren en deze over te brengen naar een HSM van Azure Key Vault:

* [Stap 1: het online werkstation voorbereiden](#step-1-prepare-your-internet-connected-workstation)
* [Stap 2: het offline werkstation voorbereiden](#step-2-prepare-your-disconnected-workstation)
* [Stap 3: uw sleutel genereren](#step-3-generate-your-key)
* [Stap 4: uw sleutel voorbereiden voor overdracht](#step-4-prepare-your-key-for-transfer)
* [Stap 5: uw sleutel overdragen naar Azure Key Vault](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>Stap 1: het online werkstation voorbereiden

Voer voor deze eerste stap de volgende procedures uit op het werkstation dat is verbonden met internet.

### <a name="step-11-install-azure-powershell"></a>Stap 1.1: Azure PowerShell installeren

Ga naar het online werkstation en download en installeer de Azure PowerShell module. Deze module bevat onder andere de cmdlets voor het beheren van Azure Key Vault. Zie [Azure PowerShell installeren en configureren](/powershell/azure/) voor de installatie-instructies.

### <a name="step-12-get-your-azure-subscription-id"></a>Stap 1.2: id van Azure-abonnement ophalen

Start een Azure PowerShell-sessie en gebruik de volgende opdracht om u aan te melden bij uw Azure-account:

```Powershell
   Connect-AzAccount
```
Voer in het pop-upvenster in de browser uw gebruikersnaam en wachtwoord voor uw Azure-account in. Gebruik vervolgens de opdracht [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription):

```powershell
   Get-AzSubscription
```
Zoek in de uitvoer de id van het abonnement dat u wilt gebruiken voor Azure Key Vault. U hebt deze abonnements-id later nodig.

Laat het venster van Azure PowerShell geopend.

### <a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>Stap 1.3: de BYOK-toolset voor Azure Key Vault downloaden

Ga naar het Microsoft Downloadcentrum en [download de BYOK-toolset voor Azure Key Vault](https://www.microsoft.com/download/details.aspx?id=45345) voor uw geografische regio of instantie van Azure. Gebruik de volgende informatie om vast te stellen welk pakket u moet downloaden en wat de bijbehorende SHA-256-pakket-hash is:

---
**Verenigde Staten:**

KeyVault-BYOK-Tools-UnitedStates.zip

2E8C00320400430106366A4E8C67B79015524E4EC24A2D3A6DC513CA1823B0D4

---
**Europa:**

KeyVault-BYOK-Tools-Europe.zip

9AAA63E2E7F20CF9BB62485868754203721D2F88D300910634A32DFA1FB19E4A

---
**Azië:**

KeyVault-BYOK-Tools-AsiaPacific.zip

4BC14059BF0FEC562CA927AF621DF665328F8A13616F44C977388EC7121EF6B5

---
**Latijns-Amerika:**

KeyVault-BYOK-Tools-LatinAmerica.zip

E7DFAFF579AFE1B9732C30D6FD80C4D03756642F25A538922DD1B01A4FACB619

---
**Japan:**

KeyVault-BYOK-Tools-Japan.zip

3933C13CC6DC06651295ADC482B027AF923A76F1F6BF98B4D4B8E94632DEC7DF

---
**Korea:**

KeyVault-BYOK-Tools-Korea.zip

71AB6BCFE06950097C8C18D532A9184BEF52A74BB944B8610DDDA05344ED136F

---
**Zuid-Afrika:**

KeyVault-BYOK-Tools-SouthAfrica.zip

C41060C5C0170AAAAD896DA732E31433D14CB9FC83AC3C67766F46D98620784A

---
**VAE:**

KeyVault-BYOK-Tools-UAE.zip

FADE80210B06962AA0913EA411DAB977929248C65F365FD953BB9F241D5FC0D3

---
**Australië:**

KeyVault-BYOK-Tools-Australia.zip

CD0FB7365053DEF8C35116D7C92D203C64A3D3EE2452A025223EEB166901C40A

---
[**Azure Government:**](https://azure.microsoft.com/features/gov/)

KeyVault-BYOK-Tools-USGovCloud.zip

F8DB2FC914A7360650922391D9AA79FF030FD3048B5795EC83ADC59DB018621A

---
**US Government DOD:**

KeyVault-BYOK-Tools-USGovernmentDoD.zip

A79DD8C6DFFF1B00B91D1812280207A205442B3DDF861B79B8B991BB55C35263

---
**Canada:**

KeyVault-BYOK-Tools-Canada.zip

61BE1A1F80AC79912A42DEBBCC42CF87C88C2CE249E271934630885799717C7B

---
**Duitsland:**

KeyVault-BYOK-Tools-Germany.zip

5385E615880AAFC02AFD9841F7BADD025D7EE819894AA29ED3C71C3F844C45D6

---
**Germany Public:**

KeyVault-BYOK-Tools-Germany-Public.zip

54534936D0A0C99C8117DB724C34A5E50FD204CFCBD75C78972B785865364A29

---
**India:**

KeyVault-BYOK-Tools-India.zip

49EDCEB3091CF1DF7B156D5B495A4ADE1CFBA77641134F61B0E0940121C436C8

---
**Frankrijk:**

KeyVault-BYOK-Tools-France.zip

5C9D1F3E4125B0C09E9F60897C9AE3A8B4CB0E7D13A14F3EDBD280128F8FE7DF

---
**Verenigd Koninkrijk:**

KeyVault-BYOK-Tools-UnitedKingdom.zip

432746BD0D3176B708672CCFF19D6144FCAA9E5EB29BB056489D3782B3B80849

---
**Zwitserland:**

KeyVault-BYOK-Tools-Switzerland.zip

88CF8D39899E26D456D4E0BC57E5C94913ABF1D73A89013FCE3BBD9599AD2FE9

---


Gebruik vanuit uw Azure PowerShell-sessie de cmdlet [Get-FileHash](/powershell/module/microsoft.powershell.utility/get-filehash) om de integriteit van uw gedownloade BYOK-toolset te valideren.

   ```powershell
   Get-FileHash KeyVault-BYOK-Tools-*.zip
   ```

De toolset bevat het volgende:

* Een KEK-pakket (Key Exchange Key) met een naam die begint met **BYOK-KEK-pkg-.**
* Een Security World-pakket met een naam die begint met **BYOK-SecurityWorld-pkg-.**
* Een Python-script met de naam **verifykeypackage.py.**
* Een uitvoerbaar bestand voor de opdrachtregel met de naam **KeyTransferRemote.exe** en de bijbehorende DLL-bestanden.
* Een Visual C++ Redistributable met de naam **vcredist_x64.exe.**

Kopieer het pakket naar een USB-station of ander draagbaar opslagmedium.

## <a name="step-2-prepare-your-disconnected-workstation"></a>Stap 2: het offline werkstation voorbereiden

Voer voor deze tweede stap de volgende procedures uit op het werkstation dat niet is verbonden met een netwerk (internet of uw interne netwerk).

### <a name="step-21-prepare-the-disconnected-workstation-with-ncipher-nshield-hsm"></a>Stap 2.1: het offline werkstation voorbereiden met de nCipher nShield-HSM

Installeer de ondersteunende software van nCipher op een Windows-computer en koppel vervolgens een nCipher nShield-HSM aan die computer.

Zorg ervoor dat de tools van nCipher zich in het pad bevinden ( **%nfast_home%\bin**). Typ bijvoorbeeld het volgende:

  ```cmd
  set PATH=%PATH%;"%nfast_home%\bin"
  ```

Zie de gebruikershandleiding voor de nShield-HSM voor meer informatie.

### <a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>Stap 2.2: de BYOK-toolset installeren op het offline werkstation

Kopieer het pakket met de BYOK-hulpmiddelenset van het USB-station of het andere draagbare opslagmedium en doe daarna het volgende:

1. Pak de bestanden in het gedownloade pakket uit naar een map.
2. Voer vcredist_x64.exe uit in die map.
3. Volg de instructies voor het installeren van de Visual C++ runtime-onderdelen voor Visual Studio 2013.

## <a name="step-3-generate-your-key"></a>Stap 3: uw sleutel genereren

Voer voor deze derde stap de volgende procedures uit op het offline werkstation. Om deze stap te voltooien moet uw HSM zich in de initialisatiemodus bevinden. 

### <a name="step-31-change-the-hsm-mode-to-i"></a>Stap 3.1: de HSM-modus wijzigen in 'I'

Als u nCipher nShield Edge gebruikt, kunt u de modus als volgt wijzigen: 1. Gebruik de Mode-knop om de vereiste modus te markeren. 2. Druk binnen een paar seconden op de Clear-knop en houdt deze enkele seconden ingedrukt. De modus is gewijzigd als het lampje van de nieuwe modus niet meer knippert en blijft branden. Het statuslampje kan een paar seconden onregelmatig knipperen en knippert vervolgens regelmatig als het apparaat klaar is. Als dat niet het geval is, blijft het apparaat in de huidige modus, en brandt het bijbehorende statuslampje.

### <a name="step-32-create-a-security-world"></a>Stap 3.2: een Security World maken

Start een opdrachtprompt en voer het programma new-world van nCipher uit.

   ```cmd
    new-world.exe --initialize --cipher-suite=DLf3072s256mRijndael --module=1 --acs-quorum=2/3
   ```

Dit programma maakt een **Beveiligingswereld**-bestand op %NFAST_KMDATA%\local\world, wat overeenkomt met de map C:\ProgramData\nCipher\Key Management Data\local. U kunt verschillende waarden voor het quorum gebruiken, maar in ons voorbeeld wordt u gevraagd om drie lege kaarten en pincodes in te voeren. Vervolgens geven twee van de kaarten volledige toegang tot de Security World. Deze kaarten worden de **Beheerderskaartenset** voor de nieuwe beveiligingswereld.

> [!NOTE]
> Als uw HSM geen ondersteuning biedt voor de nieuwere coderingssuite DLf3072s256mRijndael, kunt u --cipher-suite= DLf3072s256mRijndael vervangen door --cipher-suite=DLf1024s160mRijndael
> 
> De Security World die wordt gemaakt met new-world.exe die wordt geleverd met nCipher-software versie 12.50 is niet compatibel met deze BYOK-procedure. Er zijn twee opties beschikbaar:
> 1) Downgrade de nCipher-software naar versie 12.40.2 om een nieuwe Security World te maken.
> 2) Neem contact op met de ondersteuning van nCipher en vraag hen om een hotfix voor versie 12.50, zodat u versie 12.40.2 van new-world.exe kunt gebruiken die compatibel is met deze BYOK procedure.

Ga daarna als volgt te werk:

* Maak een back-up van het wereldbestand. Beveilig en bescherm het wereldbestand, de beheerderskaarten en de bijbehorende pincodes en zorg ervoor dat niemand toegang heeft tot meer dan één kaart.

### <a name="step-33-change-the-hsm-mode-to-o"></a>Stap 3.3: de HSM-modus wijzigen in 'O'

Als u nCipher nShield Edge gebruikt, kunt u de modus als volgt wijzigen: 1. Gebruik de Mode-knop om de vereiste modus te markeren. 2. Druk binnen een paar seconden op de Clear-knop en houdt deze enkele seconden ingedrukt. De modus is gewijzigd als het lampje van de nieuwe modus niet meer knippert en blijft branden. Het statuslampje kan een paar seconden onregelmatig knipperen en knippert vervolgens regelmatig als het apparaat klaar is. Als dat niet het geval is, blijft het apparaat in de huidige modus, en brandt het bijbehorende statuslampje.

### <a name="step-34-validate-the-downloaded-package"></a>Stap 3.4: het gedownloade pakket valideren

Deze stap is optioneel maar wordt aanbevolen, zodat u het volgende kunt valideren:

* De KEK die is opgenomen in de toolset is gegenereerd op basis van een legitieme nCipher nShield-HSM.
* De hash van de Security World die is opgenomen in de toolset is gegenereerd in een legitieme nCipher nShield-HSM.
* De sleutel voor sleuteluitwisseling (KEK-sleutel) is niet exporteerbaar.

> [!NOTE]
> Om het gedownloade pakket te valideren, moet de HSM zijn verbonden, zijn ingeschakeld en moet deze een Security World bevatten (zoals de World die u zojuist hebt gemaakt).

Het gedownloade pakket valideren:

1. Voer het script verifykeypackage.py uit door een van de volgende opdrachten te typen, afhankelijk van uw geografische regio of instantie van Azure:

   * Voor Noord-Amerika:

      ```azurepowershell
      "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
      ```
   * Voor Europa:

      ```azurepowershell
      "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
      ```
   * Voor Azië:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
        ```
   * Voor Latijns-Amerika:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
        ```
   * Voor Japan:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
        ```
   * Voor Korea:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-KOREA-1 -w BYOK-SecurityWorld-pkg-KOREA-1
        ```
   * Voor Zuid-Afrika:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SA-1 -w BYOK-SecurityWorld-pkg-SA-1
        ```
   * Voor VAE:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UAE-1 -w BYOK-SecurityWorld-pkg-UAE-1
        ```
   * Voor Australië:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
        ```
   * Voor [Azure Government](https://azure.microsoft.com/features/gov/), waarbij gebruik wordt gemaakt van de instantie US Government van Azure:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
        ```
   * Voor US Government DOD:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USDOD-1 -w BYOK-SecurityWorld-pkg-USDOD-1
        ```
   * Voor Canada:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
        ```
   * Voor Duitsland:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
        ```
   * Voor Germany Public:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
        ```
   * Voor India:

      ```azurepowershell
      "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
      ```
   * Voor Frankrijk:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-FRANCE-1 -w BYOK-SecurityWorld-pkg-FRANCE-1
        ```
   * Voor Verenigd Koninkrijk:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UK-1 -w BYOK-SecurityWorld-pkg-UK-1
        ```
   * Voor Zwitserland:

        ```azurepowershell
        "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SUI-1 -w BYOK-SecurityWorld-pkg-SUI-1
        ```

     > [!TIP]
     > De nCipher nShield-software bevat Python in %NFAST_HOME%\python\bin
     >
     >
2. Controleer of het volgende wordt weergegeven. Dit geeft aan dat de validatie is gelukt: **Result: SUCCESS**

Met dit script wordt de keten van ondertekening gecontroleerd tot de hoofdsleutel van nShield. De hash van deze hoofdsleutel is ingesloten in het script en de waarde moet gelijk zijn aan **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**. U kunt deze waarde ook afzonderlijk bevestigen door naar de [website van nCipher](https://www.ncipher.com) te gaan.

U kunt nu een nieuwe sleutel gaan maken.

### <a name="step-35-create-a-new-key"></a>Stap 3.5: een nieuwe sleutel maken

Genereer een sleutel met behulp van het nCipher nShield-programma **generatekey**.

Voer de volgende opdracht uit om de sleutel te genereren:

```azurepowershell
generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=
```

Volg de volgende instructies om deze opdracht uit te voeren:

* De parameter *protect* moet worden ingesteld op de waarde **module**, zoals weergegeven. Hiermee maakt u een modulair beveiligde sleutel. De BYOK-toolset biedt geen ondersteuning voor met OCS beveiligde sleutels.
* Vervang de waarde van *contosokey* voor de **ident** en **plainname** door een willekeurige tekenreekswaarde. Om de administratieve overhead zo veel mogelijk te beperken en de kans op fouten te verkleinen, raden we u aan om voor beiden dezelfde waarde te gebruiken. De waarde voor **ident** mag alleen cijfers, streepjes en kleine letters bevatten.
* De pubexp is leeg (standaard) in dit voorbeeld, maar u kunt specifieke waarden opgeven. Raadpleeg de [documentatie van nCipher](https://www.entrust.com/-/media/documentation/brochures/entrust-nshield-general-purpose-hsms-br-a4.pdf) voor meer informatie.

Met deze opdracht maakt u een getokeniseerd sleutelbestand in de map %NFAST_KMDATA%\local met een naam die begint met **key_simple_** , gevolgd door de **ident** die is opgegeven in de opdracht. Bijvoorbeeld: **key_simple_contosokey**. Dit bestand bevat een versleutelde sleutel.

Maak van deze Tokenized sleutel een back-up op een veilige locatie.

> [!IMPORTANT]
> Wanneer u de sleutel later overbrengt naar Azure Key Vault, kan Microsoft deze sleutel niet meer naar u exporteren. Het is dus erg belangrijk dat u een veilige back-up maakt van uw sleutel en Security World. Neem contact op met [nCipher](https://www.ncipher.com/about-us/contact-us) voor advies en aanbevolen procedures voor het maken van een back-up van uw sleutel.
>


U kunt uw sleutel nu overbrengen naar Azure Key Vault.

## <a name="step-4-prepare-your-key-for-transfer"></a>Stap 4: uw sleutel voorbereiden voor overdracht

Voer voor deze vierde stap de volgende procedures uit op het offline werkstation.

### <a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>Stap 4.1: een kopie van uw sleutel maken met beperkte machtigingen

Open een nieuwe opdrachtprompt en ga naar de map waar u het zip-bestand voor BYOK hebt uitgepakt. Als u de machtigingen voor uw sleutel wilt beperken, voert u een van de volgende opdrachten uit vanaf een opdrachtprompt, afhankelijk van de geografische regio of de instantie van Azure:

* Voor Noord-Amerika:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-
   ```
* Voor Europa:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
   ```
* Voor Azië:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
   ```
* Voor Latijns-Amerika:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
   ```
* Voor Japan:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
   ```
* Voor Korea:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1
   ```
* Voor Zuid-Afrika:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1
   ```
* Voor VAE:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1
   ```
* Voor Australië:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
   ```
* Voor [Azure Government](https://azure.microsoft.com/features/gov/), waarbij gebruik wordt gemaakt van de instantie US Government van Azure:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
   ```
* Voor US Government DOD:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1
   ```
* Voor Canada:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
   ```
* Voor Duitsland:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
   ```
* Voor Germany Public:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
   ```
* Voor India:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1
   ```
* Voor Frankrijk:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-FRANCE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-FRANCE-1
   ```
* Voor Verenigd Koninkrijk:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1
   ```
* Voor Zwitserland:

   ```azurepowershell
   KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1
   ```

Wanneer u deze opdracht uitvoert, vervangt u *contosokey* door dezelfde waarde die u hebt opgegeven in **Stap 3.5: een nieuwe sleutel maken** van de stap [Uw sleutel genereren](#step-3-generate-your-key).

U wordt gevraagd om uw beheerderskaarten voor de Security World te plaatsen.

Wanneer de opdracht is voltooid, ziet u **Result: SUCCESS** en staat er een kopie van uw sleutel met beperkte machtigingen in het bestand met de naam key_xferacId_\<contosokey>.

U kunt de toegangsbeheerlijsten controleren met behulp van de volgende opdrachten van de nCipher nShield-hulpprogramma's:

* aclprint.py:

   ```cmd
   "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
   ```
* kmfile-dump.exe:

   ```cmd
   "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
   ```
  Wanneer u deze opdrachten uitvoert, vervangt u contosokey door dezelfde waarde die u hebt opgegeven in **Stap 3.5: een nieuwe sleutel maken** van de stap [Uw sleutel genereren](#step-3-generate-your-key).

### <a name="step-42-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>Stap 4.2: uw sleutel versleutelen met behulp van de KEK van Microsoft

Voer een van de volgende opdrachten uit, afhankelijk van uw geografische regio of de instantie van Azure:

* Voor Noord-Amerika:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Voor Europa:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Voor Azië:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Voor Latijns-Amerika:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Voor Japan:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Voor Korea:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Voor Zuid-Afrika:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Voor VAE:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Voor Australië:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Voor [Azure Government](https://azure.microsoft.com/features/gov/), waarbij gebruik wordt gemaakt van de instantie US Government van Azure:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Voor US Government DOD:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Voor Canada:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Voor Duitsland:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Voor Germany Public:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Voor India:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Voor Frankrijk:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-France-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-France-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Voor Verenigd Koninkrijk:

   ```azurepowershell
   KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
   ```
* Voor Zwitserland:

  ```azurepowershell
  KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
  ```


Volg de volgende instructies om deze opdracht uit te voeren:

* Vervang *contosokey* door de id die u hebt gebruikt om de sleutel te genereren in **Stap 3.5: een nieuwe sleutel maken** van de stap [Uw sleutel genereren](#step-3-generate-your-key).
* Vervang *SubscriptionID* door de id van het Azure-abonnement dat uw sleutelkluis bevat. U hebt deze waarde eerder opgehaald, in **Stap 1.2: id van Azure-abonnement ophalen** van de stap [Het online werkstation voorbereiden](#step-1-prepare-your-internet-connected-workstation) .
* Vervang *ContosoFirstHSMKey* door een label dat wordt gebruikt voor de naam van het uitvoerbestand.

Als de opdrachten allemaal zijn voltooid, ziet u **Result: SUCCESS** en staat er in de huidige map een nieuw bestand met de volgende naam: KeyTransferPackage-*ContosoFirstHSMkey*.byok

### <a name="step-43-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>Stap 4.3: uw pakket voor sleuteloverdracht kopiëren naar het online werkstation

Gebruik een USB-station of een ander draagbaar geheugen om het uitvoerbestand van de vorige stap (KeyTransferPackage-ContosoFirstHSMkey.byok) te kopiëren naar uw online werkstation.

## <a name="step-5-transfer-your-key-to-azure-key-vault"></a>Stap 5: uw sleutel overdragen naar Azure Key Vault

Voor deze laatste stap gebruikt u op het online werkstation de cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) om het pakket voor sleuteloverdracht dat u hebt gekopieerd van het offline werkstation te uploaden naar de HSM van Azure Key Vault:

   ```powershell
        Add-AzKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\KeyTransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'
   ```

Als het uploaden is gelukt, ziet u de eigenschappen van de sleutel die u zojuist hebt toegevoegd.

## <a name="next-steps"></a>Volgende stappen

U kunt deze met HSM beveiligde sleutel nu gebruiken in uw sleutelkluis. Zie voor meer informatie deze [vergelijking](https://azure.microsoft.com/pricing/details/key-vault/) van prijzen en functies.