# Trigger Failure Strategy

This repo has a simple example of a way to handle triggers that get in a loop constantly retrying a failed command.

When the trigger reaches its maximum amount of failures, a `TriggerFailureNotification` contract is created
that includes the failure messages that accumulated during the triggers last run. The party running the
trigger then has the opportunity to exercise the `StartTrigger` choice which will reset the amount of failures
and let the trigger restart.

To test this out, build both the trigger dar and model dar (`make build` will place them in `target`) and upload
them to Daml Hub (or the example can be [run locally](https://docs.daml.com/triggers/index.html#run-a-no-op-trigger)).


Start a trigger as Alice, and then create an `AssetOrArchive` contract with `fail` set to true. When the
trigger fails 10 times, it will pause and create the notification contract, on which the `StartTrigger`
choice can be exercised and the trigger will start again.

To use this code in your own project:

1. Include `TriggerFailureNotification` and `FailedTriggerStartRequest` from [daml/Main.daml](daml/Main.daml) in your model
2. Include `FailureState`, `updateStateFailures` and `withFailures` from [triggers/daml/Trigger.daml](triggers/daml/Trigger.daml) in your trigger's Daml code
3. Use `updateStateFailure` as the `updateState` function in your `Trigger` settings function. If you have a custom `updateState` function, you will need to call `updateStateFailures` in it.
4. In your `initialize` function, use `initFailureState` with the number of possible retries
4. Use `FailureState` as the trigger's state (e.g. `T.Trigger FailureState`)
4. Wrap all code in your trigger rule in `withFailures` as seen below.

Example:

```haskell
trigger : T.Trigger FailureState
trigger = T.Trigger
  { initialize = pure $ initFailureState 10
  , updateState = updateStateFailures
  , rule = triggerRule
  , registeredTemplates = T.AllInDar
  , heartbeat = None
  }

triggerRule : Party -> T.TriggerA FailureState ()
triggerRule party = withFailures party $ do
      -- trigger code here as normal
      return ()
```

If you are already using a custom state in your trigger, you will have to include `FailureState` in it and
modify `updateStateFailures`, `initTriggerState` and `withFailures` accordingly.

# Sample code

**This repo contains sample code to help you get started with DAml. Please bear
in mind that it is provided for illustrative purposes only, and as such may not
be production quality and/or may not fit your use-cases. You may use the
contents of this repo in parts or in whole according to the BSD0 license:**

> Copyright © 2022 Digital Asset (Switzerland) GmbH and/or its affiliates
>
> Permission to use, copy, modify, and/or distribute this software for any purpose with or without fee is hereby granted.
>
> THE SOFTWARE IS PROVIDED "AS IS" AND THE AUTHOR DISCLAIMS ALL WARRANTIES WITH REGARD TO THIS SOFTWARE INCLUDING ALL IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS. IN NO EVENT SHALL THE AUTHOR BE LIABLE FOR ANY SPECIAL, DIRECT, INDIRECT, OR CONSEQUENTIAL DAMAGES OR ANY DAMAGES WHATSOEVER RESULTING FROM LOSS OF USE, DATA OR PROFITS, WHETHER IN AN ACTION OF CONTRACT, NEGLIGENCE OR OTHER TORTIOUS ACTION, ARISING OUT OF OR IN CONNECTION WITH THE USE OR PERFORMANCE OF THIS SOFTWARE.
