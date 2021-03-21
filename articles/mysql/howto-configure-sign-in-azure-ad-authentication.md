---
title: Azure Active Directory-Azure Database for MySQL gebruiken
description: Meer informatie over het instellen van Azure Active Directory (Azure AD) voor verificatie met Azure Database for MySQL
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: how-to
ms.date: 07/23/2020
ms.openlocfilehash: f5890ddb2a4b1599dbcfd1e624c9fbe71a564de7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102442754"
---
# <a name="use-azure-active-directory-for-authentication-with-mysql"></a>Azure Active Directory gebruiken voor verificatie met MySQL

Dit artikel begeleidt u stapsgewijs door de stappen voor het configureren van Azure Active Directory toegang met Azure Database for MySQL en hoe u verbinding maakt met behulp van een Azure AD-token.

> [!IMPORTANT]
> Azure Active Directory-verificatie is alleen beschikbaar voor MySQL 5,7 en nieuwer.

## <a name="setting-the-azure-ad-admin-user"></a>De gebruiker van de Azure AD-beheerder instellen

Alleen een gebruiker van Azure AD-beheerder kan gebruikers maken/inschakelen voor verificatie op basis van Azure AD. Voer de volgende stappen uit om een Azure AD-beheerders gebruiker te maken:

1. Selecteer in de Azure Portal het exemplaar van Azure Database for MySQL dat u wilt inschakelen voor Azure AD.
2. Selecteer onder instellingen de optie Active Directory beheerder:

![Azure AD-beheerder instellen][2]

3. Selecteer een geldige Azure AD-gebruiker in de Tenant van de klant als Azure AD-beheerder.

> [!IMPORTANT]
> Bij het instellen van de beheerder wordt een nieuwe gebruiker toegevoegd aan de Azure Database for MySQL-server met volledige beheerders machtigingen.

Er kan slechts één Azure AD-beheerder per MySQL-server worden gemaakt en de bestaande Azure AD-beheerder die voor de server is geconfigureerd, wordt overschreven door de selectie van een andere.

Nadat u de beheerder hebt geconfigureerd, kunt u zich nu aanmelden:

## <a name="connecting-to-azure-database-for-mysql-using-azure-ad"></a>Verbinding maken met Azure Database for MySQL met behulp van Azure AD

In het volgende diagram op hoog niveau wordt een overzicht gegeven van de werk stroom van het gebruik van Azure AD-verificatie met Azure Database for MySQL:

![verificatie stroom][1]

We hebben de integratie van Azure AD ontworpen om te werken met algemene MySQL-hulpprogram ma's zoals de MySQL CLI, die niet bekend zijn met Azure AD en die alleen ondersteuning bieden voor het opgeven van de gebruikers naam en het wacht woord wanneer er verbinding wordt gemaakt met MySQL. De Azure AD-token wordt als wacht woord door gegeven, zoals in de bovenstaande afbeelding.

Momenteel zijn de volgende clients getest:

- MySQLWorkbench 
- MySQL CLI

We hebben ook de meest algemene toepassings Stuur Programma's getest. u kunt meer informatie bekijken aan het einde van deze pagina.

Dit zijn de stappen die een gebruiker/toepassing nodig heeft om zich te verifiëren met Azure AD zoals hieronder wordt beschreven:

### <a name="prerequisites"></a>Vereisten

U kunt de Azure Cloud Shell, een Azure-VM of op uw lokale machine volgen. Zorg ervoor dat de [Azure cli is geïnstalleerd](/cli/azure/install-azure-cli).

### <a name="step-1-authenticate-with-azure-ad"></a>Stap 1: verifiëren met Azure AD

Begin door te verifiëren met Azure AD met het Azure CLI-hulp programma. Deze stap is niet vereist in Azure Cloud Shell.

```
az login
```

Met de opdracht wordt een browser venster geopend op de Azure AD-verificatie pagina. Hiervoor moet u uw gebruikers-ID en wacht woord voor Azure AD opgeven.

### <a name="step-2-retrieve-azure-ad-access-token"></a>Stap 2: het Azure AD-toegangs Token ophalen

Roep het Azure CLI-hulp programma aan om een toegangs token te verkrijgen voor de door Azure AD geverifieerde gebruiker uit stap 1 om toegang te krijgen tot Azure Database for MySQL.

Voor beeld (voor open bare Cloud):

```azurecli-interactive
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

De bovenstaande resource waarde moet exact worden opgegeven zoals wordt weer gegeven. Voor andere Clouds kan de resource waarde worden opgezocht met:

```azurecli-interactive
az cloud show
```

Voor Azure CLI versie 2.0.71 en hoger kan de opdracht worden opgegeven in de volgende, handigere versie voor alle Clouds:

```azurecli-interactive
az account get-access-token --resource-type oss-rdbms
```

Nadat de verificatie is geslaagd, wordt een toegangs token door Azure AD geretourneerd:

```json
{
  "accessToken": "TOKEN",
  "expiresOn": "...",
  "subscription": "...",
  "tenant": "...",
  "tokenType": "Bearer"
}
```

Het token is een basis teken reeks van 64 waarmee alle informatie over de geverifieerde gebruiker wordt gecodeerd en die is gericht op de Azure Database for MySQL-service.

> [!NOTE]
> De geldigheid van het toegangs token is een wille keurige periode van 5 minuten tot 60 minuten. We raden u aan het toegangs token op te halen voordat u de aanmelding initieert naar Azure Database for MySQL.

### <a name="step-3-use-token-as-password-for-logging-in-with-mysql"></a>Stap 3: token als wacht woord gebruiken voor aanmelding met MySQL

Wanneer u verbinding maakt, moet u het toegangs token gebruiken als het MySQL-gebruikers wachtwoord. Wanneer u GUI-clients gebruikt, zoals MySQLWorkbench, kunt u de bovenstaande methode gebruiken om het token op te halen. 

Wanneer u de CLI gebruikt, kunt u deze korte hand gebruiken om verbinding te maken: 

**Voor beeld (Linux/macOS):**
```
mysql -h mydb.mysql.database.azure.com \ 
  --user user@tenant.onmicrosoft.com@mydb \ 
  --enable-cleartext-plugin \ 
  --password=`az account get-access-token --resource-type oss-rdbms --output tsv --query accessToken`
```

Belang rijke overwegingen bij het maken van verbinding:

* `user@tenant.onmicrosoft.com` is de naam van de Azure AD-gebruiker of-groep waarmee u verbinding probeert te maken
* De server naam altijd toevoegen na de gebruikers-of groeps naam van Azure AD (bijvoorbeeld `@mydb` )
* Zorg ervoor dat u de exacte manier gebruikt waarop de naam van de Azure AD-gebruiker of-groep is gespeld
* Namen van Azure AD-gebruikers en-groepen zijn hoofdletter gevoelig
* Wanneer u verbinding maakt als een groep, gebruikt u alleen de groeps naam (bijvoorbeeld `GroupName@mydb` )
* Als de naam spaties bevat, gebruikt u `\` vóór elke spatie om deze te escapen

Let op de instelling ' Enable-anti-tekst-plugin ': u moet een vergelijk bare configuratie met andere clients gebruiken om ervoor te zorgen dat het token wordt verzonden naar de server zonder hash-bewerking.

U bent nu geverifieerd voor uw MySQL-server met behulp van Azure AD-verificatie.

## <a name="creating-azure-ad-users-in-azure-database-for-mysql"></a>Azure AD-gebruikers maken in Azure Database for MySQL

Als u een Azure AD-gebruiker wilt toevoegen aan uw Azure Database for MySQL-data base, voert u de volgende stappen uit nadat u verbinding hebt gemaakt (zie verderop in dit onderwerp voor verbinding maken):

1. Zorg er eerst voor dat de Azure AD-gebruiker `<user>@yourtenant.onmicrosoft.com` een geldige gebruiker is in azure AD-Tenant.
2. Meld u aan bij uw Azure Database for MySQL-exemplaar als de gebruiker van de Azure AD-beheerder.
3. Maak een gebruiker `<user>@yourtenant.onmicrosoft.com` in azure database for MySQL.

**Voorbeeld:**

```sql
CREATE AADUSER 'user1@yourtenant.onmicrosoft.com';
```

Voor gebruikers namen die 32 tekens overschrijden, is het raadzaam om in plaats daarvan een alias te gebruiken voor gebruik bij het maken van verbinding: 

Voorbeeld:

```sql
CREATE AADUSER 'userWithLongName@yourtenant.onmicrosoft.com' as 'userDefinedShortName'; 
```

> [!NOTE]
> Het verifiëren van een gebruiker via Azure AD geeft de gebruiker geen machtigingen voor toegang tot objecten binnen de Azure Database for MySQL data base. U moet de gebruiker hand matig de vereiste machtigingen verlenen.

## <a name="creating-azure-ad-groups-in-azure-database-for-mysql"></a>Azure AD-groepen maken in Azure Database for MySQL

Als u een Azure AD-groep wilt inschakelen voor toegang tot uw data base, gebruikt u hetzelfde mechanisme als voor gebruikers, maar geeft u in plaats daarvan de groeps naam op:

**Voorbeeld:**

```sql
CREATE AADUSER 'Prod_DB_Readonly';
```

Wanneer u zich aanmeldt, gebruiken leden van de groep hun persoonlijke toegangs tokens, maar u moet zich aanmelden met de groeps naam die is opgegeven als de gebruikers naam.

## <a name="token-validation"></a>Token validatie

Met Azure AD-verificatie in Azure Database for MySQL wordt gegarandeerd dat de gebruiker bestaat op de MySQL-server en wordt de geldigheid van het token gecontroleerd door de inhoud van het token te valideren. De volgende stappen voor het valideren van tokens worden uitgevoerd:

-   Het token is ondertekend door Azure AD en is niet geknoeid
-   Het token is uitgegeven door Azure AD voor de Tenant die aan de server is gekoppeld
-   Het token is niet verlopen
-   Het token is voor de Azure Database for MySQL resource (en niet een andere Azure-resource)

## <a name="compatibility-with-application-drivers"></a>Compatibiliteit met toepassings Stuur Programma's

De meeste Stuur Programma's worden ondersteund, maar u moet de instellingen voor het verzenden van het wacht woord in ongecodeerde tekst gebruiken, zodat het token zonder aanpassing wordt verzonden.

* C/C++
  * libmysqlclient: ondersteund
  * mysql-connector-c + +: ondersteund
* Java
  * Connector/J (mysql-connector-java): ondersteund, moet de `useSSL` instelling gebruiken
* Python
  * Connector/python: ondersteund
* Ruby
  * mysql2: ondersteund
* .NET
  * mysql-connector-net: ondersteund, moet invoeg toepassing voor mysql_clear_password toevoegen
  * MySQL-net-MySqlConnector: ondersteund
* Node.js
  * mysqljs: niet ondersteund (verzendt geen token in ongecodeerde tekst zonder patch)
  * node-mysql2: ondersteund
* Perl
  * DBD:: mysql: ondersteund
  * Net:: MySQL: niet ondersteund
* Go
  * Go-SQL-stuur programma: ondersteund, toevoegen `?tls=true&allowCleartextPasswords=true` aan Connection String

## <a name="next-steps"></a>Volgende stappen

* Bekijk de algemene concepten voor [Azure Active Directory verificatie met Azure database for MySQL](concepts-azure-ad-authentication.md)

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/set-azure-ad-admin.png
