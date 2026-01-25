# json
## HTTP Clients
So far, you have probably been using a browser to test your server. That works fine with simple `GET` requests (the kind of request a browser sends when you type a URL into the address bar), but it's not very useful for any other HTTP methods or requests with custom headers and bodies.

## Debugging Your Endpoints
Servers are built to be used by clients. As you develop your code, you should be using a tool that makes sending one-off requests to your server easy! Here are some of my favorites:
- REST Client for VS Code
- Postman for VS Code
- cURL
- Postman

## JSON
It's very common for `POST` requests to send JSON data in the request body. Here's how you can handle that incoming data.

### Decode JSON request body
```
{
  "name": "John",
  "age": 30
}
```
```
func handler(w http.ResponseWriter, r *http.Request){
    type parameters struct {
        Name string `json:"name"`
        Age int `json:"age"`
    }

    decoder := json.NewDecoder(r.Body)
    params := parameters{}
    err := decoder.Decode(&params)
    if err != nil {
		log.Printf("Error decoding parameters: %s", err)
		w.WriteHeader(500)
		return
    }
    // params is a struct with data populated successfully
    // ...
}
```

The struct tags (e.g. `'json:"name"'`) indicates how the keys in the JSON should be mapped to the struct fields. The struct fields themselves must be exported (start with a capital letter) if you want them parsed.

`decoder.Decode()` will return an error if the JSON is invalid or has the wrong types, and any missing fields will simply have their values in the struct set to their zero value.

### Encode JSON response body
```
func handler(w http.ResponseWriter, r *http.Request){
    // ...

    type returnVals struct {
        CreatedAt time.Time `json:"created_at"`
        ID int `json:"id"`
    }
    respBody := returnVals{
        CreatedAt: time.Now(),
        ID: 123,
    }
    dat, err := json.Marshal(respBody)
	if err != nil {
			log.Printf("Error marshalling JSON: %s", err)
			w.WriteHeader(500)
			return
	}
    w.Header().Set("Content-Type", "application/json")
    w.WriteHeader(200)
    w.Write(dat)
}
```

Again, we will use the struct tags to specify how the field names will be encoded in the JSON data. If you omit the tags, the keys will be encoded as the same names of struct fields (e.g. `CreatedAt`, `ID`)

## The Profane
Not only do we validate chirps that are under 140 characters, but we also have a list of words that are not allowed

### Assignment
We need to update the `/api/validate_chirp` endpoint to replace all "profane" words with 4 asteriks `****`.

Assuming the length validation passed, replace any of the following words in the Chirp with the static 4-character string `****`:
- kerfuffle
- sharbert
- fornax

Be sure to match against uppercase versions of the words as well, but not punctuation. "Sharbert!" does not need to be replaced, we'll consider it a different word due to the exclamation point. Finally, instead of the valid boolean, your handler should return the cleaned version of the text in a JSON response: