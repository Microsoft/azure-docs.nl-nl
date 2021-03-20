---
title: Overzicht van beveiliging in Azure Data Lake Storage Gen1 | Microsoft Docs
description: Meer informatie over de beveiligings mogelijkheden van Azure Data Lake Storage Gen1, waaronder verificatie, autorisatie, netwerk isolatie, gegevens beveiliging en controle.
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: twooley
ms.openlocfilehash: 240018381a3139a6378141d78514e43ae469de5d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92146304"
---
# <a name="security-in-azure-data-lake-storage-gen1"></a>Beveiliging in Azure Data Lake Storage Gen1

Veel bedrijven maken gebruik van big data Analytics voor zakelijke inzichten om hen te helpen bij het maken van slimme beslissingen. Een organisatie kan een complexe en gereguleerde omgeving hebben, met een toenemend aantal verschillende gebruikers. Het is van essentieel belang dat een onderneming ervoor zorgt dat kritieke Bedrijfs gegevens veilig worden opgeslagen, met het juiste toegangs niveau dat aan afzonderlijke gebruikers wordt verleend. Azure Data Lake Storage Gen1 is ontworpen om te voldoen aan deze beveiligings vereisten. In dit artikel vindt u meer informatie over de beveiligings mogelijkheden van Data Lake Storage Gen1, waaronder:

* Verificatie
* Autorisatie
* Netwerkisolatie
* Gegevensbeveiliging
* Controleren

## <a name="authentication-and-identity-management"></a>Verificatie en identiteits beheer

Verificatie is het proces waarmee de identiteit van een gebruiker wordt geverifieerd wanneer de gebruiker met Data Lake Storage Gen1 communiceert of met een service die verbinding maakt met Data Lake Storage Gen1. Data Lake Storage Gen1 maakt gebruik van identiteits beheer en-verificatie [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)een uitgebreide Cloud oplossing voor identiteits-en toegangs beheer die het beheer van gebruikers en groepen vereenvoudigt.

Elk Azure-abonnement kan worden gekoppeld aan een exemplaar van Azure Active Directory. Alleen gebruikers en service-identiteiten die in uw Azure Active Directory-service zijn gedefinieerd, hebben toegang tot uw Data Lake Storage Gen1-account met behulp van de Azure Portal, opdracht regel Programma's of client toepassingen die uw organisatie bouwt met behulp van de Data Lake Storage Gen1 SDK. De belangrijkste voor delen van het gebruik van Azure Active Directory als een gecentraliseerd toegangs beheer mechanisme zijn:

* Vereenvoudigd beheer van identiteits levenscyclus. De identiteit van een gebruiker of een service (een Service-Principal-identiteit) kan snel worden gemaakt en snel worden ingetrokken door simpelweg het account in de map te verwijderen of uit te scha kelen.
* Multi-factor Authentication. [Multi-factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md) biedt een extra beveiligingslaag voor gebruikers aanmeldingen en trans acties.
* Verificatie van elke client via een standaard open protocol, zoals OAuth of OpenID Connect.
* Federatie met ondernemings Directory Services en Cloud-id-providers.

## <a name="authorization-and-access-control"></a>Autorisatie en toegangs beheer

Nadat Azure Active Directory een gebruiker verifieert, zodat de gebruiker toegang heeft tot Data Lake Storage Gen1, controleert autorisatie de toegangs machtigingen voor Data Lake Storage Gen1. Data Lake Storage Gen1 de autorisatie voor account-en gegevensgerelateerde activiteiten op de volgende wijze gescheiden:

* [Op rollen gebaseerd toegangs beheer voor Azure (Azure RBAC)](../role-based-access-control/overview.md) voor account beheer
* POSIX-ACL voor toegang tot gegevens in de Store

### <a name="azure-rbac-for-account-management"></a>Azure RBAC voor account beheer

Standaard worden vier basis rollen gedefinieerd voor Data Lake Storage Gen1. Met de functies kunnen verschillende bewerkingen worden uitgevoerd op een Data Lake Storage Gen1 account via de Azure Portal, Power shell-cmdlets en REST-Api's. De rollen eigenaar en Inzender kunnen diverse beheer functies op het account uitvoeren. U kunt de rol van lezer toewijzen aan gebruikers die alleen account beheer gegevens weer geven.

![Azure-rollen](./media/data-lake-store-security-overview/rbac-roles.png "Azure-rollen")

Houd er rekening mee dat hoewel rollen zijn toegewezen aan account beheer, sommige functies van invloed zijn op de toegang tot gegevens. U moet Acl's gebruiken voor het beheren van de toegang tot bewerkingen die een gebruiker kan uitvoeren op het bestands systeem. In de volgende tabel ziet u een overzicht van de beheer rechten en gegevens toegangs rechten voor de standaard rollen.

| Rollen | Beheer rechten | Gegevens toegangs rechten | Uitleg |
| --- | --- | --- | --- |
| Er is geen rol toegewezen |Geen |Beheerd door ACL |De gebruiker kan de Azure Portal-of Azure PowerShell-cmdlets niet gebruiken om Data Lake Storage Gen1 te bladeren. De gebruiker kan alleen opdracht regel Programma's gebruiken. |
| Eigenaar |Alles |Alles |De rol van eigenaar is een super gebruiker. Deze rol kan alles beheren en heeft volledige toegang tot de gegevens. |
| Lezer |Alleen-lezen |Beheerd door ACL |Met de rol lezer kan alles worden weer gegeven met betrekking tot account beheer, zoals welke gebruiker aan welke rol is toegewezen. De rol van lezer kan geen wijzigingen aanbrengen. |
| Inzender |Alle behalve functies toevoegen en verwijderen |Beheerd door ACL |De rol Inzender kan sommige aspecten van een account beheren, zoals implementaties en het maken en beheren van waarschuwingen. De rol Inzender kan geen rollen toevoegen of verwijderen. |
| Beheerder van gebruikerstoegang |Rollen toevoegen en verwijderen |Beheerd door ACL |De rol beheerder van gebruikers toegang kan de gebruikers toegang tot accounts beheren. |

Zie [gebruikers of beveiligings groepen toewijzen aan data Lake Storage gen1 accounts](data-lake-store-secure-data.md#assign-users-or-security-groups-to-data-lake-storage-gen1-accounts)voor instructies.

### <a name="using-acls-for-operations-on-file-systems"></a>Acl's gebruiken voor bewerkingen op bestands systemen

Data Lake Storage Gen1 is een hiërarchisch bestands systeem zoals Hadoop Distributed File System (HDFS) en het ondersteunt [POSIX-acl's](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Hiermee worden Lees-(r), schrijf-(w) en execute (x)-machtigingen voor bronnen voor de rol eigenaar, voor de groep eigen aren en voor andere gebruikers en groepen, gecontroleerd. In Data Lake Storage Gen1 kunnen Acl's worden ingeschakeld voor de hoofdmap, submappen en afzonderlijke bestanden. Zie [toegangs beheer in data Lake Storage gen1](data-lake-store-access-control.md)voor meer informatie over de werking van acl's in de context van data Lake Storage gen1.

U wordt aangeraden Acl's voor meerdere gebruikers te definiëren met behulp van [beveiligings groepen](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Gebruikers toevoegen aan een beveiligings groep en vervolgens de Acl's voor een bestand of map aan die beveiligings groep toewijzen. Dit is handig wanneer u toegewezen machtigingen wilt opgeven, omdat u beperkt tot Maxi maal 28 vermeldingen voor toegewezen machtigingen. Zie [gebruikers of beveiligings groep als Acl's toewijzen aan het bestands systeem van data Lake Storage gen1](data-lake-store-secure-data.md#filepermissions)voor meer informatie over het beter beveiligen van gegevens die zijn opgeslagen in data Lake Storage gen1 door gebruik te maken van Azure Active Directory-beveiligings groepen.

![Toegangs machtigingen weer geven](./media/data-lake-store-security-overview/adl.acl.2.png "Toegangs machtigingen weer geven")

## <a name="network-isolation"></a>Netwerkisolatie

Gebruik Data Lake Storage Gen1 om de toegang tot uw gegevens archief op netwerk niveau te beheren. U kunt firewalls tot stand brengen en een IP-adres bereik definiëren voor uw vertrouwde clients. Met een IP-adres bereik kunnen alleen clients met een IP-adres binnen het gedefinieerde bereik verbinding maken met Data Lake Storage Gen1.

![Firewall instellingen en IP-toegang](./media/data-lake-store-security-overview/firewall-ip-access.png "Firewall instellingen en IP-adres")

Azure Virtual Networks (VNet) Support service tags voor Data Lake gen 1. Een servicetag vertegenwoordigt een groep IP-adres voorvoegsels van een bepaalde Azure-service. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen. Zie overzicht van Azure- [service Tags](../virtual-network/service-tags-overview.md)voor meer informatie.

## <a name="data-protection"></a>Gegevensbeveiliging

Data Lake Storage Gen1 beschermt uw gegevens gedurende de hele levens cyclus. Voor gegevens die onderweg zijn, gebruikt Data Lake Storage Gen1 het Protocol van de industrie norm Transport Layer Security (TLS 1,2) om gegevens via het netwerk te beveiligen.

![Versleuteling in Data Lake Storage Gen1](./media/data-lake-store-security-overview/adls-encryption.png "Versleuteling in Data Lake Storage Gen1")

Data Lake Storage Gen1 biedt ook versleuteling voor gegevens die in het account zijn opgeslagen. U kunt ervoor kiezen de gegevens te versleutelen of niet te versleutelen. Als u zich aanmeldt voor versleuteling, worden de gegevens die zijn opgeslagen in Data Lake Storage Gen1 versleuteld voordat ze worden opgeslagen op de permanente media. In dat geval worden de gegevens in Data Lake Storage Gen1 automatisch versleuteld voordat ze worden opgehaald en gedecodeerd voordat ze worden opgezocht, zodat het volledig transparant is voor de client die toegang heeft tot de gegevens. Er is geen code wijziging vereist aan de client zijde om gegevens te versleutelen/ontsleutelen.

Data Lake Storage Gen1 biedt sleutel beheer twee modi voor het beheren van uw Mek's (Master Encryption Keys), die vereist zijn voor het ontsleutelen van gegevens die zijn opgeslagen in Data Lake Storage Gen1. U kunt Data Lake Storage Gen1 de Mek's voor u laten beheren of ervoor kiezen om het eigendom van de Mek's te behouden met uw Azure Key Vault-account. U geeft de modus van sleutel beheer op tijdens het maken van een Data Lake Storage Gen1-account. Zie [aan de slag met Azure data Lake Storage gen1 met behulp van Azure Portal](data-lake-store-get-started-portal.md)voor meer informatie over het bieden van versleutelings configuratie.

## <a name="activity-and-diagnostic-logs"></a>Diagnostische en activiteitslogboeken

U kunt activiteiten of diagnostische Logboeken gebruiken, afhankelijk van het feit of u zoekt naar Logboeken voor activiteiten met betrekking tot account beheer of activiteiten met betrekking tot gegevens.

* Activiteiten met betrekking tot account beheer gebruiken Azure Resource Manager-Api's en worden in de Azure Portal via activiteiten logboeken weer gegeven.
* Gegevensgerelateerde activiteiten gebruiken WebHDFS REST-Api's en worden weer gegeven in de Azure Portal via Diagnostische logboeken.

### <a name="activity-log"></a>Activiteitenlogboek

Om te voldoen aan de voor Schriften, kan een organisatie een adequaat controle spoor van account beheer activiteiten vereisen als er specifieke incidenten moeten worden opgevolgd. Data Lake Storage Gen1 heeft ingebouwde bewaking en registreert alle account beheer activiteiten.

Bekijk en selecteer de kolommen die u wilt registreren voor de controle sporen van account beheer. U kunt ook activiteiten logboeken exporteren naar Azure Storage.

![Activiteitenlogboek](./media/data-lake-store-security-overview/activity-logs.png "Activiteitenlogboek")

Zie [activiteiten logboeken weer geven om acties op resources te controleren](../azure-resource-manager/management/view-activity-logs.md)voor meer informatie over het werken met activiteiten Logboeken.

### <a name="diagnostics-logs"></a>Logboeken met diagnostische gegevens

U kunt de controle van gegevens toegang en diagnostische logboek registratie inschakelen in de Azure Portal en de logboeken verzenden naar een Azure Blob Storage-account, een Event Hub of Azure Monitor Logboeken.

![Diagnostische logboeken](./media/data-lake-store-security-overview/diagnostic-logs.png "Logboeken met diagnostische gegevens")

Zie voor meer informatie over het werken met Diagnostische logboeken met Data Lake Storage Gen1 [Diagnostische logboeken openen voor data Lake Storage gen1](data-lake-store-diagnostic-logs.md).

## <a name="summary"></a>Samenvatting

Enter prise-klanten vragen een Cloud platform voor gegevens analyse dat veilig en gemakkelijk te gebruiken is. Data Lake Storage Gen1 is ontworpen om deze vereisten te helpen aanpakken door middel van identiteits beheer en verificatie via Azure Active Directory integratie, autorisatie op basis van toegangs beheer, netwerk isolatie, gegevens versleuteling tijdens de overdracht en in rust en controle.

Als u nieuwe functies in Data Lake Storage Gen1 wilt zien, stuurt u ons uw feedback in het [Data Lake Storage gen1 UserVoice-forum](https://feedback.azure.com/forums/327234-data-lake).

## <a name="see-also"></a>Zie ook

* [Overzicht van Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Aan de slag met Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* [Gegevens beveiligen in Data Lake Storage Gen1](data-lake-store-secure-data.md)