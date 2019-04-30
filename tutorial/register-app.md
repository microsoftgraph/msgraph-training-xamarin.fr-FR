<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez créer une application native Azure AD à l'aide du centre d'administration Azure Active Directory.

1. Ouvrez un navigateur, accédez au [Centre d’administration Azure Active Directory ](https://aad.portal.azure.com) et connectez-vous à l’aide d’un **compte personnel** (ou compte Microsoft) ou d’un **compte professionnel ou scolaire**.

1. Sélectionnez **Azure Active Directory** dans le volet de navigation gauche, puis **Inscriptions d’applications (préversion)** sous **Gérer**.

    ![Capture d'écran des inscriptions d'application ](./images/aad-portal-app-registrations.png)

1. Sélectionnez **Nouvelle inscription**. Sur la page **Inscrire une application**, définissez les valeurs comme suit.

    - Définissez le **Nom** sur `Xamarin Graph Tutorial`.
    - Définissez les **Types de comptes pris en charge** sur **Comptes dans un annuaire organisationnel et comptes personnels Microsoft**.
    - Laissez **Redirect URI** vide.

    ![Capture d'écran de la page inscrire une application](./images/aad-register-an-app.png)

1. Choisissez **Inscrire**. Sur la page **Xamarin Graph Tutorial** , copiez la valeur de l' **ID d'application (client)** et enregistrez-la, vous en aurez besoin à l'étape suivante.

    ![Capture d'écran de l'ID d'application de la nouvelle inscription de l'application](./images/aad-application-id.png)

1. Sélectionnez le lien **Ajouter un URI de redirection** . Sur la page **URI** de redirection, recherchez la section **URI de redirection suggérée pour les clients publics (mobile, bureau)** . Sélectionnez l'URI qui commence par `msal` **et** l'URI **urn: IETF: WG: OAuth: 2.0: OOB** . Copiez la valeur qui commence `msal`par, puis choisissez **Enregistrer**. Enregistrez l'URI de redirection copié, vous en aurez besoin à l'étape suivante.

    ![Capture d'écran de la page des URI de redirection](./images/aad-redirect-uris.png)