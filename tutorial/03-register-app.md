---
ms.openlocfilehash: debd685996df22a83110a14ca585cfafa4e08a0d
ms.sourcegitcommit: 9d0d10a9e8e5a1d80382d89bc412df287bee03f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822350"
---
<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="f9c61-101">Dans cet exercice, vous allez créer une inscription de l’application Web Azure AD à l’aide du centre d’administration Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="f9c61-101">In this exercise, you will create a new Azure AD web application registration using the Azure Active Directory admin center.</span></span>

1. <span data-ttu-id="f9c61-102">Ouvrez un navigateur et accédez au [Centre d’administration Azure Active Directory](https://aad.portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="f9c61-102">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com).</span></span> <span data-ttu-id="f9c61-103">Connectez-vous à l’aide d’un **compte personnel** (compte Microsoft) ou d’un **compte professionnel ou scolaire**.</span><span class="sxs-lookup"><span data-stu-id="f9c61-103">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="f9c61-104">Sélectionnez **Azure Active Directory** dans le volet de navigation gauche, puis sélectionnez **Inscriptions d’applications** sous **Gérer**.</span><span class="sxs-lookup"><span data-stu-id="f9c61-104">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="f9c61-105">Une capture d’écran des inscriptions d’applications</span><span class="sxs-lookup"><span data-stu-id="f9c61-105">A screenshot of the App registrations</span></span> ](./images/aad-portal-app-registrations.png)

1. <span data-ttu-id="f9c61-106">Sélectionnez **Nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="f9c61-106">Select **New registration**.</span></span> <span data-ttu-id="f9c61-107">Sur la page **Inscrire une application** , définissez les valeurs comme suit.</span><span class="sxs-lookup"><span data-stu-id="f9c61-107">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="f9c61-108">Définissez le **Nom** sur `ASP.NET Core Graph Tutorial`.</span><span class="sxs-lookup"><span data-stu-id="f9c61-108">Set **Name** to `ASP.NET Core Graph Tutorial`.</span></span>
    - <span data-ttu-id="f9c61-109">Définissez les **Types de comptes pris en charge** sur **Comptes dans un annuaire organisationnel et comptes personnels Microsoft**.</span><span class="sxs-lookup"><span data-stu-id="f9c61-109">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="f9c61-110">Sous **URI de redirection** , définissez la première flèche déroulante sur `Web`, et la valeur sur `https://localhost:5001/`.</span><span class="sxs-lookup"><span data-stu-id="f9c61-110">Under **Redirect URI** , set the first drop-down to `Web` and set the value to `https://localhost:5001/`.</span></span>

    ![Capture d’écran de la page Inscrire une application](./images/aad-register-an-app.png)

1. <span data-ttu-id="f9c61-112">Sélectionner **Inscription**.</span><span class="sxs-lookup"><span data-stu-id="f9c61-112">Select **Register**.</span></span> <span data-ttu-id="f9c61-113">Sur la page du **didacticiel Graph de microsoft ASP.net** , copiez la valeur de l' **ID d’application (client)** et enregistrez-la, vous en aurez besoin à l’étape suivante.</span><span class="sxs-lookup"><span data-stu-id="f9c61-113">On the **ASP.NET Core Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![Une capture d’écran de l’ID d’application de la nouvelle inscription d'application](./images/aad-application-id.png)

1. <span data-ttu-id="f9c61-115">Sous **Gérer** , sélectionnez **Authentification**.</span><span class="sxs-lookup"><span data-stu-id="f9c61-115">Select **Authentication** under **Manage**.</span></span> <span data-ttu-id="f9c61-116">Sous **URI de redirection** , ajoutez un URI avec la valeur `https://localhost:5001/signin-oidc` .</span><span class="sxs-lookup"><span data-stu-id="f9c61-116">Under **Redirect URIs** add a URI with the value `https://localhost:5001/signin-oidc`.</span></span>

1. <span data-ttu-id="f9c61-117">Définissez l' **URL de déconnexion** sur `https://localhost:5001/signout-oidc` .</span><span class="sxs-lookup"><span data-stu-id="f9c61-117">Set the **Logout URL** to `https://localhost:5001/signout-oidc`.</span></span>

1. <span data-ttu-id="f9c61-118">Recherchez la section **Octroi implicite** , puis activez **Jetons d’ID**.</span><span class="sxs-lookup"><span data-stu-id="f9c61-118">Locate the **Implicit grant** section and enable **ID tokens**.</span></span> <span data-ttu-id="f9c61-119">Sélectionnez **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="f9c61-119">Select **Save**.</span></span>

    ![Capture d’écran des paramètres de plateforme Web dans le portail Azure](./images/aad-web-platform.png)

1. <span data-ttu-id="f9c61-121">Sélectionnez **Certificats et secrets** sous **Gérer**.</span><span class="sxs-lookup"><span data-stu-id="f9c61-121">Select **Certificates & secrets** under **Manage**.</span></span> <span data-ttu-id="f9c61-122">Sélectionnez le bouton **Nouveau secret client**.</span><span class="sxs-lookup"><span data-stu-id="f9c61-122">Select the **New client secret** button.</span></span> <span data-ttu-id="f9c61-123">Entrez une valeur dans **Description** , sélectionnez une des options pour **Expire le** , puis sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="f9c61-123">Enter a value in **Description** and select one of the options for **Expires** and select **Add**.</span></span>

    ![Une capture d’écran de la boîte de dialogue Ajouter une clé secrète client](./images/aad-new-client-secret.png)

1. <span data-ttu-id="f9c61-125">Copiez la valeur due la clé secrète client avant de quitter cette page.</span><span class="sxs-lookup"><span data-stu-id="f9c61-125">Copy the client secret value before you leave this page.</span></span> <span data-ttu-id="f9c61-126">Vous en aurez besoin à l’étape suivante.</span><span class="sxs-lookup"><span data-stu-id="f9c61-126">You will need it in the next step.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="f9c61-127">Ce secret client n’apparaîtra plus jamais, aussi veillez à le copier maintenant.</span><span class="sxs-lookup"><span data-stu-id="f9c61-127">This client secret is never shown again, so make sure you copy it now.</span></span>

    ![Une capture d’écran de la clé secrète client nouvellement ajoutée](./images/aad-copy-client-secret.png)
