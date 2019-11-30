# [Challenge] 1121 AJAX, CORS, Promises & Fetch API - AJAX

##### Challenge Instruction: https://coderacademyedu.github.io/resources/unit_ajax.html

---

__Challenge 1.	Create a new 'Tweeter' rails app. Scaffold 'Tweet' with a single field: 'message'__
```
$ rails new Tweeeter -d postgresql
$ cd Tweeeter
$ sudo service postgresql start 
$ rails db:create
$ rails db:migrate 
$ rails g scaffold Tweet message:string 
$ rails db:migrate
```
---
__Challenge 2.	Start your Rails server and add a few tweets.__
```
$ rails s
```

![Tweets](./docs/tweets.JPG)

---

__Challenge 3.	Create a new HTML document, add jQuery.__

```
$ mkdir AJAX
$ cd AJAX
$ touch index.html
```
- Copy the 'jquery.min.js' file into the same folder as 'index.html'.
- Add script tag inside the head of index.html

```html
<head>
    <script src="jquery.min.js" type="text/javascript"></script>
</head>
```
---
__Challenge 4.	Use jQuery to send a request to your Rails server, GET all the tweets. You'll need to resolve the CORS issue, Google :).__

- Add '.json' to your URL to turn your Rails app endpoint (http://localhost:3000/tweets) into an API (http://localhost:3000/tweets.json)

- Go to 'config\application.rb' in your Rails app, and add the following code:
```ruby
module Tweeeter
    class Application < Rails::Application
        config.action_dispatch.default_headers = {
            'Access-Control-Allow-Origin' => '*'
        }
    end
end
```
- Remember to restart your Rails server after this

a) Using XHR requests to console.log all the tweets

```html
<button id="myBtn">Get Tweets</button>

<script>
    document.querySelector("#myBtn").addEventListener("click", getTweets);

    function getTweets(){
        // console.log("working");  //First write simple code to test if everything's working

        let request = new XMLHttpRequest();

        request.open("GET", "http://localhost:3000/tweets.json", true); // True: asynchronous

        request.onerror = () => {
            console.log("Connection error!");
        }

        request.onload = () => {
            if (request.status >= 200 && request.status < 300){
                let parsedResponse = JSON.parse(request.responseText);
                for(let i = 0; i < parsedResponse.length; i++){
                    console.log(parsedResponse[i].message);
                }
            } else {
                console.log("Error", request);
            }
        }
        
        request.send();
    }
</script>
```

b) Using jQuery AJAX to console.log all the tweets
```html
<script>
    document.querySelector("#myBtn").addEventListener("click", getTweets);

    function getTweets(){
    //longhand - $.ajax: Performs an async AJAX request
    $.ajax({
        url: "http://localhost:3000/tweets.json",
        type: "GET",
        success: (data) => {
            for(let i = 0; i < data.length; i++){
            console.log(data[i].message);
            }
        },
        error: (error) => {
            console.log(error);
        }
    });

    //shorthand - $.get(): Loads data from a server using an AJAX HTTP GET request
    $.get("http://localhost:3000/tweets.json", (data) => {
    for(let i = 0; i < data.length; i++){
        console.log(data[i].message);
        }
    }).fail(() => { //tried to chain an error handling func here, but this doesn't work
                    //though the program doesn't crash either
    console.log('woops'); 
    });
    }

</script>
```
---
__Challenge 5.	Display all of these tweets on your page. Add some styling or use a framework to make it look more Twitter-esque__

c) Using jQuery AJAX to display all the tweets

```html
<body>
    <h1>AJAX Challenge</h1>
    <button id="myBtn">Get All Tweets</button>
    <ul></ul>
    
    <script>
        document.querySelector("#myBtn").addEventListener("click", getTweets);

        function getTweets(){
            $.get("http://localhost:3000/tweets.json", (data) => {
                for(let i = 0; i < data.length; i++){
                    let newLi = document.createElement("li");
                    newLi.innerHTML = `Tweet ${i + 1}: ${data[i].message}`;
                    document.querySelector("ul").appendChild(newLi);  
                }

            })
        }
    </script>
</body>
```

Add style tag directly inside html file to create simple twitter-like colour scheme.

```html
<style>
    body {background-color: #1DA1F2; text-align: center;}
    h1, li {color: white;}
    li {text-align: left;}
</style>
```


---
__Challenge 6.	Replace your jQuery AJAX with Fetch.__

d) Using Fetch to display all the tweets

```html
<script>
    document.querySelector("#myBtn").addEventListener("click", getTweets);

    async function getTweets(){
        const response = await fetch("http://localhost:3000/tweets.json");
        const myJson = await response.json();
        for(let i = 0; i < myJson.length; i++){
            let newLi = document.createElement("li");
            newLi.innerHTML = `Tweet ${i + 1} - ${myJson[i].message}`;
            document.querySelector("ul").appendChild(newLi);      
        }
    }
</script>
```
---

# [Beast Mode]

Beast Mode is at Beast Mode branch: https://github.com/EllieChen-Git/Tweeeter/tree/Beast_Mode
--
__Beast Mode 1.	Add a form to the top of the page which allows the user to create a new tweet. Once created (use AJAX), the tweet should appear at the top of your list of tweets.__

- Add a form

```html
<div>
    <form>
        <label for="tweet">Create Your tweet</label>
        <input type="textarea" name="tweet" id="tweet"></input>
        <input type="submit" value="Create"/>
    </form>
</div>
```
- Create new tweet

app\controllers\tweets_controller.rb
```ruby
class TweetsController < ApplicationController
  skip_before_action :verify_authenticity_token
end
```

```html
<script>
    document.querySelector("input[type=submit]").addEventListener("click", createTweets);
        
    function createTweets(){
        event.preventDefault();

        let inputValue = document.querySelector("#tweet").value; //grab the value from input field
        let newTweet = { tweet: { message: inputValue } };

        $.ajax({
            url: "http://localhost:3000/tweets.json",
            type: "POST",
            data: newTweet
        })
    };
</script>
```

- new tweet appear at the top of list
```html
    <script>
        document.querySelector("#myBtn").addEventListener("click", getTweets);
        function getTweets(){
            $.get("http://localhost:3000/tweets.json", (data) => {
                for(let i = 0; i < data.length; i++){
                    let newLi = document.createElement("li");
                    newLi.innerHTML = `Tweet ${i + 1}: ${data[i].message}`;
                    document.querySelector("ul").prepend(newLi);  //changed from 'append' to 'prepend' will achieve this result
                }
            })
        }
```

- screenshot
![Tweets](./docs/beast_mode_1.JPG)

---
__Beast Mode 2. Add a 'delete' button to each tweet. This should send a DELETE request to your server. Fade out the tweet if it is deleted successfully.__

```
to do
```
---
__Beast Mode 3. Update your model to include a 'starred' boolean. Users should be able to star/favorite tweets. Add some kind of star icon on the front end.__

```
to do
```