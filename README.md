# Open Pomodoro Format

This document describes a collection of files and their formats for reading and writing [Pomodoro](https://en.wikipedia.org/wiki/Pomodoro_Technique) data.
The goal of this format is for a user to by able to use any Pomodoro application or utility,
and freely switch between them without losing data.

## Attributes

This format recognizes the following attributes relevant to each Pomodoro:

### timestamp

The time when the Pomodoro started.
This must be formatted as [RFC 3339](https://en.wikipedia.org/wiki/RFC_3339).
The offset from UTC must be included in the timestamp in order to preserve local dates.

For example:

```
2016-06-03T17:01:31-04:00
```

### duration (optional)

The length of the Pomodoro in minutes.
This defaults to 25.

### description (optional)

A description of the task to be worked on during the Pomodoro.

### tags (optional)

A set of relevant tags for the Pomodoro.
The tags are comma-separated, and may contain any characters except for comma.

## Attribute Format

All attributes except for the timestamp are stored in [logfmt](https://brandur.org/logfmt).
For example:

```
duration=25 description="Open Pomodoro Standard" tags=personal,writing
```

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

The `current` file must contain only the timestamp on the first line.
The second line should include other attributes in logfmt.

The end time of the Pomodoro is calculated by adding the duration to the timestamp.
The duration defaults to 25 minutes, but can be overridden by user input or user defaults when the Pomodoro is started.

During a Pomodoro, the end time will be in the future.
If the `current` file has a timestamp and the end time is in the past, the Pomodoro has ended, and the user has not acknowledged it yet.
If the `current` file is missing or empty, there is no current Pomodoro.

### `history`

Each line represents a single Pomodoro.
The timestamp must be at the beginning of the line.
Attributes should follow the timestamp.

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
