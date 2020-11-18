---
title: Beveiligings functies die hybride back-ups beveiligen
description: Meer informatie over het gebruik van beveiligings functies in Azure Backup om back-ups veiliger te maken
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 06/08/2017
ms.openlocfilehash: 8c671b1b54b937f518f7179bb6940f31a28a78d4
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94841015"
---
# <a name="security-features-to-help-protect-hybrid-backups-that-use-azure-backup"></a>Beveiligings functies voor het beveiligen van hybride back-ups die gebruikmaken van Azure Backup

Problemen met betrekking tot beveiligings problemen, zoals malware, Ransomware en indringing, worden verhoogd. Deze beveiligings problemen kunnen kostbaar zijn, zowel voor geld als voor gegevens. Azure Backup biedt nu beveiligings functies waarmee hybride back-ups kunnen worden beveiligd tegen dergelijke aanvallen. In dit artikel wordt beschreven hoe u deze functies kunt inschakelen en gebruiken met behulp van een Azure Recovery Services-agent en Azure Backup Server. Deze functies zijn onder andere:

- **Preventie**. Er wordt een extra beveiligingslaag toegevoegd wanneer een kritieke bewerking wordt uitgevoerd, zoals het wijzigen van een wachtwoordzin. Deze validatie is om ervoor te zorgen dat dergelijke bewerkingen alleen kunnen worden uitgevoerd door gebruikers die geldige Azure-referenties hebben.
- **Waarschuwing**. Er wordt een e-mail melding verzonden naar de abonnements beheerder wanneer er een kritieke bewerking wordt uitgevoerd, zoals het verwijderen van back-upgegevens. Dit e-mail bericht zorgt ervoor dat de gebruiker snel op de hoogte wordt gebracht van dergelijke acties.
- **Herstel**. Verwijderde back-upgegevens worden nog 14 dagen na de verwijdering bewaard. Dit waarborgt de herstel baarheid van de gegevens binnen een bepaalde periode, waardoor er geen gegevens verloren gaan, zelfs als er een aanval plaatsvindt. Daarnaast worden er voor het beveiligen van beschadigde gegevens een groter aantal minimale herstel punten onderhouden.

> [!NOTE]
> Beveiligings functies mogen niet worden ingeschakeld als u een IaaS-back-up (Infrastructure as a Service) gebruikt. Deze functies zijn nog niet beschikbaar voor IaaS VM-back-up, waardoor het niet mogelijk is om ze in te scha kelen. Beveiligings functies moeten alleen worden ingeschakeld als u het volgende gebruikt: <br/>
>
> - **Azure backup-agent**. Minimale agent versie 2.0.9052. Nadat u deze functies hebt ingeschakeld, moet u een upgrade uitvoeren naar deze agent versie om kritieke bewerkingen uit te voeren. <br/>
> - **Azure backup server**. Minimale 2.0.9052-versie van Azure Backup Agent met Azure Backup Server Update 1. <br/>
> - **Data Protection Manager van System Center**. Mini maal Azure Backup Agent versie 2.0.9052 met Data Protection Manager 2012 R2 UR12 of Data Protection Manager 2016 UR2. <br/>

> [!NOTE]
> Deze functies zijn alleen beschikbaar voor Recovery Services kluis. Voor alle nieuw gemaakte Recovery Services-kluizen zijn deze functies standaard ingeschakeld. Voor bestaande Recovery Services kluizen kunnen gebruikers deze functies inschakelen met behulp van de stappen die in de volgende sectie worden beschreven. Nadat de functies zijn ingeschakeld, zijn deze van toepassing op alle Recovery Services agent computers, Azure Backup Server instanties en Data Protection Manager servers die zijn geregistreerd bij de kluis. Het inschakelen van deze instelling is een eenmalige actie en u kunt deze functies niet uitschakelen nadat ze zijn ingeschakeld.
>

## <a name="enable-security-features"></a>Beveiligingsfuncties inschakelen

Als u een Recovery Services kluis maakt, kunt u alle beveiligings functies gebruiken. Als u met een bestaande kluis werkt, schakelt u de beveiligings functies in door de volgende stappen uit te voeren:

1. Meld u aan bij de Azure Portal met uw Azure-referenties.
2. Selecteer **Bladeren** en typ **Recovery Services**.

    ![Scherm afbeelding van de optie Azure Portal bladeren](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>

    De lijst met Recovery Services-kluizen wordt weergegeven. Selecteer een kluis in deze lijst. Het geselecteerde kluisdashboard wordt geopend.
3. Selecteer in de lijst met items onder **instellingen** de optie **Eigenschappen**.

    ![Scherm afbeelding van Recovery Services kluis opties](./media/backup-azure-security-feature/vault-list-properties.png)
4. Selecteer bij **beveiligings instellingen** de optie **bijwerken**.

    ![Scherm afbeelding van Recovery Services kluis eigenschappen](./media/backup-azure-security-feature/security-settings-update.png)

    Met de koppeling bijwerken wordt het deel venster **beveiligings instellingen** geopend, met een overzicht van de functies en kunt u deze inschakelen.
5. In de vervolg keuzelijst **hebt u Azure ad multi-factor Authentication geconfigureerd?** Selecteer een waarde om te bevestigen of u [azure AD-multi-factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md)hebt ingeschakeld. Als deze is ingeschakeld, wordt u gevraagd om te verifiëren vanaf een ander apparaat (bijvoorbeeld een mobiele telefoon) terwijl u zich aanmeldt bij de Azure Portal.

   Wanneer u kritieke bewerkingen in een back-up uitvoert, moet u een beveiligings pincode invoeren die beschikbaar is op de Azure Portal. Als u Azure AD Multi-Factor Authentication inschakelt, wordt er een beveiligingslaag toegevoegd. Alleen geautoriseerde gebruikers met geldige Azure-referenties en vanaf een tweede apparaat worden geverifieerd, hebben toegang tot de Azure Portal.
6. Als u de beveiligings instellingen wilt opslaan, selecteert u **inschakelen** en selecteert u **Opslaan**. U kunt alleen **inschakelen** selecteren nadat u een waarde hebt geselecteerd in de lijst met **GECONFIGUREERDe Azure AD-multi-factor Authentication?** in de vorige stap.

    ![Scherm opname van beveiligings instellingen](./media/backup-azure-security-feature/enable-security-settings-dpm-update.png)

## <a name="recover-deleted-backup-data"></a>Verwijderde back-upgegevens herstellen

Back-up behoudt de verwijderde back-upgegevens voor een extra 14 dagen en wordt niet onmiddellijk verwijderd als de bewerking **back-up stoppen met verwijderen van back-upgegevens** wordt uitgevoerd. Als u deze gegevens in de periode van 14 dagen wilt herstellen, voert u de volgende stappen uit, afhankelijk van wat u gebruikt:

Voor gebruikers van **Azure Recovery Services agent** :

1. Als de computer waarop back-ups werden gemaakt, nog steeds beschikbaar is, moet u de verwijderde gegevens bronnen opnieuw beveiligen en de [herstel gegevens gebruiken op dezelfde machine](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine) in azure Recovery Services om alle oude herstel punten te herstellen.
2. Als deze computer niet beschikbaar is, gebruikt u [herstellen naar een andere computer](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) om een andere Azure Recovery Services computer te gebruiken om deze gegevens op te halen.

Voor **Azure backup server** gebruikers:

1. Als de server waarop back-ups werden gemaakt, nog steeds beschikbaar is, moet u de verwijderde gegevens bronnen opnieuw beveiligen en de functie **gegevens herstellen** gebruiken om alle oude herstel punten te herstellen.
2. Als deze server niet beschikbaar is, gebruikt u [gegevens herstellen van een andere Azure backup server](backup-azure-alternate-dpm-server.md) om een ander Azure Backup Server exemplaar te gebruiken om deze gegevens op te halen.

Voor **Data Protection Manager** gebruikers:

1. Als de server waarop back-ups werden gemaakt, nog steeds beschikbaar is, moet u de verwijderde gegevens bronnen opnieuw beveiligen en de functie **gegevens herstellen** gebruiken om alle oude herstel punten te herstellen.
2. Als deze server niet beschikbaar is, gebruikt u [externe DPM toevoegen](backup-azure-alternate-dpm-server.md) om een andere Data Protection Manager server te gebruiken om deze gegevens op te halen.

## <a name="prevent-attacks"></a>Aanvallen voor komen

Er zijn controles toegevoegd om ervoor te zorgen dat alleen geldige gebruikers verschillende bewerkingen kunnen uitvoeren. Dit omvat het toevoegen van een extra laag voor verificatie en het onderhouden van een minimale Bewaar termijn voor herstel doeleinden.

### <a name="authentication-to-perform-critical-operations"></a>Verificatie voor het uitvoeren van kritieke bewerkingen

Als onderdeel van het toevoegen van een extra beveiligingslaag voor kritieke bewerkingen, wordt u gevraagd een beveiligings pincode in te voeren wanneer u **beveiliging stoppen met gegevens verwijderen** en **wachtwoordzin wijzigen** uitvoert.

> [!NOTE]
>
> Op dit moment wordt de beveiliging pincode niet ondersteund voor het stoppen van de **beveiliging met Verwijder gegevens** voor dpm en MABS.

Deze pincode ontvangen:

1. Meld u aan bij Azure Portal.
2. Blader naar **Recovery Services** eigenschappen van de kluis  >  **instellingen**  >  **Properties**.
3. Onder **BEVEILIGINGS pincode** selecteert u **genereren**. Hiermee opent u een deel venster dat de pincode bevat die moet worden ingevoerd in de gebruikers interface van de Azure Recovery Services-agent.
    Deze pincode is slechts vijf minuten geldig en wordt na die periode automatisch gegenereerd.

### <a name="maintain-a-minimum-retention-range"></a>Een minimale Bewaar termijn onderhouden

Om ervoor te zorgen dat er altijd een geldig aantal herstel punten beschikbaar is, zijn de volgende controles toegevoegd:

- Voor een dagelijkse Bewaar periode moet mini maal **zeven** dagen retentie worden uitgevoerd.
- Voor een wekelijkse Bewaar periode moet er mini maal **vier** weken worden bewaard.
- Voor een maandelijkse Bewaar periode moet er mini maal **drie** maanden worden bewaard.
- Voor een jaarlijkse Bewaar periode moet mini maal **één** jaar van bewaren worden uitgevoerd.

## <a name="notifications-for-critical-operations"></a>Meldingen voor kritieke bewerkingen

Wanneer er een kritieke bewerking wordt uitgevoerd, wordt er normaal gesp roken een e-mail melding met informatie over de bewerking verzonden. U kunt aanvullende e-mail ontvangers voor deze meldingen configureren met behulp van de Azure Portal.

De beveiligings functies die in dit artikel worden vermeld, bieden verdedigings mechanismen tegen gerichte aanvallen. Als er een aanval plaatsvindt, bieden deze functies u de mogelijkheid om uw gegevens te herstellen.

## <a name="troubleshooting-errors"></a>Fouten oplossen

| Bewerking | Foutdetails | Oplossing |
| --- | --- | --- |
| Beleids wijziging |Het back-upbeleid kan niet worden gewijzigd. Fout: de huidige bewerking is mislukt vanwege een interne service fout [0x29834]. Voer de bewerking na enige tijd opnieuw uit. Neem contact op met Microsoft Ondersteuning als het probleem zich blijft voordoen. |**Oorzaak:**<br/>Deze fout wordt weer gegeven wanneer beveiligings instellingen zijn ingeschakeld, probeert u het Bewaar bereik kleiner te maken dan de hierboven opgegeven minimum waarden en hebt u een niet-ondersteunde versie (ondersteunde versies worden in de eerste opmerking van dit artikel vermeld). <br/>**Aanbevolen actie:**<br/> In dit geval moet u de Bewaar periode instellen boven de minimale Bewaar periode (zeven dagen voor dagelijks, vier weken voor wekelijks, drie weken voor maandelijks of één jaar voor jaarlijks) om door te gaan met beleids updates. Een voorkeurs benadering is dat u de back-upagent, Azure Backup Server en/of de DPM-UR bijwerkt om alle beveiligings updates te benutten. |
| Wachtwoordzin wijzigen |De ingevoerde beveiligings pincode is onjuist. (ID: 100130) Geef de juiste beveiligings pincode op om deze bewerking te volt ooien. |**Oorzaak:**<br/> Deze fout treedt op wanneer u een ongeldige of verlopen beveiligings pincode invoert tijdens het uitvoeren van een kritieke bewerking (zoals wachtwoordzin wijzigen). <br/>**Aanbevolen actie:**<br/> Als u de bewerking wilt volt ooien, moet u een geldige beveiligings pincode opgeven. Als u de pincode wilt ontvangen, meldt u zich aan bij Azure Portal en navigeert u naar Recovery Services kluis > instellingen > eigenschappen > beveiligings pincode genereren. Gebruik deze pincode om de wachtwoordzin te wijzigen. |
| Wachtwoordzin wijzigen |De bewerking is mislukt. ID: 120002 |**Oorzaak:**<br/>Deze fout wordt weer gegeven wanneer beveiligings instellingen zijn ingeschakeld, probeert u de wachtwoordzin te wijzigen en gaat u naar een niet-ondersteunde versie (geldige versies die zijn opgegeven in de eerste opmerking van dit artikel).<br/>**Aanbevolen actie:**<br/> Als u de wachtwoordzin wilt wijzigen, moet u eerst de back-upagent bijwerken naar de minimale versie 2.0.9052, Azure Backup Server op minimale update 1 en/of DPM naar mini maal DPM 2012 R2 UR12 of DPM 2016 UR2 (onderstaande koppelingen downloaden) en vervolgens een geldige beveiligings pincode invoeren. Als u de pincode wilt ontvangen, meldt u zich aan bij de Azure Portal en navigeert u naar Recovery Services kluis > instellingen > eigenschappen > beveiligings pincode genereren. Gebruik deze pincode om de wachtwoordzin te wijzigen. |

## <a name="next-steps"></a>Volgende stappen

- [Ga aan de slag met Azure Recovery Services kluis](backup-azure-vms-first-look-arm.md) om deze functies in te scha kelen.
- [Down load de nieuwste versie van Azure Recovery Services-agent](https://aka.ms/azurebackup_agent) om Windows-computers te beveiligen en uw back-upgegevens te beschermen tegen aanvallen.
- [Down load de nieuwste Azure backup server](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3) om workloads te beveiligen en uw back-upgegevens te beschermen tegen aanvallen.
- [Down load UR12 voor System center 2012 R2 Data Protection Manager](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager) of [down load UR2 voor system Center 2016 Data Protection Manager](https://support.microsoft.com/help/3209593/update-rollup-2-for-system-center-2016-data-protection-manager) om werk belastingen te beveiligen en uw back-upgegevens te beschermen tegen aanvallen.
