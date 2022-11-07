# budgetxt

This is not accounting software. This is not double-entry. It's barely single-entry.
I've been treasurer of three different organizations, and I have a pretty good
non-accountant understanding of accounting software. This ain't it.

This is a stupid simple command-line tool for people who have to track what they
spend out of their own little budget pool for a company that pays real accountants
to do real accounting. That's what I am right now. If that's you and you think this
is useful, that's cool. If this isn't your thing, that's cool too. Have a nice day
and be kind to others.


## How do I use this thing?

budgetxt is just a command-line tool. You put your budgets and actuals into YAML
files (more on those below). Then you call budgetxt on those files, like this:

    budgetxt 2023-budget.yml 2023-actuals.yml

You can even pass multiple budget or actuals files, like this:

    budgetxt 2023-public-budget.yml 2023-public-actuals.yaml \
             2023-shaunm-budget.yml 2023-shaunm-actuals.yaml

This is useful if you have different people in charge of different parts of the
budget, and you want to be able to view and edit them separately, but also get
an aggregate report.

By the way, budgetxt wants the word `budget` or `actual` to be in the file name.
That's how it tells them apart. It'll ignore anything else.

budgetxt will output some tables to stdout. I plan to add options to output
to HTML, control which tables are output, and probably output CSV. I hope to
remember to update this file when I do that.


## How are budgets organized?

Budget numbers are attached to events and items. This nomenclature reflects
how I use budgetxt. I think it's still useful if you're doing things other
than running events.

As an example, imagine you're running a conference called Duckfest. You have
expenses for things like venue and travel that break down like this:

    Duckfest
      Venue/catering    $15,000
      A/V setups        $ 8,000
      Travel            $ 5,000
      Swag              $ 1,000

In this example, Duckfest is an event with a total budget of $29,000, broken
up into four items. In budgetxt, we can do reports both on the events (so
you can see how much you spent on Duckfest), and on the items (so you can
see how much you spent on travel across all events).


## Ok, about those YAML files?

We record the budget and the actuals in YAML files. These have different
formats. The budget file is a dictionary, where each entry is the budget
for an event. The key is some unique identifer you make for that event.
Then you provide information about that event as a dictionary. Here are
the keys that budgetxt likes to see:

* `name`: A nice human-readable event name.

* `date`: When the event is happening, in the One True Date Format,
  because we sort on it.

* `location`: Where the event is happening, in whatever format makes
  you happy.

* `items`: A dictionary that maps item identifiers to budget numbers.
  You make up the items.

Let's look at an example:

    duckfest:
      name: Duckfest 2023
      date: 2023-06-03
      location: 15 Yemen Road, Yemen
      items:
        venue:  15000
        av:      8000
        travel:  5000
        swag:    1000

The actuals file is a list of transactions (or group transactions together
if they're for the same items if you like). Each transation item is a
dictionary with the following keys:

* `date`: When the transaction happened.

* `event`: The event identifier to match in the budget file.

* `item`: The item identifier to match in the budget file.

* `amount`: How much, as a number. We don't do currency conversions, so
  figure out the right number before recording it.

* `note`: Some note about what this transaction is.

Here's an example:

    - date:   2023-05-20
      event:  duckfest
      item:   swag
      amount: 870
    - date:   2023-06-03
      event:  duckfest
      item:   venue
      amount: 12350
    - date:   2023-06-03
      event:  duckfest
      item:   av
      amount: 8600
    - date:   2023-06-10
      event:  duckfest
      item:   travel
      amount: 2100
      note:   "Flight and hotel for Rupert"
    - date:   2023-06-12
      event:  duckfest
      item:   travel
      amount: 2400
      note:   "Flight and hotel for Wanda"

It's perfectly ok to add information using other keys to the dictionaries
for budgets or events. budgetxt will just ignore any keys it's not looking
for. But keep in mind we might add functionality later that uses new keys,
so maybe prefix your keys or something to avoid conflict if you're worried
about that.

