---
title: Nalevingsdetails van de regelgeving voor CIS Microsoft Azure Foundations Benchmark
description: Informatie over het ingebouwde initiatief voor naleving van regelgeving in CIS Microsoft Azure Foundations Benchmark. Elke beheeroptie wordt toegewezen aan een of meer Azure Policy-definities die helpen bij de evaluatie.
ms.date: 11/17/2020
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: 1f9379427be69ecf8a07000744f0410c9b34f13c
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94696928"
---
# <a name="details-of-the-cis-microsoft-azure-foundations-benchmark-regulatory-compliance-built-in-initiative"></a>Informatie over het ingebouwde initiatief voor naleving van regelgeving in CIS Microsoft Azure Foundations Benchmark

Het volgende artikel beschrijft hoe de definitie van het ingebouwde initiatief voor naleving van regelgeving in Azure Policy wordt toegewezen aan **nalevingsdomeinen** en **beheeropties** in CIS Microsoft Foundations Benchmark.
Zie [CIS Microsoft Azure Foundations Benchmark](https://www.cisecurity.org/benchmark/azure/)voor meer informatie over deze nalevingsstandaard. Zie [Azure Policy-beleidsdefinitie](../concepts/definition-structure.md#type) en [Gedeelde verantwoordelijkheid in de Cloud](../../../security/fundamentals/shared-responsibility.md) om _Eigendom_ te begrijpen.

De volgende toewijzingen bevinden zich in het besturingselement **CIS Microsoft Azure Foundations Benchmark**. Gebruik de navigatie aan de rechterkant om rechtstreeks naar een toewijzing van een specifiek **nalevingsdomein** te gaan. Veel van de beheeropties worden geïmplementeerd met een [Azure Policy](../overview.md)-initiatiefdefinitie. Als u de complete initiatiefdefinitie wilt bekijken, opent u **Beleid** in de Azure-portal en selecteert u de pagina **Definities**.
Zoek en selecteer vervolgens de ingebouwde definitie van het initiatief voor naleving van regelgeving in **CIS Microsoft Azure Benchmark 1.1.0**.

Dit ingebouwde initiatief wordt geïmplementeerd als onderdeel van het [blauwdrukvoorbeeld van CIS Microsoft Azure Foundations Benchmark](../../blueprints/samples/cis-azure-1-1-0.md).

> [!IMPORTANT]
> Elke beheeroptie hieronder is gekoppeld aan een of meer [Azure Policy](../overview.md)-definities.
> Met deze beleidsregels kunt u de [compliance beoordelen](../how-to/get-compliance-data.md) met de beheeroptie. Er is echter vaak geen één-op-één- of volledige overeenkomst tussen een beheeroptie en een of meer beleidsregels. Als zodanig verwijst de term **Conform** in Azure Policy alleen naar de beleidsdefinities zelf. Dit garandeert niet dat u volledig conform bent met alle vereisten van een beheeroptie. Daarnaast bevat de nalevingsstandaard beheeropties die op dit moment nog niet worden beschreven door Azure Policy-definities. Daarom is naleving in Azure Policy slechts een gedeeltelijke weergave van uw algemene nalevingsstatus. De koppelingen tussen de beheeropties voor nalevingsdomeinen en Azure Policy definities voor deze nalevingsstandaard kunnen na verloop van tijd veranderen. Als u de wijzigingsgeschiedenis wilt bekijken, raadpleegt u de [GitHub Commit-geschiedenis](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/CISv1_1_0_audit.json).

## <a name="identity-and-access-management"></a>Identiteits- en toegangsbeheer

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>Controleren of meervoudige verificatie is ingeschakeld voor alle bevoegde gebruikers

**Id**: **Eigendom** van CIS Azure 1.1: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[MFA moet zijn ingeschakeld voor accounts met schrijfmachtigingen voor uw abonnement](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |Schakel meervoudige verificatie (MFA) in voor alle abonnementsaccounts met schrijfmachtigingen om te voorkomen dat er inbreuk wordt gepleegd op accounts of resources. |AuditIfNotExists, uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[MFA moet zijn ingeschakeld voor accounts met eigenaarsmachtigingen voor uw abonnement](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |Schakel meervoudige verificatie (MFA) in voor alle abonnementsaccounts met eigenaarsmachtigingen om te voorkomen dat er inbreuk wordt gepleegd op accounts of resources. |AuditIfNotExists, uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>Controleren of meervoudige verificatie is ingeschakeld voor alle onbevoegde gebruikers

**Id**: **Eigendom** van CIS Azure 1.2: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[MFA moet zijn ingeschakeld voor accounts met leesmachtigingen voor uw abonnement](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |Schakel meervoudige verificatie (MFA) in voor alle abonnementsaccounts met leesmachtigingen om te voorkomen dat er inbreuk wordt gepleegd op accounts of resources. |AuditIfNotExists, uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="ensure-that-there-are-no-guest-users"></a>Zorg ervoor dat er geen gastgebruikers zijn

**Id**: **Eigendom** van CIS Azure 1.3: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Externe accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |Externe accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement om onbewaakte toegang te voorkomen. |AuditIfNotExists, uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |
|[Externe accounts met leesmachtigingen moeten worden verwijderd uit uw abonnement](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f76cf89-fbf2-47fd-a3f4-b891fa780b60) |Externe accounts met leesmachtigingen moeten worden verwijderd uit uw abonnement om onbewaakte toegang te voorkomen. |AuditIfNotExists, uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsReadPermissions_Audit.json) |
|[Externe accounts met schrijfmachtigingen moeten worden verwijderd uit uw abonnement](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c607a2e-c700-4744-8254-d77e7c9eb5e4) |Externe accounts met schrijfmachtigingen moeten worden verwijderd uit uw abonnement om onbewaakte toegang te voorkomen. |AuditIfNotExists, uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWritePermissions_Audit.json) |

### <a name="ensure-that-no-custom-subscription-owner-roles-are-created"></a>Controleren of er geen aangepaste rollen voor abonnementseigenaren zijn gemaakt

**Id**: **Eigendom** van CIS Azure 1.23: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Aangepaste rollen voor abonnementseigenaren mogen niet bestaan](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Dit beleid zorgt ervoor dat er geen aangepaste rollen voor abonnementseigenaren bestaan. |Controle, uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="security-center"></a>Security Center

### <a name="ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>Zorg dat 'Automatische inrichting van bewakingsagent' is ingesteld op 'ingeschakeld'

**Id**: **Eigendom** van CIS Azure 2.2: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Automatisch inrichten van de Log Analytics-agent voor controle moet zijn ingeschakeld voor uw abonnement](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Automatisch inrichten van de Log Analytics-agent voor controle inschakelen voor het verzamelen van beveiligingsgegevens |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

### <a name="ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>Controleren of de standaardbeleidsinstelling voor "Systeemupdates bewaken" van de ASC niet is "uitgeschakeld"

**Id**: **Eigendom** van CIS Azure 2.3: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Systeemupdates moeten op uw computers worden geïnstalleerd](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |Ontbrekende beveiligingssysteemupdates op uw servers worden als aanbevelingen bewaakt door Azure Security Center |AuditIfNotExists, uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>Controleren of de standaardbeleidsinstelling voor "Beveiligingsproblemen van besturingssystemen bewaken" van de ASC niet is "uitgeschakeld"

**Id**: **Eigendom** van CIS Azure 2.4: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Beveiligingsproblemen in de beveiligingsconfiguratie op uw computers moeten worden hersteld](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Servers die niet voldoen aan de geconfigureerde basislijn, worden als aanbevelingen bewaakt door Azure Security Center |AuditIfNotExists, uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>Zorg ervoor dat de standaardbeleidsinstelling voor "Endpoint Protection" van de ASC niet is "uitgeschakeld"

**Id**: **Eigendom** van CIS Azure 2.5: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Ontbrekende eindpuntbeveiliging bewaken in Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Servers zonder geïnstalleerde agent voor eindpuntbeveiliging worden als aanbevelingen bewaakt door Azure Security Center |AuditIfNotExists, uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>Controleren of de standaardbeleidsinstelling voor "Schijfversleuteling" van de ASC niet is "uitgeschakeld"

**Id**: **Eigendom** van CIS Azure 2.6: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Schijfversleuteling moet worden toegepast op virtuele machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Virtuele machines zonder ingeschakelde schijfversleuteling worden bewaakt via Azure Security Center als aanbevelingen. |AuditIfNotExists, uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-network-security-groups-is-not-disabled"></a>Zorg ervoor dat de standaardbeleidsinstelling voor "Netwerkbeveiligingsgroepen bewaken" van de ASC niet is "uitgeschakeld"

**Id**: **Eigendom** van CIS Azure 2.7: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Aanbevelingen voor Adaptieve netwerkbeveiliging moeten worden toegepast op virtuele machines die op internet zijn gericht](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center analyseert de verkeerspatronen van virtuele machines die vanaf het internet toegankelijk zijn, en formuleert aanbevelingen voor regels voor de netwerkbeveiligingsgroep die de kans op aanvallen beperken |AuditIfNotExists, uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |

### <a name="ensure-asc-default-policy-setting-enable-next-generation-firewallngfw-monitoring-is-not-disabled"></a>Controleren of de standaardbeleidsinstelling voor "NGFW-bewaking inschakelen" van de ASC is niet is "uitgeschakeld"

**Id**: **Eigendom** van CIS Azure 2.9: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Op internet gerichte virtuele machines moeten worden beveiligd met netwerkbeveiligingsgroepen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Bescherm uw virtuele machines tegen mogelijke bedreigingen door de toegang tot de VM te beperken met een netwerkbeveiligingsgroep (Network Security Group/NSG). U vindt meer informatie over het beheren van verkeer met NSG's op [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Subnets moeten worden gekoppeld aan een netwerkbeveiligingsgroep](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Bescherm uw subnet tegen mogelijke bedreigingen door de toegang te beperken met een netwerkbeveiligingsgroep (Network Security Group/NSG). NSG's bevatten een lijst met ACL-regels (Access Control List) waarmee netwerkverkeer naar uw subnet wordt toegestaan of geweigerd. |AuditIfNotExists, uitgeschakeld |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>Controleren of de standaardbeleidsinstelling voor "Beoordeling van beveiligingsproblemen" van de ASC niet is "uitgeschakeld"

**Id**: **Eigendom** van CIS Azure 2.10: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Beveiligingsproblemen moeten worden opgelost met een oplossing voor evaluatie van beveiligingsproblemen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F760a85ff-6162-42b3-8d70-698e268f648c) |Hiermee worden beveiligingsproblemen die door de oplossing voor evaluatie van beveiligingsproblemen zijn gedetecteerd en virtuele machines zonder oplossing voor evaluatie van beveiligingsproblemen bewaakt als aanbevelingen in Azure Security Center. |AuditIfNotExists, uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VMVulnerabilities_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>Zorg ervoor dat de standaardbeleidsinstelling voor "JIT-netwerktoegang bewaken" van de ASC niet is "uitgeschakeld"

**Id**: **Eigendom** van CIS Azure 2.12: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Beheerpoorten van virtuele machines moeten worden beveiligd met Just-In-Time-netwerktoegangsbeheer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Mogelijke Just In Time-netwerktoegang (JIT) wordt als aanbeveling bewaakt door Azure Security Center |AuditIfNotExists, uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-adaptive-application-whitelisting-is-not-disabled"></a>Controleren of de standaardbeleidsinstelling voor "Adaptieve whitelisting van toepassingen" van de ASC niet is "uitgeschakeld"

**Id**: **Eigendom** van CIS Azure 2.13: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Adaptieve toepassingsbesturingselementen voor het definiëren van veilige toepassingen moeten worden ingeschakeld op uw computers](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Schakel toepassingsregelaars in om de lijst te definiëren met bekende veilige toepassingen die worden uitgevoerd op uw machines en om een waarschuwing te ontvangen wanneer andere toepassingen worden uitgevoerd. Dit helpt uw computers te beschermen tegen schadelijke software. Om het proces van het configureren en onderhouden van uw regels te vereenvoudigen, gebruikt Security Center machine learning om de toepassingen te analyseren die op elke computer worden uitgevoerd en stelt de lijst met bekende veilige toepassingen voor. |AuditIfNotExists, uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-sql-auditing-is-not-disabled"></a>Controleren of de standaardbeleidsinstelling voor "Controleren voor SQL" van de ASC niet is "uitgeschakeld"

**Id**: **Eigendom** van CIS Azure 2.14: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle op SQL Server moet zijn ingeschakeld](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |Controle op uw SQL Server moet zijn ingeschakeld om database-activiteiten te volgen voor alle databases op de server, behalve Synapse, en deze op te slaan in een auditlogboek. |AuditIfNotExists, uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>Controleren of de standaardbeleidsinstelling voor "SQL-versleuteling bewaken" van de ASC niet is "uitgeschakeld"

**Id**: **Eigendom** van CIS Azure 2.15: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Transparent Data Encryption in SQL-databases moet zijn ingeschakeld](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Transparante gegevensversleuteling moet zijn ingeschakeld om data-at-rest te beveiligen en te voldoen aan de nalevingsvereisten |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="ensure-that-security-contact-emails-is-set"></a>Controleer of het ‘E-mailadres van de contactpersoon voor beveiliging’ is ingesteld

**Id**: **Eigendom** van CIS Azure 2.16: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[U moet een e-mailadres van de contactpersoon voor beveiliging opgeven voor uw abonnement](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f4f78b8-e367-4b10-a341-d9a4ad5cf1c7) |Een e-mailadres invoeren om meldingen te ontvangen wanneer Azure Security Center gecompromitteerde resources detecteert |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_email.json) |

### <a name="ensure-that-security-contact-phone-number-is-set"></a>Controleer of het ‘telefoonnummer’ van de contactpersoon voor beveiliging is ingesteld

**Id**: **Eigendom** van CIS Azure 2.17: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[U moet een telefoonnummer van de contactpersoon voor beveiliging opgeven voor uw abonnement](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4d66858-c922-44e3-9566-5cdb7a7be744) |Een telefoonnummer invoeren om meldingen te ontvangen wanneer Azure Security Center gecompromitteerde resources detecteert |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_phone_number.json) |

### <a name="ensure-that-send-email-notification-for-high-severity-alerts-is-set-to-on"></a>Zorg dat 'E-mailmelding voor waarschuwingen met hoge urgentie verzenden' is ingesteld op 'ingeschakeld'

**Id**: **Eigendom** van CIS Azure 2.18: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[E-mailmelding voor waarschuwingen met hoge urgentie moet zijn ingeschakeld](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e2593d9-add6-4083-9c9b-4b7d2188c899) |Schakel e-mailmeldingen naar de beveiligingscontactpersoon in, zodat deze beveiligingswaarschuwingen van Microsoft ontvangt. Dit zorgt ervoor dat de juiste mensen op de hoogte zijn van mogelijke beveiligingsproblemen en dat ze de risico's kunnen beperken |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification.json) |

### <a name="ensure-that-send-email-also-to-subscription-owners-is-set-to-on"></a>Controleren of 'E-mail ook verzenden naar eigenaars van het abonnement' is ingesteld op 'ingeschakeld'

**Id**: **Eigendom** van CIS Azure 2.19: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[E-mailmelding aan abonnementseigenaar voor waarschuwingen met hoge urgentie moet zijn ingeschakeld](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b15565f-aa9e-48ba-8619-45960f2c314d) |Schakel e-mailmeldingen naar de eigenaar van het abonnement in, zodat deze beveiligingswaarschuwingen van Microsoft ontvangt. Dit zorgt ervoor dat hij op de hoogte is van mogelijke beveiligingsproblemen en het risico op tijd kan beperken |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification_to_subscription_owner.json) |

## <a name="storage-accounts"></a>Opslagaccounts

### <a name="ensure-that-secure-transfer-required-is-set-to-enabled"></a>Controleren of 'beveiligde overdracht vereist' is ingesteld op 'ingeschakeld'

**Id**: **Eigendom** van CIS Azure 3.1: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Beveiligde overdracht naar opslagaccounts moet zijn ingeschakeld](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Controleer de vereiste van beveiligde overdracht in uw opslagaccount. Beveiligde overdracht is een optie die afdwingt dat uw opslagaccount alleen aanvragen van beveiligde verbindingen (HTTPS) accepteert. Het gebruik van HTTPS zorgt voor verificatie tussen de server en de service en beveiligt gegevens tijdens de overdracht tegen netwerklaagaanvallen, zoals man-in-the-middle, meeluisteren en sessie-hijacking |Controleren, Weigeren, Uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>Controleren of de standaardregel voor netwerktoegang voor opslagaccounts is ingesteld op weigeren

**Id**: **Eigendom** van CIS Azure 3.7: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Netwerktoegang tot opslagaccounts moet zijn beperkt](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |Netwerktoegang tot opslagaccounts moet worden beperkt. Configureer netwerkregels zo dat alleen toepassingen van toegestane netwerken toegang hebben tot het opslagaccount. Om verbindingen van specifieke internet- of lokale clients toe te staan, kan toegang worden verleend aan verkeer van specifieke Azure Virtual Networks of aan openbare IP-adresbereiken voor internet |Controleren, Weigeren, Uitgeschakeld |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

### <a name="ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>Controleren of vertrouwde Microsoft-services zijn ingeschakeld voor toegang tot het opslagaccount

**Id**: **Eigendom** van CIS Azure 3.8: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Opslagaccounts moeten toegang uit vertrouwde Microsoft-services toestaan](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9d007d0-c057-4772-b18c-01e546713bcd) |Sommige Microsoft-services die communiceren met opslagaccounts, werken vanuit netwerken waaraan geen toegang kan worden verleend via netwerkregels. Om dit type service goed te laten werken, moet u de set vertrouwde Microsoft-services toestaan om de netwerkregels over te slaan. Deze services gebruiken vervolgens sterke verificatie om toegang te krijgen tot het opslagaccount. |Controleren, Weigeren, Uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccess_TrustedMicrosoftServices_Audit.json) |

## <a name="database-services"></a>Databaseservices

### <a name="ensure-that-auditing-is-set-to-on"></a>Controleren of 'Controle' is ingesteld op 'Aan'

**Id**: **Eigendom** van CIS Azure 4.1: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controle op SQL Server moet zijn ingeschakeld](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |Controle op uw SQL Server moet zijn ingeschakeld om database-activiteiten te volgen voor alle databases op de server, behalve Synapse, en deze op te slaan in een auditlogboek. |AuditIfNotExists, uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |

### <a name="ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>Controleren of het beleid 'AuditActionGroups ' in 'Controle' voor een SQL-server correct is ingesteld

**Id**: **Eigendom** van CIS Azure 4.2: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[In de instellingen van SQL-controle moeten actiegroepen zijn geconfigureerd om kritieke activiteiten te kunnen vastleggen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ff426e2-515f-405a-91c8-4f2333442eb5) |De eigenschap AuditActionsAndGroups moet ten minste SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP FAILED_DATABASE_AUTHENTICATION_GROUP, BATCH_COMPLETED_GROUP bevatten om een grondige logboekregistratie van de controle te garanderen |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_ActionsAndGroups_Audit.json) |

### <a name="ensure-that-auditing-retention-is-greater-than-90-days"></a>Controleren of de bewaarperiode van de 'Controle' 'groter is dan 90 dagen'

**Id**: **Eigendom** van CIS Azure 4.3: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL-servers moeten worden geconfigureerd met een bewaarperiode voor controleren van meer dan 90 dagen.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |Controle van SQL-servers die zijn geconfigureerd met een bewaarperiode voor controleren van minder dan 90 dagen. |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |

### <a name="ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>Controleren of 'Advanced Data Security' op een SQL-server is ingesteld op 'Aan'

**Id**: **Eigendom** van CIS Azure 4.4: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Advanced Data Security moet zijn ingeschakeld voor SQL Managed Instance](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Controleer elke SQL Managed Instance zonder Advanced Data Security. |AuditIfNotExists, uitgeschakeld |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[Advanced Data Security moet zijn ingeschakeld op uw SQL-servers](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |SQL-servers zonder Advanced Data Security controleren |AuditIfNotExists, uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |

### <a name="ensure-that-azure-active-directory-admin-is-configured"></a>Controleren of Azure Active Directory-beheerder is geconfigureerd

**Id**: **Eigendom** van CIS Azure 4.8: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Een Azure Active Directory-beheerder moet worden ingericht voor SQL-servers](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |Controleer inrichting van een Azure Active Directory-beheerder voor uw SQL-Server om Azure AD-verificatie in te schakelen. Azure AD-verificatie maakt vereenvoudigd beheer van machtigingen en gecentraliseerd identiteitsbeheer van databasegebruikers en andere Microsoft-services mogelijk |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |

### <a name="ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>Controleren of 'gegevensversleuteling' is ingesteld op 'ingeschakeld' op een SQL Database

**Id**: **Eigendom** van CIS Azure 4.9: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Transparent Data Encryption in SQL-databases moet zijn ingeschakeld](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Transparante gegevensversleuteling moet zijn ingeschakeld om data-at-rest te beveiligen en te voldoen aan de nalevingsvereisten |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>Controleren of de TDE-beveiliging van SQL-server is versleuteld met BYOK (Uw eigen sleutel gebruiken)

**Id**: **Eigendom** van CIS Azure 4.10: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[TDE-beveiliging van SQL Managed Instance moet worden versleuteld met uw eigen sleutel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |Transparent Data Encryption (TDE) met ondersteuning van uw eigen sleutels voorziet in verbeterde transparantie en controle voor TDE-beveiliging, verbeterde beveiliging via een externe service met HSM en bevordering van scheiding van taken. |AuditIfNotExists, uitgeschakeld |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[TDE-beveiliging van de SQL-server moet worden versleuteld met uw eigen sleutel](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |Transparent Data Encryption (TDE) met ondersteuning van uw eigen sleutels voorziet in verbeterde transparantie en controle voor TDE-beveiliging, verbeterde beveiliging via een externe service met HSM en bevordering van scheiding van taken. |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>Controleren of ‘SSL-verbinding afdwingen’ is ingesteld op "INGESCHAKELD" voor MySQL-databaseserver

**Id**: **Eigendom** van CIS Azure 4.11: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SSL-verbinding afdwingen moet worden ingeschakeld voor MySQL-databaseservers](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL biedt ondersteuning voor het gebruik van Secure Sockets Layer (SSL) om uw Azure Database for MySQL-server te verbinden met clienttoepassingen. Het afdwingen van SSL-verbindingen tussen uw databaseserver en clienttoepassingen zorgt dat u bent beschermt tegen 'man in the middle'-aanvallen omdat de gegevensstroom tussen de server en uw toepassing wordt versleuteld. Deze configuratie dwingt af dat SSL altijd is ingeschakeld voor toegang tot uw databaseserver. |Controle, uitgeschakeld |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="ensure-server-parameter-log_checkpoints-is-set-to-on-for-postgresql-database-server"></a>Controleren of de serverparameter ‘log_checkpoints’ is ingesteld op AAN voor PostgreSQL-databaseserver

**Id**: **Eigendom** van CIS Azure 4.12: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Logboekcontrolepunten moeten zijn ingeschakeld voor PostgreSQL-databaseservers](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e43d) |Dit beleid helpt bij het controleren van alle PostgreSQL-databases in uw omgeving zonder dat er log_checkpoints hoeven te worden ingeschakeld. |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogCheckpoint_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>Controleren of ‘SSL-verbinding afdwingen’ is ingesteld op ‘INGESCHAKELD’ voor PostgreSQL-databaseservers

**Id**: **Eigendom** van CIS Azure 4.13: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SSL-verbinding afdwingen moet worden ingeschakeld voor PostgreSQL-databaseservers](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL biedt ondersteuning voor het gebruik van Secure Sockets Layer (SSL) om uw Azure Database for PostgreSQL-server te verbinden met clienttoepassingen. Het afdwingen van SSL-verbindingen tussen uw databaseserver en clienttoepassingen zorgt dat u bent beschermt tegen 'man in the middle'-aanvallen omdat de gegevensstroom tussen de server en uw toepassing wordt versleuteld. Deze configuratie dwingt af dat SSL altijd is ingeschakeld voor toegang tot uw databaseserver. |Controle, uitgeschakeld |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="ensure-server-parameter-log_connections-is-set-to-on-for-postgresql-database-server"></a>Controleren of de serverparameter ‘log_connections’ is ingesteld op ‘AAN’ voor PostgreSQL-databaseserver

**Id**: **Eigendom** van CIS Azure 4.14: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Logboekverbindingen moeten zijn ingeschakeld voor PostgreSQL-databaseservers](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e442) |Dit beleid helpt bij het controleren van alle PostgreSQL-databases in uw omgeving zonder dat er log_connections hoeven te worden ingeschakeld. |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogConnections_Audit.json) |

### <a name="ensure-server-parameter-log_disconnections-is-set-to-on-for-postgresql-database-server"></a>Controleren of dat de serverparameter ‘log_disconnections’ is ingesteld op ‘AAN’ voor PostgreSQL-databaseserver

**Id**: **Eigendom** van CIS Azure 4.15: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Verbroken verbindingen moeten in een logboek worden vastgelegd voor PostgreSQL-databaseservers.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e446) |Dit beleid helpt bij het controleren van alle PostgreSQL-databases in uw omgeving zonder dat er log_disconnections hoeven te worden ingeschakeld. |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogDisconnections_Audit.json) |

### <a name="ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>Controleren of de serverparameter ‘connection_throttling’ is ingesteld op ‘AAN" voor PostgreSQL-databaseserver

**Id**: **Eigendom** van CIS Azure 4.17: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Verbinding beperken moet worden ingeschakeld voor PostgreSQL-databaseservers](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5345bb39-67dc-4960-a1bf-427e16b9a0bd) |Dit beleid helpt bij het controleren van alle PostgreSQL-databases in uw omgeving zonder dat er verbindingsbeperking hoeft te worden ingeschakeld. Met deze instelling schakelt u tijdelijke beperking van de verbinding per IP in voor te veel ongeldige wachtwoordaanmeldingen. |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_ConnectionThrottling_Enabled_Audit.json) |

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

### <a name="ensure-that-a-log-profile-exists"></a>Controleren of er een logboekprofiel bestaat

**Id**: **Eigendom** van CIS Azure 5.1.1: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure-abonnementen moeten een logboekprofiel voor het activiteitenlogboek hebben](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7796937f-307b-4598-941c-67d3a05ebfe7) |Dit beleid zorgt ervoor dat een logboekprofiel is ingeschakeld voor het exporteren van activiteitenlogboeken. Het controleert of er een logboekprofiel is gemaakt om de logboeken te exporteren naar een opslagaccount of een Event Hub. |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Logprofile_activityLogs_Audit.json) |

### <a name="ensure-that-activity-log-retention-is-set-365-days-or-greater"></a>Controleren of de bewaarperiode van het activiteitenlogboek op 365 dagen of groter is ingesteld

**Id**: **Eigendom** van CIS Azure 5.1.2: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Het activiteitenlogboek moet ten minste één jaar worden bewaard](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb02aacc0-b073-424e-8298-42b22829ee0a) |Met dit beleid wordt het activiteitenlogboek gecontroleerd als de bewaarperiode niet is ingesteld op 365 dagen of permanent (bewaarperiode ingesteld op 0). |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLogRetention_365orGreater.json) |

### <a name="ensure-audit-profile-captures-all-the-activities"></a>Controleren of het controleprofiel alle activiteiten vastlegt

**Id**: **Eigendom** van CIS Azure 5.1.3: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Met het Azure Monitor-logboekprofiel moeten logboeken worden verzameld voor de categorieën 'schrijven', 'verwijderen' en 'actie'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |Met dit beleid wordt ervoor gezorgd dat in een logboekprofiel logboeken worden verzameld voor de categorieën 'schrijven', 'verwijderen' en 'actie' |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |

### <a name="ensure-the-log-profile-captures-activity-logs-for-all-regions-including-global"></a>Zorg ervoor dat het logboekprofiel activiteitenlogboeken vastlegt voor alle regio's, waaronder globaal

**Id**: **Eigendom** van CIS Azure 5.1.4: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Monitor moet activiteitenlogboeken uit alle regio's verzamelen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |Met dit beleid wordt het Azure Monitor-logboekprofiel gecontroleerd waarbij geen activiteiten worden geëxporteerd uit alle door Azure ondersteunde regio's, inclusief wereldwijd. |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |

### <a name="ensure-the-storage-account-containing-the-container-with-activity-logs-is-encrypted-with-byok-use-your-own-key"></a>Controleren of het opslagaccount met de container met activiteitenlogboeken is versleuteld met BYOK (Uw eigen sleutel gebruiken)

**Id**: **Eigendom** van CIS Azure 5.1.6: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Het opslagaccount met de container met activiteitenlogboeken moet worden versleuteld met BYOK](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffbb99e8e-e444-4da0-9ff1-75c92f5a85b2) |Dit beleid controleert of het opslagaccount met de container met activiteitenlogboeken is versleuteld met BYOK. Het beleid werkt alleen als het opslagaccount is gebaseerd op hetzelfde abonnement als voor activiteitenlogboeken. Meer informatie over Azure Storage-versleuteling in rust vindt u hier [https://aka.ms/azurestoragebyok](https://aka.ms/azurestoragebyok).  |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_StorageAccountBYOK_Audit.json) |

### <a name="ensure-that-logging-for-azure-keyvault-is-enabled"></a>Controleren of logboekregistratie van Azure-sleutelkluis is 'ingeschakeld'

**Id**: **Eigendom** van CIS Azure 5.1.7: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Diagnostische logboeken in Key Vault moeten zijn ingeschakeld](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |Inschakeling van diagnostische logboeken controleren. Hiermee kunt u een activiteitenspoor opnieuw maken om te gebruiken voor onderzoeksdoeleinden wanneer een beveiligingsincident optreedt of wanneer uw netwerk is aangetast |AuditIfNotExists, uitgeschakeld |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-policy-assignment"></a>Controleren of er een waarschuwing voor een activiteitenlogboek bestaat voor het maken van beleidstoewijzing

**Id**: **Eigendom** van CIS Azure 5.2.1: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Er moet een waarschuwing voor activiteitenlogboeken bestaan voor specifieke beleidsbewerkingen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5447c04-a4d7-4ba8-a263-c9ee321a6858) |Met dit beleid worden specifieke beleidsbewerkingen gecontroleerd waarvoor geen waarschuwingen voor activiteitenlogboeken zijn geconfigureerd. |AuditIfNotExists, uitgeschakeld |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_PolicyOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group"></a>Controleren of er een waarschuwing voor activiteitenlogboek bestaat voor het maken of bijwerken van een netwerkbeveiligingsgroep

**Id**: **Eigendom** van CIS Azure 5.2.2: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Er moet een waarschuwing voor activiteitenlogboeken bestaan voor specifieke beheerbewerkingen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Met dit beleid worden specifieke beheerbewerkingen gecontroleerd waarvoor geen waarschuwingen voor activiteitenlogboeken zijn geconfigureerd. |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-network-security-group"></a>Controleren of er een waarschuwing voor activiteitenlogboek bestaat voor het verwijderen van een netwerkbeveiligingsgroep

**Id**: **Eigendom** van CIS Azure 5.2.3: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Er moet een waarschuwing voor activiteitenlogboeken bestaan voor specifieke beheerbewerkingen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Met dit beleid worden specifieke beheerbewerkingen gecontroleerd waarvoor geen waarschuwingen voor activiteitenlogboeken zijn geconfigureerd. |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group-rule"></a>Controleren of er een waarschuwing voor activiteitenlogboek bestaat voor het maken of bijwerken van een regel voor netwerkbeveiligingsgroep

**Id**: **Eigendom** van CIS Azure 5.2.4: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Er moet een waarschuwing voor activiteitenlogboeken bestaan voor specifieke beheerbewerkingen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Met dit beleid worden specifieke beheerbewerkingen gecontroleerd waarvoor geen waarschuwingen voor activiteitenlogboeken zijn geconfigureerd. |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-the-delete-network-security-group-rule"></a>Controleren of er een waarschuwing voor activiteitenlogboek bestaat voor het verwijderen van de regel voor de netwerkbeveiligingsgroep

**Id**: **Eigendom** van CIS Azure 5.2.5: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Er moet een waarschuwing voor activiteitenlogboeken bestaan voor specifieke beheerbewerkingen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Met dit beleid worden specifieke beheerbewerkingen gecontroleerd waarvoor geen waarschuwingen voor activiteitenlogboeken zijn geconfigureerd. |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-security-solution"></a>Controleren of er een waarschuwing voor activiteitenlogboek bestaat voor het maken of bijwerken van een beveiligingsoplossing

**Id**: **Eigendom** van CIS Azure 5.2.6: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Er moet een waarschuwing voor activiteitenlogboeken bestaan voor specifieke beveiligingsbewerkingen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Met dit beleid worden specifieke beveiligingsbewerkingen gecontroleerd waarvoor geen waarschuwingen voor activiteitenlogboeken zijn geconfigureerd. |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-security-solution"></a>Controleren of er een waarschuwing voor activiteitenlogboek bestaat voor het verwijderen van de beveiligingsoplossing

**Id**: **Eigendom** van CIS Azure 5.2.7: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Er moet een waarschuwing voor activiteitenlogboeken bestaan voor specifieke beveiligingsbewerkingen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Met dit beleid worden specifieke beveiligingsbewerkingen gecontroleerd waarvoor geen waarschuwingen voor activiteitenlogboeken zijn geconfigureerd. |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-or-delete-sql-server-firewall-rule"></a>Controleren of er een waarschuwing voor activiteitenlogboek bestaat voor het maken of bijwerken van Firewallregels voor SQL-server

**Id**: **Eigendom** van CIS Azure 5.2.8: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Er moet een waarschuwing voor activiteitenlogboeken bestaan voor specifieke beheerbewerkingen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Met dit beleid worden specifieke beheerbewerkingen gecontroleerd waarvoor geen waarschuwingen voor activiteitenlogboeken zijn geconfigureerd. |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-update-security-policy"></a>Controleren of er een waarschuwing voor activiteitenlogboek bestaat voor het bijwerken van het beveiligingsbeleid

**Id**: **Eigendom** van CIS Azure 5.2.9: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Er moet een waarschuwing voor activiteitenlogboeken bestaan voor specifieke beveiligingsbewerkingen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Met dit beleid worden specifieke beveiligingsbewerkingen gecontroleerd waarvoor geen waarschuwingen voor activiteitenlogboeken zijn geconfigureerd. |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

## <a name="networking"></a>Netwerken

### <a name="ensure-that-rdp-access-is-restricted-from-the-internet"></a>Controleren of de RDP-toegang is afgescheiden van het internet

**Id**: **Eigendom** van CIS Azure 6.1: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[RDP-toegang via internet moet worden geblokkeerd](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe372f825-a257-4fb8-9175-797a8a8627d6) |Met dit beleid wordt elke netwerkbeveiligingsregel gecontroleerd die RDP-toegang via internet toestaat |Controle, uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_RDPAccess_Audit.json) |

### <a name="ensure-that-ssh-access-is-restricted-from-the-internet"></a>Controleren of SSH-toegang is afgescheiden van het internet

**Id**: **Eigendom** van CIS Azure 6.2: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SSH-toegang via internet moet worden geblokkeerd](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fab) |Met dit beleid wordt elke netwerkbeveiligingsregel gecontroleerd die SSH-toegang via internet toestaat |Controle, uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_SSHAccess_Audit.json) |

### <a name="ensure-that-network-watcher-is-enabled"></a>Controleren of Network Watcher is 'ingeschakeld'

**Id**: **Eigendom** van CIS Azure 6.5: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Network Watcher moet zijn ingeschakeld](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher is een regionale service waarmee u voorwaarden op het niveau van netwerkscenario's in, naar en vanaf Azure kunt controleren en onderzoeken. Via controle op het scenarioniveau kunt u problemen analyseren met behulp van een weergave op het niveau van een end-to-end netwerk. U kunt de beschikbare diagnostische en visualisatiehulpprogramma's voor netwerken in Network Watcher gebruiken om uw netwerk in Azure te begrijpen, te analyseren en inzichten voor uw netwerk te verkrijgen. |auditIfNotExists |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="virtual-machines"></a>Virtuele machines

### <a name="ensure-that-os-disk-are-encrypted"></a>Controleren of de ‘besturingssysteemschijf’ is versleuteld

**Id**: **Eigendom** van CIS Azure 7.1: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Schijfversleuteling moet worden toegepast op virtuele machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Virtuele machines zonder ingeschakelde schijfversleuteling worden bewaakt via Azure Security Center als aanbevelingen. |AuditIfNotExists, uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-that-data-disks-are-encrypted"></a>Controleer of 'gegevensschijven' zijn versleuteld

**Id**: **Eigendom** van CIS Azure 7.2: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Schijfversleuteling moet worden toegepast op virtuele machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Virtuele machines zonder ingeschakelde schijfversleuteling worden bewaakt via Azure Security Center als aanbevelingen. |AuditIfNotExists, uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-that-unattached-disks-are-encrypted"></a>Controleer of 'niet-gekoppelde schijven' zijn versleuteld

**Id**: **Eigendom** van CIS Azure 7.3: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Niet-gekoppelde schijven moeten worden versleuteld](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |Met dit beleid wordt elke niet-gekoppelde schijf gecontroleerd waarvoor geen versleuteling is ingeschakeld. |Controle, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |

### <a name="ensure-that-only-approved-extensions-are-installed"></a>Controleren of alleen goedgekeurde extensies zijn geïnstalleerd

**Id**: **Eigendom** van CIS Azure 7.4: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Alleen goedgekeurde VM-extensies mogen worden geïnstalleerd](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc0e996f8-39cf-4af9-9f45-83fbde810432) |Dit beleid is van toepassing op extensies van virtuele machines die niet zijn goedgekeurd. |Controleren, Weigeren, Uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_ApprovedExtensions_Audit.json) |

### <a name="ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>Controleren of de meest recente besturingssysteempatches voor alle virtuele machines zijn toegepast

**Id**: **Eigendom** van CIS Azure 7.5: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Systeemupdates moeten op uw computers worden geïnstalleerd](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |Ontbrekende beveiligingssysteemupdates op uw servers worden als aanbevelingen bewaakt door Azure Security Center |AuditIfNotExists, uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

### <a name="ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>Controleer of Endpoint Protection voor alle virtuele machines is geïnstalleerd

**Id**: **Eigendom** van CIS Azure 7.6: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Ontbrekende eindpuntbeveiliging bewaken in Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Servers zonder geïnstalleerde agent voor eindpuntbeveiliging worden als aanbevelingen bewaakt door Azure Security Center |AuditIfNotExists, uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

## <a name="other-security-considerations"></a>Andere beveiligingsoverwegingen

### <a name="ensure-the-key-vault-is-recoverable"></a>Zorg ervoor dat de sleutelkluis kan worden hersteld

**Id**: **Eigendom** van CIS Azure 8.4: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Beveiliging tegen leegmaken moet zijn ingeschakeld voor de sleutelkluis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |Kwaadwillende verwijdering van een sleutelkluis kan leiden tot permanent gegevensverlies. Een kwaadwillende insider in uw organisatie kan toegang krijgen en wellicht sleutelkluizen verwijderen en leegmaken. Beveiliging tegen leegmaken beschermt u tegen aanvallen van insiders door een verplichte bewaarperiode tijdens voorlopige verwijdering af te dwingen voor sleutelkluizen. Gedurende de periode van voorlopige verwijdering kan niemand binnen uw organisatie of Microsoft uw sleutelkluizen leegmaken. |Controleren, Weigeren, Uitgeschakeld |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

### <a name="enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>Op rollen gebaseerd toegangsbeheer (RBAC) inschakelen in Azure Kubernetes Services

**Id**: **Eigendom** van CIS Azure 8.5: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Op rollen gebaseerd toegangsbeheer (RBAC) moet worden gebruikt voor Kubernetes Services](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |Gebruik op rollen gebaseerd toegangsbeheer (RBAC) om de machtigingen in Kubernetes Service-clusters te beheren en het relevante autorisatiebeleid te configureren om gedetailleerde filters te bieden voor de acties die gebruikers kunnen uitvoeren. |Controle, uitgeschakeld |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

## <a name="appservice"></a>AppService

### <a name="ensure-app-service-authentication-is-set-on-azure-app-service"></a>Controleren of App Service-verificatie is ingesteld op Azure App Service

**Id**: **Eigendom** van CIS Azure 9.1: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Verificatie moet zijn ingeschakeld in uw API-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4ebc54a-46e1-481a-bee2-d4411e95d828) |Azure App Service-verificatie is een functie waarmee wordt voorkomen dat anonieme HTTP-aanvragen de API-app bereiken of waarmee aanvragen met een token worden geverifieerd voordat ze de API-app bereiken |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_ApiApp_Audit.json) |
|[Verificatie moet zijn ingeschakeld in uw Function-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc75248c1-ea1d-4a9c-8fc9-29a6aabd5da8) |Azure App Service-verificatie is een functie waarmee wordt voorkomen dat anonieme HTTP-aanvragen de Function-app bereiken of waarmee aanvragen met een token worden geverifieerd voordat ze de Function-app bereiken |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_functionapp_Audit.json) |
|[Verificatie moet zijn ingeschakeld in uw web-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95bccee9-a7f8-4bec-9ee9-62c3473701fc) |Azure App Service-verificatie is een functie waarmee wordt voorkomen dat anonieme HTTP-aanvragen de web-app bereiken of waarmee aanvragen met een token worden geverifieerd voordat ze de web-app bereiken |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_WebApp_Audit.json) |

### <a name="ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>Controleren of de web-apps alle HTTP-verkeer omleidt naar HTTPS in Azure App Service

**Id**: **Eigendom** van CIS Azure 9.2: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Webtoepassing mag alleen toegankelijk zijn via HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |Door HTTPS te gebruiken, weet u zeker dat server-/serviceverificatie wordt uitgevoerd en dat uw gegevens tijdens de overdracht zijn beschermd tegen aanvallen die meeluisteren in de netwerklaag. |Controle, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="ensure-web-app-is-using-the-latest-version-of-tls-encryption"></a>Controleren of voor de web-app de nieuwste versie van TLS-versleuteling wordt gebruikt

**Id**: **Eigendom** van CIS Azure 9.3: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[De nieuwste TLS-versie moet worden gebruikt in uw API-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Een upgrade uitvoeren uit naar de meest recente TLS-versie |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[De nieuwste TLS-versie moet worden gebruikt in uw Function-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Een upgrade uitvoeren uit naar de meest recente TLS-versie |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[De nieuwste TLS-versie moet worden gebruikt in uw web-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Een upgrade uitvoeren uit naar de meest recente TLS-versie |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |

### <a name="ensure-the-web-app-has-client-certificates-incoming-client-certificates-set-to-on"></a>Controleren of de web-app 'Clientcertificaten' (inkomende clientcertificaten) heeft ingesteld op 'Aan'

**Id**: **Eigendom** van CIS Azure 9.4: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controleren of de API-app 'Clientcertificaten' (inkomende client-certificaten) heeft ingesteld op 'Aan'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0c192fe8-9cbb-4516-85b3-0ade8bd03886) |Met clientcertificaten kan de app een certificaat aanvragen voor binnenkomende aanvragen. Alleen clients die een geldig certificaat hebben, kunnen de app bereiken. |Controle, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_ClientCert.json) |
|[Controleren of de Function-app 'Clientcertificaten (inkomende clientcertificaten)' heeft ingesteld op 'Aan'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feaebaea7-8013-4ceb-9d14-7eb32271373c) |Met clientcertificaten kan de app een certificaat aanvragen voor binnenkomende aanvragen. Alleen clients die een geldig certificaat hebben, kunnen de app bereiken. |Controle, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_ClientCert.json) |
|[Controleren of de web-app 'Clientcertificaten (inkomende clientcertificaten)' heeft ingesteld op 'Aan'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |Met clientcertificaten kan de app een certificaat aanvragen voor binnenkomende aanvragen. Alleen clients die een geldig certificaat hebben, kunnen de app bereiken. |Controle, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |

### <a name="ensure-that-register-with-azure-active-directory-is-enabled-on-app-service"></a>Controleren of registratie in Azure Active Directory is ingeschakeld voor de App Service

**Id**: **Eigendom** van CIS Azure 9.5: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Er moet een beheerde identiteit worden gebruikt in uw API-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4d441f8-f9d9-4a9e-9cef-e82117cb3eef) |Een beheerde identiteit gebruiken voor verbeterde verificatiebeveiliging |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_ApiApp_Audit.json) |
|[Er moet een beheerde identiteit worden gebruikt in uw Function-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0da106f2-4ca3-48e8-bc85-c638fe6aea8f) |Een beheerde identiteit gebruiken voor verbeterde verificatiebeveiliging |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_FunctionApp_Audit.json) |
|[Er moet een beheerde identiteit worden gebruikt in uw web-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b9ad585-36bc-4615-b300-fd4435808332) |Een beheerde identiteit gebruiken voor verbeterde verificatiebeveiliging |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_WebApp_Audit.json) |

### <a name="ensure-that-php-version-is-the-latest-if-used-to-run-the-web-app"></a>Controleren of de ‘PHP-versie’ de meest recente is, als deze wordt gebruikt om de web-app te openen

**Id**: **Eigendom** van CIS Azure 9.7: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controleren of de PHP-versie de meest recente is als deze wordt gebruikt als onderdeel van de API-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1bc1795e-d44a-4d48-9b3b-6fff0fd5f9ba) |Er worden regelmatig nieuwere versies uitgebracht voor PHP-software, ofwel vanwege de beveiligingsfouten ofwel om extra functionaliteit toe te voegen. Het gebruik van de nieuwste PHP-versie voor API-apps wordt aanbevolen om te profiteren van beveiligingsoplossingen, indien van toepassing, en/of nieuwe functies van de nieuwste versie. Dit beleid is momenteel alleen van toepassing op Linux-web-apps. |AuditIfNotExists, uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_PHP_Latest.json) |
|[Controleren of de PHP-versie de meest recente is, als deze wordt gebruikt als onderdeel van de web-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7261b898-8a84-4db8-9e04-18527132abb3) |Er worden regelmatig nieuwere versies uitgebracht voor PHP-software, ofwel vanwege de beveiligingsfouten ofwel om extra functionaliteit toe te voegen. Het gebruik van de nieuwste PHP-versie voor web-apps wordt aanbevolen om te profiteren van beveiligingsoplossingen, indien van toepassing, en/of nieuwe functies van de nieuwste versie. Dit beleid is momenteel alleen van toepassing op Linux-web-apps. |AuditIfNotExists, uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_PHP_Latest.json) |

### <a name="ensure-that-python-version-is-the-latest-if-used-to-run-the-web-app"></a>Controleren of de ‘Python-versie’ de meest recente is, als deze wordt gebruikt om de web-app te openen

**Id**: **Eigendom** van CIS Azure 9.8: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controleren of de Python-versie de meest recente is als deze wordt gebruikt als onderdeel van de API-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74c3584d-afae-46f7-a20a-6f8adba71a16) |Er worden regelmatig nieuwere versies uitgebracht voor Python-software, ofwel vanwege de beveiligingsfouten ofwel om extra functionaliteit toe te voegen. Het gebruik van de nieuwste Python-versie voor API-apps wordt aanbevolen om te kunnen profiteren van beveiligingsoplossingen (indien van toepassing) en/of nieuwe functies van de nieuwste versie. Dit beleid is momenteel alleen van toepassing op Linux-web-apps. |AuditIfNotExists, uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_python_Latest.json) |
|[Controleren of de Python-versie de meest recente is, als deze wordt gebruikt als onderdeel van de Function-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7238174a-fd10-4ef0-817e-fc820a951d73) |Er worden regelmatig nieuwere versies uitgebracht voor Python-software, ofwel vanwege de beveiligingsfouten ofwel om extra functionaliteit toe te voegen. Het gebruik van de nieuwste Python-versie voor Function-apps wordt aanbevolen om te profiteren van beveiligingsoplossingen, indien van toepassing, en/of nieuwe functies van de nieuwste versie. Dit beleid is momenteel alleen van toepassing op Linux-web-apps. |AuditIfNotExists, uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_python_Latest.json) |
|[Controleren of de Python-versie de meest recente is, als deze wordt gebruikt als onderdeel van de web-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7008174a-fd10-4ef0-817e-fc820a951d73) |Er worden regelmatig nieuwere versies uitgebracht voor Python-software, ofwel vanwege de beveiligingsfouten ofwel om extra functionaliteit toe te voegen. Het gebruik van de nieuwste Python-versie voor web-apps wordt aanbevolen om te profiteren van beveiligingsoplossingen, indien van toepassing, en/of nieuwe functies van de nieuwste versie. Dit beleid is momenteel alleen van toepassing op Linux-web-apps. |AuditIfNotExists, uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_python_Latest.json) |

### <a name="ensure-that-java-version-is-the-latest-if-used-to-run-the-web-app"></a>Controleren of de ‘Java-versie’ de meest recente is, als deze wordt gebruikt om de web-app te openen

**Id**: **Eigendom** van CIS Azure 9.9: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controleren of de Java-versie de meest recente is als deze wordt gebruikt als onderdeel van de API-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88999f4c-376a-45c8-bcb3-4058f713cf39) |Er worden regelmatig nieuwere versies uitgebracht voor Java, ofwel vanwege de beveiligingsfouten ofwel om extra functionaliteit toe te voegen. Het gebruik van de nieuwste Python-versie voor API-apps wordt aanbevolen om te kunnen profiteren van beveiligingsoplossingen (indien van toepassing) en/of nieuwe functies van de nieuwste versie. Dit beleid is momenteel alleen van toepassing op Linux-web-apps. |AuditIfNotExists, uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_java_Latest.json) |
|[Controleren of de nieuwste versie van Java wordt gebruikt als onderdeel van de Function-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d0b6ea4-93e2-4578-bf2f-6bb17d22b4bc) |Er worden regelmatig nieuwere versies uitgebracht voor Java-software, ofwel vanwege de beveiligingsfouten ofwel om extra functionaliteit toe te voegen. Het gebruik van de nieuwste Java-versie voor Function-apps wordt aanbevolen om te profiteren van beveiligingsfixes, indien van toepassing, en/of nieuwe functies van de nieuwste versie. Dit beleid is momenteel alleen van toepassing op Linux-web-apps. |AuditIfNotExists, uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_java_Latest.json) |
|[Controleren of de Java-versie de meest recente is, als deze wordt gebruikt als onderdeel van de web-app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F496223c3-ad65-4ecd-878a-bae78737e9ed) |Er worden regelmatig nieuwere versies uitgebracht voor Java-software, ofwel vanwege de beveiligingsfouten ofwel om extra functionaliteit toe te voegen. Het gebruik van de nieuwste Java-versie voor web-apps wordt aanbevolen om te profiteren van beveiligingsfixes, indien van toepassing, en/of nieuwe functies van de nieuwste versie. Dit beleid is momenteel alleen van toepassing op Linux-web-apps. |AuditIfNotExists, uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_java_Latest.json) |

### <a name="ensure-that-http-version-is-the-latest-if-used-to-run-the-web-app"></a>Controleren of de HTTP-versie de meest recente is als deze wordt gebruikt om de web-app te openen

**Id**: **Eigendom** van CIS Azure 9.10: Klant

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controleren of de HTTP-versie de meest recente is als deze wordt gebruikt om de API-app te openen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F991310cd-e9f3-47bc-b7b6-f57b557d07db) |Er worden regelmatig nieuwere versies uitgebracht voor HTTP, ofwel vanwege de beveiligingsfouten ofwel om extra functionaliteit toe te voegen. Het gebruik van de nieuwste HTTP-versie voor web-apps wordt aanbevolen om te profiteren van beveiligingsfixes, indien van toepassing, en/of nieuwe functies van de nieuwste versie. Dit beleid is momenteel alleen van toepassing op Linux-web-apps. |AuditIfNotExists, uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_HTTP_Latest.json) |
|[Controleren of de HTTP-versie de meest recente is, als deze wordt gebruikt om de Function-app uit te voeren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe2c1c086-2d84-4019-bff3-c44ccd95113c) |Er worden regelmatig nieuwere versies uitgebracht voor HTTP, ofwel vanwege de beveiligingsfouten ofwel om extra functionaliteit toe te voegen. Het gebruik van de nieuwste HTTP-versie voor web-apps wordt aanbevolen om te profiteren van beveiligingsfixes, indien van toepassing, en/of nieuwe functies van de nieuwste versie. Dit beleid is momenteel alleen van toepassing op Linux-web-apps. |AuditIfNotExists, uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_HTTP_Latest.json) |
|[Controleren of de HTTP-versie de meest recente is, als deze wordt gebruikt om de web-app te openen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c122334-9d20-4eb8-89ea-ac9a705b74ae) |Er worden regelmatig nieuwere versies uitgebracht voor HTTP, ofwel vanwege de beveiligingsfouten ofwel om extra functionaliteit toe te voegen. Het gebruik van de nieuwste HTTP-versie voor web-apps wordt aanbevolen om te profiteren van beveiligingsfixes, indien van toepassing, en/of nieuwe functies van de nieuwste versie. Dit beleid is momenteel alleen van toepassing op Linux-web-apps. |AuditIfNotExists, uitgeschakeld |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_HTTP_Latest.json) |

> [!NOTE]
> De beschikbaarheid van specifieke Azure Policy-definities kan verschillen in Azure Government en andere nationale clouds.

## <a name="next-steps"></a>Volgende stappen

Aanvullende artikelen over Azure Policy:

- Overzicht voor [naleving van regelgeving](../concepts/regulatory-compliance.md).
- Bekijk de [structuur van initiatiefdefinities](../concepts/initiative-definition-structure.md).
- Bekijk andere voorbeelden op [Voorbeelden van Azure Policy](./index.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).
- Ontdek hoe u [niet-compatibele resources kunt herstellen](../how-to/remediate-resources.md).
