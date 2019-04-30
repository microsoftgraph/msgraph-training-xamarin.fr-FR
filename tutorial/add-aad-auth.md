<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="7d3a9-101">Dans cet exercice, vous allez étendre l'application de l'exercice précédent pour prendre en charge l'authentification avec Azure AD.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="7d3a9-102">Cela est nécessaire pour obtenir le jeton d'accès OAuth nécessaire pour appeler Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="7d3a9-103">Dans cette étape, vous allez intégrer la [bibliothèque d'authentification Microsoft pour .net (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) dans l'application.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-103">In this step you will integrate the [Microsoft Authentication Library for .NET (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) into the application.</span></span>

<span data-ttu-id="7d3a9-104">Dans **l'Explorateur de solutions**, développez le projet **GraphTutorial** , puis cliquez avec le bouton droit sur le dossier **modèles** .</span><span class="sxs-lookup"><span data-stu-id="7d3a9-104">In **Solution Explorer**, expand the **GraphTutorial** project and right-click the **Models** folder.</span></span> <span data-ttu-id="7d3a9-105">Choisissez **Ajouter une classe >.**... Nommez la `OAuthSettings` classe, puis choisissez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-105">Choose **Add > Class...**. Name the class `OAuthSettings` and choose **Add**.</span></span> <span data-ttu-id="7d3a9-106">Ouvrez le fichier **OAuthSettings.cs** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-106">Open the **OAuthSettings.cs** file and replace its contents with the following.</span></span>

```cs
namespace GraphTutorial.Models
{
    public static class OAuthSettings
    {
        public const string ApplicationId = "YOUR_APP_ID_HERE";
        public const string RedirectUri = "YOUR_REDIRECT_URI_HERE";
        public const string Scopes = "User.Read Calendars.Read";
    }
}
```

> [!IMPORTANT]
> <span data-ttu-id="7d3a9-107">Si vous utilisez le contrôle de code source tel que git, il est maintenant recommandé d'exclure le `OAuthSettings.cs` fichier du contrôle de code source afin d'éviter une fuite accidentelle de votre ID d'application.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-107">If you're using source control such as git, now would be a good time to exclude the `OAuthSettings.cs` file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="7d3a9-108">Mettre en œuvre la connexion</span><span class="sxs-lookup"><span data-stu-id="7d3a9-108">Implement sign-in</span></span>

<span data-ttu-id="7d3a9-109">Ouvrez le fichier **app.Xaml.cs** dans le projet **GraphTutorial** et ajoutez les instructions suivantes `using` en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-109">Open the **App.xaml.cs** file in the **GraphTutorial** project, and add the following `using` statements to the top of the file.</span></span>

```cs
using GraphTutorial.Models;
using Microsoft.Identity.Client;
using Microsoft.Graph;
using System.Diagnostics;
using System.Linq;
using System.Net.Http.Headers;
```

<span data-ttu-id="7d3a9-110">Ajoutez les propriétés suivantes à la `App` classe.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-110">Add the following properties to the `App` class.</span></span>

```cs
// UIParent used by Android version of the app
public static UIParent AuthUIParent = null;

// Microsoft Authentication client for native/mobile apps
public static PublicClientApplication PCA;

// Microsoft Graph client
public static GraphServiceClient GraphClient;
```

<span data-ttu-id="7d3a9-111">Ensuite, créez un nouveau `PublicClientApplication` dans le constructeur de la `App` classe.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-111">Next, create a new `PublicClientApplication` in the constructor of the `App` class.</span></span>

```cs
public App()
{
    InitializeComponent();

    PCA = new PublicClientApplication(OAuthSettings.ApplicationId);

    MainPage = new MainPage();
}
```

<span data-ttu-id="7d3a9-112">À présent, `SignIn` mettez à jour la `PublicClientApplication` fonction pour utiliser l'pour obtenir un jeton d'accès.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-112">Now update the `SignIn` function to use the `PublicClientApplication` to get an access token.</span></span> <span data-ttu-id="7d3a9-113">Ajoutez le code suivant au- `await GetUserInfo();` dessus de la ligne.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-113">Add the following code above the `await GetUserInfo();` line.</span></span>

```cs
var scopes = OAuthSettings.Scopes.Split(' ');

// First, attempt silent sign in
// If the user's information is already in the app's cache,
// they won't have to sign in again.
try
{
    var accounts = await PCA.GetAccountsAsync();
    var silentAuthResult = await PCA.AcquireTokenSilentAsync(
        scopes, accounts.FirstOrDefault());

    Debug.WriteLine("User already signed in.");
    Debug.WriteLine($"Access token: {silentAuthResult.AccessToken}");
}
catch (MsalUiRequiredException)
{
    // This exception is thrown when an interactive sign-in is required.
    // Prompt the user to sign-in
    var authResult = await PCA.AcquireTokenAsync(scopes, AuthUIParent);
    Debug.WriteLine($"Access Token: {authResult.AccessToken}");
}
```

<span data-ttu-id="7d3a9-114">Ce code tente d'abord d'obtenir un jeton d'accès en mode silencieux.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-114">This code first attempts to get an access token silently.</span></span> <span data-ttu-id="7d3a9-115">Si les informations d'un utilisateur figurent déjà dans le cache de l'application (par exemple, si l'utilisateur a fermé l'application précédemment sans se déconnecter), cette opération réussit et il n'y a aucune raison d'inviter l'utilisateur.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-115">If a user's information is already in the app's cache (for example, if the user closed the app previously without signing out), this will succeed, and there's no reason to prompt the user.</span></span> <span data-ttu-id="7d3a9-116">S'il n'y a pas d'informations d'utilisateur dans le cache `AcquireTokenSilentAsync` , la fonction génère `MsalUiRequiredException`un.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-116">If there is not a user's information in the cache, the `AcquireTokenSilentAsync` function throws an `MsalUiRequiredException`.</span></span> <span data-ttu-id="7d3a9-117">Dans ce cas, le code appelle la fonction interactive pour obtenir un jeton, `AcquireTokenAsync`.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-117">In this case, the code calls the interactive function to get a token, `AcquireTokenAsync`.</span></span>

<span data-ttu-id="7d3a9-118">À présent, `SignOut` mettez à jour la fonction pour supprimer les informations de l'utilisateur du cache.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-118">Now update the `SignOut` function to remove the user's information from the cache.</span></span> <span data-ttu-id="7d3a9-119">Ajoutez le code suivant au début de la `SignOut` fonction.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-119">Add the following code to the beginning of the `SignOut` function.</span></span>

```cs
// Get all cached accounts for the app
// (Should only be one)
var accounts = await PCA.GetAccountsAsync();
while (accounts.Any())
{
    // Remove the account info from the cache
    await PCA.RemoveAsync(accounts.First());
    accounts = await PCA.GetAccountsAsync();
}
```

<span data-ttu-id="7d3a9-120">Enfin, mettez à `MainPage` jour la classe pour vous connecter au chargement.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-120">Finally, update the `MainPage` class to sign-in when it loads.</span></span> <span data-ttu-id="7d3a9-121">Ajoutez la fonction suivante à la `MainPage` classe dans **MainPage.Xaml.cs**.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-121">Add the following function to the `MainPage` class in **MainPage.xaml.cs**.</span></span>

```cs
protected override async void OnAppearing()
{
    base.OnAppearing();
    await (Application.Current as App).SignIn();
}
```

### <a name="update-android-project-to-enable-sign-in"></a><span data-ttu-id="7d3a9-122">Mettre à jour le projet Android pour activer la connexion</span><span class="sxs-lookup"><span data-stu-id="7d3a9-122">Update Android project to enable sign-in</span></span>

<span data-ttu-id="7d3a9-123">Lorsqu'elle est utilisée dans un projet Android Xamarin, la bibliothèque d'authentification Microsoft a quelques [exigences spécifiques à Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics).</span><span class="sxs-lookup"><span data-stu-id="7d3a9-123">When used in a Xamarin Android project, the Microsoft Authentication Library has a few [requirements specific to Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics).</span></span>

<span data-ttu-id="7d3a9-124">Tout d'abord, vous devez ajouter l'URI de redirection à partir de l'inscription de votre application dans le manifeste Android.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-124">First, you need to add the redirect URI from your app registration to the Android manifest.</span></span> <span data-ttu-id="7d3a9-125">Pour ce faire, ajoutez une nouvelle activité au projet **GraphTutorial. Android** .</span><span class="sxs-lookup"><span data-stu-id="7d3a9-125">To do this, add a new activity to the **GraphTutorial.Android** project.</span></span> <span data-ttu-id="7d3a9-126">Cliquez avec le bouton droit sur **GraphTutorial. Android** , puis choisissez **Ajouter**, puis **nouvel élément..**.. Sélectionnez **activité**, nommez l' `MsalActivity`activité, puis choisissez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-126">Right-click the **GraphTutorial.Android** and choose **Add**, then **New Item...**. Choose **Activity**, name the activity `MsalActivity`, and choose **Add**.</span></span>

<span data-ttu-id="7d3a9-127">Ouvrez le fichier **MsalActivity.cs** et supprimez `[Activity(Label = "MsalActivity")]` la ligne, puis ajoutez les attributs suivants au-dessus de la déclaration de classe.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-127">Open the **MsalActivity.cs** file and delete the `[Activity(Label = "MsalActivity")]` line, then add the following attributes above the class declaration.</span></span>

```cs
// This class only exists to create the necessary activity in the Android
// manifest. Doing it this way allows the value of the RedirectUri constant
// to be inserted at build.
[Activity(Name = "microsoft.identity.client.BrowserTabActivity")]
[IntentFilter(new[] { Intent.ActionView },
    Categories = new[] { Intent.CategoryDefault, Intent.CategoryBrowsable },
    DataScheme = Models.OAuthSettings.RedirectUri, DataHost = "auth")]
```

<span data-ttu-id="7d3a9-128">Ensuite, ouvrez **MainActivity.cs** et ajoutez les instructions `using` suivantes en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-128">Next, open **MainActivity.cs** and add the following `using` statements to the top of the file.</span></span>

```cs
using Microsoft.Identity.Client;
using Android.Content;
```

<span data-ttu-id="7d3a9-129">Ensuite, substituez la `OnActivityResult` fonction pour transmettre le contrôle à la bibliothèque MSAL.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-129">Then, override the `OnActivityResult` function to pass control to the MSAL library.</span></span> <span data-ttu-id="7d3a9-130">Ajoutez les éléments suivants à `MainActivity` la classe.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-130">Add the following to the `MainActivity` class.</span></span>

```cs
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    AuthenticationContinuationHelper
        .SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

### <a name="update-ios-project-to-enable-sign-in"></a><span data-ttu-id="7d3a9-131">Mettre à jour le projet iOS pour activer la connexion</span><span class="sxs-lookup"><span data-stu-id="7d3a9-131">Update iOS project to enable sign-in</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7d3a9-132">Étant donné que MSAL nécessite l'utilisation d'un fichier habilitations. plist, vous devez configurer Visual Studio avec votre compte de développeur Apple pour activer la mise en service.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-132">Because MSAL requires use of an Entitlements.plist file, you must configure Visual Studio with your Apple developer account to enable provisioning.</span></span> <span data-ttu-id="7d3a9-133">Pour plus d'informations, consultez la rubrique [mise en service de l'appareil pour Xamarin. iOS](/xamarin/ios/get-started/installation/device-provisioning).</span><span class="sxs-lookup"><span data-stu-id="7d3a9-133">For more information, see [Device provisioning for Xamarin.iOS](/xamarin/ios/get-started/installation/device-provisioning).</span></span>

<span data-ttu-id="7d3a9-134">Lorsqu'elle est utilisée dans un projet iOS Xamarin, la bibliothèque d'authentification Microsoft a quelques [exigences spécifiques à IOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics).</span><span class="sxs-lookup"><span data-stu-id="7d3a9-134">When used in a Xamarin iOS project, the Microsoft Authentication Library has a few [requirements specific to iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics).</span></span>

<span data-ttu-id="7d3a9-135">Tout d'abord, vous devez activer l'accès à la chaîne de trousseau.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-135">First, you need to enable Keychain access.</span></span> <span data-ttu-id="7d3a9-136">Dans l'Explorateur de solutions, développez le projet **GraphTutorial. iOS** , puis ouvrez le fichier **habilitations. plist** .</span><span class="sxs-lookup"><span data-stu-id="7d3a9-136">In Solution Explorer, expand the **GraphTutorial.iOS** project, then open the **Entitlements.plist** file.</span></span> <span data-ttu-id="7d3a9-137">Localisez le **trousseau** d'autorisations, puis sélectionnez **activer la chaîne de trousseau**.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-137">Locate the **Keychain** entitlement, and select **Enable KeyChain**.</span></span> <span data-ttu-id="7d3a9-138">Dans les **groupes de trousseau**, ajoutez une entrée au format `com.YOUR_DOMAIN.GraphTutorial`.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-138">In **Keychain Groups**, add an entry with the format `com.YOUR_DOMAIN.GraphTutorial`.</span></span>

![Capture d'écran de la configuration de la habilitation de trousseau](./images/enable-keychain-access.png)

<span data-ttu-id="7d3a9-140">Ensuite, vous devez enregistrer l'URI de redirection que vous avez configuré dans l'étape d'inscription de l'application en tant que type d'URL géré par votre application.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-140">Next, you need to register the redirect URI you configured in the app registration step as a URL type that your app handles.</span></span> <span data-ttu-id="7d3a9-141">Ouvrez le fichier **info. plist** et effectuez les modifications suivantes.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-141">Open the **Info.plist** file and make the following changes.</span></span>

- <span data-ttu-id="7d3a9-142">Dans l'onglet **application** , vérifiez que la valeur de l' **identificateur groupé** correspond à la valeur que vous avez définie pour les **groupes de trousseau** dans les habilitations **. plist**.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-142">On the **Application** tab, check that the value of **Bundle identifier** matches the value you set for **Keychain Groups** in **Entitlements.plist**.</span></span> <span data-ttu-id="7d3a9-143">Si ce n'est pas le cas, mettez-le à jour maintenant.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-143">If it doesn't, update it now.</span></span>
- <span data-ttu-id="7d3a9-144">Sous l'onglet **avancé** , recherchez la section **types d'URL** .</span><span class="sxs-lookup"><span data-stu-id="7d3a9-144">On the **Advanced** tab, locate the **URL Types** section.</span></span> <span data-ttu-id="7d3a9-145">Ajoutez un type d'URL ici avec les valeurs suivantes:</span><span class="sxs-lookup"><span data-stu-id="7d3a9-145">Add a URL type here with the following values:</span></span>
    - <span data-ttu-id="7d3a9-146">**Identificateur**: défini sur la valeur de votre **identificateur de package**</span><span class="sxs-lookup"><span data-stu-id="7d3a9-146">**Identifier**: set to the value of your **Bundle identifier**</span></span>
    - <span data-ttu-id="7d3a9-147">**Schémas d'URL**: définissez sur l'URI de redirection à partir de l'inscription de votre application qui commence par`msal`</span><span class="sxs-lookup"><span data-stu-id="7d3a9-147">**URL Schemes**: set to the redirect URI from your app registration that begins with `msal`</span></span>
    - <span data-ttu-id="7d3a9-148">**Rôle**:`Editor`</span><span class="sxs-lookup"><span data-stu-id="7d3a9-148">**Role**: `Editor`</span></span>
    - <span data-ttu-id="7d3a9-149">**Icon**: laisser vide</span><span class="sxs-lookup"><span data-stu-id="7d3a9-149">**Icon**: Leave empty</span></span>

![Capture d'écran de la section types d'URL de info. plist](./images/add-url-type.png)

<span data-ttu-id="7d3a9-151">Enfin, mettez à jour le code dans le projet **GraphTutorial. iOS** pour gérer la redirection lors de l'authentification.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-151">Finally, update the code in the **GraphTutorial.iOS** project to handle the redirect during authentication.</span></span> <span data-ttu-id="7d3a9-152">Ouvrez le fichier **AppDelegate.cs** et ajoutez l'instruction `using` suivante en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-152">Open the **AppDelegate.cs** file and add the following `using` statement at the top of the file.</span></span>

```cs
using Microsoft.Identity.Client;
```

<span data-ttu-id="7d3a9-153">Ajoutez la ligne suivante pour `FinishedLaunching` fonctionner juste avant l' `return` instruction.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-153">Add the following line to `FinishedLaunching` function just before the `return` statement.</span></span>

```cs
// Specify the Keychain access group
App.PCA.iOSKeychainSecurityGroup = "com.graphdevx.GraphTutorial";
```

<span data-ttu-id="7d3a9-154">Enfin, remplacez la `OpenUrl` fonction pour transmettre l'URL à la bibliothèque MSAL.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-154">Finally, override the `OpenUrl` function to pass the URL to the MSAL library.</span></span> <span data-ttu-id="7d3a9-155">Ajoutez les éléments suivants à `AppDelegate` la classe.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-155">Add the following to the `AppDelegate` class.</span></span>

```cs
// Handling redirect URL
// See: https://github.com/azuread/microsoft-authentication-library-for-dotnet/wiki/Xamarin-iOS-specifics
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

## <a name="storing-the-tokens"></a><span data-ttu-id="7d3a9-156">Stockage des jetons</span><span class="sxs-lookup"><span data-stu-id="7d3a9-156">Storing the tokens</span></span>

<span data-ttu-id="7d3a9-157">Lorsque la bibliothèque d'authentification Microsoft est utilisée dans un projet Xamarin, elle tire parti du stockage sécurisé natif pour mettre en cache les jetons par défaut.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-157">When the Microsoft Authentication Library is used in a Xamarin project, it takes advantage of the native secure storage to cache tokens by default.</span></span> <span data-ttu-id="7d3a9-158">Pour plus d'informations, consultez [la rubrique sérialisation du cache de jetons](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) .</span><span class="sxs-lookup"><span data-stu-id="7d3a9-158">See [Token cache serialization](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) for more information.</span></span>

## <a name="test-sign-in"></a><span data-ttu-id="7d3a9-159">Tester la connexion</span><span class="sxs-lookup"><span data-stu-id="7d3a9-159">Test sign-in</span></span>

<span data-ttu-id="7d3a9-160">À ce stade, si vous exécutez l'application et que vous appuyez sur le bouton **de connexion** , vous êtes invité à vous connecter.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-160">At this point if you run the application and tap the **Sign in** button, you are prompted to sign in.</span></span> <span data-ttu-id="7d3a9-161">Lors de la connexion, le jeton d'accès doit s'afficher dans la sortie du débogueur.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-161">On successful sign in, you should see the access token printed into the debugger's output.</span></span>

![Capture d'écran de la fenêtre sortie dans Visual Studio](./images/debugger-access-token.png)

## <a name="get-user-details"></a><span data-ttu-id="7d3a9-163">Obtenir les détails de l'utilisateur</span><span class="sxs-lookup"><span data-stu-id="7d3a9-163">Get user details</span></span>

<span data-ttu-id="7d3a9-164">À présent, `SignIn` mettez à jour la fonction dans **app.Xaml.cs** pour initialiser le `GraphServiceClient`.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-164">Now update the `SignIn` function in **App.xaml.cs** to initialize the `GraphServiceClient`.</span></span> <span data-ttu-id="7d3a9-165">Ajoutez le code suivant avant la `await GetUserInfo();` ligne.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-165">Add the following code before the `await GetUserInfo();` line.</span></span>

```cs
// Initialize Graph client
GraphClient = new GraphServiceClient(new DelegateAuthenticationProvider(
    async (requestMessage) =>
    {
        var accounts = await PCA.GetAccountsAsync();

        var result = await PCA.AcquireTokenSilentAsync(scopes, accounts.FirstOrDefault());

        requestMessage.Headers.Authorization =
            new AuthenticationHeaderValue("Bearer", result.AccessToken);
    }));
```

<span data-ttu-id="7d3a9-166">À présent, `GetUserInfo` mettez à jour la fonction pour obtenir les détails de l'utilisateur à partir de Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-166">Now update the `GetUserInfo` function to get the user's details from the Microsoft Graph.</span></span> <span data-ttu-id="7d3a9-167">Remplacez la fonction `GetUserInfo` existante par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-167">Replace the existing `GetUserInfo` function with the following.</span></span>

```cs
private async Task GetUserInfo()
{
    // Get the logged on user's profile (/me)
    var user = await GraphClient.Me.Request().GetAsync();

    UserPhoto = ImageSource.FromStream(() => GetUserPhoto());
    UserName = user.DisplayName;
    UserEmail = string.IsNullOrEmpty(user.Mail) ? user.UserPrincipalName : user.Mail;
}
```

<span data-ttu-id="7d3a9-168">Si vous enregistrez vos modifications et exécutez l'application maintenant, une fois connecté, l'interface utilisateur est mise à jour avec le nom d'affichage et l'adresse de messagerie de l'utilisateur.</span><span class="sxs-lookup"><span data-stu-id="7d3a9-168">If you save your changes and run the app now, after sign-in the UI is updated with the user's display name and email address.</span></span>

