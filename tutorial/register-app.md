<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="a4e18-101">Dans cet exercice, vous allez créer une application native Azure AD à l’aide du centre d’administration Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="a4e18-101">In this exercise you will create a new Azure AD native application using the Azure Active Directory admin center.</span></span>

1. <span data-ttu-id="a4e18-102">Ouvrez un navigateur, accédez au [Centre d’administration Azure Active Directory ](https://aad.portal.azure.com) et connectez-vous à l’aide d’un **compte personnel** (ou compte Microsoft) ou d’un **compte professionnel ou scolaire**.</span><span class="sxs-lookup"><span data-stu-id="a4e18-102">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com) and login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="a4e18-103">Sélectionnez **Azure Active Directory** dans le volet de navigation de gauche, puis sélectionnez **inscriptions des applications** sous **gérer**.</span><span class="sxs-lookup"><span data-stu-id="a4e18-103">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="a4e18-104">Capture d’écran des inscriptions d’application</span><span class="sxs-lookup"><span data-stu-id="a4e18-104">A screenshot of the App registrations</span></span> ](./images/aad-portal-app-registrations.png)

1. <span data-ttu-id="a4e18-105">Sélectionnez **Nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="a4e18-105">Select **New registration**.</span></span> <span data-ttu-id="a4e18-106">Sur la page **Inscrire une application**, définissez les valeurs comme suit.</span><span class="sxs-lookup"><span data-stu-id="a4e18-106">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="a4e18-107">Définissez le **Nom** sur `Xamarin Graph Tutorial`.</span><span class="sxs-lookup"><span data-stu-id="a4e18-107">Set **Name** to `Xamarin Graph Tutorial`.</span></span>
    - <span data-ttu-id="a4e18-108">Définissez les **Types de comptes pris en charge** sur **Comptes dans un annuaire organisationnel et comptes personnels Microsoft**.</span><span class="sxs-lookup"><span data-stu-id="a4e18-108">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="a4e18-109">Sous **URI de redirection (facultatif)**, remplacez la liste déroulante par **client public (mobile & Desktop)** et définissez `urn:ietf:wg:oauth:2.0:oob`la valeur sur.</span><span class="sxs-lookup"><span data-stu-id="a4e18-109">Under **Redirect URI (optional)**, change the dropdown to **Public client (mobile & desktop)**, and set the value to `urn:ietf:wg:oauth:2.0:oob`.</span></span>

    ![Capture d’écran de la page inscrire une application](./images/aad-register-an-app.png)

1. <span data-ttu-id="a4e18-111">Choisissez **Inscrire**.</span><span class="sxs-lookup"><span data-stu-id="a4e18-111">Choose **Register**.</span></span> <span data-ttu-id="a4e18-112">Sur la page **Xamarin Graph Tutorial** , copiez la valeur de l' **ID d’application (client)** et enregistrez-la, vous en aurez besoin à l’étape suivante.</span><span class="sxs-lookup"><span data-stu-id="a4e18-112">On the **Xamarin Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![Capture d’écran de l’ID d’application de la nouvelle inscription de l’application](./images/aad-application-id.png)

1. <span data-ttu-id="a4e18-114">Sélectionnez l' **authentification** sous **gérer**.</span><span class="sxs-lookup"><span data-stu-id="a4e18-114">Select the **Authentication** under **Manage**.</span></span> <span data-ttu-id="a4e18-115">Recherchez la section **URI de redirection suggérées pour les clients publics (mobile, bureau)** .</span><span class="sxs-lookup"><span data-stu-id="a4e18-115">Locate the **Suggested Redirect URIs for public clients (mobile, desktop)** section.</span></span> <span data-ttu-id="a4e18-116">Sélectionnez l’URI qui commence par `msal`, puis choisissez **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="a4e18-116">Select the URI that begins with `msal`, then choose **Save**.</span></span>

    ![Capture d’écran de la page des URI de redirection](./images/aad-redirect-uris.png)
