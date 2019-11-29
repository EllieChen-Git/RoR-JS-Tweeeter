#### [Challenge] 1121 AJAX, CORS, Promises & Fetch API - AJAX
Challenge link: https://coderacademyedu.github.io/resources/unit_ajax.html

##### Challenge
1.	Create a new 'Tweeter' rails app. Scaffold 'Tweet' with a single field: 'message'
```
$rails new Tweeeter -d postgresql
$ cd Tweeeter
$ sudo service postgresql start 
$ rails db:create
$ rails g scaffold Tweet message:string 
$ rails db:migrate
```
2.	Start your Rails server and add a few tweets.
3.	Create a new HTML document, add jQuery.
4.	Use jQuery to send a request to your Rails server, GET all the tweets. You'll need to resolve the CORS issue, Google :).
5.	Display all of these tweets on your page. Add some styling or use a framework to make it look more Twitter-esque
6.	Replace your jQuery AJAX with Fetch.

