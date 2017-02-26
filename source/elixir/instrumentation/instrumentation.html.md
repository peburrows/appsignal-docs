---
title: "Custom instrumentation for Elixir"
---

In order to find out what specific pieces of code are causing performance
problems it's useful to add custom instrumentation to your application. This
allows us to create better breakdowns of which code runs slowest and what type
of action was the most time spent on.

Custom instrumentation is possible in two ways: with function decorators and
with instrumentation helper functions. The function decorators are easiest to
use, but are less flexible than the instrumentation helper functions.

This short guide will help you set up custom instrumentation. More details on
the usage of certain helpers can be found in the Hex docs for the [AppSignal
package](https://hexdocs.pm/appsignal/).

## Table of Contents

- [Function decorators](#function-decorators)
  - [Transaction events](#transaction-events)
  - [Transaction](#transaction)
  - [Namespaces](#namespaces)
      - [Custom namespaces](#custom-namespaces)
  - [Phoenix channels](#phoenix-channels)
- [Instrumentation helper functions](#Instrumentation-helper-functions)

## Function decorators

Using the `Appsignal.Instrumentation.Decorators` decorators it's possible
quickly add custom instrumentation to your Elixir applications without a lot of
code.

### Transaction events

In the following example we have a Phoenix controller with an `index/2`
function which calls a slow function. The `slow` function is instrumented using
the AppSignal `transaction_event` decorator which tracks it a separate event in
this Phoenix request. It will show up on AppSignal.com in the event timeline of
this transaction sample to provide more insight in where the most time was
spent during the request.

```elixir
# Phoenix controller example
defmodule PhoenixExample.PageController do
  use PhoenixExample.Web, :controller
  # Include this
  use Appsignal.Instrumentation.Decorators

  def index(conn, _params) do
    slow()
    render conn, "index.html"
  end

  # Decorate this function to add custom instrumentation
  @decorate transaction_event
  defp slow do
    :timer.sleep(1000)
  end
end
```

### Transactions

In the Phoenix example an AppSignal transaction has already been started,
thanks to the first-party support for Phoenix in the AppSignal package. Not all
frameworks and packages are currently supported directly and automatically
start transactions. The same is true for your own pure Elixir applications.

In order to track `transaction_event` decorators we will need to start an
AppSignal transaction beforehand. We can start a transaction with the
`transaction` function decorator.

```elixir
# Pure Elixir example
defmodule FunctionDecoratorsExample do
  # Include this
  use Appsignal.Instrumentation.Decorators

  # No transaction is started beforehand like in Phoenix, so we need to start
  # it ourselves.
  @decorate transaction
  def call do
    slow()
    # ...
  end

  # Decorate this function to add custom instrumentation
  @decorate transaction_event
  defp slow do
    :timer.sleep(1000)
  end
end
```

### Namespaces

In order to differentiate between HTTP requests and background jobs we can pass
a namespace to the transaction once we start it.

The following two namespaces official namespaces supported by AppSignal.

- `http_request` - the default - is called the "web" namespace
- `background_job` - creates the "background" namespace

```elixir
defmodule FunctionDecoratorsExample do
  # Include this
  use Appsignal.Instrumentation.Decorators

  # No namespace argument defaults to `:http_request`
  @decorate transaction
  def web_function do
    # do stuff
  end

  # The "background" namespace
  @decorate transaction(:background_job)
  def background_function do
    # do stuff
  end
end
```

### Custom namespaces

You can also create your own namespaces to track transactions in a separate
part of your application such as an administration panel. This will group all
the transactions with this namespace in a separate section on AppSignal.com so
that slow admin controllers don't interfere with the averages of your
application's speed.

```elixir
@decorate transaction(:admin)
def some_function do
  # do stuff
end
```

### Phoenix channels

There is a custom function decorator for Phoenix channels. This decorator is
meant to be put before the `handle_in/3` function of a `Phoenix.Channel`
module.

```elixir
defmodule FunctionDecoratorsExample.MyChannel do
  # Include this
  use Appsignal.Instrumentation.Decorators

  # Add this channel function decorator
  @decorate channel_action
  def handle_in("ping", _payload, socket) do
    # your code here..
  end
end
```

Channel events will be displayed under the "background" namespace, showing the
channel module and the action argument that it's used on.

## Instrumentation helper functions

https://hexdocs.pm/appsignal/Appsignal.Instrumentation.Helpers.html
https://hexdocs.pm/appsignal/Appsignal.Transaction.html

### Example

This example is set up as procedural as possible so any setup doesn't distract
from the order in which to execute things. You may want to wrap certain parts
in separate functions to make it more reusable.

```elixir
defmodule InstrumentationExample do
  # Include this
  use Appsignal.Instrumentation.Helpers

  def instrumented_function do
    # Start an AppSignal transaction
    transaction = Appsignal.Transaction.start(
      Appsignal.Transaction.generate_id,
      :http_request
    )
    # Set the action name of the module/controller and function which is instrumented
    |> Appsignal.Transaction.set_action("InstrumentationExample#instrumented_function")
    # Add extra data to the transaction. See also our Tagging guide.
    |> Appsignal.Transaction.set_sample_data(
      "environment", %{request_path: "/hello", method: "GET"}
    )

    try do
      # Instrument specific blocks of code
      instrument(transaction, "complex.function", "Rendering something slow", fn() ->
        :timer.sleep(100)

        # Nest instrumentation
        instrument(transaction, "request.third_party_api", "Slow API call", fn() ->
          :timer.sleep(300)
        end)

        instrument(transaction, "query.custom_database", "Slow query", fn() ->
          :timer.sleep(5000)
        end)

        # Raise an error, not required of course.
        raise("Exception!")
      end)
    rescue
      e ->
        # Report an error in this transaction.
        # It will then be reported as an error instead of a performance issue.
        Appsignal.Transaction.set_error(
          transaction,
          "SomeError",
          "So what went wrong was that I raised an exception earlier..",
          System.stacktrace
        )
    end

    # Finish and close the transaction
    Appsignal.Transaction.finish(transaction)
    :ok = Appsignal.Transaction.complete(transaction)
  end
end
```
