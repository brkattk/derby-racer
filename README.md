# Derby Racer

Pinewood Derby race management.

## Derby Rules

Each __Division__ will have enough __Heats__ so that every __Contestant__ has a _Run_
on each lane of the track. The __Division__ standings are based on the cumulative/
average time for each of its __Contestants__ _Runs_.

At the end of all the divisional __Heats__ a final set of __Heats__ will be created
and will include the top `finalists_per_division` as configured for the __Derby__.

The same standings calculation be used for the final __Heats__.

At the end of all the __Heats__, the top 3 __Contestants__ from each division will
be displayed. In addition, the top 3 overall __Contestants__ will be displayed.
There will also be time-based top __Contestants__ for fastest and slowest single
run from the entire __Derby__.

## Models

### Derby

A __Derby__ is a race event, so an example would be "Pack 50 Pinewood Derby 2015".
An administrator can configure a __Derby__ with three settings:

- `lanes`: the number of lanes that will be used on the track.
- `finalists_per_division`: the number of finalists to take from each __Division__
after the heats for that __Division__ are complete.
- `run_final`: option to run a final round of __Heats__ to determine an overall
winner.

#### Schema

- `name` (string)
- `lanes` (integer)
- `finalists_per_division` (integer)
- `run_finals` (boolean)
- `created_at` (datetime)
- `updated_at` (datetime)

### Division

A __Division__ is a group of __Contestants__, such as "Wolf", "Tiger", etc.
Administrators have the option to include a division in the finals or not,
depending on whether the __Derby__ has finals. Administrators may adjust the
`sequence` in which divisional heats are run, in case you wanted the "Tiger"
group to go before the "Wolf" group, for example.

#### Schema

- `derby_id` (integer)
- `sequence` (integer)
- `name` (string)
- `include_in_finals` (boolean)
- `created_at` (datetime)
- `updated_at` (datetime)

### Contestant

A __Contestant__ belongs to a division. If a __Contestant__ is not `checked_in`,
they will not be included when calculating __Heats__.

#### Schema

- `division_id` (integer)
- `name` (string)
- `nickname` (string)
- `car_number` (string)
- `checked_in` (boolean)
- `divisional_points` (integer) default: 0
- `final_points` (integer) default: 0
- `created_at` (datetime)
- `updated_at` (datetime)


### Heat

A __Heat__ should automatically be generated for a division based on the
associated __Contestants__ that are `checked_in` for the __Derby__. __Heats__ are run
according to their `division_id` and `sequence`.

A __Heat__ can be one of two types:

  - `divisional`: A normal heat for a __Division__ of __Contestants__
  - `final`: A heat for the top finalists from divisional heats.

A __Heat__ can have three different statuses:

  - `upcoming`: This heat has not been run.
  - `current`: This heat is currently awaiting finishing times.
  - `complete`: This heat has recorded _Run_ results.

#### Schema

  - `type` (string)
  - `sequence` (integer) index[division_id]
  - `status` (string) default: upcoming
  - `created_at` (datetime)
  - `updated_at` (datetime)

### Run

A _Run_ is created for each __Contestant__ in a __Heat__. These are the race results
for a __Heat__.

#### Schema

  - `contestant_id` (integer) index
  - `heat_id` (integer) index
  - `lane` (integer)
  - `time` (decimal) index
  - `created_at` (datetime)
  - `updated_at` (datetime)


Contributing
------------

This derby-racer application is Copyright 2015 Erik Straub, and is distributed
under the GNU Affero General Public License (see LICENSE). This license requires
that you provide the source code to users of this application.
