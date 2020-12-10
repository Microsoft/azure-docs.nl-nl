---
title: 'Zelfstudie: LDAPS configureren voor Azure Active Directory Domain Services | Microsoft Docs'
description: In deze zelfstudie leert u hoe u LDAPS (Secure Lightweight Directory Access Protocol) configureert voor een door Azure Active Directory Domain Services beheerd domein.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: d5dbb7b71e2d67ed5b3f624c93c3c143d6c98e5d
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2020
ms.locfileid: "96618532"
---
# <a name="tutorial-configure-secure-ldap-for-an-azure-active-directory-domain-services-managed-domain"></a>Zelfstudie: Secure LDAP configureren voor een door Azure Active Directory Domain Services beheerd domein

Als u met een door Azure Active Directory Domain Services (Azure AD DS) beheerd domein wilt communiceren, wordt LDAP (Lightweight Directory Access Protocol) gebruikt. Het LDAP-verkeer wordt standaard niet versleuteld, hetgeen een beveiligingsprobleem vormt voor veel omgevingen.

U kunt met Azure AD DS het beheerde domein zo configureren dat deze Secure Lightweight Directory Access Protocol (LDAPS) gebruikt. Wanneer u Secure LDAP gebruikt, wordt het verkeer versleuteld. Secure LDAP is ook wel bekend als LDAP via Secure Sockets Layer (SSL)/Transport Layer Security (TLS).

Deze zelfstudie laat zien hoe u LDAPS configureert voor een door Azure AD DS beheerd domein.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een digitaal certificaat maken dat met Azure AD DS kan worden gebruikt
> * Secure LDAP inschakelen voor Azure AD DS
> * Secure LDAP configureren zodat dit via openbaar internet kan worden gebruikt
> * Secure LDAP binden en testen voor een beheerd domein

Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om deze zelfstudie te voltooien, hebt u de volgende resources en machtigingen nodig:

* Een actief Azure-abonnement.
    * Als u nog geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een Azure Active Directory-tenant die aan uw abonnement is gekoppeld, gesynchroniseerd met een on-premises map of een cloudmap.
    * [Maak zo nodig een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account][associate-azure-ad-tenant].
* Een door Azure Active Directory Domain Services beheerd domein dat in uw Azure AD-tenant is ingeschakeld en geconfigureerd.
    * [Maak en configureer zo nodig een door Azure Active Directory Domain Services beheerd domein][create-azure-ad-ds-instance].
* Het hulpprogramma *LDP.exe* dat op uw computer moet zijn geïnstalleerd.
    * [Installeer zo nodig de Remote Server Administration Tools (RSAT)][rsat] voor *Active Directory Domain Services en LDAP*.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

In deze zelfstudie configureert u Secure LDAP voor het beheerde domein met behulp van Azure Portal. Meld u eerst aan bij [Azure Portal](https://portal.azure.com) om aan de slag te gaan.

## <a name="create-a-certificate-for-secure-ldap"></a>Een certificaat maken voor Secure LDAP

Als u Secure LDAP wilt gebruiken, wordt er een digitaal certificaat gebruikt om de communicatie te versleutelen. Dit digitale certificaat wordt toegepast op uw beheerde domein en staat hulpprogramma's als *LDP.exe* toe om beveiligde versleutelde communicatie te gebruiken bij het opvragen van gegevens. Er zijn twee manieren om een certificaat voor Secure LDAP-toegang tot het beheerde domein te maken:

* Een certificaat van een openbare certificeringsinstantie (CA) of een certificeringsinstantie voor ondernemingen.
    * Als uw organisatie certificaten van een openbare certificeringsinstantie krijgt, haalt u het Secure LDAP-certificaat van die openbare certificeringsinstantie op. Als u een certificeringsinstantie voor ondernemingen in uw organisatie gebruikt, haalt u het Secure LDAP-certificaat van de certificeringsinstantie voor ondernemingen op.
    * Een openbare certificeringsinstantie werkt alleen wanneer u een aangepaste DNS-naam gebruikt met uw beheerde domein. Als de DNS-domeinnaam van uw beheerde domein eindigt op *.onmicrosoft.com*, kunt u geen digitaal certificaat maken om de verbinding met dit standaarddomein te beveiligen. Microsoft is eigenaar van het domein *.onmicrosoft.com*, dus een openbare certificeringsinstantie zal geen certificaat uitgeven. In dit scenario maakt u een zelfondertekend certificaat en gebruikt u dat voor het configureren van Secure LDAP.
* Een zelfondertekend certificaat dat u zelf maakt.
    * Deze methode is geschikt voor testdoeleinden en wordt in deze zelfstudie beschreven.

Het certificaat dat u wilt aanvragen of maken, moet voldoen aan de volgende vereisten. Het beheerde domein ondervindt problemen als u Secure LDAP inschakelt met een ongeldig certificaat:

* **Vertrouwde uitgever**: het certificaat moet worden uitgegeven door een instantie die wordt vertrouwd door computers die verbinding maken met het beheerde domein met behulp van Secure LDAP. Deze instantie kan een openbare certificeringsinstantie of een certificeringsinstantie voor ondernemingen zijn die door deze computers wordt vertrouwd.
* **Levensduur**: het certificaat moet ten minste de volgende 3-6 maanden geldig zijn. Secure LDAP-toegang tot uw beheerde domein wordt onderbroken wanneer het certificaat is verlopen.
* **Onderwerpnaam**: de onderwerpnaam op het certificaat moet uw beheerde domein zijn. Als de naam van uw domein bijvoorbeeld *aaddscontoso.com* is, moet de onderwerpnaam van het certificaat * *.aaddscontoso.com* zijn.
    * De DNS-naam of de alternatieve naam voor het onderwerp van het certificaat moet een jokertekencertificaat zijn om ervoor te zorgen dat Secure LDAP juist werkt met Azure AD Domain Services. Domeincontrollers gebruiken willekeurige namen en kunnen worden verwijderd of toegevoegd om ervoor te zorgen dat de service beschikbaar blijft.
* **Sleutelgebruik**: het certificaat moet zijn geconfigureerd voor *digitale handtekeningen* en *sleutelcodering*.
* **Certificaatdoeleinde**: het certificaat moet geldig zijn voor TLS-serververificatie.

U kunt verschillende hulpprogramma's gebruiken voor het maken van een zelfondertekend certificaat, zoals OpenSSL, Keytool, MakeCert, de [New-SelfSignedCertificate][New-SelfSignedCertificate]-cmdlet, enzovoort.

In deze zelfstudie maakt u een zelfondertekend certificaat voor Secure LDAP met de [New-SelfSignedCertificate][New-SelfSignedCertificate]-cmdlet.

Open een PowerShell-venster als **beheerder** en voer de volgende opdrachten uit. Vervang de variabele *$dnsName* door de DNS-naam die wordt gebruikt door uw eigen beheerde domein, zoals *aaddscontoso.com*:

```powershell
# Define your own DNS name used by your managed domain
$dnsName="aaddscontoso.com"

# Get the current date to set a one-year expiration
$lifetime=Get-Date

# Create a self-signed certificate for use with Azure AD DS
New-SelfSignedCertificate -Subject *.$dnsName `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName
```

In het volgende uitvoervoorbeeld ziet u dat het certificaat is gegenereerd en is opgeslagen in het lokale certificaatarchief (*LocalMachine\MY*):

```output
PS C:\WINDOWS\system32> New-SelfSignedCertificate -Subject *.$dnsName `
>>   -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
>>   -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName.com

   PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\MY

Thumbprint                                Subject
----------                                -------
959BD1531A1E674EB09E13BD8534B2C76A45B3E6  CN=aaddscontoso.com
```

## <a name="understand-and-export-required-certificates"></a>Weten wat de vereiste certificaten zijn en deze exporteren

Om Secure LDAP te gebruiken, wordt het netwerkverkeer versleuteld met behulp van Public Key Infrastructure (PKI).

* Er wordt een **persoonlijke** sleutel toegepast op het beheerde domein.
    * Deze persoonlijke sleutel wordt gebruikt om het Secure LDAP-verkeer te *ontsleutelen*. De persoonlijke sleutel moet alleen worden toegepast op het beheerde domein en niet wijdverbreid over clientcomputers worden gedistribueerd.
    * Een certificaat dat de persoonlijke sleutel bevat, heeft de bestandsindeling *.pfx*.
    * Het versleutelingsalgoritme voor het certificaat moet *TripleDES-SHA1* zijn.
* Een **openbare** sleutel wordt toegepast op de clientcomputers.
    * Deze openbare sleutel wordt gebruikt om het beveiligde LDAP-verkeer te *versleutelen*. De openbare sleutel kan worden gedistribueerd naar clientcomputers.
    * Certificaten zonder de persoonlijke sleutel hebben de bestandsindeling *.cer*.

Deze twee sleutels, de *persoonlijke* en de *openbare* sleutel, zorgen ervoor dat alleen de juiste computers met elkaar kunnen communiceren. Als u een openbare certificeringsinstantie of een certificeringsinstantie voor ondernemingen gebruikt, wordt er aan u een certificaat verleend dat de persoonlijke sleutel bevat en dat kan worden toegepast op een beheerd domein. De openbare sleutel moet al bekend zijn en worden vertrouwd door clientcomputers.

In deze zelfstudie hebt u een zelfondertekend certificaat gemaakt met de persoonlijke sleutel, dus u moet de betreffende persoonlijke en openbare onderdelen exporteren.

### <a name="export-a-certificate-for-azure-ad-ds"></a>Een certificaat voor Azure AD DS exporteren

Voordat u het digitale certificaat dat u hebt gemaakt in de vorige stap kunt gebruiken met uw beheerde domein, exporteert u het certificaat naar een *PFX*-certificaatbestand dat de persoonlijke sleutel bevat.

1. Als u het dialoogvenster *Uitvoeren* wilt openen, selecteert u de toetsen **Windows** + **R**.
1. Open Microsoft Management Console (MMC) door **mmc** in te voeren in het dialoogvenster *Uitvoeren* en selecteer vervolgens **OK**.
1. Selecteer in de **Gebruikersaccountbeheer**-prompt **Ja** om MMC als beheerder te starten.
1. Selecteer in het menu **Bestand** de optie **Module toevoegen/verwijderen...** .
1. Kies in de wizard **De module Certificaten** de optie **Computeraccount** en selecteer vervolgens **Volgende**.
1. Kies op de pagina **Computer selecteren** de optie **Lokale computer: (de computer waarop deze console wordt uitgevoerd)** en selecteer vervolgens **Voltooien**.
1. Selecteer **OK** in het dialoogvenster **Modules toevoegen of verwijderen** om de module Certificaten aan MMC toe te voegen.
1. Vouw **Consolebasis** uit in het MMC-venster. Selecteer **Certificaten (lokale computer)** en vouw vervolgens het knooppunt **Persoonlijk** uit, gevolgd door het knooppunt **Certificaten**.

    ![Het archief met persoonlijke certificaten openen in Microsoft Management Console](./media/tutorial-configure-ldaps/open-personal-store.png)

1. Het zelfondertekende certificaat dat u hebt gemaakt in de vorige stap, bijvoorbeeld *aaddscontoso.com*, wordt weergegeven. Selecteer met de rechtermuisknop dit certificaat en kies vervolgens **Alle taken > Exporteren...**

    ![Een certificaat exporteren in Microsoft Management Console](./media/tutorial-configure-ldaps/export-cert.png)

1. Selecteer **Volgende** in de **wizard Certificaat exporteren**.
1. De persoonlijke sleutel voor het certificaat moet worden geëxporteerd. Als de persoonlijke sleutel niet is opgenomen in het geëxporteerde certificaat, wordt Secure LDAP niet ingeschakeld voor uw beheerde domein.

    Kies op de pagina **Persoonlijke sleutel exporteren** de optie **Ja, exporteer de persoonlijke sleutel** en klik op **Volgende**.
1. Beheerde domeinen bieden alleen ondersteuning voor certificaten met de bestandsindeling *.pfx* die de persoonlijke sleutel bevatten. Exporteer het certificaat niet als een certificaat met de bestandsindeling *.cer* zonder de persoonlijke sleutel.

    Selecteer op de pagina **Bestandsindeling voor export** de optie **Personal Information Exchange - PKCS #12 (.pfx)** als de bestandsindeling voor het geëxporteerde certificaat. Schakel het selectievakje voor *Indien mogelijk alle certificaten in het certificeringspad opnemen* in:

    ![Kies de optie voor het exporteren van het certificaat in de PFX-bestandsindeling (PKCS 12)](./media/tutorial-configure-ldaps/export-cert-to-pfx.png)

1. Omdat dit certificaat wordt gebruikt om gegevens te ontsleutelen, moet u de toegang zorgvuldig regelen. U kunt een wachtwoord toepassen om het gebruik van het certificaat te beveiligen. Zonder het juiste wachtwoord kan het certificaat niet worden toegepast op een service.

    Kies op de pagina **Beveiliging** de optie voor **Wachtwoord** om het *PFX*-certificaatbestand te beveiligen. Het versleutelingsalgoritme moet *TripleDES-SHA1* zijn. Voer een wachtwoord in en bevestig dit. Selecteer vervolgens **Volgende**. Dit wachtwoord wordt in de volgende sectie gebruikt om Secure LDAP in te schakelen voor uw beheerde domein.
1. Geef op de pagina **Bestand dat u wilt exporteren** de bestandsnaam en de locatie waar u het certificaat wilt exporteren op, bijvoorbeeld *C:\Users\accountname\azure-ad-ds.pfx*. Noteer het wachtwoord en de locatie van het *PFX*-bestand omdat u deze informatie in de volgende stappen nodig hebt.
1. Selecteer **Voltooien** op de controlepagina om het certificaat naar een *PFX*-certificaatbestand te exporteren. Er wordt een dialoogvenster voor bevestiging weergegeven wanneer het certificaat is geëxporteerd.
1. Laat MMC geopend voor gebruik in de volgende sectie.

### <a name="export-a-certificate-for-client-computers"></a>Een certificaat voor clientcomputers exporteren

Clientcomputers moeten de uitgever van het Secure LDAP-certificaat vertrouwen om met behulp van LDAPS verbinding te kunnen maken met het beheerde domein. De clientcomputers hebben een certificaat nodig om gegevens te versleutelen die door Azure AD DS worden ontsleuteld. Als u een openbare certificeringsinstantie gebruikt, moet de computer deze certificaatverleners automatisch vertrouwen en over een overeenkomstig certificaat beschikken.

In deze zelfstudie gebruikt u een zelfondertekend certificaat en hebt u in de vorige stap een certificaat gegenereerd dat de persoonlijke sleutel bevat. We gaan nu het zelfondertekende certificaat exporteren en installeren in het vertrouwde certificaatarchief op de clientcomputer:

1. Ga terug naar MMC voor het archief via *Certificaten (lokale computer) > Persoonlijk > Certificaten*. Het zelfondertekende certificaat dat u in een vorige stap hebt gemaakt, bijvoorbeeld *aaddscontoso.com*, wordt weergegeven. Selecteer met de rechtermuisknop op dit certificaat en kies vervolgens **Alle taken > Exporteren...**
1. Selecteer **Volgende** in de **wizard Certificaat exporteren**.
1. Omdat u de persoonlijke sleutel voor clients niet nodig hebt, kiest u op de pagina **Persoonlijke sleutel exporteren** de optie **Nee, de persoonlijke sleutel niet exporteren** en selecteert u vervolgens **Volgende**.
1. Selecteer op de pagina **Bestandsindeling voor export** de optie **Base-64 encoded X.509 (.cer)** als de bestandsindeling voor het geëxporteerde certificaat:

    ![Kies de optie voor het exporteren van het certificaat in de CER-bestandsindeling (Base-64 encoded X.509)](./media/tutorial-configure-ldaps/export-cert-to-cer-file.png)

1. Geef op de pagina **Bestand dat u wilt exporteren** de bestandsnaam en de locatie op waar u het certificaat wilt exporteren, bijvoorbeeld *C:\Users\accountname\azure-ad-ds-client.cer*.
1. Selecteer **Voltooien** op de controlepagina om het certificaat naar een *CER*-certificaatbestand te exporteren. Er wordt een dialoogvenster voor bevestiging weergegeven wanneer het certificaat is geëxporteerd.

Het *.CER*-certificaatbestand kan nu worden gedistribueerd naar clientcomputers die de Secure LDAP-verbinding met het beheerde domein moeten vertrouwen. We gaan het certificaat nu op een lokale computer installeren.

1. Open Bestandenverkenner en blader naar de locatie waar u het *CER*-certificaatbestand hebt opgeslagen, bijvoorbeeld *C:\Users\accountname\azure-ad-ds-client.cer*.
1. Selecteer met de rechtermuisknop het *CER*-certificaatbestand en kies **Certificaat installeren**.
1. Kies er in de **wizard Certificaat importeren** voor om het certificaat op te slaan op de *lokale computer* en selecteer vervolgens **Volgende**:

    ![Kies voor de optie om het certificaat in het archief op de lokale computer te importeren](./media/tutorial-configure-ldaps/import-cer-file.png)

1. Wanneer u hierom wordt gevraagd, kiest u **Ja**, zodat de computer wijzigingen kan aanbrengen.
1. Kies **Automatisch het certificaatarchief selecteren op basis van het type certificaat** en selecteer vervolgens **Volgende**.
1. Selecteer **Voltooien** op de controlepagina om het *.CER*-certificaatbestand te importeren. Er wordt een dialoogvenster voor bevestiging weergegeven wanneer het certificaat is geïmporteerd.

## <a name="enable-secure-ldap-for-azure-ad-ds"></a>Secure LDAP inschakelen voor Azure AD DS

Nu er een digitaal certificaat is gemaakt en geëxporteerd dat de persoonlijke sleutel bevat en de clientcomputer is ingesteld om de verbinding te vertrouwen, schakelt u nu in uw beheerde domein Secure LDAP in. Als u Secure LDAP wilt inschakelen in een beheerd domein, voert u de volgende configuratiestappen uit:

1. Typ in [Azure Portal](https://portal.azure.com) de tekst *domain services* in het vak **Resources zoeken**. Selecteer **Azure AD Domain Services** in de zoekresultaten.
1. Kies uw beheerde domein, bijvoorbeeld *aaddscontoso.com*.
1. Kies aan de linkerkant van het Azure AD DS-venster de optie **Secure LDAP**.
1. De Secure LDAP-toegang tot uw beheerde domein is standaard uitgeschakeld. Gebruik de schakeloptie voor **Secure LDAP** om dit **in te schakelen**.
1. De Secure LDAP-toegang tot uw beheerde domein via internet is standaard uitgeschakeld. Wanneer u openbare Secure LDAP-toegang inschakelt, is uw domein kwetsbaar voor beveiligingsaanvallen op wachtwoorden via internet. In de volgende stap wordt een netwerkbeveiligingsgroep geconfigureerd om de toegang tot uitsluitend de vereiste bron-IP-adresbereiken te beperken.

    Gebruik de schakeloptie voor **Secure LDAP-toegang via internet toestaan** om dit **in te schakelen**.

1. Selecteer het mappictogram naast **PFX-bestand met Secure LDAP-certificaat**. Blader naar het pad van het *PFX*-bestand en selecteer vervolgens het certificaat dat u in een vorige stap hebt gemaakt en dat de persoonlijke sleutel bevat.

    > [!IMPORTANT]
    > Zoals al in de vorige sectie over certificaatvereisten werd vermeld, kunt u geen certificaat van een openbare certificeringsinstantie gebruiken met het standaarddomein *.onmicrosoft.com*. Microsoft is eigenaar van het domein *.onmicrosoft.com*, dus een openbare certificeringsinstantie zal geen certificaat uitgeven.
    >
    > Zorg ervoor dat uw certificaat de juiste indeling heeft. Als dat niet het geval is, genereert het Azure-platform certificaatvalidatiefouten wanneer u Secure LDAP inschakelt.

1. Voer het **wachtwoord voor het ontsleutelen van het PFX-bestand** in dat in een vorige stap is ingesteld toen het certificaat naar een *PFX*-bestand werd geëxporteerd.
1. Selecteer **Opslaan** om Secure LDAP in te schakelen.

    ![Secure LDAP inschakelen voor een beheerd domein in Azure Portal](./media/tutorial-configure-ldaps/enable-ldaps.png)

Er wordt een melding weergegeven dat Secure LDAP wordt geconfigureerd voor het beheerde domein. U kunt geen andere instellingen voor het beheerde domein wijzigen totdat deze bewerking is voltooid.

Het duurt enkele minuten om Secure LDAP in te schakelen voor uw beheerde domein. Als het Secure LDAP-certificaat dat u opgeeft niet voldoet aan de vereiste criteria, kunt u Secure LDAP niet inschakelen voor het beheerde domein.

Enkele veelvoorkomende redenen hiervoor zijn een onjuiste domeinnaam, het versleutelingsalgoritme voor het certificaat is niet ingesteld op *TripleDES-SHA1* of het certificaat verloopt binnenkort of is al verlopen. U kunt het certificaat opnieuw maken met geldige parameters en vervolgens Secure LDAP inschakelen met dit bijgewerkte certificaat.

## <a name="lock-down-secure-ldap-access-over-the-internet"></a>Secure LDAP-toegang via internet beperken

Wanneer u Secure LDAP-toegang via internet tot uw beheerde domein inschakelt, zorgt dit voor een beveiligingsrisico. Het beheerde domein is vanaf internet bereikbaar op TCP-poort 636. U kunt het beste de toegang tot het beheerde domein beperken tot bepaalde bekende IP-adressen voor uw omgeving. U kunt een Azure-netwerkbeveiligingsgroepregel gebruiken om de toegang tot Secure LDAP te beperken.

We gaan een regel maken om inkomend verkeer voor Secure LDAP-toegang via TCP-poort 636 vanaf een opgegeven reeks IP-adressen toe te staan. Een standaardregel *DenyAll* met een lagere prioriteit is van toepassing op al het andere inkomende verkeer vanaf internet, zodat alleen de opgegeven adressen uw beheerde domein kunnen bereiken met behulp van Secure LDAP.

1. Selecteer in Azure Portal in het navigatiemenu aan de linkerkant de optie *Resourcegroepen*.
1. Kies uw resourcegroep, bijvoorbeeld *myResourceGroup* en selecteer vervolgens uw netwerkbeveiligingsgroep, bijvoorbeeld *aaads-nsg*.
1. De lijst met bestaande beveiligingsregels voor inkomend en uitgaand verkeer worden weergegeven. Kies aan de linkerkant van de netwerkbeveiligingsgroepvensters de optie **Instellingen > Beveiligingsregels voor inkomend verkeer**.
1. Selecteer **Toevoegen** en maak vervolgens een regel om *TCP*-poort *636* toe te staan. Voor een betere beveiliging kiest u *IP-adressen* als de bron en geeft u vervolgens uw eigen geldige IP-adres of -bereik voor uw organisatie op.

    | Instelling                           | Waarde        |
    |-----------------------------------|--------------|
    | Bron                            | IP-adressen |
    | IP-adressen/CIDR-bereiken van bron | Een geldig IP-adres of -bereik voor uw omgeving |
    | Poortbereiken van bron                | *            |
    | Doel                       | Alle          |
    | Poortbereiken van doel           | 636          |
    | Protocol                          | TCP          |
    | Bewerking                            | Toestaan        |
    | Prioriteit                          | 401          |
    | Naam                              | AllowLDAPS   |

1. Wanneer u klaar bent, selecteert u **Toevoegen** om de regel op te slaan en toe te passen.

    ![Een netwerkbeveiligingsgroepregel maken om Secure LDAPS-toegang via internet te beveiligen](./media/tutorial-configure-ldaps/create-inbound-nsg-rule-for-ldaps.png)

## <a name="configure-dns-zone-for-external-access"></a>DNS-zone voor externe toegang configureren

Wanneer Secure LDAP-toegang via internet is ingeschakeld, werkt u de DNS-zone bij zodat clientcomputers dit beheerde domein kunnen vinden. Het *externe IP-adres voor Secure LDAP* wordt vermeld op het tabblad **Eigenschappen** voor uw beheerde domein:

![Het externe IP-adres voor Secure LDAP voor uw beheerde domein weergeven in Azure Portal](./media/tutorial-configure-ldaps/ldaps-external-ip-address.png)

Configureer uw externe DNS-provider voor het maken van een hostrecord, bijvoorbeeld *ldaps*, om naar dit externe IP-adres om te zetten. Als u eerst lokaal op uw computer wilt testen, kunt u een vermelding in het bestand met Windows-hosts maken. Als u het bestand met hosts op uw lokale computer wilt bewerken, opent u *Kladblok* als beheerder en opent u het bestand *C:\Windows\System32\drivers\etc\hosts*

In het volgende voorbeeld van een DNS-vermelding, bij uw externe DNS-provider of in het lokale bestand met hosts, wordt het verkeer voor *ldaps.aaddscontoso.com* omgezet naar het externe IP-adres *168.62.205.103*:

```
168.62.205.103    ldaps.aaddscontoso.com
```

## <a name="test-queries-to-the-managed-domain"></a>Query's voor het beheerde domein testen

Als u verbinding en binding wilt maken met uw beheerde domein, en LDAP wilt doorzoeken, gebruikt u het hulpprogramma *LDP.exe*. Dit hulpprogramma maakt deel uit van het RSAT-pakket (Remote Server Administration Tools). Zie [Remote Server Administration Tools installeren][rsat] voor meer informatie.

1. Open *LDP.exe* en maak verbinding met het beheerde domein. Selecteer **Verbinding** en kies vervolgens **Verbinding maken...** .
1. Voer de DNS-domeinnaam van uw beheerde domein voor Secure LDAP in die in de vorige stap is gemaakt, bijvoorbeeld *ldaps.aaddscontoso.com*. Als u Secure LDAP wilt gebruiken, stelt u **Poort** in op *636* en schakelt u het selectievakje voor **SSL** in.
1. Selecteer **OK** en maak verbinding met het beheerde domein.

Maak vervolgens een binding met het beheerde domein. Gebruikers (en serviceaccounts) kunnen geen eenvoudige bindingen voor LDAP uitvoeren als NTLM-wachtwoordhashsynchronisatie is uitgeschakeld voor uw beheerde domein. Zie [Uw beheerde domein beveiligen][secure-domain] voor meer informatie over het uitschakelen van NTLM-wachtwoordhashsynchronisatie.

1. Selecteer de menuoptie **Verbinding** en kies vervolgens **Binden...** .
1. Geef de aanmeldingsgegevens van een gebruikersaccount op dat bij het beheerde domein hoort. Voer het wachtwoord van het gebruikersaccount in en voer vervolgens uw domein in, bijvoorbeeld *aaddscontoso.com*.
1. Kies voor **Bindingstype** de optie *Binden met referenties*.
1. Selecteer **OK** om het account aan het beheerde domein te binden.

U kunt als volgt zien welke objecten in uw beheerde domein zijn opgeslagen:

1. Selecteer de menuoptie **Weergave** en kies vervolgens **Structuur**.
1. Laat het veld *BaseDN* leeg en selecteer vervolgens **OK**.
1. Kies een container, zoals *AADDC-gebruikers*, klik met de rechtermuisknop op de container en kies **Zoeken**.
1. Laat de vooraf ingevulde velden ongewijzigd en selecteer vervolgens **Uitvoeren**. De resultaten van de query worden weergegeven in het venster aan de rechterkant, zoals wordt weergegeven in het volgende uitvoervoorbeeld:

    ![Zoeken naar objecten in uw beheerde domein met behulp van LDP.exe](./media/tutorial-configure-ldaps/ldp-query.png)

Als u rechtstreeks een query wilt uitvoeren op een specifieke container, kunt u in het menu **Weergave > Structuur** een **BaseDN** opgeven, bijvoorbeeld *OU=AADDC Users,DC=AADDSCONTOSO,DC=COM* of *OU=AADDC Computers,DC=AADDSCONTOSO,DC=COM*. Zie [Basisbeginselen voor LDAP-query's][ldap-query-basics] voor meer informatie over het opmaken en maken van query's.

## <a name="clean-up-resources"></a>Resources opschonen

Als u een DNS-vermelding hebt toegevoegd aan het bestand met lokale hosts van uw computer om de connectiviteit voor deze zelfstudie te testen, verwijdert u deze vermelding en voegt u een formeel record toe aan uw DNS-zone. Voer de volgende stappen uit om de vermelding uit het bestand met lokale hosts te verwijderen:

1. Open *Kladblok* als beheerder op uw lokale computer
1. Blader naar het bestand *C:\Windows\System32\drivers\etc\hosts* en open dit
1. Verwijder de regel voor de record die u hebt toegevoegd, bijvoorbeeld `168.62.205.103    ldaps.aaddscontoso.com`

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een digitaal certificaat maken dat met Azure AD DS kan worden gebruikt
> * Secure LDAP inschakelen voor Azure AD DS
> * Secure LDAP configureren zodat dit via openbaar internet kan worden gebruikt
> * Secure LDAP binden en testen voor een beheerd domein

> [!div class="nextstepaction"]
> [Wachtwoordhashsynchronisatie configureren voor een hybride Azure AD-omgeving](tutorial-configure-password-hash-sync.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[secure-domain]: secure-your-domain.md

<!-- EXTERNAL LINKS -->
[rsat]: /windows-server/remote/remote-server-administration-tools
[ldap-query-basics]: /windows/desktop/ad/creating-a-query-filter
[New-SelfSignedCertificate]: /powershell/module/pkiclient/new-selfsignedcertificate
