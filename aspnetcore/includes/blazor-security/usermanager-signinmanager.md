## <a name="usermanager-and-signinmanager"></a>UserManager e SignInManager

Impostare il tipo di attestazione dell'identificatore utente quando un'app Server richiede:Set the user identifier claim type when a Server app requires:

* <xref:Microsoft.AspNetCore.Identity.UserManager%601>o <xref:Microsoft.AspNetCore.Identity.SignInManager%601> in un endpoint API.
* <xref:Microsoft.AspNetCore.Identity.IdentityUser>dettagli, ad esempio il nome dell'utente, l'indirizzo di posta elettronica o l'ora di fine del blocco.

In `Startup.ConfigureServices`:

```csharp
services.Configure<IdentityOptions>(options => 
    options.ClaimsIdentity.UserIdClaimType = ClaimTypes.NameIdentifier);
```

Di `WeatherForecastController` seguito <xref:Microsoft.AspNetCore.Identity.IdentityUser%601.UserName> viene `Get` registrato il quando viene chiamato il metodo:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Identity;
using Microsoft.Extensions.Logging;
using BlazorAppIdentityServer.Server.Models;
using BlazorAppIdentityServer.Shared;

namespace BlazorAppIdentityServer.Server.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
    {
        private readonly UserManager<ApplicationUser> _userManager;

        private static readonly string[] Summaries = new[]
        {
            "Freezing", "Bracing", "Chilly", "Cool", "Mild", "Warm", 
            "Balmy", "Hot", "Sweltering", "Scorching"
        };

        private readonly ILogger<WeatherForecastController> logger;

        public WeatherForecastController(ILogger<WeatherForecastController> logger, 
            UserManager<ApplicationUser> userManager)
        {
            this.logger = logger;
            _userManager = userManager;
        }

        [HttpGet]
        public async Task<IEnumerable<WeatherForecast>> Get()
        {
            var rng = new Random();

            var user = await _userManager.GetUserAsync(User);

            if (user != null)
            {
                logger.LogInformation($"User.Identity.Name: {user.UserName}");
            }

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = DateTime.Now.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = Summaries[rng.Next(Summaries.Length)]
            })
            .ToArray();
        }
    }
}
```
