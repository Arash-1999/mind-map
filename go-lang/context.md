# Using Request Context

### How Request context works

Every `http.request` that our middleware and handlers process has a `context.Context` object embedded in it, Which we can use to store information during the lifetime of the request.

**Common Use Csae:** pass information between your pieces of middleware and other handlers.

**Example:** check if a user is authenticated once in some middleware, then make user information available to all other middlewares.

**Note:** requext context values are stored with the type `any`.

**Note:** it's good practice to create your own custom type to prevent key collision.

```go
type contextKey string
const someContextKey = contextKey("isAuthenticated")

// write data in request context
ctx := context.WithValue(r.Context(), someContextKey, true) // r *http.Request
r = r.WithContext(ctx)

// read data from request context
// syntax: <name>, ok := r.Context().Value(<name>).(<type>)
isAuthenticated, ok := r.Context().Value(someContextKey).(bool)
if !ok {
    return erros.New("could not convert value to bool")
}
```

**Note:** the above snippet creates a new copy of the `http.Request` object with our new context in it.

**Note:** Use Context Values only for request-scoped data that transits processes and APIs.
