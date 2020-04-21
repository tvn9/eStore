# LAPTOP STORE

An online laptop store homework project on Nodejs, Javascript, CSS, and HTML.

The goal of the exercise is to create a simple ecommerce store front page that request product data from Node server, using pure JavaScript for backend development.

## Technology Used

* HTML
* CSS
* JavaScript
* Nodejs

## Website Screenshot

### Store Font

![Store Font](data/img/appscreen01.png)

### Product Page

![Product Page](data/img/appscreen02.png)

## Code Snipets

### Js Code

```Js
const fs = require('fs');
const http = require('http');
const url = require('url');

const json = fs.readFileSync(`${__dirname}/data/data.json`, 'utf-8');
const laptopData = JSON.parse(json);
console.log(laptopData);

const server = http.createServer((req, res) => {

   const pathName = url.parse(req.url, true).pathname;
   const id = url.parse(req.url, true).query.id;
   // console.log(pathName);

   // PRODUCT OVERVIEW
   if (pathName === '/products' || pathName === '/') {
      res.writeHead(200, { 'Content-type': 'text/html' });
     
      fs.readFile(`${__dirname}/templates/overviewTemplate.html`, 'utf-8', (err, data) => {
         let oveviewOutput = data;

         fs.readFile(`${__dirname}/templates/productCardTemplate.html`, 'utf-8', (err, data) => {

            const cardsOutput = laptopData.map(el => replaceTemplate(data, el)).join('');
            oveviewOutput = oveviewOutput.replace('{%CARDS%}', cardsOutput);

            res.end(oveviewOutput);
         });

      });

   } else if (pathName === '/laptop' && id < laptopData.length) {
      res.writeHead(200, { 'Content-type': 'text/html' });

      fs.readFile(`${__dirname}/templates/laptopTemplate.html`, 'utf-8', (err, data) => {
         const laptop = laptopData[id];
         const output = replaceTemplate(data, laptop);
         res.end(output);
      });
   
   } else if ((/\.(jpg|jpeg|png|gif)$/i).test(pathName)) {
      fs.readFile(`${__dirname}/data/img/${pathName}`, (err, data) => {
         res.writeHead(200, { 'Content-type': 'image/jpg' });
         res.end(data);
      });
   } else {
      res.writeHead(404, { 'Content-type': 'text/html' });
      res.end('URL does not exist!');
   }
   console.log('Someone did access the server!');
});

server.listen(1337, '127.0.0.1', () => {
   console.log('Listening for requests now');
});

function replaceTemplate(originalHtml, laptop) {

   let output = originalHtml.replace(/{%PRODUCTNAME%}/g, laptop.productName);
   output = output.replace(/{%IMAGE%}/g, laptop.image);
   output = output.replace(/{%PRICE%}/g, laptop.price);
   output = output.replace(/{%SCREEN%}/g, laptop.screen);
   output = output.replace(/{%CPU%}/g, laptop.cpu);
   output = output.replace(/{%STORAGE%}/g, laptop.storage);
   output = output.replace(/{%RAM%}/g, laptop.ram);
   output = output.replace(/{%DESCRIPTION%}/g, laptop.description);
   output = output.replace(/{%ID%}/g, laptop.id);
   return output;
}
```

### HTML/CSS Templates

```html
<body>
    <div class="container">
        <h1>The Laptop Store!</h1>
        <figure class="laptop">
            <p class="laptop__price">${%PRICE%}</p>
            <a href="/products" class="laptop__back"><span class="emoji-left">👈</span>Back</a>
            <div class="laptop__hero">
                <img src="/{%IMAGE%}" alt="{%PRODUCTNAME%}" class="laptop__img">
            </div>
            <h2 class="laptop__name">{%PRODUCTNAME%}</h2>
            <div class="laptop__details">
                <p><span class="emoji-left">🖥</span>{%SCREEN%}</p>
                <p><span class="emoji-left">🧮</span>{%CPU%}</p>
                <p><span class="emoji-left">💾</span>{%STORAGE%}</p>
                <p><span class="emoji-left">📦</span>{%RAM%}</p>
            </div>
            <p class="laptop__description">{%DESCRIPTION%}</p>
            <p class="laptop__source">Source: <a href="https://www.techradar.com/news/mobile-computing/laptops/best-laptops-1304361"
                    target="_blank">https://www.techradar.com/news/mobile-computing/laptops/best-laptops-1304361</a></p>
            <a href="#" class="laptop__link">Buy now for ${%PRICE%} <span class="emoji-right"></span></a>
        </figure>
    </div>
</body>
```
