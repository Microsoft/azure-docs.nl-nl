---
title: LDAP-synchronisatie in zwerver en Apache Ambari in azure HDInsight
description: Adresseer de LDAP-synchronisatie in zwerver en Ambari en geef algemene richt lijnen.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: fb9035b4d816c1af84b15e6865335aa1bdf86f5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98933411"
---
# <a name="ldap-sync-in-ranger-and-apache-ambari-in-azure-hdinsight"></a>LDAP-synchronisatie in zwerver en Apache Ambari in azure HDInsight

HDInsight-clusters (ESP-Enterprise Security Package) gebruiken zwerver voor autorisatie. Apache Ambari en zwerver synchroniseren gebruikers en groepen onafhankelijk en werken iets anders. Dit artikel is bedoeld om de LDAP-synchronisatie in zwerver en Ambari te verhelpen.

## <a name="general-guidelines"></a>Algemene richtlijnen

* Implementeer altijd clusters met een of meer groepen.
* Als u meer groepen in het cluster wilt gebruiken, controleert u of het zinvol is om de groepslid maatschappen in Azure Active Directory (Azure AD) bij te werken.
* Als u de cluster groepen wilt wijzigen, kunt u de synchronisatie filters wijzigen met behulp van Ambari.
* Alle wijzigingen in het groepslid maatschap in azure AD worden in de volgende synchronisaties weer gegeven in het cluster. De wijzigingen moeten eerst worden gesynchroniseerd met Azure AD Domain Services (Azure AD DS) en vervolgens naar de clusters.
* HDInsight-clusters gebruiken Samba/winbind om de groepslid maatschappen op de cluster knooppunten te projecteren.
* Groeps leden worden transitief (alle subgroepen en hun leden) gesynchroniseerd met zowel Ambari als zwerver. 

## <a name="users-are-synced-separately"></a>Gebruikers worden afzonderlijk gesynchroniseerd

 * Ambari en zwerver delen de gebruikers database niet omdat ze twee verschillende doel einden hebben. 
   * Als een gebruiker de Ambari-gebruikers interface moet gebruiken, moet de gebruiker worden gesynchroniseerd met Ambari. 
   * Als de gebruiker niet is gesynchroniseerd met Ambari, wordt deze door de Ambari-gebruikers interface/-API geweigerd, maar andere onderdelen van het systeem werken (deze worden beschermd door zwerver of Resource Manager, en niet door Ambari).
   * Als u gebruikers of groepen wilt toevoegen in zwerver-beleid, moeten de principals in zwerver expliciet worden gesynchroniseerd.

## <a name="ambari-user-sync-and-configuration"></a>Gebruikers synchronisatie en-configuratie Ambari

Vanuit de hoofd knooppunten wordt een cron-taak, `/opt/startup_scripts/start_ambari_ldap_sync.py` , elke uur uitgevoerd om de gebruikers synchronisatie te plannen. De cron-taak roept de Ambari rest Api's aan om de synchronisatie uit te voeren. Het script verzendt een lijst met gebruikers en groepen die moeten worden gesynchroniseerd (aangezien de gebruikers geen deel uitmaken van de opgegeven groepen, worden beide afzonderlijk opgegeven.) Ambari synchroniseert de sAMAccountName als de gebruikers naam en alle groeps leden, transitief.

De logboeken moeten zijn in `/var/log/ambari-server/ambari-server.log` . Zie [Configure Ambari Logging Level](https://docs.cloudera.com/HDPDocuments/Ambari-latest/administering-ambari/content/amb_configure_ambari_logging_level.html)(Engelstalig) voor meer informatie.

In Data Lake clusters wordt de hook voor het maken van de gebruiker post gebruikt voor het maken van de basis mappen voor de gesynchroniseerde gebruikers en ze zijn ingesteld als eigen aren van de basis mappen. Als de gebruiker niet correct is gesynchroniseerd met Ambari, kan de gebruiker storingen in actieve taken ondervinden, omdat de basismap mogelijk niet correct is ingesteld.

## <a name="ranger-user-sync-and-configuration"></a>Zwerver-gebruikers synchronisatie en-configuratie

Zwerver heeft een ingebouwde synchronisatie-engine die elk uur wordt uitgevoerd om gebruikers te synchroniseren. De gebruikers database wordt niet gedeeld met Ambari. HDInsight configureert het zoek filter om de gebruiker met beheerders rechten, de watchdog-gebruiker en de leden van de groep die is opgegeven tijdens het maken van het cluster te synchroniseren. De groeps leden worden transitief gesynchroniseerd:

1. Incrementele synchronisatie uitschakelen.
1. Schakel de synchronisatie toewijzing van de gebruikers groep in.
1. Geef het zoek filter op waarmee de leden van de transitieve groep moeten worden toegevoegd.
1. Synchroniseer het sAMAccountName-kenmerk voor gebruikers en het naam kenmerk voor groepen.

### <a name="group-or-incremental-sync"></a>Groep of incrementele synchronisatie

Zwerver ondersteunt een optie voor groeps synchronisatie, maar dit werkt als een snij punt met gebruikers filter, niet als een samen voeging tussen groepslid maatschappen en gebruikers filter. Een typisch gebruiks voorbeeld voor een groeps synchronisatie filter in zwerver is-groeps filter: (DN = clusteradmingroup), gebruikers filter: (city = Seattle).

Incrementele synchronisatie werkt alleen voor gebruikers die al zijn gesynchroniseerd (de eerste keer). Met incrementeel worden nieuwe gebruikers die zijn toegevoegd aan de groepen, niet gesynchroniseerd na de initiële synchronisatie.

### <a name="update-ranger-sync-filter"></a>Synchronisatie filter zwerver bijwerken

U vindt het LDAP-filter in de Ambari-gebruikers interface, in de sectie zwerver-gebruikers synchronisatie configureren. Het bestaande filter wordt in het formulier weer `(|(userPrincipalName=bob@contoso.com)(userPrincipalName=hdiwatchdog-core01@CONTOSO.ONMICROSOFT.COM)(memberOf:1.2.840.113556.1.4.1941:=CN=hadoopgroup,OU=AADDC Users,DC=contoso,DC=onmicrosoft,DC=com))` . Zorg ervoor dat u aan het einde een predikaat toevoegt en test het filter met behulp van de `net ads` Zoek opdracht of het ldp.exe of iets dergelijks.

## <a name="ranger-user-sync-logs"></a>Zwerver gebruikers synchronisatie logboeken

Zwerver-gebruikers synchronisatie kan worden uitgevoerd op een van de hoofd knooppunten. De logboeken zijn in `/var/log/ranger/usersync/usersync.log` . Voer de volgende stappen uit om de uitgebreider van de logboeken te verg Roten:

1. Meld u aan bij Ambari.
1. Ga naar de configuratie sectie zwerver.
1. Ga naar de sectie Geavanceerde **usersync-log4j** .
1. Wijzig het `log4j.rootLogger` `DEBUG` niveau in. (Nadat u deze hebt gewijzigd, moet deze er als volgt uitzien `log4j.rootLogger = DEBUG,logFile,FilterLog` ).
1. Sla de configuratie op en start zwerver opnieuw op.

## <a name="known-issues-with-ranger-user-sync"></a>Bekende problemen met de gebruikers synchronisatie van zwerver
* Als de groeps naam Unicode-tekens bevat, mislukt de synchronisatie van Zwerver dat object. Als een gebruiker tot een groep behoort die internationale tekens heeft, synchroniseert zwerver gedeeltelijk groepslid maatschap
* De gebruikers naam (sAMAccountName) en de groeps naam (naam) moeten Maxi maal 20 tekens lang zijn. Als de groeps naam langer is, wordt de gebruiker behandeld alsof deze geen deel uitmaakt van de groep, bij het berekenen van de machtigingen.

## <a name="next-steps"></a>Volgende stappen

* [Verificatie problemen in azure HDInsight](./domain-joined-authentication-issues.md)
* [Azure AD-gebruikers synchroniseren met een HDInsight-cluster](../hdinsight-sync-aad-users-to-cluster.md)
