---
title: Azure HDInsight-architectuur met Enterprise Security Package
description: Meer informatie over het plannen van Azure HDInsight-beveiliging met Enterprise Security Package.
ms.reviewer: omidm
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/11/2020
ms.openlocfilehash: 404cd8652a4daf602f3326696ef5df2c3a23a548
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943313"
---
# <a name="use-enterprise-security-package-in-hdinsight"></a>Enterprise Security Package in HDInsight gebruiken

Het standaard Azure HDInsight-cluster is een cluster met één gebruiker. Het is geschikt voor de meeste bedrijven met kleinere toepassings teams die werk belastingen voor grote gegevens bouwen. Elke gebruiker kan een toegewezen cluster op aanvraag maken en dit vernietigen wanneer het niet meer nodig is.

Veel ondernemingen zijn verplaatst naar een model waarin IT-teams clusters beheren en meerdere toepassings teams delen clusters. Deze grotere ondernemingen hebben meerdere gebruikers toegang nodig tot elk cluster in azure HDInsight.

HDInsight is afhankelijk van een populaire ID-provider--Active Directory--op een beheerde manier. Door HDInsight te integreren met [Azure Active Directory Domain Services (Azure AD DS)](../../active-directory-domain-services/overview.md), kunt u toegang krijgen tot de clusters met behulp van uw domein referenties.

De virtuele machines (Vm's) in HDInsight zijn lid van een domein dat is gekoppeld aan het gegeven domein. Alle services die worden uitgevoerd op HDInsight (Apache Ambari, Apache Hive server, Apache zwerver, Apache Spark Thrift server en anderen), werken dus probleemloos voor de geverifieerde gebruiker. Beheerders kunnen vervolgens krachtige autorisatie beleidsregels maken met behulp van Apache zwerver om op rollen gebaseerd toegangs beheer te bieden voor bronnen in het cluster.

## <a name="integrate-hdinsight-with-active-directory"></a>HDInsight integreren met Active Directory

Open-Source Apache Hadoop is afhankelijk van het Kerberos-protocol voor verificatie en beveiliging. Daarom worden HDInsight-cluster knooppunten met Enterprise Security Package (ESP) gekoppeld aan een domein dat wordt beheerd door Azure AD DS. Kerberos-beveiliging is geconfigureerd voor de Hadoop-onderdelen op het cluster.

De volgende zaken worden automatisch gemaakt:

- Een service-principal voor elke Hadoop-component
- Een machine-principal voor elke computer die lid is van het domein
- Een organisatie-eenheid (OE) voor elk cluster voor het opslaan van deze service-en machine-principals

Als u wilt samenvatten, moet u een omgeving instellen met:

- Een Active Directory domein (beheerd door Azure AD DS). **De domein naam moet 39 tekens of minder zijn om te kunnen werken met Azure HDInsight.**
- Secure LDAP (LDAPS) ingeschakeld in azure AD DS.
- De juiste netwerk verbinding van het virtuele HDInsight-netwerk naar het virtuele netwerk van Azure AD DS als u hiervoor afzonderlijke virtuele netwerken kiest. Een VM in het virtuele netwerk van HDInsight moet over een regel van het gezichts vermogen van Azure AD DS beschikken via de peering van het virtuele netwerk. Als HDInsight en Azure AD DS in hetzelfde virtuele netwerk zijn geïmplementeerd, wordt de verbinding automatisch gegeven en is er geen verdere actie nodig.

## <a name="set-up-different-domain-controllers"></a>Verschillende domein controllers instellen

HDInsight ondersteunt momenteel alleen Azure AD DS als de hoofd domein controller die door het cluster wordt gebruikt voor Kerberos-communicatie. Maar andere complexe Active Directory-instellingen zijn mogelijk, zolang een dergelijke installatie leidt tot het inschakelen van Azure AD DS voor HDInsight-toegang.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services

[Azure AD DS](../../active-directory-domain-services/overview.md) biedt een beheerd domein dat volledig compatibel is met Windows Server Active Directory. Micro soft zorgt voor het beheren, patchen en bewaken van het domein in een configuratie met hoge Beschik baarheid (HA). U kunt uw cluster implementeren zonder dat u zich zorgen hoeft te maken over het onderhouden van domein controllers.

Gebruikers, groepen en wacht woorden worden gesynchroniseerd vanuit Azure AD. De eenrichtings synchronisatie van uw Azure AD-exemplaar naar Azure AD DS stelt gebruikers in staat om zich met dezelfde bedrijfs referenties aan te melden bij het cluster.

Zie [HDInsight-clusters met ESP configureren met behulp van Azure AD DS](./apache-domain-joined-configure-using-azure-adds.md)voor meer informatie.

### <a name="on-premises-active-directory-or-active-directory-on-iaas-vms"></a>On-premises Active Directory of Active Directory op IaaS Vm's

Als u een on-premises Active Directory-exemplaar of meer complexe Active Directory-instellingen voor uw domein hebt, kunt u deze identiteiten synchroniseren met Azure AD met behulp van Azure AD Connect. Vervolgens kunt u Azure AD DS inschakelen op die Active Directory Tenant.

Omdat Kerberos afhankelijk is van wacht woord-hashes, moet u [wachtwoord hash-synchronisatie inschakelen op Azure AD DS](../../active-directory-domain-services/tutorial-create-instance.md).

Als u Federatie gebruikt met Active Directory Federation Services (AD FS), moet u wachtwoord hash-synchronisatie inschakelen. (Zie [deze video](https://youtu.be/qQruArbu2Ew)voor een aanbevolen installatie.) Wachtwoord hash Sync helpt bij herstel na nood gevallen in geval van een storing in uw AD FS-infra structuur en biedt ook een bescherming tegen beveiligings problemen. Zie [enable password hash sync with Azure AD Connect Sync](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)(Engelstalig) voor meer informatie.

Het gebruik van on-premises Active Directory of Active Directory op IaaS Vm's alleen, zonder Azure AD en Azure AD DS, is geen ondersteunde configuratie voor HDInsight-clusters met ESP.

Als Federatie wordt gebruikt en wacht woord-hashes correct worden gesynchroniseerd, maar er verificatie fouten optreden, controleert u of Cloud wachtwoord verificatie is ingeschakeld voor de Power shell-service-principal. Als dat niet het geval is, moet u een [hrd-beleid (Home realm Discovery)](../../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) instellen voor uw Azure AD-Tenant. Het HRD-beleid controleren en instellen:

1. Installeer de preview [Azure AD Power shell-module](/powershell/azure/active-directory/install-adv2).

   ```powershell
   Install-Module AzureAD
   ```

2. Verbinding maken met behulp van de referenties van de globale beheerder (Tenant beheerder).

   ```powershell
   Connect-AzureAD
   ```

3. Controleer of de Microsoft Azure PowerShell Service-Principal al is gemaakt.

   ```powershell
   Get-AzureADServicePrincipal -SearchString "Microsoft Azure Powershell"
   ```

4. Als deze niet bestaat, maakt u de Service-Principal.

   ```powershell
   $powershellSPN = New-AzureADServicePrincipal -AppId 1950a258-227b-4e31-a9cf-717495945fc2
   ```

5. Maak en koppel het beleid aan deze service-principal.

   ```powershell
    # Determine whether policy exists
    Get-AzureADPolicy | Where {$_.DisplayName -eq "EnableDirectAuth"}

    # Create if not exists
    $policy = New-AzureADPolicy `
        -Definition @('{"HomeRealmDiscoveryPolicy":{"AllowCloudPasswordValidation":true}}') `
        -DisplayName "EnableDirectAuth" `
        -Type "HomeRealmDiscoveryPolicy"

    # Determine whether a policy for the service principal exist
    Get-AzureADServicePrincipalPolicy `
        -Id $powershellSPN.ObjectId

    # Add a service principal policy if not exist
    Add-AzureADServicePrincipalPolicy `
        -Id $powershellSPN.ObjectId `
        -refObjectID $policy.ID
   ```

## <a name="next-steps"></a>Volgende stappen

- [HDInsight-clusters configureren met ESP](apache-domain-joined-configure-using-azure-adds.md)
- [Apache Hive-beleid voor HDInsight-clusters configureren met ESP](apache-domain-joined-run-hive.md)
- [HDInsight-clusters beheren met ESP](apache-domain-joined-manage.md)