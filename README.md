# Top Ten OWASP in software security

## 1-Broken Access Control
## 2-Cryptographic Failures
## 3-Injection
## 4-Insecure Design
## 5-Security Misconfiguration
## 6-Vulnerable and Outdated Components
## 7-Identification and Authentication Failures
## 8-Software and Data Integrity Failures
## 9-Security Logging and Monitoring Failures
## 10-Server-Side Request Forgery

<br />
<br />

## 1-Broken Access Control
<br />

Plain ASP.NET controller with no authorization attributes, no access restrictions applied.
<br/>

```
public class AccountController : Controller​
{​
    public ActionResult Login()​
    {
    }

    public ActionResult Logout()​
    {
    }

    public ActionResult<string> GetAccountBalance()​
    {
    }
}
```
<br/>

Controller with authorization attributes, based on policy or role assignments. Authorized caller is able to invoke the GetCitizenTaxId method.

```
[Authorize(Policy="", Roles="")]​
public class AccountController : Controller​
{​
    [AllowAnonymous]​
    public ActionResult Login()​            
    {
    }

    public ActionResult Logout()​            
    {
    }

    [Authorize]
    public ActionResult<string> GetAccountBalance()​
    {
    }
}​
```

```
<AuthorizeView Roles="admin, superuser">​
    <Authorized>​
        <h1>Hello, @context.User.Identity.Name!</h1>​
        <p>You can only get account balance if you are authorized.</p>​
        <button @onclick="SecureMethod">Authorized Only Button</button>​
    </Authorized>​
    <NotAuthorized>​
        <h1>Authentication Failure!</h1>​
        <p>You are not signed in.</p>​
    </NotAuthorized>​
</AuthorizeView>​

@code { ​

    private void SecureMethod() ​
    { ​
        // Invoked only upon successful authorization
    }    ​ 
}
```