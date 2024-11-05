# Middleware

A common way of organizing shared functionality for many(or even all) HTTP requests is to set it up as middleware.

middleware base idea: insert another handler into chain of `ServeHTTP()`.

```go
func MyMiddleware(next http.Handler) http.Handler {
    fn := func (w http.ResponseWriter, r *http.Request) {
        // some logic...
        next.ServeHTTP(w, r)
    }

    return http.HandlerFunc(fn)
}
```

**Note:** Regardless of what you do with a closure it will always be able to acess the variables that are local to the scope it was created (e.g. `next`).

```go
fund MyMiddleware(next http.Handler) http.Handler {
    return http.HandlerFunc(func (w http.ResponseWriter, r *http.Request) {
        // Any code here will execute on the way down the chain
        next.ServeHttp(w, r)
        // Any code here will execute on the way up the chain
    }
}
```

## Middleware possinion

1. befoere the servemux: act on every request that your application receives.

`middleware -> servemux -> hanlder`

**Use Case:** log requests

2. after servemux chaing, by wrapping a specific application handler. only executed for a specific route

`servemux -> middleware -> hanlder`

**Use Case:** Authorization

## Middleware Example: Security Headers

References:

- [OWASP Secure Headers Project](https://owasp.org/www-project-secure-headers/)
- [Content Security Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP)

### Early Returns

if you call `return` in you middleware function before you call `next.ServeHTTP()`, then the chain will stop beign executed and controll will flow back upstream.

**Use Case:** Authentication middleware (only allows execution of the chain to continue if a particular check is passed)

## Middleware Example: Request Logging

It is perfectly valid to implement the middleware as am method on `application`.

```go
func (app *Application) logRequest(next http.Handler) http.Handler {
    return http.HandleFunc(func (w http.ResponseWriter, r *htpp.Request) {
        // log with app.Logger

        next.ServeHTTP(w, r)
    })
}
```

## Panic Recovery

Go's HTTP server assumes that the effect of any panic is isolated to the goroutine serving the active HTTP request.
so importantly, any panic in your handlers won't bring down your server. but it show Empty response to user.

`Connection: Close` header automatically close the current connection after a response has been sent.

The value returned by builtin `recover()` function has type `any`(whatever the parameter passed to `panic()`).

**Note:** if you have a handler which spins up another goroutine then any panics that happen in the second goroutine will not be recovered.

```go
func myHandler(w http.ResponseWriter, r *http.Request) {
    // ...
    go func() {
        defer func {
            if err := recover(); err != nil {
                // handle error
            }
        }()

        doSomeBackgroundProcessing()
    }
}
```
