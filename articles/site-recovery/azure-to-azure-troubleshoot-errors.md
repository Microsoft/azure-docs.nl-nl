---
title: Problemen met Azure VM-replicatie in Azure Site Recovery oplossen
description: Los fouten op bij het repliceren van virtuele Azure-machines voor herstel na nood gevallen.
author: rochakm
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/07/2020
ms.author: rochakm
ms.openlocfilehash: 6d61a44e671c43754fa7cccbe8ea8fe54eeba387
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97900413"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>Azure-naar-Azure-VM-replicatiefouten oplossen

In dit artikel wordt beschreven hoe u veelvoorkomende fouten in Azure Site Recovery oplost tijdens de replicatie en het herstel van virtuele Azure-machines (VM) van de ene regio naar een andere. Zie de [ondersteunings matrix voor het repliceren van virtuele Azure-machines](azure-to-azure-support-matrix.md)voor meer informatie over ondersteunde configuraties.

## <a name="azure-resource-quota-issues-error-code-150097"></a>Problemen met Azure-resource quota (fout code 150097)

Zorg ervoor dat uw abonnement is ingeschakeld voor het maken van virtuele Azure-machines in de doel regio die u wilt gebruiken als de regio voor nood herstel (DR). Uw abonnement moet voldoende quota hebben om Vm's te maken met de benodigde grootten. Site Recovery kiest standaard een doel-VM-grootte die gelijk is aan de grootte van de bron-VM. Als de overeenkomende grootte niet beschikbaar is, kiest Site Recovery automatisch de dichtstbijzijnde beschik bare grootte.

Als er geen grootte is die de configuratie van de bron-VM ondersteunt, wordt het volgende bericht weer gegeven:

```Output
Replication couldn't be enabled for the virtual machine <VmName>.
```

### <a name="possible-causes"></a>Mogelijke oorzaken

- Uw abonnements-ID is niet ingeschakeld voor het maken van virtuele machines in de doel regio locatie.
- Uw abonnements-ID is niet ingeschakeld, of heeft onvoldoende quotum, om specifieke VM-grootten in de doel regio locatie te maken.
- Er is geen geschikte VM-grootte gevonden die overeenkomt met de netwerk interface kaart (NIC) van de bron-VM (2) voor de abonnements-ID in de doel regio locatie.

### <a name="fix-the-problem"></a>Het probleem oplossen

Neem contact op met de [ondersteuning voor Azure-facturering](../azure-portal/supportability/resource-manager-core-quotas-request.md) om uw abonnement in te scha kelen voor het maken van vm's van de vereiste grootten op de doel locatie. Voer de mislukte bewerking opnieuw uit.

Als de doel locatie een capaciteits beperking heeft, schakelt u de replicatie naar die locatie uit. Schakel vervolgens replicatie naar een andere locatie in waar uw abonnement voldoende quota heeft om Vm's van de vereiste grootten te maken.

## <a name="trusted-root-certificates-error-code-151066"></a>Vertrouwde basis certificaten (fout code 151066)

Als niet alle meest recente vertrouwde basis certificaten aanwezig zijn op de VM, kan de taak voor het inschakelen van replicatie voor Site Recovery mislukken. Verificatie en autorisatie van Site Recovery service aanroepen van de virtuele machine mislukt zonder deze certificaten.

Als de taak replicatie inschakelen mislukt, wordt het volgende bericht weer gegeven:

```Output
Site Recovery configuration failed.
```

### <a name="possible-cause"></a>Mogelijke oorzaak

De vertrouwde basis certificaten die vereist zijn voor verificatie en verificatie zijn niet aanwezig op de virtuele machine.

### <a name="fix-the-problem"></a>Het probleem oplossen

#### <a name="windows"></a>Windows

Voor een virtuele machine waarop het Windows-besturings systeem wordt uitgevoerd, installeert u de meest recente Windows-updates zodat alle vertrouwde basis certificaten aanwezig zijn op de virtuele machine. Volg het typische proces voor het beheren van Windows Update-of certificaat updates in uw organisatie om de meest recente basis certificaten en de bijgewerkte lijst met ingetrokken certificaten op de Vm's op te halen.

- Als u zich in een niet-verbonden omgeving bevindt, volgt u het standaard Windows Update-proces in uw organisatie om de certificaten op te halen.
- Als de vereiste certificaten niet aanwezig zijn op de virtuele machine, zijn de aanroepen naar de Site Recovery-service om veiligheids redenen mislukt.

Ga naar in `login.microsoftonline.com` een browser in uw virtuele machine om te controleren of het probleem is opgelost.

Zie [vertrouwde hoofd mappen en niet-toegestane certificaten configureren](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn265983(v=ws.11))voor meer informatie.

#### <a name="linux"></a>Linux

Volg de richt lijnen van de Distributor van de versie van uw Linux-besturings systeem om de meest recente vertrouwde basis certificaten en de meest recente certificaatintrekkingslijst op de VM te verkrijgen.

Omdat SUSE Linux symbolische koppelingen of symlinks gebruikt om een certificaat lijst te onderhouden, volgt u deze stappen:

1. Meld u aan als **hoofd** gebruiker. Het hash-symbool ( `#` ) is de standaard opdracht prompt.

1. Als u de map wilt wijzigen, voert u deze opdracht uit:

   `cd /etc/ssl/certs`

1. Controleer of het certificaat van de Symantec-basis certificerings instantie aanwezig is:

   `ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

   - Als het certificaat van de Symantec-basis certificerings instantie niet wordt gevonden, voert u de volgende opdracht uit om het bestand te downloaden. Controleer op eventuele fouten en volg de aanbevolen acties voor netwerk fouten.

     `wget https://docs.broadcom.com/docs-and-downloads/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

1. Controleer of het Baltimore basis-CA-certificaat aanwezig is:

   `ls Baltimore_CyberTrust_Root.pem`

   - Als het basis-CA-certificaat van de Baltimore niet wordt gevonden, voert u deze opdracht uit om het certificaat te downloaden:

     `wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem`

1. Controleer of het DigiCert_Global_Root_CA certificaat aanwezig is:

   `ls DigiCert_Global_Root_CA.pem`

    - Als de DigiCert_Global_Root_CA niet wordt gevonden, voert u de volgende opdrachten uit om het certificaat te downloaden:

      ```shell
      wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt

      openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem
      ```

1. Voer het opnieuw hash-script uit om de onderwerp-hashes van het certificaat voor de zojuist gedownloade certificaten bij te werken:

   `c_rehash`

1. Als u wilt controleren of de onderwerp-hashes als symlinks zijn gemaakt voor de certificaten, voert u de volgende opdrachten uit:

   ```shell
   ls -l | grep Baltimore
   ```

   ```Output
   lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem

   -rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem
   ```

   ```shell
   ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5
   ```

   ```Output
   -rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem

   lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem
   ```

   ```shell
   ls -l | grep DigiCert_Global_Root
   ```

   ```Output
   lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem

   -rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem
   ```

1. Maak een kopie van het bestand _VeriSign_Class_3_Public_Primary_Certification_Authority_G5. pem_ met de naam _b204d74a. 0_:

   `cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0`

1. Maak een kopie van het bestand _Baltimore_CyberTrust_Root. pem_ met de naam _653b494a. 0_:

   `cp Baltimore_CyberTrust_Root.pem 653b494a.0`

1. Maak een kopie van het bestand _DigiCert_Global_Root_CA. pem_ met de naam _3513523f. 0_:

   `cp DigiCert_Global_Root_CA.pem 3513523f.0`

1. Controleer of de bestanden aanwezig zijn:

   ```shell
   ls -l 653b494a.0 b204d74a.0 3513523f.0
   ```

   ```Output
   -rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0

   -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0

   -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0
   ```

## <a name="outbound-urls-or-ip-ranges-error-code-151037-or-151072"></a>Uitgaande Url's of IP-adresbereiken (fout code 151037 of 151072)

Voor een goede werking van Site Recovery replicatie is uitgaande verbinding met specifieke Url's vereist van de virtuele machine. Als uw virtuele machine zich achter een firewall bevindt of gebruikmaakt van regels voor netwerk beveiligings groepen (NSG) om de uitgaande connectiviteit te beheren, kunt u een van deze problemen tegen komen. Hoewel we de ondersteuning van uitgaande toegang via Url's blijven ondersteunen, wordt het gebruik van een acceptatie lijst met IP-adresbereiken niet meer ondersteund.

#### <a name="possible-causes"></a>Mogelijke oorzaken

- Er kan geen verbinding tot stand worden gebracht met Site Recovery-eind punten vanwege een probleem met het omzetten van een Domain Name System (DNS).
- Dit probleem komt vaker voor tijdens het opnieuw beveiligen van de virtuele machine, maar de DNS-server is niet bereikbaar vanuit de regio voor nood herstel (DR).

#### <a name="fix-the-problem"></a>Het probleem oplossen

Als u aangepaste DNS gebruikt, moet u ervoor zorgen dat de DNS-server toegankelijk is vanuit het gebied voor nood herstel.

Controleren of de virtuele machine gebruikmaakt van een aangepaste DNS-instelling:

1. Open **virtuele machines** en selecteer de VM.
1. Navigeer naar de **instellingen** van de virtuele machines en selecteer **netwerken**.
1. Selecteer in **virtueel netwerk/subnet** de koppeling om de resource pagina van het virtuele netwerk te openen.
1. Ga naar **instellingen** en selecteer **DNS-servers**.

Probeer toegang te krijgen tot de DNS-server vanaf de virtuele machine. Als de DNS-server niet toegankelijk is, maakt u deze toegankelijk door een failover uit te voeren voor de DNS-server of door de regel van de site te maken tussen DR-netwerk en DNS.

> [!NOTE]
> Als u privé-eind punten gebruikt, moet u ervoor zorgen dat de virtuele machines de privé-DNS-records kunnen omzetten.

:::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com-fout.":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Probleem 2: configuratie van Site Recovery is mislukt (151196)

#### <a name="possible-cause"></a>Mogelijke oorzaak

Er kan geen verbinding tot stand worden gebracht met Microsoft 365 authenticatie-en identiteits IP4-eind punten.

#### <a name="fix-the-problem"></a>Het probleem oplossen

Azure Site Recovery vereiste toegang tot Microsoft 365 IP-bereiken voor verificatie.
Als u Azure Network Security Group (NSG) regels/firewall proxy gebruikt voor het beheren van uitgaande netwerk connectiviteit op de virtuele machine, moet u de NSG-regel op basis van [Azure Active Directory (Aad)](../virtual-network/network-security-groups-overview.md#service-tags) gebruiken om toegang tot Aad toe te staan. Op IP-adres gebaseerde NSG-regels worden niet meer ondersteund.

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Probleem 3: configuratie van Site Recovery is mislukt (151197)

#### <a name="possible-cause"></a>Mogelijke oorzaak

Er kan geen verbinding tot stand worden gebracht met Azure Site Recovery service-eind punten.

#### <a name="fix-the-problem"></a>Het probleem oplossen

Als u Azure Network Security Group (NSG) regels/firewall proxy gebruikt voor het beheren van uitgaande netwerk connectiviteit op de virtuele machine, moet u de service tags gebruiken. Het gebruik van een lijst met toegestane IP-adressen via Nsg's voor Azure Site Recovery wordt niet meer ondersteund.

### <a name="issue-4-replication-fails-when-network-traffic-uses-on-premises-proxy-server-151072"></a>Probleem 4: de replicatie mislukt wanneer het netwerk verkeer gebruikmaakt van een on-premises proxy server (151072)

#### <a name="possible-cause"></a>Mogelijke oorzaak

De aangepaste proxy instellingen zijn ongeldig en de Mobility Service-agent heeft de proxy-instellingen niet automatisch gedetecteerd vanuit Internet Explorer (IE).

#### <a name="fix-the-problem"></a>Het probleem oplossen

1. De Mobility Service-agent detecteert de proxy-instellingen van Internet Explorer in Windows en `/etc/environment` op Linux.
1. Als u liever proxy alleen voor de Mobility-service wilt instellen, kunt u de proxy gegevens opgeven in _ProxyInfo. conf_ op:

   - **Linux**: `/usr/local/InMage/config/`
   - **Windows**: `C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. De _ProxyInfo. conf_ moet de proxy-instellingen in de volgende _ini_ -indeling hebben.

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> De Mobility Service-agent ondersteunt alleen **niet-geverifieerde proxy's**.

### <a name="more-information"></a>Meer informatie

Volg de richt lijnen in [over netwerken in azure naar Azure-replicatie](azure-to-azure-about-networking.md)om de [vereiste url's](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) of de [vereiste IP-bereiken](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)op te geven.

## <a name="disk-not-found-in-vm-error-code-150039"></a>De schijf is niet gevonden in de virtuele machine (fout code 150039)

Een nieuwe schijf die is gekoppeld aan de virtuele machine moet worden geïnitialiseerd. Als de schijf niet wordt gevonden, wordt het volgende bericht weer gegeven:

```Output
Azure data disk <DiskName> <DiskURI> with logical unit number <LUN> <LUNValue> was not mapped to a corresponding disk being reported from within the VM that has the same LUN value.
```

### <a name="possible-causes"></a>Mogelijke oorzaken

- Er is een nieuwe gegevens schijf aan de virtuele machine gekoppeld, maar deze is niet geïnitialiseerd.
- De gegevens schijf in de virtuele machine rapporteert niet de Logical Unit Number (LUN)-waarde waarmee de schijf is gekoppeld aan de virtuele machine.

### <a name="fix-the-problem"></a>Het probleem oplossen

Zorg ervoor dat de gegevens schijven zijn geïnitialiseerd en voer de bewerking vervolgens opnieuw uit.

- **Windows**: [een nieuwe schijf koppelen en initialiseren](../virtual-machines/windows/attach-managed-disk-portal.md).
- **Linux**: [Initialiseer een nieuwe gegevens schijf in Linux](../virtual-machines/linux/add-disk.md).

Neem contact op met de ondersteuning als het probleem zich blijft voordoen.

## <a name="multiple-disks-available-for-protection-error-code-153039"></a>Meerdere schijven beschikbaar voor beveiliging (fout code 153039)

### <a name="possible-causes"></a>Mogelijke oorzaken

- Er zijn onlangs een of meer schijven aan de virtuele machine toegevoegd na de beveiliging.
- Een of meer schijven zijn geïnitialiseerd na de beveiliging van de virtuele machine.

### <a name="fix-the-problem"></a>Het probleem oplossen

Als u de replicatie status van de virtuele machine weer in orde wilt maken, kunt u kiezen of u de schijven wilt beveiligen of de waarschuwing wilt negeren.

#### <a name="to-protect-the-disks"></a>De schijven beveiligen

1. Ga naar **gerepliceerde items**  >  _VM-naam_  >  **schijven**.
1. Selecteer de niet-beveiligde schijf en selecteer vervolgens **replicatie inschakelen**:

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/add-disk.png" alt-text="Schakel replicatie in op VM-schijven.":::

#### <a name="to-dismiss-the-warning"></a>De waarschuwing negeren

1. Ga naar de VM-naam van de **gerepliceerde items**  >  .
1. Selecteer de waarschuwing in het gedeelte **overzicht** en selecteer vervolgens **OK**.

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png" alt-text="Waarschuwing nieuwe schijf negeren.":::

## <a name="vm-removed-from-vault-completed-with-information-error-code-150225"></a>De virtuele machine die is verwijderd uit de kluis is voltooid met informatie (fout code 150225)

Wanneer Site Recovery de virtuele machine beveiligt, worden er koppelingen gemaakt op de virtuele bron machine. Wanneer u de beveiliging verwijdert of replicatie uitschakelt, Site Recovery deze koppelingen verwijderen als onderdeel van de opschoon taak. Als de virtuele machine een resource vergrendeling heeft, wordt de opschoon taak voltooid met de informatie. De informatie geeft aan dat de virtuele machine is verwijderd uit de Recovery Services kluis, maar dat een aantal van de verouderde koppelingen niet kan worden opgeruimd op de bron machine.

U kunt deze waarschuwing negeren als u deze virtuele machine nooit opnieuw wilt beveiligen. Als u deze virtuele machine later moet beveiligen, volgt u de stappen in deze sectie om de koppelingen op te schonen.

> [!WARNING]
> Als u het opschonen niet doet:
>
> - Wanneer u replicatie inschakelt met behulp van de Recovery Services kluis, wordt de virtuele machine niet weer gegeven.
> - Als u de **virtuele machine** probeert te beveiligen met behulp van de  >  **instellingen** voor  >  **herstel na nood** geval, mislukt de bewerking met de bericht **replicatie kan niet worden ingeschakeld vanwege de bestaande verlopen bron koppelingen op de VM**.

### <a name="fix-the-problem"></a>Het probleem oplossen

> [!NOTE]
> Site Recovery de virtuele bron machine niet verwijdert of op enigerlei wijze beïnvloed tijdens het uitvoeren van deze stappen.

1. Verwijder de vergren deling van de virtuele machine of VM-resource groep. In de volgende afbeelding moet de resource vergrendeling op de VM met de naam bijvoorbeeld `MoveDemo` worden verwijderd:

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Vergren deling van virtuele machine verwijderen.":::

1. Down load het script om [een verouderde site Recovery configuratie te verwijderen](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Voer het script uit, _Cleanup-stale-asr-config-Azure-VM.ps1_. Geef de **abonnements-id**, **VM-resource groep** en **VM-naam** op als para meters.
1. Als u wordt gevraagd om Azure-referenties, geeft u deze op. Controleer vervolgens of het script zonder storingen wordt uitgevoerd.

## <a name="replication-not-enabled-on-vm-with-stale-resources-error-code-150226"></a>Replicatie is niet ingeschakeld op de virtuele machine met verouderde resources (fout code 150226)

### <a name="possible-causes"></a>Mogelijke oorzaken

De virtuele machine heeft een verouderde configuratie van de vorige Site Recovery beveiliging.

Een verouderde configuratie kan optreden op een Azure-VM als u replicatie voor de Azure-VM hebt ingeschakeld met behulp van Site Recovery en vervolgens:

- U hebt replicatie uitgeschakeld, maar de bron-VM had een resource vergrendeling.
- U hebt de Site Recovery kluis verwijderd zonder dat de replicatie op de virtuele machine expliciet wordt uitgeschakeld.
- U hebt de resource groep met de Site Recovery kluis verwijderd zonder dat de replicatie op de virtuele machine expliciet wordt uitgeschakeld.

### <a name="fix-the-problem"></a>Het probleem oplossen

> [!NOTE]
> Site Recovery de virtuele bron machine niet verwijdert of op enigerlei wijze beïnvloed tijdens het uitvoeren van deze stappen.

1. Verwijder de vergren deling van de virtuele machine of VM-resource groep. In de volgende afbeelding moet de resource vergrendeling op de VM met de naam bijvoorbeeld `MoveDemo` worden verwijderd:

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Vergren deling van virtuele machine verwijderen.":::

1. Down load het script om [een verouderde site Recovery configuratie te verwijderen](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Voer het script uit, _Cleanup-stale-asr-config-Azure-VM.ps1_. Geef de **abonnements-id**, **VM-resource groep** en **VM-naam** op als para meters.
1. Als u wordt gevraagd om Azure-referenties, geeft u deze op. Controleer vervolgens of het script zonder storingen wordt uitgevoerd.

## <a name="cant-select-vm-or-resource-group-in-enable-replication-job"></a>VM of resource groep kan niet worden geselecteerd in replicatie taak inschakelen

### <a name="issue-1-the-resource-group-and-source-vm-are-in-different-locations"></a>Probleem 1: de resource groep en de bron-VM bevinden zich op verschillende locaties

Site Recovery moeten de resource groep en de virtuele machines van de bron regio momenteel op dezelfde locatie zijn. Als dat niet het geval is, kunt u de virtuele machine of resource groep niet vinden wanneer u probeert beveiliging toe te passen.

Als tijdelijke oplossing kunt u de replicatie van de virtuele machine inschakelen in plaats van de Recovery Services kluis. Ga naar de eigenschappen van de **bron-VM**  >    >  **nood herstel** en schakel de replicatie in.

### <a name="issue-2-the-resource-group-isnt-part-of-the-selected-subscription"></a>Probleem 2: de resource groep maakt geen deel uit van het geselecteerde abonnement

U kunt de resource groep mogelijk niet vinden op het moment van de beveiliging als de resource groep geen deel uitmaakt van het geselecteerde abonnement. Zorg ervoor dat de resource groep deel uitmaakt van het abonnement dat u gebruikt.

### <a name="issue-3-stale-configuration"></a>Probleem 3: verouderde configuratie

Mogelijk ziet u de virtuele machine die u wilt inschakelen voor replicatie als er een verouderde Site Recovery configuratie bestaat op de virtuele machine van Azure. Dit probleem kan optreden als u replicatie voor de Azure-VM hebt ingeschakeld met behulp van Site Recovery en vervolgens:

- U hebt de Site Recovery kluis verwijderd zonder dat de replicatie op de virtuele machine expliciet wordt uitgeschakeld.
- U hebt de resource groep met de Site Recovery kluis verwijderd zonder dat de replicatie op de virtuele machine expliciet wordt uitgeschakeld.
- U hebt replicatie uitgeschakeld, maar de bron-VM had een resource vergrendeling.

### <a name="fix-the-problem"></a>Het probleem oplossen

> [!NOTE]
> Zorg ervoor dat u de module bijwerkt `AzureRM.Resources` voordat u het script gebruikt dat in deze sectie wordt genoemd. Site Recovery de virtuele bron machine niet verwijdert of op enigerlei wijze beïnvloed tijdens het uitvoeren van deze stappen.

1. Verwijder de vergren deling, indien van toepassing, van de virtuele machine of VM-resource groep. In de volgende afbeelding moet de resource vergrendeling op de VM met de naam bijvoorbeeld `MoveDemo` worden verwijderd:

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="Vergren deling van virtuele machine verwijderen.":::

1. Down load het script om [een verouderde site Recovery configuratie te verwijderen](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. Voer het script uit, _Cleanup-stale-asr-config-Azure-VM.ps1_. Geef de **abonnements-id**, **VM-resource groep** en **VM-naam** op als para meters.
1. Als u wordt gevraagd om Azure-referenties, geeft u deze op. Controleer vervolgens of het script zonder storingen wordt uitgevoerd.

## <a name="unable-to-select-a-vm-for-protection"></a>Kan geen virtuele machine selecteren voor beveiliging

### <a name="possible-cause"></a>Mogelijke oorzaak

De virtuele machine heeft een uitbrei ding die is geïnstalleerd met een mislukte of niet-reagerende status

### <a name="fix-the-problem"></a>Het probleem oplossen

Ga naar extensies voor **virtuele machines**-  >  **instellingen**  >   en controleer op uitbrei dingen met de status mislukt. Verwijder een mislukte uitbrei ding en probeer het opnieuw om de virtuele machine te beveiligen.

## <a name="vm-provisioning-state-isnt-valid-error-code-150019"></a>De inrichtings status van de virtuele machine is niet geldig (fout code 150019)

Als u de replicatie op de virtuele machine wilt inschakelen, moet de inrichtings status zijn **geslaagd**. Volg deze stappen om de inrichtings status te controleren:

1. Selecteer in de Azure Portal de **resource Verkenner** van **alle services**.
1. Vouw de lijst **abonnementen** uit en selecteer uw abonnement.
1. Vouw de lijst **ResourceGroups** uit en selecteer de resource groep van de virtuele machine.
1. Vouw de lijst met **resources** uit en selecteer de virtuele machine.
1. Controleer het veld **provisioningState** in de weer gave instantie aan de rechter kant.

### <a name="fix-the-problem"></a>Het probleem oplossen

- Als de **provisioningState** is **mislukt**, neemt u contact op met de ondersteuning met details over het oplossen van problemen.
- Als de **provisioningState** wordt **bijgewerkt**, wordt er mogelijk een andere uitbrei ding geïmplementeerd. Controleer of er actieve bewerkingen zijn op de VM, wacht totdat ze zijn voltooid en voer de mislukte Site Recovery taak opnieuw uit om replicatie in te scha kelen.

## <a name="unable-to-select-target-vm"></a>Kan de doel-VM niet selecteren

### <a name="issue-1-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>Probleem 1: VM is gekoppeld aan een netwerk dat al is toegewezen aan een doelnet netwerk

Als de bron-VM deel uitmaakt van een virtueel netwerk en er al een andere virtuele machine van hetzelfde virtuele netwerk is toegewezen aan een netwerk in de doel resource groep, is de vervolg keuzelijst netwerk selectie niet beschikbaar (grijs weer gegeven).

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png" alt-text="De lijst met netwerk selectie is niet beschikbaar.":::

### <a name="issue-2-you-previously-protected-the-vm-and-then-you-disabled-the-replication"></a>Probleem 2: u hebt de virtuele machine eerder beveiligd en vervolgens de replicatie uitgeschakeld

Als u de replicatie van een virtuele machine uitschakelt, wordt de netwerk toewijzing niet verwijderd. De toewijzing moet worden verwijderd uit de Recovery Services kluis waar de virtuele machine is beveiligd. Selecteer de **Recovery Services kluis** en ga naar   >  **site Recovery-infra structuur** beheren  >  **voor netwerk toewijzing van virtuele Azure-machines**  >  .

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png" alt-text="Netwerk toewijzing verwijderen.":::

Het doelnet-netwerk dat tijdens de installatie van nood herstel is geconfigureerd, kan na de eerste installatie worden gewijzigd en nadat de virtuele machine is beveiligd. Als u **netwerk toewijzing wilt wijzigen** , selecteert u de netwerk naam:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png" alt-text="Netwerk toewijzing wijzigen.":::


## <a name="com-or-vss-error-code-151025"></a>COM+ of VSS (fout code 151025)

Wanneer de COM+-of Volume Shadow Copy Service (VSS)-fout optreedt, wordt het volgende bericht weer gegeven:

```Output
Site Recovery extension failed to install.
```

### <a name="possible-causes"></a>Mogelijke oorzaken

- De COM+-systeem toepassings service is uitgeschakeld.
- De Volume Shadow Copy Service is uitgeschakeld.

### <a name="fix-the-problem"></a>Het probleem oplossen

Stel de COM+-systeem toepassing en Volume Shadow Copy Service in op automatische of hand matige opstart modus.

1. Open de console Services in Windows.
1. Zorg ervoor dat de COM+-systeem toepassing en Volume Shadow Copy Service niet zijn ingesteld op **uitgeschakeld** als **opstart type**.

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/com-error.png" alt-text="Controleer het opstart type van de COM plus-systeem toepassing en het Volume Shadow Copy Service.":::

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Niet-ondersteunde beheerde schijf grootte (fout code 150172)

Wanneer deze fout optreedt, wordt het volgende bericht weer gegeven:

```Output
Protection couldn't be enabled for the virtual machine as it has <DiskName> with size <DiskSize> that is lesser than the minimum supported size 1024 MB.
```

### <a name="possible-cause"></a>Mogelijke oorzaak

De schijf is kleiner dan de ondersteunde grootte van 1024 MB.

### <a name="fix-the-problem"></a>Het probleem oplossen

Zorg ervoor dat de schijf grootte binnen het ondersteunde bereik ligt en voer de bewerking opnieuw uit.

## <a name="protection-not-enabled-when-grub-uses-device-name-error-code-151126"></a>De beveiliging is niet ingeschakeld wanneer GRUB gebruikmaakt van de apparaatnaam (fout code 151126)

### <a name="possible-causes"></a>Mogelijke oorzaken

De configuratie bestanden (_/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/boot/grub2/grub.cfg_ of _/etc/default/grub_) van de linux-grub (Unified Bootloader) kunnen de werkelijke apparaatnamen opgeven in plaats van de UUID-waarden (Universally Unique Identifier) voor de `root` `resume` para meters en. Site Recovery vereist UUID, omdat de apparaatnamen kunnen veranderen. Bij het opnieuw opstarten is het mogelijk dat een virtuele machine niet dezelfde naam heeft als de failover, waardoor er problemen ontstaan.

De volgende voor beelden zijn regels uit GRUB-bestanden waarbij apparaatnamen worden weer gegeven in plaats van de vereiste UUID:

- Bestands _/boot/grub2/grub.cfg_:

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- Bestand: _/boot/grub/menu.lst_

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

### <a name="fix-the-problem"></a>Het probleem oplossen

Vervang elke naam van het apparaat door de bijbehorende UUID:

1. Zoek de UUID van het apparaat door de opdracht uit te voeren `blkid <device name>` . Bijvoorbeeld:

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Vervang de naam van het apparaat door de bijbehorende UUID in de indelingen `root=UUID=<UUID>` en `resume=UUID=<UUID>` . De regel van _/boot/grub/menu.lst_ ziet er bijvoorbeeld als volgt uit:

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. Voer de beveiliging opnieuw uit.

## <a name="protection-failed-because-grub-device-doesnt-exist-error-code-151124"></a>De beveiliging is mislukt omdat het GRUB-apparaat niet bestaat (fout code 151124)

### <a name="possible-cause"></a>Mogelijke oorzaak

De GRUB-configuratie bestanden (_/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/boot/grub2/grub.cfg_ of _/etc/default/grub_) kunnen de para meters bevatten `rd.lvm.lv` of `rd_LVM_LV` . Met deze para meters worden de LVM-apparaten (Logical Volume Manager) geïdentificeerd die tijdens de opstart tijd moeten worden gedetecteerd. Als deze LVM-apparaten niet bestaan, wordt het beveiligde systeem zelf niet gestart en blijft het in het opstart proces zitten. Hetzelfde probleem wordt ook weer gegeven met de failover-VM. Hier volgen enkele voor beelden:

- Bestand: _/boot/grub2/grub.cfg_ op RHEL7:

  `linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel=128M\@64M rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet LANG=en_US.UTF-8`

- Bestand: _/etc/default/grub_ op RHEL7:

  `GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet`

- Bestand: _/boot/grub/menu.lst_ op RHEL6:

  `kernel /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto rd_LVM_LV=rootvg/lv_root  KEYBOARDTYPE=pc KEYTABLE=us rd_LVM_LV=rootvg/lv_swap rd_NO_DM rhgb quiet`

In elk voor beeld moet GRUB twee LVM-apparaten detecteren met de namen `root` en `swap` van de volume groep `rootvg` .

### <a name="fix-the-problem"></a>Het probleem oplossen

Als het LVM-apparaat niet bestaat, maakt u het of verwijdert u de overeenkomstige para meters uit de GRUB-configuratie bestanden. Probeer het vervolgens opnieuw om de beveiliging in te scha kelen.

## <a name="mobility-service-update-finished-with-warnings-error-code-151083"></a>De update van de Mobility-service is voltooid met waarschuwingen (fout code 151083)

De Site Recovery Mobility-service heeft veel onderdelen, een van de het filter stuur programma genoemd. Het filter stuur programma wordt alleen in het systeem geheugen geladen tijdens het opnieuw opstarten van het systeem. Telkens wanneer een update van de Mobility-service wijzigingen in het filter stuur programma bevat, wordt de computer bijgewerkt, maar wordt er nog steeds een waarschuwing weer gegeven dat het opnieuw opstarten van sommige oplossingen is vereist. De waarschuwing wordt weer gegeven omdat de correcties van het filter stuur programma alleen van kracht worden wanneer het nieuwe filter stuur programma wordt geladen. dit gebeurt alleen tijdens het opnieuw opstarten.

> [!NOTE]
> Dit is slechts een waarschuwing. De bestaande replicatie blijft werken, zelfs na de nieuwe update van de agent. U kunt ervoor kiezen om opnieuw op te starten wanneer u de voor delen van het nieuwe filter stuur programma wilt, maar het oude filter stuur programma blijft actief als u de computer niet opnieuw opstart.
>
> Naast het filter stuur programma zijn de voor delen van andere verbeteringen en oplossingen in de update van de Mobility-service van kracht zonder dat de computer opnieuw hoeft te worden opgestart.

## <a name="protection-not-enabled-if-replica-managed-disk-exists"></a>De beveiliging is niet ingeschakeld als er een door de replica beheerde schijf bestaat

Deze fout treedt op wanneer de met de replica beheerde schijf al bestaat, zonder de verwachte Tags in de doel resource groep.

### <a name="possible-cause"></a>Mogelijke oorzaak

Dit probleem kan optreden als de virtuele machine eerder is beveiligd en wanneer de replicatie is uitgeschakeld, is de replica schijf niet verwijderd.

### <a name="fix-the-problem"></a>Het probleem oplossen

Verwijder de replica schijf die in het fout bericht wordt vermeld en voer de mislukte beveiligings taak opnieuw uit.

## <a name="enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137"></a>Het inschakelen van de beveiliging is mislukt omdat de hoofd schijf niet kan worden gevonden met het installatie programma (fout code 151137)

Deze fout treedt op voor Linux-machines waarop de besturingssysteem schijf is versleuteld met behulp van Azure Disk Encryption (ADE). Dit is een geldig probleem in versie 9,35 van de agent.

### <a name="possible-causes"></a>Mogelijke oorzaken

Het installatie programma kan de hoofd schijf die als host fungeert voor het hoofd bestand-systeem niet vinden.

### <a name="fix-the-problem"></a>Het probleem oplossen

Volg de onderstaande stappen om dit probleem op te lossen-

1. Zoek de agent-bits onder de Directory _/var/lib/waagent_ op RHEL-en CentOS-machines met behulp van de onderstaande opdracht: <br>

    `# find /var/lib/ -name Micro\*.gz`

   Verwachte uitvoer:

    `/var/lib/waagent/Microsoft.Azure.RecoveryServices.SiteRecovery.LinuxRHEL7-1.0.0.9139/UnifiedAgent/Microsoft-ASR_UA_9.35.0.0_RHEL7-64_GA_30Jun2020_release.tar.gz`

2. Maak een nieuwe map en wijzig de Directory in deze nieuwe map.
3. Pak het agent bestand dat u in de eerste stap hebt gevonden, uit met behulp van de onderstaande opdracht:

    `tar -xf <Tar Ball File>`

4. Open het bestand _prereq_check_installer.jsop_ en verwijder de volgende regels. Sla het bestand op.

    ```
       {
          "CheckName": "SystemDiskAvailable",
          "CheckType": "MobilityService"
       },
    ```
5. Roep het installatie programma aan met de opdracht: <br>

    `./install -d /usr/local/ASR -r MS -q -v Azure`
6. Als het installatie programma is geslaagd, voert u de taak replicatie inschakelen opnieuw uit.

## <a name="next-steps"></a>Volgende stappen

[Virtuele Azure-machines repliceren naar een andere Azure-regio](azure-to-azure-how-to-enable-replication.md)