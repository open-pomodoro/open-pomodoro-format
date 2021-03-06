# Open Pomodoro Format

This document describes a collection of files and their formats for reading and writing [Pomodoro](https://en.wikipedia.org/wiki/Pomodoro_Technique) data.
The goal of this format is for a user to by able to use any Pomodoro application or utility, and freely switch between them without losing data.

## Terminology

### States

#### Inactive

> No Pomodoro is currently active.

The application should not show a timer, or show the default duration statically.
The user should have the option to *start* a Pomodoro.

#### Active

> A Pomodoro has been started, and the duration has not yet elapsed.

The application should show the time remaining on the Pomodoro
The user should have options to *finish* the Pomodoro early or *cancel* the Pomodoro.

#### Done

> A Pomodoro was active, and then the duration elapsed.

The application should not show a timer, or show that the time remaining is zero.
An alert should be displayed.
The user should have options to *clear* the Pomodoro, *start* a new Pomodoro, or *finish* a Pomodoro late.

### Actions

#### Start

Start a Pomodoro at the current time.

The user may be given options to specify the duration, description, or tags for the Pomodoro.

#### Finish

Finish a Pomodoro early (or late).

Otherwise, Pomodoros are considered finished after the duration, which defaults to 25 minutes.

#### Clear

Clear the existing Pomodoro, if any.

## Attributes

This format recognizes the following attributes relevant to each Pomodoro:

### Format

All attributes, except for the timestamp, are stored in [logfmt](https://brandur.org/logfmt).

For example:

```
duration=25 description="Open Pomodoro Standard" tags=personal,writing
```

### `start_time`

The time when the Pomodoro started.
This must be formatted as [RFC 3339](https://en.wikipedia.org/wiki/RFC_3339).
The offset from UTC must be included in the timestamp in order to preserve local dates.

For example:

```
2016-06-03T17:01:31-04:00
```

### `duration` (optional)

The length of the Pomodoro in minutes.
This defaults to 25.

### `description` (optional)

A description of the task to be worked on during the Pomodoro.

### `tags` (optional)

A set of relevant tags for the Pomodoro.
The tags are comma-separated, and may contain any characters except for comma.

## Files

```
~/
└── .pomodoro/
    ├── current
    ├── history
    └── settings
```

All files are kept in a directory, defaulting to `~/.pomodoro/`.
There are three files within this directory:

* **current** The current Pomodoro
* **history** A log of every Pomodoro with related information
* **settings** Custom user settings

### `current`

The `current` file must begin with the timestamp on the first line.
Other attributes in logfmt may follow the timestamp.

The end time of the Pomodoro is calculated by adding the duration to the timestamp.
The duration defaults to 25 minutes, but can be overridden by user defaults, or user input when the Pomodoro is started.

During a Pomodoro, the end time will be in the future.
If the `current` file has a timestamp and the end time is in the past, the Pomodoro has done, and the user has not acknowledged it yet.
If the `current` file is missing or empty, there is no current Pomodoro (inactive).

```
2016-06-03T18:14:21-04:00 duration=25 description="Open Pomodoro Standard" tags=personal,writing
```

### `history`

Each line represents a single Pomodoro.
The timestamp must be at the beginning of the line.
Attributes may follow the timestamp.

```
2016-06-03T17:01:31-04:00 duration=25 description="Open Pomodoro Standard" tags=personal,writing
2016-06-03T17:32:49-04:00 duration=25 tags=work
2016-06-03T18:14:21-04:00 duration=25 tags=work
```

### `settings`

Each line is a key/value pair of a valid setting.
The key and value are separated with an equal sign, similar to the attributes format:

```
daily_goal=8
default_break_duration=5
default_pomodoro_duration=25
default_tags=work
```

For command-line applications implementing this standard, a settings file in the current directory (`./.pomodoro/settings`) should overwrite the user defaults if it exists.
