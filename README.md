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

## 3-Injection
Option 1: Use of Prepared Statements (with Parameterized Queries)
<br />
Option 2: Use of Properly Constructed Stored Procedures
<br />
Option 3: Allow-list Input Validation
<br />
Option 4: Escaping All User Supplied Input
<br />

### 3.1-Use of Prepared Statements
```
String query = "SELECT account_balance FROM user_data WHERE user_name = ?";
try {
  OleDbCommand command = new OleDbCommand(query, connection);
  command.Parameters.Add(new OleDbParameter("customerName", CustomerName Name.Text));
  OleDbDataReader reader = command.ExecuteReader();
  // …
} catch (OleDbException se) {
  // error handling
}
```

### 3.2-Use of Properly Constructed Stored Procedures

```
// This should REALLY be validated
String custname = request.getParameter("customerName");
try {
  CallableStatement cs = connection.prepareCall("{call sp_getAccountBalance(?)}");
  cs.setString(1, custname);
  ResultSet results = cs.executeQuery();
  // … result set handling
} catch (SQLException se) {
  // … logging and error handling
}
```

### 3.3-Allow-list Input Validation

```
String tableName;
switch(PARAM):
  case "Value1": tableName = "fooTable";
                 break;
  case "Value2": tableName = "barTable";
                 break;
  ...
  default      : throw new InputValidationException("unexpected value provided"
                                                  + " for table name");
```


```
public String someMethod(boolean sortOrder) {
 String SQLquery = "some SQL ... order by Salary " + (sortOrder ? "ASC" : "DESC");
```

### 3.4-Escaping All User Supplied Input

Each DBMS supports one or more character escaping schemes specific to certain kinds of queries. If you then escape all user supplied input using the proper escaping scheme for the database you are using, the DBMS will not confuse that input with SQL code written by the developer, thus avoiding any possible SQL injection vulnerabilities.

```
String query = "SELECT user_id FROM user_data WHERE user_name = '"
              + req.getParameter("userID")
              + "' and user_password = '" + req.getParameter("pwd") +"'";
try {
    Statement statement = connection.createStatement( … );
    ResultSet results = statement.executeQuery( query );
}
```


```
Codec ORACLE_CODEC = new OracleCodec();
String query = "SELECT user_id FROM user_data WHERE user_name = '"
+ ESAPI.encoder().encodeForSQL( ORACLE_CODEC, req.getParameter("userID"))
+ "' and user_password = '"
+ ESAPI.encoder().encodeForSQL( ORACLE_CODEC, req.getParameter("pwd")) +"'";
```

## 4-Insecure Design

### Security Principles

### 4.1-The principle of Least Privilege and Separation of Duties
### 4.2-The principle of Defense-in-Depth¶
### 4.3-The principle of Zero Trust¶
### 4.4-The principle of Security-in-the-Open¶


## 5-Security Misconfiguration

### 5.1-Debug and Stack Trace

```
<compilation xdt:Transform="RemoveAttributes(debug)" />
<trace enabled="false" xdt:Transform="Replace"/>
```

### 5.2-Cross-site request forgery

DO NOT: Send sensitive data without validating Anti-Forgery-Tokens (.NET / .NET Core).

DO: Send the anti-forgery token with every POST/PUT request:

```
using (Html.BeginForm("LogOff", "Account", FormMethod.Post, new { id = "logoutForm",
                        @class = "pull-right" }))
{
    @Html.AntiForgeryToken()
    <ul class="nav nav-pills">
        <li role="presentation">
        Logged on as @User.Identity.Name
        </li>
        <li role="presentation">
        <a href="javascript:document.getElementById('logoutForm').submit()">Log off</a>
        </li>
    </ul>
}
```

Then validate it at the method or preferably the controller level:


```
[HttpPost]
[ValidateAntiForgeryToken]
public ActionResult LogOff(
```
## 6-Vulnerable and Outdated Components

DO: Keep the .NET framework updated with the latest patches

DO: Keep your NuGet packages up to date

DO: Run the OWASP Dependency Checker against your application as part of your build process and act on any high or critical level vulnerabilities.

DO: Include SCA (software composition analysis) tools in your CI/CD pipeline to ensure that any new vulnerabilities in your dependencies are detected and acted upon.

## 7-Identification and Authentication Failures

DO: Use ASP.NET Core Identity. ASP.NET Core Identity framework is well configured by default, where it uses secure password hashes and an individual salt. Identity uses the PBKDF2 hashing function for passwords, and generates a random salt per user.

DO: Set secure password policy

```
//Startup.cs
services.Configure<IdentityOptions>(options =>
{
 // Password settings
 options.Password.RequireDigit = true;
 options.Password.RequiredLength = 8;
 options.Password.RequireNonAlphanumeric = true;
 options.Password.RequireUppercase = true;
 options.Password.RequireLowercase = true;
 options.Password.RequiredUniqueChars = 6;

 options.Lockout.DefaultLockoutTimeSpan = TimeSpan.FromMinutes(30);
 options.Lockout.MaxFailedAccessAttempts = 3;

 options.SignIn.RequireConfirmedEmail = true;

 options.User.RequireUniqueEmail = true;
});
```

DO: Set a cookie policy



```
//Startup.cs
services.ConfigureApplicationCookie(options =>
{
 options.Cookie.HttpOnly = true;
 options.Cookie.Expiration = TimeSpan.FromHours(1)
 options.SlidingExpiration = true;
});
```
## 8-Software and Data Integrity Failures
DO: Digitally sign assemblies and executable files

DO: Use Nuget package signing

DO: Review code and configuration changes to avoid malicious code or dependencies being introduced

DO NOT: Send unsigned or unencrypted serialized objects over the network

DO: Perform integrity checks or validate digital signatures on serialized objects received from the network
## 9-Security Logging and Monitoring Failures

DO: Ensure all login, access control, and server-side input validation failures are logged with sufficient user context to identify suspicious or malicious accounts.

DO: Establish effective monitoring and alerting so suspicious activities are detected and responded to in a timely fashion.

DO NOT: Log generic error messages such as: csharp Log.Error("Error was thrown");. Instead, log the stack trace, error message and user ID who caused the error.

DO NOT: Log sensitive data such as user's passwords.

## 10-Server-Side Request Forgery

DO: Validate and sanitize all user input before using it to make a request

DO: Use an allow list of allowed protocols and domains

DO: Use IPAddress.TryParse() and Uri.CheckHostName() to ensure that IP addresses and domain names are valid

DO NOT: Follow HTTP redirects

DO NOT: Forward raw HTTP responses to the user
