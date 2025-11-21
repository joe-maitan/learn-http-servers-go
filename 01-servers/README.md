# http-servers
## Learn HTTP Servers
This course is a culmination of everything you've learned so far on the Boot.dev back-end track comes together. Building HTTP servers is the bread-and-butter of a backend developer's day-to-day work.

Goals of this course:
- Understand what web servers are and how they power real-world web applications.
- Build a production-style HTTP server in Go, without the use of a framework.
- Use JSON, headers, and a status codes to communicate with clients via a RESTful API.
- Learn what makes Go a great language for building fast web servers.
- Use type safe SQL to store and retrieve data from a Postgres database.
- Implement a secure authentication/authorization system with well-tested cryptography libraries.
- Build and understand webhooks and API keys.
- Document the REST API with markdown.

###  What is a Server?
A web server is just a computer that servers data over a network, typically the internet. Servers run software that listens for incoming requests from clients. When a request is received, the server responds with the requested data.

Any server work its salt can handle many requests at the same time. In Go, we use a new goroutine for each request to handle them concurrently.

### Assignment
In this course, we'll be working on a product called "Chirpy". Chirpy is a social network similar to twitter.

One of Chirpy's servers is processing requests unbelieveably slowly. Use a goroutine to fix the bug in the `handleRequests` function. The server should be able to process all requests within the time limit.

Example Code:
```
package main

import (
	"fmt"
	"time"
)

func handleRequests(reqs <-chan request) {
	for req := range reqs {
		handleRequest(req)
	}
}

// don't touch below this line

type request struct {
	path string
}

func main() {
	reqs := make(chan request, 100)
	go handleRequests(reqs)
	for i := 0; i < 4; i++ {
		reqs <- request{path: fmt.Sprintf("/path/%d", i)}
		time.Sleep(500 * time.Millisecond)
	}

	time.Sleep(5 * time.Second)
	fmt.Println("5 seconds passed, killing server")
}

func handleRequest(req request) {
	fmt.Println("Handling request for", req.path)
	time.Sleep(2 * time.Second)
	fmt.Println("Done with request for", req.path)
}
```

Solution code:
```
func handleRequests(reqs <-chan request) {
	for req := range reqs {
		// use a goroutine to improve concurrency so multiple requests are processed concurrently.
        go handleRequest(req)
	}
}
```

## Goroutines in Servers
In Go, goroutines are used to server many requests at the same time, but not all servers are quite so performant.

Go was builtby Google, and one of the purposes of its creation was to power Google's massive web infrastructure. Go's goroutines are a great fit for web servers beacuse they're lighter weight than operating system threads, but still take advantage of multiple cores. Let's compare a Go web server's concurrency model to other popular languages and frameworks.

### Node.js / Express.js
In JavaScript land, servers are typically single-threaded. A Node.js server (often using the Express framework) only uses one CPU core at a time. It can still handle many requests at once by using the async event loop. That just meants whenver a rquest has to wait on I/O (like a database), the server puts it on pause and does something else for a bit.

### Takeaways
- Go servers are great for performance whether the workload is I/O or CPU-bound.
- Node.js and Express work well for I/O bound tasks, but struggle with CPU-bound tasks.

Go generally outperforms JavaScript when it comes to computational speed.