---
ms.openlocfilehash: 5b1a776c28b6f9218c713dde68f45e571ebfd999
ms.sourcegitcommit: 6341ad07cd5b03269e7fd20cd3212e48baee7c07
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2021
ms.locfileid: "49942154"
---
<!-- markdownlint-disable MD002 MD041 -->

Commencez par créer une application ASP.NET Web Core.

1. Ouvrez votre interface de ligne de commande (CLI) dans un répertoire où vous souhaitez créer le projet. Exécutez la commande suivante.

    ```Shell
    dotnet new mvc -o GraphTutorial
    ```

1. Une fois le projet créé, vérifiez qu’il fonctionne en modifiant le répertoire actuel en répertoire **GraphTutorial** et en exécutant la commande suivante dans votre CLI.

    ```Shell
    dotnet run
    ```

1. Ouvrez votre navigateur et accédez à `https://localhost:5001` . Si tout fonctionne, vous devez voir une page principale ASP.NET par défaut.

> [!IMPORTANT]
> Si vous recevez un avertissement signalant que le certificat pour **localhost** n’est pas approuvé, vous pouvez utiliser l’CLI .NET Core pour installer et faire confiance au certificat de développement. Voir [Appliquer HTTPS dans ASP.NET Core pour](/aspnet/core/security/enforcing-ssl?view=aspnetcore-5.0) obtenir des instructions pour des systèmes d’exploitation spécifiques.

## <a name="add-nuget-packages"></a>Ajouter des packages NuGet

Avant de passer à autre chose, installez des packages NuGet supplémentaires que vous utiliserez ultérieurement.

- [Microsoft.Identity.Web pour demander](https://www.nuget.org/packages/Microsoft.Identity.Web/) et gérer des jetons d’accès.
- [Microsoft.Identity.Web.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph/) pour ajouter le SDK Microsoft Graph via l’injection de dépendances.
- [Microsoft.Identity.Web.UI](https://www.nuget.org/packages/Microsoft.Identity.Web.UI/) pour l’interface utilisateur de la signature et de la signature.
- [TimeZoneConverter pour](https://github.com/mj1856/TimeZoneConverter) la gestion des identificateurs fuseau horaires sur plusieurs plateformes.

1. Exécutez les commandes suivantes dans votre CLI pour installer les dépendances.

    ```Shell
    dotnet add package Microsoft.Identity.Web --version 1.5.1
    dotnet add package Microsoft.Identity.Web.MicrosoftGraph --version 1.5.1
    dotnet add package Microsoft.Identity.Web.UI --version 1.5.1
    dotnet add package TimeZoneConverter
    ```

## <a name="design-the-app"></a>Concevoir l’application

Dans cette section, vous allez créer la structure d’interface utilisateur de base de l’application.

### <a name="implement-alert-extension-methods"></a>Implémenter des méthodes d’extension d’alerte

Dans cette section, vous allez créer des méthodes d’extension pour le `IActionResult` type renvoyé par les vues du contrôleur. Cette extension permet de transmettre des messages d’erreur ou de réussite temporaires à l’affichage.

> [!TIP]
> Vous pouvez utiliser n’importe quel éditeur de texte pour modifier les fichiers sources de ce didacticiel. Toutefois, [Visual Studio Code fournit](https://code.visualstudio.com/) des fonctionnalités supplémentaires, telles que le débogage et Intellisense.

1. Créez un répertoire dans le **répertoire GraphTutorial** nommé **Alertes.**

1. Créez un fichier nommé **WithAlertResult.cs** dans le répertoire **./Alerts** et ajoutez le code suivant.

    :::code language="csharp" source="../demo/GraphTutorial/Alerts/WithAlertResult.cs" id="WithAlertResultSnippet":::

1. Créez un fichier nommé **AlertExtensions.cs** dans le répertoire **./Alerts** et ajoutez le code suivant.

    :::code language="csharp" source="../demo/GraphTutorial/Alerts/AlertExtensions.cs" id="AlertExtensionsSnippet":::

### <a name="implement-user-data-extension-methods"></a>Implémenter des méthodes d’extension de données utilisateur

Dans cette section, vous allez créer des méthodes d’extension pour `ClaimsPrincipal` l’objet généré par la plateforme Microsoft Identity. Cela vous permettra d’étendre l’identité utilisateur existante avec les données de Microsoft Graph.

> [!NOTE]
> Ce code n’est qu’un espace réservé pour l’instant, vous le compléterez dans une section ultérieure.

1. Créez un répertoire dans le **répertoire GraphTutorial** nommé **Graph**.

1. Créez un fichier nommé **GraphClaimsPrincipalExtensions.cs** et ajoutez le code suivant.

    ```csharp
    using System.Security.Claims;

    namespace GraphTutorial
    {
        public static class GraphClaimTypes {
            public const string DisplayName ="graph_name";
            public const string Email = "graph_email";
            public const string Photo = "graph_photo";
            public const string TimeZone = "graph_timezone";
            public const string DateTimeFormat = "graph_datetimeformat";
        }

        // Helper methods to access Graph user data stored in
        // the claims principal
        public static class GraphClaimsPrincipalExtensions
        {
            public static string GetUserGraphDisplayName(this ClaimsPrincipal claimsPrincipal)
            {
                return "Adele Vance";
            }

            public static string GetUserGraphEmail(this ClaimsPrincipal claimsPrincipal)
            {
                return "adelev@contoso.com";
            }

            public static string GetUserGraphPhoto(this ClaimsPrincipal claimsPrincipal)
            {
                return "/img/no-profile-photo.png";
            }
        }
    }
    ```

### <a name="create-views"></a>Créer des affichages

Dans cette section, vous allez implémenter les affichages Dutable pour l’application.

1. Ajoutez un nouveau fichier nommé **_LoginPartial.cshtml** dans le répertoire **./Views/Shared** et ajoutez le code suivant.

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Shared/_LoginPartial.cshtml" id="LoginPartialSnippet":::

1. Ajoutez un nouveau fichier nommé **_AlertPartial.cshtml** dans le répertoire **./Views/Shared** et ajoutez le code suivant.

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Shared/_AlertPartial.cshtml" id="AlertPartialSnippet":::

1. Ouvrez le fichier **./Views/Shared/_Layout.cshtml** et remplacez l’intégralité de son contenu par le code suivant pour mettre à jour la disposition globale de l’application.

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Shared/_Layout.cshtml" id="LayoutSnippet":::

1. Ouvrez **./wwwroot/css/site.css** et ajoutez le code suivant en bas du fichier.

    :::code language="css" source="../demo/GraphTutorial/wwwroot/css/site.css" id="CssSnippet":::

1. Ouvrez **le fichier ./Views/Home/index.cshtml** et remplacez son contenu par ce qui suit.

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Home/Index.cshtml" id="HomeIndexSnippet":::

1. Créez un répertoire dans **le répertoire ./wwwroot** nommé **img**. Ajoutez un fichier image de votre choix nommé **no-profile-photo.png** dans ce répertoire. Cette image est utilisée comme photo de l’utilisateur lorsque l’utilisateur n’a pas de photo dans Microsoft Graph.

    > [!TIP]
    > Vous pouvez télécharger l’image utilisée dans ces captures d’écran à partir [de GitHub.](https://github.com/microsoftgraph/msgraph-training-aspnet-core/blob/master/demo/GraphTutorial/wwwroot/img/no-profile-photo.png)

1. Enregistrez toutes vos modifications et redémarrez le serveur ( `dotnet run` ). L’application doit maintenant avoir une apparence très différente.

    ![Capture d’écran de la page d’accueil repensée](./images/create-app-01.png)
