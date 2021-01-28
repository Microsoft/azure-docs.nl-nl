---
title: Algemene richt lijnen voor Enter prise Security in azure HDInsight
description: Enkele aanbevolen procedures voor het eenvoudiger maken van Enterprise Security Package-implementatie en-beheer.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 92ad8362f75cdf0613d4ee95f39c23aa6d4819bb
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933561"
---
# <a name="enterprise-security-general-information-and-guidelines-in-azure-hdinsight"></a>Algemene informatie over Enter prise Security en richt lijnen in azure HDInsight

Wanneer u een beveiligd HDInsight-cluster implementeert, zijn er enkele aanbevolen procedures waarmee de implementatie en het cluster beheer eenvoudiger worden. Hier worden enkele algemene informatie en richt lijnen besproken.

## <a name="use-of-secure-cluster"></a>Gebruik van een beveiligd cluster

### <a name="recommended"></a>Aanbevolen

* Het cluster wordt gebruikt door meerdere gebruikers tegelijk.
* Gebruikers hebben verschillende toegangs niveaus voor dezelfde gegevens.

### <a name="not-necessary"></a>Niet nodig

* U gaat alleen geautomatiseerde taken uitvoeren (zoals een account voor één gebruiker), een standaard cluster is voldoende.
* U kunt de gegevens importeren met behulp van een standaard cluster en hetzelfde opslag account gebruiken op een ander beveiligd cluster waarop gebruikers analyse taken kunnen uitvoeren.

## <a name="use-of-local-account"></a>Gebruik van lokaal account

* Als u een gedeeld gebruikers account of een lokaal account gebruikt, is het moeilijk om te bepalen wie het account heeft gebruikt om de configuratie of service te wijzigen.
* Het gebruik van lokale accounts is problematisch wanneer gebruikers niet langer deel uitmaken van de organisatie.

## <a name="ranger"></a>Ranger

### <a name="policies"></a>Beleidsregels

* Standaard gebruikt zwerver **weigeren** als beleid.

* Wanneer gegevens toegang wordt gemaakt via een service waarvoor autorisatie is ingeschakeld:
  * De autorisatie-invoeg toepassing voor Zwerver wordt aangeroepen en krijgt de context van de aanvraag.
  * Zwerver past de beleids regels toe die zijn geconfigureerd voor de service. Als het beleid voor Zwerver mislukt, wordt de toegangs controle uitgesteld naar het bestands systeem. Sommige services, zoals MapReduce, controleren alleen of het bestand of de map eigendom is van dezelfde gebruiker die de aanvraag indient. Services als Hive, Controleer of het eigendom overeenkomt met of de juiste machtigingen voor het bestands systeem ( `rwx` ).

* Voor Hive moet de gebruiker, naast de machtigingen voor maken/bijwerken/verwijderen, machtigingen hebben voor `rwx` de directory op opslag en alle submappen.

* Beleids regels kunnen worden toegepast op groepen (voor keuren) in plaats van personen.

* Zwerver-autoriseer evalueert alle zwerver-beleids regels voor die service voor elke aanvraag. Deze evaluatie kan invloed hebben op de tijd die nodig is om de taak of query te accepteren.

### <a name="storage-access"></a>Toegang tot opslag

* Als het opslag type WASB is, is er geen OAuth-token betrokken.
* Als zwerver de autorisatie heeft uitgevoerd, gebeurt de opslag toegang met behulp van de beheerde identiteit.
* Als zwerver geen enkele autorisatie heeft uitgevoerd, gebeurt de opslag toegang met het OAuth-token van de gebruiker.

### <a name="hierarchical-name-space"></a>Hiërarchische naam ruimte

Wanneer hiërarchische naam ruimte niet ingeschakeld is:

* Er zijn geen overgenomen machtigingen.
* Alleen bestandssysteem machtiging die werkt met de **opslag gegevens xxxx** Azure Role, worden rechtstreeks aan de gebruiker toegewezen in azure Portal.

### <a name="default-hdfs-permissions"></a>Standaard machtigingen voor HDFS

* Gebruikers hebben standaard geen toegang tot de **/** map op HDFS (ze moeten zich in de rol van de eigenaar van de opslag-BLOB bevinden om de toegang te kunnen volt ooien).
* Voor de map met tijdelijke bestanden voor MapReduce en anderen wordt een gebruikersspecifieke map gemaakt en de `sticky _wx` machtigingen opgegeven. Gebruikers kunnen onder andere bestanden en mappen maken, maar kunnen geen andere items bekijken.

### <a name="url-auth"></a>URL-verificatie

Als de URL-verificatie is ingeschakeld:

* De configuratie bevat de voor voegsels die worden behandeld in de URL-verificatie (zoals `adl://` ).
* Als de toegang voor deze URL is, controleert zwerver of de gebruiker zich in de acceptatie lijst bevindt.
* Zwerver controleert geen van de fijn gekorrelde beleids regels.

## <a name="resource-groups"></a>Resourcegroepen

Gebruik een nieuwe resource groep voor elk cluster, zodat u onderscheid kunt maken tussen cluster resources.

## <a name="nsgs-firewalls-and-internal-gateway"></a>Nsg's, firewalls en interne gateway

* Gebruik netwerk beveiligings groepen (Nsg's) om virtuele netwerken te vergren delen.
* Firewall gebruiken om beleid voor uitgaande toegang te verwerken.
* Gebruik de interne gateway die niet is geopend voor het open bare Internet.

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) is de cloud-gebaseerde service voor identiteits-en toegangs beheer van micro soft.

### <a name="policies"></a>Beleidsregels

* Schakel het beleid voor voorwaardelijke toegang uit met het op IP-adres gebaseerde beleid. Hiervoor moeten service-eind punten zijn ingeschakeld op de VNETs waar de clusters worden geïmplementeerd. Als u een externe service voor MFA gebruikt (iets anders dan AAD), werkt het beleid op basis van IP-adres niet

* `AllowCloudPasswordValidation` het beleid is vereist voor federatieve gebruikers. Aangezien HDInsight de gebruikers naam/het wacht woord rechtstreeks gebruikt om tokens van Azure AD op te halen, moet dit beleid worden ingeschakeld voor alle federatieve gebruikers.

* Schakel service-eind punten in als u overs laan van voorwaardelijke toegang met behulp van vertrouwde IP-adressen.

### <a name="groups"></a>Groepen

* Implementeer altijd clusters met een groep.
* Gebruik Azure AD voor het beheren van groepslid maatschappen (eenvoudiger dan bij het beheren van de afzonderlijke services in het cluster).

### <a name="user-accounts"></a>Gebruikersaccounts

* Gebruik voor elk scenario een unieke gebruikers account. Gebruik bijvoorbeeld een account voor importeren, gebruik een ander voor query of andere verwerkings taken.
* Gebruik op groep gebaseerd zwerver-beleid in plaats van afzonderlijke beleids regels.
* Een plan hebben voor het beheren van gebruikers die geen toegang meer tot clusters mogen hebben.

## <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services

[Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md) (Azure AD DS) biedt beheerde domein Services, zoals domein deelname, groeps beleid, LDAP (Lightweight Directory Access Protocol) en Kerberos/NTLM-verificatie die volledig compatibel is met Windows Server Active Directory.

Azure AD DS is vereist voor beveiligde clusters om lid te worden van een domein.
HDInsight kan niet afhankelijk zijn van on-premises domein controllers of aangepaste domein controllers, omdat er te veel fout punten, referentie delen, DNS-machtigingen, enzovoort worden geïntroduceerd. Zie [Veelgestelde vragen over Azure AD DS](../../active-directory-domain-services/faqs.md)voor meer informatie.

### <a name="azure-ad-ds-instance"></a>Azure AD DS-exemplaar

* Maak het exemplaar met de `.onmicrosoft.com domain` . Op deze manier zijn er geen meerdere DNS-servers voor het domein.
* Maak een zelfondertekend certificaat voor de LDAPS en upload het naar Azure AD DS.
* Gebruik een peered virtueel netwerk voor het implementeren van clusters (wanneer u een aantal teams hebt die gebruikmaken van HDInsight ESP-clusters), is dit nuttig. Dit zorgt ervoor dat u geen poorten (Nsg's) in het virtuele netwerk hoeft te openen met de domein controller.
* Configureer de DNS-server voor het virtuele netwerk op de juiste wijze (de naam van het Azure AD DS-domein moet worden omgezet zonder hosts-bestands vermeldingen).
* Als u uitgaand verkeer wilt beperken, moet u ervoor zorgen dat u de [firewall ondersteuning in HDInsight](../hdinsight-restrict-outbound-traffic.md) hebt gelezen.

### <a name="properties-synced-from-azure-ad-to-azure-ad-ds"></a>Eigenschappen die zijn gesynchroniseerd van Azure AD naar Azure AD DS

* Azure AD Connect synchroniseert van on-premises naar Azure AD.
* Azure-AD DS worden gesynchroniseerd vanuit Azure AD.

Azure AD DS synchroniseert periodiek objecten van Azure AD. Op de Blade Azure AD DS op de Azure Portal wordt de synchronisatie status weer gegeven. Tijdens elke fase van de synchronisatie kunnen de unieke eigenschappen conflicteren en een andere naam krijgen. Let op de eigenschaps toewijzing van Azure AD naar Azure AD DS.

Zie [Azure AD userPrincipalName-populatie](../../active-directory/hybrid/plan-connect-userprincipalname.md)en de werking van [Azure AD DS-synchronisatie](../../active-directory-domain-services/synchronization.md)voor meer informatie.

### <a name="password-hash-sync"></a>Wachtwoord-hash-synchronisatie

* Wacht woorden worden anders gesynchroniseerd vanuit andere object typen. Alleen niet-omkeer bare wacht woord-hashes worden gesynchroniseerd in azure AD en Azure AD DS
* On-premises naar Azure AD moet worden ingeschakeld via AD Connect
* De synchronisatie van Azure AD naar Azure AD DS is automatisch (latenties zijn minder dan 20 minuten).
* Wacht woord-hashes worden alleen gesynchroniseerd wanneer het wacht woord is gewijzigd. Wanneer u wacht woord-hash-synchronisatie inschakelt, worden alle bestaande wacht woorden niet automatisch gesynchroniseerd wanneer ze worden opgeslagen irreversibly. Wanneer u het wacht woord wijzigt, worden wacht woord-hashes gesynchroniseerd.

### <a name="computer-objects-location"></a>Locatie van computer objecten

Elk cluster is gekoppeld aan één OE. Een interne gebruiker is ingericht in de OE. Alle knoop punten zijn lid van een domein in dezelfde organisatie-eenheid.

### <a name="active-directory-administrative-tools"></a>Beheer Programma's Active Directory

Zie [install management tools](../../active-directory-domain-services/tutorial-create-management-vm.md)(Engelstalig) voor meer informatie over het installeren van de Active Directory-beheer Programma's op een virtuele machine met Windows Server.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="cluster-creation-fails-repeatedly"></a>Het maken van een cluster mislukt herhaaldelijk

Meest voorkomende oorzaken:

* De DNS-configuratie is niet juist, de domein deelname van de cluster knooppunten is mislukt.
* Nsg's zijn te beperkend, waardoor domein deelname niet wordt voor komen.
* De beheerde identiteit beschikt niet over voldoende machtigingen.
* De cluster naam is niet uniek voor de eerste zes tekens (met een ander live cluster of met een verwijderd cluster).

## <a name="authentication-setup-and-configuration"></a>Verificatie-instellingen en-configuratie

### <a name="user-principal-name-upn"></a>User Principal Name (UPN)

* Gebruik een kleine letter voor alle services-Upn's zijn niet hoofdletter gevoelig in ESP-clusters, maar
* Het UPN-voor voegsel moet overeenkomen met beide SAMAccountName in azure AD-DS. Het veld e-mail is niet vereist.

### <a name="ldap-properties-in-ambari-configuration"></a>LDAP-eigenschappen in Ambari-configuratie

Zie [AMBARI LDAP-verificatie-instellingen](https://ambari.apache.org/1.2.1/installing-hadoop-using-ambari/content/ambari-chap2-4.html)voor een volledige lijst van de Ambari-eigenschappen die van invloed zijn op uw HDInsight-cluster configuratie.

## <a name="next-steps"></a>Volgende stappen

* [Enterprise Security Package configuraties met Azure Active Directory Domain Services in HDInsight](./apache-domain-joined-configure-using-azure-adds.md)

* [Azure Active Directory gebruikers synchroniseren met een HDInsight-cluster](../hdinsight-sync-aad-users-to-cluster.md).
