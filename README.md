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

## 2-Cryptographic Failures

Cryptographic failures are failures related to cryptography (or lack thereof) that often leads to sensitive data exposure or system compromise.

Implementation mistakes leading to unexpected cryptographic errors. Firstly, let's being with distinguishing between encoding, encryption and hashing in general programming terms.

Encoding a value helps transmit data in a channel, such as base 64 encoding over HTTP, but doesn't provide security. It changes the format of a value so that it obstructs but not protects the value. 

Encryption is a reversible operation that translates text into what may seem a random and meaningless cypher. To decrypt the value an encryption key is needed. 

Hashing is a one-way operation of mapping input data into fixed-size values (value hash). There's no way to reverse hash a value.

### 2.1-Encryption
```
// Using Advanced Encryption Standard class
// to create a key and Initialization Vector
// to encrypt any type of managed stream, then the stream is wrapped with CryptoStream.

Aes aes = Aes.Create();​
CryptoStream cryptStream = new CryptoStream(fileStream,
                                           ​aes.CreateEncryptor(aes.Key, aes.VI),​
                                           CryptoStreamMode. Write);
```

### 2.2-Hashing
```
public static byte[] HashPassword256(string password)​
{​
    System.Security.Cryptography.SHA256 mySHA256 = System.Security.Cryptography.SHA256.Create();​
    var encoding = new System.Text.UnicodeEncoding();​
    return mySHA256.ComputeHash(encoding.GetBytes(password));​
}
```