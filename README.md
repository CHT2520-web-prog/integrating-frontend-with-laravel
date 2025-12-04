# Integrating Tailwind, JavaScript React into a Laravel app

When exploring the use of Tailwind, JavaScript and React we considered these technologies separately from Laravel. The following describes how to integrate a front-end build process using Vite into a Laravel application. As always the Laravel documentation https://laravel.com/docs/12.x/vite provides complete information. However, there are some differences to be aware of when using Codespaces, and these are described below.

## Integrating Tailwind
Default Laravel applications already have a _package.json_ file that specifies key Node packages that are needed. This includes the Vite package and the a Laravel plug-in that configures Vite to work alongside the Laravel server.
- In the terminal/shell enter:

```
npm install
```
- The packages should be installed and you'll get a *node_modules* folder. It should also generate a _vite.config.js_ file.

If you are using Codespaces you will need to make some changes to this file to add a _server_ option so that it looks like the following (make sure you swap in the name of your Codespace). If you are using XAMPP it should work without having to make any changes.

```javascript
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';
import tailwindcss from '@tailwindcss/vite';

export default defineConfig({
    plugins: [
        laravel({
            input: ['resources/css/app.css', 'resources/js/app.js'],
            refresh: true,
        }),
        tailwindcss(),
    ],
    server: {
        cors:true,
        hmr: {
            host: "name-of-codespace-5173.github.dev",
            clientPort: 443,
            protocol:'wss',
        },
        
    }

});
```
- Instead of using a CSS file in our _public_ directory. The CSS will be built using _app.css_ in the _resources/css_ directory. If you open this file, you should see that it already imports Tailwind. We don't need to make any changes to this.
- The last thing we need to do is load the Tailwind generated CSS using Vite. In your layout file, comment out the link to the CSS in your _public_ folder and use the ```@vite``` blade directive to load the Tailwind CSS:

```html
    <!-- <link href="{{asset('css/style.css')}}" type="text/css" rel="stylesheet" /> -->
    @vite(['resources/css/app.css'])
</head>
```
- In the terminal/shell enter:
```
npm run dev
```
This should start the Vite server and watch for changes to your files. If you are using Codespaces you will need to make port 5173 public (select the PORTS tab in the terminal). To run the Laravel app and view these changes you will need to open a new terminal/shell and enter the usual ```php artisan serve```. If you view your web application in a browser you should find the Tailwind styling has been applied. 

Now, if you add some Tailwind classes in your view e.g. 

```html
 <body class="bg-lime-500">
```
The changes should be reflected in the browser.

## Adding JavaScript
This is fairly straightforward. Again the JavaScript will be served from the _resources_ folder where there should already be an _app.js_ in this folder. 

- Add the following line of code in _app.js_ to check we can also use JavaScript. 
```javascript
alert("Added some JavaScript");
```
- Change the Vite directive to execute the JavaScript file.

```
@vite(['resources/css/app.css', 'resources/js/app.js'])
```
- Test this works. You should get an alert generated from JavaScript. 

## Using React

To get React to work, we need to install the React library and Vite plug-in just as we did previously.

```
npm install --save react react-dom
```
```
npm install --save-dev @vitejs/plugin-react
```

In the _resources/js_ folder create a new app.jsx file and add the following simple React code:

```javascript
import { StrictMode } from 'react'
import { createRoot } from 'react-dom/client'

function ReactButton() {
  return (
    <button>A simple React button</button>
  );
}

createRoot(document.querySelector('#react-content')).render(
  <StrictMode>
    <ReactButton/>
  </StrictMode>,
)
```
Add a ```<div>``` element into the HTML with an id of _react-content_. The React componet will be inserted into this ```<div>```:

```
<div id="react-content"></div>
```

- Make changes to the _vite.config.js_ file to use the React plug-in and to point to the .jsx file instead of the plain .js file.

```javascript
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';
import react from '@vitejs/plugin-react';
import tailwindcss from '@tailwindcss/vite';

export default defineConfig({
    plugins: [
        laravel({
            input: ['resources/css/app.css', 'resources/js/app.jsx'],
            refresh: true,
        }),
        react(),
        tailwindcss(),
    ],
    server: {
        cors:true,
        hmr: {
            host: "name-of-codespace-5173.app.github.dev",
            clientPort: 443,
            protocol:'wss',
        },
    }
});

```
- Make changes in your view to use the ```@viteReactRefresh``` directive and reference the .jsx file.

```
    <!-- <link href="{{asset('css/style.css')}}" type="text/css" rel="stylesheet" /> -->
    @viteReactRefresh
    @vite(['resources/css/app.css','resources/js/app.jsx'])
</head>
```
- Test in a browser, you should find that you have successfully inserted a React component into the page.
- Finally, to check everything is working, add a Tailwind class into the React component e.g.

```javascript
function ReactButton() {
  return (
    <button class="text-purple-500">A simple React button</button>
  );
}
```
- Refresh in a browser to confirm this works. 
