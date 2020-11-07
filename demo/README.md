---
ms.openlocfilehash: 30398bafbf47aaa374a200dd1834c9f2003e967f
ms.sourcegitcommit: 9d0d10a9e8e5a1d80382d89bc412df287bee03f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822353"
---
# <a name="how-to-run-the-completed-project"></a><span data-ttu-id="71f3b-101">Exécution du projet terminé</span><span class="sxs-lookup"><span data-stu-id="71f3b-101">How to run the completed project</span></span>

## <a name="prerequisites"></a><span data-ttu-id="71f3b-102">Conditions préalables</span><span class="sxs-lookup"><span data-stu-id="71f3b-102">Prerequisites</span></span>

<span data-ttu-id="71f3b-103">Pour exécuter le projet terminé dans ce dossier, vous avez besoin des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="71f3b-103">To run the completed project in this folder, you need the following:</span></span>

- <span data-ttu-id="71f3b-104">Le [Kit de développement logiciel .net Core](https://dotnet.microsoft.com/download) installé sur votre ordinateur de développement.</span><span class="sxs-lookup"><span data-stu-id="71f3b-104">The [.NET Core SDK](https://dotnet.microsoft.com/download) installed on your development machine.</span></span> <span data-ttu-id="71f3b-105">( **Remarque :** ce didacticiel a été écrit avec .net Core SDK version 3.1.201.</span><span class="sxs-lookup"><span data-stu-id="71f3b-105">( **Note:** This tutorial was written with .NET Core SDK version 3.1.201.</span></span> <span data-ttu-id="71f3b-106">Les étapes de ce guide peuvent fonctionner avec d’autres versions, mais cela n’a pas été testé.)</span><span class="sxs-lookup"><span data-stu-id="71f3b-106">The steps in this guide may work with other versions, but that has not been tested.)</span></span>
- <span data-ttu-id="71f3b-107">Soit un compte Microsoft personnel avec une boîte aux lettres sur Outlook.com, soit un compte professionnel ou scolaire Microsoft.</span><span class="sxs-lookup"><span data-stu-id="71f3b-107">Either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span>

<span data-ttu-id="71f3b-108">Si vous n’avez pas de compte Microsoft, vous disposez de deux options pour obtenir un compte gratuit :</span><span class="sxs-lookup"><span data-stu-id="71f3b-108">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="71f3b-109">Vous pouvez vous [inscrire pour obtenir un nouveau compte Microsoft personnel](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span><span class="sxs-lookup"><span data-stu-id="71f3b-109">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="71f3b-110">Vous pouvez vous [inscrire au programme pour les développeurs office 365](https://developer.microsoft.com/office/dev-program) pour obtenir un abonnement gratuit à Office 365.</span><span class="sxs-lookup"><span data-stu-id="71f3b-110">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

## <a name="register-a-web-application-with-the-azure-active-directory-admin-center"></a><span data-ttu-id="71f3b-111">Enregistrer une application Web avec le centre d’administration Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="71f3b-111">Register a web application with the Azure Active Directory admin center</span></span>

1. <span data-ttu-id="71f3b-112">Ouvrez un navigateur et accédez au [Centre d’administration Azure Active Directory](https://aad.portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="71f3b-112">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com).</span></span> <span data-ttu-id="71f3b-113">Connectez-vous à l’aide d’un **compte personnel** (compte Microsoft) ou d’un **compte professionnel ou scolaire**.</span><span class="sxs-lookup"><span data-stu-id="71f3b-113">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="71f3b-114">Sélectionnez **Azure Active Directory** dans le volet de navigation gauche, puis sélectionnez **Inscriptions d’applications** sous **Gérer**.</span><span class="sxs-lookup"><span data-stu-id="71f3b-114">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="71f3b-115">Une capture d’écran des inscriptions d’applications</span><span class="sxs-lookup"><span data-stu-id="71f3b-115">A screenshot of the App registrations</span></span> ](../tutorial/images/aad-portal-app-registrations.png)

1. <span data-ttu-id="71f3b-116">Sélectionnez **Nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="71f3b-116">Select **New registration**.</span></span> <span data-ttu-id="71f3b-117">Sur la page **Inscrire une application** , définissez les valeurs comme suit.</span><span class="sxs-lookup"><span data-stu-id="71f3b-117">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="71f3b-118">Définissez le **Nom** sur `ASP.NET Core Graph Tutorial`.</span><span class="sxs-lookup"><span data-stu-id="71f3b-118">Set **Name** to `ASP.NET Core Graph Tutorial`.</span></span>
    - <span data-ttu-id="71f3b-119">Définissez les **Types de comptes pris en charge** sur **Comptes dans un annuaire organisationnel et comptes personnels Microsoft**.</span><span class="sxs-lookup"><span data-stu-id="71f3b-119">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="71f3b-120">Sous **URI de redirection** , définissez la première flèche déroulante sur `Web`, et la valeur sur `https://localhost:5001/`.</span><span class="sxs-lookup"><span data-stu-id="71f3b-120">Under **Redirect URI** , set the first drop-down to `Web` and set the value to `https://localhost:5001/`.</span></span>

    ![Capture d’écran de la page Inscrire une application](../tutorial/images/aad-register-an-app.png)

1. <span data-ttu-id="71f3b-122">Sélectionner **Inscription**.</span><span class="sxs-lookup"><span data-stu-id="71f3b-122">Select **Register**.</span></span> <span data-ttu-id="71f3b-123">Sur la page du **didacticiel Graph de microsoft ASP.net** , copiez la valeur de l' **ID d’application (client)** et enregistrez-la, vous en aurez besoin à l’étape suivante.</span><span class="sxs-lookup"><span data-stu-id="71f3b-123">On the **ASP.NET Core Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![Une capture d’écran de l’ID d’application de la nouvelle inscription d'application](../tutorial/images/aad-application-id.png)

1. <span data-ttu-id="71f3b-125">Sous **Gérer** , sélectionnez **Authentification**.</span><span class="sxs-lookup"><span data-stu-id="71f3b-125">Select **Authentication** under **Manage**.</span></span> <span data-ttu-id="71f3b-126">Sous **URI de redirection** , ajoutez un URI avec la valeur `https://localhost:5001/signin-oidc` .</span><span class="sxs-lookup"><span data-stu-id="71f3b-126">Under **Redirect URIs** add a URI with the value `https://localhost:5001/signin-oidc`.</span></span>

1. <span data-ttu-id="71f3b-127">Définissez l' **URL de déconnexion** sur `https://localhost:5001/signout-oidc` .</span><span class="sxs-lookup"><span data-stu-id="71f3b-127">Set the **Logout URL** to `https://localhost:5001/signout-oidc`.</span></span>

1. <span data-ttu-id="71f3b-128">Recherchez la section **Octroi implicite** , puis activez **Jetons d’ID**.</span><span class="sxs-lookup"><span data-stu-id="71f3b-128">Locate the **Implicit grant** section and enable **ID tokens**.</span></span> <span data-ttu-id="71f3b-129">Sélectionnez **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="71f3b-129">Select **Save**.</span></span>

    ![Capture d’écran des paramètres de plateforme Web dans le portail Azure](../tutorial/images/aad-web-platform.png)

1. <span data-ttu-id="71f3b-131">Sélectionnez **Certificats et secrets** sous **Gérer**.</span><span class="sxs-lookup"><span data-stu-id="71f3b-131">Select **Certificates & secrets** under **Manage**.</span></span> <span data-ttu-id="71f3b-132">Sélectionnez le bouton **Nouveau secret client**.</span><span class="sxs-lookup"><span data-stu-id="71f3b-132">Select the **New client secret** button.</span></span> <span data-ttu-id="71f3b-133">Entrez une valeur dans **Description** , sélectionnez une des options pour **Expire le** , puis sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="71f3b-133">Enter a value in **Description** and select one of the options for **Expires** and select **Add**.</span></span>

    ![Une capture d’écran de la boîte de dialogue Ajouter une clé secrète client](../tutorial/images/aad-new-client-secret.png)

1. <span data-ttu-id="71f3b-135">Copiez la valeur due la clé secrète client avant de quitter cette page.</span><span class="sxs-lookup"><span data-stu-id="71f3b-135">Copy the client secret value before you leave this page.</span></span> <span data-ttu-id="71f3b-136">Vous en aurez besoin à l’étape suivante.</span><span class="sxs-lookup"><span data-stu-id="71f3b-136">You will need it in the next step.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="71f3b-137">Ce secret client n’apparaîtra plus jamais, aussi veillez à le copier maintenant.</span><span class="sxs-lookup"><span data-stu-id="71f3b-137">This client secret is never shown again, so make sure you copy it now.</span></span>

    ![Une capture d’écran de la clé secrète client nouvellement ajoutée](../tutorial/images/aad-copy-client-secret.png)

## <a name="configure-the-sample"></a><span data-ttu-id="71f3b-139">Configurer l’exemple</span><span class="sxs-lookup"><span data-stu-id="71f3b-139">Configure the sample</span></span>

1. <span data-ttu-id="71f3b-140">Ouvrez votre interface de ligne de commande (CLI) dans le répertoire où se trouve **GraphTutorial. csproj** , puis exécutez les commandes suivantes, en remplaçant `YOUR_APP_ID` votre ID d’application dans le portail Azure, et `YOUR_APP_SECRET` votre secret d’application.</span><span class="sxs-lookup"><span data-stu-id="71f3b-140">Open your command line interface (CLI) in the directory where **GraphTutorial.csproj** is located, and run the following commands, substituting `YOUR_APP_ID` with your application ID from the Azure portal, and `YOUR_APP_SECRET` with your application secret.</span></span>

    ```Shell
    dotnet user-secrets init
    dotnet user-secrets set "AzureAd:ClientId" "YOUR_APP_ID"
    dotnet user-secrets set "AzureAd:ClientSecret" "YOUR_APP_SECRET"
    ```

## <a name="run-the-sample"></a><span data-ttu-id="71f3b-141">Exécution de l’exemple</span><span class="sxs-lookup"><span data-stu-id="71f3b-141">Run the sample</span></span>

<span data-ttu-id="71f3b-142">Dans votre interface CLI, exécutez la commande suivante pour démarrer l’application.</span><span class="sxs-lookup"><span data-stu-id="71f3b-142">In your CLI, run the following command to start the application.</span></span>

```Shell
dotnet run
```
