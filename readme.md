Hugo iCalendar Templates
========================

About
-----

A set of simple templates for [Hugo](https://gohugo.io/) to generate
[iCalendar](https://en.wikipedia.org/wiki/ICalendar) files.

This project does *not* provide a complete implementation of all features
the iCalendar specification contains. It rather follows the 80/20 rule and
with a special focus on *event data*.

All the templates strive to be RFC compliant and produce output files
that adhere to the specification. No special hacks are included
to work around broken calendar software or the like.


This project does not provide a full turn-key solution and some
assembly will be required in most cases. Understanding of Hugo and
especially Hugo's [templating system](https://gohugo.io/templates/)
is still recommended.

The system is highly flexible and should adapt or extend easily to more
exotic use cases. On some spots the chosen defaults might be a bit opinionated.


The partial template snippets from this project should help to easily avoid the
most common mistakes when creating `ics` files. However, there is absolutely
no validation, neither on the syntactic nor the semantic level. You can always
use an external [validation service](https://icalendar.org/validator.html)
to check the output.


Usage
-----

### Step 1

Copy the partial templates from `src/layouts/partials/ical/*.ics` to your
project's layout directory. Usually this would be `.../layouts/partials/ical/`.

See also (Hugo documentation):
- [partials](https://gohugo.io/templates/partials/)


### Step 2

Have template files for the `Calendar` output format. Examples
for a single page (`single.calendar.ics`) and a list page
(`list.calendar.ics`) can be found in this repository.

See also (Hugo documentation):
- [output format](https://gohugo.io/templates/output-formats/)
- [single page templates](https://gohugo.io/templates/single-page-templates/)
- [list page templates](https://gohugo.io/templates/lists/)


### Step 3

Activate and configure the `Calendar` output format, either in your `config.toml`
or more with fine-grained control via the content front matter.

For example (in `toml` syntax) activate via:
```
[outputs]
  page = ["HTML", "Calendar"]
  section = ["HTML", "Calendar"]
```

You need to configure this output format a bit:
```
[outputFormats.Calendar]
  baseName = "calendar"
  # Avoid unencrypted webcal scheme
  protocol = "https://"
  permalinkable = true
```

See also (Hugo documentation):
- [configure output formats](https://gohugo.io/templates/output-formats/#configure-output-formats)


### Step 4

Feed in your own data from arbitrary Hugo context variables into the calendar
templates from step 2. See the `header.ics` and `event.ics` files in this
repository for a simple example.

Note: While it can be unavoidable for certain edge cases to directly adapt the
      top level partials from step 2 this should in general not be necessary.


Collect and add all timezone definitions you need to the `timezone.ics` partial.

Note: You can download ready-made `ics` files for all well known
      time zones from [http://tzurl.org](http://tzurl.org).

For example for `Europe/Zurich`:

* [http://tzurl.org/zoneinfo-outlook/Europe/Zurich](http://tzurl.org/zoneinfo-outlook/Europe/Zurich)


### Step 5

Link the generated `ics` files for download on your `html` pages.

A snippet like this should do:
```
{{ with .OutputFormats.Get "Calendar" }}
    <a href="{{ .RelPermalink }}" type="text/calendar">{{ $.Title }}</a>
{{ end }}
```

See also (Hugo documentation):
- [link to output formats](https://gohugo.io/templates/output-formats/#link-to-output-formats)
- [get another output format](https://gohugo.io/content-management/cross-references/#get-another-output-format)
- [reference your rss feed in head](https://gohugo.io/templates/rss/#reference-your-rss-feed-in-head)


Demo Site
---------

A fully working minimal site can be found in [this repository](https://github.com/raoulb/hugo-ical-templates-demo).


Known Issues
------------

### No folding of long lines

Due to the way the templates work, we do not fold long lines.
However, this is actually fine as the RFC writes *SHOULD*
instead of *MUST*:

> Lines of text SHOULD NOT be longer than 75 octets, excluding the line
> break.  Long content lines SHOULD be split into a multiple line
> representations using a line "folding" technique.

See: https://tools.ietf.org/html/rfc5545#section-3.1


### No `CRLF` line termination

This is the one place where we knowingly break RFC compliance.
While this is not correct per se, it hopefully is a minor issue
with today's calendar software.

> The iCalendar object is organized into individual lines of text,
> called content lines.  Content lines are delimited by a line break,
> which is a CRLF sequence (CR character followed by LF character).

See: https://tools.ietf.org/html/rfc5545#section-3.1


### Empty lines

The generated `ics` files often contain many completely empty lines.
This could in principle be avoided by more careful
[whitespace control](https://gohugo.io/templates/introduction/#whitespace)
inside the templates or some post processing. (Unfortunately Hugo
does not come with a suitable
[asset minification](https://gohugo.io/hugo-pipes/minification/) method.)


Major TODO
----------

- Recurrence rules
- Timezone component

In principle all missing parts (properties, parameters, components) could
be handled by the same way. They were just not in focus for the primary
application envisioned: distributing machine-readable information about
upcoming events directly from within Hugo.


Specification
-------------

- RFC 5545:
  [Internet Calendaring and Scheduling Core Object Specification (iCalendar)](https://tools.ietf.org/html/rfc5545)

- RFC 7986:
  [New Properties for iCalendar](https://tools.ietf.org/html/rfc7986)


Implementation Status
---------------------

Note: This list tracks the implementation by partial template snippets.
      Some properties like 3.7.x are hardcoded in the top level templates.
      The `Time Zone Component` is handled differently (see step 4 above).


From [rfc 5545](https://tools.ietf.org/html/rfc5545)

3.2.  Property Parameters

- [x]   3.2.1.  Alternate Text Representation
- [x]   3.2.2.  Common Name
- [ ]   3.2.3.  Calendar User Type
- [ ]   3.2.4.  Delegators
- [ ]   3.2.5.  Delegatees
- [x]   3.2.6.  Directory Entry Reference
- [ ]   3.2.7.  Inline Encoding
- [x]   3.2.8.  Format Type
- [ ]   3.2.9.  Free/Busy Time Type
- [x]   3.2.10.  Language
- [ ]   3.2.11.  Group or List Membership
- [ ]   3.2.12.  Participation Status
- [ ]   3.2.13.  Recurrence Identifier Range
- [ ]   3.2.14.  Alarm Trigger Relationship
- [ ]   3.2.15.  Relationship Type
- [ ]   3.2.16.  Participation Role
- [ ]   3.2.17.  RSVP Expectation
- [ ]   3.2.18.  Sent By
- [x]   3.2.19.  Time Zone Identifier
- [x]   3.2.20.  Value Data Types

3.3.  Property Value Data Types

- [ ]   3.3.1.  Binary
- [x]   3.3.2.  Boolean
- [x]   3.3.3.  Calendar User Address
- [x]   3.3.4.  Date
- [x]   3.3.5.  Date-Time
- [x]   3.3.6.  Duration
- [x]   3.3.7.  Float
- [x]   3.3.8.  Integer
- [ ]   3.3.9.  Period of Time
- [ ]   3.3.10.  Recurrence Rule
- [x]   3.3.11.  Text
- [x]   3.3.12.  Time
- [x]   3.3.13.  URI
- [ ]   3.3.14.  UTC Offset

3.6.  Calendar Components

- [x]   3.6.1.  Event Component
- [ ]   3.6.2.  To-Do Component
- [ ]   3.6.3.  Journal Component
- [ ]   3.6.4.  Free/Busy Component
- [ ]   3.6.5.  Time Zone Component
- [ ]   3.6.6.  Alarm Component

3.7.  Calendar Properties

- [ ]   3.7.1.  Calendar Scale
- [ ]   3.7.2.  Method
- [ ]   3.7.3.  Product Identifier
- [ ]   3.7.4.  Version

3.8.  Component Properties

3.8.1.  Descriptive Component Properties

- [ ]   3.8.1.1.  Attachment
- [ ]   3.8.1.2.  Categories
- [x]   3.8.1.3.  Classification
- [x]   3.8.1.4.  Comment
- [x]   3.8.1.5.  Description
- [x]   3.8.1.6.  Geographic Position
- [x]   3.8.1.7.  Location
- [ ]   3.8.1.8.  Percent Complete
- [ ]   3.8.1.9.  Priority
- [ ]   3.8.1.10.  Resources
- [x]   3.8.1.11.  Status
- [x]   3.8.1.12.  Summary

3.8.2.  Date and Time Component Properties

- [ ]   3.8.2.1.  Date-Time Completed
- [x]   3.8.2.2.  Date-Time End
- [ ]   3.8.2.3.  Date-Time Due
- [x]   3.8.2.4.  Date-Time Start
- [x]   3.8.2.5.  Duration
- [ ]   3.8.2.6.  Free/Busy Time
- [x]   3.8.2.7.  Time Transparency

3.8.3.  Time Zone Component Properties

- [ ]   3.8.3.1.  Time Zone Identifier
- [ ]   3.8.3.2.  Time Zone Name
- [ ]   3.8.3.3.  Time Zone Offset From
- [ ]   3.8.3.4.  Time Zone Offset To
- [ ]   3.8.3.5.  Time Zone URL

3.8.4.  Relationship Component Properties

- [ ]   3.8.4.1.  Attendee
- [x]   3.8.4.2.  Contact
- [x]   3.8.4.3.  Organizer
- [ ]   3.8.4.4.  Recurrence ID
- [ ]   3.8.4.5.  Related To
- [x]   3.8.4.6.  Uniform Resource Locator
- [x]   3.8.4.7.  Unique Identifier

3.8.5.  Recurrence Component Properties

- [ ]   3.8.5.1.  Exception Date-Times
- [ ]   3.8.5.2.  Recurrence Date-Times
- [ ]   3.8.5.3.  Recurrence Rule

3.8.6.  Alarm Component Properties

- [ ]   3.8.6.1.  Action
- [ ]   3.8.6.2.  Repeat Count
- [ ]   3.8.6.3.  Trigger

3.8.7.  Change Management Component Properties

- [x]   3.8.7.1.  Date-Time Created
- [x]   3.8.7.2.  Date-Time Stamp
- [x]   3.8.7.3.  Last Modified
- [x]   3.8.7.4.  Sequence Number

3.8.8.  Miscellaneous Component Properties

- [ ]   3.8.8.1.  IANA Properties
- [ ]   3.8.8.2.  Non-Standard Properties
- [ ]   3.8.8.3.  Request Status


From [rfc 7986]( https://tools.ietf.org/html/rfc7986)

5.  Properties

- [x]   5.1.  NAME Property
- [x]   5.2.  DESCRIPTION Property
- [x]   5.3.  UID Property
- [x]   5.4.  LAST-MODIFIED Property
- [x]   5.5.  URL Property
- [ ]   5.6.  CATEGORIES Property
- [x]   5.7.  REFRESH-INTERVAL Property
- [x]   5.8.  SOURCE Property
- [x]   5.9.  COLOR Property
- [x]   5.10.  IMAGE Property
- [ ]   5.11.  CONFERENCE Property

6.  Property Parameters

- [x]   6.1.  DISPLAY Property Parameter
- [x]   6.2.  EMAIL Property Parameter
- [ ]   6.3.  FEATURE Property Parameter
- [ ]   6.4.  LABEL Property Parameter


Etc
---

Hopefully, this whole project will some day be superseded by something
better, maybe even directly built into Hugo.
