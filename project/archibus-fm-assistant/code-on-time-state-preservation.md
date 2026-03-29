---
publish: true
---

# ADR: Code on Time OAuth State Parameter Preservation

[[client-archibus]]

## Status
**Proposed** | Date: 2025-12-22

## Context

Bruce BEM is built on the **Code on Time** framework (codeontime.com). When LibreChat initiates OAuth with a `state` parameter for session matching (see [OAuth State Session Matching ADR](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/oauth-state-session-matching)), the Code on Time framework strips "unknown" query parameters during its internal redirect before displaying the login form.

**The Problem:**
LibreChat sends `?state=user123:brucebem` → Code on Time internal redirect → state stripped → login form shown → user authenticates → callback without state → LibreChat can't match session.

## Decision

Two interception approaches identified, with recommendation to try the simpler one first.

### Option A: Application_BeginRequest (Global.asax)

Intercepts at the earliest point in ASP.NET's pipeline - before Code on Time's routing or authentication logic runs.

```csharp
// ~/Global.asax.cs
protected void Application_BeginRequest(object sender, EventArgs e)
{
    var state = HttpContext.Current.Request.QueryString["state"];
    if (!string.IsNullOrEmpty(state))
        HttpContext.Current.Items["oauth_state"] = state;  // Session not ready yet
}

protected void Application_AcquireRequestState(object sender, EventArgs e)
{
    // Transfer to Session once available
    if (HttpContext.Current.Items["oauth_state"] != null)
        HttpContext.Current.Session["oauth_state"] = HttpContext.Current.Items["oauth_state"];
}
```

**When to use:** If state is stripped during initial request routing.

**Caveat:** Session not available at BeginRequest - requires two-step storage.

---

### Option B: UserLogin Override (Partial Class)

Code on Time's documented customization pattern. Override `UserLogin` in a partial class that survives framework rebuilds.

```csharp
// ~/App_Code/custom/ApplicationServices.Overrides.cs
namespace MyCompany.Services
{
    public partial class ApplicationServices
    {
        public override bool UserLogin(string username, string password, bool createPersistentCookie)
        {
            var state = HttpContext.Current.Request.QueryString["state"];
            if (!string.IsNullOrEmpty(state))
                HttpContext.Current.Session["oauth_state"] = state;

            return base.UserLogin(username, password, createPersistentCookie);
        }
    }
}
```

**When to use:** If state survives until login form is displayed.

**Caveat:** Only works if `UserLogin` is invoked - may not fire if redirect happens before form displays.

**Source:** [Handling Login and Logout](https://codeontime.com/learn/security/handling-login-and-logout)

---

### Recommendation

1. **Try Option B first** - simpler, follows Code on Time patterns
2. **If state already stripped by UserLogin**, escalate to Option A

## Code on Time Architecture Notes

**Customization locations that survive rebuild:**
- `~/App_Code/custom/` - partial class overrides
- `~/Rules/` - business rules
- `~/Global.asax` - typically not regenerated

**Authentication flow:**
1. Client calls `$app.login()` JavaScript method
2. Triggers `DataControllerService.Login` web service
3. Calls `ApplicationServices.Login` (static)
4. Creates instance and calls virtual `UserLogin` method ← **Interception point B**

**Key classes:**
- `ApplicationServices` - central authentication service
- `AppIdentityOAuthHandler` - OAuth callback handler (for OAuth consumer scenarios)

## Consequences

### Positive
- No framework modification required
- Partial classes survive rebuild
- Standard ASP.NET patterns

### Negative
- Requires testing to determine which option works
- May need both approaches if state stripped at multiple points

## Related

- [OAuth State Session Matching ADR](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/oauth-state-session-matching) - LibreChat's Redis polling architecture
- [Standalone Auth Architecture ADR](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/standalone-auth-architecture) - Multi-provider OAuth pattern

## Sources

- [Code on Time - Handling Login and Logout](https://codeontime.com/learn/security/handling-login-and-logout)
- [Code on Time - Custom User Login Responses](https://codeontime.com/blog/2021/10/custom-user-login-responses)
- [Code on Time - Open Authentication Configuration](https://codeontime.com/learn/integrated-content-management-system/open-authentication-configuration)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

