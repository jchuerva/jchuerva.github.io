---
title: Go - Build serverless apps - Web app
date: 2022-03-24 11:00:00 +00:00
tags: [golang]
description: Go - Build serverless apps - Web app
---

Source: [Build serverless apps with Go](https://docs.microsoft.com/en-us/learn/modules/serverless-go/)

The project structure is as follows:
```bash
hello/
  function.json
.funcignore
.gitignore
host.json
local.settings.json
proxies.json
```

Now, let's add the `server.go`

```golang
package main

import (
 "fmt"
 "io/ioutil"
 "log"
 "net/http"
 "os"
)

// function to respond the /api/hello request
func helloHandler(w http.ResponseWriter, r *http.Request) {
  // set content type
  w.Header().Set("Content-Type", "application/json")
  if r.Method == "GET" {
    w.Write([]byte("hello world"))
  } else {
    body, _ := ioutil.ReadAll(r.Body)
    w.Write(body)
  }
}

func main() {
  // FUNCTIONS_CUSTOMHANDLER_PORT contains the port used by the app to listen
  customHandlerPort, exists := os.LookupEnv("FUNCTIONS_CUSTOMHANDLER_PORT")
  // Assign a default port number whether the port exists
  if !exists {
    customHandlerPort = "8080"
  }
  // Instatiate a new http server instance
  mux := http.NewServeMux()
  mux.HandleFunc("/api/hello", helloHandler)
  fmt.Println("Go server Listening on: ", customHandlerPort)
  // Listen to a specific port and signal that it's ready to received requests
  log.Fatal(http.ListenAndServe(":"+customHandlerPort, mux))
}
```




