# EctoIdentifier :dart:

_When UUID is not enough!_

[![codecov](https://codecov.io/gh/oyeb/ecto_identifier/branch/master/graph/badge.svg)](https://codecov.io/gh/oyeb/ecto_identifier)
[![Build Status](https://travis-ci.org/oyeb/ecto_identifier.svg?branch=master)](https://travis-ci.org/oyeb/ecto_identifier)

Provides some custom [`Ecto`][ecto] [type][ecto-type]s to work with identifier
fields that are generated using:

1. [`nanoid`][nanoid]s (`Ecto.Nanoid`)
2. [`hashids`][hashids] (`Ecto.Hashids`)


[ecto]: https://github.com/elixir-ecto/
[ecto-type]: https://hexdocs.pm/ecto/Ecto.Type.html
[nanoid]: https://github.com/railsmechanic/nanoid
[hashids]: https://github.com/alco/hashids-elixir

Full docs available on [hexdocs.pm](https://hexdocs.pm/ecto_identifier)

## Ecto.Nanoid

[Nanoid are as safe and sound as UUIDs, but more
compact](https://github.com/ai/nanoid). Check them out [in real
life!](https://alex7kom.github.io/nano-nanoid-cc/) :rainbow:

The type defines an `autogenerate/0` that can generate a nanoid for you at
"insert-time". You don't have to worry about adding the field to your changeset
`cast`/`validate_required` (just like those auto-generated timestamps)!

``` elixir
defmodule Post do
  use Ecto.Schema
  alias Ecto.Nanoid

  schema "posts" do
    field(:number, Ecto.Nanoid, autogenerate: true)
    field(:data, :string)
  end
end
```
And voila!

``` elixir
iex> changeset = Post.changeset(%Post{}, %{data: "the answer to everything"})
iex> changeset.changes.number
nil                                         # coz it's autogenerated!
iex> a_tough_one = Repo.insert!(changeset)
iex> %Post{
  id: 42,
  number: "swv~u7bIyewzBFOKuFbbD",          # there we have it!
}
```
> If you pass in a `:number` in the `params`, a nanoid will not be autogenerated
> for you. For more juicy details see `Ecto.Schema`.

#### Why a "type"

Well, to make Ecto compute the ID only at "insert-time", you gotta define an
`autogenerate/0` for that field's `type`.  Which means, this nanoid field needs
to be of _a custom `Ecto.Type`_, ala `Ecto.Nanoid`.

#### Can I use this as my primary key?

Well of course! Example:

``` elixir
@primary_key {:id, :string, autogenerate: {Ecto.Nanoid, :autogenerate, []}}
schema "users" do
field :email, :string
field :name, :string

timestamps()
end

```

``` elixir
def change do
create table(:users, primary_key: false) do
add :email, :string
add :id, :string, primary_key: true
add :name, :string

timestamps()
end

end
```

## Ecto.Hashids

_Coming shortly folks!_

## Installation

The package can be installed by adding `ecto_identifier` to your list of
dependencies in `mix.exs`:

```elixir
def deps do
  [
    {:ecto_identifier, "~> 0.1.0"}
  ]
end
```

# License

Copyright 2018 Ananya Bahadur

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
