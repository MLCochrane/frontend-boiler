# Frontend Boiler

Simple boilerplate for frontend projects allowing you to get up and running quick! Using handlebars, SCSS, Typescript.

## Getting Started

These instructions will get you a copy of the project up and running on your local machine for development and testing purposes.

### Install

Once you've cloned or downloaded the repo you can:

```
npm install
```

Which will install all the necessary dependancies.

Running a development environment is done with:

```
npm run dev
```

### Setting up pages

Without any server-side logic and the handling of routes, [html-webpack-plugin](https://github.com/jantimon/html-webpack-plugin) is used for the creation of our HTML files.

Each folder in the `src/js/` directory will be used to make a page unless included in the overrides array.

```javascript
const overrides = ['utils', 'homepage', 'global'];
const paths = fs.readdirSync(path.join(__dirname, '../', 'src/js')).filter((el) => overrides.indexOf(el) === -1);

const pages = paths.map((el) => new HtmlWebpackPlugin({ // eslint-disable-line no-new
  filename: `${el}/index.html`, // specify filename or else will overwrite default index.html
  inject: {},
  template: `src/views/pages/${el}.hbs`,
  templateParameters: {
    asset_path: process.env.npm_lifecycle_event === 'dev' ? './src' : '',
  },
}));
```

It should be noted that this requires the folder and file structure to follow a certain structure.

This is then concatenated like so:

```javascript
  ...
  plugins: [
  new HtmlWebpackPlugin({
    inject: {},
    template: "src/views/pages/index.hbs"
  })
].concat(pages),
```

-   Adding a folder when setting the filename for new page templates ensures that the .html extension is not included in the url.

### Handling views

The project also makes use of [Barba](https://github.com/barbajs/barba) for page transitions. If you would like to make use of this as well, then we need to touch on associating a container and a view (read more [here](https://barba.js.org/docs/v2/user/)).

Adding a namespace attribute to your page templates like this:

```javascript

{{#> layout/base title='Frontend Boiler - Contact' namespace="contact" }}
  {{#*inline "body"}}
    {{> components/contact }}
  {{/inline}}
{{/layout/base}}

```

Allows you to reference them in Barba lifecycle hooks like so:

```javascript
enter: data => {
  if (data.current.namespace === 'home') {
    slider = new Slider(data.next.namespace);
  }
},
```

This allows for some more control over state and event binding/unbinding throughout your project.

In this project there is currently a single page transition for all pages, however, Barba has support for custom rules which will dicate which transition to use. Additional transitions can be placed in the transitions array with their various rules.

```javascript
barba.init({
  transitions: [
    {
      sync: true,
      once: ({ next }) => {
        // Initial load
      },
      enter: ({next}) => {
        // entering a page
      },
      leave: async ({ current, next }) => {
        // leaving a page
      }
    }
  ]
});
```

Please refer to the official Barba documentation linked above for more details.

## Deployment

Building the project files for production is done with:

```
npm run build
```

This will create a dist folder with static assets that is ready to be uploaded to a server or as a point to deploy a [Surge](https://surge.sh/) project from.

## Built With

-   [handlebars](https://handlebarsjs.com/)
-   [webpack](https://webpack.js.org/)
-   [SCSS](https://sass-lang.com/)
-   [GSAP](https://greensock.com/gsap)
-   [Barba](https://barba.js.org/)

## Authors

-   **Luke Cochrane** [MLCochrane](https://github.com/MLCochrane/)

## License

This project is licensed under the MIT License
