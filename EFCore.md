To get all accounts and include the customer name field, you can modify your repository and controller to fetch and return the necessary data. Here's how you can do it:

First, ensure your models are set up correctly:

```csharp
public class Account
{
    public int Id { get; set; }
    public string AccountNumber { get; set; }
    public decimal Balance { get; set; }
    public int CustomerId { get; set; }
    public Customer Customer { get; set; }
}

public class Customer
{
    public int Id { get; set; }
    public string Name { get; set; }
    public List<Account> Accounts { get; set; }
}
```

Next, create an interface for your repository:

```csharp
public interface IAccountRepository
{
    Task<IEnumerable<Account>> GetAllAccountsAsync();
}
```

Then, implement the repository:

```csharp
public class AccountRepository : IAccountRepository
{
    private readonly YourDbContext _context;

    public AccountRepository(YourDbContext context)
    {
        _context = context;
    }

    public async Task<IEnumerable<Account>> GetAllAccountsAsync()
    {
        return await _context.Accounts
            .Include(a => a.Customer)
            .ToListAsync();
    }
}
```

Now, create the controller:

```csharp
[Route("api/[controller]")]
[ApiController]
public class AccountsController : ControllerBase
{
    private readonly IAccountRepository _accountRepository;

    public AccountsController(IAccountRepository accountRepository)
    {
        _accountRepository = accountRepository;
    }

    [HttpGet]
    public async Task<IActionResult> GetAllAccounts()
    {
        var accounts = await _accountRepository.GetAllAccountsAsync();

        var result = accounts.Select(a => new 
        {
            a.Id,
            a.AccountNumber,
            a.Balance,
            CustomerName = a.Customer.Name
        });

        return Ok(result);
    }
}
```

In this example:
- The `AccountRepository` class implements the `IAccountRepository` interface and uses Entity Framework Core to fetch all accounts along with the customer information.
- The `AccountsController` class has a `GetAllAccounts` method that retrieves all accounts with the customer name using the repository and returns them.

Make sure to register your repository in the `Startup.cs` or `Program.cs` file:

```csharp
services.AddScoped<IAccountRepository, AccountRepository>();
```

This setup will allow you to retrieve all accounts along with the customer's name through your API. Let me know if you need any further assistance!

To get all accounts and include the customer name field, you can modify your repository and controller to fetch and return the necessary data. Here's how you can do it:

First, ensure your models are set up correctly:

```csharp
public class Account
{
    public int Id { get; set; }
    public string AccountNumber { get; set; }
    public decimal Balance { get; set; }
    public int CustomerId { get; set; }
    public Customer Customer { get; set; }
}

public class Customer
{
    public int Id { get; set; }
    public string Name { get; set; }
    public List<Account> Accounts { get; set; }
}
```

Next, create an interface for your repository:

```csharp
public interface IAccountRepository
{
    Task<IEnumerable<Account>> GetAllAccountsAsync();
}
```

Then, implement the repository:

```csharp
public class AccountRepository : IAccountRepository
{
    private readonly YourDbContext _context;

    public AccountRepository(YourDbContext context)
    {
        _context = context;
    }

    public async Task<IEnumerable<Account>> GetAllAccountsAsync()
    {
        return await _context.Accounts
            .Include(a => a.Customer)
            .ToListAsync();
    }
}
```

Now, create the controller:

```csharp
[Route("api/[controller]")]
[ApiController]
public class AccountsController : ControllerBase
{
    private readonly IAccountRepository _accountRepository;

    public AccountsController(IAccountRepository accountRepository)
    {
        _accountRepository = accountRepository;
    }

    [HttpGet]
    public async Task<IActionResult> GetAllAccounts()
    {
        var accounts = await _accountRepository.GetAllAccountsAsync();

        var result = accounts.Select(a => new 
        {
            a.Id,
            a.AccountNumber,
            a.Balance,
            CustomerName = a.Customer.Name
        });

        return Ok(result);
    }
}
```

In this example:
- The `AccountRepository` class implements the `IAccountRepository` interface and uses Entity Framework Core to fetch all accounts along with the customer information.
- The `AccountsController` class has a `GetAllAccounts` method that retrieves all accounts with the customer name using the repository and returns them.

Make sure to register your repository in the `Startup.cs` or `Program.cs` file:

```csharp
services.AddScoped<IAccountRepository, AccountRepository>();
```

This setup will allow you to retrieve all accounts along with the customer's name through your API. Let me know if you need any further assistance!

To get all accounts and include the customer name field, you can modify your repository and controller to fetch and return the necessary data. Here's how you can do it:

First, ensure your models are set up correctly:

```csharp
public class Account
{
    public int Id { get; set; }
    public string AccountNumber { get; set; }
    public decimal Balance { get; set; }
    public int CustomerId { get; set; }
    public Customer Customer { get; set; }
}

public class Customer
{
    public int Id { get; set; }
    public string Name { get; set; }
    public List<Account> Accounts { get; set; }
}
```

Next, create an interface for your repository:

```csharp
public interface IAccountRepository
{
    Task<IEnumerable<Account>> GetAllAccountsAsync();
}
```

Then, implement the repository:

```csharp
public class AccountRepository : IAccountRepository
{
    private readonly YourDbContext _context;

    public AccountRepository(YourDbContext context)
    {
        _context = context;
    }

    public async Task<IEnumerable<Account>> GetAllAccountsAsync()
    {
        return await _context.Accounts
            .Include(a => a.Customer)
            .ToListAsync();
    }
}
```

Now, create the controller:

```csharp
[Route("api/[controller]")]
[ApiController]
public class AccountsController : ControllerBase
{
    private readonly IAccountRepository _accountRepository;

    public AccountsController(IAccountRepository accountRepository)
    {
        _accountRepository = accountRepository;
    }

    [HttpGet]
    public async Task<IActionResult> GetAllAccounts()
    {
        var accounts = await _accountRepository.GetAllAccountsAsync();

        var result = accounts.Select(a => new 
        {
            a.Id,
            a.AccountNumber,
            a.Balance,
            CustomerName = a.Customer.Name
        });

        return Ok(result);
    }
}
```

In this example:
- The `AccountRepository` class implements the `IAccountRepository` interface and uses Entity Framework Core to fetch all accounts along with the customer information.
- The `AccountsController` class has a `GetAllAccounts` method that retrieves all accounts with the customer name using the repository and returns them.

Make sure to register your repository in the `Startup.cs` or `Program.cs` file:

```csharp
services.AddScoped<IAccountRepository, AccountRepository>();
```

This setup will allow you to retrieve all accounts along with the customer's name through your API. Let me know if you need any further assistance!
