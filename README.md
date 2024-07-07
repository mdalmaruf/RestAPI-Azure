# Creating an ASP.NET Web API with Swagger and Azure SQL Database

## What is an API and Why is it Important?

An API (Application Programming Interface) is a set of rules and protocols that allows different software applications to communicate with each other. It defines the methods and data structures that developers can use to interact with the software components, operating systems, or other services.

### Importance of APIs
- **Interoperability**: APIs enable different systems to work together, allowing for the integration of various services and applications.
- **Efficiency**: By using APIs, developers can leverage existing functionalities, reducing the need to build features from scratch.
- **Scalability**: APIs allow applications to scale by integrating third-party services that handle complex operations.
- **Innovation**: APIs enable developers to create new applications and services by building on top of existing platforms.

### Example
Consider a weather application that needs to display current weather information. Instead of building a weather forecasting system from scratch, the app can use an API provided by a weather service like OpenWeatherMap. By making a request to the OpenWeatherMap API, the application can retrieve the necessary data and display it to the user.


## SOAP, REST, and Web API

| Feature                       | SOAP                                 | REST                                 | Web API                               |
|-------------------------------|--------------------------------------|--------------------------------------|---------------------------------------|
| **Protocol**                  | Protocol-based (SOAP)                | Architectural style                  | Framework for building HTTP services  |
| **Message Format**            | XML only                             | JSON, XML, HTML, plain text          | JSON, XML, etc.                       |
| **Transport Protocols**       | HTTP, SMTP, TCP                      | HTTP, HTTPS                          | HTTP, HTTPS                           |
| **Standards**                 | Highly standardized (WSDL, XSD)      | Less standardized, uses HTTP methods | Flexible, no strict standards         |
| **Operations**                | Uses service interfaces (WSDL)       | Uses standard HTTP methods (GET, POST, PUT, DELETE) | Uses HTTP methods (GET, POST, PUT, DELETE) |
| **Performance**               | Can be slower due to XML parsing     | Generally faster due to lightweight messages | Similar to REST, performance varies   |
| **Security**                  | Built-in security (WS-Security)      | Relies on HTTPS for security         | Relies on HTTPS, can use OAuth        |
| **Statefulness**              | Can be stateful or stateless         | Stateless                           | Stateless                             |
| **Error Handling**            | Uses SOAP fault                      | Uses HTTP status codes               | Uses HTTP status codes                |
| **Examples of Technologies**  | .NET (WCF), Java (JAX-WS)            | .NET (ASP.NET Web API), Node.js, Spring Boot | .NET (ASP.NET Core), Node.js, Flask   |

### Examples of Different Technologies

### SOAP
- **.NET (WCF)**: Windows Communication Foundation (WCF) is used for building SOAP-based services.
- **Java (JAX-WS)**: Java API for XML Web Services (JAX-WS) is used for creating SOAP web services in Java.

### REST
- **.NET (ASP.NET Web API)**: ASP.NET Web API is used for building RESTful services in .NET.
- **Node.js**: Express.js framework is commonly used to create RESTful APIs in Node.js.
- **Spring Boot**: Spring Boot is used to create RESTful web services in Java.

### Web API
- **.NET (ASP.NET Core)**: ASP.NET Core is a cross-platform framework for building modern web APIs.
- **Node.js**: Express.js is used for building web APIs in Node.js.
- **Flask**: Flask is a lightweight framework for building web APIs in Python.


## Creating an ASP.NET Web API with Swagger and Azure SQL Database

In this guide, we'll walk through the steps to create a RESTful Web API using ASP.NET Core, integrate Swagger for API documentation, connect to a SQL Server database on Microsoft Azure, and publish your project to Azure.

## Prerequisites

Before we begin, make sure you have the following:

- Visual Studio 2022 or later
- A Microsoft Azure student account
- SQL Server Management Studio (SSMS)

## Step 1: Setting Up Microsoft Azure Student Account

### Sign Up for Azure Student Account:
- Visit [Azure for Students](https://azure.microsoft.com/en-us/free/students/).
- Sign up with your student email to get free Azure credits and access to various Azure services.

## Step 2: Install SQL Server

### Download SQL Server:
- Visit the [SQL Server download page](https://www.microsoft.com/en-us/sql-server/sql-server-downloads).
- Choose the developer edition for free and follow the installation instructions.

### Install SQL Server Management Studio (SSMS):
- Download SSMS from the [official website](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms).
- Install and launch SSMS to manage your SQL Server databases.

## Step 3: Create a Database in Azure

### Log in to Azure Portal:
- Go to the [Azure Portal](https://portal.azure.com/).
- Use your student account credentials to log in.

### Create a SQL Database:
- In the Azure portal, click on `Create a resource`.
- Search for `SQL Database` and select it.
- Click `Create` and fill in the required information:
  - **Subscription**: Your Azure subscription
  - **Resource Group**: Create a new one or use an existing one
  - **Database name**: `MyDatabase`
  - **Server**: Create a new server or use an existing one
  - **Pricing tier**: Choose a free or low-cost tier for testing
- Summary:
  - Subscription: Azure for Students
  - Resource Group: Create or use an existing one
  - Database Name: Enter the name you prefer
  - Server: Use an existing server or create a new one
  - SQL Elastic Pool: Select No to avoid additional costs unless necessary.
  - Workload Environment: Select Development to use configurations optimized for development workloads.
  - Compute + Storage: Select the lowest available vCore and storage options.
  - Backup Storage Redundancy: Select Locally-redundant backup storage.
- Click `Review + create` and then `Create`.

### Set Up Firewall Rules:
- Once the database is created, go to the SQL server.
- Under Configure Access, and `Security`, select `Firewall and virtual networks`.
- Add your client IP address to allow your local machine to access the database.

## Step 4: Create an ASP.NET Core Web API Project

### Create a New Project in Visual Studio:
- Open Visual Studio and select `Create a new project`.
- Choose `ASP.NET Core Web API` and click `Next`.
- Name your project `MyApi` and click `Create`.

### Add Swagger for API Documentation:
With ASP.NET Core 6.0 and later, the `Startup.cs` file has been merged into the `Program.cs` file. Here’s how you can modify the `Program.cs` file to include the necessary configuration for Swagger.

- Open the `Program.cs` file and modify it to include Swagger:

    ```csharp
    using Microsoft.EntityFrameworkCore;
    using Microsoft.OpenApi.Models;
    using MyApi.Data;
    using MyApi.Models;

    var builder = WebApplication.CreateBuilder(args);

    // Add services to the container.
    builder.Services.AddControllers();

    // Add Swagger for API documentation
    builder.Services.AddSwaggerGen(c =>
    {
        c.SwaggerDoc("v1", new OpenApiInfo { Title = "My API", Version = "v1" });
    });

    // Add DbContext using SQL Server
    builder.Services.AddDbContext<MyDbContext>(options =>
        options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")));

    var app = builder.Build();

    // Configure the HTTP request pipeline.
    if (app.Environment.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseSwagger();
        app.UseSwaggerUI(c =>
        {
            c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1");
        });
    }

    app.UseHttpsRedirection();

    app.UseAuthorization();

    app.MapControllers();

    app.Run();
    ```

## Step 5: Connect to Azure SQL Database

### Install Entity Framework Core:
- Open the NuGet Package Manager and install the following packages:
  - `Microsoft.EntityFrameworkCore.SqlServer`
  - `Microsoft.EntityFrameworkCore.Tools`

### Add Connection String:
- In `appsettings.json`, add your connection string:

    ```json
    {
        "ConnectionStrings": {
            "DefaultConnection": "Server=tcp:<your_server>.database.windows.net,1433;Initial Catalog=MyDatabase;Persist Security Info=False;User ID=<your_username>;Password=<your_password>;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"
        }
    }
    ```

### Create a Data Model and DB Context:
- Add a new folder `Models` and create a `Product.cs` file:

    ```csharp
    namespace MyApi.Models
    {
        public class Product
        {
            public int Id { get; set; }
            public string Name { get; set; }
            public decimal Price { get; set; }
            public string Type { get; set; }
        }
    }
    ```

- Add a `MyDbContext.cs` file in a `Data` folder:

    ```csharp
    using Microsoft.EntityFrameworkCore;
    using MyApi.Models;

    namespace MyApi.Data
    {
        public class MyDbContext : DbContext
        {
            public MyDbContext(DbContextOptions<MyDbContext> options) : base(options) { }

            public DbSet<Product> Products { get; set; }
        }
    }
    ```

## Step 6: Add CRUD Operations

### Create a Controller:
- Add a `ProductsController.cs` file in the `Controllers` folder:

    ```csharp
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.EntityFrameworkCore;
    using MyApi.Data;
    using MyApi.Models;
    using System.Collections.Generic;
    using System.Threading.Tasks;

    namespace MyApi.Controllers
    {
        [Route("api/[controller]")]
        [ApiController]
        public class ProductsController : ControllerBase
        {
            private readonly MyDbContext _context;

            public ProductsController(MyDbContext context)
            {
                _context = context;
            }

            [HttpGet]
            public async Task<ActionResult<IEnumerable<Product>>> GetProducts()
            {
                return await _context.Products.ToListAsync();
            }

            [HttpGet("{id}")]
            public async Task<ActionResult<Product>> GetProduct(int id)
            {
                var product = await _context.Products.FindAsync(id);

                if (product == null)
                {
                    return NotFound();
                }

                return product;
            }

            [HttpPost]
            public async Task<ActionResult<Product>> PostProduct(Product product)
            {
                _context.Products.Add(product);
                await _context.SaveChangesAsync();

                return CreatedAtAction("GetProduct", new { id = product.Id }, product);
            }

            [HttpPut("{id}")]
            public async Task<IActionResult> PutProduct(int id, Product product)
            {
                if (id != product.Id)
                {
                    return BadRequest();
                }

                _context.Entry(product).State = EntityState.Modified;
                await _context.SaveChangesAsync();

                return NoContent();
            }

            [HttpDelete("{id}")]
            public async Task<IActionResult> DeleteProduct(int id)
            {
                var product = await _context.Products.FindAsync(id);
                if (product == null)
                {
                    return NotFound();
                }

                _context.Products.Remove(product);
                await _context.SaveChangesAsync();

                return NoContent();
            }
        }
    }
    ```

## Step 7: Publish to Azure

### Publish Your Project:
- Right-click the project in Visual Studio and select `Publish`.
- Choose `Azure` as the target and click `Next`.
- Select `Azure App Service (Windows)` and click `Next`.
- Sign in to your Azure account if prompted.
- Select your subscription, resource group, and App Service instance.
- Click `Finish` to publish your project.

### Verify Deployment:
- Once the deployment is complete, navigate to your Azure App Service URL.
- Append `/swagger` to the URL to access the Swagger UI and test your API.

## Conclusion

Congratulations! You've successfully created an ASP.NET Web API, integrated Swagger for documentation, connected to an Azure SQL Database, and published your project to Azure. This setup provides a robust foundation for building and scaling your web applications.
