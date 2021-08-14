# Telebot Template

```bash
$ git clone https://github.com/go-telebot/template .
$ chmod +x init.sh; ./init.sh
NOTE The script will delete itself after the configuration.

Project name: yourbot
Module path: github.com/username/yourbot

Dialect (sqlite3|mysql|postgres): postgres
Driver (github.com/lib/pq):
```

## Overview

This is a project structure template for bots developed using [`telebot.v3`](https://github.com/tucnak/telebot/tree/v3). There are two ways of organizing the root, and the version in this branch sticks to [this project structure](https://github.com/golang-standards/project-layout). Prefer [a simpler layout](https://github.com/go-telebot/template/tree/alt) for simpler apps without `pkg` and `internal` directories, keeping every package in the root. But, in the huge projects, where you may have lots of packages as has to be hidden, as exposed, the separation becomes really useful and much more convenient.

So, this is a good example of structuring your advanced bot, when there is too much code for an ordinary `main.go` file.

## Directories

### `/`

The root package, with the name of the module, usually should contain highly generic data. Consider storing some kind of _bootstrap_ structure here, which defines the basic dependencies required for a successful application startup. It should be used later in `/internal` subpackages for proper initializing.

### `/locales`

This directory consists of bot locales in `*.yml` files respectively to the `telebot/layout` format. If you don't need localization in your project, leave a single file and specify its locale code in a `lt.DefaultLocale("..")` call.

### `/sql`

Optional, if you don't use a relational database. It's a directory of `*.sql` files, formatted in a way to use with `goose` migration tool.

### `/cmd`

Main binaries for the project. The directory name for each application should match the name of the executable you want to have. Use `/cmd/bot` for the bot's primary executable. It is common to have a small main function that imports and invokes the code from the `/internal` and `/pkg` directories and nothing more.

### `/pkg`

Library code that's ok to use by external applications.

### `/internal`

Private application and library code. This is the code you don't want others importing into their applications or libraries. Note that this layout pattern is enforced by the Go compiler itself.

### `/internal/bot`

The core of the bot. It consists of all the handlers and bot behavior, logically grouped in the files. Use `bot/middle` subpackage storing custom middlewares.

For example, imagine your bot need to have settings implemented as an inline menu sent on the `/settings` command. There are several parameters to be configured, let's say the user's name and delivery address. Where you should put this logic? The best place is `settings.go` file in the `bot` package with three functions inside, which are responsible for sending settings menu, asking for a new value to update, and actual updating operation of the specific setting. That way we have three ascending actions relying on each other, and it makes them intuitive by gathering in one place.

### `/internal/database`

A wrapper to simplify the communication with your database. If you're ok with using ORM in your projects, then most likely there is no need for you in this package.

### `/internal/worker`

This becomes useful when you have some background routine to do. One file for each *worker* logic accordingly.

```go
boot := Bootstrap{...}

go worker.ProcessPayments(boot)
go worker.CollectStatistics(boot)

b.Start()
```
