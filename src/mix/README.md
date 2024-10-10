# Project Management

## Tips

You can add aliases to your `mix.exs` file to make it easier to run common
tasks.

```elixir
  defp aliases do
    [
      "ecto.setup": ["ecto.create", "ecto.migrate", "run priv/repo/seeds.exs"],
      "ecto.reset": ["ecto.drop", "ecto.setup"]
    ]
  end
```

If you come across an old project, `mix deps.update --all` to update all
dependencies might be a good idea.
