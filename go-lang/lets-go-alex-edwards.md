# Let's Go

## Foundations

### Project Structure and Organization

References:

- [Go best practices, six years in](https://peter.bourgon.org/go-best-practices-2016/#repository-structure)

```
github.com/peterbourgon/foo/
  circle.yml
  Dockerfile
  cmd/
    foosrv/
      main.go
    foocli/
      main.go
  pkg/
    fs/
      fs.go
      fs_test.go
      mock.go
      mock_test.go
    merge/
      merge.go
      merge_test.go
    api/
      api.go
      api_test.go
```

### Interface

References

- [Goalng Interfaces Explained](https://www.alexedwards.net/blog/interfaces-explained)

```go
type <type-name> interface {
    // field and methods e.g.
    // String() string
}

// receiver function (type-name could be pointer to change received argument)
func (<arg-name> <type-name>) String() string [
    // ... some code ...
}
```

we say that something satisfies an interface if it has methods with the exact signature(e.g. `http.ResponseWriter` implements `io.Writer`)

**Note:** Wherever you see declaration in Go (such as a variable, function parameter or struct field) which has an interface type, you can use an object of any type **so long as it satisfies the interface**.

```go
fund WriteLog(s fmt.Stringer) {
    log.Print(s.String())
}
```

Interface Usages

1. To help reduce duplication or boilerplate code.
2. To make it easier to use mocks instead of real objects in unit tests.
3. As an architectural tool, to help enforce decoupling between parts of your codebase.

Them **Empty Interface** type essentially describe no methods. It has no rules. And because of that, it follows that any and every object satisfies the empty interface.(something like wildcard)

**Note:** `http.HandleFunc` transorms function to interface with `ServeHTTP`.

**Note:** You can only define methods on a type defined in the same package. if you need this there are 2 sultions:

1. make a normal function instead of a method and pass the interface as an argument.
2. invert the dependency(e.g. import `entity` package in `db` and write a method/function)

### Configuration

1. cli falgs

read flags this way:

```go
  import "flag"

  type config struct {
     addr  string
  }

  var cfg config

  // read flags
  addr := flag.StringVar(&cfg.addr, "addr", ":4000", "HTTP netowrk address")

  // prase all flags
  flag.Parse()
```

third argument is default value for flag.

you can see automated help with `go run ./cmd/web -help` command.

2. environment variables

env is a good practice for passing configs but this has some drawbacks compared to using command-line flags. You can’t specify a
default setting (the return value from `os.Getenv()` is the empty string if the environment
variable doesn’t exist), you don’t get the `-help` functionality that you do with command-line
flags, and the return value from `os.Getenv()` is always a string — you don’t get automatic
type conversions like you do with `flag.Int()` and the other command line flag functions.

**Note:** you can get the best of both worlds like this way:

```bash
export CONFIG_ADDR=":4321"
go run ./cmd/web -addr=$CONFIG_ADDR
```

**Note:** another good practice for handling configurations is (YAML files)[https://pkg.go.dev/gopkg.in/yaml.v3]

### Leveled Logging

Good References:

- [GoLang Mentorship - part 1](https://x.com/mr_pouriya/status/1722717561551311284)
- [GoLang Mentorship - part 2](https://x.com/mr_pouriya/status/1725254665468813813)
- [GoLang Mentorship - part 3](https://x.com/mr_pouriya/status/1727706930771103962)
- [GoLang Mentorship - part 4](https://x.com/mr_pouriya/status/1736030373094510670)

- [SysLog Wikipedia](https://en.wikipedia.org/wiki/Syslog)
- [SysLog RFC](https://datatracker.ietf.org/doc/html/rfc5424)
- [Log Levels Explained and How to Use Them](https://betterstack.com/community/guides/logging/log-levels-explained/)

By default, if Go's HTTP server encounters an error it will log it iwth standard logger. we can chagne this behavior by initializing new `http.Server` with our configurations.

```go
import "net/http"

server := &http.Server{
    Addr: configs.port,
    Handler: mux,
    ErrorLog: YourErrorLogger,
    // ...
}
err := server.ListenAndServe()
// log error with your configured logger
```

**Note:** custom loggers created by `log.New()` are concurrency-safe.

**log severity levels**

| value |   Severity    |  Keyword  |
| :---: | :-----------: | :-------: |
|   0   |   Emergency   |  `emerg`  |
|   1   |     Alert     |  `alert`  |
|   2   |   Critical    |  `crit`   |
|   3   |     Error     |   `err`   |
|   4   |    Warning    | `warning` |
|   5   |    Notice     | `notice`  |
|   6   | Informational |  `info`   |
|   7   |     Debug     |  `debug`  |

**Note:** The server process which handles display of messages usually includes all lower (more severe) levels when display of less severe levels is requested

##### Logging Good Practice

- set log level to DEBUG after a while (2 or 3 days) set INFO or ERROR level.
- set DEBUG level in test environment and INFO in production environment.

### Dependency Injection

References:

- [Organising Database Access in Go](https://www.alexedwards.net/blog/organising-database-access)
- [Reddit: Go best practice for accessing database in handlers?](https://www.reddit.com/r/golang/comments/38hkor/go_best_practice_for_accessing_database_in/)

Most web apps will have multiple dependencies that their handle need to access. (e.g. database connection pool, centeralized error handlers, template caches, ...)

### Database

define models

the idea is that we will encapsulate the code for working with sql in a separate package.

create directory for models(e.g. `internal/models/...`). then create model files like this:

```go
type Entity1 struct {
    ID int
    // ...
}

type Entity1Model struct {
    // your database instance (e.g. DB *sql.DB)
}

// define operations like insert, get, delete, ...
func (m *Entity1Model) Insert () (int, error) {
    // execute your query on m.DB
    return 0, nil
}
```

##### **Benefits of this structure**

1. separate database logic and http stuffs.
2. easy initialization and using with dependency Injection
3. there's the opportunity to create an interface and mock it for unit testing.

### Executing queries

- `DB.Query()` -> select multiple rows
- `DB.QueryRow()` -> select at msot 1 row
- `DB.Exec)` -> statement without returen value like (insert delete)

the above functions use prepared statement behind the scenes to help prevent SQL injection.
In theory, a better approach could be to make use of the `DB.Prepare` method to create your own prepared statement once.

`rows.Scan()`: will automatically convert the raw output from the SQL database to the required native Go types. Usually:

- `CHAR`, `VARCHAR`, `TEXT` map to `string`
- `BOOLEAN` maps to `bool`
- `INT` maps to `int`
- `BIGINIT` maps to int64
- `DECIMAL`, `NUMERIC` maps to `float`
- `TIME`, `DATE`, `TIMESTAMP` map to `time.Time`

##### How to check whether an error matches a specific value?

```go
import "errors"

errors.Is(err, someError)
```
