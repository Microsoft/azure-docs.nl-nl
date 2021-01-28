---
title: De DPM-server voorbereiden op het maken van back-ups van workloads
description: In dit artikel wordt beschreven hoe u back-ups van System Center-Data Protection Manager (DPM) kunt voorbereiden op Azure met behulp van de Azure Backup-service.
ms.topic: conceptual
ms.date: 06/11/2020
ms.openlocfilehash: 823b23d99959df5f2eed20cf4136254e1702fe89
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98985628"
---
# <a name="prepare-to-back-up-workloads-to-azure-with-system-center-dpm"></a>Voorbereiden op back-ups van workloads naar Azure met System Center DPM

In dit artikel wordt uitgelegd hoe u back-ups van System Center-Data Protection Manager (DPM) kunt voorbereiden op Azure met behulp van de Azure Backup-service.

Het artikel bevat het volgende:

- Een overzicht van de implementatie van DPM met Azure Backup.
- Vereisten en beperkingen voor het gebruik van Azure Backup met DPM.
- Stappen voor het voorbereiden van Azure, inclusief het instellen van een Recovery Services back-upkluis en het wijzigen van het type Azure-opslag voor de kluis.
- Stappen voor het voorbereiden van de DPM-server, inclusief het downloaden van kluis referenties, het installeren van de Azure Backup Agent en het registreren van de DPM-server in de kluis.
- Tips voor het oplossen van veelvoorkomende fouten.

## <a name="why-back-up-dpm-to-azure"></a>Waarom back-ups maken van DPM naar Azure?

[System Center DPM](/system-center/dpm/dpm-overview) maakt back-ups van bestands-en toepassings gegevens. DPM communiceert als volgt met Azure Backup:

- **DPM uitgevoerd op een fysieke server of op een on-premises VM** : u kunt een back-up maken van gegevens naar een back-upkluis in azure, naast schijf-en tape back-ups.
- **DPM uitgevoerd op een virtuele machine van Azure** : van System Center 2012 R2 met update 3 of hoger, kunt u DPM implementeren op een virtuele Azure-machine. U kunt een back-up maken van gegevens op Azure-schijven die zijn gekoppeld aan de virtuele machine of Azure Backup gebruiken om een back-up te maken van de gegevens in een back-upkluis.

De zakelijke voor delen van het maken van back-ups van DPM-servers naar Azure zijn:

- Voor on-premises DPM biedt Azure Backup een alternatieve implementatie op de lange termijn op tape.
- Voor DPM die wordt uitgevoerd op een virtuele Azure-machine, kunt u met Azure Backup opslag van de Azure-schijf offloaden. Door oudere gegevens op te slaan in een back-upkluis kunt u uw bedrijf opschalen door nieuwe gegevens op schijf op te slaan.

## <a name="prerequisites-and-limitations"></a>Vereisten en beperkingen

**Instelling** | **Vereiste**
--- | ---
DPM op een virtuele Azure-machine | System Center 2012 R2 met DPM 2012 R2 update pakket 3 of hoger.
DPM op een fysieke server | System Center 2012 SP1 of hoger; System Center 2012 R2.
DPM op een Hyper-V-VM | System Center 2012 SP1 of hoger; System Center 2012 R2.
DPM op een virtuele VMware-machine | System Center 2012 R2 met update pakket 5 of hoger.
Onderdelen | Op de DPM-server moet Windows Power shell en .NET Framework 4,5 zijn geïnstalleerd.
Ondersteunde apps | [Ontdek](/system-center/dpm/dpm-protection-matrix) waar DPM een back-up van kan maken.
Ondersteunde bestands typen | U kunt een back-up van deze bestands typen maken met Azure Backup:<br> <li>Versleuteld (alleen volledige back-ups)<li> Gecomprimeerd (incrementele back-ups worden ondersteund) <li> Sparse (incrementele back-ups worden ondersteund)<li> Gecomprimeerd en verspreid (behandeld als sparse)
Niet-ondersteunde bestands typen | <li>Servers op hoofdletter gevoelige bestands systemen<li> vaste koppelingen (overgeslagen)<li> reparsepunten (overgeslagen)<li> versleuteld en gecomprimeerd (overgeslagen)<li> versleuteld en verspreid (overgeslagen)<li> Gecomprimeerde stroom<li> stroom parseren
Lokale opslag | Elke machine waarvan u een back-up wilt maken, moet lokale vrije opslag hebben die ten minste 5% van de grootte van de gegevens is waarvan een back-up wordt gemaakt. Voor het maken van een back-up van 100 GB aan gegevens is bijvoorbeeld mini maal 5 GB beschik bare ruimte op de Scratch locatie vereist.
Kluis opslag | Er is geen limiet voor de hoeveelheid gegevens waarvan u een back-up kunt maken naar een Azure Backup kluis, maar de grootte van een gegevens bron (bijvoorbeeld een virtuele machine of data base) mag niet groter zijn dan 54.400 GB.
Azure ExpressRoute | U kunt een back-up maken van uw gegevens via Azure ExpressRoute met open bare peering (beschikbaar voor oude circuits) en micro soft-peering. Back-up via privé-peering wordt niet ondersteund.<br/><br/> **Met open bare peering**: Zorg ervoor dat u toegang hebt tot de volgende domeinen/adressen:<br/><br/> Adres<br> `www.msftncsi.com` <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>`www.msftconnecttest.com`<br><br>IP-adressen<br>  20.190.128.0/18 <br>  40.126.0.0/18<br> <br/>Selecteer **bij micro soft-peering** de volgende services/regio's en relevante Community-waarden:<br/><br/>-Azure Active Directory (12076:5060)<br/><br/>-Microsoft Azure regio (op basis van de locatie van uw Recovery Services kluis)<br/><br/>-Azure Storage (op basis van de locatie van uw Recovery Services kluis)<br/><br/>Zie [ExpressRoute Routing requirements](../expressroute/expressroute-routing.md)(Engelstalig) voor meer informatie.<br/><br/>**Opmerking**: open bare peering is afgeschaft voor nieuwe circuits.
Azure Backup-agent | Als DPM wordt uitgevoerd op System Center 2012 SP1, installeert u Rollup 2 of hoger voor DPM SP1. Dit is vereist voor de installatie van de agent.<br/><br/> In dit artikel wordt beschreven hoe u de nieuwste versie van de Azure Backup-Agent, ook wel de MARS-agent (Microsoft Azure Recovery Service), implementeert. Als u een eerdere versie hebt geïmplementeerd, werkt u bij naar de nieuwste versie om te controleren of de back-up naar verwachting werkt.

Voordat u begint, hebt u een Azure-account nodig waarop de functie Azure Backup is ingeschakeld. Als u geen account hebt, kunt u binnen een paar minuten een gratis proefaccount maken. Meer informatie over [Azure backup prijzen](https://azure.microsoft.com/pricing/details/backup/).

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-settings"></a>Opslag instellingen wijzigen

U kunt kiezen tussen geografisch redundante opslag en lokaal redundante opslag.

- Uw kluis heeft standaard geografisch redundante opslag.
- Als de kluis uw primaire back-up is, houdt u de optie ingesteld op geografisch redundante opslag. Als u een goedkopere optie wilt gebruiken die niet zo duurzaam is, gebruikt u de volgende procedure om lokaal redundante opslag te configureren.
- Meer informatie over [Azure Storage](../storage/common/storage-redundancy.md)en de [geografisch redundante](../storage/common/storage-redundancy.md#geo-redundant-storage), [lokaal redundante](../storage/common/storage-redundancy.md#locally-redundant-storage) en [zone redundante](../storage/common/storage-redundancy.md#zone-redundant-storage) opslag opties.
- Wijzig de opslag instellingen vóór de eerste back-up. Als u al een back-up van een item hebt gemaakt, stopt u het maken van de back-up in de kluis voordat u de opslag instellingen wijzigt.

De instelling voor opslagreplicatie bewerken:

1. Open het kluis dashboard.

2. Selecteer in **beheren** de optie **back-upinfrastructuur**.

3. Selecteer in het menu **back-upconfiguratie** een opslag optie voor de kluis.

    ![Lijst met back-upkluizen](./media/backup-azure-dpm-introduction/choose-storage-configuration-rs-vault.png)

## <a name="download-vault-credentials"></a>Kluisreferenties downloaden

U gebruikt kluis referenties wanneer u de DPM-server in de kluis registreert.

- Het kluisreferentiebestand is een certificaat dat de portal voor elke back-upkluis genereert.
- De portal uploadt de openbare sleutel vervolgens naar de Access Control Service (ACS).
- Tijdens de werk stroom voor machine registratie wordt de persoonlijke sleutel van het certificaat beschikbaar gesteld aan de gebruiker, die de computer verifieert.
- Op basis van de verificatie verzendt de Azure Backup-service gegevens naar de geïdentificeerde kluis.

### <a name="best-practices-for-vault-credentials"></a>Aanbevolen procedures voor kluis referenties

Down load het kluis referentie bestand via een beveiligd kanaal van de Azure Portal om de referenties te verkrijgen:

- De kluis referenties worden alleen gebruikt tijdens de registratie werk stroom.
- Het is uw verantwoordelijkheid om ervoor te zorgen dat het kluis referentie bestand veilig is en niet is aangetast.
  - Als het beheer van de referenties is verbroken, kunnen de kluis referenties worden gebruikt om andere machines bij de kluis te registreren.
  - Back-upgegevens worden echter versleuteld met een wachtwoordzin die bij u hoort, zodat de bestaande back-upgegevens niet kunnen worden aangetast.
- Zorg ervoor dat het bestand wordt opgeslagen op een locatie die toegankelijk is vanaf de DPM-server. Als de app wordt opgeslagen in een bestands share/SMB, controleert u op de toegangs machtigingen.
- De kluisreferenties verlopen na 48 uur. U kunt zo vaak als nodig nieuwe kluis referenties downloaden. Alleen het meest recente kluis referentie bestand kan echter worden gebruikt tijdens de registratie werk stroom.
- De Azure Backup-service is niet op de hoogte van de persoonlijke sleutel van het certificaat en de persoonlijke sleutel is niet beschikbaar in de portal of de service.

Down load het kluis referentie bestand als volgt naar een lokale computer:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Open de kluis waarin u de DPM-server wilt registreren.
3. Selecteer in **instellingen** de optie **Eigenschappen**.

    ![Het menu Kluis openen](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)

4. Selecteer in **Eigenschappen**  >  **back-upreferenties** de optie **downloaden**. De portal genereert het kluis referentie bestand met behulp van een combi natie van de kluis naam en de huidige datum, en maakt deze beschikbaar voor downloaden.

    ![Referenties downloaden](./media/backup-azure-dpm-introduction/vault-credentials.png)

5. Selecteer **Opslaan** om de kluis referenties te downloaden naar de map of **Sla** het bestand op en geef een locatie op. Het duurt Maxi maal een minuut voordat het bestand is gegenereerd.

## <a name="install-the-backup-agent"></a>De back-upagent installeren

Op elke machine waarvan een back-up wordt gemaakt door Azure Backup moet de back-upagent (ook wel de MARS-agent (Microsoft Azure Recovery Service) worden geïnstalleerd). Installeer de agent op de DPM-server als volgt:

1. Open de kluis waarvoor u de DPM-server wilt registreren.
2. Selecteer in **instellingen** de optie **Eigenschappen**.

    ![Kluis instellingen openen](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. Down load de Azure Backup-Agent op de pagina **Eigenschappen** .

    ![Downloaden](./media/backup-azure-dpm-introduction/azure-backup-agent.png)

4. Na het downloaden voert u MARSAgentInstaller.exe uit. de agent op de DPM-computer installeren.
5. Selecteer een installatiemap en cachemap voor de agent. De beschik bare ruimte voor de cache locatie moet ten minste 5% van de back-upgegevens zijn.
6. Als u een proxy server gebruikt om verbinding te maken met internet, voert u in het scherm **proxy configuratie** de gegevens van de proxy server in. Als u een geverifieerde proxy gebruikt, voert u in dit scherm de gebruikers naam en het wacht woord in.
7. De Azure Backup Agent installeert .NET Framework 4,5 en Windows Power shell (als deze niet zijn geïnstalleerd) om de installatie te volt ooien.
8. Nadat de agent is geïnstalleerd, **sluit** u het venster.

    ![Sluiten](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)

## <a name="register-the-dpm-server-in-the-vault"></a>De DPM-server registreren in de kluis

1. Selecteer **online** in de DPM Administrator-console > **beheer**. Selecteer **Registreren**. De wizard Server registreren wordt geopend.
2. Geef in **proxy configuratie** de proxy-instellingen op zoals vereist.

    ![Proxyconfiguratie](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
3. In **back-upkluis**, bladert u naar het kluis referentie bestand dat u hebt gedownload en selecteert u dit.

    ![Kluis referenties](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

4. In **beperkings instellingen** kunt u eventueel bandbreedte beperking inschakelen voor back-ups. U kunt de snelheids limieten voor het opgeven van werk uren en dagen instellen.

    ![Beperkings instelling](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)

5. Geef bij instelling voor de **map voor herstel** een locatie op die kan worden gebruikt tijdens het herstellen van gegevens.

    - Azure Backup gebruikt deze locatie als een tijdelijk Holding gebied voor herstelde gegevens.
    - Na het volt ooien van het herstel van gegevens, worden de gegevens op dit gebied door Azure Backup opgeschoond.
    - De locatie moet voldoende ruimte hebben om items te bevatten waarvan u verwacht dat deze parallel worden hersteld.

    ![Instelling van herstelmap](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)

6. Genereer of geef in **versleutelings instelling** een wachtwoordzin op.

    - De wachtwoordzin wordt gebruikt voor het versleutelen van de back-ups naar de Cloud.
    - Geef mini maal 16 tekens op.
    - Sla het bestand op een veilige locatie op. Dit is nodig voor herstel.

    ![Versleuteling](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > U bent eigenaar van de coderings wachtwoordzin en micro soft heeft deze niet zichtbaar.
    > Als de wachtwoordzin verloren is gegaan of is verg eten, kan micro soft u niet helpen bij het herstellen van de back-upgegevens.

7. Selecteer **registreren** om de DPM-server te registreren bij de kluis.

Nadat de server is geregistreerd in de kluis, bent u nu klaar om een back-up te maken van Microsoft Azure. U moet de beveiligings groep configureren in de DPM-console om een back-up te maken van werk belastingen naar Azure. [Meer informatie over het](/system-center/dpm/create-dpm-protection-groups) implementeren van beveiligings groepen.

## <a name="troubleshoot-vault-credentials"></a>Problemen met kluis referenties oplossen

### <a name="expiration-error"></a>Verloop fout

Het kluis referentie bestand is alleen geldig gedurende 48 uur (nadat het is gedownload vanuit de portal). Als er een fout optreedt in dit scherm (bijvoorbeeld omdat het bestand met kluis referenties is verlopen), meldt u zich aan bij de Azure Portal en downloadt u het bestand met kluis referenties opnieuw.

### <a name="access-error"></a>Toegangs fout

Zorg ervoor dat het kluis referentie bestand beschikbaar is op een locatie die toegankelijk is voor de installatie toepassing. Als u toegang krijgt tot fouten, kopieert u het kluis referentie bestand naar een tijdelijke locatie op deze computer en voert u de bewerking opnieuw uit.

### <a name="invalid-credentials-error"></a>Fout vanwege ongeldige referenties

Als er een fout is met een ongeldige kluis Referentie (bijvoorbeeld ' ongeldige kluis referenties meegeleverd '), is het bestand beschadigd of beschikt u niet over de meest recente referenties die zijn gekoppeld aan de herstel service.

- Voer de bewerking opnieuw uit nadat u een nieuw kluis referentie bestand hebt gedownload van de portal.
- Deze fout wordt doorgaans weer gegeven wanneer u de optie **kluis referentie downloaden** selecteert in de Azure Portal, twee keer snel achter elkaar. In dit geval is alleen het tweede kluis referentie bestand geldig.
