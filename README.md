#Neko [![wercker status](https://app.wercker.com/status/2ab4b79cf2d418606e884c5d98d1ec0d/s "wercker status")](https://app.wercker.com/project/bykey/2ab4b79cf2d418606e884c5d98d1ec0d) [![GoDoc](https://godoc.org/github.com/rocwong/neko?status.svg)](https://godoc.org/github.com/rocwong/neko)

A lightweight web application framework for Golang

## Features

* Extremely simple to use.
* RESTful support
* Middleware support
* Unlimited nested group routers.

## Getting Started
Basic usage
~~~go
  package main
  import "github.com/rocwong/neko"
  func main() {
      app := neko.Classic()
      app.GET("/", func(ctx *neko.Context)  {
          ctx.Text("Hello world!")
      })
      m.Run(":3000")
  }
~~~
Initial Neko without middlewares
~~~go
  app := neko.New()
  app.Use(neko.Logger())
  app.Use(neko.Recovery())
~~~

##Routing
Using GET, POST, PUT, PATCH, DELETE, HEAD and OPTIONS
~~~go
  app.GET("/get", get)
  app.POST("/post", post)
  app.PUT("/put", put)
  app.PATCH("/patch", patch)
  app.DELETE("/delete", delete)
  app.HEAD("/head", head)
  app.OPTIONS("/options", options)
~~~
Neko uses julienschmidt's [httprouter](https://github.com/julienschmidt/httprouter) internaly.


##Group Routing
~~~go
	v1 := app.Group("/v1", func(router *neko.RouterGroup) {
    	//url: /v1/item
		router.GET("/item", item)
        
        // nested group
		router.Group("/sub", func(sub *neko.RouterGroup) {
        	//url: /v1/sub/myitem
			sub.GET("/myitem", myitem)
		})
	})
    //url: /v1/act
	v1.GET("/act", act)
~~~

## Parameters
~~~go
    // This handler will match /user/neko but will not match neither /user/ or /user
    app.GET("/user/:name", func(ctx *neko.Context) {
        ctx.Text("Hello " + ctx.Params.ByName("name"))
    })

    // This one will match /user/neko/ and also /user/neko/3, but no match /user/neko
    app.GET("/user/:name/*age", func(ctx *neko.Context) {
        name := c.Params.ByName("name")
        age := c.Params.ByName("age")
        message := name + " is " + action
        ctx.Text(message)
    })
~~~

##Response

####Writing response
~~~go
		// Text writes string
		ctx.Text("Hello world!")
        
        // Json marshals the object to json string and write
		ctx.Json(neko.JSON({"message": "hey", "status": 200}))
        
        // Xml marshals the object to xml string and write
		ctx.Xml(neko.JSON({"message": "hey", "status": 200}))
~~~

####Redirect
~~~go
	//default redirect 302
	ctx.Redirect("/")
    //redirect 301
	ctx.Redirect("/", 301)
~~~

####Headers
~~~go
  // get header
  ctx.Writer.Header()  //return map[string][]string
  
  // set header
  ctx.SetHeader("x-before", "before")
~~~

## Middlewares
####Using middlewares
~~~go
	// Global middlewares
	app.Use(neko.Logger())
    
    // Per route middlewares, you can add as many as you desire.
    app.Get("/user", mymiddleware(), mymiddleware2(), user)
    
    //Pass middlewares to groups
	v1 := app.Group("/v1", func(router *neko.RouterGroup) {
    	//url: /v1/item
		router.GET("/item", item)
	}, mymiddleware1(), mymiddleware2(), mymiddleware3())
    
    v1.Use(mymiddleware4)
~~~
####Custom middlewares
~~~go
	func (ctx *neko.Context) {
    	//before request
        t := time.Now()
        
        ctx.Next()
        
        // after request
        latency := time.Since(t)
        log.Print(latency)

        // access the status we are sending
        status := c.Writer.Status()
        log.Println(status)
        
    }
~~~



## More Middleware
For more middleware and functionality, check out the repositories in the  [neko-contrib](https://github.com/neko-contrib) organization.

## Credits & Thanks
I use code/got inspiration from these excellent libraries:

*  [Gin](https://github.com/gin-gonic/gin) - design based on.
*  [Httprouter](https://github.com/julienschmidt/httprouter)
*  [Martini](https://github.com/go-martini/martini)


## License
Neko is licensed under the MIT