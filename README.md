# Sample code

**This repo contains sample code to help you get started with DAML. Please bear
in mind that it is provided for illustrative purposes only, and as such may not
be production quality and/or may not fit your use-cases. You may use the
contents of this repo in parts or in whole according to the BSD0 license:**

> Copyright © 2020 Digital Asset (Switzerland) GmbH and/or its affiliates
>
> Permission to use, copy, modify, and/or distribute this software for any purpose with or without fee is hereby granted.
>
> THE SOFTWARE IS PROVIDED "AS IS" AND THE AUTHOR DISCLAIMS ALL WARRANTIES WITH REGARD TO THIS SOFTWARE INCLUDING ALL IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS. IN NO EVENT SHALL THE AUTHOR BE LIABLE FOR ANY SPECIAL, DIRECT, INDIRECT, OR CONSEQUENTIAL DAMAGES OR ANY DAMAGES WHATSOEVER RESULTING FROM LOSS OF USE, DATA OR PROFITS, WHETHER IN AN ACTION OF CONTRACT, NEGLIGENCE OR OTHER TORTIOUS ACTION, ARISING OUT OF OR IN CONNECTION WITH THE USE OR PERFORMANCE OF THIS SOFTWARE.


# Trigger Failure Strategy

This repo has a simple example of a way to handle triggers that fail constantly retrying.

When the trigger reaches its maximum amount of failures, a `TriggerFailureNotification` contract is created
that includes the failure messages that accumulated during the triggers last run. The party running the
trigger then has the opportunity to exercise the `StartTrigger` choice which will reset the amount of failures
and let the trigger restart.

To test this out, build both the trigger dar and model dar and upload them to Daml Hub (or run locally).

Start a trigger as Alice, and then create an `AssetOrArchive` contract with `fail` set to true. When the
trigger fails 5 times, it will pause and create the notification contract, which can then be excercised
and the trigger will start again.
