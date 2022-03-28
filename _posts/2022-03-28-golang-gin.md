---
title: Go - Simple REST API with gin
date: 2022-03-28 16:00:00 +01:00
tags: [golang, api]
description: Go - Simple REST API with gin
---

Documentation: [gin-gonic/gin](https://github.com/gin-gonic/gin)

# Basic use

The basic example (only a GET request) is:

```golang
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()
	r.GET("/ping", func(c *gin.Context) {
		c.JSON(200, gin.H{
			"message": "pong",
		})
	})
	r.Run()
}
```

We can use a function instead:

```golang
package main

import "github.com/gin-gonic/gin"

func getting(c *gin.Context) {
  c.JSON(200, gin.H{
    "message": "pong",
  })
}

func main() {
    router := gin.Default()
    router.GET("/ping", getting)
    // router.POST("/somePost", posting)
    // router.PUT("/somePut", putting)
    // router.DELETE("/someDelete", deleting)
    // router.PATCH("/somePatch", patching)
    // router.HEAD("/someHead", head)
    // router.OPTIONS("/someOptions", options)

    r.Run() // listen and serve on 0.0.0.0:8080 (for windows "localhost:8080")
    // router.Run(":3000") for another port
}
```