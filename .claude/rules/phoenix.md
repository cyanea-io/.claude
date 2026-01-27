# Phoenix Framework Rules

## Contexts

Business logic belongs in contexts, not controllers or LiveViews:

```elixir
# Good - context handles logic
def create(conn, params) do
  case Accounts.create_user(params) do
    {:ok, user} -> redirect(conn, to: ~p"/users/#{user}")
    {:error, changeset} -> render(conn, :new, changeset: changeset)
  end
end

# Avoid - logic in controller
def create(conn, params) do
  %User{}
  |> User.changeset(params)
  |> Repo.insert()
  |> case do
    # ...
  end
end
```

## Context Naming

- Contexts: singular noun (`Accounts`, not `Account`)
- Schemas: singular (`User`, not `Users`)
- Tables: plural (`users`, not `user`)

## LiveView

### Keep LiveViews Thin

LiveViews should handle:
- UI state management
- Event handling
- Rendering

Delegate business logic to contexts.

### Assign Patterns

```elixir
# Use assign_new for defaults
def mount(_params, _session, socket) do
  {:ok,
   socket
   |> assign_new(:loading, fn -> false end)
   |> assign_new(:items, fn -> [] end)}
end

# Use streams for lists
def mount(_params, _session, socket) do
  {:ok, stream(socket, :items, Catalog.list_items())}
end
```

### Handle Events

```elixir
def handle_event("save", %{"form" => params}, socket) do
  case Context.create(params) do
    {:ok, item} ->
      {:noreply,
       socket
       |> put_flash(:info, "Created successfully")
       |> push_navigate(to: ~p"/items/#{item}")}

    {:error, changeset} ->
      {:noreply, assign(socket, :changeset, changeset)}
  end
end
```

## Components

### Core Components

Use the generated `CoreComponents` module for consistent UI:

```elixir
<.button>Click me</.button>
<.input field={@form[:name]} label="Name" />
<.table id="users" rows={@users}>
  <:col :let={user} label="Name"><%= user.name %></:col>
</.table>
```

### Function Components

Keep components focused and reusable:

```elixir
attr :user, User, required: true
attr :class, :string, default: nil

def user_avatar(assigns) do
  ~H"""
  <img
    src={@user.avatar_url || "/images/default-avatar.png"}
    alt={@user.name}
    class={["rounded-full h-10 w-10", @class]}
  />
  """
end
```

## Router

- Group related routes with `scope`
- Use `pipe_through` for shared plugs
- Keep route paths RESTful

```elixir
scope "/", MyAppWeb do
  pipe_through [:browser, :require_authenticated_user]

  live "/dashboard", DashboardLive
  resources "/settings", SettingsController, only: [:edit, :update]
end
```

## Ecto Queries

- Use `Repo` functions in contexts only
- Compose queries with functions
- Preload associations explicitly

```elixir
def list_users_with_posts do
  User
  |> where([u], u.active == true)
  |> preload(:posts)
  |> order_by([u], desc: u.inserted_at)
  |> Repo.all()
end
```
