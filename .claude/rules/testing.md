# Testing Rules

## Test Organization

```
test/
├── my_app/              # Unit tests for contexts
│   ├── accounts_test.exs
│   └── catalog_test.exs
├── my_app_web/          # Integration tests
│   ├── live/            # LiveView tests
│   └── controllers/     # Controller tests
└── support/             # Test helpers
    ├── conn_case.ex
    ├── data_case.ex
    └── fixtures/
```

## Naming Conventions

- Test files mirror source files: `lib/my_app/accounts.ex` -> `test/my_app/accounts_test.exs`
- Describe blocks match function names
- Test names describe expected behavior

```elixir
describe "create_user/1" do
  test "creates user with valid attributes" do
    # ...
  end

  test "returns error changeset with invalid email" do
    # ...
  end
end
```

## Test Structure

Follow Arrange-Act-Assert pattern:

```elixir
test "updates user profile" do
  # Arrange
  user = insert(:user)
  attrs = %{name: "New Name"}

  # Act
  result = Accounts.update_user(user, attrs)

  # Assert
  assert {:ok, updated} = result
  assert updated.name == "New Name"
end
```

## Fixtures and Factories

Use ExMachina or similar for test data:

```elixir
# In test/support/factory.ex
def user_factory do
  %User{
    email: sequence(:email, &"user#{&1}@example.com"),
    name: "Test User"
  }
end

# In tests
user = insert(:user)
user = insert(:user, name: "Custom Name")
```

## Database Tests

- Use `DataCase` for context tests
- Tests run in a transaction and rollback
- Use `Ecto.Adapters.SQL.Sandbox` for async tests

```elixir
use MyApp.DataCase, async: true
```

## LiveView Tests

```elixir
use MyAppWeb.ConnCase, async: true
import Phoenix.LiveViewTest

test "renders form", %{conn: conn} do
  {:ok, view, html} = live(conn, ~p"/users/new")

  assert html =~ "Create User"
  assert has_element?(view, "input[name='user[email]']")
end

test "submits form successfully", %{conn: conn} do
  {:ok, view, _html} = live(conn, ~p"/users/new")

  view
  |> form("#user-form", user: %{email: "test@example.com"})
  |> render_submit()

  assert_redirect(view, ~p"/users")
end
```

## Mocking

Prefer dependency injection over mocking:

```elixir
# Define behaviour
defmodule MyApp.EmailSender do
  @callback send(String.t(), String.t()) :: :ok | {:error, term()}
end

# Real implementation
defmodule MyApp.EmailSender.Mailgun do
  @behaviour MyApp.EmailSender
  def send(to, body), do: # actual implementation
end

# Test implementation
defmodule MyApp.EmailSender.Mock do
  @behaviour MyApp.EmailSender
  def send(_to, _body), do: :ok
end

# Configure in config/test.exs
config :my_app, email_sender: MyApp.EmailSender.Mock
```

## Coverage

- Target 80%+ line coverage
- Use ExCoveralls: `mix coveralls.html`
- Focus on critical paths, not 100% coverage

## Performance Tests

For critical paths, add basic performance assertions:

```elixir
test "search completes in reasonable time" do
  insert_list(1000, :item)

  {time, results} = :timer.tc(fn -> Catalog.search("query") end)

  assert length(results) > 0
  assert time < 100_000  # 100ms in microseconds
end
```
