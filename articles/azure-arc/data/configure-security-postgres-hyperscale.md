---
title: Beveiliging configureren voor uw PostgreSQL Hyperscale-servergroep met Azure Arc
description: Beveiliging configureren voor uw PostgreSQL Hyperscale-servergroep met Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: d6e27fddceb69efbb2c1697c09ee9b61d7f38ee4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101687971"
---
# <a name="configure-security-for-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Beveiliging configureren voor uw PostgreSQL Hyperscale-servergroep met Azure Arc

In dit document worden verschillende aspecten van de beveiliging van uw server groep beschreven:
- Versleuteling 'at rest'
- Gebruikersbeheer
   - Algemene perspectieven
   - Het wacht woord van de gebruiker met beheerders rechten voor _post gres_ wijzigen
- Controleren

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="encryption-at-rest"></a>Versleuteling 'at rest'
U kunt versleuteling op rest implementeren door de schijven te versleutelen waarop u uw data bases opslaat en/of door database functies te gebruiken voor het versleutelen van de gegevens die u invoegt of bijwerkt.

### <a name="hardware-linux-host-volume-encryption"></a>Hardware: volume versleuteling van de Linux-host
Implementeer systeem gegevens versleuteling om alle gegevens te beveiligen die zich bevinden op de schijven die worden gebruikt door uw Azure-Arc ingeschakeld Data Services Setup. Meer informatie over dit onderwerp kunt u lezen:
- [Gegevens versleuteling in rust](https://wiki.archlinux.org/index.php/Data-at-rest_encryption) op Linux in het algemeen 
- Schijf versleuteling met `cryptsetup` de opdracht luks Encryption (Linux) (met https://www.cyberciti.biz/security/howto-linux-hard-disk-encryption-with-luks-cryptsetup-command/) name omdat Azure Arc is ingeschakeld Data Services wordt uitgevoerd op de fysieke infra structuur die u opgeeft, bent u verantwoordelijk voor het beveiligen van de infra structuur.

### <a name="software-use-the-postgresql-pgcrypto-extension-in-your-server-group"></a>Software: gebruik de `pgcrypto` uitbrei ding postgresql in uw server groep
Naast het versleutelen van de schijven die worden gebruikt voor het hosten van uw Azure Arc-installatie, kunt u uw Azure-PostgreSQL grootschalige-Server groep configureren om mechanismen beschikbaar te maken die uw toepassingen kunnen gebruiken om gegevens in uw data base (s) te versleutelen. De `pgcrypto` uitbrei ding maakt deel uit van de `contrib` uitbrei dingen van post gres en is beschikbaar in uw grootschalige-Server groep voor Azure-Arc ingeschakelde postgresql. Hier vindt u informatie over `pgcrypto` de [](https://www.postgresql.org/docs/current/pgcrypto.html)uitbrei ding.
Samen met de volgende opdrachten kunt u de uitbrei ding inschakelt, kunt u deze maken en gebruiken:


#### <a name="create-the-pgcrypto-extension"></a>De `pgcrypto` uitbrei ding maken
Maak verbinding met uw server groep met het client hulpprogramma van uw keuze en voer de standaard PostgreSQL-query uit:
```console
CREATE EXTENSION pgcrypto;
```

> [Hier](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md) vindt u informatie over hoe u verbinding kunt maken.

#### <a name="verify-the-list-the-extensions-ready-to-use-in-your-server-group"></a>Controleer de lijst met uitbrei dingen die u kunt gebruiken in uw server groep
U kunt controleren of de `pgcrypto` uitbrei ding gereed is voor gebruik door de beschik bare uitbrei dingen in uw server groep weer te geven.
Maak verbinding met uw server groep met het client hulpprogramma van uw keuze en voer de standaard PostgreSQL-query uit:
```console
select * from pg_extension;
```
U moet zien `pgcrypto` of u deze hebt ingeschakeld en gemaakt met de opdrachten die hierboven zijn aangegeven.

#### <a name="use-the-pgcrypto-extension"></a>De `pgcrypto` uitbrei ding gebruiken
Nu kunt u de code van uw toepassingen aanpassen zodat ze gebruikmaken van de functies die worden aangeboden door `pgcrypto` :
- Algemene hash-functies
- Wachtwoord hash-functies
- PGP-versleutelings functies
- Onbewerkte versleutelings functies
- Functies met wille keurige gegevens

Als u bijvoorbeeld hash-waarden wilt genereren. Voer de opdracht uit:

```console
Select crypt('Les sanglots longs des violons de l_automne', gen_salt('md5'));
```

Retourneert de volgende hash:

```console
              crypt
------------------------------------
 $1$/9ACBYOV$z52PAGjQ5WTU9xvEECBNv/   
```

Of bijvoorbeeld:

```console
select hmac('Les sanglots longs des violons de l_automne', 'md5', 'sha256');
```

retourneert de volgende hash:

```console
                                hmac
--------------------------------------------------------------------
 \xd4e4790b69d2cc8dbce3385ee63272bc7760f1603640bb211a7b864e695570c5
```

Of bijvoorbeeld om versleutelde gegevens zoals een wacht woord op te slaan:

We gaan ervan uit dat mijn toepassing geheimen opslaat in de volgende tabel:

```console
create table mysecrets(USERid int, USERname char(255), USERpassword char(512));
```

En ik Versleutel hun wacht woord bij het maken van een gebruiker:

```console
insert into mysecrets values (1, 'Me', crypt('MySecretPasswrod', gen_salt('md5')));
```

Ik zie nu dat mijn wacht woord is versleuteld:

```console
select * from mysecrets;
```

Uitvoer:

- Gebruikers-id: 1
- Gebruikers naam: mij
- USERpassword: $1 $ Uc7jzZOp $ NTfcGo7F10zGOkXOwjHy31

Wanneer ik verbinding maak met mijn toepassing en een wacht woord geef, wordt het gezocht in de `mysecrets` tabel en wordt de naam van de gebruiker geretourneerd als er een overeenkomst is tussen het wacht woord dat is opgegeven voor de toepassing en de wacht woorden die in de tabel zijn opgeslagen. Bijvoorbeeld:

- Ik geef het verkeerde wacht woord door:
   ```console
   select USERname from mysecrets where (USERpassword = crypt('WrongPassword', USERpassword));
   ```

   Uitvoer 

   ```returns
    USERname
   ---------
   (0 rows)
   ```
- Ik geef het juiste wacht woord door:

   ```console
   select USERname from mysecrets where (USERpassword = crypt('MySecretPasswrod', USERpassword));
   ``` 

   Uitvoer:

   ```output
    USERname
   ---------
   Me
   (1 row)
   ```

Dit kleine voor beeld laat zien dat u gegevens in rust kunt versleutelen (versleutelde gegevens opslaan) in azure Arc enabled PostgreSQL grootschalige met behulp van de post gres- `pgcrypto` extensie en dat uw toepassingen functies kunnen gebruiken die worden aangeboden door `pgcrypto` om deze versleutelde gegevens te bewerken.

## <a name="user-management"></a>Gebruikersbeheer
### <a name="general-perspectives"></a>Algemene perspectieven
U kunt de standaard post gres manier gebruiken om gebruikers of rollen te maken. Als u dit doet, zijn deze artefacten echter alleen beschikbaar op de coördinatorrol. Tijdens de preview hebben deze gebruikers/rollen nog geen toegang tot gegevens die zijn gedistribueerd buiten het coördinatorknooppunt en op de werkknooppunten van uw servergroep. De reden hiervoor is dat de gebruikersdefinitie in de preview niet wordt gerepliceerd naar de werkknooppunten.

### <a name="change-the-password-of-the-_postgres_-administrative-user"></a>Het wacht woord van de gebruiker met beheerders rechten voor _post gres_ wijzigen
Azure Arc enabled PostgreSQL grootschalige wordt geleverd met de standaard post gres administratieve gebruiker _post gres_ waarvoor u het wacht woord instelt wanneer u de Server groep maakt.
De algemene indeling van de opdracht voor het wijzigen van het wacht woord is:
```console
azdata arc postgres server edit --name <server group name> --admin-password
```

Waar `--admin-password` is een Booleaanse waarde die betrekking heeft op de aanwezigheid van een in de AZDATA_PASSWORD **sessie** omgevings variabele.
Als de omgevings **variabele AZDATA_PASSWORD bestaat** en een waarde heeft, wordt met de bovenstaande opdracht het wacht woord van de gebruiker post gres ingesteld op de waarde van deze omgevings variabele.

Als de variabele voor de AZDATA_PASSWORD- **sessie** bestaat, maar geen waarde heeft of de AZDATA_PASSWORD **sessie** omgevings variabele niet bestaat, wordt de gebruiker door de bovenstaande opdracht gevraagd om een wacht woord interactief in te voeren

#### <a name="change-the-password-of-the-postgres-administrative-user-in-an-interactive-way"></a>Wijzig het wacht woord van de post gres-gebruiker met beheerders rechten op een interactieve manier

1. De variabele voor de AZDATA_PASSWORD **sessie** omgeving verwijderen of de waarde ervan verwijderen
2. Voer de opdracht uit:
   ```console
   azdata arc postgres server edit --name <server group name> --admin-password
   ```
   Bijvoorbeeld
   ```console
   azdata arc postgres server edit -n postgres01 --admin-password
   ```
   U wordt gevraagd het wacht woord in te voeren en het te bevestigen:
   ```console
   Postgres Server password:
   Confirm Postgres Server password:
   ```
   Wanneer het wacht woord wordt bijgewerkt, wordt de uitvoer van de opdracht weer gegeven:
   ```console
   Updating password
   Updating postgres01 in namespace `arc`
   postgres01 is Ready
   ```
   
#### <a name="change-the-password-of-the-postgres-administrative-user-using-the-azdata_password-session-environment-variable"></a>Wijzig het wacht woord van de post gres-gebruiker met beheerders rechten met behulp van de AZDATA_PASSWORD **sessie** omgevings variabele:
1. Stel de waarde van de variabele AZDATA_PASSWORD **sessie** omgeving in op de gewenste wacht woord.
2. Voer de volgende opdracht uit:
   ```console
   azdata arc postgres server edit --name <server group name> --admin-password
   ```
   Bijvoorbeeld
   ```console
   azdata arc postgres server edit -n postgres01 --admin-password
   ```
   
   Wanneer het wacht woord wordt bijgewerkt, wordt de uitvoer van de opdracht weer gegeven:
   ```console
   Updating password
   Updating postgres01 in namespace `arc`
   postgres01 is Ready
   ```

> [!NOTE]
> Voer de volgende handelingen uit om te controleren of de omgevings variabele van de AZDATA_PASSWORD-sessie bestaat en welke waarde het heeft:
> - Op een Linux-client:
> ```console
> printenv AZDATA_PASSWORD
> ```
>
> - Op een Windows-client met Power shell:
> ```console
> echo $env:AZDATA_PASSWORD
> ```

## <a name="audit"></a>Controleren

Configureer voor controle scenario's uw server groep voor het gebruik `pgaudit` van de uitbrei dingen van post gres. Raadpleeg voor meer informatie `pgaudit` over [ `pgAudit` github-project](https://github.com/pgaudit/pgaudit/blob/master/README.md). Als u de `pgaudit` uitbrei ding in de Server groep wilt inschakelen, leest [u postgresql-extensies](using-extensions-in-postgresql-hyperscale-server-group.md).


## <a name="next-steps"></a>Volgende stappen
- Zie [ `pgcrypto` uitbrei ding](https://www.postgresql.org/docs/current/pgcrypto.html)
- Zie [postgresql-extensies gebruiken](using-extensions-in-postgresql-hyperscale-server-group.md)

