# Front End Development Notes

## Summary

3 core components of the front end: HTML, CSS, Javascript

- HTML: Contains content and structure of the site
- CSS: contains styling elements for the site (font, colors, etc.)
- Javascript: Allows you to program interactivity into the site. Changing colors, counters, dropdowns, showing/hiding info with a button

## HTML

Simple markdown language that uses things called `tags` and a nested structure to define various parts of the page and various types of content and structure. Think of it as the skeleton of a web page. Everything you see on a webpage is eventually HTML in the end, this is what's rendered by the browser. Javascript and CSS just modify this to make things look and flow nicer.

Tags define the way text is displayed (paragraphs, lists, tables, etc.). It also defines where on the page things render. It also allows you to put in input fields like checkboxes, buttons, and form fields.

This quick ref sheet is great to see all the different options HTML has:

[HTML tag quick ref](https://www.w3schools.com/tags/ref_byfunc.asp)

### Basic Structure

```html
<!DOCTYPE html> 
<html>
<head>
<title>Page Title</title>
</head>
<body>

<header>
<!--Logos and other things typically go here-->
</header>

<main>
<!--Main content of the page typically goes here-->
<h1>My First Heading</h1>
<p>My first paragraph.</p>
</main>

<footer>
<!--Social links, quick navigations, other things typically go here-->
</footer>

</body>
</html>
```

### Very Basic Webpage w/ interactive button

```html
<html>
    <head>
        <title>Advertisement</title>
    </head>
    <body>
        <header>
            <h1 style="color: white; background-color: #2d37bd; text-align: center;">New Awesome Product</h1>
        </header>
        <main>
            <img src="https://blob.sololearn.com/courses/np.png">
            <!--This is a comment that can't be seen-->
            <p>Introducing this <b>awesome</b> new product!</p>
            <p><i>Isn't this cool</i></p>
            <ul>
                <li>This is one feature</li>
                <li>This is one feature</li>
                <li>This is one feature</li>
            </ul>

            <h2>Order now!</h2>

            <form>

                <label for="shirt_size_dropdown">Size:</label>
                <select name="shirt_size" id="shirt_size_dropdown">
                    <option value="Small">Small</option>
                    <option value="Medium">Medium</option>
                    <option value="Large">Large</option>
                </select>

                <label for="shirt_color_dropdown">Color:</label>
                <select name="shirt_color" id="shirt_color_dropdown">
                    <option value="Red" style="color: red;">Red</option>
                    <option value="Blue" style="color: blue;">Blue</option>
                    <option value="Green" style="color: green;">Green</option>
                </select> 
                <br> <br>

                <label for="name_form">Name:</label>
                <input type="text" id="name_form" name="order_name">
                <br> <br>

                <label for="email_form">Email:</label>
                <input type="email" id="email_form" name="order_email">
                <br> <br>
                
                <button type="submit" style="color: white; background-color: grey; border:none; border-radius:5px" onclick="alert('Order confirmed!')">Submit</button>
            </form>
        </main>
        <footer>
            <p style="color: white; background-color: #2d37bd; text-align: right;">© Copyright 2024 DS</p>
        </footer>

    </body>
</html>
```

## CSS

CSS stands for Cascading Style Sheets and is used to style and format the content created by HTML. It's how you get nice looking sites. Styling elements include fonts, sizes, colors, position, spacing, layout, animations, and more.

- `Inline CSS` is directly inside of HTML tags, which is fine for quick styling of specific elements  
- `Internal CSS` is inside `<style>` tags and can be used to format whole tags or different parts of the documents at the same time

CSS is composed of primarily `selectors` which select what elements to style, and `declarations` which say how to style. The declarations consist of a `property` and a `value`

```css
/*example css selector for <p> and <h1>, with declarations for color and text alignment*/
p,h1 {
    color:blue;
    text-align:center;
}
```
