# Golang Gin Framework - Video API (POC)

## Deploy the App to Heroku


## Build the application

```bash
go build -o bin/golang-gin-poc -v .
```

## Test your app in your local environment (Heroku assigns port 5000)

```bash
heroku local
```

## The "heroku create" CLI command creates a new empty application on Heroku,along with an associated empty Git repository.

```bash
heroku create pragmatic-video-app
```

## Deploy the app to Heroku (from master branch)

```bash
git push heroku master
```

## Deploy the app to Heroku (from heroku-deploy branch)

```bash
git push heroku heroku-deploy:master
```

## List existing apps

```bash
heroku apps
```

## Open the app using the browser

```bash
heroku open
```

## Check the app logs

```bash
heroku logs
```

## Remove the app

```bash
heroku destroy --confirm pragmatic-video-app
```