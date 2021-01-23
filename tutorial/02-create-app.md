---
ms.openlocfilehash: 5b1a776c28b6f9218c713dde68f45e571ebfd999
ms.sourcegitcommit: 6341ad07cd5b03269e7fd20cd3212e48baee7c07
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2021
ms.locfileid: "49942154"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="cf20c-101">Commencez par créer une application ASP.NET Web Core.</span><span class="sxs-lookup"><span data-stu-id="cf20c-101">Start by creating an ASP.NET Core web app.</span></span>

1. <span data-ttu-id="cf20c-102">Ouvrez votre interface de ligne de commande (CLI) dans un répertoire où vous souhaitez créer le projet.</span><span class="sxs-lookup"><span data-stu-id="cf20c-102">Open your command-line interface (CLI) in a directory where you want to create the project.</span></span> <span data-ttu-id="cf20c-103">Exécutez la commande suivante.</span><span class="sxs-lookup"><span data-stu-id="cf20c-103">Run the following command.</span></span>

    ```Shell
    dotnet new mvc -o GraphTutorial
    ```

1. <span data-ttu-id="cf20c-104">Une fois le projet créé, vérifiez qu’il fonctionne en modifiant le répertoire actuel en répertoire **GraphTutorial** et en exécutant la commande suivante dans votre CLI.</span><span class="sxs-lookup"><span data-stu-id="cf20c-104">Once the project is created, verify that it works by changing the current directory to the **GraphTutorial** directory and running the following command in your CLI.</span></span>

    ```Shell
    dotnet run
    ```

1. <span data-ttu-id="cf20c-105">Ouvrez votre navigateur et accédez à `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="cf20c-105">Open your browser and browse to `https://localhost:5001`.</span></span> <span data-ttu-id="cf20c-106">Si tout fonctionne, vous devez voir une page principale ASP.NET par défaut.</span><span class="sxs-lookup"><span data-stu-id="cf20c-106">If everything is working, you should see a default ASP.NET Core page.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="cf20c-107">Si vous recevez un avertissement signalant que le certificat pour **localhost** n’est pas approuvé, vous pouvez utiliser l’CLI .NET Core pour installer et faire confiance au certificat de développement.</span><span class="sxs-lookup"><span data-stu-id="cf20c-107">If you receive a warning that the certificate for **localhost** is un-trusted you can use the .NET Core CLI to install and trust the development certificate.</span></span> <span data-ttu-id="cf20c-108">Voir [Appliquer HTTPS dans ASP.NET Core pour](/aspnet/core/security/enforcing-ssl?view=aspnetcore-5.0) obtenir des instructions pour des systèmes d’exploitation spécifiques.</span><span class="sxs-lookup"><span data-stu-id="cf20c-108">See [Enforce HTTPS in ASP.NET Core](/aspnet/core/security/enforcing-ssl?view=aspnetcore-5.0) for instructions for specific operating systems.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="cf20c-109">Ajouter des packages NuGet</span><span class="sxs-lookup"><span data-stu-id="cf20c-109">Add NuGet packages</span></span>

<span data-ttu-id="cf20c-110">Avant de passer à autre chose, installez des packages NuGet supplémentaires que vous utiliserez ultérieurement.</span><span class="sxs-lookup"><span data-stu-id="cf20c-110">Before moving on, install some additional NuGet packages that you will use later.</span></span>

- <span data-ttu-id="cf20c-111">[Microsoft.Identity.Web pour demander](https://www.nuget.org/packages/Microsoft.Identity.Web/) et gérer des jetons d’accès.</span><span class="sxs-lookup"><span data-stu-id="cf20c-111">[Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) for requesting and managing access tokens.</span></span>
- <span data-ttu-id="cf20c-112">[Microsoft.Identity.Web.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph/) pour ajouter le SDK Microsoft Graph via l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="cf20c-112">[Microsoft.Identity.Web.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph/) for adding the Microsoft Graph SDK via dependency injection.</span></span>
- <span data-ttu-id="cf20c-113">[Microsoft.Identity.Web.UI](https://www.nuget.org/packages/Microsoft.Identity.Web.UI/) pour l’interface utilisateur de la signature et de la signature.</span><span class="sxs-lookup"><span data-stu-id="cf20c-113">[Microsoft.Identity.Web.UI](https://www.nuget.org/packages/Microsoft.Identity.Web.UI/) for sign-in and sign-out UI.</span></span>
- <span data-ttu-id="cf20c-114">[TimeZoneConverter pour](https://github.com/mj1856/TimeZoneConverter) la gestion des identificateurs fuseau horaires sur plusieurs plateformes.</span><span class="sxs-lookup"><span data-stu-id="cf20c-114">[TimeZoneConverter](https://github.com/mj1856/TimeZoneConverter) for handling time zoned identifiers cross-platform.</span></span>

1. <span data-ttu-id="cf20c-115">Exécutez les commandes suivantes dans votre CLI pour installer les dépendances.</span><span class="sxs-lookup"><span data-stu-id="cf20c-115">Run the following commands in your CLI to install the dependencies.</span></span>

    ```Shell
    dotnet add package Microsoft.Identity.Web --version 1.5.1
    dotnet add package Microsoft.Identity.Web.MicrosoftGraph --version 1.5.1
    dotnet add package Microsoft.Identity.Web.UI --version 1.5.1
    dotnet add package TimeZoneConverter
    ```

## <a name="design-the-app"></a><span data-ttu-id="cf20c-116">Concevoir l’application</span><span class="sxs-lookup"><span data-stu-id="cf20c-116">Design the app</span></span>

<span data-ttu-id="cf20c-117">Dans cette section, vous allez créer la structure d’interface utilisateur de base de l’application.</span><span class="sxs-lookup"><span data-stu-id="cf20c-117">In this section you will create the basic UI structure of the application.</span></span>

### <a name="implement-alert-extension-methods"></a><span data-ttu-id="cf20c-118">Implémenter des méthodes d’extension d’alerte</span><span class="sxs-lookup"><span data-stu-id="cf20c-118">Implement alert extension methods</span></span>

<span data-ttu-id="cf20c-119">Dans cette section, vous allez créer des méthodes d’extension pour le `IActionResult` type renvoyé par les vues du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="cf20c-119">In this section you will create extension methods for the `IActionResult` type returned by controller views.</span></span> <span data-ttu-id="cf20c-120">Cette extension permet de transmettre des messages d’erreur ou de réussite temporaires à l’affichage.</span><span class="sxs-lookup"><span data-stu-id="cf20c-120">This extension will enable passing temporary error or success messages to the view.</span></span>

> [!TIP]
> <span data-ttu-id="cf20c-121">Vous pouvez utiliser n’importe quel éditeur de texte pour modifier les fichiers sources de ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="cf20c-121">You can use any text editor to edit the source files for this tutorial.</span></span> <span data-ttu-id="cf20c-122">Toutefois, [Visual Studio Code fournit](https://code.visualstudio.com/) des fonctionnalités supplémentaires, telles que le débogage et Intellisense.</span><span class="sxs-lookup"><span data-stu-id="cf20c-122">However, [Visual Studio Code](https://code.visualstudio.com/) provides additional features, such as debugging and Intellisense.</span></span>

1. <span data-ttu-id="cf20c-123">Créez un répertoire dans le **répertoire GraphTutorial** nommé **Alertes.**</span><span class="sxs-lookup"><span data-stu-id="cf20c-123">Create a new directory in the **GraphTutorial** directory named **Alerts**.</span></span>

1. <span data-ttu-id="cf20c-124">Créez un fichier nommé **WithAlertResult.cs** dans le répertoire **./Alerts** et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="cf20c-124">Create a new file named **WithAlertResult.cs** in the **./Alerts** directory and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Alerts/WithAlertResult.cs" id="WithAlertResultSnippet":::

1. <span data-ttu-id="cf20c-125">Créez un fichier nommé **AlertExtensions.cs** dans le répertoire **./Alerts** et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="cf20c-125">Create a new file named **AlertExtensions.cs** in the **./Alerts** directory and add the following code.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Alerts/AlertExtensions.cs" id="AlertExtensionsSnippet":::

### <a name="implement-user-data-extension-methods"></a><span data-ttu-id="cf20c-126">Implémenter des méthodes d’extension de données utilisateur</span><span class="sxs-lookup"><span data-stu-id="cf20c-126">Implement user data extension methods</span></span>

<span data-ttu-id="cf20c-127">Dans cette section, vous allez créer des méthodes d’extension pour `ClaimsPrincipal` l’objet généré par la plateforme Microsoft Identity.</span><span class="sxs-lookup"><span data-stu-id="cf20c-127">In this section you will create extension methods for the `ClaimsPrincipal` object generated by the Microsoft Identity platform.</span></span> <span data-ttu-id="cf20c-128">Cela vous permettra d’étendre l’identité utilisateur existante avec les données de Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="cf20c-128">This will allow you to extend the existing user identity with data from Microsoft Graph.</span></span>

> [!NOTE]
> <span data-ttu-id="cf20c-129">Ce code n’est qu’un espace réservé pour l’instant, vous le compléterez dans une section ultérieure.</span><span class="sxs-lookup"><span data-stu-id="cf20c-129">This code is just a placeholder for now, you will complete it in a later section.</span></span>

1. <span data-ttu-id="cf20c-130">Créez un répertoire dans le **répertoire GraphTutorial** nommé **Graph**.</span><span class="sxs-lookup"><span data-stu-id="cf20c-130">Create a new directory in the **GraphTutorial** directory named **Graph**.</span></span>

1. <span data-ttu-id="cf20c-131">Créez un fichier nommé **GraphClaimsPrincipalExtensions.cs** et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="cf20c-131">Create a new file named **GraphClaimsPrincipalExtensions.cs** and add the following code.</span></span>

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

### <a name="create-views"></a><span data-ttu-id="cf20c-132">Créer des affichages</span><span class="sxs-lookup"><span data-stu-id="cf20c-132">Create views</span></span>

<span data-ttu-id="cf20c-133">Dans cette section, vous allez implémenter les affichages Dutable pour l’application.</span><span class="sxs-lookup"><span data-stu-id="cf20c-133">In this section you will implement the Razor views for the application.</span></span>

1. <span data-ttu-id="cf20c-134">Ajoutez un nouveau fichier nommé **_LoginPartial.cshtml** dans le répertoire **./Views/Shared** et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="cf20c-134">Add a new file named **_LoginPartial.cshtml** in the **./Views/Shared** directory and add the following code.</span></span>

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Shared/_LoginPartial.cshtml" id="LoginPartialSnippet":::

1. <span data-ttu-id="cf20c-135">Ajoutez un nouveau fichier nommé **_AlertPartial.cshtml** dans le répertoire **./Views/Shared** et ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="cf20c-135">Add a new file named **_AlertPartial.cshtml** in the **./Views/Shared** directory and add the following code.</span></span>

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Shared/_AlertPartial.cshtml" id="AlertPartialSnippet":::

1. <span data-ttu-id="cf20c-136">Ouvrez le fichier **./Views/Shared/_Layout.cshtml** et remplacez l’intégralité de son contenu par le code suivant pour mettre à jour la disposition globale de l’application.</span><span class="sxs-lookup"><span data-stu-id="cf20c-136">Open the **./Views/Shared/_Layout.cshtml** file, and replace its entire contents with the following code to update the global layout of the app.</span></span>

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Shared/_Layout.cshtml" id="LayoutSnippet":::

1. <span data-ttu-id="cf20c-137">Ouvrez **./wwwroot/css/site.css** et ajoutez le code suivant en bas du fichier.</span><span class="sxs-lookup"><span data-stu-id="cf20c-137">Open **./wwwroot/css/site.css** and add the following code at the bottom of the file.</span></span>

    :::code language="css" source="../demo/GraphTutorial/wwwroot/css/site.css" id="CssSnippet":::

1. <span data-ttu-id="cf20c-138">Ouvrez **le fichier ./Views/Home/index.cshtml** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="cf20c-138">Open the **./Views/Home/index.cshtml** file and replace its contents with the following.</span></span>

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Home/Index.cshtml" id="HomeIndexSnippet":::

1. <span data-ttu-id="cf20c-139">Créez un répertoire dans **le répertoire ./wwwroot** nommé **img**.</span><span class="sxs-lookup"><span data-stu-id="cf20c-139">Create a new directory in the **./wwwroot** directory named **img**.</span></span> <span data-ttu-id="cf20c-140">Ajoutez un fichier image de votre choix nommé **no-profile-photo.png** dans ce répertoire.</span><span class="sxs-lookup"><span data-stu-id="cf20c-140">Add an image file of your choosing named **no-profile-photo.png** in this directory.</span></span> <span data-ttu-id="cf20c-141">Cette image est utilisée comme photo de l’utilisateur lorsque l’utilisateur n’a pas de photo dans Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="cf20c-141">This image will be used as the user's photo when the user has no photo in Microsoft Graph.</span></span>

    > [!TIP]
    > <span data-ttu-id="cf20c-142">Vous pouvez télécharger l’image utilisée dans ces captures d’écran à partir [de GitHub.](https://github.com/microsoftgraph/msgraph-training-aspnet-core/blob/master/demo/GraphTutorial/wwwroot/img/no-profile-photo.png)</span><span class="sxs-lookup"><span data-stu-id="cf20c-142">You can download the image used in these screenshots from [GitHub](https://github.com/microsoftgraph/msgraph-training-aspnet-core/blob/master/demo/GraphTutorial/wwwroot/img/no-profile-photo.png).</span></span>

1. <span data-ttu-id="cf20c-143">Enregistrez toutes vos modifications et redémarrez le serveur ( `dotnet run` ).</span><span class="sxs-lookup"><span data-stu-id="cf20c-143">Save all of your changes and restart the server (`dotnet run`).</span></span> <span data-ttu-id="cf20c-144">L’application doit maintenant avoir une apparence très différente.</span><span class="sxs-lookup"><span data-stu-id="cf20c-144">Now, the app should look very different.</span></span>

    ![Capture d’écran de la page d’accueil repensée](./images/create-app-01.png)
