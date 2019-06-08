This is a quiz app in Vue.js

How to run the application
# using docker command
docker run -it -p 8080:8080 --rm --name docker-quiz-vue-app styrus05/quiz-app-in-vue:latest



Done
 - Load quiz from https://opentdb.com/api.php?amount=10&category=21&difficulty=easy&type=multiple
 - Progress bar to show the progress and scorecard


 To-do
 - Authentication
 - Saving score card in DB
 - A config file for static value, like link to quiz api, etc.
 - Options to load different types of quiz (category, complexity, etc.)
 