# go-glitch
This package is designed to help with handling errors from databases and web services.  Ideally the web services you are calling are returning HTTP problem responses which
can be unmarshalled in the the `glitch.HTTPProblem` struct and then converted into an `glitch.DataError` before being returned by the client logic.  Similarly your data layer 
can process database errors and convert them into `glitch.DataError` and then return them.  Doing this allows the logic layer of your code to `switch` on the error `Code()` and
handle each machine readable code specifically.

E.G.

```go
user, err := db.GetUser(id)
if err != nil {
    var status int
    switch err.Code() {
        case userNotFound:
            status = http.StatusNotFound
        case dbConnection:
            status = http.StatusServiceUnavailable
        default:
            status = http.StatusInternalServerError
    }

    httpErr := glitch.HTTPProblem{
        Status: status,
        //...
    }

    ret, _ := json.Marshal(httpErr)
    w.WriteHeader(status)
    w.Write(ret)
    return
}

```
