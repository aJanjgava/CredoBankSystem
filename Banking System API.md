# Banking System API
1. [Main Structure Of Architecture](#struct)
2. [Dependencies](#dependencies)
	1. [Packages](#packages)
	2. [Projects](#projects)
3. [Controllers](#controllers)
    1. [Internet Bank](#internetbank)
        1. [Login](#login)
        2. [Register User](#registeruser)
        3. [Register Bank Account](#registerbankaccount)
        4. [Register Card](#registercard)
        5. [Show All Bank Accounts](#showallbankaccounts)
        6. [Show All Cards](#showallcards)
        7. [Internal Transaction](#internaltransaction)
        8. [External Transaction](#externaltransaction)
    2. [ATM](#atm)
        1. [Show Balance](#showbalance)
        2. [Withdraw Money](#withdrawmoney)
        3. [Change Pin](#changepin)
    3. [Reports](#reports)
        1. [Number of Users Registered in Current Year](#currentyearusers)
        2. [Number of Users Registered in Last One Year](#lastyearusers)
        3. [Number of Users Registered in Last 30 Days](#last30daysusers)
        4. [Number of Transactions Made in Last 1 Month/6 Months/1 Year](#numberoftransactions)
        5. [Amount of Income Received From Transactions in the Last 1 Month/6 Months/1 Year (GEL/USD/EUR)](#amountofincome)
        6. [Average Revenue From One Transaction (GEL/USD/EUR)](#averagerevenue)
        7. [Number of Transactions in the Last Month by Day (chart)](#chart)
        8. [The Total Amount of Money Withdrawn From the ATM](#atmwithdraw)
4. [Dependency Injection](#dependency)
5. [Program](#program)


# Main Structure Of Architecture <div id="struct"/>
* Presentation Layer
* Business Layer
* Persistence Layer
* Database Layer


# Dependencies <div id="dependencies">

## Packages <div id="packages">
* IbanNet (5.8.0)
* Microsoft.AspNetCore.Authentication.JwtBearer (6.0.15)
* Microsoft.AspNetCore.Identity (2.2.0)
* Microsoft.AspNetCore.Identity.EntityFrameworkCore (6.0.15)
* Microsoft.AspNetCore.JsonPatch (6.0.15)
* Microsoft.AspNetCore.Mvc.NewtonsoftJson (6.0.15)
* Microsoft.EntityFrameworkCore (7.0.4)
* Microsoft.EntityFrameworkCore.Design (7.0.4)
* Microsoft.EntityFrameworkCore.SqlServe (7.0.4)
* Microsoft.EntityFrameworkCore.Tools (7.0.4)
* Newtonsoft.Json (13.0.3)


## Projects <div id="projects">
BankingSystem.API includes following projects:
* Services
* Repositories
* DB


#App Settings
In `appsettings.json` we configure following properties:
* Connection string for a SQL Server database
* JWT(Jason Web Token) which is a type of token used in authentication and authorization processes
    * `ValidAudience`: This specifies the audience for which the token is intended.
    * `ValidIssuer` : This specifies the entity that issued the token. In this case, the token was issued by a local server.
    * `Secret` : This is the secret key that is used to sign the JWT. This key is used to verify the authenticity of the token when it is presented.
    * `TokenValidityInMinutes` : This specifies the length of time, in minutes, that the JWT is valid for.
    * `RefreshTokenValidityInDays` : This specifies the length of time, in days, for which a refresh token is valid. A refresh token is used to obtain a new access token after the original access token has expired.

# Controllers <div id="controllers">

## Internet Bank <div id="internetbank">

```c#
[ApiController]
[Authorize(Roles = "Operator, User")]
public class InternetBankController : ControllerBase
```
This code suggests that the `InternetBankController` is an API controller that requires authentication and authorization for its endpoints. Specifically, only users who have the `Operator` or `User` roles will be able to access the controller's endpoints.

##

### Log In <div id="login">

```js
POST  api/InternetBank/login
```
Request

```json
{
"Email" : "operator@admin.com",
"Password" : "Operator123!"
}
```

Response
```js
OK 200 JWTToken
```

Method
```js
[HttpPost("login")]
[AllowAnonymous]
public async Task<IActionResult> Login([FromQuery]LoginDto login)
```

The `AllowAnonymous` attribute is used to indicate that this endpoint does not require authentication and authorization.

The method first checks whether the `ModelState` is valid by using the `!ModelState.IsValid` expression. If the model state is not valid, the method returns a `BadRequest` response.

The method itself accepts a query parameter named `login` of type `LoginDto`. This suggests that the method expects to receive login information as a query string in the request.

The method then checks whether the login was successful by using the `!loginResult.Succeeded` expression. If the login was not successful, the method returns a `BadRequest` response with an error message.

If login was successful, the method returns an `Ok` response with the JWT token as the result.

##

### Register User <div id="registeruser">

```js
POST  api/InternetBank/register_user
```
Request

```json
{
"FirstName" : "Barry",
"LastName" : "White",
"IDNumber" : "6503174398088",
"BirthDate" : "03/25/1990",
"EmailAddress" : "white@gmail.com",
"Password" : "White123!",
"ConfirmPassword" : "White123!",
}
```

Response
```js
OK 200 UserId
```

Method
```js
[HttpPost("register_user")]
[Authorize(Roles = "Operator")]
public async Task<IActionResult> RegisterUser([FromQuery] RegisterUserDto userRegistration)
```
The `Authorize(Roles = "Operator")` attribute is used to indicate that this endpoint requires authentication and authorization, and only users who have the `Operator` role will be able to access the endpoint.

The method itself accepts a query parameter named `userRegistration` of type `RegisterUserDto`. This suggests that the method expects to receive user registration information as a query string in the request.

The method first checks whether the `ModelState` is valid by using the `!ModelState.IsValid` expression. If the model state is not valid, the method returns a `BadRequest` response with an error message.

If the model state is valid, the method calls an instance of the `registerUserService` class to attempt to register the user by passing the `userRegistration` object as a parameter. 

The method then checks whether the registration was successful by using the `!registerUserResult.register.Succeeded` expression. If the registration was not successful, the method returns a `BadRequest` response with an error message.

If the registration was successful, the method returns an `Ok` response with a success message.

##

### Register Bank Account <div id="registerbankaccount">

```js
POST  api/InternetBank/register_bankAccount
```
Request

```json
{
"userId" : "1", 
"IBAN" : "GE60CD0000000123456789 GE60CD0000000123456744",
"Amount" : "100000 120500",
"Currency" : "GEL USD",
}
```

Response
```js
OK 200 BankAccountId
```

Method
```js
[HttpPost("register_bankAccount")]
[Authorize(Roles = "Operator")]
public async Task<IActionResult> RegisterBankAccount([Required]int userId,[FromQuery] RegisterBankAccountDto bankAccountRegistration)
```
The `Authorize(Roles = "Operator")` attribute is used to indicate that this endpoint requires authentication and authorization, and only users who have the `Operator` role will be able to access the endpoint.

The endpoint accepts two parameters: `userId` as a required integer and `bankAccountRegistration` as a query parameter of type `RegisterBankAccountDto`.

The method first checks whether the `ModelState` is valid by using the `!ModelState.IsValid` expression. If the model state is not valid, the method returns a `BadRequest` response with an error message.

If the model state is valid, the code calls the `_registerBankAccountService.RegisterBankAccount` method asynchronously, passing in the `userId` and `bankAccountRegistration` parameters. The result of the registration process is then checked. If the registration process fails, the code returns a `BadRequest` response with a message. If the registration process is successful, the code returns an `OK` response with a message.

##

### Register Card <div id="registercard">

```js
POST  api/InternetBank/register_card
```
Request

```json
{
"bankAccountId" : "1",
}
```

Response
```js
OK 200 CardId
```

Method
```js
[HttpPost("register_card")]
[Authorize(Roles = "Operator")]
public async Task<IActionResult> RegisterCard([Required] int bankAccountId)
```
The `Authorize(Roles = "Operator")` attribute is used to indicate that this endpoint requires authentication and authorization, and only users who have the `Operator` role will be able to access the endpoint.

The endpoint accepts parameter: `bankAccountId` as a required `integer`.

The method first checks whether the `ModelState` is valid by using the `!ModelState.IsValid` expression. If the model state is not valid, the method returns a `BadRequest` response with an error message.

If the model state is valid, the code calls the `_registerCardService.RegisterCard` method asynchronously, passing in the `bankAccountId` parameter. The result of the card registration process is then checked. If the card registration process fails, the code returns a `BadRequest` response with a message. If the card registration process is successful, the code returns an `OK` response with a message.

##

### Show All Bank Accounts <div id="showallbankaccounts">

```js
GET  api/InternetBank/show_all_bankAccounts
```
Request

```json

```

Response
```js
OK 200 
```

Register Method
```js
[HttpGet("show_all_bankAccounts")]
[Authorize(Roles = "User")]
public async Task<IActionResult> ShowBankAccounts()
```
The `Authorize(Roles = "User")` attribute is used to indicate that this endpoint requires authentication and authorization, and only users who have the `User` role will be able to access the endpoint.

The code first gets the authorized user ID from the HTTP context. It then calls the `_showBankAccountsService.ShowBankAccounts` method asynchronously, passing in the authorized user ID as a parameter. The result of the method call is stored in the `bankAccountsResult` variable.

If the `bankAccountsResult` deosn't contain any data, the code returns a `BadRequest` response with a message. If the `bankAccountsResult` contain any data, the code returns an `OK` response with the `bankAccountsResult` as the response body.

##

### Show All Cards <div id="showallcards">

```js
GET  api/InternetBank/show_all_cards
```
Request

```json

```

Response
```js
OK 200 
```

Register Method
```js
[HttpGet("show_all_cards")]
[Authorize(Roles = "User")]
public async Task<IActionResult> ShowCardsList()
```
The `Authorize(Roles = "User")` attribute is used to indicate that this endpoint requires authentication and authorization, and only users who have the `User` role will be able to access the endpoint.

The code first gets the authorized user ID from the HTTP context. It then calls the `_showCardsService.ShowCardsList` method asynchronously, passing in the authorized user ID as a parameter. The result of the method call is stored in the `result` variable.

If the `result` doesn't contain a data, the code returns a `BadRequest` response with a message. If the `result` contains a data, the code returns an `OK` response with the `result` as the response body.

##

### Internal Transaction <div id="internaltransaction">

```js
POST  api/InternetBank/internal_transaction
```
Request

```json
{
"SenderIBAN" : "GE60CD0000000123456789", 
"RecipientIBAN" : "GE60CD0000000123456744",
"Amount" : "1000",
}
```

Response
```js
OK 200 
```

Method
```js
 [HttpPost("internal_transaction")]
[Authorize(Roles = "User")]
public async Task<IActionResult> InternalTransaction([FromQuery] TransactionDto transaction)
```
The `Authorize(Roles = "User")` attribute is used to indicate that this endpoint requires authentication and authorization, and only users who have the `User` role will be able to access the endpoint.

The endpoint accepts a single parameter `transaction` as a query parameter of type `TransactionDto`.

The code first gets the authorized user ID from the HTTP context. It then calls the `_internalTransactionsService.InternalTransaction` method asynchronously, passing in the authorized user ID and the `transaction` parameter. The result of the transaction process is then checked. If the transaction process fails, the code returns a `BadRequest` response with a message. If the transaction process is successful, the code returns an `OK` response with a message.

##

### External Transaction <div id="externaltransaction">

```js
POST  api/InternetBank/external_transaction
```
Request

```json
{
"SenderIBAN" : "GE60CD0000000123456789", 
"RecipientIBAN" : "GE14NQ4500455829747533",
"Amount" : "1000",
}
```

Response
```js
OK 200 
```

Method
```js
[HttpPost("external_transaction")]
[Authorize(Roles = "User")]
public async Task<IActionResult> ExternalTransaction([FromQuery] TransactionDto transaction)
```
The `Authorize(Roles = "User")` attribute is used to indicate that this endpoint requires authentication and authorization, and only users who have the `User` role will be able to access the endpoint.

The endpoint accepts a single parameter `transaction` as a query parameter of type `TransactionDto`.

The code first gets the authorized user ID from the HTTP context. It then calls the `_externalTransactionsService.ExternalTransaction` method asynchronously, passing in the authorized user ID and the `transaction` parameter. The result of the transaction process is then checked. If the transaction process fails, the code returns a `BadRequest` response with a message. If the transaction process is successful, the code returns an `OK` response with a message.




## ATM <div id="atm">

### Show Balance <div id="showbalance">

```js
POST  api/ATM/show_balance
```
Request

```json
{
"CardNumber" : "1094545759020027", 
"PIN" : "2095",
}
```

Response
```js
OK 200 
```

Method
```js
[HttpPost("show_balance")]
public async Task<IActionResult> ShowBalance([FromQuery]CardDetailsDto cardDetails)
```
The endpoint is named `show_balance` and it accepts a query parameter `cardDetails` of type `CardDetailsDto`.

The method calls a service named `_showBalanceService` to retrieve the balance of a card using the `ShowBalance` method. The result of this operation is stored in the `showBalanceResult` variable.

If the `showBalanceResult.success` is false, the method returns a `BadRequest` response. Otherwise, the method returns an `OK` response.


##

### Withdraw Money <div id="withdrawmoney">

```js
POST  api/ATM/withdraw_money
```
Request

```json
{
"CardNumber" : "1094545759020027", 
"PIN" : "2095",
"amount" : "100",
}
```

Response
```js
OK 200 
```

Method
```js
public async Task<IActionResult> WithdrawMoney([FromQuery] CardDetailsDto cardDetails,[Required] double amount)
```
The endpoint is named `WithdrawMoney` and it accepts two parameters: `cardDetails` of type `CardDetailsDto` and `amount`.

The method calls a service named `_withdrawMoneyService` to perform the withdrawal operation using the `WithdrawMoney` method. The result of this operation is stored in the `withdrawMoneyResult` variable.

If the `withdrawMoneyResult` variable is null, the method returns a `BadRequest` response. Otherwise, the method returns an `OK` response.

##

### Change PIN <div id="changepin"> 

```js
PATCH  api/ATM/change_pin
```
Request

```json
{
"CardNumber" : "1094545759020027", 
"PIN" : "2095",
"newPIN" : "1234",
}
```

Response
```js
OK 200 
```

Method
```js
[HttpPatch("change_pin")] 
public async Task<IActionResult> ChangePIN([FromQuery]ChangePinDto changePin)
```
The endpoint is named `ChangePIN` and it accepts two parameters: `changePin` of type `ChangePinDto`.

The method calls a service named `_changePINService` to perform the PIN change operation using the `ChangePIN` method. The result of this operation is stored in the `changeCardPINResult` variable.

If the `changeCardPINResult.success` variable is false, the method returns a `BadRequest` response. Otherwise, the method returns an `OK` response.

## Reports <div id="reports">

### Number of Users Registered in Current Year <div id="currentyearusers">

```js
GET  api/Reports/current_year_registered_users
```
Request

```json

```

Response
```js
OK 200 
```

Method
```js
[HttpGet("current_year_registered_users")] public async Task<IActionResult> CurrentYearRegisteredUsers()
```
The method calls a service named `_currentYearRegisteredUsers` to retrieve the quantity of registered users for the current year using the `CurrentYearRegisteredUsersQuantity` method. The result of this operation is stored in the `currentYearRegisteredUsersResult` variable.

If the `currentYearRegisteredUsersResult` doesn't contain a data, the method returns a `BadRequest` response. Otherwise, the method returns an `OK` response.

##

### Number of Users Registered in Last One Year <div id="lastyearusers">

```js
GET  api/Reports/last_one_year_registered_users
```
Request

```json

```

Response
```js
OK 200 
```

Method
```js
[HttpGet("last_one_year_registered_users")]
public async Task<IActionResult> LastOneYearRegisteredUsers()
```
The method calls a service named `_lastOneYearRegisteredUsers` to retrieve the quantity of registered users for the last year using the `LastOneYearRegisteredUsersQuantity` method. The result of this operation is stored in the `lastOneYearRegisteredUsersResult` variable.

If the `lastOneYearRegisteredUsersResult` doesn't contain a data, the method returns a `BadRequest` response. Otherwise, the method returns an `OK` response.

##

### Number of Users Registered in Last 30 Days <div id="last30daysusers">

```js
GET  api/Reports/last_thirty_days_registered_users
```
Request

```json

```

Response
```js
OK 200 
```

Method
```js
[HttpGet("last_thirty_days_registered_users")]
public async Task<IActionResult> LastThirtyDaysRegisteredUsers()
```
The method calls a service named `_lastThirtyDaysRegisteredUsers` to retrieve the quantity of registered users for the last thirty days using the `LastThirtyDaysRegisteredUsersQuantity` method. The result of this operation is stored in the `lastThirtyDaysRegisteredUsersResult` variable.

If the `lastThirtyDaysRegisteredUsersResult` doesn't contain a data, the method returns a `BadRequest` response. Otherwise, the method returns an `OK` response.

##

### Number of Transactions Made in Last 1 Month/6 Months/1 Year <div id="numberoftransactions">

```js
GET  api/Reports/count_transactions
```
Request

```json

```

Response
```js
OK 200 
```

Method
```js
[HttpGet("count_transactions")]
public async Task<IActionResult> TransactionsDuringSomePeriodsOfTime()
```
The method calls a service named `_transactionsDuringSomePeriodOfTime` to retrieve transactions that occurred during some period of time using the `TransactionsDuringSomePeriodOfTime` method. The result of this operation is stored in the `transactionsDuringSomePeriodOfTimeResult` variable.

If the `transactionsDuringSomePeriodOfTimeResult` doesn't contain a data, the method returns a `BadRequest` response. Otherwise, the method returns an `OK` response.

##

### Amount of Income Received From Transactions in the Last 1 Month/6 Months/1 Year (GEL/USD/EUR) <div id="amountofincome">

```js
GET  api/Reports/income_amount_from_transactions_during_some_period_of_time
```
Request

```json

```

Response
```js
OK 200 
```

Method
```js
[HttpGet("income_amount_from_transactions_during_some_period_of_time")]
public async Task<IActionResult> IncomeAmountFromTransactionsDuringSomePeriodOfTime()
```
The method calls a service named `_incomeAmountFromTransactions` to retrieve the income amount from transactions that occurred during some period of time using the `IncomeAmountFromTransactionsDuringSomePeriodOfTime` method. The result of this operation is stored in the `IncomeAmountFromTransactionsDuringSomePeriodOfTimeResult` variable.

If the `IncomeAmountFromTransactionsDuringSomePeriodOfTimeResult` variable is null, the method returns a `BadRequest` response. Otherwise, the method returns an `OK` response.

##

### Average Revenue From One Transaction (GEL/USD/EUR) <div id="averagerevenue">

```js
GET  api/Reports/average_income
```
Request

```json

```

Response
```js
OK 200 
```

Method
```js
[HttpGet("average_income")]
public async Task<IActionResult> AverageIncome()
```
The method calls a service named `_averageIncomeService` to retrieve the average income using the `AverageIncome` method. The result of this operation is stored in the `averageIncomeResult` variable.

If the `averageIncomeResult` doesn't contain a data, the method returns a `BadRequest` response. Otherwise, the method returns an `OK` response.

##

### Number of Transactions in the Last Month by Day (chart) <div id="chart">

```js
GET  api/Reports/transactions_chart
```
Request

```json

```

Response
```js
OK 200 
```

Method
```js
[HttpGet("transactions_chart")]
public async Task<IActionResult> TransactionsChart()
```
The method calls a service named `_transactionsChartService` to retrieve data to generate a chart of transactions using the `TransactionsChart` method. The result of this operation is stored in the `transactionChartResult` variable.

If the `transactionChartResult` doesn't contain a data, the method returns a `BadRequest` response. Otherwise, the method returns an `OK` response.

##

### The Total Amount of Money Withdrawn From the ATM <div id="atmwithdraw">

```js
GET  api/Reports/total_withdrawal_amount_from_atm
```
Request

```json

```

Response
```js
OK 200 
```

Method
```js
[HttpGet("total_withdrawal_amount_from_atm")]
public async Task<IActionResult> TotalWithdrawalAmount()
```
The method calls a service named `_totalAmountWithdrawalAtmService` to retrieve the total amount of money withdrawn from ATMs using the `TotalWithdrawalAmount` method. The result of this operation is stored in the `totalWithdrawalResult` variable.

If the `totalWithdrawalResult` doesn't contain a data, the method returns a `BadRequest` response. Otherwise, the method returns an `OK` response.

##

# Dependency Injection <div id="dependency"/>

`DependencyForServices` and `DependencyForRepositories` are static classes that provide extension methods to the `IServiceCollection` interface, allowing for easy registration of services, repositories and middlewares in the application's dependency injection container. The `IServiceCollection` is the built-in container for managing services in .NET Core applications.

By registering these services and repositories with the container, they can be easily injected into other classes that depend on them, using constructor injection or method injection. This enables loose coupling between components and facilitates unit testing and maintenance of the code.

##

# Program <div id="program"/>

The Program.cs file is the entry point for the ASP.NET Core application. It is responsible for setting up the application and configuring various components.

* It creates an instance of the `WebApplication` class using the `CreateBuilder()` method.
* It configures the connection to the database by reading the connection string from the `appsettings.json` file and using it to create an instance of the AppDbContext class.
* It configures authentication and authorization using JWT tokens. It specifies the authentication scheme, validates the token, and sets the issuer, audience, and secret key for token validation.
* It adds services to the container by calling the `InjectServices()` and `InjectRepositories()` extension methods defined in the Services and Repositories namespaces, respectively.
* It adds controllers to the application by calling the `AddControllers()` method and adds a `JsonStringEnumConverter` to the `JsonSerializerOptions` for better serialization of enums.
* It adds support for Swagger/OpenAPI documentation by calling the `AddSwaggerGen()` method and configures the security scheme to use a Bearer token.
* It sets up the HTTP request pipeline by calling the `UseSwagger()`, `UseSwaggerUI()`, `UseHttpsRedirection()`, `UseAuthentication()`, `UseAuthorization()`, and `MapControllers()` methods, in that order.

##