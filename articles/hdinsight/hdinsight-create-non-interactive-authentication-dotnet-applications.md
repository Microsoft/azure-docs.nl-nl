---
title: .NET-toepassing voor niet-interactieve verificatie-Azure HDInsight
description: Meer informatie over het maken van niet-interactieve verificatie Microsoft .NET toepassingen in azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 12/23/2019
ms.openlocfilehash: 121c5850ef47999f54d206b95b69e10775d3e5c9
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946082"
---
# <a name="create-a-non-interactive-authentication-net-hdinsight-application"></a>Een niet-interactieve verificatie-app voor .NET HDInsight maken

Voer uw Microsoft .NET Azure HDInsight-toepassing uit op basis van de eigen identiteit van de toepassing (niet-interactief) of onder de identiteit van de aangemelde gebruiker van de toepassing (interactief). In dit artikel wordt beschreven hoe u een niet-interactieve verificatie-.NET-toepassing maakt om verbinding te maken met Azure en HDInsight te beheren. Zie [verbinding maken met Azure HDInsight](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight)voor een voor beeld van een interactieve toepassing.

Vanuit uw niet-interactieve .NET-toepassing hebt u het volgende nodig:

* De Tenant-ID van uw Azure-abonnement (ook wel een *Directory-id* genoemd). Zie [Tenant-id ophalen](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).
* De client-ID van de Azure Active Directory-toepassing (Azure AD). Zie [een Azure Active Directory-toepassing maken](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) en [een toepassings-id ophalen](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).
* De geheime sleutel van de Azure AD-toepassing. Zie [toepassings verificatie sleutel ophalen](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).

## <a name="prerequisites"></a>Vereisten

Een HDInsight-cluster. Zie de [zelf studie aan de slag](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="assign-a-role-to-the-azure-ad-application"></a>Een rol toewijzen aan de Azure AD-toepassing

Wijs uw Azure AD-toepassing een [rol](../role-based-access-control/built-in-roles.md)toe om deze machtigingen te verlenen voor het uitvoeren van acties. U kunt het bereik instellen op het niveau van het abonnement, de resource groep of de resource. De machtigingen worden overgenomen door lagere bereik niveaus. Als u bijvoorbeeld een toepassing toevoegt aan de rol van lezer voor een resource groep, betekent dit dat de toepassing de resource groep en alle resources erin kan lezen. In dit artikel stelt u het bereik in op het niveau van de resource groep. Zie [roltoewijzingen gebruiken voor het beheren van toegang tot uw Azure-abonnements resources](../role-based-access-control/role-assignments-portal.md)voor meer informatie.

**De rol van eigenaar toevoegen aan de Azure AD-toepassing**

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Ga naar de resource groep met het HDInsight-cluster waarop u de Hive-query wilt uitvoeren, verderop in dit artikel. Als u een groot aantal resource groepen hebt, kunt u het filter gebruiken om het gewenste item te vinden.
1. Selecteer **toegangs beheer (IAM)** in het menu resource groep.
1. Selecteer het **tabblad roltoewijzingen om de huidige** roltoewijzingen weer te geven.
1. Selecteer **+ Toevoegen** bovenaan de pagina.
1. Volg de instructies om de rol van eigenaar toe te voegen aan uw Azure AD-toepassing. Nadat u de rol hebt toegevoegd, wordt de toepassing vermeld onder de rol eigenaar.

## <a name="develop-an-hdinsight-client-application"></a>Een HDInsight-client toepassing ontwikkelen

1. Maak een C#-consoletoepassing.
2. Voeg de volgende [NuGet](https://www.nuget.org/) -pakketten toe:

    * `Install-Package Microsoft.Azure.Common.Authentication -Pre`
    * `Install-Package Microsoft.Azure.Management.HDInsight -Pre`
    * `Install-Package Microsoft.Azure.Management.Resources -Pre`

3. Voer de volgende code uit:

    ```csharp
    using System;
    using System.Security;
    using Microsoft.Azure;
    using Microsoft.Azure.Common.Authentication;
    using Microsoft.Azure.Common.Authentication.Factories;
    using Microsoft.Azure.Common.Authentication.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.HDInsight;
    
    namespace CreateHDICluster
    {
        internal class Program
        {
            private static HDInsightManagementClient _hdiManagementClient;
    
            private static Guid SubscriptionId = new Guid("<Enter your Azure subscription ID>");
            private static string tenantID = "<Enter your tenant ID (also called directory ID)>";
            private static string applicationID = "<Enter your application ID>";
            private static string secretKey = "<Enter the application secret key>";
    
            private static void Main(string[] args)
            {
                var key = new SecureString();
                foreach (char c in secretKey) { key.AppendChar(c); }
    
                var tokenCreds = GetTokenCloudCredentials(tenantID, applicationID, key);
                var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
    
                var resourceManagementClient = new ResourceManagementClient(subCloudCredentials);
                resourceManagementClient.Providers.Register("Microsoft.HDInsight");
    
                _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
    
                var results = _hdiManagementClient.Clusters.List();
                foreach (var name in results.Clusters)
                {
                    Console.WriteLine("Cluster Name: " + name.Name);
                    Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
                    Console.WriteLine("\t Cluster location: " + name.Location);
                    Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
                }
                Console.WriteLine("Press Enter to continue");
                Console.ReadLine();
            }
    
            /// Get the access token for a service principal and provided key.          
            public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
            {
                var authFactory = new AuthenticationFactory();
                var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
                var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
                var accessToken =
                    authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
    
                return new TokenCloudCredentials(accessToken);
            }
    
            public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
            {
                return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
            }
        }
    }
    ```

## <a name="next-steps"></a>Volgende stappen

* [Maak een Azure Active Directory toepassing en Service-Principal in de Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md).
* Meer informatie over het [verifiëren van een service-principal met Azure Resource Manager](../active-directory/develop/howto-authenticate-service-principal-powershell.md).
* Meer informatie over [toegangs beheer op basis van rollen (Azure RBAC) van Azure](../role-based-access-control/role-assignments-portal.md).
