# Routing

References:

- [Routing Enhancements for Go 1.22](https://go.dev/blog/routing-enhancements)

The new routing features almost exclusively affect the pattern string passed to the two `net/http.ServeMux` methods `Handle` and `HandleFunc`, and the corresponding top-level functions `http.Handle` and `http.HandleFunc`. The only API changes are two new methods on `net/http.Request` for working with wildcard matches.

```go
func handlePost(w http.ResponseWriter, r *http.Request) {
    id := r.PathValue("id")

    // do something with id parameter...
}

http.HandleFunc("GET /posts/{id}", handlePost)
```

**Note:** Some routers disallow overlaps; others use the pattern that was registered last. Go has always allowed overlaps, and has chosen the longer pattern regardless of registration order
