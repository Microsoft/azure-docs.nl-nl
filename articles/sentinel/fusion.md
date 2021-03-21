---
title: Geavanceerde aanvals detectie in azure-Sentinel
description: Gebruik fusie technologie in azure Sentinel om waarschuwings-intensiefheid te verminderen en incidenten te maken die zijn gebaseerd op geavanceerde aanvals detectie.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2020
ms.author: yelevin
ms.openlocfilehash: 23e116eba6393f834b3368901d4440e668b16fca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101724282"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Geavanceerde aanvals detectie in azure-Sentinel

> [!IMPORTANT]
> Enkele fusie detecties (Zie de hieronder vermelde) zijn momenteel beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.

Door gebruik te maken van fusie technologie op basis van machine learning, kan Azure Sentinel automatisch meerdere fase aanvallen detecteren door combi Naties te identificeren van afwijkend gedrag en verdachte activiteiten die in verschillende stadia van de Kill-keten worden waargenomen. Op basis van deze detecties genereert Azure Sentinel incidenten die anders moeilijk te ondervangen zijn. Deze incidenten bestaan uit twee of meer waarschuwingen of activiteiten. Deze incidenten zijn bedoeld om te worden ontworpen met weinig volume, hoge betrouw baarheid en hoge urgentie.

Deze detectie technologie is aangepast voor uw omgeving en vermindert niet alleen valse positieve tarieven, maar kan ook aanvallen met beperkte of ontbrekende gegevens detecteren.

## <a name="configuration-for-advanced-multistage-attack-detection"></a>Configuratie voor geavanceerde detectie van aanvallen met meerdere fasen

Deze detectie is standaard ingeschakeld in azure Sentinel. Gebruik de volgende instructies om de status te controleren of om deze uit te scha kelen in het geval dat u een alternatieve oplossing gebruikt om incidenten te maken op basis van meerdere waarschuwingen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) als u dat nog niet hebt gedaan.

1. Ga naar **Azure Sentinel**  >  **Configuration**  >  **Analytics**

1. Selecteer **actieve regels** en zoek in de kolom **naam** **Geavanceerde detectie van aanvallen** van meerdere fasen door de lijst voor het type **Fusion** regel te filteren. Controleer de kolom **status** om te controleren of deze detectie is ingeschakeld of uitgeschakeld.

    :::image type="content" source="./media/fusion/selecting-fusion-rule-type.png" alt-text="{alt-text}":::

1. Als u de status wilt wijzigen **, selecteert u** dit item en selecteert u op de Blade **Geavanceerde aanvals detectie** .

1. Op de Blade wizard voor het **maken van regels** wordt de status wijziging automatisch voor u geselecteerd, dus Selecteer **volgende: controleren** en vervolgens **Opslaan**. 

 Omdat het type **Fusion** Rule slechts één regel bevat die niet kan worden gewijzigd, zijn regel sjablonen niet van toepassing op dit regel type.

> [!NOTE]
> Azure Sentinel maakt momenteel gebruik van 30 dagen aan historische gegevens om de machine learning systemen te trainen. Deze gegevens worden altijd versleuteld met behulp van de sleutels van micro soft, zoals door de machine learning pijp lijn wordt door gegeven. De trainings gegevens worden echter niet versleuteld met behulp van door de [klant beheerde sleutels (CMK)](customer-managed-keys.md) als u CMK hebt ingeschakeld in uw Azure Sentinel-werk ruimte. Als u wilt deel nemen aan fusie, gaat u naar **Azure Sentinel** \> **Configuration** \> **Analytics \> actieve regels \> detectie** van meerdere fasen en selecteert u in de kolom **status** de optie **uitschakelen.**

## <a name="attack-detection-scenarios"></a>Scenario's voor aanvals detectie

De volgende sectie bevat de typen correlatie scenario's, gegroepeerd op bedreigings classificatie, die door Azure Sentinel wordt gezocht voor het gebruik van fusie technologie.

Zoals hierboven vermeld, omdat fusie meerdere beveiligings waarschuwingen van verschillende producten voor het detecteren van geavanceerde multi-fase aanvallen, worden geslaagde Fusion-detecties gepresenteerd als **Fusion incidenten** op de pagina met Azure-Sentinel- **incidenten** en niet als **waarschuwingen** in de tabel met **beveiligings waarschuwingen** in **Logboeken**.

Als u deze scenario's voor het detecteren van problemen met de fusie wilt inschakelen, moeten alle gegevens bronnen die worden vermeld, worden opgenomen in de gekoppelde Azure Sentinel data-connectors.

> [!NOTE]
> Sommige van deze scenario's zijn beschikbaar als **Preview-versie**. Deze worden aangegeven.

## <a name="compute-resource-abuse"></a>Misbruik van reken bronnen

### <a name="multiple-vm-creation-activities-following-suspicious-azure-active-directory-sign-in"></a>Meerdere activiteiten voor het maken van VM'S na een verdachte Azure Active Directory aanmelding
Dit scenario is momenteel beschikbaar als **Preview-versie**.

**Mitre ATT&VERzonken tactiek:** Initiële toegang, impact 

**Mitre ATT&VERzonken technieken:** Geldig account (T1078), bron overname (T1496)

**Gegevens connector bronnen:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Beschrijving:** Fusie-incidenten van dit type geven aan dat een afwijkend aantal Vm's in één sessie is gemaakt na een verdachte aanmelding bij een Azure AD-account. Dit type waarschuwing geeft aan dat het account dat is genoteerd in de beschrijving van het Fusion-incident is aangetast en wordt gebruikt om nieuwe Vm's te maken voor niet-geautoriseerde doel einden, zoals het uitvoeren van crypto-analyse bewerkingen. De permutaties van verdachte Azure AD-aanmeldings waarschuwingen met de waarschuwing voor het maken van meerdere VM-activiteiten zijn:

- **Onmogelijke reis naar een ongewoone locatie voor het maken van meerdere VM-activiteiten**

- **Aanmeldings gebeurtenis vanaf een onbekende locatie die leidt tot meerdere activiteiten voor het maken van VM'S**

- **Aanmeldings gebeurtenis vanaf een geïnfecteerd apparaat waardoor meerdere VM-activiteiten kunnen worden gemaakt**

- **Aanmeldings gebeurtenis vanaf een anoniem IP-adres dat leidt tot meerdere activiteiten voor het maken van VM'S**

- **Aanmeldings gebeurtenis van de gebruiker met de gelekte referenties voor het maken van meerdere VM-activiteiten**

## <a name="credential-harvesting-new-threat-classification"></a>Referenties verzamelen (nieuwe bedreigings classificatie)

### <a name="malicious-credential-theft-tool-execution-following-suspicious-sign-in"></a>Uitvoering van kwaad aardige referentie diefstal programma na verdachte aanmelding

**Mitre ATT&VERzonken tactiek:** Initiële toegang, referentie toegang

**Mitre ATT&VERzonken technieken:** Geldig account (T1078), referentie dumping van het besturings systeem (T1003)

**Gegevens connector bronnen:** Azure Active Directory Identity Protection, micro soft Defender voor eind punt

**Beschrijving:** Fusion incidenten van dit type geven aan dat er een bekende referentie diefstal programma is uitgevoerd na een verdachte aanmelding bij Azure AD. Dit biedt een hoge mate van betrouw baarheid dat het gebruikers account dat is genoteerd in de beschrijving van de waarschuwing is aangetast en mogelijk een hulp programma als **Mimikatz** heeft gebruikt voor het verzamelen van referenties zoals sleutels, lees bare wacht woorden en/of wacht woord-hashes van het systeem. De geoogste referenties kunnen een aanvaller toegang tot gevoelige gegevens geven, bevoegdheden escaleren en/of op een later tijdstip verplaatsen via het netwerk. De permutaties van verdachte Azure AD-aanmeldings waarschuwingen met de schadelijke referentie diefstal hulp programma zijn:

- **Onmogelijke reis naar ongewone locaties die leiden tot kwaad aardige referentie diefstal hulp programma**

- **Aanmeldings gebeurtenis vanaf een onbekende locatie, waardoor schadelijke referentie diefstal wordt uitgevoerd**

- **Aanmeldings gebeurtenis vanaf een geïnfecteerd apparaat waardoor schadelijke referentie diefstal wordt uitgevoerd**

- **Aanmeldings gebeurtenis vanaf een anoniem IP-adres dat schadelijk is voor de uitvoering van kwaad aardige referentie diefstal**

- **Aanmeldings gebeurtenis van de gebruiker met de gelekte referenties voor de uitvoering van kwaad aardige referentie diefstal**

### <a name="suspected-credential-theft-activity-following-suspicious-sign-in"></a>Verdachte activiteit voor referentie diefstal na verdachte aanmelding

**Mitre ATT&VERzonken tactiek:** Initiële toegang, referentie toegang

**Mitre ATT&VERzonken technieken:** Geldig account (T1078), referenties van wachtwoord archieven (T1555), referentie dumping van het besturings systeem (T1003)

**Gegevens connector bronnen:** Azure Active Directory Identity Protection, micro soft Defender voor eind punt

**Beschrijving:** Fusie-incidenten van dit type geven aan dat de activiteit die is gekoppeld aan patronen van referentie diefstal, is opgetreden na een verdachte aanmelding bij Azure AD. Dit biedt een hoge mate van betrouw baarheid dat de gebruikers account in de beschrijving van de waarschuwing is aangetast en wordt gebruikt om referenties te stelen, zoals sleutels, wacht woorden voor tekst zonder opmaak, wacht woord-hashes, enzovoort. De gestolen referenties kunnen ertoe leiden dat een aanvaller toegang krijgt tot gevoelige gegevens, machtigingen kan escaleren en/of later over het netwerk kan worden verplaatst. De permutaties van verdachte Azure AD-aanmeldings waarschuwingen met de waarschuwing dief stal activiteit voor referenties zijn:

- **Onmogelijke reis naar ongewone locaties die leiden tot vermoeden dat de dief stal van referentie activiteiten**

- **Aanmeldings gebeurtenis vanaf een onbekende locatie die leidt tot een verdachte activiteit van referentie diefstal**

- **Aanmeldings gebeurtenis vanaf een geïnfecteerd apparaat waardoor de verdachte referentie diefstal activiteit**

- **Aanmeldings gebeurtenis vanaf een anoniem IP-adres dat leidt tot een verdachte referentie diefstal activiteit**

- **Aanmeldings gebeurtenis van de gebruiker met de gelekte referenties, waardoor de verdachte referentie diefstal activiteit**

## <a name="crypto-mining-new-threat-classification"></a>Crypto grafie-analyse (nieuwe bedreigings classificatie)

### <a name="crypto-mining-activity-following-suspicious-sign-in"></a>Crypto-analyse activiteit na verdacht aanmelden

**Mitre ATT&VERzonken tactiek:** Initiële toegang, referentie toegang

**Mitre ATT&VERzonken technieken:** Geldig account (T1078), bron overname (T1496)

**Gegevens connector bronnen:** Azure Active Directory Identity Protection, Azure Defender (Azure Security Center)

**Beschrijving:** Fusie-incidenten van dit type wijzen op crypto-analyse activiteiten die zijn gekoppeld aan een verdachte aanmelding bij een Azure AD-account. Dit biedt een hoge mate van betrouw baarheid dat het gebruikers account dat is genoteerd in de beschrijving van de waarschuwing, is aangetast en is gebruikt voor het overnemen van bronnen in uw omgeving naar crypto grafie. Dit kan uw resources van de reken kracht tijd resteert en/of resulteren in aanzienlijk hoger dan verwacht Cloud gebruiks facturen. De permutaties van verdachte Azure AD-aanmeldings waarschuwingen met de waarschuwing voor de activiteit van de crypto-analyse zijn:  

- **Onmogelijke reis naar ongewone locaties die leiden tot crypto grafie-activiteit**

- **Aanmeldings gebeurtenis vanaf een onbekende locatie die leidt tot crypto grafie-activiteit**

- **Aanmeldings gebeurtenis vanaf een geïnfecteerd apparaat die leidt tot crypto grafie-activiteit**

- **Aanmeldings gebeurtenis vanaf een anoniem IP-adres dat leidt tot crypto grafie-activiteit**

- **Aanmeldings gebeurtenis van de gebruiker met de gelekte referenties voor de activiteit van crypto analyse**

## <a name="data-exfiltration"></a>Gegevensoverdracht

### <a name="office-365-mailbox-exfiltration-following-a-suspicious-azure-ad-sign-in"></a>Office 365-postvak exfiltration na een verdachte Azure AD-aanmelding

**Mitre ATT&VERzonken tactiek:** Initiële toegang, exfiltration, verzameling

**Mitre ATT&VERzonken technieken:** Geldig account (T1078), e-mail verzameling (T1114), geautomatiseerd exfiltration (T1020)

**Gegevens connector bronnen:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Beschrijving:** Fusion incidenten van dit type geven aan dat een verdachte postvak-doorstuur regel is ingesteld op het postvak in van een gebruiker na een verdachte aanmelding bij een Azure AD-account. Deze indicatie biedt een hoge mate van betrouw baarheid dat de account van de gebruiker (vermeld in de beschrijving van het fusie-incident) is aangetast en dat deze is gebruikt voor het exfiltreren van gegevens van het netwerk van uw organisatie door een regel voor het door sturen van een postvak in te scha kelen zonder de echte kennis van de gebruiker. De permutaties van verdachte Azure AD-aanmeldings waarschuwingen met het Office 365-postvak exfiltration-waarschuwing zijn:

- **Onmogelijke reis naar een ongewoone locatie voor Office 365-postvak exfiltration**

- **Aanmeldings gebeurtenis vanaf een onbekende locatie die leidt tot Office 365-postvak exfiltration**

- **Aanmeldings gebeurtenis vanaf een geïnfecteerd apparaat dat het Office 365-postvak exfiltration**

- **Aanmeldings gebeurtenis vanaf een anoniem IP-adres dat leidt tot Office 365-postvak exfiltration**

- **Aanmeldings gebeurtenis van de gebruiker met de gelekte referenties die het gevolg zijn van het Office 365-postvak exfiltration**

### <a name="mass-file-download-following-suspicious-azure-ad-sign-in"></a>Massa bestanden downloaden na verdachte aanmelding bij Azure AD

**Mitre ATT&VERzonken tactiek:** Initiële toegang, exfiltration

**Mitre ATT&VERzonken technieken:** Geldig account (T1078)

**Gegevens connector bronnen:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Beschrijving:** Fusion incidenten van dit type geven aan dat een afwijkend aantal bestanden is gedownload door een gebruiker na een verdachte aanmelding bij een Azure AD-account. Deze indicatie geeft een hoge mate van betrouw baarheid dat het account dat wordt genoteerd in de beschrijving van het fusie incident is aangetast en is gebruikt om gegevens van het netwerk van uw organisatie te exfiltreren. De permutaties van verdachte Azure AD-aanmeldings waarschuwingen met de waarschuwing massa bestand downloaden zijn:  

- **Onmogelijke reis naar een ongewoone locatie voor het downloaden van massale bestanden**

- **Aanmeldings gebeurtenis vanaf een onbekende locatie die leidt tot massale bestands downloads**

- **Aanmeldings gebeurtenis vanaf een geïnfecteerd apparaat waardoor massale bestanden kunnen worden gedownload**

- **Aanmeldings gebeurtenis vanaf een anoniem IP-adres dat kan worden gedownload naar massale bestanden**

- **Aanmeldings gebeurtenis van de gebruiker met de gelekte referenties voor het downloaden van massale bestanden**

### <a name="mass-file-sharing-following-suspicious-azure-ad-sign-in"></a>Massa bestands deling na verdachte aanmelding bij Azure AD

**Mitre ATT&VERzonken tactiek:** Initiële toegang, exfiltration

**Mitre ATT&VERzonken technieken:** Geldig account (T1078), exfiltration via web service (T1567)

**Gegevens connector bronnen:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Beschrijving:** Fusie-incidenten van dit type geven aan dat een aantal bestanden boven een bepaalde drempel waarde is gedeeld met anderen na een verdachte aanmelding bij een Azure AD-account. Deze indicatie biedt een hoge mate van betrouw baarheid dat het account dat wordt genoteerd in de beschrijving van het fusie incident is aangetast en wordt gebruikt om gegevens uit het netwerk van uw organisatie te exfiltreren door bestanden zoals documenten, spread sheets enzovoort te delen, met niet-geautoriseerde gebruikers voor schadelijke doel einden. De permutaties van verdachte Azure AD-aanmeldings waarschuwingen met de waarschuwing massa bestands deling zijn:  

- **Onmogelijke reis naar een ongewoone locatie voor het delen van massa bestanden**

- **Aanmeldings gebeurtenis vanaf een onbekende locatie die leidt tot massaal delen van bestanden**

- **Aanmeldings gebeurtenis vanaf een geïnfecteerd apparaat waardoor massa bestanden kunnen worden gedeeld**

- **Aanmeldings gebeurtenis vanaf een anoniem IP-adres dat leidt tot massaal delen van bestanden**

- **Aanmeldings gebeurtenis van de gebruiker met de gelekte referenties voor het delen van massa bestanden**

### <a name="suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in"></a>Verdachte regels voor het bewerken van postvak in zijn ingesteld na verdachte aanmelding bij Azure AD
Dit scenario maakt deel uit van twee bedreigings classificaties in deze lijst: **gegevens exfiltration** en **zijdelingse verplaatsing**. Duidelijkheidshalve wordt het in beide gedeelten weer gegeven.

Dit scenario is momenteel beschikbaar als **Preview-versie**.

**Mitre ATT&VERzonken tactiek:** Initiële toegang, zijdelingse verplaatsing, exfiltration

**Mitre ATT&VERzonken technieken:** Geldig account (T1078), interne spear phishing (T1534)

**Gegevens connector bronnen:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Beschrijving:** Fusion incidenten van dit type geven aan dat er afwijkende postvak in-regels zijn ingesteld in het postvak in van een gebruiker na een verdachte aanmelding bij een Azure AD-account. Dit biedt een hoge mate van betrouw baarheid dat het account dat is genoteerd in de beschrijving van het Fusion-incident is aangetast en is gebruikt om de regels voor het postvak in van de gebruiker te bewerken voor schadelijke doel einden. Dit kan een poging van een aanvaller zijn om gegevens van het netwerk van de organisatie te exfiltreren. Het is ook mogelijk dat de aanvaller via de organisatie malafide e-mail berichten genereert (phishing-detectie mechanismen die gericht zijn op e-mail van externe bronnen) om deze later te verplaatsen door toegang te krijgen tot extra gebruikers en/of bevoorrechte accounts. De permutaties van verdachte Azure AD-aanmeldings waarschuwingen met de verdachte regels voor het bewerken van postvak in zijn:  

- **Onmogelijke reis naar een ongewoone locatie voor verdachte regel voor Postvak in**

- **Aanmeldings gebeurtenis vanaf een onbekende locatie die leidt tot verdachte postvak in-manipulatie regel**

- **Aanmeldings gebeurtenis van een geïnfecteerd apparaat dat tot een verdachte regel voor het postvak in vervalt**

- **Aanmeldings gebeurtenis vanaf een anoniem IP-adres dat leidt tot verdacht regel voor Postvak in**

- **Aanmeldings gebeurtenis van de gebruiker met de gelekte referenties voor de verdachte regel voor Postvak in**

### <a name="multiple-power-bi-report-sharing-activities-following-suspicious-azure-ad-sign-in"></a>Meerdere Power BI activiteiten voor het delen van rapporten na verdachte aanmelding bij Azure AD 
Dit scenario is momenteel beschikbaar als **Preview-versie**.

**Mitre ATT&VERzonken tactiek:** Initiële toegang, exfiltration 

**Mitre ATT&VERzonken technieken:** Geldig account (T1078), exfiltration via web service (T1567)

**Gegevens connector bronnen:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Beschrijving:** Fusie-incidenten van dit type geven aan dat een afwijkend aantal Power BI-rapporten is gedeeld in één sessie na een verdachte aanmelding bij een Azure AD-account. Deze indicatie biedt een hoge mate van betrouw baarheid dat het account dat is genoteerd in de beschrijving van het fusie incident is aangetast en is gebruikt om gegevens van het netwerk van uw organisatie te exfiltreren door Power BI-rapporten te delen met niet-geautoriseerde gebruikers voor schadelijke doel einden. De permutaties van verdachte Azure AD-aanmeldings waarschuwingen met de meerdere Power BI activiteiten voor het delen van rapporten zijn:  

- **Onmogelijke reis naar een ongewone locatie die leidt tot meerdere Power BI rapportage activiteiten voor delen**

- **Aanmeldings gebeurtenis vanaf een onbekende locatie die leidt tot meerdere Power BI activiteiten voor het delen van rapporten**

- **Aanmeldings gebeurtenis van een geïnfecteerd apparaat die kan leiden tot meerdere Power BI activiteiten voor het delen van rapporten**

- **Aanmeldings gebeurtenis vanaf een anoniem IP-adres dat leidt tot meerdere Power BI activiteiten voor het delen van rapporten**

- **Aanmeldings gebeurtenis van de gebruiker met de gelekte referenties, waardoor er meerdere Power BI rapport activiteiten voor delen**

### <a name="suspicious-power-bi-report-sharing-following-suspicious-azure-ad-sign-in"></a>Verdachte Power BI voor het delen van rapporten na verdachte Azure AD-aanmelding
Dit scenario is momenteel beschikbaar als **Preview-versie**.

**Mitre ATT&VERzonken tactiek:** Initiële toegang, exfiltration 

**Mitre ATT&VERzonken technieken:** Geldig account (T1078), exfiltration via web service (T1567)

**Gegevens connector bronnen:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Beschrijving:** Fusie-incidenten van dit type geven aan dat er een verdachte Power BI activiteit voor het delen van rapporten is opgetreden na een verdachte aanmelding bij een Azure AD-account. De activiteit voor delen is geïdentificeerd als verdacht omdat het Power BI rapport gevoelige informatie bevat die is geïdentificeerd met behulp van natuurlijke taal verwerking, en omdat het is gedeeld met een extern e-mail adres, dat is gepubliceerd op het web of wordt bezorgd als een moment opname van een extern geabonneerd e-mail adres. Deze waarschuwing geeft aan dat het account dat is genoteerd in de beschrijving van het fusie incident is aangetast en is gebruikt om gevoelige gegevens van uw organisatie te exfiltreren door Power BI-rapporten te delen met niet-geautoriseerde gebruikers voor schadelijke doel einden. De permutaties van verdachte Azure AD-aanmeldings waarschuwingen met het verdachte Power BI rapport delen zijn:  

- **Onmogelijke reis naar een ongewoone locatie voor verdacht Power BI het delen van rapporten**

- **Aanmeldings gebeurtenis vanaf een onbekende locatie die leidt tot verdacht Power BI het delen van rapporten**

- **Aanmeldings gebeurtenis vanaf een geïnfecteerd apparaat waardoor het verdacht Power BI rapport te delen**

- **Aanmeldings gebeurtenis vanaf een anoniem IP-adres dat kan leiden tot verdacht Power BI het delen van rapporten**

- **Aanmeldings gebeurtenis van de gebruiker met de gelekte referenties voor een verdacht Power BI het delen van het rapport**

## <a name="data-destruction"></a>Gegevens vernietiging

### <a name="mass-file-deletion-following-suspicious-azure-ad-sign-in"></a>Massa bestanden verwijderen na verdachte aanmelding bij Azure AD

**Mitre ATT&VERzonken tactiek:** Initiële toegang, impact

**Mitre ATT&VERzonken technieken:** Geldig account (T1078), gegevens vernietiging (T1485)

**Gegevens connector bronnen:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Beschrijving:** Fusion incidenten van dit type geven aan dat een afwijkend aantal unieke bestanden is verwijderd na een verdachte aanmelding bij een Azure AD-account. Dit geeft aan dat het account dat is genoteerd in de beschrijving van het fusie incident mogelijk is aangetast en is gebruikt om gegevens te vernietigen voor schadelijke doel einden. De permutaties van verdachte Azure AD-aanmeldings waarschuwingen met de melding massa bestand verwijderen zijn:  

- **Onmogelijke reis naar een ongewoone locatie voor het verwijderen van massale bestanden**

- **Aanmeldings gebeurtenis vanaf een onbekende locatie die leidt tot massale bestands verwijdering**

- **Aanmeldings gebeurtenis vanaf een geïnfecteerd apparaat waardoor massale bestanden kunnen worden verwijderd**

- **Aanmeldings gebeurtenis vanaf een anoniem IP-adres dat leidt tot massale bestands verwijdering**

- **Aanmeldings gebeurtenis van de gebruiker met de gelekte referenties voor het verwijderen van massale bestanden**

### <a name="suspicious-email-deletion-activity-following-suspicious-azure-ad-sign-in"></a>Activiteit verdachte e-mail verwijderen na verdachte aanmelding bij Azure AD
Dit scenario is momenteel beschikbaar als **Preview-versie**.

**Mitre ATT&VERzonken tactiek:** Initiële toegang, impact 

**Mitre ATT&VERzonken technieken:** Geldig account (T1078), gegevens vernietiging (T1485)

**Gegevens connector bronnen:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Beschrijving:** Fusie-incidenten van dit type geven aan dat een afwijkend aantal e-mails is verwijderd in één sessie na een verdachte aanmelding bij een Azure AD-account. Dit geeft aan dat het account dat is genoteerd in de beschrijving van het fusie incident mogelijk is aangetast en is gebruikt om gegevens te vernietigen voor schadelijke doel einden, zoals het schaden van de organisatie of het verbergen van e-mail activiteit met betrekking tot spam. De permutaties van verdachte Azure AD-aanmeldings waarschuwingen met de waarschuwing voor het verwijderen van verdachte e-mail berichten zijn:   

- **Geen enkele ongewoone reis naar een ongewone locatie voor het verwijderen van verdachte e-mail activiteiten**

- **Aanmeldings gebeurtenis vanaf een onbekende locatie die leidt tot verdachte e-mail verwijderings activiteit**

- **Aanmeldings gebeurtenis vanaf een geïnfecteerd apparaat waardoor de activiteit verdachte e-mail wordt verwijderd**

- **Aanmeldings gebeurtenis vanaf een anoniem IP-adres dat leidt tot verdachte e-mail verwijderings activiteit**

- **Aanmeldings gebeurtenis van de gebruiker met de gelekte referenties waardoor de verdachte e-mail activiteit wordt verwijderd**

## <a name="denial-of-service"></a>Denial of Service

### <a name="multiple-vm-delete-activities-following-suspicious-azure-ad-sign-in"></a>Meerdere VM-activiteiten verwijderen na verdachte aanmelding bij Azure AD
Dit scenario is momenteel beschikbaar als **Preview-versie**.

**Mitre ATT&VERzonken tactiek:** Initiële toegang, impact

**Mitre ATT&VERzonken technieken:** Geldig account (T1078), denial of service op eind punt (T1499)

**Gegevens connector bronnen:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Beschrijving:** Fusion incidenten van dit type geven aan dat een afwijkend aantal Vm's in één sessie is verwijderd na een verdachte aanmelding bij een Azure AD-account. Deze indicatie biedt een hoge mate van betrouw baarheid dat het account dat wordt genoteerd in de beschrijving van het fusie incident is aangetast en is gebruikt om de cloud omgeving van de organisatie te verstoren of te vernietigen. De permutaties van verdachte Azure AD-aanmeldings waarschuwingen met de waarschuwing voor het verwijderen van meerdere VM-activiteiten zijn:  

- **Onmogelijke reis naar een ongewoone locatie voor het verwijderen van meerdere VM-activiteiten**

- **Aanmeldings gebeurtenis vanaf een onbekende locatie die leidt tot meerdere VM-verwijderings activiteiten**

- **Aanmeldings gebeurtenis vanaf een geïnfecteerd apparaat waardoor meerdere VM-activiteiten kunnen worden verwijderd**

- **Aanmeldings gebeurtenis vanaf een anoniem IP-adres dat leidt tot meerdere VM-verwijderings activiteiten**

- **Aanmeldings gebeurtenis van de gebruiker met de gelekte referenties waardoor meerdere VM-activiteiten kunnen worden verwijderd**

## <a name="lateral-movement"></a>Zijwaartse beweging

### <a name="office-365-impersonation-following-suspicious-azure-ad-sign-in"></a>Office 365-imitatie na verdachte aanmelding bij Azure AD

**Mitre ATT&VERzonken tactiek:** Initiële toegang, zijdelingse verplaatsing

**Mitre ATT&VERzonken technieken:** Geldig account (T1078), interne spear phishing (T1534)

**Gegevens connector bronnen:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Beschrijving:** Met fusie-incidenten van dit type wordt aangegeven dat er een afwijkend aantal imitatie acties is opgetreden na een verdachte aanmelding vanuit een Azure AD-account. In sommige software zijn er opties om gebruikers toe te staan andere gebruikers te imiteren. E-mail services kunnen bijvoorbeeld toestaan dat gebruikers andere gebruikers machtigen om e-mail namens u te verzenden. Deze waarschuwing geeft aan dat het account dat is genoteerd in de beschrijving van het fusie incident is aangetast en is gebruikt om imitatie activiteiten uit te voeren voor schadelijke doel einden, zoals het verzenden van malafide e-mail berichten voor het distribueren van malware of de zijdelingse verplaatsing. De permutaties van verdachte Azure AD-aanmeldings waarschuwingen met de Office 365-imitatie waarschuwing zijn:  

- **Onmogelijke reis naar een ongewone locatie die leidt tot Office 365-imitatie**

- **Aanmeldings gebeurtenis vanaf een onbekende locatie die leidt tot Office 365-imitatie**

- **Aanmeldings gebeurtenis vanaf een geïnfecteerd apparaat die leidt tot Office 365-imitatie**

- **Aanmeldings gebeurtenis vanaf een anoniem IP-adres dat leidt tot Office 365-imitatie**

- **Aanmeldings gebeurtenis van de gebruiker met de gelekte referenties die het gevolg zijn van een Office 365-imitatie**
 
### <a name="suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in"></a>Verdachte regels voor het bewerken van postvak in zijn ingesteld na verdachte aanmelding bij Azure AD
Dit scenario maakt deel uit van twee bedreigings classificaties in deze lijst: **zijdelingse verplaatsing** en **gegevens exfiltration**. Duidelijkheidshalve wordt het in beide gedeelten weer gegeven.

Dit scenario is momenteel beschikbaar als **Preview-versie**.

**Mitre ATT&VERzonken tactiek:** Initiële toegang, zijdelingse verplaatsing, exfiltration

**Mitre ATT&VERzonken technieken:** Geldig account (T1078), interne spear phishing (T1534), automatische exfiltration (T1020)

**Gegevens connector bronnen:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Beschrijving:** Fusion incidenten van dit type geven aan dat er afwijkende postvak in-regels zijn ingesteld in het postvak in van een gebruiker na een verdachte aanmelding bij een Azure AD-account. Deze indicatie biedt een hoge mate van betrouw baarheid dat het account dat wordt genoteerd in de beschrijving van het Fusion-incident is aangetast en is gebruikt om de regels voor het postvak in van de gebruiker te bewerken voor schadelijke doel einden. Dit kan een poging van een aanvaller zijn om gegevens van het netwerk van de organisatie te exfiltreren. Het is ook mogelijk dat de aanvaller via de organisatie malafide e-mail berichten genereert (phishing-detectie mechanismen die gericht zijn op e-mail van externe bronnen) om deze later te verplaatsen door toegang te krijgen tot extra gebruikers en/of bevoorrechte accounts. De permutaties van verdachte Azure AD-aanmeldings waarschuwingen met de verdachte regels voor het bewerken van postvak in zijn:

- **Onmogelijke reis naar een ongewoone locatie voor verdachte regel voor Postvak in**

- **Aanmeldings gebeurtenis vanaf een onbekende locatie die leidt tot verdachte postvak in-manipulatie regel**

- **Aanmeldings gebeurtenis van een geïnfecteerd apparaat dat tot een verdachte regel voor het postvak in vervalt**

- **Aanmeldings gebeurtenis vanaf een anoniem IP-adres dat leidt tot verdacht regel voor Postvak in**

- **Aanmeldings gebeurtenis van de gebruiker met de gelekte referenties voor de verdachte regel voor Postvak in**

## <a name="malicious-administrative-activity"></a>Schadelijke administratieve activiteit

### <a name="suspicious-cloud-app-administrative-activity-following-suspicious-azure-ad-sign-in"></a>Verdachte beheer activiteit voor Cloud-apps na verdachte aanmelding bij Azure AD

**Mitre ATT&VERzonken tactiek:** Initiële toegang, persistentie, verdedigings fraude, zijdelingse verplaatsing, verzamelen, exfiltration en impact

**Mitre ATT&VERzonken technieken:** n.v.t.

**Gegevens connector bronnen:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Beschrijving:** Fusion incidenten van dit type geven aan dat er een afwijkend aantal beheer activiteiten werd uitgevoerd in één sessie na een verdachte aanmelding bij Azure AD vanuit hetzelfde account. Dit geeft aan dat het account dat is genoteerd in de beschrijving van het fusie incident mogelijk is aangetast en is gebruikt om een wille keurig aantal niet-geautoriseerde beheer acties met kwaad aardigheid te maken. Dit betekent ook dat een account met beheerders bevoegdheden mogelijk is aangetast. De permutaties van verdachte Azure AD-aanmeldings waarschuwingen met de verdachte Cloud app-waarschuwing voor beheer activiteiten zijn:  

- **Onmogelijke reis naar een ongewoone locatie om te leiden naar verdachte Cloud app Administrative-activiteit**

- **Aanmeldings gebeurtenis vanaf een onbekende locatie die leidt tot verdachte Cloud app-beheer activiteiten**

- **Aanmeldings gebeurtenis van een geïnfecteerd apparaat die tot verdachte Cloud app-beheer activiteit leidt**

- **Aanmeldings gebeurtenis vanaf een anoniem IP-adres dat leidt tot verdachte Cloud app-beheer activiteiten**

- **Aanmeldings gebeurtenis van de gebruiker met de gelekte referenties voor de verdachte Cloud app Administrative-activiteit**

## <a name="malicious-execution-with-legitimate-process"></a>Schadelijke uitvoering met legitiem proces

### <a name="powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Power Shell heeft een verdachte netwerk verbinding gemaakt, gevolgd door afwijkend verkeer dat is gemarkeerd door de firewall van Palo Alto Networks.
Dit scenario is momenteel beschikbaar als **Preview-versie**.

**Mitre ATT&VERzonken tactiek:** Uitvoering

**Mitre ATT&VERzonken technieken:** Interpreter opdracht en scripting (T1059)

**Gegevens connector bronnen:** Micro soft Defender voor eind punt (voorheen micro soft Defender Advanced Threat Protection of MDATP), Palo Alto-netwerken 

**Beschrijving:** Fusion incidenten van dit type geven aan dat er een uitgaande verbindings aanvraag is gedaan via een Power shell-opdracht en dat er afwijkende binnenkomende activiteiten zijn gedetecteerd door de firewall Palo Alto Networks. Dit geeft een indicatie dat een aanvaller waarschijnlijk toegang tot uw netwerk heeft verkregen en probeert schadelijke acties uit te voeren. Verbindings pogingen met Power shell die volgen op dit patroon, kunnen een indicatie zijn van de opdracht-en controle activiteit van schadelijke software, aanvragen voor het downloaden van extra malware of een aanvaller die externe interactieve toegang tot stand brengt. Net als bij alle ' bewoonde ' aanvallen op het land ' kan deze activiteit een authentiek gebruik zijn van Power shell. De uitvoering van de Power shell-opdracht gevolgd door de verdachte binnenkomende firewall activiteit verhoogt echter het vertrouwen dat Power shell op een kwaad aardige manier wordt gebruikt en moet verder worden onderzocht. In Palo Alto-Logboeken wordt Azure Sentinel gericht op [bedreigings logboeken](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)en verkeer wordt beschouwd als verdacht Wanneer bedreigingen zijn toegestaan (verdachte gegevens, bestanden, flooden, pakketten, scans, spyware, url's, virussen, beveiligings problemen, bosbrand-virussen, Wildfires). U kunt ook verwijzen naar het Palo Alto-bedreigings logboek dat overeenkomt met het [type bedreiging/inhoud](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) dat wordt vermeld in de beschrijving van het fusie incident voor aanvullende waarschuwings Details.

### <a name="suspicious-remote-wmi-execution-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Verdachte externe WMI-uitvoering gevolgd door afwijkend verkeer dat is gemarkeerd door de firewall Palo Alto Networks
Dit scenario is momenteel beschikbaar als **Preview-versie**.

**Mitre ATT&VERzonken tactiek:** Uitvoering, detectie

**Mitre ATT&VERzonken technieken:** Windows Management Instrumentation (T1047)

**Gegevens connector bronnen:** Micro soft Defender voor eind punt (voorheen MDATP), Palo Alto-netwerken 

**Beschrijving:** Fusie-incidenten van dit type geven aan dat WMI-opdrachten (Windows Management Interface) op afstand werden uitgevoerd op een systeem, en dat er verdachte binnenkomende activiteiten zijn gedetecteerd door de firewall Palo Alto Networks. Dit geeft aan dat een aanvaller toegang heeft verkregen tot uw netwerk en probeert zich op een later tijdstip te verplaatsen, bevoegdheden te escaleren en/of schadelijke nettoladingen uit te voeren. Net als bij alle ' bewoonde ' aanvallen op het land ' kan deze activiteit een authentiek gebruik zijn van WMI. De uitvoering van de externe WMI-opdracht gevolgd door de verdachte binnenkomende firewall activiteit verhoogt echter het vertrouwen dat WMI op een schadelijke manier wordt gebruikt en moet verder worden onderzocht. In Palo Alto-Logboeken wordt Azure Sentinel gericht op [bedreigings logboeken](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)en verkeer wordt beschouwd als verdacht Wanneer bedreigingen zijn toegestaan (verdachte gegevens, bestanden, flooden, pakketten, scans, spyware, url's, virussen, beveiligings problemen, bosbrand-virussen, Wildfires). U kunt ook verwijzen naar het Palo Alto-bedreigings logboek dat overeenkomt met het [type bedreiging/inhoud](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) dat wordt vermeld in de beschrijving van het fusie incident voor aanvullende waarschuwings Details.

### <a name="suspicious-powershell-command-line-following-suspicious-sign-in"></a>Verdachte Power shell-opdracht regel na verdacht aanmelden

**Mitre ATT&VERzonken tactiek:** Initiële toegang, uitvoering

**Mitre ATT&VERzonken technieken:** Geldig account (T1078), opdracht-en scripting-interpreter (T1059)

**Gegevens connector bronnen:** Azure Active Directory Identity Protection, micro soft Defender voor het eind punt (voorheen MDATP)

**Beschrijving:** Fusion incidenten van dit type geven aan dat een gebruiker mogelijk schadelijke Power shell-opdrachten heeft uitgevoerd na een verdachte aanmelding bij een Azure AD-account. Dit biedt een hoge mate van betrouw baarheid dat het account dat wordt genoteerd in de beschrijving van de waarschuwing, is aangetast en er nog andere schadelijke acties zijn uitgevoerd. Aanvallers maken gebruik van Power shell om schadelijke nettoladingen in het geheugen uit te voeren zonder artefacten op de schijf te verlaten, om detectie te voor komen door beveiligings mechanismen op basis van schijven zoals virus scanners. De permutaties van verdachte Azure AD-aanmeldings waarschuwingen met de verdachte Power shell-opdracht waarschuwing zijn:

- **Onmogelijke reis naar ongewone locaties die leiden tot verdachte Power shell-opdracht regel**

- **Aanmeldings gebeurtenis vanaf een onbekende locatie die leidt tot verdachte Power shell-opdracht regel**

- **Aanmeldings gebeurtenis vanaf een geïnfecteerd apparaat dat verdacht is op de opdracht regel van verdachte Power shell**

- **Aanmeldings gebeurtenis vanaf een anoniem IP-adres dat leidt tot verdachte Power shell-opdracht regel**

- **Aanmeldings gebeurtenis van de gebruiker met de gelekte referenties voor de verdachte Power shell-opdracht regel**

## <a name="malware-c2-or-download"></a>Malware C2 of downloaden

### <a name="network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Netwerk aanvraag naar TOR anoniem maken-service, gevolgd door afwijkend verkeer dat is gemarkeerd met de firewall van Palo Alto Networks.
Dit scenario is momenteel beschikbaar als **Preview-versie**.

**Mitre ATT&VERzonken tactiek:** Opdracht en controle

**Mitre ATT&VERzonken technieken:** Versleuteld kanaal (T1573), proxy (T1090)

**Gegevens connector bronnen:** Micro soft Defender voor eind punt (voorheen MDATP), Palo Alto-netwerken 

**Beschrijving:** Fusie-incidenten van dit type geven aan dat er een uitgaande verbindings aanvraag is gedaan aan de TOR anoniem maken-service en dat afwijkende binnenkomende activiteiten zijn gedetecteerd door de firewall van de Palo Alto-netwerken. Dit geeft een indicatie dat een aanvaller toegang heeft tot uw netwerk en de acties en intentie probeert te verbergen. Verbindingen met het TOR-netwerk na dit patroon kan een indicatie zijn van de opdracht-en controle activiteit voor schadelijke software, aanvragen voor het downloaden van extra malware of een aanvaller die externe interactieve toegang tot stand brengt. In Palo Alto-Logboeken wordt Azure Sentinel gericht op [bedreigings logboeken](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)en verkeer wordt beschouwd als verdacht Wanneer bedreigingen zijn toegestaan (verdachte gegevens, bestanden, flooden, pakketten, scans, spyware, url's, virussen, beveiligings problemen, bosbrand-virussen, Wildfires). U kunt ook verwijzen naar het Palo Alto-bedreigings logboek dat overeenkomt met het [type bedreiging/inhoud](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) dat wordt vermeld in de beschrijving van het fusie incident voor aanvullende waarschuwings Details.

### <a name="outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Uitgaande verbinding met IP met een geschiedenis van ongeoorloofde toegangs pogingen gevolgd door afwijkend verkeer dat is gemarkeerd door de firewall Palo Alto Networks
Dit scenario is momenteel beschikbaar als **Preview-versie**.

**Mitre ATT&VERzonken tactiek:** Opdracht en controle

**Mitre ATT&VERzonken technieken:** Niet van toepassing

**Gegevens connector bronnen:** Micro soft Defender voor eind punt (voorheen MDATP), Palo Alto-netwerken 

**Beschrijving:** Fusie-incidenten van dit type geven aan dat er een uitgaande verbinding met een IP-adres met een geschiedenis van ongeoorloofde toegang is tot stand is gebracht, en dat er afwijkende activiteiten zijn gedetecteerd door de firewall van de Palo Alto-netwerken. Dit geeft een indicatie dat een aanvaller waarschijnlijk toegang tot uw netwerk heeft verkregen. Verbindings pogingen na dit patroon kan een indicatie zijn van malware-opdracht-en controle activiteiten, aanvragen voor het downloaden van extra malware of een aanvaller die externe interactieve toegang tot stand brengt. In Palo Alto-Logboeken wordt Azure Sentinel gericht op [bedreigings logboeken](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)en verkeer wordt beschouwd als verdacht Wanneer bedreigingen zijn toegestaan (verdachte gegevens, bestanden, flooden, pakketten, scans, spyware, url's, virussen, beveiligings problemen, bosbrand-virussen, Wildfires). U kunt ook verwijzen naar het Palo Alto-bedreigings logboek dat overeenkomt met het [type bedreiging/inhoud](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) dat wordt vermeld in de beschrijving van het fusie incident voor aanvullende waarschuwings Details.

## <a name="ransomware"></a>Ransomware

### <a name="ransomware-execution-following-suspicious-azure-ad-sign-in"></a>Ransomware-uitvoering na verdachte aanmelding bij Azure AD

**Mitre ATT&VERzonken tactiek:** Initiële toegang, impact

**Mitre ATT&VERzonken technieken:** Geldig account (T1078), gegevens versleuteld voor impact (T1486)

**Gegevens connector bronnen:** Microsoft Cloud App Security, Azure Active Directory Identity Protection

**Beschrijving:** Fusion incidenten van dit type geven aan dat afwijkende gebruikers gedrag dat aangeeft dat er een Ransomware-aanval is gedetecteerd na een verdachte aanmelding bij een Azure AD-account. Deze indicatie biedt een hoge mate van zekerheid dat het account dat is genoteerd in de beschrijving van het fusie incident is aangetast en is gebruikt om gegevens te versleutelen voor de extorting van de gegevens eigenaar of de gegevens eigenaar toegang tot hun gegevens te weigeren. De permutaties van verdachte Azure AD-aanmeldings waarschuwingen met de uitvoerings waarschuwing voor Ransomware zijn:  

- **Onmogelijke reis naar een ongewoone locatie voor Ransomware in de Cloud-app**

- **Aanmeldings gebeurtenis vanaf een onbekende locatie die leidt tot Ransomware in de Cloud-app**

- **Aanmeldings gebeurtenis vanaf een geïnfecteerd apparaat die tot Ransomware kan leiden in de Cloud-app**

- **Aanmeldings gebeurtenis vanaf een anoniem IP-adres dat leidt tot Ransomware in de Cloud-app**

- **Aanmeldings gebeurtenis van gebruiker met gelekte referenties die leiden tot Ransomware in de Cloud-app**

## <a name="remote-exploitation"></a>Externe exploitatie

### <a name="suspected-use-of-attack-framework-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Verdacht gebruik van een aanvals raamwerk gevolgd door afwijkend verkeer dat is gemarkeerd met de firewall Palo Alto Networks
Dit scenario is momenteel beschikbaar als **Preview-versie**.

**Mitre ATT&VERzonken tactiek:** Initiële toegang, uitvoering, zijdelingse verplaatsing, escalatie van bevoegdheden

**Mitre ATT&VERzonken technieken:** Exploiting Public-Facing Application (T1190), uitbuiting for client Execution (T1203), uitbuiting of Remote Services (T1210), uitbuiting for bevoegdheden escalation (T1068)

**Gegevens connector bronnen:** Micro soft Defender voor eind punt (voorheen MDATP), Palo Alto-netwerken 

**Beschrijving:** Fusie-incidenten van dit type geven aan dat niet-standaard gebruik van protocollen, zoals het gebruik van Metasploit, zijn gedetecteerd en dat er verdachte inkomende activiteiten zijn gedetecteerd door de firewall van de Palo Alto-netwerken. Dit kan een eerste indicatie zijn dat een aanvaller een service heeft misbruikt om toegang te krijgen tot uw netwerk bronnen of dat een aanvaller al toegang heeft gekregen en dat er meer beschik bare systemen/services kunnen worden misbruikt om de bevoegdheden op een later tijdstip te verplaatsen en/of te escaleren. In Palo Alto-Logboeken wordt Azure Sentinel gericht op [bedreigings logboeken](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)en verkeer wordt beschouwd als verdacht Wanneer bedreigingen zijn toegestaan (verdachte gegevens, bestanden, flooden, pakketten, scans, spyware, url's, virussen, beveiligings problemen, bosbrand-virussen, Wildfires). U kunt ook verwijzen naar het Palo Alto-bedreigings logboek dat overeenkomt met het [type bedreiging/inhoud](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) dat wordt vermeld in de beschrijving van het fusie incident voor aanvullende waarschuwings Details.

## <a name="next-steps"></a>Volgende stappen

Nu u meer hebt geleerd over geavanceerde detectie van aanvallen met meerdere fasen, bent u mogelijk geïnteresseerd in de volgende Snelstartgids om te leren hoe u uw gegevens en mogelijke bedreigingen kunt zien: aan de [slag met Azure Sentinel](quickstart-get-visibility.md).

Als u klaar bent voor het onderzoeken van de incidenten die voor u zijn gemaakt, raadpleegt u de volgende zelf studie: [incidenten onderzoeken met Azure Sentinel](tutorial-investigate-cases.md).

