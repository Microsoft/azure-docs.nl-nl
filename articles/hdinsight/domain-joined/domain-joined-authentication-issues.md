---
title: Verificatie problemen in azure HDInsight
description: Verificatie problemen in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.openlocfilehash: a0ca7cb8797b90d8cf933733c48be299e79be8aa
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933618"
---
# <a name="authentication-issues-in-azure-hdinsight"></a>Verificatie problemen in azure HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

Op beveiligde clusters die worden ondersteund door Azure Data Lake (gen1 of Gen2), wordt bij een domein gebruiker aanmelden bij de Cluster Services via HDI-gateway (zoals aanmelden bij de Apache Ambari-Portal), probeert de HDI-gateway eerst een OAuth-token van Azure Active Directory (Azure AD) te verkrijgen en vervolgens een Kerberos-ticket van Azure AD DS op te halen. Verificatie kan in een van deze fasen mislukken. Dit artikel is bedoeld voor het opsporen van fouten in een aantal van deze problemen.

Wanneer de verificatie mislukt, wordt u gevraagd referenties op te geven. Als u dit dialoog venster annuleert, wordt het fout bericht afgedrukt. Hier volgen enkele veelvoorkomende fout berichten:

## <a name="invalid_grant-or-unauthorized_client-50126"></a>invalid_grant of unauthorized_client, 50126

### <a name="issue"></a>Probleem

Aanmelden mislukt voor federatieve gebruikers met fout code 50126 (aanmelden is geslaagd voor Cloud gebruikers). Fout bericht is vergelijkbaar met:

```
Reason: Bad Request, Detailed Response: {"error":"invalid_grant","error_description":"AADSTS70002: Error validating credentials. AADSTS50126: Invalid username or password\r\nTrace ID: 09cc9b95-4354-46b7-91f1-efd92665ae00\r\n Correlation ID: 4209bedf-f195-4486-b486-95a15b70fbe4\r\nTimestamp: 2019-01-28 17:49:58Z","error_codes":[70002,50126], "timestamp":"2019-01-28 17:49:58Z","trace_id":"09cc9b95-4354-46b7-91f1-efd92665ae00","correlation_id":"4209bedf-f195-4486-b486-95a15b70fbe4"}
```

### <a name="cause"></a>Oorzaak

Azure AD-fout code 50126 betekent dat het `AllowCloudPasswordValidation` beleid niet is ingesteld door de Tenant.

### <a name="resolution"></a>Oplossing

De beheerder van de Azure AD-Tenant moet Azure AD in staat stellen wacht woord-hashes te gebruiken voor gebruikers met een back-up van ADFS.  Pas de `AllowCloudPasswordValidationPolicy` weer gegeven in het artikel [Enterprise Security package in HDInsight gebruiken](../domain-joined/apache-domain-joined-architecture.md).

---

## <a name="invalid_grant-or-unauthorized_client-50034"></a>invalid_grant of unauthorized_client, 50034

### <a name="issue"></a>Probleem

Aanmelden is mislukt met fout code 50034. Fout bericht is vergelijkbaar met:

```
{"error":"invalid_grant","error_description":"AADSTS50034: The user account Microsoft.AzureAD.Telemetry.Diagnostics.PII does not exist in the 0c349e3f-1ac3-4610-8599-9db831cbaf62 directory. To sign into this application, the account must be added to the directory.\r\nTrace ID: bbb819b2-4c6f-4745-854d-0b72006d6800\r\nCorrelation ID: b009c737-ee52-43b2-83fd-706061a72b41\r\nTimestamp: 2019-04-29 15:52:16Z", "error_codes":[50034],"timestamp":"2019-04-29 15:52:16Z","trace_id":"bbb819b2-4c6f-4745-854d-0b72006d6800", "correlation_id":"b009c737-ee52-43b2-83fd-706061a72b41"}
```

### <a name="cause"></a>Oorzaak

De gebruikers naam is onjuist (bestaat niet). De gebruiker gebruikt niet dezelfde gebruikers naam die wordt gebruikt in Azure Portal.

### <a name="resolution"></a>Oplossing

Gebruik dezelfde gebruikers naam die in die portal werkt.

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant of unauthorized_client, 50053

### <a name="issue"></a>Probleem

Gebruikers account is vergrendeld, fout code 50053. Fout bericht is vergelijkbaar met:

```
{"error":"unauthorized_client","error_description":"AADSTS50053: You've tried to sign in too many times with an incorrect user ID or password.\r\nTrace ID: 844ac5d8-8160-4dee-90ce-6d8c9443d400\r\nCorrelation ID: 23fe8867-0e8f-4e56-8764-0cdc7c61c325\r\nTimestamp: 2019-06-06 09:47:23Z","error_codes":[50053],"timestamp":"2019-06-06 09:47:23Z","trace_id":"844ac5d8-8160-4dee-90ce-6d8c9443d400","correlation_id":"23fe8867-0e8f-4e56-8764-0cdc7c61c325"}
```

### <a name="cause"></a>Oorzaak

Te veel aanmeldings pogingen met een onjuist wacht woord.

### <a name="resolution"></a>Oplossing

Wacht 30 minuten of, stop alle toepassingen die mogelijk worden geverifieerd.

---

## <a name="invalid_grant-or-unauthorized_client-50053-2"></a>invalid_grant of unauthorized_client, 50053 (#2)

### <a name="issue"></a>Probleem

Wacht woord is verlopen, fout code 50053. Fout bericht is vergelijkbaar met:

```
{"error":"user_password_expired","error_description":"AADSTS50055: Password is expired.\r\nTrace ID: 241a7a47-e59f-42d8-9263-fbb7c1d51e00\r\nCorrelation ID: c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a\r\nTimestamp: 2019-06-06 17:29:37Z","error_codes":[50055],"timestamp":"2019-06-06 17:29:37Z","trace_id":"241a7a47-e59f-42d8-9263-fbb7c1d51e00","correlation_id":"c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a","suberror":"user_password_expired","password_change_url":"https://portal.microsoftonline.com/ChangePassword.aspx"}
```

### <a name="cause"></a>Oorzaak

Het wacht woord is verlopen.

### <a name="resolution"></a>Oplossing

Wijzig het wacht woord in de Azure Portal (op uw on-premises systeem) en wacht 30 minuten totdat de synchronisatie is uitgevoerd.

---

## <a name="interaction_required"></a>interaction_required

### <a name="issue"></a>Probleem

Fout bericht ontvangen `interaction_required` .

### <a name="cause"></a>Oorzaak

Het beleid voor voorwaardelijke toegang of MFA wordt toegepast op de gebruiker. Omdat interactieve verificatie nog niet wordt ondersteund, moet de gebruiker of het cluster worden uitgesloten van MFA/voorwaardelijke toegang. Als u ervoor kiest het cluster op te heffen (op IP-adres gebaseerd uitsluitings beleid), moet u ervoor zorgen dat de AD `ServiceEndpoints` voor dat vnet is ingeschakeld.

### <a name="resolution"></a>Oplossing

Gebruik het beleid voor voorwaardelijke toegang om de Hdinsight-clusters van MFA vrij te maken, zoals wordt weer gegeven in [een HDInsight-cluster configureren met Enterprise Security Package met behulp van Azure Active Directory Domain Services](./apache-domain-joined-configure-using-azure-adds.md).

---

## <a name="sign-in-denied"></a>Aanmelden geweigerd

### <a name="issue"></a>Probleem

Aanmelden is geweigerd.

### <a name="cause"></a>Oorzaak

Om deze fase op te halen, is uw OAuth-verificatie geen probleem, maar is Kerberos-verificatie. Als dit cluster wordt ondersteund door ADLS, is OAuth Sign in geslaagd voordat de Kerberos-authenticatie wordt geprobeerd. In WASB-clusters wordt OAuth Sign in niet geprobeerd. Er kunnen veel redenen zijn waarom een Kerberos-fout is opgetreden, zoals wacht woord-hashes zijn niet synchroon, het gebruikers account is vergrendeld in azure AD DS, enzovoort. Wachtwoord hashes worden alleen gesynchroniseerd wanneer de gebruiker het wacht woord wijzigt. Wanneer u het exemplaar van Azure AD DS maakt, wordt het synchroniseren van wacht woorden gestart die zijn gewijzigd nadat het is gemaakt. Wacht woorden worden niet met terugwerkende kracht gesynchroniseerd die zijn ingesteld vóór het begin.

### <a name="resolution"></a>Oplossing

Als u denkt dat wacht woorden mogelijk niet synchroon zijn, probeert u het wacht woord te wijzigen en wacht u een paar minuten om te synchroniseren.

Als u een SSH-poging wilt uitvoeren, moet u proberen om (kinit) te verifiëren met behulp van dezelfde gebruikers referenties, van een computer die lid is van het domein. SSH in het hoofd-of Edge-knoop punt met een lokale gebruiker en voer vervolgens kinit uit.

---

## <a name="kinit-fails"></a>kinit mislukt

### <a name="issue"></a>Probleem

Kinit mislukt.

### <a name="cause"></a>Oorzaak

Hangt.

### <a name="resolution"></a>Oplossing

Om kinit te laten slagen, moet u weten dat u uw `sAMAccountName` (dit is de korte account naam zonder de realm). `sAMAccountName` is doorgaans het account voorvoegsel (zoals Bob in `bob@contoso.com` ). Voor sommige gebruikers kan het verschillend zijn. U hebt de mogelijkheid om door de map te bladeren/te zoeken om uw te leren `sAMAccountName` .

Manieren om te zoeken `sAMAccountName` :

* Als u zich kunt aanmelden bij Ambari met behulp van de lokale Ambari-beheerder, kijkt u naar de lijst met gebruikers.

* Als u een [Windows-computer](../../active-directory-domain-services/tutorial-create-management-vm.md)hebt die lid is van een domein, kunt u de standaard Windows AD-hulpprogram ma's gebruiken om te bladeren. Hiervoor is een werk account in het domein vereist.

* Vanuit het hoofd knooppunt kunt u SAMBA-opdrachten gebruiken om te zoeken. Hiervoor is een geldige Kerberos-sessie (geslaagde kinit) vereist. net ADS Search (userPrincipalName = Bob *) "

    De zoek-en blader resultaten bevatten het `sAMAccountName` kenmerk. U kunt ook andere kenmerken zoals `pwdLastSet` , `badPasswordTime` , enzovoort, bekijken `userPrincipalName` om te zien of deze eigenschappen overeenkomen met wat u verwacht.

---

## <a name="kinit-fails-with-preauthentication-failure"></a>kinit mislukt met fout vooraf-verificatie

### <a name="issue"></a>Probleem

Kinit mislukt met `Preauthentication` fout.

### <a name="cause"></a>Oorzaak

De gebruikers naam of het wacht woord is onjuist.

### <a name="resolution"></a>Oplossing

Controleer uw gebruikers naam en wacht woord. Controleer ook of andere eigenschappen hierboven worden beschreven. Als u uitgebreide fout opsporing wilt inschakelen, moet u `export KRB5_TRACE=/tmp/krb.log` eerst vanuit de sessie uitvoeren voordat u kinit probeert.

---

## <a name="job--hdfs-command-fails-due-to-tokennotfoundexception"></a>Taak/HDFS-opdracht mislukt vanwege TokenNotFoundException

### <a name="issue"></a>Probleem

Taak/HDFS-opdracht mislukt vanwege `TokenNotFoundException` .

### <a name="cause"></a>Oorzaak

Het vereiste OAuth-toegangs token is niet gevonden voor het slagen van de taak/opdracht. Het ADLS/ABFS-stuur programma probeert het OAuth-toegangs token op te halen uit de referentie service voordat er opslag aanvragen worden gedaan. Dit token wordt geregistreerd wanneer u zich met dezelfde gebruiker aanmeldt bij de Ambari-Portal.

### <a name="resolution"></a>Oplossing

Zorg ervoor dat u bij de Ambari-Portal hebt aangemeld met de gebruikers naam waarvan de identiteit wordt gebruikt om de taak uit te voeren.

---

## <a name="error-fetching-access-token"></a>Fout bij het ophalen van het toegangs token

### <a name="issue"></a>Probleem

Gebruiker ontvangt een fout bericht `Error fetching access token` .

### <a name="cause"></a>Oorzaak

Deze fout treedt af en toe op wanneer gebruikers toegang proberen te krijgen tot de ADLS Gen2 met behulp van Acl's en het Kerberos-token is verlopen.

### <a name="resolution"></a>Oplossing

* Voor Azure Data Lake Storage Gen1 moet u de cache van de browser opschonen en opnieuw aanmelden bij Ambari.

* Voor Azure Data Lake Storage Gen2 voert `/usr/lib/hdinsight-common/scripts/RegisterKerbTicketAndOAuth.sh <upn>` u uit voor de gebruiker waarmee de gebruiker zich probeert aan te melden.

---

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]