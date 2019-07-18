* <span data-ttu-id="77250-101">Considerare attendibile il certificato di sviluppo HTTPS eseguendo il comando riportato di seguito:</span><span class="sxs-lookup"><span data-stu-id="77250-101">Trust the HTTPS development certificate by running the following command:</span></span>

    ```console
    dotnet dev-certs https --trust
    ```

* <span data-ttu-id="77250-102">Il comando precedente visualizza l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="77250-102">The preceding command displays the following output:</span></span>

    ```console
    Trusting the HTTPS development certificate was requested. If the certificate 
    is not already trusted we will run the following command:
    'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain 
    <<certificate>>'
    This command might prompt you for your password to install the certificate on the 
    system keychain.
    The HTTPS developer certificate was generated successfully.
    ```

* <span data-ttu-id="77250-103">Se richiesto, immettere il nome utente e la password dell'amministratore.</span><span class="sxs-lookup"><span data-stu-id="77250-103">Enter the admin username and password if prompted.</span></span>  <span data-ttu-id="77250-104">Il certificato verrà installato e impostato come attendibile.</span><span class="sxs-lookup"><span data-stu-id="77250-104">The certificate will now be installed and trusted.</span></span>

    <span data-ttu-id="77250-105">Per altre informazioni, vedere [Considerare attendibile il certificato di sviluppo di ASP.NET Core HTTPS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="77250-105">See [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) for more information.</span></span>