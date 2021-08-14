# geovation

Vue JS project that lets users add points and view existing locations of toilets from S3. 

You will need a valid mapbox account token and place in the index.vue at "MAPBOX KEY GOES HERE"


## Architecture for Gather HUB

![Diagram](https://github.com/nemotests/ghub_code/blob/main/gather%20hub.jpg)

## Assumptions

* Due to Gather Hub being a charity we likely want to aim for low on-going costs
* low maintenance would be a bonus
* Avoid lock in of data in 3rd party solution due to wanting to do analysis on data
* Mobile signal is available, if no mobile signal suggest creating app with offline capture & sync
* We only need to worry about capturing data in a single horrizontal datum, if need other datum supported PostGIS would be a good choice
* postGIS would also be a good choice if charity needed a central data store for analysis
* Setting up back-end is relatively expensive, trial data gathering with less automated techniques? Publish with QGIS2Web?


## Project setup
```
yarn install
```

### Compiles and hot-reloads for development
```
yarn serve
```

### Compiles and minifies for production
```
yarn build
```

### Lints and fixes files
```
yarn lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).
