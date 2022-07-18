---
layout: post
title: Design Course summery!
---
### HTML Section
   - **Helper extension on VSCode**
       - **Live server** it is used for reflect the updates automatically while we are updating the content
       - **auto rename tags** when we update the tag name if we are in the start tag it Reflect this update the close tag also 
       - **Color Highlight** highlight the colors which are used in CSS to show what is the color used by displaying colored border around it
       - **image preview** display the image beside the line thatis used on it 
   - **main structured element**
       `using the most sutible tags will help you to get your page more logically structure and give you aslo set of optimization in SEO this related to the `_**semantic meaning of html**_
       - **article** it is used to display the main content of the page 
       - **aside** complimentary items that is related to the main topic 

### CSS section
   - **the main text style**
       <code> 
    h1 {
            font-size: 26px;
            text-transform: uppercase;
            font-style: italic;
            color: #444;
            font-family: sans-serif;
        }</code>
   - **what is the box model?**  
      `it is how elements are dispalyed in the webpage and what is thier size (content, border, and space inside and outside it) all those items is inside the element and reflect on the actual size of the element except the margine it is outside the element `
   - **reset default universal selector** $\rightarrow$ due to the margin property doesn't inherte from the parent to child we should use the unversial selector to override the defaults  
        
   - **header style reset** for font-size to start from 40 and each one reduced by 5px and also the margin add at the top is 40px and add to bottom 20px
   - **margin collapsed** 

### Layouts section 

#### What is layout mean?
   - it is the way that image text and all other components placed in the webpage  
   - it is also the visual structure which we place the content of the webpage by it 
   - it is also divided to two types 
       - **page layout:** which define the structure and the visualization shape of the webpage
       - **component layout:** it is dedicated to the small piece of the page called component how it is structured and how its elements define  
       
       
#### How do we can build a layout?
   - **float layout:** this the old way of building the layout and this type was replaced by grid system and flex box but it is still used in some cases 
       - when you use float to the element thier container will be collapsed **(height collapse)** what this and why does it happend
       - it is effect the srounded elements they will wrap around the floated element
       - the container element not adjust its height **height collapsed**
          - can be solved by adding empty element with clear property this is called **clearfix hack**   
          - if you use psoudo selector after to fix this issue you need to add some extra attribute 
              <code>
                .clearfix::after{
                    clear:both;
                    content: '';
                    dispaly: block; // due to it is inline 
                }
              </code>
          
          
   - **Flex box Layout:** is one of the modern and most used Specially in component layout. **it is perfect in building 1-dimensional layout**
       - the main usage of this is to divide container element into set of child elements 
       - help to align element inside the perant container verticly or horizontally 
       - it helps in vertical aligment and equal height columns
       - it is a great replacement to float 
       - the parent of element is called the flex container and the child elements are the element that we need to align the main axis to align the element called the main axis which is the horizontal one and the cross axis is the vertical one 
   - **css grid:** it is used to build complete 2-dimesional layouts and it is perfect of more complex layout and the whole page layout 
   
  
### short notes 
##### what does border-size do?
   `it is used to define the actual width of the element such as in many examples when you define the width of the box and add extera padding the element will be wrapped and the layout will be changed so if you add the border-size: border-box this wll calculate the actual width of element including the padding area till the border` **padding substracted from the content area**
   - you can apply the box-size attribute to all element by adding it to the universal element due to it is not one of the attribute that will be inherited  
   - this what most developer did in this context 
   <code>
    * {
        margin: 0;
        padding: 0;
        box-sizing: border-box;
    }
   </code>
   


### Flex sheet cheat
#### Container
   - display:Enables flex for all children.  
       - display: flex 
       - display: inline-flex  
 
 
   - flex-direction: Establishes the main axis.  
        - flex-direction: row 
        - flex-direction: row-reverse 
        - flex-direction: column 
        - flex-direction: column-reverse  

   - flex-wrap: Wraps items if they can't all be made to fit on one line.   **Advanced one need to be handle by another course**
     - flex-wrap: nowrap 
     - flex-wrap: wrap 
     - flex-wrap: wrap-reverse  
     
 
   - justify-content: Attempts to distribute extra space on the main axis.
     - justify-content: flex-start 
     - justify-content: flex-end 
     - justify-content: center 
     - justify-content: space-between 
     - justify-content: space-around 
     - justify-content: space-evenly    
     

   - align-items: Determines how items are laid out on the cross axis.
      - align-items: flex-start 
      - align-items: flex-end 
      - align-items: center 
      - align-items: baseline 
      - align-items: stretch


   - align-content: Only has an effect with more than one line of content. Examples shown here use flex-wrap. **Advanced one not be covered**
    - align-content: flex-start 
    - align-content: flex-end 
    - align-content: center 
    - align-content: space-between 
    - align-content: space-around 
    - align-content: stretch
    
#### children
 those attribute used to override the inherited behaviour of the parent
   - order: Allows you to explictly set the order you want each child to appear in.(the default is that all elements have the same order which is zero)
      - order: integer
      
 
   - flex-grow: Allows you to determine how each child is allowed to grow as a part of a whole. (if you set the width auto grow helped you to define scale for the width growing 1)
       - if it set to the first child each element will take space which fit the content and then this one will acquired the reset space
       - flex-grow: 1 (applied to all)
       - flex-grow (1, 2, and 3)


   - flex-basis: Defines the size of an element before remaining space is distributed. (like put recommendation to the browser to define the width of each item and this width shrinked and extended as needed)
       - first item 20%, second item 40%


   - flex-shrink: Allows an item to shrink if necessary. Only really useful with a set size or flex-basis. (the default is one and it used to shrink the elements to fit the container when define the flex-basis)
        - both want to be 100% wide, 2nd item has flex-shrink: 2

   - align-self: Sets alignment for individual item. See "align-items" for options
        - 3rd item has align-self:flex-end

- **flext**: grow shrink basis;
