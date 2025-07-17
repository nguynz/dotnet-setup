dotnet new mvc -n TenProject
dotnet add package Microsoft.EntityFrameworkCore
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet add package Microsoft.EntityFrameworkCore.SqlServer


Program.cs
using TenProject.Data;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddDbContext<AppDbContext>(options =>
    options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")));

var app = builder.Build();

"ConnectionStrings": {
  "DefaultConnection": "Server=.;Database=TenProjectDb;Trusted_Connection=True;Trusted_Connection=True;TrustServerCertificate=True;"
}


using Microsoft.EntityFrameworkCore;
using TenProject.Models;

namespace TenProject.Data
{
    public class AppDbContext : DbContext
    {
        public AppDbContext(DbContextOptions<AppDbContext> options) : base(options) {}

        public DbSet<Category> Categories { get; set; }
        public DbSet<Product> Products { get; set; }

        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            // Seed Category
            modelBuilder.Entity<Category>().HasData(
                new Category { Id = 1, Name = "Điện thoại" },
                new Category { Id = 2, Name = "Laptop" }
            );

            // Seed Product
            modelBuilder.Entity<Product>().HasData(
                new Product { Id = 1, Name = "iPhone 15", Price = 20000000, CategoryId = 1 },
                new Product { Id = 2, Name = "MacBook Pro", Price = 50000000, CategoryId = 2 }
            );
        }
    }
}

dotnet build
dotnet ef migrations add SeedData
dotnet ef database update

<!-- Bootstrap 5 CSS -->
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet">

<!-- Bootstrap 5 JS (Popper included) -->
<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js"></script>


/ViewComponents/CategoryViewComponent.cs
using Microsoft.AspNetCore.Mvc;
using TenProject.Data;
using System.Threading.Tasks;
using Microsoft.EntityFrameworkCore;

namespace TenProject.ViewComponents
{
    public class CategoryViewComponent : ViewComponent
    {
        private readonly AppDbContext _context;
        public CategoryViewComponent(AppDbContext context)
        {
            _context = context;
        }

        public async Task<IViewComponentResult> InvokeAsync()
        {
            var categories = await _context.Categories.ToListAsync();
            return View(categories); // Truyền categories sang View của component
        }
    }
}

/Views/Shared/Components/Category/Default.cshtml
@model IEnumerable<TenProject.Models.Category>

<ul class="list-group">
    @foreach (var item in Model)
    {
        <li class="list-group-item">@item.Name</li>
    }
</ul>

builder.Services.AddScoped<IHospitalRepository, EFHospitalRepository>();

@await Component.InvokeAsync("Category")


