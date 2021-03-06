# Golang
![alt text](https://memecrunch.com/meme/BFP6K/go-go-power-rangers/image.gif?w=473&c=1 "Logo Title Text 1")
## Concurrency
One of the great things about GoLang is the concept of concurrency: the ability to throw all your code -- your routes, functionality, HTML templating -- all into one file and the computer will execute all of it it simultaneously. GoLang basically allows programmers to create functions than can be executed at the same time as other functions. 
 
## GoRoutines 

A simple thread of execution managed by Go's runtime environment that allows multiple functions to be executed at the same time. 

![alt text](https://media.giphy.com/media/5aLrlDiJPMPFS/giphy.gif "Logo Title Text 2")

## Systems-oriented programming language
- Created in 2007 by Google
- More expressive, readable, consistent, robust, clean
- Main purpose is for scalability, concurrency
- YOU DON'T NEED TO WORRY ABOUT FORGETTING SEMICOLONS

## Installation
Just download the package manager here: https://golang.org/dl/
And learn how to use it with this Go Tour: https://tour.golang.org/list

## Code Breakdown

Although Golang is concurrent, it is still possible and most times encouraged to follow 
the familiar MVC arcitecture when structering your environment. For our purposes, we decided
to keep everything tidy inside of one file.

The main file in Golang, the file that is to execute everything, starts off like so

```
package main

import (
	"encoding/json"
	"html/template"
	"fmt"
	"io/ioutil"
	"net/http"
)
```

Out of the box, Go is a powerful, yet very explicit language. We can build out the things we need... Or we can keep it simple and import some packages. "package main" is telling Go that this file is where all of our files will be executed (Convention? Maybe). Here, we are also importing some other cool things that help us out big time when making a web app. 

* "encoding/json": This allows you to easily and efficiently parse JSON data.
* "html/template": Templating (Kind of like mustaché)
* "fmt": Printing to the console.
* "io/ioutil": IDK
* "net/http": Possibly the coolest... Get a server up and running with only a few lines of code.

```
type Page struct {
  	Temperature int
  	Humidity int
  	Max int
  	Min int
  	Welcome string
}

type Weather struct {
	Main        string `json:"main"`
	Description string `json:"description"`
}


type Temperature struct {
	Temp     float64 `json:"temp"`
	Pressure float64 `json:"pressure"`
	Humidity float64 `json:"humidity"`
	TempMin  float64 `json:"temp_min"`
	TempMax  float64 `json:"temp_max"`
}

type Forecast struct {
	Weathers []Weather   `json:"weather"`
	Temp     Temperature `json:"main"`
}
```

**Structs** are a collection of fields that are extremely useful for grouping data together. Not quite an object, not quite a datatype... Maybe a safe place in the middle.

```
const(
    api = "13a75dbfde99048a6b499bcd9aca260b"
)
func getWeather(zip string)(*Forecast, error){
	url := fmt.Sprintf("http://api.openweathermap.org/data/2.5/weather?zip=%s&APPID=%s", zip, api)
	res, err := http.Get(url)

	body, err := ioutil.ReadAll(res.Body)
	if err != nil {
		return nil, err
	}


	var f Forecast
	err = json.Unmarshal(body, &f)
	if err != nil {
		return nil, err
	}

	t := f.Temp.Temp * 9/5 - 459.67
	fmt.Println(`The temperature is`, t)
	fmt.Println(f)

	return &f, nil
}
```

MORE STUFF

```
func indexHandler(w http.ResponseWriter, r *http.Request) {

    p := &Page{
        Welcome: "Welcome to GoLang!",
    }
    t, _ := template.ParseFiles("index.html")
    t.Execute(w,p)

}
 
func resultsHandler(w http.ResponseWriter, r *http.Request) {
    fmt.Println(r.Method)
	fmt.Println(r.URL)

  	z := r.FormValue("zip")
  	fmt.Println(z)
	

    cast,err := getWeather(z)
    if err != nil{
    	fmt.Println(err) 
    }

    p := &Page{
        Temperature: int(cast.Temp.Temp * 9/5 - 459.67),
        Humidity: int(cast.Temp.Humidity),
        Max: int(cast.Temp.TempMax * 9/5 - 459.67),
        Min: int(cast.Temp.TempMin * 9/5 - 459.67),
    }
    t, _ := template.ParseFiles("results.html")
    t.Execute(w, p)
}

func main() {

	getWeather("10128")

	http.HandleFunc("/", indexHandler)

	http.HandleFunc("/results", resultsHandler)

	http.ListenAndServe(":8080", nil)
}
```

```
<p> 
	Its is Currently
	<span class="number">	
		{{.Temperature}}
	</span> Degrees. 
	<br/>
	The Current Humidity is 
	<span class="number">
		{{.Humidity}}
	</span>% 
	<br/>
	The temperature today could reach a high of 
	<span class="number">
		{{.Max}}
	</span> 
	and a low of 
	<span class="number">
		{{.Min}}
	</span>
</p>

```