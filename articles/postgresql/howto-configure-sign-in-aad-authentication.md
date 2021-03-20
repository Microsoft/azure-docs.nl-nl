---
title: Azure Active Directory-Azure Database for PostgreSQL-één server gebruiken
description: Meer informatie over het instellen van Azure Active Directory (AAD) voor verificatie met Azure Database for PostgreSQL-één server
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 07/23/2020
ms.openlocfilehash: 178c339f6f47569160a9a748794678c610f35734
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87171647"
---
# <a name="use-azure-active-directory-for-authentication-with-postgresql"></a>Azure Active Directory gebruiken voor verificatie met PostgreSQL

Dit artikel begeleidt u stapsgewijs door de stappen voor het configureren van Azure Active Directory toegang met Azure Database for PostgreSQL en hoe u verbinding maakt met behulp van een Azure AD-token.

## <a name="setting-the-azure-ad-admin-user"></a>De gebruiker van de Azure AD-beheerder instellen

Alleen gebruikers van Azure AD-beheerder kunnen gebruikers maken/inschakelen voor verificatie op basis van Azure AD. We raden u aan om de Azure AD-beheerder niet te gebruiken voor reguliere database bewerkingen, omdat het een verhoogde gebruikers machtigingen heeft (bijvoorbeeld CREATEDB).

Voer de volgende stappen uit om de Azure AD-beheerder in te stellen (u kunt een gebruiker of een groep gebruiken)

1. Selecteer in de Azure Portal het exemplaar van Azure Database for PostgreSQL dat u wilt inschakelen voor Azure AD.
2. Selecteer onder instellingen de optie Active Directory beheerder:

![Azure AD-beheerder instellen][2]

3. Selecteer een geldige Azure AD-gebruiker in de Tenant van de klant als Azure AD-beheerder.

> [!IMPORTANT]
> Bij het instellen van de beheerder wordt een nieuwe gebruiker toegevoegd aan de Azure Database for PostgreSQL-server met volledige beheerders machtigingen. De gebruiker van de Azure AD-beheerder in Azure Database for PostgreSQL heeft de rol `azure_ad_admin` .

Er kan slechts één Azure AD-beheerder per PostgreSQL-server worden gemaakt. de bestaande Azure AD-beheerder die voor de server is geconfigureerd, wordt overschreven door de selectie van een andere. U kunt een Azure AD-groep opgeven in plaats van een afzonderlijke gebruiker om meerdere beheerders te hebben. Houd er rekening mee dat u zich aanmeldt met de groeps naam voor beheer doeleinden.

## <a name="connecting-to-azure-database-for-postgresql-using-azure-ad"></a>Verbinding maken met Azure Database for PostgreSQL met behulp van Azure AD

In het volgende diagram op hoog niveau wordt een overzicht gegeven van de werk stroom van het gebruik van Azure AD-verificatie met Azure Database for PostgreSQL:

![verificatie stroom][1]

We hebben de integratie van Azure AD ontworpen om te werken met algemene PostgreSQL-hulpprogram ma's zoals psql, die geen ondersteuning bieden voor Azure AD. het is alleen mogelijk om de gebruikers naam en het wacht woord op te geven wanneer er verbinding wordt gemaakt met PostgreSQL. De Azure AD-token wordt als wacht woord door gegeven, zoals in de bovenstaande afbeelding.

Momenteel zijn de volgende clients getest:

- psql commandline (gebruik de variabele PGPASSWORD om het token door te geven, zie hieronder)
- Azure Data Studio (met behulp van de PostgreSQL-extensie)
- Andere op libpq gebaseerde clients (bijvoorbeeld algemene toepassings raamwerken en ORMs)

> [!NOTE]
> Houd er rekening mee dat het gebruik van het Azure AD-token met pgAdmin momenteel niet wordt ondersteund, omdat het een vastgelegde limiet van 256 tekens heeft voor wacht woorden (die het token overschrijdt).

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

Roep het Azure CLI-hulp programma aan om een toegangs token te verkrijgen voor de door Azure AD geverifieerde gebruiker uit stap 1 om toegang te krijgen tot Azure Database for PostgreSQL.

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

Het token is een basis teken reeks van 64 waarmee alle informatie over de geverifieerde gebruiker wordt gecodeerd en die is gericht op de Azure Database for PostgreSQL-service.

> [!NOTE]
> De geldigheid van het toegangs token is een wille keurige periode van 5 minuten tot 60 minuten. We raden u aan het toegangs token op te halen voordat u de aanmelding initieert naar Azure Database for PostgreSQL.

### <a name="step-3-use-token-as-password-for-logging-in-with-postgresql"></a>Stap 3: token als wacht woord gebruiken om u aan te melden met PostgreSQL

Wanneer u verbinding maakt, moet u het toegangs token gebruiken als het PostgreSQL-gebruikers wachtwoord.

Bij gebruik van de `psql` opdracht regel-client moet het toegangs token worden door gegeven via de `PGPASSWORD` omgevings variabele, omdat het toegangs token de wachtwoord lengte overschrijdt die `psql` rechtstreeks kan worden geaccepteerd:

Windows-voor beeld:

```cmd
set PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

```PowerShell
$env:PGPASSWORD='<copy/pasted TOKEN value from step 2>'
```

Voor beeld van Linux/macOS:

```shell
export PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Nu kunt u een verbinding tot stand brengen met Azure Database for PostgreSQL zoals u gewend bent:

```shell
psql "host=mydb.postgres... user=user@tenant.onmicrosoft.com@mydb dbname=postgres sslmode=require"
```

Belang rijke overwegingen bij het maken van verbinding:

* `user@tenant.onmicrosoft.com` is de naam van de Azure AD-gebruiker of-groep waarmee u verbinding probeert te maken
* De server naam altijd toevoegen na de gebruikers-of groeps naam van Azure AD (bijvoorbeeld `@mydb` )
* Zorg ervoor dat u de exacte manier gebruikt waarop de naam van de Azure AD-gebruiker of-groep is gespeld
* Namen van Azure AD-gebruikers en-groepen zijn hoofdletter gevoelig
* Wanneer u verbinding maakt als een groep, gebruikt u alleen de groeps naam (bijvoorbeeld `GroupName@mydb` )
* Als de naam spaties bevat, gebruikt u `\` vóór elke spatie om deze te escapen

U bent nu geverifieerd voor uw PostgreSQL-server met behulp van Azure AD-verificatie.

## <a name="creating-azure-ad-users-in-azure-database-for-postgresql"></a>Azure AD-gebruikers maken in Azure Database for PostgreSQL

Als u een Azure AD-gebruiker wilt toevoegen aan uw Azure Database for PostgreSQL-data base, voert u de volgende stappen uit nadat u verbinding hebt gemaakt (zie verderop in dit onderwerp voor verbinding maken):

1. Zorg er eerst voor dat de Azure AD-gebruiker `<user>@yourtenant.onmicrosoft.com` een geldige gebruiker is in azure AD-Tenant.
2. Meld u aan bij uw Azure Database for PostgreSQL-exemplaar als de gebruiker van de Azure AD-beheerder.
3. Maak een rol `<user>@yourtenant.onmicrosoft.com` in azure database for PostgreSQL.
4. Maak `<user>@yourtenant.onmicrosoft.com` een lid van de rol azure_ad_user. Dit moet alleen worden verleend aan Azure AD-gebruikers.

**Voorbeeld:**

```sql
CREATE ROLE "user1@yourtenant.onmicrosoft.com" WITH LOGIN IN ROLE azure_ad_user;
```

> [!NOTE]
> Het verifiëren van een gebruiker via Azure AD geeft de gebruiker geen machtigingen voor toegang tot objecten binnen de Azure Database for PostgreSQL data base. U moet de gebruiker hand matig de vereiste machtigingen verlenen.

## <a name="creating-azure-ad-groups-in-azure-database-for-postgresql"></a>Azure AD-groepen maken in Azure Database for PostgreSQL

Als u een Azure AD-groep wilt inschakelen voor toegang tot uw data base, gebruikt u hetzelfde mechanisme als voor gebruikers, maar geeft u in plaats daarvan de groeps naam op:

**Voorbeeld:**

```sql
CREATE ROLE "Prod DB Readonly" WITH LOGIN IN ROLE azure_ad_user;
```

Wanneer u zich aanmeldt, gebruiken leden van de groep hun persoonlijke toegangs tokens, maar u moet zich aanmelden met de groeps naam die is opgegeven als de gebruikers naam.

## <a name="token-validation"></a>Token validatie

Azure AD-verificatie in Azure Database for PostgreSQL zorgt ervoor dat de gebruiker zich in de PostgreSQL-server bevindt en controleert de geldigheid van het token door de inhoud van het token te valideren. De volgende stappen voor het valideren van tokens worden uitgevoerd:

- Het token is ondertekend door Azure AD en is niet geknoeid
- Het token is uitgegeven door Azure AD voor de Tenant die aan de server is gekoppeld
- Het token is niet verlopen
- Het token is voor de Azure Database for PostgreSQL resource (en niet een andere Azure-resource)

## <a name="migrating-existing-postgresql-users-to-azure-ad-based-authentication"></a>Bestaande PostgreSQL-gebruikers migreren naar Azure AD-gebaseerde verificatie

U kunt Azure AD-verificatie inschakelen voor bestaande gebruikers. Er zijn twee situaties waarin u rekening moet houden:

### <a name="case-1-postgresql-username-matches-the-azure-ad-user-principal-name"></a>Voor beeld 1: PostgreSQL gebruikers naam komt overeen met de Azure AD User Principal name

In het onwaarschijnlijke geval dat uw bestaande gebruikers al overeenkomen met de gebruikers namen van Azure AD, kunt u de `azure_ad_user` rol hieraan toekennen om ze in te scha kelen voor Azure AD-verificatie:

```sql
GRANT azure_ad_user TO "existinguser@yourtenant.onmicrosoft.com";
```

Ze kunnen zich nu aanmelden met Azure AD-referenties in plaats van het eerder geconfigureerde PostgreSQL-gebruikers wachtwoord te gebruiken.

### <a name="case-2-postgresql-username-is-different-than-the-azure-ad-user-principal-name"></a>Voor beeld 2: PostgreSQL gebruikers naam wijkt af van de Azure AD User Principal name

Als een PostgreSQL-gebruiker niet bestaat in azure AD of een andere gebruikers naam heeft, kunt u Azure AD-groepen gebruiken om te verifiëren als deze PostgreSQL-gebruiker. U kunt bestaande Azure Database for PostgreSQL-gebruikers migreren naar Azure AD door een Azure AD-groep te maken met een naam die overeenkomt met de PostgreSQL-gebruiker en vervolgens de rol azure_ad_user aan de bestaande PostgreSQL-gebruiker te verlenen:

```sql
GRANT azure_ad_user TO "DBReadUser";
```

Hierbij wordt ervan uitgegaan dat u in uw Azure AD een groep ' DBReadUser ' hebt gemaakt. Gebruikers die deel uitmaken van deze groep, kunnen zich nu aanmelden bij de Data Base als deze gebruiker.

## <a name="next-steps"></a>Volgende stappen

* Bekijk de algemene concepten voor [Azure Active Directory verificatie met Azure database for PostgreSQL-enkele server](concepts-aad-authentication.md)

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/set-aad-admin.png
