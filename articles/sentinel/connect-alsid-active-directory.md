---
title: Uw Alsid voor Active Directory verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het gebruik van de Alsid voor Active Directory-connector om Alsid-logboeken te verzamelen in azure Sentinel. Bekijk Alsid-gegevens in werkmappen, maak waarschuwingen en verbeter het onderzoek.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 654ecb65068e4321b85594d96e8ca7a7f73cde7e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99566731"
---
# <a name="connect-your-alsid-for-active-directory-ad-to-azure-sentinel"></a>Uw Alsid voor Active Directory (AD) verbinden met Azure Sentinel

> [!IMPORTANT]
> De Alsid voor de Active Directory-connector is momenteel beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.

In dit artikel wordt uitgelegd hoe u uw Alsid voor AD-oplossing verbindt met Azure Sentinel. Met de Alsid voor Active Directory Data Connector kunt u eenvoudig uw Alsid voor AD-logboeken verbinden met Azure Sentinel, zodat u de gegevens in werkmappen kunt weer geven, een query voor het maken van aangepaste waarschuwingen en het opnemen ter verbetering van het onderzoek. Integratie tussen Alsid voor AD en Azure Sentinel maakt gebruik van een syslog-server waarop de Log Analytics-agent is geïnstalleerd. Er wordt ook een aangepaste, gegenereerde logboek-parser gebruikt op basis van een Kusto-functie.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="prerequisites"></a>Vereisten

- U moet schrijf machtiging hebben voor de Azure Sentinel-werk ruimte.

- Uw Alsid voor AD-oplossing moet worden geconfigureerd voor het exporteren van Logboeken via syslog.

## <a name="send-alsid-for-ad-logs-to-azure-sentinel-via-the-syslog-agent"></a>Alsid voor AD-logboeken naar Azure-Sentinel verzenden via de syslog-agent

Configureer Alsid voor AD om syslog-berichten door te sturen naar uw Azure Sentinel-werk ruimte via de syslog-agent.

1. Selecteer in het navigatie menu van de Azure-Sentinel de optie **Data connectors**.

1. Selecteer in de galerie **gegevens connectors** de **Alsid voor de Active Directory-connector (preview-versie)** en open vervolgens de **pagina connector**.

1. Volg de instructies op de pagina **Alsid for Active Directory** connector:

    1. Een syslog-server configureren

        1. Als u er nog geen hebt, maakt u een Linux syslog-server voor Alsid voor AD om logboeken naar te verzenden. Azure Sentinel ondersteunt de **rsyslog** **-en syslog-ng-** daemons. 

        1. Configureer uw syslog-server voor het uitvoeren van Alsid voor AD-Logboeken in een afzonderlijk bestand.

    1. Alsid voor AD configureren voor het verzenden van logboeken naar uw syslog-server

        1. Ga in uw **Alsid voor de AD** -Portal naar *systeem*, *configuratie* en vervolgens *syslog*. Hier kunt u een nieuwe syslog-waarschuwing maken naar uw syslog-server. Voor de externe server gebruikt u het IP-adres van de Linux-computer waarop u de Linux-agent hebt geïnstalleerd.

        1. Controleer of de logboeken correct zijn verzameld op uw server in een afzonderlijk bestand (hiervoor kunt u de knop **de configuratie testen** in de configuratie van *syslog* -waarschuwingen in Alsid voor AD gebruiken.)

    1. De Log Analytics-agent voor Linux installeren en vrijgeven

        - Kies een Azure Linux-VM of een niet-Azure Linux-machine (fysiek of virtueel). Volg de koppelingen en instructies op het scherm. Zie [uw Linux-machine of-apparaat configureren](connect-syslog.md#configure-your-linux-machine-or-appliance) voor meer informatie.

    1. De logboeken configureren die moeten worden verzameld door de Log Analytics-agent

        - Selecteer de faciliteiten en ernst in de configuratie van de geavanceerde instellingen voor de werk ruimte.

            1. Klik op de koppeling **Open uw werk ruimte configuratie van geavanceerde instellingen >** op de pagina connector.

            1. Selecteer in het scherm **Geavanceerde instellingen** de optie **gegevens** en vervolgens de **aangepaste logboeken**.

            1. Schakel het selectie vakje de **onderstaande configuratie Toep assen op mijn Linux-machines** in en klik op **toevoegen**.

            1. Klik op **bestand kiezen** om een voor beeld van een Alsid voor het AD-syslog-bestand te uploaden van de Linux-computer waarop de syslog-server wordt uitgevoerd en klik op **volgende**.

            1. Controleer of **set record scheidings teken** instellen is ingesteld op **nieuwe regel** en klik op **volgende**.

            1. Selecteer **Linux** en voer het bestandspad naar het syslog-bestand in, klik op **+** en vervolgens op **volgende**.

            1. Typ *AlsidForADLog* in het veld naam vóór het achtervoegsel _CL en klik vervolgens op **gereed**.
    
Het kan Maxi maal 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven.

## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in **Logboeken**, onder **aangepaste logboeken** in de tabel *AlsidForADLog_CL* .

Deze gegevens connector is afhankelijk van een parser op basis van een Kusto-functie die naar verwachting werkt. Gebruik de volgende stappen om de **afad_parser** Kusto-functie in te stellen voor gebruik in query's en werkmappen.

1. Selecteer **Logboeken** in het navigatie menu van de Azure-Sentinel.

1. Kopieer de volgende query en plak deze in het query venster.
    ```kusto
    let CodenameTable=datatable(Codename: string, Explanation: string) [
    "test-checker-codename", "This is a test checker",
    "", "Not an alert",
    "C-ADM-ACC-USAGE", "Recent use of the default administrator account",
    "C-UNCONST-DELEG", "Dangerous delegation",
    "C-PASSWORD-DONT-EXPIRE", "Accounts with never expiring passwords",
    "C-USERS-CAN-JOIN-COMPUTERS", "Users allowed to join computers to the domain",
    "C-CLEARTEXT-PASSWORD", "Potential clear-text password",
    "C-PROTECTED-USERS-GROUP-UNUSED", "Protected Users group not used",
    "C-PASSWORD-POLICY", "Weak password policies are applied on users",
    "C-GPO-HARDENING", "Domain without computer-hardening GPOs",
    "C-LAPS-UNSECURE-CONFIG", "Local administrative account management",
    "C-AAD-CONNECT", "Verify permissions related to AAD Connect accounts",
    "C-AAD-SSO-PASSWORD", "Verify AAD SSO account password last change",
    "C-GPO-SD-CONSISTENCY", "Verify sensitive GPO objects and files permissions",
    "C-DSHEURISTICS", "Domain using a dangerous backward-compatibility configuration",
    "C-DOMAIN-FUNCTIONAL-LEVEL", "Domains have an outdated functional level",
    "C-DISABLED-ACCOUNTS-PRIV-GROUPS", "Disabled accounts in privileged groups",
    "C-DCSHADOW", "Rogue domain controllers",
    "C-DC-ACCESS-CONSISTENCY", "Domain controllers managed by illegitimate users",
    "C-DANGEROUS-TRUST-RELATIONSHIP", "Dangerous trust relationship",
    "C-DANGEROUS-SENSITIVE-PRIVILEGES", "Dangerous sensitive privileges",
    "C-DANG-PRIMGROUPID", "User Primary Group ID",
    "C-BAD-PASSWORD-COUNT", "Brute-force attack detection",
    "C-ADMINCOUNT-ACCOUNT-PROPS", "AdminCount attribute set on standard users",
    "C-ACCOUNTS-DANG-SID-HISTORY", "Accounts having a dangerous SID History attribute",
    "C-ABNORMAL-ENTRIES-IN-SCHEMA", "Dangerous rights in AD's schema",
    "C-GPOLICY-DISABLED-UNLINKED", "Unlinked, disabled or orphan GPO",
    "C-KERBEROS-CONFIG-ACCOUNT", "Kerberos configuration on user account",
    "C-KRBTGT-PASSWORD", "KDC password last change",
    "C-LAPS-UNSECURE-CONFIG", "Local administrative account management",
    "C-NATIVE-ADM-GROUP-MEMBERS", "Native administrative group members",
    "C-NETLOGON-SECURITY", "Unsecured configuration of Netlogon protocol",
    "C-OBSOLETE-SYSTEMS", "Computers running an obsolete OS",
    "C-PASSWORD-NOT-REQUIRED", "Account that might have an empty password",
    "C-PKI-WEAK-CRYPTO", "Use of weak cryptography algorithms into Active Directory PKI",
    "C-PRE-WIN2000-ACCESS-MEMBERS", "Accounts using a pre-Windows 2000 compatible access control",
    "C-PRIV-ACCOUNTS-SPN", "Privileged accounts running Kerberos services",
    "C-REVER-PWD-GPO", "Reversible passwords in GPO",
    "C-ROOTOBJECTS-SD-CONSISTENCY", "Root objects permissions allowing DCSync-like attacks",
    "C-SDPROP-CONSISTENCY", "Ensure SDProp consistency",
    "C-SENSITIVE-CERTIFICATES-ON-USER", "Ensure SDProp consistency",
    "C-SLEEPING-ACCOUNTS", "Sleeping accounts",
    "C-USER-PASSWORD", "User account using old password",
    "C-USERS-REVER-PWDS", "Reversible passwords"
    ];
    let Common = AlsidForADLog_CL
    | parse RawData with
                         Time:datetime  " "
                         Host:string  " "
                         Product:string "["
                         PID:int "]: \""
                         MessageType:int "\" \""
                         AlertID:int "\" \""
                         Forest:string "\" \""
                         Domain:string "\" "
                         DistinctPart:string;
    let Deviances = Common
    | where MessageType == 0 | parse DistinctPart with "\""
                         Codename:string "\" \""
                         Severity:string "\" \""
                         ADObject:string "\" \""
                         DevianceID:string "\" \""
                         ProfileID:string "\" \""
                         ReasonCodename:string "\" \""
                         EventID:string "\""
                         Attributes:string "\r\n";
    let Changes = Common
    | where MessageType == 1
    | parse kind=regex DistinctPart with "\""
                         ADObject:string "\" \""
                         EventID:string "\" \""
                         EventType:string "\" "
                         Attributes:string "\r?\n";
    union Changes, Deviances
    | project-away DistinctPart, Product, _ResourceId, _SubscriptionId
    | lookup kind=leftouter CodenameTable on Codename;
    ```

1. Klik op de vervolg keuzelijst **Opslaan** en klik op **Opslaan**. In het deel venster **Opslaan** ,

    1. Voer bij **naam** **afad_parser** in.

    1. Onder **Opslaan als** kiest u **functie**.

    1. Voer bij **functie Alias** **afad_parser** in.

    1. Onder **categorie** voert u **functies** in.

    1. Klik op **Opslaan**.

    Functie-apps nemen doorgaans tussen 10 en 15 minuten om te activeren.

Nu kunt u een query uitvoeren op Alsid voor AD-gegevens door `afad_parser` in de bovenste regel van het query venster in te voeren.

Zie het tabblad **volgende stappen** op de pagina connector voor meer query voorbeelden.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Alsid voor AD verbindt met de Sentinel van Azure. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.
