# routing

## Middleware
Middleware is a way to wrap a handler with additional functionality. It is a common pattern in web applications that allows us to write DRY code.

For example, we can write a middleware that logs every request to the server. We can then wrap our handler with this middleware and every request will be logged without us having to write the logging code in every handler.

To do that, we can write the middleware function like this:
```
func middlewareLog(next http.Handler) http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		log.Printf("%s %s", r.Method, r.URL.Path)
		next.ServeHTTP(w, r)
	})
}
```

Then, any handler that needs logging can be wrapped by this middleware function:
```
mux.Handle("/app/", middlewareLog(handler))
```

## Stateful Handlers
It's frequently useful to have a way to store and access state in our handlers. For example, we might want to keep track of the number of requests we've received, or we may want to pass around an open connection to a database, or credentials to an API.

### Assignment
The product managers at Chirpy want to know how many requests are being made to serve our homepage - in essence, they want to know how many people are viewing the site!

They have asked for a simple HTTP endpoint they can hit to get the number of requests that have been processed. It will return the count as plain text in the response body.

For now, they just want the number of requests that have been processed since the last time the server was started, we don't need to worry about saving the data between restarts.

