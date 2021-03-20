---
title: Veelvoorkomende fouten oplossen - Azure Database for MySQL
description: Meer informatie over het oplossen van veelvoorkomende migratiefouten voor nieuwe gebruikers van de Azure Database for MySQL-service
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 8/20/2020
ms.openlocfilehash: ca75416a66bcf2c90028c7f1dc11fbe23a9a9bd9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98631364"
---
# <a name="common-errors"></a>Algemene fouten

Azure Database for MySQL is een volledig beheerde service die wordt ondersteund door de communityversie van MySQL. De MySQL-ervaring in een beheerde service-omgeving kan afwijken van het uitvoeren van MySQL in uw eigen omgeving. In dit artikel ziet u enkele veelvoorkomende fouten die gebruikers kunnen tegenkomen tijdens het voor de eerste keer migreren naar of ontwikkelen met de Azure Database for MySQL-service.

## <a name="common-connection-errors"></a>Veelvoorkomende verbindings fouten

#### <a name="error-1184-08s01-aborted-connection-22-to-db-db-name-user-user-host-hostip-init_connect-command-failed"></a>FOUT 1184 (08S01): de verbinding 22 met DB is afgebroken: ' db-name ' gebruiker: ' gebruiker ' host: ' hostIP ' (init_connect opdracht is mislukt)
De bovenstaande fout treedt op nadat de aanmelding is geslaagd, maar voordat een opdracht werd uitgevoerd wanneer de sessie tot stand is gebracht. In het bovenstaande bericht wordt aangegeven dat u een onjuiste waarde van init_connect server parameter hebt ingesteld, waardoor de initialisatie van de sessie mislukt.

Er zijn enkele server parameters, zoals require_secure_transport die niet worden ondersteund op sessie niveau en wanneer u de waarden van deze para meters probeert te wijzigen met behulp van init_connect kan resulteren in fout 1184 tijdens het verbinden met de MySQL-server zoals hieronder wordt weer gegeven

MySQL-> data bases weer geven; FOUT 2006 (HY000): de MySQL-server is niet verbonden. Poging tot opnieuw verbinding maken... Verbindings-id: 64897 huidige Data Base: * * * geen * * * fout 1184 (08S01): de verbinding 22 met DB is afgebroken: ' db-name ' gebruiker: ' host van gebruiker: ' hostIP ' (init_connect opdracht is mislukt)

**Oplossing** : u moet init_connect waarde op het tabblad Server parameters in azure Portal opnieuw instellen en alleen de ondersteunde server parameters instellen met behulp van de init_connect para meter. 


## <a name="errors-due-to-lack-of-super-privilege-and-dba-role"></a>Fouten vanwege ontbreken van SUPER-machtiging en DBA-rol

De SUPER-machtiging en DBA-rol worden niet ondersteund in de service. Als gevolg hiervan kunt u een aantal veelvoorkomende fouten tegenkomen die hieronder worden vermeld:

#### <a name="error-1419-you-do-not-have-the-super-privilege-and-binary-logging-is-enabled-you-might-want-to-use-the-less-safe-log_bin_trust_function_creators-variable"></a>ERROR 1419: You do not have the SUPER privilege and binary logging is enabled (you *might* want to use the less safe log_bin_trust_function_creators variable)

De bovenstaande fout kan optreden bij het maken van een functie of trigger als hieronder of het importeren van een schema. DDL-instructies als CREATE FUNCTION en CREATE TRIGGER worden naar het binaire logboek geschreven, zodat de secundaire replica deze kan uitvoeren. De SQL-replicathread heeft volledige bevoegdheid, die kan worden gebruikt om machtigingen uit te breiden. Om servers waarop binaire logboekregistratie is ingeschakeld tegen dit risico te beschermen, vereist MySQL-engine dat opgeslagen functiemakers de machtiging SUPER hebben, naast de gebruikelijke vereiste machtiging CREATE ROUTINE. 

```sql
CREATE FUNCTION f1(i INT)
RETURNS INT
DETERMINISTIC
READS SQL DATA
BEGIN
  RETURN i;
END;
```

**Oplossing**:  Om de fout op te lossen, stelt u log_bin_trust_function_creators in op 1 op de blade [Serverparameters](howto-server-parameters.md) in de portal, voert u de DDL-instructies uit of importeert u het schema om de gewenste objecten te maken en zet u de parameter log_bin_trust_function_creators terug naar de vorige waarde na het maken.

#### <a name="error-1227-42000-at-line-101-access-denied-you-need-at-least-one-of-the-super-privileges-for-this-operation-operation-failed-with-exitcode-1"></a>ERROR 1227 (42000) at line 101: Access denied; you need (at least one of) the SUPER privilege(s) for this operation. Operation failed with exitcode 1

De bovenstaande fout kan optreden bij het importeren van een dump-bestand of het maken van een procedure met [definities](https://dev.mysql.com/doc/refman/5.7/en/create-procedure.html). 

**Oplossing**:  Om deze fout op te lossen, kan de gebruiker met beheerdersrechten machtigingen verlenen om procedures te maken of uit te voeren door de opdracht GRANT uit te voeren, zoals in de volgende voorbeelden:

```sql
GRANT CREATE ROUTINE ON mydb.* TO 'someuser'@'somehost';
GRANT EXECUTE ON PROCEDURE mydb.myproc TO 'someuser'@'somehost';
```
U kunt ook de definities vervangen door de naam van de gebruiker met beheerdersrechten die het importproces uitvoert, zoals hieronder wordt weergegeven.

```sql
DELIMITER;;
/*!50003 CREATE*/ /*!50017 DEFINER=`root`@`127.0.0.1`*/ /*!50003
DELIMITER;;

/* Modified to */

DELIMITER ;;
/*!50003 CREATE*/ /*!50017 DEFINER=`AdminUserName`@`ServerName`*/ /*!50003
DELIMITER ;
```
#### <a name="error-1227-42000-at-line-295-access-denied-you-need-at-least-one-of-the-super-or-set_user_id-privileges-for-this-operation"></a>ERROR 1227 (42000) at line 295: Access denied; you need (at least one of) the SUPER or SET_USER_ID privilege(s) for this operation

De bovenstaande fout kan optreden tijdens het uitvoeren van CREATE VIEW met DEFINER-instructies, als onderdeel van het importeren van een dumpbestand of het uitvoeren van een script. In Azure Database for MySQL worden aan geen enkele gebruiker SUPER-bevoegdheden of de SET_USER_ID-bevoegdheid verleend. 

**Oplossing**: 
* Gebruik, indien mogelijk, de gebruiker van de definities om CREATE VIEW uit te voeren. Waarschijnlijk zijn er veel weergaven met verschillende definities die verschillende machtigingen hebben, zodat dit niet haalbaar is.  OF
* Bewerk het dumpbestand of CREATE VIEW-script, en verwijder de instructie DEFINER= uit het dumpbestand OF 
* Bewerk het dumpbestand of CREATE VIEW-script en vervang de definitiewaarden door de gebruiker met beheerdersmachtigingen die het scriptbestand importeert of uitvoert.

> [!Tip] 
> Gebruik sed of perl om een dumpbestand of SQL-script te wijzigen, om de instructie DEFINE= te vervangen

## <a name="common-connection-errors-for-server-admin-login"></a>Veelvoorkomende verbindingsfouten voor aanmeldgegevens van de serverbeheerder

Wanneer een Azure Database for MySQL-server wordt gemaakt, worden tijdens het maken van de server door de eindgebruiker aanmeldgegevens van de serverbeheerder opgegeven. Met de aanmeldgegevens van de serverbeheerder kunt u nieuwe databases maken, nieuwe gebruikers toevoegen en machtigingen verlenen. Als de aanmeldgegevens van de serverbeheerder worden verwijderd, worden de bijbehorende machtigingen ingetrokken of wordt het bijbehorende wachtwoord gewijzigd. Het kan zijn dat u dan verbindingsfouten ziet in de toepassing bij het verbinding maken. Hieronder ziet u enkele veelvoorkomende fouten

#### <a name="error-1045-28000-access-denied-for-user-usernameip-address-using-password-yes"></a>ERROR 1045 (28000): Access denied for user 'username'@'IP address' (using password: YES)

De bovenstaande fout treedt op als:

* De gebruikersnaam niet bestaat
* De gebruikersnaam is verwijderd
* Het bijbehorende wachtwoord is gewijzigd of opnieuw is ingesteld.

**Oplossing**: 
* Valideer of 'gebruikersnaam' bestaat als een geldige gebruiker op de server, of dat deze per ongeluk is verwijderd. U kunt de volgende query uitvoeren door u aan te melden bij de Azure Database for MySQL-gebruiker:
  ```sql
  select user from mysql.user;
  ```
* Als u zich niet kunt aanmelden bij de MySQL om de bovenstaande query uit te voeren, raden wij u aan [het beheerderswachtwoord opnieuw in te stellen in de Azure-portal](howto-create-manage-server-portal.md). Met de optie voor het opnieuw instellen van wachtwoorden in de Azure-portal kunt u de gebruiker opnieuw maken, het wachtwoord opnieuw instellen, en de beheerdersmachtigingen herstellen. Hierdoor kunt u zich aanmelden met de aanmeldgegevens van de serverbeheerder en verdere bewerkingen uitvoeren.

## <a name="next-steps"></a>Volgende stappen
Als u het antwoord op uw vraag niet hebt gevonden, kunt u het volgende doen:

- Plaats uw vraag op [de Microsoft Q&A-pagina](/answers/topics/azure-database-mysql.html) of op [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
- Stuur een e-mail naar het Azure Database for MySQL-team [@Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com). Dit e-mailadres is geen alias voor technische ondersteuning.
- Als u contact wilt opnemen met Ondersteuning voor Azure, kunt u een [ticket indienen vanuit Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Als u een probleem met uw account wilt oplossen, kunt u een [ondersteuningsaanvraag](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) indienen in Azure Portal.
- Als u feedback wilt geven of een nieuwe functie wilt aanvragen, maakt u een vermelding via [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).
