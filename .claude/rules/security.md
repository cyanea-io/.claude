# Security Rules

## OWASP Top 10 Prevention

### Injection

- Use parameterized queries with Ecto
- Never interpolate user input into queries
- Validate and sanitize all inputs

```elixir
# Good - parameterized
from(u in User, where: u.email == ^email)

# Bad - interpolation
from(u in User, where: u.email == "#{email}")
```

### Broken Authentication

- Use strong password hashing (bcrypt via Comeonin)
- Implement rate limiting on login attempts
- Use secure session management
- Invalidate sessions on logout

### Sensitive Data Exposure

- Never log sensitive data (passwords, tokens, PII)
- Use HTTPS everywhere
- Encrypt sensitive data at rest
- Implement proper access controls

### XSS (Cross-Site Scripting)

Phoenix/LiveView auto-escapes by default. Be careful with:

```elixir
# Dangerous - raw HTML
<%= raw(@user_input) %>

# Safe - auto-escaped
<%= @user_input %>

# If you must render HTML, sanitize first
<%= raw(HtmlSanitizeEx.strip_tags(@user_input)) %>
```

### CSRF

Phoenix includes CSRF protection by default:

```elixir
# In router.ex - already included in :browser pipeline
plug :protect_from_forgery
```

### Security Misconfiguration

- Never commit secrets to git
- Use environment variables for configuration
- Keep dependencies updated
- Run `mix sobelow` for security scanning

## Secrets Management

### Never Commit

- API keys
- Database passwords
- Secret keys
- Private certificates
- `.env` files

### Use Environment Variables

```elixir
# In config/runtime.exs
config :my_app, MyApp.Repo,
  url: System.get_env("DATABASE_URL")

config :my_app, :secret_key_base,
  System.get_env("SECRET_KEY_BASE")
```

### .gitignore

Ensure these are ignored:
```
.env
.env.local
*.pem
*.key
config/prod.secret.exs
```

## Input Validation

Always validate at the boundary:

```elixir
def changeset(user, attrs) do
  user
  |> cast(attrs, [:email, :name])
  |> validate_required([:email])
  |> validate_format(:email, ~r/^[^\s]+@[^\s]+$/)
  |> validate_length(:name, max: 100)
  |> unique_constraint(:email)
end
```

## Authorization

- Check permissions on every request
- Use context functions for authorization
- Never trust client-side authorization

```elixir
def update_post(user, post, attrs) do
  if can_edit?(user, post) do
    post
    |> Post.changeset(attrs)
    |> Repo.update()
  else
    {:error, :unauthorized}
  end
end
```

## File Upload Security

- Validate file types (don't trust extensions)
- Limit file sizes
- Scan for malware if possible
- Store outside web root
- Generate random filenames

```elixir
def validate_upload(upload) do
  allowed_types = ~w(image/jpeg image/png application/pdf)

  cond do
    upload.size > 10_000_000 ->
      {:error, "File too large"}
    upload.content_type not in allowed_types ->
      {:error, "Invalid file type"}
    true ->
      :ok
  end
end
```

## Dependency Security

- Run `mix deps.audit` regularly
- Keep dependencies updated
- Review new dependencies before adding
- Use `mix hex.audit` for Hex packages

## Security Headers

Configure in endpoint.ex:

```elixir
plug :put_secure_browser_headers, %{
  "content-security-policy" => "default-src 'self'",
  "x-frame-options" => "DENY",
  "x-content-type-options" => "nosniff"
}
```

## Logging

- Log security events (failed logins, permission denials)
- Don't log sensitive data
- Use structured logging
- Retain logs for audit purposes

```elixir
# Good
Logger.warning("Login failed", user_id: user.id, ip: conn.remote_ip)

# Bad - logs password
Logger.warning("Login failed", user: user, password: password)
```

## Security Scanning

Run regularly:
```bash
# Static analysis for security issues
mix sobelow

# Dependency vulnerabilities
mix deps.audit

# Hex package audit
mix hex.audit
```
