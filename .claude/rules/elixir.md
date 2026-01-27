# Elixir Development Rules

## Code Style

- Always run `mix format` before committing
- Follow the official Elixir style guide
- Use `mix credo --strict` for linting

## Naming Conventions

- Modules: PascalCase (`MyModule`)
- Functions: snake_case (`my_function`)
- Variables: snake_case (`my_variable`)
- Constants: snake_case (`@my_constant`)
- Atoms: snake_case (`:my_atom`)

## Pattern Matching

Prefer pattern matching over conditionals:

```elixir
# Good
def process({:ok, result}), do: handle_success(result)
def process({:error, reason}), do: handle_error(reason)

# Avoid
def process(result) do
  case result do
    {:ok, value} -> handle_success(value)
    {:error, reason} -> handle_error(reason)
  end
end
```

## With Statements

Use `with` for happy-path chaining:

```elixir
with {:ok, user} <- fetch_user(id),
     {:ok, profile} <- fetch_profile(user),
     {:ok, _} <- authorize(profile) do
  {:ok, profile}
else
  {:error, :not_found} -> {:error, "User not found"}
  {:error, :unauthorized} -> {:error, "Not authorized"}
  error -> error
end
```

## Pipe Operator

- Start pipes with a raw value, not a function call
- Each step should transform the data meaningfully
- Avoid single-step pipes

```elixir
# Good
user
|> fetch_profile()
|> enrich_with_stats()
|> format_response()

# Avoid
fetch_user(id) |> process()
```

## Documentation

- All public modules need `@moduledoc`
- All public functions need `@doc`
- Add typespecs with `@spec`

```elixir
@doc """
Fetches a user by their ID.

Returns `{:ok, user}` if found, `{:error, :not_found}` otherwise.
"""
@spec fetch_user(String.t()) :: {:ok, User.t()} | {:error, :not_found}
def fetch_user(id) do
  # ...
end
```

## Error Handling

- Use tagged tuples: `{:ok, value}` and `{:error, reason}`
- Avoid raising exceptions for expected error cases
- Use `!` suffix for functions that raise

## OTP Patterns

- Use Supervisors for process management
- Keep GenServer callbacks simple
- Store minimal state in processes
- Use `handle_continue` for post-init work
